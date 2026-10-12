#### Transform data with filters and plugins - Drills

The drills that follow will cover the following exam objectives. 

- Populate variables with data from external sources using lookup plugins
- Use lookup and query functions to incorporate data from external sources into playbooks and deployed template files
- Implement loops using structures other than simple lists using lookup plugins and filters
- Inspect, validate, and manipulate variables containing networking information with filters

The goal is to run this via AAP, however during testing will need to make sure all the collections needed are also available locally. 

Create a `collections` directory, `mkdir collections` inside of the working local repository. Will add this folder into `.gitignore` so it isn't tracked by git. 


Create a simple `ansible.cfg` file to make testing easy and quick, see content below.

```ini
[defaults]
collections_path = ~/.ansible/collections:/usr/share/ansible/collections:/home/ansible/dev/ansible-lab/collections
inventory=inventory
```
Install the required collections. 

`ansible-galaxy install ansible.posix -p collections`
`ansible-galaxy install ansible.utils -p collections`

Confirmed both are installed.

```shell
[ansible@tower ansible-lab (main)]$ ansible-galaxy collection list

# /home/ansible/dev/ansible-lab/collections/ansible_collections
Collection    Version
------------- -------
ansible.posix 2.2.2
ansible.utils 6.1.1
```

###### Task 1 — default + file lookup. 

On AAP, create files/motd.txt with one line of text. Write banner.yml (localhost): set banner_text from the file lookup, and set admin_contact from a variable that may not exist — use the default filter to fall back to ops@cezeh.lab. Debug both. Self-check: run once normally, then again with -e admin_contact=me@lab.local — the second run must show your value, the first the fallback.

<details>

<summary> Click to see solution </summary>

Created the file and commited it to the repository.

```shell
mkdir files

echo "Hello World" >> files/motd.txt
git add .
git commit -m "added message of the day file"
```

Solution playbook is located [here](../playbooks/banner.yml). The playbook is also moved to the private repo. 

Next step is to test it on the AAP. 


</details>

###### Task 2 — dict2items loop. 

Write appfiles.yml for the web group: given a dict apps: {shop: 8080, blog: 8081, wiki: 8082}, loop it with dict2items to create /tmp/app-<name>.conf on each host, each file containing its port. Self-check: all three files exist on frontend-1 and frontend-2 with the right ports inside.


<details>

<summary> Click to see solution </summary>

See [appfiles](../playbooks/appfiles.yml) for the solution playbook. See output of running the playbook below. 

```shell
[ansible@tower ansible-lab (main %)]$ ansible-playbook appfiles.yml 

PLAY [Playbook to demonstrate how to use dict2items] ***********************************************************************************************************************

TASK [Gathering Facts] *****************************************************************************************************************************************************
ok: [frontend-2]
ok: [frontend-1]

TASK [Create config files for each app] ************************************************************************************************************************************
ok: [frontend-2] => (item={'app': 'shop', 'port': 8080})
ok: [frontend-1] => (item={'app': 'shop', 'port': 8080})
ok: [frontend-2] => (item={'app': 'blog', 'port': 8081})
ok: [frontend-1] => (item={'app': 'blog', 'port': 8081})
ok: [frontend-2] => (item={'app': 'wiki', 'port': 8082})
ok: [frontend-1] => (item={'app': 'wiki', 'port': 8082})

PLAY RECAP *****************************************************************************************************************************************************************
frontend-1                 : ok=2    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   
frontend-2                 : ok=2    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   

[ansible@tower ansible-lab (main %)]
```

</details>

###### Task 3 — selectattr + map. 

Write filtering.yml (localhost): given a services list of dicts with name, port, and enabled keys (mix true/false), derive the list of ports for enabled services only, using selectattr and map in one expression. Debug the result, then write it to /tmp/enabled-ports.txt with the copy module.

<details>

<summary> Click to see Solution </summary>

See [filtering](../playbooks/filtering.yml) playbook for solution to the drill, see below for shell output when playbook is ran.

```shell
[ansible@tower ansible-lab (main %)]$ ansible-playbook filtering.yml 

PLAY [drill on how to use selectattr and map] ******************************************************************************************************************************

TASK [Gathering Facts] *****************************************************************************************************************************************************
ok: [localhost]

TASK [capture only the ports with enabled true] ****************************************************************************************************************************
ok: [localhost]

TASK [show the values of enabled_ports] ************************************************************************************************************************************
ok: [localhost] => {
    "enabled_ports": [
        80,
        8080,
        6379,
        22
    ]
}

TASK [write the content of enabled_ports to a file.] ***********************************************************************************************************************
ok: [localhost]

PLAY RECAP *****************************************************************************************************************************************************************
localhost                  : ok=4    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
```

A key observation when running this playbook via AAP, since AAP runs in a containerized environment, the file `/tmp/enab..` wasn't actually written to the `localhost` running AAP. To make sure the file was written to the host, I added a `delegate_to` at the end of the copy task. 


</details>

Task 4 — template module with validation. 
Write templates/vhost.conf.j2 — a Jinja2 template for an Apache <VirtualHost> block driven by three variables: vhost_port, server_name, and doc_root. Write vhost.yml that renders it with the template module to /tmp/vhost.conf on both web hosts. Use the validate parameter so the rendered file gets syntax-checked before it's put in place (think httpd -t). 



#### Key ideas for working with plugins.

- See [day 29](./day-29.md#common-ansible-plugin-types) notes for common plugin types. 

- Inside of the plugin types (`filter` is a plugin type) are the plugins, and to get a list of plugins, use;
`ansible-doc -t <plugin-type> -l`

See example below:

```shell
[ansible@tower ansible-lab (main)]$ ansible-doc -t filter -l
ansible.builtin.b64decode            Decode a base64 string
ansible.builtin.b64encode            Encode a string as base64
ansible.builtin.basename             get a path's base name
ansible.builtin.bool                 cast into a boolean
```
- To see how to use a plugin, `ansible-doc -t filter <plugin-name>`

<details>

<summary>Click to see example</summary>

```shell
[ansible@tower ansible-lab (main)]$ ansible-doc -t filter bool
> ANSIBLE.BUILTIN.BOOL    (/usr/lib/python3.9/site-packages/ansible/plugins/filter/bool.yml)

        Attempt to cast the input into a boolean (`True' or `False') value.

ADDED IN: historical

OPTIONS (= is mandatory):

= _input
        Data to cast.
        type: raw


NAME: bool

POSITIONAL: _input

EXAMPLES:

# simply encrypt my key in a vault
vars:
  isbool: "{{ (a == b)|bool }} "
  otherbool: "{{ anothervar|bool }} "

# in a task
...
when: some_string_value | bool

```

</details>

- To get a code snippet of a plugin, `ansible-doc -t lookup -s file`
  
```shell
[ansible@tower ansible-lab (main)]$ ansible-doc -t lookup -s file
# _terms(string): path(s) of files to read
# lstrip(bool): whether or not to remove whitespace from the beginning of the looked-up file
# rstrip(bool): whether or not to remove whitespace from the ending of the looked-up file

lookup('file', < _terms >, lstrip=False, rstrip=True)
```

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

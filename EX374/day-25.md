#### Using Filters

Plugins are pieces of code that can be used to enhance the core functionality of Ansible. Filters are a type of plugin that lets you modify data.

Filters are typically used for type casting a variable before they are used in conditional statements. 

Filters like other plugins are provided by collections. `ansible-doc -t filter -l` command will show you the currenty installed filters.

Example output below:

```shell
[ansible@control ansible_work]$ ansible-doc -t filter -l
ansible.builtin.b64decode                        Decode a base64 string
ansible.builtin.b64encode                        Encode a string as base64
ansible.builtin.basename                         get a path's base name
ansible.builtin.bool                             cast into a boolean
ansible.builtin.checksum                         checksum of input data
ansible.builtin.combinations                     combinations from the elements of a list
ansible.builtin.combine                          combine two dictionaries
ansible.builtin.comment                          comment out a string
ansible.builtin.commonpath                       gets the common path
ansible.builtin.dict2items                       Convert a dictionary into an itemized list of dictionaries
ansible.builtin.difference                       the difference of one list from another
ansible.builtin.dirname                          get a path's directory name
ansible.builtin.expanduser                       Returns a path with `~' translation
ansible.builtin.expandvars                       expand environment variables
ansible.builtin.extract                          extract a value based on an index or key
ansible.builtin.fileglob                         explode a path glob to matching files
ansible.builtin.flatten                          flatten lists within a list
ansible.builtin.from_json                        Convert JSON string into variable structure
ansible.builtin.from_yaml                        Convert YAML string into variable structure
ansible.builtin.from_yaml_all                    Convert a series of YAML documents into a variable structure

```
##### Making a custom filter.

We will be making use of this [filter](https://github.com/sandervanvugt/ansible_ccat/tree/main/my_collection) created by [Sander van Vugt](https://github.com/sandervanvugt/ansible_ccat/commits?author=sandervanvugt), clone the repo. I will move the collection directory to my working directory.

This is the structure of `my_collection` directory;

```shell
[ansible@control ansible_work]$ tree my_collection/
my_collection/
├── galaxy.yml
├── my_namespace-my_collection-1.0.0.tar.gz
├── plugins
│   └── filter
│       └── phone_format.py
└── README.md
```
The python file `phone_format.py` takes a simple 10 digit string, and returns it like a phone number. Hence, `input='1234567890'` will return `output=(123) 456-7890`.

Use `ansible-galaxy collection build my_collection` to build the collection, then use `ansible-galaxy collection install my_namespace-my_collection-1.0.0.tar.gz` to install. 

We can verify that our collection is installed as shown below;

```shell
[ansible@control ansible_work]$ ansible-galaxy collection list

# /home/ansible/.ansible/collections/ansible_collections
Collection                 Version
-------------------------- -------
ansible.posix              2.2.2
my_namespace.my_collection 1.0.0

# /usr/share/ansible/collections/ansible_collections
Collection                 Version
-------------------------- -------
redhat.rhel_system_roles   1.120.5


[ansible@control ansible_work]$ ansible-doc -t filter -l | grep -i my_namespace
my_namespace.my_collection.phone_format          UNDOCUMENTED


```

##### Using a custom filter.

This is a sample playbook to test the custom filter we created above.

```yaml
---
- name: demonstrating custom filter
  hosts: vm1
  gather_facts: no
  vars:
    number: '1234567890'
  tasks:
  - name: use a custom filter to format the number
    debug:
      msg: the number is {{ number | my_namespace.my_collection.phone_format }}
```

Output:

```shell
[ansible@control ansible_work]$ ansible-playbook phonenumber.yml

PLAY [demonstrating custom filter] *******************************************************************************************************************************************************************

TASK [use a custom filter to format the number] ******************************************************************************************************************************************************
ok: [vm1] => {
    "msg": "the number is (123) 456-7890"
}

PLAY RECAP *******************************************************************************************************************************************************************************************
vm1                        : ok=1    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0

```




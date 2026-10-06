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

#### Understanding plugins.

Plugin is an extension to the Ansible code that is written in Python. Ansible core comes with some standard plugins, and additional plugins can be installed via content collections. Filters which we look at earlier are a type of plugin the extendes the functionality of our ansible plays. 
Use this command `ansible-doc -t plugin-type -l` to get a list of plugins in a plugin-type. For example, the list of `lookup` plugins installed currently in my control node are below:

```shell
[ansible@control ansible_work]$ ansible-doc -t lookup -l
ansible.builtin.config              Lookup current Ansible configuration values
ansible.builtin.csvfile             read data from a TSV or CSV file
ansible.builtin.dict                returns key/value pair items from dictionaries
ansible.builtin.env                 Read the value of environment variables
ansible.builtin.file                read file contents
ansible.builtin.fileglob            list files matching a pattern
ansible.builtin.first_found         return first file found from list
ansible.builtin.indexed_items       rewrites lists to return 'indexed items'
ansible.builtin.ini                 read data from an ini file
ansible.builtin.inventory_hostnames list of inventory hosts matching a host pattern
ansible.builtin.items               list of items
ansible.builtin.lines               read lines from command
ansible.builtin.list                simply returns what it is given
ansible.builtin.nested              composes a list with nested elements of other lists
ansible.builtin.password            retrieve or generate a random password, stored in a file
ansible.builtin.pipe                read output from a command
ansible.builtin.random_choice       return random element from list
ansible.builtin.sequence            generate a list based on a number sequence
ansible.builtin.subelements         traverse nested key from a list of dictionaries
ansible.builtin.template            retrieve contents of file after templating with Jinja2
ansible.builtin.together            merges lists into synchronized list
ansible.builtin.unvault             read vaulted file(s) contents
ansible.builtin.url                 return contents from URL
ansible.builtin.varnames            Lookup matching variable names
ansible.builtin.vars                Lookup templated value of variables
ansible.utils.get_path              Retrieve the value in a variable using a path
ansible.utils.index_of              Find the indices of items in a list matching some criteria
ansible.utils.to_paths              Flatten a complex object into a dictionary of paths and values
ansible.utils.validate              Validate data with provided criteria
```

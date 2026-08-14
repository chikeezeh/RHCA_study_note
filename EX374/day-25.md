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

#### Defining Variables in practice.
##### Host/Group variables file
To use host or group specific variables, you will need to create a directory in the ansible working directly with the names `host_vars` and `group_vars` respectively. Then inside of this directories, create a variable file that corresponds to the name of host or group, and put the variables insied using the key: value pair notation. Note, these type of variables are automatically picked up by the playbook, and only the keys needs to be added where we want to use the values. 

Example, lets say we have an inventory file with the groups `rhel` and `ubuntu`:
```yaml
[rhel]
vm2

[ubuntu]
vm3
```
We can create this directory:
`<ansible_working_directory>/group_vars`

Then create these files, to have variables specific to managed hosts in either `rhel` or `ubuntu`:

`<ansible_working_directory>/group_vars/rhel.yml`
content below:
```yaml
text: "I am in group rhel"
```
`<ansible_working_directory>/group_vars/ubuntu.yml`
content below:
```yaml
text: "I am in group ubuntu"
```

Also, if we wanted host specific variables, we can create the directory;
`<ansible_working_directory>/host_vars`
Then create a host specific variable inside;
`<ansible_working_directory>/host_vars/vm2.yml`
content below:
```yaml
htext: "I am vm2"
```
`<ansible_working_directory>/host_vars/vm3.yml`
content below:
```yaml
htext: "I am vm3"
```

Once these variables are defined, the playbook below can be used to test them, note we don't have to import the variables are the top of the play, the playbook automatically detects them due to the directory structure. 

```yaml
---
- name: Testing host and group vars files
  hosts: all
  tasks:
  - name: Testing group variables
    debug:
      msg: "{{ text }}"
  - name: Testing host variables
    debug:
      msg: "{{ htext }}"
```

Result of running the playbook:

```shell
PLAY [Testing host and group vars files] *********************************************************************************************************************************************************************

TASK [Gathering Facts] ***************************************************************************************************************************************************************************************
ok: [vm3]
ok: [vm2]

TASK [Testing group variables] *******************************************************************************************************************************************************************************
ok: [vm2] => {
    "msg": "I am in group rhel"
}
ok: [vm3] => {
    "msg": " I am in group ubuntu"
}

TASK [Testing host variables] ********************************************************************************************************************************************************************************
ok: [vm2] => {
    "msg": "I am vm2"
}
ok: [vm3] => {
    "msg": "I am vm3"
}

PLAY RECAP ***************************************************************************************************************************************************************************************************
vm2                        : ok=3    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
vm3                        : ok=3    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0

```



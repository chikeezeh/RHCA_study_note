#### Using set_fact

A module for defining a fact in the playbook. The fact is global, so applies to everywhere in the playbook, however the fact can't be used after the playbook is done running. 

Example playbook below:

```yaml
- name: creating a fact
  hosts: vm1
  tasks:
  - name: Setting facts so that they will be persisted in the fact cache
    ansible.builtin.set_fact:
      one_fact: world!
- name: another play
  hosts: vm1
  gather_facts: no
  tasks:
  - name: Testing the facts we set above
    debug:
      msg: Hello {{ one_fact }}
```

Result of playbook:

```shell
 ansible-playbook setfact.yml

PLAY [creating a fact] ***************************************************************************************************************************************************************************************

TASK [Gathering Facts] ***************************************************************************************************************************************************************************************
ok: [vm1]

TASK [Setting facts so that they will be persisted in the fact cache] ****************************************************************************************************************************************
ok: [vm1]

PLAY [another play] ******************************************************************************************************************************************************************************************

TASK [Testing the facts we set above] ************************************************************************************************************************************************************************
ok: [vm1] => {
    "msg": "Hello world!"
}

PLAY RECAP ***************************************************************************************************************************************************************************************************
vm1                        : ok=3    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0

```
Note, the variable will only work on the hosts defined in the play for the `set_fact` module, so using the playbook above, it will fail if we run the second play on `vm2` instead of `vm1`.

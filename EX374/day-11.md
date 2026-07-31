#### Using conditionals contd.

##### `when`
Is used to perform an action only if a specific condition is true, for example we can use `yum/dnf` module when our OS is `RHEL` or `apt` when our OS is `Ubuntu`, and have this as one task. 

To use `when` correctly, we need to refer to the right variable type. For example:

`ansible_machine == "x86_64" ` --> variable is a string
`ansible_memfree_mb == 1024 ` --> variable is an integer
`my_variable is defined` --> variable is a boolean
`my_variable` --> variable is Boolean True

When using comparisons, variable types are important, to prevent errors, use filters to cast the variable type. See example below;

`when vgsive | int > 5` This makes sure the `vgsize` variable is an integer before comparing it to the number 5. 
`when runme | bool` This makes sure that `runme` is treated as a boolean and it checked to see if it returns `True`.

Example playbook that uses the when conditional;

```yaml
---
- name: testing the when conditionals
  hosts: rhel,ubuntu
  tasks:
  - name: This should only run on rhel
    debug:
      msg: I am a RedHat system
    when: ansible_facts['distribution'] == "RedHat"
  - name: This should only run on ubuntu
    debug:
      msg: I am an Ubuntu system
    when: ansible_facts['distribution']  == "Ubuntu"

```
Output:

```shell
[ansible@control ansible_work]$ ansible-playbook testwhen.yml

PLAY [testing the when conditionals] *************************************************************************************************************************************************************************

TASK [Gathering Facts] ***************************************************************************************************************************************************************************************
ok: [vm4]
ok: [vm1]
ok: [vm2]
ok: [vm3]

TASK [This should only run on rhel] **************************************************************************************************************************************************************************
ok: [vm1] => {
    "msg": "I am a RedHat system"
}
ok: [vm2] => {
    "msg": "I am a RedHat system"
}
ok: [vm3] => {
    "msg": "I am a RedHat system"
}
skipping: [vm4]

TASK [This should only run on ubuntu] ************************************************************************************************************************************************************************
skipping: [vm1]
skipping: [vm2]
skipping: [vm3]
ok: [vm4] => {
    "msg": "I am an Ubuntu system"
}

PLAY RECAP ***************************************************************************************************************************************************************************************************
vm1                        : ok=2    changed=0    unreachable=0    failed=0    skipped=1    rescued=0    ignored=0
vm2                        : ok=2    changed=0    unreachable=0    failed=0    skipped=1    rescued=0    ignored=0
vm3                        : ok=2    changed=0    unreachable=0    failed=0    skipped=1    rescued=0    ignored=0
vm4                        : ok=2    changed=0    unreachable=0    failed=0    skipped=1    rescued=0    ignored=0
```

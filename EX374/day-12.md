#### Using multiple conditions for when.

Like all programming languages, ansible allows for the use of multiple conditionals, using `or`, `and`, and `not`. Example below:

```yaml
---
- name: testing multiple when conditionals
  hosts: rhel,ubuntu
  tasks:
  - name: This should only run if a host is RHEL and x86_64
    debug:
      msg: I am a {{ ansible_facts['distribution'] }} running on {{ ansible_facts['machine'] }} machine.
    when: ansible_facts['distribution'] == "RedHat" and
      ansible_facts['machine'] == "x86_64"
  - name: This should run if either Ubuntu or x86_64
    debug:
      msg: I am a {{ ansible_facts['distribution'] }} running on {{ ansible_facts['machine'] }} machine.
    when: ansible_facts['distribution']  == "Ubuntu" or
      ansible_facts['machine'] == "x86_64"
```
Output:

```shell
[ansible@control ansible_work]$ ansible-playbook multiwhen.yml

PLAY [testing multiple when conditionals] ********************************************************************************************************************************************************************

TASK [Gathering Facts] ***************************************************************************************************************************************************************************************
ok: [vm4]
ok: [vm3]
ok: [vm2]
ok: [vm1]

TASK [This should only run if a host is RHEL and x86_64] *****************************************************************************************************************************************************
ok: [vm1] => {
    "msg": "I am a RedHat running on x86_64 machine."
}
ok: [vm2] => {
    "msg": "I am a RedHat running on x86_64 machine."
}
ok: [vm3] => {
    "msg": "I am a RedHat running on x86_64 machine."
}
skipping: [vm4]

TASK [This should run if either Ubuntu or x86_64] ************************************************************************************************************************************************************
ok: [vm1] => {
    "msg": "I am a RedHat running on x86_64 machine."
}
ok: [vm2] => {
    "msg": "I am a RedHat running on x86_64 machine."
}
ok: [vm3] => {
    "msg": "I am a RedHat running on x86_64 machine."
}
ok: [vm4] => {
    "msg": "I am a Ubuntu running on x86_64 machine."
}

PLAY RECAP ***************************************************************************************************************************************************************************************************
vm1                        : ok=3    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
vm2                        : ok=3    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
vm3                        : ok=3    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
vm4                        : ok=2    changed=0    unreachable=0    failed=0    skipped=1    rescued=0    ignored=0

```
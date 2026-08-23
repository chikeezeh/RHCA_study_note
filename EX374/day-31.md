#### Using the `env` lookup plugin
The `env` is used in combination with the `default` plugin to access a shell variable on the control host. 

Example below:

```yaml
---
- name: test using environment variables.
  hosts: vm1, vm2
  tasks:
  - name: show the control node hostname
    debug:
      msg: "My control node is {{ lookup('env', 'HOSTNAME') }}"
```

Output:

```shell
[ansible@control ansible_work]$ ansible-playbook envtest.yml

PLAY [test using environment variables.] *********************************************************************************************************************************************************************

TASK [Gathering Facts] ***************************************************************************************************************************************************************************************
ok: [vm2]
ok: [vm1]

TASK [show the control node hostname] ************************************************************************************************************************************************************************
ok: [vm1] => {
    "msg": "My control node is control.cezeh.lab"
}
ok: [vm2] => {
    "msg": "My control node is control.cezeh.lab"
}

PLAY RECAP ***************************************************************************************************************************************************************************************************
vm1                        : ok=2    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
vm2                        : ok=2    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
```

#### Using the `fileglob` lookup plugin

Used to iterate over a list of files according to a globbing pattern. See example below;

```yaml
---
- name: testing fileglob plugin
  hosts: vm1, vm2
  tasks:
  - name: show file in a path
    debug:
      msg: "These are the files: {{ query('fileglob', '/home/ansible/ansible_work/f*.yml')  }}"

```

Output:

```shell
[ansible@control ansible_work]$ ansible-playbook fileglob.yml

PLAY [testing fileglob plugin] *******************************************************************************************************************************************************************************

TASK [Gathering Facts] ***************************************************************************************************************************************************************************************
ok: [vm2]
ok: [vm1]

TASK [show file in a path] ***********************************************************************************************************************************************************************************
ok: [vm1] => {
    "msg": "These are the files: ['/home/ansible/ansible_work/fail.yml', '/home/ansible/ansible_work/findfiles.yml', '/home/ansible/ansible_work/fileglob.yml', '/home/ansible/ansible_work/factnot.yml', '/home/ansible/ansible_work/failure.yml']"
}
ok: [vm2] => {
    "msg": "These are the files: ['/home/ansible/ansible_work/fail.yml', '/home/ansible/ansible_work/findfiles.yml', '/home/ansible/ansible_work/fileglob.yml', '/home/ansible/ansible_work/factnot.yml', '/home/ansible/ansible_work/failure.yml']"
}

PLAY RECAP ***************************************************************************************************************************************************************************************************
vm1                        : ok=2    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
vm2                        : ok=2    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0

```


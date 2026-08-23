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


#### Securing ssh connections.

To better secure our ssh connections, we can add a passphrase to our ssh key when we generate it. However, this means we will need to put in that passphrase everytime ansible runs.
Use the following steps to cache the ssh passphrase to the current shell connection using ssh-agent;
- `ssh-keygen` to generate ssh keys, add a passphrase
- `eval ssh-agent $SHELL` to start ssh-agent
- `ssh-add ~/.ssh/id_rsa` to add your identity to the agent.

#### Understanding Tags

A tag is a label that can be use in a playbook to identify specific playbook elements.

Using tags, you can run specific parts of the playbook only by using `ansible-playbook --tag=xxx`

Example playbook.

```yaml
---
- name: showing tag demo
  hosts: vm1
  tasks:
  - name: this is task 1
    debug:
      msg: this is task 1
    tags: first
  - name: this is task 2
    debug:
      msg: this is task 2
    tags: second
```
Running playbook without any tag flags;

```shell
[ansible@control ansible_work]$ ansible-playbook tagdemo.yml

PLAY [showing tag demot] *****************************************************************************************************************************************************************************

TASK [Gathering Facts] *******************************************************************************************************************************************************************************
ok: [vm1]

TASK [this is task 1] ********************************************************************************************************************************************************************************
ok: [vm1] => {
    "msg": "this is task 1"
}

TASK [this is task 2] ********************************************************************************************************************************************************************************
ok: [vm1] => {
    "msg": "this is task 2"
}

PLAY RECAP *******************************************************************************************************************************************************************************************
vm1                        : ok=3    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0

[ansible@control ansible_work]$ vim tagdemo.yml
[ansible@control ansible_work]$ vim tagdemo.yml
[ansible@control ansible_work]$ ansible-playbook tagdemo.yml

PLAY [showing tag demo] ******************************************************************************************************************************************************************************

TASK [Gathering Facts] *******************************************************************************************************************************************************************************
ok: [vm1]

TASK [this is task 1] ********************************************************************************************************************************************************************************
ok: [vm1] => {
    "msg": "this is task 1"
}

TASK [this is task 2] ********************************************************************************************************************************************************************************
ok: [vm1] => {
    "msg": "this is task 2"
}

PLAY RECAP *******************************************************************************************************************************************************************************************
vm1                        : ok=3    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
```

Running with `ansible-playbook --tag=first`

```shell
[ansible@control ansible_work]$ ansible-playbook tagdemo.yml --tag=first

PLAY [showing tag demo] ******************************************************************************************************************************************************************************

TASK [Gathering Facts] *******************************************************************************************************************************************************************************
ok: [vm1]

TASK [this is task 1] ********************************************************************************************************************************************************************************
ok: [vm1] => {
    "msg": "this is task 1"
}

PLAY RECAP *******************************************************************************************************************************************************************************************
vm1                        : ok=2    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
```


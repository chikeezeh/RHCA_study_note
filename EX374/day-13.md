#### Using `block`, `rescue` and `always`

Block, rescue, and always are like try, except, and finally blocks in python. Block is the main task(s) to execute, the tasks in rescue only runs when a failure occurs in the block task, and always will always run. 
Example playbook below:

```yaml
---
- name: testing block, rescue, and always
  hosts: vm1
  tasks:
  - name: This is block one
    block:
    - name: this will always run
      debug:
        msg: I will run
    - name: I will fail
      copy:
        src: /tmp/nofilefound
        dest: /tmp/nofilehere
    rescue:
    - name: I am coming to the rescue
      debug:
        msg: I am the rescue task
    always: I will always run
    - name:
      debug:
        msg: I am the last result
  - name: I am a task outside the block
    debug:
      msg: I am outside block one

```
Output:
```shell
[ansible@control ansible_work]$ ansible-playbook blockexample.yml

PLAY [testing block, rescue, and always] *********************************************************************************************************************************************************************

TASK [Gathering Facts] ***************************************************************************************************************************************************************************************
ok: [vm1]

TASK [this will always run] **********************************************************************************************************************************************************************************
ok: [vm1] => {
    "msg": "I will run"
}

TASK [I will fail] *******************************************************************************************************************************************************************************************
An exception occurred during task execution. To see the full traceback, use -vvv. The error was: If you are using a module and expect the file to exist on the remote, see the remote_src option
fatal: [vm1]: FAILED! => {"changed": false, "msg": "Could not find or access '/tmp/nofilefound' on the Ansible Controller.\nIf you are using a module and expect the file to exist on the remote, see the remote_src option"}

TASK [I am coming to the rescue] *****************************************************************************************************************************************************************************
ok: [vm1] => {
    "msg": "I am the rescue task"
}

TASK [I will always run] *************************************************************************************************************************************************************************************************
ok: [vm1] => {
    "msg": "I am the last result"
}

TASK [I am a task outside the block] *************************************************************************************************************************************************************************
ok: [vm1] => {
    "msg": "I am outside block one"
}

PLAY RECAP ***************************************************************************************************************************************************************************************************
vm1                        : ok=5    changed=0    unreachable=0    failed=0    skipped=0    rescued=1    ignored=0

```

#### Using `block`, `rescue`, and `always` with `when` conditionals.
Blocks can be used with conditionals for more conditional logic. Example below:
```yaml
---
- name: testing block, rescue, and always
  hosts: rhel,ubuntu
  tasks:
  - name: This is block one
    block:
    - name: this will always run
      debug:
        msg: I will run
    - name: I will fail
      copy:
        src: /tmp/nofilefound
        dest: /tmp/nofilehere
    rescue:
    - name: I am coming to the rescue
      debug:
        msg: I am the rescue task
    always:
    - name: I will always run
      debug:
        msg: I am the last result
    when: ansible_facts['distribution'] == "RedHat"
  - name: I am a task outside the block
    debug:
      msg: I am outside block one
```
Output:


<details>
<summary> Click to expand </summary>

```shell
[ansible@control ansible_work]$ ansible-playbook blockexample2.yml

PLAY [testing block, rescue, and always] *********************************************************************************************************************************************************************

TASK [Gathering Facts] ***************************************************************************************************************************************************************************************
ok: [vm4]
ok: [vm1]
ok: [vm2]
ok: [vm3]

TASK [this will always run] **********************************************************************************************************************************************************************************
ok: [vm1] => {
    "msg": "I will run"
}
ok: [vm2] => {
    "msg": "I will run"
}
ok: [vm3] => {
    "msg": "I will run"
}
skipping: [vm4]

TASK [I will fail] *******************************************************************************************************************************************************************************************
skipping: [vm4]
An exception occurred during task execution. To see the full traceback, use -vvv. The error was: If you are using a module and expect the file to exist on the remote, see the remote_src option
fatal: [vm1]: FAILED! => {"changed": false, "msg": "Could not find or access '/tmp/nofilefound' on the Ansible Controller.\nIf you are using a module and expect the file to exist on the remote, see the remote_src option"}
An exception occurred during task execution. To see the full traceback, use -vvv. The error was: If you are using a module and expect the file to exist on the remote, see the remote_src option
fatal: [vm2]: FAILED! => {"changed": false, "msg": "Could not find or access '/tmp/nofilefound' on the Ansible Controller.\nIf you are using a module and expect the file to exist on the remote, see the remote_src option"}
An exception occurred during task execution. To see the full traceback, use -vvv. The error was: If you are using a module and expect the file to exist on the remote, see the remote_src option
fatal: [vm3]: FAILED! => {"changed": false, "msg": "Could not find or access '/tmp/nofilefound' on the Ansible Controller.\nIf you are using a module and expect the file to exist on the remote, see the remote_src option"}

TASK [I am coming to the rescue] *****************************************************************************************************************************************************************************
ok: [vm1] => {
    "msg": "I am the rescue task"
}
ok: [vm2] => {
    "msg": "I am the rescue task"
}
ok: [vm3] => {
    "msg": "I am the rescue task"
}

TASK [I will always run] *************************************************************************************************************************************************************************************************
ok: [vm1] => {
    "msg": "I am the last result"
}
ok: [vm2] => {
    "msg": "I am the last result"
}
ok: [vm3] => {
    "msg": "I am the last result"
}
skipping: [vm4]

TASK [I am a task outside the block] *************************************************************************************************************************************************************************
ok: [vm1] => {
    "msg": "I am outside block one"
}
ok: [vm2] => {
    "msg": "I am outside block one"
}
ok: [vm3] => {
    "msg": "I am outside block one"
}
ok: [vm4] => {
    "msg": "I am outside block one"
}

PLAY RECAP ***************************************************************************************************************************************************************************************************
vm1                        : ok=5    changed=0    unreachable=0    failed=0    skipped=0    rescued=1    ignored=0
vm2                        : ok=5    changed=0    unreachable=0    failed=0    skipped=0    rescued=1    ignored=0
vm3                        : ok=5    changed=0    unreachable=0    failed=0    skipped=0    rescued=1    ignored=0
vm4                        : ok=2    changed=0    unreachable=0    failed=0    skipped=3    rescued=0    ignored=0

```
</details>

Note that if you want the `when` conditional to apply to the `block`, `rescue`, and `always` tasks as shown above, the conditionals needs to be at the same indentation as the `block` task.

To have a conditional only apply to `always` move the conditional inside of the `always` task one more indentation level.

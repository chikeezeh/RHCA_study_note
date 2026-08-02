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
    always:
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

TASK [debug] *************************************************************************************************************************************************************************************************
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
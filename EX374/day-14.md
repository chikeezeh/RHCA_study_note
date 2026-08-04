#### Using the `fail` module.

A non zero exit code indicates that a task has failed on a host, if a task fails on a host, ansible stops the rest of the play execution on that host and moves to the next host. 

Errors can be handled using various methods;
- The `block`, `rescue`, and `always` as discussed above.
- Using `ignore_erros: yes` in the task or play to ignore errors.
- Using `force_handlers: yes` to make sure a handler runs even if an error occurs in the notifying task. Note `ignore_erros: yes` supercedes `force_handlers: no` if both are used together, meaning the `handler` will still run even if the `notify` task fails because we are ignoring errors. 

Furthermore, we can use `failed_when` to determine when a failure has occurred, this is useful when a task executes, but an output of the task determines if we passed or not. The `fail` module can used to show an error message based on the `register` variable, also `ignore_errors: yes` has to be set for the play to continue.

Example below:
```yaml
---
- name: testing failed when
  hosts: vm1
  tasks:
  - name: use command to show failure
    command: echo hello world
    register: command_result
    failed_when: "'world' in command_result.stdout"
```
Output:

```shell
ansible-playbook failure.yml

PLAY [testing failed when] *************************************************************************************************************************************************

TASK [Gathering Facts] *****************************************************************************************************************************************************
ok: [vm1]

TASK [use command to show failure] *****************************************************************************************************************************************
fatal: [vm1]: FAILED! => {"changed": true, "cmd": ["echo", "hello", "world"], "delta": "0:00:00.001769", "end": "2026-08-03 19:06:33.402341", "failed_when_result": true, "msg": "", "rc": 0, "start": "2026-08-03 19:06:33.400572", "stderr": "", "stderr_lines": [], "stdout": "hello world", "stdout_lines": ["hello world"]}

PLAY RECAP *****************************************************************************************************************************************************************
vm1                        : ok=1    changed=0    unreachable=0    failed=1    skipped=0    rescued=0    ignored=0
```


```yaml
--
- name: testing failed when
  hosts: vm1
  tasks:
  - name: use command to show failure
    command: echo hello world
    register: command_result
  - name: report a failure
    fail:
      msg: a failure has occured
    when: "'world' in command_result.stdout"
```
Output:

```shell
ansible-playbook fail.yml 

PLAY [testing failed when] *************************************************************************************************************************************************

TASK [Gathering Facts] *****************************************************************************************************************************************************
ok: [vm1]

TASK [use command to show failure] *****************************************************************************************************************************************
changed: [vm1]

TASK [report a failure] ****************************************************************************************************************************************************
fatal: [vm1]: FAILED! => {"changed": false, "msg": "a failure has occured"}

PLAY RECAP *****************************************************************************************************************************************************************
vm1                        : ok=2    changed=1    unreachable=0    failed=1    skipped=0    rescued=0    ignored=0 
```

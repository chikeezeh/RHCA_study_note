#### Delegate tasks - Drills

The drills that will follow will test the following exam objectives.

- Run a task for a managed host on a different host
- Control whether facts gathered by a task are delegated to the managed host or the controlling host.


###### Task 1 — delegate_to. 

Write a play targeting web. For each host, delegate a task to localhost that appends one line — the managed host's inventory hostname — to /tmp/delegation-report.txt on the control node. Prove: the file exists on tower (not on the web hosts) with one line per web host.

<details>
<summary>Click to see solution </summary>

See the playbook for this question below:

```yaml
---
- name: drill on how to delegate a task
  hosts: web
  tasks:
  - name: Write the inventory hostname to a file
    ansible.builtin.lineinfile:
      path: /tmp/delegation-report.txt
      line: "{{ inventory_hostname }}"
      create: yes
    delegate_to: localhost
    throttle: 1
```
Note used `throttle:1` to make sure ansible only runs the playbook one host at a time, so that each run doesn't overwrite each other.

```shell
[ansible@tower ansible-lab (main %)]$ ansible-playbook delegation.yml

PLAY [drill on how to delegate a task] ***********************************************************************************************************************************************************************

TASK [Gathering Facts] ***************************************************************************************************************************************************************************************
ok: [frontend-1]
ok: [frontend-2]

TASK [Write the inventory hostname to a file] ****************************************************************************************************************************************************************
changed: [frontend-1 -> localhost]
changed: [frontend-2 -> localhost]

PLAY RECAP ***************************************************************************************************************************************************************************************************
frontend-1                 : ok=2    changed=1    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
frontend-2                 : ok=2    changed=1    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0

```

</details>


###### Task 2 — run_once + local_action. 

In a play targeting web, add a task using local_action (or delegate_to: localhost + run_once) that writes the current date/time to /tmp/deploy-stamp.txt. It must execute exactly once despite multiple target hosts. Prove: the playbook output shows it ran a single time, and the file contains one timestamp.

<details>
<summary>Click to see solution </summary>

See the playbook content below:

```yaml
---
- name: run once the current date/time to a file
  hosts: web
  tasks:
  - name: get the current date/time
    copy:
      dest: /tmp/deploy-stamp.txt
      content: "{{ ansible_date_time.iso8601 }}"
    delegate_to: localhost
    run_once: true

```
See playbook output below:

```shell
[ansible@tower ansible-lab (main %)]$ ansible-playbook run_once.yml

PLAY [run once the current date/time to a file] **************************************************************************************************************************************************************

TASK [Gathering Facts] ***************************************************************************************************************************************************************************************
ok: [frontend-1]
ok: [frontend-2]

TASK [get the current date/time] *****************************************************************************************************************************************************************************
changed: [frontend-1 -> localhost]

PLAY RECAP ***************************************************************************************************************************************************************************************************
frontend-1                 : ok=2    changed=1    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
frontend-2                 : ok=1    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0

```


</details>
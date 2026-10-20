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

###### Task 3 — block / rescue / always. 

Write a play with a block that attempts something guaranteed to fail (e.g., copying a nonexistent file, or installing a package named definitely-not-real). The rescue section must write /tmp/rescue-fired.txt with a one-line explanation; the always section must append a completion line to /tmp/block-log.txt. Prove: output shows the rescue task executing, both files exist with the expected content, and the play does not report failure.

<details>
<summary>Click to see solution</summary>

See playbook below for solution to this task:

```yaml
---
- name: drill that uses block, rescue, always
  hosts: localhost
  gather_facts: false
  become: true
  vars:
    app: definitely-not-real
  tasks:
  - name: main block
    block:
    - name: this task is going to fail
      dnf:
        name: "{{ app }}"
        state: present
    rescue:
    - name: write a rescue text
      copy:
        dest: /tmp/rescure-fired.txt
        content: "Can't install application {{ app }}, because it doesn't exist"
        force: false
    always:
    - name: Write completion task
      copy:
        dest: /tmp/block-log.txt
        content: "Block 1 completed"
        force: false

```

Output:

```shell
[ansible@tower ansible-lab (main %)]$ ansible-playbook blockrescue.yml

PLAY [drill that uses block, rescue, always] *****************************************

TASK [this task is going to fail] ****************************************************
fatal: [localhost]: FAILED! => {"changed": false, "failures": ["No package definitely-                      not-real available."], "msg": "Failed to install some of the specified packages", "rc"                      : 1, "results": []}

TASK [write a rescue text] ***********************************************************
ok: [localhost]

TASK [Write completion task] *********************************************************
ok: [localhost]

PLAY RECAP ***************************************************************************
localhost                  : ok=2    changed=0    unreachable=0    failed=0    skipped                      =0    rescued=1    ignored=0

```

</details>


###### Task 4 — failed_when. 

`grep` exits 1 when the pattern isn't found — normally that fails the task. Write a play that greps for a string in /tmp/app.conf (create the file first), registers the result, and uses failed_when so the task only fails on a real error (rc ≥ 2), not on "no match". Debug whether the pattern was found. Prove: two runs — one where the pattern exists, one where it doesn't — neither failing, with correct "found / not found" reporting.


<details>
<summary>Click to see solution</summary>

See the content of the playbook below.

```yaml
---
- name: Use failed when to control failure
  hosts: localhost
  gather_facts: false
  tasks:
  - name: run a shell grep command
    shell:
      cmd: grep foundme /tmp/app.conf
    register: grep_output
    failed_when: grep_output.rc >= 2
  - name: run when pattern is found
    debug:
      msg: "Pattern found"
    when: grep_output.rc == 0
  - name: run when pattern is not found
    debug:
      msg: "Pattern not found"
    when: grep_output.rc != 0

```

See output of the playbook below.
```shell
[ansible@tower ansible-lab (main %)]$ ansible-playbook failedwhen.yml

PLAY [Use failed when to control failure] ********************************************

TASK [run a shell grep command] ******************************************************
changed: [localhost]

TASK [run when pattern is found] *****************************************************
ok: [localhost] => {
    "msg": "Pattern found"
}

PLAY RECAP ***************************************************************************
localhost                  : ok=2    changed=1    unreachable=0    failed=0    skipped                      =0    rescued=0    ignored=0


[ansible@tower ansible-lab (main %)]$ ansible-playbook failedwhen.yml

PLAY [Use failed when to control failure] ********************************************

TASK [run a shell grep command] ******************************************************
changed: [localhost]

TASK [run when pattern is found] *****************************************************
skipping: [localhost]

TASK [run when pattern is not found] *************************************************
ok: [localhost] => {
    "msg": "Pattern not found"
}

PLAY RECAP ***************************************************************************
localhost                  : ok=2    changed=1    unreachable=0    failed=0    skipped                      =1    rescued=0    ignored=0
```
</details>

###### Task 5 — changed_when. 

Write a play with two command/shell tasks: one that only reads system state (e.g., uptime) and must never report changed; one that appends a marker line to /tmp/marker.log only if it's absent, and must report changed exactly when it actually wrote. Prove: first run shows one changed task, rerun shows changed=0.

<details>
<summary>Click to see solution</summary>

See Playbook below:

```yaml
---
- name: Drill on how to use changed_when
  hosts: localhost
  gather_facts: false
  tasks:
  - name: get uptime using command module
    ansible.builtin.command:
      cmd: uptime
    register: cmdoutput
    changed_when: false
  - name: create an empty file
    ansible.builtin.copy:
      dest: /tmp/marker.log
      content: ""
      force: false
  - name: create a marker in the file
    ansible.builtin.shell:
      cmd: |
        if ! grep -qF 'DEPLOYED' /tmp/marker.log; then
          echo 'DEPLOYED' > /tmp/marker.log
          echo "CHANGED"
        fi
    register: shelloutput
    changed_when: "'CHANGED' in shelloutput.stdout"
  - name: show shelloutput output
    debug:
      var: shelloutput
```

See shell output when the playbook is ran, notice that on the second run, the shell module doesn't show changed since it didn't write to the file.

```shell
[ansible@tower ansible-lab (main %)]$ ansible-playbook changedwhen.yml

PLAY [Drill on how to use changed_when] **********************************************

TASK [get uptime using command module] ***********************************************
ok: [localhost]

TASK [create an empty file] **********************************************************
changed: [localhost]

TASK [create a marker in the file] ***************************************************
changed: [localhost]

TASK [show shelloutput output] *******************************************************
ok: [localhost] => {
    "shelloutput": {
        "changed": true,
        "cmd": "if ! grep -qF 'DEPLOYED' /tmp/marker.log; then\n  echo 'DEPLOYED' > /tmp/marker.log\n  echo \"CHANGED\"\nfi\n",
        "delta": "0:00:00.003683",
        "end": "2026-10-20 08:22:09.682294",
        "failed": false,
        "msg": "",
        "rc": 0,
        "start": "2026-10-20 08:22:09.678611",
        "stderr": "",
        "stderr_lines": [],
        "stdout": "CHANGED",
        "stdout_lines": [
            "CHANGED"
        ]
    }
}

PLAY RECAP ***************************************************************************
localhost                  : ok=4    changed=2    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0

[ansible@tower ansible-lab (main %)]$ ansible-playbook changedwhen.yml

PLAY [Drill on how to use changed_when] **********************************************

TASK [get uptime using command module] ***********************************************
ok: [localhost]

TASK [create an empty file] **********************************************************
ok: [localhost]

TASK [create a marker in the file] ***************************************************
ok: [localhost]

TASK [show shelloutput output] *******************************************************
ok: [localhost] => {
    "shelloutput": {
        "changed": false,
        "cmd": "if ! grep -qF 'DEPLOYED' /tmp/marker.log; then\n  echo 'DEPLOYED' > /tmp/marker.log\n  echo \"CHANGED\"\nfi\n",
        "delta": "0:00:00.003862",
        "end": "2026-10-20 08:22:12.807596",
        "failed": false,
        "msg": "",
        "rc": 0,
        "start": "2026-10-20 08:22:12.803734",
        "stderr": "",
        "stderr_lines": [],
        "stdout": "",
        "stdout_lines": []
    }
}

PLAY RECAP ***************************************************************************
localhost                  : ok=4    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0

```


</details>
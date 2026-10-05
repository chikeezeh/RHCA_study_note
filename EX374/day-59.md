#### Manage task execution - drills

The drills to follow will cover the following exam objectives. 

- Control privilege escalation
- Run selected tasks from a playbook

We will be practicing the following Ansible concepts.
- `become_user`
- `become`
- `become_method`
- `tags`, specifically `--tags` and `--skip-tags`
  
###### Task 1 — the tagged playbook. 
Write site.yml for your `web` group (frontend-1, frontend-2) with three tags: `package`(install `httpd` — needs become), `config` (write a file to /etc/httpd/conf.d/custom.conf — needs become), `service` (start and enable httpd — needs become). Before running anything, write down which tasks you expect from each of these: `--tags package` / `--tags config,service` / `--skip-tags service` / no flags at all. Then run all four and compare against your predictions. Helpers: --list-tags and --list-tasks.

Solution:

<details>
  <summary>Click to reveal Solution</summary>

Utilizing the setup from [day-58](./day-58.md), below are the variables that one of the managed nodes in the `web` group has access to.

```shell
[ansible@tower drill2]$ ansible-inventory -i inventory --host frontend-2 --yaml
admin_email: ansible@cezeh.lab
ansible_host: vm2
firewall_enabled: true
http_port: 8080
max_workers: 4
ntp_server: 1.1.1.1
ssh_max_auth_tries: 4
web_packages:
- httpd
- firewalld

```

Site [site.yml](../playbooks/site.yml) for the solution playbook, see below from the test using the tags.

```shell
[ansible@tower drill2]$ ansible-playbook -i inventory site.yml --tags package

PLAY [using tags to control playbook tasks] ******************************************************************************************************************************************************************

TASK [Gathering Facts] ***************************************************************************************************************************************************************************************
ok: [frontend-1]
ok: [frontend-2]

TASK [install httpd] *****************************************************************************************************************************************************************************************
changed: [frontend-1]
changed: [frontend-2]

PLAY RECAP ***************************************************************************************************************************************************************************************************
frontend-1                 : ok=2    changed=1    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
frontend-2                 : ok=2    changed=1    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0

[ansible@tower drill2]$ ansible-playbook -i inventory site.yml --tags config,service

PLAY [using tags to control playbook tasks] ******************************************************************************************************************************************************************

TASK [Gathering Facts] ***************************************************************************************************************************************************************************************
ok: [frontend-2]
ok: [frontend-1]

TASK [configure httpd] ***************************************************************************************************************************************************************************************
changed: [frontend-1]
changed: [frontend-2]

TASK [start and enable httpd] ********************************************************************************************************************************************************************************
changed: [frontend-2]
changed: [frontend-1]

PLAY RECAP ***************************************************************************************************************************************************************************************************
frontend-1                 : ok=3    changed=2    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
frontend-2                 : ok=3    changed=2    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0

[ansible@tower drill2]$ ansible-playbook -i inventory site.yml --skip-tags service

PLAY [using tags to control playbook tasks] ******************************************************************************************************************************************************************

TASK [Gathering Facts] ***************************************************************************************************************************************************************************************
ok: [frontend-1]
ok: [frontend-2]

TASK [install httpd] *****************************************************************************************************************************************************************************************
ok: [frontend-2]
ok: [frontend-1]

TASK [configure httpd] ***************************************************************************************************************************************************************************************
ok: [frontend-1]
ok: [frontend-2]

PLAY RECAP ***************************************************************************************************************************************************************************************************
frontend-1                 : ok=3    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
frontend-2                 : ok=3    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0

```


</details>


###### Task 2 — become_user. 

Add a task tagged `identity` that runs `whoami` with `become: true` and `become_user: nobody`, registers the output, and debugs it. Add a second task tagged `filetest` that creates `/tmp/nobody-test.txt` as `become_user: nobody`. Verify: the debug output says nobody, and the file on the host is owned by nobody. 

<details>

<summary>Click to reveal Solution</summary>

The two tasks are in [task2.yml](../playbooks/tasks2.yml) playbook. See the output of running the playbook below.

```shell
ansible-playbook -i inventory task2.yml

PLAY [testing become user] ***********************************************************************************************************************************************************************************

TASK [Gathering Facts] ***************************************************************************************************************************************************************************************
ok: [vm3]

TASK [first task, running whoami] ****************************************************************************************************************************************************************************
[WARNING]: Unable to use /.ansible/tmp as temporary directory, failing back to system: [Errno 13] Permission denied: '/.ansible'
changed: [vm3]

TASK [show output] *******************************************************************************************************************************************************************************************
ok: [vm3] => {
    "output": {
        "changed": true,
        "cmd": "whoami",
        "delta": "0:00:00.003150",
        "end": "2026-10-03 16:49:52.058903",
        "failed": false,
        "msg": "",
        "rc": 0,
        "start": "2026-10-03 16:49:52.055753",
        "stderr": "",
        "stderr_lines": [],
        "stdout": "nobody",
        "stdout_lines": [
            "nobody"
        ],
        "warnings": [
            "Unable to use /.ansible/tmp as temporary directory, failing back to system: [Errno 13] Permission denied: '/.ansible'"
        ]
    }
}

TASK [second task, file test] ********************************************************************************************************************************************************************************
changed: [vm3]

PLAY RECAP ***************************************************************************************************************************************************************************************************
vm3 
```

Verify that the owner of the created file is nobody.

```shell
[ansible@tower drill2]$ ansible vm3 -i inventory -m shell -a "ls -l /tmp/nobody-test.txt"
vm3 | CHANGED | rc=0 >>
-rw-r--r--. 1 nobody nobody 0 Oct  3 16:49 /tmp/nobody-test.txt
```


</details>

###### Task 3 — capstone

Write deploy.yml with two plays. Play 1 (tag users): create a user called deployer (needs become). Play 2 (tag appfiles): with become_user: deployer, create /tmp/deployer-proof.txt. Run --tags users first, then --tags appfiles, and prove the file is owned by deployer. Then run the whole playbook again with --skip-tags users — it must still succeed, because the user already exists. Tags + become + become_user + idempotency, all in one run.

<details>

<summary>Click to reveal Solution</summary>

See [deploy.yml](../playbooks/deploy.yml) for solution to the capstone question. Below is the console output from running the playbook. 

```shell
[ansible@tower drill2]$ ansible-playbook -i inventory deploy.yml --tags users

PLAY [Create a user] *****************************************************************************************************************************************************************************************

TASK [Gathering Facts] ***************************************************************************************************************************************************************************************
ok: [vm3]

TASK [Create the user deployer] ******************************************************************************************************************************************************************************
changed: [vm3]

PLAY [Create app files] **************************************************************************************************************************************************************************************

PLAY RECAP ***************************************************************************************************************************************************************************************************
vm3                        : ok=2    changed=1    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0

[ansible@tower drill2]$ ansible-playbook -i inventory deploy.yml --tags appfiles

PLAY [Create a user] *****************************************************************************************************************************************************************************************

PLAY [Create app files] **************************************************************************************************************************************************************************************

TASK [Gathering Facts] ***************************************************************************************************************************************************************************************
[WARNING]: Module remote_tmp /home/deployer/.ansible/tmp did not exist and was created with a mode of 0700, this may cause issues when running as another user. To avoid this, create the remote_tmp dir with
the correct permissions manually
ok: [vm3]

TASK [create a file] *****************************************************************************************************************************************************************************************
changed: [vm3]

PLAY RECAP ***************************************************************************************************************************************************************************************************
vm3                        : ok=2    changed=1    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0

[ansible@tower drill2]$ ansible-playbook -i inventory deploy.yml --skip-tags users

PLAY [Create a user] *****************************************************************************************************************************************************************************************

PLAY [Create app files] **************************************************************************************************************************************************************************************

TASK [Gathering Facts] ***************************************************************************************************************************************************************************************
ok: [vm3]

TASK [create a file] *****************************************************************************************************************************************************************************************
changed: [vm3]

PLAY RECAP ***************************************************************************************************************************************************************************************************
vm3                        : ok=2    changed=1    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0

[ansible@tower drill2]$ ansible vm3 -i inventory -m shell -a "ls -l /tmp/deployer-proof.txt"
vm3 | CHANGED | rc=0 >>
-rw-r--r--. 1 deployer deployer 0 Oct  5 09:29 /tmp/deployer-proof.txt

```

</details>
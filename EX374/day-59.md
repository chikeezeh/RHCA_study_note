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
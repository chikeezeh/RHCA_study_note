#### Roles continued.

Playbook example that uses the geerlingguy nginx role, to install and configure nginx on vm1.


```yaml
---
- name: use galaxy nginx role
  hosts: vm1
  roles:
    - role: geerlingguy.nginx
```
Output:
<details>
<summary> Click to expand </summary>

```shell
ansible@control:~/ansible_work$ ansible-playbook nginx-role.yml 

PLAY [use galaxy nginx role] ***********************************************************************************************************************************************

TASK [Gathering Facts] *****************************************************************************************************************************************************
ok: [vm1]

TASK [geerlingguy.nginx : Include OS-specific variables.] ******************************************************************************************************************
ok: [vm1]

TASK [geerlingguy.nginx : Define nginx_user.] ******************************************************************************************************************************
ok: [vm1]

TASK [geerlingguy.nginx : include_tasks] ***********************************************************************************************************************************
included: /home/ansible/.ansible/roles/geerlingguy.nginx/tasks/setup-RedHat.yml for vm1

TASK [geerlingguy.nginx : Enable nginx repo.] ******************************************************************************************************************************
ok: [vm1]

TASK [geerlingguy.nginx : Ensure nginx is installed.] **********************************************************************************************************************
ok: [vm1]

TASK [geerlingguy.nginx : include_tasks] ***********************************************************************************************************************************
skipping: [vm1]

TASK [geerlingguy.nginx : include_tasks] ***********************************************************************************************************************************
skipping: [vm1]

TASK [geerlingguy.nginx : include_tasks] ***********************************************************************************************************************************
skipping: [vm1]

TASK [geerlingguy.nginx : include_tasks] ***********************************************************************************************************************************
skipping: [vm1]

TASK [geerlingguy.nginx : include_tasks] ***********************************************************************************************************************************
skipping: [vm1]

TASK [geerlingguy.nginx : include_tasks] ***********************************************************************************************************************************
skipping: [vm1]

TASK [geerlingguy.nginx : Remove default nginx vhost config file (if configured).] *****************************************************************************************
skipping: [vm1]

TASK [geerlingguy.nginx : Ensure nginx_vhost_path exists.] *****************************************************************************************************************
ok: [vm1]

TASK [geerlingguy.nginx : Add managed vhost config files.] *****************************************************************************************************************
skipping: [vm1]

TASK [geerlingguy.nginx : Remove managed vhost config files.] **************************************************************************************************************
skipping: [vm1]

TASK [geerlingguy.nginx : Remove legacy vhosts.conf file.] *****************************************************************************************************************
ok: [vm1]

TASK [geerlingguy.nginx : Copy nginx configuration in place.] **************************************************************************************************************
ok: [vm1]

TASK [geerlingguy.nginx : Ensure nginx service is running as configured.] **************************************************************************************************
ok: [vm1]

PLAY RECAP *****************************************************************************************************************************************************************
vm1                        : ok=10   changed=0    unreachable=0    failed=0    skipped=9    rescued=0    ignored=0
```

</details>
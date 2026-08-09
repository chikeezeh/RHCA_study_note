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

#### Structure of a role. 

A role has certain directory structure that it needs to maintain to be able to function correctly. 
The `ansible-galaxy role init <name of role>` command in the directory that the role exists will create the correct directory structure. 

See example below:

```shell
ansible@control:~/ansible_work/roles$ ansible-galaxy role init demorole
```
Below is the structure of the directory created:

```shell
ansible@control:~/ansible_work/roles/demorole$ ls -l
total 4
drwxr-xr-x. 2 ansible ansible   22 Aug  8 18:16 defaults
drwxr-xr-x. 2 ansible ansible    6 Aug  8 18:16 files
drwxr-xr-x. 2 ansible ansible   22 Aug  8 18:16 handlers
drwxr-xr-x. 2 ansible ansible   22 Aug  8 18:16 meta
-rw-r--r--. 1 ansible ansible 1328 Aug  8 18:16 README.md
drwxr-xr-x. 2 ansible ansible   22 Aug  8 18:16 tasks
drwxr-xr-x. 2 ansible ansible    6 Aug  8 18:16 templates
drwxr-xr-x. 2 ansible ansible   39 Aug  8 18:16 tests
drwxr-xr-x. 2 ansible ansible   22 Aug  8 18:16 vars
```
The most important directories are;
- `defaults` default variables that can be changed.
- `vars` variables that shouldn't be changed.
- `tasks` contains the main playbook.
- `templates` Jinja2 templates that are needed by the role going here.
- `handlers` handlers are defined in this directory. 
#### Making a custom role.

We will create a message of the day (motd) custom module, this is based on the example in the ansible documentation.

Inside of the `roles` path run;

`ansible-galaxy role init motd`

Create a template file inside of the templates directory. `vim motd/templates/motd.j2`

Content of template file;

```jinja
Welcome to {{ ansible_hostname }}
This is host is managed by {{ ansible_manager }}
```
The next step is to create the `{{ ansible_manager }}` variable, since it isn't an ansible facts. We will edit the `motd/defaults/main.yml` file.

Content;

```yaml
---
# defaults file for motd
ansible_manager: Chike Ezeh
```

Next step is to edit the main roles file under, `motd/tasks/main.yml`.

This contains the task we want our role to do when called in a playbook. In this simple example, we utilize the template file to write content to the `/etc/motd` file.

Content;

```yaml
---
# tasks file for motd
- name: running motd
  template: 
    src: motd.j2
    dest: /etc/motd
```


Finally, we will use our new custome role in a playbook as shown below;

```yaml
---
- name: This playbook uses the custom motd role we created.
  hosts: vm1
  roles:
    - motd
```

Output:

```shell
ansible-playbook motduse.yml

PLAY [This playbook uses the custom motd role we created.] *****************************************************************************************************************

TASK [Gathering Facts] *****************************************************************************************************************************************************
ok: [vm1]

TASK [motd : running motd] *************************************************************************************************************************************************
changed: [vm1]

PLAY RECAP *****************************************************************************************************************************************************************
vm1                        : ok=2    changed=1    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0

ansible@control:~/ansible_work$ ansible vm1 -a "cat /etc/motd"
vm1 | CHANGED | rc=0 >>
Welcome to vm1
This is host is managed by Chike Ezeh
```






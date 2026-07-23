### Using Ansible Playbooks

#### Ansible Playbook structure
A playbook consist of multiple plays with various tasks that are executed by modules. The `yaml` example below shows the basic outline of a playbook.

```yaml
---
- name: play one
  hosts: the hosts for play one
  tasks: #list of tasks to be conducted, each task will have a name.
    - name: task one
      debug: a module to use
        msg: attribute of the module use, this is a child element
    - name: tasks two
      debug: another module to use
        msg: another attribute
- name: play two
  hosts: the hosts for play two
  tasks:
  - name: tasks one of play two
    debug:
      msg: output text 
```
Use `ansible-playbook <name of playbook>` or `ansible-navigator run <name of playbook>` to run a playbook. 
#### Lab: Using a playbook to deploy a webserver
Question.
Write a playbook that deploys a webserver on vm3, meeting the following requirements.
1. The webserver is installed, started, and enabled.
2. The firewall allows access
3. An index.html file contailing the text "hello world" is copied to /var/www/html


#### Using conditionals
##### `loop`
Used to process a list of items, so instead of writing multiple tasks, you write one tasks that loops over the items and executes the task per item.
See examples below:

###### `loop` without variable.
```yaml
- name: start services
  ansible.builtin.service:
    name: "{{ item }}"
    state: started
  loop:
    - vsftpd
    - httpd
```
###### `loop` using a variable.
```yaml
---
- name: Testing how loops work
  hosts: vm1
  vars:
    my_services:
      - httpd
      - sshd
      - vsftpd
  tasks:
  - name: use debug to print various tasks
    debug:
      msg: "starting service: {{ item }}"
    loop: "{{ my_services }}"

```
###### `loop` using a list of dictionaries.

```yaml
---
- name: Testing how loops with dictionaries work
  hosts: vm1
  vars:
    my_users:
      - name: anna
        groups: wheel
      - name: lisa
        groups: admin
  tasks:
  - name: use debug to print users and groups
    debug:
      msg: "Creating user {{ item.name }} and adding to group {{ item.groups }}"
    loop: "{{ my_users }}"

```
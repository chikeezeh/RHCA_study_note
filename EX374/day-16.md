#### File management continued.
The `ansible.builtin.find` module is another versatile file manipulation module, you can use it in combination with regular expressions ans `ansible.builtin.lineinfile` to change the content of multiple files.
See example playbook below:

```yaml
---
- name: This playbook uses the find module to find files
  hosts: localhost
  tasks:
  - name: use the find module to find all ansible.cfg config files on localhost
    ansible.builtin.find:
      paths:
      - /etc
      - /usr
      - /home
      recurse: yes
      patterns: 'ansible.cfg'
    register: output
  - name: show the output
    debug:
      var: output
```

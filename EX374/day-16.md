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
#### Using Templates

The `template` module in ansible incorporates `jinja2` template files with variables. To create and modify files. Also, `jinja2` allows for loops and conditionals, and this makes the `template` module very powerful.

Simple template example below:

We want to write the IP address of a host to a file. 

Template:

```jinja2
my_ip = {{ ansible_facts['default_ipv4']['address'] }}
```

Playbook:

```yaml
---
- name: demo for the template module
  hosts: rhel, ubuntu
  tasks:
  - name: use template module to make a file
    template:
      src: ip.j2
      dest: /tmp/ip_address
  - name: verify our template worked
    shell: cat /tmp/ip_address
    register: output
  - name: show the output
    debug:
      var: output
```




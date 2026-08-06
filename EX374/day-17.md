#### Advanced Template usage.

To make templates more powerful, we can apply variables and conditionals. 
- variables are placed between `{{ }}` delimiters
  - `{{ ansible_facts['distribution'] }}`
- conditionals are placed between `{% EXPR %}` delimiters
  ```
  {% for file in myfiles %}
    {{ file }}
  {% endfor %} 
   ```
##### Example usage.
In this example we loop through all the ip address, and write to a file.

```yaml
---
- name: This tests how to loop through system variables and use in a template.
  hosts: rhel
  tasks: 
  - name: using the template module to create a file
    template:
      src: hostvar.j2
      dest: /tmp/ip_hosts
```

Content of `hostvar.j2` template.

```jinja2
{% for host in groups['rhel'] %}
{{ hostvars[host]['ansible_ens18']['ipv4']['address'] }}
{% endfor %}
```

After running the playbook we can use the adhoc command below to verify that the file was created.

```shell
ansible@control:~/ansible_work$ ansible vm1 -a "cat /tmp/ip_hosts"
vm1 | CHANGED | rc=0 >>
10.10.10.5
10.10.10.6
10.10.10.8
```

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
#### Lab on advanced template usage.
Question: Using hostvars, create a `/etc/hosts` file that can be used for host name resolution to IP address.

Solution:

<details>
<summary> Click to expand Solution </summary>

Template file:
```jinja2
{% for host in groups['all'] %}
{{ hostvars[host]['ansible_ens18']['ipv4']['address'] }} {{ hostvars[host]['ansible_fqdn'] }} {{ hostvars[host]['ansible_hostname'] }}
{% endfor %}
```
Playbook:

```yaml
---
- name: use a template file to create /etc/hosts on all
  hosts: all
  tasks:
  - name: using template file to create /etc/hosts
    template:
      src: etchost.j2
      dest: /tmp/hosts
```
Output: 

```shell
ansible@control:~/ansible_work$ ansible-playbook etchosts.yml

PLAY [use a template file to create /etc/hosts on all] *********************************************************************************************************************

TASK [Gathering Facts] *****************************************************************************************************************************************************
ok: [vm2]
ok: [vm3]
ok: [webserver]
ok: [tower]
ok: [vm4]
ok: [vm1]

TASK [using template file to create /etc/hosts] ****************************************************************************************************************************
changed: [vm1]
changed: [vm3]
changed: [vm2]
changed: [webserver]
changed: [tower]
changed: [vm4]

PLAY RECAP *****************************************************************************************************************************************************************
tower                      : ok=2    changed=1    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   
vm1                        : ok=2    changed=1    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   
vm2                        : ok=2    changed=1    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   
vm3                        : ok=2    changed=1    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   
vm4                        : ok=2    changed=1    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   
webserver                  : ok=2    changed=1    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   

ansible@control:~/ansible_work$ ansible vm1 -a "cat /tmp/hosts"
vm1 | CHANGED | rc=0 >>
10.10.10.11 webserver.cezeh.lab webserver
10.10.10.5 vm1.cezeh.lab vm1
10.10.10.6 vm2.cezeh.lab vm2
10.10.10.8 vm3.cezeh.lab vm3
10.10.10.9 tower.cezeh.lab tower
10.10.10.10 vm4.cezeh.lab vm4
```

</details>
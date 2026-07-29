#### Lab: Making Playbooks Flexible with variables
##### Question.
- Use host variables to define a variable webpackage and variable webservice, for rhel (httpd) and ubuntu (apache2).
- Write a playbook that installs and starts the packages on each distribution based on these variables. 

##### Solution.
<details>
<summary> Click to expand </summary>

My approach will be to use group_vars. To make sure the playbook works correctly, I created another Ubuntu VM called `vm4`. Below is my new inventory file.

```yaml
[rhel]
vm1
vm2
vm3

[aap]
tower

[ubuntu]
vm4
```
Created the variables;

```shell
[ansible@control ansible_work]$ echo "webpackage: apache2" > group_vars/ubuntu.yml
[ansible@control ansible_work]$ echo "webpackage: httpd" > group_vars/rhel.yml
[ansible@control ansible_work]$ echo "webservice: apache2" >> group_vars/ubuntu.yml
[ansible@control ansible_work]$ echo "webservice: httpd" >> group_vars/rhel.yml
```
Playbook below


```yaml
---
- name: webserver deployment on rhel and ubuntu
  hosts: 
    - rhel
    - ubuntu
  tasks:
  - name: install apache webserver
    ansible.builtin.package:
      name: "{{ package }}"
      state: latest
  - name: starting the webserver service
    ansible.builtin.service:
      name: "{{ package }}"
      state: started
```
</details>
#### Manage inventory variables drill


- Structure host and group variables using multiple files per host or group
- Use special variables to override the host, port, or remote user for a specific host

Performing some exercises to drill and practice the two exam objectives above. All of these will be performed on the CLI and not on AAP. 

##### Manage inventory variables drill questions

###### Exercise 1 — multiple files per group. 

Inventory with a web group (web1, web2). Create group_vars/web/packages.yml containing web_packages: [httpd, firewalld], and group_vars/web/config.yml containing http_port: 8080. Verify with ansible-inventory -i inventory --host web1 --yaml — both variables must appear. Lesson: Ansible merges every .yml in the directory, so splitting is purely organizational.


<details>
  <summary>Click to reveal Solution</summary>
  
Step 1, create a simple inventory file.
```yaml
[web]
vm1
vm2
```
Step 2, create the group_vars directory. 

`mkdir -p group_vars/web`

Step 3, create the variables inside the required files.

```shell
config.yml    packages.yml
[ansible@tower drill1]$ cat group_vars/web/packages.yml
web_packages:
- httpd
- firewalld
[ansible@tower drill1]$ cat group_vars/web/config.yml
http_port: 8080
```

Step 4, verify that the hosts can see the variables.

```shell
[ansible@tower drill1]$ ansible-inventory -i inventory --host vm1 --yaml
http_port: 8080
web_packages:
- httpd
- firewalld
[ansible@tower drill1]$ ansible-inventory -i inventory --host vm2 --yaml
http_port: 8080
web_packages:
- httpd
- firewalld

```

  
</details>
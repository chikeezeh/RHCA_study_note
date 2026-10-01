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


###### Exercise 2 — host_vars as a directory. 

Create host_vars/web1/ with two files: app.yml (app_name: shop) and tuning.yml (max_workers: 8). The same --host web1 check should now show four variables (two from the group files, two from the host files). Then add http_port: 9090 to tuning.yml and confirm it beats the group's 8080 — that's host_vars outranking group_vars.

Solution:

<details>
  <summary>Click to reveal Solution</summary>

Condensed steps shown:

```shell
[ansible@tower drill1]$ mkdir -p host_vars/vm1
[ansible@tower drill1]$ vim host_vars/vm1/app.yml
[ansible@tower drill1]$ vim host_vars/vm1/tuning.yml
[ansible@tower drill1]$ ansible-inventory -i inventory --host vm1
{
    "app_name": "shop",
    "http_port": 8080,
    "max_workers": 8,
    "web_packages": [
        "httpd",
        "firewalld"
    ]
}

```
Then change the `http_port` variable.

```shell
[ansible@tower drill1]$ echo "http_port: 9090" >> host_vars/vm1/tuning.yml
[ansible@tower drill1]$ cat host_vars/vm1/tuning.yml
max_workers: 8
http_port: 9090
[ansible@tower drill1]$ ansible-inventory -i inventory --host vm1
{
    "app_name": "shop",
    "http_port": 9090,
    "max_workers": 8,
    "web_packages": [
        "httpd",
        "firewalld"
    ]
}
```
The host level variable supercedes the group level variable. 

</details>
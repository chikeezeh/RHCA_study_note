#### Manage inventory variables drill - continued


- Structure host and group variables using multiple files per host or group
- Use special variables to override the host, port, or remote user for a specific host

Performing some exercises to drill and practice the two exam objectives above. All of these will be performed on the CLI and not on AAP. 

##### Manage inventory variables drill questions - continued

###### Exercise 3 — alias vs. real hostname. 
Your `web` nodes as `frontend-1` and `frontend-2`, but your lab machines are `vm1` and `vm2`. Write an inventory where those labels work, then run two commands proving the label and the actual connection target are different things.

Solution:

<details>
  <summary>Click to reveal Solution</summary>

First step is to recreate the inventory file. 

```ini
[web]
frontend-1 ansible_host=vm1
frontend-2 ansible_host=vm2
```

Second step, verify the alias and real hostname.

```shell
[ansible@tower drill1]$ ansible -i inventory frontend-2 -m debug -a 'var=ansible_host'
frontend-2 | SUCCESS => {
    "ansible_host": "vm2"
}
[ansible@tower drill1]$ ansible-inventory -i inventory --graph
@all:
  |--@ungrouped:
  |--@web:
  |  |--frontend-1
  |  |--frontend-2
```



</details>

###### Exercise 4 — per-host user and port.

vm4 is Ubuntu and must be reached over SSH as the user ubuntu; vm2 must be contacted on port 2222. Configure both as per-host overrides, then demonstrate the settings actually took effect.

Solution:

<details>
  <summary>Click to reveal Solution</summary>

Modify the inventory file first. See the new inventory file content below:

```shell
[ansible@tower drill1]$ cat inventory
[web]
frontend-1 ansible_host=vm1
frontend-2 ansible_host=vm2 ansible_port=2222
[ubuntu]
vm4 ansible_user=ubuntu
```


Test the new variables added. 

```shell
[ansible@tower drill1]$ ansible-inventory -i inventory --host vm4
{
    "ansible_user": "ubuntu"
}
[ansible@tower drill1]$ ansible -i inventory frontend-2 -m ping
frontend-2 | UNREACHABLE! => {
    "changed": false,
    "msg": "Failed to connect to the host via ssh: ssh: connect to host vm2 port 2222: No route to host",
    "unreachable": true
}

```

</details>


###### Exercise 5 — Capstone

Structure your inventory file and variable files to have the outcome below:

group_vars/all/ — common.yml: ntp_server, admin_email / security.yml: firewall_enabled, ssh_max_auth_tries
group_vars/web/ — packages.yml: web_packages: [httpd, firewalld] / config.yml: http_port: 8080
group_vars/db/config.yml — db_engine: mariadb, db_port: 3306
host_vars/vm1/tuning.yml — max_workers: 8
host_vars/vm2/tuning.yml — max_workers: 4
host_vars/vm3/tuning.yml — db_port: 3308 (overrides the group's 3306)
host_vars/vm4/ — one file with something Ubuntu-specific, e.g. app.yml with cache_dir: /var/cache/app, plus the ansible_user=ansible override (inventory line or host_vars, your choice)

Solution:

<details>
  <summary>Click to reveal Solution</summary>

Below is the structure of my project directory, with the required group and host files. 

```shell
[ansible@tower drill1]$ tree
.
├── group_vars
│   ├── all
│   │   ├── common.yml
│   │   └── security.yml
│   ├── db
│   │   └── config.yml
│   └── web
│       ├── config.yml
│       └── packages.yml
├── host_vars
│   ├── frontend-1
│   │   ├── app.yml
│   │   └── tuning.yml
│   ├── frontend-2
│   │   └── tuning.yml
│   ├── vm3
│   │   └── tuning.yml
│   └── vm4
│       └── app.yml
├── inventory
└── testgroupvars.yml

9 directories, 12 files

```
These are the variables that all the hosts have access to, include host level overides. 

```shell
[ansible@tower drill1]$ ansible-inventory -i inventory --host frontend-1 --yaml
admin_email: ansible@cezeh.lab
ansible_host: vm1
app_name: shop
firewall_enabled: true
http_port: 8080
max_workers: 8
ntp_server: 1.1.1.1
ssh_max_auth_tries: 4
web_packages:
- httpd
- firewalld
[ansible@tower drill1]$ ansible-inventory -i inventory --host frontend-2 --yaml
admin_email: ansible@cezeh.lab
ansible_host: vm2
firewall_enabled: true
http_port: 8080
max_workers: 4
ntp_server: 1.1.1.1
ssh_max_auth_tries: 4
web_packages:
- httpd
- firewalld
[ansible@tower drill1]$ ansible-inventory -i inventory --host vm3 --yaml
admin_email: ansible@cezeh.lab
db_engine: mariadb
db_port: 3308
firewall_enabled: true
ntp_server: 1.1.1.1
ssh_max_auth_tries: 4
[ansible@tower drill1]$ ansible-inventory -i inventory --host vm4 --yaml
admin_email: ansible@cezeh.lab
ansible_user: ansible
cache_dir: /var/cache/app
firewall_enabled: true
ntp_server: 1.1.1.1
ssh_max_auth_tries: 4

```

</details>

#### Reflection

1. When creating the host variable file, use the alias name in the inventory file to create the host directory, and not the actual hostname. This is useful in a dynamic environment where the hostname could change, but we want it to still have access to those variables.
2. Use the `ansible-inventory` command to peek into the variables that a host has access to. 


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
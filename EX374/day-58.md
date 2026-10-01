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

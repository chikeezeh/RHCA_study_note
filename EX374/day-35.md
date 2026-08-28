#### Plugin Lab

##### Question
Configure ansible in such a way that screen output is reduced to a minimum using a callback plugin.


#### Solution

- We need to check available callback plugins. `ansible-doc -t callback -l`
```shell
ansible.builtin.default                default Ansible screen output
ansible.builtin.junit                  write playbook output to a JUnit file
ansible.builtin.minimal                minimal Ansible screen output
ansible.builtin.oneline                oneline Ansible screen output
ansible.builtin.tree                   Save host events to files

```
Above output truncated, but looks like we need the `ansible.builtin.minimal` callback plugin. 
- Next step is to figure out how to use it, I checked `doc.ansible.com` and figured I can change the `stdout_callback` variable in `ansible.cfg` to a value of `stdout_callback=minimal`. This will affect all playbook ran from that project directory. 

Testing a random playbook.

With default stdout:

```shell
[ansible@control ansible_work]$ ansible-playbook phonenumber.yml

PLAY [demonstrating custom filter] *******************************************************************************************************************************************************************

TASK [use a custom filter to format the number] ******************************************************************************************************************************************************
ok: [vm1] => {
    "msg": "the number is (123) 456-7890"
}

PLAY RECAP *******************************************************************************************************************************************************************************************
vm1                        : ok=1    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0

```

With minimal stdout:

```shell
[ansible@control ansible_work]$ ansible-playbook phonenumber.yml
vm1 | SUCCESS => {
    "msg": "the number is (123) 456-7890"
    }

```


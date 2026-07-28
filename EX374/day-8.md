#### Using set_fact

A module for defining a fact in the playbook. The fact is global, so applies to everywhere in the playbook, however the fact can't be used after the playbook is done running. 

Example playbook below:

```yaml
- name: creating a fact
  hosts: vm1
  tasks:
  - name: Setting facts so that they will be persisted in the fact cache
    ansible.builtin.set_fact:
      one_fact: world!
- name: another play
  hosts: vm1
  gather_facts: no
  tasks:
  - name: Testing the facts we set above
    debug:
      msg: Hello {{ one_fact }}
```

Result of playbook:

```shell
 ansible-playbook setfact.yml

PLAY [creating a fact] ***************************************************************************************************************************************************************************************

TASK [Gathering Facts] ***************************************************************************************************************************************************************************************
ok: [vm1]

TASK [Setting facts so that they will be persisted in the fact cache] ****************************************************************************************************************************************
ok: [vm1]

PLAY [another play] ******************************************************************************************************************************************************************************************

TASK [Testing the facts we set above] ************************************************************************************************************************************************************************
ok: [vm1] => {
    "msg": "Hello world!"
}

PLAY RECAP ***************************************************************************************************************************************************************************************************
vm1                        : ok=3    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0

```
Note, the variable will only work on the hosts defined in the play for the `set_fact` module, so using the playbook above, it will fail if we run the second play on `vm2` instead of `vm1`.

#### Different notations for facts and variables.

##### Ansible 2.4 and before
Individual variables: `ansible_date_time.date` 

##### Ansible 2.5 and after
Every fact is stored in one dictionary called `ansible_facts`, you can refer to specific fact using either the dotted format or square bracket.

Dotted: `ansible_facts.date_time.date`
Bracket: `ansible_facts['date_time']['date']`

Example playbook:

```yaml
- name: Showing the different fact notations
  hosts: vm2
  tasks:
  - name: dotted notation
    debug:
      msg: "The IP address is: {{ ansible_facts.default_ipv4.address }}"
  - name: square bracket
    debug:
      msg: "The IP address is: {{ ansible_facts['default_ipv4']['address'] }}"
  - name: old notation
    debug:
      msg: "The IP address is: {{ ansible_default_ipv4.address }}"
```
Result:
```shell
PLAY [Showing the different fact notations] ******************************************************************************************************************************************************************

TASK [Gathering Facts] ***************************************************************************************************************************************************************************************
ok: [vm2]

TASK [dotted notation] ***************************************************************************************************************************************************************************************
ok: [vm2] => {
    "msg": "The IP address is: 10.10.10.6"
}

TASK [square bracket] ****************************************************************************************************************************************************************************************
ok: [vm2] => {
    "msg": "The IP address is: 10.10.10.6"
}

TASK [old notation] ******************************************************************************************************************************************************************************************
ok: [vm2] => {
    "msg": "The IP address is: 10.10.10.6"
}

PLAY RECAP ***************************************************************************************************************************************************************************************************
vm2                        : ok=4    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0

```
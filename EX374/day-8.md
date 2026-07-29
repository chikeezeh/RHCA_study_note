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

#### Multi-valued variables
These are arrays and dictionaries.

##### Dictionaries.

Unordered key value pairs, example are ansible_facts. Loops are not supported in dictionaries.
Example:
```json
"ansible_date_time": {
            "date": "2026-07-28",
            "day": "28",
            "epoch": "1785277391",
            "epoch_int": "1785277391",
            "hour": "15",
            "iso8601": "2026-07-28T22:23:11Z",
            "iso8601_basic": "20260728T152311386233",
            "iso8601_basic_short": "20260728T152311",
            "iso8601_micro": "2026-07-28T22:23:11.386233Z",
            "minute": "23",
            "month": "07",
            "second": "11",
            "time": "15:23:11",
            "tz": "MST",
            "tz_dst": "MST",
            "tz_offset": "-0700",
            "weekday": "Tuesday",
            "weekday_number": "2",
            "weeknumber": "30",
            "year": "2026"
        },

```
##### Arrays.
These are ordered list of variables, loops can be used on arrays. Example list of packages to install on a host. 

Example:
```python
"dm-0": [
                    "dm-name-rhel-root",
                    "dm-uuid-LVM-GNNEflY3kgz8UsPucJrpRygDBW0UN239gvL8pFodgP3eaZUpyDt1aL9QNkR4ilHz"
                ]
```
#### Combination of arrays and dictionary

Example:
```yaml
users:
- username: linda
  shell: /bin/bash
- username: lisa
  shell: /nobin/bash
```
`users` is an array, that contains 2 list items, where each list items are dictionaries. Another way to think about it is:
`users=[{username: linda, shell: /bin/bash},{username: lisa, shell: /nobin/bash}]`

Playbook example using mixed variables:
```yaml
---
- name: mixed array and dictionary variables
  hosts: vm1
  tasks:
  - name: Get all element in the ansible_mounts
    debug:
      var: ansible_mounts
  - name: Get first element in the ansible_mounts array
    debug:
      var: ansible_mounts[0]
```

#### The `test` plugin.

This can be used in replacement of the `when` keyword. It can be used to test input properties. 

`ansible-doc -t test -l` gives a list of all the properties that can be tested, see example output below.

```shell
[ansible@control ansible_work]$ ansible-doc -t test -l
ansible.builtin.abs             is the path absolute
ansible.builtin.all             are all conditions in a list true
ansible.builtin.any             is any condition in a list true
ansible.builtin.change          did the task require changes
ansible.builtin.changed         did the task require changes
ansible.builtin.contains        does the list contain this element
ansible.builtin.directory       does the path resolve to an existing directory
ansible.builtin.exists          does the path exist, follow symlinks
ansible.builtin.failed          did the task fail
ansible.builtin.failure         did the task fail
ansible.builtin.falsy           Pythonic false
```
Example playbook based from [Sander van Vugt's](https://github.com/sandervanvugt) [Github repo](https://github.com/sandervanvugt/ansible_ccat/tree/main).

```yaml
---
- name: Example Playbook to Filter Fixed IP Addresses
  hosts: localhost
  gather_facts: false
  vars:
    all_addresses:
      - ip: "192.168.1.10"
        type: "fixed"
      - ip: "192.168.1.11"
        type: "dynamic"
      - ip: "192.168.1.12"
        type: "fixed"
      - ip: "192.168.1.13"
        type: "dynamic"
  tasks:
    - name: Filter only fixed IP addresses
      set_fact:
        only_fixed_addresses: "{{ all_addresses | selectattr('type', 'equalto', 'fixed') | list }}"
    - name: Display only fixed IP addresses
      debug:
        msg: "Fixed IP Addresses: {{ only_fixed_addresses }}"
    - name: Display only fixed IP addresses in a formatted way
      debug:
        msg: "IP Address: {{ item.ip }}"
      loop: "{{ only_fixed_addresses }}"

```
Output:

```shell
[ansible@control ansible_work]$ ansible-playbook testip.yaml

PLAY [Example Playbook to Filter Fixed IP Addresses] *************************************************************************************************************************************************

TASK [Filter only fixed IP addresses] ****************************************************************************************************************************************************************
ok: [localhost]

TASK [Display only fixed IP addresses] ***************************************************************************************************************************************************************
ok: [localhost] => {
    "msg": "Fixed IP Addresses: [{'ip': '192.168.1.10', 'type': 'fixed'}, {'ip': '192.168.1.12', 'type': 'fixed'}]"
}

TASK [Display only fixed IP addresses in a formatted way] ********************************************************************************************************************************************
ok: [localhost] => (item={'ip': '192.168.1.10', 'type': 'fixed'}) => {
    "msg": "IP Address: 192.168.1.10"
}
ok: [localhost] => (item={'ip': '192.168.1.12', 'type': 'fixed'}) => {
    "msg": "IP Address: 192.168.1.12"
}

PLAY RECAP *******************************************************************************************************************************************************************************************
localhost                  : ok=3    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0

```
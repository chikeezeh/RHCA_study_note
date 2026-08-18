#### Using filters to work with network addresses.

The `ipaddr` filter is used when working with IP addresses. To use this filter, the following packages needs to be installed on the control node; `python3-netaddr` and `python3-dns`. This filter comes from the `ansible.utils` collection. So install with `ansible-galaxy collection install ansible.utils`.

The filter has arguments for the following:
- `address` validates the input is and IP address.
- `net` checks that input values are network ranges.
- `host` ensures the IP addresses conform to CIDR prefix format. 
- `prefix` returns the network address prefix.

Example playbook based from [Sander van Vugt's](https://github.com/sandervanvugt) [Github repo](https://github.com/sandervanvugt/ansible_ccat/tree/main). Updated the playbook to use the fully qualified collection name (FQCN).


```yaml

---
- name: exploring network plugins and filters
  ignore_errors: true
  hosts: vm1 vm2
  vars:
    addresses:
    - 1.2.3.4/24
    - 10.0.0.0/8
    net_mask: "{{ ansible_default_ipv4.network }}/{{ ansible_default_ipv4.netmask }}"
  tasks:
  - name: print IP address
    debug:
      msg: the IP address is {{ ansible_facts['default_ipv4'] }}
  - name: show if this is a valid IP address
    debug:
      msg: "{{ ansible_facts['default_ipv4']['address'] | ansible.utils.ipaddr }}"
  - name: this happens if the variable does not contain a valid IP address
    debug:
      msg: "{{ ansible_facts['default_ipv4'] | ansible.utils.ipaddr }}"
  - name: now lets check out the netmask
    debug:
      msg: "{{ net_mask | ansible.utils.ipaddr('netmask') }}"
  - name: net is used to convert netmask to CIDR notation
    debug:
      msg: "{{ net_mask | ansible.utils.ipaddr('net') }}"
  - name: host/prefix will filter out only valid host IP addresses and skip network addresses from a list in 1.1.1.1/24 format
    debug:
      msg: "{{ addresses | ansible.utils.ipaddr('host/prefix') }}"
  - name: or size to show available size in this specific network
    debug:
      msg: "{{ ansible_facts['default_ipv4']['network'] | ansible.utils.ipaddr('size') }}"
  - name: rewriting an IPv4 address to IPv6
    debug:
      msg: "{{ addresses | ansible.utils.ipv4('ipv6') }}"
  - name: or int format
    debug:
      msg: "{{ ansible_facts['default_ipv4']['address'] | ansible.utils.ipaddr('int') }}"
  - name: an how about finding available IP addresses in a range
    debug:
      msg: "{{ '193.179.99.8/27' | ansible.utils.ipaddr('range_usable') }}"

```
Output:

```shell
[ansible@control ansible_work]$ ansible-playbook network.yaml

PLAY [exploring network plugins and filters] *********************************************************************************************************************************************************

TASK [Gathering Facts] *******************************************************************************************************************************************************************************
ok: [vm1]
ok: [vm2]

TASK [print IP address] ******************************************************************************************************************************************************************************
ok: [vm1] => {
    "msg": "the IP address is {'gateway': '10.10.10.1', 'interface': 'ens18', 'address': '10.10.10.5', 'broadcast': '10.10.10.255', 'netmask': '255.255.255.0', 'network': '10.10.10.0', 'prefix': '24', 'macaddress': 'bc:24:11:d4:cc:bb', 'mtu': 1500, 'type': 'ether', 'alias': 'ens18'}"
}
ok: [vm2] => {
    "msg": "the IP address is {'gateway': '10.10.10.1', 'interface': 'ens18', 'address': '10.10.10.6', 'broadcast': '10.10.10.255', 'netmask': '255.255.255.0', 'network': '10.10.10.0', 'prefix': '24', 'macaddress': 'bc:24:11:5f:f6:45', 'mtu': 1500, 'type': 'ether', 'alias': 'ens18'}"
}

TASK [show if this is a valid IP address] ************************************************************************************************************************************************************
ok: [vm1] => {
    "msg": "10.10.10.5"
}
ok: [vm2] => {
    "msg": "10.10.10.6"
}

TASK [this happens if the variable does not contain a valid IP address] ******************************************************************************************************************************
fatal: [vm1]: FAILED! => {"msg": "Unrecognized type <<class 'dict'>> for ipaddr filter <value>"}
...ignoring
fatal: [vm2]: FAILED! => {"msg": "Unrecognized type <<class 'dict'>> for ipaddr filter <value>"}
...ignoring

TASK [now lets check out the netmask] ****************************************************************************************************************************************************************
ok: [vm1] => {
    "msg": "255.255.255.0"
}
ok: [vm2] => {
    "msg": "255.255.255.0"
}

TASK [net is used to convert netmask to CIDR notation] ***********************************************************************************************************************************************
ok: [vm1] => {
    "msg": "10.10.10.0/24"
}
ok: [vm2] => {
    "msg": "10.10.10.0/24"
}

TASK [host/prefix will filter out only valid host IP addresses and skip network addresses from a list in 1.1.1.1/24 format] **************************************************************************
ok: [vm1] => {
    "msg": [
        "1.2.3.4/24"
    ]
}
ok: [vm2] => {
    "msg": [
        "1.2.3.4/24"
    ]
}

TASK [or size to show available size in this specific network] ***************************************************************************************************************************************
ok: [vm1] => {
    "msg": "1"
}
ok: [vm2] => {
    "msg": "1"
}

TASK [rewriting an IPv4 address to IPv6] *************************************************************************************************************************************************************
ok: [vm1] => {
    "msg": [
        "::ffff:1.2.3.4/120",
        "::ffff:10.0.0.0/104"
    ]
}
ok: [vm2] => {
    "msg": [
        "::ffff:1.2.3.4/120",
        "::ffff:10.0.0.0/104"
    ]
}

TASK [or int format] *********************************************************************************************************************************************************************************
ok: [vm1] => {
    "msg": "168430085"
}
ok: [vm2] => {
    "msg": "168430086"
}

TASK [an how about finding available IP addresses in a range] ****************************************************************************************************************************************
ok: [vm1] => {
    "msg": "193.179.99.1-193.179.99.30"
}
ok: [vm2] => {
    "msg": "193.179.99.1-193.179.99.30"
}

PLAY RECAP *******************************************************************************************************************************************************************************************
vm1                        : ok=11   changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=1
vm2                        : ok=11   changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=1
```


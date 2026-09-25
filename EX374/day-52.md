#### Managing Network devices with Ansible.
Managing network devices requires specialized modules. Most network device manufacturers collaborates with Ansible to develop these modules more information can be found [here](https://www.ansible.com/integrations/networks). 

Note that connecting to network devices, sometime ssh doesn't work, so we will need other connection protocols. See [ansible doc](https://docs.ansible.com/projects/ansible/latest/network/getting_started/network_differences.html#multiple-communication-protocols) for more details. The `ansible_connection` variable is what needs to be changed to determine the connection type. Examples of values for the variablle.

- `ansible.netcommon.network_cli` CLI over ssh, this is persistent
- `ansible.netcommon.netconf` XML over ssh, this is persistent
- `ansible.netcommon.httpapi` API over HTTP(S), this is persistent
- `local` provider dependent, not persistent.

With persistent connections, you can define the hosts and credentials only once, rather than in every task. 



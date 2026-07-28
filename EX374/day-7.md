#### Lab Renewal

My VMware workstation wasn't running Ansible Automation Platform with the other VMs open due to the memory limitation. So installed ProxMox on bare metal and recreating my labs. 

1. Control host, with two network interface, used Software Defined Network on Proxmox to create the networks. This is running RHEL10.2
hostname: `control.cezeh.lab`
- 192.168.86.0/24 (Default from installation) subnet so I can ssh from my laptop 
- 10.10.10.0/24 subnet where the other VMs will reside. 
2. Ansible automation platform machine, hostname: `tower.cezeh.lab`, running RHEL9.8, and Ansible automation platform 2.5
3. Three managed RHEL9 host with minimal install, all running RHEL9.8.


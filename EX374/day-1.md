#### Requirement for managing remote hosts.
1. SSH setup already on VM2 and VM3.
2. Edited `/etc/sudoers` file on VM2 and VM3 to add a timestamp so that password is only asked once, and every other 60 minutes. This is a more secure way than doing the `NOPASSWD` option. 
```shell
sudo visudo
#then add the line below
Defaults timestamp_type=global,timestamp_timeout=60
```

#### Modules and Collections.

##### Modules
Modules provide a uniform way to run tasks on the managed nodes, with well written modules, the Ansible operator doesn't have to worry about the operational differences on the managed nodes. The Package module for example can be used to install packages on different Linux distributions without having to worry about the different package managers that are used on those distributions. 

##### Collections
Ansible content Collections provide additional modules that don't come with Ansible-Core. These will need to be install separately from the regular ansible-core installation. Collections can be found on galaxy.ansible.com, and ansible automation platform if working from a Red Hat Distribution. 
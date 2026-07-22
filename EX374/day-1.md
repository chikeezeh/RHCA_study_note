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

#### More Ansible setup on Control Node.

##### Create an inventory file
1. Created a working directory; `mkdir ansible_work;cd ansible_work`
2. Added the two managed nodes into the an inventory file, nothing fancy for now. 
```shell
cat inventory 
vm2
vm3

```
3. Use ansible adhoc command to test the new inventory file.
``` shell
cezeh@rhel10:~/ansible_work$ ansible -i inventory all -u admuser -b -K -m ping
BECOME password: 
vm2 | SUCCESS => {
    "ansible_facts": {
        "discovered_interpreter_python": "/usr/bin/python3"
    },
    "changed": false,
    "ping": "pong"
}
vm3 | SUCCESS => {
    "ansible_facts": {
        "discovered_interpreter_python": "/usr/bin/python3"
    },
    "changed": false,
    "ping": "pong"
}
# -i specify inventory file to use
# -u the user on the managed node with ssh access
# -b become sudo
# -K ask for sudo password
# -m module, which in this case we used the ping module to test connectivity.
```
##### Ansible default settings
1. This will be configured using the `ansible.cfg` file.
2. Ran `ansible-config init --disable > ansible.cfg` to create a sample config file in my working directory that I will change later. 
3. Updated only the contents below:
```
[defaults]
remote_user=admuser
host_key_checking=False
inventory=inventory

[privilege_escalation]
become=True
become_method=sudo
become_user=root
become_ask_pass=False
```
#### Ansible Navigator Setup
1. Log into the podman registry using `podman login registr.redhat.io`
2. Use `ansible-navigator` to go into the Ansible navigator TUI. 
3. Use `podman search <key word>` to search for an image
4. Pulling an image. `podman pull registry.redhat.io/ansible-automation-platform-26/ee-supported-rhel9`
5. Create am ansible-navigator config file `vim ansible-navigator.yml` in the working directory with the content below;
```shell
ansible-navigator:
  execution-environment:
    image: registry.redhat.io/ansible-automation-platform-26/ee-supported-rhel9:latest
    pull:
      policy: missing
  playbook-artifact:
    enable: False

```
6. To ensure that the right config file is use, run `ansible-navigator settings` inside the working directory that contains the file created above. Output:
```shell
10│Current settings file                     False    Search path                   /home/cezeh/ansible_work/ansible-navigator.yml
```
7. To test ansible-navigator fully, I will register my controlled nodes on RHEL to enable package repositories. 
8. 
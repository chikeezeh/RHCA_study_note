#### Ansible Navigator Collections contd.

Simulated when you will need to use a collection that isn't available for the execution environment your ansible navigator is running on. The ansible playbook is shown below:

```yaml
---
- name: set SELinux to enforcing
  hosts: localhost
  gather_facts: no
  become: yes
  tasks:
  - name: set SELinux to enforcing
    ansible.posix.selinux:
      policy: targeted
      state: enforcing

```
Used a minimal execution environment that doesn't have `ansible.posix.selinux` module. 
Command: `ansible-navigator run ansible_ccat/ansible_ccat/enforce-selinux.yml --eei registry.redhat.io/ansible-automation-platform-25/ee-minimal-rhel9`

Output:
``` shell
ERROR! couldn't resolve module/action 'ansible.posix.selinux'. This often indicates a misspelling, missing collection, or incorrect module path.
 5│
 6│The error appears to be in '/home/cezeh/ansible_work/ansible_ccat/ansible_ccat/enforce-selinux.yml': line 7, column 5, but may
 7│be elsewhere in the file depending on the exact syntax problem.
 8│
 9│The offending line appears to be:
10│
11│  tasks:
12│  - name: set SELinux to enforcing
13│    ^ here
```
Installed the ansible-posix collection using:
`ansible-galaxy collection install ansible.posix -p collections`
Updated `ansible.cfg`, had the collections path include the new directory. 

#### Using another execution environment.
`ansible-navigator --eei <link to execution environment`, using this, you can view the collections that this execution environment has. 

Use `ansible-navigator settings --sample > my.yml` to create a sample configuration file for ansible-navigator, then edit to match your environment.

#### Ansible ad-hoc commands
`ansible-doc -l` gives you all the modules installed. 
Using ansible ad-hoc commands, you need the following;
1. name of target hosts
2. name of the module to be used
3. name of the module arguments in double quotes
4. Other flags, like become sudo, or inventory file if they are not already specified in the `ansible.cfg` file.
`ansible all -i inventory -m user -a "name=andrew"` This ad-hoc command applies to `all` the hosts listed in the `inventory` file, uses the `user` module, and creates a user called `andrew`. 

Used the adhoc command to copy the /etc/hosts file from the control node to the managed nodes.
`cezeh@rhel10:~/ansible_work$ ansible vm3 -m copy -a "src=/etc/hosts dest=/etc/hosts"`
Used an adhoc command to verify that the copy above worked, by pinging using only the short name.
```shell
cezeh@rhel10:~/ansible_work$ ansible vm2 -a "ping -c4 vm3"
vm2 | CHANGED | rc=0 >>
PING vm3.cezeh.lab (192.168.109.160) 56(84) bytes of data.
64 bytes from vm3.cezeh.lab (192.168.109.160): icmp_seq=1 ttl=64 time=4.22 ms
64 bytes from vm3.cezeh.lab (192.168.109.160): icmp_seq=2 ttl=64 time=0.387 ms
64 bytes from vm3.cezeh.lab (192.168.109.160): icmp_seq=3 ttl=64 time=0.384 ms
64 bytes from vm3.cezeh.lab (192.168.109.160): icmp_seq=4 ttl=64 time=0.992 ms

--- vm3.cezeh.lab ping statistics ---
4 packets transmitted, 4 received, 0% packet loss, time 3058ms
rtt min/avg/max/mdev = 0.384/1.496/4.224/1.593 ms
```
#### More Ansible CLI commands.
`ansible-doc -l` Lists the modules installed.
`ansible-galaxy collection install ansible.posix` Install a new collection that has more modules to a default directory.
`ansible-galaxy collection install ansible.posix -p <your directory>` Same as above, but to a specific directory.
`ansible-galaxy collection list` Gives a summary of the collections currently installed. 
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
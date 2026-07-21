## Lab Setup

The initial plan is to review Ansible basics, using [Ansible: From Basics to Guru by Sander van Vugt](https://www.linkedin.com/learning/ansible-from-basics-to-guru-by-pearson), this course is currently on Linkedin learning, and I got access via my local library. I will likely skip some aspects of this course, but for now I will build 3 VMs running on VMWare. 

#### Hosts
1. VM1 is a RHEL10.2 Image, registered with subscription manager and desktop environment. Red Hat offers free limited license for developer accounts.
2. VM2, and VM3, minimal RHEL9.0 images, not registered. 

#### Network Setup

1. All hosts are on the same subnet 192.168.109.0/24
2. Using NAT for the interfaces. So each host has access to the internet. 
3. Static IP addresses so they don't change.
   1. Control Node: VM1 192.168.109.158/24
   2. VM2: 192.168.109.159/24
   3. VM3: 192.168.109.160/24
4. Hostnames:
   1. VM1: rhel10.cezeh.lab
   2. VM2: vm2.cezeh.lab
   3. VM3: vm3.cezeh.lab
5. Hostname resolution, updated `/etc/hosts` on the control node with the entry below, will use Ansible to update the same file on the other two nodes as a practice. 
```shell
192.168.109.158 rhel10.cezeh.lab rhel10
192.168.109.159 vm2.cezeh.lab vm2
192.168.109.160 vm3.cezeh.lab vm3

```
Tested via ping, all VMs reachable from the Control node. 

```shell
cezeh@rhel10:~$ ping -c2 rhel10
PING rhel10.cezeh.lab (192.168.109.158) 56(84) bytes of data.
64 bytes from rhel10.cezeh.lab (192.168.109.158): icmp_seq=1 ttl=64 time=0.151 ms
64 bytes from rhel10.cezeh.lab (192.168.109.158): icmp_seq=2 ttl=64 time=0.055 ms

--- rhel10.cezeh.lab ping statistics ---
2 packets transmitted, 2 received, 0% packet loss, time 1012ms
rtt min/avg/max/mdev = 0.055/0.103/0.151/0.048 ms
cezeh@rhel10:~$ ping -c2 vm2
PING vm2.cezeh.lab (192.168.109.159) 56(84) bytes of data.
64 bytes from vm2.cezeh.lab (192.168.109.159): icmp_seq=1 ttl=64 time=3.57 ms
64 bytes from vm2.cezeh.lab (192.168.109.159): icmp_seq=2 ttl=64 time=0.391 ms

--- vm2.cezeh.lab ping statistics ---
2 packets transmitted, 2 received, 0% packet loss, time 1003ms
rtt min/avg/max/mdev = 0.391/1.982/3.573/1.591 ms
cezeh@rhel10:~$ ping -c2 vm3
PING vm3.cezeh.lab (192.168.109.160) 56(84) bytes of data.
64 bytes from vm3.cezeh.lab (192.168.109.160): icmp_seq=1 ttl=64 time=1.66 ms
64 bytes from vm3.cezeh.lab (192.168.109.160): icmp_seq=2 ttl=64 time=0.444 ms

--- vm3.cezeh.lab ping statistics ---
2 packets transmitted, 2 received, 0% packet loss, time 1002ms
rtt min/avg/max/mdev = 0.444/1.050/1.656/0.606 ms

```
Note, this could be automated, but I did all these manually. 

#### Passwordless SSH
I remember that Ansible requires keybased ssh connection to the managed hosts, so lets make that happen.
On the Control Node, I ran:
   1. `ssh-keygen -t ed25519 -C "cezeh.rhca.lab"` To create a Private/Public key pair.
   2. `ssh-copy-id admuser@vm2` To copy the public key to the remote hosts, in this case VM2/VM3. I created an user called `admuser` on both vm2 and vm3. 
   3. Tested by trying to ssh into vm2 using the admuser, and I wasn't asked for a password. 
   ```shell
    cezeh@rhel10:~$ ssh admuser@vm2
    Last login: Mon Jul 20 19:33:38 2026 from 192.168.109.158
    [admuser@vm2 ~]$ 
   ```

#### Setting up Ansible on the control Node
1. Setting up required repositories.
   1. `sudo subscription-manager repos --enable ansible-automation-platform-2.6-for-rhel-10-x86_64-rpms`
   2. `sudo subscription-manager repos --enable rhel-10-for-x86_64-appstream-rpms`
2. Install Ansible Software: `sudo dnf install ansible-core`, this installed version: `2:2.16.19-1.el10ap`
3. Install Ansible Automation Content Navigator: `sudo dnf install ansible-navigator` version installed: `26.6.0-1.el10ap`
4. Verify installation:
```shell
cezeh@rhel10:~$ ansible --version
ansible [core 2.16.19]
  config file = /etc/ansible/ansible.cfg
  configured module search path = ['/home/cezeh/.ansible/plugins/modules', '/usr/share/ansible/plugins/modules']
  ansible python module location = /usr/lib/python3.12/site-packages/ansible
  ansible collection location = /home/cezeh/.ansible/collections:/usr/share/ansible/collections
  executable location = /usr/bin/ansible
  python version = 3.12.13 (main, Jul 10 2026, 00:00:00) [GCC 14.3.1 20251022 (Red Hat 14.3.1-4)] (/usr/bin/python3)
  jinja version = 3.1.6
  libyaml = True
cezeh@rhel10:~$ ansible-navigator --version
ansible-navigator 26.6.0
```
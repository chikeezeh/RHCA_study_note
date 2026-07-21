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

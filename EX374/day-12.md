#### Using multiple conditions for when.

Like all programming languages, ansible allows for the use of multiple conditionals, using `or`, `and`, and `not`. Example below:

```yaml
---
- name: testing multiple when conditionals
  hosts: rhel,ubuntu
  tasks:
  - name: This should only run if a host is RHEL and x86_64
    debug:
      msg: I am a {{ ansible_facts['distribution'] }} running on {{ ansible_facts['machine'] }} machine.
    when: ansible_facts['distribution'] == "RedHat" and
      ansible_facts['machine'] == "x86_64"
  - name: This should run if either Ubuntu or x86_64
    debug:
      msg: I am a {{ ansible_facts['distribution'] }} running on {{ ansible_facts['machine'] }} machine.
    when: ansible_facts['distribution']  == "Ubuntu" or
      ansible_facts['machine'] == "x86_64"
```
Output:

```shell
[ansible@control ansible_work]$ ansible-playbook multiwhen.yml

PLAY [testing multiple when conditionals] ********************************************************************************************************************************************************************

TASK [Gathering Facts] ***************************************************************************************************************************************************************************************
ok: [vm4]
ok: [vm3]
ok: [vm2]
ok: [vm1]

TASK [This should only run if a host is RHEL and x86_64] *****************************************************************************************************************************************************
ok: [vm1] => {
    "msg": "I am a RedHat running on x86_64 machine."
}
ok: [vm2] => {
    "msg": "I am a RedHat running on x86_64 machine."
}
ok: [vm3] => {
    "msg": "I am a RedHat running on x86_64 machine."
}
skipping: [vm4]

TASK [This should run if either Ubuntu or x86_64] ************************************************************************************************************************************************************
ok: [vm1] => {
    "msg": "I am a RedHat running on x86_64 machine."
}
ok: [vm2] => {
    "msg": "I am a RedHat running on x86_64 machine."
}
ok: [vm3] => {
    "msg": "I am a RedHat running on x86_64 machine."
}
ok: [vm4] => {
    "msg": "I am a Ubuntu running on x86_64 machine."
}

PLAY RECAP ***************************************************************************************************************************************************************************************************
vm1                        : ok=3    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
vm2                        : ok=3    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
vm3                        : ok=3    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
vm4                        : ok=2    changed=0    unreachable=0    failed=0    skipped=1    rescued=0    ignored=0

```

##### Multiple conditions `and`

When we have multiple conditions that all need to evaluate to true, the best practice is to use yaml list as shown below:

```yaml
- name: Install Nginx on Ubuntu 22.04
  ansible.builtin.apt:
    name: nginx
    state: present
  when:
    - ansible_facts['distribution'] == 'Ubuntu'
    - ansible_facts['distribution_version'] == '22.04'
    - enable_nginx_service | default(false) | bool
```

##### Multiple conditions `or`

If any of the conditions can be True for the task to run, use explicit or operators within a single expression:

```yaml
- name: Install Nginx on Debian-based systems
  ansible.builtin.apt:
    name: nginx
    state: present
  when: ansible_facts['distribution'] == 'Ubuntu' or ansible_facts['distribution'] == 'Debian'
```

##### Multiple conditions combining `and` and `or`

For nested or mixed logic (e.g., $(A \text{ OR } B) \text{ AND } C$), use parentheses inside a single expression or combine list items with parentheses:

```yaml
- name: Deploy application package
  ansible.builtin.package:
    name: myapp
    state: present
  when:
    - (ansible_facts['distribution'] == 'Ubuntu' or ansible_facts['distribution'] == 'Debian')
    - env == 'production'
```
##### Combining `loop` and `when` conditionals.

We can loop through lists and also combine it with when conditionals for more complex logic. For example, in the playbook below, we loop through all the mounts on each host, find the `/` directory and see if it has 40Gigs of available space. 

```yaml
---
- name: combining loops and when conditional
  hosts: all
  tasks:
  - name: loop ansible_mounts, and check root directory with greater than 40Gigbytes
    debug:
      msg: "My {{ item.mount }} has {{ item.size_available }} bytes"
    loop: "{{ ansible_facts['mounts'] }}"
    when:
    - item.mount == "/"
    - item.size_available > 40000000000
```
Output:


<details>
<summary> Click to expand </summary>

```shell
[ansible@control ansible_work]$ ansible-playbook loopwhen.yml
PLAY [combining loops and when conditional] ******************************************************************************************************************************************************************

TASK [Gathering Facts] ***************************************************************************************************************************************************************************************
ok: [vm1]
ok: [vm2]
ok: [vm3]
ok: [webserver]
ok: [tower]
ok: [vm4]

TASK [loop ansible_mounts, and check root directory with greater than 40Gigbytes] ****************************************************************************************************************************
skipping: [webserver] => (item={'mount': '/', 'device': '/dev/mapper/almalinux-root', 'fstype': 'xfs', 'options': 'rw,seclabel,relatime,attr2,inode64,logbufs=8,logbsize=32k,noquota', 'size_total': 39611006976, 'size_available': 37404090368, 'block_size': 4096, 'block_total': 9670656, 'block_available': 9131858, 'block_used': 538798, 'inode_total': 19374080, 'inode_available': 19336168, 'inode_used': 37912, 'uuid': '0f2b1744-2aa1-4c49-bc55-6df8354ae7fb'})
skipping: [webserver] => (item={'mount': '/boot', 'device': '/dev/sda2', 'fstype': 'xfs', 'options': 'rw,seclabel,relatime,attr2,inode64,logbufs=8,logbsize=32k,noquota', 'size_total': 1006632960, 'size_available': 701870080, 'block_size': 4096, 'block_total': 245760, 'block_available': 171355, 'block_used': 74405, 'inode_total': 524288, 'inode_available': 523919, 'inode_used': 369, 'uuid': '6615b8a9-644f-459b-a153-7af29849ea6e'})
skipping: [vm1] => (item={'mount': '/', 'device': '/dev/mapper/rhel-root', 'fstype': 'xfs', 'options': 'rw,seclabel,relatime,attr2,inode64,logbufs=8,logbsize=32k,noquota', 'size_total': 41067339776, 'size_available': 38659330048, 'block_size': 4096, 'block_total': 10026206, 'block_available': 9438313, 'block_used': 587893, 'inode_total': 20062208, 'inode_available': 20022328, 'inode_used': 39880, 'uuid': '7695a3a7-8d67-410a-92e2-d646aff2567a'})
skipping: [vm1] => (item={'mount': '/boot', 'device': '/dev/sda1', 'fstype': 'xfs', 'options': 'rw,seclabel,relatime,attr2,inode64,logbufs=8,logbsize=32k,noquota', 'size_total': 1063256064, 'size_available': 745537536, 'block_size': 4096, 'block_total': 259584, 'block_available': 182016, 'block_used': 77568, 'inode_total': 524288, 'inode_available': 523922, 'inode_used': 366, 'uuid': 'e6e29d07-7a35-4c61-b1ba-06bcd9195841'})
skipping: [vm1] => (item={'mount': '/home', 'device': '/dev/mapper/rhel-home', 'fstype': 'xfs', 'options': 'rw,seclabel,relatime,attr2,inode64,logbufs=8,logbsize=32k,noquota', 'size_total': 20050870272, 'size_available': 19876786176, 'block_size': 4096, 'block_total': 4895232, 'block_available': 4852731, 'block_used': 42501, 'inode_total': 9795584, 'inode_available': 9795566, 'inode_used': 18, 'uuid': 'f049c9ab-0c01-45e8-b644-807ccd4b7ad2'})
skipping: [webserver]
skipping: [vm2] => (item={'mount': '/', 'device': '/dev/mapper/rhel-root', 'fstype': 'xfs', 'options': 'rw,seclabel,relatime,attr2,inode64,logbufs=8,logbsize=32k,noquota', 'size_total': 41067339776, 'size_available': 38659788800, 'block_size': 4096, 'block_total': 10026206, 'block_available': 9438425, 'block_used': 587781, 'inode_total': 20062208, 'inode_available': 20022328, 'inode_used': 39880, 'uuid': '7695a3a7-8d67-410a-92e2-d646aff2567a'})
skipping: [vm2] => (item={'mount': '/boot', 'device': '/dev/sda1', 'fstype': 'xfs', 'options': 'rw,seclabel,relatime,attr2,inode64,logbufs=8,logbsize=32k,noquota', 'size_total': 1063256064, 'size_available': 745537536, 'block_size': 4096, 'block_total': 259584, 'block_available': 182016, 'block_used': 77568, 'inode_total': 524288, 'inode_available': 523922, 'inode_used': 366, 'uuid': 'e6e29d07-7a35-4c61-b1ba-06bcd9195841'})
skipping: [vm2] => (item={'mount': '/home', 'device': '/dev/mapper/rhel-home', 'fstype': 'xfs', 'options': 'rw,seclabel,relatime,attr2,inode64,logbufs=8,logbsize=32k,noquota', 'size_total': 20050870272, 'size_available': 19876786176, 'block_size': 4096, 'block_total': 4895232, 'block_available': 4852731, 'block_used': 42501, 'inode_total': 9795584, 'inode_available': 9795566, 'inode_used': 18, 'uuid': 'f049c9ab-0c01-45e8-b644-807ccd4b7ad2'})
skipping: [vm1]
skipping: [vm3] => (item={'mount': '/', 'device': '/dev/mapper/rhel-root', 'fstype': 'xfs', 'options': 'rw,seclabel,relatime,attr2,inode64,logbufs=8,logbsize=32k,noquota', 'size_total': 41067339776, 'size_available': 38660628480, 'block_size': 4096, 'block_total': 10026206, 'block_available': 9438630, 'block_used': 587576, 'inode_total': 20062208, 'inode_available': 20022327, 'inode_used': 39881, 'uuid': '7695a3a7-8d67-410a-92e2-d646aff2567a'})
skipping: [vm3] => (item={'mount': '/boot', 'device': '/dev/sda1', 'fstype': 'xfs', 'options': 'rw,seclabel,relatime,attr2,inode64,logbufs=8,logbsize=32k,noquota', 'size_total': 1063256064, 'size_available': 745537536, 'block_size': 4096, 'block_total': 259584, 'block_available': 182016, 'block_used': 77568, 'inode_total': 524288, 'inode_available': 523922, 'inode_used': 366, 'uuid': 'e6e29d07-7a35-4c61-b1ba-06bcd9195841'})
skipping: [vm3] => (item={'mount': '/home', 'device': '/dev/mapper/rhel-home', 'fstype': 'xfs', 'options': 'rw,seclabel,relatime,attr2,inode64,logbufs=8,logbsize=32k,noquota', 'size_total': 20050870272, 'size_available': 19876782080, 'block_size': 4096, 'block_total': 4895232, 'block_available': 4852730, 'block_used': 42502, 'inode_total': 9795584, 'inode_available': 9795565, 'inode_used': 19, 'uuid': 'f049c9ab-0c01-45e8-b644-807ccd4b7ad2'})
skipping: [vm2]
skipping: [vm3]
ok: [tower] => (item={'mount': '/', 'device': '/dev/mapper/rhel-root', 'fstype': 'xfs', 'options': 'rw,seclabel,relatime,attr2,inode64,logbufs=8,logbsize=32k,noquota', 'size_total': 51200024576, 'size_available': 44974895104, 'block_size': 4096, 'block_total': 12500006, 'block_available': 10980199, 'block_used': 1519807, 'inode_total': 25012224, 'inode_available': 24870270, 'inode_used': 141954, 'uuid': '1f43cc6d-56f5-4839-9153-5decddc58497'}) => {
    "msg": "My / has 44974895104 bytes"
}
skipping: [tower] => (item={'mount': '/boot', 'device': '/dev/sda1', 'fstype': 'xfs', 'options': 'rw,seclabel,relatime,attr2,inode64,logbufs=8,logbsize=32k,noquota', 'size_total': 1063256064, 'size_available': 654057472, 'block_size': 4096, 'block_total': 259584, 'block_available': 159682, 'block_used': 99902, 'inode_total': 524288, 'inode_available': 523922, 'inode_used': 366, 'uuid': 'a99b96c1-6539-47ca-b417-157cabe13a1d'})
skipping: [tower] => (item={'mount': '/home', 'device': '/dev/mapper/rhel-home', 'fstype': 'xfs', 'options': 'rw,seclabel,relatime,attr2,inode64,logbufs=8,logbsize=32k,noquota', 'size_total': 24994230272, 'size_available': 14021292032, 'block_size': 4096, 'block_total': 6102107, 'block_available': 3423167, 'block_used': 2678940, 'inode_total': 12210176, 'inode_available': 11545835, 'inode_used': 664341, 'uuid': '8d6ad5e3-7c21-4e9c-91e5-c956c3186af7'})
skipping: [vm4] => (item={'mount': '/', 'device': '/dev/sda3', 'fstype': 'ext4', 'options': 'rw,relatime,errors=remount-ro', 'size_total': 41424257024, 'size_available': 24597819392, 'block_size': 4096, 'block_total': 10113344, 'block_available': 6005327, 'block_used': 4108017, 'inode_total': 2588672, 'inode_available': 2388196, 'inode_used': 200476, 'uuid': '9ad81ce1-1b1e-4cd3-9bfa-58a1225ca997'})
skipping: [vm4] => (item={'mount': '/snap/bare/5', 'device': '/dev/loop0', 'fstype': 'squashfs', 'options': 'ro,nodev,relatime,errors=continue,threads=single', 'size_total': 131072, 'size_available': 0, 'block_size': 131072, 'block_total': 1, 'block_available': 0, 'block_used': 1, 'inode_total': 29, 'inode_available': 0, 'inode_used': 29, 'uuid': 'N/A'})
skipping: [vm4] => (item={'mount': '/snap/core20/1587', 'device': '/dev/loop1', 'fstype': 'squashfs', 'options': 'ro,nodev,relatime,errors=continue,threads=single', 'size_total': 65011712, 'size_available': 0, 'block_size': 131072, 'block_total': 496, 'block_available': 0, 'block_used': 496, 'inode_total': 11793, 'inode_available': 0, 'inode_used': 11793, 'uuid': 'N/A'})
skipping: [vm4] => (item={'mount': '/snap/core22/2411', 'device': '/dev/loop2', 'fstype': 'squashfs', 'options': 'ro,nodev,relatime,errors=continue,threads=single', 'size_total': 77594624, 'size_available': 0, 'block_size': 131072, 'block_total': 592, 'block_available': 0, 'block_used': 592, 'inode_total': 14270, 'inode_available': 0, 'inode_used': 14270, 'uuid': 'N/A'})
skipping: [vm4] => (item={'mount': '/snap/firefox/1635', 'device': '/dev/loop4', 'fstype': 'squashfs', 'options': 'ro,nodev,relatime,errors=continue,threads=single', 'size_total': 171311104, 'size_available': 0, 'block_size': 131072, 'block_total': 1307, 'block_available': 0, 'block_used': 1307, 'inode_total': 438, 'inode_available': 0, 'inode_used': 438, 'uuid': 'N/A'})
skipping: [vm4] => (item={'mount': '/snap/core24/1643', 'device': '/dev/loop3', 'fstype': 'squashfs', 'options': 'ro,nodev,relatime,errors=continue,threads=single', 'size_total': 70123520, 'size_available': 0, 'block_size': 131072, 'block_total': 535, 'block_available': 0, 'block_used': 535, 'inode_total': 10545, 'inode_available': 0, 'inode_used': 10545, 'uuid': 'N/A'})
skipping: [vm4] => (item={'mount': '/snap/firefox/8702', 'device': '/dev/loop5', 'fstype': 'squashfs', 'options': 'ro,nodev,relatime,errors=continue,threads=single', 'size_total': 270139392, 'size_available': 0, 'block_size': 131072, 'block_total': 2061, 'block_available': 0, 'block_used': 2061, 'inode_total': 641, 'inode_available': 0, 'inode_used': 641, 'uuid': 'N/A'})
skipping: [vm4] => (item={'mount': '/snap/gnome-3-38-2004/143', 'device': '/dev/loop7', 'fstype': 'squashfs', 'options': 'ro,nodev,relatime,errors=continue,threads=single', 'size_total': 366739456, 'size_available': 0, 'block_size': 131072, 'block_total': 2798, 'block_available': 0, 'block_used': 2798, 'inode_total': 18307, 'inode_available': 0, 'inode_used': 18307, 'uuid': 'N/A'})
skipping: [vm4] => (item={'mount': '/snap/gnome-3-38-2004/112', 'device': '/dev/loop6', 'fstype': 'squashfs', 'options': 'ro,nodev,relatime,errors=continue,threads=single', 'size_total': 420347904, 'size_available': 0, 'block_size': 131072, 'block_total': 3207, 'block_available': 0, 'block_used': 3207, 'inode_total': 18120, 'inode_available': 0, 'inode_used': 18120, 'uuid': 'N/A'})
skipping: [vm4] => (item={'mount': '/snap/gnome-42-2204/263', 'device': '/dev/loop8', 'fstype': 'squashfs', 'options': 'ro,nodev,relatime,errors=continue,threads=single', 'size_total': 557318144, 'size_available': 0, 'block_size': 131072, 'block_total': 4252, 'block_available': 0, 'block_used': 4252, 'inode_total': 21236, 'inode_available': 0, 'inode_used': 21236, 'uuid': 'N/A'})
skipping: [vm4] => (item={'mount': '/snap/gnome-46-2404/164', 'device': '/dev/loop9', 'fstype': 'squashfs', 'options': 'ro,nodev,relatime,errors=continue,threads=single', 'size_total': 644481024, 'size_available': 0, 'block_size': 131072, 'block_total': 4917, 'block_available': 0, 'block_used': 4917, 'inode_total': 23643, 'inode_available': 0, 'inode_used': 23643, 'uuid': 'N/A'})
skipping: [vm4] => (item={'mount': '/snap/gtk-common-themes/1535', 'device': '/dev/loop10', 'fstype': 'squashfs', 'options': 'ro,nodev,relatime,errors=continue,threads=single', 'size_total': 96206848, 'size_available': 0, 'block_size': 131072, 'block_total': 734, 'block_available': 0, 'block_used': 734, 'inode_total': 76208, 'inode_available': 0, 'inode_used': 76208, 'uuid': 'N/A'})
skipping: [vm4] => (item={'mount': '/snap/mesa-2404/1839', 'device': '/dev/loop11', 'fstype': 'squashfs', 'options': 'ro,nodev,relatime,errors=continue,threads=single', 'size_total': 421527552, 'size_available': 0, 'block_size': 131072, 'block_total': 3216, 'block_available': 0, 'block_used': 3216, 'inode_total': 872, 'inode_available': 0, 'inode_used': 872, 'uuid': 'N/A'})
skipping: [vm4] => (item={'mount': '/snap/snap-store/582', 'device': '/dev/loop13', 'fstype': 'squashfs', 'options': 'ro,nodev,relatime,errors=continue,threads=single', 'size_total': 48103424, 'size_available': 0, 'block_size': 131072, 'block_total': 367, 'block_available': 0, 'block_used': 367, 'inode_total': 17250, 'inode_available': 0, 'inode_used': 17250, 'uuid': 'N/A'})
skipping: [vm4] => (item={'mount': '/snap/snap-store/1216', 'device': '/dev/loop12', 'fstype': 'squashfs', 'options': 'ro,nodev,relatime,errors=continue,threads=single', 'size_total': 12845056, 'size_available': 0, 'block_size': 131072, 'block_total': 98, 'block_available': 0, 'block_used': 98, 'inode_total': 1934, 'inode_available': 0, 'inode_used': 1934, 'uuid': 'N/A'})
skipping: [vm4] => (item={'mount': '/snap/snapd-desktop-integration/14', 'device': '/dev/loop15', 'fstype': 'squashfs', 'options': 'ro,nodev,relatime,errors=continue,threads=single', 'size_total': 393216, 'size_available': 0, 'block_size': 131072, 'block_total': 3, 'block_available': 0, 'block_used': 3, 'inode_total': 24, 'inode_available': 0, 'inode_used': 24, 'uuid': 'N/A'})
skipping: [vm4] => (item={'mount': '/snap/snapd/27591', 'device': '/dev/loop14', 'fstype': 'squashfs', 'options': 'ro,nodev,relatime,errors=continue,threads=single', 'size_total': 52559872, 'size_available': 0, 'block_size': 131072, 'block_total': 401, 'block_available': 0, 'block_used': 401, 'inode_total': 610, 'inode_available': 0, 'inode_used': 610, 'uuid': 'N/A'})
skipping: [vm4] => (item={'mount': '/boot/efi', 'device': '/dev/sda2', 'fstype': 'vfat', 'options': 'rw,relatime,fmask=0077,dmask=0077,codepage=437,iocharset=iso8859-1,shortname=mixed,errors=remount-ro', 'size_total': 536854528, 'size_available': 530472960, 'block_size': 4096, 'block_total': 131068, 'block_available': 129510, 'block_used': 1558, 'inode_total': 0, 'inode_available': 0, 'inode_used': 0, 'uuid': '0077-6A60'})
skipping: [vm4]

PLAY RECAP ***************************************************************************************************************************************************************************************************
tower                      : ok=2    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
vm1                        : ok=1    changed=0    unreachable=0    failed=0    skipped=1    rescued=0    ignored=0
vm2                        : ok=1    changed=0    unreachable=0    failed=0    skipped=1    rescued=0    ignored=0
vm3                        : ok=1    changed=0    unreachable=0    failed=0    skipped=1    rescued=0    ignored=0
vm4                        : ok=1    changed=0    unreachable=0    failed=0    skipped=1    rescued=0    ignored=0
webserver                  : ok=1    changed=0    unreachable=0    failed=0    skipped=1    rescued=0    ignored=0
```

</details>


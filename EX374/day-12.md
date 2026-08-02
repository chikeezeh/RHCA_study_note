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

#### Combining `when` and `register`

We can run a task, store the output using register in a variable, then use that variable in another task as a conditional.

Example below, we output the content of the /etc/passwd file, and then check if a particular user is present. 

```yaml
---
- name: Combining register and when conditionals
  hosts: rhel,ubuntu
  tasks:
  - name: get the ouput of /etc/passwd
    shell: cat /etc/passwd
    register: passwd_content
  - name: show the content of passwd
    debug:
      var: passwd_content
  - name: show only the content user apache
    debug:
      msg: /etc/passwd file contains user apache
    when: passwd_content.stdout.find('apache') != -1
```
Output:


<details>
<summary> Click to expand </summary>

```shell
[ansible@control ansible_work]$ ansible-playbook registerwhen.yml

PLAY [Combining register and when conditionals] **************************************************************************************************************************************************************

TASK [Gathering Facts] ***************************************************************************************************************************************************************************************
ok: [vm4]
ok: [vm3]
ok: [vm1]
ok: [vm2]

TASK [get the ouput of /etc/passwd] **************************************************************************************************************************************************************************
changed: [vm4]
changed: [vm3]
changed: [vm1]
changed: [vm2]

TASK [show the content of passwd] ****************************************************************************************************************************************************************************
ok: [vm1] => {
    "passwd_content": {
        "changed": true,
        "cmd": "cat /etc/passwd",
        "delta": "0:00:00.002645",
        "end": "2026-08-01 19:04:29.387469",
        "failed": false,
        "msg": "",
        "rc": 0,
        "start": "2026-08-01 19:04:29.384824",
        "stderr": "",
        "stderr_lines": [],
        "stdout": "root:x:0:0:root:/root:/bin/bash\nbin:x:1:1:bin:/bin:/sbin/nologin\ndaemon:x:2:2:daemon:/sbin:/sbin/nologin\nadm:x:3:4:adm:/var/adm:/sbin/nologin\nlp:x:4:7:lp:/var/spool/lpd:/sbin/nologin\nsync:x:5:0:sync:/sbin:/bin/sync\nshutdown:x:6:0:shutdown:/sbin:/sbin/shutdown\nhalt:x:7:0:halt:/sbin:/sbin/halt\nmail:x:8:12:mail:/var/spool/mail:/sbin/nologin\noperator:x:11:0:operator:/root:/sbin/nologin\ngames:x:12:100:games:/usr/games:/sbin/nologin\nftp:x:14:50:FTP User:/var/ftp:/sbin/nologin\nnobody:x:65534:65534:Kernel Overflow User:/:/sbin/nologin\nsystemd-coredump:x:999:997:systemd Core Dumper:/:/sbin/nologin\ndbus:x:81:81:System message bus:/:/sbin/nologin\ntss:x:59:59:Account used for TPM access:/dev/null:/sbin/nologin\nsssd:x:998:995:User for sssd:/:/sbin/nologin\nsshd:x:74:74:Privilege-separated SSH:/usr/share/empty.sshd:/sbin/nologin\nsystemd-oom:x:993:993:systemd Userspace OOM Killer:/:/usr/sbin/nologin\ncezeh:x:1000:1000:Chike Ezeh:/home/cezeh:/bin/bash\nansible:x:1001:1001::/home/ansible:/bin/bash\napache:x:48:48:Apache:/usr/share/httpd:/sbin/nologin",
        "stdout_lines": [
            "root:x:0:0:root:/root:/bin/bash",
            "bin:x:1:1:bin:/bin:/sbin/nologin",
            "daemon:x:2:2:daemon:/sbin:/sbin/nologin",
            "adm:x:3:4:adm:/var/adm:/sbin/nologin",
            "lp:x:4:7:lp:/var/spool/lpd:/sbin/nologin",
            "sync:x:5:0:sync:/sbin:/bin/sync",
            "shutdown:x:6:0:shutdown:/sbin:/sbin/shutdown",
            "halt:x:7:0:halt:/sbin:/sbin/halt",
            "mail:x:8:12:mail:/var/spool/mail:/sbin/nologin",
            "operator:x:11:0:operator:/root:/sbin/nologin",
            "games:x:12:100:games:/usr/games:/sbin/nologin",
            "ftp:x:14:50:FTP User:/var/ftp:/sbin/nologin",
            "nobody:x:65534:65534:Kernel Overflow User:/:/sbin/nologin",
            "systemd-coredump:x:999:997:systemd Core Dumper:/:/sbin/nologin",
            "dbus:x:81:81:System message bus:/:/sbin/nologin",
            "tss:x:59:59:Account used for TPM access:/dev/null:/sbin/nologin",
            "sssd:x:998:995:User for sssd:/:/sbin/nologin",
            "sshd:x:74:74:Privilege-separated SSH:/usr/share/empty.sshd:/sbin/nologin",
            "systemd-oom:x:993:993:systemd Userspace OOM Killer:/:/usr/sbin/nologin",
            "cezeh:x:1000:1000:Chike Ezeh:/home/cezeh:/bin/bash",
            "ansible:x:1001:1001::/home/ansible:/bin/bash",
            "apache:x:48:48:Apache:/usr/share/httpd:/sbin/nologin"
        ]
    }
}
ok: [vm2] => {
    "passwd_content": {
        "changed": true,
        "cmd": "cat /etc/passwd",
        "delta": "0:00:00.002642",
        "end": "2026-08-01 19:04:30.300970",
        "failed": false,
        "msg": "",
        "rc": 0,
        "start": "2026-08-01 19:04:30.298328",
        "stderr": "",
        "stderr_lines": [],
        "stdout": "root:x:0:0:root:/root:/bin/bash\nbin:x:1:1:bin:/bin:/sbin/nologin\ndaemon:x:2:2:daemon:/sbin:/sbin/nologin\nadm:x:3:4:adm:/var/adm:/sbin/nologin\nlp:x:4:7:lp:/var/spool/lpd:/sbin/nologin\nsync:x:5:0:sync:/sbin:/bin/sync\nshutdown:x:6:0:shutdown:/sbin:/sbin/shutdown\nhalt:x:7:0:halt:/sbin:/sbin/halt\nmail:x:8:12:mail:/var/spool/mail:/sbin/nologin\noperator:x:11:0:operator:/root:/sbin/nologin\ngames:x:12:100:games:/usr/games:/sbin/nologin\nftp:x:14:50:FTP User:/var/ftp:/sbin/nologin\nnobody:x:65534:65534:Kernel Overflow User:/:/sbin/nologin\nsystemd-coredump:x:999:997:systemd Core Dumper:/:/sbin/nologin\ndbus:x:81:81:System message bus:/:/sbin/nologin\ntss:x:59:59:Account used for TPM access:/dev/null:/sbin/nologin\nsssd:x:998:995:User for sssd:/:/sbin/nologin\nsshd:x:74:74:Privilege-separated SSH:/usr/share/empty.sshd:/sbin/nologin\nsystemd-oom:x:993:993:systemd Userspace OOM Killer:/:/usr/sbin/nologin\ncezeh:x:1000:1000:Chike Ezeh:/home/cezeh:/bin/bash\nansible:x:1001:1001::/home/ansible:/bin/bash\napache:x:48:48:Apache:/usr/share/httpd:/sbin/nologin",
        "stdout_lines": [
            "root:x:0:0:root:/root:/bin/bash",
            "bin:x:1:1:bin:/bin:/sbin/nologin",
            "daemon:x:2:2:daemon:/sbin:/sbin/nologin",
            "adm:x:3:4:adm:/var/adm:/sbin/nologin",
            "lp:x:4:7:lp:/var/spool/lpd:/sbin/nologin",
            "sync:x:5:0:sync:/sbin:/bin/sync",
            "shutdown:x:6:0:shutdown:/sbin:/sbin/shutdown",
            "halt:x:7:0:halt:/sbin:/sbin/halt",
            "mail:x:8:12:mail:/var/spool/mail:/sbin/nologin",
            "operator:x:11:0:operator:/root:/sbin/nologin",
            "games:x:12:100:games:/usr/games:/sbin/nologin",
            "ftp:x:14:50:FTP User:/var/ftp:/sbin/nologin",
            "nobody:x:65534:65534:Kernel Overflow User:/:/sbin/nologin",
            "systemd-coredump:x:999:997:systemd Core Dumper:/:/sbin/nologin",
            "dbus:x:81:81:System message bus:/:/sbin/nologin",
            "tss:x:59:59:Account used for TPM access:/dev/null:/sbin/nologin",
            "sssd:x:998:995:User for sssd:/:/sbin/nologin",
            "sshd:x:74:74:Privilege-separated SSH:/usr/share/empty.sshd:/sbin/nologin",
            "systemd-oom:x:993:993:systemd Userspace OOM Killer:/:/usr/sbin/nologin",
            "cezeh:x:1000:1000:Chike Ezeh:/home/cezeh:/bin/bash",
            "ansible:x:1001:1001::/home/ansible:/bin/bash",
            "apache:x:48:48:Apache:/usr/share/httpd:/sbin/nologin"
        ]
    }
}
ok: [vm3] => {
    "passwd_content": {
        "changed": true,
        "cmd": "cat /etc/passwd",
        "delta": "0:00:00.002569",
        "end": "2026-08-01 19:04:29.454583",
        "failed": false,
        "msg": "",
        "rc": 0,
        "start": "2026-08-01 19:04:29.452014",
        "stderr": "",
        "stderr_lines": [],
        "stdout": "root:x:0:0:root:/root:/bin/bash\nbin:x:1:1:bin:/bin:/sbin/nologin\ndaemon:x:2:2:daemon:/sbin:/sbin/nologin\nadm:x:3:4:adm:/var/adm:/sbin/nologin\nlp:x:4:7:lp:/var/spool/lpd:/sbin/nologin\nsync:x:5:0:sync:/sbin:/bin/sync\nshutdown:x:6:0:shutdown:/sbin:/sbin/shutdown\nhalt:x:7:0:halt:/sbin:/sbin/halt\nmail:x:8:12:mail:/var/spool/mail:/sbin/nologin\noperator:x:11:0:operator:/root:/sbin/nologin\ngames:x:12:100:games:/usr/games:/sbin/nologin\nftp:x:14:50:FTP User:/var/ftp:/sbin/nologin\nnobody:x:65534:65534:Kernel Overflow User:/:/sbin/nologin\nsystemd-coredump:x:999:997:systemd Core Dumper:/:/sbin/nologin\ndbus:x:81:81:System message bus:/:/sbin/nologin\ntss:x:59:59:Account used for TPM access:/dev/null:/sbin/nologin\nsssd:x:998:995:User for sssd:/:/sbin/nologin\nsshd:x:74:74:Privilege-separated SSH:/usr/share/empty.sshd:/sbin/nologin\nsystemd-oom:x:993:993:systemd Userspace OOM Killer:/:/usr/sbin/nologin\ncezeh:x:1000:1000:Chike Ezeh:/home/cezeh:/bin/bash\nansible:x:1001:1001::/home/ansible:/bin/bash\napache:x:48:48:Apache:/usr/share/httpd:/sbin/nologin",
        "stdout_lines": [
            "root:x:0:0:root:/root:/bin/bash",
            "bin:x:1:1:bin:/bin:/sbin/nologin",
            "daemon:x:2:2:daemon:/sbin:/sbin/nologin",
            "adm:x:3:4:adm:/var/adm:/sbin/nologin",
            "lp:x:4:7:lp:/var/spool/lpd:/sbin/nologin",
            "sync:x:5:0:sync:/sbin:/bin/sync",
            "shutdown:x:6:0:shutdown:/sbin:/sbin/shutdown",
            "halt:x:7:0:halt:/sbin:/sbin/halt",
            "mail:x:8:12:mail:/var/spool/mail:/sbin/nologin",
            "operator:x:11:0:operator:/root:/sbin/nologin",
            "games:x:12:100:games:/usr/games:/sbin/nologin",
            "ftp:x:14:50:FTP User:/var/ftp:/sbin/nologin",
            "nobody:x:65534:65534:Kernel Overflow User:/:/sbin/nologin",
            "systemd-coredump:x:999:997:systemd Core Dumper:/:/sbin/nologin",
            "dbus:x:81:81:System message bus:/:/sbin/nologin",
            "tss:x:59:59:Account used for TPM access:/dev/null:/sbin/nologin",
            "sssd:x:998:995:User for sssd:/:/sbin/nologin",
            "sshd:x:74:74:Privilege-separated SSH:/usr/share/empty.sshd:/sbin/nologin",
            "systemd-oom:x:993:993:systemd Userspace OOM Killer:/:/usr/sbin/nologin",
            "cezeh:x:1000:1000:Chike Ezeh:/home/cezeh:/bin/bash",
            "ansible:x:1001:1001::/home/ansible:/bin/bash",
            "apache:x:48:48:Apache:/usr/share/httpd:/sbin/nologin"
        ]
    }
}
ok: [vm4] => {
    "passwd_content": {
        "changed": true,
        "cmd": "cat /etc/passwd",
        "delta": "0:00:00.003292",
        "end": "2026-08-01 19:04:29.521206",
        "failed": false,
        "msg": "",
        "rc": 0,
        "start": "2026-08-01 19:04:29.517914",
        "stderr": "",
        "stderr_lines": [],
        "stdout": "root:x:0:0:root:/root:/bin/bash\ndaemon:x:1:1:daemon:/usr/sbin:/usr/sbin/nologin\nbin:x:2:2:bin:/bin:/usr/sbin/nologin\nsys:x:3:3:sys:/dev:/usr/sbin/nologin\nsync:x:4:65534:sync:/bin:/bin/sync\ngames:x:5:60:games:/usr/games:/usr/sbin/nologin\nman:x:6:12:man:/var/cache/man:/usr/sbin/nologin\nlp:x:7:7:lp:/var/spool/lpd:/usr/sbin/nologin\nmail:x:8:8:mail:/var/mail:/usr/sbin/nologin\nnews:x:9:9:news:/var/spool/news:/usr/sbin/nologin\nuucp:x:10:10:uucp:/var/spool/uucp:/usr/sbin/nologin\nproxy:x:13:13:proxy:/bin:/usr/sbin/nologin\nwww-data:x:33:33:www-data:/var/www:/usr/sbin/nologin\nbackup:x:34:34:backup:/var/backups:/usr/sbin/nologin\nlist:x:38:38:Mailing List Manager:/var/list:/usr/sbin/nologin\nirc:x:39:39:ircd:/run/ircd:/usr/sbin/nologin\ngnats:x:41:41:Gnats Bug-Reporting System (admin):/var/lib/gnats:/usr/sbin/nologin\nnobody:x:65534:65534:nobody:/nonexistent:/usr/sbin/nologin\nsystemd-network:x:100:102:systemd Network Management,,,:/run/systemd:/usr/sbin/nologin\nsystemd-resolve:x:101:103:systemd Resolver,,,:/run/systemd:/usr/sbin/nologin\nmessagebus:x:102:105::/nonexistent:/usr/sbin/nologin\nsystemd-timesync:x:103:106:systemd Time Synchronization,,,:/run/systemd:/usr/sbin/nologin\nsyslog:x:104:111::/home/syslog:/usr/sbin/nologin\n_apt:x:105:65534::/nonexistent:/usr/sbin/nologin\ntss:x:106:112:TPM software stack,,,:/var/lib/tpm:/bin/false\nuuidd:x:107:115::/run/uuidd:/usr/sbin/nologin\nsystemd-oom:x:108:116:systemd Userspace OOM Killer,,,:/run/systemd:/usr/sbin/nologin\ntcpdump:x:109:117::/nonexistent:/usr/sbin/nologin\navahi-autoipd:x:110:119:Avahi autoip daemon,,,:/var/lib/avahi-autoipd:/usr/sbin/nologin\nusbmux:x:111:46:usbmux daemon,,,:/var/lib/usbmux:/usr/sbin/nologin\ndnsmasq:x:112:65534:dnsmasq,,,:/var/lib/misc:/usr/sbin/nologin\nkernoops:x:113:65534:Kernel Oops Tracking Daemon,,,:/:/usr/sbin/nologin\navahi:x:114:121:Avahi mDNS daemon,,,:/run/avahi-daemon:/usr/sbin/nologin\ncups-pk-helper:x:115:122:user for cups-pk-helper service,,,:/home/cups-pk-helper:/usr/sbin/nologin\nrtkit:x:116:123:RealtimeKit,,,:/proc:/usr/sbin/nologin\nwhoopsie:x:117:124::/nonexistent:/bin/false\nsssd:x:118:125:SSSD system user,,,:/var/lib/sss:/usr/sbin/nologin\nspeech-dispatcher:x:119:29:Speech Dispatcher,,,:/run/speech-dispatcher:/bin/false\nnm-openvpn:x:120:126:NetworkManager OpenVPN,,,:/var/lib/openvpn/chroot:/usr/sbin/nologin\nsaned:x:121:128::/var/lib/saned:/usr/sbin/nologin\ncolord:x:122:129:colord colour management daemon,,,:/var/lib/colord:/usr/sbin/nologin\ngeoclue:x:123:130::/var/lib/geoclue:/usr/sbin/nologin\npulse:x:124:131:PulseAudio daemon,,,:/run/pulse:/usr/sbin/nologin\ngnome-initial-setup:x:125:65534::/run/gnome-initial-setup/:/bin/false\nhplip:x:126:7:HPLIP system user,,,:/run/hplip:/bin/false\ngdm:x:127:133:Gnome Display Manager:/var/lib/gdm3:/bin/false\ncezeh:x:1000:1000:Chike Ezeh,,,:/home/cezeh:/bin/bash\nansible:x:1001:1002::/home/ansible:/bin/bash\nfwupd-refresh:x:999:999:Firmware update daemon:/var/lib/fwupd:/usr/sbin/nologin\nsshd:x:128:65534::/run/sshd:/usr/sbin/nologin",
        "stdout_lines": [
            "root:x:0:0:root:/root:/bin/bash",
            "daemon:x:1:1:daemon:/usr/sbin:/usr/sbin/nologin",
            "bin:x:2:2:bin:/bin:/usr/sbin/nologin",
            "sys:x:3:3:sys:/dev:/usr/sbin/nologin",
            "sync:x:4:65534:sync:/bin:/bin/sync",
            "games:x:5:60:games:/usr/games:/usr/sbin/nologin",
            "man:x:6:12:man:/var/cache/man:/usr/sbin/nologin",
            "lp:x:7:7:lp:/var/spool/lpd:/usr/sbin/nologin",
            "mail:x:8:8:mail:/var/mail:/usr/sbin/nologin",
            "news:x:9:9:news:/var/spool/news:/usr/sbin/nologin",
            "uucp:x:10:10:uucp:/var/spool/uucp:/usr/sbin/nologin",
            "proxy:x:13:13:proxy:/bin:/usr/sbin/nologin",
            "www-data:x:33:33:www-data:/var/www:/usr/sbin/nologin",
            "backup:x:34:34:backup:/var/backups:/usr/sbin/nologin",
            "list:x:38:38:Mailing List Manager:/var/list:/usr/sbin/nologin",
            "irc:x:39:39:ircd:/run/ircd:/usr/sbin/nologin",
            "gnats:x:41:41:Gnats Bug-Reporting System (admin):/var/lib/gnats:/usr/sbin/nologin",
            "nobody:x:65534:65534:nobody:/nonexistent:/usr/sbin/nologin",
            "systemd-network:x:100:102:systemd Network Management,,,:/run/systemd:/usr/sbin/nologin",
            "systemd-resolve:x:101:103:systemd Resolver,,,:/run/systemd:/usr/sbin/nologin",
            "messagebus:x:102:105::/nonexistent:/usr/sbin/nologin",
            "systemd-timesync:x:103:106:systemd Time Synchronization,,,:/run/systemd:/usr/sbin/nologin",
            "syslog:x:104:111::/home/syslog:/usr/sbin/nologin",
            "_apt:x:105:65534::/nonexistent:/usr/sbin/nologin",
            "tss:x:106:112:TPM software stack,,,:/var/lib/tpm:/bin/false",
            "uuidd:x:107:115::/run/uuidd:/usr/sbin/nologin",
            "systemd-oom:x:108:116:systemd Userspace OOM Killer,,,:/run/systemd:/usr/sbin/nologin",
            "tcpdump:x:109:117::/nonexistent:/usr/sbin/nologin",
            "avahi-autoipd:x:110:119:Avahi autoip daemon,,,:/var/lib/avahi-autoipd:/usr/sbin/nologin",
            "usbmux:x:111:46:usbmux daemon,,,:/var/lib/usbmux:/usr/sbin/nologin",
            "dnsmasq:x:112:65534:dnsmasq,,,:/var/lib/misc:/usr/sbin/nologin",
            "kernoops:x:113:65534:Kernel Oops Tracking Daemon,,,:/:/usr/sbin/nologin",
            "avahi:x:114:121:Avahi mDNS daemon,,,:/run/avahi-daemon:/usr/sbin/nologin",
            "cups-pk-helper:x:115:122:user for cups-pk-helper service,,,:/home/cups-pk-helper:/usr/sbin/nologin",
            "rtkit:x:116:123:RealtimeKit,,,:/proc:/usr/sbin/nologin",
            "whoopsie:x:117:124::/nonexistent:/bin/false",
            "sssd:x:118:125:SSSD system user,,,:/var/lib/sss:/usr/sbin/nologin",
            "speech-dispatcher:x:119:29:Speech Dispatcher,,,:/run/speech-dispatcher:/bin/false",
            "nm-openvpn:x:120:126:NetworkManager OpenVPN,,,:/var/lib/openvpn/chroot:/usr/sbin/nologin",
            "saned:x:121:128::/var/lib/saned:/usr/sbin/nologin",
            "colord:x:122:129:colord colour management daemon,,,:/var/lib/colord:/usr/sbin/nologin",
            "geoclue:x:123:130::/var/lib/geoclue:/usr/sbin/nologin",
            "pulse:x:124:131:PulseAudio daemon,,,:/run/pulse:/usr/sbin/nologin",
            "gnome-initial-setup:x:125:65534::/run/gnome-initial-setup/:/bin/false",
            "hplip:x:126:7:HPLIP system user,,,:/run/hplip:/bin/false",
            "gdm:x:127:133:Gnome Display Manager:/var/lib/gdm3:/bin/false",
            "cezeh:x:1000:1000:Chike Ezeh,,,:/home/cezeh:/bin/bash",
            "ansible:x:1001:1002::/home/ansible:/bin/bash",
            "fwupd-refresh:x:999:999:Firmware update daemon:/var/lib/fwupd:/usr/sbin/nologin",
            "sshd:x:128:65534::/run/sshd:/usr/sbin/nologin"
        ]
    }
}

TASK [show only the content user apache] *********************************************************************************************************************************************************************
ok: [vm1] => {
    "msg": "/etc/passwd file contains user apache"
}
ok: [vm2] => {
    "msg": "/etc/passwd file contains user apache"
}
ok: [vm3] => {
    "msg": "/etc/passwd file contains user apache"
}
skipping: [vm4]

PLAY RECAP ***************************************************************************************************************************************************************************************************
vm1                        : ok=4    changed=1    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
vm2                        : ok=4    changed=1    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
vm3                        : ok=4    changed=1    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
vm4                        : ok=3    changed=1    unreachable=0    failed=0    skipped=1    rescued=0    ignored=0

```

</details>

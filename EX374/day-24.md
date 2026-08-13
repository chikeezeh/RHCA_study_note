#### Using Delegation
Delegation lets you run tasks on another hosts. The `delegate_to` task property is used to achieve this functionality. This is useful when you want to write a play that affects multiple hosts, but you want a specific task to run on a specific host without starting a new play. 

#### Requirement for delegation
1. delegation host needs to meet the requirements of the control host.
   1. Python is installed.
   2. SSH access to managed hosts is enabled.
   3. SSH keys are copied over.
   4. Hostname to IP address resolution works.
2. `delegate_to` host must exist in the inventory on the control host.

##### Example of delegation

We will copy a file from control host to only vm2, but we still want the play to affect all the host in inventory. Only the copy task will be delegated to vm2.


```yaml
---
- name: playbook to demonstrate delegation.
  hosts: all
  tasks:
  - name: This task runs on all the hosts.
    debug:
      msg: I am {{ ansible_hostname }}
  - name: copy a file to only vm2
    copy:
      src: /etc/hosts
      dest: /tmp/copyfile
    delegate_to: vm2
  - name: check if our copy worked
    stat:
      path: /tmp/copyfile
    delegate_to: vm2
    register: statfile
  - debug:
      var: statfile
    delegate_to: vm2
```

Output:

<details>
<summary> Click to expand </summary>

```shell
[ansible@control ansible_work]$ ansible-playbook copy-files.yml

PLAY [playbook to demonstrate delegation.] ***********************************************************************************************************************************************************

TASK [Gathering Facts] *******************************************************************************************************************************************************************************
ok: [vm2]
ok: [vm1]
ok: [vm3]
ok: [webserver]
ok: [tower]
ok: [vm4]

TASK [This task runs on all the hosts.] **************************************************************************************************************************************************************
ok: [webserver] => {
    "msg": "I am webserver"
}
ok: [vm1] => {
    "msg": "I am vm1"
}
ok: [vm2] => {
    "msg": "I am vm2"
}
ok: [vm3] => {
    "msg": "I am vm3"
}
ok: [tower] => {
    "msg": "I am tower"
}
ok: [vm4] => {
    "msg": "I am vm4"
}

TASK [copy a file to only vm2] ***********************************************************************************************************************************************************************
ok: [vm1 -> vm2]
ok: [webserver -> vm2]
ok: [vm3 -> vm2]
ok: [tower -> vm2]
ok: [vm2]
ok: [vm4 -> vm2]

TASK [check if our copy worked] **********************************************************************************************************************************************************************
ok: [vm2]
ok: [vm1 -> vm2]
ok: [vm3 -> vm2]
ok: [webserver -> vm2]
ok: [tower -> vm2]
ok: [vm4 -> vm2]

TASK [debug] *****************************************************************************************************************************************************************************************
ok: [webserver -> vm2] => {
    "statfile": {
        "changed": false,
        "failed": false,
        "stat": {
            "atime": 1786657129.928,
            "attr_flags": "",
            "attributes": [],
            "block_size": 4096,
            "blocks": 8,
            "charset": "us-ascii",
            "checksum": "c6a7807014974797c16d8a35b976ee62559a8510",
            "ctime": 1786656965.064,
            "dev": 64768,
            "device_type": 0,
            "executable": false,
            "exists": true,
            "gid": 0,
            "gr_name": "root",
            "inode": 67149324,
            "isblk": false,
            "ischr": false,
            "isdir": false,
            "isfifo": false,
            "isgid": false,
            "islnk": false,
            "isreg": true,
            "issock": false,
            "isuid": false,
            "mimetype": "text/plain",
            "mode": "0644",
            "mtime": 1786656964.857,
            "nlink": 1,
            "path": "/tmp/copyfile",
            "pw_name": "root",
            "readable": true,
            "rgrp": true,
            "roth": true,
            "rusr": true,
            "size": 613,
            "uid": 0,
            "version": "1254379387",
            "wgrp": false,
            "woth": false,
            "writeable": true,
            "wusr": true,
            "xgrp": false,
            "xoth": false,
            "xusr": false
        }
    }
}
ok: [vm1 -> vm2] => {
    "statfile": {
        "changed": false,
        "failed": false,
        "stat": {
            "atime": 1786657129.928,
            "attr_flags": "",
            "attributes": [],
            "block_size": 4096,
            "blocks": 8,
            "charset": "us-ascii",
            "checksum": "c6a7807014974797c16d8a35b976ee62559a8510",
            "ctime": 1786656965.064,
            "dev": 64768,
            "device_type": 0,
            "executable": false,
            "exists": true,
            "gid": 0,
            "gr_name": "root",
            "inode": 67149324,
            "isblk": false,
            "ischr": false,
            "isdir": false,
            "isfifo": false,
            "isgid": false,
            "islnk": false,
            "isreg": true,
            "issock": false,
            "isuid": false,
            "mimetype": "text/plain",
            "mode": "0644",
            "mtime": 1786656964.857,
            "nlink": 1,
            "path": "/tmp/copyfile",
            "pw_name": "root",
            "readable": true,
            "rgrp": true,
            "roth": true,
            "rusr": true,
            "size": 613,
            "uid": 0,
            "version": "1254379387",
            "wgrp": false,
            "woth": false,
            "writeable": true,
            "wusr": true,
            "xgrp": false,
            "xoth": false,
            "xusr": false
        }
    }
}
ok: [vm2] => {
    "statfile": {
        "changed": false,
        "failed": false,
        "stat": {
            "atime": 1786657129.928,
            "attr_flags": "",
            "attributes": [],
            "block_size": 4096,
            "blocks": 8,
            "charset": "us-ascii",
            "checksum": "c6a7807014974797c16d8a35b976ee62559a8510",
            "ctime": 1786656965.064,
            "dev": 64768,
            "device_type": 0,
            "executable": false,
            "exists": true,
            "gid": 0,
            "gr_name": "root",
            "inode": 67149324,
            "isblk": false,
            "ischr": false,
            "isdir": false,
            "isfifo": false,
            "isgid": false,
            "islnk": false,
            "isreg": true,
            "issock": false,
            "isuid": false,
            "mimetype": "text/plain",
            "mode": "0644",
            "mtime": 1786656964.857,
            "nlink": 1,
            "path": "/tmp/copyfile",
            "pw_name": "root",
            "readable": true,
            "rgrp": true,
            "roth": true,
            "rusr": true,
            "size": 613,
            "uid": 0,
            "version": "1254379387",
            "wgrp": false,
            "woth": false,
            "writeable": true,
            "wusr": true,
            "xgrp": false,
            "xoth": false,
            "xusr": false
        }
    }
}
ok: [vm3 -> vm2] => {
    "statfile": {
        "changed": false,
        "failed": false,
        "stat": {
            "atime": 1786657129.928,
            "attr_flags": "",
            "attributes": [],
            "block_size": 4096,
            "blocks": 8,
            "charset": "us-ascii",
            "checksum": "c6a7807014974797c16d8a35b976ee62559a8510",
            "ctime": 1786656965.064,
            "dev": 64768,
            "device_type": 0,
            "executable": false,
            "exists": true,
            "gid": 0,
            "gr_name": "root",
            "inode": 67149324,
            "isblk": false,
            "ischr": false,
            "isdir": false,
            "isfifo": false,
            "isgid": false,
            "islnk": false,
            "isreg": true,
            "issock": false,
            "isuid": false,
            "mimetype": "text/plain",
            "mode": "0644",
            "mtime": 1786656964.857,
            "nlink": 1,
            "path": "/tmp/copyfile",
            "pw_name": "root",
            "readable": true,
            "rgrp": true,
            "roth": true,
            "rusr": true,
            "size": 613,
            "uid": 0,
            "version": "1254379387",
            "wgrp": false,
            "woth": false,
            "writeable": true,
            "wusr": true,
            "xgrp": false,
            "xoth": false,
            "xusr": false
        }
    }
}
ok: [tower -> vm2] => {
    "statfile": {
        "changed": false,
        "failed": false,
        "stat": {
            "atime": 1786657129.928,
            "attr_flags": "",
            "attributes": [],
            "block_size": 4096,
            "blocks": 8,
            "charset": "us-ascii",
            "checksum": "c6a7807014974797c16d8a35b976ee62559a8510",
            "ctime": 1786656965.064,
            "dev": 64768,
            "device_type": 0,
            "executable": false,
            "exists": true,
            "gid": 0,
            "gr_name": "root",
            "inode": 67149324,
            "isblk": false,
            "ischr": false,
            "isdir": false,
            "isfifo": false,
            "isgid": false,
            "islnk": false,
            "isreg": true,
            "issock": false,
            "isuid": false,
            "mimetype": "text/plain",
            "mode": "0644",
            "mtime": 1786656964.857,
            "nlink": 1,
            "path": "/tmp/copyfile",
            "pw_name": "root",
            "readable": true,
            "rgrp": true,
            "roth": true,
            "rusr": true,
            "size": 613,
            "uid": 0,
            "version": "1254379387",
            "wgrp": false,
            "woth": false,
            "writeable": true,
            "wusr": true,
            "xgrp": false,
            "xoth": false,
            "xusr": false
        }
    }
}
ok: [vm4 -> vm2] => {
    "statfile": {
        "changed": false,
        "failed": false,
        "stat": {
            "atime": 1786657129.928,
            "attr_flags": "",
            "attributes": [],
            "block_size": 4096,
            "blocks": 8,
            "charset": "us-ascii",
            "checksum": "c6a7807014974797c16d8a35b976ee62559a8510",
            "ctime": 1786656965.064,
            "dev": 64768,
            "device_type": 0,
            "executable": false,
            "exists": true,
            "gid": 0,
            "gr_name": "root",
            "inode": 67149324,
            "isblk": false,
            "ischr": false,
            "isdir": false,
            "isfifo": false,
            "isgid": false,
            "islnk": false,
            "isreg": true,
            "issock": false,
            "isuid": false,
            "mimetype": "text/plain",
            "mode": "0644",
            "mtime": 1786656964.857,
            "nlink": 1,
            "path": "/tmp/copyfile",
            "pw_name": "root",
            "readable": true,
            "rgrp": true,
            "roth": true,
            "rusr": true,
            "size": 613,
            "uid": 0,
            "version": "1254379387",
            "wgrp": false,
            "woth": false,
            "writeable": true,
            "wusr": true,
            "xgrp": false,
            "xoth": false,
            "xusr": false
        }
    }
}

PLAY RECAP *******************************************************************************************************************************************************************************************
tower                      : ok=5    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
vm1                        : ok=5    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
vm2                        : ok=5    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
vm3                        : ok=5    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
vm4                        : ok=5    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
webserver                  : ok=5    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0


```

</details>
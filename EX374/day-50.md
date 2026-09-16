#### Managing podman containers

Podman is RHEL's native way of running containers, it is available on RHEL8 and later. To run a podman container, you have to ensure the software is available, no daemon is needed like Docker. Also, podman can run rootless containers, so doesn't require root privileges.

Example playbook to install and run a redis container on RHEL9.

```yaml
---
- name: Podman redis installation
  hosts: vm2
  collections:
  - containers.podman
  tasks:
  - name: Install podman
    package:
      name: "podman"
      state: present
  - name: Pull a redis container
    podman_image:
      name: docker.io/library/redis:alpine
  - name: Create a redis container
    podman_container:
      name: testredis
      image: docker.io/library/redis:alpine
      state: started
      published_ports:
        - "6380:6379"
```
Output:

```shell
[ansible@control ansible_work]$ ansible-playbook podmanredis.yml

PLAY [Podman redis installation] *********************************************************************************************************************************************************************

TASK [Gathering Facts] *******************************************************************************************************************************************************************************
ok: [vm2]

TASK [Install podman] ********************************************************************************************************************************************************************************
changed: [vm2]

TASK [Pull a redis container] ************************************************************************************************************************************************************************
changed: [vm2]

TASK [Create a redis container] **********************************************************************************************************************************************************************
changed: [vm2]

PLAY RECAP *******************************************************************************************************************************************************************************************
vm2                        : ok=4    changed=3    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0

[ansible@control ansible_work]$ ansible vm2 -m shell -a "podman exec -it testredis redis-cli ping"
vm2 | CHANGED | rc=0 >>
PONG
```

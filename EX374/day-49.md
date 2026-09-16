#### Using Ansible to control containers.

##### Managing docker containers.
We need to install docker and get the dockerd daemon up and running.

- Enable docker repository
```bash
sudo dnf install -y dnf-plugins-core
sudo dnf config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo
```
- Install and start docker.
```bash
sudo dnf install -y docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
sudo systemctl enable --now docker
```
- Verify daemon is active.
```bash
sudo systemctl status docker
```
- Install Python Pip Module for Docker, then install Python 3 and Pip, then install the docker Python SDK:
(Note: Use --break-system-packages if your environment enforces PEP 668 externally managed environments, or use a Python virtual environment via python3 -m venv ~/docker-env.)

```bash
sudo dnf install -y python3 python3-pip
pip3 install docker --break-system-packages
```

- Create a test container.
```bash
sudo docker run -d --name my-redis -p 6380:6379 redis:alpine
```

- Verify that the container is working.

```bash
 sudo docker exec -it my-redis redis-cli ping
```

##### Installing and starting a docker container on Ubuntu

Since our control machine is using `ansible [core 2.16.19]` we will need to install the community collection; `community.docker`, `ansible-galaxy collection install community.docker`.

The playbook below, will install docker and start a redis container.

```yaml
---
- name: Install docker and start a container on Ubuntu
  hosts: vm4
  tasks:
  - name: install necessary packages.
    ansible.builtin.package:
      name:
      - docker.io
      - pip
      state: latest
  - name: install docker pip module
    ansible.builtin.pip:
      name: docker
  - name: start and enable docker daemon
    ansible.builtin.service:
      name: docker
      enabled: yes
      state: started
  - name: create a redis container
    community.docker.docker_container:
      name: my-redis
      image: redis:alpine
      state: started
      restart_policy: unless-stopped
      published_ports:
        - "6380:6379"
  - name: generate a list of running containers
    command: docker ps
    register: docker_ps
  - name: show the output
    debug:
      var: docker_ps.stdout
```
Output:

```shell
[ansible@control ansible_work]$ ansible-playbook docker_ubuntu.yaml
[WARNING]: Collection community.docker does not support Ansible version 2.16.19

PLAY [Install docker and start a container on Ubuntu] ************************************************************************************************************************************************

TASK [Gathering Facts] *******************************************************************************************************************************************************************************
ok: [vm4]

TASK [install necessary packages.] *******************************************************************************************************************************************************************
changed: [vm4]

TASK [install docker pip module] *********************************************************************************************************************************************************************
changed: [vm4]

TASK [start and enable docker daemon] ****************************************************************************************************************************************************************
ok: [vm4]

TASK [create a redis container] **********************************************************************************************************************************************************************
changed: [vm4]

TASK [generate a list of running containers] *********************************************************************************************************************************************************
changed: [vm4]

TASK [show the output] *******************************************************************************************************************************************************************************
ok: [vm4] => {
    "docker_ps.stdout": "CONTAINER ID   IMAGE          COMMAND                  CREATED        STATUS                  PORTS                    NAMES\n09b10081d2bf   redis:alpine   \"docker-entrypoint.s…\"   1 second ago   Up Less than a second   0.0.0.0:6380->6379/tcp   my-redis"
}

PLAY RECAP *******************************************************************************************************************************************************************************************
vm4 
```


##### Installing and starting a docker container on RHEL9

This is a bit more involved, since we can't just install the docker daemon from the default repository. Please check this playbook for details on installation and starting a container.

Output:

```shell
[ansible@control ansible_work]$ ansible-playbook docker_rhel.yaml
[WARNING]: Collection community.docker does not support Ansible version 2.16.19

PLAY [Install and Configure Docker CE on RHEL 9] *****************************************************************************************************************************************************

TASK [Gathering Facts] *******************************************************************************************************************************************************************************
ok: [vm1]

TASK [Remove conflicting default container packages] *************************************************************************************************************************************************
ok: [vm1]

TASK [Install DNF core plugins and Python tools] *****************************************************************************************************************************************************
ok: [vm1]

TASK [Add official Docker CE CentOS/RHEL repository] *************************************************************************************************************************************************
ok: [vm1]

TASK [Install Docker CE packages] ********************************************************************************************************************************************************************
ok: [vm1]

TASK [Ensure Docker service is enabled and started] **************************************************************************************************************************************************
ok: [vm1]

TASK [Install Docker SDK for Python (required for community.docker tasks)] ***************************************************************************************************************************
changed: [vm1]

TASK [create a container] ****************************************************************************************************************************************************************************
changed: [vm1]

PLAY RECAP *******************************************************************************************************************************************************************************************
vm1                        : ok=8    changed=2    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0

[ansible@control ansible_work]$ ansible vm1 -m shell -a "docker exec -it my-redis redis-cli ping"
vm1 | CHANGED | rc=0 >>
PONG

```

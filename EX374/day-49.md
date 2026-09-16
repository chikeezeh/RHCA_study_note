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




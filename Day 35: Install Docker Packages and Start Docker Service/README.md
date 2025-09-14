# Install Docker Packages and Start Docker Service

- [Insall the Docker](https://docs.docker.com/engine/install/centos/)
- [Install the Docker Compose](https://docs.docker.com/compose/install/linux/)

### Install docker-ce and docker compose packages on App Server 2

```bash
dnf -y install dnf-plugins-core

dnf config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo

dnf install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin -y

systemctl enable --now docker

```

### Verify

```bash
docker compose version

docker -v
```
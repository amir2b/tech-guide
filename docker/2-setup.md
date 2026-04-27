# Setup Docker

<https://docs.docker.com/engine/install/ubuntu/>

```shell
# Uninstall old versions
sudo apt remove $(dpkg --get-selections docker.io docker-compose docker-compose-v2 docker-doc podman-docker containerd runc | cut -f1)

# Add Docker's official GPG key:
sudo apt update
sudo apt install ca-certificates curl
sudo install -m 0755 -d /etc/apt/keyrings
sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg -o /etc/apt/keyrings/docker.asc
sudo chmod a+r /etc/apt/keyrings/docker.asc

# Add the repository to Apt sources:
sudo tee /etc/apt/sources.list.d/docker.sources <<EOF
Types: deb
URIs: https://download.docker.com/linux/ubuntu
Suites: $(. /etc/os-release && echo "${UBUNTU_CODENAME:-$VERSION_CODENAME}")
Components: stable
Signed-By: /etc/apt/keyrings/docker.asc
EOF

sudo apt update

sudo apt install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
```

## Docker information

```shell
docker --version

docker version

docker info
```

## Linux post-installation steps for Docker Engine

<https://docs.docker.com/engine/install/linux-postinstall/>

```shell
sudo usermod -aG docker $USER

newgrp docker
```

## Config mirror registry

```shell
sudo tee /etc/docker/daemon.json <<EOF
{
  "registry-mirrors": ["https://docker.arvancloud.ir", "https://docker.iranserver.com", "https://docker.jamko.ir", "https://registry.docker.ir"]
}
EOF

sudo systemctl restart docker

docker info
```

## Debug Docker issues

```shell
sudo systemctl status docker

sudo journalctl -u docker -f
```

## References

* <https://docs.docker.com/desktop/>
* <https://docs.docker.com/reference/cli/docker/>

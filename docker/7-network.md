# Docker Network

Container networking refers to the ability for containers to connect to and communicate with each other, and with non-Docker network services.

Drivers:

- bridge
- host
- none
- overlay
- ipvlan
- macvlan

## Sample

```shell
docker run --network=bridge --rm busybox ip a
docker run --network=bridge --rm busybox ping -c5 docker.com

docker run --network=host --rm busybox ip a
docker run --network=host --rm busybox ping -c5 docker.com

docker run --network=none --rm busybox ip a
docker run --network=none --rm busybox ping -c5 docker.com
```

## Commands

### Create a network

```shell
docker network create net1
docker network create -d bridge net1

docker run --network=net1 --rm busybox ping -c5 docker.com
docker run --network=net1 --rm busybox ip a
```

### List networks

```shell
docker network ls
docker network list
```

### Display detailed information on one or more networks

```shell
docker network inspect net1
```

### Remove one or more networks

```shell
docker network rm net1
docker network remove net1
```

### Remove all unused networks

```shell
docker network prune
```

### docker network connect

```shell
docker network create net1
docker run -d --network=net1 --rm --name nginx1 nginx
docker run --network=net1 --rm -it busybox ping -c5 nginx1
docker run --network=net1 --rm -it busybox nc -zv nginx1 80
docker run --network=net1 --rm -it busybox wget nginx1:80
```

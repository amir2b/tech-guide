# Docker Image

<https://docs.docker.com/reference/cli/docker/image/>

## Commands

### List images

```shell
docker image ls
docker image list
docker images
```

### Search for an image

```shell
docker search nginx
docker search postgres
docker search mongo
docker search elasticsearch
docker search clickhouse
docker search spark

docker search ubuntu
docker search alpine
docker search busybox
```

### Download an image from a registry

```shell
docker image pull nginx
docker pull nginx:alpine
docker pull docker.arvancloud.ir/busybox
```

### Display detailed information on one or more images

```shell
docker inspect nginx
```

### Show the history of an image

```shell
docker image history nginx
docker history nginx
```

### Remove one or more images

```shell
docker image rm nginx
docker image remove nginx
docker rmi nginx
```

### Remove unused images

```shell
docker image prune
docker image prune -a --force --filter "until=240h"
```

### Save one or more images to a tar archive (streamed to STDOUT by default)

```shell
docker image save nginx -o nginx.tar
docker image save nginx > nginx.tar
docker save nginx | gzip > nginx.tar.gz
```

### Load an image from a tar archive or STDIN

```shell
docker image load -i nginx.tar
docker load < nginx.tar
docker load < nginx.tar.gz
```

### Import the contents from a tarball to create a filesystem image

```shell
docker image import nginx.tar nginx1
docker image import nginx.tar.gz nginx1
cat nginx.tar | docker import - nginx1
```

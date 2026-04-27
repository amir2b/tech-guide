# Docker Contex

## Connect to remote Docker

```shell
# ssh-copy-id vagrant@192.168.50.10

DOCKER_HOST=ssh://vagrant@192.168.50.10 docker ps

export DOCKER_HOST=ssh://vagrant@192.168.50.10
docker ps
```

## Commands

### List contexts

```shell
docker context ls
docker context list
```

### Create a context

```bash
docker context create --docker host=ssh://vagrant@192.168.50.10 --description="Remote Docker" server1
```

### Set the current docker context

```shell
docker context use server1
ps

docker --context server1 ps
```

### Display detailed information on one or more contexts

```shell
docker context inspect server1
```

### Remove one or more contexts

```shell
docker context rm server1
```

### Export a context to a tar archive FILE or a tar stream on STDOUT.

```shell
docker context export server1
```

### Import a context from a tar or zip file

```shell
docker context import server1 server1.dockercontext
```

## References

* <https://docs.docker.com/engine/security/protect-access/>

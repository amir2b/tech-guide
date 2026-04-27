# Dockerfile

A Dockerfile is a text file containing instructions for building your source code.

- Comment

<https://docs.docker.com/reference/dockerfile>

## Dockerfile contents

### Create a new build stage from a base image

```text
FROM <image>[:<tag>] [AS <name>]
FROM python:3.12-slim
```

### Change working directory

```text
WORKDIR <directory>
WORKDIR /app
```

### Copy files and directories

```text
COPY [OPTIONS] <src> <dest>
COPY requirements.txt .
```

### Add local or remote files and directories

```text
ADD [OPTIONS] <src> ... <dest>
ADD requirements.txt .
ADD https://dlcdn.apache.org/kafka/4.2.0/kafka_2.13-4.2.0.tgz /opt/
```

### Execute build commands

```text
RUN [OPTIONS] <command>
RUN ["pip", "install", "flask"]
RUN pip install flask
RUN apt-get update && \
    apt-get install -y curl
RUN <<EOF
    apt-get update
    apt-get install -y curl
EOF
```

### Specify default executable

```text
ENTRYPOINT ["executable", "param1", "param2"]
ENTRYPOINT command param1 param2
```

### Specify default commands

```text
CMD ["executable", "param1","param2"]
CMD ["param1","param2"]
CMD command param1 param2
CMD ["date"]
CMD ["sh", "-c", "echo $NAME"]
```

### Use build-time variables

```text
ARG <name>[=<default value>] [<name>[=<default value>]...]
ARG DEST=/app/
ARG DEBIAN_FRONTEND=noninteractive
```

### Set environment variables

```text
ENV <key>=<value> [<key>=<value>...]
ENV NAME="Amir"
```

### Describe which ports your application is listening on

```text
EXPOSE <port> [<port>/<protocol>...]
EXPOSE 80 443
EXPOSE 3306/tcp
EXPOSE 3306/udp
```

### Set user and group ID

```text
USER <user>[:<group>]
USER UID[:<GID>]
```

### Create volume mounts

```text
VOLUME ["/data"]
```

### Check a container's health on startup

```text
HEALTHCHECK --interval=5m --timeout=3s CMD curl -f http://localhost/ || exit 1
HEALTHCHECK NONE
```

### Add metadata to an image

```text
LABEL <key>=<value> [<key>=<value>...]
LABEL version="1.0"
```

## Samples

### Sample - Entrypoint and cmd

```text
FROM ubuntu
ENTRYPOINT ["date", "--utc"]
CMD ["--iso-8601"]
```

```shell
docker build -t date .

docker run --rm date:latest +%Y
docker run --rm -it --entrypoint="" date:latest bash
```

### Sample - Flask

File **app.py**

```python
from flask import Flask
app = Flask(__name__)

@app.route("/")
def hello():
    return "Hello World!"
```

File **Dockerfile**

```text
FROM python:3.12-slim
RUN pip install flask
WORKDIR /app
COPY app.py .
HEALTHCHECK --interval=10s --timeout=5s CMD curl -f http://localhost:8000/ || exit 1
EXPOSE 8000
CMD ["flask", "run", "--host", "0.0.0.0", "--port", "8000"]
```

### Sample - Multi-stage builds

<https://docs.docker.com/build/building/multi-stage/>

#### Use multi-stage builds

```text
ARG NODE_VERSION="24"
ARG ALPINE_VERSION="3.23"

FROM node:${NODE_VERSION}-alpine${ALPINE_VERSION} AS base
WORKDIR /src

FROM base AS build
COPY package*.json ./
RUN npm ci
RUN npm run build

FROM base AS production
COPY package*.json ./
RUN npm ci --omit=dev && npm cache clean --force
COPY --from=build /src/dist/ .
CMD ["node", "app.js"]
```

#### Use an external image as a stage

```text
COPY --from=nginx:latest /etc/nginx/nginx.conf /nginx.conf
```

## References

- <https://docs.docker.com/build/concepts/dockerfile/>
- <https://docs.docker.com/build/building/best-practices/>

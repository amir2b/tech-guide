# Docker Compose

Docker Compose is a tool for defining and running multi-container applications. It is the key to unlocking a streamlined and efficient development and deployment experience.

- compose.yaml
- .env file
- services: <https://docs.docker.com/reference/compose-file/services/>
- networks: <https://docs.docker.com/reference/compose-file/networks/>
- volumes: <https://docs.docker.com/reference/compose-file/volumes/>
- configs: <https://docs.docker.com/reference/compose-file/configs/>
- secrets: <https://docs.docker.com/reference/compose-file/secrets/>

## Sample

```yaml
services:
    postgres:
        image: postgres:18.2-alpine
        container_name: postgres
        hostname: postgres
        environment:
            TZ: Asia/Tehran
            POSTGRES_PASSWORD: password
            # POSTGRES_PASSWORD_FILE=/run/secrets/postgres-passwd
        volumes:
            - postgres-data:/var/lib/postgresql
            - ./postgres-backup:/backup
        restart: no # on-failure, unless-stopped, always
        cpus: 2
        mem_limit: 1g
        network_mode: none, host
        networks:
            - postgres-net
        ulimits:
            nproc: 65535
            nofile:
                soft: 20000
                hard: 40000
        configs:
            - source: app_config
              target: /config
        secrets:
            - postgres-passwd
        # command: echo "Amir Bashiri"
        # entrypoint: /code/entrypoint.sh
        # env_file: .env
        # env_file:
        #     - ./a.env
        #     - ./b.env
        healthcheck:
            test: ["CMD", "pg_isready", "-U", "postgres"]
            interval: 10s
            retries: 5
            start_period: 5s
    
    adminer:
        image: adminer:standalone
        ports:
            - 8080:8080
        networks:
            - postgres-net
        depends_on:
            - postgres
        # depends_on:
        #     postgres:
        #         condition: service_healthy
        # healthcheck:
        #     test: ["CMD", "curl", "-f", "http://localhost"]
        #     # test: curl -f https://localhost || exit 1
        #     interval: 1m30s
        #     timeout: 10s
        #     retries: 3
        #     start_period: 40s
        #     start_interval: 5s

    custom:
        # build: ./webapp
        build:
            context: ./webapp
            args:
                GIT_COMMIT: cdc3b19
        profiles: ["debug"]

volumes:
    postgres-data:

networks:
    postgres-net:

config:
    http_config:
        file: ./httpd.conf
    app_config:
        content: |
            debug=true
secrets:
    postgres-passwd:
        file: ./httpd.conf
```

## Refrences

- <https://docs.docker.com/compose/>
- <https://man7.org/linux/man-pages/man7/capabilities.7.html>

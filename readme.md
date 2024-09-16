# Keycloak Startup Template

Startup a Keycloak service setup to operate behind a nginx managed TLS proxy. Based on [keycloak-compose](https://github.com/stzagkarak/keycloak-compose) and [keycloakify](https://github.com/keycloakify/keycloakify).

### 0. Create this relative folder path

`mkdir -p instance/database/mbackups`

### 1. Create an external docker network ( if not exists )

`docker network create -d bridge container_network`

### 2. Change the appropriate values in the config files

### 3. Change your nginx config to include a redirection to the keycloak service

1. nginx docker-compose file
```bash
external_links:
    - keycloak
```

2. nginx config file
```bash
upstream keycloakServer {
    server keycloak:8080;
}

location / {
    proxy_set_header Host $http_host;
    proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    proxy_set_header X-Real-IP $remote_addr;
    proxy_set_header X-Forwarded-Host $host;
    proxy_set_header X-Forwarded-Server $host;
    proxy_set_header X-Forwarded-Port $server_port;
    proxy_set_header X-Forwarded-Proto $scheme;

    proxy_pass http://keycloakServer;

    client_max_body_size 16m;
}
```

After changes:
0. Navigate to nginx compose location.
1. Run `docker compose up -d`.
2. Enter `docker exec -it <container_name> /bin/bash` the nginx container and run `service nginx --full-restart`.

### 4. Startup the compose file

`docker compose up`
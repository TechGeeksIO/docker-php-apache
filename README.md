## Custom PHP-Apache Docker Container
### This container includes PHP 7.4, Apache 2.4 and all common modules + composer

<br/><br/>
#### Docker-compose example with traefik 2 reverse proxy

```
  <service name>:
    image: techgeeks/php-apache-dev:latest
    hostname: <your-domain.com>
    container_name: <your-domain.com>
    restart: unless-stopped
    networks:
      - proxy
    environment:
      - PUID=${PUID}
      - PGID=${PGID}
    volumes:
      - ${DATADIR}/apache24/<your-domain.com>/www:/var/www/html/
      #### ATTENTION
      # Leave the following volumes disabled in the first run, you can enable them later if you need them!
      ####
      #- ${DATADIR}/apache24/<your-domain.com>/config/apache:/etc/apache2/
      #- ${DATADIR}/apache24/<your-domain.com>/config/php:/usr/local/etc/php/
    labels:
      - "traefik.enable=true"
      # Router HTTP for https redirection
      - "traefik.http.routers.<service name>-http.rule=Host(`<your-domain.com>`)"
      - "traefik.http.routers.<service name>-http.entrypoints=web"
      - "traefik.http.routers.<service name>-http.middlewares=https-redirect@file"
      # Router HTTPS
      - "traefik.http.routers.<service name>.rule=Host(`<your-domain.com>`)"
      - "traefik.http.routers.<service name>.tls=true"
      - "traefik.http.routers.<service name>.tls.certresolver=le"
      - "traefik.http.routers.<service name>.entrypoints=websecure"
      - "traefik.http.services.<service name>.loadbalancer.server.port=80"
```

<br/><br/>

#### Initial installation

Copy default container files to your host
```bash
sudo mkdir -p <DATADIR>/apache24/<your-domain.com>
sudo docker cp <container-id><service name>:/etc/apache/ <DATADIR>/apache24/<your-domain.com>/config/
sudo docker cp <container-id><service name>:/usr/local/etc/php/ <DATADIR>/apache24/<your-domain.com>/config/
```

Change permissions to your docker user, this will allow you to change, add or remove files
```bash
sudo chown -R <docker-user>:<docker-group> <DATADIR>/apache24
```

> **Run your docker-compose**

Activate the additional volumes in your docker-compose file
```bash
      - ${DATADIR}/apache24/<your-domain.com>/config/apache:/etc/apache2/
      - ${DATADIR}/apache24/<your-domain.com>/config/php:/usr/local/etc/php/
```

> **Run your docker-compose again**

<br/><br/>

### Placeholders

Key | Example
--- | ---
`<service name>` | **example-website**
`<your-domain.com>` | **example.com**
`<docker-user>` | *docker*
`<docker-group>` | *docker*
`<DATADIR>` | **/srv/docker**

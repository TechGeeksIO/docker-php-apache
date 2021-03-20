## Custom PHP-Apache Docker Container
### This container includes PHP 7.4, Apache 2.4 and all common modules


### Docker-compose example with traefik 2 reverse proxy
```
  <your-domain><service name>:
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
      #- ${DATADIR}/apache24/<your-domain.com>/confs/apache2:/etc/apache2/
      #- ${DATADIR}/apache24/<your-domain.com>/confs/php:/usr/local/etc/php/
    labels:
      - "traefik.enable=true"
      # Router HTTP for https redirection
      - "traefik.http.routers.<your-domain><service name>-http.rule=Host(`<your-domain.com>`)"
      - "traefik.http.routers.<your-domain><service name>-http.entrypoints=web"
      - "traefik.http.routers.<your-domain><service name>-http.middlewares=https-redirect@file"
      # Router HTTPS
      - "traefik.http.routers.<your-domain><service name>.rule=Host(`<your-domain.com>`)"
      - "traefik.http.routers.<your-domain><service name>.tls=true"
      - "traefik.http.routers.<your-domain><service name>.tls.certresolver=le"
      - "traefik.http.routers.<your-domain><service name>.entrypoints=websecure"
      - "traefik.http.routers.<your-domain><service name>.middlewares=compression@file"
      - "traefik.http.services.<your-domain><service name>.loadbalancer.server.port=80"
```

### Initial installation
Copy default container files to your host
```bash
sudo mkdir -p <DATADIR>/apache24/<your-domain.com>
sudo docker cp <container-id><container-name><your-domain.com>:/etc/apache2/ <DATADIR>/apache24/<your-domain.com>/confs/
sudo docker cp <container-id><container-name><your-domain.com>:/usr/local/etc/php/ <DATADIR>/apache24/<your-domain.com>/confs/
```

Change permissions to your docker user, this will allow you to change, add or remove files
```bash
sudo chown -R <docker-user>:<docker-group> <DATADIR>/apache24
```

> Run your docker-compose

Activate the additional volumes in your docker-compose file
```bash
      - ${DATADIR}/apache24/<your-domain.com>/confs/apache2:/etc/apache2/
      - ${DATADIR}/apache24/<your-domain.com>/confs/php:/usr/local/etc/php/
```

> Run your docker-compose again

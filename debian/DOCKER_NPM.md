## Docker compose

Use **nginx-proxy-manager-container** from dockhand and replace,

```
services:
  nginx-proxy-manager:
    image: 'jc21/nginx-proxy-manager:latest'
    container_name: nginx-proxy-manager
    restart: unless-stopped
    ports:
      - '80:80'     # Public HTTP
      - '81:81'     # NPM Admin UI
      - '443:443'   # Public HTTPS
    extra_hosts:
      - "host.docker.internal:host-gateway"
    volumes:
      - ./data:/data
      - ./letsencrypt:/etc/letsencrypt
    networks:
      - vps-network-bridge

networks:
  vps-network-bridge:
    external: true
```
## Docker compose

```
services:
  postgres:
    image: postgres:16-alpine
    container_name: dockhand-db
    restart: unless-stopped
    environment:
      POSTGRES_USER: dockhand
      POSTGRES_PASSWORD: changeme
      POSTGRES_DB: dockhand
    volumes:
      - postgres_data:/var/lib/postgresql/data
    networks:
      - vps-network-bridge

  dockhand:
    image: fnsys/dockhand:latest
    container_name: dockhand
    restart: unless-stopped
    ports:
      - '3000:3000'
    environment:
      DATABASE_URL: postgres://dockhand:changeme@postgres:5432/dockhand
    volumes:
      - /var/run/docker.sock:/var/run/docker.sock
      - dockhand_data:/app/data
    depends_on:
      - postgres
    networks:
      - vps-network-bridge

volumes:
  postgres_data:
  dockhand_data:

networks:
  vps-network-bridge:
    external: true
```
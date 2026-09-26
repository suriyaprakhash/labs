# Toxiproxy

```
services:
  # =========================================================================
  # 1. TOXIPROXY ENGINE (The Network Disruptor Core)
  # =========================================================================
  toxiproxy:
    image: ghcr.io/shopify/toxiproxy:latest  # Correctly pulled from GHCR
    container_name: toxiproxy
    ports:
      - "8474:8474"   # API management port (used internally by UI / CLI)
      - "13306:13306" # <-- USE THIS IN YOUR SPRING BOOT APP (Proxied MySQL)
    networks:
      - app-network

  # =========================================================================
  # 2. UPSTREAM DATABASE (The Target Service)
  # =========================================================================
  mysql:
    image: mysql:8.0
    container_name: mysql
    environment:
      MYSQL_ROOT_PASSWORD: password
      MYSQL_DATABASE: test_db
    ports:
      - "3306:3306"   
    networks:
      - app-network

  # =========================================================================
  # 3. TOXIPROXY WEB DASHBOARD (The Visual Controller)
  # =========================================================================
  toxiproxy-ui:
    # FIXED: Replaced ghcr.io pointer with the verified Docker Hub registry image
    image: buckle/toxiproxy-frontend:latest
    container_name: toxiproxy-ui
    ports:
      - "8081:8080"   # Open http://<nas-ip>:8081 or http://<tailscale-ip>:8081
    environment:
      - TOXIPROXY_URL=http://toxiproxy:8474
    depends_on:
      - toxiproxy
    networks:
      - app-network

networks:
  app-network:
    driver: bridge
```
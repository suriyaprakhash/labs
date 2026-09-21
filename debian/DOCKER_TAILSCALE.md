## Docker compose

Use **tailscale-container** from dockhand and replace,

```
services:
  tailscale:
    image: tailscale/tailscale:latest
    container_name: tailscale
    hostname: vps-node
    restart: unless-stopped
    network_mode: host
    environment:
      - TS_STATE_DIR=/var/lib/tailscale
      - TS_USERSPACE=false
      - TS_EXTRA_ARGS=--advertise-exit-node # Enables exit node functionality
      - TS_AUTHKEY=tskey-auth-YOUR_TAILSCALE_AUTH_KEY # Optional: Auth key
    volumes:
      - ./data:/var/lib/tailscale
      - /dev/net/tun:/dev/net/tun
    cap_add:
      - NET_ADMIN
      - NET_RAW
```
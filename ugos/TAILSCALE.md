# Running tailscale

```
services:
    tailscale:
        image: tailscale/tailscale:latest
        container_name: nas-tailscale
        hostname: nas-tailscale
        network_mode: "host"
        # env_file: .env
        environment:
        - TS_AUTHKEY=tskey-auth-key 
        - TS_STATE_DIR=/var/lib/tailscale
        # - TS_USERSPACE=false
        - TS_EXTRA_ARGS=--advertise-exit-node
        restart: unless-stopped
        volumes:
        - ./tailscale-data:/var/lib/tailscale
        # - ./dev/net/tun:/dev/net/tun
        # cap_add:
        # - NET_ADMIN
        # - SYS_MODULE
        # command:
        # - tailscaled
        # devices:
        # - "/dev/net/tun"
```
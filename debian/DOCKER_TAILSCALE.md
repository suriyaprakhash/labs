## Docker compose


### Using tailscale in the Docker

> [!WARNING]
> This is potentially very slow so install it [directly on the vps](#directly-on-the-vps)

Use **tailscale-container** from dockhand and replace,

```
services:
  # 1. Dedicated Exit Node (Host Mode)
  tailscale-exit-node:
    image: tailscale/tailscale:latest
    container_name: tailscale-exit-node
    hostname: vps-exit-node
    restart: unless-stopped
    network_mode: host
    privileged: true
    environment:
      - TS_STATE_DIR=/var/lib/tailscale-exit
      - TS_USERSPACE=false
      - TS_TAILSCALED_EXTRA_ARGS=--port=41641
      - TS_EXTRA_ARGS=--advertise-exit-node
      - TS_AUTHKEY=tskey-auth-YOUR_AUTH_KEY
    volumes:
      - ./data-exit:/var/lib/tailscale-exit
      - /dev/net/tun:/dev/net/tun

  # 2. Dedicated Proxy Gateway for NPM (Bridge Mode)
  tailscale-bridge:
    image: tailscale/tailscale:latest
    container_name: tailscale-bridge
    hostname: vps-bridge-node
    restart: unless-stopped
    environment:
      - TS_STATE_DIR=/var/lib/tailscale-bridge
      - TS_USERSPACE=true
      - TS_TAILSCALED_EXTRA_ARGS=--port=41642
      - TS_AUTHKEY=tskey-auth-YOUR_AUTH_KEY
    ports:
      - "41642:41642/udp"
    volumes:
      - ./data-bridge:/var/lib/tailscale-bridge
    networks:
      - vps-network-bridge

networks:
  vps-network-bridge:
    external: true
```

**NOTE** It needs two tailscale containers, cause single tailscale container does not work between **exposing nas magic dns within npm** and **using it as exit node**


### Directly on the VPS


# Phase 2: Private Networking (Tailscale)
The installation script works perfectly on Debian.

```
# Install Tailscale
curl -fsSL https://tailscale.com/install.sh | sh

# Start and Authenticate
sudo tailscale up --advertise-exit-node
```

Now on the vps to allow forward so that you could use it as a exit node
```
# Update sysctl.conf
sudo nano /etc/sysctl.conf

# uncomment the following line
net.ipv4.ip_forward=1
net.ipv6.conf.all.forwarding=1

# Save and exit (Ctrl+O, Enter, Ctrl+X).

# Force apply
sudo sysctl -p
```

Check see if you are able to use node - update the firewall to allow
```
Protocol - UDP
Port - 41641	
Desc - For tailscale - punching a hole
```

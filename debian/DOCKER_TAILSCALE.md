## Docker compose

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
      - TS_EXTRA_ARGS=--advertise-exit-node
      - TS_AUTHKEY=tskey-auth-YOUR_AUTH_KEY # Insert auth key
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
      - TS_USERSPACE=true # Userspace mode works perfectly here for proxying
      - TS_AUTHKEY=tskey-auth-YOUR_AUTH_KEY # Insert auth key
    volumes:
      - ./data-bridge:/var/lib/tailscale-bridge
    networks:
      - vps-network-bridge

networks:
  vps-network-bridge:
    external: true
```

**NOTE** It needs two tailscale containers, cause single tailscale container does not work between **exposing nas magic dns within npm** and **using it as exit node**


## Check & Force Host IP Forwarding:VPS Terminal

If kernel packet forwarding got reset, the VPS drops all exit node traffic before it can reach the WAN interface.

Run on your VPS terminal,
```
sudo sysctl -w net.ipv4.ip_forward=1
sudo sysctl -w net.ipv6.conf.all.forwarding=1
```

To make it persistent across reboots,
```
echo "net.ipv4.ip_forward=1" | sudo tee -a /etc/sysctl.d/99-tailscale.conf
echo "net.ipv6.conf.all.forwarding=1" | sudo tee -a /etc/sysctl.d/99-tailscale.conf
sudo sysctl --system
```
Verification: Run,
```
sudo sysctl net.ipv4.ip_forward
```
It must return 1.

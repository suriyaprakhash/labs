## Create nw bridge

Run this command on your VPS host to create the isolated Docker bridge network for NPM and Dockhand,

```
sudo docker network create vps-network-bridge
```

## Verify

```
sudo docker network ls | grep vps-network-bridge
```
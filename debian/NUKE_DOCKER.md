# Stop all running containers
```
sudo docker stop $(sudo docker ps -aq)
```

# Nuke all containers, images, networks, and volumes
```
sudo docker system prune -a --volumes -f
```

in case if it did not delete the volumne,
```
sudo docker volume prune --all -f
```

## List

```
sudo docker images ls
```

```
sudo docker ps -a
```

```
sudo docker network ls
```

```
sudo docker volume ls
```

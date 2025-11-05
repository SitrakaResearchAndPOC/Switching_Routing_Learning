# CREATING H1, H2 and H3

```
docker images
```


## more 02-docker-start-frr-3-nodes
docker run --name H1 --cap-add=NET_ADMIN --cap-add SYS_ADMIN --rm -it frr_docker:h1 bash </br>
docker run --name H2 --cap-add=NET_ADMIN --cap-add SYS_ADMIN --rm -it frr_docker:h1 bash </br>
docker run --name H3 --cap-add=NET_ADMIN --cap-add SYS_ADMIN --rm -it frr_docker:h1 bash </br>

Terminal 1 for H1

```
docker run --name H1 --hostname H1 --cap-add=NET_ADMIN --cap-add SYS_ADMIN --rm -it frr_docker:h1 bash 
```
```
ifconfig
```

New terminal : ctrl+shift+T </br>
Terminal 2 for H2

```
docker run --name H2 --hostname H2 --cap-add=NET_ADMIN --cap-add SYS_ADMIN --rm -it frr_docker:h1 bash 
```
```
ifconfig
```
New terminal : ctrl+shift+T  </br>
Terminal 3 for H3

```
docker run --name H3 --hostname H3 --cap-add=NET_ADMIN --cap-add SYS_ADMIN --rm -it frr_docker:h1 bash 
```
```
ifconfig
```

New terminal : ctrl+shift+T </br>
Terminal for managing docker and router </br>

## Testing if H1, H2, H3 are created
```
docker ps
```
```
ifconfig
```
## BRIDGE
```
apt install bridge-utils
```
```
brctl show
```

## more 04-docker-network-remove-default
docker network disconnect docker0 <H1 from docker ps> </br> 
docker network disconnect docker0 <H2 from docker ps> </br>
docker network disconnect docker0 <H3 from docker ps> </br>

</br> In terminal for managing docker and router

```
docker network disconnect docker0 H1
```
```
docker network disconnect docker0 H2
```
```
docker network disconnect docker0 H3
```



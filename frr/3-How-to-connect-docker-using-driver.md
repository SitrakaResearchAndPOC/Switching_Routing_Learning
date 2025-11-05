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
# PREPARING BRIDGE
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

## inspecting bridge
```
docker network ls
```
NETWORK ID     NAME                     DRIVER    SCOPE  </br>
374ae59c2112   bridge                   bridge    local  </br>
</br>

```
docker network inspect bridge
```
we should find all ip addres on the ifconfig of each terminal of H1, H2, H3
## Disconnecting H1, H2, H3 on bridge
```
docker network disconnect bridge H1
```
```
docker network disconnect bridge H2
```
```
docker network disconnect bridge H3
```
```
docker network inspect bridge
```
we should NOT find all ip addres on the ifconfig of each terminal of H1, H2, H3










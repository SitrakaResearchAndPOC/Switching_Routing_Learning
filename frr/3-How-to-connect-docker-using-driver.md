```
docker images
```
# more 02-docker-start-frr-3-nodes

Terminal 1 for H1

```
docker run --name H1 --hostname H1 --cap-add=NET_ADMIN --cap-add SYS_ADMIN --rm -it frr_docker:h1 bash 
```
New terminal : ctrl+shift+T </br>
Terminal 2 for H2

```
docker run --name H2 --hostname H2 --cap-add=NET_ADMIN --cap-add SYS_ADMIN --rm -it frr_docker:h1 bash 
```
New terminal : ctrl+shift+T  </br>
Terminal 3 for H3

```
docker run --name H3 --hostname H3 --cap-add=NET_ADMIN --cap-add SYS_ADMIN --rm -it frr_docker:h1 bash 
```
New terminal : ctrl+shift+T </br>
Terminal for managing docker and router

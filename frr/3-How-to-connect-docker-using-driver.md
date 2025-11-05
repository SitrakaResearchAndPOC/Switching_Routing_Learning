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


` 
docker network disconnect docker0  H1  </br>
docker network disconnect docker0  H2  </br>
docker network disconnect docker0  H3  </br> 
` 

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

<table>
      <thead>
        <tr>
          <th class="col-id">NETWORK ID</th>
          <th class="col-name">NAME</th>
          <th class="col-driver">DRIVER</th>
          <th class="col-scope">SCOPE</th>
        </tr>
      </thead>
      <tbody>
        <tr>
          <td class="col-id">374ae59c2112</td>
          <td class="col-name">bridge</td>
          <td class="col-driver">bridge</td>
          <td class="col-scope">local</td>
        </tr>
      </tbody>
</table>

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


# CREATING AND ASSOCIATING FRR_SUBNET1 AND FRR_SUBNET2

## more 03-docker-network-create-3-nodes
docker network create --driver=bridge --subnet=11.11.0.0/16 frr_subnet1 </br>
docker network create --driver=bridge --subnet=12.12.0.0/16 frr_subnet2 </br>
</br>

```
docker network create --driver=bridge --subnet=11.11.0.0/16 frr_subnet1 
```
```
docker network create --driver=bridge --subnet=12.12.0.0/16 frr_subnet2 
```
```
docker network ls
```
NETWORK ID     NAME                     DRIVER    SCOPE  </br>
374ae59c2112   bridge                   bridge    local  </br>
011ee426d0b4   frr_subnet1              bridge    local  </br>
47d8249129a8   frr_subnet2              bridge    local  </br>
</br>
```
brctl show
```
We should find two bridge










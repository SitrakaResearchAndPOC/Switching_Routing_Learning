## Running all image
```
docker run --name H1 --hostname H1 --cap-add=NET_ADMIN --cap-add SYS_ADMIN --rm -it frr_docker:h1 bash 
```
In new terminal ctrl+shift+T
```
docker run --name  H2 --hostname H2 --cap-add=NET_ADMIN --cap-add SYS_ADMIN --rm -it frr_docker:h1 bash 
```
In new terminal ctrl+shift+T
```
docker run --name H3  --hostname H3 --cap-add=NET_ADMIN --cap-add SYS_ADMIN --rm -it frr_docker:h1 bash
```
## Disconnect all brctl
In new terminal ctrl+shift+T

```
docker network disconnect docker0 H1
```
```
docker network disconnect docker0 H2
```
```
docker network disconnect docker0 H3
```
```
docker network inspect bridge
```
</br></br>
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
## Creating and connecting
```
docker network create --driver=bridge --subnet=11.11.0.0/16 frr_subnet1 
```
```
docker network create --driver=bridge --subnet=12.12.0.0/16 frr_subnet2 
```
```
docker network ls
```
```
docker network connect frr_subnet1 H1 
```
```
docker network connect frr_subnet1 H2 
```
```
docker network connect frr_subnet2 H2
```
```
docker network connect frr_subnet2 H3
```

TEST ALL PING
* Between H1 and H2
* Between H2 and H3



## Configure in H1
Active OSPF in  Terimal 1

```
 vi /etc/frr/daemons
```
Active ospf and ospf6d
```
systemctl restart frr
```
```
systemctl status frr
```
```
vtysh
```
```
configure terminal
```
```
interface lo
```
```
ip address 1.1.1.1/32
```
```
no shutdown
```
```
end
```
```
show interface brief
```
<pre>

Interface       Status  VRF             Addresses
---------       ------  ---             ---------
eth0            up      default         11.11.0.2/16
lo              up      default         1.1.1.1/32
  
</pre>

Configure route
```
configure terminal
```
```
router ospf
```
```
network 11.11.0.2/16 area 0
```
```
network 1.1.1.1/32 area 0
```
```
end
```



## Configure in H2
Active OSPF in  Terimal 2

```
 vi /etc/frr/daemons
```
Active ospf and ospf6d
```
systemctl restart frr
```
```
systemctl status frr
```
```
vtysh
```
```
configure terminal
```
```
interface lo
```
```
ip address 2.2.2.2/32
```
```
no shutdown
```
```
end
```
```
show interface brief
```
<pre>

Interface       Status  VRF             Addresses
---------       ------  ---             ---------
eth0            up      default         11.11.0.3/16
eth1            up      default         12.12.0.2/16
lo              up      default         2.2.2.2/32

  
</pre>

Configure route
```
configure terminal
```
```
router ospf
```
```
network 11.11.0.3/16 area 0
```
```
network 12.12.0.2/16 area 0
```

```
network 2.2.2.2/32 area 0
```
```
end
```


## Configure in H3
Active OSPF in  Terimal 3

```
 vi /etc/frr/daemons
```
Active ospf and ospf6d
```
systemctl restart frr
```
```
systemctl status frr
```
```
vtysh
```
```
configure terminal
```
```
interface lo
```
```
ip address 3.3.3.3/32
```
```
no shutdown
```
```
end
```
```
show interface brief
```
<pre>

Interface       Status  VRF             Addresses
---------       ------  ---             ---------
eth0            up      default         12.12.0.3/16
lo              up      default         3.3.3.3/32
  
</pre>

Configure route

```
configure terminal
```
```
router ospf
```
```
network 12.12.0.2/16 area 0
```
```
network 3.3.3.3/32 area 0
```
```
end
```

TEST PING ALL

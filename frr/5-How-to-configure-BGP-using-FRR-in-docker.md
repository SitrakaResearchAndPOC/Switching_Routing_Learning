## Reconfigure as OSPF
[ospf](https://github.com/SitrakaResearchAndPOC/Switching_Routing_Learning/blob/main/frr/4-How-to-configure-OSPF-using-FRR-in-docker.md) </br>

In all terminal : 
```
show ip ospf neighbor
```
```
show ip route
```


## Active BGP
Active OSPF in  all Terminal (for H1, H2 and H3)
```
 vi /etc/frr/daemons
```
Active bgp, ospf and ospf6d
```
systemctl restart frr
```
```
systemctl status frr
```
```
vtysh
```


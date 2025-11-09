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

## Explaining iBGP

<pre>

 H1 (AS1000) --- H2 (AS1000) --- H3 (AS1000)
        |_______________________|
          Session iBGP (logique)
</pre>

<pre>
H1 est connecté à un FAI via eBGP (AS65000).
H1 apprend des routes Internet (ex : 8.8.8.0/24).
H1 annonce ces routes à H3 via iBGP.
H3 pourra alors router le trafic Internet via H1.
Sans iBGP → H3 ne connaîtrait pas ces routes externes.
</pre>

## Configure BGP

In terminal of H1
 
```
show interface brief
```
```
configure terminal 
```
```
router bgp 1000
```
```
neighbor 3.3.3.3 remote-as 1000
```
```
neighbor 3.3.3.3 update-source lo
```
```
end
```
```
show running-config
```
```
show ip bgp summary 
```

 
</br>
In terminal of H3
 
```
show interface brief
```
```
configure terminal 
```
```
router bgp 1000
```
```
neighbor 1.1.1.1 remote-as 1000
```
```
neighbor 1.1.1.1 update-source lo
```
```
end
```
```
show running-config
```
```
show ip bgp summary 
```

 
 
 

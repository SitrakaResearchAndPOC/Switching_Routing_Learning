## Installation with MPLS
```
lsmod | grep mpls
```
```
uname -r
```
```
modinfo mpls_router
```
```
modinfo mpls_iptunnel
```
```
sudo apt update
```
```
sudo apt install linux-modules-extra-$(uname -r)
sudo apt install iproute2
```
```
sudo depmod -a
```
```
modinfo mpls_router
modinfo mpls_iptunnel
```
```
sudo modprobe mpls_router
sudo modprobe mpls_iptunnel
```
```
lsmod | grep mpls
```

## Redo the exercice with OSPF wit docker run --privileged

## ALL ethernet(eth) et virtuethernet (veth) should capable to lauch mpls
### Inspecting images and interfaces 
```
docker images
```
```
docker images | grep frr_docker
```
```
docker network ls
```
```
docker network ls | grep -E 'frr_subnet1|frr_subnet2'
```

Find the created bridge named : frr_subnet1 abd frr_subnet2 (search br-...)
Find if there are multiple interface begin by br
```
ifconfig
```
For verifying what interface is bridged on routers : 
```
brctl show 
```
Should find something like :

<network_id> <name> </br>
</br>
eg : </br> 
9b48052e1fdd       frr_subnet1 </br>
19de40ff39cf      frr_subnet2 </br>
</br>
Find br-... using </br></br>
```
brctl show 
```
regard
br-9b48052e1fdd  </br>
OR </br>
br-19de40ff39cf </br>
</br>
Find also veth-... </br></br> 
eg :  </br>
For br-19de40ff39cf or frr_subnet2 </br>
veth63ab4c2 and vethdaed0a9  </br></br>
For br-9b48052e1fdd  or  frr_subnet1 </br>
vethd34a569  and vethda3dff7 </br></br>

### Finding also how the interfaces is connected
* In the terminal of H1
eg : </br>
```
ip addr show | grep veth63ab
```
Find the number if it match </br>
Like </br></br>
391 : veth63ab4c2@if390 </br></br>
* In the terminal of H2
Find also if it match, for example should see number 391 and 390 if it match
```
ip addr show 
```
For H2, shouldn't match
* In the terminal of H3
Find also if it match, for example should see number 391 and 390 if it match
```
ip addr show 
```
it matched normally,

### Step 1 : MPLS authorization
Do in the host machine and contenerizd machine (in the terminal of H1, H2, H3)
```
lsmod | grep mpls
```
```
sudo modprobe mpls_router
sudo modprobe mpls_iptunnel
```
 
### Step 2 : MPLS and interfaces
Interface should reply MPLS
```
ls /proc/sys/net/mpls/conf
```
For many interfaces, the configuration should do 
```
brctl show 
```
br-19de40ff39cf </br>
br-9b48052e1fdd </br> 
veth63ab4c2 and vethdaed0a9 </br>
vethd34a569 and vethda3dff7 </br>
</br>
Finding  and configuring all interfaces
```
ls /proc/sys/net/mpls/conf/br-19de40ff39cf/input
```
Finding the input of interfaces (for frr_subnet1 then frr_subnet2)
```
more  /proc/sys/net/mpls/conf/br-9b48052e1fdd/input
echo 1 >  /proc/sys/net/mpls/conf/br-9b48052e1fdd/input
more  /proc/sys/net/mpls/conf/br-9b48052e1fdd /input
```
```
more  /proc/sys/net/mpls/conf/br-19de40ff39cf/input
echo 1 >  /proc/sys/net/mpls/conf/br-19de40ff39cf/input
more  /proc/sys/net/mpls/conf/br-19de40ff39cf/input
```

Finding final all configuration of input bridge
```
more  /proc/sys/net/mpls/conf/br*/input
```
the number of 1 should be 2. </br>
Finding the input of veth (vethd34a569 abd vethda3dff7 and veth63ab4c2 and vethdaed0a9 )
```
more  /proc/sys/net/mpls/conf/vethd34a569/input
echo 1 >  /proc/sys/net/mpls/conf/vethd34a569/input
more  /proc/sys/net/mpls/conf/vethd34a569/input
```
```
more  /proc/sys/net/mpls/conf/vethda3dff7/input
echo 1 >  /proc/sys/net/mpls/conf/vethda3dff7/input
more  /proc/sys/net/mpls/conf/vethda3dff7/input
```
```
more  /proc/sys/net/mpls/conf/veth63ab4c2/input
echo 1 >  /proc/sys/net/mpls/conf/veth63ab4c2/input
more  /proc/sys/net/mpls/conf/veth63ab4c2/input
```
```
more  /proc/sys/net/mpls/conf/vethdaed0a9/input
echo 1 >  /proc/sys/net/mpls/conf/vethdaed0a9/input
more  /proc/sys/net/mpls/conf/vethdaed0a9/input
```
Finding final all configuration of input Veth
```
more  /proc/sys/net/mpls/conf/veth*/input
the number of 1 should be 4. </br>

```
Finding configuration of plateform label
```
more  /proc/sys/net/mpls/platform_labels 
```
100000

* In the terminal of H1
In H1 do for eth1 (if address is 11.11.x.x ou 12.12.x.x) and do also with l0
```
ifconfig
```
```
more /proc/sys/net/mpls/conf/eth1/input
echo 1 > /proc/sys/net/mpls/conf/eth1/input
more /proc/sys/net/mpls/conf/eth1/input
```
```
more /proc/sys/net/mpls/conf/l0/input
echo 1 > /proc/sys/net/mpls/conf/l0/input
more /proc/sys/net/mpls/conf/l0/input
```

* In the terminal of H2
In H2 do for eth1 (if address is 11.11.x.x ou 12.12.x.x) and do also with l0
```
ifconfig
```
```
more /proc/sys/net/mpls/conf/eth1/input
echo 1 > /proc/sys/net/mpls/conf/eth1/input
more /proc/sys/net/mpls/conf/eth1/input
```
```
more /proc/sys/net/mpls/conf/l0/input
echo 1 > /proc/sys/net/mpls/conf/l0/input
more /proc/sys/net/mpls/conf/l0/input
```

* In the terminal of H3
In H3 do for eth1 (if address is 11.11.x.x ou 12.12.x.x) and do also with l0
```
ifconfig
```
```
more /proc/sys/net/mpls/conf/eth1/input
echo 1 > /proc/sys/net/mpls/conf/eth1/input
more /proc/sys/net/mpls/conf/eth1/input
```
```
more /proc/sys/net/mpls/conf/l0/input
echo 1 > /proc/sys/net/mpls/conf/l0/input
more /proc/sys/net/mpls/conf/l0/input
```

* In the terminal of H1, H2, H3
```
systemctl restart frr
```
```
vtysh
```
```
show mpls status
```
MPLS status should be : yes for all

# To desactivate MPLS
```
stop all docker and 
```
```
lsmod | grep mpls
```
```
rmmod mpls_iptunnel
rmmod mpls_router
```

lsmod | grep mpls
uname -r
modinfo mpls_router
modinfo mpls_iptunnel

sudo apt update
sudo apt install linux-modules-extra-$(uname -r)
sudo apt install iproute2

sudo depmod -a
modinfo mpls_router
modinfo mpls_iptunnel
sudo modprobe mpls_router
sudo modprobe mpls_iptunnel

lsmod | grep mpls



RQ IMPORTANT : Demarrer OSPF en --privileged

### ALL ethernet(eth) et virtuethernet (veth) should capable to lauch mpls

docker images

docker images | grep frr_docker


docker network ls

docker network ls | grep -E 'frr_subnet1|frr_subnet2'


Chercher le bridge qui crée frr_subnet1 et frr_subnet2 (càd chercher br-1)
regarder il y a beaucoup d'interface br avec : 
ifconfig

Pour verifier quelle interface bridge connecté avec les routeurs et quelle interface faire: 
brctl show 

<network_id> <name>

eg : 
9b48052e1fdd   frr_subnet1
19de40ff39cf   frr_subnet2

avec brctl show regarde 
br-9b48052e1fdd  
ou 
br-19de40ff39cf

Regarder aussi le veth
eg : 
Pour br-19de40ff39cf ou  frr_subnet2
veth63ab4c2  et vethdaed0a9 
Pour br-9b48052e1fdd  ou  frr_subnet1
vethd34a569  et vethda3dff7

comment ses interfaces sont connéctés
eg :
Dans H1

ip addr show | grep veth63ab

regarder les numeros pour voir si ca match
391 : veth63ab4c2@if390

faire pour H2 si ca match les numeros 391 et 390
ip addr show 

ca match pas normalement

faire pour H3 si ca match les numeros 390 et 391
ip addr show 

ca match normalement

Etape 1 : Autorisation de MPLS
Faire pour la machine hote et les conteneurs (dans terminal de H1, H2, H3)
lsmod | grep mpls
sudo modprobe mpls_router
sudo modprobe mpls_iptunnel
 
Etape 2 : L'interface devrait etre capable aussi de repondre mpls
ls /proc/sys/net/mpls/conf

Beaucoup d'interfaces

Les interfaces à modifier sont dans : brctl show 
càd 
br-19de40ff39cf
br-9b48052e1fdd 
veth63ab4c2  et vethdaed0a9
vethd34a569  et vethda3dff7

ls /proc/sys/net/mpls/conf/br-19de40ff39cf/input

more  /proc/sys/net/mpls/conf/br-19de40ff39cf/input

echo 1 >  /proc/sys/net/mpls/conf/br-19de40ff39cf/input

more  /proc/sys/net/mpls/conf/br*/input
more  /proc/sys/net/mpls/conf/veth*/input



more  /proc/sys/net/mpls/platform_labels 
100000


Dans H1 faire pareil pour eth1 (ayant l'adresse 11.11.x.x ou 12.12.x.x) et l0
faire 
ifconfig
faire 
more /proc/sys/net/mpls/conf/eth1/input
echo 1 > /proc/sys/net/mpls/conf/eth1/input
more /proc/sys/net/mpls/conf/eth1/input
more /proc/sys/net/mpls/conf/l0/input
echo 1 > /proc/sys/net/mpls/conf/l0/input
more /proc/sys/net/mpls/conf/l0/input


Dans H2 faire pareil pour eth1 (ayant l'adresse 11.11.x.x ou 12.12.x.x) et l0
faire 
ifconfig
faire 
more /proc/sys/net/mpls/conf/eth1/input
echo 1 > /proc/sys/net/mpls/conf/eth1/input
more /proc/sys/net/mpls/conf/eth1/input
more /proc/sys/net/mpls/conf/l0/input
echo 1 > /proc/sys/net/mpls/conf/l0/input
more /proc/sys/net/mpls/conf/l0/input


Dans H3 faire pareil pour eth1 (ayant l'adresse 11.11.x.x ou 12.12.x.x) et l0
faire 
ifconfig
faire 
more /proc/sys/net/mpls/conf/eth1/input
echo 1 > /proc/sys/net/mpls/conf/eth1/input
more /proc/sys/net/mpls/conf/eth1/input
more /proc/sys/net/mpls/conf/l0/input
echo 1 > /proc/sys/net/mpls/conf/l0/input
more /proc/sys/net/mpls/conf/l0/input


Dans H1 H2 H3 faire :
systemctl restart frr
vtysh
show mpls status

devrait etre yes


SI VOUS VOULEZ DESACTIVER FAIRE : 
stopper les docker et faire 
lsmod | grep mpls

rmmod mpls_iptunnel
rmmod mpls_router

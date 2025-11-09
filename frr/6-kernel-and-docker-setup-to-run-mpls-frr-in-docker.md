Re-run container in privileged mode
docker run --privileged --name  H1 --hostname H2 --cap-add=NET_ADMIN --cap-add SYS_ADMIN --rm -it frr_docker:h1 bash 
docker run --privileged --name  H2 --hostname H2 --cap-add=NET_ADMIN --cap-add SYS_ADMIN --rm -it frr_docker:h1 bash 
docker run --privileged --name  H3 --hostname H2 --cap-add=NET_ADMIN --cap-add SYS_ADMIN --rm -it frr_docker:h1 bash 




Vérifier si le noyau supporte MPLS

Commence par vérifier si ton noyau a la prise en charge MPLS :


grep MPLS /boot/config-$(uname -r)

Tu devrais voir des lignes comme :

CONFIG_MPLS=y
CONFIG_MPLS_ROUTING=m
CONFIG_MPLS_IPTUNNEL=m




➡️ Si tu vois =m, cela veut dire que le module existe normalement.
➡️ Si tu vois =y, c’est compilé directement dans le noyau (pas besoin de module).
➡️ Si tu vois is not set, ton noyau ne supporte pas MPLS du tout.



2. Vérifie si le module existe réellement

find /lib/modules/$(uname -r) -type f -name '*mpls*'

Si ça ne retourne rien, c’est que le module n’existe pas pour ton noyau.
Si tu vois des fichiers comme mpls_gso.ko ou mpls_router.ko, tu peux les charger avec modprobe.


3. Activation : 
Ne pas faire en mode super utilisateur
Suivre l'ordre
sudo modprobe mpls_router
sudo modprobe mpls_gso
sudo modprobe mpls_tunnel
lsmod | grep mpls


ls /proc/sys/net/mpls/conf/


brctl show

more /proc/sys/net/mpls/conf/br-<numero 19de6e97aec5>/input 


more /proc/sys/net/mpls/conf/br-<numero: 4ec516e2ef41>/input 


echo 1 > /proc/sys/net/mpls/conf/br-<numero 19de6e97aec5>/input 
echo 1 >  /proc/sys/net/mpls/conf/br-<numero: 4ec516e2ef41>/input 

more  /proc/sys/net/mpls/conf/veth*/input 

changer en 1 , il faut le faire un à un : 

echo 1 > /proc/sys/net/mpls/conf/veth*/input 

changer en 1000

more /proc/sys/net/mpls/platform_labels 

echo 1000 > /proc/sys/net/mpls/platform_labels 

In H1 : 
ifconfig

more /proc/sys/net/mpls/conf/eth0/input 

echo 1 > /proc/sys/net/mpls/conf/eth0/input 



CONTENEUR : 

Ensuite, active MPLS sur les interfaces de l’hôte que Docker utilise (souvent br-* ou veth*) :
for i in $(ls /proc/sys/net/mpls/conf/); do echo 1 | sudo tee /proc/sys/net/mpls/conf/$i/input; done


Vérifie ensuite :
cat /proc/sys/net/mpls/platform_labels

In terminal 1 : 

vtysh

show mpls status


In terminal manager : 


lsmod | grep mpls

rmmod mpls_iptunnel






## Configuration IP et test de connectivité avec deux conteneurs Ubuntu (Docker)
* Créer deux conteneurs Ubuntu avec Docker
* Tester l’affichage des interfaces (ip addr, ifconfig)
* Attribuer des adresses IP statiques avec différentes méthodes (ip, ifconfig, nmcli, netplan)
* Vérifier l’adressage attribué
* Tester la connectivité entre conteneurs avec ping

1. Préparer l’environnement
Installer Docker si ce n’est pas déjà fait :
```
sudo apt update
```
```
sudo apt install docker.io -y
```
```
sudo systemctl enable --now docker
```
Créer un réseau bridge Docker :
```
sudo docker network create --subnet=192.168.100.0/24 mynet
```


2. Lancer deux conteneurs Ubuntu
Lancer le conteneur avec --cap-add=NET_ADMIN ou --privileged
# VM1
```
sudo docker run -dit --privileged --name vm1 --hostname vm1 --net mynet --ip 192.168.100.10 ubuntu:22.04 bash
```
Nouveau terminal : (tapez ctrl+shift+T)
# VM2
```
sudo docker run -dit --privileged --name vm2 --hostname vm2 --net mynet --ip 192.168.100.20 ubuntu:22.04 bash
```
Entrer dans chaque conteneur : </br>
Pour terminal 1 : 
```
sudo docker exec -it vm1 bash
```
```
apt update
```
```
apt install -y iproute2 net-tools iputils-ping network-manager
```

Pour terminal 2

```
sudo docker exec -it vm2 bash
```
Par défaut, Ubuntu minimal n’a pas ifconfig, ping, ni nmcli. On les installe :
```
apt update
```
```
apt install -y iproute2 net-tools iputils-ping network-manager
```
3. Affichage des interfaces
* Avec ip
```
ip addr
```
OU 
```
ip a
```

* Avec ifconfig
```
ifconfig
```
Tester en même temps du ping

4. Ajout d’adresses IP (statiques, temporaires)

* Méthode 1 : avec ip

```
ip addr add 192.168.100.30/24 dev eth0
```

Vérification :
```
ip addr show dev eth0
```

* Méthode 2 : avec ifconfig
```
ifconfig eth0 192.168.100.40 netmask 255.255.255.0 up
```
Vérification :
```
ifconfig eth0
```

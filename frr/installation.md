# Container installation using ubuntu 20.04

## Installation avec nano 
```
apt update && apt install nano
```
```
mkdir frr_ubuntu
```
```
cd frr_ubuntu
```
```
nano Dockerfile
```
## Dockefile

copy Dockerfile 

<details >
<summary> Dockerfile </summary>

        FROM ubuntu:20.04
        
        LABEL maintainer="avinousacademy@gmail.com"
        
        ARG DEBIAN_FRONTEND=noninteractive
        # arping  inetutils-ping
        # 1. Installer paquets de base
        RUN apt-get update && \
            apt-get install -y --no-install-recommends \
                apt-utils net-tools ebtables bridge-utils tcpdump iputils-ping iputils-arping \
                iproute2 tshark tcptraceroute traceroute inetutils-tools inetutils-telnet \
                inetutils-telnetd inetutils-traceroute  nginx vim nano autoconf libtool systemctl \ 
                ca-certificates git automake make libreadline-dev texinfo pkg-config \
                libpam0g-dev libjson-c-dev bison flex python3-pip libc-ares-dev \
                python3-dev python3-sphinx install-info build-essential libsnmp-dev perl\
                libcap-dev python2 libelf-dev sudo gdb curl time lua5.3 liblua5.3-dev \
                libltdl-dev cmake libpcre2-dev protobuf-c-compiler libprotobuf-c-dev \
                libzmq3-dev && \
            rm -rf /var/lib/apt/lists/*
        
        # 2. Installer pip pour Python2 et Python3
        RUN curl -f https://bootstrap.pypa.io/pip/2.7/get-pip.py --output /tmp/get-pip.py && \
            python2 /tmp/get-pip.py && rm -f /tmp/get-pip.py && \
            python3 -m pip install --no-cache-dir wheel pytest pytest-xdist scapy>=2.4.2 xmltodict && \
            python2 -m pip install --no-cache-dir 'exabgp<4.0.0'
        
        # 3. Création des utilisateurs et groupes FRR
        RUN groupadd -r -g 92 frr && \
            groupadd -r -g 85 frrvty && \
            adduser --system --ingroup frr --home /home/frr --gecos "FRR Suite" --shell /bin/bash frr && \
            usermod -a -G frrvty frr && \
            useradd -d /var/run/exabgp/ -s /bin/false exabgp && \
            echo 'frr ALL = NOPASSWD: ALL' > /etc/sudoers.d/frr && \
            mkdir -p /home/frr && chown frr:frr /home/frr
        
        USER frr
        WORKDIR /home/frr
        
        # 4. Installer libyang v2.0.0
        RUN git clone https://github.com/CESNET/libyang.git && \
            cd libyang && git checkout v2.1.128 && \
            mkdir build && cd build && \
            cmake -DCMAKE_INSTALL_PREFIX=/usr -DCMAKE_BUILD_TYPE=Release .. && \
            make -j$(nproc) && sudo make install
        
        # 5. Variables d'environnement pour pkg-config
        USER root
        #ENV LD_LIBRARY_PATH=/usr/lib:/usr/local/lib:$LD_LIBRARY_PATH
        #ENV PKG_CONFIG_PATH=/usr/lib/pkgconfig:/usr/local/lib/pkgconfig:$PKG_CONFIG_PATH
        
        USER frr
        WORKDIR /home/frr
        
        # 6. Cloner et installer FRR v7.5.1 compatible libyang 2.0.0
        RUN git clone https://github.com/frrouting/frr.git && \
            cd frr && git checkout frr-10.1.4 && \
            ./bootstrap.sh && \
            ./configure \
                --prefix=/usr \
                --localstatedir=/var/run/frr \
                --sbindir=/usr/lib/frr \
                --sysconfdir=/etc/frr \
                --enable-vtysh \
                --enable-pimd \
                --enable-sharpd \
                --enable-multipath=64 \
                --enable-user=frr \
                --enable-group=frr \
                --enable-vty-group=frrvty \
                --enable-snmp=agentx \
                --enable-scripting \
                --with-pkg-extra-version=my-manual-build && \
            make -j$(nproc) && sudo make install
        
        # 7. Configuration des fichiers FRR
        USER root
        RUN install -m 775 -o frr -g frr -d /var/log/frr && \
            install -m 775 -o frr -g frrvty -d /etc/frr && \
            install -m 640 -o frr -g frrvty /home/frr/frr/tools/etc/frr/vtysh.conf /etc/frr/vtysh.conf && \
            install -m 640 -o frr -g frr /home/frr/frr/tools/etc/frr/frr.conf /etc/frr/frr.conf && \
            install -m 640 -o frr -g frr /home/frr/frr/tools/etc/frr/daemons.conf /etc/frr/daemons.conf && \
            install -m 640 -o frr -g frr /home/frr/frr/tools/etc/frr/daemons /etc/frr/daemons && \
            install -m 644 frr/tools/frr.service /etc/systemd/system/frr.service
        
        RUN sudo systemctl enable frr
        RUN sudo systemctl start frr
        #USER frr
        #CMD ["/usr/lib/frr/frr", "start"]
        
        # 8. Nettoyage
        RUN apt-get autoremove -y && apt-get clean && rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/* /home/frr/libyang
        
        # 9. Ports FRR typiques
        EXPOSE 179 2601 2602 2603 2604 2605 2606
        
        
        # 10. Retour à l’utilisateur frr pour l’exécution
        # Add cmd vtysh if you want directly vty routing


</details>

Paste Docker
Tape ctrl+X and Y and enter

## Building image FRR
```
docker build --no-cache -t frr_docker:h1 .
```
## 2-docker-start-frr-3-nodes
```
docker run --name H1 --cap-add=NET_ADMIN --cap-add SYS_ADMIN --rm -it frr_docker:h1 bash
```
```
docker run --name H2 --cap-add=NET_ADMIN --cap-add SYS_ADMIN --rm -it frr_docker:h1 bash
```
```
docker run --name H3 --cap-add=NET_ADMIN --cap-add SYS_ADMIN --rm -it frr_docker:h1 bash
```
## Launching frr

```
systemctl status frr
```
frr.service - FRRouting  </br>
    Loaded: loaded (/etc/systemd/system/frr.service, enabled) </br>
    Active: inactive (dead) </br>
```
systemctl start frr
```
watchfrr[19]: [T83RR-8SM5G] watchfrr 10.1.4my-manual-build starting: vty@0 </br>
watchfrr[19]: [ZCJ3S-SPH5S] zebra state -> down : initial connection attempt failed </br>
watchfrr[19]: [ZCJ3S-SPH5S] mgmtd state -> down : initial connection attempt failed </br>
watchfrr[19]: [ZCJ3S-SPH5S] staticd state -> down : initial connection attempt failed </br>
watchfrr[19]: [YFT0P-5Q5YX] Forked background command [pid 20]: /usr/lib/frr/watchfrr.sh restart all </br>
watchfrr[19]: [VTVCM-Y2NW3] Configuration Read in Took: 00:00:00 </br>
watchfrr[19]: [QDG3Y-BY5TN] zebra state -> up : connect succeeded </br>
watchfrr[19]: [QDG3Y-BY5TN] mgmtd state -> up : connect succeeded </br>
watchfrr[19]: [QDG3Y-BY5TN] staticd state -> up : connect succeeded </br>
watchfrr[19]: [KWE5Q-QNGFC] all daemons up, doing startup-complete notify </br>


```
systemctl status frr
```
frr.service - FRRouting  </br>
    Loaded: loaded (/etc/systemd/system/frr.service, enabled)  </br>
    Active: active (running)  </br>

```
vtysh
```
```
show interface brief
```

```
show ip route
```
```
configure terminal
```
```
router ?
```
```
router ospf
```
ospfd is not running
```
router bgp 100
```
ospfd is not running </br>
bgpd is not running </br>

```
end
```
```
exit
```


## Activating dynamic routing ospf
```
vi /etc/frr/daemons
```
Tape i for insert mode </br>
Select as yes routing protocol like : </br> </br>
bgpd=yes </br>
ospfd=yes </br>
ospf6d=yes </br> </br>
Tape echap for avoiding insert mode </br>
Tape :wq </br>
Tape enter </br>




```
systemctl restart frr
```
watchfrr[19]: [NG1AJ-FP2TQ] Terminating on signal </br>
watchfrr[148]: [T83RR-8SM5G] watchfrr 10.1.4my-manual-build starting: vty@0 </br>
watchfrr[148]: [ZCJ3S-SPH5S] zebra state -> down : initial connection attempt failed </br>
watchfrr[148]: [ZCJ3S-SPH5S] mgmtd state -> down : initial connection attempt failed </br>
watchfrr[148]: [ZCJ3S-SPH5S] bgpd state -> down : initial connection attempt failed </br>
watchfrr[148]: [ZCJ3S-SPH5S] ospfd state -> down : initial connection attempt failed </br>
watchfrr[148]: [ZCJ3S-SPH5S] ospf6d state -> down : initial connection attempt failed </br>
watchfrr[148]: [ZCJ3S-SPH5S] staticd state -> down : initial connection attempt failed </br>
watchfrr[148]: [YFT0P-5Q5YX] Forked background command [pid 149]: /usr/lib/frr/watchfrr.sh restart all </br>
watchfrr[148]: [VTVCM-Y2NW3] Configuration Read in Took: 00:00:00 </br>
watchfrr[148]: [QDG3Y-BY5TN] zebra state -> up : connect succeeded </br>
watchfrr[148]: [QDG3Y-BY5TN] mgmtd state -> up : connect succeeded </br>
watchfrr[148]: [QDG3Y-BY5TN] bgpd state -> up : connect succeeded </br>
watchfrr[148]: [QDG3Y-BY5TN] ospfd state -> up : connect succeeded </br>
watchfrr[148]: [QDG3Y-BY5TN] ospf6d state -> up : connect succeeded </br>
watchfrr[148]: [QDG3Y-BY5TN] staticd state -> up : connect succeeded </br>
watchfrr[148]: [KWE5Q-QNGFC] all daemons up, doing startup-complete notify </br>

```
systemctl status frr
```
frr.service - FRRouting </br>
    Loaded: loaded (/etc/systemd/system/frr.service, enabled) </br>
    Active: active (running) </br>

```
vtysh
```
```
show interface brief
```
```
show ip route
```
```
configure terminal
```
```
router ?
```
```
router ospf
```
```
exit
```

```
router bgp 100
```



# Container Direct
```
docker pull frrouting/frr:latest
```
```
docker run -it --rm --privileged --name frr-container frrouting/frr:latest /bin/bash
```
```
/usr/lib/frr/frrinit.sh status
```
Status of watchfrr: FAILED </br>
Status of zebra: FAILED  </br>
Status of staticd: FAILED  </br>
```
/usr/lib/frr/frrinit.sh start
```
Starting watchfrr with command: '  /usr/lib/frr/watchfrr  -d  -F traditional   zebra staticd' </br>
watchfrr[19]: [T83RR-8SM5G] watchfrr 8.4_git starting: vty@0 </br>
watchfrr[19]: [ZCJ3S-SPH5S] zebra state -> down : initial connection attempt failed </br>
watchfrr[19]: [ZCJ3S-SPH5S] staticd state -> down : initial connection attempt failed </br>
watchfrr[19]: [YFT0P-5Q5YX] Forked background command [pid 20]: /usr/lib/frr/watchfrr.sh restart all </br>
watchfrr[19]: [QDG3Y-BY5TN] zebra state -> up : connect succeeded </br>
watchfrr[19]: [QDG3Y-BY5TN] staticd state -> up : connect succeeded </br>
watchfrr[19]: [KWE5Q-QNGFC] all daemons up, doing startup-complete notify </br>
Started watchfrr

```
/usr/lib/frr/frrinit.sh status
```
Status of watchfrr: running </br>
Status of zebra: running </br>
Status of staticd: running </br>

```
vtysh
```
```
show interface brief
```

```
show ip route
```
```
configure terminal
```
```
router ?
```
```
router ospf
```
ospfd is not running
```
router bgp 100
```
ospfd is not running </br>
bgpd is not running </br>

```
end
```
```
exit
```


## Activating dynamic routing ospf
```
vi /etc/frr/daemons
```
Tape i for insert mode </br>
Select as yes routing protocol like : </br> </br>
bgpd=yes </br>
ospfd=yes </br>
ospf6d=yes </br> </br>
Tape echap for avoiding insert mode </br>
Tape :wq </br>
Tape enter </br>

```
/usr/lib/frr/frrinit.sh restart
```
watchfrr[38]: [T83RR-8SM5G] watchfrr 8.4_git starting: vty@0 </br>
watchfrr[38]: [ZCJ3S-SPH5S] zebra state -> down : initial connection attempt failed </br>
watchfrr[38]: [ZCJ3S-SPH5S] bgpd state -> down : initial connection attempt failed </br>
watchfrr[38]: [ZCJ3S-SPH5S] ospfd state -> down : initial connection attempt failed </br>
watchfrr[38]: [ZCJ3S-SPH5S] ospf6d state -> down : initial connection attempt failed </br>
watchfrr[38]: [ZCJ3S-SPH5S] staticd state -> down : initial connection attempt failed </br>
watchfrr[38]: [YFT0P-5Q5YX] Forked background command [pid 39]: /usr/lib/frr/watchfrr.sh restart all </br>
watchfrr[38]: [QDG3Y-BY5TN] bgpd state -> up : connect succeeded </br>
watchfrr[38]: [QDG3Y-BY5TN] ospfd state -> up : connect succeeded </br>
watchfrr[38]: [QDG3Y-BY5TN] ospf6d state -> up : connect succeeded </br>
watchfrr[38]: [QDG3Y-BY5TN] staticd state -> up : connect succeeded </br>
watchfrr[38]: [KWE5Q-QNGFC] all daemons up, doing startup-complete notify </br>
Started watchfrr </br>

```
/usr/lib/frr/frrinit.sh status
```
Status of watchfrr: running </br>
Status of zebra: running </br>
Status of bgpd: running </br>
Status of ospfd: running </br>
Status of ospf6d: running </br>
Status of staticd: running </br>


```
vtysh
```

```
show interface brief
```
```
show ip route
```
```
configure terminal
```
```
router ?
```
```
router ospf
```
```
exit
```
```
router bgp 100
```






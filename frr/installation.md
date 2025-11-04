# Container installation using ubuntu 20.04


<details>
Dockerfile
    
```
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

```

</details>

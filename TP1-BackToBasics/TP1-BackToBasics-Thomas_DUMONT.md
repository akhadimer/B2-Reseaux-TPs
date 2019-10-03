# B2 Réseau 2019 - TP1 : Back to Basics
Thomas DUMONT

## I. Gather informations
* Récupérer une liste des cartes réseau avec leur nom, leur IP et leur adresse MAC
    
    ```
    [root@localhost etc]# ip a
    1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
        link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
        inet 127.0.0.1/8 scope host lo
        valid_lft forever preferred_lft forever
        inet6 ::1/128 scope host
        valid_lft forever preferred_lft forever
    2: enp0s3: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel state UP group default qlen 1000
        link/ether 08:00:27:c7:81:aa brd ff:ff:ff:ff:ff:ff
        inet 10.0.2.15/24 brd 10.0.2.255 scope global dynamic noprefixroute enp0s3
        valid_lft 86396sec preferred_lft 86396sec
        inet6 fe80::1b4d:8794:d843:dac8/64 scope link noprefixroute
        valid_lft forever preferred_lft forever
    3: enp0s8: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel state UP group default qlen 1000
        link/ether 08:00:27:02:02:f7 brd ff:ff:ff:ff:ff:ff
        inet 192.168.3.19/24 brd 192.168.3.255 scope global noprefixroute enp0s8
        valid_lft forever preferred_lft forever
        inet6 fe80::a00:27ff:fe02:2f7/64 scope link
        valid_lft forever preferred_lft forever
    ```


* Déterminer si les cartes réseaux ont récupéré une IP en DHCP ou non

    La carte enp0s3 est la seule à avoir récuperée une IP en DHCP car il y a marqué "Dynamic" dans ses informations.

    On peut obtenir ceci en faisant la commande ``nmcli -f DHCP4 con show enp0s3`` :

    ```
    [root@localhost akhadimer]# nmcli -f DHCP4 con show enp0s3
    DHCP4.OPTION[1]:                        domain_name = auvence.co
    DHCP4.OPTION[2]:                        domain_name_servers = 10.33.10.20 10.33>
    DHCP4.OPTION[3]:                        expiry = 1569588913
    DHCP4.OPTION[4]:                        ip_address = 10.0.2.15
    DHCP4.OPTION[5]:                        requested_broadcast_address = 1
    DHCP4.OPTION[6]:                        requested_dhcp_server_identifier = 1
    DHCP4.OPTION[7]:                        requested_domain_name = 1
    DHCP4.OPTION[8]:                        requested_domain_name_servers = 1
    DHCP4.OPTION[9]:                        requested_domain_search = 1
    DHCP4.OPTION[10]:                       requested_host_name = 1
    DHCP4.OPTION[11]:                       requested_interface_mtu = 1
    DHCP4.OPTION[12]:                       requested_ms_classless_static_routes = 1
    DHCP4.OPTION[13]:                       requested_nis_domain = 1
    DHCP4.OPTION[14]:                       requested_nis_servers = 1
    DHCP4.OPTION[15]:                       requested_ntp_servers = 1
    DHCP4.OPTION[16]:                       requested_rfc3442_classless_static_rout>
    DHCP4.OPTION[17]:                       requested_routers = 1
    DHCP4.OPTION[18]:                       requested_static_routes = 1
    DHCP4.OPTION[19]:                       requested_subnet_mask = 1
    DHCP4.OPTION[20]:                       requested_time_offset = 1
    DHCP4.OPTION[21]:                       requested_wpad = 1
    DHCP4.OPTION[22]:                       routers = 10.0.2.2
    DHCP4.OPTION[23]:                       subnet_mask = 255.255.255.0
    ```

* Afficher la table de routage de la machine et sa table ARP

    Table de routage :

    ```
    [root@localhost etc]# route -n
    Kernel IP routing table
    Destination     Gateway         Genmask         Flags Metric Ref    Use Iface
    0.0.0.0         192.168.3.254   0.0.0.0         UG    100    0        0 enp0s8
    0.0.0.0         10.0.2.2        0.0.0.0         UG    101    0        0 enp0s3
    10.0.2.0        0.0.0.0         255.255.255.0   U     101    0        0 enp0s3
    192.168.3.0     0.0.0.0         255.255.255.0   U     100    0        0 enp0s8
    192.168.122.0   0.0.0.0         255.255.255.0   U     0      0        0 virbr0
    ```


    Explication : 

    * Destination correspond à l'adresse IP de la route que vont suivre les paquets de données.
    * La passerelle correspond par exemple à l'adresse IP du modem
    * Le masque indique la taille de chaque partie de l'adresse IP
    * Indic correspond à l'état de la route.
    * Metric indique combien il reste d'intermédiaires il reste avant d'arriver à la destination
    * Ref indique le nombre de références associées à la route
    * Use c'est un compteur d'utilisation de la route
    * Et pour terminer, Iface est le nom de l'interface réseau qui est utilisée

    Table ARP :

    ```
    [root@localhost etc]# arp -n
    Address                  HWtype  HWaddress           Flags Mask            Iface
    192.168.3.254                    (incomplete)                              enp0s8
    192.168.3.1              ether   0a:00:27:00:00:0e   C                     enp0s8
    ```

    Explication :
    * Address est l'ip du modem
    * HWaddress est l'adresse mac du modem
    * Iface est la carte réseau utilisée

* Récupérer la liste des ports en écoute (listening) sur la machine (TCP et UDP)
    
    Il suffit de faire la commande `netstat -u -t`

    ![](https://image.noelshack.com/fichiers/2019/39/4/1569510095-netstat-u-t.png)

* Récupérer la liste des DNS utilisés par la machine

    ```
    [root@localhost etc]# cat resolv.conf
    # Generated by NetworkManager
    search auvence.co
    nameserver 10.33.10.20
    nameserver 10.33.10.2
    nameserver 8.8.8.8
    # NOTE: the libc resolver may not support more than 3 nameservers.
    # The nameservers listed below may not be recognized.
    nameserver 8.8.4.4
    ```

* Afficher l'état actuel du firewall

    ```
    [akhadimer@localhost ~]$ sudo firewall-cmd --list-all
    public (active)
        target: default
        icmp-block-inversion: no
        interfaces: enp0s3 enp0s8
        sources:
        services: cockpit dhcpv6-client ssh
        ports:
        protocols:
        masquerade: no
        forward-ports:
        source-ports:
        icmp-blocks:
        rich rules:
    ```

    L'interface enp0s3 et enps08 sont filtrées

    Aucun port TCP/UPD sont autorisés.

## II. Edit confidguration
# 1. Configuration cartes réseau

* Modifier la configuration de la carte réseau privée :

    Sur le logiciel VirtualBox : Fichier --> Gestionnaire de réseau hôte --> Ethernet Adapter --> Adresse/Masque Ipv4=192.168.3.1/24

* Dans la VM définir une IP statique pour cette nouvelle carte :

    ```
    [akhadimer@localhost network-scripts]$ cat ifcfg-enp0s8
    BOOTPROTO=static
    NAME=enp0s8
    DEVICE=enp0s8
    ONBOOT=yes
    IPADDR=192.168.3.19
    NETMASK=255.255.255.0
    GATEWAY=192.168.3.254
    ```

# 2. Serveur SSH

* Modifier la configuration du système pour que le serveur SSH tourne sur le port 2222 :

    Accéder au fichier /etc/ssh/sshd_config et modifier la ligne "port".

    *   Ajouter le port 2222 au firewall : 
    
        ```
        [root@localhost ssh]# sudo firewall-cmd --add-port=2222/tcp --permanent
        ```
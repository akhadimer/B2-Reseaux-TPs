# B2 Réseau 2019 - TP1 : Back to Basics
Thomas DUMONT

## I. Gather informations
* Récupérer une liste des cartes réseau avec leur nom, leur IP et leur adresse MAC
    
    >ip a

    ![](https://image.noelshack.com/fichiers/2019/39/4/1569504964-liste-cartes-reseaux.png)


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
    ![](https://image.noelshack.com/fichiers/2019/39/4/1569509055-route-n.png)

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
    ![](https://image.noelshack.com/fichiers/2019/39/4/1569509055-arp-n.png)

    Explication :
    * Address est l'ip du modem
    * HWaddress est l'adresse mac du modem
    * Iface est la carte réseau utilisée

* Récupérer la liste des ports en écoute (listening) sur la machine (TCP et UDP)
    
    Il suffit de faire la commande ``netstat -u -t``

    ![](https://image.noelshack.com/fichiers/2019/39/4/1569510095-netstat-u-t.png)

* Récupérer la liste des DNS utilisés par la machine

    Faire la commande ``nmcli devce show enp0s3``

    ![](https://image.noelshack.com/fichiers/2019/39/4/1569510509-nmcli-device-show-enp0s3.png)
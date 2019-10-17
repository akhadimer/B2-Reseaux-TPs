# B2 Réseau 2019 - TP2 : Network low-level, Switching
Thomas DUMONT

## I. Simplest setup

#### ToDo

* Mettre en place la topologie :

    Dans un premier temps placer deux VPC ainsi qu'un switch, que l'on peut retrouver sur la gauche de l'écran. Les relier par des câbles cliquer sur le bouton "Start" en haut de l'écran. Une fois les machiens allumées, il faut les configurer, on va faire clique droit sur une machine et cliquer sur "console"

    Ensuite on configure l'IP en faisant ``ip 10.2.1.1 255.255.255.0``.

* Faire communiquer les deux PCs :

    ```
    PC1> ping 10.2.1.2
    84 bytes from 10.2.1.2 icmp_seq=1 ttl=64 time=0.331 ms
    84 bytes from 10.2.1.2 icmp_seq=2 ttl=64 time=0.656 ms
    84 bytes from 10.2.1.2 icmp_seq=3 ttl=64 time=0.438 ms
    84 bytes from 10.2.1.2 icmp_seq=4 ttl=64 time=0.485 ms
    84 bytes from 10.2.1.2 icmp_seq=5 ttl=64 time=0.487 ms
    ```

    * Déterminer le protocole utilisé par ping à l'aide de Wireshark :

        ![](https://image.noelshack.com/fichiers/2019/41/3/1570646466-ping-pc1-vers-pc2.png)

        Le protocole utilisé par ping est l'ICMP.

    * Analyser les échanges ARP
        
        Echange ARP suite à un ping de PC2 vers PC1 :

        ![](https://image.noelshack.com/fichiers/2019/41/3/1570655646-ping-pc1-vers-pc2-wireshark-arp.png)

        Table ARP PC1 :

        ```
        PC1> show arp
        00:50:79:66:68:01  10.2.1.2 expires in 114 seconds
        ```

        Table ARP PC2 :

        ```
        PC2> show arp
        00:50:79:66:68:00  10.2.1.1 expires in 44 seconds
        ```

        On peut remarquer que PC1 indique que 10.2.1.2 a l'adresse MAC "00:50:79:66:68:01" et que Wireshark indique aussi la même chose.

*  Récapituler toutes les étapes (dans le compte-rendu, à l'écrit) quand PC1 exécute ping PC2 pour la première fois :

    Dans un premier temps, après que PC1 veuille ping PC2, il demande qui a l'adresse IP 10.2.1.2, ensuite il reçoit une réponse comme quoi l'adresse IP est à l'adresse mac "00:50:79:66:68:00". Maintenant que PC1 sait à qui envoyer le ping, il envoie une "request" (demande) et juste après il reçoit une "reply" (réponse) de la part de PC2.

* Expliquer ...

    * Pourquoi le switch n'a pas besoin d'IP :

        Car il ne sert qu'à relier des segments dans un réseau informatique et ainsi rediriger les connexions entre elle.

    * Pourquoi les machines ont besoin d'une IP pour pouvoir se ping :

        Afin d'identifier qui est qui et de savoir dans quel réseau elles sont.

## II. More switches

#### ToDo

* Mettre en place la topologie :

    ![](https://image.noelshack.com/fichiers/2019/41/4/1570711310-topologie-part2.png)

* Faire communiquer les trois PCs :

    Ping depuis le PC1 :
    ```
    PC1> ping 10.2.2.2
    84 bytes from 10.2.2.2 icmp_seq=1 ttl=64 time=0.985 ms

    PC1> ping 10.2.2.3
    84 bytes from 10.2.2.3 icmp_seq=1 ttl=64 time=0.557 ms
    ```

    Ping depuis le PC2 :
    ```
    PC2> ping 10.2.2.1
    84 bytes from 10.2.2.1 icmp_seq=1 ttl=64 time=0.316 ms
    PC2> ping 10.2.2.3
    84 bytes from 10.2.2.3 icmp_seq=1 ttl=64 time=0.402 ms
    ```

    Ping depuis le PC3 :
    ```
    PC3> ping 10.2.2.1
    84 bytes from 10.2.2.1 icmp_seq=1 ttl=64 time=0.296 ms
    PC3> ping 10.2.2.2
    84 bytes from 10.2.2.2 icmp_seq=1 ttl=64 time=0.685 ms
    ```

* Analyser la table MAC d'un switch :

    ```
          Mac Address Table
    ------------------------------------
    Vlan  Mac Address    Type      Ports
    ----  -----------    --------  -----
     1   0050.7966.6800   DYNAMIC  Et0/0
     1   0050.7966.6801   DYNAMIC  Et0/2
     1   0050.7966.6802   DYNAMIC  Et0/1
     1   aabb.cc00.0200   DYNAMIC  Et0/2
     1   aabb.cc00.0300   DYNAMIC  Et0/1
     1   aabb.cc00.0320   DYNAMIC  Et0/2
    Total Mac Addresses for this criterion: 6
    ```
    Vlan correspond au numéro de la machine virtuelle.

    Mac Address correspond aux adresses qui ont étaient enregistrées sur le switch lorsqu'une machine à voulue communiquer avec une autre.

    Type correspond aux type d'adresse MAC.

    Ports correspond à quel port emprunter sur le switch afin de pouvoir communiquer avec la machine en question.

* Trames CDP :

    CPD, qui veut dire Cisco Discovery Protocol, sert à, comme le dit son nom, découvrir les périphériques autour de lui et ainsi se faire connaître par les machines voisines.

#### Mise en évidence du Spanning Tree Protocol

    * Déterminer les informations STP :

        ```
        SW1#show spanning-tree

        VLAN0001
        Spanning tree enabled protocol rstp
        Root ID    Priority    32769
                    Address     aabb.cc00.0100
                    This bridge is the root
                    Hello Time   2 sec  Max Age 20 sec  Forward Delay 15 sec
        ```
        Le switch 1 est le root bridge.

    * Faire un schéma en représentant les informations STP :

        ![](https://image.noelshack.com/fichiers/2019/42/3/1571260664-schema-info-stp.png)

    * Confirmer les informations STP :

        Lors d'un ping d'un ping de PC3 vers PC1, l'on peut remarquer grâce à Wireshark que entre le switch 1 et le switch 3 un request et un reply passe alors que quand on regarde grâce à Wireshark entre le switch 2 et switch 3, aucun request et reply ne passe.

    * Faire un schéma qui explique le trajet d'une requête ARP lorsque PC1 ping PC3, et de sa réponse :

        ![](https://image.noelshack.com/fichiers/2019/42/3/1571262422-screen-pc1-pc3.png)

#### Reconfigurer STP

    * Changer la priorité d'un switch qui n'est pas le root bridge :

        Après avoir entré la commande ``spanning-tree vlan 1 priority 4096`` sur le witch 3, on peut remarqué le changement en faisant ``show spanning-tree`` :

            ```
            SW3#show spanning-tree

            VLAN0001
            Spanning tree enabled protocol rstp
            Root ID    Priority    4097
                        Address     aabb.cc00.0300
                        This bridge is the root
                        Hello Time   2 sec  Max Age 20 sec  Forward Delay 15 sec
            ```

            Le switch 3 est alors devenu le nouveau root bridge.

## III. Isolation

### 1. Simple

#### ToDo

* Mettre en place la topologie avec des VLANs :

    Sur le switch :

    Créer un vlan 10 en faisant ``vlan 10`` et ensuite lui donner un nom ``name client-network10``. Pareille pour le 20.

    Ensuite donner pour chacun des trois interfaces l'accès vlan qui lui convient. Par exemple pour l'interface 0/0 faire ``interface Ethernet 0/0`` puis ``switchport mode access`` et pour terminer ``switchport access vlan 10``.

* Faire communiquer les PCs deux à deux :

    ```
    PC1> ping 10.2.3.2
    host (10.2.3.2) not reachable

    PC1> ping 10.2.3.3
    host (10.2.3.3) not reachable
    ```

    ```
    PC2> ping 10.2.3.1
    host (10.2.3.1) not reachable

    PC2> ping 10.2.3.3
    84 bytes from 10.2.3.3 icmp_seq=1 ttl=64 time=0.543 ms
    84 bytes from 10.2.3.3 icmp_seq=2 ttl=64 time=0.335 ms
    ```

    ```
    PC3> ping 10.2.3.1
    host (10.2.3.1) not reachable

    PC3> ping 10.2.3.2
    84 bytes from 10.2.3.2 icmp_seq=1 ttl=64 time=0.213 ms
    84 bytes from 10.2.3.2 icmp_seq=2 ttl=64 time=0.362 ms
    ```

### 2. Avec trunk

#### ToDo

* Mettre en place la topologie :

    Pour ce qui concerne la configuration des Vlans, faire la même chose qu'au 1. du III. Pour le trunk, se placer dans un premier temps sur le switch 1, selectionner l'interface qui est connecté à l'autre interface en faisant ``interface Ethernet 0/2``, faire ensuite les commandes suivantes : ``switchport trunk encapsulation dot1q`` et ``switchport mode trunk``. Et ensuite autoriser les vlans concerné : ``switchport trunk allowed vlan 10,20 ``. Faire ensuite la même chose pour le deuxième switch.

* Faire communiquer les PCs deux à deux :

    ```
    PC1> ping 10.2.10.2
    84 bytes from 10.2.10.2 icmp_seq=1 ttl=64 time=0.321 ms
    84 bytes from 10.2.10.2 icmp_seq=2 ttl=64 time=0.491 ms
    84 bytes from 10.2.10.2 icmp_seq=3 ttl=64 time=0.643 ms
    84 bytes from 10.2.10.2 icmp_seq=4 ttl=64 time=0.514 ms
    ```

    ```
    PC4> ping 10.2.20.1
    84 bytes from 10.2.20.1 icmp_seq=1 ttl=64 time=0.303 ms
    84 bytes from 10.2.20.1 icmp_seq=2 ttl=64 time=0.490 ms
    84 bytes from 10.2.20.1 icmp_seq=3 ttl=64 time=0.767 ms
    84 bytes from 10.2.20.1 icmp_seq=4 ttl=64 time=0.806 ms
    ```
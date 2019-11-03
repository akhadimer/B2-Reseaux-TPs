# B2 Réseau 2019 - TP3 : Routage INTER-VLAN + mise en situation
Thomas DUMONT

## I. Router-on-a-stick

* Setup :

    * Schéma :

        ![](https://image.noelshack.com/fichiers/2019/44/7/1572813961-schema-part1.png)

    * Configuration des VPCS, aller sur leurs consoles et par exemple pour le PC1 taper la commande ``ip 10.3.10.1/24 10.3.10.254``. ``10.3.10.1/24`` qui est son adresse IP avec son masque et ``10.3.10.254`` qui est sa gateway ou l'adresse IP qu'on va attribuer sur le routeur.

    * Configuration du routeur, on va créer plusieurs sous-interface afin que les VPCs dans GNS3 puissent communiquer ensemble même s'ils ne sont pas dans le même réseau. Pour le PC1 on va faire les commandes suivantes :

        ```
        (config)# interface ethernet 0/0.10
        (config-if)# encapsulation dot1q 10
        (config-if)# ip address 10.3.10.254 255.255.255.0
        (config-if)# exit
        ```

        ``0/0.10`` indique l'interface qui est connecté au switch et le VLAN qui sera seulement autorisé à communiquer.

        ``ip address 10.3.10.254`` définie l'adresse IP qui sera la gateway par laquelle passera les adresses ip dans le même réseau et la même VLAN que le pc1.

        Faire la même chose pour les VLANS 20, 30 et 40 avec, respectivement, comme adresse IP ``10.3.20.254``, ``10.3.30.254`` et ``10.3.40.254``

    * Configuration des switchs :

        * Sur chaque interface des deux switchs qui sont connectés aux VPCS, il faut configurer les VLANS. Exemple pour le PC1 :
        
        Il faut créer les vlans 10,20,30 et 40 sur le switch1 et sur le switch2 les vlans 20, 30 et 40 :
        ```
        (config)# vlan 10
        (config-vlan)# name client-network10
        (config-vlan)# exit
        ```

        Et ensuite on l'attribue à l'interface du switch qui est connecté au pc1 :
        ```
        (config)# interface Ethernet 0/0
        (config-if)# switchport mode access
        (config-if)# switchport access vlan 10
        ```

        * Configurer ensuite le mode trunk :

            On selectionne l'interface souhaitée, on encapsule et on active le mode trunk :
            ```
            (config)# interface Ethernet 0/0
            (config-if)# switchport trunk encapsulation dot1q
            (config-if)# switchport mode trunk
            ```

            Et par la suite on autorise les vlans qui peuvent passer avec la commande ``switchport trunk allowed vlan 10``. Sur le switch1, pour l'interface qui le relie au switch2 on autorise la vlan 20,30 et 40, et pour l'interface qui le relie au routeur on autorise aussi les vlans 20,30 et 40. Sur le switch2, pour l'interface qui le relie au switch1 on autorise les vlans 20, 30 et 40.

* Prove me that your setup is actually working :

    * p1 :
        * to pc2 :
            ```
            P1> trace 10.3.20.2
            trace to 10.3.20.2, 8 hops max, press Ctrl+C to stop
            1   10.3.40.254   11.722 ms  9.104 ms  9.038 ms
            2     *  *  *
            3   *10.3.20.2   13.261 ms (ICMP type:3, code:3, Destination port unreachable)

            P1> ping 10.3.20.2
            84 bytes from 10.3.20.2 icmp_seq=1 ttl=63 time=14.992 ms
            84 bytes from 10.3.20.2 icmp_seq=2 ttl=63 time=12.065 ms
            ```
    * PC1 :
        * to pc3 :
            ```
            PC1> ping 10.3.20.2
            host (10.3.10.254) not reachable
            ```     
    * PC2 :
        * to PC3 :
            ```
            PC2> trace 10.3.20.3
            trace to 10.3.20.3, 8 hops max, press Ctrl+C to stop
            1   *10.3.20.3   0.410 ms (ICMP type:3, code:3, Destination port unreachable)

            PC2> ping 10.3.20.3
            84 bytes from 10.3.20.3 icmp_seq=1 ttl=64 time=0.494 ms
            84 bytes from 10.3.20.3 icmp_seq=2 ttl=64 time=0.484 ms
            84 bytes from 10.3.20.3 icmp_seq=3 ttl=64 time=0.458 ms

            ```
        * to PC4 :
            ```
            PC2> trace 10.3.30.4
            trace to 10.3.30.4, 8 hops max, press Ctrl+C to stop
            1   10.3.20.254   9.220 ms  9.550 ms  9.245 ms
            2     *  *  *
            3   *10.3.30.4   14.429 ms (ICMP type:3, code:3, Destination port unreachable)

            PC2> ping 10.3.30.4
            84 bytes from 10.3.30.4 icmp_seq=1 ttl=63 time=15.291 ms
            84 bytes from 10.3.30.4 icmp_seq=2 ttl=63 time=17.231 ms
            ```
    * PC3 :
        * to pc1 :
            ```
            PC3> trace 10.3.10.1
            trace to 10.3.10.1, 8 hops max, press Ctrl+C to stop
            1   10.3.20.254   9.717 ms  9.392 ms  9.873 ms
            2     *  *  *
            3     *  *  *
            4     *  *  *
            5     *  *  *
            6     *  *  *
            7     *  *  *
            8     *  *  *
            ```
    * PC4 :
        * to p1 :
            ```
            PC4> trace 10.3.40.1
            trace to 10.3.40.1, 8 hops max, press Ctrl+C to stop
            1   10.3.30.254   12.899 ms  9.530 ms  9.226 ms
            2     *  *  *
            3   *10.3.40.1   15.299 ms (ICMP type:3, code:3, Destination port unreachable)

            PC4> ping 10.3.40.1
            84 bytes from 10.3.40.1 icmp_seq=1 ttl=63 time=18.775 ms
            84 bytes from 10.3.40.1 icmp_seq=2 ttl=63 time=15.464 ms
            ```
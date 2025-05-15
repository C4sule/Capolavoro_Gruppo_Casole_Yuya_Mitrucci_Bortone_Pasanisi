# CONFIGURAZIONE DI RETE – ANALISI TECNICA

## INDIRIZZAMENTO IP
La rete locale è divisa in più sottoreti, ognuna con il proprio schema IP. Ad esempio:

Rete aziendale: 192.168.1.0/24

Server HTTP: 192.168.2.100

Gateway/router verso l'esterno: 200.200.200.1/24


Comando su router per configurare un’interfaccia: Router(config)# interface g0/0
````
Router(config-if)# ip address 192.168.1.1 255.255.255.0
Router(config-if)# no shutdown
````
## DHCP – ASSEGNAZIONE IP DINAMICA
Il router fornisce automaticamente indirizzi IP ai client attraverso il servizio DHCP.

Comandi di configurazione: 
````
Router(config)# ip dhcp pool LAN
Router(dhcp-config)# network 192.168.1.0 255.255.255.0
Router(dhcp-config)# default-router 192.168.1.1
Router(config)# ip dhcp excluded-address 192.168.1.1 192.168.1.10
````
## NAT (Many-to-1)
Il router esegue il NAT overload per permettere ai dispositivi della rete privata di uscire su Internet con un solo IP pubblico.

Configurazione: 
````
Router(config)# access-list 1 permit 192.168.1.0 0.0.0.255
Router(config)# interface g0/0
Router(config-if)# ip nat inside
Router(config)# interface g0/1
Router(config-if)# ip nat outside
Router(config)# ip nat inside source list 1 interface g0/1 overload
````
## SWITCH E ROUTER INTERNI (RETE PRIVATA)

All’interno della rete sono presenti switch configurati per connettere client. Il router privato è configurato per gestire una sottorete.

Per configurare uno switch per la gestione remota:
````
Switch(config)# interface vlan 1
Switch(config-if)# ip address 192.168.1.2 255.255.255.0
Switch(config-if)# no shutdown
Switch(config)# ip default-gateway 192.168.1.1
````
## SIMULAZIONE DI RETE GLOBALE

È presente una rete “Internet simulata” che collega il router aziendale con un router esterno via indirizzi pubblici (es. 200.200.200.0/24).

Esempio su router esterno: 
````
Router(config)# interface g0/0
Router(config-if)# ip address 200.200.200.2 255.255.255.0
Router(config-if)# no shutdown
````

## ROUTING – STATICO, RIP, OSPF

Sono utilizzati metodi di routing per connettere più sottoreti.

Esempio di routing statico:
````
Router(config)# ip route 192.168.2.0 255.255.255.0 10.0.0.2
````
Per attivare RIP V1:
````
Router(config)# router rip
Router(config-router)# network 192.168.1.0
Router(config-router)# network 10.0.0.0
````

Per OSPF:
````
Router(config)# router ospf 1
Router(config-router)# network 192.168.1.0 0.0.0.255 area 0
Router(config)interface g0/0
Router(config-if)ip ospf cost 5
````
## SERVIZIO REMOTO HTTP (NAT 1-to-1)
Un server web interno (192.168.2.100) è mappato a un IP pubblico (200.200.200.100) con NAT statico.

## Configurazione NAT 1-to-1: 
````
Router(config)# ip nat inside source static 192.168.2.100 200.200.200.100
Router(config)# interface g0/0
Router(config-if)# ip nat inside
Router(config)# interface g0/1
Router(config-if)# ip nat outside
````
Dalla rete globale, si potrà accedere al sito con: http://200.200.200.100

# README
LinuxGsm user sfserver : 192.168.1.14

### Lister les règles de FORWARD
iptables -L FORWARD --line-numbers -v

### Lister les règles de NAT
iptables -t nat -L --line-numbers -v

### Supprimer une règle FORWARD par numéro de ligne
iptables -D FORWARD <numéro de ligne>

### Supprimer une règle NAT par numéro de ligne
iptables -t nat -D POSTROUTING <numéro de ligne>

## Configuration  

Ajouter une regle nat pour autoriser le traffic sur le reseaux local
A faire pour chaque client VPN  

iptables -t nat -A POSTROUTING -o eth0 -s 10.66.66.5 -j MASQUERADE  
iptables -t nat -A POSTROUTING -o eth0 -s 10.66.66.2 -d 192.168.1.0/24 -j MASQUERADE  
iptables -t nat -A POSTROUTING -o eth0 -s 10.66.66.3 -j MASQUERADE  
iptables -t nat -A POSTROUTING -o eth0 -s 10.66.66.4 -d 192.168.1.0/24 -j MASQUERADE  

Dans les regles iptables de FORWARD commencer par autoriser les reponses aux demande initie :

iptables -A FORWARD -m state --state ESTABLISHED,RELATED -j ACCEPT

P1 : Acces a tous le sous reseaux local + internet

iptables -A FORWARD -i wg0 -o eth0 -s 10.66.66.5 -d 192.168.1.0/24 -j ACCEPT  

P2 : Acces a tous le reseaux local sans internet

iptables -A FORWARD -i wg0 -o eth0 -s 10.66.66.2 -d 192.168.1.1 -j DROP  
iptables -A FORWARD -i wg0 -o eth0 -s 10.66.66.2 -d 192.168.1.0/24 -j ACCEPT  

P3 : Acces que a Linuxgsm + internet

iptables -A FORWARD -i wg0 -o eth0 -s 10.66.66.3 -d 192.168.1.14 -j ACCEPT  
iptables -A FORWARD -i wg0 -o eth0 -s 10.66.66.3 -d 192.168.1.1 -j ACCEPT  
iptables -A FORWARD -i wg0 -o eth0 -s 10.66.66.3 -d 192.168.1.0/24 -j DROP  

P4 : Acces que a linuxgsm sans internet

iptables -A FORWARD -i wg0 -o eth0 -s 10.66.66.4 -d 192.168.1.1 -j DROP  
iptables -A FORWARD -i wg0 -o eth0 -s 10.66.66.4 -d 192.168.1.14 -j ACCEPT  
iptables -A FORWARD -i wg0 -o eth0 -s 10.66.66.4 -d 192.168.1.0/24 -j DROP  

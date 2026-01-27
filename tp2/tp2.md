## TP 2 - Exploration du réseau d'un point de vue client

### I. Exploration locale en solo

#### 1. Affichage d'informations sur la pile TCP/IP locale

<!-- En utilisant la ligne de commande (CLI) de votre OS :
Affichez les infos des cartes réseau de votre PC

nom, adresse MAC et adresse IP de l'interface WiFi
nom, adresse MAC et adresse IP de l'interface Ethernet
déterminer, pour chacune d'entre elles :

adresse de réseau
adresse de broadcast -->

Pour commencer, j'ai utilisé la commande 'ifconfig'

#### **Interface wifi** 
```
en0: flags=8863<UP,BROADCAST,SMART,RUNNING,SIMPLEX,MULTICAST> mtu 1500
	options=6460<TSO4,TSO6,CHANNEL_IO,PARTIAL_CSUM,ZEROINVERT_CSUM>
	ether c2:c3:21:71:b7:50
	inet6 fe80::18f1:5783:bce9:c881%en0 prefixlen 64 secured scopeid 0xb 
	inet 10.33.70.18 netmask 0xfffff000 broadcast 10.33.79.255
	nd6 options=201<PERFORMNUD,DAD>
	media: autoselect
	status: active
```
nom : en0  
adresse MAC : c2:c3:21:71:b7:50  
adresse IP : 10.33.70.18  

adresse réseau : 10.33.00.00  
broadcast :  10.33.79.255  

Pour obtenir l'adresse de la gateway, j'utilise la commande `route get default | grep gateway`

```
route get default | grep gateway
    gateway: 10.33.79.254
```

### En graphique (GUI : Graphical User Interface)

<img width="471" height="508" alt="Image" src="https://github.com/user-attachments/assets/5b49ec1c-7453-45d1-bf81-c722aa4ce411" />  

adresse MAC : c2:c3:21:71:b7:50  
adresse IP : 10.33.70.18  
gateway : 10.33.79.254

#### Questions
À quoi sert la gateway dans le réseau d'Ynov ?

Sur le réseau d'Ynov, la gateway permet de relier les différents réseaux entre eux.

### 2. Modifications des informations

#### A. Modification d'adresse IP - pt. 1

**J'ai effectué les opérations qui suivent sur mon réseau personnel avec une VM sous ParrotOS car le réseau d'Ynov ne me permettait pas de scanner avec nmap correctement**

<img width="943" height="284" alt="Image" src="https://github.com/user-attachments/assets/931b587a-9189-4636-972c-0913a39755fb" />

```
Adresse IP : 192.168.64.18
Netmask : 255.255.255.0
Broadcast : 192.168.64.255
```
J'en déduis que l'adresse réseau est `192.168.64.0`, car lorsque l'on superpose l'adresse IP et le netmask, on voit que le dernier numéro pour le netmask est 0.

Comme le broadcast utilise l'adresse `192.168.64.255`, on en déduit que la dernière adresse IP disponible est au moins `192.168.64.254`

Pour continuer, j'ai décidé de changer mon ip en `192.168.64.56`. Pour cela, j'ai utilisé la commande suivante

`ip addr add 192.168.64.56/24 dev enp0s1`

Je fais en suite un `ifconfig`pour vérifier si mon adresse IP a bien été changée,

<img width="917" height="286" alt="Image" src="https://github.com/user-attachments/assets/b8aaf851-c569-4b25-bda6-c0c3d25a5d62" />

On peut voir que le changement a été correctement effectué.

#### B. nmap

Pour continuer, j'ai scanné l'adresse réseau que j'ai obtenu à l'aide les opérations précédentes.

Pour ça j'ai fait `nmap -sN -PE 192.168.64.0/24`

<img width="715" height="233" alt="Image" src="https://github.com/user-attachments/assets/c9588ed7-c439-4608-aee9-0a7c795f61bc" />

Comme l'indique la ligne `(2 hosts up)`, on peut comprendre que le réseau est utilisé par deux appareils. En l'occurence ma machine `192.168.64.18`, et probablement par la gateway.

#### C. Modification d'adresse IP - pt. 2

## II. Exploration locale en duo

N/A

## III. Manipulations d'autres outils/protocoles côté client

### 1. DHCP

Pour cette partie, j'ai commencé par afficher l'adresse IP du DHCP avec la commande


<img width="869" height="224" alt="Image" src="https://github.com/user-attachments/assets/cda1c9cf-d426-479e-98b4-ed9fe3116a17" />

```
DHCP4.OPTION[3]:                        dhcp_server_identifier = 192.168.64.1
DHCP4.OPTION[5]:                        expiry = 1769514754
```

On a donc l'adresse IP du DHCP et son bail d'expiration
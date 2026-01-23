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

#### B. nmap

#### C. Modification d'adresse IP - pt. 2

## II. Exploration locale en duo

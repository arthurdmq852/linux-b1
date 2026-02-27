# TP 6 - Mise en place d'un serveur OpenVPN sur Ubuntu Server


# Mise en place

Pour commencer, j'installe Ubuntu Server sur Virtualbox avec SSH.

Je ne pouvais pas ping ma VM directement depuis ma machine, j'ai donc dû faire du port forwarding sur le port 2222 :

<img src=images/1.png>

# Partie 1 : Comprendre la PKI

Une fois cela fait, j'ai mis à jour le système et j'ai installé OpenVPN & Easy-RSA.

## Questions

1.  À quoi sert une autorité de certification (CA) ?
2.  Quelle différence entre clé privée et certificat ?
3.  Pourquoi un serveur VPN a-t-il besoin de certificats ?

## Réponses

1. Une autorité de certification sert à 

2. Une clé privée est secrète, tandis que le certificat est public.

3. Un serveur VPN a besoin de certificats pour assurer l'authentification, la signature & le chiffrement.

# Création de l'infrastructure Easy-RSA

Je commence par créer un environnement PKI.

Étant donné que j'ai installé easy-rsa en récupérant le lien sur github, j'ai dû l'extraire et me rendre dans le dossier correspondant.

Une fois dedans j'exécute ces commandes :

```
./easyrsa init-pki
./easyrsa build-ca
```

La seconde permettant de générer un certificat CA.

<img src=images/2.png>

Je génère ensuite le certificat serveur avec 

```
./easyrsa gen-req server nopass
./easyrsa sign-req server server
```

<img src=images/3.png>
<img src=images/4.png>

Puis je génère les paramètres DH

```
./easyrsa gen-dh
```

<img src=images/5.png>

Et enfin, je génère une clé TLS 

```
openvpn --genkey secret ta.key
```

# Questions :

1. Où Easy-RSA crée-t-il ses fichiers ?
2. Que contient le dossier `pki/` ?
3. Quelle est la différence entre gen-req et sign-req ?
4. Que se passe-t-il si vous oubliez de signer un certificat ?

# Réponses :

1. Easy-RSA crée ses fichiers dans le dossier où l'on a exécuté la commande `./easyrsa`.

2. Le dossier `pki/` contient tout ce qui a été généré précédemment et d'autres fichiers :

<img src=images/6.png>

3. La différence entre `gen-req` & `sign-req` est que l'un génère la requête tandis que l'autre la signe.

4. Si l'on oublie de certifier le contrat, on n'aura pas l'autorité de certification.

# Partie 2 : Configuration du serveur OpenVPN

Dans cette partie, je crée un fichier de configuration serveur


# Questions :


1. Que signifie dev tun ?
2. Quelle est la différence entre UDP et TCP pour un VPN ?
3. Quelle plage IP choisir pour le VPN ? Pourquoi ?

# Réponses :

1. `dev tun` correspond à l'interface réseau virtuelle qui gère le trafic IP. C'est le Tunnel 

2. TCP est précis et envoie toutes les informations (plus lent), tandis qu'UDP peut avoir des pertes, mais est plus rapide.

3.


# Routage et NAT

Activer le forwarding IP
Mettre en place une règle NAT pour avoir l'accès internet depuis le vpn


Dans cette partie, je commence par vérifier l'état de l'IP forwarding avec

```
sysctl net.ipv4.ip_forward
```

Par défaut il est désactivé 

<img src=images/7.png>

Je l'active de manière permanente en modifiant 

```
sudo nano /etc/sysctl.conf
```

<img src=images/8.png>

Je recharge enfin la configuration pour être sûr que les changements prennent effet avec :

```
sysctl -p /etc/sysctl.conf
```

Ensuite, je mets en place le NAT avec

```
ip route list default
```

J'observe l'interface que j'obtiens, et je l'applique avec IPTables

```
sudo iptables -t nat -A POSTROUTING -s 10.8.0.0/24 -o enp0s3 -j MASQUERADE
```

et enfin je rends les règles permanentes 

```
sudo apt install iptables-persistent
```

<img src=images/9.png>


# Questions :

1. Où se configure le paramètre ip_forward ?
2. Quelle commande permet d'afficher les règles NAT actuelles ?
3. Pourquoi faut-il "masquerader" le réseau VPN ?

# Réponses :

1. Le paramètre `ip_forward` se configure dans `/etc/sysctl.conf`.

2. La commande qui permet d'afficher les règles NAT actuelles est `sudo iptables -t nat`.

3. Il faut "masquerader" le réseau VPN car

# Démarrage et analyse du service

Je démarre ensuite OpenVPN et je vérifie son état avec

```
sudo systemctl enable openvpn
sudo systemctl start openvpn
sudo systemctl status openvpn
```

<img src=images/10.png>

# Partie 3 : Création du profil client

Pour cette partie, je crée un fichier `.opvn`.


# Questions :

1. Comment intégrer un certificat directement dans un fichier .ovpn ?
2. Pourquoi la clé privée ne doit-elle jamais être partagée publiquement ?

# Réponses :

1. Pour intégrer un certificat directement dans un fichier  `.opvn`, il faut copier coller le certificat du début à la fin

<img src=images/11.png>


2. La clé privée ne doit jamais être partagée publiquement car elle 

# Tests et validation

Pour finir, Je lance ma configuration avec 

```
sudo openvpn client1.ovpn
```

<img src=images/13.png>

Lorsque l'on fait un `ip a` sur la même machine cliente sur un autre terminal, on voit qu'une nouvelle interface tunnel a été créee avec les ip 

<img src=images/15.png>

Pour finir, j'envoie mon fichier configuration `client1.ovpn` à un camarade pour qu'il l'ouvre directement sur le launcher OpenVPN.



# Questions :

-   Comment vérifier que votre trafic passe par le VPN ?
-   Que se passe-t-il si le port 1194 est bloqué ?

# Réponses :

1. Pour vérifier que mon trafic passe par le VPN, je dois utiliser la commande `traceroute 8.8.8.8`

2. Si le port 1194 est bloqué, j'obtiendrais une erreur et la connexion échouera.
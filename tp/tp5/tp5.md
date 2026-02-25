# TP5 : pfSense – Bases d’un pare-feu

## Partie 1 – Prise en main et sécurisation
### 1. Accès à l’interface

Pour commencer, j'installe pfSense, je le configure et je me connecte à l'interface web.

<img src=images/1.png>


Questions :

1. Quelle est l’adresse IP du LAN  ?
2. Quelle est l’adresse IP du WAN ?
3. Pourquoi utilise-t-on HTTPS ?
4. Pourquoi faut-il changer les identifiants par défaut sur un pare-feu ?

Réponses :

1. L'adresse IP du LAN est 192.168.56.2

2. L'adresse IP du WAN est 10.0.2.15

3. On utilise HTTPS pour la sécurité

4. Il faut changer les identifiants par défaut car un individu malintentionné pourrait avoir accès à notre compte

### 2. Sécurisation de l’accès administrateur

Pour cette partie, je me rends dans `User Manager`, et je change le mot de passe


Questions :

1. Où se gèrent les utilisateurs ?
2. Qu’est-ce qu’un mot de passe robuste ?
3. Pourquoi sécuriser en priorité l’accès admin sur un équipement réseau ?

Réponses : 

1. Les utilisateurs se gèrent dans le User Manager.

2. Un mot de passe robuste est un mot de passe contenant au moins un nombre, une majuscule, un symbole, et un certain nombre de caractères.

3. On sécurise en priorité l'accès admin car c'est celui qui a tous les accès, si un individu malintentionné obtient l'accès au compte admin, il peut s'attaquer au réseau complet.

## Partie 2 – Comprendre les interfaces réseau
### 3. Vérification des interfaces

Questions :

1. Quelle interface permet l’accès Internet ?
2. Quelle interface correspond au réseau interne ?
3. Que se passerait-il si les interfaces étaient inversées ?

Réponses

1. L'interface qui permet l'accès Internet est le WAN.

2. Le réseau interne est le LAN.

3. Le réseau ne marcherait pas.

J'ai essayé d'inverser les deux interfaces pour voir ce qu'il se passerait

<img src=images/2.png>

## Partie 3 – Configuration des services réseau
### 4. DHCP

Pour cette partie, j'active le DHCP sur pfSense pour donner une IP à ma vm connecté en host only 

<img src=images/3.png>

Je rajoute ma gateway pour avoir accès à Internet

<img src=images/13.png>

Questions :

1. Pourquoi utiliser DHCP plutôt qu’une IP fixe ?
2. Quelle plage d’adresses choisir ?
3. Quelles adresses faut-il éviter d’inclure dans la plage ?

Réponses : 

1. Avec le DHCP, un nouvel appareil aurait automatiquement une nouvelle adresse IP.

2. 

3. Il faut éviter d'inclure l'adresse finissant par 0 & 255 car elles sont utilisées par 

Vérification :

Ubuntu obtient-elle automatiquement une adresse IP ?

Pour ma vm, j'ai défini une plage d'adresses compris entre 120 & 200, on voit que j'obtiens `192.168.56.120` en IP.

<img src=images/5.png>

Je peux vérifier que ma VM ping bien 8.8.8.8 pour voir si ça marche.

<img src=images/7.png>

### 5. DNS

Activez et configurez le résolveur DNS.

Dans cette partie, j'active le DNS sur pfSense, et je vérifie que je peux `ping google.com`.

<img src=images/6.png>

Questions :

1. Pourquoi un pare-feu peut-il jouer le rôle de serveur DNS ?

2. Que se passe-t-il si le DNS ne fonctionne pas mais que le ping vers 8.8.8.8 fonctionne ?

Réponses 

1. Un pare-feu peut l'accès à des adresses, comme un serveur DNS peut être paramétré pour ne pas donner certaines adresses IP.

2. Lorsque le DNS ne fonctionne pas, on peut accéder à un site Internet via l'IP mais pas le nom de domaine.

## Partie 4 – Autoriser l’accès Internet
### 6. Règles de pare-feu

Je me rends dans les règles du pare-feu, je vois qu'il y a des règles déjà créées.

<img src=images/8.png>

Lorsque je les désactive, je ne peux plus ping `8.8.8.8`, ni `pfSense`, ni `google.com`, et je n'ai plus accès aux sites web.

<img src=images/9.png>

<img src=images/10.png>

<img src=images/11.png>

<img src=images/12.png>

Questions :

1. Quelle doit être la source ?
2. Quelle doit être la destination ?
3. Faut-il autoriser tous les protocoles ?

Réponses :

1. La source doit être le LAN.

2. La destination doit être l'accès à Internet, sans ouvrir tous les ports (Wan Net).

3. Il faut autoriser les protocoles en fonction de notre usage, il faut activer au minimum Internet. 

### 7. NAT

<img src=images/14.png> 

Le NAT est en automatique, donc il génère automatiquement les règles de NAT.

Questions :

1. Pourquoi le NAT est-il nécessaire avec une interface WAN en NAT ?
2. Quelle est la différence entre NAT automatique et manuel ?
3. Comment vérifier qu’une traduction d’adresse a lieu ?

Réponses

1. Le NAT est nécessaire avec une interface WAN en NAT car

2. La différence entre NAT automatique et manuel est que

## Partie 5 – Filtrage
### 8. Blocage d’un site spécifique

Pour cette partie, j'utilise le DNS Resolver pour bloquer le site. Pour cet exemple, je vais utiliser `facebook.com`

<img src=images/23.png>

<img src=images/22.png>

Nous pouvons voir que facebook n'est plus accessible sur ma machine virtuelle.

Questions :

1. Faut-il bloquer par IP ou par nom de domaine ?
2. Que se passe-t-il si le site utilise HTTPS ?
3. Pourquoi le blocage par IP peut-il être contourné ?

Réponses 

1. On peut bloquer un site à la fois via IP & par nom de domaine.

2. Lorsque le site utilise HTTPS, 

3. Le bloquage de l'ip peut être contourné car lorsque l'on bloque une IP, le site web peut utiliser une autre IP.

### 9. Blocage d’une catégorie de sites (jeux d’argent)

Pour cette partie, je me rends dans firewall > aliases sur pfsense.

Je crée une catégorie pour bloquer les sites de jeux d'argent en y ajoutant deux sites pour tester (winamax & pmu ici).

<img src=images/24.png>

Je me rends ensuite dans rules pour crée la règle interdissant l'accès à ces sites 

<img src=images/26.png>

Je vérifie ensuite que les sites soient bien bloqués.

<img src=images/25.png>

Questions :

1. Pourquoi ne pas créer une règle par site ?
2. Où se créent les alias ?
3. Comment vérifier qu’une règle bloque réellement le trafic ?

Réponses :

1. Crée une règle par site prendrait trop de temps et ne serait pas le plus simple.

2. Ils se créent dans Firewall > Aliases

3. On peut vérifier qu'une règle bloque réellement le trafic en regardant les logs.

## Partie 6 – Aller plus loin (partie plus tendue)
### 10. Blocage par catégorie (réseaux sociaux)

Pour cette partie, je repars des mêmes étapes que la partie précédente en passant aux réseaux sociaux. J'ajoute juste l'ajout des logs lorsque je crée la règle. 

<img src=images/27.png>

Je me rends finalement dans status > System Logs > Firewall > Normal View et je vérifie les logs

<img src=images/28.png>

Malheureusement, les logs ne montraient pas les sites bloqués sur ma vm.

Question :

1. Que se passe-t-il si la règle est placée sous une règle "Pass Any" ?

Réponse :

1. Comme pfSense lit les règles de haut en bas, si une règle est placée sous une Pass Any, elle ne sera pas prise en compte car la première règle autorise l'accès au site.

### 11. Règles horaires


Pour cette partie, je crée une règle pour bloquer sur une plage horaire.

<img src=images/20.png>

J'ajoute ensuite une règle qui contient les horaires.

<img src=images/21.png>

Les sites web seront donc bloquer sur cette plage horaire.

Questions :

Pourquoi les règles horaires sont-elles utiles en entreprise ?

Réponse : 

Les règles horaires sont utiles en entreprise car elles permettent de réduire la bande passante, et de réduire les risques d'attaque, car on ne peut pas se connecter au réseau durant les heures où les plages horaires sont appliquées.


### 12. Serveur web local


Pour cette partie, j'ai utilisé Apache, qui est téléchargeable directement sur Ubuntu avec `sudo apt install apache2`.

Je paramètre comme est indiqué sur la documentation d'apache2 sur ubuntu et je vérifie sur ma machine cliente que mon site web fonctionne 

<img src=images/29.png>

Une fois le serveur bien démarré, j'autorise l'accès spécifique à ma machine cliente :

<img src=images/31.png>

et je bloque l'accès aux autres machines :

<img src=images/30.png>

Questions :


1. Filtrer par IP source ?
2. Filtrer par port ?
3. Pourquoi le pare-feu protège-t-il le LAN même en réseau interne ?

Réponses :

1. Filtrer par IP source permet de crée des rules qui ne s'applique qu'à un appareil précis.

2. Filtrer par port permet de bloquer certains accès à des ports précis, quelque chose que l'on ne voudrait pas.

3. 

### 13. Logs et analyse

Pour cette partie, je me rends dans firewall > rules et j'active le bouton log sur les règles que je veux surveiller

<img src=images/32.png>

et je me rends dans status > system logs

<img src=images/33.png>

Questions :

1. Différence entre paquet bloqué et autorisé
2. Identifier quelle règle a déclenché le blocage
3. Comprendre le sens du trafic

Réponses :

1. Un paquet bloqué est marqué par une croix rouge et un paquet autorisé par un symbole vert.

2. 

3.

### 14. DMZ

Questions :

1. Qu'est ce qu'une DMZ ?
2. Pourquoi isoler un serveur ?
3. Une machine en DMZ peut-elle accéder au LAN ?
4. Le LAN peut-il accéder librement à la DMZ ?

Réponses : 

1. Une DMZ est un sous réseau qui reste séparé du réseau qui sert de couche supplémentaire de sécurité.

2. Pour avoir un meilleur contrôle sur le serveur.

3. Une machine en DMZ ne doit pas accéder au LAN car en cas d'attaque, les hackers pourraient avoir un accès libre.

4. Le LAN peut accéder à la DMZ mais en vérifiant ce qu'il s'y passe


### 15. Filtrage MAC

Pour cette partie, je me rends dans Services > DHCP server et en bas de la page je peux ajouter mon adresse MAC

<img src=images/34.png>

Une fois cela fait, j'ajoute une IP v4 qui n'appartient pas à la plage d'adresses que l'on a défini plus tôt.

Je bloque ensuite les intrus avec deny unknown clients

<img src=images/35.png>


Questions :
1. Le filtrage MAC est-il réellement sécurisé ?
2. Pourquoi est-il facilement contournable ?

Réponses :

1. Le filtrage MAC n'est pas réellement sécurisé car il permet juste d'éviter les connexions automatiques.

2. Il peut être facilement contourné notamment avec du MAC Spoofing.


### 16. Portail captif

Dans cette partie, je me rends dans services > captive portal.


Et je paramètre mon portail captif.

<img src=images/36.png>

Questions :

1. Dans quels contextes utilise-t-on cela ?
2. Quelle(s) avantage(s) avec une simple règle de pare-feu ? 

Réponses :

1. On utilise un portail captif dans les lieux publics, et lorsque l'on utilise un contrôle parental.

2. L'avantage avec un portail captif est qu'on doit s'authentifier avec un identifiant et un mot de passe.

### 17. Sauvegarde / restauration

Enfin, je sauvegarde ma configuration en allant dans diagnostics > backup & restore, 

<img src=images/37.png>

Je peux m'amuser à la modifier. Puisque dans tous les cas j'ai juste à charger mon fichier précédemment télécharger pour récuperer ma configuration précédente.

<img src=images/38.png>


Question :

Pourquoi la sauvegarde régulière est-elle essentielle en production ?

Réponse :

La sauvegarde régulière est essentielle car si l'on à un problème, on peut revenir à notre sauvegarde, ou même si l'on souhaite revenir à une ancienne version de la configuration du pare-feu.
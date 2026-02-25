# TP°4 – Administration SSH et Serveur Web Nginx


## Partie 1 – Mise en place de l’environnement virtualisé

Pour commencer, je vérifie mon ip sur la vm avec

```
ip a
```

Puis je la ping depuis ma machine locale.

![image](https://hackmd.io/_uploads/By0KGAbOZe.png)

![image](https://hackmd.io/_uploads/H1AqfRb_bx.png)

## Partie 2 – Serveur SSH

Ensuite j'installe `openssh-server` sur la VM pour pouvoir m'y connecter depuis ma machine locale.

Puis je vérifie que le service fonctionne avec 

systemctl status openssh-server

![image](https://hackmd.io/_uploads/HkJffR-dbx.png)

Et lorsque ça marche, je me connecte en ssh via mon terminal Powershell avec la commande

```
ssh vm@<IP_VM>
```

![image](https://hackmd.io/_uploads/S1GKQ0W_bl.png)

Enfin, je génère une paire de clé publique/privée sur ma machine, que je copie sur la vm afin de pouvoir m'y connecter sans mot de passe.

### À PARTIR DE CETTE PARTIE, J'AI CHANGÉ DE MACHINE CLIENTE (macos) & DE VM (kali) CAR J'AVAIS TROP DE SOUCIS SUR MON AUTRE MACHINE

![Screenshot 2026-02-18 at 19.16.45](https://hackmd.io/_uploads/H1YGMt7uZx.png)

`ssh-keygen` pour génerer les clés sur ma machine
`ssh-copy-id vm@<ip vm>` pour copier la clé publique sur la machine.

## Partie 3 – Sécurisation SSH

Pour commencer, j'interdis l'accès root, je change le port et je retire l'authentification par mot de passe avec

`sudo nano /etc/ssh/sshd_config`

![Screenshot 2026-02-18 at 19.23.06](https://hackmd.io/_uploads/ByU57YmObe.png)

![Screenshot 2026-02-18 at 19.23.21](https://hackmd.io/_uploads/BJ4iQYmOWe.png)

Je redémarre le service ssh sur ma vm avec

`systemctl restart ssh`

Puis je réessaye de me connecter sans indiquer le port pour vérifier que la vm m'empêche de me connecter.

![Screenshot 2026-02-18 at 19.25.22](https://hackmd.io/_uploads/HyAGEt7dWe.png)

On peut voir que c'est bien le cas ici.

Je réessaye alors avec le port 2222, et on voit que ça marche

![Screenshot 2026-02-18 at 19.26.57](https://hackmd.io/_uploads/BJnuVYX_Wl.png)

Enfin, je crée un alias pour ne pas avoir à taper l'ip et le nom de la vm à chaque fois avec

`nano ~/.ssh/config` 

![Screenshot 2026-02-18 at 19.35.43](https://hackmd.io/_uploads/rkjtItmd-l.png)


![Screenshot 2026-02-18 at 19.35.13](https://hackmd.io/_uploads/BynPLK7O'bg.png)


## Partie 4 – Transfert de fichiers

Pour cette partie, je crée un fichier que je transfère avec

`scp test.file kali_ssh:/home/super`

![Screenshot 2026-02-18 at 19.39.12](https://hackmd.io/_uploads/rJ3IvKQOZg.png)

On peut voir qu'il a bien été transféré sur la VM.

![Screenshot 2026-02-18 at 19.39.43](https://hackmd.io/_uploads/BJadwY7d-g.png)

Ensuite, avec SFTP, je vérifie que je puisse lire les fichiers sur la vm. 

![Screenshot 2026-02-18 at 19.41.39](https://hackmd.io/_uploads/SygeuFXO-g.png)

Je crée ensuite un fichier `test2.file` sur ma vm que je récupere sur ma machine avec `get test2.file`

![Screenshot 2026-02-18 at 19.43.23](https://hackmd.io/_uploads/HyuI_Fm_Ze.png)

![Screenshot 2026-02-18 at 19.44.35](https://hackmd.io/_uploads/HJ65dKQOZx.png)

Enfin, je crée un fichier sur ma machine que je transfère avec 

`put test3.file`

![Screenshot 2026-02-18 at 19.45.10](https://hackmd.io/_uploads/SklpdYm_bx.png)

## Partie 5 – Analyse des logs et sécurité

Tout d'abord, je regarde les logs d'authentification
Contrairement à ce qui est indiqué dans la documentation du TP, la commande ne marchait pas pour voir les logs car je suis sur Kali. 

Je devais donc utiliser la commande

```
journalctl -u ssh.service | tail -f
```

![Screenshot 2026-02-19 at 09.37.02](https://hackmd.io/_uploads/HJ53jH4dZe.png)

## Partie 6 – Tunnel SSH


Pour me connecter via tunnel local j'utilise 

```
ssh -L 8080:localhost:80 serveur-tp
```

et pour me connecter via un tunnel distant

```
ssh -R 9090:localhost:22 serveur-tp
```

## Partie 7 – Nginx et HTTPS

Pour cette partie, je commence par installe nginx avec

```
sudo apt install nginx -y
```

Je crée le dossier du site pour tester nginx avec

```
sudo mkdir -p /var/www/site-tp4
nano /var/www/site-tp4/index.html
```
et j'ajoute dans le fichier html

```
<h1>Bienvenue sur le site TP Nginx !</h1>
```

Puis je configure Nginx

```
sudo vim /etc/nginx/sites-available/site-tp4
```

et je mets

```
server {
    listen 80;
    server_name localhost;
    root /var/www/site-tp4;
    index index.html;
}
```

Je l'active et je le test avec 
```
sudo ln -s /etc/nginx/sites-available/site-tp4 /etc/nginx/sites-enabled/
sudo nginx -t
sudo systemctl restart nginx
```

Je génère ensuite le certificat auto-signé HTTPS 

```
sudo mkdir -p /etc/nginx/ssl
cd /etc/nginx/ssl
sudo openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout site-tp4.key -out site-tp4.crt
```

Enfin, je configure Nginx pour le HTTPS en modifiant
`/etc/nginx/sites-available/site-tp`

```
server {
    listen 443 ssl;
    server_name localhost;

    ssl_certificate /etc/nginx/ssl/site-tp.crt;
    ssl_certificate_key /etc/nginx/ssl/site-tp.key;

    root /var/www/site-tp;
    index index.html;
}

server {
    listen 80;
    server_name localhost;
    return 301 https://$host$request_uri;
}
```

Je vérifie et je redémarre le service 

```
sudo nginx -t
sudo systemctl restart nginx
```

Puis j'essaye de voir si ma machine cliente télécharge la page hébergée via Nginx avec 
`curl -k https://<IP_VM>`

![Screenshot 2026-02-19 at 10.56.33](https://hackmd.io/_uploads/Hy3LRI4_bl.png)

Enfin, pour la sécurité, je mets en place un pare-feu.

```
sudo ufw allow 'Nginx Full'
sudo ufw status
```

`sudo chown -R www-data:www-data /var/www/site-tp4 et chmod -R 755 /var/www/site-tp4`

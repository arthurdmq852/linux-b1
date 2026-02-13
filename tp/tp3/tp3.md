# B1 Linux - TP3

## I. Exploration en solo
### 1. Base64

Pour  commencer, j'ai créer le fichier nommé `file_bin` contenant `dd if=/dev/urandom of=file_bin bs=1k count=50`

Puis je l'ai ensuite encodé avec **Base64** 


`openssl base64 -e -in file_bin -out file_bin_b64`

Pour vérifier si le fichier était bien encodé en base64 j'ai fait `cat file_bin_b64`

<img width="606" height="63" alt="Image" src="https://github.com/arthurdmq852/linux-b1/blob/main/tp/tp3/pictures/1.png?raw=true" />
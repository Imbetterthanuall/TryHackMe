# [Smag Grotto](https://tryhackme.com/room/smaggrotto) (EASY)
## Reconnaissance
```bash
nmap -sV sC -p- -T4 IP
```
* `-sV` énumère les versions des logiciels
* `-sC` utilise les scripts par defauts pour par exemple détécter une session anonymous en ftp disponible
* `-p-` scan tout les ports
* `-T4` augmentes la vitesse du scan
* `IP` IP de la cible

Une fois le scan fini, nous constatons que le port 22 (**SSH**) est ouvert donc qu'une fois les idendifiant obtenus nous pouvons nous connecter a la machine en ssh, le port 80 (**HTTP**) est aussi ouvert donc la cible héberge un site web accessible en faisant http://IP_CIBLE/

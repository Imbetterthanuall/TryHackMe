# [Smag Grotto](https://tryhackme.com/room/smaggrotto) (EASY)
## Nmap
```bash
nmap -sV sC -p- -T4 IP
```
* `-sV` énumère les versions des logiciels
* `-sC` utilise les scripts par defauts pour par exemple détécter une session anonymous en ftp disponible
* `-p-` scan tout les ports
* `-T4` augmentes la vitesse du scan
* `IP` IP de la cible

Une fois le scan fini, nous constatons que le port 22 (**SSH**) est ouvert donc qu'une fois les idendifiant obtenus nous pouvons nous connecter a la machine en ssh, le port 80 (**HTTP**) est aussi ouvert donc la cible héberge un site web accessible en faisant http://IP_CIBLE/  
Le <Title> de la page HTML est Smag, puisque nous somme sur TryHackMe, la fin est souvent .thm donc on peut modifier notre fichier /etc/hosts pour ajouter a la fin une ligne IP Smag.thm et retourner sur le site en utilisant son nom de domain.  

## Gobuster
```bash
gobuster dir -u http://smag.thm/ -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -t 64
```
On découvre /mail et il y a un fichier a analyser avec wireshark pour y avoir un username et password
le fichier wireshark montre une url entier d'ou est-ce que les identifiants ont été utilisé http://development.smag.thm/login.php
Une fois connecter nous avons accès a un terminal web, ideal pour faire un reverse shell ou bind shell ( j'ai utiliser le bind shell )
Une fois connecter a la machine via mon terminal je le stabilise avec python3 


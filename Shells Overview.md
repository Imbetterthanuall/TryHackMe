# [Shells Overview](https://tryhackme.com/room/shellsoverview)
## C'est quoi un shell ?

Un **shell** est un programme qui permettra à un utilisateur d'interagir avec l'OS (bash/zsh/fish...)
### Utilité
En hacking, un attaquant peut executer un **remote access control** pour executer des commande sur une machine à distance.  
Via un shell, on peut faire : 
* de l'**escalade de privilège** (passer de simple utilisateur à **root/admin**)
* de l'**exfiltration de donnée** (Volée des hash de mot de passe, code source, mot de passe ou tout type de donnée sensibles)
* de la **persistance** (**Backdoor** ou crée une autre session avec des droits root, _c'est utile pour revenir sur la machine même si la machine à supprimer notre moyen d'entrée initial ou a changer son mdp_) 
* du **pivoting** ce servir d'une permière machine compromises et s'en servir de **pivot** pour s'étendre sur le réseau interne de la cible (On hack un frigo connecter et finalement nous avons accès à la caméra du jardin )

## C'est quoi un reverse shell ?
Un reverse shell est une technique qui permet à un attaquant d'avoir accès au shell d'une machine à distance.  
C'est une technique très puissante car elle fait en sorte que la machine se connecte elle même à l'attaquant et non l'inverse ce qui permet de contourner beaucoup de sécurité qui auraient pu être mis en place.
### Comment obtenir un reverse shell
Le plus simple est d'utiiliser l'outil **_netcat(nc)_** et de se mettre en **mode écoute** en attendant une connexion car ne l'oublions pas, c'est la cible qui se connecte à nôtre machine de manière **volontaire** ou plus souvent de manière **involontaire**.   
  
1. Netcat en attendant une connexion (*Côté attaquant*) :
```bash
nc -lnvp [PORT]
```
* `nc` utilise l'outil netcat qui permet de se connecter à des services en **TCP/UDP** et aussi d'ouvrir des ports d'écoutes **TCP/UDP**
* `-l` (*listen*) met la commande en **mode écoute** et *non en mode connexion*, nous attendons une connexion
* `n` (*no resolve dns*) ne va pas tenter de résoudre le DNS de la connexion pour éviter des soucis et gagner du temps
* `-v` (*verbose*) active le **mode verbose** et donc affichera d'avantage d'informations
* `-p [PORT]` (*port*) spécifie le port d'écoute que nous ouvrons en attendant une connexion. *Il est préférable d'utilisé des ports connus comme 80,443,22,21,23 pour que du point de vu du firewall cible, cela ressemble à une connexion lambda à un service connu. Cependant il ne faut pas oublier qu'ouvrir des ports inférieur à 1024 demande des droits root*

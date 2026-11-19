# [Networking Essentials](https://tryhackme.com/room/networkingessentials)
## DHCP (*Dynamic Host Configuration Protocol*)
### Description
Le **DHCP** est un protocole très utilisé dans le monde car il permet d'attribué une IP local à un appareil dès qu'il se connecte à un réseau automatiquement.  
Sans **DHCP**, il aurait fallu configuré nôtre IP manuellement et cela aurait été très contraignant, difficile pour la plupart des personnes et aussi il y aurait eu des risques de conflit d'IP ( 2 personnes qui ont choisi la même IP ).  
Les **Ordinateur**, **Téléphone**, ou tout simplement les **IoT** en général sont configurée pour supporter et utiliser le **DHCP**.  
### Fonctionnement
Le **DHCP** suit 4 étapes : *Discover, Offer, Request, et Acknowledge* (**DORA**):
1. **DHCP Discover**: Le client *crient dans tout le réseau*(broadcasts) un message  **DHCPDISCOVER** pour *rechercher un server DHCP* s'il y en a un.
2. **DHCP Offer**: Le serveur répond avec un message **DHCPOFFER** contenant une *offre d'une addresse IP disponible* pour le client.
3. **DHCP Request**: Le client répond avec un message **DHCPREQUEST** pour faire une *requête demandant l'addresse IP offerte* par le DHCP.
4. **DHCP Acknowledge**: Le serveur répond avec un message **DHCPACK** pour confirmé que l'IP offerte à bien été assigné au client.  

    *On peut noter que puisque le client n'a pas d'IP tout le long, il communique seulement à l'aide de son addresse MAC et les packets viennent de l'IP **0.0.0.0** vers l'addresse de broadcast **255.255.255.255***  
   Une fois fini, l'appareil reçoit l'addresse du **gateway**(souvent le routeur) du réseau pour envoyé les packets en dehors du réseaux local, l'addresse du serveur **DNS** et la plage du réseau.

## ARP (*Address Resolution Protocol*)
### Description
**ARP** est un protole aussi très utilisé dans un réseau local car à l'intérieur d'un réseau local, les appareils communiques grâce à leurs **addresse MAC** et non pas leurs **addresse IP (privé)**.
Un paquet ARP sert à avoir l'addresse MAC d'un appareil en connaissant seulement sont addresse IP privé, il est indispensable pour le **switch** par exemple.
### Fonctionnement 
Pour l'exemple, l'appareil avec l'IP **192.168.1._66_** veut communiquer avec l'appareil **192.168.1._243_**  
**ARP** suit 2 étapes : *ARP Request, ARP Reply*
1. **ARP Request** : **192.168.1._66_** va envoyé un paquet **ARP Request** demandant qui est l'hôte de **192.168.1._243_**.  
   Le paquet ARP Request est envoyé a partir de son MAC ( car le message de réponse doit lui être renvoyer ) vers l'addresse de **broadcast MAC**(**ff:ff:ff:ff:ff:ff**) pour crier à tout les MAC/appareils du réseau.
2. **ARP Reply** : L'appareil avec l'IP **192.168.1._243_** va donc répondre à la demande, il va utilisé le MAC de **192.168.1._66_** qui se trouvait dans le **ARP Request** pour lui répondre seulement à lui en y donnant son addresse MAC personnel

## ICMP (*Internet Control Message Protocol*)
### Description
**ICMP** est un protocol utilisé pour faire des diagnostique réseau et/ou des rapport d'erreurs. Deux commandes sont très connus car ils utilisent le protocol ICMP à des fins précises : **ping** et **traceroute**  
### Ping 
La commande `ping` sert à envoyer une requête ICMP (**ICMP Echo Request** *ICMP Type8*) vers un.e *Nom de domaine/IP* dans l'obtique d'obtenir un *pong* (**ICMP Echo Reply** *ICMP Type0*).  
Lorsque nous recevons le *pong* de la machine distante, nous avons le nombre de miliseconde(**ms**) que la rêquete ping/pong à pris pour et les pertes de paquets. Ces informations servent à nous montrer si on peut communiqué avec une machine ( *ou internet tout simplement* ) et nous montres aussi la rapidité d'échange ( le ping ).  
Il y a aussi le nombre de bytes envoyé qui peuvent nous dire si c'est une machine **Linux ( par défaut 56(84) bytes )** ou **Windows ( par défaut 32 bytes )**  
#### Syntaxe
```bash
ping 8.8.8.8 -c 4
```
 * `ping 8.8.8.8` va envoyé un ping à l'IP **8.8.8.8**(*Le serveur DNS de Google*)
     *En général on sait que 8.8.8.8 est actif, donc si on essaie d'y envoyé un ping c'est pour savoir si nous y avons accès et donc si nous somme connecté à internet*
* `-c 4` (*-c = count*) Paramètre de la commande pour qu'elle s'arrête après avoir envoyé 4 paquet ICMP  

### Traceroute
`traceroute` est une commande qui permet de tracer la route qu'un paquet ICMP va faire pour atteindre un.e *Nom de domaine/IP*.  
Cela permet d'analyser les IP des routeurs utilisé lors du routage 
Par exemple lorsqu'un paquet à un **TTL**(*Time-To-Live*), il peut être défini à 12 ce qui veut dire qu'après avoir passer 12 routeurs, le 12e routeurs retournera une erreur d'excès de "temps"(*ICMP Time Exceeded message*).  
**Remarque** : Time-To-Live désigne bien le nombre de routage accepter et non pas un temps en seconde ou autres unité de temps.
#### Syntaxe
```bash
traceroute tryhackme.com
```
## NAT (*Network Address Translation*)
#### Contexte
Pour comprendre l'utilité du **NAT**, il faut d'abord revoir le problème que nous avons avec les IP ( notamment IPv4 puisque IPv6 règle ce problème mais n'est pas assez utilisé ).  
Dans le monde nous avons des IP disponible de **0.0.0.0** à **254.255.255.255** ( Sans prendre en comptes les IP réserver ), ce qui nous donne 4 bloc de 8 octet (256 ** 4) ce qui donne approximativement 4Md d'address possible.  
Cependant avec l'évolution d'internet, le nombre d'utilisateur qui a augmenter, les caméras qui doivent théoriquement aussi avoir leurs IP public pour être accessible, nous depassons facilement les 4Md d'appareils connecté simultanément.  
Donc il a fallu trouver un moyen pour que une seul IP Public puisse servir à plusieurs appareils simultanément
#### NAT, la solution à ça
Le NAT est un protocole utilisé par les routeurs, il permet de faire en sorte que seule l'IP du routeur fera les requête sur internet et jamais les IP privé des appareils.  
Pour ce faire, les appareils vont ouvrir un port d'écoute pour recevoir la réponse une fois la requête de retour sur le réseau et le routeur aura une table avec par exemple ( **196.168.1.24:5478** ), ensuite le routeur ouvrira un port réserver pour recevoir la réponse du site, une fois que le site aura remis la requête au routeur, le routeur va envoyé la réponse à l'IP privé à l'origine de la requête.  
Puisque un appareils peut ouvrir au maximum **65 535 ports**, un routeurs peut connecté au maximum 65 535 appareils en simultané à internet

![Image illustrative][Image NAT]  

[Image NAT]:https://cdn-images.tryhackme.com/user-uploads/5f04259cf9bf5b57aed2c476/room-content/5f04259cf9bf5b57aed2c476-1719849362861.svg

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

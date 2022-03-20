
# SSRF ou "Server-Side Request Forgery"
- Vulnérabilité qui permet à un utilisateur malveillant d'amener le serveur Web à envoyer une requête HTTP supplémentaire ou modifiée les ressources choisies par l'attaquant.

 Deux types existent : 
 - 1 : SSRF normal où les données sont renvoyées à l'écran de l'attaquant. 
 - 2 : La seconde est une vulnérabilité Blind SSRF où une SSRF se produit, mais aucune information n'est renvoyée à l'écran de l'attaquant.

 Une attaque SSRF réussie peut entraîner l'un des éléments suivants :**
 - Accès à des zones non autorisées. Accès aux données client/organisation. 
 - Capacité à s'adapter aux réseaux internes. 
 - Révéler les jetons/identifiants d'authentification.

Payload(s): 
``?x=`` : Ignore le reste de l'URL.

**Exemple :**
![[second_exemple_ssrf.png]]

#### Détecter cette failles :
- Lorsqu'une URL complète est utilisée dans un paramètre de la barre d'adresse :
	`https://website.thm/item/2?server=**https://server.website.thm/store**`

- Dans le code source d'un formulaire :

![[RFI_Formulaire.png]]
- Si le début d'un sous domaine apparait dans une url : 
    `https://website.thm/item/2?server={api}`

- Ou peut-être uniquement le chemin de l'URL :
    `https://website.thm/item/2?server={/forms/contact}`
     
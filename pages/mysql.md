---
layout: default
---

### MySQL Memento

#### [Retour](../index.md)


### Connexion au service :
`````bash
mysql -u [username] -p;
`````

### Lister les BDD :
````bash
show databases;
````

### Sélectionner une BDD pour la manipuler :
````bash
use "nom_bdd";
````

### Afficher toutes les tables d'une BDD :
````bash
show tables;
````

### Lister les index d'une table :
````bash
show index from [table];
````
### Téléchargement d'une BDD avec mysqldump (Output dans un fichier.sql)
````bash
mysqldump -u [username] -p [database] > data_backup.sql;
````
### Sources : 
- [hofmannsven github](https://gist.github.com/hofmannsven/9164408)
- [MySQL Cheat Sheet](https://www.mysqltutorial.org/mysql-cheat-sheet.aspx)
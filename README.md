# hss
```hss sert à gérer plusieurs connexions SSH vers des serveurs différents, avec des noms d’utilisateurs différents, des url différentes, des ports de connexion différent.
Le tout est stocké dans une base de données sqlite3
on peut ajouter, supprimer, renommer, lister les données
la connexion à un serveur SSH se fait par un hss shortcut. Shortcut étant le nom du raccourci qu’on a donné à la création de la donnée. On peut renommer le shortcut.
Le shortcut est le point d’entrée de la donnée, pour y accéder.

Usage :
  hss -l -> donne un listing de toutes les données en base, sous forme d’un tableau
  hss -d shortcut -> supprime la donnée de la base
  hss -a shortcut:user@url[/path]:port -> crée la donnée en base. Path est optionnel et permet de se connecter en ssh sur un dossier en particulier
  hss -r shortcut champs=valeur -> dans la donnée pointée par shortcut, on modifie le champ qui peut être shortcut,user,url ou port par une autre valeur
```

# HSS : gestionnaire de connexions SSH

**HSS** est un script **Python3** pour gérer des listes de connexions **ssh**. Souvent on a à gérer de nombreuses connexions **ssh** avec des url, des utilisateurs et des ports différents. Pour faciliter la vie, voici un script qui va conserver toutes ces données dans un fichier qui est en réalité une base de données **sqlite3**. Dans cette base il n’y aura pas de mots de passe stockés. Quand on utilise **ssh** de façon intensive, on préfère l’utilisation des clés partagées.
## installation Linux
On copie le fichier hss dans le dossier /usr/local/bin et on vérifie qu’il est bien exécutable
## installation Windows (sous Git Bash)
il faut au préalable installer **Python3** sans oublier de cocher la case pour l’inclure duns le PATH.
Puis sous Git Bash, on fait un

    echo $PATH

et on repère un dossier utilisateur accessible. On y copie hss, puis on fait un

    type python
ce qui renvoie le chemin complet de l’exécutable python, on copie le chemin complet du python.exe (avec le python.exe) et on édite le fichier hss et on remplace la première ligne

    #!/usr/bin/python3
par

    #!cheminCompletDeLExecutablePython
et ainsi on pourra lancer **hss**
## utilisation
Au premier lancement, avec une option quelconque, hss va créer la base de données dans le dossier utilisateur *~/.config/hss/*
les données sont associées à un raccourci qu’on appellera *shortcut* ici.
### insertion de données

    hss --add ratest user1@nomdedomaine.tld:22
ici on ajoute dans la base, sous le nom *ratest*, d’une connexion ssh avec l’utilisateur *user1*, vers le domaine *nomdedomaine.tld* en utilisant le port 22.
si on a besoin d’un dossier de destination autre que le dossier par défaut de l’utilisateur, on peut faire

    hss --add ratest user1@nomdedomaine.tld/home/dossierdesonchoix/sous-dossier:22
ce qui fera automatiquement une connexion ssh avec changement du dossier à la connexion
on peut ajouter une url avec une **ipV6**, dans ce cas il faut mettre des crochets autour de l’adresse **ipV6**

    hss --add test6 user1@\[2001:DB8:1200::17\]/home/lxc/vpn:8765
ici on a ajouté une **ipV6** et en ligne de commande, on doit mettre des \ antislash devant les crochets pour éviter qu’ils soient analysé par l’interpréteur de commandes. Après cette insertion, l’utilisation restera la même que pour une url litérale.
Il y a une particularité dans le cas des adresse **ipV6**, dans le cas des adresses locales, qui commencent par **FE80**, il faut spécifier la carte réseau par où les trames vont passer. Si en ligne de commande on fait un ping vers l’adresse locale du serveur

    ping fe80::4ecd
ça va fonctionner, mais si on tente une connexion ssh, ça ne fonctionnera pas.
Dans mon cas la carte réseau étant **eth0**, la connexion ssh va être

    ssh user@fe80::4ecd%eth0 -p port
donc on va insérer comme entrée

    hss --add serloc user@\[fe80::4ecd%eth0\]/var/www:258
### lister
On peut lister la base en faisant

    hss --list
    ------------------------------------------------------------
    |shortcut|user |url                                   |port|
    ------------------------------------------------------------
    |ratest  |user1|nomdedomaine.tld                      |  22|
    |serloc  |user |[fe80::4ecd%eth0]/var/www             | 258|
    |test6   |user1|[2001:DB8:1200::17]/home/lxc/vpn      |8765|
    ------------------------------------------------------------
Quand on a beaucoup de lignes, on peut utiliser un pattern de sélection

    hss --list pattern
ce **pattern** peut utiliser les wildcard suivants : * pour un groupe de caractères, et **?** pour un caractère. Comme par exemple

    hss --list s?ef*
qui va afficher toutes les données dont la première du *shortcut* est **s** et dont les 3ème et 4ème lettres sont **ef**

## renommage
il se peut que pour une raison quelconque, on ait besoin de renommer quelque chose dans une entrée. On peut soit supprimer l’entrée et la recréer, soit renommer la partie qu’on aimerait changer.
Chaque entrée est consituée ainsi : **shortcut**, **user**, **url**, **port** et c’est avec ces noms là qu’on va sélectionner ce qu’on désire changer

    hss --rename ratest shortcut=test

Ici, on vient de renommer le shortcut ratest en test

    hss --rename test port=1234

Pour l’entrée test, on a changé le port 22 en port 1234
### suppression
La suppression d’une entrée se fait par le shortcut, ici **test**

    hss --delete test

l’entrée **test** a été supprimée
## exécution
Pour lancer un ssh vers test

    hss test

ça va afficher la commande qui va être exécutée, puis la lancer réellement
vous pourrez constater que la commande diffère si on a un dossier de destination dans l’url
## scp
Souvent, quand on fait régulièrement du ssh, on utilise aussi la commande **scp**, dont la syntaxe est un peu différente de ssh. hss va donc afficher la commande scp correctement assemblée, prête à être modifiée selon les besoins

    hss --scp test

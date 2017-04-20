Azure déterminera que votre application utilise Python **si les deux conditions suivantes sont remplies**:

- fichier Requirements.txt dans le dossier racine
- n’importe quel fichier .py dans le dossier racine ou un runtime.txt qui spécifie python

Si c’est le cas, il utilisera un script de déploiement spécifiques Python qui effectue la synchronisation standard de fichiers, ainsi que d’autres opérations Python telles que :

- Gestion automatique des environnement virtuel
- Installation de packages répertoriés dans requirements.txt à l’aide de pip
- Création de web.config appropriés en fonction de la version Python sélectionnée.
- Collecter les fichiers statiques pour les applications Django

Vous pouvez contrôler certains aspects de la procédure de déploiement par défaut sans avoir à personnaliser le script.

Si vous souhaitez ignorer toutes les étapes de déploiement spécifiques Python, vous pouvez créer ce fichier vide :

    \.skipPythonDeployment

Si vous souhaitez ignorer collection des fichiers statiques pour votre application Django :

    \.skipDjango 

Pour mieux contrôler le déploiement, vous pouvez remplacer le script de déploiement par défaut en créant les fichiers suivants :

    \.deployment
    \deploy.cmd

Vous pouvez utiliser l' [interface de ligne de commande Azure][] pour créer les fichiers.  Utilisez cette commande à partir de votre dossier de projet :

    azure site deploymentscript --python

Lorsque ces fichiers n’existent pas, Azure crée un script de déploiement temporaire, exécute.  Il est identique à celui que vous créez avec la commande ci-dessus.

[Interface de ligne de commande Azure]: http://azure.microsoft.com/downloads/

Certains packages ne peuvent pas être installé à l’aide de pip sur Azure.  Il peut être simplement que le package n’est pas disponible dans l’Index de Package Python.  Il peut s’agir qu’un compilateur est requis (un compilateur n’est pas disponible sur l’ordinateur qui exécute l’application web dans le Service d’application Azure).

Dans cette section, nous allons aborder manières de traiter ce problème.

### <a name="request-wheels"></a>Demande de roues

Si l’installation du package requiert un compilateur, vous devez essayer de contacter le propriétaire du lot pour faire en sorte que roues rendus disponibles pour le package.

Avec la disponibilité du [Compilateur Microsoft Visual C++ pour Python 2.7][]récente, il est plus facile de créer des packages qui disposent de code natif pour Python 2.7.

### <a name="build-wheels-requires-windows"></a>Créer des roues (nécessite Windows)

Remarque : Lorsque vous utilisez cette option, veillez à compiler le package à l’aide d’un environnement Python qui correspond à la plateforme/architecture/version utilisée dans l’application web dans le Service d’application Azure (Windows/32-bit/2.7 ou 3.4).

Si le package ne s’installe, car elle requiert un compilateur, vous pouvez installer le compilateur sur votre ordinateur local et créer une roue du package, puis inclure dans votre référentiel.

Les utilisateurs de Mac/Linux : Si vous n’avez pas accès à un ordinateur Windows, voir [créer un Windows en cours d’exécution Machine virtuelle][] pour savoir comment créer une machine virtuelle sur Azure.  Vous pouvez l’utiliser pour générer les roues, ajoutez-les au référentiel et ignorer la machine virtuelle si vous le souhaitez. 

Pour Python 2.7, vous pouvez installer [Microsoft Visual C++ compilateur pour Python 2.7][].

Pour Python 3.4, vous pouvez installer [Microsoft Visual C++ 2010 Express][].

Pour générer roues, vous devez le package roue :

    env\scripts\pip install wheel

Vous allez utiliser `pip wheel` afin de compiler une dépendance :

    env\scripts\pip wheel azure==0.8.4

Cela crée un fichier .whl dans le dossier \wheelhouse.  Ajouter les fichiers de dossier et de la roulette \wheelhouse dans votre référentiel.

Modifier votre requirements.txt pour ajouter la `--find-links` option dans la partie supérieure. Cette option indique à pip pour rechercher une correspondance exacte dans le dossier local avant de passer à l’index de package python.

    --find-links wheelhouse
    azure==0.8.4

Si vous voulez inclure toutes vos dépendances dans le dossier \wheelhouse et ne pas utiliser l’index de package python tout, vous pouvez forcer pip pour ignorer l’index de package en ajoutant `--no-index` en haut de votre requirements.txt.

    --no-index

### <a name="customize-installation"></a>Personnaliser l’installation

Vous pouvez personnaliser le script de déploiement pour installer un package dans l’environnement virtuel en utilisant un autre programme d’installation, telles que facile\_installer.  Voir deploy.cmd pour obtenir un exemple commenter.  Assurez-vous que ces packages ne sont pas répertoriés dans requirements.txt, pour empêcher les pip de les installer.

Ajouter le script de déploiement :

    env\scripts\easy_install somepackage

Vous pourrez également utiliser facile\_installer à partir d’un programme d’installation exe (certains sont zip compatible, si facile\_installer prend en charge les).  Ajouter le programme d’installation pour votre référentiel et appeler facile\_installer en passant le chemin d’accès au fichier exécutable.

Ajouter le script de déploiement :

    env\scripts\easy_install "%DEPLOYMENT_SOURCE%\installers\somepackage.exe"

### <a name="include-the-virtual-environment-in-the-repository-requires-windows"></a>Inclure l’environnement virtuel dans le référentiel (nécessite Windows)

Remarque : Lorsque vous utilisez cette option, vérifiez que vous utilisez un environnement virtuel qui correspond à la plateforme/architecture/version utilisée dans l’application web dans le Service d’application Azure (Windows/32-bit/2.7 ou 3.4).

Si vous incluez l’environnement virtuel dans le référentiel, vous pouvez empêcher le script de déploiement de procéder de la gestion de l’environnement virtuel sur Azure en créant un fichier vide :

    .skipPythonDeployment

Nous vous recommandons de supprimer l’environnement virtuel existant sur l’application, pour empêcher les fichiers résultant de lorsque l’environnement virtuel a été géré automatiquement.


[Créer une Machine virtuelle exécutant Windows]: http://azure.microsoft.com/documentation/articles/virtual-machines-windows-hero-tutorial/
[Microsoft Visual C++ compilateur pour Python 2.7]: http://aka.ms/vcpython27
[Microsoft Visual C++ 2010 Express]: http://go.microsoft.com/?linkid=9709949

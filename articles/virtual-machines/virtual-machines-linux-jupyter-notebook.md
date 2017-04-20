<properties
    pageTitle="Créer un bloc-notes Jupyter/IPython | Microsoft Azure"
    description="Découvrez comment déployer le bloc-notes Jupyter/IPython sur une machine virtuelle Linux créée avec le modèle de déploiement de gestionnaire de ressources dans Azure."
    services="virtual-machines-linux"
    documentationCenter="python"
    authors="crwilcox"
    manager="wpickett"
    editor=""
    tags="azure-service-management,azure-resource-manager"/>

<tags
    ms.service="virtual-machines-linux"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-linux"
    ms.devlang="python"
    ms.topic="article"
    ms.date="11/10/2015"
    ms.author="crwilcox"/>

# <a name="jupyter-notebook-on-azure"></a>Bloc-notes Jupyter sur Azure

Le [projet Jupyter](http://jupyter.org), anciennement le [projet IPython](http://ipython.org), fournit un ensemble d’outils pour informatique scientifique à l’aide des puissantes shell interactif combinant l’exécution de code avec la création d’un document de calcul actives. Ces fichiers de bloc-notes peuvent contenir du texte arbitraire, formules mathématiques, code d’entrée, résultats, graphiques, vidéos et tout autre type de média qu’un navigateur web moderne est capable d’afficher. Si vous débutez absolument Python et voulez découvrir dans un environnement agréable et interactif ou certains informatique parallèle/technique sérieux, dans le bloc-notes Jupyter s’avère très intéressante.

![Capture d’écran](./media/virtual-machines-linux-jupyter-notebook/ipy-notebook-spectral.png) packages à l’aide de SciPy et Matplotlib pour analyser la structure d’un enregistrement audio.


## <a name="jupyter-two-ways-azure-notebooks-or-custom-deployment"></a>Jupyter deux façons : Blocs-notes Azure ou déploiement personnalisé
Azure fournit un service que vous pouvez utiliser pour [commencer rapidement à l’aide de Jupyter ](http://blogs.technet.com/b/machinelearning/archive/2015/07/24/introducing-jupyter-notebooks-in-azure-ml-studio.aspx).  En utilisant le Service de bloc-notes Azure, vous pouvez accéder facilement à interface web accessible de Jupyter à des ressources informatiques scalable avec toute la puissance de Python et ses nombreuses bibliothèques.  Dans la mesure où l’installation est gérée par le service, les utilisateurs peuvent accéder à ces ressources sans qu’il soit nécessaire pour l’administration et de configuration par l’utilisateur.

Si le service de bloc-notes ne fonctionne pas pour votre scénario de continuer à lire cet article qui vous seront montrent comment déployer le bloc-notes Jupyter sur Microsoft Azure, à l’aide de machines virtuelles Linux (machines virtuelles).

[AZURE.INCLUDE [create-account-and-vms-note](../../includes/create-account-and-vms-note.md)]

## <a name="create-and-configure-a-vm-on-azure"></a>Créer et configurer une machine virtuelle sur Azure

La première étape consiste à créer une machine virtuelle (machine virtuelle) s’exécutant sur Azure.
Cet ordinateur virtuel est un système d’exploitation complet dans le cloud et sera utilisé pour exécuter le bloc-notes Jupyter. Azure est capable d’exécuter des machines virtuelles Linux et Windows, puis aborde le programme d’installation de Jupyter sur les deux types de machines virtuelles.

### <a name="create-a-linux-vm-and-open-a-port-for-jupyter"></a>Créer une VM Linux et ouvrir un port pour Jupyter

Suivez les instructions donné [ici] [ portal-vm-linux] pour créer une machine virtuelle de la distribution *Ubuntu* . Ce didacticiel utilise Ubuntu Server 14.04 LTS. Nous allons supposer que les de nom d’utilisateur *azureuser*.

Une fois que la machine virtuelle déploie nous avons besoin ouvrir une règle de sécurité dans le groupe de sécurité réseau.  À partir du portail Azure, accédez aux **Groupes de sécurité réseau** et ouvrez l’onglet des groupes de sécurité correspondant à votre ordinateur virtuel. Vous devez ajouter une règle de sécurité entrants avec les paramètres suivants : **TCP** pour le protocole, **\*** pour le port source (public) et **9999** pour le port de destination (privé).

![Capture d’écran](./media/virtual-machines-linux-jupyter-notebook/azure-add-endpoint.png)

En cours de votre groupe de sécurité réseau, cliquez sur les **Interfaces réseau** et notez l' **Adresse IP publique** comme il est nécessaire pour se connecter à votre ordinateur virtuel dans l’étape suivante.

## <a name="install-required-software-on-the-vm"></a>Installer des logiciels requis sur la machine virtuelle

Pour exécuter le bloc-notes Jupyter sur notre machine virtuelle, nous devons tout d’abord installer Jupyter et ses dépendances. Se connecter à votre linux machine virtuelle à l’aide de ssh et associer le nom d’utilisateur/mot de passe que vous avez choisi lors de la création de la machine virtuelle. Dans ce didacticiel, nous utiliser PuTTY et vous connecter à partir de Windows.

### <a name="installing-jupyter-on-ubuntu"></a>L’installation de Jupyter sur Ubuntu
Installez Anaconda, une distribution python scientifique des données les plus consultés, en utilisant l’un des liens fournis par [Analytique continue](https://www.continuum.io/downloads).  Au moment de la rédaction de ce document, les liens ci-dessous sont les plus jusqu'à versions date.

#### <a name="anaconda-installs-for-linux"></a>Installations anaconda pour Linux
<table>
  <th>3.4 Python</th>
  <th>Python 2.7</th>
  <tr>
    <td>
        <a href='https://3230d63b5fc54e62148e-c95ac804525aac4b6dba79b00b39d1d3.ssl.cf1.rackcdn.com/Anaconda3-2.3.0-Linux-x86_64.sh'>64 bits</href>
    </td>
    <td>
        <a href='https://3230d63b5fc54e62148e-c95ac804525aac4b6dba79b00b39d1d3.ssl.cf1.rackcdn.com/Anaconda-2.3.0-Linux-x86_64.sh'>64 bits</href>
    </td>
  </tr>
  <tr>
    <td>
        <a href='https://3230d63b5fc54e62148e-c95ac804525aac4b6dba79b00b39d1d3.ssl.cf1.rackcdn.com/Anaconda3-2.3.0-Linux-x86.sh'>32 bits</href>
    </td>
    <td>
        <a href='https://3230d63b5fc54e62148e-c95ac804525aac4b6dba79b00b39d1d3.ssl.cf1.rackcdn.com/Anaconda-2.3.0-Linux-x86.sh'>32 bits</href>
    </td>  
  </tr>
</table>


#### <a name="installing-anaconda3-230-64-bit-on-ubuntu"></a>L’installation d’Anaconda3 2.3.0 64 bits sur Ubuntu
Par exemple, voici comment vous pouvez installer Anaconda sur Ubuntu

    # install anaconda
    cd ~
    mkdir -p anaconda
    cd anaconda/
    curl -O https://3230d63b5fc54e62148e-c95ac804525aac4b6dba79b00b39d1d3.ssl.cf1.rackcdn.com/Anaconda3-2.3.0-Linux-x86_64.sh
    sudo bash Anaconda3-2.3.0-Linux-x86_64.sh -b -f -p /anaconda3

    # clean up home directory
    cd ..
    rm -rf anaconda/

    # Update Jupyter to the latest install and generate its config file
    sudo /anaconda3/bin/conda install jupyter -y
    /anaconda3/bin/jupyter-notebook --generate-config


![Capture d’écran](./media/virtual-machines-linux-jupyter-notebook/anaconda-install.png)


### <a name="configuring-jupyter-and-using-ssl"></a>Configuration Jupyter et l’utilisation de SSL
Après l’installation, nous devons prendre quelques instants pour les configuration des fichiers d’installation pour Jupyter. Si vous rencontrez des problèmes de configuration Jupyter il peut être utile d’examiner la [Documentation Jupyter pour l’exécution d’un serveur de bloc-notes](http://jupyter-notebook.readthedocs.org/en/latest/public_server.html).

Suivante nous `cd` à l’annuaire de profil pour créer notre certificat SSL et modifier le fichier de configuration de profils.

Sous Linux, utilisez la commande suivante.

    cd ~/.jupyter

Utilisez la commande suivante pour créer le certificat SSL (Linux et Windows).

    openssl req -x509 -nodes -days 365 -newkey rsa:1024 -keyout mycert.pem -out mycert.pem

Notez que, étant donné que nous allons créer un certificat SSL auto-signé, lors de la connexion au bloc-notes votre navigateur vous donne un avertissement de sécurité.  Pour une utilisation de production à long terme, vous souhaiterez utiliser un certificat correctement connecté associé à votre organisation.  Gestion des certificats étant dépasse le cadre de cette démonstration, nous se bloquer à un certificat auto-signé pour l’instant.

En plus de l’aide d’un certificat, vous devez également fournir un mot de passe pour protéger votre bloc-notes à partir d’une utilisation non autorisée.  Pour des raisons de sécurité Jupyter utilise les mots de passe chiffrés dans son fichier de configuration, il vous faudra donc chiffrer tout d’abord votre mot de passe.  IPython fournit un utilitaire pour ce faire ; à l’invite, exécutez la commande suivante.

    /anaconda3/bin/python -c "import IPython;print(IPython.lib.passwd())"

Cela vous invite à entrer un mot de passe et confirmation et imprime ensuite le mot de passe. Remarque Cette pour l’étape suivante.

    Enter password:
    Verify password:
    sha1:b86e933199ad:a02e9592e59723da722.. (elided the rest for security)

Ensuite, nous allez modifier le fichier de configuration du profil, ce qui correspond à la `jupyter_notebook_config.py` fichier dans le répertoire.  Notez que ce fichier n’existe pas--créez simplement si c’est le cas.  Ce fichier comporte un nombre de champs et par défaut toutes sont commentés.  Vous pouvez ouvrir ce fichier avec un éditeur de texte de votre convenance, et vous devez vous assurer qu’elle comporte au moins les informations suivantes. **N’oubliez pas de remplacer les c.NotebookApp.password dans le fichier config avec la sha1 à partir de l’étape précédente**.

    c = get_config()

    # You must give the path to the certificate file.
    c.NotebookApp.certfile = u'/home/azureuser/.jupyter/mycert.pem'

    # Create your own password as indicated above
    c.NotebookApp.password = u'sha1:b86e933199ad:a02e9592e5 etc... '

    # Network and browser details. We use a fixed port (9999) so it matches
    # our Azure setup, where we've allowed traffic on that port
    c.NotebookApp.ip = '*'
    c.NotebookApp.port = 9999
    c.NotebookApp.open_browser = False

### <a name="run-the-jupyter-notebook"></a>Exécuter le bloc-notes Jupyter

À ce stade, nous sommes prêts à démarrer le bloc-notes Jupyter. Pour ce faire, accédez au répertoire que vous souhaitez stocker des blocs-notes dans et démarrer le serveur Jupyter bloc-notes avec la commande suivante.

    /anaconda3/bin/jupyter-notebook

Vous devriez maintenant pouvoir accéder à votre bloc-notes Jupyter à l’adresse `https://[PUBLIC-IP-ADDRESS]:9999`.

Lorsque vous accédez tout d’abord votre bloc-notes, la page de connexion vous invite à fournir votre mot de passe. Et une fois que vous ouvrez une session, vous verrez le « Jupyter bloc-notes tableau de bord », qui est le centre de contrôle pour toutes les opérations de bloc-notes.  À partir de cette page, vous pouvez créer des blocs-notes et ouvrir des fichiers existants.

![Capture d’écran](./media/virtual-machines-linux-jupyter-notebook/jupyter-tree-view.png)

### <a name="using-the-jupyter-notebook"></a>À l’aide de l’ordinateur portable Jupyter

Si vous cliquez sur le bouton **Nouveau** , vous verrez l’ouverture de la page suivante.

![Capture d’écran](./media/virtual-machines-linux-jupyter-notebook/jupyter-untitled-notebook.png)

La zone marquée avec une `In []:` invite est la zone d’entrée et vous pouvez entrer ici de code Python valide et il s’exécutera lorsque vous atteignez `Shift-Enter` ou cliquez sur l’icône « Lire » (le triangle pointant vers la droite dans la barre d’outils).

## <a name="a-powerful-paradigm-live-computational-documents-with-rich-media"></a>Un paradigme puissant : live calculs documents avec des biens multimédias enrichis

Le bloc-notes lui-même devrait sembler très naturel à toute personne qui a utilisé Python et un traitement de texte, car il s’agit d’une certaine manière un mélange des deux : vous pouvez exécuter des blocs de code Python, mais vous pouvez également conserver des notes et autre texte en modifiant le style d’une cellule à partir de « Code » à « Démarque » à l’aide du menu déroulant dans la barre d’outils.

Jupyter est beaucoup plus qu’un traitement de texte, car elle permet le mélange de calcul et au contenu multimédia (texte, graphiques, vidéo et pratiquement rien de qu'un navigateur web moderne peut afficher). Vous pouvez combiner, texte, code, vidéos et autres éléments !

![Capture d’écran](./media/virtual-machines-linux-jupyter-notebook/jupyter-editing-experience.png)

Grâce à la puissance de nombreuses bibliothèques excellente de Python pour scientifique et technique informatique et, dans l’écran suivant, qu'un calcul simple peut être effectué avec la même facilité comme une analyse de réseau complexe, toutes dans un environnement spécifique.

Ce paradigme de mélange de la puissance de l’Internet moderne avec calcul live offre de nombreuses possibilités et est idéal pour le cloud ; le bloc-notes peut être utilisé :

* Un bloc-notes calcul enregistrement exploratoire travailler sur un problème.

* Pour partager des résultats avec vos collègues dans « live » calcul formulaire ou dans des formats de papier (HTML, PDF).

* Pour distribuer et présenter des supports de cours pour l’enseignant live qui impliquent calcul, afin que les étudiants peuvent expérimenter immédiatement le code réel, modifiez-la et réexécutez interactive.

* Donner « livres exécutables » qui présentent des résultats de la recherche d’une manière qui peut être reproduite, validée et étendue par d’autres personnes d’immédiatement.

* En tant que plate-forme pour l’informatique collaboratif : plusieurs utilisateurs peuvent se connecter au serveur même bloc-notes pour partager une session de calcul.


Si vous accédez à de code source IPython [référentiel][], vous trouverez un répertoire entier avec des exemples de bloc-notes que vous pouvez télécharger et expérimenter sur votre propre ordinateur virtuel Jupyter Azure.  Il suffit de télécharger le `.ipynb` fichiers à partir du site et les télécharger dans le tableau de bord de votre bloc-notes Azure machine virtuelle (ou téléchargez-les directement dans la machine virtuelle).

## <a name="conclusion"></a>Conclusion

Le bloc-notes Jupyter fournit une interface puissante pour accéder au mode interactif la puissance de l’écosystème Python sur Azure.  Il traite un large éventail de cas d’utilisation, y compris d’exploration simple et l’apprentissage Python, analyse des données et de visualisation, simulation et informatique parallèle. Les documents de bloc-notes qui en résulte contiennent un enregistrement complet des calculs qui sont effectuées et peuvent être partagées avec d’autres utilisateurs Jupyter.  Le bloc-notes Jupyter peut être utilisé comme une application locale, mais il est idéal pour les déploiements cloud sur Azure

Les principales fonctionnalités de Jupyter sont également disponibles dans Visual Studio via les [Python Tools pour Visual Studio][] (PTVS). PTVS est un gratuites et libres plug-in de Microsoft qui désactive Visual Studio dans un environnement de développement Python avancé qui inclut un éditeur avancé avec IntelliSense, débogage, profilage ou en parallèle computing intégration.

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations, voir le [Centre de développement Python](/develop/python/).

[portal-vm-linux]: https://azure.microsoft.com/en-us/documentation/articles/virtual-machines-linux-tutorial-portal-rm/
[référentiel]: https://github.com/ipython/ipython
[Outils Python pour Visual Studio]: http://aka.ms/ptvs

<properties
    pageTitle="Installer MongoDB sur un Windows machine virtuelle | Microsoft Azure"
    description="Découvrez comment installer MongoDB sur un ordinateur virtuel Azure exécutant Windows Server 2012 R2 créé avec le modèle de déploiement du Gestionnaire de ressources."
    services="virtual-machines-windows"
    documentationCenter=""
    authors="iainfoulds"
    manager="timlt"
    editor=""/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/04/2016"
    ms.author="iainfou"/>

# <a name="install-and-configure-mongodb-on-a-windows-vm-in-azure"></a>Installer et configurer MongoDB sur un ordinateur virtuel Windows Azure
[MongoDB](http://www.mongodb.org) est une base de données NoSQL open source, High performance populaires. Cet article présente l’installation et configuration de MongoDB sur une machine virtuelle de Windows Server 2012 R2 (machine virtuelle) dans Azure. Vous pouvez également [installer MongoDB sur un ordinateur virtuel Linux dans Azure](virtual-machines-linux-install-mongodb.md).


## <a name="prerequisites"></a>Conditions préalables

Avant d’installer et configurer MongoDB, vous devez créer une machine virtuelle et, dans l’idéal, lui ajouter un disque de données. Consultez les articles suivants pour créer une machine virtuelle et ajouter un disque de données :

- [Créer une machine virtuelle de Windows Server à l’aide du portail Azure](virtual-machines-windows-hero-tutorial.md) ou [créer un serveur de Windows machine virtuelle à l’aide de PowerShell Azure](virtual-machines-windows-ps-create.md)
- [Joindre un disque de données pour une machine virtuelle de Windows Server à l’aide du portail Azure](virtual-machines-windows-attach-disk-portal.md) ou [joindre un disque de données pour une machine virtuelle Server Windows PowerShell Azure](https://msdn.microsoft.com/library/mt603673.aspx)
    
Pour commencer l’installation et configuration MongoDB, [Connectez-vous à votre machine virtuelle de Windows Server](virtual-machines-windows-connect-logon.md) à l’aide de bureau à distance.


## <a name="install-mongodb"></a>Installer MongoDB

> [AZURE.IMPORTANT] Fonctionnalités de sécurité MongoDB, telles que l’authentification et lien d’adresse IP, ne sont pas activées par défaut. Fonctionnalités de sécurité doivent être activées avant de déployer MongoDB dans un environnement de production. Pour plus d’informations, voir [MongoDB sécurité et authentification](http://www.mongodb.org/display/DOCS/Security+and+Authentication).

1. Une fois que vous avez connectés à votre ordinateur virtuel à l’aide de bureau à distance, ouvrez Internet Explorer dans le menu **Démarrer** sur l’ordinateur virtuel.

2. Sélectionnez **paramètres de sécurité, confidentialité et compatibilité recommandé d’utiliser** lorsque vous ouvrez Internet Explorer, puis cliquez sur **OK**.

3. Configuration de sécurité renforcée d’Internet Explorer est activée par défaut. Ajouter le site Web MongoDB à la liste des sites autorisés :

    - Sélectionnez l’icône **Outils** dans le coin supérieur droit.
    - Dans **Options Internet**, sélectionnez l’onglet **sécurité** , puis l’icône **Sites de confiance** .
    - Cliquez sur le bouton **Sites** . Ajouter _https://\*. mongodb.org_ à la liste des sites de confiance et fermez la boîte de dialogue.

    ![Configurer les paramètres de sécurité Internet Explorer](./media/virtual-machines-windows-install-mongodb/configure-internet-explorer-security.png)

4. Accédez à la page [téléchargements de packs linguistiques MongoDB -](http://www.mongodb.org/downloads) (http://www.mongodb.org/downloads).

5. Par défaut, elle doit sélectionner l’édition de **Serveur de la Communauté** et la dernière version stable en cours pour Windows Server 2008 R2 64 bits et versions ultérieures. Pour télécharger le programme d’installation, cliquez sur **télécharger (msi)**.

    ![Télécharger installer MongoDB](./media/virtual-machines-windows-install-mongodb/download-mongodb.png)

    Exécutez le programme d’installation après que le téléchargement est terminé.

6. Lisez et acceptez le contrat de licence. Lorsque vous êtes invité, sélectionnez Installer **achevé** .

7. Dans l’écran final, cliquez sur **installer**.


## <a name="configure-the-vm-and-mongodb"></a>Configurer la machine virtuelle et MongoDB

1. Les variables de chemin d’accès ne sont pas mises à jour par le programme d’installation MongoDB. Sans la MongoDB `bin` emplacement dans votre variable path, vous devez spécifier le chemin d’accès complet chaque fois que vous utilisez un fichier exécutable MongoDB. Pour ajouter l’emplacement à la variable du chemin d’accès :

    - Cliquez sur le menu **Démarrer** , puis sélectionnez **système**.
    - Cliquez sur **paramètres système avancés**, puis cliquez sur **Variables d’environnement**.
    - Sous **variables système**, sélectionnez **chemin d’accès**, puis cliquez sur **Modifier**.

    ![Configurer des variables de chemin d’accès](./media/virtual-machines-windows-install-mongodb/configure-path-variables.png)

    Ajouter le chemin d’accès à votre MongoDB `bin` dossier. MongoDB est généralement installé dans `C:\Program Files\MongoDB`. Vérifiez le chemin d’accès de l’installation sur votre ordinateur virtuel. L’exemple suivant ajoute la valeur par défaut MongoDB installer emplacement pour la `PATH` variable :

    ```
    ;C:\Program Files\MongoDB\Server\3.2\bin
    ```

    > [AZURE.NOTE] Veillez à ajouter le premier point-virgule (`;`) pour indiquer que vous ajoutez un emplacement pour votre `PATH` variable.

2. Créer des répertoires de données et le journal MongoDB sur votre disque de données. Dans le menu **Démarrer** , sélectionnez **invite de commandes**. Les exemples suivants créent les répertoires sur le lecteur f :

    ```
    mkdir F:\MongoData
    mkdir F:\MongoLogs
    ```

3. Démarrer une instance MongoDB avec la commande suivante, en ajustant le chemin d’accès à vos données et des journaux en conséquence :

    ```
    mongod --dbpath F:\MongoData\ --logpath F:\MongoLogs\mongolog.log
    ```

    Il peut prendre plusieurs minutes MongoDB allouer les fichiers journaux et commencer à l’écoute de connexions. Tous les messages journal sont dirigés vers le fichier *F:\MongoLogs\mongolog.log* en tant que `mongod.exe` serveur démarre et affecte les fichiers journaux.

    > [AZURE.NOTE] L’invite de commandes reste concentré sur cette tâche pendant que votre instance MongoDB est en cours d’exécution. Laissez la fenêtre d’invite de commandes ouverte pour continuer MongoDB en cours d’exécution. Ou, installez MongoDB en tant que service, comme indiqué dans l’étape suivante.

4. Pour une expérience MongoDB plus robuste, installez le `mongod.exe` en tant que service. Création d’un service signifie que vous n’avez pas besoin de quitter une invite de commande en cours d’exécution chaque fois que vous voulez utiliser MongoDB. Créez le service comme suit, le chemin d’accès à vos données et journal répertoires en ajustant en conséquence :

    ```
    mongod --dbpath F:\MongoData\ --logpath F:\MongoLogs\mongolog.log `
        --logappend  --install
    ```

    La commande précédente crée un service appelé MongoDB, avec une description de « Mongo DB ». Les paramètres suivants sont également spécifiés :

    - La `--dbpath` option spécifie l’emplacement du répertoire de données.
    - La `--logpath` option doit être utilisée pour spécifier un fichier journal, car le service en cours d’exécution n’est pas une fenêtre de commande pour afficher la sortie.
    - La `--logappend` option indique qu’un redémarrage du service entraîne la sortie à ajouter dans le fichier journal existant.

  Pour démarrer le service MongoDB, exécutez la commande suivante :

    ```
    net start MongoDB
    ```

    Pour plus d’informations sur la création du service MongoDB, consultez [configurer un Service Windows pour MongoDB](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-windows/#mongodb-as-a-windows-service).

## <a name="test-the-mongodb-instance"></a>Test de l’instance MongoDB

Avec MongoDB exécute comme une instance unique ou installé en tant que service, vous pouvez maintenant démarrer la création et l’utilisation de vos bases de données. Pour commencer l’environnement d’administration MongoDB, ouvrir une autre fenêtre d’invite de commandes dans le menu **Démarrer** , puis entrez la commande suivante :

```
mongo  
```

Vous pouvez répertorier les bases de données avec le `db` commande. Insérer des données comme suit :

```
db.foo.insert( { a : 1 } )
```

Rechercher des données comme suit :

```
db.foo.find()
```

Le résultat est semblable à l’exemple suivant :

```
{ "_id" : "ObjectId("57f6a86cee873a6232d74842"), "a" : 1 }
```

Quitter le `mongo` de la console comme suit :

```
exit
```

## <a name="configure-firewall-and-network-security-group-rules"></a>Configurer des règles de pare-feu et groupe de sécurité réseau
À présent que MongoDB est installé et exécuté, ouvrez un port dans le pare-feu Windows afin que vous pouvez vous connecter à distance à MongoDB. Pour créer une nouvelle règle entrante pour autoriser le port TCP 27017, ouvrez une invite PowerShell d’administration et entrez la commande suivante :

```powerShell
New-NetFirewallRule -DisplayName "Allow MongoDB" -Direction Inbound `
    -Protocol TCP -LocalPort 27017 -Action Allow
```

Vous pouvez également créer la règle à l’aide de l’outil de gestion graphique **Pare-feu Windows avec fonctions avancées de sécurité** . Créer une règle entrante pour autoriser le port TCP 27017.

Si nécessaire, créez une règle de groupe de sécurité de réseau pour autoriser l’accès à MongoDB d’en dehors du sous-réseau Azure réseau virtuel existant. Vous pouvez créer les règles de groupe de sécurité réseau à l’aide du [portail Azure](virtual-machines-windows-nsg-quickstart-portal.md) ou [PowerShell Azure](virtual-machines-windows-nsg-quickstart-powershell.md). Comme avec les règles de pare-feu Windows, autoriser le port TCP 27017 à l’interface réseau virtuel de votre MongoDB VM.

> [AZURE.NOTE] Port TCP 27017 est le port par défaut utilisé par MongoDB. Vous pouvez modifier ce port à l’aide de la `--port` paramètre lors du démarrage `mongod.exe` manuellement ou à partir d’un service. Si vous changez le port, veillez à mettre à jour les règles de pare-feu Windows et de groupe de sécurité réseau à l’étape précédente.


## <a name="next-steps"></a>Étapes suivantes
Dans ce didacticiel, vous avez appris comment installer et configurer MongoDB sur votre ordinateur Windows virtuel. Vous pouvez désormais accéder MongoDB sur votre ordinateur Windows virtuel, en suivant les rubriques avancées dans la [documentation MongoDB](https://docs.mongodb.com/manual/).

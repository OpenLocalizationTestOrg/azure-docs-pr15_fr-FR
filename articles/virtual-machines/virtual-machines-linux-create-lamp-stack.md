<properties
    pageTitle="Déployer feu sur une machine virtuelle Linux | Microsoft Azure"
    description="Découvrez comment installer la pile de feu sur un Linux VM"
    services="virtual-machines-linux"
    documentationCenter="virtual-machines"
    authors="jluk"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machines-linux"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-linux"
    ms.devlang="NA"
    ms.topic="article"
    ms.date="06/07/2016"
    ms.author="juluk"/>

# <a name="deploy-lamp-stack-on-azure"></a>Déployer pile feu sur Azure
Cet article vous guidera tout au long comment déployer un serveur web Apache, MySQL et PHP (la pile feu) sur Azure. Vous avez besoin d’un compte Azure ([Obtenez un essai gratuit](https://azure.microsoft.com/pricing/free-trial/)) et l' [Infrastructure du langage commun Azure](../xplat-cli-install.md) qui est [connecté à votre compte Azure](../xplat-cli-connect.md).

Il existe deux méthodes pour l’installation de feu traitée dans cet article :

## <a name="quick-command-summary"></a>Récapitulatif des commandes rapide

1) Déployer feu sur nouvelle machine virtuelle

```
# One command to create a resource group holding a VM with LAMP already on it
$ azure group create -n uniqueResourceGroup -l westus --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/lamp-app/azuredeploy.json
```

2) Déployer feu sur machine virtuelle existant

```
# Two commands: one updates packages, the other installs Apache, MySQL, and PHP
user@ubuntu$ sudo apt-get update
user@ubuntu$ sudo apt-get install apache2 mysql-server php5 php5-mysql
```

## <a name="deploy-lamp-on-new-vm-walkthrough"></a>Déployer feu sur nouvelle machine virtuelle procédure pas à pas

Vous pouvez commencer par créer un nouveau [groupe de ressources](../azure-resource-manager/resource-group-overview.md) qui contiendra la machine virtuelle :

    $ azure group create uniqueResourceGroup westus
    info:    Executing command group create
    info:    Getting resource group uniqueResourceGroup
    info:    Creating resource group uniqueResourceGroup
    info:    Created resource group uniqueResourceGroup
    data:    Id:                  /subscriptions/########-####-####-####-############/resourceGroups/uniqueResourceGroup
    data:    Name:                uniqueResourceGroup
    data:    Location:            westus
    data:    Provisioning State:  Succeeded
    data:    Tags: null
    data:
    info:    group create command OK

Pour créer la machine virtuelle elle-même, vous pouvez utiliser un modèle de gestionnaire de ressources Azure déjà écrite trouvé [ici sur GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/lamp-app).

    $ azure group deployment create --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/lamp-app/azuredeploy.json uniqueResourceGroup uniqueLampName

Vous devriez voir une réponse vous demandant certaines entrées plus :

    info:    Executing command group deployment create
    info:    Supply values for the following parameters
    storageAccountNamePrefix: lampprefix
    location: westus
    adminUsername: someUsername
    adminPassword: somePassword
    mySqlPassword: somePassword
    dnsLabelPrefix: azlamptest
    info:    Initializing template configurations and parameters
    info:    Creating a deployment
    info:    Created template deployment "uniqueLampName"
    info:    Waiting for deployment to complete
    data:    DeploymentName     : uniqueLampName
    data:    ResourceGroupName  : uniqueResourceGroup
    data:    ProvisioningState  : Succeeded
    data:    Timestamp          :
    data:    Mode               : Incremental
    data:    CorrelationId      : d51bbf3c-88f1-4cf3-a8b3-942c6925f381
    data:    TemplateLink       : https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/lamp-app/azuredeploy.json
    data:    ContentVersion     : 1.0.0.0
    data:    DeploymentParameters :
    data:    Name                      Type          Value
    data:    ------------------------  ------------  -----------
    data:    storageAccountNamePrefix  String        lampprefix
    data:    location                  String        westus
    data:    adminUsername             String        someUsername
    data:    adminPassword             SecureString  undefined
    data:    mySqlPassword             SecureString  undefined
    data:    dnsLabelPrefix            String        azlamptest
    data:    ubuntuOSVersion           String        14.04.2-LTS
    info:    group deployment create command OK

Vous venez de créer un Linux VM avec feu est déjà installé. Si vous le souhaitez, vous pouvez vérifier l’installation en passage vers le bas jusqu'à [vérifier feu correctement installé].

## <a name="deploy-lamp-on-existing-vm-walkthrough"></a>Déployer feu de procédure pas à pas machine virtuelle existant

Si vous avez besoin d’aide pour créer un Linux VM vous pouvez tête [ici pour découvrir comment créer un Linux VM] (. / virtual-machines-linux-quick-create-cli.md). Ensuite, vous devrez SSH dans la VM Linux. Si vous avez besoin d’aide à la création d’un code SSH vous pouvez tête [ici pour découvrir comment créer un code SSH sur Linux/Mac] (. / virtual-machines-linux-mac-create-ssh-keys.md).
Si vous avez déjà un code SSH, coder en avance et SSH votre VM Linux avec `ssh username@uniqueDNS`.

À présent que vous travaillez dans votre Linux VM, nous allons découvrir l’installation de la pile de feu sur répartitions basée sur Debian. Les commandes exactes peuvent différer pour d’autres distros Linux.

#### <a name="installing-on-debianubuntu"></a>L’installation sur Debian/Ubuntu

Vous devez les packages suivants installés : `apache2`, `mysql-server`, `php5`, et `php5-mysql`. Vous pouvez installer en saisissant ces packages directement ou en utilisant Tasksel. Instructions pour les deux options sont présentées ci-après.
Avant d’installer, vous devez télécharger et mettre à jour les listes de package.

    user@ubuntu$ sudo apt-get update
    
##### <a name="individual-packages"></a>Packages individuels
À l’aide de nature get :

    user@ubuntu$ sudo apt-get install apache2 mysql-server php5 php5-mysql

##### <a name="using-tasksel"></a>À l’aide de Tasksel
Vous pouvez également télécharger Tasksel, un outil de Debian/Ubuntu installe plusieurs packages connexes en tant que coordonnée « tâche » sur votre système.

    user@ubuntu$ sudo apt-get install tasksel
    user@ubuntu$ sudo tasksel install lamp-server

Après avoir exécuté l’une des options ci-dessus vous devez installer ces packages et un nombre d’autres dépendances. Appuyez sur « y », puis « Entrée » pour continuer et suivez les invites autres pour définir un mot de passe d’administration pour MySQL. Vous installerez les extensions PHP requises minimales requises pour utiliser PHP avec MySQL. 

![][1]

Exécutez la commande suivante pour afficher d’autres extensions PHP disponibles sous forme de packages :

    user@ubuntu$ apt-cache search php5


#### <a name="create-infophp-document"></a>Créer info.php dans le document

Vous devriez maintenant pouvoir savoir quelle version Apache, MySQL et PHP vous disposez via la ligne de commande en tapant `apache2 -v`, `mysql -v`, ou `php -v`.

Si vous voulez poursuivre l’analyse, vous pouvez créer une page d’informations PHP rapide à afficher dans un navigateur. Créer un nouveau fichier avec Nano éditeur de texte avec cette commande :

    user@ubuntu$ sudo nano /var/www/html/info.php

Dans l’éditeur de texte GNU Nano, ajoutez les lignes suivantes :

    <?php
    phpinfo();
    ?>

Puis enregistrez et fermez l’éditeur de texte.

Redémarrez Apache avec cette commande afin que toutes les nouvelles installations prennent effet.

    user@ubuntu$ sudo service apache2 restart

## <a name="verify-lamp-successfully-installed"></a>Vérifier feu installé avec succès

À présent, vous pouvez vérifier la page informations de PHP que vous venez de créer dans votre navigateur en accédant à http://youruniqueDNS/info.php, il doit ressembler à ceci.

![][2]

Vous pouvez vérifier votre installation Apache en afficher la Page par défaut de Apache2 Ubuntu en accédant à vous http://youruniqueDNS/. Vous devriez voir ressembler à ceci.

![][3]

Félicitations, vous devez le programme d’installation simplement une pile feu sur votre ordinateur virtuel Azure !

## <a name="next-steps"></a>Étapes suivantes

Consultez la documentation Ubuntu dans la pile de feu :

- [https://help.Ubuntu.com/Community/ApacheMySQLPHP](https://help.ubuntu.com/community/ApacheMySQLPHP)

[1]: ./media/virtual-machines-linux-deploy-lamp-stack/configmysqlpassword-small.png
[2]: ./media/virtual-machines-linux-deploy-lamp-stack/phpsuccesspage.png
[3]: ./media/virtual-machines-linux-deploy-lamp-stack/apachesuccesspage.png

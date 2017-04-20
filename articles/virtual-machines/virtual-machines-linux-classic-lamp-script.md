<properties
    pageTitle="Utiliser l’Extension CustomScript sur un ordinateur virtuel Linux | Microsoft Azure"
    description="Apprenez à utiliser l’extension CustomScript pour déployer des applications Linux machines virtuelles en fonctionnement dans Azure créé à l’aide du modèle de déploiement classique."
    editor="tysonn"
    manager="timlt"
    documentationCenter=""
    services="virtual-machines-linux"
    authors="gbowerman"
    tags="azure-service-management"/>

<tags
    ms.service="virtual-machines-linux"
    ms.workload="multiple"
    ms.tgt_pltfrm="linux"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/13/2016"
    ms.author="guybo"/>

#<a name="deploy-a-lamp-app-using-the-azure-customscript-extension-for-linux"></a>Déployer une application de feu à l’aide de le CustomScript Extension Azure pour Linux#

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]


L’Extension Microsoft Azure CustomScript pour Linux offre un moyen pour personnaliser vos machines virtuelles (machines virtuelles) en exécutant le code arbitraire écrit dans n’importe quel langage de script pris en charge par la machine virtuelle (par exemple, Python et Bash). Il fournit un moyen très souple pour automatiser le déploiement des applications sur plusieurs ordinateurs.

Vous pouvez déployer le CustomScript Extension à l’aide du portail classique Azure, Windows PowerShell ou l’Interface de ligne Azure (commande Azure).

Dans cet article, nous allons utiliser l’infrastructure du langage commun Azure pour déployer une application de feu simple pour une Ubuntu VM créé en utilisant le modèle de déploiement classique.

## <a name="prerequisites"></a>Conditions préalables

Dans cet exemple, commencez par créer deux Azure machines virtuelles exécutant Ubuntu 14.04 ou version ultérieure. Les ordinateurs virtuels sont appelés *machine virtuelle script* et *machine virtuelle feu*. Utilisez des noms uniques lorsque vous créez les ordinateurs virtuels. Une est utilisée pour exécuter les commandes de l’infrastructure du langage commun et une est utilisée pour déployer l’application de feu.

Vous devez également un compte de stockage Azure et une clé à y accéder (vous pouvez obtenir cette information à partir du portail classique Azure).

Si vous avez besoin d’aide pour créer des machines virtuelles Linux sur Azure consultez [créer un Linux en cours d’exécution Machine virtuelle](virtual-machines-linux-classic-createportal.md).

Les commandes d’installation part du principe Ubuntu, mais vous pouvez l’adapter l’installation de n’importe quel distro Linux pris en charge.

La machine virtuelle machine virtuelle script doit avoir installé, avec une connexion active au Azure Azure infrastructure du langage commun. Pour obtenir de l’aide, consultez [installer](../xplat-cli-install.md)et configurer l’Interface de ligne de commande Azure.

## <a name="upload-a-script"></a>Télécharger un script

Nous allons utiliser le CustomScript Extension pour exécuter un script sur un ordinateur distant virtuel pour installer la pile de feu et créer une page PHP. Pour accéder au script où que vous soyez nous allez le télécharger en tant qu’un blob Azure.

### <a name="script-overview"></a>Vue d’ensemble de script

L’exemple de script installe une pile feu Ubuntu (y compris la configuration d’une installation en mode silencieux de MySQL), écrit un fichier PHP simple et démarre Apache.

    #!/bin/bash
    # set up a silent install of MySQL
    dbpass="mySQLPassw0rd"

    export DEBIAN_FRONTEND=noninteractive
    echo mysql-server-5.6 mysql-server/root_password password $dbpass | debconf-set-selections
    echo mysql-server-5.6 mysql-server/root_password_again password $dbpass | debconf-set-selections

    # install the LAMP stack
    apt-get -y install apache2 mysql-server php5 php5-mysql  

    # write some PHP
    echo \<center\>\<h1\>My Demo App\</h1\>\<br/\>\</center\> > /var/www/html/phpinfo.php
    echo \<\?php phpinfo\(\)\; \?\> >> /var/www/html/phpinfo.php

    # restart Apache
    apachectl restart

### <a name="upload-script"></a>Télécharger le script

Enregistrez le script dans un fichier texte, par exemple *install_lamp.sh*et puis téléchargez-le sur le stockage Azure. Vous pouvez le faire facilement avec Azure infrastructure du langage commun. L’exemple suivant télécharge le fichier à un conteneur de stockage nommé « scripts ». Si le conteneur n’existe pas, vous devez tout d’abord le créer.

    azure storage blob upload -a <yourStorageAccountName> -k <yourStorageKey> --container scripts ./install_lamp.sh

Également créer un fichier JSON qui explique comment télécharger le script à partir du stockage Azure. L’enregistrer comme *public_config.json* (en remplaçant « mystorage » par le nom de votre compte de stockage) :

    {"fileUris":["https://mystorage.blob.core.windows.net/scripts/install_lamp.sh"], "commandToExecute":"sh install_lamp.sh" }


## <a name="deploy-the-extension"></a>Déploiement de l’extension

Vous pouvez maintenant utiliser la commande suivante pour déployer le CustomScript Extension Linux sur l’ordinateur distant virtuel à l’aide de l’infrastructure du langage commun Azure.

    azure vm extension set -c "./public_config.json" lamp-vm CustomScript Microsoft.Azure.Extensions 2.0

La commande précédente télécharge et exécute le script *install_lamp.sh* sur la machine virtuelle appelée *machine virtuelle feu*.

Étant donné que l’application comprend un serveur web, n’oubliez pas d’ouvrir un port d’écoute HTTP sur l’ordinateur distant virtuel avec la commande suivante.

    azure vm endpoint create -n Apache -o tcp lamp-vm 80 80

## <a name="monitoring-and-troubleshooting"></a>Surveillance et résolution des problèmes

Vous pouvez vérifier dans quelle mesure le script personnalisé s’exécute en consultant le fichier journal sur l’ordinateur distant virtuel. SSH pour *machine virtuelle feu* et la fin du fichier journal avec la commande suivante.

    cd /var/log/azure/customscript
    tail -f handler.log

Après avoir exécuté le CustomScript Extension, vous pouvez passer à la page PHP que vous avez créé pour plus d’informations. La page PHP dans l’exemple de cet article est *http://lamp-vm.cloudapp.net/phpinfo.php*.

## <a name="additional-resources"></a>Ressources supplémentaires

Vous pouvez utiliser les mêmes étapes pour déployer des applications plus complexes. Dans cet exemple, le script d’installation a été enregistré comme un blob publique dans le stockage Azure. Une option la plus sûre serait de stocker le script d’installation comme blob sécurisé avec une [Signature accès sécurisé](https://msdn.microsoft.com/library/azure/ee395415.aspx) (sa).

Ressources supplémentaires pour Azure infrastructure du langage commun, Linux et le CustomScript Extension sont décrits ci-dessous.

[Automatiser les tâches de personnalisation machine virtuelle Linux à l’aide de l’Extension CustomScript](https://azure.microsoft.com/blog/2014/08/20/automate-linux-vm-customization-tasks-using-customscript-extension/)

[Extensions de Linux Azure (GitHub)](https://github.com/Azure/azure-linux-extensions)

[Linux et Open Source informatique sur Azure](virtual-machines-linux-opensource-links.md)

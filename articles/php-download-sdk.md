<properties
    pageTitle="Téléchargez le Kit de développement Azure pour PHP"
    description="Découvrez comment télécharger et installer le Kit de développement Azure pour PHP."
    documentationCenter="php"
    services="app-service\web"
    authors="allclark"
    manager="douge"
    editor=""/>

<tags
    ms.service="app-service-web"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="PHP"
    ms.topic="article"
    ms.date="06/01/2016"
    ms.author="allclark;yaqiyang"/>

#<a name="download-the-azure-sdk-for-php"></a>Téléchargez le Kit de développement Azure pour PHP

## <a name="overview"></a>Vue d’ensemble

Le Kit de développement Azure pour PHP inclut des composants qui vous permettent de développer, déployer et gérer des applications PHP pour Azure. Plus précisément, le Kit de développement Azure pour PHP inclut les éléments suivants :

* **Bibliothèques de client le PHP pour Azure**. Ces bibliothèques de classes fournissent une interface pour accéder aux fonctionnalités Azure, telles que les services de gestion des données et services cloud.  
* **L’Interface de ligne de commande Azure pour Mac, Linux et Windows (Azure infrastructure du langage commun)**. Il s’agit d’un jeu de commandes pour déployer et gérer des services Azure, tels que des sites Web Azure et Machines virtuelles Azure. Le travail Azure infrastructure du langage commun sur n’importe quelle plate-forme, y compris Mac, Linux et Windows.
* **PowerShell azure (Windows uniquement)**. Il s’agit d’un ensemble d’applets de commande PowerShell pour le déploiement et la gestion des Services Azure, tels que les Services en nuage et Machines virtuelles.
* **Les émulateurs Azure (Windows uniquement)**. Les émulateurs informatiques et de stockage sont émulateurs locales de services de cloud et des services de gestion des données qui vous permettent de tester une application localement. Les émulateurs Azure s’exécutent sur Windows uniquement.

Les sections suivantes décrivent comment télécharger et installer les composants ci-dessus.

Les instructions fournies dans cette rubrique part du principe que vous avez [PHP] [ install-php] installé.

> [AZURE.NOTE] Vous devez avoir PHP 5,5 ou une version ultérieure pour utiliser les bibliothèques de client PHP pour Azure.

##<a name="php-client-libraries-for-azure"></a>Bibliothèques de client PHP pour Azure

Les bibliothèques de Client PHP pour Azure fournit une interface pour accéder aux fonctionnalités Azure, telles que les services de gestion des données et services, à partir de n’importe quel système d’exploitation en nuage. Ces bibliothèques peuvent être installés via le compositeur.

Pour plus d’informations sur l’utilisation des bibliothèques du Client PHP pour Azure, Découvrez [comment utiliser le Service Blob][blob-service], [comment utiliser le Service de tableau] [ table-service] et [comment utiliser le Service de file d’attente][queue-service].

###<a name="install-via-composer"></a>Installer via Composer

1. [Installer Git][install-git].


    > [AZURE.NOTE] Sous Windows, vous devrez également ajouter le Git exécutable à votre variable d’environnement PATH.

2. Créez un fichier nommé **composer.json** à la racine de votre projet et ajoutez le code suivant à celui-ci :

        {
            "require": {
                "microsoft/windowsazure": "^0.4"
            }
        }

3. Télécharger ** [composer.phar] [ composer-phar] ** dans la racine de votre projet.

4. Ouvrez une invite de commandes et exécuter cette méthode dans la racine de votre projet

        php composer.phar install

##<a name="azure-powershell-and-azure-emulators"></a>PowerShell Azure et émulateurs Azure

PowerShell Azure est un ensemble d’applets de commande PowerShell pour le déploiement et la gestion des Services Azure (par exemple, les Services en nuage et Machines virtuelles). Les émulateurs Azure sont émulateurs de services de cloud et des services de gestion des données qui vous permettent de tester une application localement. Ces composants sont prises en charge Windows uniquement.

Les procédures recommandées pour installer PowerShell Azure et les émulateurs Azure consiste à utiliser [Microsoft Web Platform Installer][download-wpi]. Notez que vous pouvez également choisir d’installer d’autres composants de développement, telles que PHP, SQL Server et les Drivers Microsoft pour SQL Server pour PHP et WebMatrix.

Pour plus d’informations sur l’utilisation de PowerShell Azure, Découvrez [comment utiliser Azure PowerShell][powershell-tools].

##<a name="azure-cli"></a>Azure infrastructure du langage commun

L’infrastructure du langage commun Azure est un ensemble de commandes pour déployer et gérer des services Azure, tels que des sites Web Azure et Machines virtuelles Azure. Pour plus d’informations sur l’installation d’Azure infrastructure du langage commun, consultez [installer l’infrastructure du langage commun Azure](xplat-cli-install.md).

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations, voir le [Centre de développement PHP](/develop/php/).


[install-php]: http://www.php.net/manual/en/install.php
[composer-github]: https://github.com/composer/composer
[composer-phar]: http://getcomposer.org/composer.phar
[nodejs-org]: http://nodejs.org/
[install-node-linux]: https://github.com/joyent/node/wiki/Installing-Node.js-via-package-manager
[download-wpi]: http://go.microsoft.com/fwlink/?LinkId=253447
[mac-installer]: http://go.microsoft.com/fwlink/?LinkId=252249
[blob-service]: http://go.microsoft.com/fwlink/?LinkId=252714
[table-service]: http://go.microsoft.com/fwlink/?LinkId=252715
[queue-service]: http://go.microsoft.com/fwlink/?LinkId=252716
[azure cli]: http://go.microsoft.com/fwlink/?LinkId=252717
[powershell-tools]: http://go.microsoft.com/fwlink/?LinkId=252718
[php-sdk-github]: http://go.microsoft.com/fwlink/?LinkId=252719
[install-git]: http://git-scm.com/book/en/Getting-Started-Installing-Git

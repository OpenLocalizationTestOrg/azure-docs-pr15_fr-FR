<properties
    pageTitle="Installer MongoDB sur un Windows machine virtuelle | Microsoft Azure"
    description="Découvrez comment installer MongoDB sur un ordinateur virtuel Azure créé avec le modèle de déploiement classique exécutant Windows Server."
    services="virtual-machines-windows"
    documentationCenter=""
    authors="iainfoulds"
    manager="timlt"
    editor="tysonn"
    tags="azure-service-management"/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/10/2016"
    ms.author="iainfou"/>

#<a name="install-mongodb-on-a-windows-vm"></a>Installer MongoDB sur une machine virtuelle Windows

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Pour installer et configurer MongoDB à l’aide du modèle de déploiement Gestionnaire de ressources, voir [cet article](virtual-machines-windows-classic-install-mongodb.md).

[MongoDB] [ MongoDB] est une base de données NoSQL open source, High performance populaires. Cet article vous guide dans la création d’un serveur de Windows de machine virtuelle (machine virtuelle) à l’aide du [portail classique Azure][AzurePortal]. Créez et joindre un disque de données de la machine virtuelle avant d’installer et configurer MongoDB. Si vous avez une machine virtuelle existante dans Azure que vous voulez utiliser, vous pouvez passer directement à [l’installation et configuration MongoDB](#install-and-run-mongodb-on-the-virtual-machine).


## <a name="create-a-virtual-machine-running-windows-server"></a>Créer une machine virtuelle exécutant Windows Server

Suivez ces instructions pour créer une machine virtuelle.

[AZURE.INCLUDE [virtual-machines-create-WindowsVM](../../includes/virtual-machines-create-windowsvm.md)]

> [AZURE.NOTE] Vous pouvez ajouter un point de terminaison pour MongoDB lors de la création de la machine virtuelle et configurez-la comme suit : nommez-la en tant que **Mongo**, utilisez le protocole **TCP** et définir les ports publiques et privées à **27017**.

## <a name="attach-a-data-disk"></a>Joindre un disque de données
Pour fournir un stockage de la machine virtuelle, joindre un disque de données, puis l’initialisation afin que Windows puisse l’utiliser. Si vous disposez déjà d’un disque de données, vous pouvez joindre ce disque existant, ou vous pouvez joindre un disque vide.

[AZURE.INCLUDE [howto-attach-disk-windows-linux](../../includes/howto-attach-disk-windows-linux.md)]

Pour obtenir des instructions sur l’initialisation du disque, voir « comment : initialisation d’un disque de données nouveau dans Windows Server » dans [l’insertion d’un disque de données pour une machine virtuelle Windows](virtual-machines-windows-classic-attach-disk.md).

## <a name="install-and-run-mongodb-on-the-virtual-machine"></a>Installer et exécuter MongoDB sur la machine virtuelle

[AZURE.INCLUDE [install-and-run-mongo-on-win2k8-vm](../../includes/install-and-run-mongo-on-win2k8-vm.md)]

## <a name="summary"></a>Résumé
Dans ce didacticiel, vous avez appris comment créer une machine virtuelle exécutant Windows Server, vous y connecter à distance et joindre un disque de données.  Vous avez également appris comment installer et configurer MongoDB sur la machine virtuelle fonctionnant sous Windows. Vous pouvez désormais accéder MongoDB sur la machine virtuelle fonctionnant sous Windows, en suivant les rubriques avancées dans la [documentation MongoDB][MongoDocs].

[MongoDocs]: http://docs.mongodb.org/manual/
[MongoDB]: http://www.mongodb.org/
[AzurePortal]: http://manage.windowsazure.com

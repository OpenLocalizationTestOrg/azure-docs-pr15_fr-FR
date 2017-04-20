<properties
    pageTitle="Configurer l’intégration de l’archivage sécurisé clés Azure pour SQL Server sur Azure machines virtuelles (responsable de ressources)"
    description="Découvrez comment automatiser la configuration de chiffrement de SQL Server pour une utilisation avec l’archivage sécurisé de clé Azure. Cette rubrique explique comment utiliser l’intégration de l’archivage sécurisé Azure clé avec des machines virtuelles SQL Server créés avec le Gestionnaire de ressources."
    services="virtual-machines-windows"
    documentationCenter=""
    authors="rothja"
    manager="jhubbard"
    editor=""
    tags="azure-service-management"/>

<tags
    ms.service="virtual-machines-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="vm-windows-sql-server"
    ms.workload="infrastructure-services"
    ms.date="10/25/2016"
    ms.author="jroth"/>

# <a name="configure-azure-key-vault-integration-for-sql-server-on-azure-vms-resource-manager"></a>Configurer l’intégration de l’archivage sécurisé clés Azure pour SQL Server sur Azure machines virtuelles (responsable de ressources)

> [AZURE.SELECTOR]
- [Gestionnaire de ressources](virtual-machines-windows-ps-sql-keyvault.md)
- [Classique](virtual-machines-windows-classic-ps-sql-keyvault.md)

## <a name="overview"></a>Vue d’ensemble
Il existe plusieurs fonctionnalités de chiffrement de SQL Server, telles que [le chiffrement transparent des données (TDE)](https://msdn.microsoft.com/library/bb934049.aspx), [chiffrement de niveau de colonne (Effacer)](https://msdn.microsoft.com/library/ms173744.aspx)et le [chiffrement de sauvegarde](https://msdn.microsoft.com/library/dn449489.aspx). Ces formes de chiffrement nécessitent vous permettent de gérer et stocker les clés de chiffrement utilisé pour le chiffrement. Le service Azure clé l’archivage sécurisé (AKV) est conçu pour améliorer la sécurité et la gestion de ces touches dans un emplacement sécurisé et hautement disponible. Le [Connecteur SQL Server](http://www.microsoft.com/download/details.aspx?id=45344) permet à SQL Server à utiliser ces raccourcis clavier de l’archivage sécurisé de clé Azure.

Si vous exécutez SQL Server avec locaux machines, il sont les [étapes à suivre pour accéder à l’archivage sécurisé de clé Azure à partir de votre ordinateur SQL Server locale](https://msdn.microsoft.com/library/dn198405.aspx). Mais pour SQL Server dans Azure machines virtuelles, vous pouvez gagner du temps en utilisant la fonctionnalité *d’Intégration de l’archivage sécurisé Azure clé* .

Lorsque cette fonctionnalité est activée, il automatiquement installe le connecteur SQL Server, configure le fournisseur EKM pour accéder à l’archivage sécurisé de clé Azure et crée les informations d’identification pour vous permettre d’accéder à votre l’archivage sécurisé. Si vous avez examiné les étapes décrites dans la documentation mentionné précédemment en local, vous pouvez voir que cette fonctionnalité automatise les étapes 2 et 3. Vous devez toujours faire manuellement rendues consiste à créer les touches et l’archivage sécurisé clé. À partir de là, la configuration de votre SQL VM entière est automatique. Une fois que cette fonctionnalité a terminé cette configuration, vous pouvez exécuter les instructions T-SQL pour commencer le chiffrement de vos bases de données ou des sauvegardes comme d’habitude.

[AZURE.INCLUDE [AKV Integration Prepare](../../includes/virtual-machines-sql-server-akv-prepare.md)]

## <a name="enabling-and-configuring-akv-integration"></a>L’activation et la configuration de l’intégration AKV
Vous pouvez activer l’intégration AKV pendant la mise en service ou configurez-le pour machines virtuelles existantes.

### <a name="new-vms"></a>Nouvelles machines virtuelles
Si vous êtes mise en service un nouvel ordinateur virtuel de SQL Server avec le Gestionnaire de ressources, le portail Azure fournit une étape pour activer l’intégration de l’archivage sécurisé de clé Azure. La fonctionnalité de l’archivage sécurisé de clé Azure est disponible uniquement pour les entreprise, le développeur et la version d’évaluation de SQL Server.

![Intégration de l’archivage sécurisé clés Azure SQL](./media/virtual-machines-windows-ps-sql-keyvault/azure-sql-arm-akv.png)

Pour des informations détaillées de mise en service, voir [mise en service une machine virtuelle SQL Server dans le portail Azure](virtual-machines-windows-portal-sql-server-provision.md).

### <a name="existing-vms"></a>Machines virtuelles existantes
Pour des machines virtuelles SQL Server existantes, sélectionnez votre machine virtuelle SQL Server. Sélectionnez ensuite la section **configuration de SQL Server** de la cuillère **paramètres** .

![Intégration de AKV SQL pour machines virtuelles existantes](./media/virtual-machines-windows-ps-sql-keyvault/azure-sql-rm-akv-existing-vms.png)

Dans la carte de **configuration SQL Server** , cliquez sur le bouton **Modifier** dans la section Intégration automatique de l’archivage sécurisé clé.

![Configurer l’intégration de AKV SQL pour machines virtuelles existantes](./media/virtual-machines-windows-ps-sql-keyvault/azure-sql-rm-akv-configuration.png)

Lorsque vous avez terminé, cliquez sur le bouton **OK** en bas de la cuillère de **configuration SQL Server** pour enregistrer vos modifications.

>[AZURE.NOTE] Vous pouvez également configurer l’intégration de AKV à l’aide d’un modèle. Pour plus d’informations, voir [démarrage rapide Azure modèle pour l’intégration de l’archivage sécurisé de clé Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-sql-existing-keyvault-update).

[AZURE.INCLUDE [AKV Integration Next Steps](../../includes/virtual-machines-sql-server-akv-next-steps.md)]

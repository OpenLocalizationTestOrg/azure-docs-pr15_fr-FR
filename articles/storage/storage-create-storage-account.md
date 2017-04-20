<properties
    pageTitle="Comment créer, gérer ou supprimer un compte de stockage dans le portail Azure | Microsoft Azure"
    description="Créer un nouveau compte de stockage, gérer les touches d’accès de votre compte ou supprimer un compte de stockage dans le portail Azure. Obtenir des informations à propos des comptes de stockage standard et premium."
    services="storage"
    documentationCenter=""
    authors="robinsh"
    manager="carmonm"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="07/26/2016"
    ms.author="robinsh"/>


# <a name="about-azure-storage-accounts"></a>À propos des comptes de stockage Azure

[AZURE.INCLUDE [storage-selector-portal-create-storage-account](../../includes/storage-selector-portal-create-storage-account.md)]
<br/>
[AZURE.INCLUDE [storage-try-azure-tools](../../includes/storage-try-azure-tools.md)]

## <a name="overview"></a>Vue d’ensemble

Un compte de stockage Azure fournit un espace de noms unique pour stocker et accéder à vos objets de données de stockage Azure. Tous les objets dans un compte de stockage sont facturés ensemble en tant que groupe. Par défaut, les données dans votre compte sont disponibles uniquement pour vous, le propriétaire du compte.

[AZURE.INCLUDE [storage-account-types-include](../../includes/storage-account-types-include.md)]

## <a name="storage-account-billing"></a>Facturation de compte de stockage

[AZURE.INCLUDE [storage-account-billing-include](../../includes/storage-account-billing-include.md)]

> [AZURE.NOTE] Lorsque vous créez une machine virtuelle Azure, un compte de stockage est créé pour vous automatiquement dans l’emplacement de déploiement si vous n’avez pas déjà un compte de stockage à cet emplacement. Il n’est pas nécessaire de suivre les étapes ci-dessous pour créer un compte de stockage pour vos disques machine virtuelle. Le nom de compte de stockage dépendra de nom de machine virtuelle. Consultez la [documentation de Machines virtuelles Azure](https://azure.microsoft.com/documentation/services/virtual-machines/) pour plus d’informations.

## <a name="storage-account-endpoints"></a>Points de terminaison compte de stockage

Chaque objet que vous stockez dans le stockage Azure possède une adresse URL unique. Le nom de compte de stockage des formulaires le sous-domaine de cette adresse. La combinaison de nom de domaine et sous-domaine, qui est spécifique à chaque service, constitue un *point de terminaison* pour votre compte de stockage.

Par exemple, si votre compte de stockage est nommé *mystorageaccount*, les points de terminaison par défaut pour votre compte de stockage sont :

- BLOB service : http://*mystorageaccount*. blob.core.windows.net

- Service de tableau : http://*mystorageaccount*. table.core.windows.net

- Service en file d’attente : http://*mystorageaccount*. queue.core.windows.net

- Service de fichiers : http://*mystorageaccount*. file.core.windows.net

> [AZURE.NOTE] Un compte de stockage Blob expose uniquement le point de terminaison du service Blob.

L’URL permettant d’accéder à un objet dans un compte de stockage est créé en ajoutant l’emplacement de l’objet dans le compte de stockage au point de terminaison. Par exemple, une adresse blob peut avoir ce format : http://*mystorageaccount*.blob.core.windows.net/*mycontainer*/*myblob*.

Vous pouvez également configurer un nom de domaine personnalisé à utiliser avec votre compte de stockage. Pour les comptes de stockage classique, consultez [configurer un nom pour votre point de terminaison du stockage Blob de domaine personnalisé](storage-custom-domain-name.md) pour plus d’informations. Pour les comptes de stockage de gestionnaire de ressources, cette fonctionnalité n’a pas été ajoutée au [portail Azure](https://portal.azure.com) encore, mais vous pouvez le configurer avec PowerShell. Pour plus d’informations, voir l’applet de commande [Set-AzureRmStorageAccount](https://msdn.microsoft.com/library/mt607146.aspx) .  

## <a name="create-a-storage-account"></a>Créer un compte de stockage

1. Connectez-vous au [portail Azure](https://portal.azure.com).

2. Dans le menu concentrateur, sélectionnez **Nouveau** -> **données + stockage** -> **compte de stockage**.

3. Entrez un nom pour votre compte de stockage. Pour plus d’informations sur l’utilisation pour résoudre vos objets dans le stockage Azure le nom de compte de stockage, voir [points de terminaison compte de stockage](#storage-account-endpoints) .

    > [AZURE.NOTE] Noms des comptes de stockage doivent être comprise entre 3 et 24 caractères et peuvent contenir des nombres et les lettres minuscules uniquement.
    >  
    > Nom de votre compte de stockage doit être unique dans Azure. Le portail Azure indique si le nom de compte de stockage que vous sélectionnez est déjà en cours d’utilisation.

4. Spécifier le modèle de déploiement à utiliser : **classique**ou **Le Gestionnaire de ressources** . **Gestionnaire de ressources** est le modèle de déploiement recommandé. Pour plus d’informations, voir [Présentation du gestionnaire ressource déploiement et déploiement classique](../resource-manager-deployment-model.md).

    > [AZURE.NOTE] Comptes de stockage BLOB peuvent être créés uniquement à l’aide du modèle de déploiement Gestionnaire de ressources.

5. Sélectionnez le type de compte de stockage : **objectif général** ou **stockage d’objets Blob**. **Objectif général** est la valeur par défaut.

    Si **l’objectif général** a été sélectionné, puis spécifiez le niveau de performance : **Standard** ou **Premium**. La valeur par défaut est **Standard**. Pour plus d’informations sur les comptes de stockage standard et premium, voir [Introduction à Microsoft Azure stockage](storage-introduction.md) et [stockage Premium : stockage High Performance pour charges de travail Azure Machine virtuelle](storage-premium-storage.md).

    Si le **Stockage Blob** a été activé, puis spécifiez le niveau d’accès : **chaud** ou **pouvant être ajoutées**. La valeur par défaut est **chaud**. Voir [stockage d’objets Blob Azure : refroidir et Hot niveaux](storage-blob-storage-tiers.md) pour plus d’informations.

6. Sélectionnez l’option de réplication pour le compte de stockage : **LRS**, **GRS**, **RA GRS**ou **ZRS**. La valeur par défaut est **RA GRS**. Pour plus d’informations sur les options de réplication de stockage Azure, voir [réplication de stockage Azure](storage-redundancy.md).

7. Sélectionnez l’abonnement dans lequel vous voulez créer le nouveau compte de stockage.

8. Spécifiez un nouveau groupe de ressources ou sélectionnez un groupe de ressources existant. Pour plus d’informations sur les groupes de ressources, voir [vue d’ensemble du Gestionnaire de ressources Azure](../azure-resource-manager/resource-group-overview.md).

9. Sélectionnez l’emplacement géographique de votre compte de stockage. Pour plus d’informations sur les services sont disponibles dans quelle région, consultez [Azure régions](https://azure.microsoft.com/regions/#services) .

10. Cliquez sur **créer** pour créer le compte de stockage.

## <a name="manage-your-storage-account"></a>Gérer votre compte de stockage

### <a name="change-your-account-configuration"></a>Modifier la configuration de votre compte

Après avoir créé votre compte de stockage, vous pouvez modifier sa configuration, telles que la modification de l’option de réplication utilisée pour le compte ou en modifiant le niveau d’accès pour un compte de stockage Blob. Dans le [portail Azure](https://portal.azure.com), accédez à votre compte de stockage, cliquez sur **tous les paramètres** , puis sur **Configuration** pour afficher ou modifier la configuration du compte.

> [AZURE.NOTE] Selon le niveau de performance que vous avez choisi lors de la création du compte de stockage, certaines options de réplication peuvent ne pas être disponibles.

L’option de réplication vous modifiez votre tarifs. Pour plus d’informations, voir la page de [stockage Azure tarifs](https://azure.microsoft.com/pricing/details/storage/) .

Pour les comptes de stockage Blob, modifier le niveau d’accès pouvant entraîner frais pour que la modification en plus de modifier vos tarifs. Voir les [comptes de stockage Blob - tarifs et de facturation](storage-blob-storage-tiers.md#pricing-and-billing) pour plus d’informations.

### <a name="manage-your-storage-access-keys"></a>Gérer vos clés d’accès de stockage

Lorsque vous créez un compte de stockage, Azure génère deux 512 bits stockage touches d’accès rapide, qui sont utilisés pour l’authentification lors de l’accès du compte de stockage. En fournissant deux touches d’accès de stockage, Azure vous permet de régénérer les touches avec sans interruption à votre service de stockage ou l’accès à ce service.

> [AZURE.NOTE] Nous vous recommandons de ne pas partager votre touches d’accès de stockage avec quelqu'un d’autre. Pour autoriser l’accès aux ressources de stockage sans communiquez votre touches d’accès rapide, vous pouvez utiliser une *signature d’un accès partagé*. Une signature accès partagé permet d’accéder à une ressource dans votre compte sur un intervalle que vous définissez et avec les autorisations que vous spécifiez. Pour plus d’informations, reportez-vous [à l’aide de partagés accès Signatures (sa)](storage-dotnet-shared-access-signature-part-1.md) .

#### <a name="view-and-copy-storage-access-keys"></a>Afficher et copier des touches d’accès de stockage

Dans le [portail Azure](https://portal.azure.com), accédez à votre compte de stockage, cliquez sur **tous les paramètres** , puis sur **les touches d’accès** pour afficher, copier et régénérer les touches d’accès de votre compte. La carte de **Touches d’accès rapide** inclut également des chaînes de connexion préconfiguré à l’aide de vos clés principales et secondaires que vous pouvez copier pour utiliser dans vos applications.

#### <a name="regenerate-storage-access-keys"></a>Régénérer les touches d’accès de stockage

Nous vous recommandons de modifier les touches d’accès à votre compte de stockage régulièrement pour préserver la sécurité de vos connexions de stockage. Deux touches d’accès rapide affectés afin que vous pouvez maintenir des connexions pour le compte de stockage en utilisant une touche d’accès rapide tandis que vous régénérez autres touche d’accès rapide.

> [AZURE.WARNING] Régénérer votre touches d’accès rapide peut affecter les services Azure, ainsi que vos propres applications qui ne dépendent du compte de stockage. Tous les clients qui utilisent la touche d’accès rapide pour accéder au compte de stockage doivent être mis à jour pour utiliser la nouvelle clé.

**Media services** - si vous avez des services de support qui dépendent de votre compte de stockage, vous devez resynchronisez les touches d’accès avec votre service de support une fois que vous régénérez les clés.

**Applications** - si vous avez des applications web ou les services en nuage qui utilisent le compte de stockage, vous allez perdre les connexions si vous régénérez les clés, à moins que vous opérationnelle vos clés.

**Stockage Explorers** - si vous utilisez des [applications de l’Explorateur de stockage](storage-explorers.md), vous devrez probablement mettre à jour la clé de stockage utilisée par ces applications.

Voici le processus de rotation de touches d’accès rapide votre stockage :

1. Mettre à jour les chaînes de connexion dans le code de votre application pour faire référence à la touche d’accès secondaire du compte de stockage.

2. Régénérer la clé primaire access pour votre compte de stockage. Sur la carte de **Touches d’accès rapide** , cliquez sur **Régénérer touche1**, puis cliquez sur **Oui** pour confirmer que vous souhaitez générer une nouvelle clé.

3. Mettre à jour les chaînes de connexion dans votre code pour faire référence à la nouvelle clé primaire access.

4. Régénérer la touche d’accès secondaire de la même manière.

## <a name="delete-a-storage-account"></a>Supprimer un compte de stockage

Pour supprimer un compte de stockage que vous utilisez n’est plus, naviguez vers le compte de stockage dans le [portail Azure](https://portal.azure.com), puis cliquez sur **Supprimer**. La suppression d’un compte de stockage supprime l’ensemble du compte, y compris toutes les données dans le compte.

> [AZURE.WARNING] Il n’est pas possible de restaurer un compte de stockage supprimés ou récupérer un des contenus qu’elle contenait avant la suppression. N’oubliez pas de sauvegarder des éléments de que votre choix à enregistrer avant de supprimer le compte. Ceci est également vrai pour toutes les ressources dans le compte, lorsque vous supprimez un blob, table, file d’attente ou fichier, il est définitivement supprimé.

Pour supprimer un compte de stockage associé à une machine virtuelle Azure, vous devez tout d’abord vous assurer que tous les disques machine virtuelle ont été supprimés. Si vous ne supprimez pas tout d’abord vos disques machine virtuelle, puis lorsque vous tentez de supprimer votre compte de stockage, vous verrez un message d’erreur semblable à :

    Failed to delete storage account <vm-storage-account-name>. Unable to delete storage account <vm-storage-account-name>: 'Storage account <vm-storage-account-name> has some active image(s) and/or disk(s). Ensure these image(s) and/or disk(s) are removed before deleting this storage account.'.

Si le compte de stockage utilise le modèle de déploiement classique, vous pouvez supprimer le disque machine virtuelle en procédant comme suit dans le [portail Azure](https://manage.windowsazure.com):

1. Accédez au [portail Azure classique](https://manage.windowsazure.com).
2. Accédez à l’onglet Machines virtuelles.
3. Cliquez sur l’onglet disques.
4. Sélectionnez le disque de données, puis cliquez sur Supprimer le disque.
5. Pour supprimer des images de disque, accédez à l’onglet Images et supprimez toutes les images qui sont stockées dans le compte.

Pour plus d’informations, consultez la [documentation de Machine virtuelle Azure](http://azure.microsoft.com/documentation/services/virtual-machines/).

## <a name="next-steps"></a>Étapes suivantes

- [Stockage Blob Azure : Couches de froid et Hot](storage-blob-storage-tiers.md)
- [Réplication du stockage Azure](storage-redundancy.md)
- [Configurer les chaînes de connexion de stockage Azure](storage-configure-connection-string.md)
- [Transférer des données avec l’utilitaire de ligne de commande AzCopy](storage-use-azcopy.md)
- Visitez le [Blog de l’équipe stockage Azure](http://blogs.msdn.com/b/windowsazurestorage/).

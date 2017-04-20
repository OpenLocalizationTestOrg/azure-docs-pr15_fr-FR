<properties
    pageTitle="Comment créer, gérer ou supprimer un compte de stockage dans le portail classique Azure | Microsoft Azure"
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

Un compte de stockage Azure permet d’accéder aux services Blob Azure, file d’attente, Table et fichier dans le stockage Azure. Votre compte de stockage fournit l’espace de noms unique pour vos objets de données de stockage Azure. Par défaut, les données dans votre compte sont disponibles uniquement pour vous, le propriétaire du compte.

Il existe deux types de comptes de stockage :

- Un compte de stockage standard inclut stockage Blob, Table, file d’attente et de fichier.
- Un compte de stockage premium prend actuellement en charge des disques Azure machine virtuelle uniquement. Voir [stockage Premium : stockage High performance pour charges de travail Azure Machine virtuelle](storage-premium-storage.md) pour une vue d’ensemble détaillée de stockage Premium.

## <a name="storage-account-billing"></a>Facturation de compte de stockage

Vous êtes facturé pour réduire l’utilisation de stockage Azure basée sur votre compte de stockage. Les coûts de stockage basés sur quatre facteurs : capacité de stockage, schéma de réplication, les transactions de stockage et sortie de données.

- Capacité de stockage fait référence à la quantité de votre part de compte de stockage que vous soyez à l’aide pour stocker les données. Le coût de stockage des données est déterminé par la quantité de données que vous stockez et comment il est dupliqué.
- Réplication détermine le nombre de copies de vos données est conservé en même temps et dans les emplacements.
- Les transactions font référence pour tous les lire et écrire des opérations au stockage Azure.
- Sortie de données fait référence aux données transférées à partir d’une région Azure. Lorsque les données dans votre compte de stockage sont consultées par une application qui n’est pas en cours d’exécution dans la même région, si cette application est un service cloud ou tout autre type d’application, vous êtes chargé de sortie de données. (Pour les services Azure, vous pouvez prendre étapes à suivre pour regrouper vos données et services dans le même centre de données pour réduire ou éliminer les frais de sortie de données).  

La page [Tarifs de stockage Azure](https://azure.microsoft.com/pricing/details/storage) fournit des informations détaillées tarification pour la capacité de stockage, réplication et transactions. La page [Détails de tarification pour les transferts de données](https://azure.microsoft.com/pricing/details/data-transfers/) fournit des informations de tarification détaillées de sortie de données.

Pour plus d’informations sur les cibles de capacité et les performances de compte de stockage, voir [extensibilité du stockage Azure et des objectifs de Performance](storage-scalability-targets.md).

> [AZURE.NOTE] Lorsque vous créez une machine virtuelle Azure, un compte de stockage est créé pour vous automatiquement dans l’emplacement de déploiement si vous n’avez pas déjà un compte de stockage à cet emplacement. Il n’est pas nécessaire de suivre les étapes ci-dessous pour créer un compte de stockage pour vos disques machine virtuelle. Le nom de compte de stockage dépendra de nom de machine virtuelle. Consultez la [documentation de Machines virtuelles Azure](https://azure.microsoft.com/documentation/services/virtual-machines/) pour plus d’informations.

## <a name="create-a-storage-account"></a>Créer un compte de stockage

1. Connectez-vous au [portail classique Azure](https://manage.windowsazure.com).

2. Dans la barre des tâches en bas de la page, cliquez sur **Nouveau** . Sélectionnez les **Services Business Data** | **stockage**, puis cliquez sur **Création rapide**.

    ![NewStorageAccount](./media/storage-create-storage-account-classic-portal/storage_NewStorageAccount.png)

3. Dans l' **URL**, entrez un nom pour votre compte de stockage.

    > [AZURE.NOTE] Noms des comptes de stockage doivent être comprise entre 3 et 24 caractères et peuvent contenir des nombres et les lettres minuscules uniquement.
    >  
    > Nom de votre compte de stockage doit être unique dans Azure. Le portail classique Azure indique si le nom de compte de stockage que vous sélectionnez est déjà extrait.

    Voir les [points de terminaison de stockage compte](#storage-account-endpoints) ci-dessous pour plus d’informations sur l’utilisation du nom de compte de stockage pour résoudre vos objets dans le stockage Azure.

4. Dans le **Groupe emplacement/affinité**, sélectionnez un emplacement pour votre compte de stockage à proximité vous ou à vos clients. Si les données dans votre compte de stockage seront accessibles à partir d’un autre service Azure, par exemple une machine virtuelle Azure ou un service cloud, vous souhaiterez peut-être sélectionner un groupe affinité dans la liste pour votre compte de stockage dans le même centre de données avec d’autres services Azure que vous utilisez pour améliorer les performances et réduire les coûts de groupe.

    Notez que vous devez sélectionner un groupe affinité lors de la création de votre compte de stockage. Vous ne pouvez pas déplacer un compte existant à un groupe affinité. Pour plus d’informations sur les groupes affinité, voir [co-création emplacement du Service avec un groupe affinité](#service-co-location-with-an-affinity-group) ci-dessous.

    >[AZURE.IMPORTANT] Pour déterminer les emplacements qui sont disponibles pour votre abonnement, vous pouvez appeler l’opération [répertorier tous les fournisseurs de ressources](https://msdn.microsoft.com/library/azure/dn790524.aspx) . Liste de fournisseurs à partir de PowerShell, appelez [Get-AzureLocation](https://msdn.microsoft.com/library/azure/dn757693.aspx). À partir de .NET, utilisez la méthode de [liste](https://msdn.microsoft.com/library/azure/microsoft.azure.management.resources.provideroperationsextensions.list.aspx) de la classe ProviderOperationsExtensions.
    >
    >En outre, voir [Régions Azure](https://azure.microsoft.com/regions/#services) pour plus d’informations sur les services sont disponibles dans quelle région.


5. Si vous avez plusieurs abonnements Azure, le champ **abonnement** s’affiche. Dans l' **abonnement**, entrez l’abonnement Azure que vous souhaitez utiliser le compte de stockage avec.

6. Dans la **réplication**, sélectionnez le niveau souhaité de la réplication de votre compte de stockage. L’option de réplication recommandée est la réplication geo redondantes, qui fournit la durabilité maximale pour vos données. Pour plus d’informations sur les options de réplication de stockage Azure, voir [réplication de stockage Azure](storage-redundancy.md).

6. Cliquez sur **créer un compte de stockage**.

    Il peut prendre quelques minutes pour créer votre compte de stockage. Pour vérifier l’état, vous pouvez surveiller les notifications en bas du portail classique Azure. Une fois le compte de stockage a été créé, votre nouveau compte de stockage de l’état **en ligne** et est prête à être utilisée.

![StoragePage](./media/storage-create-storage-account-classic-portal/Storage_StoragePage.png)


### <a name="storage-account-endpoints"></a>Points de terminaison compte de stockage

Chaque objet que vous stockez dans le stockage Azure possède une adresse URL unique. Le nom de compte de stockage des formulaires le sous-domaine de cette adresse. La combinaison de nom de domaine et sous-domaine, qui est spécifique à chaque service, constitue un *point de terminaison* pour votre compte de stockage.

Par exemple, si votre compte de stockage est nommé *mystorageaccount*, les points de terminaison par défaut pour votre compte de stockage sont :

- BLOB service : http://*mystorageaccount*. blob.core.windows.net

- Service de tableau : http://*mystorageaccount*. table.core.windows.net

- Service en file d’attente : http://*mystorageaccount*. queue.core.windows.net

- Service de fichiers : http://*mystorageaccount*. file.core.windows.net

Une fois que le compte a été créé, vous pouvez afficher les points de terminaison pour votre compte de stockage sur le tableau de bord de stockage dans le [Portail classique Azure](https://manage.windowsazure.com) .

L’URL permettant d’accéder à un objet dans un compte de stockage est créé en ajoutant l’emplacement de l’objet dans le compte de stockage au point de terminaison. Par exemple, une adresse blob peut avoir ce format : http://*mystorageaccount*.blob.core.windows.net/*mycontainer*/*myblob*.

Vous pouvez également configurer un nom de domaine personnalisé à utiliser avec votre compte de stockage. Pour plus d’informations, voir [configurer un nom de domaine personnalisé pour votre point de terminaison du stockage blob](storage-custom-domain-name.md) .

### <a name="service-co-location-with-an-affinity-group"></a>Emplacement du service co-création avec un groupe affinité

Un *groupe affinité* est un regroupement géographique de vos services Azure et machines virtuelles avec votre compte de stockage Azure. Un groupe affinité peut améliorer les performances de service en recherchant les charges de travail ordinateur dans le même centre de données ou à côté de l’audience utilisateur cible. En outre, sans frais de facturation sont engagés pour sortie lorsque les données dans un compte de stockage sont accessible à partir d’un autre service qui fait partie du même groupe affinité.

> [AZURE.NOTE]  Pour créer un groupe affinité, ouvrez la zone <b>paramètres</b> du [Portail classique Azure](https://manage.windowsazure.com)et cliquez sur <b>affinités</b>, puis cliquez sur <b>Ajouter un groupe affinité</b> ou le bouton <b>Ajouter</b> . Vous pouvez également créer et gérer des groupes d’affinité à l’aide de l’API de gestion du Service Azure. Pour plus d’informations, voir <a href="http://msdn.microsoft.com/library/azure/ee460798.aspx">opérations sur affinités</a> .

## <a name="view-copy-and-regenerate-storage-access-keys"></a>Afficher, copier et régénérer les touches d’accès de stockage

Lorsque vous créez un compte de stockage, Azure génère deux 512 bits stockage touches d’accès rapide, qui sont utilisés pour l’authentification lors de l’accès du compte de stockage. En fournissant deux touches d’accès de stockage, Azure vous permet de régénérer les touches avec sans interruption à votre service de stockage ou l’accès à ce service.

> [AZURE.NOTE] Nous vous recommandons de ne pas partager votre touches d’accès de stockage avec quelqu'un d’autre. Pour autoriser l’accès aux ressources de stockage sans communiquez votre touches d’accès rapide, vous pouvez utiliser une *signature d’un accès partagé*. Une signature accès partagé permet d’accéder à une ressource dans votre compte sur un intervalle que vous définissez et avec les autorisations que vous spécifiez. Pour plus d’informations, reportez-vous [à l’aide de partagés accès Signatures (sa)](storage-dotnet-shared-access-signature-part-1.md) .

Dans le [Portail classique Azure](https://manage.windowsazure.com), permet de **Gérer les clés** sur le tableau de bord ou de la page de **stockage** afficher, copier et régénérer les touches d’accès de stockage qui sont utilisées pour accéder aux services d’objets Blob, la Table et file d’attente.

### <a name="copy-a-storage-access-key"></a>Copier une touche d’accès de stockage  

Vous pouvez utiliser la **Gestion des clés** pour copier une touche d’accès rapide de stockage à utiliser dans une chaîne de connexion. La chaîne de connexion nécessite le nom de compte de stockage et une clé à utiliser dans l’authentification. Pour plus d’informations sur la configuration des chaînes de connexion pour accéder aux services de stockage Azure, voir [Configurer les chaînes de connexion stockage Azure](storage-configure-connection-string.md).

1. Dans le [Portail classique Azure](https://manage.windowsazure.com), cliquez sur **espace de stockage**, puis cliquez sur le nom du compte de stockage pour ouvrir le tableau de bord.

2. Cliquez sur **Gérer les clés**.

    **Gérer les touches d’accès** s’ouvre.

    ![Managekeys](./media/storage-create-storage-account-classic-portal/Storage_ManageKeys.png)


3. Pour copier une touche d’accès de stockage, sélectionnez le texte de touche. Puis avec le bouton droit, puis cliquez sur **Copier**.

### <a name="regenerate-storage-access-keys"></a>Régénérer les touches d’accès de stockage
Nous vous recommandons de modifier les touches d’accès à votre compte de stockage régulièrement pour préserver la sécurité de vos connexions de stockage. Deux touches d’accès rapide affectés afin que vous pouvez maintenir des connexions pour le compte de stockage en utilisant une touche d’accès rapide tandis que vous régénérez autres touche d’accès rapide.

> [AZURE.WARNING] Régénérer votre touches d’accès rapide peut affecter les services Azure, ainsi que vos propres applications qui ne dépendent du compte de stockage. Tous les clients qui utilisent la touche d’accès rapide pour accéder au compte de stockage doivent être mis à jour pour utiliser la nouvelle clé.

**Media services** - si vous avez des services de support qui dépendent de votre compte de stockage, vous devez resynchronisez les touches d’accès avec votre service de support une fois que vous régénérez les clés.

**Applications** - si vous avez des applications web ou les services en nuage qui utilisent le compte de stockage, vous allez perdre les connexions si vous régénérez les clés, à moins que vous opérationnelle vos clés. 

**Stockage Explorers** - si vous utilisez des [applications de l’Explorateur de stockage](storage-explorers.md), vous devrez probablement mettre à jour la clé de stockage utilisée par ces applications.

Voici le processus de rotation de touches d’accès rapide votre stockage :

1. Mettre à jour les chaînes de connexion dans le code de votre application pour faire référence à la touche d’accès secondaire du compte de stockage.

2. Régénérer la clé primaire access pour votre compte de stockage. Dans le [Portail classique Azure](https://manage.windowsazure.com), à partir du tableau de bord ou de la page **configurer** , cliquez sur **Gérer les clés**. Cliquez sur **Régénérer** sous la clé primaire access, puis cliquez sur **Oui** pour confirmer que vous souhaitez générer une nouvelle clé.

3. Mettre à jour les chaînes de connexion dans votre code pour faire référence à la nouvelle clé primaire access.

4. Régénérer la touche d’accès secondaire.

## <a name="delete-a-storage-account"></a>Supprimer un compte de stockage

Pour supprimer un compte de stockage que vous utilisez n’est plus, cliquer sur **Supprimer** sur le tableau de bord ou de la page **configurer** . **Supprimer** supprime le compte de stockage dans son intégralité, y compris tous les objets BLOB, les tables et les files d’attente dans le compte.

> [AZURE.WARNING] Il n’est pas possible de restaurer un compte de stockage supprimés ou récupérer un des contenus qu’elle contenait avant la suppression. N’oubliez pas de sauvegarder des éléments de que votre choix à enregistrer avant de supprimer le compte. Ceci est également vrai pour toutes les ressources dans le compte, lorsque vous supprimez un blob, table, file d’attente ou fichier, il est définitivement supprimé.
>
> Si votre compte de stockage contient des fichiers de disque dur virtuel pour une machine virtuelle Azure, vous devez supprimer les images et des disques qui utilisent les fichiers de disque dur virtuel avant de pouvoir supprimer le compte de stockage. Tout d’abord, arrêter la machine virtuelle si elle est en cours d’exécution, puis supprimez-le. Pour supprimer des disques, accédez à l’onglet **disques** et supprimer tous les disques il. Pour supprimer des images, accédez à l’onglet **Images** et supprimez toutes les images qui sont stockées dans le compte.

1. Dans le [Portail classique Azure](https://manage.windowsazure.com), cliquez sur **espace de stockage**.

2. Cliquez n’importe où dans l’écriture de compte de stockage, sauf le nom, puis cliquez sur **Supprimer**.

     - Ou -

    Cliquez sur le nom du compte de stockage pour ouvrir le tableau de bord, puis cliquez sur **Supprimer**.

3. Cliquez sur **Oui** pour confirmer que vous voulez supprimer le compte de stockage.

## <a name="next-steps"></a>Étapes suivantes

- Pour en savoir plus sur le stockage Azure, consultez la [documentation du stockage Azure](https://azure.microsoft.com/documentation/services/storage/).
- Visitez le [Blog de l’équipe stockage Azure](http://blogs.msdn.com/b/windowsazurestorage/).
- [Transférer des données avec l’utilitaire de ligne de commande AzCopy](storage-use-azcopy.md)

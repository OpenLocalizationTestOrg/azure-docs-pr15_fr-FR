<properties
    pageTitle="Créer un compte Azure lot | Microsoft Azure"
    description="Apprenez à créer un compte Azure lot dans le portail Azure pour exécuter les charges de travail en parallèle à grande échelle dans le cloud"
    services="batch"
    documentationCenter=""
    authors="mmacy"
    manager="timlt"
    editor=""/>

<tags
    ms.service="batch"
    ms.workload="big-compute"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="09/21/2016"
    ms.author="marsma"/>

# <a name="create-an-azure-batch-account-using-the-azure-portal"></a>Créer un compte Azure lot à l’aide du portail Azure

> [AZURE.SELECTOR]
- [Portail Azure](batch-account-create-portal.md)
- [Gestion par lots .NET](batch-management-dotnet.md)

Apprenez à créer un compte Azure lot dans le [portail Azure][azure_portal]et où la trouver importantes propriétés du compte tels que les touches d’accès et l’URL du compte. Abordent également lot tarifs et liaison d’un compte de stockage Azure à votre compte lot afin que vous puissiez utiliser [des packages d’application](batch-application-packages.md) et [conserver la sortie de projet et la tâche](batch-task-output.md).

## <a name="create-a-batch-account"></a>Créer un compte lot

1. Connectez-vous au [portail Azure][azure_portal].

2. Cliquez sur **Nouveau** > **Calculer** > **lot Service**.

    ![Traitement par lots sur le marché][marketplace_portal]

3. La carte de **Nouveau lot de compte** s’affiche. Voir éléments *un* à *e* ci-dessous pour obtenir une description de chaque élément de la carte.

    ![Créer un compte lot][account_portal]

    un. **Nom de compte**: un nom unique pour votre compte lot. Ce nom doit être unique au sein de la région Azure que le compte est créé (voir *emplacement* ci-dessous). Il peut contenir uniquement les caractères en minuscules, nombres et doit être 3-24 caractères.

    b. **Abonnement**: un abonnement dans lequel vous souhaitez créer le compte lot. Si vous avez un abonnement, il est sélectionné par défaut.

    c. **Groupe de ressources**: une ressource existante grouper pour votre nouveau compte lot, ou vous pouvez également créer un autre.

    d. **Emplacement**: région un Azure dans lequel vous souhaitez créer le compte lot. Seules les régions pris en charge par votre abonnement et le groupe de ressources sont affichées dans les options.

    e. **Compte de stockage** (facultatif) : un compte de stockage **objectif général** vous associez (lien) vers votre nouveau compte lot. Pour plus d’informations, voir [stockage Azure compte](#linked-azure-storage-account) ci-dessous.

4. Cliquez sur **créer** pour créer le compte.

  Le portail indique qu’il est **déploiement** du compte, et à la fin, une notification **déploiements a réussi** s’affiche dans *les Notifications*.

## <a name="view-batch-account-properties"></a>Afficher les propriétés du compte par lots

Une fois que le compte a été créé, vous pouvez ouvrir la **carte de compte lot** pour accéder à ses paramètres et les propriétés. Vous pouvez accéder toutes les propriétés et les paramètres du compte en utilisant le menu de gauche de la cuillère compte lot.

![Carte de compte lot portail Azure][account_blade]

* **URL de compte lot**: Applications que vous créez avec le [développement du lot API](batch-technical-overview.md#batch-development-apis) besoin d’une URL de compte pour gérer les ressources et exécuter des tâches dans le compte. Une URL de compte lot a le format suivant :

    `https://<account_name>.<region>.batch.azure.com`

![URL de compte lot portail][account_url]

* **Touches d’accès rapide**: vos applications doivent également une touche d’accès lors de l’utilisation des ressources de votre compte lot. Pour afficher ou régénérer les touches d’accès rapide de votre compte lot, entrez `keys` dans la zone de **recherche** de menu de gauche dans la carte de compte lot, puis sélectionnez **clés**.

    ![Touches de compte lot portail Azure][account_keys]

## <a name="pricing"></a>Tarifs

Comptes lot sont proposés uniquement dans un « niveau gratuit » ce qui signifie que vous ne sont pas payer pour le compte lot lui-même. Vous êtes chargé pour les ressources cluster Azure sous-jacentes qui utilisent vos solutions lot et pour les ressources utilisées par d’autres services lors de l’exécution de vos charges de travail. Par exemple, vous êtes chargé pour les nœuds de calcul dans vos pools et pour les données stockés dans le stockage Azure comme entrée ou de sortie pour vos tâches. De même, si vous utilisez la fonctionnalité [packages d’application](batch-application-packages.md) du lot, vous êtes chargé pour les ressources de stockage Azure utilisés pour stocker vos packages d’application. Consultez [lot tarification] [ batch_pricing] pour plus d’informations.

## <a name="linked-azure-storage-account"></a>Compte de stockage Azure lié

Comme mentionné précédemment, vous pouvez (le cas échéant) lier un compte de stockage **objectif général** à votre compte lot. La fonctionnalité [packages d’application](batch-application-packages.md) du lot utilise le stockage blob dans un objectif général lié compte de stockage, comme le fait la bibliothèque [Lot fichier Conventions .NET](batch-task-output.md) . Ces fonctionnalités facultatives vous aideront à déployer les applications qu'exécuteront vos tâches lot et conserver les données qu’ils produisent.

Traitement par lots actuellement prend en charge *uniquement* le type de compte de stockage **objectif général** comme décrit à l’étape 5, [créer un compte de stockage](../storage/storage-create-storage-account.md#create-a-storage-account), dans [les comptes de stockage sur Azure](../storage/storage-create-storage-account.md). Lorsque vous liez un compte de stockage Azure à votre compte lot, être que lien *seulement* un compte de stockage **objectif général** .

![Création d’un compte de stockage « Usage général »][storage_account]

Nous vous conseillons de créer un compte de stockage pour une utilisation exclusive par votre compte lot.

>[AZURE.WARNING] Veillez à lorsque régénérer les touches d’accès d’un compte de stockage lié. Régénérer qu’une clé de compte de stockage et cliquez sur la carte de compte de stockage liée **Touches de synchronisation** . Attendez cinq minutes pour des clés de Registre se propager aux nœuds de calcul dans vos pools, puis régénérer et synchroniser l’autre clé si nécessaire. Si vous régénérez les deux clés en même temps, les nœuds de votre calcul ne pourront pas synchroniser sur les clés, et elles perdront l’accès au compte de stockage.

  ![Régénérer les clés de compte de stockage][4]

## <a name="batch-service-quotas-and-limits"></a>Spécifications et limites quotas du service par lots

Sachez que comme avec votre abonnement Azure et d’autres services Azure, certaines [limites et les quotas](batch-quota-limit.md) s’appliquent aux comptes lot. Les quotas d’en cours d’un compte lot s’affichent dans le portail dans le **Propriétés**du compte.

![Compte les quotas de lot portail Azure][quotas]

N’oubliez pas ces quotas que vous concevez et mise à l’échelle des vos charges de travail lot. Par exemple, si votre pool n’est pas atteindre le nombre cible de nœuds de calcul que vous avez spécifiée, vous pourriez avez atteint la limite de quota core pour votre compte lot.

Notez également que vous n’êtes pas limité à un seul compte lot pour votre abonnement Azure. Vous pouvez exécuter plusieurs charges de travail lot dans un seul compte lot, ou distribuer vos charges de travail entre les comptes du lot dans le même abonnement, mais dans différentes régions Azure.

La plupart de ces quotas peuvent être augmentées simplement avec une demande de support de produits gratuites soumise dans le portail Azure. Pour plus d’informations sur la demande augmentations de quota, voir [les Quotas et les limites pour le service lot Azure](batch-quota-limit.md) .

## <a name="other-batch-account-management-options"></a>Autres options de gestion de compte par lots

Outre l’utilisation du portail Azure, vous pouvez également créer et gérer les comptes lot avec les éléments suivants :

* [Applets de commande PowerShell lot](batch-powershell-cmdlets-get-started.md)
* [Azure infrastructure du langage commun](../xplat-cli-install.md)
* [Gestion par lots .NET](batch-management-dotnet.md)

## <a name="next-steps"></a>Étapes suivantes

* Afficher un [Aperçu des fonctionnalités Azure lot](batch-api-basics.md) pour en savoir plus sur les fonctionnalités et les concepts de service lot. L’article aborde les principales ressources lot tels que des pools, les nœuds de calcul, les tâches et les tâches et fournit une vue d’ensemble des fonctionnalités du service qui permettent à l’exécution de la charge de travail informatique à grande échelle.

* Découvrez les principes fondamentaux de développement d’une application compatibles avec les commandes à l’aide de la [bibliothèque cliente .NET lot](batch-dotnet-get-started.md). L' [article Introduction](batch-dotnet-get-started.md) vous guide dans une application de travail qui utilise le service lot pour exécuter une charge de travail sur plusieurs nœuds de calcul, ainsi que l’utilisation du stockage Azure pour transfert de fichier de la charge de travail et la récupération.

[api_net]: https://msdn.microsoft.com/library/azure/mt348682.aspx
[api_rest]: https://msdn.microsoft.com/library/azure/Dn820158.aspx

[azure_portal]: https://portal.azure.com
[batch_pricing]: https://azure.microsoft.com/pricing/details/batch/

[4]: ./media/batch-account-create-portal/batch_acct_04.png "Régénérer les clés de compte de stockage"
[marketplace_portal]: ./media/batch-account-create-portal/marketplace_batch.PNG
[account_blade]: ./media/batch-account-create-portal/batch_blade.png
[account_portal]: ./media/batch-account-create-portal/batch_acct_portal.png
[account_keys]: ./media/batch-account-create-portal/account_keys.PNG
[account_url]: ./media/batch-account-create-portal/account_url.png
[storage_account]: ./media/batch-account-create-portal/storage_account.png
[quotas]: ./media/batch-account-create-portal/quotas.png

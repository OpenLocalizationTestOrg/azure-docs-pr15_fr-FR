<properties
   pageTitle="Prise en main Azure lot PowerShell | Microsoft Azure"
   description="Obtenir une brève introduction aux applets de commande PowerShell Azure que vous pouvez utiliser pour gérer le service lot Azure"
   services="batch"
   documentationCenter=""
   authors="mmacy"
   manager="timlt"
   editor=""/>

<tags
   ms.service="batch"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="powershell"
   ms.workload="big-compute"
   ms.date="10/20/2016"
   ms.author="marsma"/>

# <a name="get-started-with-azure-batch-powershell-cmdlets"></a>Prise en main applets de commande PowerShell de lot Azure
Avec les applets de commande PowerShell de lot Azure, vous pouvez effectuer et de script de nombreuses tâches mêmes que procéder avec l’API lot, le portail Azure et l’Interface de ligne de commande Azure (CLI). Il s’agit d’une brève introduction aux applets de commande que vous pouvez utiliser pour gérer vos comptes lot et utiliser les ressources de votre lot tels que des pools, des projets et des tâches.

Pour une liste complète des applets de commande par lots et la syntaxe de la cmdlet détaillées, voir le [lot Azure applet de commande référence](https://msdn.microsoft.com/library/azure/mt125957.aspx).

Cet article est basé sur les applets de commande PowerShell Azure version 3.0.0. Nous vous recommandons que vous mettez à jour votre PowerShell Azure fréquemment afin de tirer parti des améliorations et mises à jour de service.

## <a name="prerequisites"></a>Conditions préalables

Effectuer les opérations suivantes pour utiliser Azure PowerShell pour gérer vos ressources lot.

* [Installer et configurer PowerShell Azure](../powershell-install-configure.md)

* Exécutez l’applet de commande de **Connexion AzureRmAccount** pour vous connecter à votre abonnement (la lot Azure applets de commande d’expédition dans le module Azure le Gestionnaire de ressources) :

    `Login-AzureRmAccount`

* **Inscrire auprès de l’espace de noms de fournisseur de lot**. Cette opération ne doit être effectuée **une fois par abonnement**.

    `Register-AzureRMResourceProvider -ProviderNamespace Microsoft.Batch`

## <a name="manage-batch-accounts-and-keys"></a>Gérer les comptes lot et les touches

### <a name="create-a-batch-account"></a>Créer un compte lot

**Nouveau AzureRmBatchAccount** crée un compte lot dans un groupe de ressources spécifié. Si vous n’avez pas un groupe de ressources, créez-le en exécutant l’applet de commande [New-AzureRmResourceGroup](https://msdn.microsoft.com/library/azure/mt603739.aspx) . Spécifier l’une des régions Azure dans le paramètre **d’emplacement** , tel que « US Central ». Par exemple :

    New-AzureRmResourceGroup –Name MyBatchResourceGroup –location "Central US"

Ensuite, créez un compte de traitement par lots dans le groupe de ressources, spécifiez un nom pour le compte dans <*nom_de_compte*> et l’emplacement et le nom de votre groupe de ressources. Création d’un compte lot peut prendre un certain temps pour terminer. Par exemple :

    New-AzureRmBatchAccount –AccountName <account_name> –Location "Central US" –ResourceGroupName <res_group_name>

> [AZURE.NOTE] Le nom de compte doit être unique à la région Azure pour le groupe de ressources, contenir entre 3 et 24 caractères et utiliser les lettres minuscules et que des nombres.

### <a name="get-account-access-keys"></a>Obtenir les touches d’accès compte
**Get-AzureRmBatchAccountKeys** indique les touches d’accès associés à un compte Azure lot. Par exemple, exécutez la procédure suivante pour obtenir les clés principales et secondaires du compte que vous avez créé.

    $Account = Get-AzureRmBatchAccountKeys –AccountName <account_name>

    $Account.PrimaryAccountKey

    $Account.SecondaryAccountKey

### <a name="generate-a-new-access-key"></a>Générer une nouvelle clé d’accès
**Nouveau AzureRmBatchAccountKey** génère une nouvelle clé de compte principal ou secondaire pour un compte Azure lot. Par exemple, pour générer une nouvelle clé primaire pour votre compte de commandes, tapez :

    New-AzureRmBatchAccountKey -AccountName <account_name> -KeyType Primary

> [AZURE.NOTE] Pour générer une nouvelle clé secondaire, spécifiez « Secondaire » pour le paramètre de **type de clé** . Vous devez régénérer les clés principales et secondaires séparément.

### <a name="delete-a-batch-account"></a>Supprimer un compte par lots
**Supprimer AzureRmBatchAccount** supprime un compte lot. Par exemple :

    Remove-AzureRmBatchAccount -AccountName <account_name>

Lorsque vous y êtes invité, confirmez que vous voulez supprimer le compte. Suppression de compte peut prendre un certain temps pour terminer.

## <a name="create-a-batchaccountcontext-object"></a>Créer un objet BatchAccountContext

Pour s’authentifier en utilisant les applets de commande PowerShell lot lorsque vous créez et gérez des pools lot, tâches, tâches et autres ressources, commencez par créer un objet BatchAccountContext pour stocker votre nom de compte et des clés :

    $context = Get-AzureRmBatchAccountKeys -AccountName <account_name>

Vous passez l’objet BatchAccountContext dans les applets de commande qui utilisent le paramètre **BatchContext** .

> [AZURE.NOTE] Par défaut, la clé primaire d’un compte est utilisé pour l’authentification, mais vous pouvez sélectionner la clé à utiliser en modifiant **KeyInUse** propriété l’objet de votre BatchAccountContext : `$context.KeyInUse = "Secondary"`.

## <a name="create-and-modify-batch-resources"></a>Créer et modifier des ressources de traitement par lots
Utiliser les applets de commande tels que **Nouveau AzureBatchPool** **Nouveau AzureBatchJob**et **AzureBatchTask de nouveau** pour créer des ressources sous un compte lot. Il existe correspondants **Get -** et applets de commande **Set-** mettre à jour les propriétés des ressources existantes et **Supprimer-** applets de commande pour supprimer des ressources sous un compte lot.

Lors de l’utilisation de la plupart de ces applets de commande, en plus de passage d’un objet BatchContext, vous devez créer ou passer des objets qui contiennent des paramètres de ressources détaillées, comme illustré dans l’exemple suivant. Consultez l’aide détaillée de chaque applet de commande pour obtenir des exemples supplémentaires.

### <a name="create-a-batch-pool"></a>Créer un pool de traitement par lots

Lorsque vous créez ou mettre à jour une liste de commandes, vous sélectionnez une configuration de service cloud ou une machine virtuelle pour le système d’exploitation sur les nœuds de calcul (voir [vue d’ensemble de la fonctionnalité lot](batch-api-basics.md#pool)). Votre choix détermine si votre nœuds de calcul sont mis en image avec l’un des [mises à jour de système d’exploitation invité de Azure](../cloud-services/cloud-services-guestos-update-matrix.md#releases) ou avec l’une des images Linux ou machine virtuelle Windows pris en charge dans les Azure Marketplace.

Lorsque vous exécutez **New-AzureBatchPool**, transmettre les paramètres de système d’exploitation dans un objet PSCloudServiceConfiguration ou PSVirtualMachineConfiguration. Par exemple, l’applet de commande suivante crée un nouveau pool de lot avec des nœuds de cluster de petite taille dans la configuration du service cloud, créer une image avec la dernière version du système d’exploitation de famille 3 (Windows Server 2012). Ici, le paramètre **CloudServiceConfiguration** spécifie la variable *$configuration* que l’objet PSCloudServiceConfiguration. Le paramètre **BatchContext** spécifie une variable défini précédemment *$context* que l’objet BatchAccountContext.

    $configuration = New-Object -TypeName "Microsoft.Azure.Commands.Batch.Models.PSCloudServiceConfiguration" -ArgumentList @(4,"*")

    New-AzureBatchPool -Id "AutoScalePool" -VirtualMachineSize "Small" -CloudServiceConfiguration $configuration -AutoScaleFormula '$TargetDedicated=4;' -BatchContext $context

Le nombre cible des nœuds de calcul dans le nouveau pool est déterminé par une formule autoscaling. Dans ce cas, la formule est simplement **$TargetDedicated = 4**, qui indique le nombre de nœuds de calcul dans le pool est 4 au maximum.

## <a name="query-for-pools-jobs-tasks-and-other-details"></a>Requête pour des pools, tâches, tâches et autres informations

Utiliser les applets de commande tels que **Get-AzureBatchPool** **Get-AzureBatchJob**et **Get-AzureBatchTask** à requête pour entités créées sous un compte lot.

### <a name="query-for-data"></a>Requête de données

Par exemple, utilisez **Get-AzureBatchPools** pour trouver vos pools. Par défaut cette requêtes pour tous les pools sous votre compte, en supposant que vous déjà stocké l’objet BatchAccountContext dans *$context*:

    Get-AzureBatchPool -BatchContext $context

### <a name="use-an-odata-filter"></a>Utiliser un filtre d’OData

Vous pouvez fournir un filtre OData en utilisant le paramètre de **filtre** pour rechercher uniquement les objets que qui vous intéresse. Par exemple, vous pouvez trouver tous les pools avec ID commençant par « monpool » :

    $filter = "startswith(id,'myPool')"

    Get-AzureBatchPool -Filter $filter -BatchContext $context

Cette méthode n’est pas aussi flexible que l’utilisation de « Where-Object » dans un pipeline local. Toutefois, la requête est envoyée au service lot directement afin que tout le filtrage qui se passe sur le côté serveur, l’enregistrement de la bande passante Internet.

### <a name="use-the-id-parameter"></a>Utiliser le paramètre d’Id

Une suggestion pour un filtre d’OData consiste à utiliser le paramètre **Id** . Pour rechercher un pool spécifique avec id « monpool » :

    Get-AzureBatchPool -Id "myPool" -BatchContext $context

Le paramètre **Id** prend en charge uniquement recherche d’id de plein, pas de caractères génériques ou filtres de style OData.

### <a name="use-the-maxcount-parameter"></a>Utilisez le paramètre MaxCount

Par défaut, chaque applet de commande renvoie un maximum de 1000 objets. Si vous atteignez cette limite, affiner davantage le filtre pour mettre en moins d’objets ou définis explicitement en utilisant le paramètre **MaxCount** au maximum. Par exemple :

    Get-AzureBatchTask -MaxCount 2500 -BatchContext $context

Pour supprimer la limite supérieure, définissez **MaxCount** ou égale à 0.

### <a name="use-the-powershell-pipeline"></a>Utiliser le pipeline de PowerShell

Applets de commande lot peuvent tirer parti du pipeline de PowerShell pour envoyer des données entre les applets de commande. Cela a le même effet en tant que spécification d’un paramètre, mais vous permet de travailler avec plusieurs entités plus facilement.

Par exemple, rechercher et afficher toutes les tâches sous votre compte :

    Get-AzureBatchJob -BatchContext $context | Get-AzureBatchTask -BatchContext $context

Redémarrer (redémarrage) chaque nœud de calcul dans un pool :

    Get-AzureBatchComputeNode -PoolId "myPool" -BatchContext $context | Restart-AzureBatchComputeNode -BatchContext $context

## <a name="application-package-management"></a>Gestion de package d’application

Packages d’application fournissent un moyen simple de déployer des applications sur les nœuds de calcul dans vos pools. Avec les applets de commande PowerShell lot, vous pouvez télécharger des packages d’application de gestion de votre compte lot et déployer les versions de package pour les nœuds de calcul.

**Créer** une application :

    New-AzureRmBatchApplication -AccountName <account_name> -ResourceGroupName <res_group_name> -ApplicationId "MyBatchApplication"

**Ajouter** un package d’application :

    New-AzureRmBatchApplicationPackage -AccountName <account_name> -ResourceGroupName <res_group_name> -ApplicationId "MyBatchApplication" -ApplicationVersion "1.0" -Format zip -FilePath package001.zip

Définir la **version par défaut** pour l’application :

    Set-AzureRmBatchApplication -AccountName <account_name> -ResourceGroupName <res_group_name> -ApplicationId "MyBatchApplication" -DefaultVersion "1.0"

**Liste des** packages d’une application

    $application = Get-AzureRmBatchApplication -AccountName <account_name> -ResourceGroupName <res_group_name> -ApplicationId "MyBatchApplication"

    $application.ApplicationPackages

**Supprimer** un package d’application

    Remove-AzureRmBatchApplicationPackage -AccountName <account_name> -ResourceGroupName <res_group_name> -ApplicationId "MyBatchApplication" -ApplicationVersion "1.0"

**Supprimer** une application

    Remove-AzureRmBatchApplication -AccountName <account_name> -ResourceGroupName <res_group_name> -ApplicationId "MyBatchApplication"

>[AZURE.NOTE] Vous devez supprimer toutes les versions de package d’application d’une application avant de supprimer l’application. Vous recevrez une erreur « Conflit » si vous tentez de supprimer une application ayant des packages d’application.

### <a name="deploy-an-application-package"></a>Déployer un package d’application

Vous pouvez spécifier une ou plusieurs des packages d’application pour le déploiement lorsque vous créez un pool. Lorsque vous spécifiez un package au moment de la création de pool, il est déployé sur chaque nœud que le pool de jointures nœud. Les packages sont également déployés lorsqu’un nœud est redémarré ou que.

Spécifier la `-ApplicationPackageReference` option lors de la création d’un pool pour déployer un package d’application sur les nœuds du pool dès qu’ils rejoignent le pool. Tout d’abord, créez un objet **PSApplicationPackageReference** et configurez-le avec la version Id et package d’application que vous voulez procéder au déploiement nœuds de calcul du pool :

    $appPackageReference = New-Object Microsoft.Azure.Commands.Batch.Models.PSApplicationPackageReference

    $appPackageReference.ApplicationId = "MyBatchApplication"

    $appPackageReference.Version = "1.0"

Maintenant créer le pool, puis spécifiez l’objet de référence package comme argument de la `ApplicationPackageReferences` option :

    New-AzureBatchPool -Id "PoolWithAppPackage" -VirtualMachineSize "Small" -CloudServiceConfiguration $configuration -BatchContext $context -ApplicationPackageReferences $appPackageReference

Vous trouverez plus d’informations sur les packages d’application dans [le déploiement d’applications avec Azure lot des packages d’application](batch-application-packages.md).

>[AZURE.IMPORTANT] Vous devez [lier un compte de stockage Azure](#linked-storage-account-autostorage) à votre compte lot à utiliser des packages d’application.

### <a name="update-a-pools-application-packages"></a>Mettre à jour des packages d’application d’un pool

Pour mettre à jour les applications attribuées à un pool existant, commencez par créer un objet PSApplicationPackageReference avec les propriétés souhaitées (version Id et package d’application) :

    $appPackageReference = New-Object Microsoft.Azure.Commands.Batch.Models.PSApplicationPackageReference

    $appPackageReference.ApplicationId = "MyBatchApplication"

    $appPackageReference.Version = "2.0"

Ensuite, le pool à partir de, effacer tout package existant, ajouter une référence de package notre nouveau et le mettre à jour le service lot avec les nouveaux paramètres du pool :

    $pool = Get-AzureBatchPool -BatchContext $context -Id "PoolWithAppPackage"

    $pool.ApplicationPackageReferences.Clear()

    $pool.ApplicationPackageReferences.Add($appPackageReference)

    Set-AzureBatchPool -BatchContext $context -Pool $pool

Vous avez maintenant mis à jour les propriétés du pool dans le service lot. Toutefois, pour déployer le nouveau package d’application pour les nœuds dans le pool de calcul, vous devez redémarrer ou recréez ces nœuds. Vous pouvez redémarrer tous les nœuds dans un pool avec cette commande :

    Get-AzureBatchComputeNode -PoolId "PoolWithAppPackage" -BatchContext $context | Restart-AzureBatchComputeNode -BatchContext $context

>[AZURE.TIP] Vous pouvez déployer plusieurs packages d’application sur les nœuds de calcul dans un pool. Si vous voulez *Ajouter* un package d’application au lieu de remplacer les packages actuellement déployés, omettez le `$pool.ApplicationPackageReferences.Clear()` ligne ci-dessus.

## <a name="next-steps"></a>Étapes suivantes

* Pour la syntaxe de la cmdlet détaillées et des exemples, voir [informations de référence applet de commande lot Azure](https://msdn.microsoft.com/library/azure/mt125957.aspx).

* Pour plus d’informations sur les applications et packages d’application dans le lot, voir [déploiement d’applications avec Azure lot des packages d’application](batch-application-packages.md).

<properties
    pageTitle="Installation simple dans les applications et la gestion d’Azure lot | Microsoft Azure"
    description="Utiliser la fonctionnalité de packages d’application du lot Azure pour gérer facilement plusieurs applications et versions pour l’installation sur lot les nœuds de calcul."
    services="batch"
    documentationCenter=".net"
    authors="mmacy"
    manager="timlt"
    editor="" />

<tags
    ms.service="batch"
    ms.devlang="multiple"
    ms.topic="article"
    ms.tgt_pltfrm="vm-windows"
    ms.workload="big-compute"
    ms.date="10/21/2016"
    ms.author="marsma" />

# <a name="application-deployment-with-azure-batch-application-packages"></a>Déploiement d’applications avec des packages d’application lot Azure

La fonctionnalité de packages d’application du lot Azure fournit faciliter la gestion des demandes de tâche et leur déploiement aux nœuds de calcul dans votre liste. Avec des packages d’application, vous pouvez télécharger et gérer plusieurs versions d’applications que vos tâches effectuées, y compris ses fichiers de prise en charge. Vous pouvez ensuite automatiquement déployer une ou plusieurs de ces applications aux nœuds de calcul dans votre liste.

Dans cet article, vous allez apprendre à télécharger et gérer des packages d’application dans le portail Azure. Vous allez apprendre puis de les installer sur des nœuds de calcul d’un pool avec [Lot .NET] [ api_net] bibliothèque.

> [AZURE.NOTE] La fonctionnalité de packages d’application décrite ici remplace la fonctionnalité « Lot applications » disponible dans les versions précédentes du service.

## <a name="application-package-requirements"></a>Spécifications de package d’application

Vous devez [lier un compte de stockage Azure](#link-a-storage-account) à votre compte lot à utiliser des packages d’application.

La fonctionnalité de packages d’application mentionnée dans cet article est compatible *uniquement* avec des pools lot qui ont été créées après 10 mars 2016. Packages d’application ne seront pas déployés pour calculer des nœuds dans des pools créées avant cette date.

Cette fonctionnalité a été introduite dans [L’API REST lot] [ api_rest] version 2015-12-01.2.2). et correspondante [Lot .NET] [ api_net] version 3.1.0 de la bibliothèque. Nous vous recommandons de toujours utiliser la dernière version de l’API lorsque vous travaillez avec des lots.

> [AZURE.IMPORTANT] Pour l’instant, seuls les pools *CloudServiceConfiguration* prend en charge des packages d’application. Vous ne pouvez pas utiliser des packages d’Application dans des pools créés par l’utilisation d’images VirtualMachineConfiguration. Consultez la section [configuration de machine virtuelle](batch-linux-nodes.md#virtual-machine-configuration) de [nœuds dans des pools d’Azure lot de calcul Linux de mise en service](batch-linux-nodes.md) pour plus d’informations sur les deux configurations différentes.

## <a name="about-applications-and-application-packages"></a>À propos des applications et des packages d’application

Dans Azure lot, une *application* fait référence à un ensemble de versions binaires qui peuvent être téléchargés automatiquement aux nœuds de calcul dans votre liste. Un *package d’application* fait référence à un *ensemble spécifique* de ces fichiers binaires et représente une donnée *version* de l’application.

![Diagramme de haut niveau des applications et des packages d’application][1]

### <a name="applications"></a>Applications

Une application dans le lot contienne une ou plusieurs application packages et spécifie les options de configuration pour l’application. Par exemple, une application peut spécifier la version de package d’application par défaut à installer sur les nœuds de calcul et que ses packages pouvant être mis à jour ou supprimés.

### <a name="application-packages"></a>Packages d’application

Un package d’application est un fichier .zip contenant les fichiers binaires d’application et fichiers pris en charge qui sont requises pour l’exécution par vos tâches. Chaque package d’application représente une version spécifique de l’application.

Vous pouvez spécifier des packages d’application au niveau du pool et tâche. Vous pouvez spécifier une ou plusieurs de ces packages et (facultatif) une version lorsque vous créez une liste ou une tâche.

* **Packages d’application pool** sont déployés sur *tous* les nœuds dans le pool. Les applications sont déployées lorsqu’un nœud rejoint un pool et lorsqu’il est redémarré ou que.

    Packages d’application pool conviennent lorsque tous les nœuds dans un pool d’exécuter des tâches d’un travail. Vous pouvez spécifier une ou plusieurs des packages d’application lorsque vous créez un pool, et vous pouvez ajouter ou mettre à jour les packages d’un pool existant. Si vous mettez à jour des packages d’application d’un pool existant, vous devez redémarrer les nœuds pour installer le nouveau package.

* **Packages d’applications Office** sont déployées uniquement à un nœud de cluster planifié pour exécuter une tâche, juste avant l’exécution de ligne de commande de la tâche. Si le package d’application spécifiée et la version se trouve déjà dans le nœud, il n’est pas redéployé et le package existant est utilisé.

    Packages d’applications Office sont utiles dans les environnements pool partagés, où différentes tâches sont exécutées sur un pool et le pool n’est pas supprimé lorsqu’un travail est terminé. Si votre tâche a moins de tâches que les nœuds dans la liste, des packages d’application tâche peuvent réduire le transfert de données depuis votre application est déployée uniquement sur les nœuds qui s’exécutent les tâches.

    Autres scénarios qui peuvent tirer parti des packages d’application tâche sont des tâches qui utilisent une application très grande, mais pour qu’un petit nombre de tâches. Par exemple, une étape de prétraitement ou une tâche de fusion, où l’application prétraitement ou de fusion est lourds.

> [AZURE.IMPORTANT] Il existe des restrictions sur le nombre de packages d’application au sein d’un compte lot, ainsi que la taille maximale des applications package et d’applications. Voir [les Quotas et les limites pour le service Azure lot](batch-quota-limit.md) pour plus d’informations sur ces limites.

### <a name="benefits-of-application-packages"></a>Avantages des packages d’application

Packages d’application peuvent simplifier le code de votre solution lot et réduire les frais nécessaires pour gérer les applications qui s’exécutent de vos tâches.

Tâches de début de votre pool n’est spécifier une longue liste de fichiers de ressources individuels à installer sur les nœuds. Vous n’êtes pas obligé de manuellement gérer plusieurs versions de vos fichiers d’application dans le stockage Azure ou sur les nœuds. Et, vous n’avez pas besoin à vous soucier de générer des [URL associations de sécurité](../storage/storage-dotnet-shared-access-signature-part-1.md) pour fournir un accès aux fichiers dans votre compte de stockage. Traitement par lots fonctionne en arrière-plan avec le stockage Azure pour stocker des packages d’application et déployer les nœuds de calcul.

## <a name="upload-and-manage-applications"></a>Télécharger et gérer des applications

Vous pouvez utiliser le [portail Azure] [ portal] ou la bibliothèque [Lot Management .NET](batch-management-dotnet.md) pour gérer les packages d’application dans votre compte lot. Dans les sections suivantes, nous tout d’abord lier un compte de stockage, puis discutez ajoutant des applications et packages et leur gestion à l’aide du portail.

### <a name="link-a-storage-account"></a>Lier un compte de stockage

Pour utiliser des packages d’application, vous devez tout d’abord lier un compte de stockage Azure à votre compte lot. Si vous n’avez pas encore configuré un compte de stockage pour votre compte lot, le portail Azure affiche un avertissement s’affiche la première fois que vous cliquez sur la vignette **d’Applications** dans la carte de **compte lot** .

> [AZURE.IMPORTANT] Traitement par lots actuellement prend en charge *uniquement* le type de compte de stockage **objectif général** comme décrit à l’étape 5, [créer un compte de stockage](../storage/storage-create-storage-account.md#create-a-storage-account), dans [les comptes de stockage sur Azure](../storage/storage-create-storage-account.md). Si vous liez un compte de stockage Azure à votre compte lot, lien *uniquement* un compte de stockage **objectif général** .

![Aucun avertissement ne compte configuré stockage Azure portail][9]

Le service lot utilise le compte de stockage associé pour le stockage et l’extraction des packages d’application. Une fois que vous avez lié les deux comptes, lot pouvez déployer automatiquement les packages stockés dans le compte de stockage lié à votre nœuds de calcul. Cliquez sur **paramètres du compte de stockage** sur la carte **d’Avertissement** , puis cliquez sur **Compte de stockage** sur la carte de **Compte de stockage** pour lier un compte de stockage à votre compte lot.

![Choisissez carte de compte de stockage Azure portail][10]

Nous vous recommandons de créer un espace de stockage compte *spécifiquement* pour une utilisation avec votre compte lot sélectionnez ici. Pour plus d’informations sur la création d’un compte de stockage, voir « Créer un compte de stockage » dans [les comptes de stockage sur Azure](../storage/storage-create-storage-account.md). Une fois que vous avez créé un compte de stockage, vous pouvez ensuite le lier à votre compte lot à l’aide de la carte de **Compte de stockage** .

> [AZURE.WARNING] Étant donné que le lot utilise le stockage Azure pour stocker vos packages d’application, vous êtes [facturé comme normal] [ storage_pricing] pour les blocs de données blob. Veillez à prendre en compte la taille et le nombre de vos packages d’application et supprimer régulièrement des packages déconseillées pour réduire les coûts.

### <a name="view-current-applications"></a>Afficher les applications en cours

Pour afficher les applications de votre compte lot, cliquez sur l’élément de menu **Applications** dans le menu de gauche lorsque vous affichez la carte de **compte lot** .

![Vignette d’applications][2]

Cette action ouvre la carte **d’Applications** :

![Liste des applications][3]

La carte **Applications** affiche l’ID de chaque application dans votre compte et les propriétés suivantes :

* **Packages**--le nombre de versions associées à cette application.
* **Version par défaut**: la version qui est installée si vous ne spécifiez pas une version lorsque vous définissez l’application d’un pool. Ce paramètre est facultatif.
* **Autoriser mises à jour**: la valeur qui spécifie si package met à jour, les suppressions et les ajouts sont autorisés. Si cela est définie sur **non**, package mises à jour et les suppressions sont désactivées pour l’application. Seules les versions package application nouveau peuvent être ajoutées. La valeur par défaut est **Oui**.

### <a name="view-application-details"></a>Afficher les détails

Cliquez sur une application dans la carte **Applications** pour ouvrir la carte qui inclut les détails de cette application.

![Détails de l’application][4]

Dans la carte de détails application, vous pouvez configurer les paramètres suivants pour votre application.

* **Autoriser mises à jour**: spécifiez si son packages d’application peuvent être mis à jour ou supprimés. Voir « Mettre à jour ou supprimer un package d’application » plus loin dans cet article.
* **Version par défaut**: spécifiez un package d’application par défaut pour déployer des nœuds de calcul.
* **Nom d’affichage**: spécifiez un » « nom convivial votre lot solution peut utiliser lors de l’affichage d’informations sur l’application, comme dans l’interface utilisateur d’un service que vous fournissez vos clients via par lots.

### <a name="add-a-new-application"></a>Ajouter une nouvelle application

Pour créer une nouvelle application, ajoutez un package d’application et spécifiez un ID d’application nouveaux et uniques. Le premier package d’application que vous ajoutez avec l’ID d’application crée également la nouvelle application.

Cliquez sur **Ajouter** dans la carte **Applications** pour ouvrir la carte de la **nouvelle application** .

![Nouvelle carte application portail Azure][5]

La **nouvelle application** assure la les champs suivants pour spécifier les paramètres de votre nouvelle application et le package d’application.

**Id de l’application**

Ce champ indique l’ID de votre nouvelle application, qui est couvertes par les règles de validation code lot Azure standards :

* Peut contenir n’importe quelle combinaison de caractères alphanumériques, y compris les traits d’union et des traits de soulignement.
* Ne peut pas contenir plus de 64 caractères.
* Doivent être uniques dans le compte lot.
* Est le préserve et la casse.

**Version**

Spécifie la version du package d’application que vous téléchargez. Chaînes de version sont couvertes par les règles de validation suivantes :

* Peut contenir n’importe quelle combinaison de caractères alphanumériques, y compris des traits d’union, des traits de soulignement et des périodes.
* Ne peut pas contenir plus de 64 caractères.
* Doit être unique dans l’application.
* Cas de conservation et de la casse.

**Package d’application**

Ce champ indique le fichier .zip qui contient les fichiers binaires d’application et les fichiers pris en charge sont requises pour exécuter l’application. Cliquez sur la zone **Sélectionner un fichier** ou sur l’icône de dossier pour rechercher et sélectionner un fichier .zip qui contient des fichiers de votre application.

Une fois que vous avez sélectionné un fichier, cliquez sur **OK** pour lancer le téléchargement au stockage Azure. Lorsque l’opération de téléchargement est terminée, vous serez averti et la carte se ferme. Selon la taille du fichier que vous téléchargez et la vitesse de votre connexion réseau, cette opération peut prendre un certain temps.

> [AZURE.WARNING] Ne fermez pas la carte **nouvelle application** avant que l’opération de téléchargement soit terminée. Cela s’arrête le processus de téléchargement.

### <a name="add-a-new-application-package"></a>Ajouter un package d’application

Pour ajouter une nouvelle version de package d’application pour une application existante, sélectionnez une application dans la carte **d’Applications** , cliquez sur **Packages**, puis cliquez sur **Ajouter** pour ouvrir la carte **ajoute un package** .

![Ajouter la carte de package d’application dans le portail Azure][8]

Comme vous pouvez le voir, les champs correspondent à celles de la **nouvelle application** cuillère, mais la case **id de l’Application** est désactivée. Comme vous le faisiez pour la nouvelle application, spécifiez la **Version** pour le nouveau lot, accédez à votre fichier .zip **package d’Application** , puis cliquez sur **OK** pour télécharger le package.

### <a name="update-or-delete-an-application-package"></a>Mettre à jour ou supprimer un package d’application

Pour mettre à jour ou supprimer un package d’application existant, ouvrez la carte de détails de l’application, cliquez sur **Packages** pour ouvrir la carte de **Packages** , cliquez sur les **points de suspension** dans la ligne du package d’application que vous souhaitez modifier et sélectionnez l’action que vous voulez effectuer.

![Mettre à jour ou supprimer le package portail Azure][7]

**Mise à jour**

Lorsque vous cliquez sur **mettre à jour**, la carte de *package de mise à jour* s’affiche. Cette carte est semblable à la carte de *nouveau package d’application* , toutefois que le champ de sélection de package est activé, ce qui vous permet de spécifier un nouveau fichier ZIP à télécharger.

![Carte de package de mise à jour dans le portail Azure][11]

**Supprimer**

Lorsque vous cliquez sur **Supprimer**, vous êtes invité à confirmer la suppression de la version du lot et lot supprime le package à partir du stockage Azure. Si vous supprimez la version par défaut d’une application, le paramètre de **version par défaut** est supprimé de l’application.

![Supprimer l’application][12]

## <a name="install-applications-on-compute-nodes"></a>Installer des applications sur les nœuds de calcul

À présent que vous avez appris à gérer des packages d’application grâce au portail Azure, nous pouvons voir comment déployer les nœuds de calcul et les exécuter les tâches du lot.

### <a name="install-pool-application-packages"></a>Installer des packages d’application pool

Pour installer un package d’application sur tous les nœuds de calcul dans un pool, spécifiez une ou plusieurs application package *références* pour le pool. Les packages d’application que vous spécifiez pour un pool sont installés sur chaque nœud de calcul lorsque ce nœud rejoint le pool, et lorsque le nœud est redémarré ou que.

Dans .NET lot, spécifiez un ou plusieurs [CloudPool][net_cloudpool]. [ApplicationPackageReferences] [net_cloudpool_pkgref] lorsque vous créez un nouveau pool, ou un pool existant. [ApplicationPackageReference] [ net_pkgref] classe spécifie un ID de l’application et la version à installer sur un pool des nœuds de calcul.

```csharp
// Create the unbound CloudPool
CloudPool myCloudPool =
    batchClient.PoolOperations.CreatePool(
        poolId: "myPool",
        targetDedicated: "1",
        virtualMachineSize: "small",
        cloudServiceConfiguration: new CloudServiceConfiguration(osFamily: "4"));

// Specify the application and version to install on the compute nodes
myCloudPool.ApplicationPackageReferences = new List<ApplicationPackageReference>
{
    new ApplicationPackageReference {
        ApplicationId = "litware",
        Version = "1.1001.2b" }
};

// Commit the pool so that it's created in the Batch service. As the nodes join
// the pool, the specified application package will be installed on each.
await myCloudPool.CommitAsync();
```

>[AZURE.IMPORTANT] Si un déploiement de package d’application échoue pour une raison quelconque, le service lot marque le nœud [inutilisable][net_nodestate], et aucune tâche ne sera planifié pour l’exécution sur ce nœud. Dans ce cas, vous devez **redémarrer** le nœud pour réinitialiser la migration déploiement du package. Redémarrer le nœud permettent également la planification des tâches à nouveau sur le nœud.

### <a name="install-task-application-packages"></a>Installer des packages d’application Office

Similaire à un pool, vous spécifiez de package d’application *références* pour une tâche. Lorsqu’une tâche est planifiée pour s’exécuter sur un nœud, le package est téléchargé et extrait juste avant la ligne de commande de la tâche est exécutée. Si une version et le package spécifié est déjà installé sur le nœud, le package n’est pas téléchargé et le package existant est utilisé.

Pour installer un package d’application Office, configurez de la tâche [CloudTask][net_cloudtask]. [ApplicationPackageReferences] [net_cloudtask_pkgref] propriété :

```csharp
CloudTask task =
    new CloudTask(
        "litwaretask001",
        "cmd /c %AZ_BATCH_APP_PACKAGE_LITWARE%\\litware.exe -args -here");

task.ApplicationPackageReferences = new List<ApplicationPackageReference>
{
    new ApplicationPackageReference
    {
        ApplicationId = "litware",
        Version = "1.1001.2b"
    }
};
```

## <a name="execute-the-installed-applications"></a>Exécuter les applications installées

Les packages que vous avez spécifiés pour un pool ou d’une tâche sont téléchargés et extraits dans un répertoire nommé au sein de la `AZ_BATCH_ROOT_DIR` du nœud. Traitement par lots crée également une variable d’environnement qui contient le chemin d’accès au répertoire nommé. Les lignes de la commande tâche Utilisez cette variable d’environnement en référençant l’application sur le nœud. La variable est au format suivant :

`AZ_BATCH_APP_PACKAGE_APPLICATIONID#version`

`APPLICATIONID`et `version` sont des valeurs qui correspondent à la version de l’application et package que vous avez spécifiés pour le déploiement. Par exemple, si vous avez spécifié ce version2.7 d’application *blender* doit être installé, les lignes de la commande tâche utiliseriez cette variable d’environnement pour accéder à ses fichiers :

`AZ_BATCH_APP_PACKAGE_BLENDER#2.7`

Si vous spécifiez une version par défaut pour une application, vous pouvez omettre le suffixe de version. Par exemple, si vous définissez « 2.7 » en tant que la version par défaut pour l' application *blender*, vos tâches peuvent faire référence à la variable d’environnement suivante et qu’ils seront exécutera version2.7 :

`AZ_BATCH_APP_PACKAGE_BLENDER`

L’extrait de code suivant montre un exemple de ligne de commande tâche qui ouvre la version par défaut de l’application *blender* :

```csharp
string taskId = "blendertask01";
string commandLine =
    @"cmd /c %AZ_BATCH_APP_PACKAGE_BLENDER%\blender.exe -args -here";
CloudTask blenderTask = new CloudTask(taskId, commandLine);
```

> [AZURE.TIP] Voir [les paramètres d’environnement pour les tâches](batch-api-basics.md#environment-settings-for-tasks) dans [vue d’ensemble de la fonctionnalité lot](batch-api-basics.md) pour plus d’informations sur les paramètres d’environnement cluster nœud.

## <a name="update-a-pools-application-packages"></a>Mettre à jour des packages d’application d’un pool

Si un pool existant a déjà été configuré avec un package d’application, vous pouvez spécifier un nouveau package pour le pool. Si vous spécifiez une nouvelle référence de package pour une liste, les conditions suivantes s’appliquent :

* Tous les nouveaux nœuds formant le pool et n’importe quel nœud existant qui est redémarré ou que seront installés le package nouvellement spécifié.
* Calculer les nœuds qui existent déjà dans le pool lorsque vous mettez à jour les références de package n’installent pas automatiquement le package d’application. Calculer les nœuds doivent être redémarrés ou que pour recevoir le nouveau lot.
* Lorsqu’un nouveau package est déployé, les variables d’environnement créé reflètent les nouvelles références de package d’application.

Dans cet exemple, le pool existant a version2.7 de l’application *blender* configurée dans l’un de ses [CloudPool][net_cloudpool]. [ApplicationPackageReferences] [net_cloudpool_pkgref]. Pour mettre à jour les nœuds du pool avec la version 2.76b, spécifiez une nouvelle [ApplicationPackageReference] [ net_pkgref] avec la nouvelle version et valider la modification.

```csharp
string newVersion = "2.76b";
CloudPool boundPool = await batchClient.PoolOperations.GetPoolAsync("myPool");
boundPool.ApplicationPackageReferences = new List<ApplicationPackageReference>
{
    new ApplicationPackageReference {
        ApplicationId = "blender",
        Version = newVersion }
};
await boundPool.CommitAsync();
```

Maintenant que la nouvelle version a été configurée, les *nouveaux* nœuds qui joint le pool doit avoir la version 2.76b déployée sur celui-ci. Pour installer 2.76b sur les nœuds qui se trouvent *déjà* dans le pool, redémarrez ou recréez les. Notez que les nœuds redémarrez conservera les fichiers à partir des déploiements package précédents.

## <a name="list-the-applications-in-a-batch-account"></a>Répertorier les applications dans un compte lot

Vous pouvez répertorier les applications et les packages dans un compte lot à l’aide de [ApplicationOperations][net_appops]. [ListApplicationSummaries] [net_appops_listappsummaries] méthode.

```csharp
// List the applications and their application packages in the Batch account.
List<ApplicationSummary> applications = await batchClient.ApplicationOperations.ListApplicationSummaries().ToListAsync();
foreach (ApplicationSummary app in applications)
{
    Console.WriteLine("ID: {0} | Display Name: {1}", app.Id, app.DisplayName);

    foreach (string version in app.Versions)
    {
        Console.WriteLine("  {0}", version);
    }
}
```

## <a name="wrap-up"></a>Renvoyer à la ligne vers le haut

Packages d’application, vous permettent de sélectionner les applications pour leurs tâches et spécifier la version exacte à utiliser lors de traiter les tâches avec votre service compatibles avec les commandes de vos clients. Vous pouvez également fournir la possibilité pour vos clients de télécharger et effectuer le suivi de leurs propres applications dans votre service.

## <a name="next-steps"></a>Étapes suivantes

* L' [API REST lot] [ api_rest] prend également en charge pour l’utiliser avec des packages d’application. Par exemple, voir [applicationPackageReferences] [ rest_add_pool_with_packages] élément [d’Ajouter une liste à un compte] [ rest_add_pool] pour plus d’informations sur la spécification des packages à installer à l’aide de l’API REST. Consultez [Applications] [ rest_applications] pour plus d’informations sur la façon d’obtenir des informations sur l’application à l’aide de l’API REST lot.

* Découvrez comment par programme [Gérer les quotas avec lot Management .NET et comptes Azure lot](batch-management-dotnet.md). Le [Lot de gestion .NET] [ api_net_mgmt] bibliothèque peut activer des fonctionnalités de création et la suppression de compte pour votre application lot ou service.

[api_net]: http://msdn.microsoft.com/library/azure/mt348682.aspx
[api_net_mgmt]: https://msdn.microsoft.com/library/azure/mt463120.aspx
[api_rest]: http://msdn.microsoft.com/library/azure/dn820158.aspx
[batch_mgmt_nuget]: https://www.nuget.org/packages/Microsoft.Azure.Management.Batch/
[github_samples]: https://github.com/Azure/azure-batch-samples
[storage_pricing]: https://azure.microsoft.com/pricing/details/storage/
[net_appops]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.applicationoperations.aspx
[net_appops_listappsummaries]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.applicationoperations.listapplicationsummaries.aspx
[net_cloudpool]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.aspx
[net_cloudpool_pkgref]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.applicationpackagereferences.aspx
[net_cloudtask]: https://msdn.microsoft.com/library/microsoft.azure.batch.cloudtask.aspx
[net_cloudtask_pkgref]: https://msdn.microsoft.com/library/microsoft.azure.batch.cloudtask.applicationpackagereferences.aspx
[net_nodestate]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.computenode.state.aspx
[net_pkgref]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.applicationpackagereference.aspx
[portal]: https://portal.azure.com
[rest_applications]: https://msdn.microsoft.com/library/azure/mt643945.aspx
[rest_add_pool]: https://msdn.microsoft.com/library/azure/dn820174.aspx
[rest_add_pool_with_packages]: https://msdn.microsoft.com/library/azure/dn820174.aspx#bk_apkgreference

[1]: ./media/batch-application-packages/app_pkg_01.png "Diagramme des packages d’application"
[2]: ./media/batch-application-packages/app_pkg_02.png "Vignette d’applications dans le portail Azure"
[3]: ./media/batch-application-packages/app_pkg_03.png "Carte d’applications dans le portail Azure"
[4]: ./media/batch-application-packages/app_pkg_04.png "Carte de détails application portail Azure"
[5]: ./media/batch-application-packages/app_pkg_05.png "Nouvelle carte application portail Azure"
[7]: ./media/batch-application-packages/app_pkg_07.png "Mettre à jour ou supprimer les packages déroulante portail Azure"
[8]: ./media/batch-application-packages/app_pkg_08.png "Nouvelle carte de package d’application dans le portail Azure"
[9]: ./media/batch-application-packages/app_pkg_09.png "Aucune alerte de compte de stockage liée"
[10]: ./media/batch-application-packages/app_pkg_10.png "Choisissez carte de compte de stockage Azure portail"
[11]: ./media/batch-application-packages/app_pkg_11.png "Carte de package de mise à jour dans le portail Azure"
[12]: ./media/batch-application-packages/app_pkg_12.png "Supprimer la boîte de dialogue package confirmation portail Azure"

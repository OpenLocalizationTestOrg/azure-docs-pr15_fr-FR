<properties
    pageTitle="Gestion des ressources avec lot Management .NET du compte | Microsoft Azure"
    description="Créer, supprimer et modifier des ressources de compte Azure lot avec la bibliothèque lot Management .NET."
    services="batch"
    documentationCenter=".net"
    authors="mmacy"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="batch"
    ms.devlang="multiple"
    ms.topic="article"
    ms.tgt_pltfrm="vm-windows"
    ms.workload="big-compute"
    ms.date="10/19/2016"
    ms.author="marsma"/>

# <a name="manage-azure-batch-accounts-and-quotas-with-batch-management-net"></a>Gérer les quotas avec lot Management .NET et comptes Azure lot

> [AZURE.SELECTOR]
- [Portail Azure](batch-account-create-portal.md)
- [Gestion par lots .NET](batch-management-dotnet.md)

Vous pouvez réduire les maintenance frais dans vos applications Azure lot en utilisant le [Lot Management .NET] [ api_mgmt_net] bibliothèque pour automatiser la création du lot du compte, la suppression, gestion de clés et découverte de quota.

- **Créer et supprimer des comptes lot** dans n’importe quelle région. Si un éditeur de logiciels (fil) par exemple, vous proposent des services pour vos clients dans lequel chaque est affecté un compte lot séparé pour la facturation, vous pouvez ajouter des fonctionnalités de création et la suppression du compte à votre portail client.
- **Récupérer et compte régénérer clés** par programme pour une de vos comptes lot. Cela peut vous aider à être conformes aux stratégies de sécurité qui mettent en œuvre survol périodique ou l’expiration des clés de compte. Lorsque vous avez plusieurs comptes lot dans différentes régions Azure, l’automatisation de ce processus survol augmente l’efficacité de votre solution.
- **Vérifiez vos quotas de compte** et prendre les efforts erreur version d’évaluation et se déconnecter de déterminer les comptes lot quelles limites. En activant les quotas de votre compte avant le démarrage de tâches, à créer des pools ou en ajoutant des nœuds de calcul, vous pouvez ajuster le fait de l’emplacement ou lorsque ces ressources sont créées. Vous pouvez déterminer les comptes qui nécessitent les augmentations de quota avant l’allocation des ressources supplémentaires dans ces comptes.
- **Combiner les fonctionnalités d’autres services Azure** pour une expérience de gestion complètes--à l’aide de lot Management .NET [Azure Active Directory][aad_about]et le [Gestionnaire de ressources Azure] [ resman_overview] ensemble dans la même application. À l’aide de ces fonctions et leurs API, vous pouvez fournir une expérience d’authentification frictionless, la possibilité de créer et supprimer des groupes de ressources et les fonctionnalités qui sont décrites ci-dessus pour une solution de gestion de bout en bout.

> [AZURE.NOTE] Tandis que cet article se concentre sur la gestion de vos comptes lot, les clés et les quotas de programmation, vous pouvez effectuer les différentes activités à l’aide du [portail Azure][azure_portal]. Pour plus d’informations, voir [créer un compte Azure lot à l’aide du portail Azure](batch-account-create-portal.md) et [les Quotas et les limites pour le service lot Azure](batch-quota-limit.md).

## <a name="create-and-delete-batch-accounts"></a>Créer et supprimer des comptes de lot

Comme indiqué, parmi les principales fonctionnalités de l’API de gestion des commandes consiste à créer et supprimer des comptes lot dans une zone Azure. Pour ce faire, utilisez [BatchManagementClient.Account.CreateAsync] [ net_create] et [DeleteAsync][net_delete], ou leurs équivalents synchrones.

L’extrait de code suivant crée un compte, obtient le compte nouvellement créé à partir du service lot et puis le supprime. Dans cet extrait de code et les autres colonnes dans cet article, `batchManagementClient` est une instance de [BatchManagementClient]entièrement initialisée[net_mgmt_client].

```csharp
// Create a new Batch account
await batchManagementClient.Account.CreateAsync("MyResourceGroup",
    "mynewaccount",
    new BatchAccountCreateParameters() { Location = "West US" });

// Get the new account from the Batch service
AccountResource account = await batchManagementClient.Account.GetAsync(
    "MyResourceGroup",
    "mynewaccount");

// Delete the account
await batchManagementClient.Account.DeleteAsync("MyResourceGroup", account.Name);
```

> [AZURE.NOTE] Applications qui utilisent la bibliothèque lot Management .NET et sa classe BatchManagementClient nécessitent un accès **administrateur de service** ou **coadministrator** à l’abonnement au propriétaire du compte lot à gérer. Pour plus d’informations, voir la section [Azure Active Directory](#azure-active-directory) et [AccountManagement] [ acct_mgmt_sample] exemple de code.

## <a name="retrieve-and-regenerate-account-keys"></a>Récupérer et régénérer les clés de compte

Obtenir les clés de compte principaux et secondaires à partir de n’importe quel compte lot au sein de votre abonnement à l’aide de [ListKeysAsync][net_list_keys]. Vous pouvez régénérer ces touches à l’aide de [RegenerateKeyAsync][net_regenerate_keys].

```csharp
// Get and print the primary and secondary keys
BatchAccountListKeyResult accountKeys =
    await batchManagementClient.Account.ListKeysAsync(
        "MyResourceGroup",
        "mybatchaccount");
Console.WriteLine("Primary key:   {0}", accountKeys.Primary);
Console.WriteLine("Secondary key: {0}", accountKeys.Secondary);

// Regenerate the primary key
BatchAccountRegenerateKeyResponse newKeys =
    await batchManagementClient.Account.RegenerateKeyAsync(
        "MyResourceGroup",
        "mybatchaccount",
        new BatchAccountRegenerateKeyParameters() {
            KeyName = AccountKeyType.Primary
            });
```

> [AZURE.TIP] Vous pouvez créer un flux de travail connexion simplifiée pour vos applications de gestion. Tout d’abord, obtenir une clé de compte pour le compte de lot que vous voulez gérer avec [ListKeysAsync][net_list_keys]. Ensuite, utilisez cette clé lors de l’initialisation de la bibliothèque .NET lot [BatchSharedKeyCredentials] [ net_sharedkeycred] classe, qui est utilisé lors de l’initialisation [BatchClient][net_batch_client].

## <a name="check-azure-subscription-and-batch-account-quotas"></a>Vérifiez abonnement Azure et compte les quotas de traitement par lots

Abonnements Azure et des services Azure individuels que toutes les commandes disposent quotas par défaut limiter le nombre de certaines entités qu’ils contiennent. Pour les quotas par défaut pour les abonnements Azure, voir [abonnement Azure et les limites de service, les quotas et les contraintes](./../azure-subscription-service-limits.md). Pour les quotas par défaut du service lot, voir [les Quotas et les limites pour le service lot Azure](batch-quota-limit.md). À l’aide de la bibliothèque lot Management .NET, vous pouvez vérifier ces quotas dans vos applications. Cela vous permet de prendre des décisions de répartition avant de vous ajoutez des comptes ou calculez des ressources comme des pools et les nœuds de calcul.

### <a name="check-an-azure-subscription-for-batch-account-quotas"></a>Vérifier un abonnement Azure pour compte les quotas de traitement par lots

Avant de créer un compte lot dans une zone, vous pouvez vérifier votre abonnement Azure pour voir si vous êtes en mesure d’ajouter un compte dans cette zone.

Dans l’extrait de code ci-dessous, nous utilisons tout d’abord [BatchManagementClient.Account.ListAsync] [ net_mgmt_listaccounts] pour obtenir une collection de tous les comptes lot qui se trouvent dans un abonnement. Une fois que nous avons obtenu cette collection de sites, nous déterminer combien de comptes est dans la région cible. Puis nous utilisons [BatchManagementClient.Subscriptions] [ net_mgmt_subscriptions] pour obtenir le quota de compte lot et déterminer combien de comptes (le cas échéant) peut être créé dans cette zone.

```csharp
// Get a collection of all Batch accounts within the subscription
BatchAccountListResponse listResponse =
        await batchManagementClient.Account.ListAsync(new AccountListParameters());
IList<AccountResource> accounts = listResponse.Accounts;
Console.WriteLine("Total number of Batch accounts under subscription id {0}:  {1}",
    creds.SubscriptionId,
    accounts.Count);

// Get a count of all accounts within the target region
string region = "westus";
int accountsInRegion = accounts.Count(o => o.Location == region);

// Get the account quota for the specified region
SubscriptionQuotasGetResponse quotaResponse = await batchManagementClient.Subscriptions.GetSubscriptionQuotasAsync(region);
Console.WriteLine("Account quota for {0} region: {1}", region, quotaResponse.AccountQuota);

// Determine how many accounts can be created in the target region
Console.WriteLine("Accounts in {0}: {1}", region, accountsInRegion);
Console.WriteLine("You can create {0} accounts in the {1} region.", quotaResponse.AccountQuota - accountsInRegion, region);
```

Dans l’extrait ci-dessus, `creds` est une instance de [TokenCloudCredentials][azure_tokencreds]. Pour obtenir un exemple de création de cet objet, consultez [AccountManagement] [ acct_mgmt_sample] exemple de code de GitHub.

### <a name="check-a-batch-account-for-compute-resource-quotas"></a>Vérifier un compte de commandes pour les quotas de ressources cluster

Avant d’augmenter les ressources de cluster dans votre solution lot, vous pouvez vérifier pour vous assurer que les ressources que vous souhaitez affecter ne dépasse les quotas de compte. Dans l’extrait de code ci-dessous, nous imprimer les informations de quota pour le nom du compte lot `mybatchaccount`. Dans votre propre application, vous pouvez utiliser ces informations pour déterminer si le compte peut gérer les ressources supplémentaires à créer.

```csharp
// First obtain the Batch account
BatchAccountGetResponse getResponse =
    await batchManagementClient.Account.GetAsync("MyResourceGroup", "mybatchaccount");
AccountResource account = getResponse.Resource;

// Now print the compute resource quotas for the account
Console.WriteLine("Core quota: {0}", account.Properties.CoreQuota);
Console.WriteLine("Pool quota: {0}", account.Properties.PoolQuota);
Console.WriteLine("Active job and job schedule quota: {0}", account.Properties.ActiveJobAndJobScheduleQuota);
```

> [AZURE.IMPORTANT] Il existe des quotas par défaut pour les services et abonnements Azure, la plupart de ces limites peuvent apparaître en envoyant une requête dans le [portail Azure][azure_portal]. Par exemple, voir [les Quotas et les limites pour le service Azure lot](batch-quota-limit.md) pour obtenir des instructions sur l’augmentation de votre compte les quotas de lot.

## <a name="batch-management-net-azure-ad-and-resource-manager"></a>Gestion .NET, Azure Active Directory, de commandes et le Gestionnaire de ressources

Lorsque vous travaillez avec la bibliothèque lot Management .NET, vous en général également utiliserez [Azure Active Directory] [ aad_about] (Azure AD) et le [Gestionnaire de ressources Azure][resman_overview]. L’exemple de projet décrit ci-dessous utilise Azure Active Directory et le Gestionnaire de ressources tandis qu’il illustre l’API lot gestion .NET.

### <a name="azure-active-directory"></a>Azure Active Directory

Azure lui-même utilise Azure AD pour l’authentification de ses clients, les administrateurs de service et les utilisateurs d’organisation. Dans le cadre du lot Management .NET, vous utilisez Azure AD pour identifier un abonnement administrateur ou l’administrateur de co-création. Cela permet de la bibliothèque de gestion interroger le service lot et effectuer les opérations décrites dans cet article.

Dans l’exemple de projet indiqué ci-dessous, la [Bibliothèque de l’authentification Active Directory] Azure[ aad_adal] (terme ADAL) est utilisé pour demander à l’utilisateur ses informations d’identification Microsoft. Lorsque les informations d’identification d’administrateur ou coadministrator service sont fournies, l’application peut Azure de requête pour obtenir la liste des abonnements--et peuvent créer et supprimer des groupes de ressources et des comptes lot.

### <a name="resource-manager"></a>Gestionnaire de ressources

Lorsque vous créez des comptes lot avec la bibliothèque lot Management .NET, vous allez généralement créer les au sein d’un [groupe de ressources][resman_overview]. Vous pouvez créer le groupe de ressources par programme à l’aide de la [ResourceManagementClient] [ resman_client] classe dans le [Gestionnaire de ressources .NET] [ resman_api] bibliothèque. Ou vous pouvez ajouter un compte à un groupe de ressources existant que vous avez créé précédemment à l’aide du [portail Azure][azure_portal].

## <a name="sample-project-on-github"></a>Exemple de projet sur GitHub

Pour savoir lot Management .NET en action, consultez [AccountManagment] [ acct_mgmt_sample] exemple de projet sur GitHub. Cette application console illustre la création et l’utilisation de [BatchManagementClient] [ net_mgmt_client] et [ResourceManagementClient][resman_client]. Il illustre également l’utilisation de la [Bibliothèque de l’authentification Active Directory] Azure[ aad_adal] (terme ADAL), qui est requis par les clients.

Pour exécuter l’exemple d’application, vous devez d’abord l’enregistrer avec Azure AD à l’aide du portail Azure. Suivez les étapes décrites dans la section [Ajout d’une Application](../active-directory/active-directory-integrating-applications.md#adding-an-application) dans les [applications d’intégration avec Azure Active Directory] [ aad_integrate] pour enregistrer l’exemple d’application au sein de votre propre compte du répertoire par défaut. Veillez à sélectionner **Native Application cliente** correspondant au type d’application, et vous pouvez spécifier n’importe quel URI valide (tel que `http://myaccountmanagementsample`) pour **Rediriger URI**: il n’a pas besoin être un point de terminaison réel.

Après avoir ajouté votre application, déléguer l’autorisation de **Gestion des services Azure Access en tant qu’organisation** de l’application de *Service de gestion des API de Windows Azure* dans les paramètres de l’application dans le portail :

![Autorisations des applications de portail Azure][2]

> [AZURE.TIP] **API de gestion du Service Windows Azure** n’apparaît pas sous *autorisations à d’autres applications*, cliquez sur **Ajouter l’application**, sélectionnez **API de gestion du Service Windows Azure**, puis cliquez sur le bouton de coche. Délégué puis, comme indiqué ci-dessus.

Une fois que vous avez ajouté l’application comme décrit ci-dessus, mettre à jour `Program.cs` dans les [AccountManagment] [ acct_mgmt_sample] exemple de projet avec votre application rediriger URI et ID Client. Vous trouverez ces valeurs dans l’onglet **configuration** de votre application :

![Configuration de l’application portail Azure][3]

[AccountManagment] [ acct_mgmt_sample] application exemple illustre les opérations suivantes :

1. Acquérir un jeton de sécurité à partir d’Azure AD à l’aide du [terme ADAL][aad_adal]. Si l’utilisateur n’est pas déjà connecté, ils sont invités leurs informations d’identification Azure.
2. À l’aide du jeton de sécurité obtenu à partir d’Azure AD, créez un [SubscriptionClient] [ resman_subclient] Query Azure pour une liste des abonnements associés au compte. Cela permet de sélection d’un abonnement si plusieurs sont trouvées.
3. Créer un objet d’informations d’identification associé à l’abonnement sélectionné.
4. Créer [ResourceManagementClient] [ resman_client] en utilisant les informations d’identification.
5. Utiliser [ResourceManagementClient] [ resman_client] pour créer un groupe de ressources.
6. Utiliser [BatchManagementClient] [ net_mgmt_client] pour effectuer les opérations de compte plusieurs commandes :
  - Créer un compte lot dans le nouveau groupe de ressources.
  - Obtenir le compte nouvellement créé à partir du service lot.
  - Imprimer les clés de compte pour le nouveau compte.
  - Régénérer une nouvelle clé primaire pour le compte.
  - Imprimer les informations de quota pour le compte.
  - Imprimer les informations de quota pour l’abonnement.
  - Imprimer tous les comptes de l’abonnement.
  - Supprimer le compte nouvellement créé.
7. Supprimer le groupe de ressources.

Avant de supprimer le groupe de compte et des ressources lot nouvellement créé, vous pouvez inspecter à la fois dans le [portail Azure][azure_portal]:

![Portail Azure affichant le groupe de ressources et le lot compte][1]
<br />
*Portail Azure affichant compte lot et un nouveau groupe de ressources*

[aad_about]: ../active-directory/active-directory-whatis.md "Qu’est Azure Active Directory ?"
[aad_adal]: ../active-directory/active-directory-authentication-libraries.md
[aad_auth_scenarios]: ../active-directory/active-directory-authentication-scenarios.md "Scénarios d’authentification pour Azure AD"
[aad_integrate]: ../active-directory/active-directory-integrating-applications.md "Intégration d’Applications avec Azure Active Directory"
[acct_mgmt_sample]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/AccountManagement
[api_net]: http://msdn.microsoft.com/library/azure/mt348682.aspx
[api_mgmt_net]: https://msdn.microsoft.com/library/azure/mt463120.aspx
[azure_portal]: http://portal.azure.com
[azure_storage]: https://azure.microsoft.com/services/storage/
[azure_tokencreds]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.tokencloudcredentials.aspx
[batch_explorer_project]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/BatchExplorer
[net_batch_client]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.batchclient.aspx
[net_list_keys]: https://msdn.microsoft.com/library/azure/microsoft.azure.management.batch.accountoperationsextensions.listkeysasync.aspx
[net_create]: https://msdn.microsoft.com/library/azure/microsoft.azure.management.batch.accountoperationsextensions.createasync.aspx
[net_delete]: https://msdn.microsoft.com/library/azure/microsoft.azure.management.batch.accountoperationsextensions.deleteasync.aspx
[net_regenerate_keys]: https://msdn.microsoft.com/library/azure/microsoft.azure.management.batch.accountoperationsextensions.regeneratekeyasync.aspx
[net_sharedkeycred]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.auth.batchsharedkeycredentials.aspx
[net_mgmt_client]: https://msdn.microsoft.com/library/azure/microsoft.azure.management.batch.batchmanagementclient.aspx
[net_mgmt_subscriptions]: https://msdn.microsoft.com/library/azure/microsoft.azure.management.batch.batchmanagementclient.subscriptions.aspx
[net_mgmt_listaccounts]: https://msdn.microsoft.com/library/azure/microsoft.azure.management.batch.iaccountoperations.listasync.aspx
[resman_api]: https://msdn.microsoft.com/library/azure/mt418626.aspx
[resman_client]: https://msdn.microsoft.com/library/azure/microsoft.azure.management.resources.resourcemanagementclient.aspxs
[resman_subclient]: https://msdn.microsoft.com/library/azure/microsoft.azure.subscriptions.subscriptionclient.aspx
[resman_overview]: ../azure-resource-manager/resource-group-overview.md

[1]: ./media/batch-management-dotnet/portal-01.png
[2]: ./media/batch-management-dotnet/portal-02.png
[3]: ./media/batch-management-dotnet/portal-03.png

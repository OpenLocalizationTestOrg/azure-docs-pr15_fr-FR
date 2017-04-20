<properties 
   pageTitle="Créer des zones DNS et jeux d’enregistrements dans DNS Azure en utilisant le Kit de développement .NET | Microsoft Azure" 
   description="Découvrez comment créer des jeux d’enregistrements et zones DNS dans DNS Azure en utilisant le Kit de développement .NET." 
   services="dns" 
   documentationCenter="na" 
   authors="jtuliani" 
   manager="carmonm" 
   editor=""/>

<tags
   ms.service="dns"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services" 
   ms.date="09/19/2016"
   ms.author="jtuliani"/>


# <a name="create-dns-zones-and-record-sets-using-the-net-sdk"></a>Créer des zones DNS et des jeux d’enregistrements à l’aide du Kit de développement .NET

Vous pouvez automatiser les opérations pour créer, supprimer ou mettre à jour des zones, des jeux d’enregistrements et des enregistrements DNS à l’aide de DNS SDK avec une bibliothèque de gestion du DNS .NET. Un projet Visual Studio complè est disponible [ici.](https://www.microsoft.com/en-us/download/details.aspx?id=47268&WT.mc_id=DX_MVP4025064&e6b34bbe-475b-1abd-2c51-b5034bcdd6d2=True)

## <a name="create-a-service-principal-account"></a>Créer un compte principal de service

En règle générale, accès par programme aux ressources Azure est accordé via un compte dédié au lieu de vos propres informations d’identification utilisateur. Ces comptes dédiés sont appelés « service principal » comptes. Pour utiliser l’exemple de projet Azure DNS SDK, vous devez tout d’abord créer un compte principal de service et affecter des autorisations appropriées.

1. Suivez [ces instructions](../resource-group-authenticate-service-principal.md) pour créer un compte principal de service (l’exemple de projet Azure DNS SDK suppose l’authentification basée sur le mot de passe).

2. Créer un groupe de ressources ([Voici comment](../azure-portal/resource-group-portal.md)).

3. Utiliser RBAC Azure pour accorder le compte principal du service « DNS Zone collaboration » au groupe de ressources ([Voici comment](../active-directory/role-based-access-control-configure.md).)

4. Si vous utilisez l’exemple de projet Azure DNS SDK, modifiez le fichier « program.cs » comme suit :
    * Insérer les valeurs correctes pour la tenantId, identifiant du client (également appelé ID du compte), le code secret (mot de passe de compte principal de service) et subscriptionId servent à l’étape 1.
    * Entrez le nom du groupe de ressources choisi à l’étape 2.
    * Entrez un nom de zone DNS de votre choix.

## <a name="nuget-packages-and-namespace-declarations"></a>Packages NuGet et déclarations d’espace de noms

Pour utiliser le Kit de développement .NET de DNS Azure, vous devez installer le package NuGet **Bibliothèque de gestion DNS Azure** et autres packages Azure requis.
 
1. Dans **Visual Studio**, ouvrez un projet ou un nouveau projet. 

2. Accédez à **Outils** **>** **Gestionnaire de Package NuGet** **>** **Gérer les Packages NuGet Solution...**. 

3. Cliquez sur **Parcourir**, activez la case à cocher **inclure la version préliminaire** et tapez **Microsoft.Azure.Management.Dns** dans la zone de recherche.

4. Sélectionnez le lot, puis cliquez sur **installer** pour l’ajouter à votre projet Visual Studio.
 
5. Répétez la procédure ci-dessus pour installer également les packages suivants : **Microsoft.Rest.ClientRuntime.Azure.Authentication** et **Microsoft.Azure.Management.ResourceManager**.

## <a name="add-namespace-declarations"></a>Ajouter des déclarations d’espace de noms

Ajoutez les déclarations d’espace de noms suivantes

    using Microsoft.Rest.Azure.Authentication;
    using Microsoft.Azure.Management.Dns;
    using Microsoft.Azure.Management.Dns.Models;

## <a name="initialize-the-dns-management-client"></a>Initialisation du client de gestion DNS

La *DnsManagementClient* contient les méthodes et propriétés requises pour gérer des jeux d’enregistrements et zones DNS.  Le code suivant se connecte au compte de service principal et crée un objet DnsManagementClient.

    // Build the service credentials and DNS management client
    var serviceCreds = await ApplicationTokenProvider.LoginSilentAsync(tenantId, clientId, secret);
    var dnsClient = new DnsManagementClient(serviceCreds);
    dnsClient.SubscriptionId = subscriptionId;

## <a name="create-or-update-a-dns-zone"></a>Créer ou mettre à jour d’une zone DNS

Pour créer une zone DNS, tout d’abord un objet « Zone » est créé pour contenir les paramètres de zone DNS. Étant donné que les zones DNS ne sont pas liées à une région spécifique, l’emplacement a la valeur « global ». Dans cet exemple, un [Gestionnaire de ressources Azure 'tag'](https://azure.microsoft.com/updates/organize-your-azure-resources-with-tags/) est également ajouté à la zone.

Pour créer ou mettre à jour de la zone dans le système DNS Azure, l’objet de la zone contenant les paramètres de zone est passé à la méthode *DnsManagementClient.Zones.CreateOrUpdateAsyc* .

>[AZURE.NOTE] DnsManagementClient prend en charge trois modes d’opération : synchrone (« CreateOrUpdate »), asynchrone (« CreateOrUpdateAsync »), ou asynchrone avec l’accès à la réponse HTTP (« CreateOrUpdateWithHttpMessagesAsync »).  Vous pouvez choisir un des modes suivants, selon vos besoins d’applications.

DNS Azure prend en charge simultané, appelé [Etags](dns-getstarted-create-dnszone.md). Dans cet exemple, spécifiant « * » pour le « If-None-Match' en-tête indique DNS Azure pour créer une zone DNS s’il n’existe pas déjà.  L’appel échoue si une zone avec le nom donné existe déjà dans le groupe de ressources donné.

    // Create zone parameters
    var dnsZoneParams = new Zone("global"); // All DNS zones must have location = "global"
    
    // Create a Azure Resource Manager 'tag'.  This is optional.  You can add multiple tags
    dnsZoneParams.Tags = new Dictionary<string, string>();
    dnsZoneParams.Tags.Add("dept", "finance");
    
    // Create the actual zone.
    // Note: Uses 'If-None-Match *' ETAG check, so will fail if the zone exists already.
    // Note: For non-async usage, call dnsClient.Zones.CreateOrUpdate(resourceGroupName, zoneName, dnsZoneParams, null, "*")
    // Note: For getting the http response, call dnsClient.Zones.CreateOrUpdateWithHttpMessagesAsync(resourceGroupName, zoneName, dnsZoneParams, null, "*")
    var dnsZone = await dnsClient.Zones.CreateOrUpdateAsync(resourceGroupName, zoneName, dnsZoneParams, null, "*");

## <a name="create-dns-record-sets-and-records"></a>Créer des enregistrements et des jeux d’enregistrements DNS

Enregistrements DNS sont gérées comme un jeu d’enregistrements. Un jeu d’enregistrements est un jeu d’enregistrements avec le même nom et le type d’enregistrement dans une zone.  Le nom du jeu d’enregistrements est par rapport au nom de la zone, pas le nom DNS complet.

Pour créer ou mettre à jour un enregistrement définir, un « jeu d’enregistrements « paramètres objet est créé et passé à *DnsManagementClient.RecordSets.CreateOrUpdateAsync*. Comme avec les zones DNS, il existe trois modes d’opération : synchrone (« CreateOrUpdate »), asynchrone (« CreateOrUpdateAsync »), ou asynchrone avec l’accès à la réponse HTTP (« CreateOrUpdateWithHttpMessagesAsync »).

Comme avec les zones DNS, opérations sur les jeux d’enregistrements prennent en charge simultané.  Dans cet exemple, étant donné que « If-Match » ni « If-None-Match » est spécifiés, le jeu d’enregistrements est toujours créé.  Cet appel remplace tout enregistrement existant défini avec le même nom et le type d’enregistrement dans cette zone DNS.

    // Create record set parameters
    var recordSetParams = new RecordSet();
    recordSetParams.TTL = 3600;

    // Add records to the record set parameter object.  In this case, we'll add a record of type 'A'
    recordSetParams.ARecords = new List<ARecord>();
    recordSetParams.ARecords.Add(new ARecord("1.2.3.4"));

    // Add metadata to the record set.  Similar to Azure Resource Manager tags, this is optional and you can add multiple metadata name/value pairs
    recordSetParams.Metadata = new Dictionary<string, string>();
    recordSetParams.Metadata.Add("user", "Mary");

    // Create the actual record set in Azure DNS
    // Note: no ETAG checks specified, will overwrite existing record set if one exists
    var recordSet = await dnsClient.RecordSets.CreateOrUpdateAsync(resourceGroupName, zoneName, recordSetName, RecordType.A, recordSetParams);

## <a name="get-zones-and-record-sets"></a>Obtenir des zones et des jeux d’enregistrements

Les méthodes *DnsManagementClient.Zones.Get* et *DnsManagementClient.RecordSets.Get* récupèrent zones individuelles et jeux d’enregistrements, respectivement. Jeux d’enregistrements est identifiés par leur type, nom et le groupe zone et des ressources qu’elles existent dans. Zones identifiées par son nom et qu’elles existent dans le groupe de ressources.

    var recordSet = dnsClient.RecordSets.Get(resourceGroupName, zoneName, recordSetName, RecordType.A);
    
## <a name="update-an-existing-record-set"></a>Mettre à jour un jeu d’enregistrements existant

Pour mettre à jour un jeu d’enregistrements DNS existant, tout d’abord récupérer le jeu d’enregistrements, puis mettez à jour le contenu du jeu d’enregistrements, puis envoyez la modification.  Dans cet exemple, nous spécifier l’Etag dans le jeu d’enregistrements récupéré dans le paramètre « If-Match ». L’appel échoue si une opération simultanée a modifié l’ensemble dans l’intervalle des enregistrements.

    var recordSet = dnsClient.RecordSets.Get(resourceGroupName, zoneName, recordSetName, RecordType.A);

    // Add a new record to the local object.  Note that records in a record set must be unique/distinct
    recordSet.ARecords.Add(new ARecord("5.6.7.8"));

    // Update the record set in Azure DNS
    // Note: ETAG check specified, update will be rejected if the record set has changed in the meantime
    recordSet = await dnsClient.RecordSets.CreateOrUpdateAsync(resourceGroupName, zoneName, recordSetName, RecordType.A, recordSet, recordSet.Etag);

## <a name="list-zones-and-record-sets"></a>Zones de liste et des jeux d’enregistrements

Pour les zones de liste, utilisez les méthodes *DnsManagementClient.Zones.List...* , qui prennent en charge de la liste de toutes les zones dans un groupe de ressources donnée ou toutes les zones dans un abonnement Azure donné (pour les groupes de ressources). Pour répertorier les jeux d’enregistrements, utilisez les méthodes *DnsManagementClient.RecordSets.List...* , qui prend en charge soit répertoriant tous les jeux d’enregistrements dans une zone donnée ou uniquement les jeux d’enregistrements d’un type spécifique.

Remarque : lors de l’affichage des zones et ensembles d’enregistrements qui résulte peuvent être pagination.  L’exemple suivant montre comment parcourir les pages de résultats. (Une taille de page artificiellement petite de « 2 » est utilisée pour forcer la pagination ; dans la pratique ce paramètre doit être omis et la taille de page par défaut utilisée.)

    // Note: in this demo, we'll use a very small page size (2 record sets) to demonstrate paging
    // In practice, to improve performance you would use a large page size or just use the system default
    int recordSets = 0;
    var page = await dnsClient.RecordSets.ListAllInResourceGroupAsync(resourceGroupName, zoneName, "2");
    recordSets += page.Count();

    while (page.NextPageLink != null)
    {
        page = await dnsClient.RecordSets.ListAllInResourceGroupNextAsync(page.NextPageLink);
        recordSets += page.Count();
    }

## <a name="next-steps"></a>Étapes suivantes

Téléchargez le [Kit de développement .NET Azure DNS exemple de projet](https://www.microsoft.com/en-us/download/details.aspx?id=47268&WT.mc_id=DX_MVP4025064&e6b34bbe-475b-1abd-2c51-b5034bcdd6d2=True), qui inclut d’autres exemples d’utilisation Azure DNS .NET SDK, y compris des exemples pour d’autres types d’enregistrements.

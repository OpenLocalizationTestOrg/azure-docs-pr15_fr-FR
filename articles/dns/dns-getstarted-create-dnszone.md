<properties
   pageTitle="Prise en main DNS Azure | Microsoft Azure"
   description="Apprenez à créer des zones DNS pour Azure DNS. Il s’agit d’une étape par étape pour obtenir votre première zone DNS créé pour démarrer l’hébergement de votre domaine DNS à l’aide de PowerShell."
   services="dns"
   documentationCenter="na"
   authors="sdwheeler"
   manager="carmonm"
   editor=""/>

<tags
   ms.service="dns"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="08/16/2016"
   ms.author="sewhee"/>

# <a name="create-a-dns-zone-using-powershell"></a>Créer une zone DNS à l’aide de Powershell

> [AZURE.SELECTOR]
- [Portail Azure](dns-getstarted-create-dnszone-portal.md)
- [PowerShell](dns-getstarted-create-dnszone.md)
- [Azure infrastructure du langage commun](dns-getstarted-create-dnszone-cli.md)

Cet article vous guidera tout au long de la procédure pour créer une zone DNS à l’aide de PowerShell. Vous pouvez également créer une zone DNS à l’aide d’infrastructure du langage commun ou le portail Azure.

[AZURE.INCLUDE [dns-create-zone-about](../../includes/dns-create-zone-about-include.md)]

## <a name="tagetag"></a>À propos des balises et Etags

### <a name="etags"></a>ETags

Supposons que deux personnes ou deux processus essayez de modifier un enregistrement DNS en même temps. Celui que vous gagnant ? Et le gagnant sait-il qu’ils ont simplement remplacé les modifications créées par quelqu'un d’autre ?

DNS Azure utilise Etags pour gérer les modifications simultanées à la même ressource en toute sécurité. Chaque ressource DNS (zone ou jeu d’enregistrements) a un Etag associé. Chaque fois qu’une ressource est extraite, son Etag est également récupérée. Lors de la mise à jour d’une ressource, vous avez la possibilité pour repasser l’Etag qui Azure DNS permet de vérifier que l’Etag sur les correspondances de serveur. Étant donné que chaque mise à jour pour une ressource entraîne l’Etag régénérer en cours, une incompatibilité Etag indique une modification simultanée s’est produite. ETags sont également utilisés lorsque vous créez une nouvelle ressource pour vous assurer que la ressource n’existe pas déjà.

Par défaut, Azure DNS PowerShell utilise Etags pour bloquer les modifications simultanées dans les zones du et jeux d’enregistrements. L’option *-Remplacer* commutateur peut être utilisée pour supprimer tous les tests Etag, auquel cas toutes les modifications simultanées qui se sont produites seront remplacées.

Au niveau de l’API REST de DNS Azure, Etags sont spécifiées en utilisant les en-têtes HTTP.  Leur comportement est décrite dans le tableau suivant :

|En-tête|Comportement|
|------|--------|
|Aucun|PLACER réussit toujours (aucune vérification Etag)|
|If-match<etag>|PLACER réussit uniquement si la ressource existe et Etag correspond à|
|If-match *     | PLACER réussit uniquement si la ressource existe|
|If-none-match * |  PLACER réussit uniquement si la ressource n’existe pas|

### <a name="tags"></a>Balises

Balises sont différents des Etags. Les balises sont une liste de paires nom-valeur et sont utilisées par le Gestionnaire de ressources Azure aux ressources étiquette fins de facturation ou de regroupement. Pour plus d’informations sur les balises, voir [en utilisant des balises pour organiser vos ressources Azure](../resource-group-using-tags.md).

Azure DNS PowerShell prend en charge les balises sur les zones et jeux d’enregistrements spécifiée en utilisant les options `-Tag` paramètre.


## <a name="before-you-begin"></a>Avant de commencer

Vérifiez que vous avez les éléments suivants avant de commencer votre configuration.

- Un abonnement Azure. Si vous n’avez pas un abonnement Azure, vous pouvez activer votre [avantages d’abonné MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) ou inscrivez-vous vers le haut pour un [compte gratuit](https://azure.microsoft.com/pricing/free-trial/).

- Vous devez installer la dernière version des applets de commande PowerShell de gestionnaire de ressources Azure (1.0 ou version ultérieure). Découvrez [comment installer et configurer Azure PowerShell](../powershell-install-configure.md) pour plus d’informations sur l’installation les applets de commande PowerShell.

## <a name="step-1---sign-in"></a>Étape 1 : se connecter

Ouvrez votre console PowerShell et connectez-vous à votre compte. Pour plus d’informations, voir [Utilisation de Windows PowerShell avec le Gestionnaire de ressources](../powershell-azure-resource-manager.md).

Pour vous aider à vous connecter, utilisez l’exemple suivant :

    Login-AzureRmAccount

Vérifier les abonnements pour le compte.

    Get-AzureRmSubscription

Spécifier l’abonnement que vous voulez utiliser.

    Select-AzureRmSubscription -SubscriptionName "Replace_with_your_subscription_name"

## <a name="step-2---create-a-resource-group"></a>Étape 2 : créer un groupe de ressources

Le Gestionnaire de ressources Azure requiert que tous les groupes ressources spécifient un emplacement. Il est utilisé comme emplacement par défaut pour les ressources dans ce groupe de ressources. Toutefois, étant donné que toutes les ressources DNS sont globales, pas régionaux, le choix de la position du groupe de ressources a aucun impact sur Azure DNS.

Vous pouvez ignorer cette étape si vous utilisez un groupe de ressources existant.

    New-AzureRmResourceGroup -Name MyAzureResourceGroup -location "West US"


## <a name="step-3---register"></a>Étape 3 : Registre

Le service DNS Azure est géré par le fournisseur de ressources Microsoft.Network. Votre abonnement Azure doit être enregistré pour utiliser cette ressource de fournisseur avant de pouvoir utiliser Azure DNS. Il s’agit d’une opération unique pour chaque abonnement.

    Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Network


## <a name="step-4----create-a-dns-zone"></a>Étape 4 : créer une zone DNS

Une zone DNS est créée à l’aide de la `New-AzureRmDnsZone` applet de commande. Il existe des exemples ci-dessous pour créer une zone DNS avec ou sans balises. Pour plus d’informations sur les balises, consultez la section [balises](#tags) dans cet article.

>[AZURE.NOTE] Dans le système DNS Azure, les noms de zones doivent être indiquées sans un ne s’arrête **'.'**. Par exemple, comme «**contoso.com**» au lieu de «**contoso.com.**».

### <a name="to-create-a-dns-zone"></a>Pour créer une zone DNS

L’exemple suivant crée une zone DNS appelée *contoso.com* dans le groupe de ressources appelé *MyResourceGroup*. L’exemple permet de créer une zone DNS, en remplaçant les valeurs de votre propre.

    New-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup

### <a name="to-create-a-dns-zone-with-tags"></a>Pour créer une zone DNS avec des balises

L’exemple suivant montre comment créer une zone DNS avec deux balises *projet = demo* et *environnement = test*. L’exemple permet de créer une zone DNS, en remplaçant les valeurs de votre propre.

    New-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup -Tag @( @{ Name="project"; Value="demo" }, @{ Name="env"; Value="test" } )

## <a name="view-records"></a>Afficher les enregistrements

Création d’une zone DNS crée également les enregistrements DNS suivants :

- L’enregistrement *Démarrage de l’autorité* (SOA). Il est présent à la racine de chaque zone DNS.

- Les enregistrements de serveur de (noms NS) nom faisant autorité. Ces affichent les serveurs de noms sont qui héberge la zone. DNS Azure utilise un pool de serveurs de noms, et si différents serveurs peuvent être affectées à différentes zones dans Azure DNS. Voir [délégué un domaine à Azure DNS](dns-domain-delegation.md) pour plus d’informations.

Pour afficher ces enregistrements, utilisez `Get-AzureRmDnsRecordSet`:

    Get-AzureRmDnsRecordSet -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup

    Name              : @
    ZoneName          : contoso.com
    ResourceGroupName : MyResourceGroup
    Ttl               : 3600
    Etag              : 2b855de1-5c7e-4038-bfff-3a9e55b49caf
    RecordType        : SOA
    Records           : {[ns1-01.azure-dns.com,msnhst.microsoft.com,900,300,604800,300]}
    Tags              : {}

    Name              : @
    ZoneName          : contoso.com
    ResourceGroupName : MyResourceGroup
    Ttl               : 3600
    Etag              : 5fe92e48-cc76-4912-a78c-7652d362ca18
    RecordType        : NS
    Records           : {ns1-01.azure-dns.com, ns2-01.azure-dns.net, ns3-01.azure-dns.org,
                  ns4-01.azure-dns.info}
    Tags              : {}


Enregistrement définit à la racine (ou le *sommet*) d’un usage de Zone DNS **@** comme nom de l’ensemble de l’enregistrement.


## <a name="test"></a>Test

Vous pouvez tester votre zone DNS à l’aide d’outils DNS comme nslookup, Creusez ou l' [applet de commande PowerShell résoudre NomDNS](https://technet.microsoft.com/library/jj590781.aspx).

Si vous n’avez pas encore reçu votre domaine à utiliser la nouvelle zone dans le système DNS Azure, vous avez besoin diriger la requête DNS directement à l’un des serveurs de noms pour votre zone. Les serveurs de noms pour votre zone figurent dans les enregistrements de serveur de noms, comme indiqué par `Get-AzureRmDnsRecordSet` ci-dessus. Assurez-vous que la remplacer les valeurs correctes pour votre zone dans la ligne de commande ci-dessous.

    nslookup
    > set type=SOA
    > server ns1-01.azure-dns.com
    > contoso.com

    Server: ns1-01.azure-dns.com
    Address:  208.76.47.1

    contoso.com
            primary name server = ns1-01.azure-dns.com
            responsible mail addr = msnhst.microsoft.com
            serial  = 1
            refresh = 900 (15 mins)
            retry   = 300 (5 mins)
            expire  = 604800 (7 days)
            default TTL = 300 (5 mins)


## <a name="next-steps"></a>Étapes suivantes

Après avoir créé une zone DNS, créer des [ensembles d’enregistrements et des enregistrements](dns-getstarted-create-recordset.md) pour démarrer la résolution de noms pour votre domaine Internet.


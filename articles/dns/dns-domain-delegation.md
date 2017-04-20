<properties
   pageTitle="Déléguer votre domaine à DNS Azure | Microsoft Azure"
   description="Comprendre comment changer délégation de domaine et utilisent des serveurs de noms DNS Azure pour fournir le domaine d’hébergement."
   services="dns"
   documentationCenter="na"
   authors="sdwheeler"
   manager="carmonm"
   editor=""/>

<tags
   ms.service="dns"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="06/30/2016"
   ms.author="sewhee"/>


# <a name="delegate-a-domain-to-azure-dns"></a>Délégué un domaine à Azure DNS

Azure DNS vous permet d’héberger une zone DNS et de gérer les enregistrements DNS pour un domaine dans Azure. Dans l’ordre pour les requêtes DNS pour un domaine atteindre Azure DNS, le domaine doit être déléguée à Azure DNS du domaine parent. N’oubliez pas Azure DNS n’est pas l’enregistrement de domaines. Cet article explique comment fonctionne délégation de domaine et comment déléguer domains et Azure DNS.




## <a name="how-dns-delegation-works"></a>Fonctionne de la délégation DNS

### <a name="domains-and-zones"></a>Domaines et les zones

Le système DNS est une hiérarchie de domaines. La hiérarchie commence à partir du domaine « root », dont le nom est simplement '**.**'.  Dessous proviennent des domaines de niveau supérieur, telles que « com », « net », « org », 'uk' ou « jp ».  Dessous de ces derniers sont des domaines de deuxième niveau, tels que « org.uk » ou « co.jp ».  Et ainsi de suite. Les domaines dans la hiérarchie DNS sont hébergés à l’aide des zones DNS distinctes. Ces zones sont globalement distribués, hébergé par les serveurs de noms DNS dans le monde entier.

**Zone DNS**

Un domaine est un nom unique dans le système DNS, par exemple sur contoso.com. Une zone DNS est utilisée pour héberger les enregistrements DNS pour un domaine particulier. Par exemple, le domaine « contoso.com » peut contenir un nombre d’enregistrements DNS tels que « mail.contoso.com » (pour un serveur de messagerie) et « www.contoso.com » (pour un site Web).

**Enregistrement de domaines**

Un enregistrement de domaines est une société qui peut fournir des noms de domaine Internet. Ils vérifie si le domaine Internet que vous voulez utiliser n’est disponible et vous permettent de l’acheter. Une fois que le nom de domaine est enregistré, vous devez être le propriétaire légal du nom de domaine. Si vous disposez déjà d’un domaine Internet, vous allez utiliser l’enregistrement de domaines actuel déléguer à Azure DNS.

>[AZURE.NOTE] Pour plus d’informations sur la personne assurant un nom de domaine donnée ou pour savoir comment acheter un domaine d’informations, voir [Gestion des domaines Internet dans Azure Active Directory](https://msdn.microsoft.com/library/azure/hh969248.aspx).

### <a name="resolution-and-delegation"></a>Résolution et la délégation

Il existe deux types de serveurs DNS :

- _Serveur DNS_ héberge les zones DNS. Il répond aux requêtes DNS pour les enregistrements dans les zones uniquement.
- Un serveur DNS _récursif_ n’héberge pas de zones DNS. Il répond à toutes les requêtes DNS en appelant serveurs DNS faisant autorité pour collecter les données dont il a besoin.

>[AZURE.NOTE] Azure DNS propose un service DNS faisant autorité.  Il ne fournit pas un service DNS récursive.

> Services de cloud et machines virtuelles dans Azure sont automatiquement configurés pour utiliser un récursive DNS services qui est fourni séparément dans le cadre de l’infrastructure d’Azure.  Pour plus d’informations sur la façon de modifier ces paramètres DNS, voir [Résolution de noms dans Azure](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-using-your-own-dns-server).

Les clients DNS dans PC ou des périphériques mobiles appeler généralement un serveur DNS récursif pour effectuer des requêtes DNS que les applications clientes ont besoin.

Lorsqu’un serveur DNS récursif reçoit une requête pour un enregistrement DNS telle que « www.contoso.com », elle doit d’abord rechercher le serveur de noms qui héberge la zone pour le domaine « contoso.com ». Pour cela, il démarre les serveurs de noms racine et à partir de là détecte les serveurs de noms qui héberge la zone « com ». Il demande ensuite les serveurs de noms de « com » pour trouver les serveurs de noms qui héberge la zone « contoso.com ».  Enfin, il est en mesure d’interroger ces serveurs de noms pour « www.contoso.com ».

Il s’agit de résolution du nom DNS. En principe, la résolution DNS inclut les étapes supplémentaires telles que les enregistrements CNAME suivants, mais qui n’est pas important pour comprendre le fonctionne de la délégation DNS.

Comment une zone parente 'pointe » vers les serveurs de noms pour une zone enfant ? Pour cela, à l’aide d’un type spécial de l’enregistrement DNS appelé un enregistrement de serveur de noms (NS correspond à « name server »). Par exemple, la zone racine contient les enregistrements de serveur de noms de « com » et affiche les serveurs de noms pour la zone « com ». À son tour, la zone « com » contient les enregistrements NS pour « contoso.com », qui affiche les serveurs de noms pour la zone « contoso.com ». Configurer les enregistrements NS pour une zone enfant dans une zone parente est appelé délégation du domaine.


![Serveur de noms DNS](./media/dns-domain-delegation/image1.png)

Chaque délégation comporte deux copies des enregistrements de serveur de noms ; une dans la zone parente qui pointe vers l’enfant et une autre dans la zone enfant proprement dite. La zone « contoso.com » contient les enregistrements de serveur de noms pour « contoso.com » (en plus des enregistrements de serveur de noms dans « com »). Il s’agit des enregistrements de serveur de noms faisant autorités et ils se trouvent au sommet de la zone enfant.


## <a name="delegating-a-domain-to-azure-dns"></a>Délégation d’un domaine à Azure DNS

Une fois que vous créez votre zone DNS dans DNS Azure, vous devez configurer des enregistrements de serveur de noms dans la zone parente afin que DNS Azure la source faisant autoritée pour la résolution de noms pour votre zone. Pour les domaines acheté auprès d’un bureau d’enregistrement, votre bureau d’enregistrement propose l’option configurer ces enregistrements de serveur de noms.

>[AZURE.NOTE] Vous n’êtes pas obligé de propriétaire d’un domaine afin de créer une zone DNS avec ce nom de domaine dans le système DNS Azure. Toutefois, vous n’avez pas besoin d’êtes propriétaire du domaine à configurer la délégation pour Azure DNS dans le Registre.

Par exemple, supposons que vous achetez le domaine « contoso.com » et créez une zone avec le nom « contoso.com » dans le système DNS Azure. En tant que propriétaire du domaine, votre bureau d’enregistrement vous propose l’option permettant de configurer les adresses de serveur de noms (autrement dit, les enregistrements NS) de votre domaine. Le bureau d’enregistrement stockera ces enregistrements de serveur de noms dans le domaine parent, dans ce cas, « .com ». Les clients du monde entier puis seront dirigés vers votre domaine dans la zone DNS Azure lorsque vous tentez de résoudre les enregistrements DNS dans « contoso.com ».

### <a name="finding-the-name-server-names"></a>Recherche de noms de serveur de nom

Avant de vous pouvez déléguer votre zone DNS Azure DNS, vous devez tout d’abord connaître les noms de serveur de nom pour votre zone. Azure DNS affecte les serveurs de noms d’un pool de chaque fois qu’une zone est créée.

Pour afficher les serveurs de noms assignés à votre zone, le plus simple consiste via le portail Azure.  Dans cet exemple, la zone « contoso.net » a été affectée serveurs de noms ' ns1-01.azure-dns.com', 'ns2-01.azure-dns .net', ' ns3-01.azure-dns.org', et ' ns4-01.azure-dns.info » :

 ![Serveur de noms DNS](./media/dns-domain-delegation/viewzonens500.png)

Azure DNS crée automatiquement des enregistrements de serveur de noms faisant autorités dans votre zone contenant les serveurs de noms affectées.  Pour afficher les noms de serveur de nom via PowerShell Azure ou Azure infrastructure du langage commun, vous devez simplement récupérer ces enregistrements.

À l’aide de PowerShell Azure, les enregistrements de serveur de noms faisant autorités peuvent être récupérés comme suit. Notez que le nom de l’enregistrement “@” est utilisé pour faire référence à des enregistrements au sommet de la zone.

    PS> $zone = Get-AzureRmDnsZone –Name contoso.net –ResourceGroupName MyResourceGroup
    PS> Get-AzureRmDnsRecordSet –Name “@” –RecordType NS –Zone $zone

    Name              : @
    ZoneName          : contoso.net
    ResourceGroupName : MyResourceGroup
    Ttl               : 3600
    Etag              : 5fe92e48-cc76-4912-a78c-7652d362ca18
    RecordType        : NS
    Records           : {ns1-01.azure-dns.com, ns2-01.azure-dns.net, ns3-01.azure-dns.org,
                        ns4-01.azure-dns.info}
    Tags              : {}

Vous pouvez également utiliser l’infrastructure du langage commun Azure disponibilité sur plusieurs plateformes pour récupérer les enregistrements de serveur de noms faisant autorités et par conséquent, découvrir les serveurs de noms assignés à votre zone :

    C:\> azure network dns record-set show MyResourceGroup contoso.net @ NS
    info:    Executing command network dns record-set show
        + Looking up the DNS Record Set "@" of type "NS"
    data:    Id                              : /subscriptions/.../resourceGroups/MyResourceGroup/providers/Microsoft.Network/dnszones/contoso.net/NS/@
    data:    Name                            : @
    data:    Type                            : Microsoft.Network/dnszones/NS
    data:    Location                        : global
    data:    TTL                             : 172800
    data:    NS records
    data:        Name server domain name     : ns1-01.azure-dns.com.
    data:        Name server domain name     : ns2-01.azure-dns.net.
    data:        Name server domain name     : ns3-01.azure-dns.org.
    data:        Name server domain name     : ns4-01.azure-dns.info.
    data:
    info:    network dns record-set show command OK

### <a name="to-set-up-delegation"></a>Pour configurer la délégation

Chaque bureau d’enregistrement a ses propres outils de gestion DNS pour modifier les enregistrements de serveur de noms pour un domaine. Dans la page de gestion du DNS de registrar, modifiez les enregistrements NS et remplacez les enregistrements NS par ceux que Azure DNS créés.

Lors de la délégation d’un domaine à Azure DNS, vous devez utiliser les noms de serveur de nom fournis par le système DNS Azure.  Vous devez toujours utiliser toutes les 4 nom noms de serveur, quel que soit le nom de votre domaine.  Délégation de domaine ne nécessite pas le nom de serveur à utiliser le même domaine de niveau supérieur dans votre domaine.

Vous ne devez pas utiliser « coller des enregistrements » vers la Azure de noms DNS server adresses IP, dans la mesure où ces adresses IP peuvent changer à l’avenir. Délégation à l’aide de noms de serveur de nom de votre propre fuseau, parfois appelée « serveurs de noms vanity », n’est actuellement pas pris en charge dans le système DNS Azure.

### <a name="to-verify-name-resolution-is-working"></a>Pour vérifier la résolution de noms fonctionne

Fin de la délégation, vous pouvez vérifier que la résolution de noms fonctionne en utilisant un outil comme « nslookup » pour interroger l’enregistrement SOA pour votre zone (qui est créé automatiquement lorsque la zone est créée).

Notez que vous n’êtes pas obligé de spécifier les serveurs de noms DNS Azure, étant donné que le processus de résolution DNS normal rechercher les serveurs de noms automatiquement si la délégation a été configurée correctement.

    nslookup –type=SOA contoso.com

    Server: ns1-04.azure-dns.com
    Address: 208.76.47.4

    contoso.com
    primary name server = ns1-04.azure-dns.com
    responsible mail addr = msnhst.microsoft.com
    serial = 1
    refresh = 900 (15 mins)
    retry = 300 (5 mins)
    expire = 604800 (7 days)
    default TTL = 300 (5 mins)

## <a name="delegating-sub-domains-in-azure-dns"></a>Délégation de sous-domaines dans le système DNS Azure

Si vous souhaitez configurer une zone enfant distincte, vous pouvez déléguer un sous-domaine dans Azure DNS. Par exemple, avoir défini et déléguée « contoso.com » dans le système DNS Azure, supposons que vous voulez configurer une zone enfant séparée, « partners.contoso.com ».

La configuration d’un sous-domaine implique un processus semblable comme une délégation normale. La seule différence qui est à l’étape 3 que les enregistrements NS doivent être créés dans la zone parent « contoso.com » dans Azure DNS, plutôt que d’être configurés via un enregistrement de domaines.


1. Créer la zone enfant « partners.contoso.com » dans le système DNS Azure.
2. Rechercher les enregistrements de serveur de noms faisant autorités dans la zone enfant pour obtenir les serveurs de noms qui héberge la zone enfant dans le système DNS Azure.
3. Déléguer la zone enfant en configurant les enregistrements de serveur de noms dans la zone parente pointant vers la zone enfant.


### <a name="to-delegate-a-sub-domain"></a>Déléguer un sous-domaine

L’exemple de PowerShell suivant montre comment cela fonctionne. Les mêmes étapes peuvent être exécutées via le portail Azure, ou via l’infrastructure du langage commun Azure disponibilité sur plusieurs plateformes.

#### <a name="step-1-create-the-parent-and-child-zones"></a>Étape 1. Créer des zones de la connexion parent / enfant

Nous commençons par créer la connexion parent / enfant zones. Il peut s’agir dans le même groupe de ressources ou des différents groupes de ressources.

    $parent = New-AzureRmDnsZone -Name contoso.com -ResourceGroupName RG1
    $child = New-AzureRmDnsZone -Name partners.contoso.com -ResourceGroupName RG1

#### <a name="step-2-retrieve-ns-records"></a>Étape 2. Enregistrements de serveur de noms récupérer

Ensuite, nous récupérer les enregistrements de serveur de noms faisant autorités à partir de la zone enfant comme illustré dans l’exemple suivant.  Cet onglet contient les serveurs de noms assignés à la zone enfant.

    $child_ns_recordset = Get-AzureRmDnsRecordSet -Zone $child -Name "@" -RecordType NS

#### <a name="step-3-delegate-the-child-zone"></a>Étape 3. Déléguer la zone enfant

Créer un enregistrement NS correspondante définie dans la zone parent pour terminer la délégation. Notez que le nom du jeu d’enregistrements dans la zone parent correspond au nom de zone enfant, dans ce cas « partenaires ».

    $parent_ns_recordset = New-AzureRmDnsRecordSet -Zone $parent -Name "partners" -RecordType NS -Ttl 3600
    $parent_ns_recordset.Records = $child_ns_recordset.Records
    Set-AzureRmDnsRecordSet -RecordSet $parent_ns_recordset

### <a name="to-verify-name-resolution-is-working"></a>Pour vérifier la résolution de noms fonctionne

Vous pouvez vérifier que tout est configuré correctement en recherchant l’enregistrement SOA de la zone enfant.

    nslookup –type=SOA partners.contoso.com

    Server: ns1-08.azure-dns.com
    Address: 208.76.47.8

    partners.contoso.com
        primary name server = ns1-08.azure-dns.com
        responsible mail addr = msnhst.microsoft.com
        serial = 1
        refresh = 900 (15 mins)
        retry = 300 (5 mins)
        expire = 604800 (7 days)
        default TTL = 300 (5 mins)

## <a name="next-steps"></a>Étapes suivantes

[Gérer les zones DNS](dns-operations-dnszones.md)

[Gérer les enregistrements DNS](dns-operations-recordsets.md)


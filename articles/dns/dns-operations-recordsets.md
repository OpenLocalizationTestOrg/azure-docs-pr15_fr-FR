<properties
   pageTitle="Gérer les jeux d’enregistrements DNS et des enregistrements à l’aide du portail Azure | Microsoft Azure"
   description="Gestion des jeux d’enregistrements DNS et enregistrements DNS Azure lors de l’hébergement de votre domaine sur Azure DNS. Toutes les commandes PowerShell pour les opérations sur les jeux d’enregistrements et des enregistrements."
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

# <a name="manage-dns-records-and-record-sets-by-using-powershell"></a>Gérer les enregistrements DNS et des jeux d’enregistrements à l’aide de PowerShell



> [AZURE.SELECTOR]
- [Portail Azure](dns-operations-recordsets-portal.md)
- [Azure infrastructure du langage commun](dns-operations-recordsets-cli.md)
- [PowerShell](dns-operations-recordsets.md)



Cet article vous explique comment gérer les jeux d’enregistrements et des enregistrements pour votre zone DNS à l’aide de Windows PowerShell.

Il est important de comprendre la différence entre les jeux d’enregistrements DNS et des enregistrements DNS. Un jeu d’enregistrements est la collection d’enregistrements dans une zone qui ont le même nom et sont du même type. Pour plus d’informations, voir [jeux d’enregistrements DNS créer et enregistrements à l’aide du portail Azure](dns-getstarted-create-recordset-portal.md).

Pour gérer vos jeux d’enregistrements et les enregistrements, vous devez la dernière version des applets de commande PowerShell de gestionnaire de ressources Azure. Pour plus d’informations, voir [comment installer et configurer Azure PowerShell](../powershell-install-configure.md). Pour plus d’informations sur l’utilisation de PowerShell, voir [Utilisation de PowerShell Azure avec le Gestionnaire de ressources Azure](../powershell-azure-resource-manager.md).



## <a name="create-a-new-record-set-and-a-record"></a>Créer un nouveau jeu d’enregistrements et d’un enregistrement

Pour créer un jeu à l’aide de PowerShell d’enregistrements, voir [jeux d’enregistrements DNS créer et les enregistrements à l’aide de PowerShell](dns-getstarted-create-recordset.md).

## <a name="get-a-record-set"></a>Obtenir un jeu d’enregistrements

Pour récupérer un jeu d’enregistrements existant, utilisez `Get-AzureRmDnsRecordSet`. Spécifier que l’enregistrement définie la zone nom relatif et le type d’enregistrement.

    $rs = Get-AzureRmDnsRecordSet –Name www –RecordType A -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup

Comme avec `New-AzureRmDnsRecordSet`, le nom de l’enregistrement doit être un nom relatif, ce qui signifie qu’il doit exclure le nom de la zone.

Vous pouvez spécifier la zone en utilisant le nom de la zone et le nom de groupe de ressources, ou en utilisant un objet de la zone :

    $zone = Get-AzureRmDnsZone -Name contoso.com -ResouceGroupName MyAzureResourceGroup
    $rs = Get-AzureRmDnsRecordSet -Name www –RecordType A -Zone $zone

`Get-AzureRmDnsRecordSet`Renvoie un objet local qui représente le jeu d’enregistrements qui a été créé dans le système DNS Azure.

## <a name="list-record-sets"></a>Liste de jeux d’enregistrements

Vous pouvez également utiliser`Get-AzureRmDnsRecordSet` à la liste de jeux d’enregistrements si vous omettez le *– nom* et/ou *RecordType –* paramètres.

### <a name="to-list-all-record-sets"></a>Pour répertorier tous les jeux d’enregistrements

Cet exemple renvoie tous les jeux d’enregistrements, quel que soit le nom ou type d’enregistrement :

    $list = Get-AzureRmDnsRecordSet -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup

### <a name="to-list-record-sets-of-a-given-record-type"></a>Pour les jeux d’enregistrements liste d’un type d’enregistrement donné

Cet exemple renvoie tous les jeux d’enregistrements qui correspondent au type d’enregistrement donné. Dans ce cas, l’enregistrement qui est la valeur renvoyée est « A » enregistrements :

    $list = Get-AzureRmDnsRecordSet –RecordType A -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup

La zone peut être spécifiée en utilisant soit les *Nom de zone –* et *– ResourceGroupName* paramètres (comme illustré) ou en spécifiant un objet de la zone :

    $zone = Get-AzureRmDnsZone -Name contoso.com -ResouceGroupName MyAzureResourceGroup
    $list = Get-AzureRmDnsRecordSet -Zone $zone

## <a name="add-a-record-to-a-record-set"></a>Ajouter un enregistrement à un jeu d’enregistrements

Vous ajoutez des enregistrements à jeux d’enregistrements à l’aide de la `Add-AzureRmDnsRecordConfig` applet de commande. Il s’agit d’une opération hors connexion. Uniquement l’objet local qui représente le jeu d’enregistrements est modifié.

Les paramètres pour ajouter des enregistrements à un jeu d’enregistrements varient selon le type du jeu d’enregistrements. Par exemple, lorsque vous utilisez un jeu d’enregistrements de type « A », vous pouvez spécifier seulement les enregistrements avec le paramètre *-IPv4Address*.

Autres enregistrements peuvent être ajoutés à chaque enregistrement défini par les appels `Add-AzureRmDnsRecordConfig`. Vous pouvez ajouter jusqu'à 20 enregistrements à n’importe quel jeu d’enregistrements. Toutefois, jeux d’enregistrements de type « CNAME » peut contenir au plus un enregistrement et un jeu d’enregistrements ne peut pas contenir deux enregistrements identiques. Jeux d’enregistrements vide (avec zéro records) peut être créée, mais n’apparaisse pas sur les serveurs de noms DNS Azure.

Une fois que le jeu d’enregistrements contient la collection souhaitée d’enregistrements, vous devez valider à l’aide de la `Set-AzureRmDnsRecordSet` applet de commande. Une fois un jeu d’enregistrements a été validé, cette action remplace l’enregistrement existant définir dans Azure DNS.

### <a name="to-create-an-a-record-set-with-a-single-record"></a>Créer un enregistrement A la valeur avec un enregistrement unique

    $rs = New-AzureRmDnsRecordSet -Name "test-a" -RecordType A -Ttl 60 -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup
    Add-AzureRmDnsRecordConfig -RecordSet $rs -Ipv4Address "1.2.3.4"
    Set-AzureRmDnsRecordSet -RecordSet $rs

L’ordre des opérations pour créer un enregistrement peut également être *transmis*, ce qui signifie que vous passez l’objet jeu d’enregistrements en utilisant le canal plutôt qu’en passant en tant que paramètre. Par exemple :

    New-AzureRmDnsRecordSet -Name "test-a" -RecordType A -Ttl 60 -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup | Add-AzureRmDnsRecordConfig -Ipv4Address "1.2.3.4" | Set-AzureRmDnsRecordSet

### <a name="additional-record-type-examples"></a>Exemples de type d’enregistrement supplémentaires

[AZURE.INCLUDE [dns-add-record-ps-include](../../includes/dns-add-record-ps-include.md)]

## <a name="modify-existing-record-sets"></a>Modifier les jeux d’enregistrements existants

Les étapes de la modification d’un jeu d’enregistrements existant sont similaires aux étapes suivies lors de la création d’enregistrements. L’ordre des opérations se présente comme suit :

1.  Récupérer l’enregistrement existant à l’aide de la valeur `Get-AzureRmDnsRecordSet`.

2.  Modifier l’enregistrement définie par l’ajout d’enregistrements, suppression des enregistrements, modifier les paramètres d’enregistrement, ou modifiez l’enregistrement définissez durée de vie. Il s’agit d’une opération hors connexion. Uniquement l’objet local qui représente le jeu d’enregistrements est modifié.

3.  Valider les modifications apportées à l’aide de la `Set-AzureRmDnsRecordSet` applet de commande. Cette option remplace l’enregistrement existant définir dans Azure DNS.


### <a name="to-update-a-record-in-an-existing-record-set"></a>Mettre à jour un enregistrement dans un jeu d’enregistrements existant

Dans cet exemple, nous modifier l’adresse IP d’un enregistrement « A » existant :

    $rs = Get-AzureRmDnsRecordSet -name "test-a" -RecordType A -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup
    $rs.Records[0].Ipv4Address = "134.170.185.46"
    Set-AzureRmDnsRecordSet -RecordSet $rs

La `Set-AzureRmDnsRecordSet` applet de commande utilise etag tests pour s’assurer que les modifications simultanées ne sont pas remplacées. Utiliser le *-Remplacer* indicateur pour supprimer ces contrôles. Pour plus d’informations, voir [à propos des balises et etags](dns-getstarted-create-dnszone.md#tagetag).

### <a name="to-modify-an-soa-record"></a>Pour modifier un enregistrement SOA

Vous ne pouvez pas ajouter ou supprimer des enregistrements dans l’enregistrement SOA créés automatiquement définir au sommet zone (nom = "@"). Toutefois, vous pouvez modifier les paramètres dans l’enregistrement SOA (à l’exception de « hôte ») et l’enregistrement de la valeur TTL.

L’exemple suivant montre comment modifier la propriété de *messagerie* de l’enregistrement SOA :

    $rs = Get-AzureRmDnsRecordSet -Name "@" -RecordType SOA -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup
    $rs.Records[0].Email = "admin.contoso.com"
    Set-AzureRmDnsRecordSet -RecordSet $rs

### <a name="to-modify-ns-records-at-the-zone-apex"></a>Pour modifier les enregistrements de serveur de noms au sommet zone

Vous ne pouvez pas ajouter, pour supprimer ou modifier les enregistrements du créés automatiquement NS jeu d’enregistrements au sommet zone (nom = "@"). La seule modification est autorisée consiste à modifier le jeu d’enregistrements TTL.

L’exemple suivant montre comment modifier la propriété TTL du jeu d’enregistrements NS :

    $rs = Get-AzureRmDnsRecordSet -Name "@" -RecordType NS -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup
    $rs.Ttl = 300
    Set-AzureRmDnsRecordSet -RecordSet $rs

### <a name="to-add-records-to-an-existing-record-set"></a>Pour ajouter des enregistrements à un jeu d’enregistrements existant

Dans cet exemple, nous ajoutons deux autres enregistrements MX pour le jeu d’enregistrements existant :

    $rs = Get-AzureRmDnsRecordSet -name "test-mx" -RecordType MX -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup
    Add-AzureRmDnsRecordConfig -RecordSet $rs -Exchange "mail2.contoso.com" -Preference 10
    Add-AzureRmDnsRecordConfig -RecordSet $rs -Exchange "mail3.contoso.com" -Preference 20
    Set-AzureRmDnsRecordSet -RecordSet $rs

## <a name="remove-a-record-from-an-existing-record-set"></a>Supprimer un enregistrement d’un jeu d’enregistrements existant

Enregistrements peuvent être supprimés à partir d’un enregistrement à l’aide de la valeur `Remove-AzureRmDnsRecordConfig`. L’enregistrement est supprimé doit être une correspondance exacte avec un enregistrement existant sur tous les paramètres. Modifications doivent être validées à l’aide de `Set-AzureRmDnsRecordSet`.

Suppression du dernier enregistrement d’un jeu d’enregistrements ne supprime pas le jeu d’enregistrements. Pour plus d’informations, voir [Supprimer un jeu d’enregistrements](#delete-a-record-set) en dessous.


    $rs = Get-AzureRmDnsRecordSet -Name "test-a" -RecordType A -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup
    Remove-AzureRmDnsRecordConfig -RecordSet $rs -Ipv4Address "1.2.3.4"
    Set-AzureRmDnsRecordSet -RecordSet $rs

L’ordre des opérations pour supprimer un enregistrement à partir d’un jeu d’enregistrements peut également être transmis, ce qui signifie que vous passez l’objet jeu d’enregistrements en utilisant le canal plutôt qu’en passant en tant que paramètre. Par exemple :

    Get-AzureRmDnsRecordSet -Name "test-a" -RecordType A -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup | Remove-AzureRmDnsRecordConfig -Ipv4Address "1.2.3.4" | Set-AzureRmDnsRecordSet

### <a name="remove-an-aaaa-record-from-a-record-set"></a>Supprimer un enregistrement AAAA à partir d’un jeu d’enregistrements

    $rs = Get-AzureRmDnsRecordSet -Name "test-aaaa" -RecordType AAAA -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup
    Remove-AzureRmDnsRecordConfig -RecordSet $rs -Ipv6Address "2607:f8b0:4009:1803::1005"
    Set-AzureRmDnsRecordSet -RecordSet $rs

### <a name="remove-a-cname-record-from-a-record-set"></a>Supprimer un enregistrement CNAME à partir d’un jeu d’enregistrements

Comme un jeu d’enregistrements CNAME peut contenir au plus un enregistrement, suppression de cet enregistrement laisse un jeu d’enregistrements vide.

    $rs =  Get-AzureRmDnsRecordSet -name "test-cname" -RecordType CNAME -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup
    Remove-AzureRmDnsRecordConfig -RecordSet $rs -Cname "www.contoso.com"
    Set-AzureRmDnsRecordSet -RecordSet $rs

### <a name="remove-an-mx-record-from-a-record-set"></a>Supprimer un enregistrement MX d’un jeu d’enregistrements

    $rs = Get-AzureRmDnsRecordSet -name "test-mx" -RecordType MX -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup
    Remove-AzureRmDnsRecordConfig -RecordSet $rs -Exchange "mail.contoso.com" -Preference 5
    Set-AzureRmDnsRecordSet -RecordSet $rs

### <a name="remove-an-ns-record-from-record-set"></a>Supprimer un enregistrement de serveur de noms de jeu d’enregistrements

    $rs = Get-AzureRmDnsRecordSet -Name "test-ns" -RecordType NS -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup
    Remove-AzureRmDnsRecordConfig -RecordSet $rs -Nsdname "ns1.contoso.com"
    Set-AzureRmDnsRecordSet -RecordSet $rs

### <a name="remove-an-srv-record-from-a-record-set"></a>Supprimer un enregistrement SRV d’un jeu d’enregistrements

    $rs = Get-AzureRmDnsRecordSet -Name "_sip._tls" -RecordType SRV -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup
    Remove-AzureRmDnsRecordConfig -RecordSet $rs –Priority 0 –Weight 5 –Port 8080 –Target "sip.contoso.com"
    Set-AzureRmDnsRecordSet -RecordSet $rs

### <a name="remove-a-txt-record-from-a-record-set"></a>Supprimer un enregistrement TXT à partir d’un jeu d’enregistrements

    $rs = Get-AzureRmDnsRecordSet -Name "test-txt" -RecordType TXT -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup
    Remove-AzureRmDnsRecordConfig -RecordSet $rs -Value "This is a TXT record"
    Set-AzureRmDnsRecordSet -RecordSet $rs

## <a name="delete-a-record-set"></a>Supprimer un jeu d’enregistrements

Jeux d’enregistrements peut être supprimés à l’aide de la `Remove-AzureRmDnsRecordSet` applet de commande. Vous ne pouvez pas supprimer l’architecture orientée services et jeux d’enregistrement NS au sommet zone (nom = "@") qui ont été créées automatiquement lorsque la zone a été créée. Ils seront supprimés automatiquement si la zone est supprimée.

Pour supprimer un jeu d’enregistrements, utilisez un des trois méthodes suivantes :

### <a name="specify-all-the-parameters-by-name"></a>Spécifier tous les paramètres par nom

L’option *-Force* commutateur peut être utilisé pour supprimer l’invite de confirmation.

    Remove-AzureRmDnsRecordSet -Name "test-a" -RecordType A -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup [-Force]


### <a name="specify-the-record-set-by-name-and-type-and-specify-the-zone-by-object"></a>Spécifier le jeu d’enregistrements par nom et type, puis spécifiez la zone par objet

    $zone = Get-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup
    Remove-AzureRmDnsRecordSet -Name "test-a" -RecordType A -Zone $zone [-Force]

### <a name="specify-the-record-set-by-object"></a>Spécifier l’ensemble des enregistrements en objet

    $rs = Get-AzureRmDnsRecordSet -Name "test-a" -RecordType A -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup
    Remove-AzureRmDnsRecordSet –RecordSet $rs [-Overwrite] [-Force]

Lorsque vous spécifiez l’enregistrement définie à l’aide d’un objet, il permet de contrôles etag pour s’assurer que les modifications simultanées ne sont pas supprimées. L’option *-Remplacer* indicateur supprime ces vérifications. Pour plus d’informations, voir [Etags et balises](dns-getstarted-create-dnszone.md#tagetag) .

L’objet de jeu d’enregistrements peut également être transmis à la place de passé en tant que paramètre :

    Get-AzureRmDnsRecordSet -Name "test-a" -RecordType A -ZoneName contoso.com -ResourceGroupName MyAzureResourceGroup | Remove-AzureRmDnsRecordSet [-Overwrite] [-Force]

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur le système DNS Azure, voir [vue d’ensemble de DNS Azure](dns-overview.md). Pour plus d’informations sur l’automatisation DNS, voir [zones DNS création et enregistrement jeux en utilisant le Kit de développement .NET](dns-sdk.md).

Pour plus d’informations sur les enregistrements DNS inverses, voir [comment gérer les enregistrements DNS inverses pour vos services à l’aide de PowerShell](dns-reverse-dns-record-operations-ps.md).

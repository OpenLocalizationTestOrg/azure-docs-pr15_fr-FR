<properties
   pageTitle="Gérer les zones DNS à l’aide de PowerShell | Microsoft Azure"
   description="Vous pouvez gérer les zones DNS à l’aide de Powershell Azure. Comment mettre à jour, supprimer et créer des zones DNS sur DNS Azure"
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

# <a name="how-to-manage-dns-zones-using-powershell"></a>Comment gérer les Zones DNS à l’aide de PowerShell

> [AZURE.SELECTOR]
- [Azure infrastructure du langage commun](dns-operations-dnszones-cli.md)
- [PowerShell](dns-operations-dnszones.md)



Cet article vous explique comment gérer votre zone DNS à l’aide de PowerShell. Pour pouvoir utiliser ces étapes, vous devez installer la dernière version des applets de commande PowerShell de gestionnaire de ressources Azure (1.0 ou version ultérieure). Découvrez [comment installer et configurer Azure PowerShell](../powershell-install-configure.md) pour plus d’informations sur l’installation les applets de commande PowerShell.


## <a name="create-a-new-dns-zone"></a>Créer une nouvelle zone DNS

Pour créer une zone DNS, voir [créer une zone DNS à l’aide de PowerShell](dns-getstarted-create-dnszone.md).

## <a name="get-a-dns-zone"></a>Obtenir une zone DNS

Pour récupérer une zone DNS, utilisez la `Get-AzureRmDnsZone` applet de commande. Cette opération retourne un objet de zone DNS correspondant à une zone existante dans le système DNS Azure. L’objet contient des données sur la zone (par exemple, le nombre de jeux d’enregistrements), mais ne contient-elle pas les jeux d’enregistrement eux-mêmes.

    $zone = Get-AzureRmDnsZone -Name contoso.com –ResourceGroupName MyAzureResourceGroup

## <a name="list-dns-zones"></a>Zones DNS de liste

En omettant le nom de la zone à partir de `Get-AzureRmDnsZone`, vous pouvez énumérer toutes les zones dans un groupe de ressources. Cette opération retourne un tableau d’objets de la zone.

    $zoneList = Get-AzureRmDnsZone -ResourceGroupName MyAzureResourceGroup

## <a name="update-a-dns-zone"></a>Mettre à jour une zone DNS

Modifications à une ressource de zone DNS peuvent être effectuées à l’aide de `Set-AzureRmDnsZone`. Cela ne met pas à jour un des jeux d’enregistrement DNS dans la zone (voir [comment gérer DNS records](dns-operations-recordsets.md)). Il est utilisé uniquement pour mettre à jour des propriétés de la ressource de la zone elle-même. Il s’agit actuellement limitée pour le Gestionnaire de ressources Azure 'balises » pour les ressources de la zone. Pour plus d’informations, voir [Etags et balises](dns-getstarted-create-dnszone.md#Etags-and-tags) .

Appliquez l’une des opérations suivantes deux façons de mettre à jour DNS zone :

### <a name="specify-the-zone-using-the-zone-name-and-resource-group"></a>Spécifier la zone en utilisant le groupe zone Nom et des ressources

    Set-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup [-Tag $tags]

### <a name="specify-the-zone-using-a-zone-object"></a>Spécifier la zone à l’aide d’un objet $zone

Spécifier la zone à l’aide d’un objet $zone dans `Get-AzureRmDnsZone`. Lorsque vous utilisez `Set-AzureRmDnsZone` avec un objet $zone, tous les tests Etag servira à garantir modifications simultanées ne sont pas remplacées. Vous pouvez utiliser l’option *-Remplacer* pour accéder à supprimer ces contrôles. Pour plus d’informations, voir [Etags et balises](dns-getstarted-create-dnszone.md#Etags-and-tags) .


    $zone = Get-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup
    <..modify $zone.Tags here...>
    Set-AzureRmDnsZone -Zone $zone [-Overwrite]


## <a name="delete-a-dns-zone"></a>Supprimer une Zone DNS

Zones DNS peuvent être supprimés à l’aide de l’applet de commande Supprimer AzureRmDnsZone.

Avant de supprimer une zone DNS dans DNS Azure, vous devez supprimer tous les jeux d’enregistrements, à l’exception des enregistrements NS et SOA à la racine de la zone qui ont été créés automatiquement lorsque la zone a été créée.

Utilisez une des manières suivantes pour supprimer une zone DNS :

### <a name="specify-the-zone-using-the-zone-name-and-resource-group-name"></a>Spécifier la zone en utilisant le nom de la zone et le nom de groupe de ressources

Cette opération a facultatif *-Force* commutateur qui supprime l’invite à confirmer que vous voulez supprimer la zone DNS.

    Remove-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup [-Force]

### <a name="specify-the-zone-using-a-zone-object"></a>Spécifier la zone à l’aide d’un objet $zone

Spécifier la zone à l’aide d’un objet $zone dans `Get-AzureRmDnsZone`. Cette opération a facultatif *-Force* commutateur qui supprime l’invite à confirmer que vous voulez supprimer la zone DNS. Comme avec `Set-AzureRmDnsZone`, en spécifiant la zone à l’aide d’un objet $zone vous Etag vérifie que les modifications simultanées ne sont pas supprimées. <BR>
L’option *-Remplacer* indicateur supprime ces vérifications. Pour plus d’informations, voir [Etags et balises](dns-getstarted-create-dnszone.md#Etags-and-tags) .

    $zone = Get-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup
    Remove-AzureRmDnsZone -Zone $zone [-Force] [-Overwrite]



L’objet de la zone peut également être transmis à la place de passé en tant que paramètre :

    Get-AzureRmDnsZone -Name contoso.com -ResourceGroupName MyAzureResourceGroup | Remove-AzureRmDnsZone [-Force] [-Overwrite]

## <a name="next-steps"></a>Étapes suivantes

Après avoir créé une zone DNS, créer des [ensembles d’enregistrements et des enregistrements](dns-getstarted-create-recordset.md) pour démarrer la résolution de noms pour votre domaine Internet.
<properties
   pageTitle="Comment gérer les listes de contrôle d’accès (utilisateurs) pour les points de terminaison à l’aide de PowerShell"
   description="Découvrez comment gérer les utilisateurs avec PowerShell"
   services="virtual-network"
   documentationCenter="na"
   authors="jimdial"
   manager="carmonm"
   editor="tysonn" />
<tags
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="03/15/2016"
   ms.author="jdial" />

# <a name="how-to-manage-access-control-lists-acls-for-endpoints-by-using-powershell"></a>Comment gérer les listes de contrôle d’accès (utilisateurs) pour les points de terminaison à l’aide de PowerShell

Vous pouvez créer et gérer les listes de contrôle d’accès réseau (utilisateurs) pour les points de terminaison à l’aide de PowerShell Azure ou dans le portail de gestion. Dans cette rubrique, vous trouverez les procédures pour les tâches courantes et que vous pouvez effectuer à l’aide de PowerShell. Pour la liste des applets de commande PowerShell Azure voir [Applets de commande de gestion Azure](http://go.microsoft.com/fwlink/?LinkId=317721). Pour plus d’informations sur les utilisateurs, voir [qu’est une liste de contrôle (accès) de réseau ?](virtual-networks-acl.md). Si vous voulez gérer vos utilisateurs à l’aide du portail de gestion, Découvrez [comment définir des points de terminaison pour une Machine virtuelle](../virtual-machines/virtual-machines-windows-classic-setup-endpoints.md).

## <a name="manage-network-acls-by-using-azure-powershell"></a>Gérer les utilisateurs du réseau à l’aide de PowerShell Azure

Vous pouvez utiliser les applets de commande PowerShell Azure pour créer, supprimer et (configurer) réseau accès aux listes de contrôle (). Nous avons inclus quelques exemples de quelques-unes des méthodes que vous pouvez configurer un et l’utilisation de PowerShell.

Pour obtenir une liste complète des applets de commande PowerShell et, vous pouvez utiliser une des opérations suivantes :

    Get-Help *AzureACL*
    Get-Command -Noun AzureACLConfig

### <a name="create-a-network-acl-with-rules-that-permit-access-from-a-remote-subnet"></a>Créer un réseau et avec des règles qui autorise l’accès à partir d’un sous-réseau à distance

L’exemple suivant illustre un moyen de créer un nouveau et qui contient des règles. Cette et est ensuite appliquée à un point de terminaison machine virtuelle. Les règles et dans l’exemple ci-dessous autorise l’accès à partir d’un sous-réseau distant. Pour créer un nouveau et réseau avec des règles d’autorisation pour un sous-réseau distant, ouvrez un Azure PowerShell ISE. Copiez et collez le script ci-dessous, en configurant le script avec vos propres valeurs et exécutez le script.

1. Créer l’objet et réseau.

        $acl1 = New-AzureAclConfig

1. Définir une règle qui autorise l’accès à partir d’un sous-réseau distant. Dans l’exemple ci-dessous, vous définissez règle *100* (qui a la priorité sur règle 200 ou version ultérieure) pour permettre l’accès *10.0.0.0/8* sous-réseau distant au point de terminaison machine virtuelle. Remplacer les valeurs par votre propre configuration requise. Le nom « SharePoint ACL config » doit être remplacé par le nom convivial que vous souhaitez appeler cette règle.

        Set-AzureAclConfig –AddRule –ACL $acl1 –Order 100 `
            –Action permit –RemoteSubnet "10.0.0.0/8" `
            –Description "SharePoint ACL config"

1. D’autres règles, répétez l’applet de commande, en remplaçant les valeurs par votre propre configuration requise. N’oubliez pas de modifier la règle de nombre afin de refléter l’ordre dans lequel vous souhaitez les règles à appliquer. Le nombre inférieur de la règle est prioritaire sur le nombre plus élevé.

        Set-AzureAclConfig –AddRule –ACL $acl1 –Order 200 `
            –Action permit –RemoteSubnet "157.0.0.0/8" `
            –Description "web frontend ACL config"

1. Ensuite, vous pouvez créer un point de terminaison (Ajouter) ou définir l’et pour un point de terminaison existant (défini). Dans cet exemple, nous ajouter un nouveau point de terminaison machine virtuelle appelée « web » et mettre à jour le point de terminaison machine virtuelle avec les paramètres et.

        Get-AzureVM –ServiceName $serviceName –Name $vmName `
        | Add-AzureEndpoint –Name "web" –Protocol tcp –Localport 80 - PublicPort 80 –ACL $acl1 `
        | Update-AzureVM

1. Ensuite, combiner les applets de commande et exécuter le script. Dans cet exemple, les applets de commande combinée présente comme suit :

        $acl1 = New-AzureAclConfig
        Set-AzureAclConfig –AddRule –ACL $acl1 –Order 100 `
            –Action permit –RemoteSubnet "10.0.0.0/8" `
            –Description "Sharepoint ACL config"
        Set-AzureAclConfig –AddRule –ACL $acl1 –Order 200 `
            –Action permit –RemoteSubnet "157.0.0.0/8" `
            –Description "web frontend ACL config"
        Get-AzureVM –ServiceName $serviceName –Name $vmName `
        |Add-AzureEndpoint –Name "web" –Protocol tcp –Localport 80 - PublicPort 80 –ACL $acl1 `
        |Update-AzureVM

### <a name="remove-a-network-acl-rule-that-permits-access-from-a-remote-subnet"></a>Supprimer une règle de réseau et qui autorise l’accès à partir d’un sous-réseau à distance

L’exemple suivant illustre comment supprimer une règle et réseau.  Pour supprimer une règle et réseau avec des règles d’autorisation pour un sous-réseau distant, ouvrez un Azure PowerShell ISE. Copiez et collez le script ci-dessous, en configurant le script avec vos propres valeurs et exécutez le script.

1. Première étape consiste à obtenir l’objet et de réseau pour le point de terminaison machine virtuelle. Vous pouvez ensuite supprimer la règle et. Dans ce cas, nous allons la suppression par ID de règle. Cela ne supprime que l’ID de règle 0 dans de l’accès. Il ne supprime pas l’objet et du point de terminaison machine virtuelle.

        Get-AzureVM –ServiceName $serviceName –Name $vmName `
        | Get-AzureAclConfig –EndpointName "web" `
        | Set-AzureAclConfig –RemoveRule –ID 0 –ACL $acl1

1. Ensuite, vous devez appliquer l’objet de réseau et au point de terminaison machine virtuelle et mettre à jour de la machine virtuelle.

        Get-AzureVM –ServiceName $serviceName –Name $vmName `
        | Set-AzureEndpoint –ACL $acl1 –Name "web" `
        | Update-AzureVM

### <a name="remove-a-network-acl-from-a-virtual-machine-endpoint"></a>Supprimer un réseau et d’un point de terminaison machine virtuelle

Dans certains cas, vous souhaiterez peut-être supprimer un objet de réseau et d’un point de terminaison machine virtuelle. Pour ce faire, ouvrez un Azure PowerShell ISE. Copiez et collez le script ci-dessous, en configurant le script avec vos propres valeurs et exécutez le script.

        Get-AzureVM –ServiceName $serviceName –Name $vmName `
        | Remove-AzureAclConfig –EndpointName "web" `
        | Update-AzureVM

## <a name="next-steps"></a>Étapes suivantes

[Qu’est une liste de contrôle (accès) de réseau ?](virtual-networks-acl.md)

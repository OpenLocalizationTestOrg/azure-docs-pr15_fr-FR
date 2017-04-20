<properties
    pageTitle="Gérer les Azure CDN avec PowerShell | Microsoft Azure"
    description="Découvrez comment utiliser les applets de commande PowerShell Azure pour gérer Azure CDN."
    services="cdn"
    documentationCenter=""
    authors="camsoper"
    manager="erikre"
    editor=""/>

<tags
    ms.service="cdn"
    ms.workload="tbd"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/17/2016"
    ms.author="casoper"/>


# <a name="manage-azure-cdn-with-powershell"></a>Gérer les Azure CDN avec PowerShell

PowerShell fournit une des méthodes plus flexibles pour gérer vos profils Azure CDN et les points de terminaison.  Vous pouvez utiliser PowerShell interactive ou en écriture de scripts pour automatiser les tâches de gestion.  Ce didacticiel montre plusieurs tâches les plus courantes que vous pouvez effectuer avec PowerShell pour gérer vos profils Azure CDN et les points de terminaison.

## <a name="prerequisites"></a>Conditions préalables

Pour utiliser PowerShell pour gérer vos profils Azure CDN et les points de terminaison, vous devez disposer installé le module Azure PowerShell.  Pour savoir comment installer Azure PowerShell et se connecter à Azure à l’aide de la `Login-AzureRmAccount` applet de commande, voir [comment installer et configurer Azure PowerShell](../powershell-install-configure.md).

>[AZURE.IMPORTANT] Vous devez vous connecter à l’aide `Login-AzureRmAccount` avant de pouvoir exécuter les applets de commande PowerShell Azure.

## <a name="listing-the-azure-cdn-cmdlets"></a>Répertorier les applets de commande Azure CDN

Vous pouvez répertorier toutes les applets de commande Azure CDN à l’aide de la `Get-Command` applet de commande.

```text
PS C:\> Get-Command -Module AzureRM.Cdn

CommandType     Name                                               Version    Source
-----------     ----                                               -------    ------
Cmdlet          Get-AzureRmCdnCustomDomain                         2.0.0      AzureRm.Cdn
Cmdlet          Get-AzureRmCdnEndpoint                             2.0.0      AzureRm.Cdn
Cmdlet          Get-AzureRmCdnEndpointNameAvailability             2.0.0      AzureRm.Cdn
Cmdlet          Get-AzureRmCdnOrigin                               2.0.0      AzureRm.Cdn
Cmdlet          Get-AzureRmCdnProfile                              2.0.0      AzureRm.Cdn
Cmdlet          Get-AzureRmCdnProfileSsoUrl                        2.0.0      AzureRm.Cdn
Cmdlet          New-AzureRmCdnCustomDomain                         2.0.0      AzureRm.Cdn
Cmdlet          New-AzureRmCdnEndpoint                             2.0.0      AzureRm.Cdn
Cmdlet          New-AzureRmCdnProfile                              2.0.0      AzureRm.Cdn
Cmdlet          Publish-AzureRmCdnEndpointContent                  2.0.0      AzureRm.Cdn
Cmdlet          Remove-AzureRmCdnCustomDomain                      2.0.0      AzureRm.Cdn
Cmdlet          Remove-AzureRmCdnEndpoint                          2.0.0      AzureRm.Cdn
Cmdlet          Remove-AzureRmCdnProfile                           2.0.0      AzureRm.Cdn
Cmdlet          Set-AzureRmCdnEndpoint                             2.0.0      AzureRm.Cdn
Cmdlet          Set-AzureRmCdnOrigin                               2.0.0      AzureRm.Cdn
Cmdlet          Set-AzureRmCdnProfile                              2.0.0      AzureRm.Cdn
Cmdlet          Start-AzureRmCdnEndpoint                           2.0.0      AzureRm.Cdn
Cmdlet          Stop-AzureRmCdnEndpoint                            2.0.0      AzureRm.Cdn
Cmdlet          Test-AzureRmCdnCustomDomain                        2.0.0      AzureRm.Cdn
Cmdlet          Unpublish-AzureRmCdnEndpointContent                2.0.0      AzureRm.Cdn
```

## <a name="getting-help"></a>Obtenir de l’aide

Vous pouvez obtenir de l’aide d’un de ces applets de commande à l’aide de la `Get-Help` applet de commande.  `Get-Help`Fournit la syntaxe et l’utilisation et éventuellement répertorie des exemples.

```text
PS C:\> Get-Help Get-AzureRmCdnProfile

NAME
    Get-AzureRmCdnProfile

SYNOPSIS
    Gets an Azure CDN profile.


SYNTAX
    Get-AzureRmCdnProfile [-ProfileName <String>] [-ResourceGroupName <String>] [-InformationAction
    <ActionPreference>] [-InformationVariable <String>] [<CommonParameters>]


DESCRIPTION
    Gets an Azure CDN profile and all related information.


RELATED LINKS

REMARKS
    To see the examples, type: "get-help Get-AzureRmCdnProfile -examples".
    For more information, type: "get-help Get-AzureRmCdnProfile -detailed".
    For technical information, type: "get-help Get-AzureRmCdnProfile -full".

```

## <a name="listing-existing-azure-cdn-profiles"></a>Liste des profils Azure CDN existants

La `Get-AzureRmCdnProfile` applet de commande sans paramètres extrait tous vos profils CDN existants.

```powershell
Get-AzureRmCdnProfile
```

Ce résultat peut être transmis aux applets de commande pour énumération.

```powershell
# Output the name of all profiles on this subscription.
Get-AzureRmCdnProfile | ForEach-Object { Write-Host $_.Name }

# Return only **Azure CDN from Verizon** profiles.
Get-AzureRmCdnProfile | Where-Object { $_.Sku.Name -eq "StandardVerizon" }
```

Vous pouvez également renvoyer un seul profil en spécifiant le groupe de nom et des ressources de profil.

```powershell
Get-AzureRmCdnProfile -ProfileName CdnDemo -ResourceGroupName CdnDemoRG
```

>[AZURE.TIP] Il est possible d’avoir plusieurs profils CDN portant le même nom, dans la mesure où ils se trouvent dans différents groupes de ressources.  L’omission de la `ResourceGroupName` paramètre renvoie tous les profils avec un nom correspondant.

## <a name="listing-existing-cdn-endpoints"></a>Points de terminaison CDN existants annonce

`Get-AzureRmCdnEndpoint`pouvez récupérer un point de terminaison individuel ou tous les points de terminaison sur un profil.  

```powershell
# Get a single endpoint.
Get-AzureRmCdnEndpoint -ProfileName CdnDemo -ResourceGroupName CdnDemoRG -EndpointName cdndocdemo

# Get all of the endpoints on a given profile. 
Get-AzureRmCdnEndpoint -ProfileName CdnDemo -ResourceGroupName CdnDemoRG

# Return all of the endpoints on all of the profiles.
Get-AzureRmCdnProfile | Get-AzureRmCdnEndpoint

# Return all of the endpoints in this subscription that are currently running.
Get-AzureRmCdnProfile | Get-AzureRmCdnEndpoint | Where-Object { $_.ResourceState -eq "Running" }
```

## <a name="creating-cdn-profiles-and-endpoints"></a>Création de points de terminaison et profils CDN

`New-AzureRmCdnProfile`et `New-AzureRmCdnEndpoint` sont utilisées pour créer des profils CDN et points de terminaison.

```powershell
# Create a new profile
New-AzureRmCdnProfile -ProfileName CdnPoshDemo -ResourceGroupName CdnDemoRG -Sku StandardAkamai -Location "Central US"

# Create a new endpoint
New-AzureRmCdnEndpoint -ProfileName CdnPoshDemo -ResourceGroupName CdnDemoRG -Location "Central US" -EndpointName cdnposhdoc -OriginName "Contoso" -OriginHostName "www.contoso.com"

# Create a new profile and endpoint (same as above) in one line
New-AzureRmCdnProfile -ProfileName CdnPoshDemo -ResourceGroupName CdnDemoRG -Sku StandardAkamai -Location "Central US" | New-AzureRmCdnEndpoint -EndpointName cdnposhdoc -OriginName "Contoso" -OriginHostName "www.contoso.com"

```

## <a name="checking-endpoint-name-availability"></a>Vérification de la disponibilité du nom du point de terminaison

`Get-AzureRmCdnEndpointNameAvailability`Renvoie un objet indiquant si un nom de point de terminaison est disponible.

```powershell
# Retrieve availability
$availability = Get-AzureRmCdnEndpointNameAvailability -EndpointName "cdnposhdoc"

# If available, write a message to the console.
If($availability.NameAvailable) { Write-Host "Yes, that endpoint name is available." }
Else { Write-Host "No, that endpoint name is not available." }
```

## <a name="adding-a-custom-domain"></a>Ajout d’un domaine personnalisé

`New-AzureRmCdnCustomDomain`Ajoute un nom de domaine personnalisé à un point de terminaison existant.

>[AZURE.IMPORTANT] Vous devez configurer l’enregistrement CNAME auprès de votre fournisseur DNS, comme décrit dans [le mappage de domaine personnalisé au point de terminaison de réseau de remise de contenu (CDN)](./cdn-map-content-to-custom-domain.md).  Vous pouvez tester le mappage avant de modifier votre point de terminaison à l’aide `Test-AzureRmCdnCustomDomain`.

```powershell
# Get an existing endpoint
$endpoint = Get-AzureRmCdnEndpoint -ProfileName CdnPoshDemo -ResourceGroupName CdnDemoRG -EndpointName cdnposhdoc

# Check the mapping
$result = Test-AzureRmCdnCustomDomain -CdnEndpoint $endpoint -CustomDomainHostName "cdn.contoso.com"

# Create the custom domain on the endpoint
If($result.CustomDomainValidated){ New-AzureRmCdnCustomDomain -CustomDomainName Contoso -HostName "cdn.contoso.com" -CdnEndpoint $endpoint }
```

## <a name="modifying-an-endpoint"></a>Modification d’un point de terminaison

`Set-AzureRmCdnEndpoint`modifie un point de terminaison existant.

```powershell
# Get an existing endpoint
$endpoint = Get-AzureRmCdnEndpoint -ProfileName CdnPoshDemo -ResourceGroupName CdnDemoRG -EndpointName cdnposhdoc

# Set up content compression
$endpoint.IsCompressionEnabled = $true
$endpoint.ContentTypesToCompress = "text/javascript","text/css","application/json"

# Save the changed endpoint and apply the changes
Set-AzureRmCdnEndpoint -CdnEndpoint $endpoint
```

## <a name="purgingpre-loading-cdn-assets"></a>Purge/pré-loading actifs CDN

`Unpublish-AzureRmCdnEndpointContent`Purge mis en cache actifs, tandis que `Publish-AzureRmCdnEndpointContent` charge des biens sur les points de terminaison pris en charge.

```powershell
# Purge some assets.
Unpublish-AzureRmCdnEndpointContent -ProfileName CdnDemo -ResourceGroupName CdnDemoRG -EndpointName cdndocdemo -PurgeContent "/images/kitten.png","/video/rickroll.mp4"

# Pre-load some assets.
Publish-AzureRmCdnEndpointContent -ProfileName CdnDemo -ResourceGroupName CdnDemoRG -EndpointName cdndocdemo -LoadContent "/images/kitten.png","/video/rickroll.mp4"

# Purge everything in /images/ on all endpoints.
Get-AzureRmCdnProfile | Get-AzureRmCdnEndpoint | Unpublish-AzureRmCdnEndpointContent -PurgeContent "/images/*"
```

## <a name="startingstopping-cdn-endpoints"></a>Points de terminaison CDN démarrage/arrêt
`Start-AzureRmCdnEndpoint`et `Stop-AzureRmCdnEndpoint` peut être utilisé pour démarrer et arrêter les points de terminaison individuels ou des groupes de points de terminaison.

```powershell
# Stop the cdndocdemo endpoint
Stop-AzureRmCdnEndpoint -ProfileName CdnDemo -ResourceGroupName CdnDemoRG -EndpointName cdndocdemo

# Stop all endpoints
Get-AzureRmCdnProfile | Get-AzureRmCdnEndpoint | Stop-AzureRmCdnEndpoint

# Start all endpoints
Get-AzureRmCdnProfile | Get-AzureRmCdnEndpoint | Start-AzureRmCdnEndpoint
```

## <a name="deleting-cdn-resources"></a>Suppression de ressources CDN

`Remove-AzureRmCdnProfile`et `Remove-AzureRmCdnEndpoint` peut être utilisée pour supprimer des profils et points de terminaison.

```powershell
# Remove a single endpoint
Remove-AzureRmCdnEndpoint -ProfileName CdnPoshDemo -ResourceGroupName CdnDemoRG -EndpointName cdnposhdoc

# Remove all the endpoints on a profile and skip confirmation (-Force)
Get-AzureRmCdnProfile -ProfileName CdnPoshDemo -ResourceGroupName CdnDemoRG | Get-AzureRmCdnEndpoint | Remove-AzureRmCdnEndpoint -Force

# Remove a single profile
Remove-AzureRmCdnProfile -ProfileName CdnPoshDemo -ResourceGroupName CdnDemoRG
```

## <a name="next-steps"></a>Étapes suivantes

Découvrez comment automatiser Azure CDN avec [.NET](./cdn-app-dev-net.md) ou [Node.js](./cdn-app-dev-node.md).

Pour en savoir plus sur les fonctionnalités CDN, voir [Vue d’ensemble du fournisseur](./cdn-overview.md).



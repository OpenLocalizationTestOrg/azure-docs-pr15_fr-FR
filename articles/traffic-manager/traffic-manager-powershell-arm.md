<properties
    pageTitle="Gestionnaire de ressources Azure prise en charge pour le trafic Manager | Microsoft Azure "
    description="À l’aide de PowerShell pour le Gestionnaire de trafic avec le Gestionnaire de ressources Azure"
    services="traffic-manager"
    documentationCenter="na"
    authors="sdwheeler"
    manager="carmonm"
    editor=""
/>
<tags
    ms.service="traffic-manager"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="infrastructure-services"
    ms.date="10/11/2016"
    ms.author="sewhee"
/>

# <a name="azure-resource-manager-support-for-azure-traffic-manager"></a>Gestionnaire de ressources Azure prise en charge pour le Gestionnaire de trafic Azure

Le Gestionnaire de ressources Azure est l’interface préférée de gestion des services dans Azure. Azure profils de trafic Manager peuvent être gérés à l’aide des outils et des API basées sur le Gestionnaire de ressources Azure.

## <a name="resource-model"></a>Modèle de ressources

Gestionnaire de trafic Azure est configuré à l’aide d’un ensemble de paramètres appelé un profil Manager le trafic. Ce profil contient des paramètres DNS, les paramètres de routage du trafic, les paramètres de contrôle de point de terminaison et une liste des points de terminaison de service auquel le trafic est acheminé.

Chaque profil le trafic gestionnaire est représenté par une ressource de type « TrafficManagerProfiles ». Au niveau de l’API REST, l’URI pour chaque profil est la suivante :

    https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Network/trafficManagerProfiles/{profile-name}?api-version={api-version}

## <a name="comparison-with-the-azure-traffic-manager-classic-api"></a>Comparaison avec l’API classique Azure le trafic Manager

La prise en charge du Gestionnaire de ressources Azure pour le trafic Manager utilise la terminologie autre que le modèle de déploiement classique. Le tableau suivant montre les différences entre le Gestionnaire de ressources et classique les conditions :

| Termes du Gestionnaire de ressources | Magasin de termes classique |
|-----------------------|--------------|
| Méthode de routage du trafic | Méthode de l’équilibrage de charge |
| Méthode de priorité | Méthode de basculement |
| Méthode pondérée | Méthode alternées |
| Méthode de performances | Méthode de performances |

Selon les commentaires des clients, nous avons modifié la terminologie pour améliorer la clarté et réduire malentendus courants. Il n’existe aucune différence en termes de fonctionnalité.

## <a name="limitations"></a>Limitations

Lors du référencement d’un point de terminaison de type « AzureEndpoints » pour une application Web, le trafic Gestionnaire de points de terminaison ne peuvent référencer la valeur par défaut (production) [emplacement Web App](../app-service-web/web-sites-staged-publishing.md). Emplacements personnalisés ne sont pas pris en charge. Pour résoudre ce problème, emplacements personnalisés peuvent être configurés à l’aide du type « ExternalEndpoints ».

## <a name="setting-up-azure-powershell"></a>La configuration de PowerShell Azure

Ces instructions utilisent Microsoft Azure PowerShell. L’article suivant explique comment installer et configurer Azure PowerShell.

- [Comment installer et configurer PowerShell Azure](../powershell-install-configure.md)

Les exemples de cet article part du principe que vous disposez d’un groupe de ressources existant. Vous pouvez créer un groupe de ressources à l’aide de la commande suivante :

```powershell
    New-AzureRmResourceGroup -Name MyRG -Location "West US"
```

>[AZURE.NOTE] Le Gestionnaire de ressources Azure nécessite que tous les groupes ressources un emplacement. Cet emplacement est utilisé par défaut pour les ressources créées dans ce groupe de ressources. Toutefois, étant donné que le trafic Gestionnaire profil ressources sont globale, pas régionaux, le choix de la position du groupe de ressources n’a aucun impact sur le Gestionnaire de trafic Azure.

## <a name="create-a-traffic-manager-profile"></a>Créer un profil le trafic Manager

Pour créer un profil le trafic Manager, utilisez l’applet de commande New-AzureRmTrafficManagerProfile :

```powershell
    $profile = New-AzureRmTrafficManagerProfile -Name MyProfile -ResourceGroupName MyRG -TrafficRoutingMethod Performance -RelativeDnsName contoso -Ttl 30 -MonitorProtocol HTTP -MonitorPort 80 -MonitorPath "/"
```

Le tableau suivant décrit les paramètres :

| Paramètre | Description |
|-----------|-------------|
| Nom | Le nom de la ressource pour la ressource de profil gestionnaire le trafic. Les profils dans le même groupe de ressources doivent avoir des noms uniques. Ce nom est différent du nom DNS utilisé pour les requêtes DNS.|
| ResourceGroupName | Le nom du groupe de ressources contenant la ressource de profil.|
| TrafficRoutingMethod | Spécifie la méthode de routage du trafic utilisée pour déterminer quel point de terminaison est renvoyé en réponse à une requête DNS. Les valeurs possibles sont 'Performances', « Weighted » ou « Priorité ».|
| RelativeDnsName | Spécifie la partie du nom d’hôte du nom DNS fournie par ce profil gestionnaire le trafic. Cette valeur est combinée avec le nom de domaine DNS utilisé par le Gestionnaire de trafic Azure pour former le nom de domaine complet (FQDN) du profil. Par exemple, la définition de la valeur de « contoso » devient « contoso.trafficmanager.net ».|
| DURÉE DE VIE | Spécifie la DNS Time-to-Live (TTL), en secondes. Durée de vie vous informe les programmes de résolution DNS Local et les clients DNS combien en cache les réponses DNS pour ce profil gestionnaire le trafic.|
| MonitorProtocol | Spécifie le protocole à utiliser pour surveiller l’intégrité du point de terminaison. Valeurs possibles sont « HTTP » et « HTTPS ».|
| MonitorPort | Spécifie le port TCP utilisé pour surveiller l’intégrité du point de terminaison.|
| MonitorPath | Spécifie le chemin d’accès par rapport au nom de domaine de point de terminaison utilisé pour tester d’intégrité de point de terminaison.|

L’applet de commande crée un profil Manager le trafic dans Azure et renvoie un objet de profil correspondant à PowerShell. À ce stade, le profil ne contienne pas les points de terminaison. Pour plus d’informations sur l’ajout de points de terminaison pour un profil le trafic Manager, voir [Ajout de points de terminaison de gestionnaire de trafic](#adding-traffic-manager-endpoints).

## <a name="get-a-traffic-manager-profile"></a>Obtenir un profil le trafic Manager

Pour récupérer un objet de profil du trafic gestionnaire existant, utilisez l’applet de commande Get-AzureRmTrafficManagerProfle :

```powershell
    $profile = Get-AzureRmTrafficManagerProfile -Name MyProfile -ResourceGroupName MyRG
```

Cette applet de commande renvoie un objet de profil du Gestionnaire de trafic.

## <a name="update-a-traffic-manager-profile"></a>Mettre à jour un profil le trafic Manager

Modification des profils de trafic Manager suit un processus étape 3 :

1. Extraire le profil à l’aide de Get-AzureRmTrafficManagerProfile ou utilisez le profil retourné par AzureRmTrafficManagerProfile de nouveau.
2. Modifier le profil. Vous pouvez ajouter et supprimer des points de terminaison ou modifier les paramètres de point de terminaison ou de profil. Ces modifications sont des opérations hors ligne. Vous ne modifiez l’objet local en mémoire qui représente le profil.
3. Valider les modifications apportées à l’aide de l’applet de commande Set-AzureRmTrafficManagerProfile.

Toutes les propriétés de profil peuvent être modifiées sauf RelativeDnsName du profil. Pour modifier la RelativeDnsName, vous devez supprimer le profil et un nouveau profil avec un nouveau nom.

L’exemple suivant montre comment modifier la durée de vie du profil :

```powershell
    $profile = Get-AzureRmTrafficManagerProfile -Name MyProfile -ResourceGroupName MyRG
    $profile.Ttl = 300
    Set-AzureRmTrafficManagerProfile -TrafficManagerProfile $profile
```

Il existe trois types de points de terminaison gestionnaire le trafic :

1. **Points de terminaison Azure** sont services hébergés dans Azure
2. **Points de terminaison externes** sont des services en dehors d’Azure
3. **Points de terminaison imbriquées** sont utilisées pour créer des hiérarchies imbriquées de profils Manager le trafic. Points de terminaison imbriquées activer configurations le routage du trafic avancées pour les applications complexes.

Dans ces trois cas, les points de terminaison peuvent être ajoutés de deux façons :

1. À l’aide d’un processus en 3 étapes décrit précédemment. L’avantage de cette méthode est que plusieurs point de terminaison peuvent être modifiés dans une seule mise à jour.
2. À l’aide de l’applet de commande New-AzureRmTrafficManagerEndpoint. Cette applet de commande ajoute un point de terminaison à un profil existant le trafic gestionnaire en une seule opération.

## <a name="adding-azure-endpoints"></a>Ajout de points de terminaison Azure

Points de terminaison Azure référencent services hébergés dans Azure. Trois types de points de terminaison Azure sont prises en charge :

1. Applications Web Azure
2. « Classiques » cloud services (qui peuvent contenir un service PaaS ou machines virtuelles IaaS)
3. Ressources PublicIpAddress Azure (qui peuvent être attachés à un programme d’équilibrage de charge ou une machine virtuelle carte réseau). La PublicIpAddress doit avoir un nom DNS affecté à utiliser dans le Gestionnaire de trafic.

Dans chaque cas :

- Le service est spécifié en utilisant le paramètre « targetResourceId » d’Ajouter AzureRmTrafficManagerEndpointConfig ou New-AzureRmTrafficManagerEndpoint.
- La « Cible » et « EndpointLocation » sont implicitement le TargetResourceId.
- Si vous spécifiez le « poids » est facultatif. Poids sont utilisés uniquement si le profil est configuré pour utiliser la méthode de routage du trafic « Weighted ». Dans le cas contraire, elles sont ignorées. Si spécifié, la valeur doit être un nombre compris entre 1 et 1000. La valeur par défaut est « 1 ».
- Si vous spécifiez la 'Priority' est facultative. Priorités sont utilisées uniquement si le profil est configuré pour utiliser la méthode de routage du trafic 'Priority'. Dans le cas contraire, elles sont ignorées. Valeurs valides sont compris entre 1 et 1000 aux valeurs inférieures indiquant une priorité plus élevée. Si spécifié pour un point de terminaison, il est obligatoire pour tous les points de terminaison. En cas d’omission, les valeurs par défaut à partir de '1' sont appliquées dans l’ordre dans lequel les points de terminaison sont répertoriées.

### <a name="example-1-adding-web-app-endpoints-using-add-azurermtrafficmanagerendpointconfig"></a>Exemple 1 : Ajout de points de terminaison Web App à l’aide d’Ajouter AzureRmTrafficManagerEndpointConfig

Dans cet exemple, nous créer un profil Manager le trafic et ajoutez deux points de terminaison de l’application Web à l’aide de l’applet de commande Add-AzureRmTrafficManagerEndpointConfig.

```powershell
    $profile = New-AzureRmTrafficManagerProfile -Name myprofile -ResourceGroupName MyRG -TrafficRoutingMethod Performance -RelativeDnsName myapp -Ttl 30 -MonitorProtocol HTTP -MonitorPort 80 -MonitorPath "/"
    $webapp1 = Get-AzureRMWebApp -Name webapp1
    Add-AzureRmTrafficManagerEndpointConfig -EndpointName webapp1ep -TrafficManagerProfile $profile -Type AzureEndpoints -TargetResourceId $webapp1.Id -EndpointStatus Enabled
    $webapp2 = Get-AzureRMWebApp -Name webapp2
    Add-AzureRmTrafficManagerEndpointConfig -EndpointName webapp2ep -TrafficManagerProfile $profile -Type AzureEndpoints -TargetResourceId $webapp2.Id -EndpointStatus Enabled
    Set-AzureRmTrafficManagerProfile -TrafficManagerProfile $profile
```

### <a name="example-2-adding-a-classic-cloud-service-endpoint-using-new-azurermtrafficmanagerendpoint"></a>Exemple 2 : Ajout d’un point de terminaison du service cloud « classiques » à l’aide de nouveau AzureRmTrafficManagerEndpoint

Dans cet exemple, un point de terminaison de Service Cloud « classique » est ajouté à un profil Manager le trafic. Dans cet exemple, nous avons spécifié le profil en utilisant les noms de groupe profil et des ressources, plutôt que de transmettre un objet de profil. Les deux approches sont prises en charge.

    $cloudService = Get-AzureRmResource -ResourceName MyCloudService -ResourceType "Microsoft.ClassicCompute/domainNames" -ResourceGroupName MyCloudService
    New-AzureRmTrafficManagerEndpoint -Name MyCloudServiceEndpoint -ProfileName MyProfile -ResourceGroupName MyRG -Type AzureEndpoints -TargetResourceId $cloudService.Id -EndpointStatus Enabled

### <a name="example-3-adding-a-publicipaddress-endpoint-using-new-azurermtrafficmanagerendpoint"></a>Exemple 3 : Ajout d’un point de terminaison publicIpAddress à l’aide de nouveau AzureRmTrafficManagerEndpoint

Dans cet exemple, une ressource d’adresse IP publique est ajoutée au profil gestionnaire le trafic. L’adresse IP doit avoir un nom DNS configuré et peut être lié à la carte réseau d’un ordinateur virtuel ou à un équilibrage de charge.

    $ip = Get-AzureRmPublicIpAddress -Name MyPublicIP -ResourceGroupName MyRG
    New-AzureRmTrafficManagerEndpoint -Name MyIpEndpoint -ProfileName MyProfile -ResourceGroupName MyRG -Type AzureEndpoints -TargetResourceId $ip.Id -EndpointStatus Enabled

## <a name="adding-external-endpoints"></a>Ajout de points de terminaison externes

Le trafic Manager utilise des points de terminaison externes pour diriger le trafic vers les services en dehors d’Azure. Comme avec points de terminaison Azure, points de terminaison externes peuvent être ajoutés à l’aide de Add-AzureRmTrafficManagerEndpointConfig suivie de jeu AzureRmTrafficManagerProfile ou New-AzureRMTrafficManagerEndpoint.

Lorsque vous spécifiez des points de terminaison externes :

- Le nom de domaine de point de terminaison doit être indiqué en utilisant le paramètre « Cible »
- Si la méthode de routage du trafic 'Performances' est utilisée, la « EndpointLocation » est requise. Dans le cas contraire, elle est facultative. La valeur doit être un [nom de la région Azure valide](https://azure.microsoft.com/regions/).
- Le « Poids » et « Priority » sont facultatifs.

### <a name="example-1-adding-external-endpoints-using-add-azurermtrafficmanagerendpointconfig-and-set-azurermtrafficmanagerprofile"></a>Exemple 1 : Ajout de points de terminaison externe à l’aide d’Ajouter AzureRmTrafficManagerEndpointConfig et jeu AzureRmTrafficManagerProfile

Dans cet exemple, nous créer un profil le trafic Manager, ajoutez deux points de terminaison externes et valider les modifications.

    $profile = New-AzureRmTrafficManagerProfile -Name myprofile -ResourceGroupName MyRG -TrafficRoutingMethod Performance -RelativeDnsName myapp -Ttl 30 -MonitorProtocol HTTP -MonitorPort 80 -MonitorPath "/"
    Add-AzureRmTrafficManagerEndpointConfig -EndpointName eu-endpoint -TrafficManagerProfile $profile -Type ExternalEndpoints -Target app-eu.contoso.com -EndpointStatus Enabled
    Add-AzureRmTrafficManagerEndpointConfig -EndpointName us-endpoint -TrafficManagerProfile $profile -Type ExternalEndpoints -Target app-us.contoso.com -EndpointStatus Enabled
    Set-AzureRmTrafficManagerProfile -TrafficManagerProfile $profile

### <a name="example-2-adding-external-endpoints-using-new-azurermtrafficmanagerendpoint"></a>Exemple 2 : Ajout de points de terminaison externe à l’aide de nouveau AzureRmTrafficManagerEndpoint

Dans cet exemple, nous ajoutons un point de terminaison externe à un profil existant. Le profil est spécifié en utilisant les noms de groupe profil et des ressources.

    New-AzureRmTrafficManagerEndpoint -Name eu-endpoint -ProfileName MyProfile -ResourceGroupName MyRG -Type ExternalEndpoints -Target app-eu.contoso.com -EndpointStatus Enabled

## <a name="adding-nested-endpoints"></a>Ajout de points de terminaison « Imbriquées »

Chaque profil gestionnaire le trafic indique une méthode unique le routage du trafic. Toutefois, il existe des scénarios qui requièrent l’acheminement du trafic plus sophistiqués que le routage fournies par un seul profil gestionnaire le trafic. Vous pouvez imbriquer des profils de trafic Manager pour combiner les avantages de plusieurs méthodes de routage du trafic. Profils imbriquées permettent de modifier le comportement du trafic gestionnaire par défaut au support plus grande et les déploiements d’applications plus complexes. Pour obtenir des exemples plus détaillées, voir [Gestionnaire de trafic imbriqués profils](traffic-manager-nested-profiles.md).

Points de terminaison imbriquées sont configurées sur le profil parent, à l’aide d’un type de point de terminaison spécifique, « NestedEndpoints ». Lorsque vous spécifiez des points de terminaison imbriquées :

- Le point de terminaison doit être spécifiée en utilisant le paramètre « targetResourceId »
- Si la méthode de routage du trafic 'Performances' est utilisée, la « EndpointLocation » est requise. Dans le cas contraire, elle est facultative. La valeur doit être un [nom de la région Azure valide](http://azure.microsoft.com/regions/).
- Le « Poids » et « Priority » sont facultatives, en ce qui concerne les points de terminaison Azure.
- Le paramètre « MinChildEndpoints » est facultatif. La valeur par défaut est « 1 ». Si le nombre de points de terminaison disponibles est inférieur à ce seuil, le profil parent considère comme le profil enfant « dégradé » et redirige le trafic aux autres points de terminaison dans le profil parent.

### <a name="example-1-adding-nested-endpoints-using-add-azurermtrafficmanagerendpointconfig-and-set-azurermtrafficmanagerprofile"></a>Exemple 1 : Ajout de points de terminaison imbriquées à l’aide d’Ajouter AzureRmTrafficManagerEndpointConfig et jeu AzureRmTrafficManagerProfile

Dans cet exemple, nous créer nouveau gestionnaire le trafic enfant et parent profils, ajoutez l’enfant comme point de terminaison imbriqué au parent et valider les modifications.

    $child = New-AzureRmTrafficManagerProfile -Name child -ResourceGroupName MyRG -TrafficRoutingMethod Priority -RelativeDnsName child -Ttl 30 -MonitorProtocol HTTP -MonitorPort 80 -MonitorPath "/"
    $parent = New-AzureRmTrafficManagerProfile -Name parent -ResourceGroupName MyRG -TrafficRoutingMethod Performance -RelativeDnsName parent -Ttl 30 -MonitorProtocol HTTP -MonitorPort 80 -MonitorPath "/"
    Add-AzureRmTrafficManagerEndpointConfig -EndpointName child-endpoint -TrafficManagerProfile $parent -Type NestedEndpoints -TargetResourceId $child.Id -EndpointStatus Enabled -EndpointLocation "North Europe" -MinChildEndpoints 2
    Set-AzureRmTrafficManagerProfile -TrafficManagerProfile $profile

Pour des raisons de concision dans cet exemple, nous n’avez pas ajouté les autres points de terminaison aux profils enfant ou parent.

### <a name="example-2-adding-nested-endpoints-using-new-azurermtrafficmanagerendpoint"></a>Exemple 2 : Ajout de points de terminaison imbriquées à l’aide de nouveau AzureRmTrafficManagerEndpoint

Dans cet exemple, nous ajouter un profil enfant existant comme point de terminaison imbriquée à un profil existant parent. Le profil est spécifié en utilisant les noms de groupe profil et des ressources.

    $child = Get-AzureRmTrafficManagerEndpoint -Name child -ResourceGroupName MyRG
    New-AzureRmTrafficManagerEndpoint -Name child-endpoint -ProfileName parent -ResourceGroupName MyRG -Type NestedEndpoints -TargetResourceId $child.Id -EndpointStatus Enabled -EndpointLocation "North Europe" -MinChildEndpoints 2

## <a name="update-a-traffic-manager-endpoint"></a>Mettre à jour un point de terminaison le trafic Manager

Il existe deux façons de mettre à jour un point de terminaison le trafic gestionnaire existant :

1. Obtenir le profil de trafic Manager à l’aide de Get-AzureRmTrafficManagerProfile, mettre à jour les propriétés de point de terminaison dans le profil et valider les modifications apportées à l’aide de jeu AzureRmTrafficManagerProfile. Cette méthode a l’avantage d’être en mesure de mettre à jour les plus d’un point de terminaison en une seule opération.
2. Obtenir le point de terminaison gestionnaire le trafic à l’aide de Get-AzureRmTrafficManagerEndpoint, mettre à jour les propriétés de point de terminaison et valider les modifications apportées à l’aide de jeu AzureRmTrafficManagerEndpoint. Cette méthode est plus simple, car il ne nécessite pas l’indexation dans le tableau de points de terminaison dans le profil.

### <a name="example-1-updating-endpoints-using-get-azurermtrafficmanagerprofile-and-set-azurermtrafficmanagerprofile"></a>Exemple 1 : Mise à jour des points de terminaison à l’aide de Get-AzureRmTrafficManagerProfile et jeu AzureRmTrafficManagerProfile

Dans cet exemple, nous modifier la priorité de deux points de terminaison au sein d’un profil existant.

    $profile = Get-AzureRmTrafficManagerProfile -Name myprofile -ResourceGroupName MyRG
    $profile.Endpoints[0].Priority = 2
    $profile.Endpoints[1].Priority = 1
    Set-AzureRmTrafficManagerProfile -TrafficManagerProfile $profile

### <a name="example-2-updating-an-endpoint-using-get-azurermtrafficmanagerendpoint-and-set-azurermtrafficmanagerendpoint"></a>Exemple 2 : Mettre à jour un point de terminaison à l’aide de Get-AzureRmTrafficManagerEndpoint et jeu AzureRmTrafficManagerEndpoint

Dans cet exemple, nous modifier l’épaisseur d’un point de terminaison unique dans un profil existant.

    $endpoint = Get-AzureRmTrafficManagerEndpoint -Name myendpoint -ProfileName myprofile -ResourceGroupName MyRG -Type ExternalEndpoints
    $endpoint.Weight = 20
    Set-AzureRmTrafficManagerEndpoint -TrafficManagerEndpoint $endpoint

## <a name="enabling-and-disabling-endpoints-and-profiles"></a>Activation et désactivation des points de terminaison et des profils

Le trafic Manager permet de points de terminaison individuels à être activé et désactivé, ainsi que ce qui permet d’activation et désactivation des profils entières.
Ces modifications peuvent être effectuées par l’obtention/mise à jour/définition les ressources de point de terminaison ou de profil. Pour optimiser ces opérations courantes, ils sont également pris en charge par le biais des applets de commande dédié.

### <a name="example-1-enabling-and-disabling-a-traffic-manager-profile"></a>Exemple 1 : Activation et désactivation d’un profil le trafic Manager

Pour activer un profil le trafic Manager, utilisez activer AzureRmTrafficManagerProfile. Le profil peut être spécifié à l’aide d’un objet de profil. L’objet de profil peut être passé par le biais du pipeline ou à l’aide de la '-TrafficManagerProfile' paramètre. Dans cet exemple, nous spécifier le profil par le nom du groupe profil et des ressources.

```powershell
    Enable-AzureRmTrafficManagerProfile -Name MyProfile -ResourceGroupName MyResourceGroup
```

Pour désactiver un profil le trafic Manager :

```powershell
    Disable-AzureRmTrafficManagerProfile -Name MyProfile -ResourceGroupName MyResourceGroup
```

L’applet de commande Disable AzureRmTrafficManagerProfile invite de confirmation. Ce message peut être supprimé en utilisant la '-Force' paramètre.

### <a name="example-2-enabling-and-disabling-a-traffic-manager-endpoint"></a>Exemple 2 : Activation et désactivation d’un point de terminaison le trafic Manager

Pour activer un point de terminaison le trafic Manager, utilisez activer AzureRmTrafficManagerEndpoint. Il existe deux manières de spécifier le point de terminaison

1. À l’aide d’un objet TrafficManagerEndpoint passé via le pipeline ou à l’aide de la '-TrafficManagerEndpoint' paramètre
2. Utilisant le nom de point de terminaison, le type de point de terminaison, le nom du profil et le nom de groupe de ressources :

```powershell
    Enable-AzureRmTrafficManagerEndpoint -Name MyEndpoint -Type AzureEndpoints -ProfileName MyProfile -ResourceGroupName MyRG
```

De même, pour désactiver un point de terminaison le trafic gestionnaire :

```powershell
     Disable-AzureRmTrafficManagerEndpoint -Name MyEndpoint -Type AzureEndpoints -ProfileName MyProfile -ResourceGroupName MyRG -Force
```

À l’instar de désactiver-AzureRmTrafficManagerProfile, l’applet de commande Disable AzureRmTrafficManagerEndpoint vous invite de confirmation. Ce message peut être supprimé en utilisant la '-Force' paramètre.

## <a name="delete-a-traffic-manager-endpoint"></a>Supprimer un point de terminaison le trafic Manager

Pour supprimer des points de terminaison individuels, utilisez l’applet de commande Supprimer AzureRmTrafficManagerEndpoint :

```powershell
    Remove-AzureRmTrafficManagerEndpoint -Name MyEndpoint -Type AzureEndpoints -ProfileName MyProfile -ResourceGroupName MyRG
```

Cette applet de commande invite de confirmation. Ce message peut être supprimé en utilisant la '-Force' paramètre.

## <a name="delete-a-traffic-manager-profile"></a>Supprimer un profil le trafic Manager

Pour supprimer un profil le trafic Manager, utilisez l’applet de commande Supprimer AzureRmTrafficManagerProfile, spécifier les noms de groupe profil et des ressources :

```powershell
    Remove-AzureRmTrafficManagerProfile -Name MyProfile -ResourceGroupName MyRG [-Force]
```

Cette applet de commande invite de confirmation. Ce message peut être supprimé en utilisant la '-Force' paramètre.

Le profil à supprimer peut également être spécifié à l’aide d’un objet de profil :

```powershell
    $profile = Get-AzureRmTrafficManagerProfile -Name MyProfile -ResourceGroupName MyRG
    Remove-AzureRmTrafficManagerProfile -TrafficManagerProfile $profile [-Force]
```

Cette séquence peut également être transmise :

```powershell
    Get-AzureRmTrafficManagerProfile -Name MyProfile -ResourceGroupName MyRG | Remove-AzureRmTrafficManagerProfile [-Force]
```

## <a name="next-steps"></a>Étapes suivantes

[Analyse du trafic Manager](traffic-manager-monitoring.md)

[Remarques sur les performances du trafic Manager](traffic-manager-performance-considerations.md)

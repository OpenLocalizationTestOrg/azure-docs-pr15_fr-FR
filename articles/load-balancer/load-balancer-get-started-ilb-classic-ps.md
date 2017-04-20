<properties
   pageTitle="Créer un équilibrage de charge interne à l’aide de PowerShell dans le modèle de déploiement classique | Microsoft Azure"
   description="Apprenez à créer un équilibrage de charge interne à l’aide de PowerShell dans le modèle de déploiement classique"
   services="load-balancer"
   documentationCenter="na"
   authors="sdwheeler"
   manager="carmonm"
   editor=""
   tags="azure-service-management"
/>
<tags
   ms.service="load-balancer"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="02/09/2016"
   ms.author="sewhee" />

# <a name="get-started-creating-an-internal-load-balancer-classic-using-powershell"></a>Créer un équilibrage de charge interne (standard) à l’aide de PowerShell

[AZURE.INCLUDE [load-balancer-get-started-ilb-classic-selectors-include.md](../../includes/load-balancer-get-started-ilb-classic-selectors-include.md)]

[AZURE.INCLUDE [load-balancer-get-started-ilb-intro-include.md](../../includes/load-balancer-get-started-ilb-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-classic-include.md)]Découvrez comment [effectuer ces étapes en utilisant le modèle de gestionnaire de ressources](load-balancer-get-started-ilb-arm-ps.md).

[AZURE.INCLUDE [load-balancer-get-started-ilb-scenario-include.md](../../includes/load-balancer-get-started-ilb-scenario-include.md)]


[AZURE.INCLUDE [azure-ps-prerequisites-include.md](../../includes/azure-ps-prerequisites-include.md)]


## <a name="create-an-internal-load-balancer-set-for-virtual-machines"></a>Créer un équilibrage de charge interne défini pour machines virtuelles

Pour créer un jeu d’équilibrage de charge interne et les serveurs qui vous envoie le trafic vers celui-ci, vous devez effectuer les opérations suivantes :

1. Créer une instance d’interne l’équilibrage de charge qui sera le point de terminaison de trafic entrant pour être équilibrée sur les serveurs d’un ensemble d’équilibrage de charge.

1. Ajouter des points de terminaison correspondant aux machines virtuelles qui recevront le trafic entrant.

1. Configurer les serveurs qui enverront le trafic vers charge être équilibrée pour envoyer leur le trafic vers l’adresse IP virtuelle (VIP) de l’instance d’équilibrage de charge interne.


### <a name="step-1-create-an-internal-load-balancing-instance"></a>Étape 1 : Créer une instance de l’équilibrage de charge interne

Pour un service cloud existant ou un service cloud déployé sous un réseau virtuel régionaux, vous pouvez créer une instance de l’équilibrage de charge interne avec les commandes de Windows PowerShell suivantes :

    $svc="<Cloud Service Name>"
    $ilb="<Name of your ILB instance>"
    $subnet="<Name of the subnet within your virtual network>"
    $IP="<The IPv4 address to use on the subnet-optional>"

    Add-AzureInternalLoadBalancer -ServiceName $svc -InternalLoadBalancerName $ilb –SubnetName $subnet –StaticVNetIPAddress $IP


Notez que cette utilisation de l’applet de commande [Add-AzureEndpoint](https://msdn.microsoft.com/library/dn495300.aspx) Windows PowerShell utilise le jeu de paramètres DefaultProbe. Pour plus d’informations sur les jeux de paramètres supplémentaires, voir [Ajouter AzureEndpoint](https://msdn.microsoft.com/library/dn495300.aspx).

### <a name="step-2-add-endpoints-to-the-internal-load-balancing-instance"></a>Étape 2 : Ajouter des points de terminaison à l’instance de l’équilibrage de charge interne

Voici un exemple :

    $svc="mytestcloud"
    $vmname="DB1"
    $epname="TCP-1433-1433"
    $lbsetname="lbset"
    $prot="tcp"
    $locport=1433
    $pubport=1433
    $ilb="ilbset"
    Get-AzureVM –ServiceName $svc –Name $vmname | Add-AzureEndpoint -Name $epname -Lbset $lbsetname -Protocol $prot -LocalPort $locport -PublicPort $pubport –DefaultProbe -InternalLoadBalancerName $ilb | Update-AzureVM


### <a name="step-3-configure-your-servers-to-send-their-traffic-to-the-new-internal-load-balancing-endpoint"></a>Étape 3 : Configurer vos serveurs pour envoyer leur le trafic vers le nouveau point de terminaison l’équilibrage de charge interne

Vous devez configurer les serveurs dont le trafic va agir en charge équilibrée pour utiliser la nouvelle adresse IP (VIP publique) de l’instance d’équilibrage de charge interne. Il s’agit de l’adresse sur lequel écoute l’instance de l’équilibrage de charge interne. Dans la plupart des cas, vous devez simplement ajouter ou modifier un enregistrement DNS pour l’adresse VIP de l’instance de l’équilibrage de charge interne.

Si vous avez spécifié l’adresse IP lors de la création de l’instance de l’équilibrage de charge internes, vous avez déjà la VIP. Dans le cas contraire, vous pouvez voir l’adresse VIP parmi les commandes suivantes :

    $svc="<Cloud Service Name>"
    Get-AzureService -ServiceName $svc | Get-AzureInternalLoadBalancer



Pour utiliser ces commandes, renseignez les valeurs et supprimer le < et >. Voici un exemple :

    $svc="mytestcloud"
    Get-AzureService -ServiceName $svc | Get-AzureInternalLoadBalancer


À partir de l’affichage de la commande Get-AzureInternalLoadBalancer, notez l’adresse IP et apportez les modifications nécessaires à vos serveurs ou vos enregistrements DNS pour vous assurer que le trafic soit envoyé à l’adresse VIP.

>[AZURE.NOTE]La plate-forme Microsoft Azure utilise une adresse IPv4 statique publiquement prenant pour divers scénarios d’administration. L’adresse IP est 168.63.129.16. Cette adresse IP ne doit pas être bloquée par un pare-feu, car il peut provoquer un comportement inattendu.
>En ce qui concerne Azure interne l’équilibrage de charge, cette adresse IP est utilisée par la surveillance sondes à partir de l’équilibrage de charge pour déterminer l’état d’intégrité des machines virtuelles dans un jeu d’équilibrage de charge. Si un groupe de sécurité réseau est utilisé pour restreindre le trafic vers Azure machines virtuelles dans un jeu d’équilibrage de charge en interne ou est appliqué à un sous-réseau virtuel, assurez-vous qu’une règle de sécurité réseau est ajoutée à autoriser le trafic de 168.63.129.16.


## <a name="example-of-internal-load-balancing"></a>Exemple d’équilibrage de charge interne

Pour vous lancer le processus de fin à fin de la création d’un jeu d’équilibrage de charge pour les deux exemples de configuration, consultez les sections suivantes.

### <a name="an-internet-facing-multi-tier-application"></a>Internet en application à plusieurs niveaux

Vous voulez fournir un service de base de données d’équilibrage de charge pour un ensemble de serveurs web Internet. Les deux ensembles de serveurs hébergés dans un service cloud Azure unique. Le trafic de serveur Web vers les ports TCP 1433 doit être réparti entre deux machines virtuelles dans le niveau de base de données. Figure 1 présente la configuration.

![Ensemble d’équilibrage de charge interne pour le niveau de base de données](./media/load-balancer-internal-getstarted/IC736321.png)


La configuration se compose des éléments suivants :

- Le service cloud existant qui héberge les machines virtuelles est nommé mytestcloud.

- Les deux serveurs de base de données existante sont nommés dégressif 1, DB2.

- Les serveurs Web de la couche web se connectent aux serveurs de base de données dans le niveau de base de données à l’aide de l’adresse IP privée. Une autre option consiste à utiliser votre propre système DNS pour le réseau virtuel et enregistrer manuellement un enregistrement A pour l’ensemble d’équilibrage de charge interne.

Les commandes suivantes configurer une nouvelle instance de l’équilibrage de charge interne nommée **ILBset** et ajouter des points de terminaison aux machines virtuelles correspondant aux serveurs de base de deux données :

    $svc="mytestcloud"
    $ilb="ilbset"
    Add-AzureInternalLoadBalancer -ServiceName $svc -InternalLoadBalancerName $ilb
    $prot="tcp"
    $locport=1433
    $pubport=1433
    $epname="TCP-1433-1433"
    $lbsetname="lbset"
    $vmname="DB1"
    Get-AzureVM –ServiceName $svc –Name $vmname | Add-AzureEndpoint -Name $epname -LbSetName $lbsetname -Protocol $prot -LocalPort $locport -PublicPort $pubport –DefaultProbe -InternalLoadBalancerName $ilb | Update-AzureVM

    $epname="TCP-1433-1433-2"
    $vmname="DB2"
    Get-AzureVM –ServiceName $svc –Name $vmname | Add-AzureEndpoint -Name $epname -LbSetName $lbsetname -Protocol $prot -LocalPort $locport -PublicPort $pubport –DefaultProbe -InternalLoadBalancerName $ilb | Update-AzureVM


## <a name="remove-an-internal-load-balancing-configuration"></a>Supprimer une configuration d’équilibrage de charge interne

Pour supprimer une machine virtuelle comme un point de terminaison à partir d’une instance d’équilibrage de charge interne, utilisez les commandes suivantes :

    $svc="<Cloud service name>"
    $vmname="<Name of the VM>"
    $epname="<Name of the endpoint>"
    Get-AzureVM -ServiceName $svc -Name $vmname | Remove-AzureEndpoint -Name $epname | Update-AzureVM

Pour utiliser ces commandes, renseignez les valeurs, en supprimant la < et >.

Voici un exemple :

    $svc="mytestcloud"
    $vmname="DB1"
    $epname="TCP-1433-1433"
    Get-AzureVM -ServiceName $svc -Name $vmname | Remove-AzureEndpoint -Name $epname | Update-AzureVM

Pour supprimer une instance d’équilibrage de charge interne d’un service cloud, utilisez les commandes suivantes :

    $svc="<Cloud service name>"
    Remove-AzureInternalLoadBalancer -ServiceName $svc

Pour utiliser ces commandes, remplissez la valeur et supprimer le < et >.

Voici un exemple :

    $svc="mytestcloud"
    Remove-AzureInternalLoadBalancer -ServiceName $svc



## <a name="additional-information-about-internal-load-balancer-cmdlets"></a>Informations supplémentaires sur les applets de commande équilibrage de charge interne


Pour obtenir des informations supplémentaires sur les applets de commande de l’équilibrage de charge interne, exécutez les commandes suivantes à l’invite de Windows PowerShell :

- Get-help nouvelle AzureInternalLoadBalancerConfig-complète

- Get-help AzureInternalLoadBalancer ajouter-complète

- Get-help Get-AzureInternalLoadbalancer-complète

- Supprimer AzureInternalLoadBalancer-help Get-complète

## <a name="next-steps"></a>Étapes suivantes

[Configurer un mode de distribution d’équilibrage de charge à l’aide de l’affinité IP source](load-balancer-distribution-mode.md)

[Configurer les paramètres de délai d’expiration TCP inactives pour votre équilibrage de charge](load-balancer-tcp-idle-timeout.md)
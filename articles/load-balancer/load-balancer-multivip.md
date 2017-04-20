<properties
   pageTitle="Plusieurs VIP d’un service cloud"
   description="Vue d’ensemble des multiVIP et comment définir plusieurs VIP sur un service cloud"
   services="load-balancer"
   documentationCenter="na"
   authors="sdwheeler"
   manager="carmonm"
   editor="tysonn" />
<tags
   ms.service="load-balancer"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/24/2016"
   ms.author="sewhee" />

# <a name="configure-multiple-vips-for-a-cloud-service"></a>Configurer plusieurs VIP d’un service cloud

Accéder aux services cloud Azure via Internet à l’aide d’une adresse IP fournie par Azure. Cette adresse IP publique est appelée un VIP (virtual IP) dans la mesure où elle est liée à l’équilibrage de charge Azure et les instances pas la Machine virtuelle () au sein du service cloud. Vous pouvez accéder à toute instance machine virtuelle au sein d’un service cloud à l’aide d’une adresse VIP unique.

Toutefois, il existe des scénarios dans lesquels vous devrez peut-être plusieurs VIP comme entrée de pointez sur le même service cloud. Par exemple, votre service cloud peut héberger plusieurs sites Web qui nécessitent une connectivité SSL en utilisant le port par défaut 443, comme chaque site est hébergé pour un autre client ou du client. Dans ce scénario, vous devez avoir une adresse IP qui fait face à public différente pour chaque site Web. L’illustration ci-dessous montre un site web multiple client classique hébergement avec un besoin de plusieurs certificats SSL sur le même port public.

![Scénario multi VIP SSL](./media/load-balancer-multivip/Figure1.png)

Dans l’exemple ci-dessus, toutes les VIP utilisent le même port public (443) et le trafic est redirigé vers une ou équilibrage de charge plusieurs machines virtuelles sur un port privé unique pour l’adresse IP interne du service cloud qui héberge les sites Web.

>[AZURE.NOTE] Une autre situation exigeant l’utilisation de la plusieurs VIP héberge plusieurs récepteurs groupe de disponibilité AlwaysOn de SQL sur le même ensemble de Machines virtuelles.

VIP est dynamiques par défaut, ce qui signifie que l’adresse IP réelle attribué au service cloud peut-être changer au fil du temps. Pour éviter cela, vous pouvez réserver un VIP pour votre service. Pour en savoir plus sur VIP réservés, voir [Réservé IP Public](../virtual-network/virtual-networks-reserved-public-ip.md).

>[AZURE.NOTE] Voir [adresse IP tarification](https://azure.microsoft.com/pricing/details/ip-addresses/) pour plus d’informations sur les tarifs sur VIP et adresses IP réservé.

Vous pouvez utiliser PowerShell pour vérifier la VIP utilisés par vos services cloud, ainsi qu’ajouter et supprimer VIP, associer une adresse VIP à un point de terminaison et configurer équilibrage de charge sur une VIP spécifique.

## <a name="limitations"></a>Limitations

Pour l’instant, VIP à plusieurs fonctionnalités est limitée aux scénarios suivants :

- **IaaS uniquement**. Vous pouvez activer uniquement à plusieurs VIP pour les services en nuage qui contiennent des machines virtuelles. Vous ne pouvez pas utiliser VIP à plusieurs dans les scénarios PaaS avec instances de rôle.
- **PowerShell uniquement**. Vous pouvez uniquement gérer VIP à plusieurs à l’aide de PowerShell.

Ces limitations sont temporaires et peuvent modifier à tout moment. Vérifiez que revenir sur cette page pour vérifier les modifications ultérieures.


## <a name="how-to-add-a-vip-to-a-cloud-service"></a>Comment ajouter un VIP à un service cloud

Pour ajouter une adresse VIP à votre service, exécutez la commande PowerShell suivante :

    Add-AzureVirtualIP -VirtualIPName Vip3 -ServiceName myService

Cette commande affiche un résultat semblable à l’exemple suivant :

    OperationDescription OperationId                          OperationStatus
    -------------------- -----------                          ---------------
    Add-AzureVirtualIP   4bd7b638-d2e7-216f-ba38-5221233d70ce Succeeded

## <a name="how-to-remove-a-vip-from-a-cloud-service"></a>Comment supprimer un VIP d’un service cloud

Pour supprimer l’adresse VIP ajouté à votre service dans l’exemple ci-dessus, exécutez la commande PowerShell suivante :

    Remove-AzureVirtualIP -VirtualIPName Vip3 -ServiceName myService

>[AZURE.IMPORTANT] Vous pouvez uniquement supprimer un VIP si elle n’a associé à aucun point de terminaison.

## <a name="how-to-retrieve-vip-information-from-a-cloud-service"></a>Comment récupérer des informations de VIP à partir d’un Service Cloud

Pour récupérer la VIP associé à un service cloud, exécutez le script PowerShell suivant :

    $deployment = Get-AzureDeployment -ServiceName myService
    $deployment.VirtualIPs

Le script affiche un résultat semblable à l’exemple suivant :

    Address         : 191.238.74.148
    IsDnsProgrammed : True
    Name            : Vip1
    ReservedIPName  :
    ExtensionData   :

    Address         :
    IsDnsProgrammed :
    Name            : Vip2
    ReservedIPName  :
    ExtensionData   :

    Address         :
    IsDnsProgrammed :
    Name            : Vip3
    ReservedIPName  :
    ExtensionData   :

Dans cet exemple, le service en nuage a 3 VIP :

- **Vip1** est l’adresse IP virtuelle par défaut, vous savez que parce que la valeur de IsDnsProgrammedName est définie sur true.
- **Vip2** et **Vip3** ne sont pas utilisés car ils n’ont pas toutes les adresses IP. Ils seront utilisées uniquement si vous associez un point de terminaison avec l’adresse VIP.

>[AZURE.NOTE] Votre abonnement sera uniquement être facturé pour VIP supplémentaire dès qu’elles sont associées à un point de terminaison. Pour plus d’informations sur les prix, voir [l’adresse IP tarifs](https://azure.microsoft.com/pricing/details/ip-addresses/).

## <a name="how-to-associate-a-vip-to-an-endpoint"></a>Comment faire pour associer une adresse VIP à un point de terminaison

Pour associer une adresse VIP sur un service cloud pour un point de terminaison, exécutez la commande PowerShell suivante :

    Get-AzureVM -ServiceName myService -Name myVM1 `
  	| Add-AzureEndpoint -Name myEndpoint -Protocol tcp -LocalPort 8080 -PublicPort 80 -VirtualIPName Vip2 `
  	| Update-AzureVM

La commande crée un point de terminaison lié à l’adresse IP virtuelle appelée *Vip2* sur les ports *80*et des liens pour la machine virtuelle nommée *myVM1* dans un service cloud nommé *myService* à l’aide de *TCP* sur le port *8080*.

Pour vérifier la configuration, exécutez la commande PowerShell suivante :

    $deployment = Get-AzureDeployment -ServiceName myService
    $deployment.VirtualIPs

Le résultat est semblable à l’exemple suivant :

    Address         : 191.238.74.148
    IsDnsProgrammed : True
    Name            : Vip1
    ReservedIPName  :
    ExtensionData   :

    Address         : 191.238.74.13
    IsDnsProgrammed :
    Name            : Vip2
    ReservedIPName  :
    ExtensionData   :

    Address         :
    IsDnsProgrammed :
    Name            : Vip3
    ReservedIPName  :
    ExtensionData   :

## <a name="how-to-enable-load-balancing-on-a-specific-vip"></a>L’activation d’équilibrage de charge sur une VIP spécifique

Vous pouvez associer un VIP unique avec plusieurs machines virtuelles pour à des fins d’équilibrage de charge. Par exemple, vous avez un service cloud nommée *myService*et deux machines virtuelles nommés *myVM1* et *myVM2*. Et votre service cloud comporte plusieurs VIP, un d’eux nommé *Vip2*. Si vous souhaitez vous assurer que tout le trafic vers le port *81* sur *Vip2* est équilibré entre *myVM1* et *myVM2* sur port *8181*, exécutez le script PowerShell suivant :

    Get-AzureVM -ServiceName myService -Name myVM1 `
  	| Add-AzureEndpoint -Name myEndpoint -LoadBalancedEndpointSetName myLBSet `
        -Protocol tcp -LocalPort 8181 -PublicPort 81 -VirtualIPName Vip2  -DefaultProbe `
  	| Update-AzureVM

    Get-AzureVM -ServiceName myService -Name myVM2 `
  	| Add-AzureEndpoint -Name myEndpoint -LoadBalancedEndpointSetName myLBSet `
        -Protocol tcp -LocalPort 8181 -PublicPort 81 -VirtualIPName Vip2  -DefaultProbe `
  	| Update-AzureVM

Vous pouvez également mettre à jour votre équilibrage de charge pour utiliser un autre VIP. Par exemple, si vous exécutez la commande PowerShell suivante, vous allez modifier la jeu à utiliser un VIP nommé Vip1 d’équilibrage de charge :

    Set-AzureLoadBalancedEndpoint -ServiceName myService -LBSetName myLBSet -VirtualIPName Vip1

## <a name="next-steps"></a>Étapes suivantes

[Journal analytique pour l’équilibrage de charge Azure](load-balancer-monitor-log.md)

[Vue d’ensemble du équilibrage de charge qui fait face à Internet](load-balancer-internet-overview.md)

[Prise en main sur Internet en équilibrage de charge](load-balancer-get-started-internet-arm-ps.md)

[Vue d’ensemble du réseau virtuel](../virtual-network/virtual-networks-overview.md)

[API REST de IP réservée](https://msdn.microsoft.com/library/azure/dn722420.aspx)
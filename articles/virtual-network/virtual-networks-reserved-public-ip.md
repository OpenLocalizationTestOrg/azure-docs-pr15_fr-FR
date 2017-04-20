<properties
   pageTitle="Réservé IP | Microsoft Azure"
   description="Comprendre les adresses IP réservé et comment les gérer"
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
   ms.date="02/10/2016"
   ms.author="jdial" />

# <a name="reserved-ip-overview"></a>Vue d’ensemble IP réservée
Adresses IP dans Azure appartiennent à deux catégories : dynamique et réservé. Adresses IP publiques gérés par Azure sont dynamiques par défaut. Que signifie que l’adresse IP utilisé pour un service cloud donné (VIP) ou pour accéder à une machine virtuelle ou rôle instance directement (ILPIP) peut modifier de temps à autre, lorsque les ressources sont arrêt ou libéré.

Pour empêcher la modification des adresses IP, vous pouvez réserver une adresse IP. Adresses IP réservée peut servir uniquement un VIP, garantissant que l’adresse IP pour le service en nuage sera la même même lorsque des ressources sont arrêtés ou libéré. Par ailleurs, vous pouvez convertir existant d’adresses IP dynamique utilisée comme un VIP à une adresse IP réservée.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Découvrez comment réserver une adresse IP publique statique à l’aide du [modèle de déploiement Gestionnaire de ressources](virtual-network-ip-addresses-overview-arm.md).

Assurez-vous que vous savez comment fonctionnent les [adresses IP](virtual-network-ip-addresses-overview-classic.md) dans Azure.

## <a name="when-do-i-need-a-reserved-ip"></a>Lorsque vous avez besoin une adresse IP réservée ?
- **Vous voulez vous assurer que l’adresse IP est réservé dans votre abonnement**. Si vous voulez réserver une adresse IP qui n’est pas libérée de votre abonnement en toutes circonstances, vous devez utiliser une adresse IP publique réservée.  
- **Vous souhaitez que votre adresse IP pour rester avec votre service cloud même à travers état arrêté ou désalloué (machines virtuelles)**. Si vous souhaitez que votre service accessible à l’aide d’une adresse IP qui ne change pas même lorsque machines virtuelles dans le service cloud sont arrêter ou désalloué.
- **Vous voulez vous assurer que le trafic sortant à partir d’Azure utilise une adresse IP prévisible**. Vous devrez votre pare-feu local configuré pour autoriser uniquement le trafic provenant des adresses IP spécifiques. Par réservation une adresse IP, vous saurez l’adresse IP source et pas les mettre à jour vos règles de pare-feu en raison d’une modification IP.

## <a name="faq"></a>FAQ
1. Puis-je utiliser une adresse IP réservée pour tous les services Azure ?  
  - Adresses IP réservé peut uniquement être utilisée pour machines virtuelles et les rôles d’instance de service cloud exposés via un VIP.
1. Adresses IP réservés combien puis-je avoir ?  
  - Pour l’instant, tous les abonnements Azure sont autorisés à utiliser les adresses IP réservés 20. Toutefois, vous pouvez demander des adresses IP réservé supplémentaire. Consultez la page [d’abonnement et les limites de Service](../azure-subscription-service-limits.md) pour plus d’informations.
1. Existe-t-il une charge pour les adresses IP réservée ?
  - Voir les [Détails de tarification pour l’adresse IP réservée](http://go.microsoft.com/fwlink/?LinkID=398482) aux informations de tarification.
1. Comment réserver une adresse IP ?
  - Vous pouvez utiliser PowerShell ou l' [API REST de gestion Azure](https://msdn.microsoft.com/library/azure/dn722420.aspx) pour réserver une adresse IP dans une région particulière. Cette adresse IP réservée est associée à votre abonnement. Vous ne pouvez pas réserver une adresse IP à l’aide du portail de gestion.
1. Puis-je l’utiliser ce avec affinité du groupe en fonction de VNets ?
  - Adresses IP réservé sont uniquement pris en charge dans VNets régionaux. Il n’est pas pris en charge pour VNets qui sont associés à affinités. Pour plus d’informations sur l’association d’un VNet avec une région ou un groupe affinité, voir [à propos de régionaux VNets et affinités](virtual-networks-migrate-to-regional-vnet.md).

## <a name="how-to-manage-reserved-vips"></a>Comment gérer les VIP réservés

Avant de pouvoir utiliser réservé adresses IP, vous devez l’ajouter à votre abonnement. Pour créer une adresse IP réservée à partir du pool d’adresses d’IP publiques dans l’emplacement *Central États-Unis* , exécutez la commande PowerShell suivante :

    New-AzureReservedIP –ReservedIPName MyReservedIP –Location "Central US"

Vous constatez que vous ne pouvez pas spécifier IP sont réservés. Pour afficher les adresses IP sont réservés dans votre abonnement, exécutez la commande PowerShell suivante et notez les valeurs pour *ReservedIPName* et *l’adresse*:

    Get-AzureReservedIP

    ReservedIPName       : MyReservedIP
    Address              : 23.101.114.211
    Id                   : d73be9dd-db12-4b5e-98c8-bc62e7c42041
    Label                :
    Location             : Central US
    State                : Created
    InUse                : False
    ServiceName          :
    DeploymentName       :
    OperationDescription : Get-AzureReservedIP
    OperationId          : 55e4f245-82e4-9c66-9bd8-273e815ce30a
    OperationStatus      : Succeeded

Une fois qu’une adresse IP est réservée, elle reste associé à votre abonnement jusqu'à ce que vous supprimiez. Pour supprimer les adresses IP réservées ci-dessus, exécutez la commande PowerShell suivante :

    Remove-AzureReservedIP -ReservedIPName "MyReservedIP"

## <a name="how-to-reserve-the-ip-address-of-an-existing-cloud-service"></a>Comment faire pour réserver l’adresse IP d’un service cloud existant

Vous pouvez réserver l’adresse IP d’un service cloud existant en ajoutant le paramètre *- ServiceName* . Pour réserver l’adresse IP d’un service cloud *TestService* à l’emplacement *Central États-Unis* , exécutez la commande PowerShell suivante :

    New-AzureReservedIP –ReservedIPName MyReservedIP –Location "Central US" -ServiceName TestService


## <a name="how-to-associate-a-reserved-ip-to-a-new-cloud-service"></a>Comment faire pour associer une adresse IP réservée à un service en nuage
Le script ci-dessous crée une nouvelle adresse IP réservée, puis associe à un service en nuage nommé *TestService*.

    New-AzureReservedIP –ReservedIPName MyReservedIP –Location "Central US"
    $image = Get-AzureVMImage|?{$_.ImageName -like "*RightImage-Windows-2012R2-x64*"}
    New-AzureVMConfig -Name TestVM -InstanceSize Small -ImageName $image.ImageName `
  	| Add-AzureProvisioningConfig -Windows -AdminUsername adminuser -Password MyP@ssw0rd!! `
  	| New-AzureVM -ServiceName TestService -ReservedIPName MyReservedIP -Location "Central US"

>[AZURE.NOTE] Lorsque vous créez une adresse IP réservée à utiliser avec un service cloud, vous devez toujours faire référence à la machine virtuelle en utilisant *VIP :&lt;numéro de port >* pour la communication entrante. Réserver une adresse IP n’est pas signifie que vous pouvez vous connecter directement à la machine virtuelle. Les adresses IP réservées est affectée au service cloud qui la machine virtuelle a été déployée sur. Si vous voulez vous connecter à une machine virtuelle par IP directement, vous devez configurer une adresse IP publique au niveau de l’instance. Une adresse IP publique au niveau de l’instance est un type d’adresse IP publique (appelé un ILPIP) qui est affectée directement à votre ordinateur virtuel. Il ne peut pas être réservé. Pour plus d’informations, voir [L’occurrence de Public IP (ILPIP)](virtual-networks-instance-level-public-ip.md) .

## <a name="how-to-remove-a-reserved-ip-from-a-running-deployment"></a>Comment supprimer une adresse IP réservée d’un déploiement en cours d’exécution
Pour supprimer les adresses IP réservées ajouté au nouveau service créé dans le script ci-dessus, exécutez la commande PowerShell suivante :

    Remove-AzureReservedIPAssociation -ReservedIPName MyReservedIP -ServiceName TestService

>[AZURE.NOTE] Suppression d’une adresse IP réservée à partir d’un déploiement en cours d’exécution ne supprime pas la réservation de votre abonnement. Il libère simplement l’adresse IP devant être utilisé par une autre ressource dans votre abonnement.

## <a name="how-to-associate-a-reserved-ip-to-a-running-deployment"></a>Comment faire pour associer une adresse IP réservée à un déploiement en cours d’exécution
Le script ci-dessous crée un service cloud nommé *TestService2* avec un nouvel ordinateur virtuel nommé *TestVM2*et puis associe les adresses IP existant réservées nommé *MyReservedIP* au service cloud.

    $image = Get-AzureVMImage|?{$_.ImageName -like "*RightImage-Windows-2012R2-x64*"}
    New-AzureVMConfig -Name TestVM2 -InstanceSize Small -ImageName $image.ImageName `
  	| Add-AzureProvisioningConfig -Windows -AdminUsername adminuser -Password MyP@ssw0rd!! `
  	| New-AzureVM -ServiceName TestService2 -Location "Central US"
    Set-AzureReservedIPAssociation -ReservedIPName MyReservedIP -ServiceName TestService2

## <a name="how-to-associate-a-reserved-ip-to-a-cloud-service-by-using-a-service-configuration-file"></a>Comment faire pour associer une adresse IP réservée à un service cloud à l’aide d’un fichier de configuration de service
Vous pouvez également associer une adresse IP réservée à un service cloud à l’aide d’un fichier de configuration (CSCFG) service. L’exemple xml ci-dessous montre comment configurer un service cloud pour utiliser un VIP réservé nommé *MyReservedIP*:

    <?xml version="1.0" encoding="utf-8"?>
    <ServiceConfiguration serviceName="ReservedIPSample" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceConfiguration" osFamily="4" osVersion="*" schemaVersion="2014-01.2.3">
      <Role name="WebRole1">
        <Instances count="1" />
        <ConfigurationSettings>
          <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" value="UseDevelopmentStorage=true" />
        </ConfigurationSettings>
      </Role>
      <NetworkConfiguration>
        <AddressAssignments>
          <ReservedIPs>
           <ReservedIP name="MyReservedIP"/>
          </ReservedIPs>
        </AddressAssignments>
      </NetworkConfiguration>
    </ServiceConfiguration>

## <a name="next-steps"></a>Étapes suivantes

- Comprendre le fonctionnement de [l’adressage IP](virtual-network-ip-addresses-overview-classic.md) dans le modèle de déploiement classique.

- En savoir plus sur [les adresses IP privées réservées](virtual-networks-reserved-private-ip.md).

- Obtenir des informations sur les [adresses de l’Instance niveau Public IP (ILPIP)](virtual-networks-instance-level-public-ip.md).

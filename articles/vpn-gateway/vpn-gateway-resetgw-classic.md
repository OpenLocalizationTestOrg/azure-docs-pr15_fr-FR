<properties
   pageTitle="Réinitialiser une passerelle VPN Azure | Microsoft Azure"
   description="Cet article vous guide tout au long de la réinitialisation de votre passerelle VPN Azure. L’article s’applique aux passerelles VPN dans standard et les modèles de déploiement Gestionnaire de ressources."
   services="vpn-gateway"
   documentationCenter="na"
   authors="cherylmc"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager,azure-service-management"/>

<tags
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="09/23/2016"
   ms.author="cherylmc"/>

# <a name="reset-an-azure-vpn-gateway-using-powershell"></a>Réinitialiser une passerelle VPN Azure à l’aide de PowerShell


Cet article vous guide tout au long de la réinitialisation de votre passerelle VPN Azure à l’aide des applets de commande PowerShell. Ces instructions incluent le modèle de déploiement classique et le modèle de déploiement du Gestionnaire de ressources.

La réinitialisation de la passerelle VPN Azure est utile si vous perdez la connectivité VPN entre local sur un ou plusieurs tunnel VPN S2S. Dans ce cas, vos appareils VPN locaux sont tout fonctionne correctement, mais ne sont pas en mesure d’établir tunnel IPsec avec les passerelles VPN Azure. 

Chaque passerelle VPN Azure se compose de deux instances de machine virtuelle en cours d’exécution dans une configuration active-de réserve. Lorsque vous utilisez l’applet de commande PowerShell pour réinitialiser la passerelle, il redémarre la passerelle et puis appliquer de nouveau les configurations croisée local vers celui-ci. La passerelle conserve l’adresse IP qu’il possède déjà. Cela signifie que vous n’aurez pas besoin de mettre à jour la configuration du routeur VPN avec une nouvelle adresse IP publique pour passerelle VPN Azure.  

Une fois que la commande est émise, l’instance active actuelle de la passerelle VPN Azure est redémarré immédiatement. Un écart brève sera pendant le basculement de l’instance active (redémarré), à l’instance en attente. L’intervalle doit être inférieure à une minute.

Si la connexion n’est pas restaurée après le premier redémarrage, exécutez la commande même à nouveau pour redémarrer la deuxième instance de machine virtuelle (la nouvelle passerelle active). Si le redémarrage de deux est demandé après l’autre, il sera légèrement longtemps où les deux instances de machine virtuelle (actifs / veille) sont en cours de redémarrage. Cela interrompt plus sur la connectivité VPN, jusqu'à 2 à 4 minutes pour les machines virtuelles terminer le redémarrage.

Après le deux redémarrage, si vous rencontrez toujours des problèmes de connectivité entre local, ouvrez une demande de support à partir du portail Azure.

## <a name="before-you-begin"></a>Avant de commencer

Avant de réinitialiser votre passerelle, vérifiez les éléments clés ci-dessous pour chaque tunnel VPN IPsec de Site à Site (S2S). Toute incompatibilité dans les éléments entraîne la déconnexion de tunnel VPN S2S. Vérifier et corriger les configurations votre en local et passerelles VPN Azure vous évite de redémarrage inutiles au niveau des interruptions pour les autres connexions de travailler sur les passerelles.

Avant de réinitialiser votre passerelle, vérifiez les éléments suivants :

- Les adresses IP Internet (VIP) pour la passerelle VPN Azure et de la passerelle VPN locale sont configurés correctement dans l’Azure et les stratégies VPN en local.
- La clé doit être identique sur passerelles VPN Azure et en local.
- Si vous appliquez une configuration IPsec/IKE spécifique, telles que le chiffrement, algorithmes de hachage et PFS (confidentialité de transmission parfaite), vous assurer que la Azure et les locaux passerelles VPN ont les mêmes configurations.

## <a name="reset-a-vpn-gateway-using-the-resource-management-deployment-model"></a>Réinitialiser une passerelle VPN en utilisant le modèle de déploiement de gestion des ressources

L’applet de commande PowerShell le Gestionnaire de ressources pour la réinitialisation de passerelle est `Reset-AzureRmVirtualNetworkGateway`. L’exemple suivant rétablit la passerelle VPN Azure « VNet1GW », dans le groupe de ressources « TestRG1 ».

    $gw = Get-AzureRmVirtualNetworkGateway -Name VNet1GW -ResourceGroup TestRG1
    Reset-AzureRmVirtualNetworkGateway -VirtualNetworkGateway $gw

## <a name="reset-a-vpn-gateway-using-the-classic-deployment-model"></a>Réinitialiser une passerelle VPN à l’aide du modèle de déploiement classique

L’applet de commande PowerShell pour la réinitialisation de passerelle VPN Azure est `Reset-AzureVNetGateway`. L’exemple suivant rétablit la passerelle VPN Azure pour le réseau virtuel appelé « ContosoVNet ».
 
    Reset-AzureVNetGateway –VnetName “ContosoVNet” 

Résultat :

    Error          :
    HttpStatusCode : OK
    Id             : f1600632-c819-4b2f-ac0e-f4126bec1ff8
    Status         : Successful
    RequestId      : 9ca273de2c4d01e986480ce1ffa4d6d9
    StatusCode     : OK


## <a name="next-steps"></a>Étapes suivantes
    
Voir la [référence d’applet de commande PowerShell la gestion des services](https://msdn.microsoft.com/library/azure/mt617104.aspx) et la [référence de l’applet de commande PowerShell le Gestionnaire de ressources](http://go.microsoft.com/fwlink/?LinkId=828732) pour plus d’informations.







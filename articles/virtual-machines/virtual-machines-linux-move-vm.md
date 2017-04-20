<properties
    pageTitle="Déplacer une machine virtuelle Linux | Microsoft Azure"
    description="Déplacer un Linux VM vers un autre groupe ressource ou d’abonnement Azure dans le modèle de déploiement du Gestionnaire de ressources."
    services="virtual-machines-linux"
    documentationCenter=""
    authors="cynthn"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machines-linux"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/08/2016"
    ms.author="cynthn"/>

    


# <a name="move-a-linux-vm-to-another-subscription-or-resource-group"></a>Déplacer un Linux VM vers un autre groupe de ressource ou d’abonnement

Cet article décrit comment déplacer un Linux VM entre plusieurs abonnements ou des groupes de ressources. Déplacement d’une machine virtuelle entre les abonnements peut être utile si vous avez créé une machine virtuelle à un abonnement personnel et souhaite pour le déplacer vers l’abonnement de votre entreprise.

> [AZURE.NOTE] Nouveaux ID de ressources est créés dans le cadre du déplacement. Une fois que la machine virtuelle a été déplacée, vous devez mettre à jour vos outils et les scripts à utiliser la nouvelle ressource ID. 


## <a name="use-the-azure-cli-to-move-a-vm"></a>L’infrastructure du langage commun Azure permet de déplacer une machine virtuelle 

Pour déplacer avec succès une machine virtuelle, vous devez déplacer la machine virtuelle et toutes ses ressources prise en charge. La commande **Afficher groupe azure** permet de répertorier toutes les ressources dans un groupe de ressources et leurs numéros d’identification. Il est utile de rediriger la sortie de cette commande vers un fichier afin que vous pouvez copier et coller les ID de commandes ultérieures.

    azure group show <resourceGroupName>

Pour déplacer une machine virtuelle et ses ressources vers un autre groupe de ressources, utilisez la commande **déplacer ressource azure** d’infrastructure du langage commun. L’exemple suivant montre comment déplacer une machine virtuelle et les plus courants ressources que nécessaires. Nous utilisez le paramètre **-i** et passez dans une liste séparée par des virgules (sans espace) d’ID pour les ressources à déplacer.

    
    vm=/subscriptions/<sourceSubscriptionID>/resourceGroups/<sourceResourceGroup>/providers/Microsoft.Compute/virtualMachines/<vmName>
    nic=/subscriptions/<sourceSubscriptionID>/resourceGroups/<sourceResourceGroup>/providers/Microsoft.Network/networkInterfaces/<nicName>
    nsg=/subscriptions/<sourceSubscriptionID>/resourceGroups/<sourceResourceGroup>/providers/Microsoft.Network/networkSecurityGroups/<nsgName>
    pip=/subscriptions/<sourceSubscriptionID>/resourceGroups/<sourceResourceGroup>/providers/Microsoft.Network/publicIPAddresses/<publicIPName>
    vnet=/subscriptions/<sourceSubscriptionID>/resourceGroups/<sourceResourceGroup>/providers/Microsoft.Network/virtualNetworks/<vnetName>
    diag=/subscriptions/<sourceSubscriptionID>/resourceGroups/<sourceResourceGroup>/providers/Microsoft.Storage/storageAccounts/<diagnosticStorageAccountName>
    storage=/subscriptions/<sourceSubscriptionID>/resourceGroups/<sourceResourceGroup>/providers/Microsoft.Storage/storageAccounts/<storageAcountName>      
    
    azure resource move --ids $vm,$nic,$nsg,$pip,$vnet,$storage,$diag -d "<destinationResourceGroup>"
    
Si vous souhaitez déplacer la machine virtuelle et ses ressources vers un autre abonnement, ajoutez la **--destination subscriptionId & #60 ; destinationSubscriptionID & #62 ;** paramètre pour spécifier l’abonnement de destination.

S’il s’agit de l’invite de commande sur un ordinateur Windows, vous devez ajouter un **$** devant les noms de variables lorsque vous les déclarez. Cela n’est pas nécessaire en Linux.

Vous êtes invité à confirmer que vous voulez déplacer la ressource spécifiée. Tapez **Y** pour confirmer que vous voulez déplacer les ressources.
    

[AZURE.INCLUDE [virtual-machines-common-move-vm](../../includes/virtual-machines-common-move-vm.md)]

## <a name="next-steps"></a>Étapes suivantes

Vous pouvez déplacer différents types de ressources entre les abonnements et les groupes de ressources. Pour plus d’informations, voir [déplacer des ressources à nouveau groupe de ressources ou d’abonnement](../resource-group-move-resources.md).    
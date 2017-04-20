<properties
    pageTitle="Redimensionner une machine virtuelle Windows classique | Microsoft Azure"
    description="Redimensionner une machine virtuelle Windows créée dans le modèle de déploiement classique, à l’aide de Powershell Azure."
    services="virtual-machines-windows"
    documentationCenter=""
    authors="Drewm3"
    manager="timlt"
    editor=""
    tags="azure-service-management"/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="na"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/19/2016"
    ms.author="drewm"/>


# <a name="resize-a-windows-vm-created-in-the-classic-deployment-model"></a>Redimensionner une machine virtuelle Windows créé dans le modèle de déploiement classique

Cet article vous explique comment redimensionner une machine virtuelle Windows, créé dans le modèle de déploiement classique à l’aide de Powershell Azure.

Quand vous déterminez la possibilité de redimensionner une machine virtuelle, il existe deux concepts qui contrôlent la plage de tailles disponibles pour redimensionner la machine virtuelle. Le premier concept est la région dans lequel votre ordinateur virtuel est déployé. La liste des tailles de mémoire virtuelle disponibles dans la région se trouve sous l’onglet Services de la page web Azure régions. Le deuxième concept est le matériel qui héberge actuellement votre machine virtuelle. Les serveurs physiques d’hébergement machines virtuelles sont regroupées dans des groupes de matériel physique courantes. La méthode de modification d’une taille de la mémoire virtuelle varie en fonction de si la nouvelle taille machine virtuelle souhaitée est pris en charge par le cluster matériel qui héberge actuellement la machine virtuelle.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Vous pouvez également [redimensionner une machine virtuelle créée dans le modèle de déploiement du Gestionnaire de ressources](virtual-machines-windows-resize-vm.md).


## <a name="add-your-account"></a>Ajouter votre compte

Vous devez configurer Azure PowerShell pour travailler avec des ressources Azure classiques. Suivez les étapes ci-dessous pour configurer Azure PowerShell pour gérer les ressources classiques.

1. À l’invite PowerShell, tapez `Add-AzureAccount` et appuyez sur **entrée**. 
2. Entrez l’adresse de messagerie associée à votre abonnement Azure, puis cliquez sur **Continuer**. 
3. Tapez le mot de passe pour votre compte. 
4. Cliquez sur **se connecter**. 


## <a name="resize-in-the-same-hardware-cluster"></a>Redimensionner dans le même cluster de matériel

Pour redimensionner une machine virtuelle à une taille disponible dans le cluster matériel qui héberge la machine virtuelle, procédez comme suit.

1. Exécutez la commande PowerShell suivante pour répertorier les tailles de mémoire virtuelle disponibles dans le cluster matériel hébergeant le service de nuage qui contient la machine virtuelle.

    ```powershell
    Get-AzureService | where {$_.ServiceName -eq "<cloudServiceName>"}
    ```

2. Exécutez les commandes suivantes pour redimensionner la machine virtuelle.

    ```powershell
    Get-AzureVM -ServiceName <cloudServiceName> -Name <vmName> | Set-AzureVMSize -InstanceSize <newVMSize> | Update-AzureVM
    ```

## <a name="resize-on-a-new-hardware-cluster"></a>Redimensionner un nouveau cluster de matériel

Pour redimensionner une machine virtuelle à une taille non disponible dans le cluster matériel qui héberge la machine virtuelle, le cloud service et tous les ordinateurs virtuels dans le service cloud doivent être recréés. Chaque service cloud est hébergé sur un cluster de matériel unique pour tous les ordinateurs virtuels dans le service de nuage doivent avoir une taille qui est pris en charge sur un cluster de matériel. Les étapes suivantes décrira comment redimensionner une machine virtuelle en supprimant, puis recréer le service cloud.

1. Exécutez la commande PowerShell suivante pour répertorier les tailles de mémoire virtuelle disponibles dans la région. 

    ```powershell
    Get-AzureLocation | where {$_.Name -eq "<locationName>"}
    ```

2. Prenez note de tous les paramètres de configuration pour chaque machine virtuelle dans le service de nuage qui contient la machine virtuelle à redimensionner. 
3. Supprimer tous les ordinateurs virtuels dans le service cloud en sélectionnant l’option pour conserver les disques pour chaque machine virtuelle.
4. Recréez la machine virtuelle à redimensionner à l’aide de la taille de mémoire virtuelle souhaitée.
5. Recréez toutes les autres machines virtuelles qui étaient dans le service cloud à l’aide d’une taille de mémoire virtuelle disponible dans le cluster matériel maintenant hébergeant le service cloud.

Un exemple de script pour supprimer et recréer un service cloud à l’aide d’une nouvelle taille de la mémoire virtuelle sont accessibles [ici](https://github.com/Azure/azure-vm-scripts). 


## <a name="next-steps"></a>Étapes suivantes

- [Redimensionner une machine virtuelle créée dans le modèle de déploiement du Gestionnaire de ressources](virtual-machines-windows-resize-vm.md).
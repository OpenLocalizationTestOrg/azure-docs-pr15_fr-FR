<properties
    pageTitle="Réinitialiser le mot de passe ou la configuration de bureau à distance sur un ordinateur Windows virtuel | Microsoft Azure"
    description="Découvrez comment réinitialiser un mot de passe ou les services Bureau à distance sur un ordinateur Windows virtuel à l’aide du portail Azure ou PowerShell Azure."
    services="virtual-machines-windows"
    documentationCenter=""
    authors="iainfoulds"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/01/2016"
    ms.author="iainfou"/>

# <a name="how-to-reset-the-remote-desktop-service-or-its-login-password-in-a-windows-vm"></a>Comment réinitialiser le service de bureau à distance ou son mot de passe dans une machine virtuelle Windows

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

Si vous ne pouvez pas vous connecter à un Windows machine virtuelle (), vous pouvez réinitialiser le mot de passe d’administrateur local ou réinitialiser la configuration du service Bureau à distance. Vous pouvez utiliser le portail Azure ou l’extension accès machine virtuelle dans Azure PowerShell pour réinitialiser le mot de passe. Si vous utilisez PowerShell, vérifiez que vous avez le dernier module PowerShell installé sur votre ordinateur de travail et que vous êtes connecté à votre abonnement Azure. Pour plus d’informations, Découvrez [comment installer et configurer Azure PowerShell](../powershell-install-configure.md).

> [AZURE.TIP] Vous pouvez vérifier la version de PowerShell que vous avez installé à l’aide`Import-Module Azure, AzureRM; Get-Module Azure, AzureRM | Format-Table Name, Version`

## <a name="windows-vms-in-resource-manager-deployment-model"></a>Machines virtuelles Windows dans le modèle de déploiement Gestionnaire de ressources

### <a name="azure-portal"></a>Portail Azure
Sélectionnez votre machine virtuelle en cliquant sur **Parcourir** > **machines virtuelles** > *votre machine virtuelle Windows* > **tous les paramètres** > **Réinitialiser votre mot de passe**. La carte de réinitialiser votre mot de passe s’affiche :

![Page de réinitialisation du mot de passe](./media/virtual-machines-windows-reset-rdp/Portal-RM-PW-Reset-Windows.png)

Entrez le nom d’utilisateur et un mot de passe, puis cliquez sur **Enregistrer**. Réessayez de vous connecter à votre ordinateur virtuel.

### <a name="vmaccess-extension-and-powershell"></a>Extension VMAccess et PowerShell

Vérifiez que vous avez Azure PowerShell 1.0 ou version ultérieure est installé, et vous êtes connecté à votre compte en utilisant la `Login-AzureRmAccount` applet de commande.

#### <a name="reset-the-local-administrator-account-password"></a>**Réinitialiser le mot de passe du compte administrateur local**

Vous pouvez réinitialiser le nom d’utilisateur ou de mot de passe administrateur à l’aide de la commande PowerShell [Set-AzureRmVMAccessExtension](https://msdn.microsoft.com/library/mt619447.aspx) .

Créer des informations d’identification de compte de votre administrateur local à l’aide de la commande suivante :

    $cred=Get-Credential

Si vous tapez un nom différent de celui du compte actuel, la commande extension VMAccess suivante renomme le compte d’administrateur local, affecte le mot de passe à ce compte et génère un événement de fermeture de session Bureau à distance. Si le compte d’administrateur local est désactivé, l’extension VMAccess activée.

Utilisez l’extension accès machine virtuelle pour configurer les nouvelles informations d’identification comme suit :

    Set-AzureRmVMAccessExtension -ResourceGroupName "myRG" -VMName "myVM" -Name "myVMAccess" `
        -Location WestUS -UserName $cred.GetNetworkCredential().Username `
        -Password $cred.GetNetworkCredential().Password -typeHandlerVersion "2.0"


Remplacer `myRG`, `myVM`, `myVMAccess`et l’emplacement contenant les valeurs pertinents pour la configuration de votre.


#### <a name="reset-the-remote-desktop-service-configuration"></a>**Réinitialiser la configuration du service Bureau à distance**

Vous pouvez réinitialiser d’accès à distance à votre ordinateur virtuel à l’aide de [Jeu AzureRmVMExtension](https://msdn.microsoft.com/library/mt603745.aspx) ou [Set-AzureRmVMAccessExtension](https://msdn.microsoft.com/library/mt619447.aspx), comme suit. (Remplacer les `myRG`, `myVM`, `myVMAccess` et l’emplacement par vos propres valeurs.)

    Set-AzureRmVMExtension -ResourceGroupName "myRG" -VMName "myVM" `
        -Name "myVMAccess" -ExtensionType "VMAccessAgent" -Location WestUS `
        -Publisher "Microsoft.Compute" -typeHandlerVersion "2.0"

Ou :<br>

    Set-AzureRmVMAccessExtension -ResourceGroupName "myRG" -VMName "myVM" `
        -Name "myVMAccess" -Location WestUS -typeHandlerVersion "2.0


> [AZURE.TIP] Les deux commandes ajoutent un agent accès machine virtuelle nommé à la machine virtuelle. À tout moment, une machine virtuelle peut avoir un seul machine virtuelle accès agent. Pour définir le niveau d’accès machine virtuelle propriétés de l’agent avec succès, supprimez l’agent access défini précédemment à l’aide de des options `Remove-AzureRmVMAccessExtension` ou `Remove-AzureRmVMExtension`. À partir d’Azure PowerShell version 1.2.2, vous pouvez éviter cette étape lorsque vous utilisez `Set-AzureRmVMExtension` avec un `-ForceRerun` option. Lorsque vous utilisez `-ForceRerun`, vérifiez que vous utilisez le même nom pour l’agent d’access machine virtuelle en tant que jeu par la commande précédente.

Si vous toujours pas vous connecter à distance à votre machine virtuelle, consultez les étapes supplémentaires pour essayer de [résoudre les problèmes de bureau à distance connexions à un ordinateur fonctionnant sous Windows Azure virtuel](virtual-machines-windows-troubleshoot-rdp-connection.md).


## <a name="windows-vms-in-the-classic-deployment-model"></a>Machines virtuelles Windows dans le modèle de déploiement classique

### <a name="azure-portal"></a>Portail Azure

Pour une machine virtuelle créée à l’aide du modèle de déploiement classique, vous pouvez utiliser le [portail Azure](https://portal.azure.com) pour réinitialiser le service de bureau à distance. Cliquez sur : **Parcourir** > **machines virtuelles (classique)** > *votre machine virtuelle Windows* > **Réinitialisation à distance...**. La page suivante s’affiche.

![Rétablir la page configuration RDP](./media/virtual-machines-windows-reset-rdp/Portal-RDP-Reset-Windows.png)

Vous pouvez également essayer de réinitialiser le nom et le mot de passe du compte d’administrateur local. Cliquez sur : **Parcourir** > **machines virtuelles (classique)** > *votre machine virtuelle Windows* > **tous les paramètres** > **Réinitialiser votre mot de passe**. La page suivante s’affiche.

![Page de réinitialisation du mot de passe](./media/virtual-machines-windows-reset-rdp/Portal-PW-Reset-Windows.png)

Une fois que vous entrez le nouveau nom d’utilisateur et mot de passe, cliquez sur **Enregistrer**.

### <a name="vmaccess-extension-and-powershell"></a>Extension VMAccess et PowerShell

Vérifiez que l’Agent machine virtuelle est installé sur l’ordinateur virtuel. L’extension VMAccess ne doit pas être installé avant de pouvoir utiliser, dans la mesure où l’Agent machine virtuelle est disponible. Vérifiez que l’Agent de machine virtuelle est déjà installé à l’aide de la commande suivante. (Remplacez « myCloudService » et « myVM » par les noms de votre service cloud et votre machine virtuelle, respectivement. Vous pouvez découvrir ces noms en exécutant `Get-AzureVM` sans paramètres.)

    $vm = Get-AzureVM -ServiceName "myCloudService" -Name "myVM"
    write-host $vm.VM.ProvisionGuestAgent

Si la commande **écriture host** affiche **True**, l’Agent de machine virtuelle est installé. S’il affiche **False**, consultez les instructions et un lien vers le téléchargement dans le billet de blog Azure [Agent machine virtuelle et Extensions - partie 2](http://go.microsoft.com/fwlink/p/?linkid=403947&clcid=0x409) .

Si vous avez créé la machine virtuelle à l’aide du portail, vérifiez si `$vm.GetInstance().ProvisionGuestAgent` renvoie la **valeur True**. Dans le cas contraire, vous pouvez le configurer à l’aide de cette commande :

    $vm.GetInstance().ProvisionGuestAgent = $true

Cette commande empêche l’erreur suivante lorsque vous exécutez la commande **Set-AzureVMExtension** dans la procédure suivante : « L’Agent de mise en service invité doit être activé sur l’objet machine virtuelle avant d’Extension d’accès aux IaaS VM. »

#### <a name="reset-the-local-administrator-account-password"></a>**Réinitialiser le mot de passe du compte administrateur local**

Créez une connexion les informations d’identification avec le nom de compte d’administrateur local actuel et un nouveau mot de passe, puis exécutez la `Set-AzureVMAccessExtension` comme suit.

    $cred=Get-Credential
    Set-AzureVMAccessExtension –vm $vm -UserName $cred.GetNetworkCredential().Username `
        -Password $cred.GetNetworkCredential().Password  | Update-AzureVM

Si vous tapez un nom différent de celui du compte actuel, l’extension VMAccess renomme le compte d’administrateur local, affecte le mot de passe à ce compte et émet un bureau à distance déconnexion. Si le compte d’administrateur local est désactivé, l’extension VMAccess activée.

Ces commandes également réinitialiser la configuration du service Bureau à distance.

#### <a name="reset-the-remote-desktop-service-configuration"></a>**Réinitialiser la configuration du service Bureau à distance**

Pour réinitialiser la configuration du service Bureau à distance, exécutez la commande suivante :

    Set-AzureVMAccessExtension –vm $vm | Update-AzureVM

L’extension VMAccess s’exécute deux commandes sur l’ordinateur virtuel :

- `netsh advfirewall firewall set rule group="Remote Desktop" new enable=Yes`

Cette commande permet au groupe pare-feu Windows intégré qui permet de trafic entrant Bureau à distance, qui utilise le port TCP 3389.

- `Set-ItemProperty -Path 'HKLM:\System\CurrentControlSet\Control\Terminal Server' -name "fDenyTSConnections" -Value 0`

Cette commande définit les fDenyTSConnections valeur de Registre à 0, l’activation des connexions de bureau à distance.


## <a name="next-steps"></a>Étapes suivantes

Si l’extension accès machine virtuelle Azure ne répond pas et que vous ne pouvez pas réinitialiser le mot de passe, vous pouvez [Réinitialiser le mot de Windows local en mode hors connexion](virtual-machines-windows-reset-local-password-without-agent.md). Cette méthode est un processus plus avancé et vous oblige à se connecter le disque dur virtuel de la machine virtuelle posant problème à un autre ordinateur virtuel. Suivez les étapes décrites dans cet article tout d’abord, puis essayez uniquement la méthode de réinitialisation de mot de passe en mode hors connexion en dernier recours.

[Fonctionnalités et extensions machine virtuelle azure](virtual-machines-windows-extensions-features.md)

[Se connecter à une machine virtuelle Azure avec RDP ou SSH](http://msdn.microsoft.com/library/azure/dn535788.aspx)

[Résoudre les problèmes de connexions de bureau à distance à un ordinateur fonctionnant sous Windows Azure virtuel](virtual-machines-windows-troubleshoot-rdp-connection.md)

<properties
    pageTitle="Installer sécurité approfondie Micro tendance sur une machine virtuelle | Microsoft Azure"
    description="Cet article décrit comment installer et configurer la sécurité de tendance Micro sur un ordinateur virtuel créé avec le modèle de déploiement classique dans Azure."
    services="virtual-machines-windows"
    documentationCenter=""
    authors="iainfoulds"
    manager="timlt"
    editor=""
    tags="azure-service-management"/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-multiple"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/24/2016"
    ms.author="iainfou"/>


# <a name="how-to-install-and-configure-trend-micro-deep-security-as-a-service-on-a-windows-vm"></a>Découvrez comment installer et configurer la sécurité complète du Micro tendance en tant que Service sur un ordinateur Windows virtuel

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]

Cet article vous explique comment installer et configurer la sécurité complète du Micro tendance en tant que Service sur une nouvelle ou existante machine virtuelle (machine virtuelle) exécutant Windows Server. Sécurité approfondie en tant que Service inclut protection contre les logiciels malveillants, un pare-feu, un système de prévention des intrusions et analyse de l’intégrité référentielle.

Le client est installé avec une extension de sécurité via l’Agent de machine virtuelle. Sur un nouvel ordinateur virtuel, vous installez l’Agent machine virtuelle ainsi que l’Agent de sécurité approfondie. Sur un ordinateur virtuel existant qui n’a pas l’Agent machine virtuelle, vous devez télécharger et installer tout d’abord. Cet article aborde les deux situations.

Si vous avez un abonnement existant à partir de tendance Micro pour une solution en local, vous pouvez l’utiliser pour protéger vos machines virtuelles Azure. Si vous n’êtes pas encore un client, vous pouvez vous inscrire pour un abonnement d’évaluation. Pour plus d’informations sur cette solution, consultez le Micro de tendance blog [Microsoft Azure machine virtuelle Extension pour approfondie sécurité de l’Agent](http://go.microsoft.com/fwlink/p/?LinkId=403945).

## <a name="install-the-deep-security-agent-on-a-new-vm"></a>Installer l’Agent de sécurité approfondies sur une nouvelle machine virtuelle

Le [portail classique Azure](http://manage.windowsazure.com) vous permet d’installer l’Agent machine virtuelle et l’extension de sécurité tendance Micro lorsque vous utilisez l’option **De la galerie** pour créer la machine virtuelle. Si vous créez une seule machine virtuelle, à l’aide du portail est un moyen facile pour ajouter une protection à partir de tendance Micro.

Cette option **De la galerie** s’ouvre un Assistant qui vous permet de configurer la machine virtuelle. Vous utilisez la dernière page de l’Assistant pour installer l’Agent de machine virtuelle et l’extension de sécurité Micro de tendance. Pour obtenir des instructions générales, voir [créer une machine virtuelle exécutant Windows dans le portail classique Azure](virtual-machines-windows-classic-tutorial.md). Lorsque vous accédez à la dernière page de l’Assistant, procédez comme suit :

1.  Sous **Agent machine virtuelle**, cochez **Installation de l’Agent machine virtuelle**.

2.  Sous les **Extensions de sécurité**, vérifiez **L’Agent de sécurité approfondie tendance Micro**.

    ![Installer l’Agent machine virtuelle et l’Agent de sécurité](./media/virtual-machines-windows-classic-install-trend/InstallVMAgentandTrend.png)

3.  Activez la case à cocher pour créer la machine virtuelle.

## <a name="install-the-deep-security-agent-on-an-existing-vm"></a>Installer l’Agent de sécurité approfondie sur un ordinateur virtuel existant

Pour installer l’agent sur un ordinateur virtuel existant, vous devez les éléments suivants :

- Module Azure PowerShell, la version 0.8.2 ou versions ultérieures, installés sur votre ordinateur local. Vous pouvez vérifier la version de PowerShell Azure que vous avez installé à l’aide de la **azure Get-Module | version de la table de format** commande. Pour obtenir des instructions et un lien vers la dernière version, voir [comment installer et configurer Azure PowerShell](../powershell-install-configure.md). Connectez-vous à votre abonnement Azure à l’aide `Add-AzureAccount`.

- L’Agent de machine virtuelle installé sur l’ordinateur virtuel cible.

Tout d’abord, vérifiez que l’Agent machine virtuelle est déjà installée. Entrez le nom du service cloud et nom de la machine virtuelle, puis exécutez les commandes suivantes à une invite de commande PowerShell Azure au niveau de l’administrateur. Remplacez tout entre guillemets, y compris la < et > caractères.

    $CSName = "<cloud service name>"
    $VMName = "<virtual machine name>"
    $vm = Get-AzureVM -ServiceName $CSName -Name $VMName
    write-host $vm.VM.ProvisionGuestAgent

Si vous ne connaissez pas le service cloud et le nom de la machine virtuelle, exécutez **Get-AzureVM** pour afficher ces informations pour tous les ordinateurs virtuels dans votre abonnement actuel.

Si la commande **écriture host** renvoie la **valeur True**, l’Agent de machine virtuelle est installé. Si elle retourne **False**, consultez les instructions et un lien vers le téléchargement du blog Azure publier [Agent machine virtuelle et Extensions - partie 2](http://go.microsoft.com/fwlink/p/?LinkId=403947).

Si l’Agent machine virtuelle est installé, exécutez ces commandes.

    $Agent = Get-AzureVMAvailableExtension TrendMicro.DeepSecurity -ExtensionName TrendMicroDSA

    Set-AzureVMExtension -Publisher TrendMicro.DeepSecurity –Version $Agent.Version -ExtensionName TrendMicroDSA -VM $vm | Update-AzureVM

## <a name="next-steps"></a>Étapes suivantes

Il peut prendre quelques minutes pour l’agent démarre en cours d’exécution lorsqu’elle est installée. Après cela, vous devez activer sécurité approfondies sur l’ordinateur virtuel afin qu’il peut être géré par un gestionnaire de sécurité approfondie. Consultez la rubrique suivante pour obtenir des instructions supplémentaires :

- Article de tendance sur cette solution, [Instant-On Cloud Security pour Microsoft Azure](http://go.microsoft.com/fwlink/?LinkId=404101)
- Un [exemple de script Windows PowerShell](http://go.microsoft.com/fwlink/?LinkId=404100) pour configurer la machine virtuelle
- [Instructions](http://go.microsoft.com/fwlink/?LinkId=404099) pour l’échantillon

## <a name="additional-resources"></a>Ressources supplémentaires

[Comment se connecter à une machine virtuelle exécutant Windows Server]

[Extensions de machine virtuelle Azure et de fonctionnalités]


<!--Link references-->
[Comment se connecter à une machine virtuelle exécutant Windows Server]: virtual-machines-windows-classic-connect-logon.md
[Extensions de machine virtuelle Azure et de fonctionnalités]: http://go.microsoft.com/fwlink/p/?linkid=390493&clcid=0x409

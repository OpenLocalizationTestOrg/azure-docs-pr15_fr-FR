<properties
    pageTitle="Installer Symantec Endpoint Protection sur un ordinateur virtuel | Microsoft Azure"
    description="Découvrez comment installer et configurer l’extension de sécurité Symantec Endpoint Protection sur une nouvelle ou existante Azure machine virtuelle créée avec le modèle de déploiement classique."
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

# <a name="how-to-install-and-configure-symantec-endpoint-protection-on-a-windows-vm"></a>Comment installer et configurer Symantec Endpoint Protection sur un ordinateur Windows virtuel

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]

Cet article vous explique comment installer et configurer le client Symantec Endpoint Protection sur un ordinateur virtuel existant (machine virtuelle) exécutant Windows Server. Il s’agit du client complet, qui inclut des services tels qu’anti-intrusion antivirus et la protection contre les logiciels espions et pare-feu. Le client est installé comme une extension de sécurité à l’aide de l’Agent de machine virtuelle.

Si vous avez un abonnement existant de Symantec pour une solution en local, vous pouvez l’utiliser pour protéger vos machines virtuelles Azure. Si vous n’êtes pas encore un client, vous pouvez vous inscrire pour un abonnement d’évaluation. Pour plus d’informations sur cette solution, voir [Symantec Endpoint Protection sur Azure plate-forme Microsoft][Symantec]. Cette page comporte également des liens vers des informations relatives aux licences et des instructions pour l’installation du client si vous êtes déjà un client Symantec.

## <a name="install-symantec-endpoint-protection-on-an-existing-vm"></a>Installer Symantec Endpoint Protection sur un ordinateur virtuel existant

Avant de commencer, vous devez les éléments suivants :

- Module Azure PowerShell, la version 0.8.2 ou version ultérieure, sur votre ordinateur professionnel. Vous pouvez vérifier la version de PowerShell Azure que vous avez installé avec la **azure Get-Module | version de la table de format** commande. Pour obtenir des instructions et un lien vers la dernière version, voir [comment installer et configurer Azure PowerShell][PS]. Connectez-vous à votre abonnement Azure à l’aide `Add-AzureAccount`.

- L’Agent de machine virtuelle s’exécutant sur la Machine virtuelle Azure.

Tout d’abord, vérifiez que l’Agent de machine virtuelle est déjà installé sur l’ordinateur virtuel. Entrez le nom du service cloud et nom de la machine virtuelle, puis exécutez les commandes suivantes à une invite de commande PowerShell Azure au niveau de l’administrateur. Remplacez tout entre guillemets, y compris la < et > caractères.

> [AZURE.TIP] Si vous ne connaissez pas le service cloud et le nom de la machine virtuelle, exécutez **Get-AzureVM** pour répertorier les noms pour tous les ordinateurs virtuels dans votre abonnement actuel.

    $CSName = "<cloud service name>"
    $VMName = "<virtual machine name>"
    $vm = Get-AzureVM -ServiceName $CSName -Name $VMName
    write-host $vm.VM.ProvisionGuestAgent

Si la commande **écriture host** affiche **True**, l’Agent de machine virtuelle est installé. S’il affiche **False**, consultez les instructions et un lien vers le téléchargement du blog Azure publier [Agent machine virtuelle et Extensions - partie 2][Agent].

Si l’Agent machine virtuelle est installé, exécutez ces commandes pour installer l’agent Symantec Endpoint Protection.

    $Agent = Get-AzureVMAvailableExtension -Publisher Symantec -ExtensionName SymantecEndpointProtection

    Set-AzureVMExtension -Publisher Symantec –Version $Agent.Version -ExtensionName SymantecEndpointProtection -VM $vm | Update-AzureVM

Pour vérifier que l’extension de sécurité Symantec a été installée et est à jour :

1.  Ouvrez une session sur l’ordinateur virtuel. Pour plus d’informations, voir [comment ouvrir une session sur un ordinateur virtuel exécutant Windows Server][Logon].
2.  Pour Windows Server 2008 R2, cliquez sur **Démarrer > Symantec Endpoint Protection**. Pour Windows Server 2012 ou Windows Server 2012 R2, à partir de l’écran Démarrer, tapez **Symantec**, puis cliquez sur **Symantec Endpoint Protection**.
3.  Sous l’onglet **état** de la fenêtre **État Symantec Endpoint Protection** , appliquer des mises à jour ou redémarrez si nécessaire.

## <a name="additional-resources"></a>Ressources supplémentaires

[Comment se connecter à une Machine virtuelle exécutant Windows Server][Logon]

[Fonctionnalités et Extensions de machine virtuelle azure][Ext]


<!--Link references-->
[Symantec]: http://www.symantec.com/connect/blogs/symantec-endpoint-protection-now-microsoft-azure

[Portal]: http://manage.windowsazure.com

[Create]: virtual-machines-windows-classic-tutorial.md

[PS]: ../powershell-install-configure.md

[Agent]: http://go.microsoft.com/fwlink/p/?LinkId=403947

[Logon]: virtual-machines-windows-classic-connect-logon.md

[Ext]: http://go.microsoft.com/fwlink/p/?linkid=390493
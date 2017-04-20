<properties
    pageTitle="Azure Active Directory Domain Services : Guide d’Administration | Microsoft Azure"
    description="Participer à une machine virtuelle Windows à un domaine géré à l’aide de PowerShell Azure et le modèle de déploiement classique."
    services="active-directory-ds"
    documentationCenter=""
    authors="mahesh-unnikrishnan"
    manager="stevenpo"
    editor="curtand"/>

<tags
    ms.service="active-directory-ds"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/01/2016"
    ms.author="maheshu"/>


# <a name="join-a-windows-server-virtual-machine-to-a-managed-domain-using-powershell"></a>Participer à une machine virtuelle Windows Server pour un domaine géré à l’aide de PowerShell

> [AZURE.SELECTOR]
- [Azure portal classique - Windows](active-directory-ds-admin-guide-join-windows-vm.md)
- [PowerShell - Windows](active-directory-ds-admin-guide-join-windows-vm-classic-powershell.md)

<br>

> [AZURE.IMPORTANT] Azure comporte deux modèles de déploiement différents pour la création et utilisation des ressources : [Gestionnaire de ressources et classique](../resource-manager-deployment-model.md). Cet article décrit l’utilisation du modèle de déploiement classique. Azure Active Directory Domain Services ne prend pas en charge le modèle de gestionnaire de ressources.

Ces étapes vous montrent comment personnaliser un jeu de commandes PowerShell Azure qui créent et préconfigurer un ordinateur fonctionnant sous Windows Azure virtuel en utilisant une approche de bloc de construction. Ces étapes vous permettent de créer un ordinateur fonctionnant sous Windows Azure virtuel et le joindre à un domaine géré Azure Active Directory Domain Services.

Ces étapes suivent une approche de remplissage-en-+ u pour créer des jeux de commandes PowerShell Azure. Cette approche peut être utile si vous ne connaissez pas PowerShell ou que vous voulez savoir quelles valeurs spécifier pour une configuration réussie. Les utilisateurs expérimentés PowerShell peuvent prendre les commandes et remplacer leurs propres valeurs pour les variables (les lignes commençant par « $»).

Si vous n’avez pas déjà fait, suivez les instructions de la rubrique [comment installer et configurer Azure PowerShell](../powershell-install-configure.md) installer PowerShell Azure sur votre ordinateur local. Ensuite, ouvrez une invite de commande Windows PowerShell.

## <a name="step-1-add-your-account"></a>Étape 1 : Ajouter votre compte

1. À l’invite PowerShell, tapez **Ajouter AzureAccount** et appuyez sur **entrée**.
2. Entrez l’adresse de messagerie associée à votre abonnement Azure, puis cliquez sur **Continuer**.
3. Tapez le mot de passe pour votre compte.
4. Cliquez sur **se connecter**.

## <a name="step-2-set-your-subscription-and-storage-account"></a>Étape 2 : Configurer votre abonnement et le compte de stockage

Définir votre abonnement Azure et un compte de stockage en exécutant ces commandes à l’invite de commande Windows PowerShell. Remplacez tout entre guillemets, y compris la < et > caractères, avec les noms sont corrects.

    $subscr="<subscription name>"
    $staccount="<storage account name>"
    Select-AzureSubscription -SubscriptionName $subscr –Current
    Set-AzureSubscription -SubscriptionName $subscr -CurrentStorageAccountName $staccount

Vous pouvez obtenir le nom de l’abonnement approprié à partir de la propriété SubscriptionName de la sortie de la commande **Get-AzureSubscription** . Vous pouvez obtenir le nom de compte de stockage correct de la propriété étiquette de la sortie de la commande **Get-AzureStorageAccount** après avoir exécuté la commande **Sélectionner AzureSubscription** .


## <a name="step-3-step-by-step-walkthrough---provision-the-virtual-machine-and-join-it-to-the-managed-domain"></a>Étape 3 : Procédure pas à pas - mise en service de la machine virtuelle et joindre au domaine géré
Voici la commande PowerShell Azure correspondante définie pour créer cet ordinateur virtuel, des lignes vides entre chaque bloc pour améliorer la lisibilité.

Spécifier des informations sur la machine virtuelle Windows à mettre en service.

    $family="Windows Server 2012 R2 Datacenter"
    $vmname="Contoso100-test"
    $vmsize="ExtraSmall"

Pour les valeurs InstanceSize pour D, DS ou machines virtuelles série G, voir [Machine virtuelle et les tailles de Service Cloud pour Azure](https://msdn.microsoft.com/library/azure/dn197896.aspx).

Fournissent des informations sur le domaine géré.

    $domaindns="contoso100.com"
    $domacctdomain="contoso100"

Spécifiez le nom du service cloud.

    $svcname="Contoso100-test"

Spécifiez le nom du réseau virtuel auquel la machine virtuelle doit être jointes. Vérifiez que le domaine géré-DS DAS est disponible dans ce réseau virtuel.

    $vnetname="MyPreviewVnet"

Sélectionnez l’image de la machine virtuelle à utiliser pour la mise en service de la machine virtuelle.

    $image=Get-AzureVMImage | where { $_.ImageFamily -eq $family } | sort PublishedDate -Descending | select -ExpandProperty ImageName -First 1

Configurer la machine virtuelle - machine virtuelle nom, taille d’instance et image à utiliser.

    $vm1=New-AzureVMConfig -Name $vmname -InstanceSize $vmsize -ImageName $image

Obtenir des informations d’identification d’administrateur local pour la machine virtuelle. Choisissez un mot de passe fort administrateur local.

    $localadmincred=Get-Credential –Message "Type the name and password of the local administrator account."

Obtenir des informations d’identification pour un compte d’utilisateur appartenant au groupe « AAD DC administrateurs » à rejoindre machine virtuelle pour le domaine géré. Ne spécifiez pas le nom de domaine - par exemple, dans notre exemple, nous spécifiez « bob » comme nom d’utilisateur.

    $domainadmincred=Get-Credential –Message "Now type the name (DO NOT INCLUDE THE DOMAIN) and password of an account in the AAD DC Administrators group, that has permission to add the machine to the domain."

Configurer la machine virtuelle : indiquez exigence de jointure de domaine et les informations d’identification requises.

    $vm1 | Add-AzureProvisioningConfig -AdminUsername $localadmincred.Username -Password $localadmincred.GetNetworkCredential().Password -WindowsDomain -Domain $domacctdomain -DomainUserName $domainadmincred.Username -DomainPassword $domainadmincred.GetNetworkCredential().Password -JoinDomain $domaindns

Définissez un sous-réseau pour la machine virtuelle.

    $vm1 | Set-AzureSubnet -SubnetNames "Subnet-1"

Facultatif : Pointez sur l’adresse IP du domaine. Si vous définissez les adresses IP du domaine géré Azure Active Directory Domain Services soit les serveurs DNS pour le réseau virtuel, cette étape n’est pas obligatoire.

    $dns = New-AzureDns -Name 'contoso100-dc1' -IPAddress '10.0.0.4'

À présent, mise en service de l’ordinateur à un domaine Windows virtuel.

    New-AzureVM –ServiceName $svcname -VMs $vm1 -VNetName $vnetname -Location "Central US" -DnsSettings $dns

<br>

## <a name="script-to-provision-a-windows-vm-and-automatically-join-it-to-an-aad-domain-services-managed-domain"></a>Script de mise en service d’une machine virtuelle Windows et automatiquement le joindre à un domaine géré DAS les Services de domaine
Cet ensemble de commande PowerShell crée une machine virtuelle pour un serveur de métier qui :

- Utilise l’image de Windows Server 2012 R2 centre de données.
- Est une machine virtuelle très petite.
- Contient le nom contoso-test.
- Est automatiquement domaine rejoint le domaine géré contoso100.
- Figure dans le même réseau virtuel que le domaine géré.

Voici l’exemple complet de script pour créer la machine virtuelle Windows et joindre automatiquement le domaine géré Azure Active Directory Domain Services.

    $family="Windows Server 2012 R2 Datacenter"
    $vmname="Contoso100-test"
    $vmsize="ExtraSmall"

    $domaindns="contoso100.com"
    $domacctdomain="contoso100"

    $svcname="Contoso100-test"
    $vnetname="MyPreviewVnet"

    $image=Get-AzureVMImage | where { $_.ImageFamily -eq $family } | sort PublishedDate -Descending | select -ExpandProperty ImageName -First 1

    $vm1=New-AzureVMConfig -Name $vmname -InstanceSize $vmsize -ImageName $image

    $localadmincred=Get-Credential –Message "Type the name and password of the local administrator account."

    $domainadmincred=Get-Credential –Message "Now type the name (not including the domain) and password of an account in the AAD DC Administrators group, that has permission to add the machine to the domain."

    $vm1 | Add-AzureProvisioningConfig -AdminUsername $localadmincred.Username -Password $localadmincred.GetNetworkCredential().Password -WindowsDomain -Domain $domacctdomain -DomainUserName $domainadmincred.Username -DomainPassword $domainadmincred.GetNetworkCredential().Password -JoinDomain $domaindns

    $vm1 | Set-AzureSubnet -SubnetNames "Subnet-1"

    $dns = New-AzureDns -Name 'contoso100-dc1' -IPAddress '10.0.0.4'

    New-AzureVM –ServiceName $svcname -VMs $vm1 -VNetName $vnetname -Location "Central US" -DnsSettings $dns

<br>

## <a name="related-content"></a>Contenu associé
- [Services de domaine Azure AD - guide de mise en route](./active-directory-ds-getting-started.md)

- [Administrer un domaine géré Azure Active Directory Domain Services](./active-directory-ds-admin-guide-administer-domain.md)

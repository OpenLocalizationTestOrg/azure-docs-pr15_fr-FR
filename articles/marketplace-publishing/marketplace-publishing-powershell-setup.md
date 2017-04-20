<properties
   pageTitle="Configurer la PowerShell pour créer une machine virtuelle de Marketplace | Microsoft Azure"
   description="Instructions de configuration Azure PowerShell et l’utiliser comme un processus facultatif flux pour créer des images de machine virtuelle à déployer et vendent sur le Azure Marketplace"
   services="marketplace-publishing"
   documentationCenter=""
   authors="HannibalSII"
   manager="hascipio"
   editor=""/>

<tags
   ms.service="marketplace"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="02/04/2016"
   ms.author="hascipio"/>

# <a name="set-up-azure-powershell-to-create-an-offer-for-the-azure-marketplace"></a>Configurer la Azure PowerShell pour créer une offre de Azure Marketplace
Pour plus d’informations sur la configuration de PowerShell dans Azure, Découvrez [comment installer et configurer Azure PowerShell](../powershell-install-configure.md). Une approche simple consiste à utiliser la méthode de certificats, qui télécharge et permet d’importer un certificat nécessaire pour l’authentification. Pour obtenir le certificat requis, utilisez l’applet de commande **Get-AzurePublishSettingsFile** . Enregistrez le fichier lorsque vous êtes invité. Pour importer le certificat dans une session PowerShell, utilisez l’applet de commande **Importer AzurePublishSettingsFile** .

Pour configurer et stocker les paramètres d’abonnement Microsoft Azure courantes pour la session PowerShell, utilisez les applets de commande **Set-AzureSubscription** et **Sélectionnez AzureSubscription** :

        Set-AzureSubscription -SubscriptionName “mySubName” -CurrentStorageAccountName “mystorageaccount”
        Select-AzureSubscription -SubscriptionName "mySubName" –Current

La première commande associe un compte de stockage par défaut à l’abonnement (nécessaire pour certaines opérations de mise à disponibilité machine virtuelle).  Le second rend l’abonnement celui en cours (reconnu par d’autres applets de commande).

## <a name="see-also"></a>Voir aussi
- [Mise en route : comment publier une offre à la Azure Marketplace](marketplace-publishing-getting-started.md)
- [Création d’une image de la machine virtuelle de Marketplace](marketplace-publishing-vm-image-creation.md)

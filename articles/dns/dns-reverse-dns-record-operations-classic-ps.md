<properties
   pageTitle="Gérer les enregistrements DNS inverses pour vos services Azure (classique) à l’aide de PowerShell | Microsoft Azure"
   description="Comment gérer les enregistrements DNS inverses ou enregistrements PTR pour les services Azure à l’aide de PowerShell dans le modèle de déploiement classique. "
   services="DNS"
   documentationCenter="na"
   authors="s-malone"
   manager="carmonm"
   editor=""
   tags="azure-service-management"
/>
<tags
   ms.service="DNS"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/28/2016"
   ms.author="smalone" />

# <a name="how-to-manage-reverse-dns-records-for-your-azure-services-classic-using-azure-powershell"></a>Comment gérer les enregistrements DNS inverses pour vos services Azure (classique) à l’aide de PowerShell Azure

[AZURE.INCLUDE [dns-reverse-dns-record-operations-arm-selectors-include.md](../../includes/dns-reverse-dns-record-operations-arm-selectors-include.md)]
<BR>
[AZURE.INCLUDE [DNS-reverse-dns-record-operations-intro-include.md](../../includes/dns-reverse-dns-record-operations-intro-include.md)]
<BR>
[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-classic-include.md)]Découvrez comment [effectuer ces étapes en utilisant le modèle de gestionnaire de ressources](dns-reverse-dns-record-operations-ps.md).

## <a name="validation-of-reverse-dns-records"></a>Validation d’enregistrement DNS inverse
Pour vous assurer qu'un tiers ne peut pas créer des enregistrements DNS inverses mappage à vos domaines DNS, Azure permet uniquement la création d’un enregistrement DNS inverse où une des opérations suivantes est vraie :

- L’inverse FQDN DNS est le nom du Service Cloud pour laquelle elle a été spécifiée ou n’importe quel nom de Service Cloud dans le même abonnement, par exemple, DNS inversée est « contosoapp1.cloudapp.net. ».
- Le FQDN DNS inverse transférer résout le nom ou l’adresse IP du Service Cloud pour laquelle il a été spécifié, ou à n’importe quel nom de Service de nuage ou dans le même abonnement par exemple, DNS inversée est « app1.contoso.com ». qui est un alias CName pour contosoapp1.cloudapp.net.

Contrôles de validation sont exécutées uniquement lorsque la propriété DNS inverse d’un Service Cloud est définie ou modifiée. Ré-validation périodique n’est pas exécutée.

## <a name="add-reverse-dns-to-existing-cloud-services"></a>Ajouter DNS inverse aux Services Cloud existants
Vous pouvez ajouter un enregistrement DNS inverse à un Service Cloud existant à l’aide de l’applet de commande « Set-AzureService » :

    PS C:\> Set-AzureService –ServiceName “contosoapp1” –Description “App1 with Reverse DNS” –ReverseDnsFqdn “contosoapp1.cloudapp.net.”

## <a name="create-a-cloud-service-with-reverse-dns"></a>Créer un Service Cloud avec DNS inversée
Vous pouvez ajouter un nouveau Service Cloud avec la propriété DNS inverse spécifiée à l’aide de l’applet de commande « Set-AzureService » :

    PS C:\> New-AzureService –ServiceName “contosoapp1” –Location “West US” –Description “App1 with Reverse DNS” –ReverseDnsFqdn “contosoapp1.cloudapp.net.”

## <a name="view-reverse-dns-for-existing-cloud-services"></a>Affichage inversée DNS pour les Services en nuage existant
Vous pouvez afficher la valeur configurée pour un Service Cloud existant à l’aide de l’applet de commande « Get-AzureService » :

    PS C:\> Get-AzureService "contosoapp1"

## <a name="remove-reverse-dns-from-existing-cloud-services"></a>Supprimer DNS inverse de Services de Cloud existants
Vous pouvez supprimer une propriété DNS inverse d’un Service Cloud existant à l’aide de l’applet de commande « Set-AzureService ». Pour cela en définissant la valeur de propriété DNS inverse vide :

    PS C:\> Set-AzureService –ServiceName “contosoapp1” –Description “App1 with Reverse DNS” –ReverseDnsFqdn “”

[AZURE.INCLUDE [FAQ1](../../includes/dns-reverse-dns-record-operations-faq-host-own-arpa-zone-include.md)]

[AZURE.INCLUDE [FAQ2](../../includes/dns-reverse-dns-record-operations-faq-asm-include.md)]

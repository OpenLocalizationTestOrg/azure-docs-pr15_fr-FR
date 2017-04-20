<properties
   pageTitle="L’utilisation d’images de client Windows pour les scénarios de développement/test | Microsoft Azure"
   description="Comment utiliser les avantages de l’abonnement Visual Studio pour déployer Windows 7/8/10 dans Azure pour scénarios de développement/test"
   services="virtual-machines-windows"
   documentationCenter=""
   authors="iainfoulds"
   manager="timlt"
   editor=""/>

<tags
   ms.service="virtual-machines-windows"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-windows"
   ms.workload="infrastructure-services"
   ms.date="08/31/2016"
   ms.author="iainfou"/>

# <a name="using-windows-client-in-azure-for-devtest-scenarios"></a>À l’aide de Windows client dans Azure pour scénarios de développement/test

Vous pouvez utiliser Windows 7, Windows 8, ou Windows 10 dans Azure pour scénarios de développement/test condition que vous ayez un abonnement Visual Studio (anciennement MSDN) approprié. Cet article décrit les conditions d’éligibilité requises pour un client Windows en cours d’exécution dans Azure et l’utilisation des images Galerie Azure.


## <a name="subscription-eligibility"></a>Conditions d’éligibilité abonnement
Abonnés Visual Studio actives (personnes qui ont acquis une licence avec abonnement Visual Studio) peuvent utiliser Windows client pour le développement et à des fins de tests. Client Windows peut être utilisé dans votre propre matériel et Azure machines virtuelles en cours d’exécution dans n’importe quel type d’abonnement Azure. Client Windows ne peut pas être déployé sur ou utilisé dans Azure pour une utilisation normale de production ou par les personnes qui ne sont pas abonnés Visual Studio actifs.

Votre convenance, nous avons apporté certaines images Windows 10 disponibles dans la galerie Azure au sein de [l’offre de développement/test éligible](#eligible-offers). Les abonnés Studio visuels dans n’importe quel type d’offre peuvent également [adéquate préparer et créer](virtual-machines-windows-prepare-for-upload-vhd-image.md) une version 64 bits de Windows 7, Windows 8, ou image Windows 10, puis [télécharger dans Azure](virtual-machines-windows-upload-image.md). L’utilisation reste limitée à développement/test par les abonnés Visual Studio actives.


## <a name="eligible-offers"></a>Offres éligibles
Le tableau suivant répertorie l’ID de laquelle sont habilités à déployer Windows 10 via la galerie Azure offre. Les images Windows 10 sont uniquement visibles par les offres suivantes. Les abonnés Visual Studio ayant besoin d’exécuter client Windows dans un type d’offre différents nécessitent [adéquate préparer et créer](virtual-machines-windows-prepare-for-upload-vhd-image.md) une image de Windows 7, Windows 8 ou Windows 10 de 64 bits et [puis télécharger dans Azure](virtual-machines-windows-upload-image.md).

| Nom de l’offre | Numéro de l’offre | Images de client disponibles |
|:-----------|:------------:|:-----------------------:|
| [Paiement développement/Test](https://azure.microsoft.com/offers/ms-azr-0023p/)                          | 0023P | Windows 10 |
| [Abonnés Visual Studio Enterprise (MPN)](https://azure.microsoft.com/offers/ms-azr-0029p/)      | 0029P | Windows 10 |
| [Abonnés Visual Studio Professional](https://azure.microsoft.com/offers/ms-azr-0059p/)          | 0059P | Windows 10 |
| [Abonnés Visual Studio Test Professional](https://azure.microsoft.com/offers/ms-azr-0060p/)     | 0060P | Windows 10 |
| [Visual Studio Premium avec MSDN (avantages)](https://azure.microsoft.com/offers/ms-azr-0061p/)       | 0061P | Windows 10 |
| [Abonnés Visual Studio Enterprise](https://azure.microsoft.com/offers/ms-azr-0063p/)            | 0063P | Windows 10 |
| [Abonnés Visual Studio Enterprise (BizSpark)](https://azure.microsoft.com/offers/ms-azr-0064p/) | 0064P | Windows 10 |
| [Entreprise Développement/Test](https://azure.microsoft.com/ofers/ms-azr-0148p/)                              | 0148P | Windows 10 |


## <a name="check-your-azure-subscription"></a>Vérifier votre abonnement Azure
Si vous ne connaissez pas votre ID d’offre, vous pouvez l’obtenir via le portail Azure ou du portail du compte.

L’ID de l’offre d’abonnement est signalé dans la carte « Abonnements » au sein du portail Azure :

![Détails de l’ID de l’offre à partir du portail Azure](./media/virtual-machines-windows-client-images/offer_id_azure_portal.png) 

Vous pouvez également afficher l’ID offre à partir de l' [onglet « Abonnements »](http://account.windowsazure.com/Subscriptions) du portail compte Azure :

![Détails de l’ID de l’offre à partir du portail de compte Azure](./media/virtual-machines-windows-client-images/offer_id_azure_account_portal.png) 


## <a name="next-steps"></a>Étapes suivantes
Vous pouvez désormais déployer vos ordinateurs virtuels à l’aide de [PowerShell](virtual-machines-windows-ps-create.md), les [modèles de gestionnaire de ressources](virtual-machines-windows-ps-template.md)ou [Visual Studio](../vs-azure-tools-resource-groups-deployment-projects-create-deploy.md).

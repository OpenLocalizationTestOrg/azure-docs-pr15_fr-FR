<properties
   pageTitle="Appliquer le chiffrement des disques dans le centre de sécurité Azure | Microsoft Azure"
   description="Ce document vous montre comment mettre en œuvre la recommandation centre de sécurité Azure **Appliquer le chiffrement des disques**."
   services="security-center"
   documentationCenter="na"
   authors="TerryLanfear"
   manager="MBaldwin"
   editor=""/>

<tags
   ms.service="security-center"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="07/29/2016"
   ms.author="terrylan"/>

# <a name="apply-disk-encryption-in-azure-security-center"></a>Appliquer le chiffrement des disques dans le centre de sécurité Azure

Centre de sécurité Azure recommande appliquer le chiffrement des disques si vous avez Windows ou Linux VM disques qui ne sont pas chiffrés à l’aide de chiffrement des disques Azure. Chiffrement de disque vous permet de chiffrer vos disques Windows et Linux IaaS machine virtuelle.  Chiffrement est recommandé pour le système d’exploitation et les données des volumes sur votre ordinateur virtuel.


Chiffrement de disque s’appuie sur la fonctionnalité [BitLocker](https://technet.microsoft.com/library/cc732774.aspx) standard du secteur de Windows et la fonctionnalité [DM Crypt](https://en.wikipedia.org/wiki/Dm-crypt) de Linux pour fournir le chiffrement des données et OS pour protéger et protéger vos données et répondre à vos engagements de sécurité et conformité d’organisation. Chiffrement de disque est intégré à [L’archivage sécurisé de clé Azure](https://azure.microsoft.com/documentation/services/key-vault/) pour vous aider à contrôler et de gérer les clés de chiffrement de disque et secrets dans votre abonnement de l’archivage sécurisé clé, tout en garantissant que toutes les données dans les disques machine virtuelle sont chiffrées au reste dans le [Stockage Azure](https://azure.microsoft.com/documentation/services/storage/).

> [AZURE.NOTE] Chiffrement de disque Azure est pris en charge sur les Windows server systèmes d’exploitation suivants - Windows Server 2008 R2, Windows Server 2012 et Windows Server 2012 R2. Chiffrement de disque est pris en charge la Linux server systèmes d’exploitation suivants - Ubuntu, CentOS, SUSE et SUSE Linux Enterprise Server (SLES).

## <a name="implement-the-recommendation"></a>Mettre en œuvre la recommandation

1. Dans la carte de **recommandations** , sélectionnez **Appliquer le chiffrement des disques**.
2. Dans la carte **Appliquer le chiffrement des disques** , vous verrez une liste des ordinateurs virtuels pour lesquels le chiffrement des disques est recommandé.
3. Suivez les instructions pour appliquer le chiffrement à ces ordinateurs virtuels.

![][1]

Pour chiffrer les Machines virtuelles Azure qui ont été identifiée par le centre de sécurité comme nécessitant le chiffrement, nous vous recommandons les opérations suivantes :

- Installez et configurez Azure PowerShell. Cela vous permettra d’exécuter les commandes PowerShell nécessaires pour paramétrer les conditions préalables requises pour chiffrer Machines virtuelles Azure.
- Obtenir et exécuter le script Azure disque chiffrement conditions préalables Azure PowerShell.
- Chiffrer vos machines virtuelles.

[Chiffrer une Machine virtuelle Azure](security-center-disk-encryption.md) vous guidera tout au long de ces étapes.  Cette rubrique suppose que vous utilisez Windows 10 en tant que l’ordinateur client à partir de laquelle vous allez configurer le chiffrement des disques.

Il existe de nombreuses approches qui peuvent être utilisés pour configurer les conditions préalables et configurer le chiffrement pour le Machines virtuelles Azure. Si vous êtes déjà expérimentés dans Azure PowerShell ou Azure infrastructure du langage commun, vous pouvez utiliser les autres approches. Pour en savoir plus sur ces autres approches voir [le chiffrement des disques Azure](../security/azure-security-disk-encryption.md).



## <a name="see-also"></a>Voir aussi

Ce document vous montrer comment mettre en œuvre la recommandation centre de sécurité « Appliquer le chiffrement des disques ». Pour en savoir plus sur le chiffrement des disques, voir les rubriques suivantes :

- [Chiffrement et gestion des clés avec l’archivage sécurisé de clé Azure](https://azure.microsoft.com/documentation/videos/azurecon-2015-encryption-and-key-management-with-azure-key-vault/) (vidéo, 36 min 39 sec) : Découvrez comment utiliser la gestion de chiffrement de disque pour machines virtuelles IaaS et l’archivage sécurisé de clé Azure pour protéger et protéger vos données.
- [Chiffrer une Machine virtuelle Azure](security-center-disk-encryption.md) (document--) Découvrez comment chiffrer Machines virtuelles Azure.
- [Chiffrement de disque Azure](../security/azure-security-disk-encryption.md) (document)--Apprenez à activer le chiffrement de disque pour Windows et machines virtuelles Linux.

Pour en savoir plus sur le centre de sécurité, voir les rubriques suivantes :

- [Définition de stratégies de sécurité dans le centre de sécurité Azure](security-center-policies.md) , découvrez comment configurer des stratégies de sécurité.
- [Analyse du fonctionnement de la sécurité dans le centre de sécurité Azure](security-center-monitoring.md) , découvrez comment contrôler l’état de vos ressources Azure.
- [Gérer et répondre aux alertes de sécurité dans le centre de sécurité Azure](security-center-managing-and-responding-alerts.md) --apprendre à gérer et répondre aux alertes de sécurité.
- [Gestion des recommandations de sécurité dans le centre de sécurité Azure](security-center-recommendations.md) , découvrez comment les recommandations vous aident à protéger vos ressources Azure.
- [Forum aux questions sur Centre de sécurité azure](security-center-faq.md) --rechercher Forum aux questions sur l’utilisation du service.
- Billets de [blog sur la sécurité azure](http://blogs.msdn.com/b/azuresecurity/) --rechercher blog sur la conformité et la sécurité Azure.



<!--Image references-->
[1]: ./media/security-center-apply-disk-encryption/apply-disk-encryption.png

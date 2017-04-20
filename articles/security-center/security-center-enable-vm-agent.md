<properties
   pageTitle="Activer l’Agent machine virtuelle dans le centre de sécurité Azure | Microsoft Azure"
   description="Ce document vous montre comment mettre en œuvre la recommandation centre de sécurité Azure **Activer l’Agent de machine virtuelle**."
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
   ms.date="10/17/2016"
   ms.author="terrylan"/>

# <a name="enable-vm-agent-in-azure-security-center"></a>Activer l’Agent machine virtuelle dans le centre de sécurité Azure

L’Agent de machine virtuelle doit être installé sur des machines virtuelles (machines virtuelles) dans l’ordre pour [Activer la collecte de données](security-center-enable-data-collection.md).  Centre de sécurité Azure permet vous permet de voir quels ordinateurs virtuels nécessitent l’Agent machine virtuelle et recommandons d’activer l’Agent machine virtuelle sur ces machines virtuelles.

L’Agent de machine virtuelle est installé par défaut pour les machines virtuelles qui sont déployés à partir de la Azure Marketplace. L’article [Agent machine virtuelle et Extensions-partie 2](https://azure.microsoft.com/blog/vm-agent-and-extensions-part-2/) fournit des informations sur l’installation de l’Agent de machine virtuelle.


> [AZURE.NOTE] Ce document présente le service à l’aide d’un exemple de déploiement. Il s’agit pas d’un guide étape par étape.

## <a name="implement-the-recommendation"></a>Mettre en œuvre la recommandation

1. Dans la **carte de recommandations**, sélectionnez **Activer l’Agent de machine virtuelle**.
![Activer l’Agent machine virtuelle][1]

2. Cette action ouvre la carte **Machine virtuelle Agent est manquant ou ne répond pas**. Cette carte répertorie les ordinateurs virtuels nécessitant l’Agent machine virtuelle. Suivez les instructions de la carte pour installer l’agent machine virtuelle.
![Agent de machine virtuelle est manquant][2]

## <a name="see-also"></a>Voir aussi

Pour en savoir plus sur le centre de sécurité, voir les rubriques suivantes :

- [Définition de stratégies de sécurité dans le centre de sécurité Azure](security-center-policies.md)--Apprenez à configurer des stratégies de sécurité pour vos abonnements Azure et les groupes de ressources.
- [Gestion des recommandations de sécurité dans le centre de sécurité Azure](security-center-recommendations.md), découvrez comment les recommandations vous aident à protéger vos ressources Azure.
- [Contrôle dans le centre de sécurité Azure d’état sécurité](security-center-monitoring.md): Découvrez comment contrôler l’état de vos ressources Azure.
- [Gérer et répondre aux alertes de sécurité dans le centre de sécurité Azure](security-center-managing-and-responding-alerts.md)--apprendre à gérer et répondre aux alertes de sécurité.
- [Solutions des partenaires surveillance avec le centre de sécurité Azure](security-center-partner-solutions.md) , découvrez comment contrôler l’état d’intégrité de votre solutions des partenaires.
- [Forum aux questions sur Centre de sécurité azure](security-center-faq.md)--rechercher Forum aux questions sur l’utilisation du service.
- [Blog sur la sécurité azure](http://blogs.msdn.com/b/azuresecurity/)--obtenir les dernières informations de sécurité Azure et des informations.

<!--Image references-->
[1]: ./media/security-center-enable-vm-agent/enable-vm-agent.png
[2]: ./media/security-center-enable-vm-agent/vm-agent-is-missing.png

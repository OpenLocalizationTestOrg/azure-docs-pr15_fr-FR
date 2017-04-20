<properties
   pageTitle="Fournir des détails de contact de sécurité dans le centre de sécurité Azure | Microsoft Azure"
   description="Ce document vous montre comment fournir des détails de contact de sécurité dans le centre de sécurité Azure."
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

# <a name="provide-security-contact-details-in-azure-security-center"></a>Fournir des détails de contact de sécurité dans le centre de sécurité Azure

Centre de sécurité Azure recommande fournir des détails de contact de sécurité pour votre abonnement Azure si vous n’avez pas déjà. Ces informations servira par Microsoft pour vous contacter si le centre de réponse sécurité Microsoft (MSRC) découvre que vos données client a été utilisées par un tiers illégal ou non autorisé. MSRC effectue une analyse sélectionnez sécurité de l’infrastructure et Azure réseau et reçoit des plaintes intelligence et abuse menace provenant de tiers.

Message électronique de notification est envoyée à la première occurrence quotidienne d’une alerte et uniquement pour les alertes de gravité élevée. Préférences de courrier électronique peuvent uniquement être configurés pour les stratégies d’abonnement. Groupes de ressources au sein d’un abonnement hériteront ces paramètres.

> [AZURE.NOTE] Ce document présente le service à l’aide d’un exemple de déploiement.  Il s’agit pas d’un guide étape par étape.

## <a name="implement-the-recommendation"></a>Mettre en œuvre la recommandation

1. Dans la carte de **recommandations** , sélectionnez **fournir une sécurité détails du contact**.
![Fournir des contacts de sécurité][1]

2. Cette action ouvre la carte **fournir une sécurité détails du contact**. Sélectionnez l’abonnement Azure fournir des informations de contact sur.
![Fournir des détails de contact de sécurité][2]

3. Une deuxième carte de **fournir des détails de contact de sécurité** s’ouvre.

  - Entrez l’adresse de messagerie de sécurité ou les adresses séparées par des virgules. Il n’est pas une limite au nombre d’adresses de messagerie que vous pouvez entrer.
  - Entrez un numéro de téléphone international de sécurité.
  - Pour recevoir des messages électroniques à propos des alertes de gravité élevée, activez l’option **M’envoyer par e-mail sur les alertes**.
  - À l’avenir, vous devrez la possibilité d’envoyer des notifications par courrier électronique aux propriétaires de l’abonnement. Cette option est actuellement grisée.
  - Sélectionnez **OK** pour appliquer les informations de contact de sécurité à votre abonnement.

## <a name="see-also"></a>Voir aussi

Pour en savoir plus sur le centre de sécurité, voir les rubriques suivantes :

- [Définition de stratégies de sécurité dans le centre de sécurité Azure](security-center-policies.md) --Apprenez à configurer des stratégies de sécurité pour vos abonnements Azure et les groupes de ressources.
- [Gestion des recommandations de sécurité dans le centre de sécurité Azure](security-center-recommendations.md) , découvrez comment les recommandations vous aident à protéger vos ressources Azure.
- [Analyse du fonctionnement de la sécurité dans le centre de sécurité Azure](security-center-monitoring.md) , découvrez comment contrôler l’état de vos ressources Azure.
- [Gérer et répondre aux alertes de sécurité dans le centre de sécurité Azure](security-center-managing-and-responding-alerts.md) --apprendre à gérer et répondre aux alertes de sécurité.
- [Solutions des partenaires surveillance avec le centre de sécurité Azure](security-center-partner-solutions.md) , découvrez comment contrôler l’état d’intégrité de votre solutions des partenaires.
- [Forum aux questions sur Centre de sécurité azure](security-center-faq.md) --rechercher Forum aux questions sur l’utilisation du service.
- [Blog sur la sécurité azure](http://blogs.msdn.com/b/azuresecurity/) --obtenir les dernières informations de sécurité Azure et des informations.

<!--Image references-->
[1]: ./media/security-center-provide-security-contacts/provide-contacts.png
[2]:./media/security-center-provide-security-contacts/provide-contact-details.png

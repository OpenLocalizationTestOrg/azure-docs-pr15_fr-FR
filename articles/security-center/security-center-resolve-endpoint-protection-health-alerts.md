<properties
   pageTitle="Résoudre les alertes de fonctionnement de la protection de point de terminaison dans le centre de sécurité Azure | Microsoft Azure"
   description="Ce document vous montre comment mettre en œuvre la recommandation centre de sécurité Azure **alertes de fonctionnement résoudre Endpoint Protection**."
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

# <a name="resolve-endpoint-protection-health-alerts-in-azure-security-center"></a>Résoudre les alertes de fonctionnement de la protection de point de terminaison dans le centre de sécurité Azure

Centre de sécurité Azure recommandons de résoudre détecté endpoint protection santé alertes.  Centre de sécurité permet de voir quels ordinateurs virtuels (machines virtuelles) disposent des défaillances de protection de point de terminaison et combien.

> [AZURE.NOTE] Ce document présente le service à l’aide d’un exemple de déploiement. Il s’agit pas d’un guide étape par étape.

## <a name="implement-the-recommendation"></a>Mettre en œuvre la recommandation

1. Dans la **carte de recommandations**, sélectionnez **résoudre Endpoint Protection santé alertes**.
![Résoudre les alertes de fonctionnement de la protection de point de terminaison][1]

2. Cette action ouvre la carte **Endpoint Protection échec** qui répertorie des machines virtuelles avec échecs et le nombre d’échecs pour chaque machine virtuelle. Sélectionnez une machine virtuelle dans la liste.
![Échec de la protection de point de terminaison][2]

3. Une carte de la **Liste des échecs** s’ouvre pour la machine virtuelle sélectionnée, affichant une liste des échecs. Sélectionnez une défaillance dans la liste pour en savoir plus. Cette action ouvre une carte avec les informations relatives à l’échec sélectionné.
![Liste des échecs][3]
  ![événement Échec][4]

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
[1]: ./media/security-center-resolve-endpoint-protection/resolve-endpoint-protection.png
[2]: ./media/security-center-resolve-endpoint-protection/endpoint-protection-failure.png
[3]: ./media/security-center-resolve-endpoint-protection/failure-list.png
[4]: ./media/security-center-resolve-endpoint-protection/failure-event.png

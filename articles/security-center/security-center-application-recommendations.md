<properties
   pageTitle="Protection de vos applications dans le centre de sécurité Azure | Microsoft Azure"
   description="Ce document répond recommandations dans le centre de sécurité Azure pour vous aider à protègent vos applications Azure et restent dans le respect des stratégies de sécurité."
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
   ms.date="08/04/2016"
   ms.author="terrylan"/>

# <a name="protecting-your-applications-in-azure-security-center"></a>Protection de vos applications dans le centre de sécurité Azure

Centre de sécurité Azure analyse l’état de sécurité de vos ressources Azure. Lorsque le centre de sécurité identifie les problèmes de sécurité potentielles, qu’il crée des recommandations qui vous guident dans le processus de configuration des contrôles nécessaires.  Recommandations s’appliquent aux types de ressources Azure : machines virtuelles (machines virtuelles), mise en réseau, SQL et les applications.

Cet article aborde les recommandations qui s’appliquent aux applications.  Centre de recommandations application autour de déploiement d’un pare-feu d’application web.  Utilisez le tableau ci-dessous comme référence pour vous aider à comprendre les recommandations application disponible et que chacun d’eux faire si vous appliquez.

## <a name="available-application-recommendations"></a>Recommandations d’application disponible

|Recommandation|Description|
|-----|-----|
|[Ajouter un pare-feu d’application web](security-center-add-web-application-firewall.md)|Recommande de déployer un pare-feu d’application web (WAF) pour les points de terminaison web. Vous pouvez protéger plusieurs applications web dans le centre de sécurité en ajoutant ces applications à votre des déploiements WAF existants. Appareils WAF (créés avec le modèle de déploiement du Gestionnaire de ressources) doivent être déployés à un réseau virtuel distinct. Appareils WAF (créés avec le modèle de déploiement classique) sont limités à l’aide d’un groupe de sécurité réseau. Cette prise en charge sera étendu à un déploiement entièrement personnalisé d’un appareil WAF (classique) à l’avenir.|
|[Finalisation de la protection d’application](security-center-add-web-application-firewall.md#finalize-application-protection)|Pour terminer la configuration d’un WAF, le trafic doit être redirigé vers l’application WAF. Suivre cette recommandation doivent effectuer les modifications de configuration nécessaires.|

## <a name="see-also"></a>Voir aussi

Pour en savoir plus sur les recommandations qui s’appliquent à d’autres types de ressources Azure, voir les rubriques suivantes :

- [Protection de vos machines virtuelles dans le centre de sécurité Azure](security-center-virtual-machine-recommendations.md)
- [Protection de votre réseau dans le centre de sécurité Azure](security-center-network-recommendations.md)
- [Protection de votre service SQL Azure dans le centre de sécurité Azure](security-center-sql-service-recommendations.md)

Pour en savoir plus sur le centre de sécurité, voir les rubriques suivantes :

- [Définition de stratégies de sécurité dans le centre de sécurité Azure](security-center-policies.md) --Apprenez à configurer des stratégies de sécurité pour vos abonnements Azure et les groupes de ressources.
- [Gérer et répondre aux alertes de sécurité dans le centre de sécurité Azure](security-center-managing-and-responding-alerts.md) --apprendre à gérer et répondre aux alertes de sécurité.
- [Forum aux questions sur Centre de sécurité azure](security-center-faq.md) --rechercher Forum aux questions sur l’utilisation du service.

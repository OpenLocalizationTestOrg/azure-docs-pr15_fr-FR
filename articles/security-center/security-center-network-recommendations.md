<properties
   pageTitle="Protection de votre réseau dans le centre de sécurité Azure | Microsoft Azure"
   description="Ce document répond recommandations dans le centre de sécurité Azure pour vous aider à protéger votre réseau Azure et restent dans le respect des stratégies de sécurité."
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

# <a name="protecting-your-network-in-azure-security-center"></a>Protection de votre réseau dans le centre de sécurité Azure

Centre de sécurité Azure analyse l’état de sécurité de vos ressources Azure. Lorsque le centre de sécurité identifie les problèmes de sécurité potentielles, qu’il crée des recommandations qui vous guident dans le processus de configuration des contrôles nécessaires.  Recommandations s’appliquent aux types de ressources Azure : machines virtuelles (machines virtuelles), mise en réseau, SQL et les applications.

Cet article aborde les recommandations qui s’appliquent à votre réseau.  Centre de recommandations réseau autour des pare-feux de prochaine génération, groupes de sécurité réseau, les règles de trafic entrant configuration et plus.  Utilisez le tableau ci-dessous comme référence pour vous aider à comprendre les recommandations réseau disponible et que chacun d’eux faire si vous appliquez.

## <a name="available-network-recommendations"></a>Recommandations réseau disponibles

|Recommandation|Description|
|-----|-----|
|[Ajouter un pare-feu de génération suivant](security-center-add-next-generation-firewall.md)|Recommande que vous ajoutiez un pare-feu prochaine génération (Conviction) d’un partenaire Microsoft pour augmenter la protection de votre sécurité.|
|[Acheminer le trafic via Conviction uniquement](security-center-add-next-generation-firewall.md#route-traffic-through-ngfw-only)|Recommande de configurer les règles de groupe (NSG) de sécurité réseau qui forcer le trafic entrant sur votre ordinateur virtuel via votre Conviction.|
|[Activer les groupes de sécurité réseau sur sous-réseaux ou machines virtuelles](security-center-enable-network-security-groups.md)|Recommande d’activer NSGs sur sous-réseaux ou machines virtuelles.|
|[Limiter l’accès à Internet les en regard de point de terminaison](security-center-restrict-access-through-internet-facing-endpoints.md)|Recommande de configurer les règles de trafic entrant pour NSGs.|

## <a name="see-also"></a>Voir aussi

Pour en savoir plus sur les recommandations qui s’appliquent à d’autres types de ressources Azure, voir les rubriques suivantes :

- [Protection de vos machines virtuelles dans le centre de sécurité Azure](security-center-virtual-machine-recommendations.md)
- [Protection de vos applications dans le centre de sécurité Azure](security-center-application-recommendations.md)
- [Protection de votre service SQL Azure dans le centre de sécurité Azure](security-center-sql-service-recommendations.md)

Pour en savoir plus sur le centre de sécurité, voir les rubriques suivantes :

- [Définition de stratégies de sécurité dans le centre de sécurité Azure](security-center-policies.md) --Apprenez à configurer des stratégies de sécurité pour vos abonnements Azure et les groupes de ressources.
- [Gérer et répondre aux alertes de sécurité dans le centre de sécurité Azure](security-center-managing-and-responding-alerts.md) --apprendre à gérer et répondre aux alertes de sécurité.
- [Forum aux questions sur Centre de sécurité azure](security-center-faq.md) --rechercher Forum aux questions sur l’utilisation du service.

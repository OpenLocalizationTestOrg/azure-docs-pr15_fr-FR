<properties
   pageTitle="Protection de service SQL Azure dans le centre de sécurité Azure | Microsoft Azure"
   description="Ce document répond recommandations dans le centre de sécurité Azure pour vous aider à protéger le service SQL Azure et restent dans le respect des stratégies de sécurité."
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

# <a name="protecting-azure-sql-service-in-azure-security-center"></a>Protection de service SQL Azure dans le centre de sécurité Azure

Centre de sécurité Azure analyse l’état de sécurité de vos ressources Azure. Lorsque le centre de sécurité identifie les problèmes de sécurité potentielles, qu’il crée des recommandations qui vous guident dans le processus de configuration des contrôles nécessaires.  Recommandations s’appliquent aux types de ressources Azure : machines virtuelles (machines virtuelles), mise en réseau, SQL et les applications.

Cet article aborde les recommandations qui s’appliquent au service SQL Azure.  Centre recommandations de service SQL Azure autour de l’activation de l’audit pour les serveurs SQL Azure et bases de données et en activant le chiffrement des bases de données SQL.  Utilisez le tableau ci-dessous comme référence pour vous aider à comprendre les recommandations de service SQL disponibles et que chacun d’eux faire si vous appliquez.

## <a name="available-sql-service-recommendations"></a>Recommandations de service SQL disponibles

|Recommandation|Description|
|-----|-----|
|[Activer l’audit SQL de serveur](security-center-enable-auditing-on-sql-servers.md)|Recommande que vous activez l’audit pour les serveurs SQL Azure (service SQL Azure uniquement ; n’inclut pas SQL en cours d’exécution sur vos ordinateurs virtuels).|
|[Activer l’audit SQL de base de données](security-center-enable-auditing-on-sql-databases.md)|Recommande que vous activez l’audit pour les bases de données SQL Azure (service SQL Azure uniquement ; n’inclut pas SQL en cours d’exécution sur vos ordinateurs virtuels).|
|[Activer le chiffrement Transparent des données sur les bases de données SQL](security-center-enable-transparent-data-encryption.md)|Recommande l’activation de chiffrement des bases de données SQL (service SQL Azure uniquement).|

## <a name="see-also"></a>Voir aussi

Pour en savoir plus sur les recommandations qui s’appliquent à d’autres types de ressources Azure, voir les rubriques suivantes :

- [Protection de vos machines virtuelles dans le centre de sécurité Azure](security-center-virtual-machine-recommendations.md)
- [Protection de vos applications dans le centre de sécurité Azure](security-center-application-recommendations.md)
- [Protection de votre réseau dans le centre de sécurité Azure](security-center-network-recommendations.md)

Pour en savoir plus sur le centre de sécurité, voir les rubriques suivantes :

- [Définition de stratégies de sécurité dans le centre de sécurité Azure](security-center-policies.md) --Apprenez à configurer des stratégies de sécurité pour vos abonnements Azure et les groupes de ressources.
- [Gérer et répondre aux alertes de sécurité dans le centre de sécurité Azure](security-center-managing-and-responding-alerts.md) --apprendre à gérer et répondre aux alertes de sécurité.
- [Forum aux questions sur Centre de sécurité azure](security-center-faq.md) --rechercher Forum aux questions sur l’utilisation du service.

<properties
   pageTitle="Protection de vos machines virtuelles dans le centre de sécurité Azure | Microsoft Azure"
   description="Ce document répond recommandations dans le centre de sécurité Azure pour vous aider à protéger vos machines virtuelles et restent dans le respect des stratégies de sécurité."
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
   ms.date="09/25/2016"
   ms.author="terrylan"/>

# <a name="protecting-your-virtual-machines-in-azure-security-center"></a>Protection de vos machines virtuelles dans le centre de sécurité Azure

Centre de sécurité Azure analyse l’état de sécurité de vos ressources Azure. Lorsque le centre de sécurité identifie les problèmes de sécurité potentielles, qu’il crée des recommandations qui vous guident dans le processus de configuration des contrôles nécessaires.  Recommandations s’appliquent aux types de ressources Azure : machines virtuelles (machines virtuelles), mise en réseau, SQL et les applications.

Cet article aborde les recommandations qui s’appliquent aux machines virtuelles.  Centre de recommandations machine virtuelle autour de la collecte des données, appliquer des mises à jour du système, mise en service contre les logiciels malveillants, chiffrer vos disques machine virtuelle et bien plus encore.  Utilisez le tableau ci-dessous comme référence pour vous aider à comprendre les recommandations machine virtuelle disponibles et que chacun d’eux faire si vous appliquez.

## <a name="available-vm-recommendations"></a>Recommandations machine virtuelle disponibles

|Recommandation|Description|
|-----|-----|
|[Activer la collecte de données pour les abonnements](security-center-enable-data-collection.md)|Recommande que vous activez collecte de données dans la stratégie de sécurité pour chacun de vos abonnements et toutes les machines virtuelles (machines virtuelles) de vos abonnements.|
|[Intéressé du système d’exploitation](security-center-remediate-os-vulnerabilities.md)|Recommande que vous alignez vos configurations système d’exploitation avec les règles de configuration recommandée, par exemple, n’autorisent pas les mots de passe doit être enregistré.|
|[Appliquer des mises à jour système](security-center-apply-system-updates.md)|Recommande de déployer la sécurité du système manquants et mises à jour critiques aux machines virtuelles.|
|[Redémarrez après mise à jour du système](security-center-apply-system-updates.md#reboot-after-system-updates)|Vous conseille de redémarrer une machine virtuelle pour terminer le processus de l’application des mises à jour système.|
|[Installer la Protection du point de terminaison](security-center-install-endpoint-protection.md)|Recommande que vous devez configurer les programmes contre les logiciels malveillants machines virtuelles (machines virtuelles Windows uniquement).|
|[Résoudre les alertes de fonctionnement Endpoint Protection](security-center-resolve-endpoint-protection-health-alerts.md)|Recommande que vous résoudre les échecs de protection de point de terminaison.|
|[Activer l’Agent machine virtuelle](security-center-enable-vm-agent.md)|Permet de déterminer les machines virtuelles nécessitent l’Agent machine virtuelle. L’Agent machine virtuelle doit être installé sur machines virtuelles afin de prévoir le correctif analyse, référence d’analyse et programmes contre les logiciels malveillants. L’Agent de machine virtuelle est installé par défaut pour les machines virtuelles qui sont déployés à partir de la Azure Marketplace. L’article [Agent machine virtuelle et Extensions-partie 2](http://azure.microsoft.com/blog/2014/04/15/vm-agent-and-extensions-part-2/) fournit des informations sur l’installation de l’Agent de machine virtuelle.|
| [Appliquer le chiffrement disque](security-center-apply-disk-encryption.md) |Recommande que vous chiffrez vos disques machine virtuelle à l’aide de chiffrement de disque Azure (Windows et Linux machines virtuelles). Chiffrement est recommandé pour le système d’exploitation et les données des volumes sur votre ordinateur virtuel.|
| [Mettre à jour de la version du système d’exploitation](security-center-update-os-version.md) | Recommande mettre à jour la version du système d’exploitation (OS) votre service Cloud pour la version la plus récente disponible pour votre famille du système d’exploitation.  Pour en savoir plus sur les Services en nuage, consultez la [vue d’ensemble des Services Cloud](../cloud-services/cloud-services-choose-me.md). |
| [Évaluation des vulnérabilités ne pas installée](security-center-vulnerability-assessment-recommendations.md) | Recommande d’installer une solution d’évaluation vulnérabilité sur votre ordinateur virtuel. |
| [Intéressé](security-center-vulnerability-assessment-recommendations.md#review-recommendation) | Vous permet de voir système et application vulnérabilités détectées par la solution d’évaluation vulnérabilité installée sur votre ordinateur virtuel. |

## <a name="see-also"></a>Voir aussi

Pour en savoir plus sur les recommandations qui s’appliquent à d’autres types de ressources Azure, voir les rubriques suivantes :

- [Protection de vos applications dans le centre de sécurité Azure](security-center-application-recommendations.md)
- [Protection de votre réseau dans le centre de sécurité Azure](security-center-network-recommendations.md)
- [Protection de votre service SQL Azure dans le centre de sécurité Azure](security-center-sql-service-recommendations.md)

Pour en savoir plus sur le centre de sécurité, voir les rubriques suivantes :

- [Définition de stratégies de sécurité dans le centre de sécurité Azure](security-center-policies.md) --Apprenez à configurer des stratégies de sécurité pour vos abonnements Azure et les groupes de ressources.
- [Gérer et répondre aux alertes de sécurité dans le centre de sécurité Azure](security-center-managing-and-responding-alerts.md) --apprendre à gérer et répondre aux alertes de sécurité.
- [Forum aux questions sur Centre de sécurité azure](security-center-faq.md) --rechercher Forum aux questions sur l’utilisation du service.

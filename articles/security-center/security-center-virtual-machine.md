<properties
   pageTitle="Centre de sécurité Azure et Azure Machines virtuelles | Microsoft Azure"
   description="Ce document vous aide à comprendre comment le centre de sécurité Azure peuvent préserver Machines virtuelles Azure."
   services="security-center"
   documentationCenter="na"
   authors="YuriDio"
   manager="swadhwa"
   editor=""/>

<tags
   ms.service="security-center"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/07/2016"
   ms.author="yurid"/>

# <a name="azure-security-center-and-azure-virtual-machines"></a>Centre de sécurité Azure et Machines virtuelles Azure

[Centre de sécurité Azure](https://azure.microsoft.com/services/security-center/) vous permet d’empêcher, détecter et répondre aux menaces. Il assure la gestion de surveillance et la stratégie de sécurité intégrée sur vos abonnements Azure, permettant de détecter les menaces qui pourraient passer inaperçus et fonctionne avec une large réseau de solutions de sécurité.

Cet article vous explique comment le centre de sécurité peuvent vous aider à sécuriser votre Azure Machines virtuelles (machine virtuelle).

## <a name="why-use-security-center"></a>Pourquoi utiliser le centre de sécurité ?

Centre de sécurité vous aident à protéger les données de machine virtuelle dans Azure grâce à la visibilité des paramètres de sécurité de votre ordinateur virtuel. Lorsque le centre de sécurité protège vos ordinateurs virtuels, les fonctionnalités suivantes sont disponibles :

- Paramètres de sécurité de système d’exploitation (OS) avec les règles de configuration recommandée
- Sécurité du système et mises à jour critiques sont manquantes
- Recommandations de protection de point de terminaison
- Validation de chiffrement de disque
- Mise à jour et évaluation des vulnérabilités
- Détection des menaces

En plus de vous aider à protéger vos ordinateurs virtuels Azure, centre de sécurité fournit également le contrôle de la sécurité et gestion des Services en nuage, Services d’application, réseaux virtuels et plus. 

>[AZURE.NOTE] Voir [Introduction au centre de sécurité Azure](security-center-intro.md) en savoir plus sur le centre de sécurité Azure.

## <a name="prerequisites"></a>Conditions préalables

Pour commencer à utiliser le centre de sécurité Azure, vous devez connaître et posez-vous les questions suivantes :

- Vous devez posséder un abonnement à Microsoft Azure. Pour plus d’informations sur les niveaux gratuites et standards du centre de sécurité, voir [Tarifs de centre de sécurité](https://azure.microsoft.com/pricing/details/security-center/) .
- Planifier l’adoption de votre centre de sécurité, consultez le [guident de planification du centre de sécurité Azure et opérations](security-center-planning-and-operations-guide.md) pour en savoir plus sur les considérations relatives à la planification et opérations.
- Pour plus d’informations concernant la prise en charge du système d’exploitation, voir le [Centre de sécurité Azure Forum aux questions (FAQ)](security-center-faq.md). 

## <a name="set-security-policy"></a>Définir la stratégie de sécurité

Collecte de données doit être activée pour que ce centre de sécurité Azure peut recueillir les informations nécessaires fournir des recommandations et les alertes qui sont générés en fonction de la stratégie de sécurité que vous configurez. Dans l’illustration ci-dessous, vous pouvez voir que la **collecte de données** a été **activé**.

Une stratégie de sécurité définit le jeu de contrôles qui sont recommandés pour les ressources au sein de l’abonnement spécifié ou le groupe de ressources. Avant d’activer la stratégie de sécurité, vous devez disposer de collecte de données activée, centre de sécurité collecte des données à partir de vos machines virtuelles afin d’évaluer leur état de sécurité, fournissent des recommandations de sécurité et signale les menaces. Dans le centre de sécurité, vous définissez des stratégies pour vos abonnements Azure ou les groupes de ressources en fonction des besoins de sécurité de votre société et le type d’applications ou critère de diffusion des données dans chaque abonnement. 

![Stratégie de sécurité](./media/security-center-virtual-machine/security-center-virtual-machine-fig1.png)

>[AZURE.NOTE] Pour en savoir plus sur chaque **stratégie de prévention** disponible, voir l’article [définir des stratégies de sécurité](security-center-policies.md) .

## <a name="manage-security-recommendations"></a>Gérer les recommandations de sécurité

Centre de sécurité analyse l’état de sécurité de vos ressources Azure. Lorsque le centre de sécurité identifie les problèmes de sécurité potentielles, qu’il crée des recommandations. Les recommandations vous guident dans le processus de configuration des contrôles nécessaires.

Après avoir défini une stratégie de sécurité, centre de sécurité analyse l’état de sécurité de vos ressources pour identifier les faiblesses potentielles. Les recommandations sont affichées dans un format de tableau où chaque ligne représente une seule recommandation particulière. Le tableau suivant fournit des exemples de recommandations pour les ordinateurs virtuels Azure et que chacun d’eux faire si vous appliquez. Lorsque vous sélectionnez une recommandation, vous recevrez les informations qui vous montre comment mettre en œuvre la recommandation dans le centre de sécurité.

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
| [Évaluation des vulnérabilités ne pas installée](security-center-vulnerability-assessment-recommendations.md) | Recommande d’installer une solution d’évaluation vulnérabilité sur votre ordinateur virtuel. |
| [Intéressé](security-center-vulnerability-assessment-recommendations.md#review-recommendation) | Vous permet de voir système et application vulnérabilités détectées par la solution d’évaluation vulnérabilité installée sur votre ordinateur virtuel. |

>[AZURE.NOTE] Pour en savoir plus sur les recommandations, voir l’article de la [Gestion des recommandations](security-center-recommendations.md) .

## <a name="monitor-security-health"></a>Surveiller l’intégrité du sécurité

Après avoir activé [les stratégies de sécurité](security-center-policies.md) pour les ressources d’un abonnement, le centre de sécurité analysera la sécurité de vos ressources pour identifier les faiblesses potentielles.  Vous pouvez afficher l’état de sécurité de vos ressources, ainsi que les éventuels problèmes relatifs à la carte **santé sécurité des ressources** . Lorsque vous cliquez sur **machines virtuelles** dans la vignette au niveau de **sécurité des ressources** , la carte **machines virtuelles** s’ouvre avec les recommandations pour vos ordinateurs virtuels. 

![Au niveau de sécurité](./media/security-center-virtual-machine/security-center-virtual-machine-fig2.png)

## <a name="manage-and-respond-to-security-alerts"></a>Gérer et répondre aux alertes de sécurité

Centre de sécurité automatiquement collecte, analyse et intègre les données du journal à partir de vos ressources Azure, le réseau et les solutions des partenaires connectés (par exemple, pare-feu et endpoint protection solutions), pour détecter les menaces réels et réduire les fausses. Grâce à une agrégation centralisée des [fonctionnalités Détection](security-center-detection-capabilities.md), centre de sécurité est en mesure de générer des alertes de sécurité par ordre de priorité pour vous aider à déterminer l’origine du problème et fournir des recommandations pour savoir comment mettre à jour les attaques possibles.

![Alertes de sécurité](./media/security-center-virtual-machine/security-center-virtual-machine-fig3.png)

Sélectionnez une alerte de sécurité pour en savoir plus sur les événements qui a déclenché l’alerte et quoi, si un, étapes que vous devez suivre pour convertir une attaque. Alertes de sécurité sont regroupées par [type](security-center-alerts-type.md) et la date.


## <a name="see-also"></a>Voir aussi

Pour en savoir plus sur le centre de sécurité, voir les rubriques suivantes :

- [Définition de stratégies de sécurité dans le centre de sécurité Azure](security-center-policies.md) --Apprenez à configurer des stratégies de sécurité pour vos abonnements Azure et les groupes de ressources.
- [Gérer et répondre aux alertes de sécurité dans le centre de sécurité Azure](security-center-managing-and-responding-alerts.md) --apprendre à gérer et répondre aux alertes de sécurité.
- [Forum aux questions sur Centre de sécurité azure](security-center-faq.md) --rechercher Forum aux questions sur l’utilisation du service.

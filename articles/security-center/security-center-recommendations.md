<properties
   pageTitle="Gestion des recommandations de sécurité dans le centre de sécurité Azure | Microsoft Azure"
   description="Ce document vous guide tout au long de recommandations dans le centre de sécurité Azure comment vous aident à protéger vos ressources Azure et rester dans le respect des stratégies de sécurité."
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

# <a name="managing-security-recommendations-in-azure-security-center"></a>Gestion des recommandations en matière de sécurité dans le centre de sécurité Azure

Ce document vous explique comment utiliser les recommandations dans le centre de sécurité Azure pour vous aider à protéger vos ressources Azure.

> [AZURE.NOTE] Ce document présente le service à l’aide d’un exemple de déploiement.  Il s’agit pas d’un guide étape par étape.

## <a name="what-are-security-recommendations"></a>Quelles sont les recommandations de sécurité ?
Centre de sécurité analyse régulièrement l’état de sécurité de vos ressources Azure. Lorsque le centre de sécurité identifie les problèmes de sécurité potentielles, qu’il crée des recommandations. Les recommandations vous guident dans le processus de configuration des contrôles nécessaires.

## <a name="implementing-security-recommendations"></a>Mettre en œuvre des recommandations de sécurité

### <a name="set-recommendations"></a>Jeu de recommandations

Dans la [définition des stratégies de sécurité dans le centre de sécurité Azure](security-center-policies.md), vous apprenez à :

- Configurer des stratégies de sécurité.
- Activer la collecte de données.
- Choisissez les recommandations pour afficher dans le cadre de votre stratégie de sécurité.

Stratégie recommandations poste actuel autour des mises à jour système, des règles de référence, des programmes contre les logiciels malveillants, [groupes de sécurité réseau](../virtual-network/virtual-networks-nsg.md) sur sous-réseaux et interfaces réseau, SQL de base de données d’audit, chiffrement transparent des données de base de données SQL et pare-feu d’application web.  [Définition de stratégies de sécurité](security-center-policies.md) fournit une description de chaque option recommandations.

### <a name="monitor-recommendations"></a>Recommandations moniteur
Après avoir défini une stratégie de sécurité, centre de sécurité analyse l’état de sécurité de vos ressources pour identifier les faiblesses potentielles. La vignette de **recommandations** sur la carte du **Centre de sécurité** vous permet de déterminer le nombre total de recommandations identifiée par le centre de sécurité.

![Vignette de recommandations][1]

Pour afficher les détails de chaque recommandation :

1. Cliquez sur la **vignette des recommandations** sur la carte du **Centre de sécurité** . La carte de **recommandations** s’ouvre.

Les recommandations sont affichées dans un format de tableau où chaque ligne représente une seule recommandation particulière. Les colonnes de cette table sont :

- **DESCRIPTION**: explique la recommandation et ce qui doit être effectuée pour y remédier.
- **Ressource**: répertorie les ressources à laquelle s’applique cette recommandation.
- **État**: décrit l’état actuel de la recommandation :
    - **Ouvrir**: la recommandation n’a pas encore été adressée.
    - **En cours**: la recommandation actuellement appliquée aux ressources et aucune action n’est requise par vous.
    - **Résolu**: la recommandation a déjà été effectuée (dans ce cas, la ligne n’est pas disponible).
- **Gravité**: décrit la gravité de cette recommandation spécifique :
    - **Haute**: un problème existe avec une ressource significative (par exemple, une application, une machine virtuelle ou un groupe de sécurité réseau) et nécessite une attention.
    - **Support**: il existe un problème et non critiques ou autres étapes sont nécessaires pour éliminer ou pour effectuer un processus.
    - **Faible**: une faille qui doivent être traités, mais ne nécessite pas une attention immédiate. (Par défaut, recommandations faibles ne sont pas présentées, mais vous pouvez filtrer sur les recommandations faibles si vous souhaitez les voir.)

Utilisez le tableau ci-dessous comme référence pour vous aider à comprendre les recommandations disponibles et que chacun d’eux faire si vous appliquez.

> [AZURE.NOTE] Vous devez comprendre les [classique et modèles de déploiement Gestionnaire de ressources](../azure-classic-rm.md) pour les ressources Azure.

|Recommandation|Description|
|-----|-----|
|[Activer la collecte de données pour les abonnements](security-center-enable-data-collection.md)|Recommande que vous activez collecte de données dans la stratégie de sécurité pour chacun de vos abonnements et toutes les machines virtuelles (machines virtuelles) de vos abonnements.|
|[Intéressé du système d’exploitation](security-center-remediate-os-vulnerabilities.md)|Recommande que vous alignez vos configurations système d’exploitation avec les règles de configuration recommandée, par exemple, n’autorisent pas les mots de passe doit être enregistré.|
|[Appliquer des mises à jour système](security-center-apply-system-updates.md)|Recommande de déployer la sécurité du système manquants et mises à jour critiques aux machines virtuelles.|
|[Redémarrez après mise à jour du système](security-center-apply-system-updates.md#reboot-after-system-updates)|Vous conseille de redémarrer une machine virtuelle pour terminer le processus de l’application des mises à jour système.|
|[Ajouter un pare-feu d’application web](security-center-add-web-application-firewall.md)|Recommande de déployer un pare-feu d’application web (WAF) pour les points de terminaison web. Vous pouvez protéger plusieurs applications web dans le centre de sécurité en ajoutant ces applications à votre des déploiements WAF existants. Appareils WAF (créés avec le modèle de déploiement du Gestionnaire de ressources) doivent être déployés à un réseau virtuel distinct. Appareils WAF (créés avec le modèle de déploiement classique) sont limités à l’aide d’un groupe de sécurité réseau. Cette prise en charge sera étendu à un déploiement entièrement personnalisé d’un appareil WAF (classique) à l’avenir. Centre de sécurité recommande que vous devez configurer un WAF pour contribuer à protéger contre les attaques de ciblage de vos applications web sur machines virtuelles et sur application Service environnement (ASE). Pour en savoir plus sur ASE, consultez la [Documentation environnement du Service d’application](../app-service/app-service-app-service-environments-readme.md). |
|[Finalisation de la protection d’application](security-center-add-web-application-firewall.md#finalize-application-protection)|Pour terminer la configuration d’un WAF, le trafic doit être redirigé vers l’application WAF. Suivre cette recommandation doivent effectuer les modifications de configuration nécessaires.|
|[Ajouter un pare-feu de génération suivant](security-center-add-next-generation-firewall.md)|Recommande que vous ajoutiez un pare-feu prochaine génération (Conviction) d’un partenaire Microsoft pour augmenter la protection de votre sécurité.|
|[Acheminer le trafic via Conviction uniquement](security-center-add-next-generation-firewall.md#route-traffic-through-ngfw-only)|Recommande de configurer les règles de groupe (NSG) de sécurité réseau qui forcer le trafic entrant sur votre ordinateur virtuel via votre Conviction.|
|[Installer la Protection du point de terminaison](security-center-install-endpoint-protection.md)|Recommande que vous devez configurer les programmes contre les logiciels malveillants machines virtuelles (machines virtuelles Windows uniquement).|
|[Résoudre les alertes de fonctionnement Endpoint Protection](security-center-resolve-endpoint-protection-health-alerts.md)|Recommande que vous résoudre les échecs de protection de point de terminaison.|
|[Activer les groupes de sécurité réseau sur sous-réseaux ou machines virtuelles](security-center-enable-network-security-groups.md)|Recommande d’activer NSGs sur sous-réseaux ou machines virtuelles.|
|[Limiter l’accès à Internet les en regard de point de terminaison](security-center-restrict-access-through-internet-facing-endpoints.md)|Recommande de configurer les règles de trafic entrant pour NSGs.|
|[Activer l’audit SQL de serveur](security-center-enable-auditing-on-sql-servers.md)|Recommande que vous activez l’audit pour les serveurs SQL Azure (service SQL Azure uniquement ; n’inclut pas SQL en cours d’exécution sur vos ordinateurs virtuels).|
|[Activer l’audit SQL de base de données](security-center-enable-auditing-on-sql-databases.md)|Recommande que vous activez l’audit pour les bases de données SQL Azure (service SQL Azure uniquement ; n’inclut pas SQL en cours d’exécution sur vos ordinateurs virtuels).|
|[Activer le chiffrement Transparent des données sur les bases de données SQL](security-center-enable-transparent-data-encryption.md)|Recommande l’activation de chiffrement des bases de données SQL (service SQL Azure uniquement).|
|[Activer l’Agent machine virtuelle](security-center-enable-vm-agent.md)|Permet de déterminer les machines virtuelles nécessitent l’Agent machine virtuelle. L’Agent machine virtuelle doit être installé sur machines virtuelles afin de prévoir le correctif analyse, référence d’analyse et programmes contre les logiciels malveillants. L’Agent de machine virtuelle est installé par défaut pour les machines virtuelles qui sont déployés à partir de la Azure Marketplace. L’article [Agent machine virtuelle et Extensions-partie 2](http://azure.microsoft.com/blog/2014/04/15/vm-agent-and-extensions-part-2/) fournit des informations sur l’installation de l’Agent de machine virtuelle.|
| [Appliquer le chiffrement disque](security-center-apply-disk-encryption.md) |Recommande que vous chiffrez vos disques machine virtuelle à l’aide de chiffrement de disque Azure (Windows et Linux machines virtuelles). Chiffrement est recommandé pour le système d’exploitation et les données des volumes sur votre ordinateur virtuel.|
|[Fournir des détails de contact de sécurité](security-center-provide-security-contact-details.md) | Informations de contact pour chacun de vos abonnements recommande que vous fournissez une sécurité. Informations de contact sont un messagerie adresse et numéro de téléphone. Les informations permettant de vous contacter si notre équipe de sécurité détecte que vos ressources sont compromis. |
| [Mettre à jour de la version du système d’exploitation](security-center-update-os-version.md) | Recommande mettre à jour la version du système d’exploitation (OS) votre service Cloud pour la version la plus récente disponible pour votre famille du système d’exploitation.  Pour en savoir plus sur les Services en nuage, consultez la [vue d’ensemble des Services Cloud](../cloud-services/cloud-services-choose-me.md). |
| [Évaluation des vulnérabilités ne pas installée](security-center-vulnerability-assessment-recommendations.md) | Recommande d’installer une solution d’évaluation vulnérabilité sur votre ordinateur virtuel. |
| [Intéressé](security-center-vulnerability-assessment-recommendations.md#review-recommendation) | Vous permet de voir système et application vulnérabilités détectées par la solution d’évaluation vulnérabilité installée sur votre ordinateur virtuel. |

Vous pouvez filtrer ou ignorer des recommandations.

1. Cliquez sur **filtre** dans la carte de **recommandations** . La carte de **filtre** s’ouvre et vous sélectionnez les valeurs de gravité et l’état que vous souhaitez afficher.

    ![Filtrer les recommandations][2]

2. Si vous estimez que recommandation n’est pas applicable, vous pouvez faire disparaître la recommandation et leur filtrage puis se déconnecter de votre affichage. Il existe deux façons pour ignorer une recommandation. Un moyen consiste à cliquer avec le bouton droit sur un élément et sélectionnez **faire disparaître**. L’autre consiste à pointez sur un élément, cliquez sur les points de suspension qui s’affichent à droite, puis sélectionnez **faire disparaître**. Vous pouvez afficher les recommandations ignorées en cliquant sur **filtre**, puis en sélectionnant **Dismissed**.

    ![Faire disparaître la zone recommandations][3]

### <a name="apply-recommendations"></a>Appliquer les recommandations
Après avoir examiné toutes les recommandations, choisissez celui que vous devez appliquer tout d’abord. Nous vous recommandons d’utiliser l’indice de gravité comme le paramètre principal pour évaluer les recommandations doit être tout d’abord appliqué.

Dans la table des recommandations ci-dessus, sélectionnez une recommandation et passez en revue en tant qu’illustre comment appliquer une recommandation.

## <a name="see-also"></a>Voir aussi
Dans ce document, vous avez abordé recommandations de sécurité dans le centre de sécurité. Pour en savoir plus sur le centre de sécurité, voir les rubriques suivantes :

- [Définition de stratégies de sécurité dans le centre de sécurité Azure](security-center-policies.md) , découvrez comment configurer des stratégies de sécurité pour vos abonnements Azure et les groupes de ressources.
- [Contrôle dans le centre de sécurité Azure d’état sécurité](security-center-monitoring.md) : Découvrez comment contrôler l’état de vos ressources Azure.
- [Gérer et répondre aux alertes de sécurité dans le centre de sécurité Azure](security-center-managing-and-responding-alerts.md) , découvrez comment gérer et répondre aux alertes de sécurité.
- [Surveillance solutions des partenaires avec le centre de sécurité Azure](security-center-partner-solutions.md) , découvrez comment contrôler l’état d’intégrité de votre solutions des partenaires.
- [Forum aux questions sur Centre de sécurité Azure](security-center-faq.md) : Forum aux questions sur l’utilisation du service de recherche.
- [Blog sur la sécurité Azure](http://blogs.msdn.com/b/azuresecurity/) : rechercher des billets de blog sur la sécurité Azure et de conformité.

<!--Image references-->
[1]: ./media/security-center-recommendations/recommendations-tile.png
[2]: ./media/security-center-recommendations/filter-recommendations.png
[3]: ./media/security-center-recommendations/dismiss-recommendations.png

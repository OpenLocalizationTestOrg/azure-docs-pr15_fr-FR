<properties
   pageTitle="Introduction au centre de sécurité Azure | Microsoft Azure"
   description="En savoir plus sur le centre de sécurité Azure, ses fonctionnalités clées et son fonctionnement."
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
   ms.date="07/21/2016"
   ms.author="terrylan"/>

# <a name="introduction-to-azure-security-center"></a>Introduction au centre de sécurité Azure

En savoir plus sur le centre de sécurité Azure, ses fonctionnalités clées et son fonctionnement.

> [AZURE.NOTE] Ce document présente le service à l’aide d’un exemple de déploiement.

## <a name="what-is-azure-security-center"></a>Quel est le centre de sécurité Azure ?
 Centre de sécurité vous permet d’empêcher, détecter et répondre aux menaces avec une meilleure visibilité et contrôler la sécurité de vos ressources Azure. Il assure la gestion de surveillance et la stratégie de sécurité intégrée sur vos abonnements Azure, permettant de détecter les menaces qui pourraient passer inaperçus et fonctionne avec une large réseau de solutions de sécurité.

##  <a name="key-capabilities"></a>Fonctionnalités clés
 Centre de sécurité propose des capacités de prévention, détection et réponse intégrées menace facile à utiliser et efficace sur Azure. Fonctionnalités clés sont :

| | |
|----- |-----|
| Empêcher | Surveille l’état de sécurité de vos ressources Azure |
| | Définit les stratégies pour vos abonnements Azure et les groupes de ressources en fonction des besoins de sécurité de votre entreprise, les types d’applications que vous utilisez et le critère de diffusion de vos données |
| | Recommandations de sécurité utilise prédéfinis pour aider les propriétaires de services par le biais du processus d’implémentation nécessaire contrôles |
| | Déploie rapidement des services de sécurité et appareils Microsoft et partenaires |
| Détecter |Collecte et analyse les données de sécurité de vos ressources Azure, le réseau et les solutions des partenaires comme programmes contre les logiciels malveillants et les pare-feu automatiquement |
| | Exploite globale des menaces intelligence à partir de produits Microsoft et services Microsoft numérique délits unité (DCU), la sécurité réponse centre MSRC (Microsoft) et flux externes |
| | S’applique analytique avancées, y compris l’apprentissage automatique et du comportement |
| Répondre | Fournit des incidents/alertes de sécurité par ordre de priorité |
| | Analyse des offres dans la source de l’attaque et ressources concernés |
| | Suggère des façons d’arrêter l’attaque en cours et protéger contre les attaques futures |

## <a name="introductory-walkthrough"></a>Procédure pas à pas d’introduction
 Vous accédez centre de sécurité à partir du [portail Azure](https://azure.microsoft.com/features/azure-portal/). [Se connecter au portail](https://portal.azure.com), sélectionnez **Parcourir**, puis faites défiler jusqu'à l’option **Centre de sécurité** et sélectionnez la vignette du **Centre de sécurité** que vous avez précédemment épinglés au tableau de bord du portail.

![Mosaïque de sécurité dans le portail Azure][1]

À partir du centre de sécurité, vous pouvez définir des stratégies de sécurité, surveiller les configurations de sécurité et afficher les alertes de sécurité.

### <a name="security-policies"></a>Stratégies de sécurité

Vous pouvez définir des stratégies pour vos abonnements Azure et les groupes de ressources en fonction des besoins de sécurité de votre entreprise. Vous pouvez également personnaliser les types d’applications que vous utilisez ou le critère de diffusion des données dans chaque abonnement. Par exemple, les ressources permettant de développement ou du test peuvent avoir des exigences de sécurité différentes de celles utilisées pour les applications de production. De même, les applications avec réglementé données telles que des informations d’identification personnelle peuvent nécessiter un niveau plus élevé de sécurité.

> [AZURE.NOTE] Pour modifier une stratégie de sécurité au niveau de l’abonnement ou le niveau de groupe de ressources, vous devez être le propriétaire de l’abonnement ou un collaborateur à celui-ci.

Dans la carte du **Centre de sécurité** , sélectionnez la vignette de **stratégie** pour une liste de vos abonnements et les groupes de ressources.   

![Carte du centre de sécurité][2]

Sur la carte de **stratégie de sécurité** , sélectionnez un abonnement pour afficher les détails de la stratégie.

![Abonnement de carte de stratégie de sécurité][3]

**Collecte de données** (voir ci-dessus) Active la collecte de données pour une stratégie de sécurité. L’activation offre :

- Analyse quotidienne de tous les pris en charge machines virtuelles de contrôle de la sécurité et de recommandations.
- Collection d’événements de sécurité pour la détection d’analyse et de menace.

**Choisir un compte de stockage par région** (voir ci-dessus) vous permet de choisir, pour chaque région dans lequel vous avez machines virtuelles en cours d’exécution, le compte de stockage où sont stockées les données collectées à partir de ces ordinateurs virtuels. Si vous ne choisissez pas un compte de stockage pour chaque région, il sera créé pour vous. Les données collectées sont logiquement isolées à partir des données d’autres clients pour des raisons de sécurité.

> [AZURE.NOTE] Collecte de données et en choisissant un compte de stockage par région est configuré au niveau de l’abonnement.

Sélectionnez la **stratégie de prévention** (voir ci-dessus) pour ouvrir la carte de **stratégie de prévention** . **Afficher les recommandations pour** vous permet de choisir les contrôles de sécurité que vous souhaitez surveiller et recommandé en fonction des besoins de sécurité des ressources de l’abonnement.

Ensuite, sélectionnez un groupe de ressources pour afficher les détails de la stratégie.

![Groupe de ressources de carte de stratégie sécurité][4]

**Héritage des autorisations** (voir ci-dessus) vous permet de définir le groupe de ressources en tant que :

- Hérité (par défaut) qui signifie que toutes les stratégies de sécurité pour ce groupe de ressources sont héritées à partir du niveau d’abonnement.
- Unique qui signifie que le groupe de ressources ont une stratégie de sécurité personnalisée. Vous avez besoin apporter des modifications sous **Afficher les recommandations pour**.

> [AZURE.NOTE] S’il existe un conflit entre la stratégie de l’abonnement et stratégie de niveau de groupe de ressources, la stratégie de niveau de groupe de ressources est prioritaire.

### <a name="security-recommendations"></a>Recommandations de sécurité

 Centre de sécurité analyse l’état de sécurité de vos ressources Azure pour identifier les problèmes de sécurité potentielles. Une liste de recommandations vous guide tout au long du processus de configuration des contrôles nécessaires. Voici quelques exemples :

- Mise en service contre les logiciels malveillants pour aider à identifier et supprimer des logiciels malveillants
- Configuration des groupes de sécurité réseau et des règles pour contrôler le trafic vers des machines virtuelles
- Mise en service de pare-feu d’application web à protéger contre les attaques cette cible vos applications web
- Déploiement mises à jour système manquants
- Définir les destinataires de configurations système d’exploitation qui ne correspondent pas les plannings de référence recommandés

Cliquez sur la vignette de **recommandations** pour une liste des recommandations. Cliquez sur chaque recommandation pour afficher des informations supplémentaires ou effectuer une action pour résoudre le problème.

![Recommandations de sécurité dans le centre de sécurité Azure][5]

### <a name="resource-health"></a>Santé des ressources

La vignette **au niveau de sécurité de ressource** indique la position de sécurité générale de l’environnement par type de ressource, y compris les machines virtuelles, des applications web et autres ressources.   

Sélectionnez un type de ressource sur la vignette **santé sécurité des ressources** pour afficher plus d’informations, notamment une liste des problèmes de sécurité potentielles ont été identifiées. (**Machines virtuelles** est sélectionnée dans l’exemple ci-dessous).

![Vignette d’intégrité de ressources][6]

### <a name="security-alerts"></a>Alertes de sécurité

 Centre de sécurité automatiquement collecte, analyse et intègre les données du journal à partir de vos ressources Azure, le réseau et les solutions des partenaires comme programmes contre les logiciels malveillants et de pare-feu. Lorsque des menaces sont détectées, une alerte de sécurité est créée. Voici quelques exemples de détection de :

- Machines virtuelles compromis communiquer avec des adresses IP malveillants connus
- Logiciels malveillants avancées détectés à l’aide de rapport d’erreurs Windows
- Attaques en force contre machines virtuelles
- Alertes de sécurité intégrée contre les logiciels malveillants programmes et de pare-feu

En cliquant sur la vignette **d’alertes de sécurité** affiche une liste des alertes par ordre de priorité.

![Alertes de sécurité][7]

Sélection d’une alerte affiche plus d’informations sur l’attaque et suggestions sur la façon de corriger celle-ci.

![Détails de l’alerte sécurité][8]

### <a name="partner-solutions"></a>Solutions des partenaires

La vignette **solutions des partenaires** vous permet de vous moniteur en un clin de œil l’état d’intégrité de votre solutions des partenaires intégré à votre abonnement Azure. Centre de sécurité affiche des alertes en provenance de solutions.

Sélectionnez la vignette **solutions des partenaires** . Une carte s’ouvre affiche une liste de toutes les solutions des partenaires connecté.

![Solutions des partenaires][9]

## <a name="get-started"></a>Prise en main
Pour commencer à utiliser le centre de sécurité, vous avez besoin d’un abonnement à Microsoft Azure. Centre de sécurité est compatible avec votre abonnement Azure. Si vous ne disposez pas d’un abonnement, vous pouvez vous inscrire à une [version d’évaluation gratuite](https://azure.microsoft.com/pricing/free-trial/).

 Vous accédez centre de sécurité à partir du [portail Azure](https://azure.microsoft.com/features/azure-portal/). Consultez la [documentation du portail](https://azure.microsoft.com/documentation/services/azure-portal/) pour en savoir plus.

[Prise en main du centre de sécurité Azure](security-center-get-started.md) Guide rapidement vous guide dans le contrôle de la sécurité et les composants de gestion des stratégies de centre de sécurité.

## <a name="see-also"></a>Voir aussi
Dans ce document, vous avez abordé centre de sécurité, ses fonctionnalités clées et la mise en route. Pour plus d’informations, voir les rubriques suivantes :

- [Définition de stratégies de sécurité dans le centre de sécurité Azure](security-center-policies.md) , découvrez comment configurer des stratégies de sécurité pour vos abonnements Azure et les groupes de ressources.
- [Gestion des recommandations de sécurité dans le centre de sécurité Azure](security-center-recommendations.md) , découvrez comment les recommandations vous aident à protéger vos ressources Azure.
- [Contrôle dans le centre de sécurité Azure d’état sécurité](security-center-monitoring.md) : Découvrez comment contrôler l’état de vos ressources Azure.
- [Gérer et répondre aux alertes de sécurité dans le centre de sécurité Azure](security-center-managing-and-responding-alerts.md) , découvrez comment gérer et répondre aux alertes de sécurité.
- [Surveillance solutions des partenaires avec le centre de sécurité Azure](security-center-partner-solutions.md) , découvrez comment contrôler l’état d’intégrité de votre solutions des partenaires.
- [Forum aux questions sur Centre de sécurité Azure](security-center-faq.md) : Forum aux questions sur l’utilisation du service de recherche.
- [Blog sur la sécurité Azure](http://blogs.msdn.com/b/azuresecurity/) — obtenir les dernières informations de sécurité Azure et des informations.

<!--Image references-->
[1]: ./media/security-center-intro/security-tile.png
[2]: ./media/security-center-intro/security-center.png
[3]: ./media/security-center-intro/security-policy.png
[4]: ./media/security-center-intro/security-policy-blade.png
[5]: ./media/security-center-intro/recommendations.png
[6]: ./media/security-center-intro/resources-health.png
[7]: ./media/security-center-intro/security-alert.png
[8]: ./media/security-center-intro/security-alert-detail.png
[9]: ./media/security-center-intro/partner-solutions.png

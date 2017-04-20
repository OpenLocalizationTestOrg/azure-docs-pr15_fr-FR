<properties
   pageTitle="Guide de démarrage rapide du centre de sécurité Azure | Microsoft Azure"
   description="Cet article vous permet de prise en main rapidement le centre de sécurité Azure en vous guider dans les composants de gestion de surveillance et la stratégie de sécurité et aux étapes suivantes."
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
   ms.date="10/28/2016"
   ms.author="terrylan"/>

# <a name="azure-security-center-quick-start-guide"></a>Guide de démarrage rapide du centre de sécurité Azure

Cet article vous permet de commencer rapidement avec le centre de sécurité Azure par vous guider dans les surveillance et la stratégie de gestion des composants de sécurité du centre de sécurité.

> [AZURE.NOTE] Cet article présente le service à l’aide d’un exemple de déploiement. Cet article n’est pas un guide étape par étape.

## <a name="prerequisites"></a>Conditions préalables

Pour commencer à utiliser le centre de sécurité, vous devez posséder un abonnement à Microsoft Azure. Si vous ne disposez pas d’un abonnement, vous pouvez vous inscrire pour un [compte gratuit](https://azure.microsoft.com/pricing/free-trial/).

La couche libre du centre de sécurité est activée automatiquement avec votre abonnement et fournit une visibilité sur l’état de sécurité de vos ressources Azure. Il propose des recommandations de sécurité, gestion des stratégies de sécurité de base et l’intégration avec les produits et services à partir de partenaires Azure.

Vous accédez centre de sécurité à partir du [portail Azure](https://azure.microsoft.com/features/azure-portal/). Pour en savoir plus sur le portail Azure, consultez la [documentation du portail](https://azure.microsoft.com/documentation/services/azure-portal/).

## <a name="data-collection"></a>Collecte de données

Centre de sécurité collecte des données à partir de vos machines virtuelles (machines virtuelles) pour évaluer leur état de sécurité, fournissent des recommandations de sécurité et signale les menaces. Lorsque vous accédez tout d’abord le centre de sécurité, collecte de données est activée sur tous les ordinateurs virtuels dans votre abonnement. Collecte de données est recommandée, mais vous pouvez désactiver cette fonctionnalité en désactivant la collecte de données dans la stratégie du centre de sécurité.

Les étapes suivantes décrivent comment accéder et utiliser les composants du centre de sécurité. Dans ces étapes, nous vous montrer comment désactiver la collecte de données si vous choisissez de désactiver cette fonctionnalité.

## <a name="access-security-center"></a>Centre de sécurité d’Access

Dans le portail, procédez comme suit pour accéder au centre de sécurité :

1. Dans le menu **Microsoft Azure** , sélectionnez **Le centre de sécurité**.
![Menu Azure][1]

2. Si vous accédez à centre de sécurité pour la première fois, la carte **Bienvenue** s’ouvre. Sélectionnez Oui **! Je souhaite le centre de sécurité Azure lancement** pour ouvrir la carte du **Centre de sécurité** et pour activer la collecte de données.
![Écran d’accueil][10]

3. Une fois que vous lancez le centre de sécurité à partir de la carte Bienvenue ou sélectionnez Centre de sécurité dans le menu de Microsoft Azure, la carte du **Centre de sécurité** s’ouvre. Pour faciliter l’accès à la carte du **Centre de sécurité** à l’avenir, sélectionnez l’option de **carte de code confidentiel au tableau de bord** (coin supérieur droit).
![Carte de code confidentiel au tableau de bord][2]

## <a name="use-security-center"></a>Utilisez le centre de sécurité

Vous pouvez configurer des stratégies de sécurité pour vos abonnements Azure et les groupes de ressources. Nous allons configurer une stratégie de sécurité pour votre abonnement :

1. Dans la carte du **Centre de sécurité** , sélectionnez la vignette de **stratégie** .
![Stratégie de sécurité][3]

2. Dans la carte de **stratégie de sécurité - définir la stratégie par abonnement ou ressource par groupe** , sélectionnez un abonnement.
3. Sur la carte de **stratégie de sécurité** , **la collecte des données** est activée automatiquement collecter les journaux. L’extension de surveillance est configurée sur tous les ordinateurs virtuels actuelles et le nouveau dans l’abonnement. (Vous pouvez cesser d’utiliser la collecte des données en définissant la **collecte de données** pour **désactiver**, mais cela empêche le centre de sécurité de vous donnant recommandations et les alertes de sécurité).
4. Sur la carte de **stratégie de sécurité** , sélectionnez **Choisir un compte de stockage par région**. Pour chaque région dans lequel vous avez machines virtuelles en cours d’exécution, vous choisissez le compte de stockage où sont stockées les données collectées à partir de ces ordinateurs virtuels. Si vous ne choisissez pas un compte de stockage pour chaque région, il est créé pour vous. Les données collectées sont logiquement isolées à partir des données d’autres clients pour des raisons de sécurité.

     > [AZURE.NOTE] Nous vous conseillons activez la collecte de données et choisissez tout d’abord un compte de stockage au niveau de l’abonnement. Stratégies de sécurité peuvent être définies au niveau de l’abonnement Azure et niveau de groupe de ressources, mais la configuration de compte de stockage et de données se produit au niveau de l’abonnement uniquement.

5. Sur la carte de **stratégie de sécurité** , sélectionnez la **stratégie de prévention**. Cette action ouvre la carte de **stratégie de prévention** .
![Stratégie de prévention][4]

6. Sur la carte de **stratégie de prévention** , activez les recommandations que vous souhaitez faire figurer dans le cadre de votre stratégie de sécurité. Exemples :

 - Définition des **mises à jour système** à **On** analyse tous les ordinateurs virtuels pris en charge pour les mises à jour du système d’exploitation manquantes.
 - Paramétrer le **vulnérabilités du système d’exploitation** **sur** analyse tous les ordinateurs virtuels pris en charge pour identifier les configurations système d’exploitation qui risquent d’exposer la machine virtuelle plus à s’attaquer aux.

### <a name="view-recommendations"></a>Recommandations relatives aux vues

1. Revenez à la carte du **Centre de sécurité** et sélectionnez la vignette de **recommandations** . Centre de sécurité analyse régulièrement l’état de sécurité de vos ressources Azure. Lorsque le centre de sécurité identifie les problèmes de sécurité potentielles, il affiche recommandations sur la carte de **recommandations** .
![Recommandations dans le centre de sécurité Azure][5]

2.  Sélectionnez une recommandation sur la carte de **recommandations** pour afficher plus d’informations et/ou à prendre des mesures pour résoudre le problème.

### <a name="view-the-health-and-security-state-of-your-resources"></a>Afficher l’état d’intégrité et la sécurité de vos ressources

1.  Revenir à la carte du **Centre de sécurité** . La vignette **au niveau de sécurité de ressources** comporte des indicateurs de l’état de sécurité pour machines virtuelles, mise en réseau, les données et les applications.
2.  Sélectionnez **machines virtuelles** pour afficher plus d’informations. La carte **machines virtuelles** s’ouvre et affiche une synthèse d’état des programmes contre les logiciels malveillants, mises à jour système, redémarre et faiblesses du système d’exploitation des vos ordinateurs virtuels.
![La vignette d’intégrité de ressources dans le centre de sécurité Azure][6]

3.  Sélectionnez une recommandation sous **MACHINE virtuelle recommandations** pour afficher davantage d’informations et/ou agir pour configurer les contrôles nécessaires.
4.  Sélectionnez une machine virtuelle sous **machines virtuelles** pour afficher des détails supplémentaires.

### <a name="view-security-alerts"></a>Afficher les alertes de sécurité

1.  Revenez à la carte du **Centre de sécurité** , puis sélectionnez la vignette **d’alertes de sécurité** . La carte **d’alertes de sécurité** s’ouvre et affiche une liste des alertes. L’analyse du centre de sécurité de vos journaux de sécurité et vos activités réseau génère ces alertes. Alertes de solutions des partenaires intégrées sont incluses.
![Alertes de sécurité dans le centre de sécurité Azure][7]

    > [AZURE.NOTE] Alertes de sécurité sont uniquement disponibles si la couche Standard du centre de sécurité est activée. Une version d’évaluation gratuite de 90 jours de la couche Standard est disponible. Consultez les [étapes suivantes](#next-steps) pour plus d’informations sur l’obtention de la couche Standard.

2.  Sélectionnez une alerte pour afficher des informations supplémentaires. Dans cet exemple, nous allons sélectionnez **système modifié binaire découvertes**. Cette action ouvre cartes qui fournissent des détails supplémentaires sur l’alerte.
![Détails de l’alerte sécurité dans le centre de sécurité Azure][8]

### <a name="view-the-health-of-your-partner-solutions"></a>Afficher l’état de vos solutions des partenaires

1. Revenir à la carte du **Centre de sécurité** . La vignette **solutions des partenaires** vous permet de contrôler, en un clin de œil, l’état d’intégrité de votre solutions des partenaires intégré à votre abonnement Azure.
2. Sélectionnez la vignette **solutions des partenaires** . Une carte s’ouvre et affiche une liste de vos solutions des partenaires connecté au centre de sécurité.
![Solutions des partenaires][9]

3. Sélectionnez une solution partenaire. Dans cet exemple, commençons par sélectionner la solution **F5 WAF** .  Une carte s’ouvre et vous indique l’état de la solution partenaire et des ressources associées de la solution. Sélectionnez **console Solution** pour ouvrir l’expérience de gestion de partenaire pour cette solution.

## <a name="next-steps"></a>Étapes suivantes
Cet article vous présente les surveillance et la stratégie de gestion des composants de sécurité du centre de sécurité. À présent que vous avez l’habitude de centre de sécurité, procédez comme suit :

- Configurer une stratégie de sécurité pour votre abonnement Azure. Pour plus d’informations, reportez-vous à [définition des stratégies de sécurité dans le centre de sécurité Azure](security-center-policies.md).
- Utilisez les recommandations dans le centre de sécurité pour vous aider à protéger vos ressources Azure. Pour plus d’informations, voir [Gestion des recommandations de sécurité dans le centre de sécurité Azure](security-center-recommendations.md).
- Passez en revue et gérer vos alertes de sécurité en cours. Pour plus d’informations, voir [gérer et répondre aux alertes de sécurité dans le centre de sécurité Azure](security-center-managing-and-responding-alerts.md).
- Plus d’informations sur [menace détection fonctionnalités avancées](security-center-detection-capabilities.md) fournis avec la [couche Standard](security-center-pricing.md) du centre de sécurité. Une version d’évaluation gratuite de 90 jours de la couche Standard est disponible.
- Si vous avez des questions sur l’utilisation du centre de sécurité, consultez le [Forum aux questions sur Centre de sécurité Azure](security-center-faq.md).

<!--Image references-->
[1]: ./media/security-center-get-started/azure-menu.png
[2]: ./media/security-center-get-started/security-center-pin.png
[3]: ./media/security-center-get-started/security-policy.png
[4]: ./media/security-center-get-started/prevention-policy.png
[5]: ./media/security-center-get-started/recommendations.png
[6]: ./media/security-center-get-started/resources-health.png
[7]: ./media/security-center-get-started/security-alert.png
[8]: ./media/security-center-get-started/security-alert-detail.png
[9]: ./media/security-center-get-started/partner-solutions.png
[10]: ./media/security-center-get-started/welcome.png

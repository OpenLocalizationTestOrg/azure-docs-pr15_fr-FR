<properties
   pageTitle="Centre de sécurité tarifs | Microsoft Azure"
   description="Cet article fournit des informations sur les tarifs des Azure le centre de sécurité."
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
   ms.date="10/12/2016"
   ms.author="terrylan"/>

# <a name="azure-security-center-pricing"></a>Centre de sécurité Azure tarifs

Centre de sécurité Azure vous permet d’empêcher, détecter et répondre aux menaces avec une meilleure visibilité et contrôler la sécurité de vos ressources Azure. Il assure la gestion de surveillance et la stratégie de sécurité intégrée sur vos abonnements Azure, permettant de détecter les menaces qui pourraient passer inaperçus et fonctionne avec une large réseau de solutions de sécurité.

## <a name="pricing-tiers"></a>Tarifs niveaux

Centre de sécurité est proposé en deux versions :

- La **couche libre** est automatiquement activée sur tous les abonnements Azure. La catégorie gratuite offre une visibilité sur l’état de sécurité de vos ressources Azure, stratégie de sécurité de base, recommandations de sécurité et l’intégration avec les produits et services à partir de partenaires.
- La **couche Standard** ajoute des fonctionnalités de détection menace avancées, y compris les menaces, l’analyse comportement, détection des anomalies, incidents de sécurité et de rapports d’évaluation menace. Une **version d’évaluation gratuite de 90 jours** est disponible pour la couche Standard.

Pour plus d’informations, voir le centre de sécurité [tarifs page](https://azure.microsoft.com/pricing/details/security-center/).

> [AZURE.NOTE] Centre de sécurité utilise le stockage Azure pour enregistrer les données de sécurité générées à partir de vos nœuds protégées. Les coûts associés à ce système de stockage ne sont pas inclus dans le prix du service et sont facturées séparément au régulières [taux stockage Azure](https://azure.microsoft.com/pricing/details/storage/blobs/). Frais de stockage applicables même pendant la période d’évaluation.

## <a name="try-standard-free-for-90-days"></a>Essayez gratuitement pendant 90 jours Standard

Une version d’évaluation gratuite de 90 jours est disponible pour la couche Standard. Pour obtenir la version d’évaluation gratuite de la couche Standard, sélectionnez la vignette de **stratégie** dans la carte du **Centre de sécurité** . Sélectionnez l’abonnement auquel vous souhaitez mettre à niveau en Standard. Sur la carte de **stratégie de sécurité** , sélectionnez le **niveau de tarification**. Sur la carte de **Choisir votre niveau de tarification** , sélectionnez **Standard – version d’évaluation gratuite**.

![Version d’évaluation gratuite][1]

À la fin de 90 jours, si vous décidez de continuer à utiliser le service, nous démarre automatiquement pour réduire l’utilisation de chargement.

## <a name="upgrade-to-standard"></a>Mise à niveau en Standard

Mettre à niveau vers la couche Standard pour ajouter une détection des menaces avancées. Pour obtenir la couche Standard, sélectionnez la vignette de **stratégie** dans la carte du **Centre de sécurité** . Sélectionnez l’abonnement auquel vous souhaitez mettre à niveau en Standard. Dans la carte de **stratégie de sécurité** , sélectionnez **niveau de prix**. Dans la carte de **Choisir votre niveau de tarification** , sélectionnez **Standard**.

![Couche standard][2]

## <a name="why-upgrade-to-standard"></a>Pourquoi mettre à niveau vers Standard ?

La catégorie Standard du centre de sécurité offre toutes les fonctionnalités de la couche libre plus de détection avancées. Détection des menaces avancé permet d’identifier les menaces actives un ciblage de vos ressources Azure et vous fournit les informations nécessaires pour répondre rapidement.

Centre de sécurité emploie analytique de sécurité avancés, qui sont dépassent approches basée sur les signatures. Innovations dans big data et d’apprentissage technologies automatique sont exploitées pour évaluer les événements au sein de la structure de toute cloud – détection des menaces qu’il est impossibles d’identifier à l’aide des approches manuels et de prédiction de l’évolution des attaques.

Analytique de sécurité fournis avec la couche Standard est les suivantes :

- **Informations sur les menaces** - cherche connus bad intervenants à l’aide sur les menaces globale à partir de produits Microsoft et services, l’unité délits numérique Microsoft, Microsoft Security Response Center et flux externes
- **Comportement** - applique un motif connus pour découvrir les comportements malveillants
- **Détection des anomalies** - utilise le profil statistiques pour générer un planning de référence historique. Il signale les déviations par rapport à établie plannings de référence qui sont conformes à un vecteur d’attaque potentiels

Dans la carte **d’alertes de sécurité** ci-dessous, centre de sécurité a détecté un **incident**de sécurité. Un incident de sécurité est un regroupement de toutes les alertes pour une ressource qui s’alignent avec des motifs de chaîne arrêt. Sélection de l’incident de sécurité pour afficher plus d’informations sur l’incident et répertorie les alertes associées. Sélection d’une alerte fournit plus d’informations sur cette occurrence.

![Incident de sécurité][3]

L’alerte de **communication réseau** ci-dessous fournit des détails sur l’alerte. Les détails incluent sa description complète, sa gravité, son état actuel (qui dans ce cas est fermé, ce qui signifie que l’utilisateur a eu action à faire disparaître), les ressources attaque et les étapes de mise à jour. Il existe également une liste de liens vers des rapports sur les menaces Microsoft. Ces rapports peuvent être utilisés à des fins défensifs et protection contre les risques.

![Détails de l’alerte sécurité][4]

## <a name="enable-data-collection"></a>Activer la collecte de données

Pour activer la machine virtuelle comportement analytique, collecte de données doit être activée. Vous devrez peut-être activer la collecte de données lorsque vous accédez tout d’abord le centre de sécurité ou lorsque vous créez une stratégie de sécurité.

Pour valider que la collecte de données est activée, sélectionnez la vignette de **stratégie** . La carte de **stratégie de sécurité** s’ouvre répertoriant vos abonnements Azure. Sélectionnez un abonnement. Si **la collecte des données** est désactivé, modifiez-le activé et enregistrez la modification. Voir [Activer la collecte des données dans le centre de sécurité Azure](security-center-enable-data-collection.md).

## <a name="next-steps"></a>Étapes suivantes

- Dans ce document, vous avez abordé tarification pour le centre de sécurité. Pour plus d’informations tarification, consultez le centre de sécurité [tarifs page](https://azure.microsoft.com/pricing/details/security-center/).
- Pour en savoir plus sur les fonctionnalités de détection avancée du centre de sécurité, voir [le centre de sécurité Azure des fonctionnalités de détection](security-center-detection-capabilities.md).
- Si vous avez des questions sur l’utilisation du centre de sécurité, consultez le [Forum aux questions sur Centre de sécurité Azure](security-center-faq.md).
- Si vous avez encore des questions sur l’utilisation du centre de sécurité ou Azure, consultez les [Forums Azure](https://social.msdn.microsoft.com/Forums/home?forum=AzureSecurityCenter&filter=alltypes&sort=lastpostdesc).

<!--Image references-->
[1]: ./media/security-center-pricing/free-trial.png
[2]: ./media/security-center-pricing/standard.png
[3]: ./media/security-center-pricing/incident.png
[4]: ./media/security-center-pricing/network-alert.png

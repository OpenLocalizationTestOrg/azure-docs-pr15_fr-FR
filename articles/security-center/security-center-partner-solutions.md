<properties
   pageTitle="Gestion des solutions des partenaires dans le centre de sécurité Azure | Microsoft Azure"
   description="Ce document vous explique comment le centre de sécurité Azure vous permet de vous moniteur en un clin de œil l’état d’intégrité de votre solutions des partenaires intégré à votre abonnement Azure."
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
   ms.date="10/26/2016"
   ms.author="terrylan"/>

# <a name="monitoring-partner-solutions-with-azure-security-center"></a>Surveillance solutions des partenaires avec le centre de sécurité Azure

Ce document vous explique comment faire pour surveiller l’état d’intégrité de votre solutions des partenaires dans le centre de sécurité Azure.

> [AZURE.NOTE] Ce document présente le service à l’aide d’un exemple de déploiement. Il s’agit pas d’un guide étape par étape.

## <a name="monitoring-partner-solutions"></a>Surveillance solutions des partenaires

La vignette **solutions des partenaires** sur la carte du **Centre de sécurité** vous permet de vous moniteur en un clin de œil l’état d’intégrité de votre solutions des partenaires intégré à votre abonnement Azure.
![Vignette de solutions des partenaires][1]

La vignette **solutions des partenaires** affiche le nombre de solutions des partenaires et un résumé de ces solutions de l’état.

L' **état** d’une solution partenaire peut être :

- Protège (vert), il n’y a aucun problème d’intégrité.
- Incorrecte du rapport (rouge) - actuellement un problème d’intégrité qui exige une attention immédiate.
- Arrêté (orange) de création de rapports : la solution a cessé de création de rapports son état.
- État de protection inconnue (orange) - l’état de la solution est inconnu pour l’instant en raison d’un processus d’échec de l’ajout d’une nouvelle ressource à la solution existante.
- Pas signalé (gris) - la solution n’a pas signalé rien encore, état d’une solution peut être non signalé si elle vient d’être connecté et déploie toujours.

Si aucune solution intégré à votre abonnement la vignette indique qu’il n’existe aucune solution. Sélectionnez la vignette **solutions des partenaires** vous permettra d’ouvrir la carte de **recommandations** pour déployer des solutions de sécurité des partenaires.
![Aucune solutions des partenaires][2]

Pour afficher l’état de vos solutions des partenaires :

1. Sélectionnez la vignette **solutions des partenaires** . Une carte s’ouvre affiche une liste de vos solutions des partenaires connecté au centre de sécurité.
![Solutions des partenaires][3]

2. Sélectionnez une solution partenaire. Dans cet exemple, permet de sélectionner la solution **F5 WAF2** .  Une carte s’ouvre pour afficher que l’état de la solution partenaire et la solution les ressources associées. Sélectionnez **console Solution** pour ouvrir l’expérience de gestion de partenaire pour cette solution.
![Détail de solution partenaire][4]

3. Revenez à la carte **F5 WAF2** et sélectionnez **lien application**. La carte de **Lien Applications** s’ouvre. Ici, vous pouvez connecter ressources à la solution partenaire.
![Lien ressources solution partenaire][5]

## <a name="see-also"></a>Voir aussi
Dans ce document, vous avez abordé à la vignette **Solutions des partenaires** dans le centre de sécurité. Pour en savoir plus sur le centre de sécurité, voir les rubriques suivantes :

- [Définition de stratégies de sécurité dans le centre de sécurité Azure](security-center-policies.md) , découvrez comment configurer des stratégies de sécurité pour vos abonnements Azure et les groupes de ressources.
- [Gestion des recommandations de sécurité dans le centre de sécurité Azure](security-center-recommendations.md) , découvrez comment les recommandations vous aident à protéger vos ressources Azure.
- [Contrôle dans le centre de sécurité Azure d’état sécurité](security-center-monitoring.md) : Découvrez comment contrôler l’état de vos ressources Azure.
- [Gérer et répondre aux alertes de sécurité dans le centre de sécurité Azure](security-center-managing-and-responding-alerts.md) , découvrez comment gérer et répondre aux alertes de sécurité.
- [Forum aux questions sur Centre de sécurité Azure](security-center-faq.md) : Forum aux questions sur l’utilisation du service de recherche.
- [Blog sur la sécurité Azure](http://blogs.msdn.com/b/azuresecurity/) — obtenir les dernières informations de sécurité Azure et des informations.

<!--Image references-->
[1]: ./media/security-center-partner-solutions/partner-solutions-tile.png
[2]: ./media/security-center-partner-solutions/no-partner-solutions-to-display.png
[3]: ./media/security-center-partner-solutions/partner-solutions.png
[4]: ./media/security-center-partner-solutions/partner-solutions-detail.png
[5]: ./media/security-center-partner-solutions/link-applications.png

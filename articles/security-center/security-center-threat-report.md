<properties
   pageTitle="Rapport sur les données du centre de sécurité Azure menace | Microsoft Azure"
   description="Ce document vous permet d’utiliser les rapports Intelligent du menace centre de sécurité Azure au cours d’une enquête pour trouver plus d’informations sur une alerte de sécurité."
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
   ms.date="10/17/2016"
   ms.author="yurid"/>

# <a name="azure-security-center-threat-intelligence-report"></a>Rapport sur les données menace Azure le centre de sécurité
Ce document explique comment Azure centre menace Intelligent des rapports de sécurité peuvent vous aider à en savoir plus sur une menace ayant généré une alerte de sécurité.

## <a name="what-is-a-threat-intelligence-report"></a>Qu’est un rapport sur les données menace ?
Détection des menaces centre de sécurité est de surveiller les informations de sécurité de vos ressources Azure, le réseau et les solutions des partenaires connecté. Il analyse ces informations, corrélation souvent des informations de plusieurs sources d’identifier les menaces. Ce processus fait partie des [fonctionnalités de détection](security-center-detection-capabilities.md)centre de sécurité. 

Lorsque le centre de sécurité identifie une menace, il déclenche une [alerte de sécurité](security-center-managing-and-responding-alerts.md), qui contient des informations détaillées sur un événement particulier, y compris des suggestions pour la conversion. Pour vous aider incidents équipes examiner et corriger les menaces, centre de sécurité inclut un rapport sur les données menace qui contient des informations sur la menace qui a été détectée, y compris des informations telles que le : 

- Son identité ou associations (si ces informations sont accessibles)
- Objectifs des intrus
- Campagnes attaque actuelles et historiques (si ces informations sont accessibles)
- Des intrus tactiques, outils et procédures
- Indicateurs associés de compromis (IoC) tels que les URL et hachage de fichier
- Victimology, ce qui correspond à l’industrie et prédominance géographique pour vous aider à déterminer si vos ressources Azure sont exposés
- Informations de mise à jour et d’atténuation

>[AZURE.NOTE] La quantité d’informations dans n’importe quel rapport donné varie ; le niveau de détail est basé sur les activités et prédominance du logiciel malveillant.

Centre de sécurité contient trois types de rapports de menace, qui peuvent varier en fonction de l’attaque. Les rapports disponibles sont :

- **Rapport de groupe d’activité**: fournit tels que l’approfondie dans les pirates, objectifs et tactiques.
- **Rapport de campagne**: met l’accent sur les détails des campagnes attaque spécifique. 
- **Rapport de synthèse de menace**: couvre tous les éléments dans les deux rapports précédents.

Ce type d’information est très utile pendant le processus de [réponse aux incidents](security-center-incident-response.md) , lorsqu’il existe une enquête en cours pour comprendre la source de l’attaque, les motivations par le pirate et procédure à suivre pour atténuer ce problème aller plus loin. 

## <a name="how-to-access-the-threat-intelligence-report"></a>Comment accéder au rapport intelligence menace ?

Vous pouvez consulter vos alertes en cours en consultant la vignette **d’alertes de sécurité** . Ouvrez le portail Azure et suivez les étapes ci-dessous pour afficher plus d’informations sur chaque alerte :

1. Dans le tableau de bord centre de sécurité, vous verrez la vignette **d’alertes de sécurité** .

2. Cliquez sur la vignette pour ouvrir la carte **d’alertes de sécurité** qui contient plus d’informations sur les alertes, puis cliquez sur l’alerte de sécurité que vous souhaitez obtenir plus d’informations à propos de.

    ![Alertes de sécurité](./media/security-center-threat-report/security-center-threat-report-fig1.png)

3. Dans ce cas la carte **suspect processus exécuté** affiche les détails de l’alerte comme indiqué dans l’illustration ci-dessous :

    ![Detais alerte de sécurité](./media/security-center-threat-report/security-center-threat-report-fig2.png)

4.  La quantité d’informations disponibles pour chaque alerte de sécurité varient selon le type d’alerte. Dans le champ **rapports** vous avez un lien vers le rapport de menace. Cliquez dessus et une autre fenêtre de navigateur s’affiche avec le fichier PDF.

    ![Sélection de stockage](./media/security-center-threat-report/security-center-threat-report-fig3.png)

À partir de là, vous pouvez télécharger le fichier PDF ce rapport et en savoir plus sur le problème de sécurité qui a été détecté et effectuer des actions en fonction des informations fournies.

## <a name="see-also"></a>Voir aussi

Dans ce document, vous avez appris comment Azure centre menace Intelligent des rapports de sécurité peut vous aider au cours d’une enquête à propos des alertes de sécurité. Pour en savoir plus sur le centre de sécurité Azure, voir les rubriques suivantes :

- [Forum aux questions sur le centre de sécurité azure](security-center-faq.md). Rechercher des questions fréquemment posées sur l’utilisation du service.
- [Exploiter les centre de sécurité Azure réponse aux incidents](security-center-incident-response.md)
- [Fonctionnalités de détection Azure le centre de sécurité](security-center-detection-capabilities.md)
- [Guident de planification du centre de sécurité azure et opérations](security-center-planning-and-operations-guide.md). Apprenez à planifier et comprendre les considérations de conception à adopter Azure le centre de sécurité.
- [Gérer et répondre aux alertes de sécurité dans le centre de sécurité Azure](security-center-managing-and-responding-alerts.md). Apprenez à gérer et répondre aux alertes de sécurité.
- [Gestion des incidents de sécurité dans le centre de sécurité Azure](security-center-incident.md)
- [Blog sur la sécurité azure](http://blogs.msdn.com/b/azuresecurity/). Rechercher des billets de blog sur la conformité et la sécurité Azure.

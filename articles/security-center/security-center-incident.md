<properties
   pageTitle="Gestion des incidents de sécurité dans le centre de sécurité Azure | Microsoft Azure"
   description="Ce document vous permet d’utiliser des fonctionnalités du centre de sécurité Azure pour gérer des incidents de sécurité."
   services="security-center"
   documentationCenter="na"
   authors="YuriDio"
   manager="swadhwa"
   editor=""/>

<tags
   ms.service="security-center"
   ms.topic="hero-article"
   ms.devlang="na"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/18/2016"
   ms.author="yurid"/>

# <a name="handling-security-incident-in-azure-security-center"></a>Gestion des incidents de sécurité dans le centre de sécurité Azure 
Tri et analyse des alertes de sécurité peuvent être beaucoup de temps pour même les analystes de sécurité expérimentés, et pour un grand nombre, il est difficile de savoir où commencer. En utilisant [analytique](security-center-detection-capabilities.md) pour connecter les informations entre distinct [alertes de sécurité](security-center-managing-and-responding-alerts.md), centre de sécurité peut vous fournir une seule vue d’une campagne d’attaque et toutes les alertes connexes : vous pouvez rapidement comprendre quelles actions l’intrus a eu et les ressources qui ont été affectées.

Ce document explique comment utiliser la fonction d’alerte de sécurité dans le centre de sécurité pour vous aider à des incidents de sécurité.


## <a name="what-is-a-security-incident"></a>Qu’est un incident de sécurité ?

Dans le centre de sécurité, un incident de sécurité est un regroupement de toutes les alertes pour une ressource qui s’alignent avec des motifs [Arrêter chaîne](https://blogs.technet.microsoft.com/office365security/addressing-your-cxos-top-five-cloud-security-concerns/) . Incidents s’affichent dans la vignette [d’Alertes de sécurité](security-center-managing-and-responding-alerts.md) et de la carte. Un Incident dévoilent la liste des alertes associées, qui permet de vous permettent d’obtenir plus d’informations sur chaque occurrence.

## <a name="managing-security-incidents"></a>Gestion des incidents de sécurité

Vous pouvez consulter votre incidents de sécurité en cours en consultant la vignette d’alertes de sécurité. Accéder au portail Azure et suivez les étapes ci-dessous pour voir davantage de détails sur chaque incident de sécurité :

1. Dans le tableau de bord centre de sécurité, vous verrez la vignette **d’alertes de sécurité** .

    ![Vignette d’alertes de sécurité dans le centre de sécurité](./media/security-center-incident/security-center-incident-fig1.png)

2.  Cliquez sur cette vignette pour développer celle-ci et si un incident de sécurité est détecté, elle apparaît sous le graphique d’alertes de sécurité comme indiqué ci-dessous :

    ![Incident de sécurité](./media/security-center-incident/security-center-incident-fig2.png)

3.  Notez que la description de l’incident sécurité possède une icône différente par rapport aux autres alertes. Cliquez sur pour afficher plus d’informations sur cet incident.

    ![Incident de sécurité](./media/security-center-incident/security-center-incident-fig3.png)

4.  Sur l' **incident de** carte vous verrez plus détails concernant cet incident de sécurité, qui inclut sa description complète, sa gravité (c'est-à-dire dans ce cas haute), son état actuel (dans ce cas elle n’est toujours *active*, qui implique que l’utilisateur n’a pas effectué une action pour *faire disparaître* , cela en cliquant avec le bouton droit sur l’incident dans la carte **d’alertes de sécurité** ) , cette ressource attaque (dans ce cas *VM1*), la mise à jour les étapes pour l’incident, et dans le volet inférieur que vous avez les alertes qui ont été inclus dans cet incident. Si vous souhaitez obtenir plus d’informations sur chaque alerte, cliquez simplement sur une autre carte s’ouvre, comme illustré ci-dessous :

    ![Incident de sécurité](./media/security-center-incident/security-center-incident-fig4.png)

Les informations sur cette carte varient en fonction de l’alerte. Pour plus d’informations sur la façon de gérer les alertes, lisez [gérer et répondre aux alertes de sécurité dans le centre de sécurité Azure](security-center-managing-and-responding-alerts.md) . Certaines considérations importantes concernant cette fonctionnalité :

- Un nouveau filtre vous permet de personnaliser l’affichage à l’Incident uniquement, uniquement les alertes ou les deux. 
- Le même message d’alerte peut exister dans le cadre d’un Incident (le cas échéant), ainsi qu’à être représentés sous forme d’une alerte autonome. 
- Faire disparaître un incident fera disparaître pas les alertes associées.

## <a name="see-also"></a>Voir aussi

Dans ce document, vous avez appris à utiliser la fonctionnalité incident de sécurité dans le centre de sécurité. Pour en savoir plus sur le centre de sécurité, voir les rubriques suivantes :

- [Gestion et répondre aux alertes de sécurité dans le centre de sécurité Azure](security-center-managing-and-responding-alerts.md)
- [Fonctionnalités de détection Azure le centre de sécurité](security-center-detection-capabilities.md)
- [Centre de sécurité Azure Guide de planification et opérations](security-center-planning-and-operations-guide.md)
- [Gestion et répondre aux alertes de sécurité dans le centre de sécurité Azure](security-center-managing-and-responding-alerts.md)
- [Forum aux questions sur Centre de sécurité azure](security-center-faq.md)--rechercher Forum aux questions sur l’utilisation du service.
- Billets de [blog sur la sécurité azure](http://blogs.msdn.com/b/azuresecurity/)--rechercher blog sur la conformité et la sécurité Azure.

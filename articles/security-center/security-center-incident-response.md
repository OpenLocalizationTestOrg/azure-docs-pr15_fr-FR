<properties
   pageTitle="À l’aide du centre de sécurité Azure pour une réponse à un incident | Microsoft Azure"
   description="Ce document explique comment utiliser le centre de sécurité Azure pour un scénario de réponse aux incidents."
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
   ms.date="09/20/2016"
   ms.author="yurid"/>

# <a name="using-azure-security-center-for-an-incident-response"></a>À l’aide du centre de sécurité Azure pour une réponse à un incident
De nombreuses organisations apprennent à répondre aux incidents de sécurité qu’après avoir subi une attaque. Pour réduire les coûts et dommages, il est important de disposer d’une réponse à un incident plan avant une attaque a lieu. Vous pouvez utiliser le centre de sécurité Azure dans différentes étapes de la réponse à un incident.

## <a name="incident-response-planning"></a>Planification de la réponse aux incidents

Un plan efficace dépend de trois fonctionnalités principales : la possibilité de protéger, détecter et répondre aux menaces. Protection est protection contre les incidents, la détection est sur l’identification des menaces au plus tôt et réponse est sur le pirate la suppression et la restauration des systèmes pour atténuer les conséquences d’une violation.

Cet article utilisera les étapes de réponse à un incident de sécurité à partir de l’article de la [Réponse de sécurité Microsoft Azure dans le Cloud](https://gallery.technet.microsoft.com/Azure-Security-Response-in-dd18c678) , comme illustré dans le diagramme suivant :

![Cycle de vie des incidents](./media/security-center-incident-response/security-center-incident-response-fig1.png)

Vous pouvez utiliser le centre de sécurité au cours des étapes détecter, évaluer et diagnostiquer. Voici quelques exemples de comment le centre de sécurité peut être utile au cours des trois phases incidents initiale :

- **Détecter**: passez en revue la première indication d’une enquête événement.
    - Exemple : révision la vérification initiale qu’une alerte de sécurité avec une priorité élevée a été élevée dans le tableau de bord centre de sécurité.
- **Évaluer**: effectuer l’évaluation initiale pour obtenir plus d’informations sur l’activité suspecte.
    - Exemple : obtenir plus d’informations sur l’alerte de sécurité.
- **Diagnostiquer**: effectuez une enquête technique et identifier les stratégies imbrication d’atténuation et la solution de contournement.
    - Exemple : suivez les étapes de mise à jour décrites par le centre de sécurité dans cette alerte de sécurité particulière.

Le scénario qui suit vous montre comment tirer parti du centre de sécurité au cours des étapes détecter, évaluer et diagnostiquer/répondre d’un incident de sécurité. Dans le centre de sécurité, un [incident de sécurité](security-center-incident.md) est un regroupement de toutes les alertes pour une ressource qui s’alignent avec des motifs [Arrêter chaîne](https://blogs.technet.microsoft.com/office365security/addressing-your-cxos-top-five-cloud-security-concerns/) . Incidents s’affichent dans la vignette [d’alertes de sécurité](security-center-managing-and-responding-alerts.md) et de la carte. Un incident de pour afficher la liste des alertes associées, qui permet de vous permettent d’obtenir plus d’informations sur chaque occurrence. Centre de sécurité présente également autonome des alertes de sécurité qui peuvent servir à analyser une activité suspecte.

## <a name="scenario"></a>Scénario

Contoso migrés récemment certaines de leurs ressources locales vers Azure, y compris certains basées sur machine virtuelle professionnelles de charges de travail et les bases de données SQL. Pour l’instant, Core ordinateur réponse d’équipe (incidents de Contoso de sécurité) a un problème de rechercher les problèmes de sécurité en raison d’intelligence sécurité ne pas intégré avec leurs outils de réponse aux incidents en cours. Ce manque d’intégration présente un problème lors de l’étape de détecter (trop fausses), ainsi qu’au cours des étapes évaluer et diagnostiquer. Dans le cadre de cette migration, ils décidé de participer pour le centre de sécurité afin de les aider à résoudre le problème.

La première phase de cette migration terminée après qu’ils onboarded toutes les ressources et adressé toutes les recommandations de sécurité du centre de sécurité. Contoso CSIRT est le point central en matière de gestion des incidents de sécurité informatique. L’équipe est constitué d’un groupe de personnes ayant des responsabilités en matière de gestion des incidents de sécurité. Les membres d’équipe ont clairement définis droits pour vous assurer qu’aucune zone de réponse ne reste non couvert.

Aux fins de ce scénario, nous allons pour se concentrer sur les rôles suivants personnage qui font partie de Contoso CSIRT :

![Cycle de vie des incidents](./media/security-center-incident-response/security-center-incident-response-fig2.png)

Karina se trouve dans les opérations de sécurité. Ses responsabilités :

- La surveillance et répondre aux menaces la nuit.
- Passer au propriétaire de la charge de travail cloud ou analyste de la sécurité selon vos besoins.

SAM est un analyste de la sécurité et ses responsabilités :

- Source d’une attaque.
- Correction d’alertes.
- Utiliser les propriétaires de la charge de travail pour déterminer et appliquer des réductions.

Comme vous pouvez le voir, Karina et Sam ont différentes responsabilités, et ils doivent collaborer pour partager des informations du centre de sécurité.

## <a name="recommended-solution"></a>Solution recommandée

Dans la mesure où Karina et Sam ont des rôles différents, ils utiliserez dorénavant différentes parties du centre de sécurité pour obtenir des informations pertinentes sur leurs activités quotidiennes. Karina utiliseront les **alertes de sécurité** dans le cadre de ses surveillance quotidienne.

![Alertes de sécurité](./media/security-center-incident-response/security-center-incident-response-fig3.png)

Karina utiliseront les alertes de sécurité au cours des étapes détecter et évaluer. Karina après l’évaluation initiale, elle peut résoudre le problème à Sam si une enquête supplémentaire est requise. À ce stade, Sam utilisera les informations qui ont été fournies par le centre de sécurité, parfois conjointement avec d’autres sources de données, pour passer à l’étape diagnostiquer.


## <a name="how-to-implement-this-solution"></a>Comment mettre en œuvre cette solution

Pour voir comment utiliser le centre de sécurité Azure dans un scénario de réponse aux incidents, nous allons procédez de Karina au cours des étapes détecter et évaluer et puis voir quel est le Sam pour identifier le problème.

### <a name="detect-and-assess-incident-response-stages"></a>Détecter et évaluer les étapes de réponse aux incidents

Karina connecté au portail Azure et utilisation de la console Centre de sécurité. Dans le cadre de ses quotidien suivi des activités, elle en main de révision des alertes de sécurité avec une priorité élevée en effectuant les étapes suivantes :

1. Cliquez sur la vignette **d’alertes de sécurité** et accéder à la carte **d’alertes de sécurité** .
    ![Carte alerte de sécurité](./media/security-center-incident-response/security-center-incident-response-fig4.png)

    > [AZURE.NOTE] Aux fins de ce scénario, Karina va effectuer une évaluation dans l’alerte d’activité SQL malveillants, comme illustré dans l’illustration précédente.
2. Cliquez sur l’alerte **SQL malveillants activité** et passez en revue les ressources dans la carte **activité SQL malveillant** attaqués :  ![détails de l’Incident](./media/security-center-incident-response/security-center-incident-response-fig5.png)

    Dans cette carte Karina peut prendre des notes en ce qui concerne les ressources attaqués, comment autant de fois cette attaque s’est produite, et lorsqu’il a été détecté.
3. Cliquez sur **ressource une attaque** pour obtenir plus d’informations sur cette attaque.

Après avoir lu la description, Karina est convaincue qu’il ne s’agit pas d’un faux positif et qu’elle doit transmettre le cas présent SAM.

### <a name="diagnose-incident-response-stage"></a>Diagnostiquer scène de réponse aux incidents

SAM reçoit le cas de Karina et commence à consulter la procédure de mise à jour le centre de sécurité suggéré.

![Cycle de vie des incidents](./media/security-center-incident-response/security-center-incident-response-fig6.png)

### <a name="additional-resources"></a>Ressources supplémentaires

L’équipe de réponse aux incidents peut également tirer parti de la fonctionnalité de [Sécurité Centre Power BI](security-center-powerbi.md) pour afficher les différents types de rapports. Ces rapports peuvent aider les lors de l’enquête supplémentaire afin de visualiser, analyser et filtrer les recommandations et les alertes de sécurité. Pour les entreprises qui utilisent leurs informations de sécurité et la solution de gestion (SIEM) événement pendant le processus d’enquête, ils peuvent également [intégrer le centre de sécurité avec leurs solutions](security-center-integrating-alerts-with-log-integration.md). Vous pouvez également intégrer les journaux d’audit Azure et les événements de sécurité machine virtuelle () à l’aide de l' [outil d’intégration de journal Azure](https://blogs.msdn.microsoft.com/azuresecurity/2016/07/21/microsoft-azure-log-integration-preview/). Pour savoir si une attaque, vous pouvez utiliser ces informations en association avec les informations fournies par le centre de sécurité.


## <a name="conclusion"></a>Conclusion

Mise en place une équipe avant qu’un incident se produise est très important de votre organisation et positive influencent la gestion des incidents. Les outils appropriés pour surveiller les performances permet cette équipe à prendre des mesures précises pour mettre à jour un incident de sécurité. Centre de sécurité [des fonctionnalités de détection](security-center-detection-capabilities.md) peut aider rapidement répondre aux incidents de sécurité et de résoudre les problèmes de sécurité.

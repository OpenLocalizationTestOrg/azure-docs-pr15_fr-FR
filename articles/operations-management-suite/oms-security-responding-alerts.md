<properties
   pageTitle="Surveiller et répondre aux alertes de sécurité dans les opérations de sécurité Suite de gestion et d’Audit Solution | Microsoft Azure"
   description="Ce document vous permet d’utiliser l’option d’intelligence menace disponible dans OMS sécurité et d’Audit pour surveiller et répondre aux alertes de sécurité."
   services="operations-management-suite"
   documentationCenter="na"
   authors="YuriDio"
   manager="swadhwa"
   editor=""/>

<tags
   ms.service="operations-management-suite"
   ms.topic="article" 
   ms.devlang="na"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/18/2016"
   ms.author="yurid"/>

# <a name="monitoring-and-responding-to-security-alerts-in-operations-management-suite-security-and-audit-solution"></a>Surveiller et répondre aux alertes de sécurité dans opérations gestion Suite Solution de sécurité et d’Audit

Ce document vous permet d’utiliser l’option d’intelligence menace disponible dans OMS sécurité et d’Audit pour surveiller et répondre aux alertes de sécurité.

## <a name="what-is-oms"></a>Qu’est OMS ?

Suite de gestion des opérations Microsoft (OMS) repose sur cloud de Microsoft solution de gestion informatique qui vous permet de gérer et protéger vos localement et infrastructure en nuage. Pour plus d’informations sur OMS, consultez l’article [Opérations Management Suite](https://technet.microsoft.com/library/mt484091.aspx).

## <a name="threat-intelligence"></a>Informations sur les menaces

Dans un environnement où les utilisateurs ont un accès étendu au réseau et utilisent des appareils variés pour vous connecter aux données d’entreprise d’entreprise, il est essentiel que vous pouvez surveiller activement vos ressources et répondre rapidement à des incidents de sécurité. Il s’agit particulièrement important du point de vue de cycle de vie de sécurité, car certaines sécurité menaces ne peuvent pas porter des alertes ou des activités qui peuvent être identifiées par des contrôles techniques de sécurité traditionnel. 

En utilisant l’option **d’Informations sur les menaces** disponible dans sécurité OMS et d’Audit, les administrateurs informatiques peut identifier menaces contre l’environnement, par exemple, identifiez si un ordinateur particulier fait partie d’un [réseau de robots](https://www.microsoft.com/security/sir/story/default.aspx#!botnetsection). Ordinateurs peuvent devenir des nœuds dans un réseau de robots lorsque les pirates illégalement installer des logiciels malveillants qui secret se connecte cet ordinateur à la commande et le contrôle. Il peut également identifier les menaces potentielles en provenance de canaux de communication de fond, tels que [darknet](https://www.microsoft.com/security/sir/story/default.aspx#!botnetsection_honeypots_darkents). 

Pour créer ce menaces, OMS sécurité et Audit utilisent des données provenant de plusieurs sources au sein de Microsoft. Sécurité OMS et Audit exploiter les ces données pour identifier les menaces potentielles par rapport à votre environnement.

Le volet d’informations sur les menaces est composé par trois options principales :
- Serveurs avec le trafic malveillant sortant
- Types de menaces détectées
- Carte d’intelligence des menaces

> [AZURE.NOTE] pour une vue d’ensemble de toutes ces options, lisez [mise en route d’opérations gestion Suite de sécurité et Solution d’Audit](oms-security-getting-started.md).

### <a name="responding-to-security-alerts"></a>Répondre aux alertes de sécurité

Effectuez les étapes d’un processus de [réponse aux incidents de sécurité](https://technet.microsoft.com/library/cc512623.aspx) est d’identifier la gravité du ou des systèmes compromis. Dans cette phase, vous devez effectuer les tâches suivantes :

- Déterminer la nature de l’attaque
- Déterminer le point attaque d’origine
- Déterminer l’objectif de l’attaque. A été l’attaque spécifiquement dirigée contre votre organisation pour obtenir des informations spécifiques ou a été aléatoire ?
- Identifier les systèmes qui ont été compromis
- Identifier les fichiers qui ont accédé et déterminent le critère de diffusion de ces fichiers

Vous pouvez exploiter les informations **Sur les menaces** dans OMS solution de sécurité et d’Audit pour vous aider à effectuer ces tâches. Suivez les étapes ci-dessous pour accéder à ces options **Sur les menaces** :

1. Dans le tableau de bord principale **Microsoft Operations Management Suite** cliquez sur mosaïque de **sécurité et Audit** .

    ![Sécurité et Audit](./media/oms-security-responding-alerts/oms-security-responding-alerts-fig1.png)

2. Dans le tableau de bord de **sécurité et Audit** , vous verrez les options **Sur les menaces** de droite, comme indiqué ci-dessous :

    ![Menace intel](./media/oms-security-responding-alerts/oms-security-responding-alerts-fig2-ga.png)

Ces trois vignettes vous donne une vue d’ensemble des menaces en cours. Sur le **serveur avec le trafic malveillant sortant** que vous ne pourrez pas identifier s’il existe n’importe quel ordinateur que vous analysez (à l’intérieur ou en dehors de votre réseau) qui envoie le trafic malveillant à Internet. 

La vignette de **types de menaces détectés** affiche un résumé des menaces qui sont en cours « dans la nature », si vous cliquez sur cette vignette vous verrez plus d’informations sur ces menaces comme indiqué ci-dessous :

![Types de menace détectée](./media/oms-security-responding-alerts/oms-security-responding-alerts-fig3.png)

Vous pouvez extraire plus d’informations sur chaque menace en cliquant dessus. L’exemple ci-après affiche plus d’informations sur le réseau de robots :

![obtenir plus d’informations relatives à une menace](./media/oms-security-responding-alerts/oms-security-responding-alerts-fig4.png)

Comme décrit dans le début de cette section, ces informations peuvent être très utiles au cours d’un cas de réponse aux incidents. Il peut également être important pendant une investigation informatique, où vous devez trouver la source de l’attaque, système qui a été déchiffré et la chronologie. Dans ce rapport, vous pouvez facilement identifier certaines informations clés sur l’attaque, telles que : la source de l’attaque, l’adresse IP locale qui a été déchiffré et l’état de session en cours de la connexion. 

La **carte d’intelligence des menaces** vous aidera à identifier l’emplacement actuel du monde ayant le trafic malveillant. Il existe orange (entrant) et flèches (sortants) rouge dans ce mappage qui identifient la direction du trafic, si vous cliquez sur l’une de ces flèches, il affiche le type de menace et la direction du trafic comme indiqué ci-dessous :

![carte d’intel des menaces](./media/oms-security-responding-alerts/oms-security-responding-alerts-fig5.png)

> [AZURE.NOTE] Vous pouvez voir une démonstration sur l’utilisation de cette fonctionnalité pendant un incident de processus de réponse en regardant la présentation [menaces atténuer centre de données avec guidé enquête à l’aide de la Suite de gestion des opérations](https://myignite.microsoft.com/videos/5000) envoyées à Microsoft Ignite.

## <a name="see-also"></a>Voir aussi

Dans ce document, vous avez appris comment utiliser l’option **d’Informations sur les menaces** dans OMS solution de sécurité et d’Audit pour répondre aux alertes de sécurité. Pour en savoir plus sur la sécurité OMS, consultez les articles suivants :

- [Vue d’ensemble de la gestion des Suite (OMS) opérations](operations-management-suite-overview.md)
- [Prise en main opérations gestion Suite Solution de sécurité et d’Audit](oms-security-getting-started.md)
- [Suivi des ressources dans opérations gestion Suite Solution de sécurité et d’Audit](oms-security-monitoring-resources.md)

<properties
    pageTitle="Azure Active Directory hybride identité conception - vue d’ensemble | Microsoft Azure"
    description="Vue d’ensemble et carte de contenu de guide de considérations relatives à la conception d’identité hybride"
    documentationCenter=""
    services="active-directory"
    authors="billmath"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="identity" 
    ms.date="08/08/2016"
    ms.author="billmath"/>

# <a name="azure-active-directory-hybrid-identity-design-considerations"></a>Considérations relatives à la conception identité hybride Azure Active Directory

Appareils consommateur sont prolifération du monde entier d’entreprise, puis sur le nuage logiciel en tant que service (SaaS) applications sont faciles à adopter. Par conséquent, la conservation de contrôle d’accès des utilisateurs application plateformes d’interne centres de données et cloud est complexe.  Solutions d’identité de Microsoft couvrant en local et fonctionnalités de cloud, création d’une identité utilisateur unique pour l’authentification et l’autorisation à toutes les ressources, quel que soit l’emplacement. Nous appelons cette identité hybride. Il existe différente création et options de configuration d’identité hybride en utilisant les solutions Microsoft et dans certains cas, qu'il peut être difficile à déterminer quelle combinaison mieux répondre aux besoins de votre organisation. Ce Guide de considérations relatives à la conception hybride identité vous aidera à comprendre comment concevoir une solution d’identité hybride qui correspond le mieux à l’entreprise et technologie a besoin pour votre organisation.  Ce guide en détail une série d’étapes et les tâches que vous pouvez suivre pour vous aider à concevoir une solution d’identité hybride qui répond aux besoins de votre organisation. Dans l’ensemble de la procédure et les tâches, le guide présentera les technologies pertinents et les options de fonctionnalités disponibles pour les organisations à se réunir fonctionnelle et la qualité du service (par exemple, disponibilité, extensibilité élevées, performances, la facilité de gestion et de sécurité) niveau requis. En particulier, les objectifs hybride identité création considérations guide sont les questions suivantes : 

- Quelles sont les questions dois-je me poser et répondre pour piloter une conception hybride identité spécifiques pour un domaine technologie ou d’un problème qui répond à mes besoins ?
- Séquence d’activités dois-je effectuer pour concevoir une solution d’identité hybride pour le domaine de technologie ou d’un problème ? 
- Les options de configuration et technologie d’identité hybride sont disponibles pour m’aider à correspond à mes besoins ? Quelles sont les compromis entre ces options afin que je peux sélectionner l’option mon entreprise ?


## <a name="who-is-this-guide-intended-for"></a>Qui ce guide est destiné à
 Responsable de l’information, CITO, chef identité architectes, architectes d’entreprise, architectes informatiques responsable de la conception d’une solution d’identité hybride pour les moyennes et grandes entreprises.

## <a name="how-can-this-guide-help-you"></a>Comment ce guide peut vous aider ? 
Vous pouvez utiliser ce guide pour comprendre comment concevoir une solution d’identité hybride qui est en mesure d’intégrer un système de gestion d’identité basée sur le nuage avec votre solution identité locale actuelle. Le graphique suivant montre un exemple une solution d’identité hybride qui permet aux administrateurs informatiques à gérer pour intégrer leurs solutions Windows Server Active Directory actuelle situé locales avec Microsoft Azure Active Directory pour permettre aux utilisateurs d’utiliser l’authentification unique (SSO) entre les applications situées dans le nuage et en local.

![](./media/hybrid-id-design-considerations/hybridID-example.png)


L’illustration ci-dessus est un exemple d’une solution d’identité hybride qui est exploiter les services cloud pour intégrer des fonctionnalités locales afin de fournir une expérience unique pour le processus d’authentification de l’utilisateur final et de faciliter informatique gestion de ces ressources. Bien que cela peut être très fréquent, conception d’identité de chaque organisation hybride est susceptible d’être différent de celui de l’exemple illustré dans la Figure 1 en raison des exigences différentes. Ce guide fournit une série d’étapes et les tâches que vous pouvez suivre pour concevoir une solution d’identité hybride qui répond aux besoins de votre organisation. Dans l’ensemble les étapes suivantes et les tâches, le guide présente les technologies pertinents et les options des fonctionnalités disponibles pour répondre aux exigences de qualité de niveaux de service pour votre organisation et fonctionnel.

**Hypothèses**: avoir une certaine expérience avec Windows Server, Active Directory Domain Services et Azure Active Directory. Dans ce document, nous part du principe que vous recherchez comment ces solutions peuvent répondre aux besoins de votre entreprise dans leurs propres ou dans une solution intégrée.

## <a name="design-considerations-overview"></a>Vue d’ensemble des considérations relatives à la conception
Ce document fournit un ensemble d’étapes et les tâches que vous pouvez suivre pour concevoir une solution d’identité hybride qui correspond le mieux à vos besoins. Les étapes sont présentées dans un ordre donné. Considérations relatives à la conception que vous découvrez dans les étapes suivantes peuvent nécessiter vous permet de modifier les décisions que vous prenez dans les étapes précédentes, cependant, en raison des choix de conception en conflit. Chaque tentative pour vous signaler les conflits de conception potentiels dans l’ensemble du document. 

Vous arrivera à la conception qui répond à vos besoins en matière uniquement une fois l’itération les étapes autant de fois que nécessaire pour intégrer toutes les considérations au sein du document. 

| Phase d’identité hybride                                             | Liste des rubriques                                                                                                                                                                                       |
|-------------------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Déterminer les besoins d’identité                                   | [Déterminer les besoins professionnels](active-directory-hybrid-identity-design-considerations-business-needs.md)<br> [Déterminer les besoins de synchronisation d’annuaire](active-directory-hybrid-identity-design-considerations-directory-sync-requirements.md)<br> [Déterminer la configuration requise pour l’authentification multifacteur](active-directory-hybrid-identity-design-considerations-multifactor-auth-requirements.md)<br> [Définir une stratégie d’adoption identité hybride](active-directory-hybrid-identity-design-considerations-identity-adoption-strategy.md)                       |
| Planifier pour renforcer la sécurité des données via la solution forte identité | [Déterminer les besoins de protection des données](active-directory-hybrid-identity-design-considerations-dataprotection-requirements.md) <br> [Déterminer les besoins de gestion de contenu](active-directory-hybrid-identity-design-considerations-contentmgt-requirements.md)<br> [Déterminer les exigences de contrôle d’accès](active-directory-hybrid-identity-design-considerations-accesscontrol-requirements.md)<br> [Déterminer les besoins de réponse aux incidents](active-directory-hybrid-identity-design-considerations-incident-response-requirements.md) <br> [Définir la stratégie de protection des données](active-directory-hybrid-identity-design-considerations-data-protection-strategy.md)  |
| Plan du cycle de vie d’identité hybride                                | [Déterminer les tâches de gestion des identités hybride](active-directory-hybrid-identity-design-considerations-hybrid-id-management-tasks.md) <br> [Gestion de la synchronisation](active-directory-hybrid-identity-design-considerations-hybrid-id-management-tasks.md)<br> [Déterminer la stratégie adoption de gestion des identités hybride](active-directory-hybrid-identity-design-considerations-lifecycle-adoption-strategy.md) |     


##<a name="download-this-guide"></a>Téléchargez ce guide
Vous pouvez télécharger une version pdf du guide de conception hybride identité à partir de la [Galerie Technet](https://gallery.technet.microsoft.com/Azure-Hybrid-Identity-b06c8288). 

                                                             

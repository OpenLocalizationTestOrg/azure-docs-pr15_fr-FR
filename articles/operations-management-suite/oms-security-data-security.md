<properties
   pageTitle="Opérations de sécurité Suite de gestion et de sécurité des données d’Audit Solution | Microsoft Azure"
   description="Ce document explique comment les données sont gérées et sauvegardées dans opérations gestion Suite Solution de sécurité et d’Audit."
   services="operations-management-suite"
   documentationCenter="na"
   authors="YuriDio"
   manager="swadhwa"
   editor=""/>

<tags
   ms.service="operations-management-suite"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/15/2016"
   ms.author="yurid"/>

# <a name="operations-management-suite-security-and-audit-solution-data-security"></a>Sécurité des données solution opérations gestion Suite de sécurité et d’Audit

Pour aider les clients prévenir, détecter et répondre aux menaces, [opérationnelle gestion de la sécurité Suite (OMS) et Solution d’Audit](operations-management-suite-overview.md) collecte et traite les données relatives à vos ressources, qui inclut :

- Journal des événements de sécurité
- Événements de suivi pour Windows (ETW)
- Événements d’audit AppLocker
- Journal du pare-feu Windows
- Événements de menace Analytique avancés
- Résultats de l’évaluation de référence
- Résultats de l’évaluation contre les logiciels malveillants
- Résultats de l’évaluation de mise à jour/correctifs
- Flux de données alertes n’est activés explicitement sur l’agent

Nous rendre engagements forts pour protéger la confidentialité et la sécurité de ces données. Microsoft est conforme aux directives de sécurité et conformité strictes — depuis le codage à un service.
Cet article explique comment les données sont gérées et sauvegardées dans OMS Solution de sécurité et d’Audit.

## <a name="data-sources"></a>Sources de données

OMS Solution de sécurité et d’Audit analysent des données à partir du Machines virtuelles et physiques ordinateurs sur lesquels l’Agent OMS est installé. OMS Solution de sécurité et d’Audit recueille des informations de configuration des événements de sécurité, tels que des événements Windows, les journaux d’audit, les journaux IIS et les messages journal système. Voici quelques exemples de ce type de données : type de système d’exploitation et la version, en cours d’exécution des processus, nom de l’ordinateur, adresses IP, connecté utilisateur et l’ID de client.  

## <a name="data-protection"></a>Protection des données

**Répartition des données**: les données sont conservées logiquement séparées sur chaque composant dans l’ensemble du service. Toutes les données sont référencées par l’organisation. Ce marquage persiste tout au long du cycle de vie des données, et elle est appliquée à chaque calque du service. 

**Accès aux données**: pour fournir des recommandations de sécurité et recherchez des menaces de sécurité potentielles, personnel de Microsoft peut accéder aux informations collectées ou analysées par les services. Nous respecter les [Termes du contrat Microsoft Online Services](http://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=31) et la [Déclaration de confidentialité](https://www.microsoft.com/privacystatement/en-us/OnlineServices/Default.aspx), état dans lequel que Microsoft ne doit pas utiliser les données client ou obtenir les informations à partir de celui-ci à des fins commerciales publicités ou similaires. Pour fournir des recommandations de sécurité et recherchez des menaces de sécurité potentielles, personnel de Microsoft peut-être accéder aux informations collectées ou analysées par les services. Nous utilisons uniquement les données client de selon vos besoins pour vous fournir des services Azure, y compris à des fins compatibles avec assurant ces services. Conservation de tous les droits à vos propres données.

**Utilisation des données**: Microsoft utilise des modèles et menaces vu entre plusieurs clients afin d’améliorer nos capacités de détection et de prévention ; nous le faire conformément aux engagements confidentialité décrites dans notre [Déclaration de confidentialité](https://www.microsoft.com/privacystatement/en-us/OnlineServices/Default.aspx).

> [AZURE.NOTE] Emplacement des données est configuré au niveau de l’espace de travail OMS, lors de la création d’espace de travail, qui fait partie du processus de configuration initial OMS sécurité et d’Audit.

## <a name="see-also"></a>Voir aussi

Dans ce document, vous avez appris comment les données sont gérées et sauvegardées dans OMS. Pour en savoir plus sur la solution OMS sécurité et d’Audit, voir :

- [Vue d’ensemble de la gestion des Suite (OMS) opérations](operations-management-suite-overview.md)
- [Surveiller et répondre aux alertes de sécurité dans opérations gestion Suite Solution de sécurité et d’Audit](oms-security-responding-alerts.md)
- [Suivi des ressources dans opérations gestion Suite Solution de sécurité et d’Audit](oms-security-monitoring-resources.md)


<properties
    pageTitle="Stratégies de rétention Azure Active Directory rapport | Microsoft Azure"
    description="Stratégies de rétention des données de rapport dans votre Azure Active Directory"
    services="active-directory"
    documentationCenter=""
    authors="dhanyahk"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="identity"
    ms.date="03/07/2016"
    ms.author="dhanyahk"/>

# <a name="azure-active-directory-report-retention-policies"></a>Stratégies de rétention Azure Active Directory rapport

*Cette documentation fait partie du [Guide Azure Active Directory création de rapports](active-directory-reporting-guide.md).*

Azure Active Directory (AD Azure) rapports conservent les données pendant un certain nombre de jours, indiqué ici.

Rapport                                                  | Description
------------------------------------------------------- | -----------
Signe-ins provenant de sources inconnues                           | 30 jours
Signe-ins après plusieurs échecs                        | 30 jours
Signe-ins à partir de plusieurs zones géographiques                      | 30 jours
Signe-ins provenant d’adresses IP avec une activité suspecte     | 30 jours
Signe-ins à partir d’appareils éventuellement infectés                 | 30 jours
Activité de connexion irrégulier                              | 30 jours
Utilisateurs avec anormale connexion à une activité                   | 30 jours
Utilisateurs avec des informations d’identification perdues                           | 30 jours
Rapport d’audit                                            | 180 jours
Mot de passe réinitialisé activité (Azure AD)                      | 30 jours
Mot de passe réinitialisé activité (identité Manager)              | 30 jours
Mot de passe réinitialisé l’activité d’enregistrement (Azure AD)         | 30 jours
Mot de passe réinitialisé l’activité d’enregistrement (Gestionnaire d’identité) | 30 jours
Activité de groupes (Azure AD) en libre service                 | 30 jours
Activité de groupes (identité Manager) en libre service         | 30 jours
Utilisation de l’application                                       | 30 jours
Mise en service d’activité du compte                           | 30 jours
État de survol de mot de passe                                | 30 jours
Erreurs de mise en service du compte                             | 30 jours
Utilisation de RMS                                               | 30 jours
Utilisateurs RMS plus actifs                                   | 30 jours
Utilisation du périphérique RMS                                        | 30 jours
Utilisation des applications activées RMS                           | 30 jours

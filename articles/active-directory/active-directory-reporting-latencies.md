<properties
   pageTitle="Azure Active Directory Reporting latence | Microsoft Azure"
   description="Durée que nécessaire pour que les événements de création de rapports s’affiche dans votre Azure Active Directory"
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

# <a name="azure-active-directory-report-latencies"></a>Latence de rapport Azure Active Directory

*Cette documentation fait partie du [Guide Azure Active Directory création de rapports](active-directory-reporting-guide.md).*

Rapport                                                  | Minimum  | Moyenne    | Valeur maximale
------------------------------------------------------- | -------- | ---------- | ----------
**Rapports de sécurité**                                    |          |            |
Activité de connexion irrégulier                              | 2 heures  | 4 heures    | 8 heures
Signe-ins provenant de sources inconnues                           | 2 heures  | 4 heures    | 8 heures
Signe-ins après plusieurs échecs                        | 2 heures  | 4 heures    | 8 heures
Signe-ins à partir de plusieurs zones géographiques                      | 2 heures  | 4 heures    | 8 heures
Signe-ins provenant d’adresses IP avec une activité suspecte     | 2 heures  | 4 heures    | 8 heures
Signe-ins à partir d’appareils éventuellement infectés                 | 2 heures  | 4 heures    | 8 heures
Utilisateurs avec anormale connexion à une activité                   | 2 heures  | 4 heures    | 8 heures
Utilisateurs avec des informations d’identification perdues                           | 2 heures  | 4 heures    | 8 heures
**Rapports de l’application**                                 |          |            |
Mise en service d’activité du compte                           | 2 heures  | 4 heures    | 8 heures
Erreurs de mise en service du compte                             | 2 heures  | 4 heures    | 8 heures
Utilisation de l’application                                       | 2 heures  | 4 heures    | 8 heures
État de survol de mot de passe                                | 2 heures  | 4 heures    | 8 heures
**D’audit et les rapports d’activité**                            |          |            |
Rapport d’audit                                            | minute | 15 minutes | 30 minutes
Mot de passe réinitialisé activité (Azure AD)                      | 2 heures  | 4 heures    | 8 heures
Mot de passe réinitialisé activité (identité Manager)              | 2 heures  | 4 heures    | 8 heures
Mot de passe réinitialisé l’activité d’enregistrement (Azure AD)         | 2 heures  | 4 heures    | 8 heures
Mot de passe réinitialisé l’activité d’enregistrement (Gestionnaire d’identité) | 2 heures  | 4 heures    | 8 heures
Activité de groupes (Azure AD) en libre service                 | 2 heures  | 4 heures    | 8 heures
Activité de groupes (identité Manager) en libre service         | 2 heures  | 4 heures    | 8 heures
**Rapports RMS**                                         |          |            |
Utilisateurs RMS plus actifs                                   | 2 heures  | 4 heures    | 8 heures
Utilisation de RMS                                               | 2 heures  | 4 heures    | 8 heures
Utilisation du périphérique RMS                                        | 2 heures  | 4 heures    | 8 heures
Utilisation des applications activées RMS                           | 2 heures  | 4 heures    | 8 heures
**Rapports d’aperçu privé**                             |          |            |
Toutes les activités de connexion utilisateur                               | 2 heures  | 4 heures    | 8 heures

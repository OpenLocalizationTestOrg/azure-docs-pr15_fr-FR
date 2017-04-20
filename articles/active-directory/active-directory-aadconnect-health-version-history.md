<properties
    pageTitle="Azure AD Connect historique des versions d’intégrité"
    description="Ce document décrit les versions pour Azure AD connecter santé et ce qui a été inclus dans ces versions."
    services="active-directory"
    documentationCenter=""
    authors="karavar"
    manager="samueld"
    editor="curtand"/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/18/2016"
    ms.author="vakarand"/>

# <a name="azure-ad-connect-health-version-release-history"></a>Azure AD Connect d’intégrité : Version historique des versions

L’équipe Azure Active Directory met à jour régulièrement la santé Azure AD se connecter avec les nouvelles fonctionnalités. Cet article répertorie les versions et les fonctionnalités qui ont été publiées.

## <a name="october-2016"></a>Octobre 2016
**Mise à jour de l’agent :**
- Azure agent d’intégrité de se connecter AD pour AD FS \(version 2.6.408.0\)
    1. Améliorations de la détection des adresses IP des clients dans les requêtes d’authentification
    2. Correctifs des alertes
- Azure agent d’intégrité de se connecter AD pour AD DS (version 2.6.408.0)
    1. Correctifs des alertes.
- Azure agent d’intégrité de se connecter AD pour la synchronisation (version 2.6.353.0) fournie avec Azure AD Connect version 1.1.281.0
    1. Fournir les informations requises pour les rapports d’erreurs de synchronisation
    2. Correctifs des alertes

**Nouvelles fonctionnalités d’aperçu :**
- Rapports d’erreurs de synchronisation pour Azure AD se connecter

**Nouvelles fonctionnalités :**
- Azure AD connecter santé pour AD FS - champ adresse IP est disponible dans le rapport sur 50 utilisateurs avec nom d’utilisateur/mot de passe incorrect.

## <a name="july-2016"></a>Juillet 2016

**Nouvelles fonctionnalités d’aperçu :**

- [Azure AD Connect santé pour les services AD DS](active-directory-aadconnect-health-adds.md).


## <a name="january-2016"></a>Janvier 2016


**Mise à jour de l’agent :**

- Azure agent d’intégrité de se connecter AD pour AD FS (version 2.6.91.1512)


**Nouvelles fonctionnalités :**

- [Outil de test de connectivité pour Azure AD connecter Agents d’intégrité](active-directory-aadconnect-health-agent-install.md#test-connectivity-to-azure-ad-connect-health-service)


## <a name="november-2015"></a>Novembre 2015


**Nouvelles fonctionnalités :**

- Prise en charge pour [le contrôle d’accès basé sur un rôle](active-directory-aadconnect-health-operations.md#manage-access-with-role-based-access-control)


**Nouvelles fonctionnalités d’aperçu :**

- [Azure AD se connecter au niveau pour la synchronisation](active-directory-aadconnect-health-sync.md).

**Problèmes résolus :**

- Correctifs d’erreurs visibles pendant les enregistrements de l’agent.

## <a name="september-2015"></a>Septembre 2015

**Nouvelles fonctionnalités :**

- Incorrect rapport de mot de passe nom d’utilisateur pour AD FS
- Prise en charge pour configurer non authentifiés Proxy HTTP
- Prise en charge pour configurer l’agent sur Server core
- Améliorations apportées aux alertes pour AD FS
- Téléchargement des améliorations dans Azure Agent d’intégrité connecter AD pour AD FS pour la connectivité et de données.


**Problèmes résolus :**

- Correctifs dans analyse de l’utilisation pour les types d’erreur AD FS.


## <a name="june-2015"></a>Juin 2015

**Version initiale de la santé connecter Azure AD pour AD FS et Proxy AD FS.**

**Nouvelles fonctionnalités :**

- Alertes de surveillance des serveurs AD FS et Proxy AD FS avec les notifications par courrier électronique.
- Faciliter l’accès à la topologie AD FS et modèles dans compteurs AD FS.
- Tendance de demandes jetons réussies sur des serveurs AD FS regroupés par Applications, les méthodes d’authentification, etc. d’emplacement réseau demander.
- L’évolution des demandes qui ont échoué sur des serveurs AD FS regroupés par des Applications, etc. des Types d’erreur.
- Déploiement de l’Agent plus simple à l’aide des informations d’identification d’administrateur général AD Azure.  




## <a name="next-steps"></a>Étapes suivantes
Apprenez-en davantage sur [l’Analyseur vos localement infrastructure et la synchronisation services d’identité dans le cloud](active-directory-aadconnect-health.md).

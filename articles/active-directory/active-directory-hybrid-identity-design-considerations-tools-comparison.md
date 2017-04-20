<properties
    pageTitle="Identité hybride : Intégration d’annuaire outils Comparaison | Microsoft Azure"
    description="Ceci est la page fournit un tableau complet qui compare les outils d’intégration répertoire différentes pouvant être utilisées pour l’intégration d’annuaire."
    services="active-directory"
    documentationCenter=""
    authors="billmath"
    manager="femila"
    editor="curtand"/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="08/08/2016"
    ms.author="billmath"/>

# <a name="hybrid-identity-directory-integration-tools-comparison"></a>Intégration d’annuaire hybride identité outils de comparaison

Années les outils d’intégration répertoire ont se développée et tiré de la diapositive.  Ce document est afin de fournir une vue d’ensemble de ces outils et une comparaison des fonctionnalités qui sont disponibles dans chacun.

<!-- The hardcoded link is a workaround for campaign ids not working in acom links-->

>[AZURE.NOTE] Azure AD Connect intègre les composants et les fonctionnalités publiées précédemment sous forme de synchronisation d’annuaire et AAD Sync. Ces outils sont publiées n’est plus individuellement, et toutes les futures améliorations sont incluses dans les mises à jour pour Azure AD Connect, afin que vous savez toujours où obtenir les dernières fonctionnalités.
>
>Synchronisation d’annuaire et Azure AD Sync sont déconseillées. Vous trouverez plus d’informations dans [ici](active-directory-aadconnect-dirsync-deprecated.md).


Utilisez la clé suivante pour chacune des tables.

● = Disponible maintenant  
FR = version ultérieure  
Pages = version d’évaluation  

## <a name="on-premises-to-cloud-synchronization"></a>Local vers le Cloud synchronisation

| Fonctionnalité  | Azure Active Directory se connecter  | Services de synchronisation Azure Active Directory AAD Sync) | Outil de synchronisation Azure Active Directory (DirSync)| Forefront Identity Manager 2010 R2 (FIM) |Gestionnaire d’identité Microsoft 2016 (MIM)|
| :-------- |:--------:|:--------:|:--------:|:--------:|:--------:
| Se connecter à la même forêt Active Directory local | ● | ● | ● | ● |● |
| Se connecter à plusieurs forêts Active Directory local |●  | ● |  | ● |● |
| Se connecter à plusieurs organisations de Exchange en local | ● |  |  |  | |
| Se connecter à l’annuaire LDAP unique en local | FR |  |  | ● |● |
| Se connecter à plusieurs annuaires LDAP en local |FR  |  |  | ● |● |
| Se connecter à Active Directory local et annuaires LDAP local |FR  |  |  | ● |● |
| Se connecter à des systèmes personnalisés (c'est-à-dire SQL, Oracle, MySQL, etc.) | FR |  |  | ● |● |
| Synchroniser les attributs définies par le client (extensions directory) | ● |  |  |  |  |
|Se connecter à HR locaux (c'est-à-dire, SAP, Oracle e-Business, PeopleSoft)| FR |  |  | ● |● |
|Prend en charge les règles de synchronisation FIM et des connecteurs de mise en service pour les systèmes locaux.|  |  |  | ● |● |

## <a name="cloud-to-on-premises-synchronization"></a>Nuage à la synchronisation locale

| Fonctionnalité  | Azure Active Directory se connecter  | Services de synchronisation Azure Active Directory | Outil de synchronisation Azure Active Directory (DirSync) | Forefront Identity Manager 2010 R2 (FIM) |Gestionnaire d’identité Microsoft 2016 (MIM)|
| :-------- |:--------:|:--------:|:--------:|:--------:|:--------:
| Écriture différée des appareils | ● |  | ● |  ||
| Attribut d’écriture différée (pour le déploiement Exchange hybride) | ● | ● | ● | ● |● |
| Écriture différée d’objets utilisateurs et groupes |  ●|  | |  ||
| Écriture différée des mots de passe (de réinitialisation du mot de passe libre-service (SSPR) et modifier votre mot de passe) |  ● | ● |  |  ||



## <a name="authentication-feature-support"></a>Prise en charge des fonctionnalités d’authentification

| Fonctionnalité  | Azure Active Directory se connecter | Services de synchronisation Azure Active Directory | Outil de synchronisation Azure Active Directory (DirSync) | Forefront Identity Manager 2010 R2 (FIM) |Gestionnaire d’identité Microsoft 2016 (MIM)|
| :-------- |:--------:|:--------:|:--------:|:--------:|:--------:
| Synchronisation de mot de passe pour unique local forêt Active Directory | ● | ● | ● |  ||
| Synchronisation de mot de passe pour plusieurs locaux forêts AD |  ●| ● |  |  ||
| Authentification unique avec la fédération | ● | ● | ● | ● |● |
| Écriture différée des mots de passe (à partir de modification SSPR et mot de passe) |●  | ● |  |  ||



## <a name="set-up-and-installation"></a>Installation et configuration

| Fonctionnalité  | Azure Active Directory se connecter  | Services de synchronisation Azure Active Directory | Outil de synchronisation Azure Active Directory (DirSync) | Gestionnaire d’identité Microsoft 2016 (MIM) |
| :-------- |:--------:|:--------:|:--------:|:--------:
| Prend en charge l’installation sur un contrôleur de domaine | ● | ● | ● |  |
| Prend en charge l’installation à l’aide de SQL Express | ● | ● | ● |  |
| Mise à niveau à partir de la synchronisation d’annuaire |● |  |  |  |
| Localisation d’administrateur UX langues Windows Server | ● | ● | ● |  |
|Localisation d’utilisateur final UX langues Windows Server| |  |  |● |
| Prise en charge de Windows Server 2008 et Windows Server 2008 R2 | ● pour la synchronisation, ne pour la fédération| ● | ●  | ● |
| Prise en charge de Windows Server 2012 et Windows Server 2012 R2 | ● | ● | ● | ● |

## <a name="filtering-and-configuration"></a>Filtrage et Configuration

Fonctionnalité  | Azure Active Directory se connecter | Services de synchronisation Azure Active Directory | Outil de synchronisation Azure Active Directory (DirSync) | Forefront Identity Manager 2010 R2 (FIM)| Gestionnaire d’identité Microsoft 2016 (MIM)
:-------- |:--------:|:--------:|:--------:|:--------:|:--------:|
Filtrer sur les domaines et unités d’organisation | ● | ● | ● | ●  | ●
Filtrer les valeurs des attributs des objets | ● | ● | ● | ●| ●
Autoriser l’ensemble minimal des attributs à être synchronisé (MinSync) | ● | ● |  ||
Autoriser les modèles de services différente à appliquer dans les flux d’attributs |●  | ● |  ||
Autoriser la suppression des attributs de déborder depuis Active Directory vers Azure Active Directory | ● | ● |  |  |
Autoriser la personnalisation avancée pour les flux d’attributs | ● | ● |  | ●  | ●

## <a name="next-steps"></a>Étapes suivantes
En savoir plus sur [l’intégration de vos identités locales avec Azure Active Directory](active-directory-aadconnect.md).

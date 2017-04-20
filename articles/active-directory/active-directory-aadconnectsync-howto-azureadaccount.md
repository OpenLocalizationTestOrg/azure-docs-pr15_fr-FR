<properties
    pageTitle="Azure AD Connect synchronisation : comment gérer le compte de service Azure AD | Microsoft Azure"
    description="Cette rubrique décrit comment restaurer le compte de service Azure AD."
    services="active-directory"
    keywords="AADSTS70002, AADSTS50054, comment faire pour réinitialiser le mot de passe pour la compte de service de connecteur de synchronisation Azure AD Connect"
    documentationCenter=""
    authors="andkjell"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/01/2016"
    ms.author="billmath"/>

# <a name="azure-ad-connect-sync-how-to-manage-the-azure-ad-service-account"></a>Azure AD Connect synchronisation : comment gérer le compte de service Azure AD
Le compte de service utilisé par le connecteur AD Azure est censée pour être service gratuit. Si vous avez besoin rétablir ses informations d’identification, cette rubrique concerne les vous. Par exemple, si un administrateur général a par erreur réinitialiser le mot de passe du compte de service à l’aide de PowerShell.

## <a name="reset-the-credentials"></a>Réinitialiser les informations d’identification
Si le compte de service défini Azure AD Connector ne peut pas contacter Azure AD en raison de problèmes d’authentification, le mot de passe peut être réinitialisé.

1. Se connecter au serveur de synchronisation Azure AD Connect et démarrer PowerShell.
2. Exécuter `Add-ADSyncAADServiceAccount`.  
![Addadsyncaadserviceaccount d’applet de commande PowerShell](./media/active-directory-aadconnectsync-howto-azureadaccount/addadsyncaadserviceaccount.png)
3. Fournir des informations d’identification d’administrateur général Azure AD.

Cette applet de commande Réinitialiser le mot de passe du compte de service et mettre à jour dans Azure AD et dans le moteur de synchronisation.

## <a name="known-issues-these-steps-can-solve"></a>Cette procédure peut résoudre les problèmes connus
Cette section est une liste des messages d’erreur signalés par les clients qui ont été corrigés par une réinitialisation sur le compte de service Azure AD les informations d’identification.

-----------
Événement 6900  
Le serveur a rencontré une erreur inattendue pendant le traitement d’une notification de modification du mot de passe :  
AADSTS70002 : Erreur validation informations d’identification. AADSTS50054 : Ancien mot de passe est utilisé pour l’authentification.

----------
Événement 659  
Erreur lors de la récupération de configuration de synchronisation de stratégie de mot de passe. Microsoft.IdentityModel.Clients.ActiveDirectory.AdalServiceException :  
AADSTS70002 : Erreur validation informations d’identification. AADSTS50054 : Ancien mot de passe est utilisé pour l’authentification.

## <a name="next-steps"></a>Étapes suivantes

**Rubriques de présentation**

- [Azure AD Connect synchronisation : comprendre et personnaliser la synchronisation](active-directory-aadconnectsync-whatis.md)
- [Intégration de vos identités locales avec Azure Active Directory](active-directory-aadconnect.md)

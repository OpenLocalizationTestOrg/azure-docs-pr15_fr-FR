<properties
   pageTitle="Azure AD Connect : Fonctionnalités dans l’aperçu | Microsoft Azure"
   description="Cette rubrique décrit dans les autres fonctionnalités de détail qui se trouvent dans Aperçu dans Azure AD Connect."
   services="active-directory"
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
   ms.date="06/27/2016"
   ms.author="billmath"/>

# <a name="more-details-about-features-in-preview"></a>Obtenir plus d’informations sur les fonctionnalités d’aperçu
Cette rubrique décrit comment utiliser les fonctionnalités actuellement dans l’aperçu.

## <a name="group-writeback"></a>Groupe écriture différée non validée
L’option pour l’écriture différée de groupe dans des fonctionnalités facultatives vous permettra d’écriture différée **Groupes Office 365** à une forêt avec Exchange est installé. Il s’agit d’un groupe qui est toujours maîtriser dans le cloud. Si vous avez Exchange en local, puis vous pouvez répondre ces groupes vers local donc utilisateurs avec une boîte aux lettres locale Exchange peuvent envoyer et recevoir des messages électroniques à partir de ces groupes.

Vous pouvez trouver plus d’informations sur les groupes Office 365 et comment les utiliser [ici](http://aka.ms/O365g).

Ce groupe est représenté sous forme d’un groupe de distribution en local les services AD DS. Votre serveur Exchange local doit être mise à jour cumulative Exchange 2013 8 (publiée en mars 2015) ou Exchange 2016 reconnaître ce nouveau type de groupe.

**Notes lors de la visualisation**

- L’attribut de carnet d’adresses n’est actuellement pas remplie dans l’aperçu. Sans cet attribut, le groupe ne sera pas visible dans la liste d’adresses globale. Pour remplir cet attribut le plus simple consiste à utiliser l’applet de commande PowerShell Exchange `update-recipient`.
- Seules les forêts avec le schéma Exchange sont valides cibles pour les groupes. Si aucun Exchange a été détecté, groupe écriture différée ne sera pas possible d’activer.
- Uniquement les déploiements d’organisation basée sur une forêt Exchange sont actuellement prises en charge. Si vous avez plusieurs Exchange organisation locale, vous devrez une solution GALSync locale pour ces groupes doivent apparaître dans vos autres forêts.
- La fonctionnalité d’écriture différée groupe ne traite pas actuellement les groupes de sécurité ou groupes de distribution.

>[AZURE.NOTE] Un abonnement à Azure AD Premium est requis pour l’écriture différée du groupe.

## <a name="user-writeback"></a>Écriture différée utilisateur
> [AZURE.IMPORTANT] La fonctionnalité d’aperçu d’écriture différée utilisateur a été supprimée dans la mise à jour août 2015 Azure AD Connect. Si vous l’avez activé, vous devez désactiver cette fonctionnalité.

## <a name="next-steps"></a>Étapes suivantes
Poursuivez votre [installation personnalisée de Azure AD Connect](./connect/active-directory-aadconnect-get-started-custom.md).

En savoir plus sur [l’intégration de vos identités locales avec Azure Active Directory](active-directory-aadconnect.md).

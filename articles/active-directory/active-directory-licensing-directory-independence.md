<properties
   pageTitle="Ajouter et gérer plusieurs annuaires Azure Active Directory | Microsoft Azure"
   description="Instructions et des pratiques recommandées pour l’ajout et la gestion de vos répertoires Azure Active Directory, expliquant annuaires comme un entièrement indépendante des ressources"
   services="active-directory"
   documentationCenter=""
   authors="curtand"
   manager="femila"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="08/23/2016"
   ms.author="curtand"/>

# <a name="add-and-manage-multiple-azure-active-directory-directories"></a>Ajouter et gérer plusieurs annuaires Azure Active Directory

Dans Azure Active Directory (AD Azure), chaque répertoire est une ressource entièrement indépendante : un homologue, complet et logiquement indépendant des autres annuaires que vous gérez. Il n’y a aucune relation parent-enfant entre les répertoires. Cette indépendance entre annuaires inclut indépendance ressource, indépendance d’administration et indépendance de synchronisation.

##<a name="resource-independence"></a>Indépendance des ressources

Si vous créez ou supprimez une ressource d’un annuaire, il a aucun impact sur n’importe quelle ressource dans un autre répertoire, à l’exception partielle des utilisateurs externes, décrite ci-dessous. Si vous utilisez un domaine personnalisé « contoso.com » avec un répertoire, il ne peut pas être utilisé avec n’importe quel autre répertoire.

##<a name="administrative-independence"></a>Indépendance d’administration.

Si un utilisateur non administratif du répertoire « Contoso » crée un répertoire de test « Test » puis :
- Par défaut, l’utilisateur qui crée un répertoire est ajouté sous la forme d’un utilisateur externe de ce nouveau répertoire et du rôle d’administrateur global dans ce répertoire.
- Les administrateurs du répertoire « Contoso » pas sans privilèges directs au répertoire « Test », sauf si un administrateur de « Test » leur accorde spécifiquement ces privilèges. Les administrateurs de « Contoso » peuvent contrôler l’accès au répertoire « Test » si qu’ils contrôlent le compte d’utilisateur qui a créé « Test ».
- Si vous changez (ajouter ou supprimer) un rôle d’administrateur pour un utilisateur dans un répertoire, la modification n’affecte pas un rôle d’administrateur que l’utilisateur peut avoir dans un autre répertoire.

##<a name="synchronization-independence"></a>Indépendance de synchronisation

Vous pouvez configurer chaque répertoire Azure AD séparément pour obtenir des données synchronisées à partir d’une seule instance de des options :
  - L’outil de synchronisation d’annuaires (DirSync), pour synchroniser des données avec une seule forêt AD.
  - Azure Active Directory Connector pour Forefront Identity Manager, pour synchroniser des données avec un ou plusieurs forêts en local, et/ou des sources de données non Azure AD.

##<a name="add-an-azure-ad-directory"></a>Ajouter un répertoire Azure AD

Pour ajouter un répertoire Azure AD dans le portail classique Azure, sélectionnez l’extension Azure Active Directory sur la gauche, appuyez sur **Ajouter**.

> [AZURE.NOTE]   Contrairement à d’autres ressources Azure, vos répertoires ne sont pas ressources enfants d’un abonnement Azure. Si vous annulez ou permettre à votre abonnement Azure arrive à expiration, vous pouvez toujours accéder aux données de votre annuaire à l’aide de PowerShell Azure, l’API Graph Azure ou les autres interfaces telles que le centre d’administration Office 365. Vous pouvez également associer un autre abonnement avec l’annuaire.

Pour obtenir une vue d’ensemble des problèmes de licence Azure AD et des pratiques recommandées, voir [qu’est licences Azure Active Directory ?](active-directory-licensing-what-is.md).

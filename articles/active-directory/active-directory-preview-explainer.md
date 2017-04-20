<properties
    pageTitle="Explicateur de preview Azure Active Directory | Microsoft Azure"
    description="Rubrique explique la différence entre Azure Active Directory dans le portail classique et l’aperçu Azure Active Directory dans le portail Azure."
    services="active-directory"
    documentationCenter=""
    authors="curtand"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/12/2016"
    ms.author="curtand"/>


# <a name="preview-of-the-azure-active-directory-management-experience-in-the-azure-portal"></a>Aperçu de l’expérience de gestion Azure Active Directory dans le portail Azure

L’expérience de gestion Azure Active Directory (AD Azure) est dans l’aperçu du portail Azure. Vous pouvez l’essayer arrière en vous connectant au [portail Azure](https://portal.azure.com) comme un administrateur global de votre répertoire. Ensuite, sélectionnez Azure Active Directory dans la liste des services s’il est visible, ou sélectionnez **plusieurs services** pour afficher la liste de tous les services. Vous n’avez pas besoin d’un abonnement à utiliser la Azure Azure expérience de gestion Active Directory dans le portail Azure.


## <a name="capabilities-of-the-preview-experience"></a>Fonctionnalités de l’expérience de visualisation

L’expérience de prévisualisation permet de gérer les nombreuses ressources annuaire tels que les utilisateurs, les groupes et les applications, ainsi que les paramètres de répertoire, dans le portail Azure. Nous améliorons cette expérience pour inclure toutes les fonctions qui existent dans la Azure expérience de gestion Active Directory dans le [portail classique Azure](https://manage.windowsazure.com). Jusque là, il existe certaines tâches que vous devez toujours réaliser sur le portail classique gestion de l’annuaire.

## <a name="manage-the-same-azure-ad-tenants"></a>Gérer les clients Azure AD mêmes

L’expérience de prévisualisation lit et écrit dans le même client Azure Active Directory, ainsi que le portail classique, le centre d’administration Office 365. Modifications apportées dans un de ces portails sont répercutées dans toutes les autres colonnes.

## <a name="use-the-same-authorization-logic"></a>Utiliser la même logique d’autorisation

L’expérience de prévisualisation utilise la même logique d’autorisation en tant que clients Active Directory existants. Les utilisateurs autorisés à apporter des modifications aux ressources répertoire en fonction de leur rôle d’annuaire, telles que l’administrateur global, administrateur d’utilisateur et administrateur de mot de passe. Avec un rôle sur les ressources Azure ou un abonnement Azure n’autorise pas un utilisateur pour gérer les ressources de l’annuaire. Pour plus d’informations des rôles d’Azure AD, voir [affectation de rôles administrateur dans Azure Active Directory](active-directory-assign-admin-roles.md). 

L’expérience de l’aperçu est optimisé pour les administrateurs globaux. Si vous utilisez l’expérience de visualisation alors que connecté en tant qu’un utilisateur qui n’est pas un administrateur global, vous devrez une expérience dégradée. Par exemple, vous pourrez peut-être sélectionner un bouton qui vous permet de commencer une tâche que vous ne pouvez pas effectuer de l’annuaire. Nous améliorons cette expérience plus rapidement.
 
## <a name="tell-us-what-you-think"></a>Donner son avis

Vous pouvez fournir des commentaires sur l’expérience d’aperçu dans la section du portail d’administration du [évaluations Azure AD](https://social.msdn.microsoft.com/Forums/home?forum=WindowsAzureAD&filter=alltypes&sort=lastpostdesc).

<properties
   pageTitle="Résolution des problèmes : Élément 'Active Directory' est manquant ou n’est pas disponible | Microsoft Azure "
   description="Procédure à suivre lors de l’élément de menu Active Directory n’apparaît pas dans le portail de gestion Azure."
   services="active-directory"
   documentationCenter="na"
   authors="bryanla"
   manager="mbaldwin"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="09/16/2016"
   ms.author="mbaldwin"/>

# <a name="troubleshooting-active-directory-item-is-missing-or-not-available"></a>Résolution des problèmes : Élément 'Active Directory' est manquant ou n’est pas disponible

La plupart des instructions pour l’utilisation de services et fonctionnalités Azure Active Directory commencent par « Accédez au portail de gestion Azure et cliquez sur **Active Directory**». Mais que faire si l’élément de menu ou extension Active Directory n’apparaît pas ou si elle est marquée **N’est pas disponible**? Cette rubrique est conçue pour vous aider. Il décrit les conditions dans lesquelles **Active Directory** n’apparaît pas ou n’est pas disponible et explique comment procéder.

## <a name="active-directory-is-missing"></a>Active Directory est manquant

En règle générale, un élément **Active Directory** apparaît dans le menu de navigation gauche. Les instructions de procédures Azure Active Directory part du principe que cet élément est dans la vue.

![Capture d’écran : Active Directory dans Azure](./media/active-directory-troubleshooting/typical-view.png)

L’élément Active Directory apparaît dans le menu de navigation gauche lorsqu’une des conditions suivantes est vraie. Dans le cas contraire, l’élément n’apparaît pas.

* L’utilisateur actuel connecté avec un compte Microsoft (auparavant appelé Windows Live ID).

    OR

* Le client Azure possède un répertoire et le compte actif est un administrateur d’annuaire.

    OR

* Le client Azure comporte au moins un espace de noms de contrôle d’accès AD Azure (ACS). Pour plus d’informations, voir [Namespace de contrôle d’accès](https://msdn.microsoft.com/library/azure/gg185908.aspx).

    OR

* Le client Azure comporte au moins un fournisseur de l’authentification multifacteur Azure. Pour plus d’informations, voir [Fournisseurs d’authentification multifacteur Azure administration](../multi-factor-authentication/multi-factor-authentication-get-started-cloud.md).

Pour créer un espace de noms de contrôle d’accès ou d’un fournisseur de l’authentification multifacteur, cliquez sur **+ Nouveau** > **Application Services** > **Active Directory**.

Pour obtenir les droits d’administrateur pour un répertoire, demandez à un administrateur attribuer un rôle d’administrateur à votre compte. Pour plus d’informations, voir [affectation de rôles administrateur](active-directory-assign-admin-roles.md).

## <a name="active-directory-is-not-available"></a>Active Directory n’est pas disponible

Lorsque vous cliquez sur **+ Nouveau** > **Services d’application**, un élément **Active Directory** apparaît. Plus précisément, l’élément Active Directory s’affiche lorsque toutes les fonctionnalités Active Directory, tels que le répertoire, le contrôle d’accès ou fournisseur d’authentification multifacteur, sont disponibles à l’utilisateur actuel.

Toutefois, pendant le chargement de la page, l’élément apparaît en grisé et est marquée **N’est pas disponible**. Il s’agit d’un état temporaire. Si vous patientez quelques secondes, l’élément devienne disponible. Si le délai est prolongé, actualiser la page web souvent résout le problème.

![Capture d’écran : Active Directory n’est pas disponible](./media/active-directory-troubleshooting/not-available.png)

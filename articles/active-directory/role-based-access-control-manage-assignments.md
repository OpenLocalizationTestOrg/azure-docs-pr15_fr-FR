<properties
    pageTitle="Afficher les affectations access Azure | Microsoft Azure"
    description="Afficher et gérer toutes les affectations de contrôle d’accès basé sur un rôle pour un utilisateur ou un groupe dans le portail Azure"
    services="active-directory"
    documentationCenter=""
    authors="kgremban"
    manager="femila"
    editor="jeffsta"/>

<tags
    ms.service="active-directory"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="identity"
    ms.date="10/10/2016"
    ms.author="kgremban"/>

# <a name="view-access-assignments-for-users-and-groups-in-the-azure-portal---public-preview"></a>Afficher les affectations de l’accès des utilisateurs et groupes dans le portail Azure - version d’évaluation

> [AZURE.SELECTOR]
- [Gérer l’accès par utilisateur ou groupe](role-based-access-control-manage-assignments.md)
- [Gérer l’accès par ressource](role-based-access-control-configure.md)

Avec basée sur les rôles contrôle d’accès (RBAC) dans l’aperçu Azure Active Directory, vous pouvez gérer l’accès à vos ressources Azure. [Nouveautés dans l’aperçu](active-directory-preview-explainer.md)

Accès affectée à l’aide RBAC est permissions car il existe deux façons, vous pouvez limiter les autorisations :

- **Étendue :** Attributions de rôle RBAC sont limitées à un abonnement spécifique, un groupe de ressources ou une ressource. Un utilisateur ayant reçu l’accès à une seule ressource ne peut pas accéder à d’autres ressources dans le même abonnement.
- **Rôle :** Dans le cadre de l’affectation, access est réduite davantage en attribution d’un rôle. Rôles peuvent être haut niveau, comme le propriétaire ou spécifiques, comme lecteur machine virtuelle.

Rôles peuvent uniquement être affectées à partir de dans l’abonnement, un groupe de ressources ou une ressource qui est la portée de l’affectation. Mais vous pouvez afficher toutes les affectations d’accès pour un utilisateur donné ou un groupe dans un emplacement unique.

Obtenir plus d’informations sur la façon [d’utiliser les affectations de rôle pour gérer l’accès à vos ressources abonnement Azure](role-based-access-control-configure.md).

##  <a name="view-access-assignments"></a>Afficher les affectations d’access

Pour rechercher les affectations d’accès pour un seul utilisateur ou un groupe, démarrez dans Azure Active Directory dans le [portail Azure](http://portal.azure.com).

1. Sélectionnez **Azure Active Directory**. Si cette option n’est pas visible dans votre liste de navigation, sélectionnez les **Autres Services** , puis accédez à rechercher **Azure Active Directory**.
2. Sélectionnez **utilisateurs et groupes**, puis soit **tous les utilisateurs** ou **tous les groupes**. Dans cet exemple, nous concentrer sur les utilisateurs individuels.
    ![Gérer les utilisateurs et groupes dans Azure Active Directory - capture d’écran](./media/role-based-access-control-manage-assignments/rbac_users_groups.png)
3. Recherchez l’utilisateur par nom ou nom d’utilisateur.
4. Sélectionnez **ressources Azure** dans la carte de l’utilisateur. Toutes les affectations d’accès pour cet utilisateur s’affichent.

### <a name="read-permissions-to-view-assignments"></a>Autorisations de lecture pour afficher les affectations

Cette page affiche uniquement les affectations access que vous êtes autorisé à lire. Par exemple, vous avez accès en lecture à un abonnement et accédez à la carte de ressources Azure pour vérifier les affectations d’un utilisateur. Vous pouvez voir ses affectations d’accès pour un abonnement, mais que vous ne verrez qu’elle a également d’affectations d’accès lors de l’abonnement B.

## <a name="delete-access-assignments"></a>Supprimer les affectations d’accès

À partir de cette carte, vous pouvez supprimer les affectations d’accès qui ont été affectées directement à un utilisateur ou groupe. Si l’affectation access a été héritée d’un groupe parent, vous devez accéder à la ressource ou d’abonnement et gérer l’affectation il.

1. Dans la liste de toutes les affectations d’accès pour un utilisateur ou groupe, sélectionnez celle que vous voulez supprimer.
2. Sélectionnez **Supprimer** , puis sur **Oui** pour confirmer.
    ![Supprimer l’accès affectation - capture d’écran](./media/role-based-access-control-manage-assignments/delete_assignment.png)

## <a name="related-topics"></a>Rubriques connexes

- Prise en main contrôle d’accès basé sur un rôle à [utiliser les affectations de rôle pour gérer l’accès à vos ressources abonnement Azure](role-based-access-control-configure.md)
- Voir les [rôles intégrés RBAC](role-based-access-built-in-roles.md)

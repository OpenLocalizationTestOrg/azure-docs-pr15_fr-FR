<properties
    pageTitle="Gérer les autorisations à des ressources par utilisateur dans la pile d’Azure (administrateur de service et client) | Microsoft Azure"
    description="Comme un administrateur de service ou le client, découvrez comment gérer les autorisations à des ressources par utilisateur."
    services="azure-stack"
    documentationCenter=""
    authors="ErikjeMS"
    manager="byronr"
    editor=""/>

<tags
    ms.service="azure-stack"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/26/2016"
    ms.author="erikje"/>

# <a name="manage-user-permissions"></a>Gérer les autorisations utilisateur

Un utilisateur dans une pile Azure peut être un lecteur, le propriétaire ou collaborateur pour chaque instance d’un abonnement, le groupe de ressources ou le service. Par exemple, l’utilisateur A peut disposer des autorisations de lecture à 1 abonnement, mais disposer des autorisations de propriétaire pour Machine virtuelle 7.

-   Lecteur : Utilisateur peut afficher tout, mais ne peuvent pas apporter les modifications.

-   Collaboration : Utilisateur peut gérer tous les éléments à l’exception de l’accès aux ressources.

-   Propriétaire : Utilisateur peut gérer tous les éléments, y compris l’accès aux ressources.


## <a name="set-access-permissions-for-a-user"></a>Définir des autorisations d’accès pour un utilisateur

1.  Connectez-vous à l’aide d’un compte disposant des autorisations de propriétaire pour la ressource que vous voulez gérer.

2.  Dans la carte pour la ressource, cliquez sur l’icône **d’accès** ![](media/azure-stack-manage-permissions/image1.png).

3.  Dans la carte **utilisateurs** , cliquez sur **les rôles**.

4.  Dans la carte de **rôles** , cliquez sur **Ajouter** pour ajouter des autorisations pour l’utilisateur.

## <a name="next-steps"></a>Étapes suivantes

[Ajouter un client Azure pile](azure-stack-add-new-user-aad.md)

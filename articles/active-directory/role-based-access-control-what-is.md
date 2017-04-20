<properties
    pageTitle="Contrôle d’accès basé sur un rôle | Microsoft Azure"
    description="Prise en main de gestion de l’accès contrôle d’accès basé sur un rôle Azure dans le portail Azure. Attributions de rôle permet d’affecter des autorisations dans votre annuaire."
    services="active-directory"
    documentationCenter=""
    authors="kgremban"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="identity"
    ms.date="08/03/2016"
    ms.author="kgremban"/>

# <a name="get-started-with-access-management-in-the-azure-portal"></a>Prise en main la gestion des accès dans le portail Azure

Sociétés orientées sécurité doivent se concentrer sur fournir aux employés les autorisations exactes que dont elles ont besoin. Trop d’autorisations expose un compte aux intrus. Autorisations trop peu signifie que les employés ne peuvent pas accéder à leur travail efficacement. Azure contrôle d’accès basé sur un rôle (RBAC) permet de résoudre le problème en offrant gestion des permissions d’accès pour Azure.

L’utilisation de RBAC, vous pouvez séparer les droits au sein de votre équipe et accorder uniquement la quantité d’accès aux utilisateurs dont ils ont besoin pour effectuer leur travail. Au lieu de tout le monde donnant des autorisations illimitées dans votre abonnement Azure ou les ressources, vous pouvez autoriser uniquement certaines actions. Par exemple, utilisez RBAC pour laisser un employé gérer machines virtuelles dans un abonnement, tandis qu’un autre peut gérer les bases de données SQL dans le même abonnement.

## <a name="basics-of-access-management-in-azure"></a>Concepts de base de gestion d’accès dans Azure
Chaque abonnement Azure est associé à un répertoire Azure Active Directory (AD). Les utilisateurs, des groupes et des applications à partir de ce répertoire peuvent gérer les ressources de l’abonnement Azure. Attribuer ces droits d’accès à l’aide du portail Azure, outils de ligne de commande Azure et des API de gestion Azure.

Accorder l’accès en affectant le rôle RBAC approprié à des utilisateurs, des groupes et des applications à une certaine étendue. La portée d’une attribution de rôle peut être un abonnement, un groupe de ressources ou une ressource. Un rôle affecté à une étendue parent accorde également l’accès aux enfants qu’il contient. Par exemple, un utilisateur ayant accès à un groupe de ressources peut gérer toutes les ressources qu’il contient, tels que des sites Web, machines virtuelles et sous-réseaux.

![Relation entre les éléments Azure Active Directory - de diagramme](./media/role-based-access-control-what-is/rbac_aad.png)

Le rôle RBAC que vous affectez détermine les ressources de l’utilisateur, le groupe ou l’application peut gérer dans cette étendue.

## <a name="built-in-roles"></a>Rôles intégrés
RBAC Azure comporte trois rôles base qui s’appliquent à tous les types de ressource :

- **Propriétaire** a un accès complet à toutes les ressources, y compris le droit de l’accès délégué à d’autres personnes.
- **Collaboration** peut créer et gérer tous les types de ressources Azure mais ne peut pas accorder l’accès à d’autres personnes.
- **Lecteur** peut afficher les ressources Azure existants.

Le reste des rôles RBAC dans Azure autoriser la gestion des ressources Azure spécifiques. Par exemple, le rôle de collaborateur Machine virtuelle permet à l’utilisateur créer et gérer des machines virtuelles. Il ne donne pas les accès au réseau virtuel ou le sous-réseau la machine virtuelle se connecte à.

[Rôles intégrés RBAC](role-based-access-built-in-roles.md) répertorie les rôles disponibles dans Azure. Il indique les opérations et l’étendue chaque rôle intégré accorder aux utilisateurs. Si vous avez besoin pour définir vos propres rôles pour davantage de contrôle, voir comment créer des [rôles personnalisés dans Azure RBAC](role-based-access-control-custom-roles.md).

## <a name="resource-hierarchy-and-access-inheritance"></a>Héritage des ressources hiérarchie et access
- Chaque **abonnement** dans Azure appartient à un seul répertoire.
- Chaque **groupe de ressources** appartient à un abonnement.
- Chaque **ressource** appartient uniquement un groupe de ressources.

Accès que vous accordez dans des portées parent est hérité en étendues enfants. Par exemple :

- Vous attribuez le rôle de lecteur à un groupe Azure AD au niveau de la portée d’abonnement. Les membres de ce groupe peuvent afficher chaque groupe de ressources et des ressources de l’abonnement.
- Vous attribuez le rôle de collaborateur à l’application à l’étendue de groupe de ressources. Il peut gérer les ressources de tous les types de ce groupe de ressources, mais pas d’autres groupes de ressources de l’abonnement.

## <a name="azure-rbac-vs-classic-subscription-administrators"></a>Azure RBAC et les administrateurs abonnement classique
Les administrateurs abonnement classique et coadministrateurs ont un accès complet à l’abonnement Azure. Ils peuvent gérer des ressources à l’aide du [portail Azure](https://portal.azure.com) avec des API de gestionnaire de ressources Azure, ou le modèle de déploiement classique [Azure portal classique](https://manage.windowsazure.com) et Azure. Dans le modèle RBAC, les administrateurs classiques affectés le rôle de propriétaire au niveau de la portée d’abonnement.

Uniquement le portail Azure et les nouvelles API de gestionnaire de ressources Azure prend en charge RBAC Azure. Les utilisateurs et applications affectés rôles RBAC ne peut pas utiliser le portail de gestion classique et le modèle de déploiement d’Azure classique.

## <a name="authorization-for-management-vs-data-operations"></a>Autorisation pour la gestion et opérations de données
RBAC Azure prend uniquement en charge les opérations de gestion des ressources Azure dans le portail Azure et API du Gestionnaire de ressources Azure. Il ne peut pas autoriser toutes les opérations de niveau de données pour les ressources Azure. Par exemple, vous pouvez autoriser une personne à gérer les comptes de stockage, mais pas pour les objets BLOB ou des tableaux au sein d’un compte de stockage ne peut pas. De même, une base de données SQL peut être géré, mais pas les tables qu’il contient.

## <a name="next-steps"></a>Étapes suivantes
- Prise en main [contrôle d’accès basé sur un rôle dans le portail Azure](role-based-access-control-configure.md).
- Voir les [rôles intégrés RBAC](role-based-access-built-in-roles.md)
- Définir vos propres [rôles personnalisés dans Azure RBAC](role-based-access-control-custom-roles.md)

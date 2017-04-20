<properties
    pageTitle="Gestion de l’accès aux ressources des groupes de Azure Active Directory | Microsoft Azure"
    description="Découvrez comment utiliser des groupes dans Azure Active Directory pour gérer l’accès utilisateur aux locaux et des applications de cloud et des ressources."
    services="active-directory"
    documentationCenter=""
    authors="curtand"
    manager="femila"
    editor=""
/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/10/2016"
    ms.author="curtand"/>


# <a name="managing-access-to-resources-with-azure-active-directory-groups"></a>Gestion de l’accès aux ressources des groupes de Azure Active Directory

Azure Active Directory (AD Azure) est une solution complète de gestion des identités et des accès qui propose un ensemble de fonctionnalités pour gérer l’accès aux locaux et applications cloud et aux ressources, y compris les services Microsoft online services tels que Office 365 et des applications non - Microsoft SaaS robuste. Cet article fournit une vue d’ensemble, mais si vous voulez commencer à l’aide de groupes d’annonces Azure immédiatement, suivez les instructions de [Gestion des groupes de sécurité dans Azure Active Directory](active-directory-accessmanagement-manage-groups.md). Si vous voulez voir comment vous pouvez utiliser PowerShell pour gérer les groupes dans Azure Active directory, vous pouvez lire en plus [applets de commande Azure Active Directory Aperçu pour la gestion de groupe](active-directory-accessmanagement-groups-settings-v2-cmdlets.md).


> [AZURE.NOTE] Pour utiliser Azure Active Directory, vous avez besoin d’un compte Azure. Si vous n’avez pas un compte, vous pouvez [vous inscrire un compte Azure gratuit](https://azure.microsoft.com/pricing/free-trial/).


Dans Azure AD, un des principales fonctionnalités est la possibilité de gérer l’accès aux ressources. Ces ressources peuvent faire partie de l’annuaire, comme dans le cas des autorisations pour gérer des objets au moyen de rôles dans le répertoire ou les ressources qui sont externes à l’annuaire, tels que des sites SharePoint SaaS applications et services Azure ou sur les ressources de site. Il existe quatre manières que droits d’accès à une ressource peut être affecté à un utilisateur :


1. Affectation directe

    Les utilisateurs peuvent être affectées directement à une ressource par le propriétaire de la ressource.

2. Appartenance aux groupes

    Un groupe peut être affecté à une ressource par le propriétaire de la ressource et en procédant ainsi, octroi les membres de ce groupe l’accès à la ressource. L’appartenance au groupe puis peut être géré par le propriétaire du groupe. En fait, le propriétaire de la ressource délégués l’autorisation à affecter des utilisateurs à leur ressource au propriétaire du groupe.

3. Fondées sur des règles

    Propriétaire de la ressource peut utiliser une règle pour exprimer les utilisateurs qui doivent être affectées accès à une ressource. Le résultat de la règle dépend les attributs utilisés dans cette règle et leurs valeurs pour des utilisateurs spécifiques, et d’en procédant ainsi, le propriétaire de la ressource délégués efficacement droite pour gérer l’accès à leurs ressources à la source faisant autoritée pour les attributs qui sont utilisées dans la règle. Propriétaire de la ressource gère la règle elle-même toujours et détermine les attributs et les valeurs permettent d’accéder à leur ressource.

4. Autorité externe

    L’accès à une ressource est dérivé d’une source externe ; par exemple, un groupe est synchronisé à partir d’une source faisant autoritée telle qu’un répertoire local ou une application SaaS comme journée de travail. Propriétaire de la ressource affecte le groupe pour fournir un accès à la ressource, et la source externe gère les membres du groupe.

  ![Vue d’ensemble du diagramme de gestion des accès](./media/active-directory-access-management-groups/access-management-overview.png)


## <a name="watch-a-video-that-explains-access-management"></a>Regardez une vidéo qui explique son objectif de gestion de l’accès

Vous pouvez regarder une courte vidéo qui explique son objectif plus à ce sujet :

**Azure AD : Introduction aux membres dynamiques pour les groupes**

> [AZURE.VIDEO azure-ad--introduction-to-dynamic-memberships-for-groups]

## <a name="how-does-access-management-in-azure-active-directory-work"></a>Comment accède-t-il à la gestion des travaux Azure Active Directory ?
Solution de gestion des accès au centre de l’annonce Azure inclut du groupe de sécurité. À l’aide d’un groupe de sécurité pour gérer l’accès aux ressources est un paradigme connu, ce qui permet un moyen flexible et facile d’utilisation fournir un accès à une ressource pour le groupe initial d’utilisateurs. Propriétaire de la ressource (ou l’administrateur de l’annuaire) peut affecter un groupe à fournir un accès certain vers la droite pour les ressources que dont ils sont propriétaires. Les membres du groupe seront fournies le niveau d’accès et le propriétaire de la ressource peut déléguer droite pour gérer la liste des membres d’un groupe à quelqu'un d’autre, par exemple un responsable ou un administrateur du support technique.

![Diagramme de gestion des accès Azure Active Directory](./media/active-directory-access-management-groups/active-directory-access-management-works.png)

Le propriétaire d’un groupe peut également rendre ce groupe disponibles pour les demandes de libre-service. Dans ce cas, un utilisateur final peut rechercher et trouver le groupe et demander à rejoindre, recherche efficace d’autorisation pour accéder aux ressources qui sont gérés par le biais du groupe. Le propriétaire du groupe peut configurer le groupe afin que les demandes de jointure sont automatiquement acceptées ou doivent être approuvés par le propriétaire du groupe. Lorsqu’un utilisateur effectue une demande de rejoindre un groupe, la demande d’adhésion est transférée vers les propriétaires du groupe. Si un des propriétaires approuve la demande, l’utilisateur est informé et l’utilisateur est joint au groupe. Si un des propriétaires refuse la demande, l’utilisateur est informé mais pas rejoint le groupe.


## <a name="getting-started-with-access-management"></a>Prise en main la gestion des accès
Êtes-vous prêt à commencer ? Vous devez essayer parmi les tâches de base que vous pouvez faire avec les groupes d’annonces Azure. Utilisez ces fonctionnalités pour fournir un accès dédié à différents groupes de personnes pour différentes ressources de votre organisation. Vous trouverez ci-dessous une liste de base premières étapes.

* [Création d’une règle simple pour configurer les appartenances dynamiques pour un groupe](active-directory-accessmanagement-manage-groups.md#how-can-i-manage-the-membership-of-a-group-dynamically)

* [Utiliser un groupe pour gérer l’accès aux applications SaaS](active-directory-accessmanagement-group-saasapps.md)

* [Mise à disposition un groupe pour l’utilisateur final libre-service](active-directory-accessmanagement-self-service-group-management.md)

* [La synchronisation d’un groupe local vers Azure à l’aide d’Azure AD Connect](active-directory-aadconnect.md)

* [Gestion des propriétaires pour un groupe](active-directory-accessmanagement-managing-group-owners.md)


## <a name="next-steps-for-access-management"></a>Étapes suivantes pour la gestion des accès
À présent que vous avez compris les bases de la gestion de l’accès, voici quelques fonctionnalités avancées supplémentaires disponibles dans Azure Active Directory pour gérer l’accès à vos applications et ressources.

* [Utilisation des attributs pour créer des règles avancées](active-directory-accessmanagement-groups-with-advanced-rules.md)

* [Gestion des groupes de sécurité dans Azure Active Directory](active-directory-accessmanagement-manage-groups.md)

* [Définition des groupes dédiés dans Azure AD](active-directory-accessmanagement-dedicated-groups.md)

* [Référence Graph API pour les groupes](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/groups-operations#GroupFunctions)

* [Azure Active Directory applets de commande pour configurer les paramètres de groupe](active-directory-accessmanagement-groups-settings-cmdlets.md)

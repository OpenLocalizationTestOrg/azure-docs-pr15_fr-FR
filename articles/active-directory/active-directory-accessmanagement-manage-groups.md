<properties
    pageTitle="Gestion des groupes dans Azure Active Directory | Microsoft Azure"
    description="Découvrez comment créer et gérer des groupes pour gérer les utilisateurs Azure à l’aide d’Azure Active Directory."
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
    ms.topic="get-started-article"
    ms.date="09/29/2016"
    ms.author="curtand"/>


# <a name="managing-groups-in-azure-active-directory"></a>Gestion des groupes dans Azure Active Directory

> [AZURE.SELECTOR]
- [Portail Azure](active-directory-groups-create-azure-portal.md)
- [Portail classique Azure](active-directory-accessmanagement-manage-groups.md)
- [PowerShell](active-directory-accessmanagement-groups-settings-v2-cmdlets.md)


Un des fonctionnalités de gestion des utilisateurs Azure Active Directory (AD Azure) est la possibilité de créer des groupes d’utilisateurs. Un groupe vous permet d’effectuer des tâches de gestion telles que l’affectation des licences ou les autorisations pour un nombre d’utilisateurs en même temps. Vous pouvez également utiliser des groupes pour attribuer l’autorisation d’accès à

- Ressources comme des objets dans l’annuaire
- Ressources externes à l’annuaire telles que les applications SaaS, services Azure et les sites SharePoint, ou des ressources locales

En outre, un propriétaire de la ressource peut affecter également accès à une ressource à un groupe d’annonces Azure appartenant à une autre personne. Cette affectation accorde les membres de ce groupe l’accès à la ressource. Ensuite, le propriétaire du groupe gère appartenance au groupe. En fait, le propriétaire de la ressource délégués au propriétaire du groupe l’autorisation d’affecter des utilisateurs à leur ressource.

## <a name="how-do-i-create-a-group"></a>Comment créer un groupe ?

Selon les services à laquelle votre organisation est abonnée, vous pouvez créer un groupe à l’aide d’une des opérations suivantes :
- le portail classique Azure
- le portail de compte Office 365
- le portail de compte Windows Intune

Nous allons décrire les tâches comme effectuée dans le portail classique Azure. Pour plus d’informations sur l’utilisation de portails non Azure pour gérer votre annuaire Azure AD, consultez [administration des recherches dans l’annuaire Active Directory Azure](active-directory-administer.md).

1. Dans le [portail classique Azure](https://manage.windowsazure.com), sélectionnez **Active Directory**, puis le nom de l’annuaire de votre organisation.

2. Sélectionnez l’onglet **groupes** .

3. Sélectionnez **Ajouter un groupe**.

4. Dans la fenêtre **Ajouter un groupe** , spécifiez le nom et la description d’un groupe.


## <a name="how-do-i-add-or-remove-individual-users-in-a-security-group"></a>Comment ajouter ou supprimer des utilisateurs spécifiques dans un groupe de sécurité ?

**Pour ajouter un utilisateur individuel à un groupe**

1. Dans le [portail classique Azure](https://manage.windowsazure.com), sélectionnez **Active Directory**, puis le nom de l’annuaire de votre organisation.

2. Sélectionnez l’onglet **groupes** .

3. Ouvrez le groupe auquel vous souhaitez ajouter des membres. Ouvrez l’onglet **membres** du groupe sélectionné si elle n'affiche pas déjà.

4. Sélectionnez **Ajouter des membres**.

5. Dans la page **Ajouter des membres** , sélectionnez le nom de l’utilisateur ou un groupe que vous voulez ajouter en tant que membre de ce groupe. Assurez-vous que ce nom figure dans le volet **sélectionnées** .


**Pour supprimer un utilisateur individuel d’un groupe**

1. Dans le [portail classique Azure](https://manage.windowsazure.com), sélectionnez **Active Directory**, puis le nom de l’annuaire de votre organisation.

2. Sélectionnez l’onglet **groupes** .

3. Ouvrez le groupe à partir de laquelle vous voulez supprimer des membres.

4. Sélectionnez l’onglet **membres** , sélectionnez le nom du membre que vous souhaitez supprimer du groupe, puis cliquez sur **Supprimer**.

6. À l’invite, confirmez que vous voulez supprimer ce membre du groupe.


## <a name="how-can-i-manage-the-membership-of-a-group-dynamically"></a>Comment puis-je gérer dynamiquement l’appartenance d’un groupe ?

Dans Azure Active Directory, vous pouvez configurer très facilement une règle simple pour déterminer les utilisateurs qui doivent être membres du groupe. Une règle simple est une valeur qui ne rend qu’une seule comparaison. Par exemple, si un groupe est affecté à une application SaaS, vous pouvez configurer une règle pour ajouter des utilisateurs qui ont un titre de « Commercial ». Cette règle puis accorde l’accès à cette application SaaS à tous les utilisateurs avec cette fonction dans votre annuaire.

Lorsque tous les attributs d’un changement d’utilisateur, le système évalue toutes les règles de groupe dynamique dans un répertoire pour voir si la modification de l’attribut de l’utilisateur susceptibles de déclencher n’importe quel groupe ajoute ou supprime. Si un utilisateur satisfait à une règle sur un groupe, ils sont ajoutés en tant que membre à ce groupe. Si elles satisfont n’est plus à la règle d’un groupe, qu'il s’agit d’un membre, ils sont supprimés en tant que membres de ce groupe.

> [AZURE.NOTE] Vous pouvez configurer une règle pour les membres dynamiques dans des groupes de sécurité ou Office 365. Appartenance aux groupes imbriqués ne sont pas pris en charge pour affectation basée sur un groupe pour les applications.
>
> Appartenance dynamique pour les groupes nécessite pas de licence pour être assignée à Azure AD Premium
>
> - L’administrateur qui gère la règle sur un groupe
> - Tous les membres du groupe

**Pour activer l’appartenance dynamique pour un groupe**

1. Dans le [portail classique Azure](https://manage.windowsazure.com), sélectionnez **Active Directory**, puis le nom de l’annuaire de votre organisation.

2. Sélectionnez l’onglet **groupes** et ouvrez le groupe que vous voulez modifier.

3. Sélectionnez l’onglet **configurer** et définissez **Activer dynamique appartenances** sur **Oui**.

4. Configurer une règle simple unique pour le groupe contrôler l’appartenance dynamique pour les fonctions de ce groupe. Vérifiez que la **Ajouter des utilisateurs où** option est sélectionnée et sélectionnez une propriété utilisateur dans la liste (par exemple, département, jobTitle, etc.),

5. Ensuite, sélectionnez une condition (pas est égal à égal à, pas commence par, commence par, pas contient, contient, ne correspondent pas, EQUIV).

6. Spécifier une valeur de comparaison pour la propriété utilisateur sélectionné.

Pour découvrir comment créer des règles *avancées* (règles qui peuvent contenir plusieurs comparaisons) pour l’appartenance aux groupes dynamiques, voir [attributs à l’aide pour créer des règles avancées](active-directory-accessmanagement-groups-with-advanced-rules.md).

## <a name="additional-information"></a>Informations supplémentaires

Les articles suivants fournissent des informations supplémentaires sur Azure Active Directory.

* [Gestion de l’accès aux ressources des groupes de Azure Active Directory](active-directory-manage-groups.md)

* [Azure Active Directory applets de commande pour configurer les paramètres de groupe](active-directory-accessmanagement-groups-settings-cmdlets.md)

* [Index des articles de gestion des applications dans Azure Active Directory](active-directory-apps-index.md)

* [Qu’est Azure Active Directory ?](active-directory-whatis.md)

* [Intégration de vos identités locales avec Azure Active Directory](active-directory-aadconnect.md)

<properties
    pageTitle="Dédié groupes dans Azure Active Directory | Microsoft Azure"
    description="Vue d’ensemble des groupes dédiés comment travailler dans Azure Active Directory et comment ils sont créés."
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

# <a name="dedicated-groups-in-azure-active-directory"></a>Groupes dédiés dans Azure Active Directory

Dans Azure Active Directory (AD Azure), la fonctionnalité groupes dédiés crée automatiquement et remplit l’appartenance aux groupes d’annonces Azure prédéfinis. Membres de groupes dédiés ne peut pas être ajoutés ou supprimé à l’aide du portail classique Azure, les applets de commande Windows PowerShell, ou par programme.

>[AZURE.NOTE] Groupes dédiés requièrent qu’une licence Azure AD Premium est affectée à
>- l’administrateur qui gère la règle sur un groupe
>- tous les utilisateurs qui sont sélectionnés par la règle à être membre du groupe

**Pour activer les groupes dédiés**

1. Dans le [portail classique Azure](https://manage.windowsazure.com), sélectionnez **Active Directory**et puis ouvrez le répertoire de votre organisation.

2. Sélectionnez l’onglet **groupes** et ouvrez le groupe que vous voulez modifier.

3. Sélectionnez l’onglet **configurer** et définissez **Activer les groupes dédié** sur **Oui**.

Une fois que le commutateur groupes dédiés activer est défini sur **Oui**, vous pouvez activer davantage de l’annuaire créer automatiquement un groupe dédié tous les utilisateurs en définissant la **Activer « Tous les utilisateurs » groupe** basculer sur **Oui**. Vous pouvez ensuite également modifier le nom de ce groupe dédié en le retapant dans la **nom d’affichage pour « Tous les utilisateurs » groupe** champ.

Le groupe tous les utilisateurs peut servir à affecter les mêmes autorisations à tous les utilisateurs dans votre annuaire. Par exemple, vous pouvez accorder tous les utilisateurs dans votre accès à l’annuaire pour une application SaaS en assignant le groupe dédié tous les utilisateurs l’accès à cette application.

Le groupe tous les utilisateurs dédié inclut tous les utilisateurs dans le répertoire, y compris les invités et les utilisateurs externes. Si vous avez besoin d’un groupe qui exclut les utilisateurs externes, puis pour ce faire, vous pouvez créer un groupe avec une règle dynamique basée sur les attributs tels que les éléments suivants :

                (user.userPrincipalName -notContains "#EXT#@")

Pour un groupe qui exclut tous les invités, utilisez une règle comme suit :

                (user.userType -ne "Guest")

Pour découvrir comment créer des règles *avancées* (règles qui peuvent contenir plusieurs comparaisons) pour l’appartenance aux groupes dynamiques, voir [attributs à l’aide pour créer des règles avancées](active-directory-accessmanagement-groups-with-advanced-rules.md).


Les articles suivants fournissent des informations supplémentaires sur Azure Active Directory.

* [Gestion de l’accès aux ressources des groupes de Azure Active Directory](active-directory-manage-groups.md)
* [Index des articles de gestion des applications dans Azure Active Directory](active-directory-apps-index.md)
* [Qu’est Azure Active Directory ?](active-directory-whatis.md)
* [Intégration de vos identités locales avec Azure Active Directory](active-directory-aadconnect.md)

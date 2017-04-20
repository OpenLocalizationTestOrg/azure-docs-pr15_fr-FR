<properties
    pageTitle="Assigner des utilisateurs à un domaine personnalisé dans Azure Active Directory | Microsoft Azure"
    description="Comment remplir un domaine personnalisé dans Azure Active Directory aux comptes d’utilisateur."
    services="active-directory"
    documentationCenter=""
    authors="jeffsta"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/04/2016"
    ms.author="curtand;jeffsta"/>

# <a name="assign-users-to-a-custom-domain"></a>Assigner des utilisateurs à un domaine personnalisé

Après avoir ajouté votre domaine personnalisé à Azure Active Directory, vous devez ajouter les comptes d’utilisateurs pour ce domaine afin que vous puissiez commencer authentifier ces dernières.

## <a name="users-synced-in-from-a-directory-on-your-corporate-network"></a>Utilisateurs synchronisés dans à partir d’un répertoire sur votre réseau d’entreprise

Si vous avez déjà configuré une connexion entre vos localement Active Directory et Azure Active Directory, la synchronisation peut renseigner les comptes. Pour plus d’informations sur la synchronisation Azure Active Directory avec Active Directory local, voir [l’intégration de vos identités locales avec Azure Active Directory](active-directory-aadconnect.md).

## <a name="users-added-and-managed-in-the-cloud"></a>Les utilisateurs ajoutés et gérés dans le cloud

Pour modifier le domaine d’un compte d’utilisateur existant :

1.  Ouvrez le portail classique Azure à l’aide d’un compte qui est un administrateur général ou administrateur d’utilisateurs.

2.  Ouvrez votre répertoire.

3.  Sélectionnez l’onglet **utilisateurs** .

4.  Sélectionnez l’utilisateur dans la liste.

5.  Modifier le domaine pour l’utilisateur, puis sélectionnez **Enregistrer**.

Vous pouvez également le faire à l’aide de [PowerShell Microsoft](https://msdn.microsoft.com/library/azure/e1ef403f-3347-4409-8f46-d72dafa116e0#BKMK_ManageDomains) ou l' [API de graphique](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/domains-operations).

## <a name="select-a-custom-domain-when-creating-a-new-user"></a>Sélectionnez un domaine personnalisé lorsque vous créez un nouvel utilisateur

1.  Ouvrez le portail classique Azure à l’aide d’un compte qui est un administrateur général ou administrateur d’utilisateurs.

2.  Ouvrez votre répertoire.

3.  Sélectionnez l’onglet **utilisateurs** .

4.  Dans la barre de commandes, sélectionnez **Ajouter**.

5.  Lorsque vous ajoutez le nom d’utilisateur, sélectionnez le domaine personnalisé à partir de la liste de domaines.

6.  Cliquez sur **Enregistrer**.

## <a name="next-steps"></a>Étapes suivantes

-   [Utilisation de noms de domaine personnalisé pour simplifier l’expérience de connexion pour vos utilisateurs](active-directory-add-domain.md)

-   [Gérer des noms de domaine personnalisé](active-directory-add-manage-domain-names.md)

-   [En savoir plus sur les concepts de gestion de domaine dans Azure Active Directory](active-directory-add-domain-concepts.md)

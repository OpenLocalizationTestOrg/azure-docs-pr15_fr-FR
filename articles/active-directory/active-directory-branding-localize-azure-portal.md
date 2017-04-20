<properties
pageTitle="Ajouter de marque à votre page de connexion dans l’aperçu Azure Active Directory de l’entreprise spécifiques à une langue | Microsoft Azure"
description="Découvrez comment ajouter une société spécifique langue personnalisation des images et du texte à une page de connexion Azure"
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

# <a name="add-language-specific-company-branding-to-your-sign-in-page-in-the-azure-active-directory-preview"></a>Ajouter de marque à votre page de connexion dans l’aperçu Azure Active Directory de l’entreprise spécifiques à une langue

Pour éviter toute confusion, de nombreuses sociétés voulez appliquer une apparence cohérente sur tous les sites Web et services qu’ils gérer. Azure preview Active Directory permet cela grâce à ce qui vous permet de personnaliser l’apparence de la page se connecter avec votre logo de société et les jeux de couleurs personnalisé. [Nouveautés dans l’aperçu](active-directory-preview-explainer.md) La page de connexion est la page qui s’affiche lorsque vous êtes connecté à Office 365 ou d’autres applications basées sur le web qui utilisent Azure AD comme votre fournisseur d’identité. Vous interagissez avec cette page pour entrer vos informations d’identification.

## <a name="customizing-the-sign-in-page-for-another-language"></a>Personnalisation de la page de connexion pour une autre langue

Vous pouvez ajouter des éléments spécifiques à une langue à votre page de connexion personnalisée uniquement si vous avez déjà créé une page de connexion personnalisée comme décrit dans [Ajouter marque à votre page de connexion de l’entreprise](active-directory-branding-custom-signon-azure-portal.md). Vous pouvez configurer une connexion à page par répertoire avec un ensemble par défaut des éléments personnalisables. Une fois que vous avez configuré le jeu par défaut des éléments de page, vous pouvez configurer plusieurs versions pour des langues différentes. Vous pouvez également mélanger et correspondent à divers éléments. Par exemple, vous pouvez :

- Créer une **image de la page connexion** qui convient à toutes les cultures, puis créer des versions pour l’anglais et Français par défaut. Lorsque vous définissez votre navigateurs à un de ces deux langues, l’image spécifiques à une langue apparaît, tandis que l’illustration par défaut s’affiche pour toutes les autres langues.

- Configurer plusieurs logos pour votre organisation (par exemple, les versions japonais ou l’hébreu).

Nous vous recommandons de conserver le nombre de variantes langue faible, pour des raisons de performances et maintenance.

**Pour ajouter la marque à votre annuaire de l’entreprise :**

1.  Connectez-vous au [portail Azure](https://portal.azure.com) avec un compte qui est un administrateur global pour l’annuaire.

2.  Sélectionnez **plusieurs services**, entrez les **utilisateurs et groupes** dans la zone de texte et puis appuyez sur **entrée**.

    ![Gestion des utilisateurs de l’ouverture](./media/active-directory-branding-localize-azure-portal/user-management.png)

3. Dans la carte **utilisateurs et groupes** , sélectionnez **Personnalisation de la société**.

4. Sur la **utilisateurs et groupes - personnalisation de la société** carte, sélectionnez la commande **Ajouter une langue** .

    ![Ajouter des éléments de personnalisation spécifiques à une langue](./media/active-directory-branding-localize-azure-portal/add-language.png)

5. Modifier les éléments que vous voulez personnaliser. Tous les éléments sont facultatives.

6. Cliquez sur **Enregistrer**.

Il peut prendre jusqu'à une heure pour que toutes les modifications apportées à la page de connexion de personnalisation qui pour doit apparaître.

## <a name="next-steps"></a>Étapes suivantes

[Ajouter de marque à votre page de connexion de l’entreprise](active-directory-branding-custom-signon-azure-portal.md)

<properties
pageTitle="Personnaliser votre page de connexion dans l’aperçu Azure Active Directory | Microsoft Azure"
description="Découvrez comment ajouter une marque à la page de connexion Azure de l’entreprise"
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
ms.date="09/30/2016"
ms.author="curtand"/>

# <a name="add-company-branding-to-your-sign-in-page-in-the-azure-active-directory-preview"></a>Ajouter de marque à votre page de connexion dans l’aperçu Azure Active Directory de l’entreprise

Pour éviter toute confusion, de nombreuses sociétés voulez appliquer une apparence cohérente sur tous les sites Web et services qu’ils gérer. Azure preview Active Directory permet cela grâce à ce qui vous permet de personnaliser l’apparence de la page se connecter avec votre logo de société et les jeux de couleurs personnalisé. [Nouveautés dans l’aperçu](active-directory-preview-explainer.md) La page de connexion est la page qui s’affiche lorsque vous êtes connecté à Office 365 ou d’autres applications basées sur le web qui utilisent Azure AD comme votre fournisseur d’identité. Vous interagissez avec cette page pour entrer vos informations d’identification.

Si vous voulez afficher la marque de votre société, couleurs et autres éléments personnalisables sur cette page, voir les images suivantes de comprendre la différence entre les deux expériences.

La capture d’écran montre et l’exemple de la page de connexion à Office 365 sur un ordinateur de bureau **avant** une personnalisation suivants :

![Page connexion à Office 365 avant de personnalisation](./media/active-directory-branding-custom-signon-azure-portal/sign-in-page-before-customization.png)

La capture d’écran montre et l’exemple de la page de connexion à Office 365 sur un ordinateur de bureau **après** une personnalisation suivants :

![Page connexion à Office 365 après la personnalisation](./media/active-directory-branding-custom-signon-azure-portal/sign-in-page-after-customization.png)


## <a name="customizing-the-sign-in-page"></a>Personnalisation de la page de connexion

En règle générale, si vous avez besoin d’accès basé sur le navigateur à vos applications cloud et services que votre organisation s’est abonnée, vous utilisez la page de connexion.

Si vous avez appliqué des modifications à votre page de connexion, il peut prendre jusqu'à une heure pour afficher les modifications.

Une page de connexion à l’effigie s’affiche uniquement lorsque vous accédez à un service présentant une URL spécifique client tels que https://outlook.com/**contoso**.com ou https://mail. **Contoso**. com.

Lorsque vous visitez un service avec des URL spécifiques non client (par exemple : https://mail.office365.com), une page de connexion non personnalisée s’affiche. Dans ce cas, votre marque apparaît une fois que vous avez entré votre nom d’utilisateur ou que vous avez sélectionné une vignette de l’utilisateur.

> [AZURE.NOTE]
>
- Votre nom de domaine doit apparaître comme « Actif » dans la partie de **domaines** du portail Azure dans lequel vous avez configuré de personnalisation. Pour plus d’informations, voir [Ajouter des noms de domaine personnalisé](active-directory-domains-add-azure-portal.md).
- Personnalisation de la page de connexion n’alors à la page de Microsoft connexion grand public. Si vous vous connecter avec un compte Microsoft, vous pouvez voir une liste à l’effigie de mosaïques utilisateur affichées par Azure Active Directory, mais la personnalisation de votre organisation ne s’applique pas à la page connexion de compte Microsoft.

Dans votre page de connexion, la case à cocher **Conserver ma connexion** permet à un utilisateur doit rester connecté lorsqu’ils fermer et rouvrir leur navigateur. 

   ![Rester connecté](./media/active-directory-branding-custom-signon-azure-portal/01.png)

Il n’affecte pas la durée de vie session. Vous pouvez masquer la case à cocher sur la page de connexion Azure Active Directory.
Si la case à cocher est affiché dépend du paramètre de **maintenir dans désactivé**.

   ![Rester connecté](./media/active-directory-branding-custom-signon-azure-portal/02.png)


Pour masquer la case à cocher, configurez ce paramètre sur **Oui**. 

> [AZURE.NOTE] Certaines fonctionnalités de SharePoint Online et Office 2010 dépendent des utilisateurs en Activez cette case à cocher. Si vous configurez ce paramètre pour masqué, vos utilisateurs peuvent voir invites supplémentaires et inattendus pour se connecter.




**Pour ajouter la marque à votre annuaire de l’entreprise :**

1.  Connectez-vous au [portail Azure](https://portal.azure.com) avec un compte qui est un administrateur global pour l’annuaire.

2.  Sélectionnez **plusieurs services**, entrez les **utilisateurs et groupes** dans la zone de texte et puis appuyez sur **entrée**.

    ![Gestion des utilisateurs de l’ouverture](./media/active-directory-branding-custom-signon-azure-portal/user-management.png)

3. Dans la carte **utilisateurs et groupes** , sélectionnez **Personnalisation de la société**.

4. Sur la **utilisateurs et groupes - personnalisation de la société** carte, sélectionnez la commande **Modifier** .

    ![Modifier la personnalisation](./media/active-directory-branding-custom-signon-azure-portal/edit-branding.png)

5. Modifier les éléments que vous voulez personnaliser. Tous les éléments sont facultatives.

6. Cliquez sur **Enregistrer**.

Il peut prendre jusqu'à une heure pour que toutes les modifications apportées à la page de connexion de personnalisation qui pour doit apparaître.

## <a name="next-steps"></a>Étapes suivantes

[Ajouter la marque de l’entreprise spécifiques à une langue](active-directory-branding-localize-azure-portal.md)

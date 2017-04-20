<properties
    pageTitle="Azure B2C annuaire Active : Attributs personnalisés | Microsoft Azure"
    description="Comment utiliser des attributs personnalisés dans Azure Active Directory B2C pour recueillir des informations sur vos consommateurs"
    services="active-directory-b2c"
    documentationCenter=""
    authors="swkrish"
    manager="mbaldwin"
    editor="bryanla"/>

<tags
    ms.service="active-directory-b2c"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/24/2016"
    ms.author="swkrish"/>

#  <a name="azure-active-directory-b2c-use-custom-attributes-to-collect-information-about-your-consumers"></a>Azure B2C annuaire Active : Utiliser des attributs personnalisés pour recueillir des informations sur vos consommateurs

Dans l’annuaire B2C Azure Active Directory (AD Azure) est fourni avec un jeu intégré des informations (attributs) : prénom, nom, ville, Code Postal et autres attributs. Toutefois, toutes les applications pour les consommateurs a des besoins uniques sur quels attributs à collectez les consommateurs. Avec Azure AD B2C, vous pouvez étendre l’ensemble des attributs que qui se trouve sur chaque compte grand public. Vous pouvez créer des attributs personnalisés dans le [portail Azure](https://portal.azure.com/) et l’utiliser dans vos stratégies d’abonnement, comme illustré ci-dessous. Vous pouvez également lire et écrire ces attributs à l’aide de l' [API Azure AD Graph](active-directory-b2c-devquickstarts-graph-dotnet.md).

> [AZURE.NOTE]
Attributs personnalisés utilisent des [Extensions de schéma Azure AD Graph API Directory](https://msdn.microsoft.com/library/azure/dn720459.aspx).

## <a name="create-a-custom-attribute"></a>Créer un attribut personnalisé

1. [Procédez comme suit pour accéder à la carte de fonctionnalités B2C sur le portail Azure](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade).
2. Cliquez sur **attributs de l’utilisateur**.
3. Cliquez sur **+ Ajouter** en haut de la carte.
4. Attribuez un **nom** pour l’attribut personnalisé (par exemple, « ShoeSize ») et éventuellement une **Description**. Cliquez sur **créer**.

    > [AZURE.NOTE]
    Seul le « Chaîne » **Type de données** est actuellement disponible.

L’attribut personnalisé est désormais disponible dans la liste des **attributs de l’utilisateur**et à utiliser dans vos stratégies d’inscription.

## <a name="use-a-custom-attribute-in-your-sign-up-policy"></a>Utiliser un attribut personnalisé dans votre stratégie d’abonnement

1. [Procédez comme suit pour accéder à la carte de fonctionnalités B2C sur le portail Azure](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade).
2. Cliquez sur **stratégies d’inscription**.
3. Cliquez sur votre stratégie d’inscription (par exemple, « B2C_1_SiUp ») pour l’ouvrir. Cliquez sur **Modifier** en haut de la carte.
4. Cliquez sur **attributs d’abonnement** et sélectionnez l’attribut personnalisé (par exemple, « ShoeSize »). Cliquez sur **OK**.
5. Cliquez **sur les revendications Application** et sélectionnez l’attribut personnalisé. Cliquez sur **OK**.
6. Cliquez sur **Enregistrer** en haut de la carte.

Vous pouvez utiliser la fonctionnalité « Exécuter maintenant » sur la stratégie pour vérifier l’expérience grand public. Vous devez maintenant voir « ShoeSize » dans la liste des attributs collectées pendant consommateur d’abonnement et voir dans le jeton envoyé à votre application.

## <a name="notes"></a>Notes

- Ainsi que les stratégies d’inscription, attributs personnalisés peuvent également être utilisés dans les stratégies d’abonnement ou se connecter et stratégies de modification du profil.
- Il existe une limitation connue des attributs personnalisés. Il est créé uniquement la première fois qu’il est utilisé dans n’importe quelle stratégie, et non lorsque vous ajoutez à la liste des **attributs de l’utilisateur**.

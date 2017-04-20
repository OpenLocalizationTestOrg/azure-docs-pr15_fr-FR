<properties
    pageTitle="Azure B2C annuaire Active : Inscription d’Application | Microsoft Azure"
    description="Comment enregistrer votre application avec Azure Active Directory B2C"
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
    ms.topic="get-started-article"
    ms.date="08/30/2016"
    ms.author="swkrish"/>


# <a name="azure-active-directory-b2c-register-your-application"></a>Azure B2C annuaire Active : Enregistrer votre application

## <a name="prerequisite"></a>Conditions préalables

Pour créer une application qui accepte grand public d’abonnement et se connecter, vous devez tout d’abord enregistrer l’application avec un client Azure Active Directory B2C. Obtenez votre propre client en suivant les étapes décrites dans [créer un client Azure AD B2C](active-directory-b2c-get-started.md). Après avoir suivi les étapes de cet article, vous aurez la carte de fonctionnalités B2C épinglée à votre Startboard.

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-v2-apps](../../includes/active-directory-b2c-devquickstarts-v2-apps.md)]

## <a name="navigate-to-the-b2c-features-blade"></a>Accédez à la carte de fonctionnalités B2C

Si vous avez la carte de fonctionnalités B2C épinglée à votre Startboard, vous verrez la carte dès que vous connectez au [portail Azure](https://portal.azure.com/) en tant qu’administrateur Global du client B2C.

Vous pouvez également accéder à la carte en cliquant sur **Parcourir** , puis sur **Azure AD B2C** dans le volet de navigation gauche sur le [portail Azure](https://portal.azure.com/).

> [AZURE.IMPORTANT] Vous devez être un administrateur Global du client B2C puissent accéder à la carte de fonctionnalités B2C. Un administrateur Global à partir de n’importe quel autre client ou un utilisateur à partir d’un client ne peut pas y accéder.  Vous pouvez basculer à votre client B2C à l’aide du sélecteur de client dans le coin supérieur droit du portail Azure.

## <a name="register-an-application"></a>Enregistrer une application

1. Dans la carte de fonctionnalités B2C sur le portail Azure, cliquez sur **Applications**.
2. Cliquez sur **+ Ajouter** en haut de la carte.
3. Entrez un **nom** pour l’application qui décrira votre application pour les consommateurs. Par exemple, vous pouvez entrer « Contoso B2C application ».
4. Si vous écrivez une application basée sur le web, basculez **inclure dans le navigateur / web API** **Oui**. L' **URL de réponse** sont les points de terminaison où Azure AD B2C renverra les jetons que les demandes de votre application. Par exemple, entrez `https://localhost:44321/`. Si certains web API sécurisé par Azure AD B2C également en appelant votre application web, vous souhaiterez créer une **Application Secret** également en cliquant sur le bouton **Générer une clé** .

    > [AZURE.NOTE] Un **Code Secret Application** est une information d’identification de sécurité importants et doivent être correctement sécurisée.

5. Si vous écrivez une application mobile, basculez le **inclure native client** sur **Oui**. Recopier vers le bas de la valeur par défaut **Rediriger URI** qui est créé automatiquement pour vous.
6. Cliquez sur **créer** pour enregistrer votre application.
7. Cliquez sur l’application que vous venez de créer et Recopier vers le bas globalement unique **ID de Client de l’Application** que vous utiliserez plus loin dans votre code.

> [AZURE.IMPORTANT] Applications créées dans la carte de fonctionnalités B2C offrent gérés au même emplacement. Si vous modifiez B2C applications à l’aide de PowerShell ou un autre portail, ils deviennent non prises en charge et ne fonctionnera probablement pas avec Azure AD B2C.

## <a name="build-a-quick-start-application"></a>Créer une Application de démarrage rapide

Maintenant que vous avez une application enregistrée avec Azure AD B2C, vous pouvez effectuer une de nos didacticiels de démarrage rapide pour être opérationnel. Voici quelques recommandations :

[AZURE.INCLUDE [active-directory-v2-quickstart-table](../../includes/active-directory-b2c-quickstart-table.md)]

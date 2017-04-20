<properties
    pageTitle="inscription de l’application 2.0 | Microsoft Azure"
    description="Comment enregistrer une application auprès de Microsoft pour l’activation de se connecter et accéder aux services Microsoft à l’aide de point de terminaison de la version 2.0"
    services="active-directory"
    documentationCenter=""
    authors="dstrockis"
    manager="mbaldwin"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/16/2016"
    ms.author="dastrock"/>

# <a name="how-to-register-an-app-with-the-v20-endpoint"></a>Comment enregistrer une application avec le point de terminaison version 2.0

Pour créer une application acceptant MSA et Azure AD se connecter, vous commencez par devez enregistrer une application auprès de Microsoft.  Pour le moment, vous ne pourrez utiliser toutes les applications existantes que vous deviez avec Azure AD ou MSA - vous devez créer un nouveau.

> [AZURE.NOTE]
    Pas tous les scénarios Azure Active Directory et fonctionnalités sont prises en charge par le point de terminaison version 2.0.  Pour déterminer si vous devez utiliser le point de terminaison version 2.0, lisez les [limitations de la version 2.0](active-directory-v2-limitations.md).

## <a name="visit-the-microsoft-app-registration-portal"></a>Visitez le portail d’inscription de l’application Microsoft
Premières choses que tout d’abord - accédez à [https://apps.dev.microsoft.com/?deeplink=/appList](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList).  Il s’agit d’un nouveau portail d’inscription application où vous pouvez gérer les applications Microsoft.

Connectez-vous à l’aide de des options personnelle ou travailler ou scolaire compte Microsoft.  Si vous n’avez pas soit, inscrivez-vous à un compte personnel. Alors, ne longue : nous allons attendre ici.

Vous avez terminé ? Vous devriez maintenant voir auprès de votre liste d’applications de Microsoft, qui est probablement vide.  Nous allons modifier l’aspect.

Cliquez sur **Ajouter une application**et lui donner un nom.  Le portail affecte votre application un Id globalement unique de l’Application que vous utiliserez plus loin dans votre code.  Si votre application inclut un composant côté serveur qui nécessite des jetons d’accès pour appeler des API (pensez : Office, Azure ou votre propre API web), vous souhaiterez créer une **Application Secret** ici également.
<!-- TODO: Link for app secrets -->

Ensuite, ajoutez les plateformes que votre application utilisera.

- Pour les applications web basé, fournir un **URI rediriger** où se connecter messages peuvent être envoyés.
- Pour les applications mobiles, Recopier vers le bas de l’uri de redirection par défaut créé automatiquement.

Si vous le souhaitez, vous pouvez personnaliser l’apparence de votre page de connexion dans la section de profil.  Veillez à cliquer sur **Enregistrer** avant de poursuivre.

> [AZURE.NOTE] Lorsque vous créez une application à l’aide de [https://apps.dev.microsoft.com/?deeplink=/appList](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList), l’application est enregistrée dans le client accueil du compte que vous utilisez pour se connecter au portail.  Cela signifie que vous ne pouvez pas enregistrer une application dans votre client Azure AD à l’aide d’un compte Microsoft personnel.  Si vous le souhaitez explicitement enregistrer une application dans un client particulier, connectez-vous à l’aide d’un compte à l’origine créé dans ce client.

## <a name="build-a-quick-start-app"></a>Créer une application de démarrage rapide
Maintenant que vous avez une application Microsoft, vous pouvez effectuer une de nos didacticiels de démarrage rapide de version 2.0.  Voici quelques recommandations :

[AZURE.INCLUDE [active-directory-v2-quickstart-table](../../includes/active-directory-v2-quickstart-table.md)]

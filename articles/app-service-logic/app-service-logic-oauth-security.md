<properties
    pageTitle="Sécurité OAUTH SaaS connecteurs et des API applications | Azure"
    description="En savoir plus sur la sécurité OAUTH dans les connecteurs et les applications de l’API dans le Service d’application Azure ; architecture microservices ; SaaS"
    services="logic-apps"
    documentationCenter=""
    authors="MandiOhlinger"
    manager="dwrede"
    editor="cgronlun"/>

<tags
    ms.service="logic-apps"
    ms.workload="integration"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/23/2016"
    ms.author="mandia"/>


# <a name="learn-about-oauth-security-in-saas-connectors"></a>Découvrez la sécurité de jeton dans les connecteurs SaaS

>[AZURE.NOTE] Cette version de l’article s’applique à la version du schéma logique applications 2014-12-01-aperçu.

La plupart du logiciel en tant qu’un connecteurs Service (SaaS) comme Facebook, Twitter, DropBox et ainsi de suite demander aux utilisateurs de s’authentifier à l’aide du protocole OAUTH.  Lorsque vous utilisez ces connecteurs SaaS à partir des applications logique, nous offrent une expérience utilisateur simplifiée où vous cliquez sur « Autoriser » dans le Concepteur d’applications logique. Quand vous **Autoriser**, vous êtes invité à vous connecter à (si ce n’est pas déjà) et fournissent le consentement pour vous connecter au service SaaS à votre place. Une fois que vous fournir consentement et autorisez les, vos applications logique pouvez accéder à ces services SaaS.

## <a name="create-your-own-saas-app"></a>Créer votre propre application SaaS
Cette expérience simplifiée est possible, car nous avons précédemment créé et enregistré notre application dans ces services SaaS.  Dans certains cas, vous souhaiterez enregistrer et utiliser votre propre application.  Cela est nécessaire, par exemple, lorsque vous souhaitez utiliser ces connecteurs SaaS dans vos applications personnalisées. Cet exemple utilise le connecteur DropBox, mais le processus est le même pour tous les connecteurs qui s’appuient sur OAUTH.

Même dans le cadre de la logique d’applications, vous pouvez utiliser votre propre application au lieu d’utiliser l’application par défaut que nous fournissons. Si le bouton « Autoriser » ne parvient pas à vous connecter, vous pouvez essayer de créer votre propre application. Ces étapes pour le connecteur Twitter répertorie les opérations suivantes :

1. Ouvrez le lien Twitter dans le portail Azure preview. Accédez à **Parcourir** > **API applications**. Sélectionnez le lien Twitter :  
    ![][1]

2. Sélectionnez **paramètres** > **authentification**:  
    ![][2]

3. Copiez la valeur **Rediriger URI** :  
    ![][3]

4. Accédez à [Twitter](http://apps.twitter.com) et **créer une nouvelle application**. Dans la propriété **URL de rappel** , collez la valeur de **Rediriger URI** copiée à partir de votre lien Twitter : ![][4]  
5. Lors de la création de votre application Twitter, activez **la clé et jetons d’accès**. Copiez ces valeurs.
6. Dans vos paramètres d’authentification de connecteur Twitter, collez les valeurs suivantes dans les propriétés de **l’ID de Client** et le **Code Secret Client** :   
    ![][5]  
7. Enregistrer vos paramètres de connecteur.  

À présent, vous devriez pouvoir utiliser votre connecteur à partir des applications logique. Lorsque vous utilisez ce connecteur à partir des applications logique, elle utilise votre application au lieu de l’application par défaut.  

> [AZURE.NOTE] Si vous avez précédemment autorisé une application, vous devrez peut-être autoriser à nouveau de l’application.


<!--Image references-->
[1]: ./media/app-service-logic-oauth-security/TwitterConnector.png
[2]: ./media/app-service-logic-oauth-security/Authentication.png
[3]: ./media/app-service-logic-oauth-security/RedirectURI.png
[4]: ./media/app-service-logic-oauth-security/TwitterApp.png
[5]: ./media/app-service-logic-oauth-security/TwitterKeys.png

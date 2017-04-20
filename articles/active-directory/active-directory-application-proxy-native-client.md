<properties
    pageTitle="Comment activer la publication des applications native client avec les applications proxy | Microsoft Azure"
    description="Explique comment activer les applications native client communiquer avec Azure AD Application Proxy Connector à fournir un accès sécurisé à distance à vos applications en local."
    services="active-directory"
    documentationCenter=""
    authors="kgremban"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="06/22/2016"
    ms.author="kgremban"/>

# <a name="how-to-enable-native-client-apps-to-interact-with-proxy-applications"></a>Comment activer les applications native client interagir avec les Applications de proxy

Azure Active Directory Application Proxy est largement utilisée pour publier des applications de navigateur tels que SharePoint, Outlook Web Access et trait personnalisé des applications métier. Il peut également être utilisé pour publier des applications native client, qui sont différents des applications web car les installer sur un appareil. Pour cela, Azure AD jetons qui sont envoyés dans les en-têtes HTTP autoriser standards émis de prise en charge.

![Relation entre les applications publiées, Azure Active Directory et utilisateurs finaux](./media/active-directory-application-proxy-native-client/richclientflow.png)

La méthode recommandée pour publier des applications consiste à utiliser la bibliothèque de l’authentification Azure AD, qui prend en charge tous les contraintes d’authentification et prend en charge de nombreux environnements autre client. Proxy de l’application s’adapte à l' [Application Native scénario d’API Web](active-directory-authentication-scenarios.md#native-application-to-web-api). Le processus permettant d’accomplir cela est la suivante :

## <a name="step-1-publish-your-application"></a>Étape 1 : Publier votre application

Publiez votre application proxy comme vous le feriez pour toute autre application, attribuer aux utilisateurs et leur donner premium ou des licences base. Pour plus d’informations, voir [publier des applications avec Proxy de l’Application](active-directory-application-proxy-publish.md).

## <a name="step-2-configure-your-application"></a>Étape 2 : Configurer votre application

Configurez votre application native comme suit :

1. Connectez-vous au portail classique Azure.
2. Sélectionnez l’icône Active Directory dans le menu de gauche, puis dans l’annuaire.
3. Dans le menu supérieur, cliquez sur **Applications**. Si aucun applications n’ont été ajoutées à votre répertoire, cette page ne montrera que le lien **Ajouter une application** . Cliquez sur le lien, ou vous pouvez également cliquer sur le bouton **Ajouter** dans la barre de commandes.
4. Dans la page **que voulez-vous faire** , cliquez sur le lien Ajouter **une application de développe de mon organisation**.
5. Dans la page **dites-nous relatives à votre application** , spécifiez un nom pour votre application et choisissez **application cliente Native**. Cliquez sur l’icône de flèche pour continuer.
6. Dans la page **informations sur l’Application** , fournissent les **Rediriger URI** pour l’application cliente native, puis cliquez sur la coche en regard de fin.

Votre application a été ajoutée, et vous serez sur la page de démarrage rapide pour votre application.

## <a name="step-3-grant-access-to-other-applications"></a>Étape 3 : Accorder l’accès à d’autres applications

Activer l’application native pour être exposée à d’autres applications dans votre annuaire :

1. Dans le menu supérieur, cliquez sur **Applications**, sélectionnez la nouvelle application native, puis cliquez sur **configurer**.
2. Faites défiler jusqu'à la section **autorisations à d’autres applications** . Cliquez sur le bouton **Ajouter l’application** et sélectionnez l’application proxy que vous souhaitez accorder l’accès application native, puis cliquez sur la coche située dans le coin inférieur droit. Dans le menu déroulant **Délégué les autorisations** , sélectionnez la nouvelle autorisation.

![Autorisations pour les autres applications capture d’écran - Ajouter une application](./media/active-directory-application-proxy-native-client/delegate_native_app.png)

## <a name="step-4-edit-the-active-directory-authentication-library"></a>Étape 4 : Modifier la bibliothèque d’authentification Active Directory

Modifier le code de l’application d’origine dans le cadre de l’authentification de l’Active Directory authentification bibliothèque (terme ADAL) pour inclure les éléments suivants :

        // Acquire Access Token from AAD for Proxy Application
        AuthenticationContext authContext = new AuthenticationContext("https://login.microsoftonline.com/<TenantId>");
        AuthenticationResult result = authContext.AcquireToken("< Frontend Url of Proxy App >",
                                                        "< Client Id of the Native app>",
                                                        new Uri("< Redirect Uri of the Native App>"),
                                                        PromptBehavior.Never);

        //Use the Access Token to access the Proxy Application
        HttpClient httpClient = new HttpClient();
        httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", result.AccessToken);
        HttpResponseMessage response = await httpClient.GetAsync("< Proxy App API Url >");

Les variables doivent être remplacées comme suit :

- **TenantId** sont accessibles dans le GUID dans l’URL de la page de **Configuration** de l’application, juste après « / répertoire / ».
- **Frontend URL** est l’URL du serveur frontal entrées dans l’Application de Proxy et vous pouvez trouver dans la page **Configuration** de l’application de proxy.
- **Id de client** de l’application native sont accessibles dans la page **configuration** de l’application native.
- **Rediriger URI de l’application native** sont accessibles dans la page **configuration** de l’application native.

![Capture d’écran de la page Configurer les nouvelle application native](./media/active-directory-application-proxy-native-client/new_native_app.png)

Pour plus d’informations sur le flux de l’application native, voir [application Native pour API web](active-directory-authentication-scenarios.md#native-application-to-web-api).


## <a name="see-also"></a>Voir aussi

- [Publier des applications à l’aide de votre propre nom de domaine](active-directory-application-proxy-custom-domains.md)
- [Activer l’accès conditionnel](active-directory-application-proxy-conditional-access.md)
- [Utilisation des applications prenant en charge sur les revendications](active-directory-application-proxy-claims-aware-apps.md)
- [Activer l’authentification unique](active-directory-application-proxy-sso-using-kcd.md)

Pour les dernières nouvelles et mises à jour, consultez le [blog de Proxy de l’Application](http://blogs.technet.com/b/applicationproxyblog/)

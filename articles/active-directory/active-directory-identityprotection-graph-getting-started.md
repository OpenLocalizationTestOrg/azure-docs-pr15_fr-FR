<properties
    pageTitle="Prise en main Azure Active Directory identité Protection et Microsoft Graph | Microsoft Azure"
    description="Fournit une introduction à Microsoft Graph de requête pour une liste des événements de risque et les informations associées à partir d’Azure Active Directory."
    services="active-directory"
    keywords="protection d’identité Azure AD, événements risque, vulnérabilité, stratégie de sécurité, Microsoft Graph"
    documentationCenter=""
    authors="markusvi"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/22/2016"
    ms.author="markvi"/>

# <a name="get-started-with-azure-active-directory-identity-protection-and-microsoft-graph"></a>Prise en main Azure Active Directory identité Protection et Microsoft Graph

Microsoft Graph est le point de terminaison de l’API unifiée de Microsoft et de la page d’accueil de [Azure Active Directory identité Protection](active-directory-identityprotection.md) d’API. Notre première API, **identityRiskEvents**, vous permet de requête Microsoft Graph pour une liste des [événements de risque](active-directory-identityprotection-risk-events-types.md) et les informations associées. Cet article vous permet de vous familiariser interroger cette API. Pour des informations détaillées présentation, une documentation complète et l’accès à l’Explorateur de graphique, consultez le [site Microsoft Graph](https://graph.microsoft.io/).

Il existe trois étapes pour accéder aux données de la Protection d’identité via Microsoft Graph :

1. Ajouter une application avec un mot de passe client. 

2. Utilisez ce code secret et quelques autres éléments d’information s’authentifier à Microsoft Graph, dans lequel vous recevez un jeton d’authentification. 

3. Utilisez ce jeton pour effectuer des demandes sur le point de terminaison API et obtenir des données de Protection d’identité.

Avant de commencer, vous devez :

- Privilèges d’administrateur pour créer l’application dans Azure AD
- Le nom de domaine de votre client (par exemple, contoso.onmicrosoft.com)



## <a name="add-an-application-with-a-client-secret"></a>Ajouter une application avec un mot de passe client


1. [Se connecter](https://manage.windowsazure.com) à votre portail Azure classique en tant qu’administrateur. 

1. Sous dans le volet de navigation gauche, cliquez sur **Active Directory**. 

    ![Création d’une application](./media/active-directory-identityprotection-graph-getting-started/tutorial_general_01.png)

2. Dans la liste **répertoire** , sélectionnez le répertoire pour lequel vous souhaitez activer l’intégration d’annuaire.

3. Dans le menu dans la partie supérieure, cliquez sur **Applications**.

    ![Création d’une application](./media/active-directory-identityprotection-graph-getting-started/tutorial_general_02.png)

4. Cliquez sur **Ajouter** en bas de la page.

    ![Création d’une application](./media/active-directory-identityprotection-graph-getting-started/tutorial_general_03.png)

5. Dans la boîte de dialogue **que voulez-vous faire** , cliquez sur **Ajouter une application de développe de mon organisation**.

    ![Création d’une application](./media/active-directory-identityprotection-graph-getting-started/tutorial_general_04.png)


5. Dans la boîte de dialogue **donner à propos de votre application** , effectuez les opérations suivantes :

    ![Création d’une application](./media/active-directory-identityprotection-graph-getting-started/tutorial_general_05.png)

    un. Dans la zone de texte **nom** , tapez un nom pour votre application (par exemple : Application d’API AADIP risque événements).

    b. En tant que **Type**, sélectionnez **Web API Application et/ou Web**.

    c. Cliquez sur **suivant**.


5. Dans la boîte de dialogue **Propriétés de l’application** , effectuez les opérations suivantes :

    ![Création d’une application](./media/active-directory-identityprotection-graph-getting-started/tutorial_general_06.png)

    un. Dans la zone de texte **URL authentification** , tapez `http://localhost`.

    b. Dans la zone de texte **URI ID de l’application** , tapez `http://localhost`.

    c. Cliquez sur **terminé**.


Désormais, vous pouvez configurer votre application.

![Création d’une application](./media/active-directory-identityprotection-graph-getting-started/tutorial_general_07.png)



## <a name="grant-your-application-permission-to-use-the-api"></a>Autoriser l’utilisation de l’API votre application


1. Dans votre page de l’application, dans le menu de la partie supérieure, cliquez sur **configurer**. 

    ![Création d’une application](./media/active-directory-identityprotection-graph-getting-started/tutorial_general_08.png)

2. Dans la section **autorisations à d’autres applications** , cliquez sur **Ajouter application**.

    ![Création d’une application](./media/active-directory-identityprotection-graph-getting-started/tutorial_general_09.png)


2. Dans la boîte de dialogue **autorisations à d’autres applications** , procédez comme suit :

    ![Création d’une application](./media/active-directory-identityprotection-graph-getting-started/tutorial_general_10.png)

    un. Sélectionnez **Microsoft Graph**.

    b. Cliquez sur **terminé**.


1. Cliquez sur **Permissions d’Application : 0**, puis sélectionnez **lire toutes les informations d’événement risque identité**.

    ![Création d’une application](./media/active-directory-identityprotection-graph-getting-started/tutorial_general_11.png)

1. Cliquez sur **Enregistrer** dans la partie inférieure de la page.

    ![Création d’une application](./media/active-directory-identityprotection-graph-getting-started/tutorial_general_12.png)


## <a name="get-an-access-key"></a>Obtenir une touche d’accès rapide

1. Sur votre page de l’application, dans la section **clés** , sélectionnez 1 an sous la forme durée.

    ![Création d’une application](./media/active-directory-identityprotection-graph-getting-started/tutorial_general_13.png)

1. Cliquez sur **Enregistrer** dans la partie inférieure de la page.

    ![Création d’une application](./media/active-directory-identityprotection-graph-getting-started/tutorial_general_12.png)

1. dans la section touches, copiez la valeur de votre clé nouvellement créé et collez-le dans un emplacement sécurisé.

    ![Création d’une application](./media/active-directory-identityprotection-graph-getting-started/tutorial_general_14.png)

    > [AZURE.NOTE] Si vous perdez cette clé, vous devrez revenir à cette section et créer une nouvelle clé. Conservez cette clé un secret : toutes les personnes qui l’a peuvent accéder à vos données.


1. Dans la section **Propriétés** , copiez l' **ID de Client**et collez-le dans un emplacement sécurisé. 


## <a name="authenticate-to-microsoft-graph-and-query-the-identity-risk-events-api"></a>S’authentifier à Microsoft Graph et l’API identité risque événements de requête

À ce stade, vous devez avoir :

- L’ID de client que vous avez copié au-dessus

- La clé que vous avez copié au-dessus

- Le nom de domaine de votre client


Pour l’authentification, envoyez une demande de publication pour `https://login.microsoft.com` avec les paramètres suivants dans le corps :

- grant_type : «**client_credentials**»

- ressource : «**https://graph.microsoft.com**»

- identifiant_client :<your client ID>

- client_secret :<your key>


> [AZURE.NOTE] Vous devez fournir des valeurs pour **identifiant_client** et le paramètre **client_secret** .

En cas de réussite, cela renvoie un jeton d’authentification.  
Pour appeler l’API, créez un en-tête avec le paramètre suivant :

    `Authorization`=”<token_type> <access_token>"


Lors de l’authentification, vous pouvez trouver le type de jeton et jeton d’accès dans le jeton retourné.

Envoyer cet en-tête comme une requête à l’URL de l’API suivants :`https://graph.microsoft.com/beta/identityRiskEvents`

La réponse en cas de réussite, est un ensemble d’événements de risque d’identité et données associées dans le format OData JSON, qui peut être analysée et gérée comme vos besoins.

Voici des exemples de code pour l’authentification et l’appel de l’API à l’aide de Powershell.  
Il suffit d’ajouter votre ID client, clé et domaine du client.

    $ClientID       = "<your client ID here>"        # Should be a ~36 hex character string; insert your info here
    $ClientSecret   = "<your client secret here>"    # Should be a ~44 character string; insert your info here
    $tenantdomain   = "<your tenant domain here>"    # For example, contoso.onmicrosoft.com

    $loginURL       = "https://login.microsoft.com"
    $resource       = "https://graph.microsoft.com"

    $body       = @{grant_type="client_credentials";resource=$resource;client_id=$ClientID;client_secret=$ClientSecret}
    $oauth      = Invoke-RestMethod -Method Post -Uri $loginURL/$tenantdomain/oauth2/token?api-version=1.0 -Body $body

    Write-Output $oauth

    if ($oauth.access_token -ne $null) {
        $headerParams = @{'Authorization'="$($oauth.token_type) $($oauth.access_token)"}

        $url = "https://graph.microsoft.com/beta/identityRiskEvents"
        Write-Output $url

        $myReport = (Invoke-WebRequest -UseBasicParsing -Headers $headerParams -Uri $url)

        foreach ($event in ($myReport.Content | ConvertFrom-Json).value) {
            Write-Output $event
        }

    } else {
        Write-Host "ERROR: No Access Token"
    } 


## <a name="next-steps"></a>Étapes suivantes

Félicitations, vous venez de créer votre premier appel à Microsoft Graph.  
Vous pouvez désormais interroger événements de risque d’identité et utiliser les données de façon la plus appropriée.

Pour en savoir plus sur Microsoft Graph et explique comment créer des applications à l’aide de l’API Graph, vérifiez la [documentation](https://graph.microsoft.io/docs) et beaucoup plus sur le [site Microsoft Graph](https://graph.microsoft.io/). En outre, vérifiez que signet de la page de [protection des données Azure AD personnelles API](https://graph.microsoft.io/docs/api-reference/beta/resources/identityprotection_root) qui répertorie tous les API de Protection identité disponibles dans le graphique. À mesure que nous ajoutez de nouvelles méthodes de travail avec Protection d’identité via l’API, ils apparaissent dans la page.


## <a name="additional-resources"></a>Ressources supplémentaires

- [Protection d’identité Azure Active Directory](active-directory-identityprotection.md)

- [Types d’événements risque détectés par Azure Active Directory identité Protection](active-directory-identityprotection-risk-events-types.md)

- [Microsoft Graph](https://graph.microsoft.io/)

- [Vue d’ensemble de Microsoft Graph](https://graph.microsoft.io/docs)

- [Racine du Service Azure AD identité Protection](https://graph.microsoft.io/docs/api-reference/beta/resources/identityprotection_root)

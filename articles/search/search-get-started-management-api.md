<properties 
    pageTitle="Prise en main l’API REST de gestion de recherche d’Azure | Microsoft Azure | Service de recherche cloud hébergé" 
    description="Administrer votre service de recherche Azure à l’aide d’une API REST de gestion de cloud hébergé" 
    services="search" 
    documentationCenter="" 
    authors="HeidiSteen" 
    manager="jhubbard" 
    editor=""/>

<tags 
    ms.service="search" 
    ms.devlang="rest-api" 
    ms.workload="search" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.date="10/17/2016" 
    ms.author="heidist"/>

# <a name="get-started-with-azure-search-management-rest-api"></a>Prise en main l’API REST de gestion de recherche d’Azure
> [AZURE.SELECTOR]
- [Portail](search-manage.md)
- [PowerShell](search-manage-powershell.md)
- [API REST](search-get-started-management-api.md)

L’API de gestion Azure recherche reste est une alternative de programmation pour effectuer des tâches administratives dans le portail. Opérations de gestion du service incluent création ou suppression du service, le service de mise à l’échelle et gestion des clés. Ce didacticiel est fourni avec un exemple d’application cliente qui montre l’API de gestion de service. Il inclut également des étapes de configuration nécessaires pour exécuter l’exemple dans votre environnement de développement local.

Pour effectuer ce didacticiel, vous aurez besoin :

- Visual Studio 2012 ou 2013
- le téléchargement d’application exemple client

Dans le cadre de la fin du didacticiel, deux services seront déployés : recherche Azure et Azure Active Directory (AD). En outre, vous allez créer une application AD qui établit une approbation entre votre application cliente et le point de terminaison de gestionnaire de ressources dans Azure.

Vous avez besoin d’un compte Azure pour effectuer ce didacticiel.


##<a name="download-the-sample-application"></a>Télécharger l’exemple d’application

Ce didacticiel est basé sur une application de console Windows écrite en c#, que vous pouvez modifier et exécuter dans Visual Studio 2012 ou 2013

Vous pouvez trouver l’application cliente sur Github en [Azure recherche .NET gestion des API démo](https://github.com/Azure-Samples/search-dotnet-management-api/).


##<a name="configure-the-application"></a>Configurer l’application

Avant de pouvoir exécuter l’exemple d’application, vous devez activer l’authentification afin que les demandes envoyées à partir de l’application cliente à l’extrémité de gestionnaire de ressources peuvent être acceptées. La nécessité d’authentification provient avec le [Gestionnaire de ressources Azure](https://msdn.microsoft.com/library/azure/dn790568.aspx), qui sert de base pour toutes les opérations liées portail demandé par le biais d’API, y compris ceux liés à la gestion de service de recherche. La gestion des services API pour la recherche Azure est simplement une extension du Gestionnaire de ressources Azure et par conséquent hérite ses dépendances.  

Le Gestionnaire de ressources Azure nécessite le service d’Azure Active Directory en tant que fournisseur d’identité. 

Pour obtenir un jeton d’accès qui autorise les demandes d’atteindre le Gestionnaire de ressources, l’application cliente inclut un segment de code qui appelle Active Directory. Le segment de code, ainsi que les étapes préalables à l’aide du segment de code, ont été empruntés à partir de cet article : [les demandes d’authentification Azure directeur des ressources](http://msdn.microsoft.com/library/azure/dn790557.aspx).

Vous pouvez suivre les instructions fournies dans le lien ci-dessus, ou utiliser les étapes décrites dans ce document si vous préférez parcourir le didacticiel détaillé.

Dans cette section, vous allez effectuer les tâches suivantes :

1. Créer un service d’annonces
1. Créer une application AD
1. Configurer l’application AD en enregistrant plus d’informations sur l’application cliente exemple que vous avez téléchargée
1. Charger l’exemple d’application cliente avec des valeurs qu'il utilisera pour obtenir l’autorisation pour ses demandes

> [AZURE.NOTE] Ces liens fournissent un arrière-plan à l’aide d’Azure Active Directory pour l’authentification des demandes des clients au Gestionnaire de ressources : [Gestionnaire de ressources Azure](http://msdn.microsoft.com/library/azure/dn790568.aspx), [demandes d’authentification Azure directeur des ressources](http://msdn.microsoft.com/library/azure/dn790557.aspx)et [Azure Active Directory](http://msdn.microsoft.com/library/azure/jj673460.aspx).

###<a name="create-an-active-directory-service"></a>Créer un Service d’annuaire Active Directory

1. Connectez-vous au [portail Azure](https://manage.windowsazure.com).

2. Faites défiler le volet de navigation gauche, puis cliquez sur **Active Directory**.

4. Cliquez sur **Nouveau** pour ouvrir les **Services d’application** | **Active Directory**. Dans cette étape, vous créez un nouveau service Active Directory. Ce service héberge l’application AD que vous définirez quelques étapes sont nécessaires à partir de maintenant. Création d’un nouveau service permet d’isoler le didacticiel provenant d’autres applications que vous pouvez héberger déjà dans Azure.

5. Cliquez sur **le répertoire** | **créer personnalisé**.

6. Entrez un nom de service, domaine et géolocalisation. Le domaine doit être unique. Activez la case à cocher pour créer le service.

     ![][5]

###<a name="create-a-new-ad-application-for-this-service"></a>Créer une nouvelle application AD pour ce service

1. Sélectionnez le service d’annuaire Active « SearchTutorial » que vous venez de créer.

2. Dans le menu supérieur, cliquez sur **Applications**. 
 
3. Cliquez sur **Ajouter une Application**. Une application AD stocke des informations sur les applications clientes qui utilisent il comme fournisseur d’identité.  
 
4. Cliquez sur **Ajouter une application de développe de mon organisation**. Cette option fournit des paramètres d’enregistrement pour les applications qui ne sont pas publiées dans la galerie d’application. Étant donné que l’application cliente ne fait pas partie de la galerie d’applications, il s’agit le bon choix pour ce didacticiel.

     ![][6]
 
5. Entrez un nom, tel que « Gestionnaire de recherches Azure ».

6. Choisissez **l’application cliente Native** pour type d’application. Cette valeur correspond à l’exemple d’application ; Il se trouve qu’une application cliente (console) de Windows, n’est pas une application web.

     ![][7]
 
7. Dans Rediriger URI, entrez « http://localhost/Azure-Search-Manager-App ». C’est un URI à quels Azure Active Directory redirigera l’agent utilisateur en réponse à une demande d’autorisation OAuth 2.0. La valeur n’a pas besoin d’être un point de terminaison physique, mais doit être un URI valide. 

    Aux fins de ce didacticiel, la valeur peut être quelconque, mais que vous entrez devient une entrée requise pour la connexion d’administration dans l’exemple d’application. 
 
7. Activez la case à cocher pour créer l’application Active Directory. Vous devriez voir « Azure-recherche-Gestionnaire-App » dans le volet de navigation gauche.

###<a name="configure-the-ad-application"></a>Configurer l’application AD
 
9. Cliquez sur l’application AD, « Azure-recherche-Gestionnaire-App », que vous venez de créer. Il doit se qu'affiche dans le volet de navigation gauche.

10. Cliquez sur **configurer** dans le menu supérieur.
 
11. Faites défiler jusqu'à autorisations et sélectionnez **API de gestion des Azure**. Dans cette étape, vous spécifiez l’API (dans ce cas, l’API du Gestionnaire de ressources Azure) que l’application cliente a besoin d’accéder à, ainsi que le niveau d’accès dont il a besoin.

12. Dans autorisations délégués, cliquez sur la liste déroulante et sélectionnez **Gestion des services Azure Access (Preview**).
 
     ![][8]
 
13. Enregistrer les modifications. 

Laissez la page de configuration d’application ouverte. Dans l’étape suivante, vous copiez les valeurs de cette page et les entrer dans l’exemple d’application.

###<a name="load-the-sample-application-program-with-registration-and-subscription-values"></a>Charger le programme d’application exemple avec des valeurs de l’enregistrement et l’abonnement

Dans cette section, vous devez modifier la solution dans Visual Studio, en substituant des valeurs valides obtenus à partir du portail.
Les valeurs que vous ajoutez s’affichent dans la partie supérieure de Program.cs :

        private const string TenantId = "<your tenant id>";
        private const string ClientId = "<your client id>";
        private const string SubscriptionId = "<your subscription id>";
        private static readonly Uri RedirectUrl = new Uri("<your redirect url>");

Si vous n’avez pas encore [téléchargé l’exemple d’application à partir de Github](https://github.com/Azure-Samples/search-dotnet-management-api/), vous en aurez besoin pour cette étape.

1. Ouvrez la **ManagementAPI.sln** dans Visual Studio.

2. Ouvrez Program.cs.

3. Fournir `ClientId`. À partir de l’application AD configuration page gauche ouvrir à partir de l’étape précédente, copiez l’ID de Client à partir de la page de configuration d’application AD dans le portail et collez-le dans Program.cs.

4. Fournir `RedirectUrl`. Copiez rediriger URI à partir de la même page du portail et collez-le dans Program.cs.

    ![][9]

5. Fournir`TenantID.` 
    - Revenez à Active Directory | SearchTutorial (service). 
    - Cliquez sur **Applications** à partir de la barre supérieure. 
    - Cliquez sur **Les points de terminaison de mode** dans la partie inférieure de la page. 
    - Copier le point de terminaison OAUTH 2.0 d’autorisation en bas de la liste. 
    - Collez le point de terminaison TenantID, réduction de la valeur de tous les paramètres URI à l’exception de l’ID de client.

    Étant donné « https://login.windows.net/55e324c7-1656-4afe-8dc3-43efcd4ffa50/oauth2/authorize?api-version=1.0 », supprimez tout sauf « 55e324c7-1656-4afe-8dc3-43efcd4ffa50 ».

    ![][10]

6. Fournir `SubscriptionID`.
    - Accédez à la page du portail principale.
    - Cliquez sur **paramètres** en bas du volet de navigation gauche.
    - Sous l’onglet Abonnements, copiez l’ID de l’abonnement et collez-le dans Program.cs.

7. Enregistrer avant de générer la solution.


##<a name="explore-the-application"></a>Explorer l’application

Ajouter un point d’arrêt au premier appel méthode afin que vous pouvez lancer le programme. Appuyez sur **F5** pour exécuter l’application et appuyez sur **F11** pour parcourir le code.

L’exemple d’application crée un service de recherche Azure gratuit pour un abonnement Azure existant. S’il existe déjà un service gratuit pour votre abonnement, l’exemple d’application échouera. Service de recherche gratuit qu’un seul par abonnement est autorisé.

1. Ouvrez Program.cs à partir de l’Explorateur de solutions et accédez à la fonction Main (string [] void). 
 
3. Notez que **ExecuteArmRequest** est utilisé pour exécuter des requêtes le point de terminaison Azure le Gestionnaire de ressources, `https://management.azure.com/subscriptions` spécifié `subscriptionID`. Cette méthode est utilisée dans l’ensemble du programme pour effectuer des opérations à l’aide de l’API du Gestionnaire de ressources Azure ou API de gestion de recherche.

3. Demandes au Gestionnaire de ressources Azure doivent être authentifiés et autorisés. Cela revient à l’aide de la méthode **GetAuthorizationHeader** , appelée par la méthode **ExecuteArmRequest** , provenant de [requêtes d’authentification Azure directeur des ressources](http://msdn.microsoft.com/library/azure/dn790557.aspx). Notez que **GetAuthorizationHeader** appelle `https://management.core.windows.net` pour obtenir un jeton d’accès.

4. Vous êtes invité à vous connecter avec un nom d’utilisateur et mot de passe valide pour votre abonnement.

5. Ensuite, un nouveau service de recherche Azure est enregistré avec le fournisseur Azure le Gestionnaire de ressources. Là encore, il s’agit de la méthode **ExecuteArmRequest** , utilisée cette fois-ci pour créer le service de recherche sur Azure pour votre abonnement via `providers/Microsoft.Search/register`. 

6. Le reste du programme utilise l' [API REST de gestion des recherche Azure](http://msdn.microsoft.com/library/dn832684.aspx). Notez que la `api-version` pour cette API est la différence entre la version de api Azure le Gestionnaire de ressources. Par exemple, `/listAdminKeys?api-version=2014-07-31-Preview` affiche la `api-version` de l’API REST de Azure recherche gestion.

    La série suivante d’opérations de récupérer la définition du service que vous venez de créer, les clés de l’api d’administration, régénère extrait les touches, modifie la copie et la partition et supprime enfin le service.

    Lorsque vous modifiez le nombre de réplica ou partition du service, il est prévu que cette action échoue si vous utilisez l’édition gratuite. Uniquement l’édition standard peut rendre utiliser des partitions supplémentaires et des duplications.

    Suppression du service est la dernière opération.

##<a name="next-steps"></a>Étapes suivantes

Après avoir terminé ce didacticiel, vous souhaiterez peut-être en savoir plus sur la gestion des services ou de l’authentification avec le service Active Directory :

- En savoir plus sur l’intégration d’une application cliente avec Active Directory. Consultez [l’intégration des Applications dans Azure Active Directory](http://msdn.microsoft.com/library/azure/dn151122.aspx).
- Découvrez les autres opérations de gestion de service dans Azure. Reportez-vous à [gestion de vos Services](http://msdn.microsoft.com/library/azure/dn578292.aspx).

<!--Anchors-->
[Download the sample application]: #Download
[Configure the application]: #config
[Explore the application]: #explore
[Next Steps]: #next-steps

<!--Image references-->
[5]: ./media/search-get-started-management-api/Azure-Search-MGMT-AD-Service.PNG
[6]: ./media/search-get-started-management-api/Azure-Search-MGMT-AD-App.PNG
[7]: ./media/search-get-started-management-api/Azure-Search-MGMT-AD-App-prop.PNG
[8]: ./media/search-get-started-management-api/Azure-Search-MGMT-AD-ConfigPermissions.PNG
[9]: ./media/search-get-started-management-api/Azure-Search-MGMT-AD-ConfigPage.PNG
[10]: ./media/search-get-started-management-api/Azure-Search-MGMT-AD-OAuthEndpoint.PNG

<!--Link references-->
[Manage your search solution in Microsoft Azure]: search-manage.md
[Azure Search development workflow]: search-workflow.md
[Create your first azure search solution]: search-create-first-solution.md
[Create a geospatial search app using Azure Search]: search-create-geospatial.md


 

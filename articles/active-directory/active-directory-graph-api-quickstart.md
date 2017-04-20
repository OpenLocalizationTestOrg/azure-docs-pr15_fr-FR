<properties
   pageTitle="Démarrage rapide pour le graphique AD Azure API | Microsoft Aure"
   description="L’API de Graph Azure Active Directory offre un accès par programme aux Azure AD via les points de terminaison API REST OData. Applications peuvent utiliser l’API Graph pour effectuer créer, lire, mettre à jour et supprimer les opérations sur les objets et les données de l’annuaire."
   services="active-directory"
   documentationCenter="n/a"
   authors="PatAltimore"
   manager="mbaldwin"
   editor=""
   tags=""/>


   <tags
      ms.service="active-directory"
      ms.devlang="na"
      ms.topic="article"
      ms.tgt_pltfrm="na"
      ms.workload="identity"
      ms.date="09/16/2016"
      ms.author="patricka"/>

# <a name="quickstart-for-the-azure-ad-graph-api"></a>Démarrage rapide pour le graphique AD Azure API

L’API Graph Azure Active Directory (AD) offre un accès par programme aux Azure AD et points de terminaison de l’API REST OData. Applications peuvent utiliser l’API Graph pour effectuer créer, lire, mettre à jour et supprimer les opérations sur les objets et les données de l’annuaire. Par exemple, vous pouvez utiliser l’API Graph pour créer un nouvel utilisateur, afficher ou mettre à jour les propriétés de l’utilisateur, modifier le mot de passe de l’utilisateur, vérifier l’appartenance aux groupes pour l’accès basé sur un rôle, désactiver ou supprimer l’utilisateur. Pour plus d’informations sur les fonctionnalités de l’API de graphique et les scénarios d’application, voir [l’API Azure AD Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog) et [conditions préalables à l’API Azure AD Graph](https://msdn.microsoft.com/library/hh974476.aspx). 

> [AZURE.IMPORTANT] Azure AD Graph API fonctionnalité est également disponible via [Microsoft Graph](https://graph.microsoft.io/), une API unifiée qui inclut des API à partir d’autres services Microsoft tels que Outlook OneDrive, OneNote, planificateur et Office Graph, accessible via un point de terminaison unique et avec un jeton d’accès unique.

## <a name="how-to-construct-a-graph-api-url"></a>Comment créer une URL de l’API de graphique

Dans Graph API, afin d’accéder aux données de l’annuaire et objets (autrement dit, ressources ou entités) par rapport à laquelle vous voulez effectuer des opérations CRUD, vous pouvez utiliser URL basées sur le protocole Open Data (OData). Les URL utilisées dans l’API Graph consistant de quatre parties principales : racine, un identificateur client, chemin d’accès de ressources et options de chaîne de requête de service : `https://graph.windows.net/{tenant-identifier}/{resource-path}?[query-parameters]`. Prenons l’exemple de l’URL suivante : `https://graph.windows.net/contoso.com/groups?api-version=1.6`.

- **Racine du service**: dans Azure AD Graph API, la racine du service est toujours https://graph.windows.net.
- **Identificateur client**: Ceci peut être un nom de domaine (inscrits) vérifié, dans l’exemple ci-dessus, contoso.com. Il peut également être alias un ID d’objet client ou la « myorganiztion » ou « me ». Pour plus d’informations, consultez [définir les destinataires d’entités et opérations dans l’API Graph).](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-operations-overview)
- **Chemin de la ressource**: cette section d’une URL identifie la ressource pour exploiter (utilisateurs, groupes, un utilisateur spécifique, ou un groupe spécifique, etc.) Dans l’exemple ci-dessus, il est « groupes de niveau supérieur « adresse jeu de ressources. Vous pouvez également traiter une entité spécifique, par exemple « utilisateurs / {objectId} » ou « utilisateurs/userPrincipalName ».
- **Paramètres de la requête**: ? sépare la partie du chemin d’accès de ressources à partir de la section Paramètres de requête. Le paramètre de requête « api version » est requis pour toutes les demandes de l’API de graphique. L’API Graph prend également en charge les options de requête OData suivantes : **$filter**, **$orderby**, **développez $**, **$top**et **$format**. Les options de requête suivantes ne sont pas actuellement pris en charge : **$count** **$inlinecount**et **$skip**. Pour plus d’informations, voir [prise en charge des requêtes, des filtres et des Options de pagination dans Azure AD Graph API](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-supported-queries-filters-and-paging-options).

## <a name="graph-api-versions"></a>Versions Graph API

Vous spécifiez la version pour une demande d’API de graphique dans le paramètre de requête « api version ». Pour version 1,5 et versions ultérieure, vous utilisez une valeur numérique version ; version de l’API = 1,6. Pour les versions antérieures, vous utilisez une chaîne de date qui respecte le format AAAA-MM-JJ ; par exemple, de l’api version = 2013-11-08. Pour les fonctionnalités d’utiliser la chaîne « bêta » ; par exemple, de l’api version = bêta. Pour plus d’informations sur les différences entre les versions de l’API Graph, voir [le contrôle de version API Azure AD Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-versioning).

## <a name="graph-api-metadata"></a>Métadonnées Graph API

Pour renvoyer le fichier de métadonnées de l’API Graph, ajoutez le segment « $metadata » après l’identificateur de client dans l’exemple d’URL pour, l’URL suivante retourne les métadonnées de la société de démonstration utilisée par l’Explorateur Graph : `https://graph.windows.net/GraphDir1.OnMicrosoft.com/$metadata?api-version=1.6`. Vous pouvez entrer cette URL dans la barre d’adresse d’un navigateur web pour afficher les métadonnées. Le document de métadonnées CSDL renvoyé décrit les entités et types complexes, leurs propriétés et les fonctions et les actions exposées par la version de l’API Graph souhaitée. L’omission du paramètre de version de l’api renverra métadonnées pour la version la plus récente.

## <a name="common-queries"></a>Requêtes communes

[Requêtes communes Azure AD Graph API](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-supported-queries-filters-and-paging-options#CommonQueries) répertorie les requêtes courantes qui peuvent être utilisés avec le graphique Azure AD, y compris les requêtes peuvent être utilisées pour accéder aux ressources de niveau supérieur dans votre annuaire et des requêtes pour effectuer des opérations dans votre annuaire.

Par exemple, `https://graph.windows.net/contoso.com/tenantDetails?api-version=1.6` renvoie des informations pour contoso.com répertoire société.

Ou `https://graph.windows.net/contoso.com/users?api-version=1.6` répertorie tous les objets utilisateur dans le répertoire contoso.com.

## <a name="using-the-graph-explorer"></a>À l’aide de l’Explorateur de graphique

Vous pouvez utiliser l’Explorateur de graphique pour l’API Azure AD Graph pour interroger des données de l’annuaire à mesure que vous créez votre application.

> [AZURE.IMPORTANT] L’Explorateur de graphique ne reconnaît pas écrire ou à la suppression des données à partir d’un répertoire. Vous ne pouvez effectuer les opérations de lecture sur votre répertoire Azure AD avec l’Explorateur de graphique.

Voici le résultat que vous voyez si vous deviez naviguer dans l’Explorateur de graphique, sélectionnez utiliser démo société et entrez `https://graph.windows.net/GraphDir1.OnMicrosoft.com/users?api-version=1.6` pour afficher tous les utilisateurs dans l’annuaire de démonstration :

![Explorer l’api de graph Azure AD](./media/active-directory-graph-api-quickstart/graph_explorer.png)

**Charger l’Explorateur Graph**: pour charger l’outil, accédez à [https://graphexplorer.cloudapp.net/](https://graphexplorer.cloudapp.net/). Cliquez sur la **Société de démonstration d’utilisation** pour exécuter l’Explorateur Graph sur des données à partir d’un client exemple. Vous n’avez pas besoin d’informations d’identification à utiliser la société de démonstration. Vous pouvez également cliquer sur **se connecter** et connectez-vous à l’aide de vos informations d’identification du compte Azure AD pour exécuter l’Explorateur de graphique par rapport à votre client. Si vous exécutez Explorer Graph sur votre propre client, vous ou votre administrateur devrez consentement au cours de connexion. Si vous avez un abonnement Office 365, vous avez automatiquement un client Azure AD. Les informations d’identification que vous utilisez pour vous connecter à Office 365 sont en fait, comptes Azure AD, et vous pouvez utiliser ces informations d’identification avec l’Explorateur de graphique.

**Exécuter une requête**: pour exécuter une requête, tapez votre requête dans la zone de texte de requête et cliquez sur **obtenir** ou cliquez sur la touche **entrée** . Les résultats sont affichés dans la zone réponse. Par exemple, `https://graph.windows.net/graphdir1.onmicrosoft.com /groups?api-version=1.6` permet de répertorier tous les objets de groupe dans le répertoire démo.

Notez les fonctionnalités et limitations de l’Explorateur de graphiques suivantes :
- Fonctionnalité de saisie semi-automatique sur les ensembles de ressources. Pour voir cela, cliquez sur **Utiliser démo société** , puis sur la zone de texte demande (où apparaît l’URL de la société). Vous pouvez sélectionner une ressource dans la liste déroulante.

- Prend en charge la valeur « me » et « nommée » adressage alias. Par exemple, vous pouvez utiliser `https://graph.windows.net/me?api-version=1.6` pour renvoyer l’objet utilisateur de l’utilisateur connecté ou `https://graph.windows.net/myorganization/users?api-version=1.6` pour renvoyer tous les utilisateurs dans le répertoire actif. Notez qu’à l’aide de la valeur « me » alias retourne une erreur pour la société de démonstration, car il n’existe aucun utilisateur connecté dans qui effectue la demande.

- Une section d’en-tête de réponse. Cela peut servir à résoudre les problèmes qui se produisent lors de l’exécution des requêtes.

- Une visionneuse JSON pour la réponse aux fonctionnalités développer et réduire.

- Aucune prise en charge pour l’affichage d’une photo miniature.

## <a name="using-fiddler-to-write-to-the-directory"></a>À l’aide de Fiddler à écrire dans l’annuaire

Aux fins de ce guide de démarrage rapide, vous pouvez utiliser le débogueur Web Fiddler afin de l’exercice pratique « écrire » opérations contre vous répertoire Azure AD. Pour plus d’informations et pour installer Fiddler, consultez [http://www.telerik.com/fiddler](http://www.telerik.com/fiddler).

Dans l’exemple ci-dessous, vous allez utiliser Fiddler Web débogueur pour créer un nouveau groupe de sécurité « MyTestGroup » dans votre annuaire Azure AD.

**Obtenir un jeton d’accès**: pour accéder à Azure AD Graph, les clients doivent authentifiés à Azure AD tout d’abord. Pour plus d’informations, voir [Scénarios d’authentification pour Azure AD](active-directory-authentication-scenarios.md).

**Composition et exécuter une requête**: suivez les étapes ci-dessous.

1. Ouvrez Fiddler Web débogueur et accédez à l’onglet **Composer** .
2. Dans la mesure où vous voulez créer un nouveau groupe de sécurité, sélectionnez **Publier** en tant que la méthode HTTP dans le menu déroulant. Pour plus d’informations sur les opérations et les autorisations sur un objet de groupe, voir [groupe](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#GroupEntity) au sein de la [référence de l’API REST Azure AD Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog).
3. Dans le champ en regard du **billet**, tapez les éléments suivants en tant que l’URL de requête : `https://graph.windows.net/mytenantdomain/groups?api-version=1.6`.

    > [AZURE.NOTE] Vous devez remplacer par mytenantdomain avec le nom de domaine de votre propre répertoire Azure AD.

4. Dans le champ directement en dessous de publication liste déroulante, tapez les informations suivantes :

    ```
Host: graph.windows.net
Authorization: your access token
Content-Type: application/json
```

    > [AZURE.NOTE] Substitue votre &lt;votre jeton d’accès&gt; avec le jeton d’accès pour votre répertoire Azure AD.

5. Dans le champ **corps de requête** , tapez les informations suivantes :

    ```
        {
            "displayName":"MyTestGroup",
            "mailNickname":"MyTestGroup",
            "mailEnabled":"false",
            "securityEnabled": true
        }
```

    Pour plus d’informations sur la création de groupes, voir [Créer un groupe](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/groups-operations#CreateGroup).

Pour plus d’informations sur les entités Azure AD et les types exposés par graphique et d’informations sur les opérations qui peuvent être effectuées sur ceux-ci avec Graph, voir [référence de l’API REST Azure AD Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog).

## <a name="next-steps"></a>Étapes suivantes

- En savoir plus sur l' [API Azure AD Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog)
- Pour plus d’informations sur les [étendues Azure AD Graph API d’autorisation](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-permission-scopes)

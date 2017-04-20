<properties
   pageTitle="Gestionnaire de ressources reste API | Microsoft Azure"
   description="Une vue d’ensemble des exemples de l’authentification et l’utilisation des API REST de gestionnaire de ressources"
   services="azure-resource-manager"
   documentationCenter="na"
   authors="navalev"
   manager="timlt"
   editor=""/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="06/23/2016"
   ms.author="navale;tomfitz;"/>
   
# <a name="resource-manager-rest-apis"></a>API REST de gestionnaire de ressources

> [AZURE.SELECTOR]
- [PowerShell Azure](powershell-azure-resource-manager.md)
- [Azure infrastructure du langage commun](xplat-cli-azure-resource-manager.md)
- [Portail](./azure-portal/resource-group-portal.md) 
- [API REST](resource-manager-rest-api.md)

Derrière chaque appel au Gestionnaire de ressources Azure, derrière chaque modèle déployé, derrière chaque compte de stockage configuré est un ou plusieurs appels à API RESTful du Gestionnaire de ressources Azure. Cette rubrique est consacrée à ces API et comment vous pouvez les appeler sans à l’aide de n’importe quel SDK du tout. Cela peut être très utile si vous souhaitez que le contrôle total de toutes les requêtes vers Azure ou si le Kit de développement pour la langue par défaut n’est pas disponible ou non prises en charge les opérations que vous souhaitez effectuer.

Cet article ne sera pas traitée chaque API qui est exposé dans Azure, mais préférez utilisera certaines par exemple comment vous continuez et s’y connecter. Si vous connaissez les concepts de base vous pouvez alors et lisez la [Référence de l’API REST Azure Gestionnaire de ressources](https://msdn.microsoft.com/library/azure/dn790568.aspx) pour trouver des informations détaillées sur la façon d’utiliser le reste des API.

## <a name="authentication"></a>Authentification
Authentification pour processeur est gérée par Azure Active Directory (AD). Pour vous connecter à n’importe quelle API vous devez tout d’abord vous authentifier avec Azure AD pour recevoir un jeton d’authentification que vous pouvez passer à chaque demande. Comme nous sommes décrivant un appel intégral directement à l’API REST, également suppose que vous ne voulez pas vous authentifier avec un mot de passe nom d’utilisateur normal où un pop-haut-écran peut vous demander nom d’utilisateur et mot de passe et peut-être même autres mécanismes d’authentification utilisés dans les deux scénarios d’authentification facteur. Par conséquent, nous allons créer ce qui est appelé une Application Azure AD et un Principal de Service qui sera utilisée pour vous connecter avec. Mais n’oubliez pas que Azure AD prend en charge plusieurs procédures d’authentification et celles-ci peuvent être utilisées pour récupérer ce jeton d’authentification dont nous avons besoin pour les demandes suivantes d’API.
Pour obtenir des instructions étape par étape, suivez [créer Azure AD Application et principe de Service](./resource-group-create-service-principal-portal.md) .

### <a name="generating-an-access-token"></a>Générer un jeton d’accès 
Authentification par rapport à Azure AD résulte de l’appel des Azure AD, situé à login.microsoftonline.com. Afin de s’authentifier vous devez disposer des informations suivantes :

* ID de client Azure AD (le nom de cette Azure AD que vous utilisez pour vous connecter, souvent identique à votre société mais n’est pas nécessaire)
* ID de l’application (prise lors de l’étape de création d’une application Azure AD)
* Mot de passe (que vous avez sélectionné lors de la création de l’Application AD Azure)

Dans la demande HTTP ci-dessous veillez à remplacer « ID de client Azure AD », « ID de l’Application » et « Mot de passe » avec les valeurs correctes.

**Demande HTTP générique :**

```HTTP
POST /<Azure AD Tenant ID>/oauth2/token?api-version=1.0 HTTP/1.1 HTTP/1.1
Host: login.microsoftonline.com
Cache-Control: no-cache
Content-Type: application/x-www-form-urlencoded

grant_type=client_credentials&resource=https%3A%2F%2Fmanagement.core.windows.net%2F&client_id=<Application ID>&client_secret=<Password>
```

... will (si l’authentification réussit) génère une réponse similaire à ceci :

```json
{
  "token_type": "Bearer",
  "expires_in": "3600",
  "expires_on": "1448199959",
  "not_before": "1448196059",
  "resource": "https://management.core.windows.net/",
  "access_token": "eyJ0eXAiOiJKV1QiLCJhb...86U3JI_0InPUk_lZqWvKiEWsayA"
}
```
(L’access_token dans la réponse ci-dessus ont été raccourcie pour améliorer la lisibilité)

**Génération du jeton d’accès à l’aide de Bash :**

```console
curl -X POST -H "Content-Type: application/x-www-form-urlencoded" -d "grant_type=client_credentials&resource=https://management.core.windows.net&client_id=<application id>&client_secret=<password you selected for authentication>" https://login.microsoftonline.com/<Azure AD Tenant ID>/oauth2/token?api-version=1.0
```

**Génération du jeton d’accès à l’aide de PowerShell :**

```powershell
Invoke-RestMethod -Uri https://login.microsoftonline.com/<Azure AD Tenant ID>/oauth2/token?api-version=1.0 -Method Post
 -Body @{"grant_type" = "client_credentials"; "resource" = "https://management.core.windows.net/"; "client_id" = "<application id>"; "client_secret" = "<password you selected for authentication>" }
```

La réponse contient un jeton d’accès, informations sur l’intervalle de temps ce jeton est valide et informations sur la ressource que vous pouvez utiliser ce jeton pour.
Le jeton d’accès que vous avez reçu dans l’appel HTTP précédente doit être passé dans pour toutes les demandes à l’API processeur en tant qu’en-tête nommé « Autorisation » avec la valeur « PORTEUR YOUR_ACCESS_TOKEN ». Notez l’espace entre « Porteur » et votre jeton d’accès.

Comme vous pouvez voir dans le résultat HTTP ci-dessus, le jeton est valide pour une certaine durée pendant laquelle vous devez mettre en cache et réutiliser ce même jeton. Même s’il est possible de s’authentifier Azure AD pour chaque appel d’API, il est très inefficace.

## <a name="calling-arm-rest-apis"></a>Vous appelez processeur reste API

[Azure Gestionnaire de ressources reste API sont décrites ici](https://msdn.microsoft.com/library/azure/dn790568.aspx) et il est se déconnecter de l’étendue de ce didacticiel au document de l’utilisation de chaque. Cette documentation utilisera uniquement quelques API d’expliquer l’utilisation de la base des API et après que nous vous reporter à la documentation officielle.

### <a name="list-all-subscriptions"></a>Liste de tous les abonnements

Une des opérations que possibles de la plus simple consiste aux abonnements disponibles que vous pouvez accéder à la liste. Dans la demande, vous pouvez voir comment le jeton d’accès est passé comme un en-tête ci-dessous.

(Remplacez YOUR_ACCESS_TOKEN par votre jeton d’accès réel).

```HTTP
GET /subscriptions?api-version=2015-01-01 HTTP/1.1
Host: management.azure.com
Authorization: Bearer YOUR_ACCESS_TOKEN
Content-Type: application/json
```

… et par conséquent, vous obtiendrez une liste des abonnements parmi lesquels ce Service Principal est autorisé à accéder à

(ID de l’abonnement ci-dessous ont été raccourcie pour une meilleure lisibilité)

```json
{
  "value": [
    {
      "id": "/subscriptions/3a8555...555995",
      "subscriptionId": "3a8555...555995",
      "displayName": "Azure Subscription",
      "state": "Enabled",
      "subscriptionPolicies": {
        "locationPlacementId": "Internal_2014-09-01",
        "quotaId": "Internal_2014-09-01"
      }
    }
  ]
}
```

### <a name="list-all-resource-groups-in-a-specific-subscription"></a>Répertorier tous les groupes de ressources dans un abonnement spécifique

Toutes les ressources disponibles avec les API processeur sont imbriqués dans un groupe de ressources. Nous allons requête processeur pour le groupe de ressources existant dans notre abonnement à l’aide de l’en dessous de la demande HTTP GET. Notez comment l’ID de l’abonnement est passé dans le cadre de l’URL cette fois.

(Remplacez YOUR_ACCESS_TOKEN et ID_ABONNEMENT par votre réel jeton d’accès et ID de l’abonnement)

```HTTP
GET /subscriptions/SUBSCRIPTION_ID/resourcegroups?api-version=2015-01-01 HTTP/1.1
Host: management.azure.com
Authorization: Bearer YOUR_ACCESS_TOKEN
Content-Type: application/json
```

La réponse dépend de si vous avez défini aucun groupe de ressources et le cas échéant, nombre.

(ID de l’abonnement ci-dessous ont été raccourcie pour une meilleure lisibilité)

```json
{
    "value": [
        {
            "id": "/subscriptions/3a8555...555995/resourceGroups/myfirstresourcegroup",
            "name": "myfirstresourcegroup",
            "location": "eastus",
            "properties": {
                "provisioningState": "Succeeded"
            }
        },
        {
            "id": "/subscriptions/3a8555...555995/resourceGroups/mysecondresourcegroup",
            "name": "mysecondresourcegroup",
            "location": "northeurope",
            "tags": {
                "tagname1": "My first tag"
            },
            "properties": {
                "provisioningState": "Succeeded"
            }
        }
    ]
}
```

### <a name="create-a-resource-group"></a>Créer un groupe de ressources

Jusqu'à présent nous avons uniquement été interroger les API processeur pour plus d’informations, il est temps nous créer des ressources à la place et commençons par la plus simple de toutes les, un groupe de ressources. La requête HTTP suivante crée un nouveau groupe de ressources dans un emplacement/région de votre choix et lui ajoute une ou plusieurs balises (l’exemple ci-dessous en réalité ajoute une balise).

(Remplacez YOUR_ACCESS_TOKEN, ID_ABONNEMENT, nom_groupe_ressource par votre réel jeton d’accès, ID de l’abonnement et le nom du groupe de ressources que vous voulez créer)

```HTTP
PUT /subscriptions/SUBSCRIPTION_ID/resourcegroups/RESOURCE_GROUP_NAME?api-version=2015-01-01 HTTP/1.1
Host: management.azure.com
Authorization: Bearer YOUR_ACCESS_TOKEN
Content-Type: application/json

{
  "location": "northeurope",
  "tags": {
    "tagname1": "test-tag"
  }
}
```

En cas de réussite, vous obtiendrez une réponse similaire à ce

```json
{
  "id": "/subscriptions/3a8555...555995/resourceGroups/RESOURCE_GROUP_NAME",
  "name": "RESOURCE_GROUP_NAME",
  "location": "northeurope",
  "tags": {
    "tagname1": "test-tag"
  },
  "properties": {
    "provisioningState": "Succeeded"
  }
}
```

Vous avez créé un groupe de ressources dans Azure. Félicitations !

### <a name="deploy-resources-to-a-resource-group-using-an-arm-template"></a>Déployer des ressources à un groupe de ressources à l’aide d’un modèle de processeur

Avec processeur, vous pouvez déployer vos ressources à l’aide de modèles de processeur. Un modèle de processeur définit plusieurs ressources et leurs dépendances. Pour cette section uniquement suppose que vous connaissez processeur modèles et nous vous venez montrent comment passer l’appel API pour démarrer le déploiement d’un. Une documentation détaillée des modèles de processeur sont accessibles ici.

Déploiement d’un modèle de processeur ne diffèrent beaucoup comment vous appeler d’autres API. Un aspect important est que déploiement d’un modèle peut prendre beaucoup de temps, selon ce qui se trouve dans le modèle et l’appel API renverra uniquement et vers le haut pour vous en tant que développeur pour demander l’état du déploiement afin de déterminer quand le déploiement est terminé.

Dans cet exemple, nous allons utiliser un modèle de processeur exposée publiquement disponible sur [GitHub](https://github.com/Azure/azure-quickstart-templates). Le modèle, que nous allons utiliser déployez une Linux VM la région des États-Unis Ouest. Bien que ce modèle aura le modèle disponible dans un référentiel public comme GitHub, vous pouvez également sélectionner pour passer du modèle complè dans le cadre de la demande. Notez que nous fournissons des valeurs de paramètre dans le cadre de la requête qui sera utilisée dans le modèle utilisé.

(Remplacez ID_ABONNEMENT, nom_groupe_ressource, DEPLOYMENT_NAME, YOUR_ACCESS_TOKEN, GLOBALY_UNIQUE_STORAGE_ACCOUNT_NAME, ADMIN_USER_NAME, ADMIN_PASSWORD et DNS_NAME_FOR_PUBLIC_IP aux valeurs appropriées pour votre requête)

```HTTP
PUT /subscriptions/SUBSCRIPTION_ID/resourcegroups/RESOURCE_GROUP_NAME/providers/microsoft.resources/deployments/DEPLOYMENT_NAME?api-version=2015-01-01 HTTP/1.1
Host: management.azure.com
Authorization: Bearer YOUR_ACCESS_TOKEN
Content-Type: application/json

{
  "properties": {
    "templateLink": {
      "uri": "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-simple-linux-vm/azuredeploy.json",
      "contentVersion": "1.0.0.0",
    },
    "mode": "Incremental",
    "parameters": {
        "newStorageAccountName": {
          "value": "GLOBALY_UNIQUE_STORAGE_ACCOUNT_NAME"
        },
        "adminUsername": {
          "value": "ADMIN_USER_NAME"
        },
        "adminPassword": {
          "value": "ADMIN_PASSWORD"
        },
        "dnsNameForPublicIP": {
          "value": "DNS_NAME_FOR_PUBLIC_IP"
        },
        "ubuntuOSVersion": {
          "value": "15.04"
        }
    }
  }
}
```

La réponse JSON assez longue à cette requête ont été omis afin d’améliorer la lisibilité de cette documentation. La réponse contient des informations relatives au déploiement basé sur un modèle que vous venez de créer.


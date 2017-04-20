<properties
 pageTitle="Planificateur d’authentification sortante"
 description="Planificateur d’authentification sortante"
 services="scheduler"
 documentationCenter=".NET"
 authors="derek1ee"
 manager="kevinlam1"
 editor=""/>
<tags
 ms.service="scheduler"
 ms.workload="infrastructure-services"
 ms.tgt_pltfrm="na"
 ms.devlang="dotnet"
 ms.topic="article"
 ms.date="08/15/2016"
 ms.author="deli"/>

# <a name="scheduler-outbound-authentication"></a>Planificateur d’authentification sortante

Tâches du planificateur devrez faire appel à services nécessitant une authentification. Ainsi, un service appelé Déterminez si la tâche du planificateur peut accéder à ses ressources. Certains de ces services incluent d’autres services Azure, Salesforce.com, Facebook et des sites Web personnalisés sécurisés.

## <a name="adding-and-removing-authentication"></a>Ajout et suppression d’authentification

Ajout d’authentification pour une tâche de planificateur est simple : ajouter un élément enfant JSON `authentication` à la `request` élément lors de la création ou mise à jour d’une tâche. Secrets passé au service du planificateur dans une demande de place, correctif ou POST – dans le cadre de la `authentication` objet – ne sont jamais renvoyées dans les réponses. Dans les réponses, informations confidentielles sont la valeur null ou peuvent avoir un jeton public qui représente l’entité authentifiée.

Pour supprimer l’authentification, placez ou correctif le travail explicitement, définir la `authentication` objet null. Vous ne verrez pas les propriétés d’authentification serveur en réponse.

Pour l’instant, les types d’authentifications pris en charge uniquement sont les `ClientCertificate` modèle (pour en utilisant les certificats de client SSL/TLS), la `Basic` modèle (pour l’authentification de base) et la `ActiveDirectoryOAuth` modèle (pour l’authentification Active Directory OAuth.)

## <a name="request-body-for-clientcertificate-authentication"></a>Corps de requête pour l’authentification ClientCertificate

Lorsque vous ajoutez à l’aide de l’authentification par le `ClientCertificate` du modèle, spécifiez les éléments supplémentaires suivants dans le corps de la requête.  

|Élément|Description|
|:---|:---|
|_authentification (élément parent)_|Objet d’authentification pour l’utilisation d’un certificat client SSL.|
|_type_|Obligatoire. Type d’authentification. Pour les certificats SSL client, la valeur doit être `ClientCertificate`.|
|_PFX_|Obligatoire. Contenu codé en base 64 du fichier PFX.|
|_mot de passe_|Obligatoire. Mot de passe pour accéder au fichier PFX.|


## <a name="response-body-for-clientcertificate-authentication"></a>Corps de la réponse pour l’authentification ClientCertificate

Lorsqu’une demande est envoyée avec vos informations d’authentification, la réponse contient les éléments suivants liés à l’authentification.

|Élément |Description |
|:--|:--|
|_authentification (élément parent)_ |Objet d’authentification pour l’utilisation d’un certificat client SSL.|
|_type_ |Type d’authentification. Pour les certificats SSL client, la valeur est `ClientCertificate`.|
|_certificateThumbprint_ |L’empreinte numérique du certificat.|
|_certificateSubjectName_ |Nom unique objet du certificat.|
|_certificateExpiration_ |La date d’expiration du certificat.|

## <a name="sample-rest-request-for-clientcertificate-authentication"></a>Exemple de requête reste pour l’authentification ClientCertificate

```
PUT https://management.azure.com/subscriptions/1fe0abdf-581e-4dfe-9ec7-e5cb8e7b205e/resourceGroups/CS-SoutheastAsia-scheduler/providers/Microsoft.Scheduler/jobcollections/southeastasiajc/jobs/httpjob?api-version=2016-01-01 HTTP/1.1
User-Agent: Fiddler
Host: management.azure.com
Authorization: Bearer sometoken
Content-Type: application/json; charset=utf-8

{
  "properties": {
    "startTime": "2015-05-14T14:10:00Z",
    "action": {
      "request": {
        "uri": "https://mywebserviceendpoint.com",
        "method": "GET",
        "headers": {
          "x-ms-version": "2013-03-01"
        },
        "authentication": {
          "type": "clientcertificate",
          "password": "password",
          "pfx": "pfx key"
        }
      },
      "type": "http"
    },
    "recurrence": {
      "frequency": "minute",
      "endTime": "2016-04-10T08:00:00Z",
      "interval": 1
    },
    "state": "enabled",
  }
}
```

## <a name="sample-rest-response-for-clientcertificate-authentication"></a>Exemple de réponse reste pour l’authentification ClientCertificate

```
HTTP/1.1 200 OK
Cache-Control: no-cache
Pragma: no-cache
Content-Length: 858
Content-Type: application/json; charset=utf-8
Expires: -1
x-ms-request-id: 56c7b40e-721a-437e-88e6-f68562a73aa8
Server: Microsoft-IIS/8.5
X-AspNet-Version: 4.0.30319
X-Powered-By: ASP.NET
x-ms-ratelimit-remaining-subscription-resource-requests: 599
x-ms-correlation-request-id: 1075219e-e879-4030-bc81-094e54fbabce
x-ms-routing-request-id: WESTUS:20160316T190424Z:1075219e-e879-4030-bc81-094e54fbabce
Strict-Transport-Security: max-age=31536000; includeSubDomains
Date: Wed, 16 Mar 2016 19:04:23 GMT

{
  "id": "/subscriptions/1fe0abdf-581e-4dfe-9ec7-e5cb8e7b205e/resourceGroups/CS-SoutheastAsia-scheduler/providers/Microsoft.Scheduler/jobCollections/southeastasiajc/jobs/httpjob",
  "type": "Microsoft.Scheduler/jobCollections/jobs",
  "name": "southeastasiajc/httpjob",
  "properties": {
    "startTime": "2015-05-14T14:10:00Z",
    "action": {
      "request": {
        "uri": "https://mywebserviceendpoint.com",
        "method": "GET",
        "headers": {
          "x-ms-version": "2013-03-01"
        },
        "authentication": {
          "certificateThumbprint": "88105CG9DF9ADE75B835711D899296CB217D7055",
          "certificateExpiration": "2021-01-01T07:00:00Z",
          "certificateSubjectName": "CN=Scheduler Mgmt",
          "type": "ClientCertificate"
        }
      },
      "type": "http"
    },
    "recurrence": {
      "frequency": "minute",
      "endTime": "2016-04-10T08:00:00Z",
      "interval": 1
    },
    "state": "enabled",
    "status": {
      "nextExecutionTime": "2016-03-16T19:05:00Z",
      "executionCount": 0,
      "failureCount": 0,
      "faultedCount": 0
    }
  }
}
```

## <a name="request-body-for-basic-authentication"></a>Corps de la demande pour l’authentification de base

Lorsque vous ajoutez à l’aide de l’authentification par le `Basic` du modèle, spécifiez les éléments supplémentaires suivants dans le corps de la requête.

|Élément|Description|
|:--|:--|
|_authentification (élément parent)_ |Objet d’authentification pour l’authentification de base.|
|_type_ |Obligatoire. Type d’authentification. Pour l’authentification de base, la valeur doit être `Basic`.|
|_nom d’utilisateur_ |Obligatoire. Nom d’utilisateur s’authentifier.|
|_mot de passe_ |Obligatoire. Mot de passe pour l’authentification.|

## <a name="response-body-for-basic-authentication"></a>Corps de la réponse pour l’authentification de base

Lorsqu’une demande est envoyée avec vos informations d’authentification, la réponse contient les éléments suivants liés à l’authentification.

|Élément|Description|
|:--|:--|
|_authentification (élément parent)_ |Objet d’authentification pour l’authentification de base.|
|_type_ |Type d’authentification. Pour l’authentification de base, la valeur est `Basic`.|
|_nom d’utilisateur_ |Le nom d’utilisateur authentifié.|

## <a name="sample-rest-request-for-basic-authentication"></a>Exemple de requête reste pour l’authentification de base

```
PUT https://management.azure.com/subscriptions/1d908808-e491-4fe5-b97e-29886e18efd4/resourceGroups/CS-SoutheastAsia-scheduler/providers/Microsoft.Scheduler/jobcollections/southeastasiajc/jobs/httpjob?api-version=2016-01-01 HTTP/1.1
User-Agent: Fiddler
Host: management.azure.com
Authorization: Bearer sometoken
Content-Length: 562
Content-Type: application/json; charset=utf-8

{
  "properties": {
    "startTime": "2015-05-14T14:10:00Z",
    "action": {
      "request": {
        "uri": "https://mywebserviceendpoint.com",
        "method": "GET",
        "headers": {
          "x-ms-version": "2013-03-01"
        },
        "authentication": {
          "type": "basic",
          "username": "user",
          "password": "password"
        }
      },
      "type": "http"
    },
    "recurrence": {
      "frequency": "minute",
      "endTime": "2016-04-10T08:00:00Z",
      "interval": 1
    },
    "state": "enabled",
  }
}
```

## <a name="sample-rest-response-for-basic-authentication"></a>Exemple de réponse reste pour l’authentification de base

```
HTTP/1.1 200 OK
Cache-Control: no-cache
Pragma: no-cache
Content-Length: 701
Content-Type: application/json; charset=utf-8
Expires: -1
x-ms-request-id: a2dcb9cd-1aea-4887-8893-d81273a8cf04
Server: Microsoft-IIS/8.5
X-AspNet-Version: 4.0.30319
X-Powered-By: ASP.NET
x-ms-ratelimit-remaining-subscription-resource-requests: 599
x-ms-correlation-request-id: 7816f222-6ea7-468d-b919-e6ddebbd7e95
x-ms-routing-request-id: WESTUS:20160316T190506Z:7816f222-6ea7-468d-b919-e6ddebbd7e95
Strict-Transport-Security: max-age=31536000; includeSubDomains
Date: Wed, 16 Mar 2016 19:05:06 GMT

{  
   "id":"/subscriptions/1d908808-e491-4fe5-b97e-29886e18efd4/resourceGroups/CS-SoutheastAsia-scheduler/providers/Microsoft.Scheduler/jobCollections/southeastasiajc/jobs/httpjob",
   "type":"Microsoft.Scheduler/jobCollections/jobs",
   "name":"southeastasiajc/httpjob",
   "properties":{  
      "startTime":"2015-05-14T14:10:00Z",
      "action":{  
         "request":{  
            "uri":"https://mywebserviceendpoint.com",
            "method":"GET",
            "headers":{  
               "x-ms-version":"2013-03-01"
            },
            "authentication":{  
               "username":"user1",
               "type":"Basic"
            }
         },
         "type":"http"
      },
      "recurrence":{  
         "frequency":"minute",
         "endTime":"2016-04-10T08:00:00Z",
         "interval":1
      },
      "state":"enabled",
      "status":{  
         "nextExecutionTime":"2016-03-16T19:06:00Z",
         "executionCount":0,
         "failureCount":0,
         "faultedCount":0
      }
   }
}
```

## <a name="request-body-for-activedirectoryoauth-authentication"></a>Corps de requête pour l’authentification ActiveDirectoryOAuth

Lorsque vous ajoutez à l’aide de l’authentification par le `ActiveDirectoryOAuth` du modèle, spécifiez les éléments supplémentaires suivants dans le corps de la requête.

|Élément |Description |
|:--|:--|
|_authentification (élément parent)_ |Objet d’authentification pour l’authentification ActiveDirectoryOAuth.|
|_type_ |Obligatoire. Type d’authentification. Pour l’authentification ActiveDirectoryOAuth, la valeur doit être `ActiveDirectoryOAuth`.|
|_client_ |Obligatoire. L’identificateur de client pour le client Azure AD.|
|_audience_ |Obligatoire. Ceci est défini sur https://management.core.windows.net/.|
|_identifiant du client_ |Obligatoire. Fournir l’identificateur du client pour l’application Azure AD.|
|_code secret_ |Obligatoire. Code secret du client qui demande le jeton.|

### <a name="determining-your-tenant-identifier"></a>Déterminer votre identificateur de client

Vous pouvez trouver l’identificateur de client pour le client Azure AD en exécutant `Get-AzureAccount` dans Azure PowerShell.

## <a name="response-body-for-activedirectoryoauth-authentication"></a>Corps de la réponse pour l’authentification ActiveDirectoryOAuth

Lorsqu’une demande est envoyée avec vos informations d’authentification, la réponse contient les éléments suivants liés à l’authentification.

|Élément |Description |
|:--|:--|
|_authentification (élément parent)_ |Objet d’authentification pour l’authentification ActiveDirectoryOAuth.|
|_type_ |Type d’authentification. Pour l’authentification ActiveDirectoryOAuth, la valeur est `ActiveDirectoryOAuth`.|
|_client_ |L’identificateur de client pour le client Azure AD. |
|_audience_ |Ceci est défini sur https://management.core.windows.net/.|
|_identifiant du client_ |L’identificateur de client de l’application Azure AD.|

## <a name="sample-rest-request-for-activedirectoryoauth-authentication"></a>Exemple de requête reste pour l’authentification ActiveDirectoryOAuth

```
PUT https://management.azure.com/subscriptions/1d908808-e491-4fe5-b97e-29886e18efd4/resourceGroups/CS-SoutheastAsia-scheduler/providers/Microsoft.Scheduler/jobcollections/southeastasiajc/jobs/httpjob?api-version=2016-01-01 HTTP/1.1
User-Agent: Fiddler
Host: management.azure.com
Authorization: Bearer sometoken
Content-Length: 757
Content-Type: application/json; charset=utf-8

{
  "properties": {
    "startTime": "2015-05-14T14:10:00Z",
    "action": {
      "request": {
        "uri": "https://mywebserviceendpoint.com",
        "method": "GET",
        "headers": {
          "x-ms-version": "2013-03-01"
        },
        "authentication": {
          "tenant":"microsoft.onmicrosoft.com",
          "audience":"https://management.core.windows.net/",
          "clientId":"dc23e764-9be6-4a33-9b9a-c46e36f0c137",
          "secret": "G6u071r8Gjw4V4KSibnb+VK4+tX399hkHaj7LOyHuj5=",
          "type":"ActiveDirectoryOAuth"
        }
      },
      "type": "http"
    },
    "recurrence": {
      "frequency": "minute",
      "endTime": "2016-04-10T08:00:00Z",
      "interval": 1
    },
    "state": "enabled",
  }
}
```

## <a name="sample-rest-response-for-activedirectoryoauth-authentication"></a>Exemple de réponse reste pour l’authentification ActiveDirectoryOAuth

```
HTTP/1.1 200 OK
Cache-Control: no-cache
Pragma: no-cache
Content-Length: 885
Content-Type: application/json; charset=utf-8
Expires: -1
x-ms-request-id: 86d8e9fd-ac0d-4bed-9420-9baba1af3251
Server: Microsoft-IIS/8.5
X-AspNet-Version: 4.0.30319
X-Powered-By: ASP.NET
x-ms-ratelimit-remaining-subscription-resource-requests: 599
x-ms-correlation-request-id: 5183bbf4-9fa1-44bb-98c6-6872e3f2e7ce
x-ms-routing-request-id: WESTUS:20160316T191003Z:5183bbf4-9fa1-44bb-98c6-6872e3f2e7ce
Strict-Transport-Security: max-age=31536000; includeSubDomains
Date: Wed, 16 Mar 2016 19:10:02 GMT

{  
   "id":"/subscriptions/1d908808-e491-4fe5-b97e-29886e18efd4/resourceGroups/CS-SoutheastAsia-scheduler/providers/Microsoft.Scheduler/jobCollections/southeastasiajc/jobs/httpjob",
   "type":"Microsoft.Scheduler/jobCollections/jobs",
   "name":"southeastasiajc/httpjob",
   "properties":{  
      "startTime":"2015-05-14T14:10:00Z",
      "action":{  
         "request":{  
            "uri":"https://mywebserviceendpoint.com",
            "method":"GET",
            "headers":{  
               "x-ms-version":"2013-03-01"
            },
            "authentication":{  
               "tenant":"microsoft.onmicrosoft.com",
               "audience":"https://management.core.windows.net/",
               "clientId":"dc23e764-9be6-4a33-9b9a-c46e36f0c137",
               "type":"ActiveDirectoryOAuth"
            }
         },
         "type":"http"
      },
      "recurrence":{  
         "frequency":"minute",
         "endTime":"2016-04-10T08:00:00Z",
         "interval":1
      },
      "state":"enabled",
      "status":{  
         "lastExecutionTime":"2016-03-16T19:10:00.3762123Z",
         "nextExecutionTime":"2016-03-16T19:11:00Z",
         "executionCount":5,
         "failureCount":5,
         "faultedCount":1
      }
   }
}
```

## <a name="see-also"></a>Voir aussi


 [Quel est le planificateur ?](scheduler-intro.md)

 [Azure Scheduler concepts, terminologie et hiérarchie d’entités](scheduler-concepts-terms.md)

 [Commencer à utiliser le planificateur dans le portail Azure](scheduler-get-started-portal.md)

 [Les offres et facturation dans le planificateur d’Azure](scheduler-plans-billing.md)

 [Référence de l’API REST Scheduler Azure](https://msdn.microsoft.com/library/mt629143)

 [Référence à des applets de commande PowerShell Scheduler Azure](scheduler-powershell-reference.md)

 [Azure Scheduler disponibilité et fiabilité](scheduler-high-availability-reliability.md)

 [Limites de planificateur Azure, les valeurs par défaut et codes d’erreur](scheduler-limits-defaults-errors.md)

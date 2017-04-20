<properties
   pageTitle="Gérer les Azure données Lake Analytique à l’aide d’Azure SDK pour Node.js | Azure"
   description="Découvrez comment gérer les comptes données Lake Analytique, les sources de données, les travaux et les utilisateurs à l’aide d’Azure SDK pour Node.js"
   services="data-lake-analytics"
   documentationCenter=""
   authors="edmacauley"
   manager="jhubbard"
   editor="cgronlun"/>

<tags
   ms.service="data-lake-analytics"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="05/16/2016"
   ms.author="edmaca"/>

# <a name="manage-azure-data-lake-analytics-using-azure-sdk-for-nodejs"></a>Gérer les Azure données Lake Analytique à l’aide d’Azure SDK pour Node.js


[AZURE.INCLUDE [manage-selector](../../includes/data-lake-analytics-selector-manage.md)]

Le Kit de développement Azure pour Node.js peut être utilisé pour la gestion des comptes Azure données Lake Analytique, des tâches et des catalogues. Pour afficher la rubrique Gestion à l’aide d’autres outils, cliquez sur l’onglet, sélectionnez ci-dessus.

Maintenant, il prend en charge :

  *  **Version Node.js : 0.10.0 ou une version ultérieure**
  *  **Version de l’API REST de compte : 2015-10-01-aperçu**
  *  **Version de l’API REST de catalogue : 2015-10-01-aperçu**
  *  **Version de l’API REST de travail : 2016-03-20-aperçu**

## <a name="features"></a>Fonctionnalités

- Gestion des comptes : créer, obtenir, de la liste, mettre à jour et supprimer.
- Gestion du travail : envoyer, obtenir, list, Annuler.
- Gestion du catalogue : obtenir, list, créer (secrets), (secrets) de mettre à jour, supprimer (secrets).

## <a name="how-to-install"></a>Comment installer

```bash
npm install azure-arm-datalake-analytics
```

## <a name="authenticate-using-azure-active-directory"></a>S’authentifier à l’aide d’Azure Active Directory

 ```javascript
 var msrestAzure = require('ms-rest-azure');
 //user authentication
 var credentials = new msRestAzure.UserTokenCredentials('your-client-id', 'your-domain', 'your-username', 'your-password', 'your-redirect-uri');
 //service principal authentication
 var credentials = new msRestAzure.ApplicationTokenCredentials('your-client-id', 'your-domain', 'your-secret');
 ```

## <a name="create-the-data-lake-analytics-client"></a>Créer le client de données Lake Analytique

```javascript
var adlaManagement = require("azure-arm-datalake-analytics");
var acccountClient = new adlaManagement.DataLakeAnalyticsAccountClient(credentials, 'your-subscription-id');
var jobClient = new adlaManagement.DataLakeAnalyticsJobClient(credentials, 'azuredatalakeanalytics.net');
var catalogClient = new adlaManagement.DataLakeAnalyticsCatalogClient(credentials, 'azuredatalakeanalytics.net');
```

## <a name="create-a-data-lake-analytics-account"></a>Créer un compte de données Lake Analytique

```javascript
var util = require('util');
var resourceGroupName = 'testrg';
var accountName = 'testadlaacct';
var location = 'eastus2';

// A Data Lake Store account must already have been created to create
// a Data Lake Analytics account. See the Data Lake Store readme for
// information on doing so. For now, we assume one exists already.
var datalakeStoreAccountName = 'existingadlsaccount';

// account object to create
var accountToCreate = {
  tags: {
    testtag1: 'testvalue1',
    testtag2: 'testvalue2'
  },
  name: accountName,
  location: location,
  properties: {
    defaultDataLakeStoreAccount: datalakeStoreAccountName,
    dataLakeStoreAccounts: [
      {
        name: datalakeStoreAccountName
      }
    ]
  }
};

client.account.create(resourceGroupName, accountName, accountToCreate, function (err, result, request, response) {
  if (err) {
    console.log(err);
    /*err has reference to the actual request and response, so you can see what was sent and received on the wire.
      The structure of err looks like this:
      err: {
        code: 'Error Code',
        message: 'Error Message',
        body: 'The response body if any',
        request: reference to a stripped version of http request
        response: reference to a stripped version of the response
      }
    */
  } else {
    console.log('result is: ' + util.inspect(result, {depth: null}));
  }
});
```

## <a name="get-a-list-of-jobs"></a>Obtenir une liste de tâches

```javascript
var util = require('util');
var accountName = 'testadlaacct';
jobClient.job.list(accountName, function (err, result, request, response) {
  if (err) {
    console.log(err);
  } else {
    console.log('result is: ' + util.inspect(result, {depth: null}));
  }
});
```

## <a name="get-a-list-of-databases-in-the-data-lake-analytics-catalog"></a>Obtenir une liste des bases de données dans le catalogue données Lake Analytique
```javascript
var util = require('util');
var accountName = 'testadlaacct';
catalogClient.catalog.listDatabases(accountName, function (err, result, request, response) {
  if (err) {
    console.log(err);
  } else {
    console.log('result is: ' + util.inspect(result, {depth: null}));
  }
});
```

## <a name="see-also"></a>Voir aussi

- [Kit de développement Microsoft Azure pour Node.js](https://github.com/azure/azure-sdk-for-node)
- [Kit de développement Microsoft Azure pour Node.js - gestion des données Lake Store](https://github.com/Azure/azure-sdk-for-node/tree/autorest/lib/services/dataLake.Store)

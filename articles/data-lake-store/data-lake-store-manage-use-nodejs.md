<properties 
   pageTitle="Prise en main Azure données Lake Stores à l’aide d’Azure SDK pour Node.js | Microsoft Azure"
   description="Découvrez comment utiliser Node.js pour travailler avec des comptes de données Lake Store et le système de fichiers." 
   services="data-lake-store" 
   documentationCenter="" 
   authors="nitinme" 
   manager="jhubbard" 
   editor="cgronlun"/>
 
<tags
   ms.service="data-lake-store"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="09/27/2016"
   ms.author="nitinme"/>

# <a name="get-started-with-azure-data-lake-store-using-azure-sdk-for-nodejs"></a>Prise en main Azure données Lake Store à l’aide d’Azure SDK pour Node.js

> [AZURE.SELECTOR]
- [Portail](data-lake-store-get-started-portal.md)
- [PowerShell](data-lake-store-get-started-powershell.md)
- [KIT DE DÉVELOPPEMENT .NET](data-lake-store-get-started-net-sdk.md)
- [Java SDK](data-lake-store-get-started-java-sdk.md)
- [API REST](data-lake-store-get-started-rest-api.md)
- [Azure infrastructure du langage commun](data-lake-store-get-started-cli.md)
- [Node.js](data-lake-store-manage-use-nodejs.md)


Découvrez comment utiliser le Kit de développement Azure pour Node.js pour créer un compte Azure données Lake Store et effectuer des opérations de base telles que la création de dossiers, chargement et téléchargement des fichiers de données, supprimez votre compte, etc.. Pour plus d’informations sur les données Lake Store, voir [Vue d’ensemble de données Lake Store](data-lake-store-overview.md). Pour l’instant, le Kit de développement prend en charge

  *  **Version Node.js : 0.10.0 ou une version ultérieure**
  *  **Version de l’API REST de compte : 2015-10-01-aperçu**
  *  **Version de l’API REST de système de fichiers : 2015-10-01-aperçu**

## <a name="prerequisites"></a>Conditions préalables

Avant de commencer cet article, vous devez disposer des éléments suivants :

- **Azure un abonnement**. Voir [Azure obtenir la version d’évaluation gratuite](https://azure.microsoft.com/pricing/free-trial/).

- **Créer une Application Azure Active Directory**. Vous utilisez l’application Azure AD pour l’authentification de l’application de données Lake Store avec Azure AD. Il existe différentes approches pour vous authentifier avec Azure AD, qui sont **l’authentification de l’utilisateur final** ou **service à service**. Pour plus d’informations sur la façon de s’authentifier et des instructions, voir [authentifier avec données Lake Store à l’aide de Azure Active Directory](data-lake-store-authenticate-using-active-directory.md).

## <a name="how-to-install"></a>Comment installer

```bash
npm install azure-arm-datalake-store
```

## <a name="authenticate-using-azure-active-directory"></a>S’authentifier à l’aide d’Azure Active Directory

Extraits de code ci-dessous montrent deux façons distinctes d’authentification avec données Lake Store à l’aide d’Azure AD. Pour plus d’informations sur les différentes méthodes à utiliser pour l’authentification avec données Lake banque d’informations, voir [authentifier avec données Lake Store à l’aide de Azure Active Directory](data-lake-store-authenticate-using-active-directory.md).

L’extrait de code ci-dessous requiert également entrées comme nom de domaine Azure AD, ID client pour une application Azure AD, etc.. Toutes ces informations peuvent être extraites d’une Azure AD application que vous devez créée, lesquels sont également inclus dans le lien ci-dessus.

 ```javascript
 var msrestAzure = require('ms-rest-azure');
 //user authentication
 var credentials = new msRestAzure.UserTokenCredentials('your-client-id', 'your-domain', 'your-username', 'your-password', 'your-redirect-uri');
 //service principal authentication
 var credentials = new msRestAzure.ApplicationTokenCredentials('your-client-id', 'your-domain', 'your-secret');
 ```

## <a name="create-the-data-lake-store-clients"></a>Créer les Clients données Lake Store

```javascript
var adlsManagement = require("azure-arm-datalake-store");
var acccountClient = new adlsManagement.DataLakeStoreAccountClient(credentials, "your-subscription-id");
var filesystemClient = new adlsManagement.DataLakeStoreFileSystemClient(credentials);
```

## <a name="create-a-data-lake-store-account"></a>Créer un compte de données Lake Store

```javascript
var util = require('util');
var resourceGroupName = 'testrg';
var accountName = 'testadlsacct';
var location = 'eastus2';

// account object to create
var accountToCreate = {
  tags: {
    testtag1: 'testvalue1',
    testtag2: 'testvalue2'
  },
  name: accountName,
  location: location
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

## <a name="create-a-file-with-content"></a>Créer un fichier avec contenu
```javascript
var util = require('util');
var accountName = 'testadlsacct';
var fileToCreate = '/myfolder/myfile.txt';
var options = {
  streamContents: new Buffer('some string content')
}

filesystemClient.fileSystem.listFileStatus(accountName, fileToCreate, options, function (err, result, request, response) {
  if (err) {
    console.log(err);
  } else {
    // no result is returned, only a 201 response for success.
    console.log('response is: ' + util.inspect(response, {depth: null}));
  }
});
```

## <a name="get-a-list-of-files-and-folders"></a>Obtenir une liste de fichiers et dossiers

```javascript
var util = require('util');
var accountName = 'testadlsacct';
var pathToEnumerate = '/myfolder';
filesystemClient.fileSystem.listFileStatus(accountName, pathToEnumerate, function (err, result, request, response) {
  if (err) {
    console.log(err);
  } else {
    console.log('result is: ' + util.inspect(result, {depth: null}));
  }
});
```

## <a name="see-also"></a>Voir aussi

- [Kit de développement Microsoft Azure pour Node.js](https://github.com/azure/azure-sdk-for-node)
- [Kit de développement Microsoft Azure pour Node.js - gestion des données Lake Analytique](https://www.npmjs.com/package/azure-arm-datalake-analytics)

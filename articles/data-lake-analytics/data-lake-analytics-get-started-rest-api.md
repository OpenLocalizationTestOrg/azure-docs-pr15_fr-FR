<properties 
   pageTitle="Prise en main données Lake Analytique à l’aide de l’API REST | Microsoft Azure" 
   description="API REST de WebHDFS permet d’effectuer les opérations sur les données Lake Analytique" 
   services="data-lake-analytics" 
   documentationCenter="" 
   authors="mumian" 
   manager="jhubbard" 
   editor="cgronlun"/>
 
<tags
   ms.service="data-lake-store"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="10/19/2016"
   ms.author="jgao"/>

# <a name="get-started-with-azure-data-lake-analytics-using-rest-apis"></a>Prise en main Azure données Lake Analytique à l’aide des API REST

[AZURE.INCLUDE [get-started-selector](../../includes/data-lake-analytics-selector-get-started.md)]

Découvrez comment utiliser WebHDFS reste API et données Lake Analytique reste pour gérer les comptes données Lake Analytique, les tâches et catalogue. 

## <a name="prerequisites"></a>Conditions préalables

- **Azure un abonnement**. Voir [Azure obtenir la version d’évaluation gratuite](https://azure.microsoft.com/pricing/free-trial/).

- **Créer une Application Azure Active Directory**. Vous utilisez l’application Azure AD pour l’authentification de l’application de données Lake Analytique avec Azure AD. Il existe différentes approches pour vous authentifier avec Azure AD, qui sont **l’authentification de l’utilisateur final** ou **service à service**. Pour plus d’informations sur la façon de s’authentifier et des instructions, voir [vous authentifier avec données Lake Analytique à l’aide d’Azure Active Directory](../data-lake-store/data-lake-store-authenticate-using-active-directory.md).

- [coin](http://curl.haxx.se/). Cet article utilise ourlée pour vous montrer comment effectuer des appels API REST par rapport à un compte de données Lake Analytique.

## <a name="authenticate-with-azure-active-directory"></a>Authentifier avec Azure Active Directory

Il existe deux méthodes d’authentification avec Azure Active Directory.

### <a name="end-user-authentication-interactive"></a>Authentification de l’utilisateur final (interactive)

À l’aide de cette méthode, application invite l’utilisateur pour se connecter ainsi que toutes les opérations effectuées dans le contexte de l’utilisateur. 

Procédez comme suit pour l’authentification interactive :

1. Grâce à votre application, rediriger l’utilisateur vers l’URL suivante :

        https://login.microsoftonline.com/<TENANT-ID>/oauth2/authorize?client_id=<CLIENT-ID>&response_type=code&redirect_uri=<REDIRECT-URI>

    >[AZURE.NOTE] \<Rediriger URI > doit être codé à utiliser dans une URL. Par conséquent, pour https://localhost, utilisez `https%3A%2F%2Flocalhost`)

    Aux fins de ce didacticiel, vous pouvez remplacer les valeurs d’espace réservé dans l’URL ci-dessus et collez-les dans la barre d’adresse d’un navigateur web. Vous serez redirigé pour s’authentifier à l’aide de votre nom d’utilisateur Azure. Une fois que vous correctement connecté, la réponse est affichée dans la barre d’adresses du navigateur. La réponse sera au format suivant :
        
        http://localhost/?code=<AUTHORIZATION-CODE>&session_state=<GUID>

2. Capturer le code d’autorisation à partir de la réponse. Pour ce didacticiel, vous pouvez copier le code d’autorisation à partir de la barre d’adresses du navigateur web et passez dans la demande de publication au point de terminaison jeton, comme indiqué ci-dessous :

        curl -X POST https://login.microsoftonline.com/<TENANT-ID>/oauth2/token \
        -F redirect_uri=<REDIRECT-URI> \
        -F grant_type=authorization_code \
        -F resource=https://management.core.windows.net/ \
        -F client_id=<CLIENT-ID> \
        -F code=<AUTHORIZATION-CODE>

    >[AZURE.NOTE] Dans ce cas, le \<redirection URI > ne doivent pas être codé.

3. La réponse est un objet JSON qui contient un jeton d’accès (par exemple, `"access_token": "<ACCESS_TOKEN>"`) et un jeton d’actualisation (par exemple, `"refresh_token": "<REFRESH_TOKEN>"`). Votre application utilise le jeton d’accès s’ils accèdent à Azure Data Lake Store et le jeton d’actualisation pour obtenir un autre jeton d’accès lorsqu’un jeton d’accès arrive à expiration.

        {"token_type":"Bearer","scope":"user_impersonation","expires_in":"3599","expires_on":"1461865782","not_before": "1461861882","resource":"https://management.core.windows.net/","access_token":"<REDACTED>","refresh_token":"<REDACTED>","id_token":"<REDACTED>"}

4.  Lorsque le jeton d’accès arrive à expiration, vous pouvez demander un nouveau jeton d’accès en utilisant le jeton d’actualisation, comme indiqué ci-dessous :

         curl -X POST https://login.microsoftonline.com/<TENANT-ID>/oauth2/token  \
            -F grant_type=refresh_token \
            -F resource=https://management.core.windows.net/ \
            -F client_id=<CLIENT-ID> \
            -F refresh_token=<REFRESH-TOKEN>
 
Pour plus d’informations sur l’authentification des utilisateurs interactifs, voir [code d’autorisation accorder flux](https://msdn.microsoft.com/library/azure/dn645542.aspx).

### <a name="service-to-service-authentication-non-interactive"></a>Authentification de service à service (non interactif)

Application à l’aide de cette méthode, fournit ses propres informations d’identification pour effectuer les opérations. Pour ce faire, vous devez exécuter une requête POST similaire à celui illustré ci-dessous : 

    curl -X POST https://login.microsoftonline.com/<TENANT-ID>/oauth2/token  \
      -F grant_type=client_credentials \
      -F resource=https://management.core.windows.net/ \
      -F client_id=<CLIENT-ID> \
      -F client_secret=<AUTH-KEY>

Le résultat de cette requête doit inclure un jeton d’autorisation (indiqués par `access-token` dans la sortie ci-dessous) que vous passera par la suite avec vos appels API REST. Enregistrer ce jeton d’authentification dans un fichier texte ; Vous devez cela plus loin dans cet article.

    {"token_type":"Bearer","expires_in":"3599","expires_on":"1458245447","not_before":"1458241547","resource":"https://management.core.windows.net/","access_token":"<REDACTED>"}

Cet article utilise la **non interactif** approche. Pour plus d’informations sur non interactif (appels du service à service), voir [Service pour les appels de service à l’aide des informations d’identification](https://msdn.microsoft.com/library/azure/dn645543.aspx).
## <a name="create-a-data-lake-analytics-account"></a>Créer un compte de données Lake Analytique

Vous devez créer un groupe de ressources Azure et d’un compte de données Lake Store avant de pouvoir créer un compte de données Lake Analytique.  Voir [créer un compte Data Lake Store](../data-lake-store/data-lake-store-get-started-rest-api.md#create-a-data-lake-store-account).

La commande Curl suivante montre comment créer un compte :

    curl -i -X PUT -H "Authorization: Bearer <REDACTED>" -H "Content-Type: application/json" https://management.azure.com/subscriptions/<AzureSubscriptionID>/resourceGroups/<AzureResourceGroupName>/providers/Microsoft.DataLakeAnalytics/accounts/<NewAzureDataLakeAnalyticsAccountName>?api-version=2016-11-01 -d@"C:\tutorials\adla\CreateDataLakeAnalyticsAccountRequest.json"

Remplacer \< `REDACTED` \> avec le jeton d’autorisation, \< `AzureSubscriptionID` \> avec votre ID de l’abonnement, \< `AzureResourceGroupName` \> avec un nom de groupe de ressources Azure existant, et \< `NewAzureDataLakeAnalyticsAccountName` \> avec un nouveau nom de compte de données Lake Analytique. La charge utile de demande de cette commande figure dans le fichier **CreateDatalakeAnalyticsAccountRequest.json** qui est fourni pour le `-d` paramètre ci-dessus. Le contenu du fichier input.json se présenter comme suit :

    {  
        "location": "East US 2",  
        "name": "myadla1004",  
        "tags": {},  
        "properties": {  
            "defaultDataLakeStoreAccount": "my1004store",  
            "dataLakeStoreAccounts": [  
                {  
                    "name": "my1004store"  
                }     
            ]
        }  
    }  


## <a name="list-data-lake-analytics-accounts-in-a-subscription"></a>Comptes d’Analytique Lake de données de liste dans un abonnement

La commande Curl suivante montre comment répertorier les comptes dans un abonnement :

    curl -i -X GET -H "Authorization: Bearer <REDACTED>" https://management.azure.com/subscriptions/<AzureSubscriptionID>/providers/Microsoft.DataLakeAnalytics/Accounts?api-version=2016-11-01

Remplacer \< `REDACTED` \> avec le jeton d’autorisation, \< `AzureSubscriptionID` \> avec votre ID d’abonnement. Le résultat est semblable à :

    {
        "value": [
            {
            "properties": {
                "provisioningState": "Succeeded",
                "state": "Active",
                "endpoint": "myadla0831.azuredatalakeanalytics.net",
                "accountId": "21e74660-0941-4880-ae72-b143c2615ea9",
                "creationTime": "2016-09-01T12:49:12.7451428Z",
                "lastModifiedTime": "2016-09-01T12:49:12.7451428Z"
            },
            "location": "East US 2",
            "tags": {},
            "id": "/subscriptions/65a1016d-0f67-45d2-b838-b8f373d6d52e/resourceGroups/myadla0831rg/providers/Microsoft.DataLakeAnalytics/accounts/myadla0831",
            "name": "myadla0831",
            "type": "Microsoft.DataLakeAnalytics/accounts"
            },
            {
            "properties": {
                "provisioningState": "Succeeded",
                "state": "Active",
                "endpoint": "myadla1004.azuredatalakeanalytics.net",
                "accountId": "3ff9b93b-11c4-43c6-83cc-276292eeb350",
                "creationTime": "2016-10-04T20:46:42.287147Z",
                "lastModifiedTime": "2016-10-04T20:46:42.287147Z"
            },
            "location": "East US 2",
            "tags": {},
            "id": "/subscriptions/65a1016d-0f67-45d2-b838-b8f373d6d52e/resourceGroups/myadla1004rg/providers/Microsoft.DataLakeAnalytics/accounts/myadla1004",
            "name": "myadla1004",
            "type": "Microsoft.DataLakeAnalytics/accounts"
            }
        ]
    }

## <a name="get-information-about-a-data-lake-analytics-account"></a>Obtenir des informations sur un compte de données Lake Analytique

La commande Curl suivante montre comment obtenir une informations de compte :

    curl -i -X GET -H "Authorization: Bearer <REDACTED>" https://management.azure.com/subscriptions/<AzureSubscriptionID>/resourceGroups/<AzureResourceGroupName>/providers/Microsoft.DataLakeAnalytics/accounts/<DataLakeAnalyticsAccountName>?api-version=2015-11-01

Remplacer \< `REDACTED` \> avec le jeton d’autorisation, \< `AzureSubscriptionID` \> avec votre ID de l’abonnement, \< `AzureResourceGroupName` \> avec un nom de groupe de ressources Azure existant, et \< `DataLakeAnalyticsAccountName` \> avec le nom d’un compte d’Analytique Lake données existant. Le résultat est semblable à :

    {
        "properties": {
            "defaultDataLakeStoreAccount": "my1004store",
            "dataLakeStoreAccounts": [
            {
                "properties": {
                "suffix": "azuredatalakestore.net"
                },
                "name": "my1004store"
            }
            ],
            "provisioningState": "Creating",
            "state": null,
            "endpoint": null,
            "accountId": "3ff9b93b-11c4-43c6-83cc-276292eeb350",
            "creationTime": null,
            "lastModifiedTime": null
        },
        "location": "East US 2",
        "tags": {},
        "id": "/subscriptions/65a1016d-0f67-45d2-b838-b8f373d6d52e/resourceGroups/myadla1004rg/providers/Microsoft.DataLakeAnalytics/accounts/myadla1004",
        "name": "myadla1004",
        "type": "Microsoft.DataLakeAnalytics/accounts"
    }

## <a name="list-data-lake-stores-of-a-data-lake-analytics-account"></a>Liste de données Lake banques d’un compte de données Lake Analytique

La commande Curl suivante montre comment répertorier les données Lake banques d’un compte :

    curl -i -X GET -H "Authorization: Bearer <REDACTED>" https://management.azure.com/subscriptions/<AzureSubscriptionID>/resourceGroups/<AzureResourceGroupName>/providers/Microsoft.DataLakeAnalytics/accounts/<DataLakeAnalyticsAccountName>/DataLakeStoreAccounts/?api-version=2016-11-01

Remplacer \< `REDACTED` \> avec le jeton d’autorisation, \< `AzureSubscriptionID` \> avec votre ID de l’abonnement, \< `AzureResourceGroupName` \> avec un nom de groupe de ressources Azure existant, et \< `DataLakeAnalyticsAccountName` \> avec le nom d’un compte d’Analytique Lake données existant. Le résultat est semblable à :

    {
        "value": [
            {
            "properties": {
                "suffix": "azuredatalakestore.net"
            },
            "id": "/subscriptions/65a1016d-0f67-45d2-b838-b8f373d6d52e/resourceGroups/myadla1004rg/providers/Microsoft.DataLakeAnalytics/accounts/myadla1004/dataLakeStoreAccounts/my1004store",
            "name": "my1004store",
            "type": "Microsoft.DataLakeAnalytics/accounts/dataLakeStoreAccounts"
            }
        ]
    }

## <a name="submit-u-sql-jobs"></a>Soumettre des travaux d’U-SQL

La commande Curl suivante montre comment envoyer un travail U-SQL :

    curl -i -X PUT -H "Authorization: Bearer <REDACTED>" https://<DataLakeAnalyticsAccountName>.azuredatalakeanalytics.net/Jobs/<NewGUID>?api-version=2016-03-20-preview -d@"C:\tutorials\adla\SubmitADLAJob.json"

Remplacer \< `REDACTED` \> avec le jeton d’autorisation, \< `DataLakeAnalyticsAccountName` \> avec le nom d’un compte d’Analytique Lake données existant. La charge utile de demande de cette commande figure dans le fichier **SubmitADLAJob.json** qui est fourni pour le `-d` paramètre ci-dessus. Le contenu du fichier input.json se présenter comme suit :

    {
        "jobId": "8f8ebf8c-4b63-428a-ab46-a03d2cc5b65a",
        "name": "convertTSVtoCSV",
        "type": "USql",
        "degreeOfParallelism": 1,
        "priority": 1000,
        "properties": {
            "type": "USql",
            "script": "@searchlog =\n    EXTRACT UserId          int,\n            Start           DateTime,\n            Region          string,\n            Query          
        string,\n            Duration        int?,\n            Urls            string,\n            ClickedUrls     string\n    FROM \"/Samples/Data/SearchLog.tsv\"\n    US
        ING Extractors.Tsv();\n\nOUTPUT @searchlog   \n    TO \"/Output/SearchLog-from-Data-Lake.csv\"\nUSING Outputters.Csv();"
        }
    }

Le résultat est semblable à :

    {
        "jobId": "8f8ebf8c-4b63-428a-ab46-a03d2cc5b65a",
        "name": "convertTSVtoCSV",
        "type": "USql",
        "submitter": "myadl@SPI",
        "degreeOfParallelism": 1,
        "priority": 1000,
        "submitTime": "2016-10-05T13:54:59.9871859+00:00",
        "state": "Compiling",
        "result": "Succeeded",
        "stateAuditRecords": [
            {
            "newState": "New",
            "timeStamp": "2016-10-05T13:54:59.9871859+00:00",
            "details": "userName:myadl@SPI;submitMachine:N/A"
            }
        ],
        "properties": {
            "owner": "myadl@SPI",
            "resources": [],
            "runtimeVersion": "default",
            "rootProcessNodeId": "00000000-0000-0000-0000-000000000000",
            "algebraFilePath": "adl://myadls0831.azuredatalakestore.net/system/jobservice/jobs/Usql/2016/10/05/13/54/8f8ebf8c-4b63-428a-ab46-a03d2cc5b65a/algebra.xml",
            "compileMode": "Semantic",
            "errorSource": "Unknown",
            "totalCompilationTime": "PT0S",
            "totalPausedTime": "PT0S",
            "totalQueuedTime": "PT0S",
            "totalRunningTime": "PT0S",
            "type": "USql"
        }
    }


## <a name="list-u-sql-jobs"></a>Répertorier les tâches U-SQL

La commande Curl suivante montre comment répertorier les travaux U-SQL :

    curl -i -X GET -H "Authorization: Bearer <REDACTED>" https://<DataLakeAnalyticsAccountName>.azuredatalakeanalytics.net/Jobs?api-version=2016-11-01 

Remplacer \< `REDACTED` \> avec le jeton d’autorisation, et \< `DataLakeAnalyticsAccountName` \> avec le nom d’un compte d’Analytique Lake données existant. 


Le résultat est semblable à :

    {
    "value": [
        {
        "jobId": "65cf1691-9dbe-43cd-90ed-1cafbfb406fb",
        "name": "convertTSVtoCSV",
        "type": "USql",
        "submitter": "someone@microsoft.com",
        "account": null,
        "degreeOfParallelism": 1,
        "priority": 1000,
        "submitTime": "Wed, 05 Oct 2016 13:46:53 GMT",
        "startTime": "Wed, 05 Oct 2016 13:47:33 GMT",
        "endTime": "Wed, 05 Oct 2016 13:48:07 GMT",
        "state": "Ended",
        "result": "Succeeded",
        "errorMessage": null,
        "storageAccounts": null,
        "stateAuditRecords": null,
        "logFilePatterns": null,
        "properties": null
        },
        {
        "jobId": "8f8ebf8c-4b63-428a-ab46-a03d2cc5b65a",
        "name": "convertTSVtoCSV",
        "type": "USql",
        "submitter": "someoneadl@SPI",
        "account": null,
        "degreeOfParallelism": 1,
        "priority": 1000,
        "submitTime": "Wed, 05 Oct 2016 13:54:59 GMT",
        "startTime": "Wed, 05 Oct 2016 13:55:43 GMT",
        "endTime": "Wed, 05 Oct 2016 13:56:11 GMT",
        "state": "Ended",
        "result": "Succeeded",
        "errorMessage": null,
        "storageAccounts": null,
        "stateAuditRecords": null,
        "logFilePatterns": null,
        "properties": null
        }
    ],
    "nextLink": null,
    "count": null
    }


## <a name="get-catalog-items"></a>Obtenir les éléments du catalogue

La commande Curl suivante montre comment obtenir les bases de données à partir du catalogue :

    curl -i -X GET -H "Authorization: Bearer <REDACTED>" https://<DataLakeAnalyticsAccountName>.azuredatalakeanalytics.net/catalog/usql/databases?api-version=2016-11-01

Le résultat est semblable à :

    {
    "@odata.context":"https://myadla0831.azuredatalakeanalytics.net/sqlip/$metadata#databases","value":[
        {
        "computeAccountName":"myadla0831","databaseName":"mytest","version":"f6956327-90b8-4648-ad8b-de3ff09274ea"
        },{
        "computeAccountName":"myadla0831","databaseName":"master","version":"e8bca908-cc73-41a3-9564-e9bcfaa21f4e"
        }
    ]
    }

## <a name="see-also"></a>Voir aussi

- Pour plus d’une requête plus complexe, voir [journaux d’analyse Web à l’aide de Azure données Lake Analytique](data-lake-analytics-analyze-weblogs.md).
- Pour commencer à développer des applications U-SQL, voir [scripts U-SQL développer à l’aide de données Lake Tools pour Visual Studio](data-lake-analytics-data-lake-tools-get-started.md).
- Pour plus d’U-SQL, voir [prise en main langue données Lake Analytique U-SQL Azure](data-lake-analytics-u-sql-get-started.md).
- Pour des tâches de gestion, voir [Gérer Azure données Lake Analytique à l’aide de portail Azure](data-lake-analytics-manage-use-portal.md).
- Pour obtenir une vue d’ensemble de données Lake Analytique, voir [vue d’ensemble Azure données Lake Analytique](data-lake-analytics-overview.md).
- Pour afficher le même didacticiel à l’aide d’autres outils, cliquez sur les sélecteurs onglet en haut de la page.
- Pour enregistrer les informations de diagnostic, consultez [accès à des journaux de diagnostics pour Azure données Lake Analytique](data-lake-analytics-diagnostic-logs.md)

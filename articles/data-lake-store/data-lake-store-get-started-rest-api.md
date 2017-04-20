<properties 
   pageTitle="Prise en main du Store Lake de données à l’aide de l’API REST | Microsoft Azure" 
   description="API REST de WebHDFS permet d’effectuer les opérations sur les données Lake Store" 
   services="data-lake-store" 
   documentationCenter="" 
   authors="nitinme" 
   manager="jhubbard" 
   editor="cgronlun"/>
 
<tags
   ms.service="data-lake-store"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="09/27/2016"
   ms.author="nitinme"/>

# <a name="get-started-with-azure-data-lake-store-using-rest-apis"></a>Prise en main Azure données Lake Store à l’aide des API REST

> [AZURE.SELECTOR]
- [Portail](data-lake-store-get-started-portal.md)
- [PowerShell](data-lake-store-get-started-powershell.md)
- [KIT DE DÉVELOPPEMENT .NET](data-lake-store-get-started-net-sdk.md)
- [Java SDK](data-lake-store-get-started-java-sdk.md)
- [API REST](data-lake-store-get-started-rest-api.md)
- [Azure infrastructure du langage commun](data-lake-store-get-started-cli.md)
- [Node.js](data-lake-store-manage-use-nodejs.md)

Dans cet article, vous allez apprendre à utiliser WebHDFS reste API et données Lake Store reste pour effectuer la gestion des comptes ainsi que les opérations de système de fichiers sur Azure données Lake magasin. Magasin de Lake données Azure expose sa propre API REST pour les opérations de gestion de compte. Toutefois, étant donné que Data Lake Store est compatible avec réseau HADOOP et Hadoop, qui prend en charge à l’aide des API REST de WebHDFS pour les opérations de système de fichiers.

>[AZURE.NOTE] Pour plus d’informations sur l’API REST prise en charge des données Lake Store, voir [Référence des API REST Azure données Lake Store](https://msdn.microsoft.com/library/mt693424.aspx).

## <a name="prerequisites"></a>Conditions préalables

- **Azure un abonnement**. Voir [Azure obtenir la version d’évaluation gratuite](https://azure.microsoft.com/pricing/free-trial/).

- **Créer une Application Azure Active Directory**. Vous utilisez l’application Azure AD pour l’authentification de l’application de données Lake Store avec Azure AD. Il existe différentes approches pour vous authentifier avec Azure AD, qui sont **l’authentification de l’utilisateur final** ou **service à service**. Pour plus d’informations sur la façon de s’authentifier et des instructions, voir [authentifier avec données Lake Store à l’aide de Azure Active Directory](data-lake-store-authenticate-using-active-directory.md).

- [coin](http://curl.haxx.se/). Cet article utilise ourlée pour vous montrer comment effectuer des appels API REST par rapport à un compte de données Lake Store.

## <a name="how-do-i-authenticate-using-azure-active-directory"></a>Comment s’authentifier à l’aide d’Azure Active Directory ?

Vous pouvez utiliser deux approches s’authentifier à l’aide d’Azure Active Directory.

### <a name="end-user-authentication-interactive"></a>Authentification de l’utilisateur final (interactive)

Dans ce scénario, l’application invite l’utilisateur à se connecter ainsi que toutes les opérations effectuées dans le contexte de l’utilisateur. Procédez comme suit pour l’authentification interactive.

1. Grâce à votre application, rediriger l’utilisateur vers l’URL suivante :

        https://login.microsoftonline.com/<TENANT-ID>/oauth2/authorize?client_id=<CLIENT-ID>&response_type=code&redirect_uri=<REDIRECT-URI>

    >[AZURE.NOTE] \<Rediriger URI > doit être codé à utiliser dans une URL. Par conséquent, pour https://localhost, utilisez `https%3A%2F%2Flocalhost`)

    Aux fins de ce didacticiel, vous pouvez remplacer les valeurs d’espace réservé dans l’URL ci-dessus et collez-les dans la barre d’adresse d’un navigateur web. Vous serez redirigé pour s’authentifier à l’aide de votre nom d’utilisateur Azure. Une fois que vous vous connectez avec succès, la réponse est affichée dans la barre d’adresses du navigateur. La réponse sera au format suivant :
        
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

Dans ce scénario, le l’application fournit ses propres informations d’identification pour effectuer les opérations. Pour ce faire, vous devez exécuter une requête POST similaire à celui illustré ci-dessous. 

    curl -X POST https://login.microsoftonline.com/<TENANT-ID>/oauth2/token  \
      -F grant_type=client_credentials \
      -F resource=https://management.core.windows.net/ \
      -F client_id=<CLIENT-ID> \
      -F client_secret=<AUTH-KEY>

Le résultat de cette requête doit inclure un jeton d’autorisation (indiqués par `access-token` dans la sortie ci-dessous) que vous passera par la suite avec vos appels API REST. Enregistrer ce jeton d’authentification dans un fichier texte ; Vous devez cela plus loin dans cet article.

    {"token_type":"Bearer","expires_in":"3599","expires_on":"1458245447","not_before":"1458241547","resource":"https://management.core.windows.net/","access_token":"<REDACTED>"}

Cet article utilise la **non interactif** approche. Pour plus d’informations sur non interactif (appels du service à service), voir [Service pour les appels de service à l’aide des informations d’identification](https://msdn.microsoft.com/library/azure/dn645543.aspx).

## <a name="create-a-data-lake-store-account"></a>Créer un compte Data Lake Store

Cette opération est basée sur l’API REST appel défini [ici](https://msdn.microsoft.com/library/mt694078.aspx).

Utilisez la commande cURL suivante. Remplacer ** \<yourstorename >** avec votre nom de données Lake Store.

    curl -i -X PUT -H "Authorization: Bearer <REDACTED>" -H "Content-Type: application/json" https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.DataLakeStore/accounts/<yourstorename>?api-version=2015-10-01-preview -d@"C:\temp\input.json"

Dans la commande ci-dessus, remplacez \< `REDACTED` \> avec le jeton d’autorisation que vous avez récupérée précédemment. La charge utile de demande de cette commande figure dans le fichier **input.json** qui est fourni pour le `-d` paramètre ci-dessus. Le contenu du fichier input.json se présenter comme suit :

    {
    "location": "eastus2",
    "tags": {
        "department": "finance"
        },
    "properties": {}
    }   

## <a name="create-folders-in-a-data-lake-store-account"></a>Créer des dossiers dans un compte de données Lake Store

Cette opération est basée sur l’API REST WebHDFS appel défini [ici](http://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/WebHDFS.html#Make_a_Directory).

Utilisez la commande cURL suivante. Remplacer ** \<yourstorename >** avec votre nom de données Lake Store.

    curl -i -X PUT -H "Authorization: Bearer <REDACTED>" -d "" https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/?op=MKDIRS

Dans la commande ci-dessus, remplacez \< `REDACTED` \> avec le jeton d’autorisation que vous avez récupérée précédemment. Cette commande crée un répertoire appelé **mytempdir** sous le dossier racine de votre compte de données Lake Store.

Vous devriez voir une réponse comme suit si l’opération réussit :

    {"boolean":true}

## <a name="list-folders-in-a-data-lake-store-account"></a>Liste des dossiers dans un compte de données Lake Store

Cette opération est basée sur l’API REST WebHDFS appel défini [ici](http://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/WebHDFS.html#List_a_Directory).

Utilisez la commande cURL suivante. Remplacer ** \<yourstorename >** avec votre nom de données Lake Store.

    curl -i -X GET -H "Authorization: Bearer <REDACTED>" https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/?op=LISTSTATUS

Dans la commande ci-dessus, remplacez \< `REDACTED` \> avec le jeton d’autorisation que vous avez récupérée précédemment.

Vous devriez voir une réponse comme suit si l’opération réussit :

    {
    "FileStatuses": {
        "FileStatus": [{
            "length": 0,
            "pathSuffix": "mytempdir",
            "type": "DIRECTORY",
            "blockSize": 268435456,
            "accessTime": 1458324719512,
            "modificationTime": 1458324719512,
            "replication": 0,
            "permission": "777",
            "owner": "NotSupportYet",
            "group": "NotSupportYet"
        }]
    }
    }

## <a name="upload-data-into-a-data-lake-store-account"></a>Télécharger des données dans un compte de données Lake Store

Cette opération est basée sur l’API REST WebHDFS appel défini [ici](http://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/WebHDFS.html#Create_and_Write_to_a_File).

Téléchargement de données à l’aide de l’API REST WebHDFS est un processus en deux étapes, comme décrit ci-dessous.

1. Envoyez une demande HTTP placer sans envoyer les données du fichier à télécharger. Dans la commande suivante, remplacez ** \<yourstorename >** avec votre nom de données Lake Store.

        curl -i -X PUT -H "Authorization: Bearer <REDACTED>" -d "" https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/?op=CREATE

    Le résultat de cette commande être contiendra une URL de redirection temporaire, comme celui illustré ci-dessous.

        HTTP/1.1 100 Continue

        HTTP/1.1 307 Temporary Redirect
        ...
        ...
        Location: https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/somerandomfile.txt?op=CREATE&write=true
        ...
        ...

2. Vous devez soumettre maintenant une autre requête HTTP placer par rapport à l’URL indiquée pour la propriété **emplacement** dans la réponse. Remplacer ** \<yourstorename >** avec votre nom de données Lake Store.

        curl -i -X PUT -T myinputfile.txt -H "Authorization: Bearer <REDACTED>" https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/myinputfile.txt?op=CREATE&write=true

    Le résultat sera semblable à ce qui suit :

        HTTP/1.1 100 Continue

        HTTP/1.1 201 Created
        ...
        ...

## <a name="read-data-from-a-data-lake-store-account"></a>Lire les données à partir d’un compte de données Lake Store

Cette opération est basée sur l’API REST WebHDFS appel défini [ici](http://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/WebHDFS.html#Open_and_Read_a_File).

Lecture de données à partir d’un magasin Lake données compte est un processus en deux étapes.

* Vous envoyez tout d’abord une requête GET contre le point de terminaison `https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/myinputfile.txt?op=OPEN`. Cette opération retourne un emplacement pour envoyer la demande GET suivante à.
* Vous puis soumettez la requête GET contre le point de terminaison `https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/myinputfile.txt?op=OPEN&read=true`. Ceci affiche le contenu du fichier.

Toutefois, étant donné qu’aucune différence dans les paramètres d’entrée entre la première et la deuxième étape, vous pouvez utiliser la `-L` paramètre soumettre la première requête. `-L`option regroupe deux demandes en une seule essentiellement et fera ourlée rétablir la demande dans le nouvel emplacement. Enfin, le résultat de tous les appels de demande s’affiche, comme illustré ci-dessous. Remplacer ** \<yourstorename >** avec votre nom de données Lake Store.

    curl -i -L GET -H "Authorization: Bearer <REDACTED>" https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/myinputfile.txt?op=OPEN

Vous devriez voir un résultat semblable à ce qui suit :

    HTTP/1.1 307 Temporary Redirect
    ...
    Location: https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/somerandomfile.txt?op=OPEN&read=true
    ...
    
    HTTP/1.1 200 OK
    ...
    
    Hello, Data Lake Store user!

## <a name="rename-a-file-in-a-data-lake-store-account"></a>Renommer un fichier dans un compte de données Lake Store

Cette opération est basée sur l’API REST WebHDFS appel défini [ici](http://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/WebHDFS.html#Rename_a_FileDirectory).

Utilisez la commande cURL suivante pour renommer un fichier. Remplacer ** \<yourstorename >** avec votre nom de données Lake Store.

    curl -i -X PUT -H "Authorization: Bearer <REDACTED>" -d "" https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/myinputfile.txt?op=RENAME&destination=/mytempdir/myinputfile1.txt

Vous devriez voir un résultat semblable à ce qui suit :

    HTTP/1.1 200 OK
    ...
    
    {"boolean":true}

## <a name="delete-a-file-from-a-data-lake-store-account"></a>Supprimer un fichier à partir d’un compte de données Lake Store

Cette opération est basée sur l’API REST WebHDFS appel défini [ici](http://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/WebHDFS.html#Delete_a_FileDirectory).

Pour supprimer un fichier, utilisez la commande cURL suivante. Remplacer ** \<yourstorename >** avec votre nom de données Lake Store.

    curl -i -X DELETE -H "Authorization: Bearer <REDACTED>" https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/myinputfile1.txt?op=DELETE

Vous devriez voir un résultat comme suit :

    HTTP/1.1 200 OK
    ...
    
    {"boolean":true}

## <a name="delete-a-data-lake-store-account"></a>Supprimer un compte de données Lake Store

Cette opération est basée sur l’API REST appel défini [ici](https://msdn.microsoft.com/library/mt694075.aspx).

Utilisez la commande cURL suivante pour supprimer un compte de données Lake Store. Remplacer ** \<yourstorename >** avec votre nom de données Lake Store.

    curl -i -X DELETE -H "Authorization: Bearer <REDACTED>" https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.DataLakeStore/accounts/<yourstorename>?api-version=2015-10-01-preview

Vous devriez voir un résultat comme suit :

    HTTP/1.1 200 OK
    ...
    ...

## <a name="see-also"></a>Voir aussi

- [Ouvrir les applications Big Data Source compatibles avec Azure données Lake Store](data-lake-store-compatible-oss-other-applications.md)
 

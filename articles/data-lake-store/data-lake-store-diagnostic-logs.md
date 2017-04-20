<properties 
   pageTitle="Afficher les journaux de diagnostic pour Azure données Lake Store | Microsoft Azure" 
   description="Comprendre comment configurer et accéder aux journaux de diagnostic pour Azure données Lake Store " 
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
   ms.date="10/05/2016"
   ms.author="nitinme"/>

# <a name="accessing-diagnostic-logs-for-azure-data-lake-store"></a>Accès aux journaux de diagnostics pour Azure données Lake Store

Découvrez comment activer la journalisation des diagnostics pour votre compte de données Lake Store et comment afficher les journaux collectées pour votre compte.

Organisations peuvent activer la journalisation des diagnostics pour leur compte Azure données Lake Store collecter les journaux d’audit d’accès aux données qui fournit des informations telles que de la liste des utilisateurs l’accès aux données, la fréquence à laquelle les données sont accessibles, la quantité de données est stockée dans le compte, etc..

## <a name="prerequisites"></a>Conditions préalables

- **Azure un abonnement**. Voir [Azure obtenir la version d’évaluation gratuite](https://azure.microsoft.com/pricing/free-trial/).

- **Compte azure données Lake Store**. Suivez les instructions sur la [prise en main Azure données Lake Store à l’aide du portail Azure](data-lake-store-get-started-portal.md).

## <a name="enable-diagnostic-logging-for-your-data-lake-store-account"></a>Activer la journalisation des diagnostics pour votre compte de données Lake Store

1. Ouverture de session au nouveau [Portail Azure](https://portal.azure.com).

2. Ouvrez votre compte données Lake Store et à partir de votre carte de compte données Lake Store, cliquez sur **paramètres**, puis cliquez sur **Paramètres de Diagnostic**.

3. Dans la carte de **Diagnostic** , apportez les modifications suivantes pour configurer la journalisation des Diagnostics.

    ![Activer la journalisation des diagnostics] (./media/data-lake-store-diagnostic-logs/enable-diagnostic-logs.png "Activer les journaux de diagnostic")

    * Définir le **statut** sur **sur** pour activer la journalisation des Diagnostics.
    * Vous pouvez choisir banque/traiter les données de deux façons différentes.
        * Sélectionnez l’option **d’exportation à événement concentrateur** pour répartir des données journal à un concentrateur événement Azure. Vous utiliserez probablement cette option si vous avez un pipeline de traitement en aval analyser les journaux entrants en temps réel. Si vous sélectionnez cette option, vous devez fournir les détails pour le Hub événement Azure que vous voulez utiliser.
        * Sélectionnez l’option **d’exportation pour le compte de stockage** pour stocker les fichiers journaux à un compte de stockage Azure. Vous utilisez cette option si vous souhaitez archiver les données qui seront traitées par lots à une date ultérieure. Si vous sélectionnez cette option, vous devez fournir un compte de stockage Azure pour enregistrer les journaux.
    * Indiquez si vous souhaitez obtenir les journaux d’audit ou les journaux de requêtes ou les deux.
    * Spécifier le nombre de jours pour lesquels les données doivent être conservées.
    * Cliquez sur **Enregistrer**.

Une fois que vous avez activé les paramètres de diagnostic, vous pouvez visionner les journaux dans l’onglet **Journaux de Diagnostic** .

## <a name="view-diagnostic-logs-for-your-data-lake-store-account"></a>Afficher les journaux des diagnostics pour votre compte de données Lake Store

Il existe deux manières d’afficher les données du journal pour votre compte de données Lake Store.

* Dans l’affichage de paramètres de compte données Lake Store
* À partir du compte de stockage Azure où sont stockées les données

### <a name="using-the-data-lake-store-settings-view"></a>Utilisation de la vue de données Lake Store paramètres

1. À partir de votre carte de **paramètres** de compte données Lake Store, cliquez sur **Les journaux de Diagnostic**.

    ![Journalisation des diagnostics affichage] (./media/data-lake-store-diagnostic-logs/view-diagnostic-logs.png "Afficher les journaux de diagnostic") 

2. Dans la carte **Les journaux de Diagnostic** , vous devriez voir les journaux classés par **Les journaux d’Audit** et les **Journaux de requêtes**.
    * Les journaux de demandes capturent chaque demande d’API effectuée dans le compte de données Lake Store.
    * Journaux d’audit sont similaires aux journaux de requêtes mais fournir une description plus détaillée des opérations effectuées sur le compte de données Lake Store. Par exemple, un appel API téléchargement unique dans les journaux de demandes peut entraîner plusieurs opérations « Ajouter » dans les journaux d’audit.

3. Cliquez sur le lien de **téléchargement** par rapport à chaque entrée de journal pour télécharger les journaux.

### <a name="from-the-azure-storage-account-that-contains-log-data"></a>À partir du compte de stockage Azure qui contient les données du journal

1. Ouvrir la carte de compte de stockage Azure associé aux données Lake Store pour l’enregistrement, puis cliquez sur des objets BLOB. La carte de **service d’objets Blob** répertorie les deux conteneurs.

    ![Journalisation des diagnostics affichage] (./media/data-lake-store-diagnostic-logs/view-diagnostic-logs-storage-account.png "Afficher les journaux de diagnostic")

    * conteneur **perspectives-journaux-d’audit** contient les journaux d’audit.
    * Le conteneur **demandes de perspectives journaux** contient les journaux des requêtes.

2. Ces conteneurs, les journaux sont stockées sous la structure suivante.

    ![Journalisation des diagnostics affichage] (./media/data-lake-store-diagnostic-logs/view-diagnostic-logs-storage-account-structure.png "Afficher les journaux de diagnostic")

    Par exemple, le chemin d’accès complet à un journal d’audit peut être`https://adllogs.blob.core.windows.net/insights-logs-audit/resourceId=/SUBSCRIPTIONS/<sub-id>/RESOURCEGROUPS/myresourcegroup/PROVIDERS/MICROSOFT.DATALAKESTORE/ACCOUNTS/mydatalakestore/y=2016/m=07/d=18/h=04/m=00/PT1H.json`

    Similary, le chemin d’accès complet à un journal des demandes peut être`https://adllogs.blob.core.windows.net/insights-logs-requests/resourceId=/SUBSCRIPTIONS/<sub-id>/RESOURCEGROUPS/myresourcegroup/PROVIDERS/MICROSOFT.DATALAKESTORE/ACCOUNTS/mydatalakestore/y=2016/m=07/d=18/h=14/m=00/PT1H.json`

## <a name="understand-the-structure-of-the-log-data"></a>Comprendre la structure de données du journal

Les journaux d’audit et demande sont dans un format JSON. Dans cette section, nous examinons la structure de JSON demande et des journaux d’audit.

### <a name="request-logs"></a>Journaux de demandes

Voici un exemple d’entrée dans le journal des demandes au format JSON. Chaque blob dispose d’un objet racine appelé **enregistrements** qui contient un tableau d’objets de journal.

    {
    "records": 
      [     
        . . . .
        ,
        {
             "time": "2016-07-07T21:02:53.456Z",
             "resourceId": "/SUBSCRIPTIONS/<subscription_id>/RESOURCEGROUPS/<resource_group_name>/PROVIDERS/MICROSOFT.DATALAKESTORE/ACCOUNTS/<data_lake_store_account_name>",
             "category": "Requests",
             "operationName": "GETCustomerIngressEgress",
             "resultType": "200",
             "callerIpAddress": "::ffff:1.1.1.1",
             "correlationId": "4a11c709-05f5-417c-a98d-6e81b3e29c58",
             "identity": "1808bd5f-62af-45f4-89d8-03c5e81bac30",
             "properties": {"HttpMethod":"GET","Path":"/webhdfs/v1/Samples/Outputs/Drivers.csv","RequestContentLength":0,"ClientRequestId":"3b7adbd9-3519-4f28-a61c-bd89506163b8","StartTime":"2016-07-07T21:02:52.472Z","EndTime":"2016-07-07T21:02:53.456Z"}
        }
        ,
        . . . .
      ]
    }

#### <a name="request-log-schema"></a>Schéma des journaux de demande

| Nom            | Type   | Description                                                                    |
|-----------------|--------|--------------------------------------------------------------------------------|
| heure            | Chaîne | L’horodatage (au format UTC) du journal                                              |
| resourceId      | Chaîne | Placer l’ID de la ressource ayant opération                            |
| catégorie        | Chaîne | La catégorie de journal. Par exemple, **demandes**.                                   |
| NomOpération   | Chaîne | Nom de l’opération qui est consignée. Par exemple, getfilestatus.              |
| resultType      | Chaîne | L’état de l’opération, par exemple, 200.                                 |
| callerIpAddress | Chaîne | L’adresse IP du client qui effectue la demande                                |
| ID de corrélation   | Chaîne | L’id du journal pouvant être utilisé pour regrouper un ensemble d’entrées de journal connexes |
| identité        | Objet | L’identité qui a généré le journal                                            |
| propriétés      | JSON   | Voir ci-dessous pour plus d’informations                                                          |

#### <a name="request-log-properties-schema"></a>Schéma des propriétés de la demande de journaux

| Nom                 | Type   | Description                                               |
|----------------------|--------|-----------------------------------------------------------|
| HttpMethod           | Chaîne | La méthode HTTP utilisée pour l’opération. Par exemple, obtenez. |
| Chemin d’accès                 | Chaîne | Le chemin d’accès l’opération a été effectuée sur                   |
| RequestContentLength | ent    | La longueur du contenu de la requête HTTP                    |
| ClientRequestId      | Chaîne | L’Id qui identifie de cette demande              |
| Heure de début            | Chaîne | L’heure à laquelle le serveur a reçu la demande         |
| L’heure de fin              | Chaîne | L’heure à laquelle le serveur a envoyé une réponse              |

### <a name="audit-logs"></a>Journaux d’audit

Voici un exemple d’entrée dans le journal d’audit au format JSON. Chaque blob dispose d’un objet racine appelé **enregistrements** qui contient un tableau d’objets de journal

    {
    "records": 
      [     
        . . . .
        ,
        {
             "time": "2016-07-08T19:08:59.359Z",
             "resourceId": "/SUBSCRIPTIONS/<subscription_id>/RESOURCEGROUPS/<resource_group_name>/PROVIDERS/MICROSOFT.DATALAKESTORE/ACCOUNTS/<data_lake_store_account_name>",
             "category": "Audit",
             "operationName": "SeOpenStream",
             "resultType": "0",
             "correlationId": "381110fc03534e1cb99ec52376ceebdf;Append_BrEKAmg;25.66.9.145",
             "identity": "A9DAFFAF-FFEE-4BB5-A4A0-1B6CBBF24355",
             "properties": {"StreamName":"adl://<data_lake_store_account_name>.azuredatalakestore.net/logs.csv"}
        }
        ,
        . . . .
      ]
    }

#### <a name="audit-log-schema"></a>Schéma des journaux d’audit

| Nom            | Type   | Description                                                                    |
|-----------------|--------|--------------------------------------------------------------------------------|
| heure            | Chaîne | L’horodatage (au format UTC) du journal                                              |
| resourceId      | Chaîne | Placer l’ID de la ressource ayant opération                            |
| catégorie        | Chaîne | La catégorie de journal. Par exemple, **Audit**.                                      |
| NomOpération   | Chaîne | Nom de l’opération qui est consignée. Par exemple, getfilestatus.              |
| resultType      | Chaîne | L’état de l’opération, par exemple, 200.                                 |
| ID de corrélation   | Chaîne | L’id du journal pouvant être utilisé pour regrouper un ensemble d’entrées de journal connexes |
| identité        | Objet | L’identité qui a généré le journal                                            |
| propriétés      | JSON   | Voir ci-dessous pour plus d’informations                                                          |

#### <a name="audit-log-properties-schema"></a>Schéma de propriétés des journaux d’audit

| Nom       | Type   | Description                              |
|------------|--------|------------------------------------------|
| StreamName | Chaîne | Le chemin d’accès l’opération a été effectuée sur  |


## <a name="samples-to-process-the-log-data"></a>Exemples de traiter les données du journal

Magasin de Lake données Azure fournit un exemple sur la façon de traiter et analyser les données du journal. Vous pouvez trouver l’échantillon sur [https://github.com/Azure/AzureDataLake/tree/master/Samples/AzureDiagnosticsSample](https://github.com/Azure/AzureDataLake/tree/master/Samples/AzureDiagnosticsSample). 


## <a name="see-also"></a>Voir aussi

- [Vue d’ensemble Azure Lake de magasin de données](data-lake-store-overview.md)
- [Sécuriser les données dans les données Lake Store](data-lake-store-secure-data.md)


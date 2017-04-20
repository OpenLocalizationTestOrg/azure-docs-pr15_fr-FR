<properties 
   pageTitle="Afficher les journaux de diagnostic pour Azure données Lake Analytique | Microsoft Azure" 
   description="Comprendre comment configurer et accéder aux journaux de diagnostic pour analytique Lake de données Azure " 
   services="data-lake-analytics" 
   documentationCenter="" 
   authors="Blackmist" 
   manager="jhubbard" 
   editor="cgronlun"/>
 
<tags
   ms.service="data-lake-analytics"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="08/11/2016"
   ms.author="larryfr"/>

# <a name="accessing-diagnostic-logs-for-azure-data-lake-analytics"></a>Accès aux journaux de diagnostics pour Azure données Lake Analytique

Découvrez comment activer la journalisation des diagnostics pour votre compte de données Lake Analytique et comment afficher les journaux collectées pour votre compte.

Organisations peuvent activer la journalisation des diagnostics pour leur compte Azure données Lake Analytique recueillir des données access audits. Ces journaux fournissent des informations telles que :

* Liste des utilisateurs ayant accédé les données.
* La fréquence à laquelle les données sont accessibles.
* La quantité de données est stockée dans le compte.

## <a name="prerequisites"></a>Conditions préalables

- **Azure un abonnement**. Voir [Azure obtenir la version d’évaluation gratuite](https://azure.microsoft.com/pricing/free-trial/).
- **Activer votre abonnement Azure** pour les données Lake Analytique Public Preview. Consultez les [instructions](data-lake-analytics-get-started-portal.md#signup).
- **Compte azure données Lake Analytique**. Suivez les instructions sur la [prise en main Azure données Lake Analytique à l’aide du portail Azure](data-lake-analytics-get-started-portal.md).

## <a name="enable-logging"></a>Activer la journalisation

1. Ouverture de session au nouveau [portail Azure](https://portal.azure.com).

2. Ouvrez votre compte données Lake Analytique et à partir de votre carte de compte données Lake Analytique, cliquez sur **paramètres**, puis cliquez sur **Paramètres de Diagnostic**.

3. Dans la carte de **Diagnostic** , apportez les modifications suivantes pour configurer la journalisation des Diagnostics.

    ![Activer la journalisation des diagnostics] (./media/data-lake-analytics-diagnostic-logs/enable-diagnostic-logs.png "Activer les journaux de diagnostic")

    * Définir le **statut** sur **sur** pour activer la journalisation des Diagnostics.
    * Vous pouvez choisir banque/traiter les données de deux façons différentes.
        * Sélectionnez **Exporter vers événement concentrateur** pour répartir des données journal à un concentrateur événement Azure. Utilisez cette option si vous avez un pipeline de traitement en aval analyser les journaux entrants en temps réel. Si vous sélectionnez cette option, vous devez fournir les détails pour le Hub événement Azure que vous voulez utiliser.
        * Sélectionnez **Exporter vers un compte de stockage** pour stocker les fichiers journaux à un compte de stockage Azure. Utilisez cette option si vous souhaitez archiver les données. Si vous sélectionnez cette option, vous devez fournir un compte de stockage Azure pour enregistrer les journaux.
    * Indiquez si vous souhaitez obtenir les journaux d’audit ou les journaux de requêtes ou les deux.
    * Spécifier le nombre de jours pour lesquels les données doivent être conservées.
    * Cliquez sur **Enregistrer**.

Une fois que vous avez activé les paramètres de diagnostic, vous pouvez visionner les journaux dans l’onglet **Journaux de Diagnostic** .

## <a name="view-logs"></a>Afficher les journaux

Il existe deux manières d’afficher les données du journal pour votre compte de données Lake Analytique.

* Dans les paramètres du compte données Lake Analytique
* À partir du compte de stockage Azure où sont stockées les données

### <a name="using-the-data-lake-analytics-settings-view"></a>Utilisation de la vue de données Lake Analytique Paramètres

1. À partir de votre carte de **paramètres** de compte données Lake Analytique, cliquez sur **Les journaux de Diagnostic**.

    ![Journalisation des diagnostics affichage] (./media/data-lake-analytics-diagnostic-logs/view-diagnostic-logs.png "Afficher les journaux de diagnostic") 

2. Dans la carte **Les journaux de Diagnostic** , vous devriez voir les journaux classés par **Les journaux d’Audit** et les **Journaux de requêtes**.
    * Les journaux de demandes capturent chaque demande d’API effectuée dans le compte de données Lake Analytique.
    * Journaux d’audit sont similaires aux journaux de requêtes mais fournir une description plus détaillée des opérations effectuées sur le compte de données Lake Analytique. Par exemple, un appel API téléchargement unique dans les journaux de demandes peut entraîner plusieurs opérations « Ajouter » dans les journaux d’audit.

3. Cliquez sur le lien de **téléchargement** pour une entrée de journal télécharger les journaux.

### <a name="from-the-azure-storage-account-that-contains-log-data"></a>À partir du compte de stockage Azure qui contient les données du journal

1. Ouvrir la carte de compte de stockage Azure associé aux données Lake Analytique pour l’enregistrement, puis cliquez sur des objets BLOB. La carte de **service d’objets Blob** répertorie les deux conteneurs.

    ![Journalisation des diagnostics affichage] (./media/data-lake-analytics-diagnostic-logs/view-diagnostic-logs-storage-account.png "Afficher les journaux de diagnostic")

    * conteneur **perspectives-journaux-d’audit** contient les journaux d’audit.
    * Le conteneur **demandes journaux d’analyse** contient les journaux des requêtes.

2. Ces conteneurs, les journaux sont stockées sous la structure suivante.

        resourceId=/
          SUBSCRIPTIONS/
            <<SUBSCRIPTION_ID>>/
              RESOURCEGROUPS/
                <<RESOURCE_GRP_NAME>>/
                  PROVIDERS/
                    MICROSOFT.DATALAKEANALYTICS/
                      ACCOUNTS/
                        <DATA_LAKE_ANALYTICS_NAME>>/
                          y=####/
                            m=##/
                              d=##/
                                h=##/
                                  m=00/
                                    PT1H.json
    
    > [AZURE.NOTE] La `##` entrées dans le chemin d’accès contient l’année, mois, jour et heure dans laquelle le journal a été créé. Données Lake Analytique crée, un seul fichier toutes les heures, donc `m=` doit toujours contenir une valeur de `00`.

    Par exemple, le chemin d’accès complet à un journal d’audit peut être :
    
        https://adllogs.blob.core.windows.net/insights-logs-audit/resourceId=/SUBSCRIPTIONS/<sub-id>/RESOURCEGROUPS/myresourcegroup/PROVIDERS/MICROSOFT.DATALAKEANALYTICS/ACCOUNTS/mydatalakeanalytics/y=2016/m=07/d=18/h=04/m=00/PT1H.json

    De même, le chemin d’accès complet à un journal des demandes peut être :
    
        https://adllogs.blob.core.windows.net/insights-logs-requests/resourceId=/SUBSCRIPTIONS/<sub-id>/RESOURCEGROUPS/myresourcegroup/PROVIDERS/MICROSOFT.DATALAKEANALYTICS/ACCOUNTS/mydatalakeanalytics/y=2016/m=07/d=18/h=14/m=00/PT1H.json

## <a name="log-structure"></a>Structure de journal

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
             "resourceId": "/SUBSCRIPTIONS/<subscription_id>/RESOURCEGROUPS/<resource_group_name>/PROVIDERS/MICROSOFT.DATALAKEANALYTICS/ACCOUNTS/<data_lake_analytics_account_name>",
             "category": "Requests",
             "operationName": "GetAggregatedJobHistory",
             "resultType": "200",
             "callerIpAddress": "::ffff:1.1.1.1",
             "correlationId": "4a11c709-05f5-417c-a98d-6e81b3e29c58",
             "identity": "1808bd5f-62af-45f4-89d8-03c5e81bac30",
             "properties": {
                 "HttpMethod":"POST",
                 "Path":"/JobAggregatedHistory",
                 "RequestContentLength":122,
                 "ClientRequestId":"3b7adbd9-3519-4f28-a61c-bd89506163b8",
                 "StartTime":"2016-07-07T21:02:52.472Z",
                 "EndTime":"2016-07-07T21:02:53.456Z"
                 }
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
| NomOpération   | Chaîne | Nom de l’opération qui est consignée. Par exemple, GetAggregatedJobHistory.              |
| resultType      | Chaîne | L’état de l’opération, par exemple, 200.                                 |
| callerIpAddress | Chaîne | L’adresse IP du client qui effectue la demande                                |
| ID de corrélation   | Chaîne | L’id du journal. Cette valeur peut être utilisée pour regrouper un ensemble d’entrées de journal connexes |
| identité        | Objet | L’identité qui a généré le journal                                            |
| propriétés      | JSON   | Consultez la section suivante (schéma des propriétés de la demande de journal) pour plus d’informations |

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
             "time": "2016-07-28T19:15:16.245Z",
             "resourceId": "/SUBSCRIPTIONS/<subscription_id>/RESOURCEGROUPS/<resource_group_name>/PROVIDERS/MICROSOFT.DATALAKEANALYTICS/ACCOUNTS/<data_lake_ANALYTICS_account_name>",
             "category": "Audit",
             "operationName": "JobSubmitted",
             "identity": "user@somewhere.com",
             "properties": {
                 "JobId":"D74B928F-5194-4E6C-971F-C27026C290E6",
                 "JobName": "New Job", 
                 "JobRuntimeName": "default",
                 "SubmitTime": "7/28/2016 7:14:57 PM"
                 }
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
| NomOpération   | Chaîne | Nom de l’opération qui est consignée. Par exemple, JobSubmitted.              |
| resultType | Chaîne | Sous-état pour l’état du travail (NomOpération). |
| resultSignature | Chaîne | Plus d’informations sur l’état du travail (NomOpération). |
| identité      | Chaîne | L’utilisateur qui a demandé l’opération. Par exemple, susan@contoso.com.                                 |
| propriétés      | JSON   | Consultez la section suivante (schéma des propriétés du journal d’Audit) pour plus d’informations |

> [AZURE.NOTE] __resultType__ __resultSignature__ fournissent des informations sur le résultat d’une opération et contenir une valeur si une opération est terminée. Par exemple, elles contiennent une valeur lorsque __NomOpération__ contient une valeur de __JobStarted__ ou __JobEnded__.

#### <a name="audit-log-properties-schema"></a>Schéma de propriétés des journaux d’audit

| Nom       | Type   | Description                              |
|------------|--------|------------------------------------------|
| ID de travail | Chaîne | Le code affecté à la tâche  |
| JobName | Chaîne | Le nom qui a été fourni pour le travail |
| JobRunTime | Chaîne | Le runtime permet de traiter le travail |
| SubmitTime | Chaîne | L’heure (UTC) que la tâche a été envoyée |
| Heure de début | Chaîne | Heure de que la tâche a démarré après la présentation (au format UTC). |
| L’heure de fin | Chaîne | Heure de fin de la tâche. |
| Parallélisme | Chaîne | Le nombre d’unités de données Lake Analytique demandé pour cette tâche lors de l’envoi. |

> [AZURE.NOTE] __SubmitTime__, __heure de début__, __heure de fin__ et __parallélisme__ fournissent des informations sur une opération et contient une valeur si une opération a commencé ou terminé. Par exemple, __SubmitTime__ contient une valeur après __NomOpération__ indique __JobSubmitted__.

## <a name="process-the-log-data"></a>Traiter les données du journal

Azure données Lake Analytique fournit un exemple sur la façon de traiter et analyser les données du journal. Vous pouvez trouver l’échantillon sur [https://github.com/Azure/AzureDataLake/tree/master/Samples/AzureDiagnosticsSample](https://github.com/Azure/AzureDataLake/tree/master/Samples/AzureDiagnosticsSample). 


## <a name="next-steps"></a>Étapes suivantes

- [Vue d’ensemble des données Azure Lake Analytique](data-lake-analytics-overview.md)



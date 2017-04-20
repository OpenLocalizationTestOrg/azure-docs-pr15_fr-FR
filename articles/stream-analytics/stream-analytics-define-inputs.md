<properties
    pageTitle="Connexion de données : entrées à partir d’un flux d’événements de flux de données | Microsoft Azure"
    description="En savoir plus sur la configuration d’une connexion de données à Analytique de flux de données appelée « entrées ». Entrées incluent un flux de données à partir d’événements et également référence à des données."
    keywords="flux de données, connexion de données, flux d’événements"
    services="stream-analytics"
    documentationCenter=""
    authors="jeffstokes72"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="stream-analytics"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="data-services"
    ms.date="09/26/2016"
    ms.author="jeffstok"/>

# <a name="data-connection-learn-about-data-stream-inputs-from-events-to-stream-analytics"></a>Connexion de données : en savoir plus sur les données entrées flux à partir d’événements de flux Analytique

La connexion de données au flux Analytique est un flux de données d’événements à partir d’une source de données. Cette option est appelée « entrée ». Flux Analytique a première classe intégration avec Azure flux sources événement Hub, concentrateur IoT et Blob stockage des données qui peut être de l’abonnement Azure identiques ou différent en tant que votre tâche analytique.

## <a name="data-input-types-data-stream-and-reference-data"></a>Types de saisie de données : les données de référence et des flux de données
Comme données sont basculées vers une source de données, il est utilisé par la tâche de flux de données Analytique et traité en temps réel. Entrées sont divisées en deux types : entrées de flux de données et des entrées de données de référence.

### <a name="data-stream-inputs"></a>Entrées du flux de données
Un flux de données est séquence illimitée d’événements bientôt au fil du temps. Tâches d’Analytique flux doivent inclure entrée de flux de données au moins une pour être consommées et transformées par la tâche. Stockage d’objets BLOB, événement Hubs et IoT Hubs sont prises en charge en tant que sources d’entrée de flux de données. Événement Hubs servent à recueillir les flux d’événements à partir de plusieurs périphériques et services, tels que les flux d’activités médias sociaux, informations commerciales boursier ou données de capteurs. IoT Hubs sont optimisés pour collecter les données à partir d’appareils connectés dans les scénarios Internet des objets (IoT).  Stockage BLOB peut être utilisé comme source d’entrée pour traiter les données en bloc sous forme de flux.  

### <a name="reference-data"></a>Données de référence
Flux Analytique prend en charge un deuxième type d’entrée appelé des données de référence. Il s’agit des données auxiliaires qui est statique ou variation lente au fil du temps et sont généralement utilisées pour effectuer des recherches et corrélation. Stockage d’objets Blob Azure est actuellement la seule source d’entrée pris en charge pour les données de référence. Des objets BLOB de source de données de référence sur la taille sont limitées à 100 Mo.
Pour apprendre à créer des entrées de données de référence, voir [Utiliser les données de référence](stream-analytics-use-reference-data.md)  

## <a name="create-a-data-stream-input-with-an-event-hub"></a>Créer une entrée de flux de données avec un concentrateur de l’événement

[Azure événement Hubs](https://azure.microsoft.com/services/event-hubs/) sont très scalable publication abonnement ingestor événement. Il recueille des millions d’événements par seconde, afin que vous puissiez traiter et analyser les grandes quantités de données obtenues par vos périphériques connectés et les applications. Il s’agit des entrées plus couramment utilisées pour les flux Analytique. Événement Hubs et flux Analytique ensemble proposer une solution de bout en bout pour analytique en temps réel. Événement Hubs permettent aux flux des événements à Azure en temps réel clients et tâches de flux de données Analytique puissent les traiter en temps réel. Par exemple, clients peuvent envoyer web clics, lectures des capteurs, événements du journal online aux événements Hubs et créer des tâches de flux Analytique pour utiliser l’événement Hubs comme les flux de données d’entrée de filtrage en temps réel, de regroupement et de corrélation.

Il est important de noter que l’horodatage par défaut des événements en provenance de Hubs événement dans le flux de données Analytique est l’horodatage de l’événement arrivés dans Hub événement qui est EventEnqueuedUtcTime. Pour traiter les données sous forme de flux à l’aide d’un horodatage dans la charge utile événement, le mot-clé [Horodatage par](https://msdn.microsoft.com/library/azure/dn834998.aspx) doit être utilisé.

### <a name="consumer-groups"></a>Groupes de consommateurs

Chaque entrée flux Analytique événement concentrateur doit être configurée pour avoir son propre groupe grand public. Lorsqu’une tâche contient une jointure réflexive ou plusieurs entrées, une entrée peut-être être lus par le lecteur de plusieurs en aval, qui a un impact sur le nombre de lecteurs dans un groupe seul consommateur. Pour éviter de dépassement de la limite d’événement concentrateur de 5 lecteurs par groupe consommateur par partition, il est recommandé de désigner un groupe de fournisseurs correspondant à chaque tâche de flux de données Analytique. Notez qu’il existe également une limite de 20 groupes consommateur par événement concentrateur. Pour plus d’informations, voir le [Guide de programmation Hubs événement](../event-hubs/event-hubs-programming-guide.md).

### <a name="configure-event-hub-as-an-input-data-stream"></a>Configurer événement concentrateur comme un flux de données d’entrée

Le tableau ci-dessous répertorie chaque propriété en cas de concentrateur onglet avec sa description saisie :

| NOM DE LA PROPRIÉTÉ | DESCRIPTION |
|------|------|
| Alias d’entrée | Un nom convivial qui servira de la requête de travail pour faire référence à cette entrée |
| Service Bus Namespace | Un espace de noms Bus des services est un conteneur pour un ensemble d’entités de messagerie. Lorsque vous avez créé un nouveau concentrateur d’événement, vous avez créé également un espace de noms Bus des services. |
| Concentrateur de l’événement | Le nom de votre plateforme d’événement d’entrée. |
| Nom de la stratégie concentrateur événement | La stratégie d’accès partagé, qui peut être créée sous l’onglet événement concentrateur configurer. Chaque stratégie d’accès partagé sera ont un nom, les autorisations que vous définissez, et les touches d’accès. |
| Clé de stratégie concentrateur événement | Touche d’accès partagé utilisée pour authentifier l’accès à l’espace de noms Bus des services. |
| Groupe des consommateurs concentrateur événement (facultatif) | Le groupe consommateur pour ajouter des données à partir du Hub de l’événement. Si ne pas spécifié, tâches de flux de données Analytique utilisera le groupe consommateur par défaut pour ajouter des données à partir du Hub de l’événement. Il est recommandé d’utiliser un groupe de consommateur distinct pour chaque tâche de flux de données Analytique. |
| Format de sérialisation événement | Pour vous assurer que vos requêtes fonctionnent comme prévu, Analytique de flux de données a besoin de savoir quel format de sérialisation (JSON, CSV ou Avro), vous utilisez pour flux de données entrant. |
| Codage | UTF-8 est le format de codage pris en charge uniquement pour le moment. |

Lorsque vos données proviennent d’une source d’événement concentrateur, vous pouvez accéder à plusieurs champs de métadonnées dans votre requête Analytique de flux de données. Le tableau ci-dessous répertorie les champs et leur description.

| PROPRIÉTÉ | DESCRIPTION |
|------|------|
| EventProcessedUtcTime | La date et l’heure auxquelles l’événement a été traitée par flux Analytique. |
| EventEnqueuedUtcTime | La date et l’heure auxquelles l’événement a été reçu par événement Hubs. |
| IDPartition | L’ID de partition base zéro pour la carte d’entrée. |

Par exemple, vous pouvez écrire une requête comme suit :

````
SELECT
    EventProcessedUtcTime,
    EventEnqueuedUtcTime,
    PartitionId
FROM Input
````

## <a name="create-an-iot-hub-data-stream-input"></a>Créer une entrée de flux de données IoT concentrateur

Concentrateur Iot Azure est très scalable publication abonnement ingestor événement optimisé pour les scénarios IoT.
Il est important de noter que l’horodatage par défaut des événements en provenance de Hubs IoT dans flux Analytique est l’horodatage de l’événement arrivés dans IoT Hub qui est EventEnqueuedUtcTime. Pour traiter les données sous forme de flux à l’aide d’un horodatage dans la charge utile événement, le mot-clé [Horodatage par](https://msdn.microsoft.com/library/azure/dn834998.aspx) doit être utilisé.

> [AZURE.NOTE] Seuls les messages envoyés avec une propriété DeviceClient peuvent être traités.

### <a name="consumer-groups"></a>Groupes de consommateurs

Chaque entrée flux Analytique IoT concentrateur doit être configurée pour avoir son propre groupe grand public. Lorsqu’une tâche contient une jointure réflexive ou plusieurs entrées, une entrée peut-être être lus par le lecteur de plusieurs en aval, qui a un impact sur le nombre de lecteurs dans un groupe seul consommateur. Pour éviter de dépasser limite IoT concentrateur de 5 lecteurs par groupe consommateur par partition, il est recommandé de désigner un groupe de fournisseurs correspondant à chaque tâche de flux de données Analytique.

### <a name="configure-iot-hub-as-an-data-stream-input"></a>Configurer IoT concentrateur comme un flux de données d’entrée

Le tableau ci-dessous répertorie chaque propriété de l’onglet d’entrée IoT concentrateur avec sa description :

| NOM DE LA PROPRIÉTÉ | DESCRIPTION |
|------|------|
| Alias d’entrée | Un nom convivial qui servira de la requête de travail pour faire référence à cette entrée. |
| Concentrateur IoT | Un concentrateur IoT est un conteneur pour un ensemble d’entités de messagerie. |
| Point de terminaison | Le nom de votre point de terminaison IoT concentrateur. |
| Nom de la stratégie accès partagé | La stratégie d’accès partagé pour donner accès au IoT Hub. Chaque stratégie d’accès partagé sera ont un nom, les autorisations que vous définissez, et les touches d’accès. |
| Clé de stratégie d’accès partagé | Touche d’accès partagé utilisée pour authentifier l’accès au concentrateur IoT. |
| Groupe consommateurs (facultatif) | Le groupe consommateur pour ajouter des données à partir du IoT Hub. Si ne pas spécifié, tâches de flux de données Analytique utilisera le groupe consommateur par défaut pour ajouter des données à partir du IoT Hub. Il est recommandé d’utiliser un groupe de consommateur distinct pour chaque tâche de flux de données Analytique. |
| Format de sérialisation événement | Pour vous assurer que vos requêtes fonctionnent comme prévu, Analytique de flux de données a besoin de savoir quel format de sérialisation (JSON, CSV ou Avro), vous utilisez pour flux de données entrant. |
| Codage | UTF-8 est le format de codage pris en charge uniquement pour le moment. |

Lorsque vos données proviennent d’une source IoT concentrateur, vous pouvez accéder à plusieurs champs de métadonnées dans votre requête Analytique de flux de données. Le tableau ci-dessous répertorie les champs et leur description.

| PROPRIÉTÉ | DESCRIPTION |
|------|------|
| EventProcessedUtcTime | La date et l’heure à laquelle l’événement a été traité. |
| EventEnqueuedUtcTime | La date et l’heure auxquelles l’événement a été reçu par le IoT Hub. |
| IDPartition | L’ID de partition base zéro pour la carte d’entrée. |
| IoTHub.MessageId | Utilisées pour faire correspondre une communication bidirectionnelle dans IoT Hub. |
| IoTHub.CorrelationId | Utilisés dans les réponses aux messages et les commentaires dans IoT Hub. |
| IoTHub.ConnectionDeviceId | Id authentifié utilisé pour envoyer ce message, cachet sur les messages servicebound IoT concentrateur. |
| IoTHub.ConnectionDeviceGenerationId | La generationId du périphérique authentifié utilisé pour envoyer ce message, cachet sur les messages servicebound IoT concentrateur. |
| IoTHub.EnqueuedTime | Temps lorsque le message a été reçu par IoT concentrateur. |
| IoTHub.StreamId | Propriété de type événement personnalisé ajoutée par le périphérique de l’expéditeur. |

## <a name="create-a-blob-storage-data-stream-input"></a>Créer une entrée de flux de données de stockage Blob

Scénarios de grandes quantités de données non structurées à stocker dans le nuage, stockage d’objets Blob offre une solution rentable et format SVG. Données dans le [stockage Blob](https://azure.microsoft.com/services/storage/blobs/) sont généralement considéré comme données « inactives », mais il peut être traité comme un flux de données par flux Analytique. Un scénario courant pour les entrées de stockage Blob avec flux Analytique est traitement du journal, où télémétrie est capturé à partir d’un système et doit être analysé et traités pour extraire les données significatives.

Il est important de noter que l’horodatage par défaut des événements de stockage Blob dans flux Analytique est l’horodatage que le blob a été modifié en dernier les *isBlobLastModifiedUtcTime*. Pour traiter les données sous forme de flux à l’aide d’un horodatage dans la charge utile événement, le mot-clé [Horodatage par](https://msdn.microsoft.com/library/azure/dn834998.aspx) doit être utilisé.

Notez également que CSV mis en forme entrées **nécessitent** une ligne d’en-tête pour définir des champs pour le jeu de données. Autres champs de ligne d’en-tête doivent être **unique**.

> [AZURE.NOTE] Flux Analytique n’autorise pas l’ajout de contenu à un blob existant. Flux Analytique sera uniquement afficher un blob une seule fois et les modifications effectuées après que cette lecture ne sera pas traité. La meilleure solution consiste à télécharger une fois toutes les données et pas ajouter tous les événements supplémentaires dans le magasin d’objets blob.

Le tableau ci-dessous répertorie chaque propriété de l’onglet d’entrée du stockage Blob avec sa description :

<table>
<tbody>
<tr>
<td>NOM DE LA PROPRIÉTÉ</td>
<td>DESCRIPTION</td>
</tr>
<tr>
<td>Alias d’entrée</td>
<td>Un nom convivial qui servira de la requête de travail pour faire référence à cette entrée.</td>
</tr>
<tr>
<td>Compte de stockage</td>
<td>Le nom du compte de stockage où se trouvent vos fichiers blob.</td>
</tr>
<tr>
<td>Clé de compte de stockage</td>
<td>Le code secret associé au compte de stockage.</td>
</tr>
<tr>
<td>Conteneur de stockage
</td>
<td>Les conteneurs fournissent un regroupement logique pour les objets BLOB stockées dans le service Microsoft Azure Blob. Lorsque vous téléchargez un blob au service Blob, vous devez spécifier un conteneur pour ce blob.</td>
</tr>
<tr>
<td>Chemin d’accès du préfixe motif [facultatif]</td>
<td>Le chemin d’accès de fichier utilisé pour localiser votre BLOB dans le conteneur spécifié.
Dans le chemin d’accès, vous pouvez choisir de spécifier une ou plusieurs instances des 3 variables suivantes :<BR>{date}, {time}<BR>{partition}<BR>Exemple 1 : cluster1/journaux / {date} / {time} / {partition}<BR>Exemple 2 : cluster1/journaux / {date}<P>Notez que « * » n’est pas une valeur autorisée pour pathprefix. Uniquement valide <a HREF="https://msdn.microsoft.com/library/azure/dd135715.aspx">caractères blob Azure</a> sont autorisés.</td>
</tr>
<tr>
<td>Format de date [facultatif]</td>
<td>Si le jeton de date est utilisé dans le chemin d’accès de préfixe, vous pouvez sélectionner le format de date dans lequel vos fichiers sont organisées. Exemple : AAAA/MM/JJ</td>
</tr>
<tr>
<td>Format d’heure [facultatif]</td>
<td>Si le jeton de temps est utilisé dans le chemin d’accès de préfixe, spécifiez le format d’heure dans lequel vos fichiers sont organisées. Actuellement la seule valeur pris en charge est HH.</td>
</tr>
<tr>
<td>Format de sérialisation événement</td>
<td>Pour vous assurer que vos requêtes fonctionnent comme prévu, Analytique de flux de données a besoin de savoir quel format de sérialisation (JSON, CSV ou Avro), vous utilisez pour flux de données entrant.</td>
</tr>
<tr>
<td>Codage</td>
<td>Pour CSV et JSON, UTF-8 est le format de codage pris en charge uniquement pour le moment.</td>
</tr>
<tr>
<td>Délimiteur</td>
<td>Flux Analytique prend en charge un certain nombre de délimiteurs communs pour sérialisation des données au format CSV. Valeurs prises en charge sont virgule, point-virgule, espace, onglet et barre verticale.</td>
</tr>
</tbody>
</table>

Lorsque vos données proviennent d’une source de stockage Blob, vous pouvez accéder plusieurs champs de métadonnées dans votre requête Analytique de flux de données. Le tableau ci-dessous répertorie les champs et leur description.

| PROPRIÉTÉ | DESCRIPTION |
|------|------|
| BlobName | Le nom de l’objet blob d’entrée dont provient l’événement. |
| EventProcessedUtcTime | La date et l’heure auxquelles l’événement a été traitée par flux Analytique. |
| BlobLastModifiedUtcTime | La date et l’heure auxquelles le blob a été modifié en dernier. |
| IDPartition | L’ID de partition base zéro pour la carte d’entrée. |

Par exemple, vous pouvez écrire une requête comme suit :

````
SELECT
    BlobName,
    EventProcessedUtcTime,
    BlobLastModifiedUtcTime
FROM Input
````


## <a name="get-help"></a>Obtenir de l’aide
Pour obtenir une aide supplémentaire, essayez de notre [forum Azure flux Analytique](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>Étapes suivantes
Vous avez appris sur les options de connexion de données dans Azure pour vos travaux Analytique de flux de données. Pour en savoir plus sur les flux Analytique, voir :

- [Commencer à utiliser Azure flux Analytique](stream-analytics-get-started.md)
- [Échelle Azure flux Analytique travaux](stream-analytics-scale-jobs.md)
- [Référence du langage flux Azure Analytique requête](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Référence de l’API REST gestion Analytique flux Azure](https://msdn.microsoft.com/library/azure/dn835031.aspx)

<!--Link references-->
[stream.analytics.developer.guide]: ../stream-analytics-developer-guide.md
[stream.analytics.scale.jobs]: stream-analytics-scale-jobs.md
[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-get-started.md
[stream.analytics.query.language.reference]: http://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: http://go.microsoft.com/fwlink/?LinkId=517301

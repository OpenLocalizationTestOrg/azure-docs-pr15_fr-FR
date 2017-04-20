<properties
    pageTitle="Flux des sorties Analytique : Options pour le stockage, analyse | Microsoft Azure"
    description="Obtenir des informations à propos du ciblage flux Analytique données sorties options notamment Power BI pour les résultats de l’analyse."
    keywords="transformation de données, les résultats de l’analyse, les options de stockage de données"
    services="stream-analytics,documentdb,sql-database,event-hubs,service-bus,storage"
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

# <a name="stream-analytics-outputs-options-for-storage-analysis"></a>Flux des sorties Analytique : Options pour le stockage, analyse

Lorsque vous créez une tâche de flux de données Analytique, envisagez comment les données résultantes seront consommées. Comment consulterez les résultats de la tâche de flux de données Analytique et dans lequel vous stockera il ?

Pour activer une variété de modèles d’application, Azure flux Analytique contient différentes options pour le stockage de sortie et afficher les résultats de l’analyse. Cela facilite la sortie des tâches permet d’afficher et vous propose de flexibilité lors de la consommation et le stockage de la sortie des tâches pour data warehouse et d’autres fins. Aucun résultat configuré dans le travail doit exister avant que le travail est démarré et événements Démarrer s’étalant. Par exemple, si vous utilisez le stockage Blob comme résultat, le travail ne crée pas un compte de stockage automatiquement. Il doit être créés par l’utilisateur avant le début de la tâche ASA.

## <a name="azure-data-lake-store"></a>Magasin de Lake données Azure

Flux Analytique prend en charge [Azure données Lake Store](https://azure.microsoft.com/services/data-lake-store/). Ce stockage permet de stocker des données de n’importe quelle vitesse taille, le type et réception d’analytique opérationnelles et exploratoires. Pour l’instant, la création et la configuration des sorties de données Lake Store est pris en charge uniquement dans le portail classique Azure. En outre, flux Analytique doit être autorisés à accéder au magasin Lake de données. Vous trouverez plus d’informations sur l’autorisation et comment s’inscrire à l’aperçu des données Lake Store (si nécessaire) dans l' [article de sortie Lake de données](stream-analytics-data-lake-output.md).

### <a name="authorize-an-azure-data-lake-store"></a>Autoriser un magasin Lake de données Azure

Lors de stockage des données Lake est sélectionné comme résultat dans le portail de gestion Azure, vous demandera pour autoriser la connexion à un magasin Lake données existant.  

![Autorisez les données Lake Store](./media/stream-analytics-define-outputs/06-stream-analytics-define-outputs.png)  

Puis remplissez les propriétés de la sortie de données Lake Store comme indiqué ci-dessous :

![Autorisez les données Lake Store](./media/stream-analytics-define-outputs/07-stream-analytics-define-outputs.png)  

Le tableau ci-dessous répertorie les noms des propriétés et leur description nécessaires pour créer un données Lake stocker les résultats.

<table>
<tbody>
<tr>
<td><B>NOM DE LA PROPRIÉTÉ</B></td>
<td><B>DESCRIPTION</B></td>
</tr>
<tr>
<td>Alias de sortie</td>
<td>Il s’agit d’un nom convivial permettant de diriger le résultat de la requête pour le magasin Lake de données dans des requêtes.</td>
</tr>
<tr>
<td>Nom du compte</td>
<td>Le nom du compte de stockage des données Lake où vous envoyez votre sortie. S’affiche avec une liste déroulante liste des comptes de données Lake Store qui a accès à l’utilisateur connecté au portail.</td>
</tr>
<tr>
<td>Chemin d’accès du préfixe motif [<I>facultatif</I>]</td>
<td>Le chemin d’accès de fichier utilisé pour écrire vos fichiers dans le compte de magasin de données Lake spécifié. <BR>{date}, {time}<BR>Exemple 1 : dossier 1/journaux / {date} / {time}<BR>Exemple 2 : dossier 1/journaux / {date}</td>
</tr>
<tr>
<td>Format de date [<I>facultatif</I>]</td>
<td>Si le jeton de date est utilisé dans le chemin d’accès de préfixe, vous pouvez sélectionner le format de date dans lequel vos fichiers sont organisées. Exemple : AAAA/MM/JJ</td>
</tr>
<tr>
<td>Format d’heure [<I>facultatif</I>]</td>
<td>Si le jeton de temps est utilisé dans le chemin d’accès de préfixe, spécifiez le format d’heure dans lequel vos fichiers sont organisées. Actuellement la seule valeur pris en charge est HH.</td>
</tr>
<tr>
<td>Format de sérialisation événement</td>
<td>Format de sérialisation pour les données de sortie. JSON, CSV et Avro sont pris en charge.</td>
</tr>
<tr>
<td>Codage</td>
<td>Si le format CSV ou JSON, un codage doit être spécifié. UTF-8 est le format de codage pris en charge uniquement pour le moment.</td>
</tr>
<tr>
<td>Délimiteur</td>
<td>Uniquement applicable pour la sérialisation CSV. Flux Analytique prend en charge un certain nombre de délimiteurs communs pour sérialisation des données CSV. Valeurs prises en charge sont virgule, point-virgule, espace, onglet et barre verticale.</td>
</tr>
<tr>
<td>Mettre en forme</td>
<td>Uniquement applicable pour la sérialisation JSON. Ligne séparée indique que le résultat sera formaté en demandant à chaque objet JSON séparé par une nouvelle ligne. Tableau d’indique que le résultat sera mis en forme comme un tableau d’objets JSON.</td>
</tr>
</tbody>
</table>

### <a name="renew-data-lake-store-authorization"></a>Renouveler l’autorisation de données Lake Store

Vous devrez s’authentifier à nouveau votre compte données Lake Store si son mot de passe a changé depuis votre travail a été créé ou dernier authentifié.

![Autorisez les données Lake Store](./media/stream-analytics-define-outputs/08-stream-analytics-define-outputs.png)  


## <a name="sql-database"></a>Base de données SQL

[Base de données SQL Azure](https://azure.microsoft.com/services/sql-database/) utilisable comme résultat pour les données relationnelles nature ou pour les applications qui dépendent du contenu hébergé dans une base de données relationnelle. Tâches d’Analytique flux écrit dans une table existante dans une base de données SQL Azure.  Notez que le schéma de table doit correspondre exactement les champs et leurs types de sortie à partir de votre projet. Un [Entrepôt de données SQL Azure](https://azure.microsoft.com/documentation/services/sql-data-warehouse/) peut également être spécifié en tant que résultat via la base de données SQL option de sortie également (il s’agit d’une fonction d’aperçu). Le tableau ci-dessous répertorie les noms des propriétés et leur description pour la création d’un résultat de la base de données SQL.

| Nom de la propriété | Description |
|---------------|-------------|
| Alias de sortie | Il s’agit d’un nom convivial permettant de diriger le résultat de la requête à cette base de données dans des requêtes. |
| Base de données | Le nom de la base de données dans laquelle vous envoyez votre sortie |
| Nom du serveur | Le nom du serveur de base de données SQL |
| Nom d’utilisateur | Le nom d’utilisateur qui a accès à écrire dans la base de données |
| Mot de passe | Le mot de passe pour vous connecter à la base de données |
| Table | Le nom de table dans laquelle le résultat sera écrit. Le nom de table respecte la casse et le schéma de cette table doit correspondre exactement au nombre de champs et leurs types générés par votre sortie des tâches. |

> [AZURE.NOTE] Actuellement une sortie des tâches de flux de données Analytique est prise en charge l’offre de base de données SQL Azure. Toutefois, une Machine virtuelle Azure qui exécute SQL Server avec une base de données pièce jointe n’est pas pris en charge. Cela peut être modifiée dans les versions ultérieures.

## <a name="blob-storage"></a>Stockage d’objets BLOB

Stockage d’objets BLOB offre une solution rentable et format SVG pour le stockage de grandes quantités de données non structurées dans le cloud.  Pour une présentation sur le stockage d’objets Blob Azure et de son utilisation, consultez la documentation à [l’utilisation d’objets BLOB](../storage/storage-dotnet-how-to-use-blobs.md).

Le tableau ci-dessous répertorie les noms des propriétés et leur description pour la création d’une sortie blob.

<table>
<tbody>
<tr>
<td>NOM DE LA PROPRIÉTÉ</td>
<td>DESCRIPTION</td>
</tr>
<tr>
<td>Alias de sortie</td>
<td>Il s’agit d’un nom convivial permettant de diriger le résultat de la requête à ce stockage blob dans les requêtes.</td>
</tr>
<tr>
<td>Compte de stockage</td>
<td>Le nom du compte de stockage où vous envoyez votre sortie.</td>
</tr>
<tr>
<td>Clé de compte de stockage</td>
<td>Le code secret associé au compte de stockage.</td>
</tr>
<tr>
<td>Conteneur de stockage</td>
<td>Les conteneurs fournissent un regroupement logique pour les objets BLOB stockées dans le service Microsoft Azure Blob. Lorsque vous téléchargez un blob au service Blob, vous devez spécifier un conteneur pour ce blob.</td>
</tr>
<tr>
<td>Chemin d’accès du préfixe motif [facultatif]</td>
<td>Le chemin d’accès de fichier utilisé pour écrire vos objets BLOB dans le conteneur spécifié.<BR>Dans le chemin d’accès, vous pouvez choisir d’utiliser une ou plusieurs instances des 2 variables suivantes permettent de spécifier la fréquence à laquelle des objets BLOB sont écrits :<BR>{date}, {time}<BR>Exemple 1 : cluster1/journaux / {date} / {time}<BR>Exemple 2 : cluster1/journaux / {date}</td>
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
<td>Format de sérialisation pour les données de sortie.  JSON, CSV et Avro sont pris en charge.</td>
</tr>
<tr>
<td>Codage</td>
<td>Si le format CSV ou JSON, un codage doit être spécifié. UTF-8 est le format de codage pris en charge uniquement pour le moment.</td>
</tr>
<tr>
<td>Délimiteur</td>
<td>Uniquement applicable pour la sérialisation CSV. Flux Analytique prend en charge un certain nombre de délimiteurs communs pour sérialisation des données CSV. Valeurs prises en charge sont virgule, point-virgule, espace, onglet et barre verticale.</td>
</tr>
<tr>
<td>Mettre en forme</td>
<td>Uniquement applicable pour la sérialisation JSON. Ligne séparée indique que le résultat sera formaté en demandant à chaque objet JSON séparé par une nouvelle ligne. Tableau d’indique que le résultat sera mis en forme comme un tableau d’objets JSON.</td>
</tr>
</tbody>
</table>

## <a name="event-hub"></a>Concentrateur de l’événement

[Événement Hubs](https://azure.microsoft.com/services/event-hubs/) est très scalable publication abonnement ingestor événement. Il recueille des millions d’événements par seconde.  Utiliser un concentrateur de l’événement en tant que sortie est lorsque le résultat d’une tâche de flux de données Analytique sera l’entrée d’une autre tâche diffusion en continu.

Il existe quelques paramètres qui sont nécessaires pour configurer des flux de données concentrateur événement comme résultat.

| Nom de la propriété | Description |
|---------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Alias de sortie | Il s’agit d’un nom convivial permettant de diriger le résultat de la requête à ce concentrateur événement dans les requêtes. |
| Service Bus Namespace | Un espace de noms Bus des services est un conteneur pour un ensemble d’entités de messagerie. Lorsque vous avez créé un nouveau concentrateur d’événement, vous avez également créé un espace de noms Bus des services |
| Concentrateur de l’événement | Le nom de votre événement concentrateur de sortie |
| Nom de la stratégie concentrateur événement | La stratégie d’accès partagé, qui peut être créée sous l’onglet événement concentrateur configurer. Chaque stratégie d’accès partagé aura un nom, les autorisations que vous définissez, et les touches d’accès |
| Clé de stratégie concentrateur événement | La touche d’accès partagé utilisée pour authentifier l’accès à l’espace de noms Bus des services |
| Colonne de clé partition [facultatif] | Cette colonne contienne la clé de partition pour la sortie concentrateur de l’événement. |
| Format de sérialisation événement | Format de sérialisation pour les données de sortie.  JSON, CSV et Avro sont pris en charge. |
| Codage | Pour CSV et JSON, UTF-8 est le format de codage pris en charge uniquement pour le moment |
| Délimiteur | Uniquement applicable pour la sérialisation CSV. Flux Analytique prend en charge un certain nombre de délimiteurs communs pour sérialisation des données au format CSV. Valeurs prises en charge sont virgule, point-virgule, espace, onglet et barre verticale. |
| Mettre en forme | Applicable uniquement pour le type JSON. Ligne séparée indique que le résultat sera formaté en demandant à chaque objet JSON séparé par une nouvelle ligne. Tableau d’indique que le résultat sera mis en forme comme un tableau d’objets JSON. |

## <a name="power-bi"></a>Power BI

[Power BI](https://powerbi.microsoft.com/) peut servir comme résultat d’une tâche de flux de données Analytique à fournir pour une expérience de visualisation riche des résultats de l’analyse. Cette fonctionnalité peut être utilisée pour les tableaux de bord opérationnels, génération de rapports et métrique par l’effort de création de rapports.

### <a name="authorize-a-power-bi-account"></a>Autoriser un compte Power BI

1.  Lorsque Power BI est sélectionné comme résultat dans le portail de gestion Azure, vous devrez pour autoriser un utilisateur BI Power existant ou pour créer un nouveau compte de Power BI.  

    ![Autorisez les Power BI utilisateur](./media/stream-analytics-define-outputs/01-stream-analytics-define-outputs.png)  

2.  Créer un nouveau compte si vous ne pas encore avoir un, puis cliquez sur Autoriser.  Un écran semblable à celui-ci s’affiche.  

    ![Compte Azure Power BI](./media/stream-analytics-define-outputs/02-stream-analytics-define-outputs.png)  

3.  Dans cette étape, indiquez le compte professionnel ou scolaire pour autoriser la sortie de Power BI. Si vous n'êtes pas déjà inscrit à Power BI, sélectionnez maintenant se vers le haut. Le compte professionnel ou scolaire que vous utilisez pour Power BI peut être différent du compte d’abonnement Azure auquel vous êtes connecté à l’aide.

### <a name="configure-the-power-bi-output-properties"></a>Configurer les propriétés de sortie Power BI

Une fois que le compte de Power BI authentifié, vous pouvez configurer les propriétés pour les impressions de Power BI. Le tableau ci-dessous est la liste des noms de propriété et leur description pour configurer votre sortie Power BI.

| Nom de la propriété | Description |
|---------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Alias de sortie | Il s’agit d’un nom convivial permettant de diriger le résultat de la requête à cette sortie PowerBI dans les requêtes. |
| Espace de travail de groupe | Pour activer le partage de données avec d’autres utilisateurs de Power BI, vous pouvez sélectionner des groupes à l’intérieur de votre compte de Power BI ou choisissez « Mon espace de travail » si vous ne souhaitez pas écrire à un groupe.  Mise à jour d’un groupe existant nécessite de renouvellement de l’authentification de Power BI. | 
| Nom du jeu de données | Indiquez un nom de jeu de données dont il est nécessaire pour la sortie de Power BI à utiliser |
| Nom de la table | Attribuez un nom de tableau sous le jeu de données de la sortie de Power BI. Pour l’instant, sortie de Power BI des tâches de flux de données Analytique peut avoir uniquement une table dans un jeu de données |

Pour une vue d’ensemble de la configuration d’un tableau de bord et de sortie de Power BI, voir l’article [Azure flux Analytique et Power BI](stream-analytics-power-bi-dashboard.md) .

> [AZURE.NOTE] Ne créez pas explicitement le jeu de données et la table dans le tableau de bord Power BI. Le jeu de données et la table seront automatiquement remplies lorsque le travail est démarré et que la tâche démarre sortie pompage dans Power BI. Notez que si la requête de travail ne génère pas tous les résultats, le jeu de données et la table n’est créées. Sachez également que si Power BI a déjà un jeu de données et une table portant le même nom que celle décrite dans cette tâche de flux de données Analytique, les données existantes seront remplacées.

### <a name="renew-power-bi-authorization"></a>Renouveler l’autorisation de Power BI

Vous devrez s’authentifier à nouveau votre compte Power BI si son mot de passe a changé depuis votre travail a été créé ou dernier authentifié. Si l’authentification multifacteur (MFA) est configurée sur votre client Azure Active Directory (DAS), vous devez également renouveler autorisation Power BI toutes les deux semaines. Un problème de ce problème est aucune sortie des tâches et une « erreur d’utilisateur d’authentifier » dans les journaux d’opération :

  ![Erreur de Power BI actualisation jeton](./media/stream-analytics-define-outputs/03-stream-analytics-define-outputs.png)  

Pour résoudre ce problème, arrêter votre travail en cours d’exécution et accédez à votre sortie Power BI.  Cliquez sur le lien « Renouvellement d’autorisation », puis redémarrez votre travail à partir de la dernière fois arrêté pour éviter toute perte de données.

  ![Power BI renouveler d’autorisation](./media/stream-analytics-define-outputs/04-stream-analytics-define-outputs.png)  

## <a name="table-storage"></a>Stockage de table

[Stockage de Table Azure](../storage/storage-introduction.md) offre un stockage hautement disponible et considérablement scalable, afin qu’une application peut s’adapter automatiquement pour répondre à la demande de l’utilisateur. Stockage de table est banque de clé/attribut de Microsoft NoSQL celui peut exploiter pour les données structurées avec moins les contraintes sur le schéma. Stockage de Table Azure peut être utilisé pour stocker les données de persistance et d’extraction efficace.

Le tableau ci-dessous répertorie les noms des propriétés et leur description pour la création d’un résultat de la table.

| Nom de la propriété | Description |
|---------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Alias de sortie | Il s’agit d’un nom convivial permettant de diriger le résultat de la requête à ce stockage de table dans les requêtes. |
| Compte de stockage | Le nom du compte de stockage où vous envoyez votre sortie. |
| Clé de compte de stockage | Touche d’accès associée au compte de stockage. |
| Nom de la table | Le nom de la table. La table sera créée s’il n’existe pas. |
| Clé de partition | Le nom de la colonne de sortie contenant la clé de partition. La clé de partition est un identificateur unique pour la partition d’une table donnée qui constitue la première partie de la clé primaire d’une entité. Il est une valeur de chaîne qui peut être jusqu'à 1 Ko. |
| Clé de ligne | Le nom de la colonne de sortie contenant la clé de ligne. La clé de ligne est un identificateur unique pour une entité au sein d’une partition donnée. Il constitue la deuxième partie de la clé primaire d’une entité. La clé de ligne est une valeur de chaîne qui peut être jusqu'à 1 Ko. |
| Taille du lot | Le nombre d’enregistrements pour une opération par lots. Généralement, la valeur par défaut est suffisant pour la plupart des tâches, reportez-vous à la [spécification de Table par lots](https://msdn.microsoft.com/library/microsoft.windowsazure.storage.table.tablebatchoperation.aspx) pour plus d’informations sur la modification de ce paramètre. |

## <a name="service-bus-queues"></a>Service Bus files d’attente

[Files d’attente de service Bus](https://msdn.microsoft.com/library/azure/hh367516.aspx) offrent une première dans, remise des messages à un ou plusieurs consommateurs concurrents premier arrière (FIFO). En règle générale, les messages doivent être reçus et traités par les destinataires dans l’ordre temporel dans lequel ils ont été ajoutés à la file d’attente, et chaque message est reçue et traitée par consommateur qu’un seul message.

Le tableau ci-dessous répertorie les noms des propriétés et leur description pour la création d’une sortie file d’attente.

| Nom de la propriété | Description |
|----------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Alias de sortie | Il s’agit d’un nom convivial permettant de diriger le résultat de la requête à cette file d’attente de Bus de Service dans les requêtes. |
| Service Bus Namespace | Un espace de noms Bus des services est un conteneur pour un ensemble d’entités de messagerie. |
| Nom de la file d’attente | Le nom de la file d’attente de Bus de Service. |
| Nom de la stratégie file d’attente | Lorsque vous créez une file d’attente, vous pouvez également créer les stratégies d’accès partagé sous l’onglet Configurer file d’attente. Chaque stratégie d’accès partagé sera ont un nom, les autorisations que vous définissez, et les touches d’accès. |
| Clé de stratégie file d’attente | La touche d’accès partagé utilisée pour authentifier l’accès à l’espace de noms Bus des services |
| Format de sérialisation événement | Format de sérialisation pour les données de sortie.  JSON, CSV et Avro sont pris en charge. |
| Codage | Pour CSV et JSON, UTF-8 est le format de codage pris en charge uniquement pour le moment |
| Délimiteur | Uniquement applicable pour la sérialisation CSV. Flux Analytique prend en charge un certain nombre de délimiteurs communs pour sérialisation des données au format CSV. Valeurs prises en charge sont virgule, point-virgule, espace, onglet et barre verticale. |
| Mettre en forme | Applicable uniquement pour le type JSON. Ligne séparée indique que le résultat sera formaté en demandant à chaque objet JSON séparé par une nouvelle ligne. Tableau d’indique que le résultat sera mis en forme comme un tableau d’objets JSON. |

## <a name="service-bus-topics"></a>Service Bus rubriques

Tandis que files d’attente de Service Bus offrent un moyen de communication à celui de l’expéditeur au destinataire, [Service Bus rubriques](https://msdn.microsoft.com/library/azure/hh367516.aspx) fournissent un formulaire un-à-plusieurs de communication.

Le tableau ci-dessous répertorie les noms des propriétés et leur description pour la création d’un résultat de la table.

| Nom de la propriété | Description |
|----------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Alias de sortie | Il s’agit d’un nom convivial permettant de diriger le résultat de la requête à cette rubrique Bus de Service dans les requêtes. |
| Service Bus Namespace | Un espace de noms Bus des services est un conteneur pour un ensemble d’entités de messagerie. Lorsque vous avez créé un nouveau concentrateur d’événement, vous avez également créé un espace de noms Bus des services |
| Nom de la rubrique | Rubriques sont messagerie entités, similaires aux files d’attente et hubs événement. Ils sont conçus pour collecter les flux d’événements à partir d’un nombre d’appareils différents et services. Lorsqu’une rubrique est créée, il est également remplacé par un nom spécifique. Les messages envoyés à un sujet ne sera pas disponibles sauf si un abonnement est créée vous assurer qu’un ou plusieurs abonnements dans la rubrique |
| Nom de la stratégie rubrique | Lorsque vous créez une rubrique, vous pouvez également créer des stratégies d’accès partagé sous l’onglet Configuration de la rubrique. Chaque stratégie d’accès partagé aura un nom, les autorisations que vous définissez, et les touches d’accès |
| Rubrique stratégie clé | La touche d’accès partagé utilisée pour authentifier l’accès à l’espace de noms Bus des services |
| Format de sérialisation événement | Format de sérialisation pour les données de sortie.  JSON, CSV et Avro sont pris en charge. |
| Codage | Si le format CSV ou JSON, un codage doit être spécifié. UTF-8 est le format de codage pris en charge uniquement pour le moment |
| Délimiteur | Uniquement applicable pour la sérialisation CSV. Flux Analytique prend en charge un certain nombre de délimiteurs communs pour sérialisation des données au format CSV. Valeurs prises en charge sont virgule, point-virgule, espace, onglet et barre verticale. |

## <a name="documentdb"></a>DocumentDB

[Azure DocumentDB](https://azure.microsoft.com/services/documentdb/) est un entièrement gérés NoSQL document de base de données service qui propose des requêtes et des transactions sur exempt de schéma de données, des performances prévisibles et fiables et développer rapidement.

Le tableau ci-dessous répertorie les noms des propriétés et leur description pour la création d’une sortie DocumentDB.

<table>
<tbody>
<tr>
<td>NOM DE LA PROPRIÉTÉ</td>
<td>DESCRIPTION</td>
</tr>
<tr>
<td>Nom du compte</td>
<td>Le nom du compte DocumentDB.  Cela peut également être le point de terminaison du compte.</td>
</tr>
<tr>
<td>Clé de compte</td>
<td>Touche d’accès partagé pour le compte DocumentDB.</td>
</tr>
<tr>
<td>Base de données</td>
<td>Le nom de base de données DocumentDB.</td>
</tr>
<tr>
<td>Modèle de nom de collection de sites</td>
<td>Le modèle de nom de collection de sites pour les collections à utiliser. Format du nom de la collection de sites peut être créé à l’aide du jeton facultatif {partition}, où partitions démarrer à partir de 0.<BR>Par exemple Types suivants sont des entrées valides :<BR>MyCollection {partition}<BR>MyCollection<BR>Notez que collections doivent exister avant que la tâche de flux de données Analytique est démarrée et n’est pas créée automatiquement.</td>
</tr>
<tr>
<td>Clé de partition</td>
<td>Le nom du champ dans les événements de sortie permet de spécifier la clé de division sortie provenant de plusieurs collections.</td>
</tr>
<tr>
<td>ID de document</td>
<td>Le nom du champ dans les événements de sortie permet de spécifier la clé primaire de laquelle insérer ou mettre à jour les opérations sont basés sur.</td>
</tr>
</tbody>
</table>


## <a name="get-help"></a>Obtenir de l’aide
Pour obtenir une aide supplémentaire, essayez de notre [forum Azure flux Analytique](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>Étapes suivantes
Vous avez été abordé flux Analytique, un service de diffusion en continu analytique sur des données à partir de l’Internet des objets géré. Pour en savoir plus sur ce service, voir :

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

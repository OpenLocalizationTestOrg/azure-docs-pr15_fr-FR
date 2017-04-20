<properties
    pageTitle="Stockage Analytique permet de collecter les données les journaux et les mesures | Microsoft Azure"
    description="Stockage Analytique vous permet de pour effectuer le suivi des données de métrique pour tous les services de stockage et collecter les journaux de stockage Blob, file d’attente et Table."
    services="storage"
    documentationCenter=""
    authors="robinsh"
    manager="carmonm"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="08/03/2016"
    ms.author="robinsh"/>

# <a name="storage-analytics"></a>Stockage Analytique

## <a name="overview"></a>Vue d’ensemble

Azure Analytique de stockage dans un journal et fournit des données de mesures pour un compte de stockage. Vous pouvez utiliser ces données à tracer les demandes, analyser les tendances d’utilisation et diagnostiquer les problèmes avec votre compte de stockage.

Pour utiliser le stockage Analytique, vous devez l’activer individuellement pour chaque service que vous souhaitez analyser. Vous pouvez l’activer à partir du [Portail Azure](https://portal.azure.com). Pour plus d’informations, voir [moniteur un compte de stockage dans le portail Azure](storage-monitor-storage-account.md). Vous pouvez également activer Analytique de stockage par programme via l’API REST ou la bibliothèque de client. Utiliser les opérations [Obtenir les propriétés d’objets Blob Service](https://msdn.microsoft.com/library/hh452239.aspx), [Obtenir les propriétés de Service file d’attente](https://msdn.microsoft.com/library/hh452243.aspx), [Obtenir les propriétés de Table Service](https://msdn.microsoft.com/library/hh452238.aspx)et [Obtenir les propriétés de fichier Service](https://msdn.microsoft.com/library/mt427369.aspx) pour activer le stockage Analytique pour chaque service.

Les données agrégées sont stockées dans un blob connu (pour la journalisation) et dans les tableaux connus (pour les indicateurs), qui peuvent être accessible à l’aide du service Blob et API de service de tableau.

Stockage Analytique a une limite de 20 To sur la quantité de données stockées sont indépendantes de la limite de votre compte de stockage total. Pour plus d’informations sur la facturation et les stratégies de rétention des données, voir [Analytique de stockage et de facturation](https://msdn.microsoft.com/library/hh360997.aspx). Pour plus d’informations sur les limites de compte de stockage, voir [extensibilité du stockage Azure et des objectifs de Performance](storage-scalability-targets.md).

Pour un guide détaillé sur l’utilisation de stockage Analytique et autres outils pour identifier, diagnostiquer et résoudre les problèmes liés au stockage Azure, voir [moniteur, diagnostiquer et résoudre les problèmes de stockage de Microsoft Azure](storage-monitoring-diagnosing-troubleshooting.md).


## <a name="about-storage-analytics-logging"></a>À propos de la journalisation de stockage Analytique

Stockage Analytique enregistre des informations détaillées sur les requêtes réussies et non dans un service de stockage. Ces informations peuvent servir à surveiller les requêtes individuelles et de diagnostiquer les problèmes liés à un service de stockage. Demandes sont enregistrés sur une base de mieux.

Entrées du journal sont créées uniquement s’il existe des activités de service de stockage. Par exemple, si un compte de stockage comporte des activités dans son service Blob mais pas dans ses services tableau ou file d’attente, seuls les journaux relatifs au service Blob est créés.

Journalisation de stockage Analytique n’est pas disponible pour le Service de fichiers Azure.

### <a name="logging-authenticated-requests"></a>Demandes de journalisation authentifiée

Les types de requêtes authentifiés suivants sont enregistrés :

- Demandes réussies.

- Échec de requêtes, y compris le délai d’attente, la limitation, réseau, l’autorisation et autres erreurs.

- Requêtes qui utilisent un partagés accès Signature (sa), y compris les échecs et réussites requêtes.

- Demandes de données analytique.

Demandes effectuées par Analytique de stockage lui-même, telles que la création de journal ou la suppression, ne sont pas enregistrées. Une liste complète des données journalisées présentée dans les rubriques [stockage Analytique connecté opérations et les Messages d’état](https://msdn.microsoft.com/library/hh343260.aspx) et [Mettre en forme de stockage Analytique journal](https://msdn.microsoft.com/library/hh343259.aspx) .

### <a name="logging-anonymous-requests"></a>Enregistrement des demandes anonymes
Les types de requêtes anonymes suivants sont enregistrés :

- Demandes réussies.

- Erreurs du serveur.

- Erreurs de délai d’expiration pour client et serveur.

- Échecs requêtes GET avec code d’erreur 304 (non modifié).

Toutes les autres requêtes anonymes échecs n’êtes pas connectés. Une liste complète des données journalisées présentée dans les rubriques [stockage Analytique connecté opérations et les Messages d’état](https://msdn.microsoft.com/library/hh343260.aspx) et [Mettre en forme de stockage Analytique journal](https://msdn.microsoft.com/library/hh343259.aspx) .

### <a name="how-logs-are-stored"></a>Mode de stockage des journaux
Tous les journaux sont stockés dans des objets BLOB bloc dans un conteneur nommé $logs, qui est créé automatiquement lorsque le stockage Analytique est activé pour un compte de stockage. Le conteneur $logs se trouve dans l’espace de noms d’objets blob du compte de stockage, par exemple : `http://<accountname>.blob.core.windows.net/$logs`. Ce conteneur ne peut pas être supprimé dès que stockage Analytique a été activé, bien que son contenu peut être supprimé.

>[Azure.NOTE] Le conteneur $logs n’est pas affiché lorsqu’un conteneur répertoriant opération est effectué, telles que la méthode [ListContainers](https://msdn.microsoft.com/library/azure/dd179352.aspx) . Vous devez y accéder directement. Par exemple, vous pouvez utiliser la méthode [ListBlobs](https://msdn.microsoft.com/library/azure/dd135734.aspx) pour accéder aux objets BLOB dans la `$logs` conteneur.
Comme les demandes sont enregistrées, stockage Analytique sont-elles immédiatement les résultats intermédiaires en tant que blocs. Stockage Analytique sera régulièrement, valider ces blocs et les rendre disponibles comme un objet blob.

Enregistrements en double existent pour les journaux créés dans la même heure. Vous pouvez déterminer si un enregistrement est un doublon en vérifiant **l’ID de demande** et **opération** le numéro.

### <a name="log-naming-conventions"></a>Conventions d’appellation de journal
Chaque journal sera écrit sous la forme suivante.

    <service-name>/YYYY/MM/DD/hhmm/<counter>.log

Le tableau suivant décrit chaque attribut dans le nom du journal.

| Attribut         | Description                                                                                                                                                                                   |
|----------------   |--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------   |
| < nom du service >    | Le nom du service de stockage. Par exemple : blob, un tableau ou file d’attente.                                                                                                                          |
| AAAA              | L’année sur quatre chiffres pour le journal. Par exemple : 2011.                                                                                                                                           |
| MM                | Mois à deux chiffres pour le journal. Par exemple : 07.                                                                                                                                             |
| JJ                | Mois à deux chiffres pour le journal. Par exemple : 07.                                                                                                                                             |
| hh                | L’heure à deux chiffres qui indique l’heure de début pour les journaux, au format UTC 24 heures. Par exemple : 18.                                                                                     |
| mm                | Le numéro à deux chiffres qui indique les minutes de départ pour que les journaux. Cette valeur est non prises en charge dans la version actuelle du stockage Analytique, et sa valeur sera toujours 00.     |
| <counter>         | Compteur de base zéro avec six chiffres qui indique le nombre d’objets BLOB journal généré pour le service de stockage en une heure laps de temps. Ce compteur démarre 000000. Par exemple : 000001.     |

Voici un exemple complet de nom de journal qui combine les exemples précédents.

    blob/2011/07/31/1800/000001.log

Voici un exemple d’URI qui peut être utilisé pour accéder au journal précédent.

    https://<accountname>.blob.core.windows.net/$logs/blob/2011/07/31/1800/000001.log

Lorsqu’une demande de stockage est consignée, le nom du journal qui en résulte correspond à l’heure de fin de l’opération demandée. Par exemple, si une demande de GetBlob ont été achevée à 6 h 30 sur le 31/7/2011, le journal serait écrite par le préfixe suivant :`blob/2011/07/31/1800/`

### <a name="log-metadata"></a>Journal des métadonnées
Tous les objets BLOB journal sont stockés avec les métadonnées qui peuvent être utilisée pour identifier les données de journalisation contient le blob. Le tableau suivant décrit chaque attribut de métadonnées.

| Attribut     | Description                                                                                                                                                                                                                                                   |
|------------   |-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------    |
| LogType       | Décrit si le journal contient des informations relatives à la lecture, écriture ou suppression d’opérations. Cette valeur peut inclure un type ou une combinaison des trois, séparées par des virgules. Exemple 1 : écriture ; Exemple 2 : lire, écrire ; Exemple 3 : lecture, écriture, suppression.    |
| Heure de début     | La première heure d’une entrée dans le journal, dans le formulaire AAAA-MM-JJThh. Par exemple : 2011-07-31T18:21:46Z.                                                                                                                                             |
| L’heure de fin       | La dernière fois d’une entrée dans le journal, dans le formulaire AAAA-MM-JJThh. Par exemple : 2011-07-31T18:22:09Z.                                                                                                                                               |
| LogVersion    | La version du format de journal. Actuellement la seule valeur pris en charge est 1.0.                                                                                                                                                                                     |

La liste suivante affiche les métadonnées d’exemple complet à l’aide des exemples précédents.

- LogType = écriture

- Heure de début = 2011-07-31T18:21:46Z

- L’heure de fin = 2011-07-31T18:22:09Z

- LogVersion = 1.0

### <a name="accessing-logging-data"></a>Accès aux données de journalisation

Toutes les données de la `$logs` conteneur est accessible à l’aide de l’API de service Blob, y compris l’API .NET fournies par la Azure bibliothèque managée. L’administrateur de compte de stockage peut lire et supprimer les fichiers journaux, mais ne peut pas créer ou mettre à jour. Nom et que les métadonnées de journal peuvent servir lors de la recherche d’un journal. Il est possible pour les fichiers journaux d’une heure précise à apparaître désordre, mais les métadonnées toujours spécifiant la période d’entrées de journal dans un journal. Par conséquent, vous pouvez utiliser une combinaison de noms de journal et les métadonnées lors de la recherche d’un journal spécifique.

## <a name="about-storage-analytics-metrics"></a>Sur métriques de stockage Analytique

Stockage Analytique peut stocker des indicateurs qui incluent des données statistiques et à la capacité transaction agrégé sur les requêtes à un service de stockage. Transactions sont les deux du niveau API opération et au niveau du service de stockage capacité est indiquée et au niveau du service de stockage. Données métriques peuvent être utilisées pour analyser l’utilisation du service stockage, diagnostiquer les problèmes avec les demandes effectuées par rapport au service de stockage et d’améliorer les performances des applications qui utilisent un service.

Pour utiliser le stockage Analytique, vous devez l’activer individuellement pour chaque service que vous souhaitez analyser. Vous pouvez l’activer à partir du [Portail Azure](https://portal.azure.com). Pour plus d’informations, voir [moniteur un compte de stockage dans le portail Azure](storage-monitor-storage-account.md). Vous pouvez également activer Analytique de stockage par programme via l’API REST ou la bibliothèque de client. Utiliser les opérations **d’Obtenir les propriétés de Service** pour activer le stockage Analytique pour chaque service.

### <a name="transaction-metrics"></a>Métriques de transactions

Un ensemble de données robuste est enregistré à intervalles toutes les heures ou minutes pour chaque service de stockage et a demandé API opération, y compris pénétration/sortie, disponibilité, erreurs et associés à la demande des pourcentages. Vous pouvez obtenir une liste complète des détails de transactions dans la rubrique [Schéma de la Table Analytique métriques de stockage](https://msdn.microsoft.com/library/hh343264.aspx) .

Données de transaction sont enregistrées à deux niveaux : le niveau de service et le niveau d’opération API. Au niveau du service, statistiques récapitule tous les demandé opérations API sont écrites dans une table entité toutes les heures même si aucune demande ont été apportées au service. Au niveau des opérations API, statistiques sont écrites uniquement à une entité si l’opération a été demandée au sein de l’heure qu’elle représente.

Par exemple, si vous effectuez une opération **GetBlob** votre service d’objets Blob, des indicateurs de stockage Analytique enregistrer la requête et inclure dans les données agrégées pour le service Blob ainsi que l’opération **GetBlob** . Toutefois, si aucune opération **GetBlob** n’est demandée pendant l’heure, une entité ne pas être écrite dans le `$MetricsTransactionsBlob` pour cette opération.

Métriques de transactions sont enregistrés pour les demandes de l’utilisateur et les demandes effectuées par Analytique stockage lui-même. Par exemple, demandes en stockage Analytique pour écrire les journaux et les entités de table sont enregistrées. Pour plus d’informations sur la façon dont ces demandes sont facturés, voir [Analytique de stockage et de facturation](https://msdn.microsoft.com/library/hh360997.aspx).

### <a name="capacity-metrics"></a>Mesures de capacité

>[AZURE.NOTE] Pour l’instant, les mesures de capacité ne sont disponibles pour le service d’objets Blob. Capacité mesures pour les services de tableau et file d’attente sera disponibles dans les versions ultérieures de stockage Analytique.

Données capacitées sont enregistrées de tous les jours pour Blob service d’un compte stockage et deux entités de table sont écrits. Une seule entité fournit des statistiques pour les données utilisateur et l’autre fournit des statistiques sur les `$logs` conteneur blob utilisé par stockage Analytique. La `$MetricsCapacityBlob` table inclut les statistiques suivantes :

- **Capacité**: la quantité de stockage utilisé par Blob service du compte stockage, en octets.

- **ContainerCount**: le nombre de conteneurs blob dans le service d’objets Blob de compte de stockage.

- **ObjectCount**: le nombre de validée et non validées BLOB bloc ou une page dans le service d’objets Blob de compte de stockage.

Pour plus d’informations sur les mesures de capacité, voir [Schéma de la Table Analytique métriques de stockage](https://msdn.microsoft.com/library/hh343264.aspx).

### <a name="how-metrics-are-stored"></a>Mode de stockage des indicateurs

Toutes les données de mesures pour chacun des services de stockage sont stockées dans trois tables réservés pour ce service : une table pour les informations sur les transactions, une table pour les informations sur les transactions minute et une autre table pour les informations sur la capacité. Informations sur les transactions transaction et minute est composée des données de demande et réponse et informations sur les capacités se composent de stockage des données d’utilisation. Indicateurs d’heure, minutes mesures et capacité de Blob service d’un compte stockage sont accessibles dans les tables qui sont nommés comme décrit dans le tableau suivant.

| Niveau de mesures                         | Noms des tables                                                                                                                   | Versions prises en charge                                                                                                                        |
|------------------------------------   |-----------------------------------------------------------------------------------------------------------------------------  |---------------------------------------------------------------------------------------------------------------------------------------------- |
| Mesures de toutes les heures, emplacement principal      |  $MetricsTransactionsBlob <br/>$MetricsTransactionsTable <br/> $MetricsTransactionsQueue                                                  | Versions antérieures à 2013-08-15 uniquement. Ces noms sont toujours pris en charge, il est recommandé que vous passez à l’aide des tables présentées ci-après.  |
| Mesures de toutes les heures, emplacement principal      | $MetricsHourPrimaryTransactionsBlob <br/>$MetricsHourPrimaryTransactionsTable <br/>$MetricsHourPrimaryTransactionsQueue               | Toutes les versions, y compris 2013-08-15.                                                                                                               |
| Métrique minute, emplacement principal      | $MetricsMinutePrimaryTransactionsBlob <br/>$MetricsMinutePrimaryTransactionsTable <br/>$MetricsMinutePrimaryTransactionsQueue         | Toutes les versions, y compris 2013-08-15.                                                                                                               |
| Mesures de toutes les heures, emplacement secondaire    | $MetricsHourSecondaryTransactionsBlob <br/>$MetricsHourSecondaryTransactionsTable <br/>$MetricsHourSecondaryTransactionsQueue         | Toutes les versions, y compris 2013-08-15. Réplication geo redondants accès en lecture doit être activée.                                                    |
| Métrique minute, emplacement secondaire    | $MetricsMinuteSecondaryTransactionsBlob <br/>$MetricsMinuteSecondaryTransactionsTable <br/>$MetricsMinuteSecondaryTransactionsQueue   | Toutes les versions, y compris 2013-08-15. Réplication geo redondants accès en lecture doit être activée.                                                    |
| Capacité (service Blob uniquement)          | $MetricsCapacityBlob                                                                                                          | Toutes les versions, y compris 2013-08-15.                                                                                                               |


Ces tables sont automatiquement créées lorsque le stockage Analytique est activé pour un compte de stockage. Ils sont accessibles via l’espace de noms du compte de stockage, par exemple :`https://<accountname>.table.core.windows.net/Tables("$MetricsTransactionsBlob")`

### <a name="accessing-metrics-data"></a>Accès aux données de mesures

Toutes les données dans les tables de mesures sont accessibles à l’aide de l’API de service de tableau, y compris l’API .NET fournies par la Azure géré bibliothèque. L’administrateur de compte de stockage peut lire et supprimer des entités de table, mais ne peut pas créer ou les mettre à jour.

## <a name="billing-for-storage-analytics"></a>Facturation pour stockage Analytique

Stockage Analytique est activée par un propriétaire du compte de stockage ; Il n’est pas activée par défaut. Toutes les données de métrique est rédigé par les services d’un compte de stockage. Par conséquent, chaque opération d’écriture effectuée par stockage Analytique est facturable. En outre, la quantité de stockage utilisé par les données métriques est également facturable.

Les actions suivantes effectuées par stockage Analytique sont facturables :

- Demandes de création d’objets BLOB pour l’enregistrement. 

- Demandes de création d’entités de table pour les mesures.

Si vous avez configuré une stratégie de rétention des données, vous ne sont pas facturés pour supprimer des transactions lorsque le stockage Analytique supprime des données de journalisation et d’indicateurs anciennes. Toutefois, les transactions de suppression d’un client sont facturables. Pour plus d’informations sur les stratégies de rétention, voir [définir une stratégie de rétention des données de stockage Analytique](https://msdn.microsoft.com/library/azure/hh343263.aspx).

### <a name="understanding-billable-requests"></a>Présentation des requêtes facturables

Toutes les requêtes adressées au service de stockage d’un compte sont facturables ou non facturables. Stockage Analytique enregistre chaque demande individuelle apportée à un service, notamment un message d’état qui indique comment la demande a été traitée. De même, stockage Analytique stocke les mesures pour un service et les opérations de l’API de ce service, y compris les pourcentages et le nombre de certains messages d’état. Ensemble, ces fonctionnalités peuvent vous aider à analyser vos demandes facturables, apporter des améliorations dans votre application et diagnostiquer les problèmes avec les demandes à vos services. Pour plus d’informations sur la facturation, voir [Présentation Azure stockage facturation - la bande passante, Transactions et à la capacité](http://blogs.msdn.com/b/windowsazurestorage/archive/2010/07/09/understanding-windows-azure-storage-billing-bandwidth-transactions-and-capacity.aspx).

Lorsque vous examinez les données de stockage Analytique, vous pouvez utiliser les tables dans la rubrique [stockage Analytique connecté opérations et les Messages d’état](https://msdn.microsoft.com/library/azure/hh343260.aspx) pour déterminer quelles demandes sont facturables. Vous pouvez ensuite comparer vos journaux et des données de mesures pour les messages d’état pour voir si vous facturées une demande particulière. Vous pouvez également utiliser les tables dans la rubrique précédente pour étudier la disponibilité d’un service de stockage ou opération API individuelle.

## <a name="next-steps"></a>Étapes suivantes

### <a name="setting-up-storage-analytics"></a>La configuration de stockage Analytique
- [Analyser un compte de stockage dans le portail Azure](storage-monitor-storage-account.md)
- [Activation et configuration de stockage Analytique](https://msdn.microsoft.com/library/hh360996.aspx)

### <a name="storage-analytics-logging"></a>Journalisation Analytique de stockage  
- [Informations sur le stockage Analytique journal](https://msdn.microsoft.com/library/hh343262.aspx)
- [Format de stockage du journal Analytique](https://msdn.microsoft.com/library/hh343259.aspx)
- [Stockage Analytique connecté opérations et les Messages d’état](https://msdn.microsoft.com/library/hh343260.aspx)

### <a name="storage-analytics-metrics"></a>Indicateurs d’Analytique du stockage
- [À propos des indicateurs de stockage Analytique](https://msdn.microsoft.com/library/hh343258.aspx)
- [Schéma de la Table Analytique métriques de stockage](https://msdn.microsoft.com/library/hh343264.aspx)
- [Stockage Analytique connecté opérations et les Messages d’état](https://msdn.microsoft.com/library/hh343260.aspx)  

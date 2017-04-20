<properties
    pageTitle="Mettre à l’échelle des travaux de flux Analytique pour augmenter le débit | Microsoft Azure"
    description="Apprenez à l’échelle des tâches de flux de données Analytique en configuration des partitions d’entrée, adapter la définition de la requête et la diffusion en continu les unités de travail."
    keywords="données, la diffusion en continu de traitement des données, optimiser les analytique"
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

# <a name="scale-azure-stream-analytics-jobs-to-increase-stream-data-processing-throughput"></a>Mettre à l’échelle travaux Azure flux Analytique pour augmenter le débit de traitement des données de flux de données

Découvrez comment optimiser les travaux analytique et calculer la *diffusion en continu unités* pour flux Analytique, comment mettre à l’échelle tâches de flux de données Analytique en configuration des partitions d’entrée, adapter la définition de la requête analytique et en définissant des unités de diffusion en continu de travail. 

## <a name="what-are-the-parts-of-a-stream-analytics-job"></a>Quels sont les composants d’une tâche de flux de données Analytique ?
Une définition de tâche de flux de données Analytique inclut des entrées, une requête et sortie. Les entrées sont à partir de l’endroit où le travail lit le flux de données, la requête permet de transformer le flux d’entrée de données et le résultat est l’endroit où le travail envoie des résultats de la tâche.  

Une tâche exige au moins une source d’entrée de flux de données. La source d’entrée de flux de données peut être stockée dans un événement Azure Service concentrateur ou dans le stockage Blob Azure. Pour plus d’informations, voir [Présentation des Azure flux Analytique](stream-analytics-introduction.md) et [commencer à utiliser Azure flux Analytique](stream-analytics-get-started.md).

## <a name="configuring-streaming-units"></a>La configuration des unités de diffusion en continu
Unités de diffusion en continu (SUs) représente les ressources et ressources informatiques requises pour exécuter une tâche Azure flux Analytique. SUs offrent un moyen pour décrire l’événement relative basée sur une mesure combinée du processeur, mémoire, la capacité de traitement et lire et écrire des taux. Chaque unité de diffusion en continu correspond à environ 1 Mo/seconde de débit. 

En choisissant SUs combien sont requises pour une tâche particulière dépend de la configuration des partitions pour les entrées et la requête définie pour la tâche. Vous pouvez sélectionner jusqu'à votre quota en continu intensité d’utilisation pour une tâche à l’aide du portail classique Azure. Chaque abonnement Azure par défaut a un quota de jusqu'à 50 unités de diffusion en continu de tous les projets analytique d’une région spécifique. Pour augmenter la diffusion en continu intensité d’utilisation pour vos abonnements, contactez le [Support technique Microsoft](http://support.microsoft.com).

Le nombre d’unités de diffusion en continu un travail pouvant utiliser dépend de la configuration des partitions pour les entrées et la requête définie pour la tâche. Notez également qu’une valeur valide pour les unités de flux de données doit être utilisée. Les valeurs valides commencent à 1, 3, 6, puis vers le haut par incréments de 6, comme illustré ci-dessous.

![Flux de données Azure Analytique flux unités échelle][img.stream.analytics.streaming.units.scale]

Cet article vous explique comment calculer et ajuster la requête pour augmenter le débit pour les travaux analytique.

## <a name="embarrassingly-parallel-job"></a>Ces tâches
La tâche ces est le scénario plus scalable que nous avons dans Azure flux Analytique. Il connecte à une partition de l’entrée d’une instance de la requête à une partition de la sortie. Pour réaliser ce parallélisme, quelques points :

1.  Si votre requête logique dépendant de la même clé traitée par la même instance de requête, vous devez vous assurer que les événements accédez à la même partition de votre saisie. Dans le cas d’événement Hubs, cela signifie que les données d’événement doit comporter **PartitionKey** définir ou vous pouvez utiliser expéditeurs partitionnées. Pour les objets Blob, cela signifie que les événements sont envoyés dans le même dossier partition. Si votre logique de requête ne nécessite pas la même clé soient traités par la même instance de requête, vous pouvez ignorer cette exigence. Un exemple de ce serait une requête sélection ou un projet/filtre simple.  
2.  Une fois que les données sont disposées comme il doit se trouver sur le côté d’entrée, nous avons besoin de vous assurer que votre requête est répartie. Pour cela, vous utilisez **Partition By** dans toutes les étapes. Étapes multiples sont autorisés, mais ils sont tous doivent être partition par la même clé. Une autre chose à noter est que, pour l’instant, la touche partition doit être définie sur **IDPartition** pour avoir une opération entièrement en parallèle.  
3.  Actuellement uniquement événement Hubs et Blob prennent en charge sortie partitionnée. Pour la sortie Hubs d’événement, vous devez configurer le champ **PartitionKey** pour être **IDPartition**. Pour les objets Blob, vous n’avez rien à faire.  
4.  Une autre chose à noter, le nombre de partitions d’entrée doit être égale au nombre de partitions de sortie. Sortie BLOB ne prend actuellement en charge les partitions, mais il s’agit OK, car elle hérite le modèle de partition de la requête en amont. Exemples de valeurs partition permettant à une tâche totalement parallèle :  
    1.  8 Hubs événement entrée partitions et 8 événement Hubs sortie partitions
    2.  8 événement Hubs d’entrée partitions et sortie Blob  
    3.  8 des partitions blob d’entrée et sortie Blob  
    4.  8 partitions d’entrée blob et 8 événement Hubs sortie partitions  

Voici quelques exemples de scénarios qui sont ces parallèles.

### <a name="simple-query"></a>Requête simple
Entrée – Hubs événement avec 8 partitions sortie – événement concentrateur avec 8 partitions

**Requête :**

    SELECT TollBoothId
    FROM Input1 Partition By PartitionId
    WHERE TollBoothId > 100

Cette requête est un filtre simple et par conséquent, nous n’avez pas besoin à vous soucier de partition l’entrée que nous envoyer aux événements Hubs. Vous remarquerez que la requête a **Partition By** **IDPartition**, afin que nous répondre demande #2 ci-dessus. Pour la sortie, nous avons besoin de configurer la sortie Hubs événement dans le projet pour que le champ **PartitionKey** défini sur **IDPartition**. Une dernière vérification, partitions d’entrée == partitions de sortie. Cette topologie est ces.

### <a name="query-with-grouping-key"></a>Requête avec une clé de regroupement
Entrée – Hubs événement avec 8 partitions sortie – Blob

**Requête :**

    SELECT COUNT(*) AS Count, TollBoothId
    FROM Input1 Partition By PartitionId
    GROUP BY TumblingWindow(minute, 3), TollBoothId, PartitionId

Cette requête possède une clé de regroupement et par conséquent, la même clé doit être traité par la même instance de requête. Cela signifie que nous devons envoyer notre événements aux événements Hubs de manière partitionnée. Touche qui nous intéressent ? **IDPartition** est un concept logique de travail, la touche que nous intéresse est **TollBoothId**. Cela signifie que nous devons définir la **PartitionKey** des données d’événement nous envoyer aux événements Hubs à être **TollBoothId** de l’événement. La requête a **Partition By** des **IDPartition**, afin que nous existe-t-il bonne. Pour la sortie, car il s’agit d’objets Blob, nous ne pas besoin à vous soucier de configuration **PartitionKey**. Pour cette exigence #4, là encore, il s’agit Blob, afin que nous n’avez pas besoin de soucier. Cette topologie est ces.

### <a name="multi-step-query-with-grouping-key"></a>Requête étape à plusieurs avec clé de regroupement ###
Entrée – concentrateur événement avec 8 partitions sortie – événement concentrateur avec 8 partitions

**Requête :**

    WITH Step1 AS (
    SELECT COUNT(*) AS Count, TollBoothId, PartitionId
    FROM Input1 Partition By PartitionId
    GROUP BY TumblingWindow(minute, 3), TollBoothId, PartitionId
    )
    
    SELECT SUM(Count) AS Count, TollBoothId
    FROM Step1 Partition By PartitionId
    GROUP BY TumblingWindow(minute, 3), TollBoothId, PartitionId

Cette requête possède une clé de regroupement et par conséquent, la même clé doit être traité par la même instance de requête. Nous pouvons utiliser la même stratégie que la requête précédente. La requête comporte plusieurs étapes. Chaque étape a **Partition By** des **IDPartition**? Oui, afin que nous sont corrects. Pour la sortie, nous avons besoin d’auquel la **PartitionKey** **IDPartition** comme décrit ci-dessus et nous pouvons également voir qu'il a le même nombre de partitions comme entrée. Cette topologie est ces.


## <a name="example-scenarios-that-are-not-embarrassingly-parallel"></a>Exemples de scénarios qui ne sont pas ces

### <a name="mismatched-partition-count"></a>Nombre de partitions qui ne correspondent pas ###
Entrée – Hubs événement avec 8 partitions sortie – événement concentrateur avec 32 partitions

Il n’a aucune importance que la requête est dans ce cas, car l’entrée partition Nb ! = nombre de partitions de sortie.

### <a name="not-using-event-hubs-or-blobs-as-output"></a>N’utilisez ne pas Hubs événement ou des objets BLOB sous forme de sortie
Entrée – Hubs événement avec 8 partitions sortie – PowerBI

Sortie PowerBI ne prend pas en charge partition.

### <a name="multi-step-query-with-different-partition-by-values"></a>Requête d’étape à plusieurs avec différentes Partition par valeurs
Entrée – concentrateur événement avec 8 partitions sortie – événement concentrateur avec 8 partitions

**Requête :**

    WITH Step1 AS (
    SELECT COUNT(*) AS Count, TollBoothId, PartitionId
    FROM Input1 Partition By PartitionId
    GROUP BY TumblingWindow(minute, 3), TollBoothId, PartitionId
    )
    
    SELECT SUM(Count) AS Count, TollBoothId
    FROM Step1 Partition By TollBoothId
    GROUP BY TumblingWindow(minute, 3), TollBoothId
    
Comme vous pouvez le voir, la deuxième étape utilise **TollBoothId** comme clé partition. Ce n’est pas identique à la première étape et donc nous faire un aléatoire. 

Voici quelques exemples et counterexamples des tâches de flux Analytique qui seront en mesure d’obtenir une topologie ces et diminuée le potentiel d’échelle maximale. Pour les travaux qui ne correspondent pas un de ces profils, mises à jour ultérieures expliquant en détail comment adapter au maximum des autres scénarios flux Analytique canoniques sont effectués.

Pour effectuer maintenant utiliser des orientations générales ci-dessous :

## <a name="calculate-the-maximum-streaming-units-of-a-job"></a>Calculer la valeur maximale en continu intensité d’utilisation d’une tâche
Le nombre total de diffusion en continu unités qui peuvent être utilisées par une tâche de flux de données Analytique dépend du nombre des étapes de la requête définie pour la tâche et le nombre de partitions pour chaque étape.

### <a name="steps-in-a-query"></a>Étapes dans une requête
Une requête peut comporter un ou plusieurs étapes. Chaque étape est une sous-requête définie à l’aide de **l’aide** du mot clé. La requête uniquement est en dehors de **l’aide** du mot clé est également considérée comme une étape, par exemple, l’instruction **SELECT** dans la requête suivante :

    WITH Step1 AS (
        SELECT COUNT(*) AS Count, TollBoothId
        FROM Input1 Partition By PartitionId
        GROUP BY TumblingWindow(minute, 3), TollBoothId, PartitionId
    )

    SELECT SUM(Count) AS Count, TollBoothId
    FROM Step1
    GROUP BY TumblingWindow(minute,3), TollBoothId

La requête précédente comporte deux étapes.

> [AZURE.NOTE] Cet exemple de requête sera expliqué plus loin dans l’article.

### <a name="partition-a-step"></a>Partition une étape

Partition une étape requiert les conditions suivantes :

- La source d’entrée doit être partition. Pour plus d’informations, voir le [Guide de programmation Hubs événement](../event-hubs/event-hubs-programming-guide.md).
- L’instruction **SELECT** de la requête doit lire à partir d’une source d’entrée partitionnée.
- La requête dans l’étape doit avoir le mot-clé **Partition By**

Lorsqu’une requête est segmentée, les événements d’entrée sera groupes transformés et agrégé dans partition distincte, et des événements sorties sont générés pour chacun des groupes. Si un agrégat combiné c'est-à-dire, vous devez créer une deuxième étape non partitions à agréger.

### <a name="calculate-the-max-streaming-units-for-a-job"></a>Calculer la max streaming intensité d’utilisation pour une tâche

Toutes les étapes non partitions ensemble peuvent évoluer jusqu'à six unités de diffusion en continu pour une tâche de flux de données Analytique. Pour ajouter des autres unités, la diffusion une étape doit être partition. Chaque partition peut avoir des unités de diffusion en continu six.

<table border="1">
<tr><th>Requête d’une tâche</th><th>Max streaming intensité d’utilisation de la tâche</th></td>

<tr><td>
<ul>
<li>La requête contient une seule étape.</li>
<li>L’étape ne fonctionne pas.</li>
</ul>
</td>
<td>6</td></tr>

<tr><td>
<ul>
<li>Le flux de données d’entrée est partition par 3.</li>
<li>La requête contient une seule étape.</li>
<li>L’étape partition.</li>
</ul>
</td>
<td>18</td></tr>

<tr><td>
<ul>
<li>La requête contient deux étapes.</li>
<li>Aucune des étapes est partition.</li>
</ul>
</td>
<td>6</td></tr>



<tr><td>
<ul>
<li>L’entrée de flux de données est partition par 3.</li>
<li>La requête contient deux étapes. L’étape d’entrée est répartie et la deuxième étape n’est pas.</li>
<li>L’instruction SELECT lit à partir de l’entrée partitionnée.</li>
</ul>
</td>
<td>24 (18 pour obtenir la procédure partitionnée) + 6 pour obtenir la procédure non partitions</td></tr>
</table>

### <a name="examples-of-scale"></a>Exemples d’échelle
La requête suivante calcule le nombre de voitures passant par un poste numéro payant avec trois péages dans une fenêtre de trois minutes. Cette requête peut être mise à l’échelle jusqu'à six unités de diffusion en continu.

    SELECT COUNT(*) AS Count, TollBoothId
    FROM Input1
    GROUP BY TumblingWindow(minute, 3), TollBoothId, PartitionId

Pour utiliser d’autres unités de diffusion en continu pour la requête, à la fois le flux de données d’entrée et la requête doit être partition. Étant donné que la partition de flux de données est définie sur 3, la requête suivante modifiée peut être mise à l’échelle jusqu'à 18 unités de diffusion en continu :

    SELECT COUNT(*) AS Count, TollBoothId
    FROM Input1 Partition By PartitionId
    GROUP BY TumblingWindow(minute, 3), TollBoothId, PartitionId

Lorsqu’une requête est segmentée, les événements d’entrée sont traitées et agrégés dans les groupes de partition distincte. Événements de sortie sont également générés pour chacun des groupes. Partition peut entraîner des résultats inattendus lorsque le champ **Group by** n’est pas la clé de Partition dans l’entrée de flux de données. Par exemple, le champ **TollBoothId** dans l’exemple de requête précédent n’est pas la clé de Partition de Input1. Les données à partir du 1 # péage peuvent se propager dans plusieurs partitions.

Chacune des partitions Input1 est traité séparément par flux Analytique et plusieurs enregistrements de l’inventaire passe de voiture via pour la même péage dans la fenêtre qui s’écroulent même seront créés. Si la clé de partition d’entrée ne peut pas être modifiée, ce problème peut être résolu en ajoutant une étape supplémentaire partition non, par exemple :

    WITH Step1 AS (
        SELECT COUNT(*) AS Count, TollBoothId
        FROM Input1 Partition By PartitionId
        GROUP BY TumblingWindow(minute, 3), TollBoothId, PartitionId
    )

    SELECT SUM(Count) AS Count, TollBoothId
    FROM Step1
    GROUP BY TumblingWindow(minute, 3), TollBoothId

Cette requête peut être adaptée à 24 unités de la diffusion.

>[AZURE.NOTE] Si vous joignez des deux flux, assurez-vous que les flux de données est répartis par la clé de partition de la colonne que vous effectuez les jointures, et que vous avez le même nombre de partitions dans les deux flux.


## <a name="configure-stream-analytics-job-partition"></a>Configurer Analytique flux travail partition

**Pour ajuster une unité de diffusion en continu pour une tâche**

1. Connectez-vous au [portail de gestion](https://manage.windowsazure.com).
2. Dans le volet gauche, cliquez sur **Flux Analytique** .
3. Cliquez sur la tâche de flux Analytique que vous souhaitez mettre à l’échelle.
4. Cliquez sur **échelle** en haut de la page.

![Flux de données Azure Analytique flux unités échelle][img.stream.analytics.streaming.units.scale]

Dans le portail Azure, les paramètres d’échelle sont accessibles sous Paramètres :

![Configuration du travail portail flux Analytique Azure][img.stream.analytics.preview.portal.settings.scale]

## <a name="monitor-job-performance"></a>Surveiller les performances de travail

À l’aide du portail de gestion, vous pouvez suivre le débit d’une tâche dans événements/seconde :

![Azure flux Analytique surveiller les tâches][img.stream.analytics.monitor.job]

Calculez le débit de la charge de travail attendu événements/seconde. Si le débit est inférieure à celle attendue, régler la partition d’entrée, paramétrez la requête et ajouter des unités de diffusion en continu supplémentaires à une tâche.

## <a name="stream-analytics-throughput-at-scale---raspberry-pi-scenario"></a>Débit en flux Analytique à l’échelle - scénario framboises Pi


Pour mieux comprendre comment travaux analytique de flux adapter dans un scénario classique en termes de débit de traitement sur plusieurs unités de diffusion en continu, voici une expérience qui envoie des données de capteur (clients) à un concentrateur de l’événement, il traite et envoie alerte ou vos statistiques sous la forme d’une sortie à un autre événement concentrateur.

Le client envoie des données de synthèse capteur aux événements Hubs au format JSON à flux Analytique et le résultat est également au format JSON.  Voici comment les données d’exemple serait j’aime –  

    {"devicetime":"2014-12-11T02:24:56.8850110Z","hmdt":42.7,"temp":72.6,"prss":98187.75,"lght":0.38,"dspl":"R-PI Olivier's Office"}

Requête : « envoyer une alerte lorsque la lumière est éteint »  

    SELECT AVG(lght),
     “LightOff” as AlertText
    FROM input TIMESTAMP
    BY devicetime
     WHERE
        lght< 0.05 GROUP BY TumblingWindow(second, 1)

Mesure de débit : Débit dans ce contexte est la quantité de données d’entrée traitées par Analytique de flux de données dans un certain temps (10 minutes). Pour obtenir les meilleurs résultats de traitement pour les données d’entrée, à la fois le flux de données d’entrée et la requête doit être partition. **COUNT()**est également inclus dans la requête mesurer le nombre d’événements d’entrée ont été traité. Pour vous assurer que la tâche est en attente pas simplement pour les événements d’entrée à venir, chaque partition du concentrateur événement d’entrée a été préchargée avec des données d’entrée suffisantes (environ 300 Mo).  

Voici les résultats avec augmenter le nombre d’unités de diffusion en continu et Partition correspondante statistiques dans Hubs événement.  

<table border="1">
<tr><th>Partitions d’entrée</th><th>Partitions de sortie</th><th>Unités de diffusion en continu</th><th>Débit
</th></td>

<tr><td>12</td>
<td>12</td>
<td>6</td>
<td>4.06 Mo/s</td>
</tr>

<tr><td>12</td>
<td>12</td>
<td>12</td>
<td>8.06 Mo/s</td>
</tr>

<tr><td>48</td>
<td>48</td>
<td>48</td>
<td>38.32 Mo/s</td>
</tr>

<tr><td>192</td>
<td>192</td>
<td>192</td>
<td>172.67 Mo/s</td>
</tr>

<tr><td>480</td>
<td>480</td>
<td>480</td>
<td>454.27 Mo/s</td>
</tr>

<tr><td>720</td>
<td>720</td>
<td>720</td>
<td>609.69 Mo/s</td>
</tr>
</table>

![IMG.Stream.Analytics.perfgraph][img.stream.analytics.perfgraph]

## <a name="get-help"></a>Obtenir de l’aide
Pour obtenir une aide supplémentaire, essayez de notre [forum Azure flux Analytique](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics).


## <a name="next-steps"></a>Étapes suivantes

- [Présentation des flux de données Azure Analytique](stream-analytics-introduction.md)
- [Commencer à utiliser Azure flux Analytique](stream-analytics-get-started.md)
- [Référence du langage flux Azure Analytique requête](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Référence de l’API REST gestion Analytique flux Azure](https://msdn.microsoft.com/library/azure/dn835031.aspx)


<!--Image references-->

[img.stream.analytics.monitor.job]: ./media/stream-analytics-scale-jobs/StreamAnalytics.job.monitor.png
[img.stream.analytics.configure.scale]: ./media/stream-analytics-scale-jobs/StreamAnalytics.configure.scale.png
[img.stream.analytics.perfgraph]: ./media/stream-analytics-scale-jobs/perf.png
[img.stream.analytics.streaming.units.scale]: ./media/stream-analytics-scale-jobs/StreamAnalyticsStreamingUnitsExample.jpg
[img.stream.analytics.preview.portal.settings.scale]: ./media/stream-analytics-scale-jobs/StreamAnalyticsPreviewPortalJobSettings.png

<!--Link references-->

[microsoft.support]: http://support.microsoft.com
[azure.management.portal]: http://manage.windowsazure.com
[azure.event.hubs.developer.guide]: http://msdn.microsoft.com/library/azure/dn789972.aspx

[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-get-started.md
[stream.analytics.query.language.reference]: http://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: http://go.microsoft.com/fwlink/?LinkId=517301
 

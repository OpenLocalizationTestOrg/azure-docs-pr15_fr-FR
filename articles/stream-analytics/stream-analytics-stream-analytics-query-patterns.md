<properties
    pageTitle="Exemples de modes d’utilisation courants dans les flux de données Analytique de requête | Microsoft Azure"
    description="Modèles de requête Analytique Azure flux communs "
    keywords="exemples de requête"
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
    ms.workload="big-data"
    ms.date="09/26/2016"
    ms.author="jeffstok"/>


# <a name="query-examples-for-common-stream-analytics-usage-patterns"></a>Exemples de modes d’utilisation courants Analytique de flux de données de requête

## <a name="introduction"></a>Introduction

Requêtes dans Azure flux Analytique sont exprimés dans un langage de requête SQL similaires présentée dans le guide de [Référence du langage flux Analytique requête](https://msdn.microsoft.com/library/azure/dn834998.aspx) .  Cet article donne des solutions à plusieurs modèles de requête courants basés sur des scénarios réels.  Il est en cours et continue à être mis à jour avec les nouveaux modèles de manière continue.

## <a name="query-example-data-type-conversions"></a>Exemple de requête : conversions de types de données
**Description**: définir les types de propriétés dans le flux d’entrée.
Par exemple, poids voiture arrive sur le flux d’entrée comme chaînes et doit être converti en INT pour effectuer de synthèse.

**Entrée**:

| Rendre | Heure | Poids |
| --- | --- | --- |
| Honda | 2015-01-01T00:00:01.0000000Z | « 1000 » |
| Honda | 2015-01-01T00:00:02.0000000Z | « 2000 » |

**Résultat**:

| Rendre | Poids |
| --- | --- |
| Honda | 3000 |

**Solution**:

    SELECT
        Make,
        SUM(CAST(Weight AS BIGINT)) AS Weight
    FROM
        Input TIMESTAMP BY Time
    GROUP BY
        Make,
        TumblingWindow(second, 10)

**Explication**: utiliser une instruction CAST sur le champ poids pour spécifier son type (voir la liste des Types de données pris en charge [ici](https://msdn.microsoft.com/library/azure/dn835065.aspx)).


## <a name="query-example-using-likenot-like-to-do-pattern-matching"></a>Exemple de requête : utilisant j’aime/Not like à filtre de correspondance
**Description**: vérifier qu’une valeur du champ à l’événement correspond à un certain modèle par exemple, expéditeur plaques licence qui commencent par A et se terminent par 9

**Entrée**:

| Rendre | LicensePlate | Heure |
| --- | --- | --- |
| Honda | ABC-123 | 2015-01-01T00:00:01.0000000Z |
| Toyota | AAA-999 | 2015-01-01T00:00:02.0000000Z |
| Nissan | ABC 369 | 2015-01-01T00:00:03.0000000Z |

**Résultat**:

| Rendre | LicensePlate | Heure |
| --- | --- | --- |
| Toyota | AAA-999 | 2015-01-01T00:00:02.0000000Z |
| Nissan | ABC 369 | 2015-01-01T00:00:03.0000000Z |

**Solution**:

    SELECT
        *
    FROM
        Input TIMESTAMP BY Time
    WHERE
        LicensePlate LIKE 'A%9'

**Explication**: utiliser l’instruction LIKE pour vérifier que la valeur du champ LicensePlate commence par A puis comporte une chaîne de zéro ou plusieurs caractères et elle se termine par 9. 

## <a name="query-example-specify-logic-for-different-casesvalues-case-statements"></a>Exemple de requête : spécifiez une logique de cas/valeurs différentes (instructions CASE)
**Description**: fournir calcul différent pour un champ en fonction de certains critères.
Par exemple, fournir une description de chaîne pour voitures combien passé d’utiliser la même faire avec un cas spécial pour 1.

**Entrée**:

| Rendre | Heure |
| --- | --- |
| Honda | 2015-01-01T00:00:01.0000000Z |
| Toyota | 2015-01-01T00:00:02.0000000Z |
| Toyota | 2015-01-01T00:00:03.0000000Z |

**Résultat**:

| CarsPassed | Heure |
| --- | --- | --- |
| 1 Honda | 2015-01-01T00:00:10.0000000Z |
| 2 Toyotas | 2015-01-01T00:00:10.0000000Z |

**Solution**:

    SELECT
        CASE
            WHEN COUNT(*) = 1 THEN CONCAT('1 ', Make)
            ELSE CONCAT(CAST(COUNT(*) AS NVARCHAR(MAX)), ' ', Make, 's')
        END AS CarsPassed,
        System.TimeStamp AS Time
    FROM
        Input TIMESTAMP BY Time
    GROUP BY
        Make,
        TumblingWindow(second, 10)

**Explication**: clause la CASE permet de fournir un calcul différent en fonction de certains critères (dans notre cas, le nombre de voitures dans la fenêtre agrégation).

## <a name="query-example-send-data-to-multiple-outputs"></a>Exemple de requête : envoyer des données à plusieurs sorties
**Description**: envoyer des données à plusieurs cibles de sortie à partir d’une seule tâche.
Par exemple, analyser les données d’une alerte de seuil et archiver tous les événements au stockage d’objets blob

**Entrée**:

| Rendre | Heure |
| --- | --- |
| Honda | 2015-01-01T00:00:01.0000000Z |
| Honda | 2015-01-01T00:00:02.0000000Z |
| Toyota | 2015-01-01T00:00:01.0000000Z |
| Toyota | 2015-01-01T00:00:02.0000000Z |
| Toyota | 2015-01-01T00:00:03.0000000Z |

**Output1**:

| Rendre | Heure |
| --- | --- |
| Honda | 2015-01-01T00:00:01.0000000Z |
| Honda | 2015-01-01T00:00:02.0000000Z |
| Toyota | 2015-01-01T00:00:01.0000000Z |
| Toyota | 2015-01-01T00:00:02.0000000Z |
| Toyota | 2015-01-01T00:00:03.0000000Z |

**Output2**:

| Rendre | Heure | Nombre |
| --- | --- | --- |
| Toyota | 2015-01-01T00:00:10.0000000Z | 3 |

**Solution**:

    SELECT
        *
    INTO
        ArchiveOutput
    FROM
        Input TIMESTAMP BY Time

    SELECT
        Make,
        System.TimeStamp AS Time,
        COUNT(*) AS [Count]
    INTO
        AlertOutput
    FROM
        Input TIMESTAMP BY Time
    GROUP BY
        Make,
        TumblingWindow(second, 10)
    HAVING
        [Count] >= 3

**Explication**: dans la clause indique flux Analytique qui des sorties pour écrire les données à partir de cette déclaration.
La première requête est un directe des données que nous avons reçu vers une sortie que nous nommé ArchiveOutput.
La deuxième requête effectue une agrégation simple et le filtrage et envoyer les résultats à un système d’alerte en aval.
*Remarque*: vous pouvez également réutiliser les résultats de CTE (avec les relevés) dans plusieurs instructions de sortie – cela a l’avantage d’ouverture de lecteurs d’un nombre moins élevé à la source d’entrée.
Par exemple, 

    WITH AllRedCars AS (
        SELECT
            *
        FROM
            Input TIMESTAMP BY Time
        WHERE
            Color = 'red'
    )
    SELECT * INTO HondaOutput FROM AllRedCars WHERE Make = 'Honda'
    SELECT * INTO ToyotaOutput FROM AllRedCars WHERE Make = 'Toyota'

## <a name="query-example-counting-unique-values"></a>Exemple de requête : compter les valeurs uniques
**Description**: compter le nombre de valeurs de champ uniques qui s’affichent dans le flux de données dans une fenêtre de temps.
Par exemple, rendre unique combien de voitures passé le stand payants dans une fenêtre de deuxième 2 ?

**Entrée**:

| Rendre | Heure |
| --- | --- |
| Honda | 2015-01-01T00:00:01.0000000Z |
| Honda | 2015-01-01T00:00:02.0000000Z |
| Toyota | 2015-01-01T00:00:01.0000000Z |
| Toyota | 2015-01-01T00:00:02.0000000Z |
| Toyota | 2015-01-01T00:00:03.0000000Z |

**Résultat :**

| Nombre | Heure |
| --- | --- |
| 2 | 2015-01-01T00:00:02.000Z |
| 1 | 2015-01-01T00:00:04.000Z |

**Solution :**

    WITH Makes AS (
        SELECT
            Make,
            COUNT(*) AS CountMake
        FROM
            Input TIMESTAMP BY Time
        GROUP BY
              Make,
              TumblingWindow(second, 2)
    )
    SELECT
        COUNT(*) AS Count,
        System.TimeStamp AS Time
    FROM
        Makes
    GROUP BY
        TumblingWindow(second, 1)


**Explication :** Nous faire une agrégation initiale pour obtenir rend uniques avec leur compte au-dessus de la fenêtre.
Nous puis effectuez l’une agrégation de marques combien nous se – donné unique toutes les valeurs dans une fenêtre obtiennent le même horodatage puis la fenêtre agrégation deuxième doit être minimales pour n'agréger pas 2 windows à partir de la première étape.

## <a name="query-example-determine-if-a-value-has-changed"></a>Exemple de requête : déterminer si une valeur a été modifiée#
**Description**: regardez une valeur précédente pour déterminer si celle-ci est différente de celle de la valeur actuelle est par exemple, la voiture précédente en déplacement payants la même émettre en tant que la voiture en cours ?

**Entrée**:

| Rendre | Heure |
| --- | --- |
| Honda | 2015-01-01T00:00:01.0000000Z |
| Toyota | 2015-01-01T00:00:02.0000000Z |

**Résultat**:

| Rendre | Heure |
| --- | --- |
| Toyota | 2015-01-01T00:00:02.0000000Z |

**Solution**:

    SELECT
        Make,
        Time
    FROM
        Input TIMESTAMP BY Time
    WHERE
        LAG(Make, 1) OVER (LIMIT DURATION(minute, 1)) <> Make

**Explication**: utilisation décalage permettant de lire en entrée diffuser un événement précédent et obtenir la valeur de création. Comparer la marque sur l’événement en cours, puis l’événement de sortie s’ils sont différents.

## <a name="query-example-find-first-event-in-a-window"></a>Exemple de requête : rechercher premier événement dans une fenêtre
**Description**: rechercher première voiture dans chaque intervalle de 10 minutes ?

**Entrée**:

| LicensePlate | Rendre | Heure |
| --- | --- | --- |
| DXE 5291 | Honda | 2015-07-27T00:00:05.0000000Z |
| YZK 5704 | Ford | 2015-07-27T00:02:17.0000000Z |
| ENLV 8282 | Honda | 2015-07-27T00:05:01.0000000Z |
| YHN 6970 | Toyota | 2015-07-27T00:06:00.0000000Z |
| VFE 1616 | Toyota | 2015-07-27T00:09:31.0000000Z |
| QYF 9358 | Honda | 2015-07-27T00:12:02.0000000Z |
| MDR 6128 | BMW | 2015-07-27T00:13:45.0000000Z |

**Résultat**:

| LicensePlate | Rendre | Heure |
| --- | --- | --- |
| DXE 5291 | Honda | 2015-07-27T00:00:05.0000000Z |
| QYF 9358 | Honda | 2015-07-27T00:12:02.0000000Z |

**Solution**:

    SELECT 
        LicensePlate,
        Make,
        Time
    FROM 
        Input TIMESTAMP BY Time
    WHERE 
        IsFirst(minute, 10) = 1

À présent, nous allons modifier la première voiture problème et rechercher de particulier en faire dans chaque intervalle de 10 minutes.

| LicensePlate | Rendre | Heure |
| --- | --- | --- |
| DXE 5291 | Honda | 2015-07-27T00:00:05.0000000Z |
| YZK 5704 | Ford | 2015-07-27T00:02:17.0000000Z |
| YHN 6970 | Toyota | 2015-07-27T00:06:00.0000000Z |
| QYF 9358 | Honda | 2015-07-27T00:12:02.0000000Z |
| MDR 6128 | BMW | 2015-07-27T00:13:45.0000000Z |

**Solution**:

    SELECT 
        LicensePlate,
        Make,
        Time
    FROM 
        Input TIMESTAMP BY Time
    WHERE 
        IsFirst(minute, 10) OVER (PARTITION BY Make) = 1

## <a name="query-example-find-last-event-in-a-window"></a>Exemple de requête : rechercher dernier événement dans une fenêtre
**Description**: voiture dernière rechercher dans chaque intervalle de 10 minutes.

**Entrée**:

| LicensePlate | Rendre | Heure |
| --- | --- | --- |
| DXE 5291 | Honda | 2015-07-27T00:00:05.0000000Z |
| YZK 5704 | Ford | 2015-07-27T00:02:17.0000000Z |
| ENLV 8282 | Honda | 2015-07-27T00:05:01.0000000Z |
| YHN 6970 | Toyota | 2015-07-27T00:06:00.0000000Z |
| VFE 1616 | Toyota | 2015-07-27T00:09:31.0000000Z |
| QYF 9358 | Honda | 2015-07-27T00:12:02.0000000Z |
| MDR 6128 | BMW | 2015-07-27T00:13:45.0000000Z |

**Résultat**:

| LicensePlate | Rendre | Heure |
| --- | --- | --- |
| VFE 1616 | Toyota | 2015-07-27T00:09:31.0000000Z |
| MDR 6128 | BMW | 2015-07-27T00:13:45.0000000Z |

**Solution**:

    WITH LastInWindow AS
    (
        SELECT 
            MAX(Time) AS LastEventTime
        FROM 
            Input TIMESTAMP BY Time
        GROUP BY 
            TumblingWindow(minute, 10)
    )
    SELECT 
        Input.LicensePlate,
        Input.Make,
        Input.Time
    FROM
        Input TIMESTAMP BY Time 
        INNER JOIN LastInWindow
        ON DATEDIFF(minute, Input, LastInWindow) BETWEEN 0 AND 10
        AND Input.Time = LastInWindow.LastEventTime

**Explication**: il existe deux étapes dans la requête : la première partie recherche horodatage le plus récent dans windows 10 minutes. La deuxième étape joint les résultats de la première requête avec des flux de données d’origine pour rechercher des événements qui correspondent aux horodatages dernières dans chaque fenêtre. 

## <a name="query-example-detect-the-absence-of-events"></a>Exemple de requête : détecter l’absence d’événements
**Description**: Vérifiez ne qu’aucune valeur qui correspond à un certain critère à un flux de données.
Par exemple, 2 voitures consécutives à partir de la même marque saisi la route payants 90 secondes ?

**Entrée**:

| Rendre | LicensePlate | Heure |
| --- | --- | --- |
| Honda | ABC-123 | 2015-01-01T00:00:01.0000000Z |
| Honda | AAA-999 | 2015-01-01T00:00:02.0000000Z |
| Toyota | DÉFINITION 987 | 2015-01-01T00:00:03.0000000Z |
| Honda | GHI 345 | 2015-01-01T00:00:04.0000000Z |

**Résultat**:

| Rendre | Heure | CurrentCarLicensePlate | FirstCarLicensePlate | FirstCarTime |
| --- | --- | --- | --- | --- |
| Honda | 2015-01-01T00:00:02.0000000Z | AAA-999 | ABC-123 | 2015-01-01T00:00:01.0000000Z |

**Solution**:

    SELECT
        Make,
        Time,
        LicensePlate AS CurrentCarLicensePlate,
        LAG(LicensePlate, 1) OVER (LIMIT DURATION(second, 90)) AS FirstCarLicensePlate,
        LAG(Time, 1) OVER (LIMIT DURATION(second, 90)) AS FirstCarTime
    FROM
        Input TIMESTAMP BY Time
    WHERE
        LAG(Make, 1) OVER (LIMIT DURATION(second, 90)) = Make

**Explication**: utilisation décalage permettant de lire en entrée diffuser un événement précédent et obtenir la valeur de création. Comparer la marque sur l’événement en cours, puis l’événement de sortie s’ils sont identiques et décalage permet d’obtenir les données relatives à la voiture précédente.

## <a name="query-example-detect-duration-between-events"></a>Exemple de requête : détecter la durée entre les événements
**Description**: trouver la durée d’un événement donné. Par exemple, étant donné un parcours web déterminer le temps passé sur une fonctionnalité.

**Entrée**:  
  
| Utilisateur | Fonctionnalité | Événement | Heure |
| --- | --- | --- | --- |
| user@location.com | RightMenu | Démarrer | 2015-01-01T00:00:01.0000000Z |
| user@location.com | RightMenu | Fin | 2015-01-01T00:00:08.0000000Z |
  
**Résultat**:  
  
| Utilisateur | Fonctionnalité | Durée |
| --- | --- | --- |
| user@location.com | RightMenu | 7 |
  

**Solution**

````
    SELECT
        [user], feature, DATEDIFF(second, LAST(Time) OVER (PARTITION BY [user], feature LIMIT DURATION(hour, 1) WHEN Event = 'start'), Time) as duration
    FROM input TIMESTAMP BY Time
    WHERE
        Event = 'end'
````

**Explication**: fonction dernière utilisation pour récupérer la dernière valeur d’heure lorsque le type d’événement a été 'Start'. Notez que la dernière fonction utilise PARTITION BY [utilisateur] pour indiquer le résultat calculé par utilisateur unique.  La requête comporte un seuil maximal 1 heure pour la différence de temps entre les événements « Début » et « Arrêter » mais peut être configurée en tant que nécessaire (limite DURATION(hour, 1).

## <a name="query-example-detect-duration-of-a-condition"></a>Exemple de requête : détecter la durée d’une condition
**Description**: savoir combien de temps une condition s’est produite.
Par exemple, supposons qu’un bogue qui a donné lieu à toutes les voitures ayant un poids incorrect (supérieures à 20 000 livres) – nous voulons calculer la durée du bogue.

**Entrée**:

| Rendre | Heure | Poids |
| --- | --- | --- |
| Honda | 2015-01-01T00:00:01.0000000Z | 2000 |
| Toyota | 2015-01-01T00:00:02.0000000Z | 25000 |
| Honda | 2015-01-01T00:00:03.0000000Z | 26000 |
| Toyota | 2015-01-01T00:00:04.0000000Z | 25000 |
| Honda | 2015-01-01T00:00:05.0000000Z | 26000 |
| Toyota | 2015-01-01T00:00:06.0000000Z | 25000 |
| Honda | 2015-01-01T00:00:07.0000000Z | 26000 |
| Toyota | 2015-01-01T00:00:08.0000000Z | 2000 |

**Résultat**:

| StartFault | EndFault |
| --- | --- |
| 2015-01-01T00:00:02.000Z | 2015-01-01T00:00:07.000Z |

**Solution**:

````
    WITH SelectPreviousEvent AS
    (
    SELECT
    *,
        LAG([time]) OVER (LIMIT DURATION(hour, 24)) as previousTime,
        LAG([weight]) OVER (LIMIT DURATION(hour, 24)) as previousWeight
    FROM input TIMESTAMP BY [time]
    )

    SELECT 
        LAG(time) OVER (LIMIT DURATION(hour, 24) WHEN previousWeight < 20000 ) [StartFault],
        previousTime [EndFault]
    FROM SelectPreviousEvent
    WHERE
        [weight] < 20000
        AND previousWeight > 20000
````

**Explication**: utilisation décalage pour afficher le flux d’entrée de 24 heures et recherchez instances où StartFault et StopFault sont étend weight < 20000.

## <a name="query-example-fill-missing-values"></a>Exemple de requête : remplir les valeurs manquantes
**Description**: pour le flux des événements qui ont des valeurs manquantes, produire un flux d’événements à intervalles réguliers.
Par exemple, générer un événement toutes les 5 secondes signalant le point de données plus récemment utilisé.

**Entrée**:

| t | valeur |
|--------------------------|-------|
| « 2014-01-01T06:01:00 » | 1 |
| « 2014-01-01T06:01:05 » | 2 |
| « 2014-01-01T06:01:10 » | 3 |
| « 2014-01-01T06:01:15 » | 4 |
| « 2014-01-01T06:01:30 » | 5 |
| « 2014-01-01T06:01:35 » | 6 |

**Sortie (10 premières lignes)**:

| windowend | lastevent.t | lastevent.Value |
|--------------------------|--------------------------|--------|
| 2014-01-01T14:01:00.000Z | 2014-01-01T14:01:00.000Z | 1 |
| 2014-01-01T14:01:05.000Z | 2014-01-01T14:01:05.000Z | 2 |
| 2014-01-01T14:01:10.000Z | 2014-01-01T14:01:10.000Z | 3 |
| 2014-01-01T14:01:15.000Z | 2014-01-01T14:01:15.000Z | 4 |
| 2014-01-01T14:01:20.000Z | 2014-01-01T14:01:15.000Z | 4 |
| 2014-01-01T14:01:25.000Z | 2014-01-01T14:01:15.000Z | 4 |
| 2014-01-01T14:01:30.000Z | 2014-01-01T14:01:30.000Z | 5 |
| 2014-01-01T14:01:35.000Z | 2014-01-01T14:01:35.000Z | 6 |
| 2014-01-01T14:01:40.000Z | 2014-01-01T14:01:35.000Z | 6 |
| 2014-01-01T14:01:45.000Z | 2014-01-01T14:01:35.000Z | 6 |

    
**Solution**:

    SELECT
        System.Timestamp AS windowEnd,
        TopOne() OVER (ORDER BY t DESC) AS lastEvent
    FROM
        input TIMESTAMP BY t
    GROUP BY HOPPINGWINDOW(second, 300, 5)


**Explication**: cette requête génère des événements toutes les 5 secondes et affiche le dernier événement a été reçu avant. [Saut de fenêtre] Durée de la (https://msdn.microsoft.com/library/dn835041.aspx "Fenêtre saut - Azure flux Analytique") détermine nombre de jours passés ressemblera la requête pour rechercher le dernier événement (300 secondes dans cet exemple).


## <a name="get-help"></a>Obtenir de l’aide
Pour obtenir une aide supplémentaire, essayez de notre [forum Azure flux Analytique](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>Étapes suivantes

- [Présentation des flux de données Azure Analytique](stream-analytics-introduction.md)
- [Commencer à utiliser Azure flux Analytique](stream-analytics-get-started.md)
- [Échelle Azure flux Analytique travaux](stream-analytics-scale-jobs.md)
- [Référence du langage flux Azure Analytique requête](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Référence de l’API REST gestion Analytique flux Azure](https://msdn.microsoft.com/library/azure/dn835031.aspx)
 

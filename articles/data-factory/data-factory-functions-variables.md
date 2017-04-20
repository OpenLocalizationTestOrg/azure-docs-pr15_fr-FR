<properties 
    pageTitle="Fonctions d’usine de données et les Variables système | Microsoft Azure" 
    description="Fournit la liste des variables système et les fonctions d’usine données Azure" 
    documentationCenter="" 
    authors="sharonlo101" 
    manager="jhubbard" 
    editor="monicar"
    services="data-factory"
/>

<tags 
    ms.service="data-factory" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/11/2016" 
    ms.author="shlo"/>

# <a name="azure-data-factory---functions-and-system-variables"></a>Données Azure Factory - Variables système et fonctions
Cet article fournit des informations sur les fonctions et variables pris en charge par Azure Data Factory.
  
## <a name="data-factory-system-variables"></a>Variables de système de données par défaut

Nom d’une variable | Description | Étendue d’un objet | JSON étendues et exemples d’utilisation
------------- | ----------- | ------------ | ------------------------
WindowStart | Début de l’intervalle de temps pour exécuter la fenêtre d’activité actuelle | activité | <ol><li>Spécifier des requêtes de sélection de données. Consultez les articles de connecteur référencées dans l’article [Activités de déplacement des données](data-factory-data-movement-activities.md) .</li><li>Passer des paramètres au script Hive (exemple ci-dessus).</li>
WindowEnd | Fin de l’intervalle de temps pour exécuter la fenêtre d’activité actuelle | activité | identique à celui ci-dessus.
SliceStart | Début de l’intervalle de temps pour le secteur de données en cours de production | activité<br/>jeu de données | <ol><li>Spécifier les chemins d’accès de dossier dynamique et les noms de fichiers lorsque vous travaillez avec des [Objets Blob Azure](data-factory-azure-blob-connector.md) et des [datasets de système de fichiers](data-factory-onprem-file-system-connector.md).</li><li>Spécifier les dépendances d’entrée avec les fonctions d’usine de données dans la collection d’entrées activité.</li></ol>
SliceEnd | Fin de l’intervalle de temps pour le secteur de données actuel en cours de production | activité<br/>jeu de données | identique à celle ci-dessus. 

> [AZURE.NOTE] Actuellement usine données exige que la planification spécifiée dans l’activité exactement à la planification spécifiée dans la disponibilité du jeu de données de sortie. Cela signifie que WindowStart, WindowEnd et SliceStart et SliceEnd sont toujours mappent à la même période et un secteur de sortie unique.
 
## <a name="data-factory-functions"></a>Fonctions d’usine de données 

Vous pouvez utiliser des fonctions dans usine de données avec au-dessus de variables système mentionnées dans les cas suivants :

1.  Spécification de requêtes de sélection de données (voir les articles de connecteur référencées par l’article [Activités de déplacement des données](data-factory-data-movement-activities.md) .

    La syntaxe pour appeler une fonction d’usine de données est : ** $$ ** pour les requêtes de sélection de données et d’autres propriétés de l’activité et les jeux de données.  
2. Spécifier les dépendances d’entrée avec les fonctions d’usine données dans activité entrées collection de sites (voir l’exemple ci-dessus).

    $$ n’est pas nécessaire pour spécifier les expressions de dépendance d’entrée.   

Dans l’exemple suivant, propriété **sqlReaderQuery** dans un fichier JSON est affectée à une valeur renvoyée par la fonction **Text.Format** . Cet exemple utilise également une variable système nommée **WindowStart**, qui représente l’heure de début de l’activité exécutée fenêtre.
    
    {
        "Type": "SqlSource",
        "sqlReaderQuery": "$$Text.Format('SELECT * FROM MyTable WHERE StartTime = \\'{0:yyyyMMdd-HH}\\'', WindowStart)"
    }

### <a name="functions"></a>Fonctions

Les tableaux suivants répertorient toutes les fonctions d’Azure Data Factory :

Catégorie | Fonction | Paramètres | Description
-------- | -------- | ---------- | ----------- 
Heure | AddHours(X,Y) | X : date/heure <br/><br/>Y : int | Ajoute des heures Y à X une heure précise. <br/><br/>Exemple : 9/5/2013 12:00:00 PM + 2 heures = 9/5/2013 2:00:00 PM
Heure | AddMinutes(X,Y) | X : date/heure <br/><br/>Y : int | Ajoute Y minutes à X.<br/><br/>Exemple : 9/15/2013 12:00:00 PM + 15 minutes = 9/15/2013 12:15:00 PM
Heure | StartOfHour(X) | X : date/heure | Obtient l’heure de début de l’heure représentée par le composant heure de X. <br/><br/>Exemple : StartOfHour de 15/9/2013 05:10:23 PM est 15/9/2013 05:00:00 PM
Date | AddDays(X,Y) | X : date/heure<br/><br/>Y : int | Permet d’ajouter des jours de Y à X.<br/><br/>Exemple : 9/15/2013 12:00:00 PM + 2 jours = 9/17/2013 12:00:00 PM
Date | AddMonths(X,Y) | X : date/heure<br/><br/>Y : int | Ajoute Y mois à X.<br/><br/>Exemple : 9/15/2013 12:00:00 PM + 1 mois = 10/15/2013 12:00:00 PM 
Date | AddQuarters(X,Y) | X : date/heure <br/><br/>Y : int | Ajoute Y * 3 mois à X.<br/><br/>Exemple : 9/15/2013 12:00:00 PM + 1 trimestre = 12/15/2013 12:00:00 PM
Date | AddWeeks(X,Y) | X : date/heure<br/><br/>Y : int | Ajoute Y * 7 jours pour X<br/><br/>Exemple : 9/15/2013 12:00:00 PM + 1 semaine = 9/22/2013 12:00:00 PM
Date | AddYears(X,Y) | X : date/heure<br/><br/>Y : int | Ajoute Y ans à X.<br/><br/>Exemple : 9/15/2013 12:00:00 PM + 1 année = 9/15/2014 12:00:00 PM
Date | Day(X) | X : date/heure | Obtient le composant jour de X.<br/><br/>Exemple : Jour 9/15/2013 12:00:00 PM est 9. 
Date | DayOfWeek(X) | X : date/heure | Obtient le jour du composant de semaine de X.<br/><br/>Exemple : Jour de la semaine de 9/15/2013 12:00:00 PM est dimanche.
Date | DayOfYear(X) | X : date/heure | Obtient le jour de l’année représentée par le composant année de X.<br/><br/>Exemples :<br/>1/12/2015 : jour 335 / 2015<br/>31/12/2015 : jour 365 / 2015<br/>31/12/2016 : jour 366 de 2016 (année bissextile)
Date | DaysInMonth(X) | X : date/heure | Obtient les jours du mois représenté par le composant "mois" du paramètre X.<br/><br/>Exemple : JoursDansLeMois de 9/15/2013 sont 30 car il existe des 30 derniers jours dans le mois de septembre.
Date | EndOfDay(X) | X : date/heure | Obtient la date-heure qui représente la fin de la journée (composant "jour") de X.<br/><br/>Exemple : EndOfDay de 15/9/2013 05:10:23 PM est 15/9/2013 11:59:59 PM.
Date | EndOfMonth(X) | X : date/heure | Obtient la fin de mois représenté par composant "mois" du paramètre X. <br/><br/>Exemple : EndOfMonth de 15/9/2013 05:10:23 PM est 30/9/2013 11:59:59 PM (heure date qui représente la fin du mois de septembre)
Date | StartOfDay(X) | X : date/heure | Obtient le début de la journée représenté par le composant jour du paramètre X.<br/><br/>Exemple : StartOfDay de 15/9/2013 05:10:23 PM est 15/9/2013 12:00:00 AM.
Date/heure | FROM(X) | X : chaîne | Analyser chaîne X à une date heure.
Date/heure | Ticks(X) | X : date/heure | Obtient les graduations propriété du paramètre X. Une graduation est égale à 100 nanosecondes. La valeur de cette propriété représente le nombre de graduations qui se sont écoulées depuis minuit 12:00:00, le 1er janvier 0001. 
Texte | Format(X) | X : variable chaîne | Met en forme le texte.

#### <a name="textformat-example"></a>Exemple de Text.Format

    "defines": { 
        "Year" : "$$Text.Format('{0:yyyy}',WindowStart)",
        "Month" : "$$Text.Format('{0:MM}',WindowStart)",
        "Day" : "$$Text.Format('{0:dd}',WindowStart)",
        "Hour" : "$$Text.Format('{0:hh}',WindowStart)"
    }

Voir les [chaînes de Format d’heure et de Date personnalisée](https://msdn.microsoft.com/library/8kb3ddd4.aspx) rubrique qui décrit les différentes options de mise en forme que vous pouvez utiliser (par exemple : yy / aaaa). 

> [AZURE.NOTE] Lorsque vous utilisez une fonction dans une autre fonction, vous n’avez pas besoin d’utiliser **$$** préfixe de la fonction interne. Par exemple : $$Text.Format (' eq PartitionKey \\' my_pkey_filter_value\\' et RowKey page \\' {0 : yyyy-MM-JJ HH : mm :}\\'', Time.AddHours (SliceStart, -6)). Dans cet exemple, notez que **$$** préfixe n’est pas utilisé pour la fonction **Time.AddHours** . 
  


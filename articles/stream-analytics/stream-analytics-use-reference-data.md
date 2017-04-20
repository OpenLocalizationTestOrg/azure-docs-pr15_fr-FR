<properties
    pageTitle="Utiliser les tables de données et la recherche de références dans flux Analytique | Microsoft Azure"
    description="Utiliser des données de référence dans une requête Analytique de flux de données"
    keywords="table de choix, les données de référence"
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

# <a name="using-reference-data-or-lookup-tables-in-a-stream-analytics-input-stream"></a>Utiliser des tableaux de données ou liste de choix référence dans un flux d’entrée flux Analytique

Données de référence (également appelé une table de choix) sont un jeu de données déterminée qui est statique ou ralentir modification par nature, utilisé pour effectuer une recherche ou pour faire correspondre à votre flux de données. Pour utiliser des données de référence dans votre travail Azure flux Analytique vous généralement utilise une [Jointure des données de référence](https://msdn.microsoft.com/library/azure/dn949258.aspx) dans votre requête. Flux Analytique utilise le stockage Blob Azure en tant que la couche de stockage pour les données de référence et avec une référence Azure Data Factory données peuvent être transformées et/ou copiées vers le stockage Blob Azure, pour une utilisation en tant que données de référence, à partir [d’un nombre quelconque de cloud basé et banques de données locale](../data-factory/data-factory-data-movement-activities.md). Données de référence équivaut à une séquence d’objets BLOB (défini dans la configuration d’entrée) dans l’ordre croissant de la date/heure spécifié dans le nom d’objets blob. Il **ne** prend en charge l’ajout à la fin de la séquence en utilisant une date/heure **supérieur** à celui spécifié par le dernier blob dans l’ordre.

## <a name="configuring-reference-data"></a>Configuration des données de référence

Pour configurer vos données de référence, vous devez d’abord créer une entrée est de type de **Données de référence**. Le tableau ci-dessous répertorie chaque propriété dont vous aurez besoin pour fournir lors de la création des données de référence d’entrée avec sa description :

<table>
<tbody>
<tr>
<td>Nom de la propriété</td>
<td>Description</td>
</tr>
<tr>
<td>Alias d’entrée</td>
<td>Un nom convivial qui servira de la requête de travail pour faire référence à cette entrée.</td>
</tr>
<tr>
<td>Compte de stockage</td>
<td>Le nom du compte de stockage où se trouvent vos fichiers blob. Si elle est dans le même abonnement en tant que flux Analytique travailler, vous pouvez le sélectionner dans la liste déroulante vers le bas.</td>
</tr>
<tr>
<td>Clé de compte de stockage</td>
<td>Le code secret associé au compte de stockage. Ceci est rempli automatiquement si le compte de stockage se trouve dans le même abonnement tant que votre tâche de flux Analytique.</td>
</tr>
<tr>
<td>Conteneur de stockage</td>
<td>Les conteneurs fournissent un regroupement logique pour les objets BLOB stockées dans le service Microsoft Azure Blob. Lorsque vous téléchargez un blob au service Blob, vous devez spécifier un conteneur pour ce blob.</td>
</tr>
<tr>
<td>Chemin d’accès au modèle</td>
<td>Le chemin d’accès de fichier utilisé pour localiser votre BLOB dans le conteneur spécifié. Dans le chemin d’accès, vous pouvez choisir de spécifier une ou plusieurs instances des 2 variables suivantes :<BR>{date}, {time}<BR>Exemple 1 : products/{date}/{time}/product-list.csv<BR>Exemple 2 : products/{date}/product-list.csv
</tr>
<tr>
<td>Format de date [facultatif]</td>
<td>Si vous avez utilisé {date} dans le modèle de chemin d’accès que vous avez spécifié, vous pouvez sélectionner le format de date dans lequel vos fichiers sont organisés dans la liste déroulante des formats pris en charge. Exemple : AAAA/MM/JJ</td>
</tr>
<tr>
<td>Format d’heure [facultatif]</td>
<td>Si vous avez utilisé {time} dans le modèle de chemin d’accès que vous avez spécifié, vous pouvez sélectionner le format d’heure dans lequel vos fichiers sont organisés dans la liste déroulante des formats pris en charge. Exemple : HH</td>
</tr>
<tr>
<td>Format de sérialisation événement</td>
<td>Pour vous assurer que vos requêtes fonctionnent comme prévu, Analytique de flux de données a besoin de savoir quel format de sérialisation vous utilisez pour flux de données entrant. Pour les données de référence, les formats pris en charge sont CSV et JSON.</td>
</tr>
<tr>
<td>Codage</td>
<td>UTF-8 est le format de codage pris en charge uniquement pour le moment</td>
</tr>
</tbody>
</table>

## <a name="generating-reference-data-on-a-schedule"></a>Générer des données sur un planning de référence

Si vos données de référence sont une série de données variation lente, puis prennent en charge d’actualisation des données sont activées en spécifiant un motif de chemin d’accès dans la configuration d’entrée en utilisant la date {} de référence et les jetons substitution {temps}. Flux Analytique chercher les définitions de données de référence mis à jour en fonction de ce modèle de chemin d’accès. Par exemple, un motif de `sample/{date}/{time}/products.csv` avec un format de date de **« AAAA-MM-JJ »** et une heure format **«** hh : mm » indique à flux Analytique à Décrochez le blob mis à jour `sample/2015-04-16/17:30/products.csv` à 17 h 30 avril 2015 16 UTC fuseau horaire.

> [AZURE.NOTE] Actuellement tâches de flux de données Analytique recherchez l’actualisation blob uniquement lorsque l’ordinateur passe à l’heure codée dans le nom d’objets blob. Par exemple la tâche recherche `sample/2015-04-16/17:30/products.csv` dès que possible, mais aucune version antérieure à 17 h 30 avril 2015 16 UTC fuseau horaire. Il sera *jamais* apparence pour un fichier avec une heure au format antérieure à celle dernière découvertes.
> 
> Par exemple une fois que le travail recherche l’objets blob `sample/2015-04-16/17:30/products.csv` il ignore les fichiers dont la date au format 17 h 30 avril 2015 16 au plus tôt si un arrive au plus tard `sample/2015-04-16/17:25/products.csv` blob est créé dans le même conteneur la tâche ne l’utilisez pas.
> 
> Même si `sample/2015-04-16/17:30/products.csv` se produit uniquement à 10 h 03 avril 2015 16 mais aucun blob avec une date antérieure n’est présent dans le conteneur, le travail utiliseront ce fichier en commençant à 10:03 PM avril 2015 16 et les données de référence précédente jusque là.
> 
> Une exception à cette règle est lorsque la tâche doit nouveau traiter les données dans le temps ou lorsque la tâche est d’abord démarré. Au moment de démarrer que le travail recherche la plus récent blob produit avant le travail heure de début spécifiée. Pour ce faire pour vous assurer qu’il existe un jeu de données de référence **non vide** lorsque la tâche démarre. Si un ne peut pas être trouvé, la tâche affiche le diagnostic suivant : `Initializing input without a valid reference data blob for UTC time <start time>`.


[Azure Data Factory](https://azure.microsoft.com/documentation/services/data-factory/) peut servir à organiser la tâche de la création des mise à jour des objets BLOB requises Analytique de flux de données pour mettre à jour les définitions de données de référence. Données Factory est un service d’intégration de données sur le nuage qui orchestre et automatisation déplacement et transformation de données. Données usine prend en charge [qui se connecte à un grand nombre de basé sur le cloud et banques de données locale](../data-factory/data-factory-data-movement-activities.md) et transfert de données facilement à intervalles réguliers que vous spécifiez. Pour plus d’informations et des instructions étape par étape sur la façon de configurer un pipeline de données par défaut pour générer des données de référence pour flux Analytique qui est actualisé sur une planification prédéfinie, consultez cet [exemple GitHub](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/ReferenceDataRefreshForASAJobs).

## <a name="tips-on-refreshing-your-reference-data"></a>Conseils sur l’actualisation de vos données de référence ##

1. Remplacement de référence des objets BLOB ne va pas entraîner Analytique flux recharger le blob et dans certains cas, il peut entraîner l’échec du travail. Il est recommandé de modifier les données de référence pour ajouter un nouveau blob en utilisant le même conteneur et motif de chemin d’accès défini dans l’entrée de travail et utiliser une date/heure **supérieur** à celui spécifié par le dernier blob dans l’ordre.
2.  Données de référence des objets BLOB ne sont pas classées par heure « Dernière modification » de l’objet blob, mais uniquement par l’heure et la date spécifiée dans le blob nommer à l’aide de la date {} et {temps} substitutions.
3.  À plusieurs reprises qu'une tâche doit revenir en arrière dans le temps, par conséquent référence des objets BLOB ne doivent pas être modifiés ou supprimés.

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

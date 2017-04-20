<properties
   pageTitle="Base de données Microsoft Power BI - connexion à une source de données"
   description="Power BI incorporé, vous connecter aux sources de données"
   services="power-bi-embedded"
   documentationCenter=""
   authors="guyinacube"
   manager="erikre"
   editor=""
   tags=""/>
<tags
   ms.service="power-bi-embedded"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="powerbi"
   ms.date="10/04/2016"
   ms.author="asaxton"/>

# <a name="connect-to-a-data-source"></a>Se connecter à une source de données

Avec **Power BI incorporé**, vous pouvez incorporer des rapports dans votre propre application. Lorsque vous incorporez un rapport Power BI dans votre application, le rapport connecte aux données sous-jacentes en **important** une copie des données ou en **connexion directement** à la source de données à l’aide de **DirectQuery**.

Voici les différences entre l’utilisation **d’importation** et **DirectQuery**.

|Importation | DirectQuery
|---|---
|Tables, colonnes *et les données* sont importées ou copiées dans le jeu de données du rapport. Pour afficher les modifications apportées aux données sous-jacentes, vous devez actualiser ou importer un dataset complète et actif à nouveau.|Uniquement les *tables et les colonnes* sont importées ou copiées dans le jeu de données du rapport. Vous toujours Affichez les données les plus récentes.
Avec Power BI incorporé, vous pouvez utiliser DirectQuery des sources de données cloud mais pas locale des sources de données pour le moment.

## <a name="benefits-of-using-directquery"></a>Avantages de l’utilisation de DirectQuery

Il existe deux principaux avantages lors de l’utilisation de **DirectQuery**:

   -    **DirectQuery** vous permet de créer des visualisations sur jeux de données volumineux, où il dans le cas contraire serait fonctionner première importation toutes les données.
   -    Sous-jacent des modifications aux données peut nécessiter une actualisation des données, et pour certains rapports, qu’il soit nécessaire pour afficher des données en cours peut nécessiter des transferts de données volumineux, rendre les données réimportez fonctionner. En revanche, **DirectQuery** signale toujours utiliser les données en cours.

## <a name="limitations-of-directquery"></a>Limitations de DirectQuery

   Il existe quelques limitations à l’aide de **DirectQuery**:

   -    Toutes les tables doivent provenir d’une seule base de données.
   -    Si la requête est trop complexe, une erreur est générée. Pour corriger l’erreur, vous devez refactoriser la requête afin qu’elle soit moins complexe. Si la quuery doit être complexe, vous avez besoin importer les données au lieu d’utiliser **DirectQuery**.
   -    Filtrage de relation est limité à une seule direction, plutôt que les deux sens.
   -    Vous ne pouvez pas modifier le type de données d’une colonne.
   -    Par défaut, les limitations sont placées expressions DAX autorisés dans les mesures. Voir [DirectQuery et les mesures](#measures).

<a name="measures"/>
## <a name="directquery-and-measures"></a>DirectQuery et les mesures

Pour vous assurer que les requêtes envoyées à la source de données sous-jacente présentent des performances acceptables, des restrictions sur les mesures. Lorsque l’utilisation de **Power BI Desktop**, les utilisateurs expérimentés vous pouvez choisir d’ignorer cette limitation en choisissant **fichier > Options et paramètres > Options**. Dans la boîte de dialogue **Options** , choisissez **DirectQuery**, puis sélectionnez l’option **Autoriser les mesures non restreint en mode DirectQuery**. Lorsque cette option est sélectionnée, toute expression DAX qui n’est valide pour une mesure peut être utilisée. Utilisateurs doivent tenir compte ; Toutefois, que certaines expressions qui effectuer très bien lorsque les données sont importées, peut entraîner dans les requêtes très lentes à la source et en mode **DirectQuery** . 

## <a name="see-also"></a>Voir aussi
- [Prise en main avec Microsoft Power BI incorporé](power-bi-embedded-get-started.md)
- [Power BI Desktop](https://powerbi.microsoft.com/documentation/powerbi-desktop-get-the-desktop/)

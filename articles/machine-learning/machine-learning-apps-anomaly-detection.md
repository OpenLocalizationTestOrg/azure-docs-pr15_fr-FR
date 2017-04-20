<properties 
    pageTitle="Application d’apprentissage automatique : Service de détection des anomalies | Microsoft Azure" 
    description="API de détection des anomalies est un exemple intégré avec Microsoft Azure Machine Learning qui détecte anomalies dans les données de série de date avec des valeurs numériques sont espacées de façon uniforme dans le temps." 
    services="machine-learning" 
    documentationCenter="" 
    authors="alokkirpal" 
    manager="jhubbard"
    editor="cgronlun" /> 

<tags 
    ms.service="machine-learning" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.workload="multiple" 
    ms.date="10/11/2016" 
    ms.author="alokkirpal"/>


# <a name="machine-learning-anomaly-detection-service"></a>Service de détection des anomalies machine apprentissage#

##<a name="overview"></a>Vue d’ensemble

[API de détection des anomalies](https://datamarket.azure.com/dataset/aml_labs/anomalydetection) est un exemple créé avec apprentissage automatique Azure qui détecte anomalies dans les données de série de date avec des valeurs numériques sont espacées de façon uniforme dans le temps. 

Cette API détecte les types suivants de modèles anormales dans les données de série de date :

* **Positifs et négatifs tendances**: par exemple, quand surveiller l’utilisation de mémoire de l’informatique hausse peut s’avérer utiles comme il peut s’agir d’une perte de mémoire

* **Modifications dans la plage de valeurs dynamique**: par exemple, lorsque vous analysez les exceptions levées par un service cloud, toutes les modifications dans la plage de valeurs dynamique indique une instabilité dans l’état du service, et

* **Dépasse et devient**: par exemple, lorsque vous analysez le nombre d’échecs de connexion dans un service ou nombre d’extractions dans un site de commerce, pointes ou creux indique un comportement anormal.

Ces détecteurs apprentissage machine suivre ces modifications de valeurs fil de signaler les modifications en cours dans leurs valeurs en tant que scores anomalie. Ils ne nécessitent pas de réglage du seuil ad hoc et leurs scores peuvent être utilisés pour contrôler le taux de faux positifs. La détection des anomalies API est utile dans plusieurs scénarios comme service Surveillance grâce au suivi des indicateurs de performance clés dans le temps, l’utilisation de surveillance via statistiques telles que le nombre de recherches, les numéros de clics, l’analyse des performances par le biais des compteurs comme mémoire, processeur, fichier lit, etc. au fil du temps.

L’offre de détection des anomalies offre des outils utiles pour vous aider à démarrer. 

* L' [application web](http://anomalydetection-aml.azurewebsites.net/) vous permet d’évaluer et visualiser les résultats de détection des anomalies API sur vos données. 

* L' [exemple de code](http://adresultparser.codeplex.com/) montre comment accéder à l’API et analyser les résultats en c# par programme.

>[AZURE.NOTE]
>Essayez de **solution informatique anomalie Insights** optimisé par [cette API](https://datamarket.azure.com/dataset/aml_labs/anomalydetection)
>
>Pour obtenir cette solution de bout en bout déployée à votre abonnement Azure <a href="https://gallery.cortanaintelligence.com/Solution/Anomaly-Detection-Pre-Configured-Solution-1" target="_blank"> **Commencez ici >**</a>


##<a name="api-definition"></a>Définition de l’API

Le service fournit une API basée sur REST sur HTTPS qui peuvent être utilisés dans les différentes façons dont un site ou une application mobile, R, Python, Excel, etc..  Vous envoyez vos données de série de temps à ce service via un appel API REST, et elle s’exécute une combinaison de trois anomalie types décrits précédemment. Le service s’exécute sur la plateforme d’apprentissage automatique Azure, qui s’adapte à vos besoins professionnels en toute transparence et fournit des SLA.

###<a name="headers"></a>En-têtes
Veillez à inclure les en-têtes corrects dans la demande, qui doit se présenter comme suit :

    Authorization: Basic <creds>
    Accept: application/json
               
    Where <creds> = ConvertToBase64(“AccountKey:” + yourActualAccountKey);  

Vous pouvez trouver votre clé de compte à partir de votre compte sur le [Marché des données Azure](https://datamarket.azure.com/account/keys). 

###<a name="score-api"></a>API score

L’API Score est utilisé pour l’exécution de détection des anomalies sur série de données non saisonnier heure. L’API s’exécute un certain nombre de DETECTEURS d’anomalies sur les données et retourne leurs scores anomalie. L’illustration suivante montre un exemple d’anomalies permettant de détecter l’API Score. Cette série de temps comporte les modifications au niveau distinctes 2 et 3 pointes. Les points rouge indiquent l’heure à laquelle la modification du niveau est détectée, tandis que les points noirs affichent les pointes détectés.

![API score][1]
    
**URL**

    https://api.datamarket.azure.com/data.ashx/aml_labs/anomalydetection/v2/Score 

**Corps de la requête exemple**

Dans la demande ci-dessous, nous envoyer une série de temps (comme illustrée tronquée) avec des points de données à partir de 3/1/2016 via 3/10/2016 et les paramètres de détecteurs Pique-notes à l’API pour détecter si un de ces points de données est anormal. 

    {
      "data": [
        [ "9/21/2014 11:05:00 AM", "3" ],
        [ "9/21/2014 11:10:00 AM", "9.09" ],
        [ "9/21/2014 11:15:00 AM", "0" ]
      ],
      "params": {
        "tspikedetector.sensitivity": "3",
        "zspikedetector.sensitivity": "3",
        "trenddetector.sensitivity": "3.25",
        "bileveldetector.sensitivity": "3.25",
        "postprocess.tailRows": "2"
      }
    }

La réponse à cette seront : 

    {
      "odata.metadata":"https://api.datamarket.azure.com/data.ashx/aml_labs/anomalydetection/v2/$metadata#AnomalyDetection.FrontEndService.Models.AnomalyDetectionResult",
      "ADOutput":"{
        "ColumnNames":["Time","Data","TSpike","ZSpike","rpscore","rpalert","tscore","talert"],
        "ColumnTypes":["DateTime","Double","Double","Double","Double","Int32","Double","Int32"],
        "Values":[
          ["9/21/2014 11:10:00 AM","9.09","0","0","-1.07030497733224","0","-0.884548154298423","0"],
          ["9/21/2014 11:15:00 AM","0","0","0","-1.05186237440962","0","-1.173800281031","0"]
        ]
      }"
    }

###<a name="scorewithseasonality-api"></a>ScoreWithSeasonality API

L’API ScoreWithSeasonality est utilisé pour l’exécution de détection des anomalies sur série de temps qui ont saisonnier. Cette API est utile pour détecter des écarts dans saisonnier.  

La figure suivante montre un exemple d’anomalies détectées dans une série chronologique saisonnier. La série chronologique a un pique-notes (le 1er point noir), deux creux (le point noir 2ème et une à la fin) et une modification au niveau (point rouge). Notez que les deux le fondu au milieu de la série chronologique et la modification du niveau sont uniquement perceptibles après que saisonniers composants sont supprimés de la série.

![Saisonnalité API][2]

**URL**

    https://api.datamarket.azure.com/data.ashx/aml_labs/anomalydetection/v2/ScoreWithSeasonality 

**Corps de la requête exemple**

Dans la demande ci-dessous, nous envoyer une série de temps (comme illustrée tronquée) avec des points de données à partir de 3/1/2016 via 3/10/2016 et les paramètres à l’API pour détecter si un de ces points de données est anormal. 

    {
      "data": [
        [ "9/21/2014 11:10:00 AM", "9" ],
        [ "9/21/2014 11:15:00 AM", "12" ],
        [ "9/21/2014 11:20:00 AM", "10" ]
      ],
      "params": {
        "preprocess.aggregationInterval": "0",
        "preprocess.aggregationFunc": "mean",
        "preprocess.replaceMissing": "lkv",
        "postprocess.tailRows": "1",
        "zspikedetector.sensitivity": "3",
        "tspikedetector.sensitivity": "3",
        "upleveldetector.sensitivity": "3.25",
        "bileveldetector.sensitivity": "3.25",
        "trenddetector.sensitivity": "3.25",
        "detectors.historywindow": "500",
        "seasonality.enable": "true",
        "seasonality.transform": "deseason",
        "seasonality.numSeasonality": "1"
      }
    }

La réponse à cette seront : 

    {
        "odata.metadata": "https://api.datamarket.azure.com/data.ashx/aml_labs/anomalydetection/v2/$metadata#AnomalyDetection.FrontEndService.Models.AnomalyDetectionResult",
        "ADOutput": "{
            "ColumnNames":["Time","OriginalData","ProcessedData","TSpike","ZSpike","PScore","PAlert","RPScore","RPAlert","TScore","TAlert"],
            "ColumnTypes":["DateTime","Double","Double","Double","Double","Double","Int32","Double","Int32","Double","Int32"],
            "Values":[
                ["9/21/201411: 20: 00AM","10","10","0","0","-1.30229513613974","0","-1.30229513613974","0","-1.173800281031","0"]
            ]
        }"
    }

###<a name="detectors"></a>Détecteurs

La détection des anomalies API prend en charge détecteurs 3 grandes catégories. Vous trouverez plus d’informations sur les paramètres d’entrée spécifiques et sorties pour chaque détection dans le tableau suivant.

|Catégorie de détection|Détection|Description|Paramètres d’entrée|Sorties
|---|---|---|---|---|
|Détecteurs Pique-notes|Détection TSpike|Détecter les pointes et DIP basée sur les valeurs complètement proviennent les premier et troisième quartiles|*tspikedetector.sensitivity :* prend valeur entière dans la plage 1-10, par défaut : 3 ; Des valeurs élevées détecte plusieurs valeurs extrêmes facilitant moins sensibles|TSpike : binaires valeurs – 1 si un pique-notes/fondu est détecté, « 0 » dans le cas contraire|
||Détection ZSpike|Détecter les pointes et DIP dépendent de la distance à laquelle les points de données à leur moyenne arithmétique|*zspikedetector.sensitivity :* prendre entier dans la plage 1-10, par défaut : 3 ; Des valeurs élevées détecte plusieurs valeurs extrêmes rendant moins sensibles|ZSpike : binaires valeurs – 1 si un pique-notes/fondu est détecté, « 0 » dans le cas contraire|
|Détection de tendance lent|Détection de tendance lent|Détecter lente tendance positive selon le critère de diffusion ensemble|*trenddetector.sensitivity :* seuil score général de détection (par défaut : 3,25, 3,25 – 5 est une plage acceptable pour sélectionner ce ; Est élevé moins sensibles)|TScore : flottante nombre qui représente la note anomalie pour tendance|
|Modification du niveau détecteurs|Détection de modification du niveau unidirectionnelle|Détecter vers le haut niveau modifier selon le critère de diffusion ensemble|*upleveldetector.sensitivity :* seuil score général de détection (par défaut : 3,25, 3,25 – 5 est une plage acceptable pour sélectionner ce ; Est élevé moins sensibles)|PScore : nombre qui représente score anomalie vers le haut à flottante changement de niveau|
||Détection de changement de niveau bidirectionnelle|Détecter modification du niveau vers le haut et vers le bas selon le critère de diffusion ensemble|*bileveldetector.sensitivity :* seuil score général de détection (par défaut : 3,25, 3,25 – 5 est une plage acceptable pour sélectionner ce ; Est élevé moins sensibles)|RPScore : flottante nombre anomalie représentant la note pour vers le haut et vers le bas niveau modifier

###<a name="parameters"></a>Paramètres

Plus d’informations sur ces paramètres d’entrée sont répertoriées dans le tableau ci-dessous :

|Paramètres d’entrée|Description|Paramètre par défaut|Type|Plage valide|Plage suggérée|
|---|---|---|---|---|---|
|preprocess.aggregationInterval|Intervalle d’agrégation en secondes pour l’agrégation d’entrée série chronologique.|0 (aucune agrégation n’est effectuée)|nombre entier|0 : ignorer l’agrégation, > 0 dans le cas contraire|5 minutes pour un jour, dépendant de la série chronologique.
|preprocess.aggregationFunc|Fonction utilisée pour agréger des données dans le AggregationInterval spécifié|moyenne.|énumérée|somme, moyenne, longueur|N/A|
|preprocess.replaceMissing|Valeurs utilisées pour imputer données manquantes|lkv (dernière connue valeur)|énumérée|zéro, lkv, moyenne.|N/A|
|detectors.historyWindow|Historique (en nombre de points de données) utilisé pour le calcul du score anomalie|500|nombre entier|10-2000|Dépendant de la série chronologique.|
|upleveldetector.Sensitivity|Critère de diffusion de vers le haut niveau modifier détection. |3,25|Double|Aucun|3,25 5(Lesser values mean more sensitive)|
|bileveldetector.Sensitivity|Critère de diffusion de détection de modification du niveau bidirectionnelle. |3,25|Double|Aucun|3,25 5(Lesser values mean more sensitive)|
|trenddetector.Sensitivity|Critère de diffusion de détection de tendance positive. |3,25|Double|Aucun|3,25 5(Lesser values mean more sensitive)|
|tspikedetector.Sensitivity |Critère de diffusion de détection TSpike|3|nombre entier|1-10|3-5(Lesser values mean more sensitive)|
|zspikedetector.Sensitivity |Critère de diffusion de détection ZSpike|3|nombre entier|1-10 |3-5(Lesser values mean more sensitive)|
|seasonality.Enable|Si analyse saisonnalité doit être effectuée|Vrai|valeur booléenne|Vrai, faux|Dépendant de la série chronologique.|
|seasonality.numSeasonality |Nombre maximal de cycles périodiques, pour être détectées|1|nombre entier|1, 2|1-2|
|seasonality.Transform |Si saisonnier (et) tendance éléments doivent être déposés avant d’appliquer la détection des anomalies|deseason|énumérée|Aucun, deseason, deseasontrend|N/A|
|postprocess.tailRows |Numéro des plus récentes de points de données à conserver dans les résultats|0|nombre entier|0 (laisser tous les points de données), ou spécifier le nombre de points à garder dans les résultats|N/A|

###<a name="output"></a>Sortie
L’API s’exécute tous les détecteurs sur vos données de série de temps et retourne anomalie scores et indicateurs Pique-notes binaire pour chaque point dans le temps. Le tableau ci-dessous répertorie des sorties à partir de l’API. 

|Sorties|Description|
|---|---|
|Heure|Horodatages à partir des données brutes ou des données (et/ou) d’agrégé imputées si agrégation (et/ou) manquants imputation de données est appliquée|
|OriginalData|Si les valeurs de données brutes ou des données (et/ou) d’agrégé imputées agrégation (et/ou) manquants imputation de données est appliquée|
|ProcessedData|Une des opérations suivantes : <ul><li>Saisonnières série chronologique si saisonnalité significative a été détecté et deseason option est sélectionnée ;</li><li>saisonnières ajusté et redressé série chronologique si saisonnalité significative a été détectée et deseasontrend option est sélectionnée</li><li>Sinon, c’est différente de celle OriginalData</li>|
|TSpike|Indicateur binaire pour indiquer si un pique-notes est détectés par détection TSpike|
|ZSpike|Indicateur binaire pour indiquer si un pique-notes est détectés par détection ZSpike|
|Pscore|Un nombre flottant représentant score anomalie vers le haut au niveau de la modifier|
|Palert|valeur de 1/0 indiquant un niveau vers le haut modifier anomalie basée sur le critère de diffusion d’entrée|
|RPScore|Une nombre anomalie représentant flottante score général sur Modification du niveau bidirectionnel|
|RPAlert|valeur de 1/0 qui indique le niveau de bidirectionnelle est modifier anomalie basée sur le critère de diffusion d’entrée|
|TScore|Une nombre anomalie représentant flottante score général sur tendance positive|
|TAlert|valeur de 1/0 indiquant qu’est une anomalie tendance positive basée sur le critère de diffusion d’entrée|


Ce résultat peut être analysé à l’aide d’un [simple analyseur](https://adresultparser.codeplex.com/) : elle comporte des exemples de code qui montre comment se connecter à l’API et analyser la sortie. Les anomalies détectées pouvant affichées dans un tableau de bord et/ou transmis à des experts pour les actions de correction ou intégrés dans les systèmes de réservation.


[1]: ./media/machine-learning-apps-anomaly-detection/anomaly-detection-score.png
[2]: ./media/machine-learning-apps-anomaly-detection/anomaly-detection-seasonal.png

 

 

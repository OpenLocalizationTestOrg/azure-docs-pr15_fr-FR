<properties
    pageTitle="Utiliser les jeux de données d’exemple dans Machine apprentissage Studio | Microsoft Azure"
    description="Descriptions des ensembles de données utilisés dans les exemples de modèles inclus dans Studio ML. Vous pouvez utiliser ces jeux de données d’exemple pour vos expériences."
    services="machine-learning"
    documentationCenter=""
    authors="garyericson"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="machine-learning"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/16/2016"
    ms.author="garye"/>


# <a name="use-the-sample-data-sets-in-azure-machine-learning-studio"></a>Utiliser les jeux de données d’exemple dans Azure Machine apprentissage Studio

[top]: #machine-learning-sample-datasets

Lorsque vous créez un nouvel espace de travail dans l’apprentissage automatique Azure, un certain nombre de jeux de données d’exemple expériences est inclus par défaut. La plupart de ces jeux de données exemple sont utilisées par les exemples de modèles dans la [Galerie d’Intelligence Azure Cortana](http://gallery.cortanaintelligence.com/)et d’autres personnes sont incluses comme des exemples de différents types de données généralement utilisées dans l’apprentissage automatique.

Certaines de ces jeux de données sont disponibles dans le stockage Blob Azure. Pour ces ensembles de données le tableau suivant fournit un lien direct. Vous pouvez utiliser ces jeux de données dans vos expériences à l’aide de l' [Importation de données] [ import-data] module.

Le reste de ces jeux de données d’exemple sont répertoriés sous **Jeux de données enregistré** dans la palette de module à gauche de la zone de dessin expérience lorsque vous ouvrez ou créez une nouvelle expérience dans Studio ML.
Vous pouvez utiliser un de ces jeux de données dans votre propre expérience en le faisant glisser vers la zone de travail expérience.


<!--
For a list of sample experiments available in ML Studio, see [Machine Learning Sample Experiments][sample-experiments].

[sample-experiments]: machine-learning-sample-experiments.md
-->

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]


<table>

<tr>
  <th align=left>Nom du jeu de données</th>
  <th align=left>Description du jeu de données</th>
</tr>

<tr>
  <td valign=top>Gros dataset recensement revenus binaire Classification</td>
  <td valign=top>
Un sous-ensemble de la base de données recensement 1994, à l’aide de travail adultes âgés de 16 avec un indice revenu ajusté > 100.<p> </p><b>Syntaxe :</b> classer les personnes qui utilisent des données démographiques pour prévoir si une personne reçoit plus de 50 Ko an.<p> </p><b>Recherche :</b> Kohavi, r, Becker, b, (1996). Machine UCI référentiel <a href="http://archive.ics.uci.edu/ml">http://archive.ics.uci.edu/ml</a>d’apprentissage. Irvine, Californie : Université de Californie, scolaire des informations et informatique </td>
</tr>

<tr ID=airport-codes-dataset>
  <td valign=top>Jeu de données de Codes d’aéroport</td>
  <td valign=top>
Codes d’aéroport français.<p> </p>Ce groupe de données contient une ligne pour chaque aéroport US, fournir le numéro d’identification aéroport et le nom ainsi que la ville emplacement et l’état.
  </td>
</tr>

<tr>
  <td valign=top>Données de prix voiture (vierge)</td>
  <td valign=top>
Informations sur les voitures par marque et le modèle, y compris le prix, fonctionnalités telles que le nombre de cylindres et MPG, mais aussi un score du risque d’assurance.<p> </p>Le score risque est initialement associées à des prix automatique et puis ajustées pour réel risque associé à un processus connu pour actuaires comme symboling. Une valeur de + 3 indique que l’automatique est risquée, et une valeur de -3 qu’elle est probablement assez fiable.<p> </p><b>Syntaxe :</b> prévoir le score de risque en fonctionnalités, à l’aide de régression ou MULTIVARIABLES classification. <p> </p><b>Recherche :</b> Schlimmer, J.C. (1987). Machine UCI référentiel <a href="http://archive.ics.uci.edu/ml">http://archive.ics.uci.edu/ml</a>d’apprentissage. Irvine, Californie : Université de Californie, scolaire des informations et informatique </td>
</tr>

<tr ID=bike-rental-uci-dataset>
  <td valign=top>Vélo location UCI dataset</td>
  <td valign=top>
Dataset UCI vélo location basé sur des données réelles à partir de la société Bikeshare majuscule qui met à jour un réseau location vélo de Washington DC.<p> </p>Le jeu de données comporte une ligne pour chaque heure de chaque jour de 2011 et 2012, pour un total de 17,379 lignes. La plage de toutes les heures des mensualités vélo est de 1 à 977.

  </td>
</tr>

<tr ID=bill-gates-rgb-image>
  <td valign=top>Image RVB de Bill Gates</td>
  <td valign=top>
Fichier d’image accessible au public convertis en données CSV.<p> </p>Le code de conversion de l’image est fourni dans la page de détails de modèle <strong>quantification à l’aide de moyens K cluster des couleurs</strong> .
  </td>
</tr>

<tr>
  <td valign=top>Données de don contrôleur</td>
  <td valign=top>
Un sous-ensemble de données à partir de la base de données de donneurs contrôleur du centre de Hsin Chu de Service contrôleur Transfusion ville, Taïwan.<p> </p>Données donneurs incluent les mois depuis le dernier don), fréquence, ou le nombre total de dons, l’heure depuis le dernier don et montant de contrôleur donné.<p> </p><b>Syntaxe :</b> l’objectif est de prédire via classification si le donateur dons contrôleur en mars 2007, où 1 indique un donneur pendant la période cible et 0 un donneur-non. <p> </p><b>Recherche :</b> ' YA ', parties, (2008). Machine UCI référentiel <a href="http://archive.ics.uci.edu/ml">http://archive.ics.uci.edu/ml</a>d’apprentissage. Irvine, Californie : Université de Californie, scolaire des informations et informatique <p> </p>' YA ', j’ai-Cheng, Yang, pivot-Jang et Ting, appuyer sur-Ming, « découverte de connaissances sur le modèle de mode de fonctionnalités réduites à l’aide de la séquence de Bernoulli « systèmes experts avec les Applications, 2008, <a href="http://dx.doi.org/10.1016/j.eswa.2008.07.018">http://dx.doi.org/10.1016/j.eswa.2008.07.018</a>
  </td>
</tr>

<tr ID=book-reviews-from-amazon>
  <td valign=top>Critiques à partir d’Amazon</td>
  <td valign=top>
Révisions de livres d’Amazon, prises depuis le site Web amazon.com par les chercheurs de l’Université de Pennsylvanie (<a href="http://www.cs.jhu.edu/~mdredze/datasets/sentiment/">opinion</a>). Voir l’article de recherche « Biographies, films indiens, zones de flèche et mélangeurs : Adaptation de domaine pour la Classification opinion » par John Blitzer, Mark Dredze et Fernando Pereira ; Association de calcul linguistiques (et) 2007.<p> </p>Le jeu de données d’origine a 975K des révisions avec classements 1, 2, 3, 4 ou 5. Les évaluations ont été écrits en anglais et sont à partir de la période 1997-2007. Le jeu de données a été échantillonnée vers le bas pour des révisions 10 Ko.
  </td>
</tr>

<tr>
  <td valign=top>Données sur le cancer du sein</td>
  <td valign=top>
Une des trois liées au cancer jeux de données fournies par l’Institute oncologie apparaissant fréquemment dans la documentation apprentissage machine. Combine les informations de diagnostic avec des fonctionnalités d’analyse de laboratoire d’environ 300 échantillons de tissu.<p> </p><b>Syntaxe :</b> classez le type de cancer, en fonction de 9 attributs, certaines d'entre elles sont linéaires, mais par catégorie. <p> </p><b>Recherche :</b> O.L. Wohlberg, W.H., rue, W.N. et Mangasarian, (1995). Machine UCI référentiel <a href="http://archive.ics.uci.edu/ml">http://archive.ics.uci.edu/ml</a>d’apprentissage. Irvine, Californie : Université de Californie, scolaire des informations et informatique </td>
</tr>

<tr ID=breast-cancer-features>
  <td valign=top>Fonctionnalités de Cancer du sein <td valign=top>
Le jeu de données contient des informations pour les régions suspect 102K (candidats) des images à rayons x, chacun décrit par 117 fonctionnalités. Les fonctionnalités sont propriétaires et leur signification n’est pas révélée par les créateurs de dataset (Siemens la santé). 
  </td>
</tr>

<tr ID=breast-cancer-info>
  <td valign=top>Informations de Cancer du sein</td>
  <td valign=top>
Le dataset contient des informations supplémentaires pour chaque zone suspect d’image rayons x. Chaque exemple fournit des informations (par exemple, étiquette, patients ID, coordonnées du correctif par rapport à l’image entière) sur le numéro de ligne correspondant au sein Cancer fonctionnalités dataset. Les patients contient un nombre d’exemples. Pour patients ayant un cancer, voici quelques exemples : positifs et certaines sont négatives. Pour patients qui n’ont pas un cancer, tous les exemples sont négatives. Le jeu de données dispose d’exemples 102K. Le jeu de données enregistre un écart, 0,6 % des points sont positifs, le reste est négative. Le jeu de données a été mis à disposition par la santé Siemens.
  </td>
</tr>

<tr ID=crm-appetency-labels-shared>
  <td valign=top>Étiquettes de Appetency CRM partagés</td>
  <td valign=top>
Étiquettes du défi de prédiction de relation KDD Cup 2009 client (<a href="http://www.sigkdd.org/site/2009/files/orange_small_train_appetency.labels">orange_small_train_appetency.labels</a>).
  </td>
</tr>

<tr ID=crm-churn-labels-shared>
  <td valign=top>Étiquettes d’évolution du CRM partagés</td>
  <td valign=top>
Étiquettes du défi de prédiction de relation KDD Cup 2009 client (<a href="http://www.sigkdd.org/site/2009/files/orange_small_train_churn.labels">orange_small_train_churn.labels</a>).
  </td>
</tr>

<tr ID=crm-dataset-shared>
  <td valign=top>Jeu de données CRM partagé</td>
  <td valign=top>
Ces données proviennent du défi de prédiction de relation KDD Cup 2009 client (<a href="http://www.sigkdd.org/kdd-cup-2009-customer-relationship-prediction - orange_small_train.data.zip">orange_small_train.data.zip</a>).<p> </p>Le dataset contient 50 Ko clients dans la société de télécommunications Français Orange. Chaque client a 230 fonctionnalités rendues, 190 dont sont numériques et 40 sont par catégorie. Les fonctionnalités sont très incomplets.
  </td>
</tr>

<tr ID=crm-upselling-labels-shared>
  <td valign=top>Étiquettes de vente incitative CRM partagés</td>
  <td valign=top>
Étiquettes du défi de prédiction de relation KDD Cup 2009 client (<a href="http://www.sigkdd.org/site/2009/files/orange_large_train_upselling.labels">orange_large_train_upselling.labels</a>).
  </td>
</tr>

<tr>
  <td valign=top>Données de régression l’efficacité énergétique</td>
  <td valign=top>
Collection de profils énergétique simulé, en fonction de 12 construction différentes formes. Bâtiments diffèrent par rapport à se distinguent par 8 fonctionnalités, telles que les verre zone, la distribution de zone vitrage et l’orientation.<p> </p><b>Syntaxe :</b> utiliser régression ou classification pour prévoir l’efficacité énergétique évaluation de base dans l’un des deux réponses réel à plusieurs valeurs. Pour les cours multiples classification, est arrondir la variable de réponse à l’entier le plus proche. <p> </p><b>Recherche :</b> Xifara, a & Tsanas, A. (2012). Machine UCI référentiel <a href="http://archive.ics.uci.edu/ml">http://archive.ics.uci.edu/ml</a>d’apprentissage. Irvine, Californie : Université de Californie, scolaire des informations et informatique </td>
</tr>

<tr ID=flight-delays-data>
  <td valign=top>Données des retards de vol</td>
  <td valign=top>
Voyageurs aérienne ponctuelle des données de performance provenant de la collecte de données TranStats du département américain du transport (<a href="http://www.transtats.bts.gov/DL_SelectFields.asp?Table_ID=236&DB_Short_Name=On-Time">Temps</a>).<p> </p>Le jeu de données couvre la période avril-octobre 2013. Avant de télécharger à Azure ML Studio, le jeu de données a été traitée comme suit :<ul><li>Le jeu de données a été filtré pour couvrir le plus occupés 70 aéroports uniquement dans la continental des États-Unis.</li><li>Vols annulées ont été présentés comme retardée de plus de 15 minutes</li><li>Vols transférés ont été filtrés</li><li>Les colonnes suivantes ont été sélectionnées : année, mois, DayofMonth, jour de la semaine, opérateur, OriginAirportID, DestAirportID, CRSDepTime, DepDelay, DepDel15, CRSArrTime, ArrDelay, ArrDel15, annulé</li></ul>
</td>
</tr>

<tr>
  <td valign=top>Performances ponctuels aérienne (vierge)</td>
  <td valign=top>
Enregistrements d’avion aérienne arrivées et les départs au sein des États-Unis à partir d’octobre 2011.<p> </p><b>Syntaxe :</b> prévoir les retards des avions. <p> </p><b>Recherche :</b> de service US de transport <a href="http://www.transtats.bts.gov/DL_SelectFields.asp?Table_ID=236&DB_Short_Name=On-Time">http://www.transtats.bts.gov/DL_SelectFields.asp?Table_ID=236&DB_Short_Name=On-Time</a>.
  </td>
</tr>

<tr>
  <td valign=top>Forêt déclenche données</td>
  <td valign=top>
Contient des données météorologiques, tels que des indices température et humidité et vent, d’une zone de mise en correspondance Portugal, combiné avec les enregistrements des feux de forêt.<p> </p><b>Syntaxe :</b> il s’agit d’une tâche de régression difficile, où l’objectif consiste à prévoir la zone vous-mêmes des feux de forêt. <p> </p><b>Recherche :</b> Cortez, P., & Morais, A. (2008). Machine UCI référentiel <a href="http://archive.ics.uci.edu/ml">http://archive.ics.uci.edu/ml</a>d’apprentissage. Irvine, Californie : Université de Californie, scolaire des informations et informatique <p> </p>[Cortez et Morais, 2007] P. Cortez et r. Morais. Une approche exploration de données pour prévoir forêt se déclenche à l’aide de données météorologiques. Dans J. Neves, m F. Santos et J. Machado éditeurs, nouvelles tendances intelligence artificielle, une procédure des depuis le 13 EPIA 2007 - portugaise conférence sur Intelligence artificielle, le mois de décembre, Guimarães (Portugal), pages 512-523, 2007. APPIA, ISBN-13 978-989-95618-0-9. Disponibles dans la page : <a href="http://www.dsi.uminho.pt/~pcortez/fires.pdf">http://www.dsi.uminho.pt/~pcortez/fires.pdf</a>.
  </td>
</tr>

<tr ID=german-credit-card-uci-dataset>
  <td valign=top>Jeu de données UCI de carte de crédit allemand</td>
  <td valign=top>
Le dataset UCI Statlog (carte de crédit allemand) (<a href="http://archive.ics.uci.edu/ml/datasets/Statlog+(German+Credit+Data)">Données + crédit + allemand + Statlog</a>), en utilisant le fichier german.data.<p> </p>Le jeu de données considère personnes, décrites par un ensemble d’attributs, comme les risques de crédit haute ou faible. Chaque exemple représente une personne. Il existe 20 fonctionnalités, numériques et par catégorie et une étiquette binaire (la valeur de risque de crédit). Les entrées de risque élevé crédit ont étiquette = 2, crédit faible risque entrées ont étiquette = 1. Le coût d’un exemple de faible risque élevé de classer par erreur entrées est 1, tandis que le coût d’un exemple de risque élevé au niveau inférieur de classer par erreur entrées est 5.
  </td>
</tr>

<tr ID=imdb-movie-titles>
  <td valign=top>Titres des films IMDB</td>
  <td valign=top>
Le jeu de données contient des informations sur les vidéos qui ont été classés dans tweet Twitter : IMDB film ID, nom du film et genre, année de production. Il existe des films 17K dans le groupe de données. Le jeu de données a été introduite dans le livre blanc « S. Dooms, T. De Pessemier et Martens L.. MovieTweetings : un film évaluation de jeu de données collectées à partir de Twitter. Atelier sur marketing participatif et le calcul des conseils sur les systèmes, CrowdRec en 2013 RecSys. »
  </td>
</tr>

<tr>
  <td valign=top>Données de classe IRIS deux</td>
  <td valign=top>
Il s’agit peut-être la base de données plus connu doivent figurer dans la documentation de reconnaissance de motif. Le jeu de données est relativement petit, contenant 50 exemples de mesures pétales de trois variétés iris.<p> </p><b>Syntaxe :</b> prévoir le type iris dans les mesures.  <p> </p><b>Recherche :</b> Fisher, autorité (1988). Machine UCI référentiel <a href="http://archive.ics.uci.edu/ml">http://archive.ics.uci.edu/ml</a>d’apprentissage. Irvine, Californie : Université de Californie, scolaire des informations et informatique </td>
</tr>

<tr ID=movie-tweets>
  <td valign=top>Tweets vidéo</td>
  <td valign=top>
Le jeu de données est une version étendue du dataset Tweetings vidéo. Le jeu de données a évaluations 170K pour les films, extraites tweet bien structurés sur Twitter. Chaque instance représente un tweet et est un uplet : ID d’utilisateur, l’ID de film IMDB, évaluation, horodatage et nombre de favoris pour cette tweet et le nombre de retweets de cette tweet. Le jeu de données a été effectuée disponible en a dit, S. Dooms, Loni B. et Tikk D. de conseils sur les systèmes HIP 2014.
  </td>
</tr>

<tr>
  <td valign=top>Données MPG pour différentes voitures</td>
  <td valign=top>
Ce groupe de données est une version légèrement modifiée du dataset fournie par la bibliothèque StatLib de Carnegie Mellon université. Le jeu de données a été utilisée dans les spécifications Association 1983 américain statistiques.<p> </p>Les listes de données consommation de carburant pour différents véhicules miles par gallon, ainsi que des informations, notamment le nombre de cylindres, du déplacement de moteur, puissance, poids total et l’accélération.<p> </p><b>Syntaxe :</b> prédiction de consommation de carburant selon 3 attributs discrètes à plusieurs valeurs et 5 attributs continus. <p> </p><b>Recherche :</b> StatLib, Carnegie Mellon université, (1993). Machine UCI référentiel <a href="http://archive.ics.uci.edu/ml">http://archive.ics.uci.edu/ml</a>d’apprentissage. Irvine, Californie : Université de Californie, scolaire des informations et informatique </td>
</tr>

<tr>
  <td valign=top>Jeu de données PIMA indiens DIABÈTE binaire Classification</td>
  <td valign=top>
Un sous-ensemble de données à partir du National Institute de diabète et la base de données de l’appareil et NEPHROPATHIES. Le jeu de données a été filtré pour se concentrer sur féminins patients d’héritage indiennes Pima. Les données contiennent des données médicales tels que glucose et niveaux d’INSULINE, ainsi que les facteurs de style de vie.<p> </p><b>Syntaxe :</b> prédire si l’objet comporte le diabète (classification binaire). <p> </p><b>Recherche :</b> Sigillito, V. (1990). Machine UCI référentiel d’apprentissage <a href="http://archive.ics.uci.edu/ml">http://archive.ics.uci.edu/ml »</a>. Irvine, Californie : Université de Californie, scolaire des informations et informatique </td>
</tr>

<tr>
  <td valign=top>Données client restaurant</td>
  <td valign=top>
Un ensemble de métadonnées relatives aux clients, y compris les données démographiques et les préférences.<p> </p><b>Syntaxe :</b> utiliser ce groupe de données, en combinaison avec les autres jeux de données de deux restaurant, pour former et tester un système de recommandation. <p> </p><b>Recherche :</b> Bache, k et Lichman, m (2013). Machine UCI référentiel <a href="http://archive.ics.uci.edu/ml">http://archive.ics.uci.edu/ml</a>d’apprentissage. Irvine, Californie : Université de Californie, scolaire des informations et informatique.
  </td>
</tr>

<tr>
  <td valign=top>Données de la fonctionnalité restaurant</td>
  <td valign=top>
Un ensemble de métadonnées sur restaurants et leurs fonctionnalités, telles que le type d’alimentation, style dîner et l’emplacement.<p> </p><b>Syntaxe :</b> utiliser ce groupe de données, en combinaison avec les autres jeux de données de deux restaurant, pour former et tester un système de recommandation. <p> </p><b>Recherche :</b> Bache, k et Lichman, m (2013). Machine UCI référentiel <a href="http://archive.ics.uci.edu/ml">http://archive.ics.uci.edu/ml</a>d’apprentissage. Irvine, Californie : Université de Californie, scolaire des informations et informatique.
  </td>
</tr>

<tr>
  <td valign=top>Évaluations de restaurant</td>
  <td valign=top>
Contient des évaluations fournies par aux utilisateurs de restaurants sur une échelle de 0 à 2.<p> </p><b>Syntaxe :</b> utiliser ce groupe de données, en combinaison avec les autres jeux de données de deux restaurant, pour former et tester un système de recommandation. <p> </p><b>Recherche :</b> Bache, k et Lichman, m (2013). Machine UCI référentiel <a href="http://archive.ics.uci.edu/ml">http://archive.ics.uci.edu/ml</a>d’apprentissage. Irvine, Californie : Université de Californie, scolaire des informations et informatique.
  </td>
</tr>

<tr>
  <td valign=top>Métallique RECUIT multiples classe dataset</td>
  <td valign=top>
Ce groupe de données contient une série d’enregistrements à partir d’aciers RECUIT essais avec les attributs physiques (largeur, épaisseur, types de type (bobines, feuille, etc.) des résultats d’acier.<p> </p><b>Syntaxe :</b> prédiction d’un des deux attributs de classe numérique ; dureté ou rupture. Vous pourriez également analyser corrélations entre les attributs.<p> </p>Nuances d’acier suivent un jeu standard, défini par SAE et d’autres organisations. Vous recherchez un spécifique « classe » (la variable de classe) et que vous souhaitez comprendre les valeurs requises. <p> </p><b>Recherche :</b> Sterling, D. & Buntine, w, (NA). Machine UCI référentiel <a href="http://archive.ics.uci.edu/ml">http://archive.ics.uci.edu/ml</a>d’apprentissage. Irvine, Californie : Université de Californie, scolaire des informations et informatique <p> </p>Un guide utile nuances d’acier sont accessibles ici : <a href="http://www.outokumpu.com/SiteCollectionDocuments/Outokumpu-steel-grades-properties-global-standards.pdf">http://www.outokumpu.com/SiteCollectionDocuments/Outokumpu-steel-grades-properties-global-standards.pdf</a>
  </td>
</tr>

<tr>
  <td valign=top>Données télescope</td>
  <td valign=top>
Enregistrements de rupture de particules énergétique haute gamma ainsi que le bruit de fond, les deux simulé à l’aide d’un processus Monte Carlo.<p> </p>L’objectif de la simulation était d’améliorer la précision d’au sol atmosphériques Cherenkov gamma spéciaux, à l’aide des méthodes statistiques faire la distinction entre le signal souhaité (douches RAYONNEMENT Cherenkov) et le bruit de fond (hadronic douches initiées par rayons cosmiques dans l’atmosphère supérieur).<p> </p>Les données ont été traitement préalable pour créer un cluster allongé avec le long axe est orienté vers le centre de caméra. Les caractéristiques de cette ellipse (souvent appelés paramètres Hillas) figurant parmi les paramètres de l’image qui peuvent être utilisées pour discrimination.<p> </p><b>Syntaxe :</b> prédire si image d’une pluie représente bruit signal ou un arrière-plan.<p> </p><b>Remarques :</b> précision simplifier la classification n’est pas explicite pour ces données, depuis un événement en arrière-plan la classification comme signal est pire que classer un événement de signal comme arrière-plan. Comparaison des différentes classifieurs le graphique ROC doit être utilisé. La probabilité d’accepter un événement en arrière-plan en tant que signal doit être inférieure de l’un des deux seuils suivants : 0,01, 0,02, 0,05, 0,1 ou 0,2.<p> </p>Notez également que le nombre d’événements d’arrière-plan (h, pour douches hadronic) est sous-estimé, alors que dans les mesures réels, la classe h ou bruit représente la majorité des événements. <p> </p><b>Recherche :</b> objet, R.K. (1995). Machine UCI référentiel <a href="http://archive.ics.uci.edu/ml">http://archive.ics.uci.edu/ml</a>d’apprentissage. Irvine, Californie : Université de Californie, scolaire d’informations </td>
</tr>

<tr ID=weather-dataset>
  <td valign=top>Jeu de données météorologiques</td>
  <td valign=top>
Observations météo terrestre horaire à partir de NOAA (<a href="http://cdo.ncdc.noaa.gov/qclcd_ascii/, merged data from 201304 to 201310">données fusionnées à partir de 201304 à 201310</a>).<p> </p>Les données météorologiques traite des observations effectuées à partir de l’aéroport météo postes, couvrant la période de temps avril-octobre 2013. Avant de télécharger à Azure ML Studio, le jeu de données a été traitée comme suit :<ul><li>ID de poste météo ont été mappés à l’aéroport correspondante ID</li><li>Consoles météo non associés avec les 70 aéroports plus chargées ont été filtrés</li><li>La colonne Date a été fractionnée en colonnes distinctes année, mois et jour</li><li>Les colonnes suivantes ont été sélectionnées : AirportID, année, mois, jour, heure, fuseau horaire, SkyCondition, visibilité, WeatherType, DryBulbFarenheit, DryBulbCelsius, WetBulbFarenheit, WetBulbCelsius, DewPointFarenheit, DewPointCelsius, RelativeHumidity, vitesse du vent, WindDirection, ValueForWindCharacter, StationPressure, PressureTendency, PressureChange, SeaLevelPressure, RecordType, HourlyPrecip, altimètre</li></ul>
  </td>
</tr>

<tr ID=wikipedia-sp-500-dataset>
  <td valign=top>Wikipédia SP 500 Dataset</td>
  <td valign=top>
Données sont dérivées de Wikipédia (<a href="http://www.wikipedia.org/">http://www.wikipedia.org/</a>) en fonction des articles de chaque société S & P 500, stockés en tant que données XML.<p> </p>Avant de télécharger à Azure ML Studio, le jeu de données a été traitée comme suit :<ul><li>Extraire le contenu de texte pour chaque société spécifique</li><li>Supprimer la mise en forme de wiki</li><li>Supprimer des caractères non-alphanumériques</li><li>Convertir tout le texte en minuscules.</li><li>Catégories de société connus ont été ajoutés</li></ul><p> </p>Notez que certaines sociétés un article est introuvable, afin que le nombre d’enregistrements est inférieur à 500.
  </td>
</tr>





<tr ID=direct-marketing>
  <td valign=top><a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/direct_marketing.csv">direct_marketing.csv</a></td>
  <td valign=top>
Le jeu de données contient des données client et des indications sur leur réponse à une campagne de publipostage direct. Chaque ligne représente un client. Le jeu de données contient des 9 fonctionnalités sur les données démographiques des utilisateurs et après le comportement et 3 colonnes étiquette (visitez, conversion et dépenser).  Visite est une colonne binaire qui indique qu’un client visité après la campagne marketing, conversion indique un client acheté quelque chose et dépenser est le montant passé.  Le jeu de données a été mis à disposition par Kevin Hillstrom pour MineThatData messagerie Analytique et données exploration défi.
  </td>
</tr>

<tr ID=lyrl2004-tokens-test>
  <td valign=top><a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/lyrl2004_tokens_test.csv">lyrl2004_tokens_test.csv</a></td>
  <td valign=top>
Fonctionnalités d’exemples de test dans le dataset Actualités Reuters RCV1-V2. Le jeu de données comporte des articles de news 781K ainsi que leurs ID (première colonne du jeu de données). Chaque article est sous forme de jetons, stopworded et tige. Le jeu de données a été mis à disposition par David. D. Lewis.
  </td>
</tr>

<tr ID=lyrl2004-tokens-train>
  <td valign=top><a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/lyrl2004_tokens_train.csv">lyrl2004_tokens_train.csv</a></td>
  <td valign=top>
Fonctionnalités d’exemples de formation dans le dataset Actualités Reuters RCV1-V2. Le jeu de données comporte des articles de news 23K ainsi que leurs ID (première colonne du jeu de données). Chaque article est sous forme de jetons, stopworded et tige. Le jeu de données a été mis à disposition par David. D. Lewis.
  </td>
</tr>

<tr ID=intrusion-detection>
  <td valign=top><a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/network_intrusion_detection.csv">network_intrusion_detection.csv</a><br></td>
  <td valign=top>
Jeu de données à partir de la découverte de connaissances Cup 1999 KDD et outils concurrence (<a href="http://kdd.ics.uci.edu/databases/kddcup99/kddcup99.html">kddcup99.html</a>) d’exploration de données.<p> </p>Le jeu de données a été téléchargé et stockées dans le stockage Blob Azure (<a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/network_intrusion_detection.csv">network_intrusion_detection.csv</a>) et comprend la formation et test des jeux de données. Le dataset formation a environ 126K lignes et 43 colonnes, y compris les étiquettes ; 3 colonnes incluses dans les informations d’étiquette, puis 40 colonnes, composé de fonctions numériques et de chaîne/par catégorie, sont disponibles pour le modèle de formation. Les données de test ont approximativement 22,5 K exemples avec les mêmes 43 colonnes comme dans les données de formation de test.

  </td>
</tr>

<tr ID=rcv1-v2-topics-qrels>
  <td valign=top><a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/rcv1-v2.topics.qrels.csv">rcv1 v2.topics.qrels.csv</a></td>
  <td valign=top>
Affectations rubrique des articles de presse dans le dataset Actualités Reuters RCV1-V2. Un article de news peut être affecté à plusieurs sujets. Le format de chaque ligne est «<topic name>  <document id> 1 ». Le dataset contient les affectations de rubrique 2.6M. Le jeu de données a été mis à disposition par David. D. Lewis.
  </td>
</tr>

<tr ID=student-performance>
  <td valign=top><a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/student_performance.txt">student_performance.txt</a></td>
  <td valign=top>
Ces données proviennent du défi d’évaluation KDD Cup 2010 étudiant performances (<a href="http://www.kdd.org/kdd-cup-2010-student-performance-evaluation">évaluation des performances étudiant</a>). Les données utilisées sont l’ensemble d’apprentissage Algebra_2008_2009 (matrice, J., Niculescu-Mizil, a, Ritter, s, Gordon, G.J. et Koedinger, K.R. (2010). Algèbre je 2008-2009. HIP jeu de données à partir de KDD Cup 2010 les données exploration HIP. Trouver en <a href="http://pslcdatashop.web.cmu.edu/KDDCup/downloads.jsp">downloads.jsp</a> ou <a href="http://www.kdd.org/sites/default/files/kddcup/site/2010/files/algebra_2008_2009.zip">algebra_2008_2009.zip</a>.<p> </p>Le jeu de données a été téléchargé et stockées dans le stockage Blob Azure (<a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/student_performance.txt">student_performance.txt</a>) et contient les fichiers journaux à partir d’un étudiant cours particuliers système. Les fonctionnalités fournies incluent identification du problème et sa description brève, ID de l’étudiant, horodatage et tentatives combien la loi apportée avant de résoudre le problème de la façon de droite. Le jeu de données d’origine a enregistrements 8,9 M, le jeu de données a été échantillonnée vers le bas pour les premières lignes de 100 Ko. Le dataset a 23 colonnes séparées par des tabulations de divers types : numériques, par catégorie et horodatage.

  </td>
</tr>




</table>


<!-- Module References -->
[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/

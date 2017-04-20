<properties
    pageTitle="Analyse à l’aide d’apprentissage automatique l’évolution du client | Microsoft Azure"
    description="Étude de cas de développement d’un modèle intégré pour l’analyse et de notation évolution du client"
    services="machine-learning"
    documentationCenter=""
    authors="jeannt"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="machine-learning"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/20/2016" 
    ms.author="jeannt"/>

# <a name="analyzing-customer-churn-by-using-azure-machine-learning"></a>Analyse de l’évolution du client à l’aide d’apprentissage automatique Azure

##<a name="overview"></a>Vue d’ensemble
Cette rubrique présente une implémentation de référence d’un projet d’analyse évolution du client intégré à l’aide de Azure Machine apprentissage Studio. Il traite des modèles de génériques associés permettant de façon holistique résoudre le problème de l’évolution du client industriel. Nous avons également mesurer la précision des modèles sont créés à l’aide d’apprentissage automatique, et nous évaluer itinéraires développements.  

### <a name="acknowledgements"></a>Accusés de réception

Cette expérience a été développée et testée par Serge Berger, Principal données senior chez Microsoft et Roger Barga, anciennement Gestionnaire de produit pour Microsoft Azure Machine Learning. L’équipe de documentation Azure reconnaît leur expertise grandement appréciées et les Merci pour le partage de ce livre blanc.

>[AZURE.NOTE] Les données utilisées pour cette expérience ne sont pas disponibles publiquement. Pour obtenir un exemple de création d’un modèle d’apprentissage machine pour l’analyse de l’évolution du, voir : [télécommunications évolution du modèle](http://gallery.cortanaintelligence.com/Experiment/Telco-Customer-Churn-5) dans la [Galerie d’Intelligence Cortana](http://gallery.cortanaintelligence.com/)


[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

##<a name="the-problem-of-customer-churn"></a>Le problème de l’évolution du client
Les entreprises sur le marché grand public et dans tous les secteurs entreprise ont affaire avec évolution du. Parfois évolution du est excessif et influe sur les décisions de stratégie. La solution traditionnelle consiste à prévoir haute tendance churners et répondre à leurs besoins via un Hervé, campagnes marketing ou en appliquant dispenses spéciales. Ces approches peuvent varier d’industrie à industrie et même à partir d’un cluster consommateur particulière à l’autre dans un secteur d’activité (par exemple, télécommunications).

Le facteur courantes est entreprises doivent réduire ces efforts de rétention client spécial. Par conséquent, une méthodologie naturelle serait de score tous vos clients avec la probabilité de l’évolution du et traiter les N premiers sont ceux qui. Les meilleurs clients peuvent être les plus rentables ; par exemple, dans des scénarios plus sophistiqués, une fonction de la marge est utilisée lors de la sélection des candidats pour la distribution spéciale. Toutefois, les considérations suivantes sont uniquement une partie de la stratégie en matière de gestion évolution du holistique. Entreprises doivent également tenir compte risques (et tolérance au risque associé), le niveau et le coût de l’intervention et segmentation plausible client.  

##<a name="industry-outlook-and-approaches"></a>Approches et industrie outlook
Évolution du traitement élaboré est un signe d’un secteur maturité. L’exemple classique est le secteur des télécommunications où les abonnés posent fréquemment passer d’un fournisseur à l’autre. Cette évolution du volontaire pose problème principal. En outre, fournisseurs ont accumulé significative connaissance *l’évolution du pilotes*, quelles sont les facteurs qui clients lecteur pour basculer.

Par exemple, choix combiné ou un appareil est un pilote connu d’évolution dans l’entreprise de téléphone mobile. Par conséquent, une stratégie populaires consiste à subventionnent le prix d’un combiné pour les nouveaux abonnés et un prix total pour les clients existants pour une mise à niveau. Traditionnellement, cette stratégie a conduit aux clients saut d’un fournisseur à l’autre pour obtenir une nouvelle remise, qui à son tour, a invité fournisseurs d’affiner leurs stratégies.

Haute volatilité des offres combiné est un facteur qui invalide très rapidement des modèles d’évolution du basés sur des modèles combiné en cours. En outre, les téléphones mobiles ne sont pas uniquement aux périphériques de télécommunication ; ils sont également des instructions de manière (envisagez l’iPhone), et ces indicateurs sociales sont en dehors de l’étendue des jeux de données télécommunications normal.

Le résultat net pour une modélisation est que vous ne pouvez pas concevoir une stratégie de son simplement en supprimant les raisons connus pour l’évolution du. En fait, une stratégie de modélisation continue, y compris des modèles classiques quantifiez variables par catégorie (par exemple décision), est **obligatoire**.

À l’aide de jeux de données volumineux sur leurs clients, organisations effectuez analytique de données volumineux (en particulier, détection évolution du basés sur des données volumineux) comme une stratégie efficace du problème. Vous trouverez plus d’informations sur le traitement des données volumineux pour le problème de l’évolution du figurant dans les recommandations section ETL.  

##<a name="methodology-to-model-customer-churn"></a>Méthodologie de l’évolution du modèle client
Un processus de résolution des problèmes courant pour résoudre l’évolution du client est représenté dans Figures 1 à 3 :  

1.  Un modèle de risque permet à prendre en compte l’impact des actions probabilité et les risques.
2.  Un modèle d’intervention à prendre en compte la façon dont le niveau de l’intervention peut affecter la probabilité de l’évolution du et la quantité de client permet de valeur de durée de vie (CLV).
3.  Cette analyse prête à une analyse qualitative qui est affectée à une campagne marketing proactive qui cible segments client pour présenter l’offre optimale.  

![][1]

Cette approche transférer aspect est la meilleure façon de traiter évolution du, mais il est fourni avec la complexité : nous avons développer un archétype comportant plusieurs modèle et traçage des dépendances entre les modèles. L’interaction entre les modèles peut être encapsulée comme indiqué dans le diagramme suivant :  

![][2]

*Figure 4 : Unifiée archétype comportant plusieurs modèle*  

L’interaction entre les modèles est clé si nous voulons effectuer une approche holistique devant rétention client. Chaque modèle nécessairement diminue dans le temps ; Par conséquent, l’architecture est une boucle implicite (semblable à l’archétype du défini par la norme exploration de données nette-DM, [***3***]).  

Le cycle de global de risque-décision-marketing segmentation/décomposition est toujours une structure GRG qui s’applique à de nombreux problèmes d’entreprise. Analyse de l’évolution du est simplement un représentant fort de ce groupe des problèmes de synchronisation, car il présente toutes les caractéristiques d’un problème métier complexe qui ne permet pas d’une solution prédictive simplifiée. Les aspects sociaux de l’approche moderne pour l’évolution du ne sont pas particulièrement mis en surbrillance dans l’approche, mais les aspects sociaux sont encapsulés dans l’archétype modélisation, tels qu’ils seraient dans n’importe quel modèle.  

Un complément intéressant ici est analytique données volumineuses. Télécommunications et les entreprises de vente au détail d’aujourd'hui collectent des données exhaustives sur leurs clients et nous pouvons facilement prévoir qu’il soit nécessaire de connectivité comportant plusieurs modèle deviendra une tendance courantes, compte tenue de nouvelles tendances tels que les Internet des objets et les périphériques partout, qui permettent d’entreprise pour utiliser des solutions smart à plusieurs niveaux.  

 
##<a name="implementing-the-modeling-archetype-in-machine-learning-studio"></a>Implémentation de la modélisation archétype dans Machine apprentissage Studio
Étant donné le problème vient d’être décrit, quelle est la meilleure façon de mettre en œuvre une modélisation intégrée et score approche ? Dans cette section, nous va vous montrer comment nous avons accompli cela à l’aide de Azure Machine apprentissage Studio.  

L’approche comportant plusieurs modèle est indispensable lorsque vous créez un archétype général pour l’évolution du. Même la score (prédictive) une partie de l’approche doit être comportant plusieurs modèle.  

Le diagramme suivant illustre le prototype que nous avons créé, qui utilise des quatre algorithmes score dans Machine apprentissage Studio pour prévoir l’évolution du. La raison d’utiliser une approche comportant plusieurs modèle n’est pas uniquement pour créer un classifieur ensemble pour augmenter la précision, mais également pour vous protéger contre surapprentissage et pour améliorer la sélection des fonctionnalités des.  

![][3]

*Figure 5 : Prototype d’un évolution du approche de modélisation*  

Les sections suivantes fournissent plus d’informations sur le prototype score du modèle que nous avons implémentées à l’aide de Machine apprentissage Studio.  

###<a name="data-selection-and-preparation"></a>Préparation et la sélection de données
Les données utilisées pour créer les modèles et clients score obtenu à partir d’une solution verticale CRM, avec les données masquées pour protéger la confidentialité des clients. Les données contient des informations à propos des 8 000 abonnements aux États-Unis et combine trois sources : mise en service de données (abonnement métadonnées), les données d’activité (l’utilisation du système) et les données de support client. Les données n’incluent pas de toute entreprise des informations sur les clients. par exemple, il n’inclut pas les notes de crédit ou de métadonnées de fidélité.  

Pour simplifier, ETL et processus de nettoyage des données sont hors de portée, car nous part du principe que préparation des données a déjà été effectuées ailleurs.   

Sélection de fonctionnalités pour une modélisation est basée sur précision chevauchant notation de l’ensemble des indicateurs, inclus dans le processus qui utilise le module forêt aléatoires. Pour l’implémentation de Machine apprentissage Studio, nous calcule la moyenne, MEDIANE et plages de fonctionnalités représentatives. Par exemple, nous avons ajouté agrégats pour les données qualitatives, telles que les valeurs minimales et maximales pour l’activité des utilisateurs.    

Nous capturé également des informations temporelles pour les six mois plus récentes. Nous avons analysé les données d’un an et nous établie que même s’il y a des tendances statistique significatives, l’impact sur l’évolution du est réduite considérablement après six mois.  

L’essentiel est que l’ensemble du processus, y compris ETL, sélection de fonctionnalités et de modélisation a été activée dans Studio apprentissage Machine, à l’aide de sources de données dans Microsoft Azure.   

Les diagrammes suivants illustrent les données qui a été utilisées.  

![][4]

*Figure 6 : Extrait de source de données (brouillé)*  

![][5]


*Figure 7 : Fonctionnalités extraites de source de données*
 
> Notez que ces données sont privées et par conséquent le modèle et les données ne peut pas être partagées.
> Toutefois, pour un modèle similaire à l’aide de données accessibles au public, voir cet exemple tester dans la [Galerie d’Intelligence Cortana](http://gallery.cortanaintelligence.com/): [Évolution du client de télécommunications](http://gallery.cortanaintelligence.com/Experiment/31c19425ee874f628c847f7e2d93e383).
> 
> Pour en savoir plus sur la façon d’implémenter un modèle d’analyse évolution à l’aide de Cortana Intelligence Suite, nous vous recommandons également [cette vidéo](https://info.microsoft.com/Webinar-Harness-Predictive-Customer-Churn-Model.html) par programme senior ouvrables Hyong Tok. 
> 

###<a name="algorithms-used-in-the-prototype"></a>Algorithmes utilisés dans le prototype

Nous avons utilisé quatre algorithmes apprentissage machine suivants pour générer le prototype (sans personnalisation) :  

1.  Régression logistique (longue distance)
2.  Arbre de décision augmentée (BT)
3.  Moyenne perceptron (PA)
4.  Machine à vecteur support (SVM)  


Le diagramme suivant montre une partie de l’aire de conception expérience, qui indique l’ordre dans lequel les modèles ont été créés :  

![][6]  


*Figure 8 : Création de modèles dans Machine apprentissage Studio*  

###<a name="scoring-methods"></a>Méthodes de notation
Nous a obtenu les quatre modèles à l’aide d’un jeu de données formation libellée.  

Nous avons également été soumis dataset score à un modèle comparable intégré à l’aide de l’édition desktop de sa entreprise mineur 12. Nous avons mesuré la précision du modèle associations de sécurité et tous les modèles Machine apprentissage Studio quatre.  

##<a name="results"></a>Résultats
Dans cette section, nous vous présentons nos conclusions sur la précision des modèles, basé sur le score dataset.  

###<a name="accuracy-and-precision-of-scoring"></a>Précision de calcul des points
En règle générale, la mise en œuvre dans l’apprentissage automatique Azure est protégé par sa précision d’environ 10 à 15 % (zone sous courbe ou AUC).  

Toutefois, la mesure les plus importantes de l’évolution du est le taux de mauvaise classification : autrement dit, des churners N premiers conformément aux prévisions par le classifieur, laquelle d'entre elles a effectivement **pas** évolution du et encore reçu un traitement spécial ? Le diagramme suivant compare ce taux mauvaise classification à tous les modèles :  

![][7]


*Figure 9 : Passau prototype superficie courbe*

###<a name="using-auc-to-compare-results"></a>À l’aide de AUC pour comparer les résultats
Zone sous courbe (AUC) est une mesure qui représente une mesure globale de *séparation* entre les répartitions de résultats pour le remplissage positif et négatif. Il est semblable au graphique combiné opérateur caractéristique (ROC) traditionnel, mais une différence importante est que la métrique AUC ne requiert pas vous permet de choisir une valeur de seuil. Au lieu de cela, il affiche les résultats sur **tous les** choix possibles. En revanche, le graphique ROC traditionnel présente le taux positif sur l’axe vertical et le taux de faux positifs sur l’axe horizontal et le seuil de classification varie.   

AUC est généralement utilisé comme une mesure de la valeur pour différents algorithmes (ou différents systèmes) car elle permet de modèles à comparer au moyen de leurs valeurs AUC. Il s’agit d’une approche populaires dans des secteurs tels que météorologie et biosciences. Par conséquent, AUC représente un outil populaire pour évaluer les performances de classifieur.  

###<a name="comparing-misclassification-rates"></a>Comparaison des taux de mauvaise classification
Nous comparer les taux mauvaise classification sur le jeu de données en question en utilisant les données CRM d’environ 8 000 abonnements.  

-   Le taux de mauvaise classification sa a été 10 à 15 %.
-   Le taux de mauvaise classification Machine apprentissage Studio a été 15-20 % pour les churners haut 200-300.  

Dans le secteur des télécommunications, il est important de répondre uniquement aux clients ayant risque le plus élevé à l’évolution du en leur proposant un Hervé ou tout autre traitement spécial. Dans ce domaine, la mise en œuvre Machine apprentissage Studio atteint résultats compatibles avec le modèle associations de sécurité.  

Dans la même perspective, précision est plus importante que la précision parce que nous intéresse principalement classer correctement churners potentiels.  

Le diagramme suivant à partir de Wikipédia illustre les relations dans un graphique animer, faciles à comprendre :  

![][8]

*Figure 10 : Compromis entre précision*

###<a name="accuracy-and-precision-results-for-boosted-decision-tree-model"></a>Résultats de la précision de modèle d’arbre de décision augmentée  

Le tableau suivant affiche les résultats brutes à partir de la notation en utilisant le prototype apprentissage automatique pour le modèle d’arborescence augmentée décision, qui se trouve être la plus précise entre les quatre modèles :  

![][9]

*Figure 11 : Caractéristiques de modèle arbre de décision augmentée*

##<a name="performance-comparison"></a>Comparaison des performances
Nous comparée la vitesse des données a été évaluées à l’aide des modèles Machine apprentissage Studio et un modèle comparable créé à l’aide de l’édition desktop de sa Enterprise mineur 12.1.  

Le tableau suivant résume les performances des algorithmes :  

*Tableau 1. Performances générales (précision) des algorithmes*

| LONGUE DISTANCE|BT|POINT D’ACCÈS|SVM|
|---|---|---|---|
|Modèle moyenne|Le meilleur modèle|Performants|Modèle moyenne|

Les modèles hébergés dans Machine apprentissage Studio sa surpassé par 15 à 25 % de la vitesse d’exécution, mais précision a été largement à égalité.  

##<a name="discussion-and-recommendations"></a>Discussion et recommandations
Du secteur des télécommunications, plusieurs méthodes ont été suggéré pour analyser l’évolution du, y compris :  

-   Dérivent métriques de quatre catégories fondamentales :
    -   **Entité (par exemple, un abonnement)**. Mise en service des informations de base sur l’abonnement et/ou client est le sujet de l’évolution du.
    -   **Activité**. Obtenir toutes les informations d’utilisation possible associé à l’entité, par exemple, le nombre de connexions.
    -   **Services de support technique**. Collecter des informations à partir des journaux de support client pour indiquer si l’abonnement a des problèmes ou des interactions avec le support technique.
    -   **Données d’entreprise et sur la concurrence**. Obtenir des informations possibles sur le client (par exemple, peut être indisponible ou difficiles à suivre).
-   Utilisez l’importance à la sélection de fonctionnalité de lecteur. Cela signifie que le modèle d’arbre de décision augmentée est toujours une approche en faisant des promesses.  

L’utilisation de ces quatre catégories crée impression une approche simple *déterministe* , formé sur raisonnables facteurs par catégorie, à partir d’index doit suffit pour identifier les clients au risque d’évolution du. Malheureusement, cette notion semble plausible, mais il est une compréhension false. La raison est qu’évolution du est un effet temporel et les facteurs pour l’évolution du sont généralement états transitoires. Quelles sont les causes un client à prendre en considération quitter aujourd'hui peuvent être différents demain, et il certainement sera différent six mois à partir de maintenant. Par conséquent, une *probabilité* modèle est nécessaire.  

Cette observation importantes est souvent ignorée dans business, qui préfère généralement une approche orientée intelligence d’entreprise analytique, principalement parce qu’il est plus facile vendre et admet automation simple.  

Toutefois, les promesses du libre-service analytique à l’aide de Machine d’apprentissage Studio sont que les quatre catégories d’informations, classés par ou département devient une source précieuse pour apprentissage automatique à propos de l’évolution du.  

Une autre fonctionnalité original bientôt dans l’apprentissage automatique Azure consiste à ajouter un module personnalisé vers le référentiel de modules prédéfinis qui sont déjà disponibles. Cette fonctionnalité, crée essentiellement une opportunité pour sélectionner des bibliothèques et créer des modèles pour marchés verticaux. Il est une importante font la différence d’apprentissage automatique Azure sur le marché.  

Nous espérons continuer cette rubrique à l’avenir, tout particulièrement lié aux analytique données volumineuses.
  
##<a name="conclusion"></a>Conclusion
Cet article décrit une bonne approche de résolution du problème courantes d’évolution du client à l’aide d’une structure générique. Nous considéré comme un prototype pour les modèles de score et implémentée à l’aide d’apprentissage automatique Azure. Pour finir, nous évaluer la précision et les performances de la solution prototype en ce qui concerne les algorithmes comparables dans les associations de sécurité.  

**Pour plus d’informations :**  

Ce document ne vous ont permis ? Veuillez nous envoyer vos commentaires. Donner son sur une échelle de 1 (mauvais) à 5 (excellent), évaluez ce document et pourquoi vous lui ayez donné cette note ? Par exemple :  

-   Soyez vous évaluation il haute en raison des bons exemples, captures d’écran excellente, clairement écriture ou une autre raison ?
-   Soyez vous évaluation il faible en raison d’un problème exemples, captures d’écran floue ou écriture précisé ?  

Ces commentaires permettent d’améliorer la qualité des livres blancs que nous relâchez.   

[Envoyer des commentaires](mailto:sqlfback@microsoft.com).
 
##<a name="references"></a>Références
Analytique prédictive [1] : au-delà de la gestion des informations des prédictions, McKnight ouest, juillet/août 2011, p. 18-20.  

Article Wikipedia [2] : [précision](http://en.wikipedia.org/wiki/Accuracy_and_precision)

[3] [nette-DM 1.0 : Guide d’exploration de données étape par étape](http://www.the-modeling-agency.com/crisp-dm.pdf)   

[4] [Marketing big Data : engager vos clients plus efficacement et à la valeur du lecteur](http://www.amazon.com/Big-Data-Marketing-Customers-Effectively/dp/1118733894/ref=sr_1_12?ie=UTF8&qid=1387541531&sr=8-12&keywords=customer+churn)

[5] [télécommunications évolution du modèle](http://gallery.cortanaintelligence.com/Experiment/Telco-Customer-Churn-5) dans la [Galerie d’Intelligence Cortana](http://gallery.cortanaintelligence.com/) 
 
##<a name="appendix"></a>Appendice

![][10]

*Figure 12 : Instantané d’une présentation sur l’évolution du prototype*


[1]: ./media/machine-learning-azure-ml-customer-churn-scenario/churn-1.png
[2]: ./media/machine-learning-azure-ml-customer-churn-scenario/churn-2.png
[3]: ./media/machine-learning-azure-ml-customer-churn-scenario/churn-3.png
[4]: ./media/machine-learning-azure-ml-customer-churn-scenario/churn-4.png
[5]: ./media/machine-learning-azure-ml-customer-churn-scenario/churn-5.png
[6]: ./media/machine-learning-azure-ml-customer-churn-scenario/churn-6.png
[7]: ./media/machine-learning-azure-ml-customer-churn-scenario/churn-7.png
[8]: ./media/machine-learning-azure-ml-customer-churn-scenario/churn-8.png
[9]: ./media/machine-learning-azure-ml-customer-churn-scenario/churn-9.png
[10]: ./media/machine-learning-azure-ml-customer-churn-scenario/churn-10.png

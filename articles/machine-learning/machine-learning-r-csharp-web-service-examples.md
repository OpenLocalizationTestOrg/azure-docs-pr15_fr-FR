<properties 
    pageTitle="Exemples créées avec R des services web de formation machine | Microsoft Azure" 
    description="Rechercher un jeu utile du site web des exemples de services créés avec code R et d’apprentissage automatique et publiés vers Azure Marketplace." 
    keywords="CSharp, code r, exemples de services web"
    services="machine-learning" 
    documentationCenter="" 
    authors="jaymathe" 
    manager="jhubbard" 
    editor="cgronlun"/>

<tags 
    ms.service="machine-learning" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/14/2016" 
    ms.author="jaymathe"/> 


#<a name="web-services-examples-using-r-code-on-azure-machine-learning-and-published-to-microsoft-azure-marketplace"></a>Web services exemples d’utilisation du code R sur apprentissage automatique Azure et publiés sur Microsoft Azure Marketplace

Dans cet article sont exemple web services ont été créés à l’aide d’apprentissage automatique Azure et publiés dans la Azure Marketplace. Chaque exemple de service web a un document complet joint, l’incorporation de jeux de données d’exemple pour tester les services et expliquant comment l’utilisateur peut créer un service similaire eux-mêmes. 

Dans Azure Machine apprentissage Studio, les utilisateurs peuvent écrire du code R et en quelques clics, publier sous la forme d’un service web pour les applications et les périphériques à utiliser dans le monde entier. 


[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]


À partir de production calculatrices simples qui fournissent des fonctionnalités statistiques pour la création de PRÉDICTEUR analyse opinion exploration du texte personnalisé, les utilisateurs de R nouvelles et expérimentés peuvent bénéficier de la facilité avec laquelle les utilisateurs d’apprentissage automatique Azure effectuent des code R. Pendant que ces web services peuvent être utilisés par les utilisateurs, potentiellement par le biais d’une application mobile ou un site Web, l’objectif de ces exemples de services web consiste à expliquer comment pouvez effectuent des apprentissage automatique R fins d’analyse des scripts et être utilisée pour créer des services web en haut du code R.

Chaque exemple inclut un exemple c# pour consommation de service web.


![Diagramme de code R dans l’apprentissage automatique Azure : solutions R pour propriétaires utiliser ou publiés sur le Azure Marketplace.][1]

Examinons les scénarios suivants.

##<a name="scenario-1-generic-model"></a>Scénario 1 : Modèle générique 
Un utilisateur fonctionne avec un modèle générique qui peut être appliqué aux données d’un nouvel utilisateur, par exemple une prévision base de données de série de date ou d’une méthode R personnalisée avec analytique avancées. Cet utilisateur publie le modèle comme un service web pour d’autres personnes à utiliser avec leurs données.



* [Classifieur binaire](machine-learning-r-csharp-binary-classifier.md)
* [Modèle de cluster](machine-learning-r-csharp-cluster-model.md)
* [A variables régression linéaire.](machine-learning-r-csharp-multivariate-linear-regression.md)
* [Réaliser des prévisions - lissage exponentiel](machine-learning-r-csharp-forecasting-exponential-smoothing.md)
* [ETS + STL réaliser des prévisions](machine-learning-r-csharp-retail-demand-forecasting.md)
* [Réaliser des prévisions - AUTOREGRESSIVE a intégrés de moyenne mobile (ARIMA)](machine-learning-r-csharp-arima.md)
* [Analyse de survie](machine-learning-r-csharp-survival-analysis.md)


##<a name="scenario-2-trained-model--specific-data"></a>Scénario 2 : Modèle formé – des données spécifiques 
Un utilisateur dispose de données qui fournit des prédictions utiles à travers le code R, tels que des échantillons de questionnaire personnalité groupé via un algorithme des moyens k pour prévoir de l’utilisateur type de personnalité ou données de l’enquête santé pouvant être utilisées pour prévoir les risques d’un individu pour cancer du poumon avec un package d’analyse R survie. L’utilisateur publie les données via un service web qui prévoit est issue d’un nouvel utilisateur.

##<a name="scenario-3-trained-model--generic-data"></a>Scénario 3 : Modèle formé – données générique 
Un utilisateur a données générique (par exemple, un corpus texte) qui permettent à un service web doivent être générés et appliqué générique entre différents types de scénarios et d’exemples d’utilisation.

* [Lexique basé opinion analyse](machine-learning-r-csharp-lexicon-based-sentiment-analysis.md)

##<a name="scenario-4-advanced-calculator"></a>Scénario 4 : Calculatrice avancée 
Un utilisateur fournit des calculs complexes ou simulations qui ne nécessitent pas n’importe quel modèle formé ou ajustement d’un modèle de données de l’utilisateur.

* [Différence en Proportions Test](machine-learning-r-csharp-difference-in-two-proportions.md)
* [Suite de la distribution normale.](machine-learning-r-csharp-normal-distribution.md)
* [Distribution binomiale Suite](machine-learning-r-csharp-binomial-distribution.md)

##<a name="faq"></a>FAQ
Pour des questions fréquemment posées sur la consommation du service web ou de publication à la place de marché, voir [ici](machine-learning-marketplace-faq.md).

[1]: ./media/machine-learning-r-csharp-web-service-examples/machine-learning-r-code-options-for-using-and-sharing-cloud.png


 

<properties
    pageTitle="Comment faire pour identifier les scénarios et prévoir les advanced traitement analytique des données | Microsoft Azure"
    description="Planifier les avancées analytique en tenant compte d’une série de questions clées."
    services="machine-learning"
    documentationCenter=""
    authors="bradsev"
    manager="jhubbard"
    editor="cgronlun" />

<tags
    ms.service="machine-learning"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/19/2016"
    ms.author="bradsev" />


# <a name="how-to-identify-scenarios-and-plan-for-advanced-analytics-data-processing"></a>Comment identifier les scénarios et planifier les traitement analytique avancée des données

Les ressources qui devez vous prévoyez d’inclure lors de la configuration d’un environnement faire analytique avancées traitement sur un jeu de données ? Cet article propose une série de poser des questions qui vous aidera à identifier les tâches et les ressources pertinentes votre scénario. L’ordre des principales étapes à suivre pour analytique prédictive est entourée de [Quel est le processus de scientifique des données (TDSP) d’équipe ?](data-science-process-overview.md). Chacune de ces étapes nécessite des ressources spécifiques pour les tâches pertinentes à votre situation. Les principales questions à identifier votre scénario concernent organisation des données, caractéristiques, la qualité des jeux de données et les outils et les langues que vous souhaitez effectuer l’analyse.

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

## <a name="logistic-questions-data-locations-and-movement"></a>Questions logistiques : emplacements des données et le déplacement
Les questions logistiques concernent l’emplacement de la **source de données**, la **destination de cibler** dans Azure et configuration requise pour le déplacement des données, y compris la planification et des ressources impliquées. Vous devrez les données à déplacer plusieurs fois au cours du processus analytique. Un scénario courant consiste à déplacer des données locales dans une forme de stockage sur Azure, puis en Machine apprentissage Studio.

1. **Quelle est votre source de données ?** S’agit-il local ou dans le cloud ? Par exemple :
    - Les données sont accessibles à une adresse HTTP.
    - Les données se trouvent dans un emplacement local/réseau.
    - Les données sont dans une base de données SQL Server.
    - Les données sont stockées dans un conteneur de stockage Azure

2. **Quelle est la destination Azure ?** Où doit-il être pour le traitement ou de modélisation ? Par exemple :
    - Stockage d’objets Blob Azure
    - Bases de données SQL Azure
    - SQL Server sur machine virtuelle Azure
    - HDInsight (Hadoop sur Azure) ou des tableaux Hive
    - Apprentissage automatique Azure
    - Monter Azure des disques durs virtuels.

3. **Comment allez-vous déplacer les données ?** Les procédures et les ressources disponibles d’acquisition ou charger les données dans une variété de stockage différents et environnements de traitement sont décrites dans les rubriques suivantes.

    -  [Charger les données dans les environnements de stockage pour analytique](machine-learning-data-science-ingest-data.md)
    -  [Importer vos données formation dans Azure Machine apprentissage Studio à partir de diverses sources de données](machine-learning-data-science-import-data.md).

4. **Les données doivent-elles être déplacés à intervalles réguliers ou modifiées lors de la migration ?** Envisagez d’utiliser Azure données usine (chargeur) lorsque les données devant être migrées en permanence, en particulier si un scénario hybride qui accède à la fois sur site et ressources de cloud intervient, ou lorsque les données sont traitées ou devant être modifié ou que la logique métier ajoutée dans le cadre en cours de migration. Pour plus d’informations, voir [déplacer des données d’un serveur SQL localement à SQL Azure avec Azure Data Factory](machine-learning-data-science-move-sql-azure-adf.md)

5. **La quantité de données doit être déplacé vers Azure ?** Jeux de données qui est très volumineux peut dépasser la capacité de stockage de certains environnements. Pour obtenir un exemple, consultez la rubrique sur les limites de taille pour Machine apprentissage Studio dans la section suivante. Dans ce cas, un exemple de données peut être utilisé lors de l’analyse. Pour plus d’informations pour un jeu de données dans divers environnements Azure échantillons vers le bas, voir des [exemples de données dans le processus d’équipe données scientifique](machine-learning-data-science-sample-data.md).


## <a name="data-characteristics-questions-type-format-and-size"></a>Questions de caractéristiques de données : type, format et la taille
Ces questions vous permettront de planification de votre espace de stockage et le traitement des environnements, chacun d'entre eux sont adaptées aux différents types de données et chacun d'entre eux ont certaines restrictions.

1. **Quels sont les types de données ?** Par exemple :
    - Numérique
    - Par catégorie
    - Chaînes
    - Binaire

2. **Comment vos données sont formatées ?** Par exemple :
    - Séparées par des virgules (CSV) ou séparé par des tabulations (TSV) plane fichiers
    - Compressé ou non compressé
    - Objets BLOB Azure
    - Tables Hadoop Hive
    - Tables SQL Server

2. **La taille sont vos données ?**
    - Petites : inférieure à 2 Go
    - Support : Supérieure à 2 et inférieure à 10 Go
    - Grande : Supérieure à 10 Go

Prenons l’exemple de l’environnement Azure Machine apprentissage Studio :

- Pour obtenir la liste des formats de données et types pris en charge par Azure Machine apprentissage Studio, reportez-vous à la section [types de données et les formats de données pris en charge](machine-learning-data-science-import-data.md#data-formats-and-data-types-supported) .
- Pour plus d’informations sur les limites de données pour Azure Machine apprentissage Studio, voir la **la taille du jeu de données possible pour mon modules ?** section [d’importation et exportation de données d’apprentissage automatique](machine-learning-faq.md#machine-learning-studio-questions)

Pour plus d’informations sur les limitations d’autres services Azure utilisés dans le processus analytique, voir [abonnement Azure et limites de Service, les Quotas et les contraintes](../azure-subscription-service-limits.md).

## <a name="data-quality-questions-exploration-and-pre-processing"></a>Questions de qualité de données : exploration et prétraitement

1. **Que savoir sur vos données ?** Explorer des données lorsque vous avez besoin d’obtenir un comprendre les caractéristiques de base. Que des modèles ou des tendances il illustrations, quelles sont les valeurs aberrantes a ou le nombre de valeurs est manquant. Cette étape est importante pour déterminer l’étendue de traitement préalable requis pour les formulation hypothèses qui pourraient suggérer les fonctionnalités les plus appropriées ou tapez d’analyse et de la formulation de plans pour la collecte de données supplémentaires. Calcul descriptives et le traçage visualisations sont techniques utiles pour le contrôle de données. Pour plus d’informations Explorer un jeu de données dans divers environnements Azure, voir [Explorer des données dans le processus d’équipe données scientifique](machine-learning-data-science-explore-data.md).

2. **Les données nécessite-t-il prétraitement ou nettoyage ?**
Prétraitement des et nettoyage des données sont des tâches importantes généralement doivent être effectués avant de dataset peut être utilisé efficacement pour apprentissage automatique. Données brutes sont souvent bruits et sources non fiables et peuvent être pas les valeurs. À l’aide de ce type de données pour une modélisation peut produire des résultats confusion. Pour obtenir une description, voir [tâches pour préparer des données pour une machine améliorée d’apprentissage](machine-learning-data-science-prepare-data.md).

## <a name="tools-and-languages-questions"></a>Questions sur outils et de langues
Il existe plusieurs possibilités ici selon les langues et les environnements de développement ou les outils de vous devez ou sont plus conformable à l’aide.

1. **Quelles sont les langues que vous souhaitez utiliser pour l’analyse**  
    - R
    - Python
    - SQL

2. **Quels outils doit utiliser pour l’analyse de données ?**
    - [Microsoft Azure Powershell](powershell-install-configure.md) - un langage de script permettant de gérer vos ressources Azure dans un langage de script.
    - [Machine Azure apprentissage Studio](machine-learning-what-is-ml-studio/)
    - [Révolution Analytique](http://www.revolutionanalytics.com/revolution-r-open)
    - [RStudio](http://www.rstudio.com)
    - [Outils Python pour Visual Studio](http://microsoft.github.io/PTVS/)
    - [Anaconda](https://www.continuum.io/why-anaconda)
    - [Blocs-notes Jupyter](http://jupyter.org/)
    - [Microsoft Power BI](http://powerbi.microsoft.com)


## <a name="identify-your-advanced-analytics-scenario"></a>Identifier votre scénario analytique avancées
Une fois que vous avez répondu aux questions dans la section précédente, vous êtes prêt à déterminer quel scénario meilleur adaptée à votre cas. Les exemples de scénarios sont décrites dans les [scénarios d’analytique avancée dans l’apprentissage automatique Azure](machine-learning-data-science-plan-sample-scenarios.md).

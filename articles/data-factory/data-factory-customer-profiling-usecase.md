<properties 
    pageTitle="Utiliser la casse - création de profils utilisateur" 
    description="Découvrez comment Azure Data Factory est utilisé pour créer un pilotées par les données du flux de travail (pipeline) pour les clients de jeux de profil." 
    services="data-factory" 
    documentationCenter="" 
    authors="sharonlo101" 
    manager="jhubbard" 
    editor="monicar"/>

<tags 
    ms.service="data-factory" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/06/2016" 
    ms.author="shlo"/>

# <a name="use-case---customer-profiling"></a>Utiliser la casse - création de profils utilisateur

Azure Data Factory est un des nombreux services utilisés pour implémenter l’Intelligence Cortana Suite d’accélérateurs de solutions.  Pour plus d’informations sur Cortana Intelligence, visitez [Cortana Intelligence Suite](http://www.microsoft.com/cortanaanalytics). Dans ce document, nous décrire un cas d’utilisation simple pour vous aider à prendre en main comprendre comment Azure Data Factory permet de résoudre les problèmes courants analytique.

Il vous suffit d’accéder et essayer ce cas d’utilisation simple est un [abonnement Azure](https://azure.microsoft.com/pricing/free-trial/).  Vous pouvez déployer un échantillon qui mettent en œuvre, ce cas d’utilisation en suivant les étapes décrites dans l’article [exemples](data-factory-samples.md) .

## <a name="scenario"></a>Scénario

Contoso est une société jeu qui crée des jeux pour plusieurs plateformes : fausser consoles poche et ordinateurs personnels (PC). Lecteurs de lire ces jeux, grand volume de données du journal est produit qui effectue le suivi des modèles d’utilisation, style de jeu et préférences de l’utilisateur.  Lorsqu’il est combiné avec démographiques, régionales et les données de produit, Contoso peut effectuer analytique pour le guider comment faire améliorer l’expérience de joueurs et cible les mises à jour et de la partie des achats. 

Objectif de Contoso consiste à identifier à distance-vendre/des opportunités de vente en fonction de l’historique des jeux de ses lecteurs et ajouter des fonctionnalités intéressantes à croissance de l’activité lecteur et améliorer l’expérience aux clients. Pour ce cas d’utilisation, nous utilisons une société jeu comme un exemple d’une entreprise. La société souhaite optimiser ses jeux basés sur le comportement de joueurs. Ces principes s’appliquent à toute entreprise qui souhaite recours à une de ses clients autour de ses produits et services et d’améliorer l’expérience de leurs clients.

## <a name="challenges"></a>Défis


## <a name="solution-overview"></a>Présentation de la solution

Le cas d’utilisation simple utilisable comme illustre comment vous pouvez utiliser Azure Data Factory d’acquisition, préparer, transformer, leur analyse et publier des données.

![Flux de travail de bout en bout](./media/data-factory-customer-profiling-usecase/EndToEndWorkflow.png)

Cette Figure décrit comment les pipelines de données s’affichent dans le portail Azure après que qu’ils ont été déployés.

1.  La **PartitionGameLogsPipeline** lit les événements jeu bruts depuis le stockage blob et crée des partitions en fonction de l’année, mois et jour.
2.  **EnrichGameLogsPipeline** joint partitionnées événements jeu des données de référence de code geo et enrichit les données en mappant des adresses IP vers les emplacements de geo correspondants.
3.  Le pipeline de **AnalyzeMarketingCampaignPipeline** utilise les données enrichies et traite avec les données publicitaires pour créer le résultat final qui contient l’efficacité de la campagne marketing.

Dans cet exemple, Data Factory est utilisé pour organiser les activités que copier des données d’entrée, de transformation et de traiter les données et copiez les données à une base de données SQL Azure finales.  Vous pouvez également visualiser le réseau de pipelines de données, les gérer et surveiller leur statut à partir de l’interface utilisateur.

## <a name="benefits"></a>Avantages

En optimisant leur analytique de profil utilisateur et alignement avec les objectifs, société jeu peut recueillir les modèles d’utilisation et d’analyser rapidement l’efficacité de ses campagnes marketing.





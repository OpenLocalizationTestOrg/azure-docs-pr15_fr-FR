<properties
    pageTitle="Module de PowerShell pour apprentissage automatique | Microsoft Azure"
    description="Le module PowerShell pour apprentissage automatique Azure est disponible en mode Aperçu public. Utiliser PowerShell pour créer et gérer des espaces de travail, des expériences, des services web et autres éléments."
    keywords="essayer, régression linéaire, algorithmes d’apprentissage machine, didacticiel d’apprentissage machine, techniques de modélisation prédictive expérience scientifique de données"
    services="machine-learning"
    documentationCenter=""
    authors="hning86"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="machine-learning"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/05/2016"
    ms.author="garye;haining"/>

# <a name="powershell-module-for-microsoft-azure-machine-learning"></a>Module de PowerShell pour Microsoft Azure Machine Learning

Le module PowerShell pour apprentissage automatique Azure est un outil puissant qui vous permet d’utiliser Windows PowerShell pour gérer les espaces de travail, expériences, jeux de données, serivces web et autres éléments.

Vous pouvez consulter la documentation et télécharger le module, ainsi que le code source complet, en [https://aka.ms/amlps](https://aka.ms/amlps). 

## <a name="what-is-the-machine-learning-powershell-module"></a>Quel est le module Machine apprentissage PowerShell ?

Le module Machine apprentissage PowerShell est un. En fonction des NET module de la DLL qui vous permet de gérer entièrement apprentissage automatique Azure espaces de travail, expériences, jeux de données, services web et points de terminaison de service web à partir de Windows PowerShell. Ainsi que le module, vous pouvez télécharger le code source complet qui inclut un [calque API c#](https://github.com/hning86/azuremlps/blob/master/code/AzureMLSDK.cs)séparés correctement. Cela signifie que vous pouvez faire référence à cette DLL à partir de votre propre projet .NET et gérer apprentissage automatique Azure à travers le code .NET. En outre, la DLL dépend de l’API REST sous-jacentes que vous pouvez exploiter directement à partir de votre client favori.

## <a name="what-can-i-do-with-the-powershell-module"></a>Que puis-je faire avec le module PowerShell ?

Voici quelques-unes des tâches que vous pouvez effectuer grâce à ce module PowerShell. Consultez la [documentation complète](https://aka.ms/amlps) de ces et davantage de fonctions.

- Configurer un espace de travail à l’aide d’un certificat de gestion ([New-AmlWorkspace](https://github.com/hning86/azuremlps#new-amlworkspace))
- Exporter et importer un fichier JSON représentant un graphique de l’expérience ([AmlExperimentGraph à l’exportation](https://github.com/hning86/azuremlps#export-amlexperimentgraph) et [Importation AmlExperimentGraph](https://github.com/hning86/azuremlps#import-amlexperimentgraph))
- Exécuter une expérience ([Démarrer AmlExperiment](https://github.com/hning86/azuremlps#start-amlexperiment))
- Créer un service web déconnecter une expérience prédictive ([New-AmlWebService](https://github.com/hning86/azuremlps#new-amlwebservice))
- Créer un point de terminaison sur un service web publiés ([Ajouter AmlWebServiceEndpoint](https://github.com/hning86/azuremlps#add-amlwebserviceendpoint))
- Appeler un enregistrements et/ou BES web service point de terminaison ([Appeler AmlWebServiceRRSEndpoint](https://github.com/hning86/azuremlps#invoke-amlwebservicerrsendpoint) et [Appeler AmlWebServicBESEndpoint](https://github.com/hning86/azuremlps#invoke-amlwebservicebesendpoint))

Voici un exemple rapide de l’utilisation de PowerShell pour exécuter une expérience existante :

        #Find the first Experiment named “xyz”
        $exp = (Get-AmlExperiment | where Description -eq ‘xyz’)[0]
        #Run the Experiment
        Start-AmlExperiment -ExperimentId $exp.ExperimentId 

Pour un cas d’utilisation plus détaillé, consultez cet article sur l’utilisation du module PowerShell pour automatiser une tâche très demandés : [créer de nombreux modèles d’apprentissage automatique et web points de terminaison de service à partir d’une expérience à l’aide de PowerShell](machine-learning-create-models-and-endpoints-with-powershell.md).

## <a name="how-do-i-get-started"></a>Comment puis-je commencer ?

Pour commencer à utiliser PowerShell d’apprentissage Machine, téléchargez le [package de version](https://github.com/hning86/azuremlps/releases) GitHub et suivez les [instructions d’installation](https://github.com/hning86/azuremlps/blob/master/README.md). Vous devez débloquer la DLL téléchargé/décompressés, puis les importer dans votre environnement PowerShell. La plupart des applets de commande requièrent que vous fournissez l’ID de l’espace de travail, le jeton d’autorisation espace de travail et la région Azure figurant dans l’espace de travail. Le moyen le plus simple pour fournir ces consiste à utiliser un fichier de config.json par défaut, qui est couvertes en détail dans les instructions d’installation. Bien entendu, vous pouvez également cloner l’arborescence git et modifier/compiler le code localement à l’aide de Visual Studio.

## <a name="next-steps"></a>Étapes suivantes

Le module PowerShell continuent à être améliorée et développés pendant cette période d’aperçu. Surveillez [Cortana Intelligence et Machine apprentissage Blog](https://blogs.technet.microsoft.com/machinelearning/) pour plus d’informations et de news.

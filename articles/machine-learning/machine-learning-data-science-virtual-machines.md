<properties
    pageTitle="Machines virtuelles scientifique données dans Azure | Microsoft Azure"
    description="Définir l’une Machine virtuelle de données scientifique"
    services="machine-learning"
    documentationCenter=""
    authors="bradsev"
    manager="jhubbard" 
    editor="cgronlun"  />

<tags
    ms.service="machine-learning"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/19/2016"
    ms.author="xibingao;bradsev" />

# <a name="data-science-virtual-machines-in-azure"></a>Machines virtuelles scientifique données dans Azure

Des instructions sont fournies ici qui décrivent comment configurer une machine virtuelle Azure et une machine virtuelle Azure avec le Service SQL en tant que serveurs IPython bloc-notes. La machine virtuelle Windows est configurée avec prise en charge d’outils tels que le bloc-notes IPython, Explorateur de stockage Azure et AzCopy, ainsi que d’autres utilitaires pouvant être utilisés pour les projets scientifique de données. Explorateur de stockage Azure et AzCopy, par exemple, fournissent peut être pratique pour télécharger des données sur stockage Azure à partir de votre ordinateur local ou pour le télécharger sur votre ordinateur local à partir du stockage. 

Ce menu liens vers des rubriques qui décrivent comment configurer les différents environnements de science données utilisés par l' [Équipe données scientifique processus (TDSP)](data-science-process-overview.md).

[AZURE.INCLUDE [data-science-environment-setup](../../includes/cap-setup-environments.md)]

Plusieurs types de machines virtuelles Azure peuvent être sa mise en service et configurés pour être utilisé dans le cadre d’un environnement scientifique de données sur le nuage. La décision sur la version de machine virtuelle d’utiliser varie selon le type et la quantité de données à modéliser avec apprentissage automatique et la destination pour ces données dans le cloud. 

* Pour des instructions sur les questions à prendre en considération lorsque vous prenez cette décision, voir [Planifier votre Azure Machine apprentissage scientifique environnement de données](machine-learning-data-science-plan-your-environment.md). 
* Pour un catalogue de certains scénarios que vous pouvez rencontrer lors de l’exécution analytique avancées, voir [scénarios pour avancées Analytique processus et technologie dans l’apprentissage automatique Azure](machine-learning-data-science-plan-sample-scenarios.md)

Deux ensembles d’instructions sont fournis :

* [Configurer une machine virtuelle Azure comme un serveur IPython bloc-notes pour avancées analytique](machine-learning-data-science-setup-virtual-machine.md) montre comment configurer une machine virtuelle Azure avec IPython bloc-notes et d’autres outils permettant d’effectuer scientifique de données dans les cas dans lesquels une forme de stockage Azure différent de SQL peut servir à stocker les données.

* [Configurer une machine virtuelle Azure SQL Server comme un serveur IPython bloc-notes pour avancées analytique](machine-learning-data-science-setup-sql-server-virtual-machine.md) montre comment configurer une machine virtuelle Azure SQL Server avec IPython bloc-notes et d’autres outils permettant d’effectuer scientifique de données dans les cas dans lesquels une base de données SQL peut servir à stocker les données.

Une fois généré et configuré, ces machines virtuelles sont prêts à l’emploi en tant que serveurs IPython bloc-notes pour l’exploration et le traitement des données et d’autres tâches nécessaires conjointement avec apprentissage automatique Azure et le processus de scientifique des données (TDSP) d’équipe. Les étapes du processus de données scientifique sont mappées dans la [rubrique d’apprentissage TDSP](https://azure.microsoft.com/documentation/learning-paths/cortana-analytics-process/) et peuvent inclure les étapes qui déplacement des données dans SQL Server ou HDInsight, processus et aperçu en vue de la formation à partir des données avec Azure d’apprentissage automatique.


> [AZURE.NOTE] Machines virtuelles Azure sont facturés comme **payez uniquement ce que vous utilisez**. Pour vous assurer que vous ne soyez pas facturé lorsque vous N'utilisez pas votre machine virtuelle, il doit se trouver dans l’état **arrêté (Deallocated)** à partir du [Portail classique Azure](http://manage.windowsazure.com/). Pour obtenir des instructions étape par étape ou comment vous libérer machine virtuelle, voir [arrêt et libérer la machine virtuelle lorsqu’elles pas en cours d’utilisation](machine-learning-data-science-setup-virtual-machine.md#shutdown)
 

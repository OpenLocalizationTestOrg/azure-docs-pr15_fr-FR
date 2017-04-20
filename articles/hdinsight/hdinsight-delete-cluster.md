<properties
pageTitle="Comment supprimer un cluster HDInsight | Azure"
description="Informations sur les différentes méthodes que vous pouvez supprimer un cluster HDInsight."
services="hdinsight"
documentationCenter=""
authors="Blackmist"
manager="jhubbard"
editor="cgronlun"/>

<tags
ms.service="hdinsight"
ms.devlang="na"
ms.topic="article"
ms.tgt_pltfrm="na"
ms.workload="big-data"
ms.date="10/28/2016"
ms.author="larryfr"/>

#<a name="how-to-delete-an-hdinsight-cluster"></a>Comment supprimer un cluster HDInsight

HDInsight cluster facturation démarre une fois qu’un cluster est créé et s’arrête lorsque le cluster est supprimé et étant au prorata par minute, vous devriez toujours supprimer votre cluster lorsqu’il n’est plus en cours d’utilisation. Dans ce document, vous allez apprendre à supprimer un cluster en utilisant le portail Azure Azure PowerShell et l’infrastructure du langage commun Azure.

> [AZURE.IMPORTANT] Supprimer un cluster HDInsight ne supprime pas l’ou les comptes Azure stockage associé au cluster. Cela vous permet de préserver et réutiliser toutes les données stockées par le cluster.

##<a name="azure-portal"></a>Portail Azure

1. Connexion au [portail Azure](https://portal.azure.com) et sélectionnez votre cluster HDInsight. Si votre cluster HDInsight n’est pas épinglée au tableau de bord, vous pouvez rechercher pour qu’elle par nom en utilisant le champ de recherche (icône de loupe), sur le côté droit de la barre de navigation.

    ![recherche du portail](./media/hdinsight-delete-cluster/navbar.png)

2. Une fois la carte ouvert pour le cluster, sélectionnez l’icône __Supprimer__ . Lorsque vous y êtes invité, sélectionnez __Oui__ pour supprimer le cluster.

    ![icône Supprimer](./media/hdinsight-delete-cluster/deletecluster.png)

##<a name="azure-powershell"></a>PowerShell Azure

À partir d’une invite PowerShell, utilisez la commande suivante pour supprimer le cluster :

    Remove-AzureRmHDInsightCluster -ClusterName CLUSTERNAME

Remplacez __NOMDUCLUSTER__ par le nom de votre cluster HDInsight.

##<a name="azure-cli"></a>Azure infrastructure du langage commun

À l’invite, utilisez ce qui suit pour supprimer le cluster :

    azure hdinsight cluster delete CLUSTERNAME
    
Remplacez __NOMDUCLUSTER__ par le nom de votre cluster HDInsight.

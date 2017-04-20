<properties
    pageTitle="Ouvre une application Hadoop fils Access par programme | Microsoft Azure"
    description="Ouvre une application Access par programme sur un cluster Hadoop dans HDInsight."
    services="hdinsight"
    documentationCenter=""
    tags="azure-portal"
    authors="mumian" 
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="hdinsight"
    ms.workload="big-data"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/19/2016"
    ms.author="jgao"/>

# <a name="access-yarn-application-logs-on-windows-based-hdinsight"></a>Application fils Access ouvre une session sur HDInsight fonctionnant sous Windows

Cette rubrique explique comment accéder aux journaux des applications fils (encore une autre ressource négociateur) que vous ont terminé de sur un cluster Hadoop dans Azure HDInsight

> [AZURE.NOTE] Les informations contenues dans ce document s’applique uniquement aux clusters HDInsight fonctionnant sous Windows. Pour des informations sur l’accès à des fils se connecte clusters basés sur Linux HDInsight, voir [application Access fils ouvre une session sur basé sur Linux Hadoop sur HDInsight](hdinsight-hadoop-access-yarn-app-logs-linux.md)

### <a name="prerequisites"></a>Conditions préalables

- Un cluster HDInsight fonctionnant sous Windows.  Voir [Hadoop fonctionnant sous Windows de créer des groupes dans un HDInsight](hdinsight-provision-clusters.md).


## <a name="yarn-timeline-server"></a>FILS chronologie Server

Le <a href="http://hadoop.apache.org/docs/r2.4.0/hadoop-yarn/hadoop-yarn-site/TimelineServer.html" target="_blank">Serveur de chronologie fils</a> fournit des informations génériques sur applications complètes ainsi que des informations spécifiques à l’infrastructure application via deux interfaces différentes. Plus précisément :

* Stockage et l’extraction des informations sur l’application générique sur clusters HDInsight a été activée avec la version 3.1.1.374 ou une version ultérieure.
* Le composant d’informations spécifiques à l’infrastructure application du serveur chronologie n’est pas actuellement disponible sur les clusters HDInsight.


Informations générales sur les applications incluent les types suivants de données :

* L’ID de l’application, un identificateur unique d’une application
* L’utilisateur qui a démarré l’application
* Informations sur les tentatives pour terminer l’application
* Les conteneurs utilisés par toute tentative d’application donnée

Sur vos clusters HDInsight, ces informations seront stockées par le Gestionnaire de ressources Azure à un magasin de l’historique dans le conteneur par défaut de votre compte de stockage Azure par défaut. Ces données génériques applications terminées peuvent être récupérées via une API REST :

    GET on https://<cluster-dns-name>.azurehdinsight.net/ws/v1/applicationhistory/apps


## <a name="yarn-applications-and-logs"></a>Les journaux et les applications fils

FILS prend en charge plusieurs modèles de programmation (MapReduce d'entre eux étant) par la gestion des ressources de l’application planification/analyse de découplage. Pour ce faire via un global *ResourceManager* (RM), nœud de travail *NodeManagers* (NMs) et de l’application *ApplicationMasters* (AMs). La AM par application ressources (processeur, mémoire, disque, réseau) tente d’obtenir complexe exécuter votre application avec le Gestionnaire de ressources. Le Gestionnaire de ressources fonctionne avec NMs octroyer à ces ressources, qui sont accordées en tant que *conteneurs*. La AM est responsable de suivi de l’avancement des conteneurs affectées par le RM. Une application peut nécessiter de nombreux conteneurs selon la nature de l’application.

En outre, chaque application peut contenir plusieurs *application tente* afin de terminer en présence se bloque ou en raison de la perte de communication entre un AM et un RM. Par conséquent, conteneurs sont accordées à une tentative d’une application spécifique. Dans un sens, un conteneur fournit la description de l’unité de base du travail effectué par une application fils et tout le travail est effectué dans le contexte d’un conteneur est effectué sur le nœud de travail unique sur lequel le conteneur a été attribué. Voir [Concepts fils] [ YARN-concepts] pour référence ultérieure.

Journaux d’application (et les journaux de conteneur associées) sont essentiels dans débogage d’applications Hadoop problématiques. FILS fournit une infrastructure piles pour recueillir, agréger et le stockage des journaux d’application avec l' [Agrégation de journal] [ log-aggregation] fonctionnalité. La fonction d’agrégation journal rend l’accès aux journaux d’application plus déterministe, tel qu’il regroupe les journaux sur tous les conteneurs sur un nœud de travail et les stocke sous forme d’un fichier journal agrégé par poste de travail sur le système de fichiers par défaut après se termine à une application. Votre application peut utiliser des centaines ou des milliers de conteneurs, mais les journaux de tous les conteneurs exécutés sur un seul collaborateur nœud seront toujours agrégées à un seul fichier, ce qui entraîne un fichier journal par nœud de travail utilisé par votre application. Agrégation de journal est activée par défaut sur les clusters HDInsight (version 3.0 et versions ultérieures), et les journaux agrégés se trouve dans le conteneur par défaut de votre cluster à l’emplacement suivant :

    wasbs:///app-logs/<user>/logs/<applicationId>

Dans la mesure où un emplacement, *l’utilisateur* est le nom de l’utilisateur qui a démarré l’application et *applicationId* est l’identificateur unique d’une application attribué par le RM fils.

Les journaux agrégés ne sont pas lisibles directement, tels qu’ils apparaissent dans un [TFile][T-file], [format binaire] [ binary-format] indexées par conteneur. FILS fournit des outils d’infrastructure du langage commun pour vider ces fichiers journaux au format texte brut pour les applications ou conteneurs d’intérêt. Vous pouvez afficher ces fichiers journaux au format texte brut en exécutant une des suivant fil commandes directement sous les nœuds de cluster (après vous être connecté à celui-ci via RDP) :

    yarn logs -applicationId <applicationId> -appOwner <user-who-started-the-application>
    yarn logs -applicationId <applicationId> -appOwner <user-who-started-the-application> -containerId <containerId> -nodeAddress <worker-node-address>


## <a name="yarn-resourcemanager-ui"></a>Interface utilisateur ResourceManager fils

L’interface utilisateur ResourceManager fils s’exécute sur le headnode cluster et sont accessibles via le tableau de bord du portail Azure : 

1. Se connecter au [portail Azure](https://portal.azure.com/). 
2. Dans le menu de gauche, cliquez sur **Parcourir**, cliquez sur **HDInsight Clusters**un cluster de Windows que vous souhaitez accéder les journaux des applications fils.
3. Dans le menu supérieur, cliquez sur **tableau de bord**. Vous verrez une page ouverte dans une nouvelle fenêtre de navigateur onglet appelée **Console de requête HDInsight**.
4. À partir de **HDInsight requête Console**, cliquez sur **L’interface utilisateur fils**.




[YARN-timeline-server]:http://hadoop.apache.org/docs/r2.4.0/hadoop-yarn/hadoop-yarn-site/TimelineServer.html
[log-aggregation]:http://hortonworks.com/blog/simplifying-user-logs-management-and-access-in-yarn/
[T-file]:https://issues.apache.org/jira/secure/attachment/12396286/TFile%20Specification%2020081217.pdf
[binary-format]:https://issues.apache.org/jira/browse/HADOOP-3315
[YARN-concepts]:http://hortonworks.com/blog/apache-hadoop-yarn-concepts-and-applications/

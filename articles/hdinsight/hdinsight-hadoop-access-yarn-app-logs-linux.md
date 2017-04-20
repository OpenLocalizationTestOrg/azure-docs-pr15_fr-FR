<properties
    pageTitle="Application Hadoop fils Access ouvre une session sur basé sur Linux HDInsight | Microsoft Azure"
    description="Découvrez comment accéder aux journaux d’application fils sur un cluster basé sur Linux HDInsight (Hadoop) à l’aide de la ligne de commande et un navigateur web."
    services="hdinsight"
    documentationCenter=""
    tags="azure-portal"
    authors="Blackmist" 
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="hdinsight"
    ms.workload="big-data"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/21/2016"
    ms.author="larryfr"/>

# <a name="access-yarn-application-logs-on-linux-based-hdinsight"></a>Application fils Access ouvre une session sur HDInsight basé sur Linux 

Ce document explique comment accéder aux journaux des applications fils (encore une autre ressource négociateur) que vous ont terminé de sur un cluster Hadoop dans Azure HDInsight.

> [AZURE.NOTE] Les informations de ce document sont spécifiques aux clusters HDInsight basé sur Linux. Pour plus d’informations sur les clusters fonctionnant sous Windows, consultez [application Access fils ouvre une session HDInsight fonctionnant sous Windows](hdinsight-hadoop-access-yarn-app-logs.md)

## <a name="prerequisites"></a>Conditions préalables

* Un cluster basé sur Linux HDInsight.

* Vous devez [créer un tunnel SSH](hdinsight-linux-ambari-ssh-tunnel.md) avant d’accéder à l’interface utilisateur de ResourceManager journaux web.

## <a name="YARNTimelineServer"></a>FILS chronologie Server

Le [Serveur de chronologie fils](http://hadoop.apache.org/docs/r2.4.0/hadoop-yarn/hadoop-yarn-site/TimelineServer.html) fournit des informations génériques sur applications complètes ainsi que des informations spécifiques à l’infrastructure application via deux interfaces différentes. Plus précisément :

* Stockage et l’extraction des informations sur l’application générique sur clusters HDInsight a été activée avec la version 3.1.1.374 ou une version ultérieure.
* Le composant d’informations spécifiques à l’infrastructure application du serveur chronologie n’est pas actuellement disponible sur les clusters HDInsight.

Informations générales sur les applications incluent les types suivants de données :

* L’ID de l’application, un identificateur unique d’une application
* L’utilisateur qui a démarré l’application
* Informations sur les tentatives pour terminer l’application
* Les conteneurs utilisés par toute tentative d’application donnée

## <a name="YARNAppsAndLogs"></a>Les journaux et les applications fils

FILS prend en charge plusieurs modèles de programmation (MapReduce d'entre eux étant) par la gestion des ressources de l’application planification/analyse de découplage. Pour ce faire via un global *ResourceManager* (RM), nœud de travail *NodeManagers* (NMs) et de l’application *ApplicationMasters* (AMs). La AM par application ressources (processeur, mémoire, disque, réseau) tente d’obtenir complexe exécuter votre application avec le Gestionnaire de ressources. Le Gestionnaire de ressources fonctionne avec NMs octroyer à ces ressources, qui sont accordées en tant que *conteneurs*. La AM est responsable de suivi de l’avancement des conteneurs affectées par le RM. Une application peut nécessiter de nombreux conteneurs selon la nature de l’application.

En outre, chaque application peut contenir plusieurs *application tente* afin de terminer en présence se bloque ou en raison de la perte de communication entre un AM et un RM. Par conséquent, conteneurs sont accordées à une tentative d’une application spécifique. Dans un sens, un conteneur fournit la description de l’unité de base du travail effectué par une application fils et tout le travail est effectué dans le contexte d’un conteneur est effectué sur le nœud de travail unique sur lequel le conteneur a été attribué. Voir [Concepts fils] [ YARN-concepts] pour référence ultérieure.

Journaux d’application (et les journaux de conteneur associées) sont essentiels dans débogage d’applications Hadoop problématiques. FILS fournit une infrastructure piles pour recueillir, agréger et le stockage des journaux d’application avec l' [Agrégation de journal] [ log-aggregation] fonctionnalité. La fonction d’agrégation journal rend l’accès aux journaux d’application plus déterministe, tel qu’il regroupe les journaux sur tous les conteneurs sur un nœud de travail et les stocke sous forme d’un fichier journal agrégé par poste de travail sur le système de fichiers par défaut après se termine à une application. Votre application peut utiliser des centaines ou des milliers de conteneurs, mais les journaux de tous les conteneurs exécutés sur un seul collaborateur nœud seront toujours agrégées à un seul fichier, ce qui entraîne un fichier journal par nœud de travail utilisé par votre application. Agrégation de journal est activée par défaut sur les clusters HDInsight (version 3.0 et versions ultérieures), et les journaux agrégés se trouve dans le conteneur par défaut de votre cluster à l’emplacement suivant :

    wasbs:///app-logs/<user>/logs/<applicationId>

Dans la mesure où un emplacement, *l’utilisateur* est le nom de l’utilisateur qui a démarré l’application et *applicationId* est l’identificateur unique d’une application attribué par le RM fils.

Les journaux agrégés ne sont pas lisibles directement, tels qu’ils apparaissent dans un [TFile][T-file], [format binaire] [ binary-format] indexées par conteneur. Vous devez utiliser les journaux des fils ResourceManager ou les outils de l’infrastructure du langage commun pour afficher ces fichiers journaux au format texte brut pour les applications ou conteneurs d’intérêt. 

##<a name="yarn-cli-tools"></a>Outils de l’infrastructure du langage commun fils

Afin d’utiliser les outils de l’infrastructure du langage commun fils, vous devez tout d’abord vous connecter au cluster HDInsight à l’aide de SSH. Pour plus d’informations sur l’utilisation de SSH avec HDInsight, utilisez un des documents suivants :

- [Utiliser le protocole SSH avec basé sur Linux Hadoop sur HDInsight de Linux, Unix ou OS X](hdinsight-hadoop-linux-use-ssh-unix.md)

- [Utiliser le protocole SSH avec basé sur Linux Hadoop sur HDInsight à partir de Windows](hdinsight-hadoop-linux-use-ssh-windows.md)
    
Vous pouvez afficher ces fichiers journaux au format texte brut en exécutant une des commandes suivantes :

    yarn logs -applicationId <applicationId> -appOwner <user-who-started-the-application>
    yarn logs -applicationId <applicationId> -appOwner <user-who-started-the-application> -containerId <containerId> -nodeAddress <worker-node-address>
    
Vous devez spécifier la &lt;applicationId >, &lt;utilisateur qui-mise en route-applications >, &lt;Id_conteneur >, et & adresse de nœud ltworker > informations lors de l’exécution de ces commandes.

##<a name="yarn-resourcemanager-ui"></a>Interface utilisateur ResourceManager fils

L’interface utilisateur ResourceManager fils s’exécute sur le headnode cluster et sont accessibles via le site web Ambari l’interface utilisateur ; Toutefois, vous devez d’abord [créer un tunnel SSH](hdinsight-linux-ambari-ssh-tunnel.md) avant de pouvoir accéder le ResourceManager UI.

Une fois que vous avez créé un tunnel SSH, procédez comme suit pour afficher les journaux fils :

1. Dans votre navigateur web, accédez à https://CLUSTERNAME.azurehdinsight.net. Remplacez NOMDUCLUSTER par le nom de votre cluster HDInsight.

2. Dans la liste des services sur la gauche, sélectionnez __fils__.

    ![Service fils sélectionné](./media/hdinsight-hadoop-access-yarn-app-logs-linux/yarnservice.png)

3. Dans le menu déroulant __Liens rapides__ , sélectionnez une des nœuds de tête du cluster, puis sélectionnez __Journal ResourceManager__.

    ![Linnks rapide fils](./media/hdinsight-hadoop-access-yarn-app-logs-linux/yarnquicklinks.png)
    
    Une liste de liens vers les journaux fils s’affiche.

[YARN-timeline-server]:http://hadoop.apache.org/docs/r2.4.0/hadoop-yarn/hadoop-yarn-site/TimelineServer.html
[log-aggregation]:http://hortonworks.com/blog/simplifying-user-logs-management-and-access-in-yarn/
[T-file]:https://issues.apache.org/jira/secure/attachment/12396286/TFile%20Specification%2020081217.pdf
[binary-format]:https://issues.apache.org/jira/browse/HADOOP-3315
[YARN-concepts]:http://hortonworks.com/blog/apache-hadoop-yarn-concepts-and-applications/

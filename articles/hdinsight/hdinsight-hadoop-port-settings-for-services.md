<properties
pageTitle="Ports utilisés par HDInsight | Azure"
description="Liste de ports utilisés par les services Hadoop s’exécutant sur HDInsight."
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
ms.date="10/03/2016"
ms.author="larryfr"/>

# <a name="ports-and-uris-used-by-hdinsight"></a>Ports et protocoles MU utilisé par HDInsight

Ce document fournit une liste des ports utilisés par les services Hadoop s’exécutant sur clusters HDInsight basé sur Linux. Il fournit également des informations sur les ports utilisés pour se connecter au cluster à l’aide de SSH.

## <a name="public-ports-vs-non-public-ports"></a>Ports publics et non publics ports

Basé sur Linux HDInsight clusters expose uniquement trois ports public sur internet ; 22, 23 et 443. Celles-ci sont utilisées pour accéder en toute sécurité le cluster à l’aide de SSH et services exposés via le protocole HTTPS sécurisé.

En interne, HDInsight est mise en œuvre par plusieurs Azure Machines virtuelles (nœuds au sein du cluster) en cours d’exécution sur un réseau virtuel Azure. Au sein du réseau virtuel, vous pouvez accéder ports ne pas exposés par le biais d’internet. Par exemple, si vous vous connectez à un des nœuds têtes à l’aide de SSH, du nœud de tête vous pouvez puis directement accéder aux services en cours d’exécution sur les nœuds du cluster.

> [AZURE.IMPORTANT] Lorsque vous créez un cluster de HDInsight, si vous ne spécifiez pas un réseau virtuel Azure comme option de configuration, un bloc-notes est créé ; Toutefois, vous ne pouvez pas joindre d’autres ordinateurs (par exemple, les autres Machines virtuelles Azure ou votre ordinateur de développement client,) à ce créé automatiquement réseau virtuel. 

Pour accéder à d’autres ordinateurs au réseau virtuel, vous devez tout d’abord créer le réseau virtuel et définissez-le lorsque vous créez votre cluster HDInsight. Pour plus d’informations, voir [fonctionnalités d’étendre HDInsight à l’aide d’un réseau virtuel Azure](hdinsight-extend-hadoop-virtual-network.md)

## <a name="public-ports"></a>Ports publics

Tous les nœuds dans un cluster HDInsight se trouvent dans un réseau virtuel Azure et ne sont pas accessibles directement à partir d’internet. Une passerelle publique fournit un accès internet pour les ports suivants, qui sont communes à tous les types de cluster HDInsight.

| Service | Port | Protocole | Description |
| ---- | ---------- | -------- | ----------- | ----------- |
| sshd | 22 | SSH | Se connecte clients à sshd sur le headnode principal. Voir [utiliser SSH avec HDInsight basé sur Linux](hdinsight-hadoop-linux-use-ssh-windows.md) |
| sshd | 22 | SSH | Se connecte clients à sshd sur le nœud de bord (HDInsight Premium uniquement). Voir [prise en main à l’aide de serveur R sur HDInsight](hdinsight-hadoop-r-server-get-started.md) |
| sshd | 23 | SSH | Se connecte clients à sshd sur la headnode secondaire. Voir [utiliser SSH avec HDInsight basé sur Linux](hdinsight-hadoop-linux-use-ssh-windows.md) |
| Ambari | 443 | HTTPS | Interface utilisateur web Ambari. Consultez [Gérer HDInsight à l’aide de l’interface utilisateur Web Ambari](hdinsight-hadoop-manage-ambari.md) |
| Ambari | 443 | HTTPS | API REST Ambari. Consultez [Gérer HDInsight à l’aide de l’API REST Ambari](hdinsight-hadoop-manage-ambari-rest-api.md) |
| WebHCat | 443 | HTTPS | API REST HCatalog. Voir [utiliser Hive bouclé](hdinsight-hadoop-use-pig-curl.md), [Utilisez cochon bouclé](hdinsight-hadoop-use-pig-curl.md), [Utilisez MapReduce bouclé](hdinsight-hadoop-use-mapreduce-curl.md) |
| HiveServer2 | 443 | ODBC | Se connecte à Hive à l’aide de ODBC. Voir [Connecter Excel à HDInsight avec le pilote ODBC Microsoft](hdinsight-connect-excel-hive-odbc-driver.md). |
| HiveServer2 | 443 | JDBC | Se connecte à Hive à l’aide de JDBC. Voir [se connecter à Hive sur HDInsight à l’aide du pilote JDBC ruche](hdinsight-connect-hive-jdbc-driver.md) |

Les éléments suivants sont disponibles pour les types de cluster spécifique :

| Service | Port | Protocole |Type de cluster | Description |
| ------------ | ---- |  ----------- | --- | ----------- |
| Stargate | 443 | HTTPS | HBase | API REST HBase. Voir [prise en main à l’aide de HBase](hdinsight-hbase-tutorial-get-started-linux.md) |
| Livy | 443 | HTTPS |  Explosion | API REST explosion. Voir [explosion soumettre des travaux à distance en utilisant Livy](hdinsight-apache-spark-livy-rest-interface.md) |
| Vague d' | 443 | HTTPS | Vague d' | Interface utilisateur web vague. Voir [déployer et gérer des topologies vague sur HDInsight](hdinsight-storm-deploy-monitor-topology-linux.md)

### <a name="authentication"></a>Authentification

Tous les services accessibles sur internet doivent être authentifiés :

| Port | Informations d’identification |
| ---- | ----------- |
| 22 ou 23 | Les informations d’identification utilisateur SSH spécifiées lors de la création de cluster |
| 443 | Le nom de connexion (par défaut : administrateur,) et mot de passe qui ont été définis au cours de la création d’un cluster |

## <a name="non-public-ports"></a>Ports non publics

> [AZURE.NOTE] Certains services sont disponibles uniquement sous types de cluster spécifique. Par exemple, HBase est uniquement disponible sur les types de cluster HBase.

### <a name="hdfs-ports"></a>Ports HADOOP

| Service | Ou plusieurs nœuds | Port | Protocole | Description |
| ------- | ------- | ---- | -------- | ----------- | 
| Interface utilisateur NameNode web | Nœuds de tête | 30070 | HTTPS | Interface utilisateur pour afficher l’état actuel Web |
| Service de métadonnées NameNode | nœuds de tête | 8020 | IPC | Métadonnées de système de fichiers 
| DataNode | Tous les nœuds de travail | 30075 | HTTPS | Interface utilisateur Web pour afficher l’état, les journaux, etc.. |
| DataNode | Tous les nœuds de travail | 30010 | &nbsp; | Transfert de données |
| DataNode | Tous les nœuds de travail | 30020 | IPC | Opérations de métadonnées |
| NameNode secondaire | Nœuds de tête | 50090 | HTTP | Point de contrôle pour les métadonnées NameNode |

### <a name="yarn-ports"></a>Ports fils

| Service | Ou plusieurs nœuds | Port | Protocole | Description |
| ------- | ------- | ---- | -------- | ----------- |
| Interface utilisateur de gestionnaire de ressources web | Nœuds de tête | 8088 | HTTP | Interface utilisateur pour le Gestionnaire de ressources Web |
| Interface utilisateur de gestionnaire de ressources web | Nœuds de tête | 8090 | HTTPS | Interface utilisateur pour le Gestionnaire de ressources Web |
| Interface d’administration du Gestionnaire de ressources | nœuds de tête | 8141 | IPC | Pour les soumissions application (Hive, serveur Hive, cochon, etc..) |
| Planificateur de gestionnaire de ressources | nœuds de tête | 8030 | HTTP | Interface d’administration |
| Interface d’application Gestionnaire de ressources | nœuds de tête | 8050 | HTTP |Adresse de l’interface de gestionnaire d’applications |
| NodeManager | Tous les nœuds de travail | 30050 | &nbsp; | L’adresse du Gestionnaire de conteneur |
| Interface utilisateur NodeManager web | Tous les nœuds de travail | 30060 | HTTP | Interface du Gestionnaire de ressources |
| Adresse de la barre de planning | Nœuds de tête | 10200 | RPC | Le service RPC du service chronologie. |
| Interface utilisateur web de barre de planning | Nœuds de tête | 8181 | HTTP | L’interface utilisateur du web service chronologie |

### <a name="hive-ports"></a>Ports Hive

| Service | Ou plusieurs nœuds | Port | Protocole | Description |
| ------- | ------- | ---- | -------- | ----------- |
| HiveServer2 | Nœuds de tête | 10001 | Thrift | Service de connexion par programme à Hive (Thrift/JDBC) |
| HiveServer | Nœuds de tête | 10000 | Thrift | Service de connexion par programme à Hive (Thrift/JDBC) |
| Hive Metastore | Nœuds de tête | 9083 | Thrift | Service de connexion par programme Hive métadonnées (Thrift/JDBC) |

### <a name="webhcat-ports"></a>Ports WebHCat

| Service | Ou plusieurs nœuds | Port | Protocole | Description |
| ------- | ------- | ---- | -------- | ----------- |
| Serveur WebHCat | Nœuds de tête | 30111 | HTTP | API Web en haut de HCatalog et d’autres services Hadoop |

### <a name="mapreduce-ports"></a>Ports MapReduce

| Service | Ou plusieurs nœuds | Port | Protocole | Description |
| ------- | ------- | ---- | -------- | ----------- |
| JobHistory | Nœuds de tête | 19888 | HTTP | Interface utilisateur MapReduce JobHistory web |
| JobHistory | Nœuds de tête | 10020 | &nbsp; | MapReduce JobHistory server |
| ShuffleHandler | &nbsp; | 13562 | &nbsp; | Mappage intermédiaire transferts sorties à la demande de REDUCTEURS |

### <a name="oozie"></a>Oozie

| Service | Ou plusieurs nœuds | Port | Protocole | Description |
| ------- | ------- | ---- | -------- | ----------- |
| Serveur Oozie | Nœuds de tête | 11000 | HTTP | URL pour le service Oozie |
| Serveur Oozie | Nœuds de tête | 11001 | HTTP | Port pour Oozie d’administration |

### <a name="ambari-metrics"></a>Indicateurs de Ambari

| Service | Ou plusieurs nœuds | Port | Protocole | Description |
| ------- | ------- | ---- | -------- | ----------- |
| Application (historique) | Nœuds de tête | 6188 | HTTP | L’interface utilisateur du web service chronologie |
| Application (historique) | Nœuds de tête | 30200 | RPC | L’interface utilisateur du web service chronologie |

### <a name="hbase-ports"></a>Ports HBase

| Service | Ou plusieurs nœuds | Port | Protocole | Description |
| ------- | ------- | ---- | -------- | ----------- |
| HMaster | Nœuds de tête | 16000 | &nbsp; | &nbsp; |
| Informations HMaster l’interface utilisateur Web | Nœuds de tête | 16010 | HTTP | Le port pour le site web HBase masque l’interface utilisateur |
| Serveur de région | Tous les nœuds de travail | 16020 | &nbsp; | &nbsp; |
| &nbsp; | &nbsp; | 2181 | &nbsp; | Le port que les clients utilisent pour se connecter à soigneur |

### <a name="kafka-ports"></a>Ports Kafka

| Service | Ou plusieurs nœuds | Port | Protocole | Description |
| ------- | ------- | ---- | -------- | ----------- |
| Intermédiaire  | Nœuds de travail | 9092 | [Protocole Kafka](http://kafka.apache.org/protocol.html) | Utilisé pour les communications client |
| &nbsp; | Nœuds soigneur | 2181 | &nbsp; | Le port que les clients utilisent pour se connecter à soigneur |

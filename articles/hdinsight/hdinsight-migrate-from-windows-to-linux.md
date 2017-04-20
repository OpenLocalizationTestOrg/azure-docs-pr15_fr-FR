<properties
pageTitle="Migrer à partir de HDInsight fonctionnant sous Windows à HDInsight basé sur Linux | Azure"
description="Découvrez comment migrer à partir d’un cluster HDInsight fonctionnant sous Windows vers un cluster basé sur Linux HDInsight."
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

#<a name="migrate-from-a-windows-based-hdinsight-cluster-to-a-linux-based-cluster"></a>Migrer à partir d’un cluster HDInsight fonctionnant sous Windows vers un cluster Linux

Alors que HDInsight fonctionnant sous Windows fournit un moyen facile d’utiliser Hadoop dans le cloud, vous pouvez constater que vous avez besoin d’un cluster Linux pour tirer parti d’outils et de technologies d’assistance requis pour votre solution. Nombre d’éléments dans le réseau Hadoop sont développés sur des systèmes Linux et certaines ne soient pas disponibles pour une utilisation avec HDInsight fonctionnant sous Windows. En outre, nombreux livres, vidéos et autres documents de formation part du principe que vous utilisez un système Linux lorsque vous travaillez avec Hadoop.

Ce document fournit des détails sur les différences entre les HDInsight sous Windows et Linux et les instructions sur la migration de charges de travail existants vers un cluster Linux.

> [AZURE.NOTE] HDInsight clusters utilisent prise en charge à long terme Ubuntu (LTS) en tant que le système d’exploitation pour les nœuds dans le cluster. Pour plus d’informations sur la version de Ubuntu disponible avec HDInsight, ainsi que d’autres informations sur les composants du contrôle de version, voir [les versions des composants HDInsight](hdinsight-component-versioning.md).

## <a name="migration-tasks"></a>Tâches de migration

Flux de travail général de migration est la suivante.

![Diagramme de flux de travail de migration](./media/hdinsight-migrate-from-windows-to-linux/workflow.png)

1.  Lire chaque section de ce document à comprendre les modifications qui peuvent être nécessaires lors de la migration de votre flux de travail existant, les tâches, etc. à un cluster Linux.

2.  Créer un cluster Linux comme un environnement assurance test/qualité. Pour plus d’informations sur la création d’un cluster Linux, voir [basé sur Linux créer des groupes dans un HDInsight](hdinsight-hadoop-provision-linux-clusters.md).

3.  Copier des tâches existantes, les sources de données et les récepteurs vers le nouvel environnement. Voir les copier des données dans la section environnement de test pour plus d’informations.

4.  Effectuez des tests de validation pour vous assurer que vos travaux fonctionnent comme prévu sur le nouveau cluster.

Une fois que vous avez vérifié que tout fonctionne comme prévu, planifier une interruption pour la migration. Pendant ce temps d’arrêt, effectuez les actions suivantes.

1.  Sauvegarder toutes les données transitoires stockées localement dans les nœuds de cluster. Par exemple, si vous avez des données stockées directement sur un nœud de tête.

2.  Supprimer le cluster fonctionnant sous Windows.

3.  Créer un cluster Linux à l’aide de la même magasin de données par défaut utilisé le cluster fonctionnant sous Windows. Ainsi, le nouveau cluster continuer à travailler sur vos données de production existant.

4.  Importer des données transitoires que vous sauvegardé.

5.  Démarrer travaux/continuer en utilisant le nouveau cluster.

### <a name="copy-data-to-the-test-environment"></a>Copier des données dans l’environnement de test

Il existe plusieurs méthodes pour copier les données et les tâches, cependant les deux mentionnés dans cette section sont les méthodes plus simple à directement déplacer des fichiers vers un cluster de test.

#### <a name="hdfs-dfs-copy"></a>Copie HDFS DFS

Vous pouvez utiliser la commande Hadoop HDFS pour copier directement des données à partir du stockage pour votre cluster de production existant, dans l’espace de stockage pour un nouveau cluster de test en suivant ces étapes.

1. Trouver les informations de stockage par défaut et compte conteneur pour votre cluster existant. Vous pouvez le faire en utilisant le script PowerShell Azure suivant.

        $clusterName="Your existing HDInsight cluster name"
        $clusterInfo = Get-AzureRmHDInsightCluster -ClusterName $clusterName
        write-host "Storage account name: $clusterInfo.DefaultStorageAccount.split('.')[0]"
        write-host "Default container: $clusterInfo.DefaultStorageContainer"

2. Suivez les étapes décrites dans les groupes de basé sur Linux créer dans un document HDInsight pour créer un nouvel environnement de test. Arrêter avant de créer le cluster, puis sélectionnez à la place de **Configuration facultatives**.

3. À partir de la carte Configuration facultatives, sélectionnez **Comptes de stockage liées**.

4. Sélectionnez **Ajouter une clé de stockage**et lorsque vous y êtes invité, sélectionnez le compte de stockage qui a été retourné par le script PowerShell à l’étape 1. Cliquez sur **Sélectionner** dans chaque carte à les fermer. Enfin, créez le cluster.

5. Une fois le cluster a été créé, se connecter à l’aide de **SSH.** Si vous n’êtes pas familiarisé avec l’utilisation de SSH avec HDInsight, voir les articles suivants.

    * [Utiliser le protocole SSH avec basé sur Linux HDInsight à partir de clients Windows](hdinsight-hadoop-linux-use-ssh-windows.md)

    * [Utiliser le protocole SSH avec basé sur Linux HDInsight à partir de clients Linux, Unix et Mac](hdinsight-hadoop-linux-use-ssh-unix.md)

6. À partir de la session SSH, utilisez la commande suivante pour copier des fichiers à partir du compte de stockage sur le nouveau compte de stockage par défaut. Remplacez le conteneur et compte du conteneur et les informations de compte renvoyée par le script PowerShell à l’étape 1. Remplacez le chemin d’accès aux données par le chemin d’accès à un fichier de données.

        hdfs dfs -cp wasbs://CONTAINER@ACCOUNT.blob.core.windows.net/path/to/old/data /path/to/new/location

    [AZURE.NOTE] Si la structure du répertoire qui contient les données n’existe pas dans l’environnement de test, vous pouvez créer à l’aide de la commande suivante.

        hdfs dfs -mkdir -p /new/path/to/create

    La `-p` commutateur permet la création de tous les répertoires dans le chemin d’accès.

#### <a name="direct-copy-between-azure-storage-blobs"></a>Copie directe entre des objets BLOB Azure Storage

Par ailleurs, vous souhaiterez peut-être utiliser les `Start-AzureStorageBlobCopy` applet de commande PowerShell Azure pour copier des objets BLOB entre des comptes de stockage en dehors de HDInsight. Pour plus d’informations, voir comment gérer section objets BLOB Azure d’à l’aide de PowerShell Azure avec stockage Azure.

##<a name="client-side-technologies"></a>Technologies côté client

En règle générale, technologies côté client telles que les [applets de commande PowerShell Azure](../powershell-install-configure.md), [Azure infrastructure du langage commun](../xplat-cli-install.md) ou le [Kit de développement .NET pour Hadoop](https://hadoopsdk.codeplex.com/) continueront à fonctionner même avec clusters basés sur Linux comme elles s’appuient sur API REST qui sont les mêmes dans les deux types de cluster du système d’exploitation.

##<a name="server-side-technologies"></a>Technologies côté serveur

Le tableau suivant fournit des conseils sur des composants côté serveur migration qui sont spécifiques de Windows.

| Si vous utilisez cette technologie... | Procédure à suivre |
| ----- | ----- |
| **PowerShell** (scripts côté serveur, y compris les Actions de Script utilisé lors de la création de cluster) | Réécriture sous forme de scripts Bash. Actions d’un Script, voir [basé sur Linux personnaliser HDInsight avec Actions de Script](hdinsight-hadoop-customize-cluster-linux.md) et le [développement d’action de Script pour HDInsight basé sur Linux](hdinsight-hadoop-script-actions-linux.md). |
| **Azure infrastructure du langage commun** (scripts côté serveur) | Tandis que l’infrastructure du langage commun Azure est disponible sous Linux, il n’est pas déjà installé sur les nœuds de tête cluster HDInsight. Si vous avez besoin pour l’écriture de script côté serveur, voir [installer l’infrastructure du langage commun Azure](../xplat-cli-install.md) pour plus d’informations sur l’installation sur les plateformes Linux. |
| **Composants .NET** | .NET n’est pas entièrement prise en charge sur les clusters basés sur Linux HDInsight. Vague de Linux sur HDInsight cluster créées après la prise en charge de 28/10/2017 topologies c# vague à l’aide de l’infrastructure SCP.NET. Prise en charge supplémentaire pour .NET sera ajouté à l’avenir mises à jour. |
| **Composants Win32 ou toute autre technologie Windows uniquement** | Conseils dépendant du composant ou de la technologie ; Vous pourrez trouver une version compatible avec Linux, ou vous devrez peut-être trouver une solution alternative ou réécriture ce composant. |

##<a name="cluster-creation"></a>Création d’un cluster

Cette section fournit des informations sur les différences de création d’un cluster.

### <a name="ssh-user"></a>SSH utilisateur

Basé sur Linux HDInsight clusters utilisent le protocole **SSH (Secure Shell)** pour offrir un accès aux nœuds du cluster. Clusters basés sur le Bureau à distance pour Windows, la plupart des clients SSH ne fournissent pas une expérience utilisateur graphique Contrairement à la place fournit une ligne de commande qui vous permet d’exécuter des commandes sur le cluster. Certains clients (par exemple, [MobaXterm](http://mobaxterm.mobatek.net/),) fournissent un navigateur de système de fichiers graphiques outre une distance de ligne de commande.

Lors de la création de cluster, vous devez fournir un utilisateur SSH et un **mot de passe** ou **certificat de clé publique** pour l’authentification.

Nous vous recommandons d’utiliser certificat de clé publique, qu’il est plus sécurisé qu’à l’aide d’un mot de passe. Authentification par certificat fonctionne en génère une paire de clés public/privé signée, puis fournir la clé publique lors de la création du cluster. Lors de la connexion au serveur à l’aide de SSH, la clé privée sur le client fournit une authentification pour la connexion.

Pour plus d’informations sur l’utilisation de SSH avec HDInsight, voir :

- [Utiliser le protocole SSH avec HDInsight à partir de clients Windows](hdinsight-hadoop-linux-use-ssh-windows.md)

- [Utiliser le protocole SSH avec HDInsight à partir de clients Linux, Unix et OS X](hdinsight-hadoop-linux-use-ssh-unix.md)

### <a name="cluster-customization"></a>Personnalisation de cluster

**Actions de script** utilisé avec clusters basés sur Linux doivent être écrites dans Bash script. Tandis que les Actions de Script peut être utilisées lors de la création de cluster, pour clusters basés sur Linux elles peuvent également être utilisés pour exécuter personnalisation après qu’un cluster est disponible et en cours d’exécution. Pour plus d’informations, voir [HDInsight basé sur Linux personnaliser des Actions de Script](hdinsight-hadoop-customize-cluster-linux.md) et le [développement d’action de Script pour HDInsight basé sur Linux](hdinsight-hadoop-script-actions-linux.md).

Une autre fonctionnalité de personnalisation est **démarrage**. Clusters Windows, ce paramètre vous permet de spécifier l’emplacement de bibliothèques supplémentaires pour une utilisation avec Hive. Après la création de cluster sont automatiquement disponibles pour une utilisation avec des requêtes Hive sans qu’il soit nécessaire d’utiliser ces bibliothèques `ADD JAR`.

Démarrage de clusters basés sur Linux ne fournit pas cette fonctionnalité. Utilisez plutôt l’action de script présentée dans les [bibliothèques ajouter ruche lors de la création de cluster](hdinsight-hadoop-add-hive-libraries.md).

### <a name="virtual-networks"></a>Réseaux virtuels

HDInsight fonctionnant sous Windows cluster fonctionnent uniquement avec les réseaux virtuels classique, tandis que HDInsight basé sur Linux clusters requièrent des réseaux virtuels Gestionnaire de ressources. Si vous disposez de ressources dans un réseau virtuel classique qui le cluster Linux HDInsight doit se connecter à, voir [se connecter un réseau virtuel classique à un réseau virtuelle Gestionnaire de ressources](../vpn-gateway/vpn-gateway-connect-different-deployment-models-portal.md).

Pour plus d’informations sur la configuration requise pour l’utilisation de réseaux virtuels Azure avec HDInsight, voir [fonctionnalités d’étendre HDInsight à l’aide d’un réseau virtuel](hdinsight-extend-hadoop-virtual-network.md).

##<a name="management-and-monitoring"></a>Gestion et suivi

La plupart du site web interfaces utilisateur vous pourriez avoir utilisé avec HDInsight fonctionnant sous Windows, telles que l’historique des travaux ou de l’interface utilisateur fils, sont disponibles via Ambari. En outre, l’affichage de la ruche Ambari permet d’exécuter des requêtes Hive à l’aide de votre navigateur web. L’interface utilisateur Web Ambari est disponible sur les clusters basés sur Linux en https://CLUSTERNAME.azurehdinsight.net.

Pour plus d’informations sur l’utilisation de Ambari, consultez les documents suivants :

- [Ambari Web](hdinsight-hadoop-manage-ambari.md)

- [API REST Ambari](hdinsight-hadoop-manage-ambari-rest-api.md)

### <a name="ambari-alerts"></a>Alertes Ambari

Ambari contient un système d’alerte qui peut vous indiquer des problèmes potentiels avec le cluster. Alertes s’affichent comme entrées jaunes ou rouge dans l’interface utilisateur de Web Ambari, mais vous pouvez également les récupérer via l’API REST.

> [AZURE.IMPORTANT] Alertes Ambari indiquent qu’il *peut* y avoir un problème, pas il *est* un problème. Par exemple, vous pouvez recevoir une alerte que HiveServer2 ne sont pas accessibles, même si vous pouvez y accéder normalement.
>
> Plusieurs alertes sont implémentées comme requêtes intervalle basé sur un service et ils doivent s’attendre une réponse dans un intervalle de temps spécifique. Pour que l’alerte ne signifie pas nécessairement que le service est vers le bas, juste qu’elle n’a pas été retourner des résultats dans l’intervalle de temps attendu.

En règle générale, vous devez évaluer si une alerte a été se produisant pendant une période prolongée ou reflète les problèmes des utilisateurs qui ont été consignées précédemment avec le cluster avant d’effectuer l’action dessus.

##<a name="file-system-locations"></a>Emplacement des fichiers système

Le système de fichiers Linux cluster est disposé différemment clusters HDInsight fonctionnant sous Windows. Utilisez le tableau suivant pour rechercher fréquemment utilisée des fichiers.

| Je dois connaître... | Il se trouve... |
| ----- | ----- |
| Configuration | `/etc`. Par exemple,`/etc/hadoop/conf/core-site.xml` |
| Fichiers journaux | `/var/logs` |
| Données Hortonworks plateforme (HDP) | `/usr/hdp`. Il existe deux répertoires situés ici, qui est la version actuelle HDP (par exemple, `2.2.9.1-1`,) et `current`. La `current` répertoire contient des symboles liens vers des fichiers et des répertoires situés dans le répertoire de numéro de version et est fournie comme un moyen pratique d’accéder aux fichiers HDP depuis la version nombre ne change que la version HDP est mis à jour. |
| Hadoop streaming.jar | `/usr/hdp/current/hadoop-mapreduce-client/hadoop-streaming.jar` |

En règle générale, si vous connaissez le nom du fichier, vous pouvez utiliser la commande suivante à partir d’une session SSH pour rechercher le chemin d’accès :

    find / -name FILENAME 2>/dev/null

Vous pouvez également utiliser des caractères génériques avec le nom du fichier. Par exemple, `find / -name *streaming*.jar 2>/dev/null` renvoie le chemin d’accès aux fichiers jar qui contiennent le mot « streaming » dans le cadre du nom du fichier.

##<a name="hive-pig-and-mapreduce"></a>Hive cochon et MapReduce

Cochon et MapReduce charges de travail sont très semblables clusters basés sur Linux sur - la différence principale est si vous utilisez Bureau à distance pour vous connecter à un cluster fonctionnant sous Windows et exécuter des tâches, vous allez utiliser SSH clusters basés sur Linux.

- [Utiliser cochon avec SSH](hdinsight-hadoop-use-pig-ssh.md)

- [Utiliser MapReduce avec SSH](hdinsight-hadoop-use-mapreduce-ssh.md)

### <a name="hive"></a>Hive

Le tableau suivant fournit des conseils sur la migration de vos charges de travail Hive.

| Sous fonctionnant sous Windows, utiliser... | Sous basé sur Linux... |
| ----- | ----- |
| **Éditeur Hive** | [Affichage Hive dans Ambari](hdinsight-hadoop-use-hive-ambari-view.md) |
| `set hive.execution.engine=tez;`Pour activer Tez | Tez est le moteur d’exécution par défaut pour les clusters basés sur Linux, afin que l’instruction set n’est plus nécessaire. |
| Fichiers CMD ou des scripts sur le serveur appelé dans le cadre d’un travail Hive | utiliser des scripts Bash |
| `hive`commande à partir du Bureau à distance | Utiliser [Beeline](hdinsight-hadoop-use-hive-beeline.md) ou [ruche à partir d’une session SSH](hdinsight-hadoop-use-hive-ssh.md) |

##<a name="storm"></a>Vague d'

| Sous fonctionnant sous Windows, utiliser... | Sous basé sur Linux... |
| ----- | ----- |
| Tableau de bord vague | Le tableau de bord vague de n’est pas disponible. Voir [déployer et gérer vague de topologies sur HDInsight basé sur Linux](hdinsight-storm-deploy-monitor-topology-linux.md) pour savoir comment soumettre topologies |
| Vague d’interface utilisateur | L’interface utilisateur vague d’est disponible dans https://CLUSTERNAME.azurehdinsight.net/stormui |
| Visual Studio pour créer, déployer et gérer des topologies c# ou hybride | Visual Studio peut être utilisé pour créer, déployer et gérer c# (SCP.NET) ou topologies hybrides sur vague de Linux sur clusters HDInsight créées après 28/10/2017. |

##<a name="hbase"></a>HBase

Basé sur Linux clusters sur est le parent znode d’HBase `/hbase-unsecure`. Vous devez configurer ce dans la configuration d’un client Java applications qui utilisent des API de Java HBase native.

Pour un exemple de client qui définit cette valeur, voir [créer une application basée sur Java HBase](hdinsight-hbase-build-java-maven.md) .

##<a name="spark"></a>Explosion

Explosion clusters étaient disponibles sur Windows en cluster lors de l’aperçu ; Toutefois, virtualisation, explosion est disponible uniquement avec les clusters basés sur Linux. Il n’existe aucun chemin d’accès de migration à partir d’un cluster preview explosion fonctionnant sous Windows vers un cluster explosion basé sur Linux release.

##<a name="known-issues"></a>Problèmes connus

### <a name="azure-data-factory-custom-net-activities"></a>Activités .NET personnalisées Data Factory Azure

Les activités .NET personnalisées Data Factory Azure ne sont pas actuellement pris en charge clusters HDInsight basé sur Linux. À la place, vous devez utiliser une des méthodes suivantes pour mettre en œuvre des activités personnalisées dans le cadre de votre pipeline de définition d’application.

-   Exécutez des activités .NET sur pool lot Azure. Consultez la section service Azure lot lié [d’utiliser les activités personnalisées dans un pipeline Azure Data Factory](../data-factory/data-factory-use-custom-activities.md#AzureBatch)

-   Implémentez l’activité comme une activité MapReduce. Pour plus d’informations, consultez [Appeler MapReduce programmes provenant d’une usine de données](../data-factory/data-factory-map-reduce.md) .

### <a name="line-endings"></a>Fins de ligne

En règle générale, les fins de ligne sur les systèmes Windows utilisent CRLF, tandis que systèmes Linux utilisent saut de ligne. Si vous produire ou que vous attendez, les données à des fins de ligne CRLF, il se pouvez que vous deviez modifier le producteur ou consommateurs pour fonctionner avec la fin de ligne de saut de ligne.

Par exemple, à l’aide de PowerShell Azure à requête HDInsight sur un cluster Windows renverra les données par CRLF. La même requête avec un cluster Linux renverra saut de ligne. Dans de nombreux cas, cela n’a aucune importance pour les fournisseurs de données, mais il doit être traité avant de migrer vers un cluster Linux.

Si vous disposez de scripts qui seront exécutées directement sur les nœuds cluster Linux (par exemple, un script Python utilisée avec Hive ou d’une tâche MapReduce), vous devez toujours utiliser saut de ligne en tant que la fin de ligne. Si vous utilisez CRLF, vous pouvez voir erreurs lors de l’exécution des scripts sur un cluster Linux.

Si vous savez que les scripts ne contiennent pas de chaînes de caractères CR incorporés, vous pouvez en bloc modifier les fins de ligne à l’aide d’une des méthodes suivantes :

-   **Si vous avez des scripts que vous prévoyez de télécharger vers le cluster**, utilisez les instructions PowerShell suivantes pour modifier les fins de ligne à partir de CRLF en saut de ligne avant de télécharger le script vers le cluster.

        $original_file ='c:\path\to\script.py'
        $text = [IO.File]::ReadAllText($original_file) -replace "`r`n", "`n"
        [IO.File]::WriteAllText($original_file, $text)

-   **Si vous avez des scripts qui se trouvent déjà dans l’espace de stockage utilisé par le cluster**, vous pouvez utiliser la commande suivante à partir d’une session SSH pour le cluster basé sur Linux pour modifier le script.

        hdfs dfs -get wasbs:///path/to/script.py oldscript.py
        tr -d '\r' < oldscript.py > script.py
        hdfs dfs -put -f script.py wasbs:///path/to/script.py

##<a name="next-steps"></a>Étapes suivantes

-   [Apprenez à créer des clusters basés sur Linux HDInsight](hdinsight-hadoop-provision-linux-clusters.md)

-   [Se connecter à un cluster Linux à l’aide de SSH à partir d’un client Windows](hdinsight-hadoop-linux-use-ssh-windows.md)

-   [Se connecter à un cluster Linux à l’aide de SSH à partir d’un client Linux, Unix ou Mac](hdinsight-hadoop-linux-use-ssh-unix.md)

-   [Gérer un cluster Linux à l’aide de Ambari](hdinsight-hadoop-manage-ambari.md)

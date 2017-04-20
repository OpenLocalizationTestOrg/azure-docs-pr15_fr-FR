<properties
    pageTitle="Déboguer et analyser les services Hadoop avec vidage du segment | Microsoft Azure"
    description="Recueillir les images de segment pour les services Hadoop et placer dans le compte de stockage Blob Azure de débogage et d’analyse automatiquement."
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


# <a name="collect-heap-dumps-in-blob-storage-to-debug-and-analyze-hadoop-services"></a>Exporte les collecter segment dans le stockage Blob déboguer et analyser les services Hadoop

[AZURE.INCLUDE [heapdump-selector](../../includes/hdinsight-selector-heap-dump.md)]

Les images de segment contenir un instantané de la mémoire de l’application, y compris les valeurs de variables au moment de que la création de l’image. Pour qu’ils soient très utiles pour diagnostiquer les problèmes se produisant au moment de l’exécution. Les images complètes segment peuvent être automatiquement collectées pour les services Hadoop et placés à l’intérieur du compte de stockage Blob Azure d’un utilisateur sous HDInsightHeapDumps /. 

La collection d’images de segment pour différents services doit être activée pour les services sur clusters individuels. La valeur par défaut pour cette fonctionnalité doit être désactivée pour un cluster. Ces images segment peuvent être volumineux, il est recommandé de surveiller le compte de stockage Blob où ils sont enregistrées dès que la collection de sites a été activé.

> [AZURE.NOTE] Les informations contenues dans cet article s’applique uniquement à HDInsight fonctionnant sous Windows. Pour plus d’informations sur basé sur Linux HDInsight, voir [Activer le vidage de segment pour les services Hadoop sur HDInsight basé sur Linux](hdinsight-hadoop-collect-debug-heap-dump-linux.md)

## <a name="eligible-services-for-heap-dumps"></a>Services éligibles pour les images complètes segment

Vous pouvez activer le vidage de segment pour les services suivants :

*  **hcatalog** - tempelton
*  **hive** - hiveserver2, metastore, derbyserver
*  **mapreduce** - jobhistoryserver
*  **fils** - resourcemanager, nodemanager, timelineserver
*  **Hadoop** - datanode, secondarynamenode, namenode

## <a name="configuration-elements-that-enable-heap-dumps"></a>Éléments de configuration Activer le vidage du segment

Pour activer le vidage de segment d’un service, vous devez définir les éléments de configuration appropriées dans la section pour ce service, qui a été spécifié par **service_name**.

    "javaargs.<service_name>.XX:+HeapDumpOnOutOfMemoryError" = "-XX:+HeapDumpOnOutOfMemoryError",
    "javaargs.<service_name>.XX:HeapDumpPath" = "-XX:HeapDumpPath=c:\Dumps\<service_name>_%date:~4,2%%date:~7,2%%date:~10,2%%time:~0,2%%time:~3,2%%time:~6,2%.hprof"

La valeur de **service_name** peut être un des services répertoriés ci-dessus : tempelton, hiveserver2, metastore, derbyserver, jobhistoryserver, resourcemanager, nodemanager, timelineserver, datanode, secondarynamenode, ou namenode.

## <a name="enable-using-azure-powershell"></a>Activer l’utilisation de PowerShell Azure

Par exemple, pour activer les images complètes segment à l’aide de PowerShell Azure pour jobhistoryserver, effectuez ce qui suit :

[AZURE.INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell.md)]

    $MapRedConfigValues = new-object 'Microsoft.WindowsAzure.Management.HDInsight.Cmdlet.DataObjects.AzureHDInsightMapReduceConfiguration'

    $MapRedConfigValues.Configuration = @{ "javaargs.jobhistoryserver.XX:+HeapDumpOnOutOfMemoryError"="-XX:+HeapDumpOnOutOfMemoryError" ; "javaargs.jobhistoryserver.XX:HeapDumpPath" = "-XX:HeapDumpPath=c:\\Dumps\\jobhistoryserver_%date:~4,2%_%date:~7,2%_%date:~10,2%_%time:~0,2%_%time:~3,2%_%time:~6,2%.hprof" }

## <a name="enable-using-net-sdk"></a>Activer l’utilisation du Kit de développement .NET

Par exemple, pour activer les images complètes segment à l’aide du Kit de développement Azure HDInsight .NET pour jobhistoryserver, effectuez ce qui suit :

    clusterInfo.MapReduceConfiguration.ConfigurationCollection.Add(new KeyValuePair<string, string>("javaargs.jobhistoryserver.XX:+HeapDumpOnOutOfMemoryError", "-XX:+HeapDumpOnOutOfMemoryError"));

    clusterInfo.MapReduceConfiguration.ConfigurationCollection.Add(new KeyValuePair<string, string>("javaargs.jobhistoryserver.XX:HeapDumpPath", "-XX:HeapDumpPath=c:\\Dumps\\jobhistoryserver_%date:~4,2%_%date:~7,2%_%date:~10,2%_%time:~0,2%_%time:~3,2%_%time:~6,2%.hprof"));

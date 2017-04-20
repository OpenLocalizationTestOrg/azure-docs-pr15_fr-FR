<properties
pageTitle="Ajouter des bibliothèques Hive lors de la création de cluster HDInsight | Azure"
description="Découvrez comment ajouter des bibliothèques Hive (fichiersJAR), à un cluster HDInsight lors de la création de cluster."
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
ms.date="09/20/2016"
ms.author="larryfr"/>

#<a name="add-hive-libraries-during-hdinsight-cluster-creation"></a>Ajouter des bibliothèques Hive lors de la création de cluster HDInsight

Si vous avez des bibliothèques que vous utilisez fréquemment avec Hive sur HDInsight, ce document contient des informations sur l’utilisation d’une Action de Script pour chargez au préalable les bibliothèques lors de la création de cluster. Ainsi, les bibliothèques globalement disponible Hive (sans avoir à utiliser [Ajouter JAR](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+Cli) pour charger les).

##<a name="how-it-works"></a>Mode de fonctionnement

Lorsque vous créez un cluster, vous pouvez éventuellement spécifier une Action de Script qui s’exécute un script sur les nœuds de cluster lors de leur création. Le script dans ce document accepte un seul paramètre, ce qui correspond à un emplacement WASB qui contient les bibliothèques (stockés en tant que fichiers jar,) qui seront préalablement chargés.

Lors de la création de cluster, le script énumère les fichiers, les copie la `/usr/lib/customhivelibs/` répertoire sur des nœuds de tête et de travail, puis les ajoute à la `hive.aux.jars.path` propriété dans la `core-site.xml` fichier. Sur les clusters basés sur Linux, il met également à jour la `hive-env.sh` indiquant l’emplacement des fichiers.

> [AZURE.NOTE] Les actions de script dans cet article rendre les bibliothèques disponible dans les scénarios suivants :
>
> * __HDInsight basé sur Linux__ - lors de l’utilisation de la __ruche de ligne de commande__ __WebHCat__ (Templeton) et __HiveServer2__.
> * __HDInsight basée sur Windows__ - lors de l’utilisation de la __ruche de ligne de commande__ et __WebHCat__ (Templeton).

##<a name="the-script"></a>Le script

__Emplacement de script__

Pour les __clusters basés sur Linux__: [https://hdiconfigactions.blob.core.windows.net/linuxsetupcustomhivelibsv01/setup-customhivelibs-v01.sh](https://hdiconfigactions.blob.core.windows.net/linuxsetupcustomhivelibsv01/setup-customhivelibs-v01.sh)

Pour les __clusters fonctionnant sous Windows__: [https://hdiconfigactions.blob.core.windows.net/setupcustomhivelibsv01/setup-customhivelibs-v01.ps1](https://hdiconfigactions.blob.core.windows.net/setupcustomhivelibsv01/setup-customhivelibs-v01.ps1)

__Configuration requise__

* Les scripts doivent être appliqués aux __nœuds de tête__ et __nœuds de travail__.

* Les fichiers JAR que vous souhaitez installer doivent être stockés dans le stockage Blob Azure dans un __seul conteneur__. 

* Le compte de stockage contenant la bibliothèque de jar fichiers __doit__ être lié au cluster HDInsight lors de la création. Ceci peut être effectué de deux façons :

    * Lorsqu’ils sont dans un conteneur sur le compte de stockage par défaut pour le cluster.
    
    * Lorsqu’ils sont dans un conteneur dans un conteneur de stockage liées. Par exemple, dans le portail, vous pouvez utiliser __Configuration facultatives__, __comptes de stockage lié__ à ajouter d’espace de stockage supplémentaire.

* Le chemin d’accès WASB au conteneur doit être spécifié en tant que paramètre à l’Action de Script. Par exemple, si les fichiers JAR sont stockées dans un conteneur nommé __et les bibliothèques__ dans un compte de stockage nommé __mystorage__, le paramètre serait __wasbs://libs@mystorage.blob.core.windows.net/__.

    > [AZURE.NOTE] Ce document suppose que vous avez déjà créé un compte de stockage, blob conteneur et les fichiers téléchargés à celui-ci. 
    >
    > Si vous n’avez pas créé un compte de stockage, vous pouvez le faire via le [portail Azure](https://portal.azure.com). Vous pouvez ensuite utiliser un utilitaire tel que [Explorateur de stockage Azure](http://storageexplorer.com/) pour créer un nouveau conteneur dans le compte et télécharger des fichiers vers celui-ci.

##<a name="create-a-cluster-using-the-script"></a>Créer un cluster en utilisant le script

> [AZURE.NOTE] Les étapes suivantes créent un nouveau cluster HDInsight basé sur Linux. Pour créer un nouveau cluster fonctionnant sous Windows, sélectionnez __Windows__ que le cluster du système d’exploitation lors de la création du cluster et utilisez le script Windows (PowerShell) au lieu du script bash.
> 
> Vous pouvez également utiliser PowerShell Azure ou le Kit de développement .NET HDInsight pour créer un cluster à l’aide de ce script. Pour plus d’informations sur l’utilisation de ces méthodes, voir [Personnaliser HDInsight clusters avec les Actions de Script](hdinsight-hadoop-customize-cluster-linux.md).

1. Démarrer un cluster de mise en service en suivant les étapes de [mise en service HDInsight clusters sur Linux](hdinsight-hadoop-provision-linux-clusters.md#portal), mais ne se terminent pas mise en service.

2. Sur la carte de **Configuration facultatives** , sélectionnez les **Actions de Script**et fournissez les informations comme indiqué ci-dessous :

    * __Nom__: entrez un nom convivial pour l’action de script.
    * __SCRIPT URI__: https://hdiconfigactions.blob.core.windows.net/linuxsetupcustomhivelibsv01/setup-customhivelibs-v01.sh
    * __Tête__: Activez cette option
    * __Employé CONSULTANT__: Activez cette option.
    * __SOIGNEUR__: laissez ce champ vide.
    * __Paramètres__: entrez l’adresse WASB sur le compte de stockage et le conteneur qui contient les fichiers JAR. Par exemple, __wasbs://libs@mystorage.blob.core.windows.net/__.

3. En bas des **Actions de Script**, utilisez le bouton **Sélectionner** pour enregistrer la configuration.

4. Sur la carte de **Configuration facultatives** , sélectionnez __Comptes de stockage lié__ , puis sélectionnez le lien __Ajouter une clé de stockage__ . Sélectionnez le compte de stockage qui contient les fichiers JAR, puis utilisez les boutons __Sélectionner__ pour enregistrer les paramètres et renvoyer la carte de __Configuration facultatives__ .

5. Utilisez le bouton **Sélectionner** en bas de la cuillère **Configuration facultatives** pour enregistrer les informations de configuration facultatif.

6. Continuez à mise en service le cluster comme décrit dans [mise en service HDInsight clusters sur Linux](hdinsight-hadoop-provision-linux-clusters.md#portal).

Une fois la création d’un cluster est terminée, vous pourrez utiliser les fichiers JAR ajoutés via ce script à partir de Hive sans utiliser le `ADD JAR` instruction.

##<a name="next-steps"></a>Étapes suivantes

Dans ce document, vous avez appris à ajouter des bibliothèques Hive contenues dans les fichiers jar vers un cluster HDInsight lors de la création de cluster. Pour plus d’informations sur l’utilisation des Hive, voir [Utiliser la ruche avec HDInsight](hdinsight-use-hive.md)

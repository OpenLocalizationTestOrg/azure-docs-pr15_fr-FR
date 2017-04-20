<properties
    pageTitle="Script de développement Action avec HDInsight | Microsoft Azure"
    description="Découvrez comment personnaliser clusters Hadoop avec l’Action de Script. Action de script peut être utilisée pour installer d’autres logiciels en cours d’exécution sur un cluster Hadoop ou pour changer la configuration des applications installées sur un cluster."
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

# <a name="develop-script-action-scripts-for-hdinsight-windows-based-clusters"></a>Développer des scripts Action de Script pour clusters basés sur HDInsight Windows

Apprenez à écrire des scripts d’Action de Script pour HDInsight. Pour plus d’informations sur l’utilisation de scripts d’Action de Script, voir [clusters HDInsight personnaliser à l’aide de Script Action](hdinsight-hadoop-customize-cluster.md). Pour l’article même écrite pour clusters basés sur Linux HDInsight, voir [scripts développer une Action de Script pour HDInsight](hdinsight-hadoop-script-actions-linux.md).

> [AZURE.NOTE] Les informations de ce document sont spécifiques aux clusters HDInsight fonctionnant sous Windows. Pour plus d’informations sur l’utilisation des actions de script avec clusters fonctionnant sous Windows, consultez [Script action développement avec HDInsight (Linux)](hdinsight-hadoop-script-actions-linux.md).


Action de script peut être utilisée pour installer d’autres logiciels en cours d’exécution sur un cluster Hadoop ou pour changer la configuration des applications installées sur un cluster. Actions de script sont des scripts qui s’exécutent sur les nœuds du cluster lorsque le déploiement des clusters HDInsight, et elles sont exécutées une fois que les nœuds du cluster terminer la configuration de HDInsight. Une action de script est exécutée sous privilèges de compte d’administrateur système et fournit les droits d’accès complet pour les nœuds de cluster. Chaque cluster peut être fourni avec une liste d’actions de script à être exécutées dans l’ordre dans lequel elles sont définies.

> [AZURE.NOTE] Si vous rencontrez le message d’erreur suivant :
>
>     System.Management.Automation.CommandNotFoundException; ExceptionMessage : The term 'Save-HDIFile' is not recognized as the name of a cmdlet, function, script file, or operable program. Check the spelling of the name, or if a path was included, verify that the path is correct and try again.
> C’est parce que vous n’avez pas inclure les méthodes d’assistance.  Voir les [méthodes d’assistance pour les scripts personnalisés](hdinsight-hadoop-script-actions.md#helper-methods-for-custom-scripts).

## <a name="sample-scripts"></a>Exemples de scripts

Pour créer des clusters HDInsight sur système d’exploitation Windows, l’Action de Script est script PowerShell Azure. Voici un exemple de script pour configurer les fichiers de configuration de site :

[AZURE.INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell.md)]

    param (
        [parameter(Mandatory)][string] $ConfigFileName,
        [parameter(Mandatory)][string] $Name,
        [parameter(Mandatory)][string] $Value,
        [parameter()][string] $Description
    )

    if (!$Description) {
        $Description = ""
    }

    $hdiConfigFiles = @{
        "hive-site.xml" = "$env:HIVE_HOME\conf\hive-site.xml";
        "core-site.xml" = "$env:HADOOP_HOME\etc\hadoop\core-site.xml";
        "hdfs-site.xml" = "$env:HADOOP_HOME\etc\hadoop\hdfs-site.xml";
        "mapred-site.xml" = "$env:HADOOP_HOME\etc\hadoop\mapred-site.xml";
        "yarn-site.xml" = "$env:HADOOP_HOME\etc\hadoop\yarn-site.xml"
    }

    if (!($hdiConfigFiles[$ConfigFileName])) {
        Write-HDILog "Unable to configure $ConfigFileName because it is not part of the HDI configuration files."
        return
    }

    [xml]$configFile = Get-Content $hdiConfigFiles[$ConfigFileName]

    $existingproperty = $configFile.configuration.property | where {$_.Name -eq $Name}

    if ($existingproperty) {
        $existingproperty.Value = $Value
        $existingproperty.Description = $Description
    } else {
        $newproperty = @($configFile.configuration.property)[0].Clone()
        $newproperty.Name = $Name
        $newproperty.Value = $Value
        $newproperty.Description = $Description
        $configFile.configuration.AppendChild($newproperty)
    }

    $configFile.Save($hdiConfigFiles[$ConfigFileName])

    Write-HDILog "$configFileName has been configured."

Le script prend quatre paramètres, le nom de fichier de configuration, la propriété que vous souhaitez modifier, la valeur que vous souhaitez définir, et une description. Par exemple :

    hive-site.xml hive.metastore.client.socket.timeout 90

Ces paramètres définit la valeur hive.metastore.client.socket.timeout 90 dans le fichier hive site.xml.  La valeur par défaut est 60 secondes.

Cet exemple de script vous pouvez également trouver en [https://hditutorialdata.blob.core.windows.net/customizecluster/editSiteConfig.ps1](https://hditutorialdata.blob.core.windows.net/customizecluster/editSiteConfig.ps1).

HDInsight fournit plusieurs scripts pour installer des composants supplémentaires sur les clusters HDInsight :

Nom | Script
----- | -----
**Installer explosion** | https://hdiconfigactions.BLOB.Core.Windows.NET/sparkconfigactionv03/Spark-installer-v03.ps1. Voir [installer et utiliser au service sur clusters HDInsight][hdinsight-install-spark].
**Installer R** | https://hdiconfigactions.BLOB.Core.Windows.NET/rconfigactionv02/r-installer-v02.ps1. Voir [installer et utiliser R sur clusters HDInsight][hdinsight-r-scripts].
**Installer mode série sur LAN.r** | https://hdiconfigactions.BLOB.Core.Windows.NET/solrconfigactionv01/solr-installer-v01.ps1. Voir [installer et utiliser le mode série sur LAN.r sur HDInsight clusters](hdinsight-hadoop-solr-install.md).
- **Installer Giraph** | https://hdiconfigactions.BLOB.Core.Windows.NET/giraphconfigactionv01/giraph-installer-v01.ps1. Voir [installer et utiliser des clusters Giraph sur HDInsight](hdinsight-hadoop-giraph-install.md).

Action de script peut être déployée à partir du portail Azure, PowerShell Azure ou en utilisant le Kit de développement .NET HDInsight.  Pour plus d’informations, voir [clusters HDInsight personnaliser à l’aide de Script Action][hdinsight-cluster-customize].

> [AZURE.NOTE] Les exemples de scripts fonctionnent uniquement avec la version de cluster HDInsight 3.1 ou au-dessus. Pour plus d’informations sur les versions de cluster HDInsight, voir [HDInsight cluster versions](hdinsight-component-versioning.md).





## <a name="helper-methods-for-custom-scripts"></a>Méthodes d’assistance pour les scripts personnalisés

Méthodes de script Action d’assistance sont utilitaires que vous pouvez utiliser lors de l’écriture de scripts personnalisés. Ils sont définis dans [https://hdiconfigactions.blob.core.windows.net/configactionmodulev05/HDInsightUtilities-v05.psm1](https://hdiconfigactions.blob.core.windows.net/configactionmodulev05/HDInsightUtilities-v05.psm1)et peuvent être incluses dans vos scripts en utilisant les éléments suivants :

    # Download config action module from a well-known directory.
    $CONFIGACTIONURI = "https://hdiconfigactions.blob.core.windows.net/configactionmodulev05/HDInsightUtilities-v05.psm1";
    $CONFIGACTIONMODULE = "C:\apps\dist\HDInsightUtilities.psm1";
    $webclient = New-Object System.Net.WebClient;
    $webclient.DownloadFile($CONFIGACTIONURI, $CONFIGACTIONMODULE);

    # (TIP) Import config action helper method module to make writing config action easy.
    if (Test-Path ($CONFIGACTIONMODULE))
    {
        Import-Module $CONFIGACTIONMODULE;
    }
    else
    {
        Write-Output "Failed to load HDInsightUtilities module, exiting ...";
        exit;
    }

Voici les méthodes d’assistance qui sont fournies par ce script :

Méthode d’assistance | Description
-------------- | -----------
**Enregistrer HDIFile** | Télécharger un fichier à partir de la spécifié identificateur URI (Uniform Resource) vers un emplacement sur le disque local est associé le nœud machine virtuelle Azure affecté au cluster.
**Développez HDIZippedFile** | Décompressez un fichier zippé s’ouvre.
**HDICmdScript appeler** | Exécuter un script à partir de cmd.exe.
**Écriture HDILog** | Écrire la sortie à partir du script personnalisé utilisé pour une action de script.
**Get-Services** | Obtenir une liste des services en cours d’exécution sur l’ordinateur où le script s’exécute.
**Get-Service** | Avec le nom du service spécifique comme entrée, obtenir des informations détaillées d’un service spécifique (nom du service, identificateur de processus, état, etc.) sur l’ordinateur où le script s’exécute.
**Get-HDIServices** | Obtenir une liste des services HDInsight en cours d’exécution sur l’ordinateur où le script s’exécute.
**Get-HDIService** | Avec le nom du service HDInsight spécifique comme entrée, obtenir des informations détaillées d’un service spécifique (nom du service, identificateur de processus, état, etc.) sur l’ordinateur où le script s’exécute.
**Get-serviceexécution** | Obtenir une liste des services en cours d’exécution sur l’ordinateur où le script s’exécute.
**Get-ServiceRunning** | Vérifier si un service spécifique (par nom) est en cours d’exécution sur l’ordinateur où le script s’exécute.
**Get-HDIServicesRunning** | Obtenir une liste des services HDInsight en cours d’exécution sur l’ordinateur où le script s’exécute.
**Get-HDIServiceRunning** | Vérifier si un service HDInsight spécifique (par nom) est en cours d’exécution sur l’ordinateur où le script s’exécute.
**Get-HDIHadoopVersion** | Obtenir la version d’Hadoop sur l’ordinateur où le script s’exécute.
**Test-IsHDIHeadNode** | Vérifiez si l’ordinateur où s’exécute le script est un nœud de tête.
**Test-IsActiveHDIHeadNode** | Vérifiez si l’ordinateur où s’exécute le script est un nœud de tête actif.
**Test-IsHDIDataNode** | Vérifiez si l’ordinateur où s’exécute le script est un nœud de données.
**Modifier HDIConfigFile** | Modifier les fichiers de configuration de la hive-site.xml, core site.xml, Hadoop site.xml, mapred site.xml ou fils site.xml.


## <a name="best-practices-for-script-development"></a>Meilleures pratiques pour le développement de script

Lorsque vous développez un script personnalisé pour un cluster de HDInsight, il existe plusieurs pratiques recommandées à prendre en compte :

- Vérifier la présence de la version Hadoop

    Uniquement HDInsight version 3.1 (Hadoop 2.4) et au-dessus de prise en charge de l’Action de Script pour installer des composants personnalisés sur un cluster. Dans votre script personnalisé, vous devez utiliser la méthode d’assistance **Get-HDIHadoopVersion** pour vérifier la version Hadoop avant de procéder à effectuer d’autres tâches dans le script.


- Fournir stable des liens vers des ressources de script

    Les utilisateurs devraient vous assurer que tous les scripts et autres objets utilisés dans la personnalisation d’un cluster restent disponibles dans l’ensemble de la durée de vie du cluster et que les versions de ces fichiers ne changent pas pendant la durée. Ces ressources sont nécessaires si le ré-création d’images de nœuds dans le cluster est requis. La meilleure solution consiste à télécharger et archivés dans un compte de stockage contrôlant l’utilisateur. Cela peut être le compte de stockage par défaut ou les comptes de stockage supplémentaires spécifiés au moment de déploiement d’un cluster personnalisé.
    Dans l’explosion et R personnalisé exemples cluster fournies dans la documentation, par exemple, nous avons une copie locale des ressources dans ce compte de stockage : https://hdiconfigactions.blob.core.windows.net/.


- Vérifiez que le script de personnalisation cluster est idempotent

    Vous devez vous attendre que les nœuds d’un cluster HDInsight sera ré-image pendant la durée de vie cluster. Le script de personnalisation cluster s’exécute chaque fois qu’un cluster est ré-image. Ce script doit être conçu pour être idempotents dans le sens que lors de la création d’images, le script doit s’assurer que le cluster est retourné au même personnalisé état qui était le sien juste après que le script a été exécuté pour la première fois lorsque le cluster a été initialement créé. Par exemple, si un script personnalisé installé une application au D:\AppLocation sur sa première exécution, puis sur chaque exécution ultérieure, lors de la création d’images, le script doit vérifier si l’application existe à l’emplacement D:\AppLocation avant de poursuivre avec d’autres étapes dans le script.


- Installer des composants personnalisés à l’emplacement optimal

    Lorsque les nœuds de cluster sont ré-image, le lecteur de ressources C:\ et le lecteur système D:\ peuvent être remise en forme, ce qui entraîne la perte de données et d’applications qui avaient été installées sur ces lecteurs. Cela pourrait également se produire si un nœud Azure machine virtuelle () qui fait partie du cluster s’arrête et est remplacé par un nouveau nœud. Vous pouvez installer des composants sur le lecteur D:\ ou dans un emplacement C:\apps sur le cluster. Tous les autres emplacements sur le lecteur C:\ sont réservés. Spécifiez l’emplacement où applications ou des bibliothèques doivent être installés dans le script de personnalisation cluster.


- Optimiser la disponibilité de l’architecture de cluster

    HDInsight a une architecture actif-passif de disponibilité, dans lequel un nœud principal est en mode actif (où les services HDInsight s’exécutent) et le nœud de tête est en mode mise en veille (dans le HDInsight services exécutent pas). Les nœuds de changer de mode actif et passif si HDInsight services sont interrompus. Si une action de script est utilisée pour installer les services sur les deux nœuds têtes de disponibilité, notez que le mécanisme de basculement HDInsight ne seront pas en mesure de basculer automatiquement sur ces services installés par l’utilisateur. Services donc installés par les utilisateurs sur des nœuds de tête HDInsight qui doivent être hautement disponible doivent ont leur propre mécanisme de basculement en cas de mode actif passif ou être en mode actif.

    Une commande d’Action de Script HDInsight s’exécute sur les deux nœuds têtes lorsque le rôle serveur de nœud est spécifié comme une valeur dans le paramètre *ClusterRoleCollection* . Lorsque vous créez un script personnalisé, vérifiez que que votre script connaît de ce programme d’installation. Ne pas utiliser des difficultés à l’endroit où les mêmes services sont installés et démarrés sur les deux des nœuds têtes et ils finissent concurrents entre eux. En outre, n’oubliez pas que les données seront perdues lors de la création d’images, afin de logiciel installé via l’Action de Script doit être résistant à ces événements. Applications doivent être conçues pour fonctionner avec la disponibilité des données qui sont réparties entre le nombre de nœuds. Notez que jusqu'à 1/5 des nœuds dans un cluster peut être remise en image en même temps.


- Configurer les composants personnalisés pour utiliser le stockage Blob Azure

    Les composants personnalisés que vous installez sur les nœuds du cluster peuvent avoir une configuration par défaut à utiliser le stockage de système de fichier distribué Hadoop (HDFS). Vous devez modifier la configuration pour utiliser à la place de stockage d’objets Blob Azure. Sur un cluster nouvelle image, le système de fichiers HADOOP obtient mises en forme, vous le feriez perdre des données stockées à cet emplacement. À l’aide de stockage d’objets Blob Azure à la place garantit que vos données sont conservées.

## <a name="common-usage-patterns"></a>Modes d’utilisation courants

Cette section fournit des conseils sur l’implémentation parmi les modèles d’utilisation courants que vous pouvez rencontrer lors de l’écriture de votre propre script personnalisé.

### <a name="configure-environment-variables"></a>Configurer les variables d’environnement

Dans le développement d’action script, vous sembleront souvent la nécessité de définir des variables d’environnement. Par exemple, un scénario probablement est lorsque vous téléchargez un fichier binaire à partir d’un site externe, installez sur le cluster et ajoutez l’emplacement de l’endroit où il est installé sur votre variable d’environnement « PATH ». L’extrait suivant vous montre comment définir des variables d’environnement dans le script personnalisé.

    Write-HDILog "Starting environment variable setting at: $(Get-Date)";
    [Environment]::SetEnvironmentVariable('MDS_RUNNER_CUSTOM_CLUSTER', 'true', 'Machine');

Cette instruction définit la variable d’environnement **MDS_RUNNER_CUSTOM_CLUSTER** à la valeur « true » et également définit la portée de cette variable doivent d’échelle de l’ordinateur. Dans certains cas, il est important que les variables d’environnement sont définies à la portée appropriée – machine ou utilisateur. Consultez [ici] [ 1] pour plus d’informations sur la définition des variables d’environnement.

### <a name="access-to-locations-where-the-custom-scripts-are-stored"></a>Accès aux emplacements où se trouvent les scripts personnalisés

Scripts utilisés pour personnaliser un cluster doivent être à des options dans le compte de stockage par défaut pour le cluster ou dans un conteneur en lecture seule public sur un autre compte de stockage. Si votre script accède aux ressources situées ailleurs ces devront être dans accessible au public (public au moins en lecture seule). Par exemple, vous souhaiterez peut-être accéder à un fichier et enregistrez-le à l’aide de la commande SaveFile HDI.

    Save-HDIFile -SrcUri 'https://somestorageaccount.blob.core.windows.net/somecontainer/some-file.jar' -DestFile 'C:\apps\dist\hadoop-2.4.0.2.1.9.0-2196\share\hadoop\mapreduce\some-file.jar'

Dans cet exemple, vous devez vous assurer que le conteneur « somecontainer » dans le compte de stockage 'somestorageaccount' est accessible au public. Dans le cas contraire, le script lever une exception « Introuvable » et échouer.

### <a name="pass-parameters-to-the-add-azurermhdinsightscriptaction-cmdlet"></a>Passer des paramètres à l’applet de commande Add-AzureRmHDInsightScriptAction

Pour passer plusieurs paramètres à l’applet de commande Add-AzureRmHDInsightScriptAction, vous devez mettre en forme la valeur de chaîne pour contenir tous les paramètres pour le script. Par exemple :

    "-CertifcateUri wasbs:///abc.pfx -CertificatePassword 123456 -InstallFolderName MyFolder"

ou

    $parameters = '-Parameters "{0};{1};{2}"' -f $CertificateName,$certUriWithSasToken,$CertificatePassword


### <a name="throw-exception-for-failed-cluster-deployment"></a>Lever une exception pour le déploiement de cluster a échoué

Si vous souhaitez être informé avec précision du fait que le cluster personnalisation n’a pas réussi comme prévu, il est important de lever une exception et échouer la création d’un cluster. Par exemple, vous souhaiterez peut-être traite un fichier s’il existe et gérer le cas d’erreur où le fichier n’existe pas. Cela garantie que le script s’arrête correctement et l’état du cluster est correctement connue. L’extrait suivant donne un exemple de la façon d’y parvenir :

    If(Test-Path($SomePath)) {
        #Process file in some way
    } else {
        # File does not exist; handle error case
        # Print error message
    exit
    }

Dans cet extrait, si le fichier n’existe pas, il entraînerait un mode dans lequel le script réellement s’arrête correctement après l’impression le message d’erreur, et le cluster atteint état en cours d’exécution en supposant qu’il » « terminé cluster personnalisation processus. Si vous souhaitez être informé précisément du fait que le cluster essentiellement personnalisation n’a pas réussi comme prévu en raison d’un fichier manquant, il est plus approprié de lever une exception et que cette action de personnalisation cluster échoue. Pour ce faire, vous devez plutôt utiliser l’extrait de code exemple suivant.

    If(Test-Path($SomePath)) {
        #Process file in some way
    } else {
        # File does not exist; handle error case
        # Print error message
    throw
    }


## <a name="checklist-for-deploying-a-script-action"></a>Liste de vérification pour le déploiement d’une action de script
Voici les étapes que nous avons avant de déployer ces scripts :

1. Placer les fichiers qui contiennent les scripts personnalisés dans un emplacement accessible par les nœuds cluster pendant le déploiement. Cela peut être un des comptes de stockage supplémentaires spécifiés au moment de déploiement de cluster, ou tout autre conteneur de stockage accessible au public par défaut.
2. Ajouter des contrôles à des scripts pour vous assurer qu’ils exécutent idempotently, afin que le script peut être exécuté plusieurs fois sur le même nœud.
3. Utiliser l’applet de commande PowerShell Azure **Écriture sortie** pour l’impression STDOUT ainsi que STDERR. N’utilisez pas **d’Écriture hôte**.
4. Utiliser un dossier de fichier temporaire, par exemple $env : TEMP, pour conserver le fichier téléchargé utilisé par les scripts et puis les nettoyer après ont l’exécution de scripts.
5. Installez le logiciel personnalisé uniquement à la D:\ ou C:\apps. D’autres emplacements sur le lecteur C: ne doivent pas être utilisés car ils sont réservés. Notez que l’installation des fichiers sur le lecteur C: en dehors du dossier C:\apps, peut entraîner des échecs de configuration au cours d’une nouvelle image du nœud.
6. Dans le cas où les paramètres au niveau du système d’exploitation ou les fichiers de configuration du service Hadoop ont été modifiés, vous voudrez peut-être redémarrer HDInsight services afin qu’ils peuvent intercepter tous les paramètres au niveau du système d’exploitation, tels que les variables d’environnement définies dans les scripts.

## <a name="debug-custom-scripts"></a>Déboguer des scripts personnalisés

Les journaux d’erreurs de script sont stockées, ainsi que d’autres sortie, dans le compte de stockage par défaut que vous avez spécifié pour le cluster lors de sa création. Les journaux sont stockés dans une table avec le nom *u < \cluster-name-fragment >< \time-stamp > setuplog*. Il s’agit des journaux agrégés ayant des enregistrements à partir de tous les nœuds (nœud de tête et nœuds de travail) sur lequel le script s’exécute dans le cluster.
Un moyen facile de vérifier les journaux consiste à utiliser HDInsight Tools pour Visual Studio. Pour installer les outils, voir [commencer à utiliser les outils de Visual Studio Hadoop pour HDInsight](hdinsight-hadoop-visual-studio-tools-get-started.md#install-hdinsight-tools-for-visual-studio)

**Pour vérifier le journal à l’aide de Visual Studio**

1. Ouvrez Visual Studio.
2. Cliquez sur **affichage**, puis cliquez sur **Explorateur de serveurs**.
3. Avec le bouton droit « Azure » et cliquez sur se connecter à **Microsoft Azure abonnements**, puis entrez vos informations d’identification.
4. Développez **stockage**, développer le compte de stockage Azure utilisé comme système de fichiers par défaut, développez le nœud **Tables**et puis double-cliquez sur le nom de table.


Vous pouvez également à distance dans les nœuds de cluster pour afficher les deux STDOUT et STDERR des scripts personnalisés. Les journaux sur chaque nœud sont uniquement ce nœud et êtes connectés à **C:\HDInsightLogs\DeploymentAgent.log**. Ces fichiers journaux enregistrent toutes les sorties à partir d’un script personnalisé. Un extrait de journal exemple pour une action de script explosion ressemble à ceci :

    Microsoft.Hadoop.Deployment.Engine.CustomPowershellScriptCommand; Details : BEGIN: Invoking powershell script https://configactions.blob.core.windows.net/sparkconfigactions/spark-installer.ps1.;
    Version : 2.1.0.0;
    ActivityId : 739e61f5-aa22-4254-aafc-9faf56fc2692;
    AzureVMName : HEADNODE0;
    IsException : False;
    ExceptionType : ;
    ExceptionMessage : ;
    InnerExceptionType : ;
    InnerExceptionMessage : ;
    Exception : ;
    ...

    Starting Spark installation at: 09/04/2014 21:46:02 Done with Spark installation at: 09/04/2014 21:46:38;

    Version : 2.1.0.0;
    ActivityId : 739e61f5-aa22-4254-aafc-9faf56fc2692;
    AzureVMName : HEADNODE0;
    IsException : False;
    ExceptionType : ;
    ExceptionMessage : ;
    InnerExceptionType : ;
    InnerExceptionMessage : ;
    Exception : ;
    ...

    Microsoft.Hadoop.Deployment.Engine.CustomPowershellScriptCommand;
    Details : END: Invoking powershell script https://configactions.blob.core.windows.net/sparkconfigactions/spark-installer.ps1.;
    Version : 2.1.0.0;
    ActivityId : 739e61f5-aa22-4254-aafc-9faf56fc2692;
    AzureVMName : HEADNODE0;
    IsException : False;
    ExceptionType : ;
    ExceptionMessage : ;
    InnerExceptionType : ;
    InnerExceptionMessage : ;
    Exception : ;


Dans ce journal, il est évident que l’action de script explosion a été exécutée sur l’ordinateur virtuel nommé HEADNODE0 et qu’aucun exceptions ont été levées pendant l’exécution.

Dans le cas où une erreur d’exécution se produit, la sortie décrivant il sera également figurer dans ce fichier journal. Les informations fournies dans ces journaux devraient vous aider résoudre les problèmes de script qui peuvent survenir.


## <a name="see-also"></a>Voir aussi

- [Personnaliser les HDInsight à l’aide de Script Action][hdinsight-cluster-customize]
- [Installer et utiliser explosion sur clusters HDInsight][hdinsight-install-spark]
- [Installer et utiliser R sur clusters HDInsight][hdinsight-r-scripts]
- [Installer et utiliser le mode série sur LAN.r sur HDInsight clusters](hdinsight-hadoop-solr-install.md).
- [Installer et utiliser les clusters Giraph sur HDInsight](hdinsight-hadoop-giraph-install.md).

[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-cluster-customize]: hdinsight-hadoop-customize-cluster.md
[hdinsight-install-spark]: hdinsight-hadoop-spark-install.md
[hdinsight-r-scripts]: hdinsight-hadoop-r-scripts.md
[powershell-install-configure]: install-configure-powershell.md

<!--Reference links in article-->
[1]: https://msdn.microsoft.com/library/96xafkes(v=vs.110).aspx

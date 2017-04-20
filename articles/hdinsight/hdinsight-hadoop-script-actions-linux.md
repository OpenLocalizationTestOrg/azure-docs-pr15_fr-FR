<properties
    pageTitle="Script de développement action avec basé sur Linux HDInsight | Microsoft Azure"
    description="Comment personnaliser HDInsight basé sur Linux cluster avec l’Action de Script. Actions de script constituent un moyen pour personnaliser Azure HDInsight clusters en spécifiant des paramètres de configuration cluster ou en installant des services supplémentaires, outils ou autres logiciels sur le cluster. "
    services="hdinsight"
    documentationCenter=""
    authors="Blackmist"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="hdinsight"
    ms.workload="big-data"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/05/2016"
    ms.author="larryfr"/>

# <a name="script-action-development-with-hdinsight"></a>Développement d’action script avec HDInsight

Actions de script constituent un moyen pour personnaliser Azure HDInsight clusters en spécifiant des paramètres de configuration cluster ou en installant des services supplémentaires, outils ou autres logiciels sur le cluster. Vous pouvez utiliser les actions de script lors de la création de cluster ou sur un cluster en cours d’exécution.

> [AZURE.NOTE] Les informations de ce document sont spécifiques aux clusters HDInsight basé sur Linux. Pour plus d’informations sur l’utilisation des actions de script avec clusters fonctionnant sous Windows, consultez [Script action développement avec HDInsight (Windows)](hdinsight-hadoop-script-actions.md).

## <a name="what-are-script-actions"></a>Quelles sont les actions de script ?

Actions de script sont des scripts Bash Azure s’exécute sur les nœuds de cluster à apporter des modifications de configuration ou installer le logiciel. Une action de script est exécutée en tant que racine et fournit les droits d’accès complet pour les nœuds de cluster.

Actions de script peuvent être appliquées via les méthodes suivantes :

| Utilisez cette option pour appliquer un script... | Lors de la création du cluster... | Sur un cluster en cours d’exécution... |
| ----- |:-----:|:-----:|
| Portail Azure | ✓ | ✓ |
| PowerShell Azure | ✓ | ✓ |
| Azure infrastructure du langage commun | &nbsp; | ✓ |
| Kit de développement .NET HDInsight | ✓ | ✓ |
| Modèle de gestionnaire de ressources Azure | ✓ | &nbsp; |

Pour plus d’informations sur l’utilisation de ces méthodes pour appliquer des actions de script, voir [Personnaliser HDInsight clusters à l’aide des actions de script](hdinsight-hadoop-customize-cluster-linux.md).

## <a name="bestPracticeScripting"></a>Meilleures pratiques pour le développement de script

Lorsque vous développez un script personnalisé pour un cluster de HDInsight, il existe plusieurs pratiques recommandées à prendre en compte :

- [Cibler la version Hadoop](#bPS1)
- [Cibler la Version du système d’exploitation](#bps10)
- [Fournir stable des liens vers des ressources de script](#bPS2)
- [Utilisez les ressources préalablement compilées](#bPS4)
- [Vérifiez que le script de personnalisation cluster est idempotent](#bPS3)
- [Optimiser la disponibilité de l’architecture de cluster](#bPS5)
- [Configurer les composants personnalisés pour utiliser le stockage Blob Azure](#bPS6)
- [Écrire des informations dans STDOUT et STDERR](#bPS7)
- [Enregistrer des fichiers au format ASCII fins de ligne de saut de ligne](#bps8)
- [Utiliser la logique de nouvelle tentative pour récupérer les erreurs transitoires](#bps9)

> [AZURE.IMPORTANT] Actions de script doivent se terminer dans 60 minutes ou qu’ils n’expire. Lors de la configuration de nœud, le script est exécuté en même temps que d’autres processus d’installation et de configuration. Concurrence pour les ressources telles que la bande passante réseau ou de temps processeur peut entraîner le script de prendre plus de temps que c’est le cas dans votre environnement de développement.

### <a name="bPS1"></a>Cibler la version Hadoop

Plusieurs versions de HDInsight disposent de différentes versions de services Hadoop et des composants installés. Si votre script attend une version spécifique d’un service ou un composant, vous devez utiliser uniquement le script avec la version de HDInsight qui inclut les composants requis. Vous inclut des informations sur les versions des composants inclus avec HDInsight en utilisant le document [Versioning des composants HDInsight](hdinsight-component-versioning.md) .

###<a name="bps10"></a>Cibler la version du système d’exploitation

HDInsight basé sur Linux est basé sur la distribution Ubuntu Linux. Plusieurs versions de HDInsight s’appuient sur les différentes versions d’Ubuntu, qui peut affecter le comportement de votre script. Par exemple, HDInsight 3.4 et les versions antérieur sont basées sur les versions de Ubuntu qui utilisent Upstart. Version 3.5 est basée sur Ubuntu 16.04, qui utilise Systemd. Systemd et Upstart s’appuient sur différentes commandes, afin que votre script doit être écrits pour fonctionner avec les deux.

Une autre différence importante entre HDInsight 3.4 et 3.5 est qui `JAVA_HOME` pointe désormais sur Java 8.

Vous pouvez vérifier la version du système d’exploitation en utilisant `lsb_release`. Extraits de code suivantes à partir du script d’installation teinte montre comment déterminer si le script s’exécute sur Ubuntu 14 ou 16 :

    OS_VERSION=$(lsb_release -sr)
    if [[ $OS_VERSION == 14* ]]; then
        echo "OS verion is $OS_VERSION. Using hue-binaries-14-04."
        HUE_TARFILE=hue-binaries-14-04.tgz
    elif [[ $OS_VERSION == 16* ]]; then
        echo "OS verion is $OS_VERSION. Using hue-binaries-16-04."
        HUE_TARFILE=hue-binaries-16-04.tgz
    fi
    ...
    if [[ $OS_VERSION == 16* ]]; then
        echo "Using systemd configuration"
        systemctl daemon-reload
        systemctl stop webwasb.service    
        systemctl start webwasb.service
    else
        echo "Using upstart configuration"
        initctl reload-configuration
        stop webwasb
        start webwasb
    fi
    ...
    if [[ $OS_VERSION == 14* ]]; then
        export JAVA_HOME=/usr/lib/jvm/java-7-openjdk-amd64
    elif [[ $OS_VERSION == 16* ]]; then
        export JAVA_HOME=/usr/lib/jvm/java-8-openjdk-amd64
    fi

Vous pouvez trouver le script complet qui contient ces extraits en https://hdiconfigactions.blob.core.windows.net/linuxhueconfigactionv02/install-hue-uber-v02.sh.

Pour la version d’Ubuntu utilisé par HDInsight, voir le document [version du composant HDInsight](hdinsight-component-versioning.md) .

Pour mieux comprendre les différences entre Systemd et Upstart, consultez [Systemd pour les utilisateurs Upstart](https://wiki.ubuntu.com/SystemdForUpstartUsers).

### <a name="bPS2"></a>Fournir stable des liens vers des ressources de script

Vous devez vous assurer que les scripts et les ressources utilisées par le script restent disponibles dans l’ensemble de la durée de vie du cluster, et que les versions de ces fichiers ne changent pas pendant la durée. Ces ressources sont nécessaires si de nouveaux nœuds sont ajoutés au cluster pendant le dimensionnement opérations.

La meilleure solution consiste à télécharger et archivés dans un compte de stockage Azure sur votre abonnement.

> [AZURE.IMPORTANT] Le compte de stockage utilisé doit être le compte de stockage par défaut pour le cluster ou un conteneur public, en lecture seule sur un autre compte de stockage.

Par exemple, les exemples fournis par Microsoft sont stockés dans le compte de stockage [https://hdiconfigactions.blob.core.windows.net/](https://hdiconfigactions.blob.core.windows.net/) , qui est un conteneur public, en lecture seule conservé par l’équipe HDInsight.

### <a name="bPS4"></a>Utilisez les ressources préalablement compilées

Pour réduire la durée que nécessaire pour exécuter le script, évitez les opérations que compiler les ressources de code source. En revanche, préalablement compiler les ressources et stocker la version binaire dans le stockage Blob Azure afin qu’il peut rapidement être téléchargé vers le cluster à partir de votre script.

### <a name="bPS3"></a>Vérifiez que le script de personnalisation cluster est idempotent

Les scripts doivent être conçus pour être équipotentes dans la mesure où si le script est exécuté plusieurs fois, il doit s’assurer que le cluster est retourné au même état chaque fois qu’il est exécuté.

Par exemple, si un script personnalisé installe une application en local/bin sur sa première exécution, puis sur chaque ultérieur exécuter le script doit vérifier si l’application existe déjà à l’emplacement local/bin avant de poursuivre avec d’autres étapes dans le script.

### <a name="bPS5"></a>Optimiser la disponibilité de l’architecture de cluster

Basé sur Linux HDInsight clusters fournit deux nœuds de tête qui sont actives au sein du cluster et script actions sont a été exécuté pour les deux nœuds. Si le composant que vous installez ne prévoyez qu’un seul nœud de tête, vous devez concevoir un script qui va installer uniquement le composant sur l’un des deux têtes nœuds dans le cluster.

> [AZURE.IMPORTANT] Services par défaut installés dans le cadre de HDInsight sont conçus pour basculer entre les deux nœuds de tête selon vos besoins, cependant cette fonctionnalité n’est pas étendue aux composants personnalisés installés par l’intermédiaire ctions de script. Si vous avez besoin de components via une action de script soit hautement disponible, vous devez implémenter votre propre mécanisme de basculement qui utilise les deux nœuds de tête disponibles.

### <a name="bPS6"></a>Configurer les composants personnalisés pour utiliser le stockage Blob Azure

Composants que vous installez sur le cluster peuvent avoir une configuration par défaut qui utilise le stockage système un fichier distribué Hadoop (HDFS). HDInsight utilise le stockage d’objets Blob Azure (WASB) comme le stockage par défaut. Cela fournit un système de fichiers compatibles HADOOP qui conserve les données même si le cluster est supprimé. Vous devez configurer les composants que vous installez pour utiliser WASB au lieu de HADOOP.

Par exemple, les éléments suivants copie du fichier giraph examples.jar de système de fichiers local vers WASB :

    hadoop fs -copyFromLocal /usr/hdp/current/giraph/giraph-examples.jar /example/jars/

### <a name="bPS7"></a>Écrire des informations dans STDOUT et STDERR

Informations écrites dans STDOUT et STDERR pendant l’exécution de scripts sont connectées et peuvent être affichées à l’aide de l’interface utilisateur de Ambari web.

> [AZURE.NOTE] Ambari sera uniquement disponible si le cluster est créé avec succès. Si vous utilisez une action de script lors de la création de cluster et création échoue, consultez la section Dépannage [que personnaliser HDInsight clusters à l’aide d’action de script](hdinsight-hadoop-customize-cluster-linux.md#troubleshooting) d’autres manières d’accéder aux informations enregistrées.

La plupart des packages d’installation et utilitaires déjà écrit les informations dans STDOUT et STDERR, cependant, vous souhaiterez peut-être ajouter journalisation supplémentaires. Pour envoyer du texte à utiliser STDOUT `echo`. Par exemple :

        echo "Getting ready to install Foo"

Par défaut, `echo` vous envoie la chaîne à STDOUT. Pour diriger vers STDERR, ajoutez `>&2` avant `echo`. Par exemple :

        >&2 echo "An error occurred installing Foo"

Cela redirige les informations envoyées à STDOUT (1, ce qui est par défaut donc ne pas répertorié ici,) vers STDERR (2). Pour plus d’informations sur la redirection IO, voir [http://www.tldp.org/LDP/abs/html/io-redirection.html](http://www.tldp.org/LDP/abs/html/io-redirection.html).

Pour plus d’informations sur l’affichage des informations enregistrées par les actions de script, voir [Personnaliser HDInsight clusters à l’aide d’action de script](hdinsight-hadoop-customize-cluster-linux.md#troubleshooting)

###<a name="bps8"></a>Enregistrer des fichiers au format ASCII fins de ligne de saut de ligne

Les scripts Bash doivent être stockés en tant que format ASCII, avec des lignes terminées par le saut de ligne. Si les fichiers stockés en tant que UTF-8, qui peut inclure une marque d’ordre octet au début du fichier, ou à des fins de ligne de CRLF, ce qui est courant pour les éditeurs de Windows, le script échoue avec erreurs semblables à ce qui suit :

    $'\r': command not found
    line 1: #!/usr/bin/env: No such file or directory

###<a name="bps9"></a>Utiliser la logique de nouvelle tentative pour récupérer les erreurs transitoires

Lorsque vous téléchargez des fichiers, l’installation de packages à l’aide de nature get, ou autres actions transmettre des données via internet, l’action peut échouer en raison des erreurs de mise en réseau temporaires. Par exemple, la ressource distante avec que vous communiquez peut-être en basculer vers un nœud de secours.

Pour apporter votre script résistant aux erreurs transitoires, vous pouvez implémenter une logique de nouvelle tentative. Voici un exemple d’une fonction qui exécutera n’importe quelle commande passée à celui-ci et (si la commande échoue,) réessayer jusqu'à trois fois. Il attend deux secondes entre chaque tentative.

    #retry
    MAXATTEMPTS=3

    retry() {
        local -r CMD="$@"
        local -i ATTMEPTNUM=1
        local -i RETRYINTERVAL=2

        until $CMD
        do
            if (( ATTMEPTNUM == MAXATTEMPTS ))
            then
                    echo "Attempt $ATTMEPTNUM failed. no more attempts left."
                    return 1
            else
                    echo "Attempt $ATTMEPTNUM failed! Retrying in $RETRYINTERVAL seconds..."
                    sleep $(( RETRYINTERVAL ))
                    ATTMEPTNUM=$ATTMEPTNUM+1
            fi
        done
    }

Voici quelques exemples d’utilisation de cette fonction.

    retry ls -ltr foo

    retry wget -O ./tmpfile.sh https://hdiconfigactions.blob.core.windows.net/linuxhueconfigactionv02/install-hue-uber-v02.sh

## <a name="helpermethods"></a>Méthodes d’assistance pour les scripts personnalisés

méthodes de script action d’assistance sont utilitaires que vous pouvez utiliser lors de l’écriture de scripts personnalisés. Ils sont définis dans [https://hdiconfigactions.blob.core.windows.net/linuxconfigactionmodulev01/HDInsightUtilities-v01.sh](https://hdiconfigactions.blob.core.windows.net/linuxconfigactionmodulev01/HDInsightUtilities-v01.sh)et peuvent être incluses dans vos scripts en utilisant les éléments suivants :

    # Import the helper method module.
    wget -O /tmp/HDInsightUtilities-v01.sh -q https://hdiconfigactions.blob.core.windows.net/linuxconfigactionmodulev01/HDInsightUtilities-v01.sh && source /tmp/HDInsightUtilities-v01.sh && rm -f /tmp/HDInsightUtilities-v01.sh

Les applications d’assistance suivantes fait désormais disponible pour une utilisation dans votre script :

| Utilisation d’assistance | Description |
| ------------ | ----------- |
| `download_file SOURCEURL DESTFILEPATH [OVERWRITE]` | Télécharge un fichier à partir de l’URL source sur le chemin d’accès de fichier spécifié. Par défaut, elle remplacera pas un fichier existant. |
| `untar_file TARFILE DESTDIR` | Extrait un fichier tar (à l’aide de `-xf`,) au répertoire de destination. |
| `test_is_headnode` | Si exécuté sur un nœud de tête cluster renvoie la valeur 1 ; Sinon, 0. |
| `test_is_datanode` | Si le nœud actuel est un nœud de données (travail), renvoie la valeur 1 ; Sinon, 0. |
| `test_is_first_datanode` | Si le nœud actuel est le premier nœud de données (collaborateur) (nommé workernode0), renvoie la valeur 1 ; Sinon, 0. |
| `get_headnodes` | Renvoie le nom de domaine complet de la headnodes dans le cluster. Les noms sont séparés par des virgules. Une chaîne vide est renvoyée erreur. |
| `get_primary_headnode` | Obtient le nom de domaine complet de la headnode principal. Une chaîne vide est renvoyée erreur. |
| `get_secondary_headnode` | Obtient le nom de domaine complet de la headnode secondaire. Une chaîne vide est renvoyée erreur. |
| `get_primary_headnode_number` | Obtient le suffixe numérique de la headnode principal. Une chaîne vide est renvoyée erreur. |
| `get_secondary_headnode_number` | Obtient le suffixe numérique de la headnode secondaire. Une chaîne vide est renvoyée erreur. |

## <a name="commonusage"></a>Modes d’utilisation courants

Cette section fournit des conseils sur l’implémentation parmi les modèles d’utilisation courants que vous pouvez rencontrer lors de l’écriture de votre propre script personnalisé.

### <a name="passing-parameters-to-a-script"></a>Passer des paramètres à un script

Dans certains cas, votre script peut-être nécessiter des paramètres. Par exemple, vous devrez peut-être le mot de passe d’administrateur pour le cluster afin de récupérer des informations à partir de l’API REST Ambari.

Paramètres passés au script sont appelées _des paramètres de positionnement_et sont affectées aux `$1` pour le premier paramètre, `$2` pour la deuxième et afin de. `$0`Indique le nom du script lui-même.

Valeurs transmis au script en tant que paramètres doivent être placées par des guillemets simples (') afin que la valeur passée est considérée comme un littéral et un traitement spécial n’est pas donné à caractères inclus tels que « ! ».

### <a name="setting-environment-variables"></a>Définition des variables d’environnement

Définition d’une variable d’environnement est effectuée par les éléments suivants :

    VARIABLENAME=value

Où NomVariable est le nom de la variable. Pour accéder à la variable après cela, utilisez `$VARIABLENAME`. Par exemple, pour attribuer une valeur fournie par un paramètre de position dans une variable d’environnement nommée mot de passe, utilisez ce qui suit :

    PASSWORD=$1

Accès ultérieures aux informations peut ensuite l’utiliser `$PASSWORD`.

Variables d’environnement définies dans le script existent uniquement au sein de l’étendue du script. Dans certains cas, vous devrez peut-être ajouter des variables d’environnement large système persistent une fois le script est terminé. En règle générale, il s’agit et permettre aux utilisateurs qui se connectent au cluster viaSSH servir les composants sont installés par votre script. Vous pouvez effectuer cela en ajoutant la variable d’environnement `/etc/environment`. Par exemple, l’exemple suivant ajoute __HADOOP\_la rendez-vous\_DIR__:

    echo "HADOOP_CONF_DIR=/etc/hadoop/conf" | sudo tee -a /etc/environment

### <a name="access-to-locations-where-the-custom-scripts-are-stored"></a>Accès aux emplacements où se trouvent les scripts personnalisés

À des options scripts permettent de personnaliser un cluster doivent être dans le compte de stockage par défaut pour le cluster ou, si sur un autre compte de stockage, dans un conteneur public en lecture seule. Si votre script accède aux ressources situées ailleurs ces doivent également être dans accessible au public (public au moins en lecture seule). Par exemple, vous souhaiterez peut-être télécharger un fichier sur le cluster à l’aide `download_file`.

Stockez le fichier dans un compte de stockage Azure accessible par le cluster (par exemple, le compte de stockage par défaut), fournissent un accès rapide, ce module complémentaire stockage est au sein du réseau Azure.

### <a name="checking-the-operating-system-version"></a>Vérification de la version du système d’exploitation

Plusieurs versions de HDInsight s’appuient sur des versions spécifiques de Ubuntu. Il peut y avoir des différences entre les versions du système d’exploitation que vous devez archiver pour dans votre script. Par exemple, vous devrez peut-être installer un fichier binaire qui est lié à la version d’Ubuntu.

Pour vérifier la version du système d’exploitation, utilisez `lsb_release`. Par exemple, le texte suivant montre comment faire référence à un fichier tar différentes en fonction de la version du système d’exploitation :

    OS_VERSION=$(lsb_release -sr)
    if [[ $OS_VERSION == 14* ]]; then
        echo "OS verion is $OS_VERSION. Using hue-binaries-14-04."
        HUE_TARFILE=hue-binaries-14-04.tgz
    elif [[ $OS_VERSION == 16* ]]; then
        echo "OS verion is $OS_VERSION. Using hue-binaries-16-04."
        HUE_TARFILE=hue-binaries-16-04.tgz
    fi

## <a name="deployScript"></a>Liste de vérification pour le déploiement d’une action de script

Voici les étapes que nous avons avant de déployer ces scripts :

- Placer les fichiers qui contiennent les scripts personnalisés dans un emplacement accessible par les nœuds cluster pendant le déploiement. Cela peut être un des comptes de stockage supplémentaires spécifiés au moment de déploiement de cluster, ou tout autre conteneur de stockage accessible au public par défaut.

- Ajouter des contrôles à des scripts pour vous assurer qu’ils exécutent impotently, afin que le script peut être exécuté plusieurs fois sur le même nœud.

- Utilisez un /tmp répertoire fichier temporaire pour conserver les fichiers téléchargés utilisés par les scripts et puis les nettoyer après ont l’exécution de scripts.

- Dans le cas où les paramètres au niveau du système d’exploitation ou les fichiers de configuration du service Hadoop ont été modifiés, vous voudrez peut-être redémarrer HDInsight services afin qu’ils peuvent intercepter tous les paramètres au niveau du système d’exploitation, tels que les variables d’environnement définies dans les scripts.

## <a name="runScriptAction"></a>Comment faire pour exécuter une action de script

Vous pouvez utiliser les actions de script pour personnaliser clusters HDInsight à l’aide du portail Azure, Azure PowerShell, modèles Azure Resource Manager (ARM) ou le Kit de développement .NET HDInsight. Pour obtenir des instructions, voir [comment utiliser l’action de script](hdinsight-hadoop-customize-cluster-linux.md).

## <a name="sampleScripts"></a>Exemples de script personnalisé

Microsoft fournit des exemples de scripts pour installer sur un cluster HDInsight. Exemples de scripts et obtenir des instructions sur la façon de les utiliser sont disponibles via les liens ci-dessous :

- [Installer et utiliser teinte sur clusters HDInsight](hdinsight-hadoop-hue-linux.md)
- [Installer et utiliser R sur HDInsight Hadoop clusters](hdinsight-hadoop-r-scripts-linux.md)
- [Installer et utiliser le mode série sur LAN.r sur clusters HDInsight](hdinsight-hadoop-solr-install-linux.md)
- [Installer et utiliser Giraph sur clusters HDInsight](hdinsight-hadoop-giraph-install-linux.md)  

> [AZURE.NOTE] Les documents liés ci-dessus sont spécifiques aux clusters HDInsight basé sur Linux. Pour les scripts fonctionnant avec HDInsight fonctionnant sous Windows, voir [développement d’action Script avec HDInsight (Windows)](hdinsight-hadoop-script-actions.md) ou utilisez les liens disponibles en haut de chaque article.

##<a name="troubleshooting"></a>Résolution des problèmes

Vous pouvez rencontrer lors de l’utilisation de scripts que vous avez développé des erreurs sont les suivantes :

__Error__: `$'\r': command not found`. Parfois suivi `syntax error: unexpected end of file`.

_Cause_: cette erreur se produit lorsque les lignes dans un script se terminent par CRLF. Les systèmes UNIX prévu uniquement le saut de ligne en tant que la fin de ligne.

Ce problème survient souvent lorsque le script a été créé dans un environnement Windows, comme CRLF est une ligne courantes se terminant pour de nombreux éditeurs de texte sous Windows.

_Résolution_: si elle est une option dans votre éditeur de texte, sélectionnez format Unix ou saut de ligne pour la fin de ligne. Vous pouvez également utiliser les commandes suivantes sur un système Unix pour modifier le CRLF à un saut de ligne :

> [AZURE.NOTE] Les commandes suivantes sont globalement équivalentes dans la mesure où ils doivent en changer les fins de ligne CRLF à saut de ligne. Sélectionnez une basée sur les utilitaires disponibles sur votre système.

| Commande | Notes |
| ------- | ----- |
| `unix2dos -b INFILE` | Le fichier d’origine est sauvegardé avec un. Extension BAK |
| `tr -d '\r' < INFILE > OUTFILE` | FICHIER_SORTIE contiendra une version avec uniquement les fins de saut de ligne |
| `perl -pi -e 's/\r\n/\n/g' INFILE` | Cela va modifier le fichier directement sans créer un nouveau fichier |
| ```sed 's/$'"/`echo \\\r`/" INFILE > OUTFILE``` | FICHIER_SORTIE contiendra une version avec uniquement les fins de saut de ligne.

__Error__: `line 1: #!/usr/bin/env: No such file or directory`.

_Cause_: cette erreur se produit lorsque le script a été enregistré en tant que UTF-8 avec une marque d’ordre d’octets (nomenclature).

_Résolution_: enregistrer le fichier au format ASCII ou UTF-8 sans une nomenclature. Vous pouvez également utiliser la commande suivante sur un système Linux ou Unix pour créer un nouveau fichier sans la nomenclature :

    awk 'NR==1{sub(/^\xef\xbb\xbf/,"")}{print}' INFILE > OUTFILE

Pour la commande ci-dessus, remplacez le fichier contenant la nomenclature __INFILE__ . __FICHIER_SORTIE__ doit être un nouveau nom de fichier qui contienne le script sans la nomenclature.

## <a name="seeAlso"></a>Étapes suivantes

* Découvrez comment [Personnaliser HDInsight clusters à l’aide d’action de script](hdinsight-hadoop-customize-cluster-linux.md)

* Utiliser la [référence HDInsight .NET SDK](https://msdn.microsoft.com/library/mt271028.aspx) pour en savoir plus sur la création d’applications .NET qui gèrent HDInsight

* Utiliser l' [API REST HDInsight](https://msdn.microsoft.com/library/azure/mt622197.aspx) pour apprendre à utiliser reste pour effectuer des actions de gestion sur clusters HDInsight.

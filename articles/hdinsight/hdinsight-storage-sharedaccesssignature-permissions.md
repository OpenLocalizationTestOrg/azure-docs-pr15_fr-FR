<properties
pageTitle="Limiter l’accès HDInsight à des données à l’aide de Signatures d’accès partagé"
description="Découvrez comment utiliser partagés accès Signatures limiter l’accès HDInsight aux données stockées dans des objets BLOB Azure stockage."
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
ms.date="10/11/2016"
ms.author="larryfr"/>

#<a name="use-azure-storage-shared-access-signatures-to-restrict-access-to-data-with-hdinsight"></a>Permet de limiter l’accès aux données avec HDInsight Azure stockage partagés accès Signatures

HDInsight utilise des objets BLOB Azure stockage pour stocker les données. Bien qu’HDInsight doit avoir un accès intégral au blob utilisé comme espace de stockage par défaut pour le cluster, vous pouvez limiter les autorisations aux données stockées dans d’autres objets BLOB utilisés par le cluster. Par exemple, vous souhaiterez peut-être rendre des données en lecture seule. Pour cela, l’utilisation de Signatures de l’accès partagé.

Accès aux Signatures (sa partagées) sont une fonctionnalité de comptes de stockage Azure qui vous permet de limiter l’accès aux données. Par exemple, fournir un accès en lecture seule aux données. Dans ce document, vous allez apprendre à utiliser les associations de sécurité pour activer des accès en lecture et liste uniquement à un conteneur blob à partir de HDInsight.

##<a name="requirements"></a>Configuration requise

* Un abonnement Azure

* C# ou Python. Exemple de code c# est fourni sous forme d’une solution Visual Studio.

    * Visual Studio doit être version 2013 ou 2015
    
    * Python doit être de version 2.7 ou version ultérieure

* Un HDInsight basé sur Linux cluster ou [Azure PowerShell] [ powershell] -si vous avez un cluster basé sur Linux existant, vous pouvez utiliser Ambari pour ajouter une Signature accès partagé pour le cluster. Si ce n’est pas le cas, vous pouvez utiliser PowerShell Azure pour créer un nouveau cluster et ajouter une Signature accès partagé lors de la création de cluster.

* Les exemples de fichiers à partir de [https://github.com/Azure-Samples/hdinsight-dotnet-python-azure-storage-shared-access-signature](https://github.com/Azure-Samples/hdinsight-dotnet-python-azure-storage-shared-access-signature). Ce référentiel comporte les éléments suivants :

    * Un projet Visual Studio qui peut créer un conteneur de stockage, stratégie stockée et associations de sécurité pour une utilisation avec HDInsight
    
    * Un script Python qui peut créer un conteneur de stockage, la stratégie stockée et associations de sécurité à utiliser avec HDInsight
    
    * Un script PowerShell qui peut créer un nouveau cluster HDInsight et configurez-le pour utiliser les associations de sécurité.

##<a name="shared-access-signatures"></a>Accès partagé Signatures

Il existe deux formes de Signatures de Access partagé :

* Ad hoc : l’heure de début, date d’expiration et des autorisations pour les associations de sécurité sont tout spécifié sur SAS URI (ou implicite, dans le cas où l’heure de début est omis).

* Stratégie d’accès de stockage : une stratégie d’accès stockée est définie sur un conteneur de ressources - un conteneur blob, table, file d’attente ou partage de fichiers - et peut être utilisée pour gérer les contraintes pour une ou plusieurs signatures accès partagé. Lorsque vous associez une associations de sécurité à une stratégie d’accès stockées, les associations de sécurité hérite les contraintes - l’heure de début, délai d’expiration et autorisations - définies pour la stratégie d’accès stockée.

La différence entre les deux formes est importante pour l’un des scénarios clés : révocation. Une sa est une URL, afin que les personnes qui obtiennent les associations de sécurité s’en servir, quelle que soit la qui a demandé dans un premier temps. Si une sa est publié publiquement, il peut être utilisée par tout le monde dans le monde. Associations de sécurité sont distribuée sont valide jusqu'à ce qu’une des quatre situations suivantes se produit :

1. Le délai d’expiration spécifié dans les associations de sécurité est atteint.

2. Le délai d’expiration indiqué sur la stratégie d’accès stockée référencée par les associations de sécurité est atteint (si une stratégie d’accès stockée est référencée, et que vous spécifiez un délai d’expiration). Cela peut se produire soit parce que l’intervalle s’écoule ou parce que vous avez modifié la stratégie d’accès stockée pour avoir un délai d’expiration passée, qui est un moyen de révoquer les associations de sécurité.

3. La stratégie d’accès stockée référencée par les associations de sécurité est supprimée, qui est une autre façon de révoquer les associations de sécurité. Notez que si vous recréez la stratégie d’accès stockées avec exactement le même nom, tous les jetons associations de sécurité existants à nouveau sera valides selon les autorisations associées à cette stratégie d’accès stockée (en supposant que le délai d’expiration sur les associations de sécurité n’a pas été). Si vous avez l’intention de révoquer les associations de sécurité, veillez à utiliser un nom différent si vous recréez la stratégie d’accès avec un délai d’expiration à l’avenir.

4. La clé du compte qui a été utilisée pour créer les associations de sécurité est régénérer. Notez que cette opération va entraîner le tous les composants d’application en utilisant cette clé de compte pour ne pas s’authentifier jusqu'à ce qu’ils sont mis à jour pour utiliser la clé de compte que vous venez régénérée ou l’autre clé de compte valide.

> [AZURE.IMPORTANT] Une signature accès partagé URI associée à la clé de compte utilisée pour créer la signature, et associées stockées stratégie d’accès (le cas échéant). Si aucune stratégie d’accès stockée n’est spécifié, le seul moyen pour révoquer une signature accès partagé consiste à modifier la clé de compte. 

Il est recommandé de toujours utiliser les stratégies d’accès stockées, afin que vous puissiez révoquer les signatures ou étendre la date d’expiration selon vos besoins. Les étapes décrites dans ce document utilisent stockées les stratégies d’accès pour générer les associations de sécurité.

Pour plus d’informations sur les Signatures de Access partagés, voir [Présentation du modèle associations de sécurité](../storage/storage-dotnet-shared-access-signature-part-1.md).

##<a name="create-a-stored-policy-and-generate-a-sas"></a>Créer une stratégie stockée et générer une associations de sécurité

Actuellement vous devez créer une stratégie stockée par programme. Vous pouvez trouver le c# et exemple Python de créer une stratégie stockée et les associations de sécurité de [https://github.com/Azure-Samples/hdinsight-dotnet-python-azure-storage-shared-access-signature](https://github.com/Azure-Samples/hdinsight-dotnet-python-azure-storage-shared-access-signature).

###<a name="create-a-stored-policy-and-sas-using-c"></a>Créer une stratégie stockée et les associations de sécurité à l’aide de C\#

1. Ouvrez la solution dans Visual Studio.

2. Dans l’Explorateur de solutions, avec le bouton droit sur le projet __SASToken__ et sélectionnez __Propriétés__.

3. Cliquez sur __paramètres__ et ajouter des valeurs pour les entrées suivantes :

    * StorageConnectionString : La chaîne de connexion pour le compte de stockage que vous souhaitez créer une stratégie stockée et les associations de sécurité pour. Le format doit être `DefaultEndpointsProtocol=https;AccountName=myaccount;AccountKey=mykey` où `myaccount` est le nom de votre compte de stockage et `mykey` est la clé pour le compte de stockage.
    
    * ContainerName : Le conteneur dans le compte de stockage que vous voulez limiter l’accès à.
    
    * SASPolicyName : Le nom à utiliser pour la stratégie stockée qui est créée.
    
    * FileToUpload : Le chemin d’accès à un fichier qui est téléchargé dans le conteneur.
    
4. Exécutez le projet. Une fenêtre de console s’affichent et informations semblables à celui-ci seront affichera une fois que les associations de sécurité a été générée :

        Container SAS token using stored access policy: sr=c&si=policyname&sig=dOAi8CXuz5Fm15EjRUu5dHlOzYNtcK3Afp1xqxniEps%3D&sv=2014-02-14
        
    Enregistrer le jeton de stratégie associations de sécurité, car vous en aurez besoin ceci lorsque vous associez le compte de stockage à votre cluster HDInsight. Vous devez également le nom de compte de stockage et le nom du conteneur.
    
###<a name="create-a-stored-policy-and-sas-using-python"></a>Créer une stratégie stockée et les associations de sécurité à l’aide de Python

1. Ouvrez le fichier SASToken.py et modifiez les valeurs suivantes :

    * stratégie\_nom : le nom à utiliser pour la stratégie stockée qui est créée.
    
    * stockage\_compte\_nom : le nom de votre compte de stockage.
    
    * stockage\_compte\_clé : la clé pour le compte de stockage.
    
    * stockage\_conteneur\_nom : le conteneur dans le compte de stockage que vous voulez limiter l’accès à.
    
    * exemple\_fichier\_chemin d’accès : le chemin d’accès à un fichier qui est téléchargé dans le conteneur

2. Exécuter le script. Le jeton de sa semblable au suivant s’affiche lorsque le script terminé :

        sr=c&si=policyname&sig=dOAi8CXuz5Fm15EjRUu5dHlOzYNtcK3Afp1xqxniEps%3D&sv=2014-02-14
    
    Enregistrer le jeton de stratégie associations de sécurité, car vous en aurez besoin ceci lorsque vous associez le compte de stockage à votre cluster HDInsight. Vous devez également le nom de compte de stockage et le nom du conteneur.
    
##<a name="use-the-sas-with-hdinsight"></a>Utiliser les associations de sécurité avec HDInsight

Lorsque vous créez un cluster de HDInsight, vous devez spécifier un compte de stockage principal et vous pouvez éventuellement spécifier des comptes d’espace de stockage supplémentaire. Ces deux méthodes d’ajout de stockage nécessitent un accès intégral aux comptes de stockage et conteneurs qui sont utilisés.

Pour pouvoir utiliser une Signature accès partagés pour limiter l’accès à un conteneur, vous devez ajouter une entrée personnalisée à la configuration du __site de base__ pour le cluster.

* Pour les clusters __Windows__ ou __basé sur Linux__ HDInsight, cela lors de la création de cluster à l’aide de PowerShell.

* Pour les __basé sur Linux__ HDInsight clusters, vous modifiez la configuration après la création d’un cluster à l’aide de Ambari.

###<a name="create-a-new-cluster-that-uses-the-sas"></a>Créer un nouveau cluster qui utilise les associations de sécurité

Exemple de création d’un cluster de HDInsight qui utilise les associations de sécurité est inclus dans le `CreateCluster` répertoire du référentiel. Pour l’utiliser, procédez comme suit :

1. Ouvrir le `CreateCluster\HDInsightSAS.ps1` de fichier dans un éditeur de texte et modifiez les valeurs suivantes au début du document.

        # Replace 'mycluster' with the name of the cluster to be created
        $clusterName = 'mycluster'
        # Valid values are 'Linux' and 'Windows'
        $osType = 'Linux'
        # Replace 'myresourcegroup' with the name of the group to be created
        $resourceGroupName = 'myresourcegroup'
        # Replace with the Azure data center you want to the cluster to live in
        $location = 'North Europe'
        # Replace with the name of the default storage account to be created
        $defaultStorageAccountName = 'mystorageaccount'
        # Replace with the name of the SAS container created earlier
        $SASContainerName = 'sascontainer'
        # Replace with the name of the SAS storage account created earlier
        $SASStorageAccountName = 'sasaccount'
        # Replace with the SAS token generated earlier
        $SASToken = 'sastoken'
        # Set the number of worker nodes in the cluster
        $clusterSizeInNodes = 2
        
    Par exemple, remplacez `'mycluster'` le nom du cluster que vous voulez créer. Les valeurs de sa doivent correspondre aux valeurs des étapes précédentes lorsque vous créez un compte de stockage et le jeton associations de sécurité.
    
    Une fois que vous avez modifié les valeurs, enregistrez le fichier.

1. Ouvrez une nouvelle invite PowerShell Azure. Si vous ne connaissez pas Azure PowerShell ou que vous ne le n'avez pas installé, voir [installer et configurer Azure PowerShell][powershell].

2. À partir de l’invite, utilisez ce qui suit s’authentifier à votre abonnement Azure :

        Login-AzureRmAccount
    
    Lorsque vous y êtes invité, connectez-vous à l’aide du compte pour votre abonnement Azure.
    
    Si votre nom d’utilisateur est associé à plusieurs abonnements Azure, vous devrez peut-être utiliser `Select-AzureRmSubscription` pour sélectionner l’abonnement que vous souhaitez utiliser.

2. À l’invite, accédez à la `CreateCluster` répertoire qui contient le fichier HDInsightSAS.ps1. Utilisez ensuite les éléments suivants pour exécuter le script
        
        .\HDInsightSAS.ps1
    
    Comme il s’exécute, sera journal sortie à l’invite PowerShell lorsqu’il crée des comptes de groupe et stockage la ressource. Il vous invitera ensuite à entrer des utilisateurs HTTP pour le cluster HDInsight. Il s’agit le compte d’utilisateur utilisé pour sécuriser l’accès au cluster HTTP/s.
    
    Si vous créez un cluster Linux, vous également devra pour un nom de compte d’utilisateur SSH et le mot de passe. Il est utilisé pour vous connecter à distance du cluster.
    
    > [AZURE.IMPORTANT] Lorsque vous y êtes invité le HTTP/s ou SSH nom d’utilisateur et mot de passe, vous devez fournir un mot de passe qui répond aux critères suivants :
    >
    > - Doit comporter au moins 10 caractères
    > - Doit contenir au moins un chiffre
    > - Doit contenir au moins un caractère non alphanumériques
    > - Doit contenir au moins une majuscule et minuscule


Il est assez long pour ce script pour s’exécuter, généralement environ 15 minutes. Une fois le script terminé sans erreur, le cluster a été créé.

###<a name="update-an-existing-cluster-to-use-the-sas"></a>Mettre à jour un cluster existant pour utiliser les associations de sécurité

Si vous avez un cluster basé sur Linux existant, vous pouvez ajouter les associations de sécurité à la configuration du __site de base__ en procédant comme suit :

1. Ouvrez le site web Ambari l’interface utilisateur pour votre cluster. L’adresse de cette page est https://YOURCLUSTERNAME.azurehdinsight.net. Lorsque vous y êtes invité, s’authentifier pour le cluster en utilisant le nom d’administrateur (administrateur) et mot de passe utilisé lors de la création du cluster.

2. Dans le côté gauche de l’interface utilisateur de Ambari web, sélectionnez __HADOOP__ , puis sélectionnez l’onglet __configurations__ au milieu de la page.

3. Sélectionnez l’onglet __Avancé__ , puis puis faites défiler jusqu'à la section __Custom core-site__ .

4. Développez la section __Custom core-site__ , puis faites défiler jusqu'à la fin et sélectionnez le lien __Ajouter une propriété...__ . Utilisez les valeurs suivantes pour les champs de __clé__ et la __valeur__ :

    * __Clé__: fs.azure.sas.CONTAINERNAME.STORAGEACCOUNTNAME.blob.core.windows.net
    
    * __Valeur__: les associations de sécurité retournée par l’application c# ou Python que vous avez exécuté précédemment
    
    Remplacez __CONTAINERNAME__ par le nom du conteneur que vous avez utilisé avec l’application c# ou associations de sécurité. Remplacez __STORAGEACCOUNTNAME__ par le nom de compte de stockage utilisé.

5. Cliquez sur le bouton __Ajouter__ pour enregistrer cette clé et la valeur, puis cliquez sur le bouton __Enregistrer__ pour enregistrer les modifications de configuration. Lorsque vous y êtes invité, ajouter une description de la modification (« en ajoutant l’accès au stockage sa » par exemple), puis cliquez sur __Enregistrer__.

    Cliquez sur __OK__ lorsque les modifications ont été effectuées.

    > [AZURE.IMPORTANT] Cela enregistre les modifications de configuration, mais vous devez redémarrer plusieurs services avant que la modification prenne effet.

6. Dans l’interface utilisateur du Ambari web, sélectionnez __HADOOP__ à partir de la liste à gauche, puis __Redémarrez tous les__ dans la liste à droite déroulante __Actions de Service__ . Lorsque vous y êtes invité, sélectionnez __Activer le mode maintenance__ et puis sélectionnez __Conform redémarrez tous les ».

    Répétez cette procédure pour les entrées MapReduce2 et fils à partir de la liste à gauche de la page.

7. Une fois ces ont redémarré, sélectionnez chacun d'entre eux et désactivent le mode maintenance à partir des __Actions de Service__ liste déroulante.

##<a name="test-restricted-access"></a>Tester l’accès restreint

Pour vérifier que vous avez limité l’accès, procédez comme suit :

* Pour les clusters de HDInsight __fonctionnant sous Windows__ , utilisez Bureau à distance pour vous connecter au cluster. Pour plus d’informations, consultez [pu se connecter à HDInsight à l’aide de RDP](hdinsight-administer-use-management-portal.md#connect-to-clusters-using-rdp) .

    Une fois connecté, utilisez l’icône de __ligne de commande Hadoop__ sur le bureau pour ouvrir une invite de commande.

* Pour les __basé sur Linux__ HDInsight clusters, utiliser SSH pour vous connecter au cluster. Consultez une des opérations suivantes pour plus d’informations sur l’utilisation de SSH avec clusters basés sur Linux :

    * [Utiliser le protocole SSH avec basé sur Linux Hadoop sur HDInsight de Linux, OS X et Unix](hdinsight-hadoop-linux-use-ssh-unix.md)
    * [Utiliser le protocole SSH avec basé sur Linux Hadoop sur HDInsight à partir de Windows](hdinsight-hadoop-linux-use-ssh-windows.md)
    
Une fois connecté au cluster, procédez comme suit pour vérifier que vous pouvez uniquement des éléments de liste et en lecture sur le compte de stockage associations de sécurité :

1. À l’invite, tapez les informations suivantes. Remplacez __SASCONTAINER__ par le nom du conteneur créé pour le compte de stockage associations de sécurité. Remplacez __SASACCOUNTNAME__ par le nom du compte de stockage utilisé pour les associations de sécurité :

        hdfs dfs -ls wasbs://SASCONTAINER@SASACCOUNTNAME.blob.core.windows.net/
    
    Cela permet de répertorier le contenu du conteneur, qui doit inclure le fichier qui a été téléchargé lorsque le conteneur et les associations de sécurité a été créé.
    
2. Utilisez ce qui suit pour vérifier que vous pouvez lire le contenu du fichier. Remplacez le __SASCONTAINER__ et __SASACCOUNTNAME__ comme dans l’étape précédente. Remplacez le __nom de fichier__ par le nom du fichier affiché dans la commande précédente :

        hdfs dfs -text wasbs://SASCONTAINER@SASACCOUNTNAME.blob.core.windows.net/FILENAME
        
    Cela permet de répertorier le contenu du fichier.
    
3. Pour télécharger le fichier sur le système de fichiers local, utilisez ce qui suit :

        hdfs dfs -get wasbs://SASCONTAINER@SASACCOUNTNAME.blob.core.windows.net/FILENAME testfile.txt
    
    Cela télécharge le fichier dans un fichier local nommé __testfile.txt__.

4. Pour télécharger le fichier local dans un nouveau fichier nommé __testupload.txt__ sur le stockage de sa, utilisez ce qui suit :

        hdfs dfs -put testfile.txt wasbs://SASCONTAINER@SASACCOUNTNAME.blob.core.windows.net/testupload.txt
    
    Vous recevrez un message semblable à ce qui suit :
    
        put: java.io.IOException
        
    Cette erreur se produit car l’emplacement de stockage est lu + liste uniquement. Mettre les données sur le stockage par défaut pour le cluster, qui est accessible en écriture, utilisez ce qui suit :
    
        hdfs dfs -put testfile.txt wasbs:///testupload.txt
        
    Cette fois, l’opération doit se terminer normalement.
    
##<a name="troubleshooting"></a>Résolution des problèmes

###<a name="a-task-was-canceled"></a>Une tâche a été annulée.

__Symptômes__: lorsque vous créez un cluster en utilisant le script PowerShell, vous pouvez recevoir le message d’erreur suivant :

    New-AzureRmHDInsightCluster : A task was canceled.
    At C:\Users\larryfr\Documents\GitHub\hdinsight-azure-storage-sas\CreateCluster\HDInsightSAS.ps1:62 char:5
    +     New-AzureRmHDInsightCluster `
    +     ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
        + CategoryInfo          : NotSpecified: (:) [New-AzureRmHDInsightCluster], CloudException
        + FullyQualifiedErrorId : Hyak.Common.CloudException,Microsoft.Azure.Commands.HDInsight.NewAzureHDInsightClusterCommand

__Cause__: cette erreur peut se produire si vous utilisez un mot de passe pour l’utilisateur d’administration/HTTP pour le cluster ou (pour les clusters basés sur Linux,) l’utilisateur SSH.

__Résolution__: utiliser un mot de passe qui répond aux critères suivants :

- Doit comporter au moins 10 caractères
- Doit contenir au moins un chiffre
- Doit contenir au moins un caractère non alphanumériques
- Doit contenir au moins une majuscule et minuscule

##<a name="next-steps"></a>Étapes suivantes

À présent que vous avez appris à ajouter un stockage à accès limité à votre cluster HDInsight, découvrez d’autres méthodes pour travailler avec des données sur votre cluster :

* [Utiliser Hive avec HDInsight](hdinsight-use-hive.md)

* [Utiliser cochon avec HDInsight](hdinsight-use-pig.md)

* [Utiliser MapReduce avec HDInsight](hdinsight-use-mapreduce.md)

[powershell]: ../powershell-install-configure.md

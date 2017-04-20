<properties
    pageTitle="Installer RStudio avec serveur R sur HDInsight (preview) | Microsoft Azure"
    description="Comment installer RStudio avec serveur R sur HDInsight (preview)."
    services="hdinsight"
    documentationCenter=""
    authors="jeffstokes72"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="09/16/2016"
   ms.author="jeffstok"/>


# <a name="installing-rstudio-with-r-server-on-hdinsight-preview"></a>L’installation de RStudio avec serveur R sur HDInsight (preview)

Il existe plusieurs environnements de développement intégré (IDE) pour R aujourd'hui, notamment de Microsoft récemment annoncé [R Tools pour Visual Studio](https://www.visualstudio.com/en-us/features/rtvs-vs.aspx) (RTV), une gamme d’outils de bureau et de serveur de [RStudio](https://www.rstudio.com/products/rstudio-server/)ou basés sur Eclipse de Walware [StatET](http://www.walware.de/goto/statet). Parmi les plus populaires sur Linux est l’utilisation du [Serveur RStudio](https://www.rstudio.com/products/rstudio-server/) qui fournit un IDE via un navigateur à utiliser par les clients à distance.  Installez RStudio Server sur le nœud de bord d’un cluster HDInsight Premium fournit un environnement IDE complet pour le développement et l’exécution de scripts R avec serveur R sur le cluster et peut être beaucoup plus productif à utiliser par défaut de la Console R.

Dans cet article, vous allez apprendre à installer la version de (gratuit) communautaire du serveur RStudio sur le nœud de bord d’un cluster à l’aide d’un script personnalisé. Si vous préférez utiliser la version Pro commercialement sous licence du serveur RStudio, vous devez suivre les instructions d’installation de [Serveur RStudio](https://www.rstudio.com/products/rstudio/download-server/).

> [AZURE.NOTE] Les étapes décrites dans ce document requièrent un serveur R sur cluster HDInsight et ne fonctionnera pas correctement si vous utilisez un cluster HDInsight où R a été installé à l’aide de l' [Action de Script R installer](hdinsight-hadoop-r-scripts-linux.md).

## <a name="prerequisites"></a>Conditions préalables

* Un cluster Azure HDInsight et R Server est installé. Pour plus d’informations, voir [prise en main serveur R sur clusters HDInsight](hdinsight-hadoop-r-server-get-started.md).
* Un clientSSH. Pour la distribution Linux et Unix ou Macintosh OS X, la `ssh` commande est fournie avec le système d’exploitation. Pour Windows, nous vous recommandons de [Cygwin](http://www.redhat.com/services/custom/cygwin/) avec [l’option OpenSSH](https://www.youtube.com/watch?v=CwYSvvGaiWU), ou [PuTTY](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html).  


## <a name="install-rstudio-on-the-cluster-using-a-custom-script"></a>Installer RStudio sur le cluster à l’aide d’un script personnalisé

1. Identifier le nœud de bord du cluster. Pour un cluster HDInsight avec serveur R, voici la dénomination nœud de tête et nœud de bord.

    * Serveur de nœud-`CLUSTERNAME-ssh.azurehdinsight.net`
    * Nœud de bord :`R-Server.CLUSTERNAME-ssh.azurehdinsight.net` 

2. SSH dans le nœud de bord du cluster en utilisant le modèle d’appellation ci-dessus. 
 
    * Si vous vous connectez à partir d’un client Linux, voir [se connecter à un cluster basé sur Linux HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md#connect-to-a-linux-based-hdinsight-cluster).
    * Si vous vous connectez à partir d’un client Windows, voir [se connecter à un cluster basé sur Linux HDInsight à l’aide de PuTTY](hdinsight-hadoop-linux-use-ssh-windows.md#connect-to-a-linux-based-hdinsight-cluster).

3. Une fois que vous êtes connecté, deviennent un utilisateur racine sur le cluster. Dans la session SSH, utilisez la commande suivante.

        sudo su -

4. Téléchargez le script personnalisé pour installer RStudio. Utilisez la commande suivante.

        wget http://mrsactionscripts.blob.core.windows.net/rstudio-server-community-v01/InstallRStudio.sh

5. Modifier les autorisations sur le fichier de script personnalisé et exécuter le script. Utilisez les commandes suivantes.

        chmod 755 InstallRStudio.sh
        ./InstallRStudio.sh

6. Si vous avez utilisé un mot de passe SSH lors de la création d’un cluster HDInsight avec serveur R, vous pouvez ignorer cette étape et passez à la suivante. Si vous avez utilisé un code SSH à la place pour créer le cluster, vous devez définir un mot de passe pour votre utilisateur SSH. Vous avez besoin de ce mot de passe lorsque vous vous connectez à RStudio. Exécutez les commandes suivantes. Lorsque vous y êtes invité pour un **mot de passe Kerberos actif**, appuyez simplement sur **entrée**.  Notez que vous devez remplacer `USERNAME` avec un utilisateur SSH pour votre cluster HDInsight.

        passwd USERNAME
        Current Kerberos password:
        New password:
        Retype new password:
        Current Kerberos password:
        
    Si votre mot de passe est configuré correctement, vous devez voir un message comme suit.

        passwd: password updated successfully


    Quitter la session SSH.

7. Créer un tunnel SSH pour le cluster en mappant `localhost:8787` sur le cluster HDInsight sur l’ordinateur client. Vous devez créer un tunnel SSH avant d’ouvrir une nouvelle session de navigateur.

    * Sur un client Linux ou d’un client Windows avec [Cygwin](http://www.redhat.com/services/custom/cygwin/) puis ouvrez une session Terminal Server et utilisez la commande suivante.

            ssh -L localhost:8787:localhost:8787 USERNAME@R-Server.CLUSTERNAME-ssh.azurehdinsight.net
            
        Remplacez le **nom d’utilisateur** avec un utilisateur SSH pour votre cluster HDInsight et **NOMDUCLUSTER** avec le nom de votre cluster HDInsight vous pouvez également utiliser une clé SSH au lieu d’un mot de passe en ajoutant`-i id_rsa_key`     

    * Si vous utilisez un client Windows avec PuTTY puis

        1.  Ouvrez PuTTY et entrez vos informations de connexion. Si vous n’êtes pas familiarisé avec PuTTY, voir [Utiliser SSH avec basé sur Linux Hadoop sur HDInsight à partir de Windows](hdinsight-hadoop-linux-use-ssh-windows.md) pour plus d’informations sur l’utilisation avec HDInsight.
        2.  Dans la section **catégorie** à gauche de la boîte de dialogue, développez **connexion**, développez **SSH**et puis sélectionnez **tunnel**.
        3.  Dans l’écran **Options de contrôle de transfert de port SSH** , fournissent les informations suivantes :

            * **Port source** : le port sur le client que vous souhaitez transférer. Par exemple, **8787**.
            * **Destination** - la destination doit être mappée à l’ordinateur client local. Par exemple, **localhost:8787**.

            ![Créer un tunnel SSH] (./media/hdinsight-hadoop-r-server-install-r-studio/createsshtunnel.png "Créer un tunnel SSH")

        4. Cliquez sur **Ajouter** pour ajouter les paramètres, puis cliquez sur **Ouvrir** pour ouvrir une connexion SSH.
        5. Lorsque vous y êtes invité, connectez-vous au serveur. Vous établissez une session SSH et activer le tunnel.

8. Ouvrez un navigateur web et entrez l’URL suivante en fonction du port que vous avez entré du tunnel.

        http://localhost:8787/ 

9. Vous devrez entrer le nom d’utilisateur SSH et le mot de passe pour vous connecter au cluster. Si vous avez utilisé un code SSH lors de la création du cluster, vous devez entrer le mot de passe que vous avez créé à l’étape 5 ci-dessus.

    ![Se connecter à R Studio] (./media/hdinsight-hadoop-r-server-install-r-studio/connecttostudio.png "Créer un tunnel SSH")

10. Pour vérifier que l’installation RStudio a réussi, vous pouvez effectuer un test script qui s’exécute R en fonction des travaux MapReduce et explosion sur le cluster. Revenez à la console SSH et entrez les commandes suivantes pour télécharger le script de test pour s’exécuter en RStudio.

    * Si vous avez créé un cluster Hadoop avec R, utilisez cette commande.
        
            wget http://mrsactionscripts.blob.core.windows.net/rstudio-server-community-v01/testhdi.r

    * Si vous avez créé un cluster explosion avec R, utilisez cette commande.

            wget http://mrsactionscripts.blob.core.windows.net/rstudio-server-community-v01/testhdi_spark.r

11. Dans RStudio, vous verrez le script de test que vous avez téléchargé. Double-cliquez sur le fichier pour l’ouvrir, sélectionnez le contenu du fichier, puis sur **exécuter**. Vous devriez voir le résultat dans le volet **Console** .
 
    ![Test de l’installation] (./media/hdinsight-hadoop-r-server-install-r-studio/test-r-script.png "Test de l’installation")

Une autre option serait d’entrer `source(testhdi.r)` ou `source(testhdi_spark.r)` pour exécuter le script.

## <a name="see-also"></a>Voir aussi

- [Calculer les options de contexte pour R Server sur clusters HDInsight](hdinsight-hadoop-r-server-compute-contexts.md)

- [Azure options de stockage pour serveur R HDInsight premium](hdinsight-hadoop-r-server-storage.md)


 

<properties
    pageTitle="Configurer une machine virtuelle SQL Server comme un serveur de bloc-notes IPython | Microsoft Azure"
    description="Définir la Machine virtuelle scientifique un données avec SQL Server et IPython Server."
    services="machine-learning"
    documentationCenter=""
    authors="bradsev" 
    manager="jhubbard"
    editor="cgronlun" />

<tags
    ms.service="machine-learning"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/19/2016"
    ms.author="xibingao;bradsev" />

# <a name="set-up-an-azure-sql-server-virtual-machine-as-an-ipython-notebook-server-for-advanced-analytics"></a>Configurer une machine virtuelle Azure SQL Server comme un serveur IPython bloc-notes pour analytique avancées

Cette rubrique indique comment mettre en service et configurer une machine virtuelle SQL Server à utiliser dans le cadre d’un environnement scientifique de données sur le nuage. La machine virtuelle Windows est configurée avec prise en charge d’outils tels que le bloc-notes IPython, Explorateur de stockage Azure et AzCopy, ainsi que d’autres utilitaires pouvant être utilisés pour les projets scientifique de données. Explorateur de stockage Azure et AzCopy, par exemple, fournissent peut être pratique pour télécharger des données à Azure blob storage à partir de votre ordinateur local ou pour le télécharger sur votre ordinateur local depuis le stockage blob.

La galerie Azure machine virtuelle inclut plusieurs images contenant Microsoft SQL Server. Sélectionnez une image de machine virtuelle SQL Server est adaptée à vos besoins en matière de données. Images recommandés sont :

- SQL Server 2012 Service Pack 2 Enterprise pour les petites et tailles de données moyennes
- SQL Server 2012 SP2 entreprise optimisé pour les charges de travail DataWarehousing pour les formats de données volumineux à très grande

 > [AZURE.NOTE] SQL Server 2012 Service Pack 2 Enterprise image **n’inclut pas d’un disque de données**. Vous devrez ajouter et/ou joindre un ou plusieurs disques durs virtuels pour stocker vos données. Lorsque vous créez une machine virtuelle Azure, il a un disque du système d’exploitation mappé avec le lecteur C et un disque temporaire mappée avec le lecteur D. N’utilisez pas le lecteur D pour stocker les données. Comme le nom l’indique, il fournit un stockage temporaire uniquement. Il ne propose aucune redondance ou la sauvegarde, car il ne se trouvent dans le stockage Azure.


##<a name="Provision"></a>Se connecter au portail classique Azure et configurer une machine virtuelle SQL Server

1.  Connectez-vous au [Portail classique Azure](http://manage.windowsazure.com/) à l’aide de votre compte.
    Si vous n’avez pas un compte Azure, visitez [Azure essai gratuit](https://azure.microsoft.com/pricing/free-trial/).

2.  Dans le portail classique Azure, en bas à gauche de la page web, cliquez sur **+ Nouveau**et cliquez sur **Calculer**, cliquez sur **MACHINE virtuelle**, puis cliquez sur **Galerie FROM**.

3.  Dans la page **créer une Machine virtuelle** , sélectionnez une image de machine virtuelle contenant SQL Server selon vos besoins en matière de données, puis cliquez sur la flèche en bas à droite de la page suivante. Pour obtenir les dernières informations sur les images de SQL Server pris en charge sur Azure, consultez [Prise en main SQL Server dans le Machines virtuelles Azure](http://go.microsoft.com/fwlink/p/?LinkId=294720) rubrique dans la documentation de [SQL Server dans le Machines virtuelles Azure](http://go.microsoft.com/fwlink/p/?LinkId=294719) définir.

    ![Sélectionnez SQL Server machine virtuelle][1]

4.  Sur la première page de **Configuration de Machine virtuelle** , fournissent les informations suivantes :

    -   Attribuez un **nom de la MACHINE virtuelle**.
    -   Dans la zone **Nouveau nom d’utilisateur** , tapez le nom d’utilisateur unique pour le compte d’administrateur local machine virtuelle.
    -   Dans la zone **Nouveau mot de passe** , tapez un mot de passe. Pour plus d’informations, voir [Les mots de passe forts](http://msdn.microsoft.com/library/ms161962.aspx).
    -   Dans la zone **Confirmer le mot de passe** , retapez le mot de passe.
    -   Sélectionnez la **taille** appropriée dans la liste déroulante.

     > [AZURE.NOTE] La taille de la machine virtuelle est spécifiée lors de la mise en service : A2 est la plus petite taille recommandée pour les charges de travail. La taille minimale recommandée pour une machine virtuelle est la cellule A3 lors de l’utilisation de SQL Server Enterprise Edition. Sélectionnez la cellule A3 ou une version ultérieure lors de l’utilisation de SQL Server Enterprise Edition. Sélectionnez A4 lors de l’utilisation de SQL Server 2012 ou 2014 entreprise optimisée pour les images de transactions charges de travail.
Sélectionnez A7 lors de l’utilisation de SQL Server 2012 ou 2014 entreprise optimisée pour les images de charges de travail de stockage de données. Le nombre de disques de données que vous pouvez configurer les limites de la taille sélectionnée. Pour obtenir des informations plus récentes sur taille machine virtuelle disponibles et le nombre de disques de données que vous pouvez joindre à une machine virtuelle, voir [Tailles Machine virtuelle pour Azure](http://msdn.microsoft.com/library/azure/dn197896.aspx). Informations de tarification, voir [VIrtual Macines tarifs](https://azure.microsoft.com/pricing/details/virtual-machines/).

    Cliquez sur la flèche suivant dans la partie inférieure droite pour continuer.

    ![Configuration de machines virtuelles][2]

5.  Sur la deuxième page de **configuration de machine virtuelle** , configurer les ressources pour la mise en réseau, le stockage et disponibilité :

    -   Dans la zone de **Service Cloud** , choisissez **créer un nouveau service cloud**.
    -   Dans la zone **Nom DNS du Service Cloud** , fournissent la première partie d’un nom DNS de votre choix, afin qu’elle termine un nom au format **TESTNAME.cloudapp.net**
    -   Dans la zone **Réseau de groupe/virtuelle affinité/région** , sélectionnez une région hébergeant cette image virtuelle.
    -   Dans le **Compte de stockage**, sélectionnez un compte de stockage existant ou sélectionnez une générée automatiquement.
    -   Dans la zone **Définir disponibilité** , sélectionnez **(aucun)**.
    -   Lisez et acceptez les informations de tarification.

6.  Dans la section **points de terminaison** , cliquez sur dans la liste déroulante vide sous **nom**et sélectionnez **MSSQL** , puis tapez le numéro de port de l’instance du moteur de base de données (**1433** pour l’instance par défaut).

7.  Votre machine virtuelle de SQL Server peut également servir d’un serveur de bloc-notes IPython, qui sera configuré dans une étape ultérieure.
    Ajouter un point de terminaison pour spécifier le port à utiliser pour votre serveur IPython bloc-notes. Entrez un nom dans la colonne **nom** , sélectionnez un numéro de port de votre choix pour le port public et 9999 pour le port privé.

    Cliquez sur la flèche suivant dans la partie inférieure droite pour continuer.

    ![Sélectionnez les ports MSSQL et IPython][3]

8.  Acceptez l’option **installer machine virtuelle agent** par défaut activée, puis cliquez sur la la coche située dans le coin inférieur droit de l’Assistant pour terminer la machine virtuelle processus de mise en service.

    `![Machine virtuelle dernières Options][4]

9.  Patientez pendant l’Azure prépare votre machine virtuelle. Attendez l’état de la machine virtuelle pour passer :

    -   Début (mise en service)
    -   Arrêté
    -   Début (mise en service)
    -   En cours d’exécution (mise en service)
    -   En cours d’exécution


##<a name="RemoteDesktop"></a>Ouvrez la machine virtuelle à l’aide de bureau à distance et terminer l’installation

1.  Lors de la mise en service terminée, cliquez sur le nom de votre machine virtuelle pour accéder à la page de tableau de bord. Dans la partie inférieure de la page, cliquez sur **se connecter**.

2.  Cliquez sur pour ouvrir le fichier rpd en utilisant le programme de bureau à distance Windows (`%windir%\system32\mstsc.exe`).

3.  Dans la boîte de dialogue **Sécurité de Windows** , fournissent le mot de passe du compte d’administrateur local que vous avez spécifié dans une étape précédente.
    (Vous pourriez être invité à vérifier les informations d’identification de la machine virtuelle.)

4.  La première fois que vous ouvrez une session sur cet ordinateur virtuel, plusieurs processus peuvent avoir besoin terminer, y compris le programme d’installation de votre bureau, mises à jour Windows et de fin des tâches de configuration initiale de Windows (sysprep). Une fois Windows sysprep terminée, le programme d’installation de SQL Server termine les tâches de configuration. Ces tâches peuvent entraîner un délai de quelques minutes pendant qu’ils terminer. `SELECT @@SERVERNAME`peut ne pas renvoyer le nom correct jusqu'à ce que le programme d’installation de SQL Server est terminée, SQL Server Management Studio peut ne pas être visible dans la page de démarrage.

Une fois que vous êtes connecté à la machine virtuelle avec Bureau à distance Windows, la machine virtuelle fonctionne comme n’importe quel autre ordinateur. Connectez-vous à l’instance par défaut de SQL Server avec SQL Server Management Studio (en cours d’exécution sur l’ordinateur virtuel) à l’accoutumée.


##<a name="InstallIPython"></a>Installer IPython bloc-notes et autres outils de prise en charge

Pour configurer votre nouvel ordinateur SQL serveur virtuel pour être utilisé comme un serveur IPython bloc-notes et installer d’autres outils de prise en charge ce AzCopy, Explorateur de stockage Azure, packages données scientifique Python utiles et d’autres personnes, un script de personnalisation spécial est fourni pour vous. Pour installer :

- Cliquez sur l’icône de **Démarrage de Windows** , puis cliquez sur **invite de commandes (administrateur)**
- Copiez les commandes suivantes et coller à l’invite.

        set script='https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/MachineSetup/Azure_VM_Setup_Windows.ps1'
        @powershell -NoProfile -ExecutionPolicy unrestricted -Command "iex ((new-object net.webclient).DownloadString(%script%))"

- Lorsque vous y êtes invité, entrez un mot de passe de votre choix pour le serveur IPython bloc-notes.
- Le script de personnalisation automatise plusieurs procédures après l’installation, qui incluent :
    + Installation et configuration du serveur IPython bloc-notes
    + Pour ouvrir les ports TCP dans le pare-feu Windows pour les points de terminaison créé précédemment :
    + Pour la connectivité à distance SQL Server
    + Pour la connectivité à distance bloc-notes IPython server
    + L’extraction des exemples d’agendas IPython et les scripts SQL
    + Téléchargement et installation de packages données scientifique Python utiles
    + Télécharger et installer les outils Azure tels que AzCopy et Explorateur de stockage Azure  
<br>
- Vous pouvez accéder et exécuter IPython bloc-notes à partir de n’importe quel navigateur locale ou distante à l’aide d’une URL de l’écran `https://<virtual_machine_DNS_name>:<port>`, où port est le port public IPython sélectionné lors de la mise en service de la machine virtuelle.
- Serveur IPython bloc-notes s’exécute comme un service en arrière-plan et sera redémarré automatiquement lorsque vous redémarrez l’ordinateur virtuel.

##<a name="Optional"></a>Joindre disque de données selon vos besoins

Si votre image machine virtuelle n’inclut pas de données disques, c'est-à-dire que le lecteur C (disque du système d’exploitation) ou le lecteur D (disque temporaire), vous devez ajouter un ou plusieurs disques de données pour stocker vos données. L’image de la machine virtuelle pour SQL Server 2012 Service Pack 2 entreprise optimisé pour les charges de travail DataWarehousing est préconfigurée avec des disques supplémentaires pour les fichiers journaux et de données SQL Server.

 > [AZURE.NOTE] N’utilisez pas le lecteur D pour stocker les données. Comme le nom l’indique, il fournit un stockage temporaire uniquement. Il ne propose aucune redondance ou la sauvegarde, car il ne se trouvent dans le stockage Azure.

Pour joindre disques de données supplémentaires, suivez les étapes décrites dans la [façon de joindre un disque de données pour une Machine virtuelle Windows](virtual-machines-windows-classic-attach-disk.md), qui vous guidera :

1. Attacher des disques vides à la machine virtuelle sa mise en service dans les premières étapes
2. Initialisation des nouvelle disques dans la machine virtuelle


##<a name="SSMS"></a>Se connecter à SQL Server Management Studio et activer le mode d’authentification mixte

Le moteur de base de données SQL Server ne peut pas utiliser l’authentification Windows sans environnement de domaine. Pour vous connecter au moteur de base de données à partir d’un autre ordinateur, configurez SQL Server pour l’authentification en mode mixte. Mode d’authentification mixte permet l’authentification SQL Server et l’authentification Windows. Mode d’authentification SQL est nécessaire pour l’acquisition de données directement à partir de vos bases de données machine virtuelle SQL Server dans [Azure Machine apprentissage Studio](https://studio.azureml.net) l’utilisation du module importer des données.

1.  Lorsque vous êtes connecté à la machine virtuelle à l’aide de bureau à distance, utilisez le volet Windows **Search** et tapez **SQL Server Management Studio** (SMS). Cliquez pour démarrer SQL Server Management Studio (SSMS). Voulez-vous ajouter un raccourci vers SSMS sur votre ordinateur de bureau pour une utilisation ultérieure.

    ![Démarrer SSMS][5]

    La première fois que vous ouvrez Management Studio il doit créer l’environnement Management Studio des utilisateurs. Cela peut prendre quelques instants.

2.  Lors de l’ouverture, Management Studio présente la boîte de dialogue **se connecter au serveur** . Dans la zone **nom du serveur** , tapez le nom de la machine virtuelle pour vous connecter au moteur de base de données avec l’Explorateur d’objets.
    (Plutôt que le nom de la machine virtuelle vous pouvez également utiliser **(local)** ou une période donnée en tant que le **nom du serveur**. Sélectionnez **L’authentification Windows**et laissez ** *votre\_machine virtuelle\_nom*\\votre\_local\_administrateur** dans la zone **nom d’utilisateur** . Cliquez sur **se connecter**.

    ![Se connecter au serveur][6]

    <br>

     > [AZURE.TIP] Vous pouvez modifier le mode d’authentification SQL Server à l’aide d’un changement de clé de Registre Windows ou SQL Server Management Studio. Pour modifier le mode d’authentification à l’aide de la modification de la clé du Registre, démarrez une **Nouvelle requête** et exécutez le script suivant :

        USE master
        go

        EXEC xp_instance_regwrite N'HKEY_LOCAL_MACHINE', N'Software\Microsoft\MSSQLServer\MSSQLServer', N'LoginMode', REG_DWORD, 2
        go


    Pour modifier le mode d’authentification à l’aide de SQL Server management Studio :

3.  Dans l' **Explorateur d’objets SQL Server Management Studio**, double-cliquez sur le nom de l’instance de SQL Server (le nom de la machine virtuelle), puis cliquez sur **Propriétés**.

    ![Propriétés du serveur][7]

4.  Dans la page de **sécurité** , sous **authentification du serveur**, sélectionnez **SQL Server et mode d’authentification Windows**, puis cliquez sur **OK**.

    ![Sélectionnez le Mode d’authentification][8]

5.  Dans la boîte de dialogue **SQL Server Management Studio** , cliquez sur **OK** pour confirmer la configuration minimale requise pour redémarrer SQL Server.

6.  Dans l' **Explorateur d’objets**, avec le bouton droit de votre serveur, puis cliquez sur **redémarrer**. (Si l’Agent SQL Server est en cours d’exécution, elle doit également être redémarré.)

    ![Redémarrez][9]

7.  Dans la boîte de dialogue **SQL Server Management Studio** , cliquez sur **Oui** pour accepter que vous souhaitez redémarrer SQL Server.

##<a name="Logins"></a>Créer des connexions de l’authentification SQL Server

Pour vous connecter au moteur de base de données à partir d’un autre ordinateur, vous devez créer au moins une connexion de l’authentification SQL Server.  

Vous pouvez créer des connexions SQL Server nouvelle par programme ou en utilisant SQL Server Management Studio. Pour créer un nouvel utilisateur avec l’authentification SQL par programme, démarrez une **Nouvelle requête** et exécutez le script suivant. Remplacez < nouveau nom d’utilisateur\> et < nouveau mot de passe\> avec un choix de *nom d’utilisateur* et *mot de passe*. 


    USE master
    go

    CREATE LOGIN <new user name> WITH PASSWORD = N'<new password>',
        CHECK_POLICY = OFF,
        CHECK_EXPIRATION = OFF;

    EXEC sp_addsrvrolemember @loginame = N'<new user name>', @rolename = N'sysadmin';


Réglez la stratégie de mot de passe (le code exemple désactive les expiration de mot de passe et de vérification de stratégie). Pour plus d’informations sur les connexions SQL Server, voir [créer une connexion](http://msdn.microsoft.com/library/aa337562.aspx).  

Pour créer de nouvelles connexions SQL Server à l’aide de SQL Server Management Studio :

1.  Dans l' **Explorateur d’objets SQL Server Management Studio**, développez le dossier de l’instance du serveur dans lequel vous voulez créer la nouvelle connexion.

2.  Cliquez sur le dossier **sécurité** , pointez sur **Nouveau**, puis sélectionnez **connexion...**.

    ![Nouvelle connexion][10]

3.  Dans la boîte de dialogue **nouvelle connexion** , dans la page **Général** , entrez le nom du nouvel utilisateur dans la zone **nom de connexion** .

4.  Sélectionnez **l’authentification SQL Server**.

5.  Dans la zone **mot de passe** , entrez un mot de passe pour le nouvel utilisateur. Entrez à nouveau ce mot de passe dans la zone **Confirmer le mot de passe** .

6.  Pour appliquer les options de stratégie de mot de passe pour la complexité et l’application, sélectionnez la **stratégie de mot de passe appliquer** (recommandé). Il s’agit d’une option par défaut lors de l’authentification SQL Server est sélectionnée.

7.  Pour appliquer les options de stratégie de mot de passe pour l’expiration, sélectionnez **appliquer l’expiration du mot de passe** (recommandé). Appliquer la stratégie de mot de passe doit être activée pour activer cette case à cocher. Il s’agit d’une option par défaut lors de l’authentification SQL Server est sélectionnée.

8.  Pour obliger l’utilisateur à créer un nouveau mot de passe après la première utilisation de la connexion, sélectionnez l' **utilisateur doit changer le mot de passe à la prochaine connexion** (recommandé si cette connexion est destiné à quelqu'un d’autre à utiliser. Si la connexion est pour votre utilisation personnelle, ne sélectionnez pas cette option.) Appliquer expiration du mot de passe doit être activée pour que cette case à cocher. Il s’agit d’une option par défaut lors de l’authentification SQL Server est sélectionnée.

9.  Dans la liste de la **base de données par défaut** , sélectionnez une base de données par défaut pour la connexion. **maître** est la valeur par défaut pour cette option. Si vous n’avez pas encore créé une base de données utilisateur, conservez la valeur **principale**.

10. Dans la liste **langue par défaut** , conservez la valeur **par défaut** .

    ![Propriétés de la connexion][11]

11. S’il s’agit de la première connexion que vous créez, vous souhaiterez peut-être désigner cette connexion en tant qu’un administrateur de SQL Server. Si c’est le cas, dans la page **Rôles de serveur** , cochez **sysadmin**.

    > [AZURE.IMPORTANT] Les membres du rôle serveur fixe sysadmin contrôlez entièrement le moteur de base de données. Pour des raisons de sécurité, vous devez avec soin restreindre l’appartenance à ce rôle.

    ![sysadmin][12]

12. Cliquez sur OK.

##<a name="DNS"></a>Déterminer le nom DNS de la machine virtuelle

Pour vous connecter pour le moteur de base de données SQL Server à partir d’un autre ordinateur, vous devez connaître le nom du système DNS (Domain Name) de la machine virtuelle.

(Il s’agit du nom qu'internet utilise pour identifier la machine virtuelle. Vous pouvez utiliser l’adresse IP, mais l’adresse IP peut changer lorsque Azure déplace ressources pour redondance ou de maintenance. Le nom DNS sera stable, car elle peut être redirigé vers une nouvelle adresse IP.)

1.  Dans le portail classique Azure (ou à partir de l’étape précédente), sélectionnez **MACHINES virtuelles**.

2.  Dans la page **INSTANCES MACHINE virtuelle** , dans la colonne **Nom de DNS** , recherchez et copiez le nom du DNS pour la machine virtuelle qui s’affiche précédée par **http://**. (L’interface utilisateur ne peut pas afficher la totalité du nom, mais vous pouvez avec le bouton droit dessus, puis sélectionnez Copier.)

##<a name="cde"></a>Se connecter au moteur de base de données à partir d’un autre ordinateur

1.  Sur un ordinateur connecté à internet, ouvrez SQL Server Management Studio.

2.  Dans la boîte de dialogue **se connecter au serveur** ou **se connecter au moteur de base de données** , dans la zone **nom du serveur** , entrez le nom DNS de la machine virtuelle (déterminé à l’étape précédente) et un numéro de port public point de terminaison dans le format de *NomDNS, numéro de port* tel que **tutorialtestVM.cloudapp.net,57500**.

3.  Dans la zone **authentification** , sélectionnez **Authentification SQL Server**.

4.  Dans la zone **connexion** , tapez le nom d’une connexion que vous avez créé dans une tâche précédente.

5.  Dans la zone **mot de passe** , tapez le mot de passe de la connexion que vous créez dans une tâche précédente.

6.  Cliquez sur **se connecter**.

##<a name="amlconnect"></a>Se connecter au moteur de base de données à partir d’apprentissage automatique Azure

Par la suite du processus d’équipe données scientifique, vous utiliserez l' [Azure Machine d’apprentissage Studio](https://studio.azureml.net) pour créer et déployer des modèles de formation des machines. Pour l’acquisition de données à partir de vos bases de données machine virtuelle SQL Server directement dans Azure Machine apprentissage de formation ou d’évaluation, utilisez le module **Importer des données** dans une nouvelle expérience [Azure Machine apprentissage Studio](https://studio.azureml.net) . Cette rubrique vous trouverez plus d’informations via les liens de guide de processus scientifique des données d’équipe. Pour une présentation, voir [Nouveautés Azure Machine apprentissage Studio ?](machine-learning-what-is-ml-studio.md).

2.  Dans le volet **Propriétés** du [module importer des données](https://msdn.microsoft.com/library/azure/dn905997.aspx), sélectionnez la **Base de données SQL Azure** dans la liste déroulante **Source de données** .

3.  Dans la zone de texte **nom du serveur de base de données** , entrez`tcp:<DNS name of your virtual machine>,1433`

4.  Entrez le nom d’utilisateur SQL dans la zone de texte **nom du compte utilisateur Server** .

5.  Entrez votre mot de passe de l’utilisateur sql dans la zone de texte **mot de passe utilisateur Server** .

    ![Azure ML importer des données][13]

##<a name="shutdown"></a>Arrêt et libérer la machine virtuelle lorsqu’elles pas en cours d’utilisation

Machines virtuelles Azure sont facturés comme **payez uniquement ce que vous utilisez**. Pour vous assurer que vous ne soyez pas facturé lorsque vous N'utilisez pas votre machine virtuelle, il doit se trouver dans l’état **arrêté (Deallocated)** .

> [AZURE.NOTE] Arrêter la machine virtuelle à partir d’à l’intérieur (à l’aide des options d’alimentation Windows), la machine virtuelle est arrêtée mais reste affectée. Pour vous assurer que vous n’êtes pas en cours facturés, arrêtez toujours machines virtuelles à partir du [Portail classique Azure](http://manage.windowsazure.com/). Vous pouvez également arrêter la machine virtuelle via Powershell en appelant ShutdownRoleOperation avec « PostShutdownAction » égale à « StoppedDeallocated ».

Pour arrêter et libérer la machine virtuelle :

1. Connectez-vous au [Portail classique Azure](http://manage.windowsazure.com/) à l’aide de votre compte.  

2. Sélectionnez **MACHINES virtuelles** à partir de la barre de navigation gauche.

3. Dans la liste des machines virtuelles, cliquez sur le nom de votre machine virtuelle, puis accédez à la page de **tableau de bord** .

4. Dans la partie inférieure de la page, cliquez sur **Arrêter**.

![Machine virtuelle arrêt][15]

La machine virtuelle sera libérée mais pas supprimée. Vous pouvez redémarrer votre machine virtuelle à tout moment à partir du portail classique Azure.

## <a name="your-azure-sql-server-vm-is-ready-to-use-whats-next"></a>Votre machine virtuelle d’Azure SQL Server est prête à utiliser : quel est l’avenir ?

Votre machine virtuelle est maintenant prête à utiliser dans vos exercices scientifique de données. La machine virtuelle est également prête à utiliser comme un serveur IPython bloc-notes pour l’exploration et la transformation de données et d’autres tâches conjointement avec apprentissage automatique Azure et le processus de scientifique des données (TDSP) d’équipe.

Les étapes du processus de données scientifique sont mappées dans le [Processus de scientifique des données d’équipe](https://azure.microsoft.com/documentation/learning-paths/cortana-analytics-process/) et peuvent inclure les étapes qui déplacement des données dans HDInsight, processus et aperçu en vue de la formation à partir des données avec Azure Machine d’apprentissage.


[1]: ./media/machine-learning-data-science-setup-sql-server-virtual-machine/selectsqlvmimg.png
[2]: ./media/machine-learning-data-science-setup-sql-server-virtual-machine/4vm-config.png
[3]: ./media/machine-learning-data-science-setup-sql-server-virtual-machine/sqlvmports.png
[4]: ./media/machine-learning-data-science-setup-sql-server-virtual-machine/vmpostopts.png
[5]:./media/machine-learning-data-science-setup-sql-server-virtual-machine/searchssms.png
[6]: ./media/machine-learning-data-science-setup-sql-server-virtual-machine/19connect-to-server.png
[7]: ./media/machine-learning-data-science-setup-sql-server-virtual-machine/20server-properties.png
[8]: ./media/machine-learning-data-science-setup-sql-server-virtual-machine/21mixed-mode.png
[9]: ./media/machine-learning-data-science-setup-sql-server-virtual-machine/22restart2.png
[10]: ./media/machine-learning-data-science-setup-sql-server-virtual-machine/23new-login.png
[11]: ./media/machine-learning-data-science-setup-sql-server-virtual-machine/24test-login.png
[12]: ./media/machine-learning-data-science-setup-sql-server-virtual-machine/25sysadmin.png
[13]: ./media/machine-learning-data-science-setup-sql-server-virtual-machine/amlreader.png
[15]: ./media/machine-learning-data-science-setup-sql-server-virtual-machine/vmshutdown.png
 

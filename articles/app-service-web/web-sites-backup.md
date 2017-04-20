<properties 
    pageTitle="Sauvegarder votre application dans Azure" 
    description="Apprenez à créer des copies de sauvegarde de vos applications dans le Service d’application Azure." 
    services="app-service" 
    documentationCenter="" 
    authors="cephalin" 
    manager="wpickett" 
    editor="jimbe"/>

<tags 
    ms.service="app-service" 
    ms.workload="na" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="07/06/2016" 
    ms.author="cephalin"/>

# <a name="back-up-your-app-in-azure"></a>Sauvegarder votre application dans Azure


La fonctionnalité de sauvegarde et de restauration dans le [Service d’application Azure](../app-service/app-service-value-prop-what-is.md) vous permet de créer aisément des sauvegardes application manuellement ou automatiquement. Vous pouvez restaurer votre application à un état précédent, ou créer une nouvelle application basée sur une des copies de sauvegarde de votre application d’origine. 

Pour plus d’informations sur la restauration d’une application à partir de sauvegarde, voir [restaurer une application dans Azure](web-sites-restore.md).

<a name="whatsbackedup"></a>
## <a name="what-gets-backed-up"></a>Que sont sauvegardé 
Application Service peut en sauvegarder les informations suivantes :

* Configuration d’application
* Contenu du fichier
* Les bases de données SQL Azure ou les bases de données MySQL Azure (ClearDB) connectés à votre application (vous pouvez choisir celles que vous voulez inclure dans la sauvegarde)

Ces informations sont sauvegardées sur le compte de stockage Azure et le conteneur que vous spécifiez. 

> [AZURE.NOTE] Chaque sauvegarde est une copie complète en mode hors connexion de votre application, pas une mise à jour incrémentiel.

<a name="requirements"></a>
## <a name="requirements-and-restrictions"></a>Configuration requise et restrictions

* La fonctionnalité de sauvegarde et restauration nécessite le plan de services d’application dans la couche **Standard** ou une valeur supérieure. Pour plus d’informations sur la mise à l’échelle de votre plan de services d’application à utiliser un niveau supérieur, consultez [mettre à l’échelle d’une application dans Azure](web-sites-scale.md). Notez que niveau **Premium** permet un plus grand nombre de sauvegardes quotidiennes que niveau **Standard** .
* Vous avez besoin d’un compte de stockage Azure et un conteneur dans le même abonnement en tant que l’application que vous souhaitez sauvegarder. Pour plus d’informations sur les comptes de stockage Azure, consultez les [liens](#moreaboutstorage) à la fin de cet article.
* Sauvegardes peuvent être pouvant atteindre 10 Go contenus d’application et base de données. Vous obtenez une erreur si la taille de la sauvegarde dépasse cette limite. 

<a name="manualbackup"></a>
## <a name="create-a-manual-backup"></a>Créer une sauvegarde manuelle

2. Dans le [Portail Azure](https://portal.azure.com), accédez à la carte de votre application, sélectionnez **paramètres**, puis **des sauvegardes**. La carte de **sauvegardes** s’affiche.
    
    ![Page de sauvegardes][ChooseBackupsPage]

    >[AZURE.NOTE]Si vous voyez le message ci-dessous, cliquez dessus pour mettre à niveau votre plan de services d’application avant de procéder à des sauvegardes.
Pour plus d’informations, voir [mettre à l’échelle d’une application dans Azure](web-sites-scale.md) .  
    >![Sélectionnez le compte de stockage](./media/web-sites-backup/01UpgradePlan.png)

3. Dans la carte **des sauvegardes** , cliquez sur **stockage : ne pas configuré** pour configurer un compte de stockage.

    ![Sélectionnez le compte de stockage][ChooseStorageAccount]
    
4. Choisissez la destination de la sauvegarde en sélectionnant un **Compte de stockage** et le **conteneur**. Le compte de stockage doit appartenir à la même abonnement en tant que l’application que vous souhaitez sauvegarder. Si vous le souhaitez, vous pouvez créer un nouveau compte de stockage ou un nouveau conteneur dans les cartes correspondantes. Lorsque vous avez terminé, cliquez sur **Sélectionner**.
    
    ![Sélectionnez le compte de stockage](./media/web-sites-backup/02ChooseStorageAccount1.png)
    
5. Dans la carte de **Configurer les paramètres de sauvegarde** reste encore ouvert, cliquez sur **Paramètres de base de données**, puis sélectionnez les bases de données que vous voulez inclure dans les sauvegardes (base de données SQL ou MySQL), puis cliquez sur **OK**.  

    ![Sélectionnez le compte de stockage](./media/web-sites-backup/03ConfigureDatabase.png)

    > [AZURE.NOTE]  Pour une base de données apparaissent dans cette liste, sa chaîne de connexion doit exister dans la section **chaînes de connexion** de la cuillère de **paramètres de l’Application** de votre application.

6. Dans la carte de **Configurer les paramètres de sauvegarde** , cliquez sur **Enregistrer**.  

7. Dans la barre de commandes de la cuillère **des sauvegardes** , cliquez sur **Sauvegarder maintenant**.
    
    ![Bouton BackUpNow][BackUpNow]
    
    Vous verrez un message de progression pendant le processus de sauvegarde.

Une fois que vous avez configuré un compte de stockage et le conteneur des sauvegardes, vous pouvez modifier manuellement sauvegarde à tout moment.  

<a name="automatedbackups"></a>
## <a name="configure-automated-backups"></a>Configurer des sauvegardes automatisées

1. Dans la carte **des sauvegardes** , cliquez sur **planification : ne pas configuré**. 

    ![Sélectionnez le compte de stockage](./media/web-sites-backup/05ScheduleBackup.png)
    
1. Sur la carte de **Paramètres de planification de sauvegarde** , définissez **Sauvegarde planifiée** **sur**, puis configurer la planification de sauvegarde comme vous le souhaitez, puis cliquez sur **OK**.
    
    ![Activer des sauvegardes automatisées][SetAutomatedBackupOn]
    
4. Dans la carte de **Configurer les paramètres de sauvegarde** reste encore ouvert, cliquez sur **Paramètres de stockage**, puis choisissez la destination de la sauvegarde en sélectionnant un **Compte de stockage** et le **conteneur**. Le compte de stockage doit appartenir à la même abonnement en tant que l’application que vous souhaitez sauvegarder. Si vous le souhaitez, vous pouvez créer un nouveau compte de stockage ou un nouveau conteneur dans les cartes correspondantes. Lorsque vous avez terminé, cliquez sur **Sélectionner**.
    
    ![Sélectionnez le compte de stockage](./media/web-sites-backup/02ChooseStorageAccount1.png)
    
5. Dans la carte de **Configurer les paramètres de sauvegarde** , cliquez sur **Paramètres de base de données**, puis sélectionnez les bases de données que vous voulez inclure dans les sauvegardes (base de données SQL ou MySQL), puis cliquez sur **OK**.  

    ![Sélectionnez le compte de stockage](./media/web-sites-backup/03ConfigureDatabase.png)

    > [AZURE.NOTE]  Pour une base de données apparaissent dans cette liste, sa chaîne de connexion doit exister dans la section **chaînes de connexion** de la cuillère de **paramètres de l’Application** de votre application.

6. Dans la carte de **Configurer les paramètres de sauvegarde** , cliquez sur **Enregistrer**.  

<a name="partialbackups"></a>
## <a name="backup-just-part-of-your-app"></a>Sauvegarde seulement une partie de votre application

Parfois, vous ne voulez sauvegarder tout le contenu de votre application. Voici quelques exemples :

-   Vous [configurez des sauvegardes hebdomadaires](web-sites-backup.md#configure-automated-backups) de votre application qui contient du contenu statique qui ne change jamais, tels que des billets de blog ancien ou des images.
-   Votre application comporte plus de 10 Go de contenu (c’est la durée maximale, que vous pouvez sauvegarder à la fois).
-   Vous ne voulez pas sauvegarder les fichiers journaux.

Sauvegardes partielles vous permet de choisir exactement les fichiers que vous souhaitez sauvegarder.

### <a name="exclude-files-from-your-backup"></a>Exclure des fichiers à partir de votre sauvegarde

Pour exclure des fichiers et dossiers à partir des sauvegardes, créez un `_backup.filter` fichier dans le dossier D:\home\site\wwwroot de votre application et spécifiez la liste des fichiers et dossiers que vous voulez exclure dans cet emplacement. Un moyen facile pour accéder à ce consiste à utiliser la [Console Kudu](https://github.com/projectkudu/kudu/wiki/Kudu-console). 

Supposons que vous avez une application qui contient les fichiers journaux et les images statiques à partir de ces dernières années qui ne sont jamais accédant à modifier. Vous disposez déjà d’une sauvegarde complète de l’application qui inclut les images anciens. Voulez-vous sauvegarder l’application quotidiennement, mais vous ne voulez pas payer pour le stockage de fichiers journaux ou les fichiers image statique qui ne changent jamais.

![Dossier des journaux][LogsFolder]
![dossier des Images][ImagesFolder]
    
Les étapes ci-dessous afficher comment vous voulez exclure ces fichiers de la sauvegarde.

1. Accédez à `http://{yourapp}.scm.azurewebsites.net/DebugConsole` et identifiez les dossiers que vous voulez exclure de vos sauvegardes. Dans cet exemple, vous pouvez exclure les fichiers et dossiers figurant dans cette interface utilisateur suivants :

        D:\home\site\wwwroot\Logs
        D:\home\LogFiles
        D:\home\site\wwwroot\Images\2013
        D:\home\site\wwwroot\Images\2014
        D:\home\site\wwwroot\Images\brand.png

    [AZURE.NOTE] La dernière ligne indique que vous pouvez exclure les fichiers individus ainsi que des dossiers.

2. Créer un fichier nommé `_backup.filter` et placer la liste ci-dessus dans le fichier, mais supprimer `D:\home`. Liste d’un répertoire ou fichier par ligne. Afin que le contenu du fichier doit être :

    \site\wwwroot\Logs \LogFiles \site\wwwroot\Images\2013 \site\wwwroot\Images\2014 \site\wwwroot\Images\brand.png

3. Téléchargez ce fichier à la `D:\home\site\wwwroot\` répertoire de votre site à l’aide de [ftp](web-sites-deploy.md#ftp) ou toute autre méthode. Si vous le souhaitez, vous pouvez créer le fichier directement dans `http://{yourapp}.scm.azurewebsites.net/DebugConsole` et insérer le contenu il.

4. Exécuter des sauvegardes la même façon que vous le feriez normalement, [manuellement](#create-a-manual-backup) ou [automatiquement](#configure-automated-backups).

Maintenant, tous les fichiers et dossiers qui sont spécifiés dans `_backup.filter` seront exclus de la sauvegarde. Dans cet exemple, les fichiers journaux et les fichiers image 2013 et 2014 seront n’est plus sauvegardés, ainsi que brand.png.

>[AZURE.NOTE] Vous restaurez des sauvegardes partielles de votre site de la même façon que vous le feriez pour [restaurer une sauvegarde régulière](web-sites-restore.md). Le processus de restauration fera l’attitude.
>
>Lorsqu’une sauvegarde complète est restaurée, tout le contenu sur le site est remplacé par se trouvant dans la sauvegarde. Si un fichier se trouve sur le site, mais pas dans la sauvegarde, il est supprimé. Mais lorsqu’une sauvegarde partielle est restaurée, tout le contenu se trouve dans l’un des répertoires bloqués ou tous les fichiers bloqués, il reste en l’état.

<a name="aboutbackups"></a>

## <a name="how-backups-are-stored"></a>Mode de stockage des sauvegardes

Une fois que vous avez apporté une ou plusieurs copies de sauvegarde de votre application, les sauvegardes seront visibles sur la carte de **conteneurs** de votre compte de stockage, ainsi que votre application. Dans le compte de stockage, chaque sauvegarde consiste en un fichier .zip qui contient les données de sauvegarde et un fichier .xml qui contient un manifeste du contenu du fichier .zip. Vous pouvez décompresser le fichier et parcourir ces fichiers si vous voulez accéder à vos sauvegardes sans réellement effectuer une restauration de l’application.

La sauvegarde de base de données pour l’application est stockée dans la racine du fichier .zip. Pour une base de données SQL, ceci est un fichier à DOS (sans extension de fichier) et peut être importé. Pour créer une nouvelle base de données SQL en fonction de l’exportation à DOS, voir [Importer un fichier à DOS pour créer une nouvelle base de données utilisateur](http://technet.microsoft.com/library/hh710052.aspx).

> [AZURE.WARNING] Modification des fichiers dans le conteneur **websitebackups** peut engendrer la sauvegarde deviennent non valides et par conséquent non-peuvent être restaurées.

<a name="nextsteps"></a>
## <a name="next-steps"></a>Étapes suivantes
Pour plus d’informations sur la restauration d’une application à partir d’une sauvegarde, voir [restaurer une application dans Azure](web-sites-restore.md). Vous pouvez également sauvegarder et restaurer des applications de Service d’application à l’aide de l’API REST (voir [Utilisation reste pour sauvegarder et restaurer des applications de Service d’application](websites-csm-backup.md)).

>[AZURE.NOTE] Si vous voulez commencer à utiliser le Service d’application Azure avant de vous inscrire pour un compte Azure, accédez à [Essayer le Service application](http://go.microsoft.com/fwlink/?LinkId=523751), où vous pouvez créer une application web starter courte immédiatement dans le Service d’application. Aucune carte de crédit obligatoire ; Aucune engagements.


<!-- IMAGES -->
[ChooseBackupsPage]: ./media/web-sites-backup/01ChooseBackupsPage.png
[ChooseStorageAccount]: ./media/web-sites-backup/02ChooseStorageAccount.png
[IncludedDatabases]: ./media/web-sites-backup/03IncludedDatabases.png
[BackUpNow]: ./media/web-sites-backup/04BackUpNow.png
[BackupProgress]: ./media/web-sites-backup/05BackupProgress.png
[SetAutomatedBackupOn]: ./media/web-sites-backup/06SetAutomatedBackupOn.png
[Frequency]: ./media/web-sites-backup/07Frequency.png
[StartDate]: ./media/web-sites-backup/08StartDate.png
[StartTime]: ./media/web-sites-backup/09StartTime.png
[SaveIcon]: ./media/web-sites-backup/10SaveIcon.png
[ImagesFolder]: ./media/web-sites-backup/11Images.png
[LogsFolder]: ./media/web-sites-backup/12Logs.png
[GhostUpgradeWarning]: ./media/web-sites-backup/13GhostUpgradeWarning.png
 

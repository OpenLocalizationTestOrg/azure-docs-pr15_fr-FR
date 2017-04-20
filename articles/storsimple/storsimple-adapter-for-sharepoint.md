<properties 
   pageTitle="Carte StorSimple pour SharePoint | Microsoft Azure"
   description="Décrit comment installer et configurer ou supprimer la carte StorSimple de SharePoint dans une batterie de serveurs SharePoint."
   services="storsimple"
   documentationCenter="NA"
   authors="SharS"
   manager="carmonm"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD"
   ms.date="07/11/2016"
   ms.author="v-sharos" />

# <a name="install-and-configure-the-storsimple-adapter-for-sharepoint"></a>Installer et configurer la carte StorSimple pour SharePoint

## <a name="overview"></a>Vue d’ensemble

La carte StorSimple pour SharePoint est un composant qui vous permet de fournir un stockage flexible Microsoft Azure StorSimple et protection des données aux batteries de serveurs SharePoint. Vous pouvez utiliser la carte pour déplacer le contenu de l’objet binaire volumineux (BLOB) des bases de données de contenu SQL Server à l’appareil de stockage cloud Microsoft Azure StorSimple hybride.

La carte StorSimple pour SharePoint fonctionne comme un fournisseur de stockage d’objets BLOB distants (RBS) et utilise la fonctionnalité de stockage BLOB distant de SQL Server pour stocker le contenu SharePoint non structuré (sous la forme d’objets BLOB) sur un serveur de fichiers stocké par un appareil StorSimple.

>[AZURE.NOTE] La carte StorSimple pour SharePoint prend en charge le stockage BLOB SharePoint Server 2010 à distance (RBS). Il ne prend pas en charge stockage BLOB de SharePoint Server 2010 externe (EBS).

- Pour télécharger la carte StorSimple pour SharePoint, accédez à [StorSimple carte pour SharePoint] [ 1] dans du Microsoft Download Center.

- Pour plus d’informations sur la planification des RBS et RBS limitations, accédez au [choix du nombre à utiliser le champ RBS dans SharePoint 2013] [ 2] ou [planifier RBS (SharePoint Server 2010)][3].

Le reste de cette présentation décrit brièvement le rôle de la carte StorSimple pour SharePoint et les limites de capacité et les performances de SharePoint que vous devez connaître avant d’installer et configurer la carte. Après avoir examiné ces informations, accédez à [StorSimple carte pour l’installation SharePoint](#storsimple-adapter-for-sharepoint-installation) de commencer la configuration de la carte.

### <a name="storsimple-adapter-for-sharepoint-benefits"></a>Carte StorSimple aux avantages de SharePoint

Dans un site SharePoint, le contenu est stocké en tant que données BLOB non structurées dans une ou plusieurs bases de données de contenu. Par défaut, ces bases de données sont hébergés sur les ordinateurs qui exécutent SQL Server et qui se trouvent dans la batterie de serveurs SharePoint. Objets BLOB peuvent augmenter rapidement la taille, utilisent une grande quantité de stockage local. Pour cette raison, vous souhaiterez peut-être trouver stockage économique, une autre solution. SQL Server fournit une technologie appelée à distance Blob Storage (RBS) qui vous permet de stocker le contenu d’objets BLOB dans le système de fichiers, en dehors de la base de données SQL Server. Avec RBS, des objets BLOB peuvent résider dans le système de fichiers sur l’ordinateur qui exécute SQL Server ou qu’elles puissent être stockées dans le système de fichiers sur un autre ordinateur serveur.

RBS requiert que vous utilisiez un fournisseur RBS, telles que la carte StorSimple pour SharePoint, pour activer RBS dans SharePoint. La carte StorSimple pour SharePoint fonctionne avec RBS, ce qui vous permet de déplacer des objets BLOB sur un serveur sauvegardé par le système Microsoft Azure StorSimple. Microsoft Azure StorSimple stocke les données BLOB localement ou dans le cloud, en fonction de l’utilisation. Objets BLOB qui sont très actives (généralement appelée sous forme de niveau 1 ou hot) résident localement. Données moins actifs et les données d’archivage résident dans le cloud. Après avoir activé RBS sur une base de données de contenu, tout nouveau contenu BLOB créé dans SharePoint est stockée sur le périphérique StorSimple et non dans la base de données de contenu.

L’implémentation Microsoft Azure StorSimple de RBS offre les avantages suivants :

- En déplaçant le contenu BLOB sur un serveur distinct, vous pouvez réduire la charge de requête sur SQL Server, qui permet d’améliorer la réactivité de SQL Server. 

- StorSimple Azure utilise déduplication et la compression pour réduire la taille des données.

- Azure StorSimple protège les données dans le formulaire de locale et des instantanés cloud. En outre, si vous placez la base de données sur le périphérique StorSimple, vous pouvez sauvegarder la base de données de contenu et des objets BLOB ensemble de façon cohérente incident. (Déplacement de la base de données de contenu à l’appareil est uniquement prise en charge pour le périphérique série 8000 StorSimple. Cette fonctionnalité n'est pas pris en charge pour la série 5000 ou 7000.)

- Azure StorSimple intègre des fonctionnalités de récupération d’urgence, y compris le basculement, de récupération de fichier et le volume (y compris le test de restauration) et la restauration rapide des données.

- Vous pouvez utiliser le logiciel de récupération de données, tels que Kroll Ontrack PowerControls, avec des instantanés StorSimple des données BLOB pour effectuer la récupération au niveau des éléments de contenu SharePoint. (Ce logiciel de récupération de données est un achat distinct).

- La carte StorSimple pour SharePoint se branche sur le portail d’Administration centrale de SharePoint, qui vous permet de gérer votre solution SharePoint entière à partir d’un emplacement central.

Déplacement du contenu BLOB vers le système de fichiers peut fournir d’autres économies et avantages. Par exemple, à l’aide de RBS pouvez encore réduire la nécessité de stockage coûteux de niveau 1 et, car il est réduit la base de données de contenu, RBS peut réduire le nombre de bases de données requise dans la batterie de serveurs SharePoint. Toutefois, d’autres facteurs, tels que les limites de taille de base de données et la quantité de contenu non RBS, peut affecter également les besoins de stockage. Pour plus d’informations sur les coûts et les avantages de l’utilisation de RBS, voir [planifier les RBS (SharePoint Foundation 2010)] [ 4] et [choix entre pour utiliser le champ RBS dans SharePoint 2013][5].

### <a name="capacity-and-performance-limits"></a>Limites de capacité et les performances

Avant de vous envisagez d’utiliser RBS de votre solution SharePoint, vous devez être connaître les performances testé et les limites de capacité de SharePoint Server 2010 et SharePoint Server 2013 et comment ces limites sont liés aux performances acceptables. Pour plus d’informations, voir [limites et frontières logicielles pour SharePoint 2013](https://technet.microsoft.com/library/cc262787.aspx).

Passez en revue les éléments suivants avant de configurer RBS :

- Assurez-vous que la taille totale du contenu (la taille d’une base de données contenu) plus la taille de n’importe quel des objets BLOB externalisées associées ne dépasse pas la limite de taille RBS pris en charge par SharePoint. Cette limite est de 200 Go. 

    **À mesure de base de données contenu et la taille de BLOB**

     1. Exécutez cette requête sur le WFE Administration centrale. Démarrez SharePoint Management Shell, puis entrez la commande Windows PowerShell suivante pour obtenir la taille des bases de données de contenu :

        `Get-SPContentDatabase | Select-Object -ExpandProperty DiskSizeRequired`

         Cette étape Obtient la taille de la base de données de contenu sur le disque.

     2. Exécutez une des requêtes SQL suivantes dans SQL Management Studio sur la zone serveur SQL sur chaque base de données contenu et ajouter le résultat à l’étape obtenu en nombre 1.

        Dans les bases de données de contenu SharePoint 2013, entrez :

        `SELECT SUM([Size]) FROM [ContentDatabaseName].[dbo].[DocStreams] WHERE [Content] IS NULL`

        Dans les bases de données de contenu SharePoint 2010, entrez :

        `SELECT SUM([Size]) FROM [ContentDatabaseName].[dbo].[AllDocs] WHERE [Content] IS NULL`

        Cette étape Obtient la taille des objets BLOB qui ont été externalisé.

- Nous vous conseillons de stocker tout le contenu BLOB et base de données localement sur le périphérique StorSimple. Le périphérique StorSimple est un cluster à deux nœuds de disponibilité. En plaçant les bases de données de contenu et des objets BLOB sur le périphérique StorSimple fournit une disponibilité élevée.

    Utilisez traditionnel SQL Server migration meilleures pratiques pour déplacer la base de données de contenu à l’appareil StorSimple. Déplacer la base de données qu’une fois que tout le contenu BLOB de la base de données a été déplacé vers le partage de fichiers via RBS. Si vous choisissez de déplacer la base de données de contenu à l’appareil StorSimple, nous vous recommandons de configurer le stockage de base de données de contenu sur le périphérique qu’un volume principal.

- Dans Microsoft Azure StorSimple, si vous utilisez des volumes hiérarchisés, il n’existe aucun moyen pour vous assurer que le contenu stocké localement sur la StorSimple appareil n’est pas en cascade au stockage cloud Microsoft Azure. Par conséquent, nous recommandons d’utiliser des volumes StorSimple localement épinglée conjointement avec SharePoint RBS. Ainsi assuré que tout le contenu BLOB reste localement sur le périphérique StorSimple et se déplace pas vers Microsoft Azure.

- Si vous ne stockez pas les bases de données de contenu sur le périphérique StorSimple, utilisez traditionnel SQL Server haute disponibilité meilleures pratiques qui prennent en charge RBS. Cluster SQL Server prend en charge RBS, SQL Server lors de la mise en miroir est pas le cas. 

>[AZURE.WARNING] Si vous n’avez pas activé RBS, nous ne recommandons pas le déplacement de la base de données de contenu à l’appareil StorSimple. Il s’agit d’une configuration non testée.
 
## <a name="storsimple-adapter-for-sharepoint-installation"></a>Carte StorSimple pour l’installation SharePoint

Avant d’installer la carte StorSimple pour SharePoint, vous devez configurer le périphérique StorSimple et assurez-vous que la batterie de serveurs SharePoint et instanciation SQL Server répondent à toutes les conditions préalables. Ce didacticiel décrit la configuration requise, ainsi que les procédures d’installation et la mise à niveau de la carte StorSimple pour SharePoint. 

## <a name="configure-prerequisites"></a>Configurer les conditions préalables

Avant d’installer la carte StorSimple pour SharePoint, assurez-vous que l’appareil StorSimple, batterie de serveurs SharePoint et instanciation SQL Server répondent aux conditions préalables suivantes.

### <a name="system-requirements"></a>Configuration système requise

La carte StorSimple pour SharePoint fonctionne avec du matériel et les logiciels suivants :

- Prise en charge du système d’exploitation Windows Server 2008 R2 SP1, Windows Server 2012 ou Windows Server 2012 R2 

- Versions de SharePoint prises en charge – SharePoint Server 2010 ou SharePoint Server 2013

- Versions de SQL Server pris en charge – SQL Server 2008 Enterprise Edition, SQL Server 2008 R2 Enterprise Edition ou SQL Server 2012 Enterprise Edition

- Prise en charge des appareils StorSimple – StorSimple 8000, série 7000 StorSimple ou série 5000 StorSimple.

### <a name="storsimple-device-configuration-prerequisites"></a>Conditions préalables pour les StorSimple appareil configuration

Le périphérique StorSimple est un périphérique de bloc et en tant que tel nécessite un serveur de fichiers sur lequel les données peuvent être hébergées. Nous vous recommandons d’utiliser un serveur différent au lieu d’un serveur existant à partir de la batterie de serveurs SharePoint. Ce serveur de fichiers doit se trouver sur le même réseau local (LAN) en tant que l’ordinateur SQL Server qui héberge les bases de données de contenu. 

>[AZURE.TIP] 
>
>- Si vous configurez votre batterie de serveurs SharePoint de disponibilité, vous devez déployer le serveur de fichiers de disponibilité également.
>
>- Si vous ne pas stockez la base de données de contenu sur le périphérique StorSimple, utilisez disponibilité traditionnel meilleures pratiques qui prennent en charge RBS. Cluster SQL Server prend en charge RBS, SQL Server lors de la mise en miroir est pas le cas. 

Vérifiez que votre appareil StorSimple est correctement configuré et que les volumes appropriés pour prendre en charge votre déploiement de SharePoint sont configurés et accessibles à partir de votre ordinateur SQL Server. Accédez à [déployer votre appareil StorSimple local](storsimple-deployment-walkthrough.md) si vous n’avez pas encore déployé et configuré votre appareil StorSimple. Notez l’adresse IP du périphérique StorSimple ; vous en aurez besoin pendant StorSimple carte pour l’installation SharePoint. 

En outre, vérifiez que le volume à utiliser pour externalisation BLOB remplit les conditions suivantes :

- Le volume doit être mis en forme avec une taille d’unité d’allocation 64 Ko.

- Votre site web frontal (WFE) et les serveurs d’applications doivent être en mesure d’accéder au volume via un chemin d’accès de Convention d’affectation de noms (UNC). 

- La batterie de serveurs SharePoint doit être configurée pour écrire sur le volume.

>[AZURE.NOTE] Après avoir installé et configuré la carte, tous les externalisation BLOB doit passer par le périphérique StorSimple (le périphérique présente les volumes à SQL Server et gérer les niveaux de stockage). Vous ne pouvez pas utiliser d’autres cibles pour externalisation BLOB.
 
Si vous envisagez d’utiliser le Gestionnaire d’instantané StorSimple pour prendre des instantanés des données BLOB et base de données, veillez à installer le Gestionnaire d’instantanés StorSimple sur le serveur de base de données afin qu’il puisse utiliser le Service SQL Writer pour implémenter Windows Volume Shadow Copy Service (VSS). 

>[AZURE.IMPORTANT] Gestionnaire d’instantanés StorSimple ne prend pas en charge le Writer SharePoint et ne peuvent pas cohérentes avec les applications instantanés des données de SharePoint. Dans un scénario de SharePoint, gestionnaire d’instantanés StorSimple fournit uniquement des sauvegardes blocage cohérentes. 
 
## <a name="sharepoint-farm-configuration-prerequisites"></a>Conditions préalables configuration de batterie de serveurs SharePoint

Vérifiez que votre batterie de serveurs SharePoint est correctement configuré, comme suit :

- Vérifiez que votre batterie de serveurs SharePoint est dans un état correct, puis vérifiez les points suivants : 

- Tous les SharePoint WFE et enregistrés dans la batterie de serveurs d’applications en cours d’exécution et peuvent être exécutée sur à partir du serveur sur lequel vous installerez la carte StorSimple pour SharePoint.

- Le service du minuteur SharePoint (SPTimerV3 ou SPTimerV4) est en cours d’exécution sur chaque WFE et application server.

- Le service du minuteur SharePoint et le pool d’applications IIS sous lequel s’exécute le site Administration centrale de SharePoint ont des privilèges d’administrateur. 

- Assurez-vous qu’Internet Explorer amélioré contexte de sécurité (renforcée) est désactivé. Suivez ces étapes pour désactiver renforcée d’Internet Explorer :

    1. Fermez toutes les instances d’Internet Explorer.

    2. Démarrer le Gestionnaire de serveur.

    3. Dans le volet gauche, cliquez sur **Le serveur Local**.

    4. Dans le volet droit, en regard de **La Configuration de sécurité renforcée d’Internet Explorer**, cliquez **sur**.

    5. Sous **les administrateurs**, cliquez sur **désactiver**.

    6. Cliquez sur **OK**.

## <a name="remote-blob-storage-rbs-prerequisites"></a>Conditions préalables stockage BLOB (RBS) à distance

Vérifiez que vous utilisez une version prise en charge de SQL Server. Uniquement les versions suivantes sont prises en charge et en mesure d’utiliser le champ RBS :

- SQL Server 2008 Édition entreprise

- SQL Server 2008 R2 Enterprise Edition

- SQL Server 2012 Enterprise Edition

Objets BLOB peuvent être externalisé sur uniquement les volumes qui présente le périphérique StorSimple vers SQL Server. Aucune autre cible pour externalisation BLOB n’est prises en charge.

Lorsque vous avez terminé toutes les étapes de configuration du logiciel requis, accédez à [installer la carte StorSimple pour SharePoint](#install-the-storsimple-adapter-for-sharepoint).

## <a name="install-the-storsimple-adapter-for-sharepoint"></a>Installation de la carte StorSimple pour SharePoint

Utilisez les étapes suivantes pour installer la carte StorSimple pour SharePoint. Si vous réinstallez le logiciel, voir [mise à niveau ou réinstaller la carte StorSimple pour SharePoint](#upgrade-or-reinstall-the-storsimple-adapter-for-sharepoint). Le temps nécessaire pour l’installation varie selon le nombre total de bases de données SharePoint dans votre batterie de serveurs SharePoint.

[AZURE.INCLUDE [storsimple-install-sharepoint-adapter](../../includes/storsimple-install-sharepoint-adapter.md)]


## <a name="configure-rbs"></a>Configurer la RBS

Après avoir installé la carte StorSimple pour SharePoint, configurez RBS comme décrit dans la procédure suivante :

>[AZURE.TIP] La carte StorSimple pour SharePoint se branche sur la page Administration centrale de SharePoint, ce qui permet de RBS soit activée ou désactivée sur chaque base de données contenu dans la batterie de serveurs SharePoint. Toutefois, l’activation ou désactivation RBS sur la base de données contenu entraîne une réinitialisation IIS, qui, selon la configuration de votre batterie de serveurs, peut interrompre momentanément la disponibilité de la SharePoint web frontal (WFE). (Certains facteurs tels que l’utilisation d’un équilibrage de charge de front, la charge du serveur en cours et ainsi de suite, peuvent limiter ou supprimer cette interruption). Pour empêcher les utilisateurs une interruption, nous vous recommandons d’activer ou de désactiver la RBS uniquement pendant une période de maintenance planifiée.

[AZURE.INCLUDE [storsimple-sharepoint-adapter-configure-rbs](../../includes/storsimple-sharepoint-adapter-configure-rbs.md)]
 

## <a name="configure-garbage-collection"></a>Configurer le nettoyage

Lorsque des objets sont supprimés à partir d’un site SharePoint, ils ne sont pas automatiquement supprimés à partir du volume store RBS. En revanche, un asynchrone, programme de maintenance arrière-plan supprime des objets BLOB orphelins dans le stockage de fichiers. Les administrateurs système peuvent planifier ce processus pour une exécution périodique ou qu’ils puissent commencer il dès que nécessaire.

Ce programme de maintenance (Microsoft.Data.SqlRemoteBlobs.Maintainer.exe) est automatiquement installé sur tous les serveurs SharePoint WFE et application lorsque vous activez RBS. Le programme est installé à l’emplacement suivant : *lecteur de démarrage*: \Program Files\Microsoft stockage Blob distant SQL 10.50\Maintainer\

Pour plus d’informations sur la configuration et l’utilisation du programme de maintenance, voir [Mettre à jour RBS dans SharePoint Server 2013][8].

>[AZURE.IMPORTANT] Le programme d’intendant RBS est beaucoup de ressources. Vous devez planifier qu’elle s’exécute uniquement pendant les périodes d’activité légère dans la batterie de serveurs SharePoint.

### <a name="delete-orphaned-blobs-immediately"></a>Supprimer des objets BLOB orphelins immédiatement

Si vous avez besoin de supprimer des objets BLOB orphelins immédiatement, vous pouvez utiliser les instructions ci-après. Notez que ces instructions sont illustre comment vous pouvez faire dans un environnement SharePoint 2013 avec les composants suivants :

- Le nom de la base de données contenu est WSS_Content.
- Le nom de SQL Server est SHRPT13 SQL12\SHRPT13.
- Le nom de l’application web est SharePoint – 80.

[AZURE.INCLUDE [storsimple-sharepoint-adapter-garbage-collection](../../includes/storsimple-sharepoint-adapter-garbage-collection.md)]


## <a name="upgrade-or-reinstall-the-storsimple-adapter-for-sharepoint"></a>Mettre à jour ou réinstaller la carte StorSimple pour SharePoint

Utilisez la procédure suivante pour mettre à niveau de SharePoint server, puis réinstallez StorSimple carte pour SharePoint ou pour simplement mettre à niveau ou réinstaller la carte dans une batterie de serveurs SharePoint existante. 

>[AZURE.IMPORTANT] Passez en revue les informations suivantes avant d’essayer de mettre à niveau votre logiciel SharePoint et/ou la mise à niveau ou réinstaller la carte StorSimple pour SharePoint :
>
>- Tous les fichiers qui ont été précédemment déplacés vers le stockage externe via RBS ne sera pas disponibles jusqu'à ce que vous avez terminée la réinstallation et la fonctionnalité RBS est activée à nouveau. Pour limiter l’impact sur l’utilisateur, effectuez une mise à niveau ou la réinstallation pendant une période de maintenance planifiée.
>
>- Le temps nécessaire pour la mise à niveau/réinstallation peut varier selon le nombre total de bases de données SharePoint dans la batterie de serveurs SharePoint.
>
>- Après que la mise à niveau/réinstallation est terminée, vous devez activer RBS pour les bases de données de contenu. Pour plus d’informations, consultez [Configurer RBS](#configure-rbs) .
>
>- Si vous configurez RBS pour une batterie de serveurs SharePoint qui compte un grand nombre de bases de données (plus de 200), la page **Administration centrale de SharePoint** peut-être délai d’expiration. Ce cas, actualisez la page. Cela n’affecte pas le processus de configuration.

[AZURE.INCLUDE [storsimple-upgrade-sharepoint-adapter](../../includes/storsimple-upgrade-sharepoint-adapter.md)]
 
## <a name="storsimple-adapter-for-sharepoint-removal"></a>Carte StorSimple pour la suppression de SharePoint

Les procédures suivantes décrivent comment déplacer les objets BLOB revenir aux bases de données de contenu SQL Server et puis désinstaller la carte StorSimple pour SharePoint. 

>[AZURE.IMPORTANT] Vous devez replacer les objets BLOB dans les bases de données contenus avant de désinstaller le logiciel de la carte. 

### <a name="before-you-begin"></a>Avant de commencer 

Collecter les informations suivantes avant de vous replacez les données pour les bases de données de contenu de SQL Server et commencez le processus de suppression de carte :

- Les noms de toutes les bases de données pour lesquelles RBS est activé
- Le chemin UNC de la banque d’objets BLOB configuré

### <a name="move-the-blobs-back-to-the-content-databases"></a>Déplacez les objets BLOB vers les bases de données de contenu

Avant de désinstaller la carte StorSimple pour logiciel SharePoint, vous devez migrer tous les objets BLOB qui ont été externalisé revenir vers les bases de données de contenu de SQL Server. Si vous essayez de désinstaller la carte StorSimple pour SharePoint avant de vous déplacez tous les objets BLOB pour les bases de données de contenu, vous verrez le message d’avertissement suivant.

![Message d’avertissement](./media/storsimple-adapter-for-sharepoint/sasp1.png)
 
####<a name="to-move-the-blobs-back-to-the-content-databases"></a>Pour déplacer les objets BLOB vers les bases de données de contenu 

1. Chacun des objets externalisées télécharger.

2. Ouvrez la page **Administration centrale de SharePoint** et accédez à **Paramètres système**. 

3. Sous **StorSimple Azure**, cliquez sur **Configuration de la carte StorSimple**.

4. Dans la page **Configuration de la carte StorSimple** , cliquez sur le bouton **désactiver** en dessous de chacune des bases de données contenus que vous souhaitez supprimer du stockage BLOB externes. 

5. Supprimer les objets à partir de SharePoint et les télécharger à nouveau.

Vous pouvez également utiliser Microsoft` RBS Migrate()` applet de commande PowerShell inclus dans SharePoint. Pour plus d’informations, voir [migrer le contenu ou disparition RBS](https://technet.microsoft.com/library/ff628255.aspx).

Une fois que vous déplacez les objets BLOB vers la base de données de contenu, accédez à l’étape suivante : [désinstaller la carte](#uninstall-the-adapter).

### <a name="uninstall-the-adapter"></a>Désinstallez la carte

Une fois que vous déplacez les objets BLOB vers les bases de données de contenu de SQL Server, utilisez une des options suivantes pour désinstaller la carte StorSimple pour SharePoint.

#### <a name="to-use-the-installation-program-to-uninstall-the-adapter"></a>Utiliser le programme d’installation pour désinstaller la carte 

1. Utilisez un compte doté des privilèges administrateur pour ouvrir une session sur le serveur web frontal (WFE).
2. Double-cliquez sur la carte de StorSimple pour installer SharePoint. L’Assistant Installation démarre.

    ![Assistant de configuration](./media/storsimple-adapter-for-sharepoint/sasp2.png)

3. Cliquez sur **suivant**. La page suivante s’affiche.

    ![Page de suppression de l’Assistant de configuration](./media/storsimple-adapter-for-sharepoint/sasp3.png)

4. Cliquez sur **Supprimer** pour sélectionner le processus de suppression. La page suivante s’affiche.

    ![Page de confirmation de l’Assistant de configuration](./media/storsimple-adapter-for-sharepoint/sasp4.png)

5. Cliquez sur **Supprimer** pour confirmer la suppression. La page suivante de l’avancement s’affiche.

    ![Page de l’avancement de l’Assistant de configuration](./media/storsimple-adapter-for-sharepoint/sasp5.png)

6. Lorsque la suppression est terminée, la page Terminer s’affiche. Cliquez sur **Terminer** pour fermer l’Assistant Configuration.

#### <a name="to-use-the-control-panel-to-uninstall-the-adapter"></a>Utiliser le panneau de configuration pour désinstaller la carte 

1. Ouvrez le panneau de configuration, puis cliquez sur **programmes et fonctionnalités**.

2. Sélectionner la **Carte StorSimple pour SharePoint**, puis cliquez sur **désinstaller**. 

## <a name="next-steps"></a>Étapes suivantes

[En savoir plus sur StorSimple](storsimple-overview.md).

<!--Reference links-->
[1]: https://www.microsoft.com/download/details.aspx?id=44073
[2]: https://technet.microsoft.com/library/ff628583(v=office.15).aspx
[3]: https://technet.microsoft.com/library/ff628583(v=office.14).aspx
[4]: https://technet.microsoft.com/library/ff628569(v=office.14).aspx
[5]: https://technet.microsoft.com/library/ff628583(v=office.15).aspx
[8]: https://technet.microsoft.com/en-us/library/ff943565.aspx

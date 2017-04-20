<properties
    pageTitle="Configuration de la protection des données Oracle machines virtuelles | Microsoft Azure"
    description="Passez en revue un didacticiel sur la configuration et la mise en œuvre de protection des données Oracle sur Azure machines virtuelles pour haute disponibilité et récupération d’urgence."
    services="virtual-machines-windows"
    authors="rickstercdn"
    manager="timlt"
    documentationCenter=""
    tags="azure-service-management"/>
<tags
    ms.service="virtual-machines-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="vm-windows"
    ms.workload="infrastructure-services"
    ms.date="09/06/2016"
    ms.author="rclaus" />

#<a name="configuring-oracle-data-guard-for-azure"></a>Configuration de protection des données Oracle pour Azure


Ce didacticiel montre comment configurer et mise en œuvre de protection des données Oracle dans environnement Machines virtuelles Azure haute disponibilité et récupération d’urgence. Le didacticiel se concentre sur réplication à sens unique pour les bases de données non - RAC Oracle.

Protection des données Oracle prend en charge la protection des données et reprise pour la base de données Oracle. Il est une simple, High performance dans le désordre solution pour sinistre, protection des données et la disponibilité de la base de données Oracle entière.

Ce didacticiel suppose que vous avez déjà connaissances théoriques et pratiques sur les concepts de base de données Oracle haute disponibilité et récupération d’urgence. Pour plus d’informations, voir le [site web Oracle](http://www.oracle.com/technetwork/database/features/availability/index.html) et également sur les [Concepts de protection de données Oracle et Guide d’Administration](https://docs.oracle.com/cd/E11882_01/server.112/e41134/toc.htm).

En outre, le didacticiel suppose que vous avez déjà implémenté les conditions préalables suivantes :

- Vous avez déjà examiné la section disponibilité et les considérations sur la récupération urgence dans la rubrique [Machine virtuelle Oracle images - considérations divers](virtual-machines-windows-classic-oracle-considerations.md) . Azure prend en charge actuellement instances de base de données Oracle autonome, mais pas Oracle RAC (Oracle RAC).


- Vous avez créé deux Machines virtuelles (machines virtuelles) dans Azure à l’aide de la même plate-forme fournie image Oracle Enterprise Edition. Vérifiez que les ordinateurs virtuels sont dans le [même service cloud](virtual-machines-windows-load-balance.md) et dans le même réseau virtuel pour vous assurer qu’ils peuvent accéder à eux via l’adresse IP privée permanente. Par ailleurs, il est recommandé de placer les ordinateurs virtuels dans la même [disponibilité définie](virtual-machines-windows-manage-availability.md) pour autoriser Azure placez-les dans les domaines d’erreur distincte et mettre à niveau des domaines. Protection des données Oracle est uniquement disponible avec Enterprise Edition de la base de données Oracle. Chaque ordinateur doit avoir au moins de 2 Go de mémoire et 5 Go d’espace disque. Pour obtenir les informations les plus récentes sur la plate-forme fournie tailles machine virtuelle, voir [Tailles Machine virtuelle pour Azure](virtual-machines-windows-sizes.md). Si vous avez besoin de volume de disque supplémentaires pour vos ordinateurs virtuels, vous pouvez joindre des disques supplémentaires. Pour plus d’informations, voir [l’insertion d’un disque de données pour une Machine virtuelle](virtual-machines-windows-classic-attach-disk.md).



- Vous avez configuré les noms de Machine virtuelle comme « Machine1 » pour la machine virtuelle et « Ordinateur2 » de la machine virtuelle secours primaire sur le portail classique Azure.

- Vous avez défini la variable d’environnement **ORACLE_HOME** pour qu’il pointe vers le même chemin d’installation racine oracle dans principal et secondaire Machines virtuelles, tel que `C:\OracleDatabase\product\11.2.0\dbhome_1\database`.

- Vous connecter à votre serveur Windows en tant que membre du groupe **administrateurs** ou membre du groupe **ORA_DBA** .

Dans ce didacticiel, vous allez :

Mettre en œuvre l’environnement de base de données en attente physique

1. Créer une base de données principale

2. Préparer la base de données principale pour la création de base de données en attente

    1. Activer la journalisation forcée

    2. Créer un fichier de mot de passe

    3. Configurer un journal de rétablissement en attente

    4. Activer l’archivage

    5. Définir les paramètres de l’initialisation de base de données principale

Créer une base de données en attente physique

1. Préparer un fichier de paramètres de l’initialisation de base de données en attente

2. Configurer le récepteur et tnsnames pour prendre en charge de la base de données sur les ordinateurs principales et de secours

    1. Configurer listener.ora sur les deux serveurs pour contenir les entrées pour ces deux bases de données

    2. Pour mettre en attente d’entrées pour les bases de données principales et de secours, configurez tnsnames.ora sur les ordinateurs virtuels principal et de secours. 

    3. Démarrez le récepteur, puis activez tnsping sur les deux Machines virtuelles pour les deux services.

3. Démarrage de l’instance en attente dans un état sans montage

4. Utiliser RMAN pour cloner la base de données et pour créer une base de données en attente

5. Démarrer la base de données en attente physique en mode de récupération gérées

6. Vérifiez que la base de données en attente physique

> [AZURE.IMPORTANT] Ce didacticiel a été configurer et tester par rapport à la configuration matérielle et logicielle suivante :
>
>|                      | **Base de données principale**                      | **Base de données en attente**                      |
>|----------------------|-------------------------------------------|-------------------------------------------|
>| **Version Oracle**   | Version entreprise Oracle11g (11.2.0.4.0) | Version entreprise Oracle11g (11.2.0.4.0) |
>| **Nom de l’ordinateur**     | Machine1                                  | Machine2                                  |
>| **Système d'exploitation** | Windows 2008 R2                           | Windows 2008 R2                           |
>| **Oracle identificateur de sécurité**       | TEST                                      | TEST\_STBY                                |
>| **Mémoire**           | Min 2 Go                                  | Min 2 Go                                  |
>| **Espace disque**       | Min 5 Go                                  | Min 5 Go                                  |

Pour les versions ultérieures de base de données Oracle et protection des données Oracle, vous devrez éventuellement certaines modifications supplémentaires dont vous avez besoin pour mettre en œuvre. Pour les dernières informations spécifiques à la version, voir documentation de [Protection des données](http://www.oracle.com/technetwork/database/features/availability/data-guard-documentation-152848.html) et de [Base de données Oracle](http://www.oracle.com/us/corporate/features/database-12c/index.html) sur le site web Oracle.

##<a name="implement-the-physical-standby-database-environment"></a>Mettre en œuvre l’environnement de base de données en attente physique
### <a name="1-create-a-primary-database"></a>1. créer une base de données principale

- Créez une base de données principale « TEST » dans la Machine virtuelle principal. Pour plus d’informations, voir créer et configurer une base de données Oracle.
- Pour voir le nom de votre base de données, vous connecter à votre base de données en tant que l’utilisateur SYS avec rôle SYSDBA dans l’instruction SQL * Plus invite de commandes et exécutez l’instruction suivante :

        SQL> select name from v$database;

        The result will display like the following:

        NAME
        ---------
        TEST
- Ensuite, les noms des fichiers de base de données à partir de la vue système dba_data_files la requête :

        SQL> select file_name from dba_data_files;
        FILE_NAME
        -------------------------------------------------------------------------------
        C:\ <YourLocalFolder>\TEST\USERS01.DBF
        C:\ <YourLocalFolder>\TEST\UNDOTBS01.DBF
        C:\ <YourLocalFolder>\TEST\SYSAUX01.DBF
        C:\<YourLocalFolder>\TEST\SYSTEM01.DBF
        C:\<YourLocalFolder>\TEST\EXAMPLE01.DBF

### <a name="2-prepare-the-primary-database-for-standby-database-creation"></a>2. préparer la base de données principale pour la création de base de données en attente

Avant de créer une base de données en attente, il est recommandé de vérifier que la base de données principale est configuré correctement. Voici une liste des étapes que vous devez effectuer :

1. Activer la journalisation forcée
2. Créer un fichier de mot de passe
3. Configurer un journal de rétablissement en attente
4. Activer l’archivage
5. Définir les paramètres de l’initialisation de base de données principale

#### <a name="enable-forced-logging"></a>Activer la journalisation forcée

Pour mettre en œuvre une base de données secondaire, nous avons besoin d’activer la « Forcé journalisation » dans la base de données principale. Cette option garantit que même si une opération « NoLogging. » est terminée, force la journalisation est prioritaire et toutes les opérations sont enregistrées dans les journaux de rétablissement. Par conséquent, nous Assurez-vous que tous les éléments de la base de données principale sont connecté et réplication de secours inclut toutes les opérations dans la base de données principale. Pour activer la journalisation force, exécutez l’instruction alter database :

    SQL> ALTER DATABASE FORCE LOGGING;

    Database altered.

#### <a name="create-a-password-file"></a>Créer un fichier de mot de passe

Pour pouvoir envoyer et appliquer les journaux archivés à partir du serveur principal sur le serveur de mise en veille, le mot de passe sys doit être identique sur les serveurs principales et de secours. C’est pourquoi vous créez un fichier de mot de passe sur la base de données principale et le copier sur le serveur de mise en veille.

>[AZURE.IMPORTANT] Lorsque vous utilisez la base de données Oracle 12c, il est un nouvel utilisateur, **SYSDG**, que vous pouvez utiliser pour administrer assure votre protection des données Oracle. Pour plus d’informations, voir [modifications dans la base de données Oracle 12 c Release](http://docs.oracle.com/database/121/UNXAR/release_changes.htm#UNXAR404).

En outre, assurez-vous que la ORACLE\_environnement accueil est déjà défini dans Machine1. Dans le cas contraire, définissez-le comme une variable d’environnement à l’aide de la boîte de dialogue Variables d’environnement. Pour accéder à cette boîte de dialogue, démarrez l’utilitaire de **système** en double-cliquant sur l’icône système dans le **Panneau de configuration**. Cliquez sur l’onglet **Avancé** , puis sélectionnez **Variables d’environnement**. Pour définir les variables d’environnement, cliquez sur le bouton **Nouveau** sous **Variables système**. Après avoir configuré les variables d’environnement, fermez l’invite de commandes existant et ouvrir une nouvelle visualisation.

Exécutez l’instruction suivante pour passer à la Oracle\_répertoire de base, telles que c :\\OracleDatabase\\produit\\11.2.0\\dbhome\_1\\base de données.

    cd %ORACLE_HOME%\database

Ensuite, créez un fichier de mot de passe à l’aide de l’utilitaire de création de fichier mot de passe, [ORAPWD](http://docs.oracle.com/cd/B28359_01/server.111/b28310/dba007.htm). Dans la même invite de commande Windows dans Machine1, exécutez la commande suivante en définissant la valeur de mot de passe en tant que le mot de passe de **SYS**:

    ORAPWD FILE=PWDTEST.ora PASSWORD=password FORCE=y

Cette commande crée un fichier de mot de passe, nommé PWDTEST.ora, dans le ORACLE\_famille\\répertoire de base de données. Vous devez copier ce fichier sur % ORACLE\_accueil %\\de base de données répertoire dans Machine2 manuellement.

#### <a name="configure-a-standby-redo-log"></a>Configurer un journal de rétablissement en attente

Ensuite, vous devez configurer un journal de rétablir en attente afin que le serveur principal peut recevoir correctement le rétablissement dès qu’elle est une mise en veille. Création préalable les ici permet également les journaux de rétablissement en attente à créer automatiquement en attente. Il est important de configurer la mise en veille rétablir les journaux (SRL) avec la même taille que journaux de rétablissement en ligne. La taille des fichiers journaux rétablir secours actuel doit correspondre exactement à la taille des fichiers journaux rétablissement en ligne de base de données principale active.

Exécutez l’instruction suivante dans l’instruction SQL\*PLUS invite de commande dans Machine1. Le fichier journal $ v est une vue système qui contient des informations sur les fichiers journaux.

    SQL> select * from v$logfile;
    GROUP# STATUS  TYPE    MEMBER                                                       IS_
    ---------- ------- ------- ------------------------------------------------------------ ---
    3         ONLINE  C:\<YourLocalFolder>\TEST\REDO03.LOG               NO
    2         ONLINE  C:\<YourLocalFolder>\TEST\REDO02.LOG               NO
    1         ONLINE  C:\<YourLocalFolder>\TEST\REDO01.LOG               NO
    Next, query the v$log system view, displays log file information from the control file.
    SQL> select bytes from v$log;
    BYTES
    ----------
    52428800
    52428800
    52428800


Notez que 52428800 est 50 mégaoctets.

Puis, dans le code SQL\*Plus fenêtre, exécutez les instructions suivantes pour ajouter un nouvelle mise en veille groupe de fichiers journaux à une base de données en attente et spécifiez un nombre qui identifie le groupe à l’aide de la clause GROUP. Groupe de nombres peuvent rendent l’administration de groupes de fichiers de journal en attente rétablir plus facilement :

    SQL> ALTER DATABASE ADD STANDBY LOGFILE GROUP 4 'C:\<YourLocalFolder>\TEST\REDO04.LOG' SIZE 50M;
    Database altered.
    SQL> ALTER DATABASE ADD STANDBY LOGFILE GROUP 5 'C:\<YourLocalFolder>\TEST\REDO05.LOG' SIZE 50M;
    Database altered.
    SQL> ALTER DATABASE ADD STANDBY LOGFILE GROUP 6 'C:\<YourLocalFolder>\TEST\REDO06.LOG' SIZE 50M;
    Database altered.

Ensuite, exécutez la vue système suivante pour répertorier les informations sur Rétablir les fichiers journaux. Cette opération permet également de vérifier que les groupes de fichiers journaux rétablir en attente ont été créés :

    SQL> select * from v$logfile;
    GROUP# STATUS  TYPE MEMBER IS_
    ---------- ------- ------- --------------------------------------------- ---
    3         ONLINE C:\<YourLocalFolder>\TEST\REDO03.LOG NO
    2         ONLINE C:\<YourLocalFolder>\TEST\REDO02.LOG NO
    1         ONLINE C:\<YourLocalFolder>\TEST\REDO01.LOG NO
    4         STANDBY C:\<YourLocalFolder>\TEST\REDO04.LOG
    5         STANDBY C:\<YourLocalFolder>\TEST\REDO05.LOG NO
    6         STANDBY C:\<YourLocalFolder>\TEST\REDO06.LOG NO
    6 rows selected.

#### <a name="enable-archiving"></a>Activer l’archivage

Ensuite, activez l’archivage en exécutant les instructions suivantes pour placer la base de données principale en mode journal d’archive et activer l’archivage automatique. Vous pouvez activer le mode journal archive en monter la base de données, puis en exécutant la commande de journal d’archive.

Tout d’abord, ouvrez une session en tant que sysdba. Dans l’invite de commandes, exécutez :

    sqlplus /nolog

    connect / as sysdba

Ensuite, arrêt la base de données dans l’instruction SQL\*Plus invite de commandes :

    SQL> shutdown immediate;
    Database closed.
    Database dismounted.
    ORACLE instance shut down.

Ensuite, exécutez la commande de montage démarrage monter la base de données. Cela garantit que Oracle associe l’instance à la base de données spécifiée.

    SQL> startup mount;
    ORACLE instance started.
    Total System Global Area 1503199232 bytes
    Fixed Size                  2281416 bytes
    Variable Size             922746936 bytes
    Database Buffers          570425344 bytes
    Redo Buffers                7745536 bytes
    Database mounted.

Ensuite, exécutez :

    SQL> alter database archivelog;
    Database altered.

Ensuite, exécutez la Alter instruction de base de données avec la clause ouverte pour que la base de données disponibles pour une utilisation normale :

    SQL> alter database open;

    Database altered.

#### <a name="set-primary-database-initialization-parameters"></a>Définir les paramètres de l’initialisation de base de données principale

Pour configurer la protection des données, vous devez créer et configurer les paramètres de mise en veille sur un fichier pfile régulière (fichier de paramètres de l’initialisation du texte) première. Lorsque le fichier pfile est prêt, vous avez besoin pour le convertir en un fichier de paramètres de serveur (SPFILE).

Vous pouvez contrôler l’environnement de protection des données avec les paramètres de l’initialisation. Fichier RA. Lorsque vous suivez ce didacticiel, vous devez mettre à jour l’initialisation de la base de données principale. RA afin qu’elle peut contenir les deux rôles : principal ou secondaire.

    SQL> create pfile from spfile;
    File created.

Ensuite, vous devez modifier le fichier pfile pour ajouter les paramètres de mise en veille. Pour ce faire, ouvrez la INITTEST. OU d’un fichier à l’emplacement de % ORACLE\_accueil %\\base de données. Ajoutez ensuite les instructions suivantes dans le fichier INITTEST.ora. Convention d’appellation pour votre initialisation. Fichier RA est initialisation\<Votre_nom_base_de_données\>. RA.

    db_name='TEST'
    db_unique_name='TEST'
    LOG_ARCHIVE_CONFIG='DG_CONFIG=(TEST,TEST_STBY)'
    LOG_ARCHIVE_DEST_1= 'LOCATION=C:\OracleDatabase\archive   VALID_FOR=(ALL_LOGFILES,ALL_ROLES) DB_UNIQUE_NAME=TEST'
    LOG_ARCHIVE_DEST_2= 'SERVICE=TEST_STBY LGWR ASYNC VALID_FOR=(ONLINE_LOGFILES,PRIMARY_ROLE) DB_UNIQUE_NAME=TEST_STBY'
    LOG_ARCHIVE_DEST_STATE_1=ENABLE
    LOG_ARCHIVE_DEST_STATE_2=ENABLE
    REMOTE_LOGIN_PASSWORDFILE=EXCLUSIVE
    LOG_ARCHIVE_FORMAT=%t_%s_%r.arc
    LOG_ARCHIVE_MAX_PROCESSES=30
    # Standby role parameters --------------------------------------------------------------------
    fal_server=TEST_STBY
    fal_client=TEST
    standby_file_management=auto
    db_file_name_convert='TEST_STBY','TEST'
    log_file_name_convert='TEST_STBY','TEST'
    # ---------------------------------------------------------------------------------------------


Le bloc d’instructions précédente inclut trois éléments importants configuration :
-   **LOG_ARCHIVE_CONFIG... :** Vous définissez les ID de base de données unique à l’aide de cette déclaration.
-   **LOG_ARCHIVE_DEST_1... :** Vous définissez l’emplacement du dossier archivage local à l’aide de cette déclaration. Nous vous recommandons de vous créez un nouveau répertoire pour des besoins de votre base de données d’archivage et spécifiez l’emplacement d’archivage local à l’aide de cette déclaration explicite plutôt que d’utiliser par défaut dossier % d’Oracle ORACLE_HOME%\database\archive.
-   **LOG_ARCHIVE_DEST_2... LGWR asynchrone... :** vous définissez un processus d’écriture journal asynchrone (LGWR) pour collecter les données de rétablir transaction et transmettre aux destinations en attente. Ici, le DB_UNIQUE_NAME spécifie un nom unique pour la base de données au niveau du serveur en attente de destination.

Une fois que le nouveau fichier de paramètre est prêt, vous devez créer le spfile à partir de celui-ci.

Tout d’abord, fermeture de la base de données :

    SQL> shutdown immediate;

    Database closed.

    Database dismounted.

    ORACLE instance shut down.

Ensuite, exécutez commande sans montage de démarrage comme suit :

    SQL> startup nomount pfile='c:\OracleDatabase\product\11.2.0\dbhome_1\database\initTEST.ora';
    ORACLE instance started.
    Total System Global Area 1503199232 bytes
    Fixed Size                  2281416 bytes
    Variable Size             922746936 bytes
    Database Buffers          570425344 bytes
    Redo Buffers                7745536 bytes

À présent, créez un spfile :

    SQL>create spfile frompfile='c:\OracleDatabase\product\11.2.0\dbhome\_1\database\initTEST.ora';

    File created.

Ensuite, arrêt la base de données :

    SQL> shutdown immediate;

    ORA-01507: database not mounted

Ensuite, utilisez la commande de démarrage pour démarrer une instance :

    SQL> startup;
    ORACLE instance started.
    Total System Global Area 1503199232 bytes
    Fixed Size                  2281416 bytes
    Variable Size             922746936 bytes
    Database Buffers          570425344 bytes
    Redo Buffers                7745536 bytes
    Database mounted.
    Database opened.

##<a name="create-a-physical-standby-database"></a>Créer une base de données en attente physique
Cette section se concentre sur les étapes que vous devez effectuer dans Machine2 pour préparer la base de données en attente physique.

Vous devez tout d’abord, Bureau à distance à Machine2 via le portail classique Azure.

Puis, sur le serveur en attente (Machine2), créez tous les dossiers nécessaires pour la base de données en attente, par exemple C:\\\<YourLocalFolder\>\\TEST. Tout en suivant ce didacticiel, assurez-vous que la structure du dossier correspond à la structure des dossiers sur Machine1 à garder tous les fichiers nécessaires, tels que des fichiers controlfile, fichiers de données, redologfiles, udump, bdump et cdump. En outre, définir la ORACLE\_famille et ORACLE\_variables d’environnement de BASE dans Machine2. Dans le cas contraire, définissez-les comme une variable d’environnement à l’aide de la boîte de dialogue Variables d’environnement. Pour accéder à cette boîte de dialogue, démarrez l’utilitaire de **système** en double-cliquant sur l’icône système dans le **Panneau de configuration**. Cliquez sur l’onglet **Avancé** , puis sélectionnez **Variables d’environnement**. Pour définir les variables d’environnement, cliquez sur le bouton **Nouveau** sous **Variables système**. Après avoir configuré les variables d’environnement, vous devez fermer l’invite de commande Windows existant et ouvrez un nouveau pour afficher les modifications.

Ensuite, procédez comme suit :

1. Préparer un fichier de paramètres de l’initialisation de base de données en attente

2. Configurer le récepteur et tnsnames pour prendre en charge de la base de données sur les ordinateurs principales et de secours

    1. Configurer listener.ora sur les deux serveurs pour contenir les entrées pour ces deux bases de données

    2. Configurer tnsnames.ora sur les ordinateurs virtuels principal et de secours pour contenir les entrées pour les bases de données principales et de secours

    3. Démarrez le récepteur, puis activez tnsping sur les deux Machines virtuelles pour les deux services.

3. Démarrage de l’instance en attente dans un état sans montage

4. Utiliser RMAN pour cloner la base de données et pour créer une base de données en attente

5. Démarrer la base de données en attente physique en mode de récupération gérées

6. Vérifiez que la base de données en attente physique

### <a name="1-prepare-an-initialization-parameter-file-for-standby-database"></a>1. préparer un fichier de paramètres de l’initialisation de base de données en attente

Cette section montre comment préparer un fichier de paramètres de l’initialisation de la base de données en attente. Pour ce faire, copiez d’abord le INITTEST. OU d’un fichier à partir de la Machine 1 à l’ordinateur2 manuellement. Vous devez être en mesure de voir la INITTEST. OU d’un fichier dans le champ % ORACLE\_accueil %\\dossier de base de données dans les deux ordinateurs. Ensuite, modifiez le fichier INITTEST.ora dans Machine2 à définir pour le rôle de secondaire comme indiqué ci-dessous :

    db_name='TEST'
    db_unique_name='TEST_STBY'
    db_create_file_dest='c:\OracleDatabase\oradata\test_stby’
    db_file_name_convert=’TEST’,’TEST_STBY’
    log_file_name_convert='TEST','TEST_STBY'


    job_queue_processes=10
    LOG_ARCHIVE_CONFIG='DG_CONFIG=(TEST,TEST_STBY)'
    LOG_ARCHIVE_DEST_1='LOCATION=c:\OracleDatabase\TEST_STBY\archives VALID_FOR=(ALL_LOGFILES,ALL_ROLES) DB_UNIQUE_NAME=’TEST'
    LOG_ARCHIVE_DEST_2='SERVICE=TEST LGWR ASYNC VALID_FOR=(ONLINE_LOGFILES,PRIMARY_ROLE)
    LOG_ARCHIVE_DEST_STATE_1='ENABLE'
    LOG_ARCHIVE_DEST_STATE_2='ENABLE'
    LOG_ARCHIVE_FORMAT='%t_%s_%r.arc'
    LOG_ARCHIVE_MAX_PROCESSES=30


Le bloc d’instructions précédente inclut deux options de configuration importantes :

-   **\*. LOG_ARCHIVE_DEST_1 :** vous devez créer manuellement le dossier c:\OracleDatabase\TEST_STBY\archives dans Machine2.
-   **\*. LOG_ARCHIVE_DEST_2 :** cette étape est facultative. Vous définissez cette option si celui-ci peut être nécessaire lors de l’ordinateur principal est de maintenance et l’unité secondaire devient une base de données principale.

Ensuite, vous devez démarrer l’instance en attente. Sur le serveur de base de données en attente, entrez la commande suivante à une invite de commande Windows pour créer une instance Oracle en créant un service Windows :

    oradim -NEW -SID TEST\_STBY -STARTMODE MANUAL

La commande **Oradim** crée une instance Oracle mais ne la démarre pas. Vous pouvez le trouver dans le lecteur C:\\OracleDatabase\\produit\\11.2.0\\dbhome\_1\\répertoire BIN.

##<a name="configure-the-listener-and-tnsnames-to-support-the-database-on-primary-and-standby-machines"></a>Configurer le récepteur et tnsnames pour prendre en charge de la base de données sur les ordinateurs principales et de secours
Avant de créer une base de données en attente, vous devez vous assurer que les bases de données principales et de secours dans votre configuration peuvent communiquer entre eux. Pour ce faire, vous devez configurer le récepteur et TNSNames manuellement ou à l’aide de l’utilitaire de configuration réseau NETCA. Il s’agit d’une tâche obligatoire lorsque vous utilisez l’utilitaire de récupération Manager (RMAN).

### <a name="configure-listenerora-on-both-servers-to-hold-entries-for-both-databases"></a>Configurer listener.ora sur les deux serveurs pour contenir les entrées pour ces deux bases de données

Bureau à distance à Ordinateur1 et modifier le fichier listener.ora comme indiqué ci-dessous. Lorsque vous modifiez le fichier listener.ora, toujours vous assurer que l’ouverture et la parenthèse fermante alignent dans la même colonne. Vous pouvez trouver le fichier listener.ora dans le dossier suivant : c:\\OracleDatabase\\produit\\11.2.0\\dbhome\_1\\réseau\\administrateur\\.

    # listener.ora Network Configuration File: C:\OracleDatabase\product\11.2.0\dbhome_1\network\admin\listener.ora

    # Generated by Oracle configuration tools.

    SID_LIST_LISTENER =
      (SID_LIST =
        (SID_DESC =
          (SID_NAME = test)
          (ORACLE_HOME = C:\OracleDatabase\product\11.2.0\dbhome_1)
          (PROGRAM = extproc)
          (ENVS = "EXTPROC_DLLS=ONLY:C:\OracleDatabase\product\11.2.0\dbhome_1\bin\oraclr11.dll")
        )
      )

    LISTENER =
      (DESCRIPTION_LIST =
        (DESCRIPTION =
          (ADDRESS = (PROTOCOL = TCP)(HOST = MACHINE1)(PORT = 1521))
          (ADDRESS = (PROTOCOL = IPC)(KEY = EXTPROC1521))
        )
      )

Bureau à distance, suivant Machine2 et modifier la listener.ora de fichiers comme suit : # listener.ora fichier de Configuration réseau : C:\OracleDatabase\product\11.2.0\dbhome_1\network\admin\listener.ora

    # Generated by Oracle configuration tools.

    SID_LIST_LISTENER =
      (SID_LIST =
        (SID_DESC =
          (SID_NAME = test_stby)
          (ORACLE_HOME = C:\OracleDatabase\product\11.2.0\dbhome_1)
          (PROGRAM = extproc)
          (ENVS = "EXTPROC_DLLS=ONLY:C:\OracleDatabase\product\11.2.0\dbhome_1\bin\oraclr11.dll")
        )
      )

    LISTENER =
      (DESCRIPTION_LIST =
        (DESCRIPTION =
          (ADDRESS = (PROTOCOL = TCP)(HOST = MACHINE2)(PORT = 1521))
          (ADDRESS = (PROTOCOL = IPC)(KEY = EXTPROC1521))
        )
      )


### <a name="configure-tnsnamesora-on-the-primary-and-standby-virtual-machines-to-hold-entries-for-both-primary-and-standby-databases"></a>Configurer tnsnames.ora sur les ordinateurs virtuels principal et de secours pour contenir les entrées pour les bases de données principales et de secours

Bureau à distance à Ordinateur1 et modifier le fichier tnsnames.ora comme indiqué ci-dessous. Vous pouvez trouver le fichier tnsnames.ora dans le dossier suivant : c:\\OracleDatabase\\produit\\11.2.0\\dbhome\_1\\réseau\\administrateur\\.

    TEST =
      (DESCRIPTION =
        (ADDRESS_LIST =
          (ADDRESS = (PROTOCOL = TCP)(HOST = MACHINE1)(PORT = 1521))
        )
        (CONNECT_DATA =
          (SERVICE_NAME = test)
        )
      )

    TEST_STBY =
      (DESCRIPTION =
        (ADDRESS_LIST =
          (ADDRESS = (PROTOCOL = TCP)(HOST = MACHINE2)(PORT = 1521))
        )
        (CONNECT_DATA =
          (SERVICE_NAME = test_stby)
        )
      )

Bureau à distance à Machine2 et modifier le tnsnames.ora comme suit :

    TEST =
      (DESCRIPTION =
        (ADDRESS_LIST =
          (ADDRESS = (PROTOCOL = TCP)(HOST = MACHINE1)(PORT = 1521))
        )
        (CONNECT_DATA =
          (SERVICE_NAME = test)
        )
      )

    TEST_STBY =
      (DESCRIPTION =
        (ADDRESS_LIST =
          (ADDRESS = (PROTOCOL = TCP)(HOST = MACHINE2)(PORT = 1521))
        )
        (CONNECT_DATA =
          (SERVICE_NAME = test_stby)
        )
      )


### <a name="start-the-listener-and-check-tnsping-on-both-virtual-machines-to-both-services"></a>Démarrez le récepteur, puis activez tnsping sur les deux Machines virtuelles pour les deux services.

Ouvrez une invite de commandes Windows nouveau dans des Machines virtuelles principal et de secours et exécutez les instructions suivantes :

    C:\Users\DBAdmin>tnsping test

    TNS Ping Utility for 64-bit Windows: Version 11.2.0.1.0 - Production on 14-NOV-2013 06:29:08
    Copyright (c) 1997, 2010, Oracle.  All rights reserved.
    Used parameter files:
    C:\OracleDatabase\product\11.2.0\dbhome_1\network\admin\sqlnet.ora
    Used TNSNAMES adapter to resolve the alias
    Attempting to contact (DESCRIPTION = (ADDRESS_LIST = (ADDRESS = (PROTOCOL = TCP)(HOST = MACHINE1)(PORT = 1521))) (CONNECT_DATA = (SER
    VICE_NAME = test)))
    OK (0 msec)


    C:\Users\DBAdmin>tnsping test_stby

    TNS Ping Utility for 64-bit Windows: Version 11.2.0.1.0 - Production on 14-NOV-2013 06:29:16
    Copyright (c) 1997, 2010, Oracle.  All rights reserved.
    Used parameter files:
    C:\OracleDatabase\product\11.2.0\dbhome_1\network\admin\sqlnet.ora
    Used TNSNAMES adapter to resolve the alias
    Attempting to contact (DESCRIPTION = (ADDRESS_LIST = (ADDRESS = (PROTOCOL = TCP)(HOST = MACHINE2)(PORT = 1521))) (CONNECT_DATA = (SER
    VICE_NAME = test_stby)))
    OK (260 msec)


##<a name="start-up-the-standby-instance-in-nomount-state"></a>Démarrage de l’instance en attente dans un état sans montage
Pour configurer l’environnement pour prendre en charge de la base de données en attente sur l’ordinateur virtuel en attente (MACHINE2).

Tout d’abord, copiez le fichier de mot de passe de la machine primaire (Machine1) sur l’ordinateur en attente (Machine2) manuellement. Cela est nécessaire que le mot de passe **sys** doit être identique sur les deux ordinateurs.

Ensuite, ouvrez l’invite de commandes en Machine2 et configurer les variables d’environnement pour pointer vers la base de données secondaire comme suit :

    SET ORACLE_HOME=C:\OracleDatabase\product\11.2.0\dbhome_1
    SET ORACLE_SID=TEST_STBY

Ensuite, démarrez la base de données secondaire dans l’état sans montage et puis générer un spfile.

Démarrer la base de données :

    SQL>shutdown immediate;

    SQL>startup nomount
    ORACLE instance started.

    Total System Global Area  747417600 bytes
    Fixed Size                  2179496 bytes
    Variable Size             473960024 bytes
    Database Buffers          264241152 bytes
    Redo Buffers                7036928 bytes


##<a name="use-rman-to-clone-the-database-and-to-create-a-standby-database"></a>Utiliser RMAN pour cloner la base de données et pour créer une base de données en attente
Vous pouvez utiliser l’utilitaire de récupération Manager (RMAN) pour prendre une copie de sauvegarde de la base de données principale pour créer la base de données en attente physique.

Bureau à distance à la Machine virtuelle en attente (MACHINE2) et exécuter l’utilitaire RMAN en spécifiant une connexion complète de chaîne pour les deux la cible (base de données principale, Machine1) et auxiliaire (en attente de base de données, Machine2) instances.

>[AZURE.IMPORTANT] N’utilisez pas l’authentification de système d’exploitation car il n’existe aucune base de données dans l’ordinateur serveur de secours encore.

    C:\> RMAN TARGET sys/password@test AUXILIARY sys/password@test_STBY

    RMAN>DUPLICATE TARGET DATABASE
      FOR STANDBY
      FROM ACTIVE DATABASE
      DORECOVER
        NOFILENAMECHECK;

##<a name="start-the-physical-standby-database-in-managed-recovery-mode"></a>Démarrer la base de données en attente physique en mode de récupération gérées
Ce didacticiel montre comment créer une base de données en attente physique. Pour plus d’informations sur la création d’une base de données en attente logique, consultez la documentation Oracle.

Ouvrez SQL\*ainsi qu’invite de commandes et activer la protection des données sur le serveur (MACHINE2) ou en attente Machine virtuelle comme suit :

    SHUTDOWN IMMEDIATE;
    STARTUP MOUNT;
    ALTER DATABASE RECOVER MANAGED STANDBY DATABASE DISCONNECT FROM SESSION;

Lorsque vous ouvrez la base de données en attente en mode **Monter** , le journal d’archivage continue de livraison et le processus de récupération gérées continue journal application sur la base de données en attente. Cela garantit que la base de données secondaire reste à jour avec la base de données principale. Notez que la base de données en attente ne peut pas être accessible pour les rapports pendant cette période.

Lorsque vous ouvrez la base de données en attente en mode **Lecture seule** , l’archive envoi des journaux continue. Mais le processus de récupération gérées s’arrête. Ainsi, la base de données en attente devenir plus obsolètes jusqu'à ce que le processus de récupération gérées est repris. Vous pouvez accéder à la base de données en attente pour les rapports pendant ce temps, mais données peuvent ne pas reflètent les dernières modifications.

En règle générale, nous vous recommandons de conserver la base de données en attente en mode **Monter** pour conserver les données dans la base de données en attente à jour s’il existe une défaillance de la base de données principale. Toutefois, vous pouvez conserver la base de données en attente en mode **Lecture seule** pour les rapports en fonction des besoins de votre application. Les étapes suivantes expliquent comment activer la protection des données en mode lecture seule en utilisant SQL\*Plus :

    SHUTDOWN IMMEDIATE;
    STARTUP MOUNT;
    ALTER DATABASE OPEN READ ONLY;


##<a name="verify-the-physical-standby-database"></a>Vérifiez que la base de données en attente physique
Cette section montre comment vérifier la configuration de disponibilité en tant qu’administrateur.

Ouvrez SQL\*ainsi que la fenêtre d’invite de commande et vérifiez archivés rétablissement journal sur la Machine virtuelle mise en veille (Machine2) :

    SQL> show parameters db_unique_name;

    NAME                                TYPE       VALUE
    ------------------------------------ ----------- ------------------------------
    db_unique_name                      string     TEST_STBY

    SQL> SELECT NAME FROM V$DATABASE

    SQL> SELECT SEQUENCE#, FIRST_TIME, NEXT_TIME, APPLIED FROM V$ARCHIVED_LOG ORDER BY SEQUENCE#;

    SEQUENCE# FIRST_TIM NEXT_TIM APPLIED
    ----------------  ---------------  --------------- ------------
    45                    23-FEB-14   23-FEB-14   YES
    45                    23-FEB-14   23-FEB-14   NO
    46                    23-FEB-14   23-FEB-14   NO
    46                    23-FEB-14   23-FEB-14   YES
    47                    23-FEB-14   23-FEB-14   NO
    47                    23-FEB-14   23-FEB-14   NO

Ouvrez SQL * Plus fichiers journaux fenêtre et le commutateur invite de commandes sur l’ordinateur principal (Machine1) :

    SQL> alter system switch logfile;
    System altered.

    SQL> archive log list
    Database log mode              Archive Mode
    Automatic archival             Enabled
    Archive destination            C:\OracleDatabase\archive
    Oldest online log sequence     69
    Next log sequence to archive   71
    Current log sequence           71

Vérifier le journal de rétablissement archivé sur la Machine virtuelle mise en veille (Machine2) :

    SQL> SELECT SEQUENCE#, FIRST_TIME, NEXT_TIME, APPLIED FROM V$ARCHIVED_LOG ORDER BY SEQUENCE#;

    SEQUENCE# FIRST_TIM NEXT_TIM APPLIED
    ----------------  ---------------  --------------- ------------
    45                    23-FEB-14   23-FEB-14   YES
    46                    23-FEB-14   23-FEB-14   YES
    47                    23-FEB-14   23-FEB-14   YES
    48                    23-FEB-14   23-FEB-14   YES

    49                    23-FEB-14   23-FEB-14   YES
    50                    23-FEB-14   23-FEB-14   IN-MEMORY

Vérifier la présence de tout écart sur la Machine virtuelle mise en veille (Machine2) :

    SQL> SELECT * FROM V$ARCHIVE_GAP;
    no rows selected.

Une autre méthode de vérification peut être pour basculer sur la base de données en attente, puis vérifiez s’il est possible de retour arrière vers la base de données principale. Pour activer la base de données en attente comme une base de données principale, utilisez les instructions suivantes :

    SQL> ALTER DATABASE RECOVER MANAGED STANDBY DATABASE FINISH;
    SQL> ALTER DATABASE ACTIVATE STANDBY DATABASE;

Si vous n’avez pas activé flashback sur la base de données principale d’origine, il est recommandé d’avoir abandonner la base de données principale d’origine et recréer comme une base de données en attente.

Nous vous recommandons d’activer la base de données flashback sur le serveur principal et les bases de données en attente. Lorsqu’un basculement se produit, la base de données principale peut être revenir mise à jour le délai avant le basculement et rapidement converti en une base de données en attente.

##<a name="additional-resources"></a>Ressources supplémentaires
[Images de Machine virtuelle Oracle pour Azure](virtual-machines-windows-classic-oracle-images.md)

<properties
    pageTitle="Configuration de machines virtuelles Oracle GoldenGate | Microsoft Azure"
    description="Passez en revue un didacticiel sur la configuration et la mise en œuvre Oracle GoldenGate sur Azure Windows Server machines virtuelles pour haute disponibilité et récupération d’urgence."
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


#<a name="configuring-oracle-goldengate-for-azure"></a>Configuration Oracle GoldenGate pour Azure


Ce didacticiel montre comment configurer Oracle GoldenGate pour Machines virtuelles Azure environnement haute disponibilité et récupération d’urgence. Le didacticiel se concentre sur la [réplication bidirectionnelle](http://docs.oracle.com/goldengate/1212/gg-winux/GWUAD/wu_about_gg.htm) des bases de données Oracle RAC non - et nécessite que les deux sites sont actifs.

Oracle GoldenGate prend en charge la distribution des données et l’intégration des données. Il vous permet de configurer une distribution des données et la solution de synchronisation des données via la configuration de la réplication Oracle Oracle et fournit une solution flexible haute disponibilité. Oracle GoldenGate complète la protection des données Oracle avec ses capacités de réplication pour activer des migrations et des mises à niveau sans interruption de service et de distribution des informations à l’échelle de l’entreprise. Pour plus d’informations, reportez-vous [à l’aide de Oracle GoldenGate avec protection des données Oracle](http://docs.oracle.com/cd/E11882_01/server.112/e17157/unplanned.htm).

Oracle GoldenGate contient les composants principaux suivants : extraire, traces pompe, Replicat, de données ou extraire des fichiers, les points de contrôle, responsable et collecteur. Pour que la réplication bidirectionnelle entre deux sites, vous devez créer et commencer à tous les composants sur les deux sites. Pour plus d’informations sur l’architecture Oracle GoldenGate, voir [GoldenGate Guide Oracle](http://docs.oracle.com/goldengate/1212/gg-winux/index.html).

Ce didacticiel suppose que vous avez déjà connaissance théorique et pratique sur les concepts de base de données Oracle haute disponibilité et reprise ainsi que [GoldenGate Oracle](http://docs.oracle.com/goldengate/1212/gg-winux/index.html). Pour plus d’informations, consultez le [site web Oracle](http://www.oracle.com/technetwork/database/features/availability/index.html).

En outre, le didacticiel suppose que vous avez déjà implémenté les conditions préalables suivantes :

- Vous avez déjà examiné la section disponibilité et les considérations sur la récupération urgence dans la rubrique [Machine virtuelle Oracle images - considérations divers](virtual-machines-windows-classic-oracle-considerations.md) . Notez que Azure prend en charge les instances de base de données Oracle autonome, mais pas Oracle RAC (Oracle RAC) pour le moment.

- Vous avez téléchargé le logiciel Oracle GoldenGate à partir du site web [Téléchargements Oracle](http://www.oracle.com/us/downloads/index.html) . Vous avez sélectionné logiciels intermédiaires produit Pack Oracle Fusion – intégration de données. Ensuite, vous avez sélectionné Oracle GoldenGate sur Oracle v11.2.1 Media Pack pour Microsoft Windows x64 (64 bits) pour une base de données Oracle 11 g. Ensuite, téléchargez Oracle GoldenGate V11.2.1.0.3 pour Oracle 11g 64 bits sur Windows 2008 (64 bits).

- Vous avez créé deux Machines virtuelles (machines virtuelles) dans Azure à l’aide de Oracle Enterprise Edition sur Windows Server. Assurez-vous que les ordinateurs virtuels sont dans le [même service cloud](virtual-machines-linux-load-balance.md) et dans le même [Réseau virtuel](https://azure.microsoft.com/documentation/services/virtual-network/) pour vous assurer qu’ils peuvent accéder à eux via l’adresse IP privée permanente.

- Vous avez défini les noms de Machine virtuelle comme « MachineGG1 » pour un Site et « MachineGG2 » pour le Site B sur le portail classique Azure.

- Vous avez créé les bases de données de test « TestGG1 » sur un Site et « TestGG2 » sur le Site B.

- Vous connecter à votre serveur Windows en tant que membre du groupe Administrateurs ou membre du groupe **ORA_DBA** .

Dans ce didacticiel, vous allez :

1. Configuration de base de données sur le Site A et B de Site  

    1. Chargement des données initiale

2. Préparer Site A et B de Site pour la réplication de base de données

3. Créer tous les objets nécessaires pour prendre en charge de la réplication DDL

4. Configurer le Gestionnaire de GoldenGate sur les sites A et B

5. Créer un groupe extraire et Data Pump processus sur le Site A et B de Site

    1. Créer des processus extraire et Data Pump sur un Site

    2. Créer une table de point de contrôle GoldenGate sur le Site B

    3. Ajouter des REPLICAT sur Site B

    4. Créer des processus extraire et Data Pump sur le Site B

    5. Créer une table de point de contrôle GoldenGate sur un Site

    6. Ajouter REPLICAT sur un Site

    7. Ajouter trandata sur le Site A et B de Site

    8. Démarrer des processus extraire et Data Pump sur un Site

    9. Démarrer des processus extraire et Data Pump sur Site B

    10. Démarrer le processus de REPLICAT sur un Site

    11. Démarrer le processus de REPLICAT sur le Site B

6. Vérifier le processus de réplication bidirectionnelle

>[AZURE.IMPORTANT] Ce didacticiel a été le programme d’installation et tester par rapport à la configuration logicielle suivante :
>
>|                        | **Une base de données du site**              | **Base de données site B**              |
>|------------------------|----------------------------------|----------------------------------|
>| **Version Oracle**     | Oracle11g Release 2-(11.2.0.1) | Oracle11g Release 2-(11.2.0.1) |
>| **Nom de l’ordinateur**       | MachineGG1                       | MachineGG2                       |
>| **Système d'exploitation**   | Windows 2008 R2                  | Windows 2008 R2                  |
>| **Oracle identificateur de sécurité**         | TESTGG1                          | TESTGG2                          |
>| **Schéma de réplication** | SCOTT                            | SCOTT                            |

Pour les versions ultérieures de base de données Oracle et Oracle GoldenGate, vous devrez éventuellement certaines modifications supplémentaires dont vous avez besoin pour mettre en œuvre. Pour plus d’informations spécifiques version plus récentes, voir documentation [Oracle GoldenGate](http://docs.oracle.com/goldengate/1212/gg-winux/index.html) et [Base de données Oracle](http://www.oracle.com/us/corporate/features/database-12c/index.html) sur le site web Oracle. Par exemple, pour une base de données source release 11.2.0.4 et versions ultérieures, la capture de DDL est exécutée par le serveur logmining façon asynchrone et requiert aucun déclencheur spécial, tableaux ou autres objets de base de données doit être installé. Les mises à niveau Oracle GoldenGate peuvent être effectuées sans arrêt des applications de l’utilisateur. L’utilisation d’un déclencheur DDL et objets de prise en charge est nécessaire lors de l’extraction est en mode intégré avec une base de données source de 11g Oracle qui est antérieure à la version 11.2.0.4. Pour obtenir des instructions détaillées, consultez [installation et configuration Oracle GoldenGate de base de données Oracle](http://docs.oracle.com/goldengate/1212/gg-winux/GIORA.pdf).

##<a name="1-setup-database-on-site-a-and-site-b"></a>1. Configuration de base de données sur le Site A et B de Site
Cette section explique comment effectuer les conditions préalables de base de données sur des sites A et b de Site. Vous devez effectuer les étapes de cette section sur les deux sites : Site A et b de Site.

Bureau à distance, première au Site A et B de Site via le portail classique Azure. Ouvrez une invite de commandes Windows et créer un répertoire de base pour les fichiers d’installation Oracle GoldenGate :

    mkdir C:\OracleGG

Ensuite, décompresser le fichier et installez le logiciel Oracle GoldenGate dans ce dossier. Une fois cette étape, vous pouvez lancer le relais de commande logiciel GoldenGate (GGSCI) en exécutant la commande suivante :

    C:\OracleGG\.\ggsci

Vous pouvez utiliser [GGSCI](http://docs.oracle.com/goldengate/1212/gg-winux/GWUAD/wu_gettingstarted.htm) pour exécuter plusieurs commandes configurer, contrôler et surveiller Oracle GoldenGate.

Ensuite, exécutez la commande suivante pour créer tous les sous-dossiers du package d’installation :

    GGSCI (Hostname) 1> CREATE SUBDIRS

Exécutez la commande suivante pour quitter l’invite de commandes GGSCI :

    GGSCI (Hostname) 1> EXIT

Ensuite, vous devez créer un utilisateur de base de données, qui sera utilisé par les processus Oracle GoldenGate Manager, extraire et la réplication. Notez que vous pouvez créer des utilisateurs individuels pour chaque processus ou configurer un seul utilisateur courantes. Dans ce didacticiel, nous créons un utilisateur, qui est appelé ggate. Ensuite, nous accorder à cet utilisateur les droits nécessaires. Notez que vous devez effectuer les opérations suivantes sur le Site A et b de Site.

Ouvrez SQL\*Plus fenêtre de commande sur le Site A et B de Site avec des privilèges d’administrateur de base de données à l’aide de **SYSDBA**, telles que :

    Enter username: / as sysdba

Et exécutez :

    SQL> create tablespace ggs_data   datafile 'c:\OracleDatabase\oradata\<DBNAME>\<DBNAME>ggs_data01.dbf' size 200m;
    SQL> create user ggate identified by ggate default tablespace ggs_data  temporary tablespace temp;
          grant connect, resource to ggate;
          grant select any dictionary, select any table to ggate;
          grant create table to ggate;
          grant flashback any table to ggate;
          grant execute on dbms_flashback to ggate;
          grant execute on utl_file to ggate;
          grant create any table to ggate;
          grant insert any table to ggate;
          grant update any table to ggate;
          grant delete any table to ggate;
          grant drop any table to ggate;

Ensuite, localisez l’initialisation\<DatabaseSID\>. OU d’un fichier dans le champ % ORACLE\_accueil %\\dossier sur Site A et B de Site de base de données et ajoutez les paramètres de base de données suivants à INITTEST.ora :

    UNDO\_MANAGEMENT=AUTO
    UNDO\_RETENTION=86400

Pour une liste complète de toutes les commandes Oracle GoldenGate GGSCI, voir [informations de référence pour Oracle GoldenGate pour Windows](http://docs.oracle.com/goldengate/1212/gg-winux/GWURF/ggsci_commands.htm).

### <a name="perform-initial-data-load"></a>Chargement des données initiale

Vous pouvez effectuer le chargement initial des données dans la base de données en suivant plusieurs méthodes. Par exemple, vous pouvez utiliser le [Chargement Direct Oracle GoldenGate](http://docs.oracle.com/goldengate/1212/gg-winux/GWUAD/wu_initsync.htm) ou normales utilitaires d’importation et exportation pour exporter les données de la table à partir de Site A au Site B.

Pour illustrer le processus de réplication Oracle GoldenGate, ce didacticiel illustre la création d’une table sur des sites A et B de site à l’aide des commandes suivantes.

Pour commencer, ouvrez la SQL\*Plus fenêtre de commande et exécutez la commande suivante pour créer une table d’inventaire sur bases de données de Site A et B de Site :

    create table scott.inventory
    (prod_id number,
    prod_category varchar2(20),
    qty_in_stock number,
    last_dml timestamp default systimestamp);

Ensuite, ajoutez une contrainte à la table nouvellement créée de Site A et de bases de données de Site B :

    alter table scott.inventory add constraint pk_inventory primary key (prod_id) ;

Ensuite, accordez tous les privilèges sur la nouvelle table stock à la ggate utilisateur de Site A et b de Site :

    grant all on scott.inventory to ggate;

Ensuite, créer et activer un déclencheur de base de données, INVENTORY_CDR_TRG, sur la table pour vous assurer que toutes les transactions vers la nouvelle table sont enregistrées si l’utilisateur n’est pas ggate nouvellement créé. Effectuer cette opération de Site A et b de Site.

    CREATE OR REPLACE TRIGGER INVENTORY_CDR_TRG
    BEFORE UPDATE
    ON SCOTT.INVENTORY
    REFERENCING NEW AS New OLD AS Old
    FOR EACH ROW
    BEGIN
    IF SYS_CONTEXT ('USERENV', 'SESSION_USER') != 'GGATE'
    THEN
    :NEW.LAST_DML := SYSTIMESTAMP;
    END IF;
    END;
    /


##<a name="2-prepare-site-a-and-site-b-for-database-replication"></a>2. préparer Site A et B de Site pour la réplication de base de données
Cette section explique comment préparer Site A et B de Site pour la réplication de base de données. Vous devez effectuer les étapes de cette section sur les deux sites : Site A et b de Site.

Bureau à distance, première au Site A et B de Site via le portail classique Azure. Passer de la base de données en mode de journal d’archive à l’aide de l’instruction SQL * Plus fenêtre de commande :

    sql>shutdown immediate
    sql>startup mount
    sql>alter database archivelog;
    sql>alter database open;


Ensuite, activez minimale [journalisation supplémentaire](http://docs.oracle.com/cd/E11882_01/server.112/e22490/logminer.htm) comme suit :

    SQL> ALTER DATABASE ADD SUPPLEMENTAL LOG DATA (ALL) COLUMNS;

Ensuite, préparer la base de données pour prendre en charge de la réplication DDL (langage de définition de données) :

    SQL> alter system set recyclebin=off scope=spfile;

Ensuite, puis arrêter et redémarrer la base de données :

    sql>shutdown immediate
    sql>startup


##<a name="3-create-all-necessary-objects-to-support-ddl-replication"></a>3. créer tous les objets nécessaires pour prendre en charge de la réplication DDL
Cette section répertorie les scripts que vous souhaitez utiliser pour créer tous les objets nécessaires pour prendre en charge de la réplication DDL. Vous devez exécuter les scripts spécifiés dans cette section sur le Site A et b de Site.

Ouvrez une invite de commandes Windows et accédez au dossier GoldenGate Oracle, tel que c :\\OracleGG. Démarrez SQL\*Plus invite de commandes en tant qu’administrateur de base de données, par exemple à l’aide de **SYSDBA** de Site A et b de Site.

Ensuite, exécutez les scripts suivants :

    SQL> @marker_setup.sql  
    Enter GoldenGate schema name: ggate
    SQL> @ddl_setup.sql  
    Enter GoldenGate schema name: ggate
    SQL> @role_setup.sql
    Enter GoldenGate schema name: ggate
    SQL> grant ggs_ggsuser_role to ggate;
     Grant succeeded.
     SQL> @ddl_enable
     Trigger altered.
     SQL> @ddl_pin ggate


Outil GoldenGate Oracle nécessite une connexion au niveau de table pour la prise en charge DDL (langage de définition de données). C’est pourquoi, activer supplémentaire journalisation au niveau de la table à l’aide de la commande Ajouter TRANDATA. Ouvrez la fenêtre de relais Oracle GoldenGate commande, connectez-vous à la base de données et puis exécutez la commande TRANDATA ajouter :

    GGSCI 5> DBLOGIN USERID ggate, PASSWORD ggate

    GGSCI(Hostname) 6> add trandata scott.inventory

##<a name="4-configure-goldengate-manager-on-site-a-and-site-b"></a>4. configurer le Gestionnaire de GoldenGate sur les sites A et B
Le Gestionnaire de GoldenGate Oracle effectue un certain nombre de fonctions, telles que les autres processus GoldenGate, gestion du fichier journal de piste et création de rapports de démarrage.

Vous devez configurer le processus Oracle GoldenGate Manager sur des sites A et b de Site. Pour ce faire, effectuez les opérations suivantes sur le Site A et b de Site.

Fenêtres ouvertes fenêtre de commande et lancer le relais de commande Oracle GoldenGate :

    cd C:\OracleGG\
    c:\OracleGG>ggsci
    Oracle GoldenGate Command Interpreter for Oracle
    Version 11.2.1.0.3 14400833 OGGCORE_11.2.1.0.3_PLATFORMS_120823.1258
    Windows x64 (optimized), Oracle 11g on Aug 23 2012 16:50:36
    Copyright (C) 1995, 2012, Oracle and/or its affiliates. All rights reserved.


Enregistre la session GGSCI dans une base de données afin que vous pouvez exécuter des commandes qui affectent la base de données :

    GGSCI (HostName) 1> DBLOGIN USERID ggate, PASSWORD ggate
    Successfully logged into database.

Afficher l’état et un décalage (le cas échéant) pour tous les processus responsable, extraire et Replicat sur un système :

    GGSCI (HostName) 2> info all
    Program     Status      Group       Lag           Time Since Chkpt
    MANAGER     STOPPED

Ouvrez le fichier de paramètres à l’aide de la commande modifier les paramètres, puis ajoutez les informations suivantes :

    GGSCI (HostName) 3> edit params mgr
    PORT 7809
    USERID ggate, PASSWORD ggate
    PURGEOLDEXTRACTS  C:\OracleGG\dirdat\ex, USECHECKPOINTS

Afficher l’état et un décalage (le cas échéant) pour tous les processus responsable, extraire et Replicat sur un système :

    GGSCI (HostName) 46> info all
    Program     Status      Group       Lag           Time Since Chkpt
    MANAGER     STOPPED

Enregistre la session GGSCI dans une base de données afin que vous pouvez exécuter des commandes qui affectent la base de données :

    GGSCI (HostName) 47> dblogin USERID ggate, PASSWORD ggate

    Successfully logged into database.

Démarrer le processus de gestionnaire :

    GGSCI (HostName) 48> start manager
    Manager started.

##<a name="5-create-extract-group-and-data-pump-processes-on-site-a-and-site-b"></a>5. créer extraire groupe et Data Pump processus sur le Site A et B de Site

###<a name="create-extract-and-data-pump-processes-on-site-a"></a>Créer des processus extraire et Data Pump sur un Site

Vous devez créer les processus extraire et Data Pump de Site A et Site B. distance au Site A et B de Site via le portail classique Azure. Ouvrir la fenêtre de relais GGSCI commande. Exécutez les commandes suivantes sur le Site a :

    GGSCI (MachineGG1) 14> add extract ext1 tranlog begin now
    EXTRACT added.
    GGSCI (MachineGG1) 4> add exttrail C:\OracleGG\dirdat\lt, extract ext1
    EXTTRAIL added.
    GGSCI (MachineGG1) 16> add extract dpump1 exttrailsource C:\OracleGG\dirdat\aa
    EXTRACT added.
    GGSCI (MachineGG1) 17> add rmttrail C:\OracleGG\dirdat\ab extract dpump1
    RMTTRAIL added.

Ouvrez le fichier de paramètres à l’aide de la commande modifier les paramètres, puis ajoutez les informations suivantes : GGSCI (MachineGG1) 18 > modifier les paramètres ext1 extraire ext1 ID utilisateur ggate, mot de passe ggate EXTTRAIL C:\OracleGG\dirdat\aa TRANLOGOPTIONS EXCLUDEUSER ggate TABLE scott.inventory, GETBEFORECOLS (KEYINCLUDING de mise à jour suite (prod_category, qty_in_stock, last_dml), sur Supprimer KEYINCLUDING (prod_category, qty_in_stock, last_dml)) ;

Ouvrez le fichier de paramètres à l’aide de la commande modifier les paramètres, puis ajoutez les informations suivantes :

    GGSCI (MachineGG1) 15> edit params dpump1
    EXTRACT dpump1
     USERID ggate, PASSWORD ggate
     RMTHOST ActiveGG2orcldb, MGRPORT 7809, TCPBUFSIZE 100000
     RMTTRAIL C:\OracleGG\dirdat\ab
     PASSTHRU
     TABLE scott.inventory;

###<a name="create-a-goldengate-checkpoint-table-on-site-b"></a>Créer une table de point de contrôle GoldenGate sur le Site B

Ensuite, vous devez ajouter un tableau de point de contrôle sur le Site B. Pour ce faire, vous devez ouvrir une fenêtre de relais GoldenGate commande et exécuter :

    C:\OracleGG\ggsci
    GGSCI (MachineGG2) 1> DBLOGIN USERID ggate, PASSWORD ggate
    Successfully logged into database.

Et ensuite, ajoutez la table point de contrôle pour la base de données, où ggate est le propriétaire :

    GGSCI (MachineGG2) 2> ADD CHECKPOINTTABLE ggate.checkpointtable
    Successfully created checkpoint table ggate.checkpointtable.

Ajoutez le nom de la table de point à cocher pour le fichier global sur le serveur cible, ce qui correspond à Site B dans cette étape. Modifier le fichier global sur Site b :

    GGSCI (MachineGG2) 1> EDIT PARAMS ./GLOBALS

Ajoutez ensuite le paramètre CHECKPOINTTABLE au fichier globales existant :

    GGSCHEMA ggate
    CHECKPOINTTABLE ggate.checkpointtable

En tant que dernière étape, enregistrez et fermez le fichier de paramètre global.


###<a name="add-replicat-on-site-b"></a>Ajouter des REPLICAT sur Site B
Cette section décrit comment ajouter un processus REPLICAT « REP2 » sur le Site B.

Commande Ajouter REPLICAT permet de créer un groupe Replicat sur Site b :

    GGSCI (MachineGG2) 37> add replicat rep2 exttrail C:\OracleGG\dirdatab, checkpointtable ggate.checkpointtable

Ouvrez le fichier de paramètres à l’aide de la commande modifier les paramètres, puis ajoutez les informations suivantes :

    GGSCI (MachineGG2) 10> edit params rep2
    REPLICAT rep2
    ASSUMETARGETDEFS
    USERID ggate, PASSWORD ggate
    DISCARDFILE C:\OracleGG\dirdat\discard.txt, append,megabytes 10
    MAP scott.inventory, TARGET scott.inventory;

###<a name="create-extract-and-data-pump-processes-on-site-b"></a>Créer des processus extraire et Data Pump sur le Site B

Cette section décrit comment créer un nouveau processus extrait « EXT2 » et un processus pompe de nouvelles données « DPUMP2 » sur le Site b :

    GGSCI (MachineGG2) 3> add extract ext2 tranlog begin now
     EXTRACT added.
    GGSCI (MachineGG2) 4> add exttrail C:\OracleGG\dirdat\ac extract ext2
     EXTTRAIL added.
    GGSCI (MachineGG2) 5> add extract dpump2 exttrailsource C:\OracleGG\dirdat\ac
     EXTRACT added.
    GGSCI (MachineGG2) 6> add rmttrail C:\OracleGG\dirdat\ad extract dpump2
     RMTTRAIL added.

Ouvrez le fichier de paramètres à l’aide de la commande modifier les paramètres, puis ajoutez les informations suivantes :

    GGSCI (MachineGG2) 31> edit params ext2
    EXTRACT ext2
    USERID ggate, PASSWORD ggate
    EXTTRAIL C:\OracleGG\dirdat\ac
    TRANLOGOPTIONS EXCLUDEUSER ggate
    TABLE scott.inventory,
    GETBEFORECOLS (
    ON UPDATE KEYINCLUDING (prod_category,qty_in_stock, last_dml),
    ON DELETE KEYINCLUDING (prod_category,qty_in_stock, last_dml));

Ouvrez le fichier de paramètres à l’aide de la commande modifier les paramètres, puis ajoutez les informations suivantes :

    GGSCI (MachineGG2) 32> edit params dpump2
    EXTRACT dpump2
    USERID ggate, PASSWORD ggate
    RMTHOST MachineGG1, MGRPORT 7809, TCPBUFSIZE 100000
    RMTTRAIL C:\OracleGG\dirdat\ad
    PASSTHRU
    TABLE scott.inventory;

###<a name="create-a-goldengate-checkpoint-table-on-site-a"></a>Créer une table de point de contrôle GoldenGate sur un Site

Ouvrez la fenêtre de relais Oracle GoldenGate commande et créer une table de point de contrôle :

    GGSCI (MachineGG1) 1> DBLOGIN USERID ggate, PASSWORD ggate
    Successfully logged into database.

    GGSCI (MachineGG1) 2> ADD CHECKPOINTTABLE ggate.checkpointtable

    Successfully created checkpoint table ggate.checkpointtable.

Vous devez également ajouter le nom de la table de point à cocher au fichier globales sur le Site A.

Ouvrez la fenêtre de relais commande Oracle GoldenGate et modifiez le fichier globales sur Site a :

    GGSCI (MachineGG1) 1> EDIT PARAMS ./GLOBALS
    Add the CHECKPOINTTABLE parameter to the existing GLOBALS file as follows:
    GGSCHEMA ggate
    CHECKPOINTTABLE ggate.checkpointtable

Enregistrez et fermez le fichier de paramètre global.

###<a name="add-replicat-on-site-a"></a>Ajouter REPLICAT sur un Site

Cette section décrit comment ajouter un processus REPLICAT « REP1 » sur le Site A.

La commande suivante crée une rep1 groupe Replicat avec le nom d’une trace, la checkpointtable associé.

    GGSCI (MachineGG1) 21> add replicat rep1 exttrail C:\OracleGG\dirdat\ad,checkpointtable ggate.checkpointtable
     REPLICAT added.

Ouvrez le fichier de paramètres à l’aide de la commande modifier les paramètres, puis ajoutez les informations suivantes :

    GGSCI (MachineGG1) 10> edit params rep1
    REPLICAT rep1
    ASSUMETARGETDEFS
    USERID ggate, PASSWORD ggate
    DISCARDFILE C:\OracleGG\dirdat\discard.txt, append, megabytes 10
    MAP scott.inventory, TARGET scott.inventory;

### <a name="add-trandata-on-site-a-and-site-b"></a>Ajouter trandata sur le Site A et B de Site

Activer la journalisation supplémentaire au niveau de la table à l’aide de la commande Ajouter TRANDATA. Ouvrez la fenêtre de relais Oracle GoldenGate commande, connectez-vous à la base de données et puis exécutez la commande Ajouter TRANDATA.

Bureau à distance à MachineGG1, ouvrez interprète de commande Oracle GoldenGate et exécutez :

    GGSCI (MachineGG1) 11> dblogin userid ggate password ggate
     Successfully logged into database.
    GGSCI (MachineGG1) 12> add trandata scott.inventory cols (prod_category,qty_in_stock, last_dml)
    GGSCI (MachineGG1) 13> info trandata scott.inventory
    Logging of supplemental redo log data is enabled for table SCOTT.INVENTORY.
    Columns supplementally logged for table SCOTT.INVENTORY: PROD_ID, PROD_CATEGORY, QTY_IN_STOCK, LAST_DML.

Bureau à distance à MachineGG2, ouvrez interprète de commande Oracle GoldenGate et exécutez :

    GGSCI (MachineGG2) 18> dblogin userid ggate password ggate
     Successfully logged into database.
    GGSCI (MachineGG2) 14> add trandata scott.inventory cols (prod_category,qty_in_stock, last_dml)
    Logging of supplemental redo data enabled for table SCOTT.INVENTORY.

Affichage d’informations sur l’état d’au niveau des tables supplémentaire d’enregistrement :

    GGSCI (MachineGG2) 15> info trandata scott.inventory
    Logging of supplemental redo log data is enabled for table SCOTT.INVENTORY.
    Columns supplementally logged for table SCOTT.INVENTORY: PROD_ID, PROD_CATEGORY, QTY_IN_STOCK, LAST_DML.

###<a name="add-trandata-on-site-a-and-site-b"></a>Ajouter trandata sur le Site A et B de Site

Activer la journalisation supplémentaire au niveau de la table à l’aide de la commande Ajouter TRANDATA. Ouvrez la fenêtre de relais Oracle GoldenGate commande, connectez-vous à la base de données et puis exécutez la commande Ajouter TRANDATA.

Bureau à distance à MachineGG1, ouvrez interprète de commande Oracle GoldenGate et exécutez :

    GGSCI (MachineGG1) 11> dblogin userid ggate password ggate
     Successfully logged into database.
    GGSCI (MachineGG1) 12> add trandata scott.inventory cols (prod_category,qty_in_stock, last_dml)
    GGSCI (MachineGG1) 13> info trandata scott.inventory
    Logging of supplemental redo log data is enabled for table SCOTT.INVENTORY.
    Columns supplementally logged for table SCOTT.INVENTORY: PROD_ID, PROD_CATEGORY, QTY_IN_STOCK, LAST_DML.

Bureau à distance à MachineGG2, ouvrez interprète de commande Oracle GoldenGate et exécutez :

    GGSCI (MachineGG2) 18> dblogin userid ggate password ggate
     Successfully logged into database.
    GGSCI (MachineGG2) 14> add trandata scott.inventory cols (prod_category,qty_in_stock, last_dml)
    Logging of supplemental redo data enabled for table SCOTT.INVENTORY.
    Display information about the state of table-level supplemental logging:
    GGSCI (MachineGG2) 15> info trandata scott.inventory
    Logging of supplemental redo log data is enabled for table SCOTT.INVENTORY.
    Columns supplementally logged for table SCOTT.INVENTORY: PROD_ID, PROD_CATEGORY, QTY_IN_STOCK, LAST_DML.

###<a name="start-extract-and-data-pump-processes-on-site-a"></a>Démarrer des processus extraire et Data Pump sur un Site

Démarrer l’ext1 processus extrait sur Site a :

    GGSCI (MachineGG1) 31> start extract ext1
    Sending START request to MANAGER …
    EXTRACT EXT1 starting

Démarrer la dpump1 données pompe processus sur Site a :

    GGSCI (MachineGG1) 23> start extract dpump1
    Sending START request to MANAGER …
    EXTRACT DPUMP1 starting
Afficher des informations sur l’ext1 groupe extrait : GGSCI (MachineGG1) 32 > informations extraire ext1 extraire EXT1 dernière mise en route 2013-11-25 08:03 état en cours d’exécution du point de contrôle décalage 00:00:00 (mis à jour 00:00:02 il y a) journal en lecture du point de contrôle Oracle rétablir les journaux 2013-11-25 08:03:18 Seqno 6, RBA 3230720 SCN 0.1074371 (1074371)

Afficher l’état et un décalage (le cas échéant) pour tous les processus responsable, extraire et Replicat sur un système :

    GGSCI (MachineGG1) 16> info all
    Program     Status      Group       Lag at Chkpt  Time Since Chkpt

    MANAGER     RUNNING
    EXTRACT     RUNNING     DPUMP1      00:00:00      00:46:33
    EXTRACT     RUNNING     EXT1        00:00:00      00:00:04

###<a name="start-extract-and-data-pump-processes-on-site-b"></a>Démarrer des processus extraire et Data Pump sur Site B

Démarrer l’ext2 processus extrait sur Site b :

    GGSCI (MachineGG2) 22> start extract ext2
    Sending START request to MANAGER …
    EXTRACT EXT2 starting

Démarrer la dpump2 données pompe processus sur Site b :

    GGSCI (MachineGG2) 23> start extract dpump2
    Sending START request to MANAGER …
    EXTRACT DPUMP2 starting

Afficher l’état et un décalage (le cas échéant) pour tous les processus responsable, extraire et Replicat sur un système :

    GGSCI (ActiveGG2orcldb) 6> info all
    Program     Status      Group       Lag at Chkpt  Time Since Chkpt

    MANAGER     RUNNING
    EXTRACT     RUNNING     DPUMP2      00:00:00      136:13:33
    EXTRACT     RUNNING     EXT2        00:00:00      00:00:04

### <a name="start-replicat-process-on-site-a"></a>Démarrer le processus de REPLICAT sur un Site

Cette section décrit comment démarrer le processus REPLICAT « REP1 » sur le Site A.

Démarrer le processus Replicat sur Site a :

    GGSCI (MachineGG1) 38> start replicat rep1
    Sending START request to MANAGER …
    REPLICAT REP1 starting

Afficher le statut d’un groupe Replicat :

    GGSCI (MachineGG1) 39> status replicat rep1
     REPLICAT REP1: RUNNING

###<a name="start-replicat-process-on-site-b"></a>Démarrer le processus de REPLICAT sur le Site B

Cette section décrit comment démarrer le processus REPLICAT « REP2 » sur le Site B.

Démarrer le processus Replicat sur Site b :

    GGSCI (MachineGG2) 26> start replicat rep2
    Sending START request to MANAGER …
    REPLICAT REP2 starting

Afficher le statut d’un groupe Replicat :

    GGSCI (MachineGG2) 27> status replicat rep2
    REPLICAT REP2: RUNNING

##<a name="6-verify-the-bi-directional-replication-process"></a>6. Vérifiez le processus de réplication bidirectionnelle

Pour vérifier la configuration Oracle GoldenGate, insérez une ligne dans la base de données au Bureau à distance de Site a à Site A. Ouvrez SQL * Plus la fenêtre de commande et exécuter : SQL > nom de sélection de base de données v$ ;

    NAME
    ———
    TESTGG

    SQL> insert into inventory values  (100,’TV’,100,sysdate);

    1 row created.

    SQL> commit;

    Commit complete.

Ensuite, vérifiez si cette ligne est répliquée sur le Site B. Pour ce faire, Bureau à distance sur Site B. ouvrir SQL Plus fenêtre vers le haut et exécuter :

    SQL> select name from v$database;

    NAME
    ———
    TESTGG

    SQL> select * from inventory;

    PROD_ID PROD_CATEGORY QTY_IN_STOCK LAST_DML
    ———- ——————– ———— ———
    100 TV 100 22-MAR-13

Insérer un nouvel enregistrement au Site b :

    SQL> insert into inventory  values  (101,’DVD’,10,sysdate);
    1 row created.

    SQL> commit;

    Commit complete.

Bureau à distance sur un Site et vérifiez si la réplication a eu lieu :

    SQL> select * from inventory;

    PROD_ID PROD_CATEGORY QTY_IN_STOCK LAST_DML
    ———- ——————– ———— ———
    100 TV 100 22-MAR-13
    101 DVD 10 22-MAR-13

##<a name="additional-resources"></a>Ressources supplémentaires
[Images de Machine virtuelle Oracle pour Azure](virtual-machines-linux-classic-oracle-images.md)

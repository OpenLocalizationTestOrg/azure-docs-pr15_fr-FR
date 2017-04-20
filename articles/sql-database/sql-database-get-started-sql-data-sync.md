<properties
    pageTitle="Mise en route de la synchronisation de données de bases de données SQL"
    description="Ce didacticiel vous permet de commencer à utiliser la synchronisation de données SQL Azure (Preview)."
    services="sql-database"
    documentationCenter=""
    authors="jennieHubbard"
    manager="jhubbard"
    editor=""/>

<tags
    ms.service="sql-database"
    ms.workload="data-management"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/11/2016"
    ms.author="jhubbard"/>


#<a name="getting-started-with-azure-sql-data-sync-preview"></a>Mise en route de la synchronisation de données SQL Azure (Preview)
Dans ce didacticiel, vous découvrez les principes de base de la synchronisation de données SQL Azure à l’aide du portail classique Azure.

Ce didacticiel suppose minimale expérience préalable avec SQL Server et base de données SQL Azure. Dans ce didacticiel, vous créez un groupe de synchronisation hybride (SQL Server et instances de base de données SQL) entièrement configuré et synchronisé sur la planification définie.

> [AZURE.NOTE] La documentation technique complète pour la synchronisation des données SQL Azure, qui se trouvaient auparavant sur MSDN, est disponible dans un fichier .pdf. Téléchargez-le [ici](http://download.microsoft.com/download/4/E/3/4E394315-A4CB-4C59-9696-B25215A19CEF/SQL_Data_Sync_Preview.pdf).

## <a name="step-1-connect-to-the-azure-sql-database"></a>Étape 1 : Se connecter à la base de données SQL Azure

1. Connectez-vous au [portail classique](http://manage.windowsazure.com).

2. Dans le volet gauche, cliquez sur **Bases de données SQL** .

3. Cliquez sur **synchronisation** en bas de la page. Lorsque vous cliquez sur Synchroniser, une liste s’affiche présente les choses que vous pouvez ajouter - **Nouveau groupe de synchronisation** et **Nouvel Agent de synchronisation**.

4. Pour lancer l’Assistant nouvel Agent de synchronisation de données SQL, cliquez sur **Nouvel Agent de synchronisation**.

5. Si vous n’avez pas ajouté un agent avant, **cliquez sur Télécharger ici**.

    ![Image1](./media/sql-database-get-started-sql-data-sync/SQLDatabaseScreen-Figure1.PNG)


## <a name="step-2-add-a-client-agent"></a>Étape 2 : Ajouter un Agent de Client
Cette étape est requise uniquement si vous vous apprêtez à une base de données de SQL Server locale inclus dans votre groupe de synchronisation. Si votre groupe de synchronisation comporte uniquement les instances de base de données SQL, passez à l’étape 4.

<a id="InstallRequiredSoftware"></a>
### <a name="step-2a-install-the-required-software"></a>Étape 2 : installer le logiciel requis
N’oubliez pas que vous avez les éléments suivants sur l’ordinateur sur lequel vous installez l’Agent du Client.

- **.NET framework 4.0**

 Installez .NET Framework 4.0 à partir [d’ici](http://go.microsoft.com/fwlink/?linkid=205836).

- **Microsoft SQL Server 2008 R2 SP1 Types CLR du système (x86)**

 Installer les Microsoft SQL Server 2008 R2 SP1 Types CLR du système (x86) [ici](http://www.microsoft.com/download/en/details.aspx?id=26728)

- **Microsoft SQL Server 2008 R2 SP1 partagés gestion des objets (x86)**

 Installer Microsoft SQL Server 2008 R2 SP1 Shared Management Objects (x86) [ici](http://www.microsoft.com/download/en/details.aspx?id=26728)



<a id="InstallClient"></a>
### <a name="step-2b-install-a-new-client-agent"></a>Étape 2 b : installer un nouvel Agent de Client

Suivez les instructions dans [installer un Agent du Client (synchronisation de données SQL)](http://download.microsoft.com/download/4/E/3/4E394315-A4CB-4C59-9696-B25215A19CEF/SQL_Data_Sync_Preview.pdf) pour installer l’agent.



<a id="RegisterSSDb"></a>
### <a name="step-2c-finish-the-new-sql-data-sync-agent-wizard"></a>Étape 2c : fin de l’Assistant nouvel Agent de synchronisation de données SQL

1.  Revenir à l’Assistant nouvel Agent de synchronisation de données SQL.
2.  Donnez un nom significatif à l’agent.
3.  Dans la liste déroulante, sélectionnez la **région** (centre de données) pour héberger cet agent.
4.  Dans la liste déroulante, sélectionnez l' **abonnement** pour héberger cet agent.
5.  Cliquez sur la flèche droite.



## <a name="step-3-register-a-sql-server-database-with-the-client-agent"></a>Étape 3 : Enregistrer une base de données SQL Server avec l’Agent du Client

Après que l’Agent du Client est installé, enregistrez chaque base de données SQL Server en local que vous souhaitez inclure dans un groupe de synchronisation avec l’agent.
Pour enregistrer une base de données avec l’agent, suivez les instructions à [Enregistrer une base de données SQL Server avec un Agent Client](http://download.microsoft.com/download/4/E/3/4E394315-A4CB-4C59-9696-B25215A19CEF/SQL_Data_Sync_Preview.pdf).



## <a name="step-4-create-a-sync-group"></a>Étape 4 : Créer un groupe de synchronisation


<a id="StartNewSGWizard"></a>
### <a name="step-4a-start-the-new-sync-group-wizard"></a>Étape 4 : démarrer l’Assistant Nouveau groupe de synchronisation

1.  Revenez au [portail classique](http://manage.windowsazure.com).
2.  Cliquez sur **bases de données SQL**.
3.  Cliquez sur **Ajouter la synchronisation** dans la partie inférieure de la page, puis sélectionnez Nouveau groupe de synchronisation dans le bac d’alimentation.

    ![Image2](./media/sql-database-get-started-sql-data-sync/NewSyncGroup-Figure2.png)



<a id=""></a>
### <a name="step-4b-enter-the-basic-settings"></a>Étape 4 b : entrer les paramètres de base


1.  Entrez un nom significatif pour le groupe de synchronisation.
2.  Dans la liste déroulante, sélectionnez la **région** (Data Center) pour héberger ce groupe de synchronisation.
3. Cliquez sur la flèche droite.

    ![Sa stratégie partenaires3](./media/sql-database-get-started-sql-data-sync/NewSyncGroupName-Figure3.PNG)


<a id="DefineHubDB"></a>
### <a name="step-4c-define-the-sync-hub"></a>Étape 4c : définissez le hub de synchronisation

1. Dans la liste déroulante, sélectionnez l’instance SQL de base de données à utiliser comme concentrateur de groupe de synchronisation.
2. Entrez les informations d’identification pour cette instance de base de données SQL - **Concentrateur nom d’utilisateur** et **Mot de passe concentrateur**.
3. Attendre la synchronisation de données SQL confirmer le nom d’utilisateur et mot de passe. Vous verrez une coche verte apparaît à droite du mot de passe lorsque les informations d’identification sont confirmées.
4. Dans la liste déroulante, sélectionnez la stratégie de **Résolution des conflits** .

 **Concentrateur Wins** - toute modification écrite sur l’écriture de base de données concentrateur pour les bases de données de référence, remplacement des modifications dans la même faire référence à la base de données enregistrement. Point de vue fonctionnel, cela signifie que la première modification écrite dans le hub est répercuté dans les autres bases de données.


 **Client Wins** - modifications écrites dans le hub sont remplacées par les modifications dans les bases de référence. Point de vue fonctionnel, cela signifie que la dernière modification écrite dans le hub est celle conservés et propagée aux autres bases de données.

5.  Cliquez sur la flèche droite.

    ![Image4](./media/sql-database-get-started-sql-data-sync/NewSyncGroupHub-Figure4.PNG)

<a id="AddRefDB"></a>
### <a name="step-4d-add-a-reference-database"></a>Étape 4d : ajouter une base de données de référence


Répétez cette étape pour chaque base de données supplémentaire que vous voulez ajouter au groupe de synchronisation.

1. Dans la liste déroulante, sélectionnez la base de données à ajouter.

    Bases de données dans la liste déroulante incluent deux bases de données SQL Server qui ont été enregistrés avec l’agent et instances de base de données SQL.
2.  Entrez les informations d’identification pour cette base de données - **nom d’utilisateur** et **mot de passe**.
3.  Dans la liste déroulante, sélectionnez le **Sens de la synchronisation** de cette base de données.

    **Bidirectionnelle** - modifications dans la base de données de référence sont écrits sur la base de données concentrateur, et les modifications apportées à la base de données concentrateur sont écrits sur la base de données de référence.

    **Synchronisation à partir du Hub** - la base de données reçoit les mises à jour à partir du Hub. Il n’envoie pas de modifications au concentrateur.

    **Synchroniser avec le Hub** - la base de données envoie des mises à jour au concentrateur. Modifications dans le Hub ne sont pas écrites à cette base de données.

4.  Pour terminer la création du groupe de synchronisation, cliquez sur la coche située dans le coin inférieur droit de l’Assistant. Attendez que la synchronisation de données SQL confirmer les informations d’identification. Une coche verte indique que les informations d’identification sont confirmées.

5.  Activez la case à cocher une seconde fois. Vous revenez à la page de **synchronisation** sous bases de données SQL. Ce groupe de synchronisation est désormais répertorié avec d’autres groupes de synchronisation et agents.

    ![Image5](./media/sql-database-get-started-sql-data-sync/NewSyncGroupReference-Figure5.PNG)


## <a name="step-5-define-the-data-to-sync"></a>Étape 5 : Définir les données à synchroniser

Synchronisation de données SQL Azure vous permet de sélectionner des tables et des colonnes à synchroniser. Si vous souhaitez également filtrer une colonne afin que seules les lignes avec des valeurs spécifiques (par exemple, l’âge > = 65) synchroniser, utilisez le portail de synchronisation de données SQL en Azure et la documentation à sélectionner les Tables, colonnes et lignes à synchroniser pour définir les données à synchroniser.

1.  Revenez au [portail classique](http://manage.windowsazure.com).
2.  Cliquez sur **bases de données SQL**.
3.  Cliquez sur l’onglet **synchronisation** .
4.  Cliquez sur le nom de ce groupe de synchronisation.
5.  Cliquez sur l’onglet **Règles de synchronisation** .
6.  Sélectionnez la base de données que vous souhaitez fournir le schéma de groupe de synchronisation.
7.  Cliquez sur la flèche droite.
8.  Cliquez sur **Actualiser le schéma**.
9.  Pour chaque table dans la base de données, sélectionnez les colonnes à inclure dans les synchronisations.
    - Impossible de sélectionner les colonnes avec des types de données non prises en charge.
    - Si aucune colonne dans une table n’est sélectionnés, le tableau n’est pas inclus dans le groupe de synchronisation.
    - Pour sélectionner/désélectionner toutes les tables, cliquez sur Sélectionner dans la partie inférieure de l’écran.
10. Cliquez sur **Enregistrer**, puis patientez pour le groupe de synchronisation terminer la mise en service.
11. Pour revenir à la page d’accueil de synchronisation de données, cliquez sur la flèche précédent dans le coin supérieur gauche de l’écran (au-dessus de la synchronisation du nom de groupe).

    ![Image6](./media/sql-database-get-started-sql-data-sync/NewSyncGroupSyncRules-Figure6.PNG)

## <a name="step-6-configure-your-sync-group"></a>Étape 6 : Configurer votre groupe de synchronisation

Vous pouvez toujours synchroniser un groupe de synchronisation en cliquant sur synchronisation en bas de la page de lancement de synchronisation de données.
Pour synchroniser sur un planning, vous configurez le groupe de synchronisation.

1.  Revenez au [portail classique](http://manage.windowsazure.com).
2.  Cliquez sur **bases de données SQL**.
3.  Cliquez sur l’onglet **synchronisation** .
4.  Cliquez sur le nom de ce groupe de synchronisation.
5.  Cliquez sur l’onglet **configurer** .
6.  **SYNCHRONISATION AUTOMATIQUE**
    - Pour configurer le groupe de synchronisation pour synchroniser sur une fréquence définie, cliquez sur **activé**. Vous pouvez toujours synchroniser à la demande en cliquant sur Synchroniser.
    - Cliquez sur **désactivé** pour configurer le groupe de synchronisation pour synchroniser uniquement lorsque vous cliquez sur Synchroniser.
7.  **FRÉQUENCE DE SYNCHRONISATION**
    - Si la synchronisation automatique est activé, définissez la fréquence de synchronisation. La fréquence doit être comprise entre 5 Minutes et 1 mois.
8.  Cliquez sur **Enregistrer**.

![Image7](./media/sql-database-get-started-sql-data-sync/NewSyncGroupConfigure-Figure7.PNG)

Félicitations. Vous avez créé un groupe de synchronisation qui inclut une instance de base de données SQL et une base de données SQL Server.

## <a name="next-steps"></a>Étapes suivantes
Pour plus d’informations sur la base de données SQL et la synchronisation de données SQL, consultez :

* [Téléchargez la documentation technique complète de la synchronisation de données SQL](http://download.microsoft.com/download/4/E/3/4E394315-A4CB-4C59-9696-B25215A19CEF/SQL_Data_Sync_Preview.pdf)
* [Vue d’ensemble de la base de données SQL](sql-database-technical-overview.md)
* [Gestion du cycle de vie de base de données](https://msdn.microsoft.com/library/jj907294.aspx)
 

 

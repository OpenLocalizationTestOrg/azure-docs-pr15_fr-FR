<properties
   pageTitle="Analyser les journaux de site Web à l’aide d’Azure données Lake Analytique | Azure"
   description="Découvrez comment analyser les journaux de site Web à l’aide de données Lake Analytique. "
   services="data-lake-analytics"
   documentationCenter=""
   authors="edmacauley"
   manager="jhubbard"
   editor="cgronlun"/>

<tags
   ms.service="data-lake-analytics"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="05/16/2016"
   ms.author="edmaca"/>

# <a name="tutorial-analyze-website-logs-using-azure-data-lake-analytics"></a>Didacticiel : Analyse des journaux de site Web à l’aide d’Azure données Lake Analytique

Découvrez comment analyser les journaux de site Web à l’aide de données Lake Analytique, en particulier sur découvrir les renvois rencontré des erreurs quand ils ont essayé de visitez le site Web.

>[AZURE.NOTE] Si vous souhaitez simplement afficher l’application, aller plus vite traitée [didacticiels interactifs utilisation Azure données Lake Analytique](data-lake-analytics-use-interactive-tutorials.md). Ce didacticiel est basé sur le même scénario et le même code. L’objectif de ce didacticiel consiste à permettre aux développeurs de l’expérience de création et l’exécution d’une application de données Lake Analytique de bout en bout.

## <a name="prerequisites"></a>Conditions requises :

- **Visual Studio 2015 Visual Studio 2013 mise à jour et 4, ou Visual Studio 2012 avec Visual C++ installé**.
- **Kit de développement Microsoft Azure pour .NET version 2.5 ou au-dessus**.  Installer à l’aide du [programme d’installation de la plateforme Web](http://www.microsoft.com/web/downloads/platform.aspx).
- **[Données Lake Tools pour Visual Studio](http://aka.ms/adltoolsvs)**.

    Une fois que les données Lake Tools pour Visual Studio est installé, vous verrez un menu **Données Lake** dans Visual Studio :

    ![Menu U-SQL Visual Studio](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-analytics-data-lake-tools-menu.png)

- **Connaissance de base de données Lake Analytique et données Lake Tools pour Visual Studio**. Pour commencer, voir :

    - [Prise en main Azure données Lake Analytique à l’aide du portail Azure](data-lake-analytics-get-started-portal.md).
    - [Script de développer U-SQL à l’aide de données Lake tools pour Visual Studio](data-lake-analytics-data-lake-tools-get-started.md).

- **Un compte données Lake Analytique.**  Voir [Création d’un compte Azure données Lake Analytique](data-lake-analytics-get-started-portal.md#create_adl_analytics_account).

    Les outils de Lake données non prises en charge la création de comptes données Lake Analytique.  Vous devez donc créer à l’aide du portail Azure, Azure PowerShell, Kit de développement .NET ou Azure infrastructure du langage commun.
- **Téléchargez les exemples de données sur le compte de données Lake Analytique.** Voir [Télécharger SearchLog.tsv sur le compte de stockage des données Lake par défaut](data-lake-analytics-get-started-portal.md#update-data-to-the-default-adl-storage-account).

    Pour exécuter une tâche de données Lake Analytique, vous avez besoin des données. Même si les outils de Lake données prend en charge télécharger les données, vous allez utiliser le portail pour charger les exemples de données pour faciliter la suivez ce didacticiel.

## <a name="connect-to-azure"></a>Se connecter à Azure

Avant de pouvoir créer et tester les scripts U-SQL, vous devez d’abord vous connecter à Azure.

**Pour vous connecter à des données Lake Analytique**

1. Ouvrez Visual Studio.
2. Dans le menu **Lake de données** , cliquez sur **Options et paramètres**.
4. Cliquez sur **Se connecter**ou **Modifier un utilisateur** si une personne a connecté, puis suivez les instructions.
5. Cliquez sur **OK** pour fermer la boîte de dialogue Options et paramètres.

**Pour parcourir vos comptes de données Lake Analytique**

1. À partir de Visual Studio, ouvrez **l’Explorateur de serveur** en appuyez sur **CTRL + ALT + S**.
2. Dans l' **Explorateur de serveurs**, puis développez **Azure**et **Données Lake Analytique**. Vous doit afficher la liste de vos comptes de données Lake Analytique le cas échéant. Vous ne pouvez pas créer des comptes de données Lake Analytique à partir de la studio. Pour créer un compte, voir [Prise en main Azure données Lake Analytique à l’aide du portail Azure](data-lake-analytics-get-started-portal.md) ou [Prise en main Azure données Lake Analytique à l’aide de PowerShell Azure](data-lake-analytics-get-started-powershell.md).

## <a name="develop-u-sql-application"></a>Développer application U-SQL

Une application U-SQL est principalement un script U-SQL. Pour en savoir plus sur U-SQL, voir [prise en main U-SQL](data-lake-analytics-u-sql-get-started.md).

Vous pouvez ajouter des opérateurs d’addition définies par l’utilisateur à l’application.  Pour plus d’informations, voir [opérateurs pour les tâches de données Lake Analytique définis par l’utilisateur de développer U-SQL](data-lake-analytics-u-sql-develop-user-defined-operators.md).

**Pour créer et soumettre une tâche de données Lake Analytique**

1. Dans le menu **fichier** , cliquez sur **Nouveau**, puis cliquez sur **projet**.
2. Sélectionnez le type de projet U-SQL.

    ![nouveau projet U-SQL Visual Studio](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-analytics-data-lake-tools-new-project.png)

3. Cliquez sur **OK**. Visual studio crée une solution avec un fichier Script.usql.
4. Entrez le script suivant dans le fichier Script.usql :

        // Create a database for easy reuse, so you don't need to read from a file every time.
        CREATE DATABASE IF NOT EXISTS SampleDBTutorials;

        // Create a Table valued function. TVF ensures that your jobs fetch data from the weblog file with the correct schema.
        DROP FUNCTION IF EXISTS SampleDBTutorials.dbo.WeblogsView;
        CREATE FUNCTION SampleDBTutorials.dbo.WeblogsView()
        RETURNS @result TABLE
        (
            s_date DateTime,
            s_time string,
            s_sitename string,
            cs_method string,
            cs_uristem string,
            cs_uriquery string,
            s_port int,
            cs_username string,
            c_ip string,
            cs_useragent string,
            cs_cookie string,
            cs_referer string,
            cs_host string,
            sc_status int,
            sc_substatus int,
            sc_win32status int,
            sc_bytes int,
            cs_bytes int,
            s_timetaken int
        )
        AS
        BEGIN

            @result = EXTRACT
                s_date DateTime,
                s_time string,
                s_sitename string,
                cs_method string,
                cs_uristem string,
                cs_uriquery string,
                s_port int,
                cs_username string,
                c_ip string,
                cs_useragent string,
                cs_cookie string,
                cs_referer string,
                cs_host string,
                sc_status int,
                sc_substatus int,
                sc_win32status int,
                sc_bytes int,
                cs_bytes int,
                s_timetaken int
            FROM @"/Samples/Data/WebLog.log"
            USING Extractors.Text(delimiter:' ');
            RETURN;
        END;

        // Create a table for storing referrers and status
        DROP TABLE IF EXISTS SampleDBTutorials.dbo.ReferrersPerDay;
        @weblog = SampleDBTutorials.dbo.WeblogsView();
        CREATE TABLE SampleDBTutorials.dbo.ReferrersPerDay
        (
            INDEX idx1
            CLUSTERED(Year ASC)
            PARTITIONED BY HASH(Year)
        ) AS

        SELECT s_date.Year AS Year,
            s_date.Month AS Month,
            s_date.Day AS Day,
            cs_referer,
            sc_status,
            COUNT(DISTINCT c_ip) AS cnt
        FROM @weblog
        GROUP BY s_date,
                cs_referer,
                sc_status;

    Pour mieux comprendre l’U-SQL, consultez [prise en main langage données Lake Analytique U-SQL](data-lake-analytics-u-sql-get-started.md).    

5. Ajouter un nouveau script U SQL à votre projet et entrez les informations suivantes :

        // Query the referrers that ran into errors
        @content =
            SELECT *
            FROM SampleDBTutorials.dbo.ReferrersPerDay
            WHERE sc_status >=400 AND sc_status < 500;

        OUTPUT @content
        TO @"/Samples/Outputs/UnsuccessfulResponses.log"
        USING Outputters.Tsv();

6. Passer au premier script U SQL et en regard du bouton **Envoyer** , spécifiez votre compte Analytique.
7. À partir **L’Explorateur de solutions**, cliquez avec le bouton droit sur **Script.usql**, puis cliquez sur **Créer un Script**. Vérifier les résultats dans le volet résultat.
8. À partir **L’Explorateur de solutions**, cliquez avec le bouton droit sur **Script.usql**, puis cliquez sur **Envoyer le Script**.
9. Vérifiez que le **Compte Analytique** est celle où vous voulez exécuter la tâche, puis cliquez sur **Envoyer**. Résultats de l’envoi et de liaison de tâche sont disponibles dans les données Lake fenêtre Tools for Visual Studio se traduit par lorsque la présentation est terminée.
10. Attendez que la tâche est terminée avec succès.  Si la tâche a échoué, le fichier source est probablement manquant.  Consultez la section prérequises de ce didacticiel. Pour plus d’informations, voir [moniteur et résoudre les problèmes de travaux Azure données Lake Analytique](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorial.md).

    Lorsque la tâche est terminée, vous doit afficher l’écran suivant :

    ![analytique de lake données analyse les journaux de site Web de blogs](./media/data-lake-analytics-analyze-weblogs/data-lake-analytics-analyze-weblogs-job-completed.png)

11. Maintenant Répétez les étapes 7 à 10 pour **Script1.usql**.

>[AZURE.NOTE]Vous ne pouvez pas lire ou écrire dans une table U-SQL qui a été créée ou modifiée dans le même script.  C’est pourquoi utiliser deux scripts pour cet exemple.

**Pour afficher la sortie des tâches**

1. Dans l' **Explorateur de serveurs**, développez **Azure**, développez **Données Lake Analytique**, développer votre compte données Lake Analytique, développer des **Comptes de stockage**, double-cliquez sur le compte de stockage des données Lake par défaut, puis cliquez sur **Explorer**.
2.  Double-cliquez sur **échantillons** pour ouvrir le dossier, puis sur **sorties**.
3.  Double-cliquez sur **UnsuccessfulResponsees.log**.
4.  Vous pouvez également double-cliquer le fichier de copie à l’intérieur de l’affichage Graphe du travail afin d’accéder directement à la sortie.

## <a name="see-also"></a>Voir aussi

Pour commencer à utiliser les données Lake Analytique à l’aide des différents outils, voir :

- [Prise en main données Lake Analytique à l’aide du portail Azure](data-lake-analytics-get-started-portal.md)
- [Prise en main données Lake Analytique à l’aide de PowerShell Azure](data-lake-analytics-get-started-powershell.md)
- [Prise en main données Lake Analytique à l’aide du Kit de développement .NET](data-lake-analytics-get-started-net-sdk.md)

Pour afficher d’autres rubriques de développement :

- [Développer des scripts U-SQL à l’aide de Lake Data Tools pour Visual Studio](data-lake-analytics-data-lake-tools-get-started.md)
- [Prise en main langue données Lake Analytique U-SQL Azure](data-lake-analytics-u-sql-get-started.md)
- [Développer des opérateurs U-SQL sont définis par l’utilisateur pour les tâches de données Lake Analytique](data-lake-analytics-u-sql-develop-user-defined-operators.md)

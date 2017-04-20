<properties
   pageTitle="Utiliser des flux de données Azure Analytique avec Data Warehouse SQL | Microsoft Azure"
   description="Conseils pour l’utilisation d’Azure flux Analytique avec Azure SQL Data Warehouse pour développer des solutions."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="kevinvngo"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="08/16/2016"
   ms.author="kevin;barbkess;sonyama"/>

# <a name="use-azure-stream-analytics-with-sql-data-warehouse"></a>Utiliser des flux de données Azure Analytique avec Data Warehouse SQL

Azure Analytique de flux de données est un service entièrement géré qui offre un traitement de l’événement complexes faible latence, hautement disponible, scalable sur diffusion en continu de données dans le cloud. Vous pouvez découvrir les notions de base en lisant l’article [Introduction aux Azure flux Analytique][]. Vous pouvez puis apprenez à créer une solution de bout en bout avec flux Analytique en suivant le didacticiel [commencer à utiliser Azure flux Analytique][] .

Dans cet article, vous allez apprendre à utiliser votre base de données SQL Azure Data Warehouse comme un récepteur de sortie pour vos travaux vapeur Analytique.

## <a name="prerequisites"></a>Conditions préalables

Tout d’abord, exécutez les étapes suivantes dans le didacticiel [commencer à utiliser Azure flux Analytique][] .  

1. Créer une entrée d’événement concentrateur
2. Configurer et démarrer l’application Générateur d’événements
3. Mise en service d’une tâche de flux de données Analytique
4. Spécifier d’entrée de tâche et de requête

Ensuite, créez une base de données SQL Azure Data Warehouse

## <a name="specify-job-output-azure-sql-data-warehouse-database"></a>Spécifier la sortie des tâches : base de données SQL Azure Data Warehouse

### <a name="step-1"></a>Étape 1

Dans votre tâche de flux de données Analytique cliquez sur **sortie** à partir du haut de la page, puis cliquez sur **Ajouter une sortie**.

### <a name="step-2"></a>Étape 2

Sélectionnez la base de données SQL et cliquez sur Suivant.

![][add-output]

### <a name="step-3"></a>Étape 3
Dans la page suivante, entrez les valeurs suivantes :

- *Alias de sortie*: entrez un nom convivial pour cette sortie des tâches.
- *Abonnement*:
    - Si votre base de données SQL Data Warehouse se trouve dans le même abonnement en tant que la tâche de flux de données Analytique, sélectionnez Use base de données SQL à partir d’abonnement actif.
    - Si votre base de données se trouve dans un autre abonnement, sélectionnez Use base de données SQL à partir d’un autre abonnement.
- *Base de données*: indiquez le nom d’une base de données de destination.
- *Nom du serveur*: spécifiez le nom du serveur pour la base de données que vous venez d’indiquer. Vous pouvez utiliser le portail classique Azure pour trouver cette.

![][server-name]

- *Nom d’utilisateur*: spécifiez le nom d’utilisateur d’un compte disposant des autorisations d’écriture pour la base de données.
- *Mot de passe*: fournir le mot de passe pour le compte d’utilisateur spécifié.
- *Tableau*: spécifiez le nom de la table cible dans la base de données.

![][add-database]

### <a name="step-4"></a>Étape 4

Cliquez sur le bouton de vérification pour ajouter cette sortie des tâches et vérifiez que flux Analytique pouvez vous connecter à la base de données.

![][test-connection]

Lorsque la connexion à la base de données a réussi, vous verrez une notification dans la partie inférieure du portail de. Vous pouvez cliquer sur Tester la connexion dans la partie inférieure pour tester la connexion à la base de données.

## <a name="next-steps"></a>Étapes suivantes

Pour une vue d’ensemble de l’intégration, voir [vue d’ensemble de l’intégration de SQL Data Warehouse][].

Pour obtenir d’autres conseils de développement, voir [vue d’ensemble du développement Data Warehouse SQL][].

<!--Image references-->

[add-output]: ./media/sql-data-warehouse-integrate-azure-stream-analytics/add-output.png
[server-name]: ./media/sql-data-warehouse-integrate-azure-stream-analytics/dw-server-name.png
[add-database]: ./media/sql-data-warehouse-integrate-azure-stream-analytics/add-database.png
[test-connection]: ./media/sql-data-warehouse-integrate-azure-stream-analytics/test-connection.png

<!--Article references-->

[Présentation des flux de données Azure Analytique]: ../stream-analytics/stream-analytics-introduction.md
[Commencer à utiliser Azure flux Analytique]: ../stream-analytics/stream-analytics-get-started.md
[Vue d’ensemble du développement Data Warehouse SQL]:  ./sql-data-warehouse-overview-develop.md
[Présentation de l’intégration Data Warehouse SQL]:  ./sql-data-warehouse-overview-integrate.md

<!--MSDN references-->

<!--Other Web references-->
[Azure Stream Analytics documentation]: http://azure.microsoft.com/documentation/services/stream-analytics/

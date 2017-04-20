<properties
   pageTitle="L’utilisation du connecteur SQL dans les applications logique | Service d’application Microsoft Azure"
   description="Comment créer et configurer l’application Connecteur SQL ou API et l’utiliser dans une application logique dans le Service d’application Azure"
   services="logic-apps"
   documentationCenter=".net,nodejs,java"
   authors="anuragdalmia"
   manager="erikre"
   editor=""/>

<tags
   ms.service="logic-apps"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration"
   ms.date="09/01/2016"
   ms.author="sameerch"/>


# <a name="get-started-with-the-microsoft-sql-connector-and-add-it-to-your-logic-app"></a>Prise en main du connecteur SQL Microsoft et l’ajouter à votre application logique
>[AZURE.NOTE] Cette version de l’article s’applique à la version de schéma logique applications 2014-12-01-preview. Pour la version de schéma Azure SQL 2015-08-01-aperçu, cliquez sur [L’API SQL Azure](../connectors/connectors-create-api-sqlazure.md).

Connectez-vous à un local SQL Server ou une base de données SQL Azure pour créer et modifier vos informations ou données. Connecteurs peuvent servir dans les applications logique pour récupérer, processus, ou transmission des données dans le cadre d’un « flux de travail ». Lorsque vous utilisez le connecteur SQL dans votre flux de travail, vous pouvez obtenir une variété de scénarios. Par exemple, vous pouvez :

- Exposer une section de données résidant dans votre base de données SQL à l’aide d’un site ou une application mobile.
- Insérer des données dans une table de base de données SQL pour le stockage. Par exemple, vous pouvez entrer des enregistrements employé, mettre à jour des commandes client et ainsi de suite.
- Obtenir des données à partir de SQL et l’utiliser dans un processus d’entreprise. Par exemple, vous pouvez obtenir des enregistrements des clients et placer les enregistrements des clients SalesForce.

Vous pouvez ajouter le connecteur SQL à vos données de flux de travail et processus métier dans le cadre de ce flux de travail au sein d’une application logique. 

## <a name="triggers-and-actions"></a>Déclencheurs et Actions
*Déclencheurs* sont des événements qui se produisent. Par exemple, lorsqu’une commande est mis à jour ou lorsqu’un nouveau client est ajouté. Une *Action* est le résultat d’un déclencheur. Par exemple, lorsqu’une commande est mis à jour, envoyer une alerte au commercial. Ou bien, lorsque vous ajoutez un nouveau client, envoyer un message électronique de Bienvenue au nouveau client.

Le connecteur SQL peut être utilisé comme un déclencheur ou une action dans une logique application et prend en charge des données aux formats JSON et XML. Pour chaque table inclus dans votre package de paramètres (plus plus loin dans cette rubrique), il est un ensemble d’actions JSON et un ensemble d’actions XML.

Le connecteur SQL contient les déclencheurs et les Actions disponibles suivants :

Déclencheurs | Actions
--- | ---
Données de sondage | <ul><li>Insérer dans la Table</li><li>Mettre à jour la Table</li><li>Sélectionnez à partir de Table</li><li>Supprimer la Table</li><li>Appel de procédure stockée</li></ul>

## <a name="create-the-sql-connector"></a>Créer le connecteur SQL

Un connecteur peut être créé dans une application logique ou être créé directement à partir de la Azure Marketplace. Pour créer un lien à partir de la place de marché :  

1. Dans la startboard Azure, sélectionnez **Marketplace**.
2. Recherchez « Connecteur SQL », sélectionnez-le, puis sélectionnez **créer**.
3. Entrez le nom, Plan de Service d’application et d’autres propriétés.
4. Entrez les paramètres de package suivants :

    Nom | Obligatoire |  Description
--- | --- | ---
Nom du serveur | Oui | Entrez le nom de SQL Server. Par exemple, entrez *SQL Server/sqlexpress* ou *SQLserver.mydomain.com*.
Port | N° | Valeur par défaut est 1433.
Nom d’utilisateur | Oui | Entrez un nom d’utilisateur qui peut se connecter au serveur SQL. Si vous vous connectez à un serveur SQL local, entrez les informations d’identification de l’authentification SQL.
Mot de passe | Oui | Entrez le mot de passe utilisateur.
Nom de la base de données | Oui | Entrez la base de données que vous vous connectez. Par exemple, vous pouvez entrer des *clients* ou *dbo/commandes*.
En local | Oui | Valeur par défaut est False. Entrez False si la connexion à une base de données SQL Azure. Entrez True si la connexion à un serveur SQL local.
Chaîne de connexion de service Bus | N° | Si vous vous connectez à en local, entrez la chaîne de connexion de relais Bus des services.<br/><br/>[L’utilisation du Gestionnaire de connexion hybride](app-service-logic-hybrid-connection-manager.md)<br/>[Tarifs Bus du service](https://azure.microsoft.com/pricing/details/service-bus/)
Nom du serveur partenaire | N° | Si le serveur principal n’est pas disponible, vous pouvez entrer un serveur partenaire comme un serveur secondaire ou de sauvegarde.
Tables | N° | Liste des tables de base de données pouvant être mis à jour par le connecteur. Par exemple, entrez *OrdersTable* ou *EmployeeTable*. Si aucune table n’est entrées, toutes les tables peuvent être utilisées. Tables valides et/ou des procédures stockées sont requises pour utiliser ce connecteur comme une action.
Procédures stockées | N° | Entrez une procédure stockée existante peut être appelée par le lien. Par exemple, entrez *sp_IsEmployeeEligible* ou *sp_CalculateOrderDiscount*. Tables valides et/ou des procédures stockées sont requises pour utiliser ce connecteur comme une action.
Requête disponibles de données | Prise en charge du déclencheur | Instruction SQL pour déterminer si des données seront disponibles pour l’interrogation d’une table de base de données SQL Server. Cela doit renvoyer une valeur numérique représentant le nombre de lignes de données disponibles. Exemple : Sélectionnez Count dans table_name.
Requête de données de sondage | Prise en charge du déclencheur | L’instruction SQL pour interroger la table de base de données SQL Server. Vous pouvez entrer un nombre quelconque d’instructions SQL séparées par un point-virgule. Cette instruction est exécutée transactionnel et validée uniquement lorsque les données sont stockées en toute sécurité dans votre application logique. Exemple : Sélectionnez *dans table_name ; SUPPRIMER à partir de table_name. <br/>**Note**<br/>Vous devez fournir une instruction de sondage afin d’éviter d’une boucle en supprimant, déplacement ou mise à jour des données sélectionnées pour vous assurer que les mêmes données n’est pas interrogées à nouveau.

5. Lorsque vous avez terminé, les paramètres du Package ressembler à ce qui suit :  
![][1]  

6. Sélectionnez **créer**. 


## <a name="use-the-connector-as-a-trigger"></a>Utiliser le connecteur comme un déclencheur
Examinons une application logique simple qui consulte les données d’une table SQL, ajoute les données dans une autre table et met à jour les données.

Pour utiliser le connecteur SQL comme un déclencheur, entrez les valeurs de **Données disponibles requête** et **Requête de données de sondage** . **Données disponibles requête** est exécutée sur l’échéancier vous permet d’entrer et détermine si les données sont disponibles. Étant donné que cette requête renvoie uniquement un nombre scalaire, peut être réglé et optimisée pour l’exécution fréquente.

**Sondage données requête** est exécutée uniquement lorsque la requête disponible données indique que les données sont disponibles. Cette déclaration s’exécute au sein d’une transaction et est uniquement validée lorsque les données extraites sont durablement stockées dans votre flux de travail. Il est important d’éviter infini extraire à nouveau les mêmes données. La nature transactions de cette exécution peut servir à supprimer ou mettre à jour les données pour vous assurer qu’il n’est pas collectées la prochaine fois que l’interrogation de données.

> [AZURE.NOTE] Le schéma retourné par cette instruction identifie les propriétés disponibles dans le lien. Toutes les colonnes doivent être nommés.

#### <a name="data-available-query-example"></a>Exemple de requête de données

    SELECT COUNT(*) FROM [Order] WHERE OrderStatus = 'ProcessedForCollection'

#### <a name="poll-data-query-example"></a>Exemple de requête de données de sondage

    SELECT *, GetData() as 'PollTime' FROM [Order]
        WHERE OrderStatus = 'ProcessedForCollection'
        ORDER BY Id DESC;
    UPDATE [Order] SET OrderStatus = 'ProcessedForFrontDesk'
        WHERE Id =
        (SELECT Id FROM [Order] WHERE OrderStatus = 'ProcessedForCollection' ORDER BY Id DESC)

### <a name="add-the-trigger"></a>Ajouter le déclencheur
1. Lorsque vous créez ou modification d’une application logique, sélectionnez le connecteur SQL que vous avez créé comme déclencheur. Cette liste répertorie les déclencheurs disponibles : **Données sondage (JSON)** et le **Sondage données (XML)**:  
![][5]

2. Sélectionnez le déclencheur **Sondage données JSON ()** , entrez la fréquence, puis cliquez sur le ✓ :  
![][6]

3. Le déclencheur apparaît désormais comme configuré dans l’application logique. Les sorties du déclencheur sont affichées et peuvent être utilisées comme entrées dans toutes les actions suivantes :  
![][7]

## <a name="use-the-connector-as-an-action"></a>Utiliser le lien sous forme d’Action
À l’aide de notre scénario d’application logique simple qui consulte les données d’une table SQL, ajoute les données dans une autre table, puis met à jour les données.

Pour utiliser le connecteur SQL comme une action, entrez le nom des Tables et/ou des procédures stockées que vous avez entré lorsque vous avez créé le connecteur SQL :

1. Une fois votre déclencheur (ou cliquez sur « Exécuter cette logique manuellement »), ajoutez le connecteur SQL créées à partir de la galerie. Sélectionnez une des actions insertion, comme *Insérer dans TempEmployeeDetails (JSON)*:  
![][8]

2. Entrez les valeurs d’entrée de l’enregistrement à insérer, puis cliquez sur le ✓ :  
![][9]

3. Dans la galerie, sélectionnez le même connecteur SQL que vous avez créé. Sous forme d’action, sélectionnez l’action de mise à jour sur la même table, comme *EmployeeDetails mise à jour*:  
![][11]

4. Entrez les valeurs d’entrée pour l’action de mise à jour et cliquez sur le ✓ :  
![][12]

Vous pouvez tester l’application logique en ajoutant un nouvel enregistrement dans la table qui est interrogée.

## <a name="what-you-can-and-cannot-do"></a>Ce que vous pouvez et ne pouvez pas faire

Requête SQL | Prise en charge | Non pris en charge
--- | --- | ---
Où clause | <ul><li>Opérateurs : Et, ou, =, <>, <, < =, >, > = et comme</li><li>Une ou plusieurs conditions sub peuvent être combinée par « (« et ») »</li><li>Littéraux de chaîne, date/heure (placé entre guillemets simples), numéros (doit contenir uniquement des caractères numériques)</li><li>Doit être strictement dans un format binaire expression, comme ((operand operator operand) et/ou (opérande d’opérateur opérande)) *</li></ul> | <ul><li>Opérateurs : Entre, IN</li><li>Toutes les fonctions intégrées, telles que ADD(), maintenant() MAX(), POWER() et ainsi de suite</li><li>Opérateurs mathématiques comme *, -, +, et ainsi de suite</li><li>Concaténations de chaînes à l’aide de +.</li><li>Toutes les jointures</li><li>EST NULL et n’est pas Null</li><li>Les nombres avec des caractères non numériques, tels que les nombres hexadécimales</li></ul>
Champs (dans une requête sélection) | <ul><li>Noms de colonne corrects séparées par des virgules. Aucune préfixes des noms de tableau n’autorisée (le fonctionnement du connecteur sur une seule table à la fois).</li><li>Noms peuvent être d’échappement avec ' [ » et «] »</li></ul> | <ul><li>Mots clés, tels que haut, DISTINCT et ainsi de suite</li><li>Alias, telles que la rue, ville + code postal comme adresse</li><li>Fonctions tout intégrées, telles que ADD(), maintenant() MAX(), POWER() et ainsi de suite</li><li>Opérateurs mathématiques, tels que *, -, +, et ainsi de suite</li><li>Concaténations de chaînes à l’aide de +</li></ul>

#### <a name="tips"></a>Conseils

- Pour les requêtes avancées, nous vous recommandons de création d’une procédure stockée et exécuter à l’aide de la procédure execute stockée API.
- Lorsque vous utilisez des requêtes internes, les utiliser dans des procédures stockées.
- Pour participer à une ou plusieurs conditions, vous pouvez utiliser l’opérateur « et » et opérateurs « Ou ».

## <a name="hybrid-configuration-optional"></a>Configuration hybride (facultative)

> [AZURE.NOTE] Cette étape est requise uniquement si vous utilisez SQL Server en local derrière le pare-feu.

Service d’application utilise le Gestionnaire de Configuration hybride pour vous connecter en toute sécurité à votre système local. Si vous êtes connecteur utilise un SQL Server en local, le Gestionnaire de connexions hybride est requis.

> [AZURE.NOTE] Si vous voulez commencer à utiliser les applications de logique Azure avant de vous inscrire pour un compte Azure, accédez à [Essayer l’application logique](https://tryappservice.azure.com/?appservice=logic), où vous pouvez créer une application de logique starter courtes immédiatement dans le Service d’application. Aucune carte de crédit obligatoire ; Aucune engagements.

Consultez [l’aide du Gestionnaire de connexion hybride](app-service-logic-hybrid-connection-manager.md).


## <a name="do-more-with-your-connector"></a>Faire plus avec le lien
Maintenant que le lien est créé, vous pouvez l’ajouter à un flux de travail d’entreprise à l’aide d’une application logique. Voir [que sont les applications logique ?](app-service-logic-what-are-logic-apps.md).

Afficher la référence Swagger des API REST en [liens et des API applications référence](http://go.microsoft.com/fwlink/p/?LinkId=529766).

Vous pouvez également consulter les statistiques et contrôler les performances de sécurité au connecteur. Voir [gérer et analyser vos applications API et les connecteurs intégrés](app-service-logic-monitor-your-connectors.md).


<!--Image references-->
[1]: ./media/app-service-logic-connector-sql/Create.png
[5]: ./media/app-service-logic-connector-sql/LogicApp1.png
[6]: ./media/app-service-logic-connector-sql/LogicApp2.png
[7]: ./media/app-service-logic-connector-sql/LogicApp3.png
[8]: ./media/app-service-logic-connector-sql/LogicApp4.png
[9]: ./media/app-service-logic-connector-sql/LogicApp5.png
[10]: ./media/app-service-logic-connector-sql/LogicApp6.png
[11]: ./media/app-service-logic-connector-sql/LogicApp7.png
[12]: ./media/app-service-logic-connector-sql/LogicApp8.png

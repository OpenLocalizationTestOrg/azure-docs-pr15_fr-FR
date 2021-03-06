<properties
   pageTitle="L’utilisation du connecteur Informix dans le Service d’application Microsoft Azure | Microsoft Azure"
   description="Comment utiliser le connecteur Informix avec actions et déclencheurs d’application logique"
   services="logic-apps"
   documentationCenter=".net,nodejs,java"
   authors="gplarsen"
   manager="erikre"
   editor=""/>

<tags
   ms.service="logic-apps"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration"
   ms.date="05/31/2016"
   ms.author="plarsen"/>

# <a name="informix-connector"></a>Connecteur Informix
>[AZURE.NOTE] Cette version de l’article s’applique à la version du schéma logique applications 2014-12-01-aperçu.

Connecteur Microsoft pour Informix est une application API pour connecter des applications Azure application service aux ressources stockées dans une base de données IBM Informix. Connecteur inclut un Client Microsoft pour vous connecter à des serveurs Informix à distance via une connexion de réseau TCP/IP, y compris les connexions hybride Azure aux serveurs de Informix local en utilisant le relais Azure Service Bus. Connecteur prend en charge les opérations de base de données suivantes :

- Sélectionnez les lignes en lecture à l’aide
- Sondage pour lire les lignes à l’aide de COUNT sélectionnez suivi sélectionnez
- Ajouter une ou plusieurs lignes (en bloc) à l’aide d’insertion
- Modifier une seule ligne ou plusieurs lignes (en bloc) à l’aide de la mise à jour
- Supprimer une ou plusieurs lignes (en bloc) en utilisant la touche
- Lu pour modifier les lignes à l’aide du curseur sélectionnez suivi par mise à jour l’emplacement actuel du curseur
- Lu pour supprimer les lignes à l’aide du curseur sélectionnez suivi par mise à jour l’emplacement actuel du curseur
- Exécuter une procédure avec des paramètres d’entrée et de sortie, la valeur de retour, jeu de résultats, à l’aide d’appel
- Commandes personnalisées et opérations composites à l’aide de sélectionner, insérer, mettre à jour, supprimer

## <a name="triggers-and-actions"></a>Déclencheurs et actions
Connecteur prend en charge les actions et déclencheurs d’application logique suivants :

Déclencheurs | Actions
--- | ---
<ul><li>Données de sondage</li></ul> | <ul><li>Insertion en bloc</li><li>Insérer</li><li>Mise à jour en bloc</li><li>Mise à jour</li><li>Appel</li><li>Supprimer en bloc</li><li>Supprimer</li><li>Sélectionnez</li><li>Mise à jour conditionnelle</li><li>Publier sur EntitySet</li><li>Supprimer conditionnelle</li><li>Sélectionnez entité unique</li><li>Supprimer</li><li>Upsert à EntitySet</li><li>Commandes personnalisées</li><li>Opérations de composite</li></ul>


## <a name="create-the-informix-connector"></a>Créer le lien Informix
Vous pouvez définir un lien dans une application logique ou à partir de la Azure Marketplace, comme dans l’exemple suivant :  

1. Dans la startboard Azure, sélectionnez **Marketplace**.
2. Dans la carte **tous les éléments** , tapez **informix** dans la zone **Rechercher dans tout** , puis cliquez sur la touche ENTRÉE.
3. Dans la recherche tout volet des résultats, sélectionnez **connecteur Informix**.
4. Dans la carte de description du connecteur Informix, sélectionnez **créer**.
5. Dans la carte de package de connecteur Informix, entrez le nom (par exemple, « InformixConnectorNewOrders »), Plan de Service d’application et d’autres propriétés.
6. Sélectionnez **paramètres du Package**, puis entrez les paramètres de package suivants.

    Nom | Obligatoire |  Description
--- | --- | ---
ConnectionString | Oui | Chaîne de connexion cliente Informix (par exemple, « adresse réseau = nom du serveur ; Port réseau = 9089 ; ID d’utilisateur = nom d’utilisateur ; Mot de passe = mot de passe ; catalogue Initial = Les Comptoirs ; schéma par défaut = informix »).
Tables | Oui | Liste séparées par des virgules des noms de tables, afficher et alias requis pour les opérations OData et pour générer la documentation swagger avec des exemples (par exemple, « NEWORDERS »).
Procédures | Oui | Liste séparées par des virgules des noms de procédure et de fonction (par exemple, « SPORDERID »).
Locales | N° | Déploiement local à l’aide d’Azure Service Bus relais.
ServiceBusConnectionString | N° | Chaîne de connexion de Service Bus relais Azure.
PollToCheckData | N° | Nb sélectionnez un relevé à utiliser avec un déclencheur d’application logique (par exemple, « SELECT COUNT (\*) à partir de NEWORDERS où SHIPDATE IS NULL »).
PollToReadData | N° | Instruction SELECT à utiliser avec un déclencheur d’application logique (par exemple, « sélectionnez \* de NEWORDERS où SHIPDATE est mise à jour de FOR NULL »).
PollToAlterData | N° | Mettre à jour ou supprimer un relevé à utiliser avec un déclencheur d’application logique (par exemple, « mise à jour NEWORDERS définir SHIPDATE = actuelle DATE WHERE CURRENT OF &lt;curseur&gt;»).

7. Sélectionnez **OK**, puis **créer**.
8. Lorsque vous avez terminé, les paramètres du Package ressembler à ce qui suit :  
![][1]


## <a name="logic-app-with-informix-connector-action-to-add-data"></a>Application logique avec l’action de connecteur Informix pour ajouter des données ##
Vous pouvez définir une action d’application logique pour ajouter des données à une table Informix à l’aide d’une instruction Insert de l’API ou le billet à OData entité opération. Par exemple, vous pouvez insérer un nouvel enregistrement de commande client, en traitement d’une instruction SQL INSERT par rapport à une table définie avec une colonne d’identité, renvoi de la valeur d’identité ou les lignes affectées à l’application logique (sélectionnez le ORDID à partir de la dernière TABLE (valeurs insérer dans NEWORDERS (CUSTID, destinataire, SHIPADDR, Ville livraison, SHIPREG, SHIPZIP) ( ?, ?, ?, ?, ?, ?))).

> [AZURE.TIP] Connexion Informix «*publier sur EntitySet*» renvoie la valeur de la colonne identité et «*API insérer*» renvoie lignes affectées

1. Dans la startboard Azure, sélectionnez **+** (signe plus), **Web + Mobile**, puis **application logique**.
2. Entrez le nom (par exemple, « NewOrdersInformix »), Plan de Service d’application et d’autres propriétés, puis sélectionnez **créer**.
3. Dans la startboard Azure, sélectionnez la logique application que vous venez de créer, **paramètres**, puis **déclencheurs et actions**.
4. Dans la carte déclencheurs et les actions, sélectionnez **créer à partir de zéro** au sein de l’application logique modèles.
5. Dans le volet applications API, sélectionnez **périodicité**, définissez une fréquence et intervalle, puis **coche**.
6. Dans le volet applications API, sélectionnez **lien Informix**, développez la liste opérations pour sélectionner **Insérer dans NEWORDER**.
7. Développer la liste des paramètres pour entrer les valeurs suivantes :  

    Nom | Valeur
--- | --- 
CUSTID | 10042
N° MESSAGER | 10000
DESTINATAIRE | K jattes Kountry Store 
SHIPADDR | Terrace orchestre 12
VILLE LIVRAISON | Walla Walla 
SHIPREG | WA
SHIPZIP | 99362 

8. Sélectionnez la **coche** pour enregistrer les paramètres des actions, puis **Enregistrer**.
9. Les paramètres doivent se présenter comme suit :  
![][3]  
10. Dans la liste **s’exécute à toutes les** **opérations**, sélectionnez l’élément premiers dans la liste (exécuter plus récent). 
11. Dans la carte **logique exécuter l’application** , sélectionnez l' élément **ACTION** **informixconnectorneworders**.
12. Dans la carte de **cette action logique application** , sélectionnez le **Lien entrées**. Connecteur Informix utilise ces entrées pour traiter une instruction INSERT paramétrée.
13. Dans la carte de **cette action logique application** , sélectionnez le **Lien sorties**. Les entrées doivent se présenter comme suit :  
![][4]

#### <a name="what-you-need-to-know"></a>Ce que vous devez savoir

- Connecteur tronque les noms de table Informix lors de la formation de noms d’actions application logique. Par exemple, l’opération **Insérer dans NEWORDERS** est tronquée à **Insérer dans NEWORDER**.
- Après avoir enregistré l’application logique **déclencheurs et les actions**, application logique traite l’opération. Il peut y avoir un délai d’un nombre de secondes (par exemple, 3 à 5 secondes) avant la logique de l’application traite l’opération. Si vous le souhaitez, vous pouvez cliquer sur **Exécuter maintenant** pour l’opération.
- Connecteur Informix définit membres EntitySet avec les attributs, notamment si le membre correspond à une colonne Informix avec une valeur par défaut ou de colonnes (par exemple, identité) générées. L’application logique affiche un astérisque rouge en regard du nom de code de membre EntitySet, pour indiquer les colonnes Informix nécessitant des valeurs. Vous ne devez pas entrer une valeur pour le membre ORDID, ce qui correspond à la colonne d’identité Informix. Vous pouvez entrer des valeurs pour d’autres membres facultatifs (articles, ORDDATE, REQDATE, n° messager, frais de transport, SHIPCTRY), qui correspondent aux colonnes Informix avec des valeurs par défaut. 
- Connecteur Informix renvoie la réponse à une logique application sur le billet à EntitySet qui inclut les valeurs des colonnes d’identité, qui est dérivée de la DRDA SQLDARD (SQL zone réponse de données) dans l’instruction SQL INSERT préparée. Serveur Informix ne renvoie pas les valeurs pour les colonnes insérées avec les valeurs par défaut.  


## <a name="logic-app-with-informix-connector-action-to-add-bulk-data"></a>Application logique avec l’action de connecteur Informix pour ajouter des données en bloc ##
Vous pouvez définir une action d’application logique pour ajouter des données à une table Informix à l’aide d’une opération d’insertion de l’API en bloc. Par exemple, vous pouvez insérer deux enregistrements de commande client nouveau, en traitement d’une instruction SQL INSERT à l’aide d’un tableau de valeurs de ligne par rapport à une table définie avec une colonne d’identité, renvoyant les lignes affectées à l’application logique (sélectionnez le ORDID à partir de la dernière TABLE (valeurs insérer dans NEWORDERS (CUSTID, destinataire, SHIPADDR, Ville livraison, SHIPREG, SHIPZIP) ( ?, ?, ?, ?, ?, ?))).

1. Dans la startboard Azure, sélectionnez **+** (signe plus), **Web + Mobile**, puis **application logique**.
2. Entrez le nom (par exemple, « NewOrdersBulkInformix »), Plan de Service d’application et d’autres propriétés, puis sélectionnez **créer**.
3. Dans la startboard Azure, sélectionnez la logique application que vous venez de créer, **paramètres**, puis **déclencheurs et actions**.
4. Dans la carte déclencheurs et les actions, sélectionnez **créer à partir de zéro** au sein de l’application logique modèles.
5. Dans le volet applications API, sélectionnez **périodicité**, définissez une fréquence et intervalle, puis **coche**.
6. Dans le volet applications API, sélectionnez **lien Informix**, développez la liste opérations pour sélectionner **En bloc insérer de nouveau**.
7. Entrez la valeur de **lignes** sous forme de tableau. Par exemple, copiez et collez les éléments suivants :  

    ```
    [{"custid":10081,"shipid":10000,"shipname":"Trail's Head Gourmet Provisioners","shipaddr":"722 DaVinci Blvd.","shipcity":"Kirkland","shipreg":"WA","shipzip":"98034"},{"custid":10088,"shipid":10000,"shipname":"White Clover Markets","shipaddr":"305 14th Ave. S. Suite 3B","shipcity":"Seattle","shipreg":"WA","shipzip":"98128","shipctry":"USA"}]
    ```
        
8. Sélectionnez la **coche** pour enregistrer les paramètres des actions, puis **Enregistrer**. Les paramètres doivent se présenter comme suit :  
![][6]

9. Dans la liste **tous les s’exécute** sous **opérations**, cliquez sur l’élément premiers dans la liste (exécuter plus récent).
10. Dans la carte **logique exécuter l’application** , cliquez sur l’élément **d’ACTION** .
11. Dans la carte de **cette action logique application** , cliquez sur le **Lien entrées**. Sorties doivent se présenter comme suit :  
[][7]
12. Dans la carte de **cette action logique application** , cliquez sur le **Lien sorties**. Sorties doivent se présenter comme suit :  
![][8]

#### <a name="what-you-need-to-know"></a>Ce que vous devez savoir

- Connecteur tronque les noms de table Informix lors de la formation de noms d’actions application logique. Par exemple, l’opération **En bloc insérer dans NEWORDERS** est tronquée **en bloc**insérer de nouveau.
- Base de données Informix peut doit respecter la casse aux noms de tables et de colonnes. Par exemple, les noms de colonne de tableau opération en bloc insérer devrez être indiquée en minuscules (« custid ») au lieu de majuscules (« CUSTID »).
- En omettant des colonnes d’identité (par exemple, ORDID), des colonnes nullables (par exemple, SHIPDATE) et des colonnes avec des valeurs par défaut (par exemple ORDDATE, REQDATE, n° messager, frais de transport, SHIPCTRY), base de données Informix génère des valeurs.
- En spécifiant « aujourd'hui » et « demain », le connecteur Informix génère « DATE actuelle » et « DATE actuelle + 1 jour » fonctionne (par exemple, REQDATE). 


## <a name="logic-app-with-informix-connector-trigger-to-read-alter-or-delete-data"></a>Application logique avec déclencheur de connecteur Informix à lire, modifier ou supprimer des données ##
Vous pouvez définir un déclencheur d’application logique pour interroger et lire les données d’une table Informix à l’aide d’une opération composite API sondage données. Par exemple, vous pouvez lire une ou plusieurs nouvelles ordre enregistrements, renvoyer les enregistrements à l’application logique. Les paramètres de package/application Informix connexion se présente comme suit :

    Paramètre d’application | Valeur
--- | --- | ---
PollToCheckData | Sélectionner COUNT (\*) à partir de NEWORDERS où SHIPDATE est NULL
PollToReadData | Sélectionnez \* de NEWORDERS où SHIPDATE est NULL pour mettre à jour
PollToAlterData | <no value specified>


En outre, vous pouvez définir un déclencheur d’application logique pour interroger, lire et modifier les données dans une table Informix à l’aide d’une opération composite API sondage données. Par exemple, vous pouvez lire l’un ou plusieurs enregistrements de commande client nouvelle, mettre à jour les valeurs de ligne, renvoyer les enregistrements sélectionnés (avant la mise à jour) dans l’application logique. Les paramètres de package/application Informix connexion se présente comme suit :

    Paramètre d’application | Valeur
--- | --- | ---
PollToCheckData | Sélectionner COUNT (\*) à partir de NEWORDERS où SHIPDATE est NULL
PollToReadData | Sélectionnez \* de NEWORDERS où SHIPDATE est NULL pour mettre à jour
PollToAlterData | Mise à jour NEWORDERS ensemble SHIPDATE = DATE du jour où des actuel &lt;curseur&gt;


En outre, vous pouvez définir un déclencheur d’application logique pour interroger, lisez et supprimer des données à partir d’une table Informix à l’aide d’une opération composite API sondage données. Par exemple, vous pouvez lire l’un ou plusieurs enregistrements de commande client nouvelle, supprimez les lignes, renvoyer les enregistrements sélectionnés (avant la suppression) dans l’application logique. Les paramètres de package/application Informix connexion se présente comme suit :

    Paramètre d’application | Valeur
--- | --- | ---
PollToCheckData | Sélectionner COUNT (\*) à partir de NEWORDERS où SHIPDATE est NULL
PollToReadData | Sélectionnez \* de NEWORDERS où SHIPDATE est NULL pour mettre à jour
PollToAlterData | SUPPRIMER NEWORDERS l’emplacement actuel des &lt;curseur&gt;

Dans cet exemple, application logique sera interroger, lire, mettre à jour et puis relire des données dans la table Informix.

1. Dans la startboard Azure, sélectionnez **+** (signe plus), **Web + Mobile**, puis **application logique**.
2. Entrez le nom (par exemple, « ShipOrdersInformix »), Plan de Service d’application et d’autres propriétés, puis sélectionnez **créer**.
3. Dans la startboard Azure, sélectionnez la logique application que vous venez de créer, **paramètres**, puis **déclencheurs et actions**.
4. Dans la carte déclencheurs et les actions, sélectionnez **créer à partir de zéro** au sein de l’application logique modèles.
5. Dans le volet applications API, sélectionnez **lien Informix**, définissez une fréquence et intervalle, puis **coche**.
6. Dans le volet applications API, sélectionnez **lien Informix**, développez la liste opérations pour sélectionner **à partir de NEWORDERS**.
7. Sélectionnez la **coche** pour enregistrer les paramètres des actions, puis **Enregistrer**. Les paramètres doivent se présenter comme suit :  
![][10]
8. Cliquez sur pour fermer la carte **déclencheurs et les actions** , puis cliquez sur pour fermer la carte de **paramètres** .
9. Dans la liste **tous les s’exécute** sous **opérations**, cliquez sur l’élément premiers dans la liste (exécuter plus récent).
10. Dans la carte **logique exécuter l’application** , cliquez sur l’élément **d’ACTION** .
11. Dans la carte de **cette action logique application** , cliquez sur le **Lien sorties**. Sorties doivent se présenter comme suit :  
![][11]


## <a name="logic-app-with-informix-connector-action-to-remove-data"></a>Application logique avec l’action de connecteur Informix pour supprimer des données ##
Vous pouvez définir une action d’application logique pour supprimer les données à partir d’une table Informix à l’aide d’un API supprimer ou un billet à OData entité opération. Par exemple, vous pouvez insérer un nouvel enregistrement de commande client, en traitement d’une instruction SQL INSERT par rapport à une table définie avec une colonne d’identité, renvoi de la valeur d’identité ou les lignes affectées à l’application logique (sélectionnez le ORDID à partir de la dernière TABLE (valeurs insérer dans NEWORDERS (CUSTID, destinataire, SHIPADDR, Ville livraison, SHIPREG, SHIPZIP) ( ?, ?, ?, ?, ?, ?))).

## <a name="create-logic-app-using-informix-connector-to-remove-data"></a>Créer application logique à l’aide de connecteur Informix pour supprimer des données ##
Vous pouvez créer une nouvelle application logique à partir de la Azure Marketplace et ensuite utiliser le connecteur Informix sous forme d’action pour supprimer les commandes des clients. Par exemple, vous pouvez utiliser l’opération de suppression conditionnelle Informix connecteur pour traiter une instruction SQL DELETE (supprimer à partir de NEWORDERS où ORDID > = 10000).

1. Dans le menu concentrateur de la carte Azure **Démarrer** , cliquez sur **+** (signe plus), cliquez sur **Web + Mobile**, puis cliquez sur **application logique**. 
2. Dans la carte de **créer une logique de l’application** , tapez un **nom**, par exemple **RemoveOrdersInformix**.
3. Sélectionnez ou définissez les valeurs pour les autres paramètres (par exemple, le plan de services, le groupe de ressources).
4. Les paramètres se présente comme suit. Cliquez sur **créer**:  
![][12]
5. Dans la carte de **paramètres** , cliquez sur **déclencheurs et actions**.
6. Dans la carte **déclencheurs et les actions** , dans la liste des **applications logique modèles** , cliquez sur **créer à partir de zéro**.
7. Dans la carte **déclencheurs et les actions** , dans le volet **Applications API** , dans le groupe de ressources, cliquez sur **périodicité**.
8. Dans la surface de l’application de logique, cliquez sur l’élément de **périodicité** , **fréquence** et l' **intervalle**, par exemple les **jours** et **1**, la valeur, puis sur la **coche** pour enregistrer les paramètres d’élément de périodicité.
9. Dans la carte **déclencheurs et les actions** , dans le volet **Applications API** , dans le groupe de ressources, cliquez sur **lien Informix**.
10. Dans la surface de l’application de logique, cliquez sur l’élément d’action **Informix connecteur** , cliquez sur les points de suspension (...****) pour développer la liste opérations, puis cliquez sur **conditionnel supprimer de N**.
11. Dans l’élément d’action Informix connecteur, tapez **ordid page 10000** pour une **expression qui identifie un sous-ensemble d’entrées**.
12. Cliquez sur la **coche** pour enregistrer les paramètres des actions, puis cliquez sur **Enregistrer**. Les paramètres doivent se présenter comme suit :  
![][13]
13. Cliquez sur pour fermer la carte **déclencheurs et les actions** , puis cliquez sur pour fermer la carte de **paramètres** .
14. Dans la liste **tous les s’exécute** sous **opérations**, cliquez sur l’élément premiers dans la liste (exécuter plus récent).
15. Dans la carte **logique exécuter l’application** , cliquez sur l’élément **d’ACTION** .
16. Dans la carte de **cette action logique application** , cliquez sur le **Lien sorties**. Sorties doivent se présenter comme suit :  
![][14]

**Remarque :** Concepteur de l’application logique tronque les noms des tables. Par exemple l’opération **conditionnelle supprime NEWORDERS** est tronquée **conditionnel**supprimer de N.


> [AZURE.TIP] Utilisez les instructions SQL suivantes pour créer l’exemple de tableau et les procédures stockées. 

Vous pouvez créer l’exemple de tableau NEWORDERS avec les instructions DDL SQL Informix suivantes :
 
    create table neworders (  
        ordid serial(10000) unique ,  
        custid int not null ,  
        empid int not null default 10000 ,  
        orddate date not null default today ,  
        reqdate date default today ,  
        shipdate date ,  
        shipid int not null default 10000 ,  
        freight decimal (9,2) not null default 0.00 ,  
        shipname char (40) not null ,  
        shipaddr char (60) not null ,  
        shipcity char (20) not null ,  
        shipreg char (15) not null ,  
        shipzip char (10) not null ,  
        shipctry char (15) not null default ''USA'' 
        )


Vous pouvez créer l’exemple de procédure SPORDERID stockée à l’aide de l’instruction Informix DDL suivante :
 
    create procedure sporderid ( ord_id int)  
        returning int, int, int, date, date, date, int, decimal (9,2), char (40), char (60), char (20), char (15), char (10), char (15)  
        define xordid, xcustid, xempid, xshipid int;  
        define xorddate, xreqdate, xshipdate date;  
        define xfreight decimal (9,2);  
        define xshipname char (40);  
        define xshipaddr char (60);  
        define xshipcity char (20);  
        define xshipreg, xshipctry char (15);  
        define xshipzip char (10);  
        select ordid, custid, empid, orddate, reqdate, shipdate, shipid, freight, shipname, shipaddr, shipcity, shipreg, shipzip, shipctry  
            into xordid, xcustid, xempid, xorddate, xreqdate, xshipdate, xshipid, xfreight, xshipname, xshipaddr, xshipcity, xshipreg, xshipzip, xshipctry  
            from neworders where ordid = ord_id;  
        return xordid, xcustid, xempid, xorddate, xreqdate, xshipdate, xshipid, xfreight, xshipname, xshipaddr, xshipcity, xshipreg, xshipzip, xshipctry;  
    end procedure; 


## <a name="hybrid-configuration-optional"></a>Configuration hybride (facultatif)

> [AZURE.NOTE] Cette étape est requise uniquement si vous utilisez DB2 connecteur local derrière le pare-feu.

Service d’application utilise le Gestionnaire de Configuration hybride pour vous connecter en toute sécurité à votre système local. Si le connecteur utilise un serveur local IBM DB2 pour Windows, le Gestionnaire de connexions hybride est requis.

Consultez [l’aide du Gestionnaire de connexion hybride](app-service-logic-hybrid-connection-manager.md).


## <a name="do-more-with-your-connector"></a>Faire plus avec le lien
Maintenant que le lien est créé, vous pouvez l’ajouter à un flux de travail de l’entreprise à l’aide d’une application logique. Voir [que sont les applications logique ?](app-service-logic-what-are-logic-apps.md).

Créer les applications API à l’aide des API REST. Voir les [liens et des API applications référence](http://go.microsoft.com/fwlink/p/?LinkId=529766).

Vous pouvez également consulter les statistiques et contrôler les performances de sécurité au connecteur. Voir [gérer et analyser vos applications API et les connecteurs intégrés](app-service-logic-monitor-your-connectors.md).


<!--Image references-->
[1]: ./media/app-service-logic-connector-informix/ApiApp_InformixConnector_Create.png
[2]: ./media/app-service-logic-connector-informix/LogicApp_NewOrdersInformix_Create.png
[3]: ./media/app-service-logic-connector-informix/LogicApp_NewOrdersInformix_TriggersActions.png
[4]: ./media/app-service-logic-connector-informix/LogicApp_NewOrdersInformix_Outputs.png
[5]: ./media/app-service-logic-connector-informix/LogicApp_NewOrdersBulkInformix_Create.png
[6]: ./media/app-service-logic-connector-informix/LogicApp_NewOrdersBulkInformix_TriggersActions.png
[7]: ./media/app-service-logic-connector-informix/LogicApp_NewOrdersBulkInformix_Inputs.png
[8]: ./media/app-service-logic-connector-informix/LogicApp_NewOrdersBulkInformix_Outputs.png
[9]: ./media/app-service-logic-connector-informix/LogicApp_UpdateOrdersInformix_Create.png
[10]: ./media/app-service-logic-connector-informix/LogicApp_UpdateOrdersInformix_TriggersActions.png
[11]: ./media/app-service-logic-connector-informix/LogicApp_UpdateOrdersInformix_Outputs.png
[12]: ./media/app-service-logic-connector-informix/LogicApp_RemoveOrdersInformix_Create.png
[13]: ./media/app-service-logic-connector-informix/LogicApp_RemoveOrdersInformix_TriggersActions.png
[14]: ./media/app-service-logic-connector-informix/LogicApp_RemoveOrdersInformix_Outputs.png



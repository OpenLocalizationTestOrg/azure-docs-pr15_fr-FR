<properties 
    pageTitle="Connecteurs commerciaux et les applications de l’API dans les applications de logique | Microsoft Azure" 
    description="Découvrez comment créer et configurer des connecteurs EDI, EDIFACT, AS2 et plateforme sécurisée ; architecture microservices" 
    services="logic-apps" 
    documentationCenter="" 
    authors="MandiOhlinger" 
    manager="anneta" 
    editor=""/>

<tags 
    ms.service="logic-apps" 
    ms.workload="integration" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/18/2016" 
    ms.author="mandia"/> 

# <a name="business-to-business-connectors-and-api-apps"></a>Connecteurs commerciaux et applications API

[AZURE.INCLUDE [app-service-logic-version-message](../../includes/app-service-logic-version-message.md)]

Logique applications inclut plusieurs applications API BizTalk qui sont essentiels au environnements d’intégration. Ces applications API sont basées sur les concepts et outils utilisés dans BizTalk Server, mais sont désormais disponibles dans le cadre de la logique d’applications. 

Une catégorie de ces applications API sont les applications de l’API-entreprises (B2B). À l’aide de ces applications API B2B, vous pouvez facilement ajouter partenaires, créer des accords et faire tout ce que vous serait local à l’aide de EDIFACT EDI et AS2.  

Ces applications API B2B offrent des fonctionnalités « Déclencheur » et « Action ». Un déclencheur démarre une nouvelle instance basée sur un événement spécifique, comme l’arrivée d’un X12 message à partir d’un partenaire. Une Action est le résultat, comme après un X12 de réception du message, puis envoyez le message à l’aide de AS2.


## <a name="what-is-a-business-to-business-connector-or-api-apps"></a>Qu’est un connecteur commerciaux ou API applications
La fonctionnalité entreprises (B2B) inclut des applications API existant, prédéfinies qui permettent de sociétés différentes divisions, applications, et ainsi de suite afin de communiquer au moyen de AS2, EDI et EDIFACT. 

Les applications de l’API B2B sont les suivantes : 

Connecteur ou applications API | Description
--- | ---
Gestion des partenaires commerciaux BizTalk | Une application API qui crée des relations de-entreprises (B2B) à l’aide des partenaires et des accords. Ces relations utilisent le AS2, EDIFACT et X12 protocole.<br/><br/>L’application de l’API de plateforme sécurisée est l’exigence de base du connecteur AS2 et la X12 ou EDIFACT API applications. 
Connecteur AS2 | Connecteur qui reçoit et envoie des messages à l’aide du transport AS2. Le connecteur transfère les données en toute sécurité et fiable via Internet.
BizTalk EDIFACT | Une application API qui reçoit et envoie des messages à l’aide de EDIFACT. EDIFACT est également appelé UN/EDIFACT (United Nations/électronique données Interchange pour l’Administration, Commerce and Transport) et est largement utilisé secteurs.
BizTalk X12 | Une application API qui reçoit et envoie des messages à l’aide de la X12 protocole. X12 est également appelés ASC X 12 (comité X12) et est largement utilisé secteurs. 


À l’aide de ces applications API, vous pouvez effectuer EDI différente tâches de messagerie. Par exemple, à l’aide du connecteur AS2, vous pouvez en toute sécurité recevoir et envoyer des différents types de messages (fichier-EDI, XML, plate, et ainsi de suite) à un client, une division au sein de votre société comme des ressources humaines, ou toute autre personne qui utilise AS2. 

Vous pouvez créer des applications de l’API autant que vous voulez créez facilement. Vous pouvez également réutiliser un seul API application dans plusieurs scénarios ou flux de travail.

Vous pouvez le faire sans écrire de code. Prise en main. 


## <a name="requirements-to-get-started"></a>Configuration requise pour la prise en main
Lorsque vous créez des applications de l’API B2B, il existe certaines ressources requises. Ces éléments doivent être créés par vous avant qu’ils peuvent être utilisés dans d’autres applications de l’API. Ces exigences sont les suivantes : 

Configuration requise | Description
--- | ---
Base de données SQL Azure | Stocke les éléments B2B, y compris des partenaires, des schémas, des certificats et des accords. Chacune des applications API B2B nécessite sa propre base de données SQL Azure. <br/><br/>**Remarque** Copiez la chaîne de connexion à cette base de données.<br/><br/>[Créer une base de données SQL Azure](../sql-database/sql-database-get-started.md)
Conteneur de stockage d’objets Blob Azure | Propriétés du message Stores lors de l’archivage AS2 est activé. Si vous n’avez pas besoin d’archivage de messages AS2, un conteneur de stockage n’est pas nécessaire. <br/><br/>**Remarque** Si vous activez l’archivage, copiez la chaîne de connexion à ce stockage Blob.<br/><br/>[À propos des comptes de stockage Azure](../storage/storage-create-storage-account.md)
Service Bus Namespace et ses valeurs de clé | Stocke X12 et EDIFACT le traitement par lots de données. Si vous n’avez pas besoin le traitement par lots, un espace de noms Bus de Service n’est pas nécessaire.<br/><br/>**Remarque** Si vous activez le traitement par lots, copiez ces valeurs.<br/><br/>[Créer un Namespace Bus de Service](http://msdn.microsoft.com/library/azure/hh690931.aspx)
Instance de plateforme sécurisée | Une instance de gestion des partenaires commerciaux (GPC) BizTalk est requise pour créer un connecteur AS2 et X12 ou EDIFACT API application. Lorsque vous créez l’application de l’API de plateforme sécurisée, vous créez l’Instance de plateforme sécurisée. <br/><br/>**Remarque** Connaître le nom de votre application de l’API de plateforme sécurisée. 


## <a name="create-the-api-apps"></a>Créer les applications API
Applications de l’API B2B pouvant être créées à l’aide du portail Azure ou API REST. 


### <a name="create-the-api-apps-using-rest-apis"></a>Créer les applications API à l’aide des API REST
[Consultez la documentation sur l’utilisation de l’API REST.](http://go.microsoft.com/fwlink/p/?LinkId=529766)


### <a name="create-the-b2b-api-apps-in-the-azure-portal"></a>Créer les applications API B2B dans le portail Azure
Dans le portail Azure, vous pouvez créer des applications d’API B2B lors de la création d’applications logique, des applications Web ou des applications Mobile. Ou bien, vous pouvez en créer un à l’aide de sa propre carte. Les deux méthodes sont faciles afin qu’il varie selon vos besoins ou les préférences. Certains utilisateurs préfèrent tout d’abord créer toutes les applications de l’API B2B avec leurs propriétés spécifiques. Ensuite, créer des applications logique applications/Web applications/Mobile et ajoutez les applications API B2B que vous avez créé.  

Les étapes suivantes créent les applications API B2B à l’aide de la carte de l’API applications.


#### <a name="create-the-biztalk-trading-partner-management-tpm-api-apps"></a>Créer la BizTalk cotation partenaire gestion API applications

> [AZURE.NOTE] Une instance de gestion des partenaires commerciaux (GPC) BizTalk est requise pour créer un connecteur AS2 et X12 ou EDIFACT API application. Lorsque vous créez l’application de l’API de plateforme sécurisée, vous créez l’Instance de plateforme sécurisée.

Les étapes suivantes créent l’instance de plateforme sécurisée :

1. Dans le portail Azure Startboard (la page d’accueil), sélectionnez **Marketplace**. **Applications API** répertorie tous les applications API existant et connecteurs. Vous pouvez également **Rechercher** pour les applications de l’API B2B spécifique.
2. Sélectionnez **BizTalk gestion des partenaires commerciaux**. Dans la nouvelle carte, sélectionnez **créer**. 
3. Entrez les propriétés : 

    Propriété | Description
--- | ---
Nom | Entrez un nom pour l’instance de plateforme sécurisée. Par exemple, vous pouvez nommez-la *AccountsPayableTPM*.
Paramètres du package | Entrez la **Chaîne de connexion de base de données** de ADO.NET à la base de données SQL Azure que vous avez créé. <br/><br/>Lorsque vous copiez la chaîne de connexion, le mot de passe n’est pas ajouté à la chaîne de connexion. Veillez à entrer le mot de passe dans la chaîne de connexion.
Plan de services d’application | Listes de votre plan de paiement. Vous pouvez le modifier si vous avez besoin plus ou moins de ressources.
Niveau de tarification | Propriété en lecture seule qui répertorie la catégorie tarification au sein de votre abonnement Azure. 
Groupe de ressources | Créer un autre ou utiliser un groupe existant. Toutes les applications de l’API et des connecteurs pour vos applications de logique, les applications Web et les applications Mobile doivent être placé dans le même groupe de ressources. <br/><br/>[À l’aide de groupes de ressources](../azure-resource-manager/resource-group-overview.md) explique cette propriété. 
Abonnement | Propriété en lecture seule qui répertorie votre abonnement actuel.
Emplacement | L’emplacement géographique qui héberge votre service Azure. 
Ajouter à Startboard | Sélectionnez cette option pour ajouter l’application de l’API B2B à votre tribord (la page d’accueil).

4. Sélectionnez **créer**. 

Une fois l’application de l’API de plateforme sécurisée (Instance de plateforme sécurisée) est créée, vous pouvez ensuite créer le connecteur AS2 et/ou la X12 ou EDIFACT API applications. 


#### <a name="create-the-as2-connector"></a>Créer le connecteur AS2

1. Dans le portail Azure Startboard (la page d’accueil), sélectionnez **Marketplace**. **Applications API** répertorie tous les applications API existant et connecteurs. Vous pouvez également **Rechercher** pour les applications de l’API B2B spécifique.
2. Sélectionnez **connecteur AS2**. Dans la nouvelle carte, sélectionnez **créer**. 
3. Entrez les propriétés : 

    Propriété | Description
--- | ---
Nom | Entrez un nom pour le connecteur AS2. Par exemple, vous pouvez nommez-la *AS2Connector*.
Paramètres du package | Entrez les paramètres spécifiques à cette application API, telles que le nom de l’Instance de plateforme sécurisée. <br/><br/>Dans cette rubrique pour les propriétés spécifiques, voir [Ajouter des paramètres du Package AS2](#AddAS2Conn) . 
Plan de services d’application | Listes de votre plan de paiement. Vous pouvez le modifier si vous avez besoin plus ou moins de ressources.
Niveau de tarification | Propriété en lecture seule qui répertorie la catégorie tarification au sein de votre abonnement Azure. 
Groupe de ressources | Créer un autre ou utiliser un groupe existant. [À l’aide de groupes de ressources](../azure-resource-manager/resource-group-overview.md) explique cette propriété. 
Abonnement | Propriété en lecture seule qui répertorie votre abonnement actuel.
Emplacement | L’emplacement géographique qui héberge votre service Azure. 
Ajouter à Startboard | Sélectionnez cette option pour ajouter l’application de l’API B2B à votre tribord (la page d’accueil).

    **<a name="AddAS2Conn"></a>Connecteur AS2 paramètres du Package**

    Propriété | Description
--- | --- 
Chaîne de connexion de base de données | Entrez la chaîne de connexion ADO.NET à la base de données SQL Azure que vous avez créé. Lorsque vous copiez la chaîne de connexion, le mot de passe n’est pas ajouté à la chaîne de connexion. Veillez à entrer le mot de passe dans la chaîne de connexion avant de les coller.
Activer l’archivage des messages entrants | Facultatif. Activer cette propriété stocker les propriétés du message d’un message AS2 entrant reçus à partir d’un partenaire. 
Chaîne de connexion de stockage Blob Azure  | Entrez la chaîne de connexion au conteneur de stockage d’objets Blob Azure que vous avez créé. Lorsque l’archivage est activée, les messages au format et décodées sont stockés dans ce conteneur de stockage.
Nom de l’Instance plateforme sécurisée | Entrez le nom de la **Gestion des partenaires commerciaux BizTalk** application API que vous avez créé précédemment. Lorsque vous créez le connecteur AS2, ce connecteur exécute uniquement les accords AS2 dans cette instance spécifique de la plateforme sécurisée.

4. Sélectionnez **créer**. 


#### <a name="create-the-x12-or-edifact-api-apps"></a>Créer les applications de l’API X12 ou EDIFACT

1. Dans le portail Azure Startboard (la page d’accueil), sélectionnez **Marketplace**. **Applications API** répertorie tous les applications API existant et connecteurs. Vous pouvez également **Rechercher** pour les applications de l’API B2B spécifique.
2. Sélectionnez **BizTalk X 12** ou **EDIFACT BizTalk**. Dans la nouvelle carte, sélectionnez **créer**. 
3. Entrez les propriétés : 

    Propriété | Description
--- | ---
Nom | Entrez un nom pour l’application de l’API B2B. Par exemple, vous pouvez nommez-la *EDI850APIApp*.
Paramètres du package | Entrez les paramètres spécifiques à cette application API, telles que le nom de l’Instance de plateforme sécurisée. <br/><br/>Voir [X12 ou paramètres du Package EDIFACT](#AddX12) dans cette rubrique pour les propriétés spécifiques. 
Plan de services d’application | Listes de votre plan de paiement. Vous pouvez le modifier si vous avez besoin plus ou moins de ressources.
Niveau de tarification | Propriété en lecture seule qui répertorie la catégorie tarification au sein de votre abonnement Azure. 
Groupe de ressources | Créer un autre ou utiliser un groupe existant. [À l’aide de groupes de ressources](../azure-resource-manager/resource-group-overview.md) explique cette propriété. 
Abonnement | Propriété en lecture seule qui répertorie votre abonnement actuel.
Emplacement | L’emplacement géographique qui héberge votre service Azure. 
Ajouter à Startboard | Sélectionnez cette option pour ajouter l’application de l’API B2B à votre tribord (la page d’accueil).

    **<a name="AddX12"></a>Paramètres du Package d’applications API X12 et EDIFACT**  

    Propriété | Description
--- | --- 
Chaîne de connexion de base de données | Entrez la chaîne de connexion ADO.NET à la base de données SQL Azure que vous avez créé. Lorsque vous copiez la chaîne de connexion, le mot de passe n’est pas ajouté à la chaîne de connexion. Veillez à entrer le mot de passe dans la chaîne de connexion avant de les coller.
Service Bus Namespace | Entrez l’espace de noms Bus des services que vous avez créé. Obligatoire uniquement lorsque le traitement par lots est activé. 
Nom de service Bus Namespace partagés touche d’accès rapide | Entrez l’espace de noms de Service Bus touche d’accès rapide que vous avez créé. Obligatoire uniquement lorsque le traitement par lots est activé. 
Valeur de service Bus Namespace partagés touche d’accès rapide | Entrez l’espace de noms de Service Bus valeur touche d’accès rapide que vous avez créé. Obligatoire uniquement lorsque le traitement par lots est activé. 
Nom de l’Instance plateforme sécurisée | Entrez le nom de la **Gestion des partenaires commerciaux BizTalk** application API que vous avez créé précédemment. Lorsque vous créez la X12 ou EDIFACT API applications, cette application API exécute uniquement les accords X12/EDFIACT dans cette instance spécifique de la plateforme sécurisée.

4. Sélectionnez **créer**. 


## <a name="add-your-partners-agreements-certificates-and-schemas"></a>Ajouter votre partenaires, les accords, les certificats et les schémas 
Dans le portail Azure, ouvrez votre application de l’API de plateforme sécurisée. Dans la section **composants** , ajoutez votre partenaires, les accords, les certificats et les schémas. 

Vous pouvez également ajouter des accords à vos liens AS2, X12 API applications et les applications de l’API EDIFACT. 


## <a name="monitor-your-api-apps"></a>Surveiller les applications API
Dans le portail Azure, ouvrez votre application de l’API de plateforme sécurisée. Dans la section **opérations** , vous pouvez afficher les opérations de gestion de l’autre. Par exemple, vous pouvez :

- Événements d’information affichage et d’erreur
- Afficher le nombre de thread et l’utilisation de mémoire du processus de travail (w3wp)
- Afficher les journaux de serveur web et une Application

Plus sur le serveur [Moniteur vos applications logique](app-service-logic-monitor-your-logic-apps.md).


## <a name="add-the-api-apps-to-your-application"></a>Ajouter les applications API à votre application 
Microsoft Azure Application Service expose différents types d’applications qui peuvent utiliser ces applications API B2B. Vous pouvez créer un nouveau ou ajouter vos applications API B2B existant pour applications logique, applications Mobile ou un Web Apps. 

Au sein de votre application, il vous suffit sélectionné les applications API B2B à partir de la galerie automatiquement est ajouté à votre application.  

> [AZURE.IMPORTANT] Pour ajouter des liens et des API applications que vous avez créé précédemment, créez la logique d’applications, applications Mobile ou applications Web dans le même groupe de ressources. 

Les étapes suivantes ajoutent les applications API B2B à logique d’applications, des applications Mobile ou des applications Web : 

1. Dans le portail Azure Startboard (page d’accueil), accédez à la **Marketplace**, puis recherchez votre logique, Mobile ou Web Apps. 

    Si vous créez une nouvelle application, recherchez logique d’applications, des applications Mobile ou des applications Web. Sélectionnez l’application et dans la nouvelle carte, sélectionnez **créer**. [Créer une application logique](app-service-logic-create-a-logic-app.md) répertorie les étapes. 

2. Ouvrez votre application et sélectionnez **déclencheurs et Actions**. 

3. Dans la **Galerie**, sélectionnez l’application de l’API B2B, qui ajoute automatiquement à votre application. Vous pouvez également créer une nouvelle application API B2B.

    > [AZURE.IMPORTANT] Le connecteur AS2 et X12, EDIFACT API applications nécessitent une Instance de plateforme sécurisée. Si vous êtes en train de créer nouvelles applications API B2B, tout d’abord créer l’application de l’API de plateforme sécurisée, et puis créer le connecteur AS2, X12 API application ou EDIFACT API application. 

4. Cliquez sur **OK** pour enregistrer vos modifications. 

>[AZURE.NOTE] Mise en route Azure logique applications avant de vous inscrire pour un compte Azure, [Essayez les applications logique](https://tryappservice.azure.com/?appservice=logic). Vous pouvez immédiatement créer une application de logique courte starter. Aucune carte de crédit obligatoire ; Aucune engagements.

## <a name="more-b2b-resources"></a>Davantage de ressources B2B

[Création d’un processus B2B](app-service-logic-create-a-b2b-process.md)<br/>
[Création d’un contrat partenaire commercial](app-service-logic-create-a-trading-partner-agreement.md)<br/>
[Quels sont les connecteurs et les applications de l’API BizTalk](app-service-logic-what-are-biztalk-api-apps.md)


## <a name="read-about-logic-apps-and-web-apps"></a>En savoir plus sur les applications logique et Web Apps
[Que sont les applications logique ?](app-service-logic-what-are-logic-apps.md)<br/>
[Sites Web et applications Web dans le Service d’application Azure](../app-service-web/app-service-web-overview.md)


## <a name="more-connectors"></a>Connecteurs plus

[Connecteurs et liste d’applications API](app-service-logic-connectors-list.md)<br/><br/>
[Quels sont les connecteurs et les applications de l’API BizTalk](app-service-logic-what-are-biztalk-api-apps.md) 

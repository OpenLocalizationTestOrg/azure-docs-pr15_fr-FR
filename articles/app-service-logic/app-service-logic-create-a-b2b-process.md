<properties 
   pageTitle="Création d’un processus B2B dans le Service d’application Azure | Microsoft Azure" 
   description="Vue d’ensemble de la création d’un processus d’entreprise à entreprise" 
   services="logic-apps" 
   documentationCenter=".net,nodejs,java" 
   authors="rajram" 
   manager="erikre" 
   editor=""/>

<tags
   ms.service="logic-apps"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration" 
   ms.date="04/20/2016"
   ms.author="rajram"/>

# <a name="creating-a-b2b-process"></a>Création d’un processus B2B

[AZURE.INCLUDE [app-service-logic-version-message](../../includes/app-service-logic-version-message.md)]


## <a name="business-scenario"></a>Scénario d’entreprise 
Contoso et Northwind sont deux partenaires professionnels. Contoso (vente au détail) envoie bons de commande à Northwind (le fournisseur) sur un type de transport au niveau du secteur tels que AS2. Northwind enregistre toutes les commandes entrants dans son espace de stockage Cloud. Les commandes d’achat sont les messages XML entre ces deux partenaires. Une fois que le message est stocké dans un stockage cloud de Northwind puis processus internes de Northwind poignée de la commande à partir de ce point.
 
L’objectif de ce didacticiel consiste à établir comment Northwind pouvez établir un processus d’entreprise via lequel il peut recevoir des messages (bons de commande au format XML) à partir de son partenaire de Contoso via AS2 et puis de le conserver dans son espace de stockage Cloud.


## <a name="capabilities-demonstrated"></a>Fonctionnalités illustrées 
Ce didacticiel vous aide à mettre en valeur les fonctionnalités suivantes : 

- **Transport de message**: le revendeur et le fournisseur peuvent être sur différentes plateformes mais ils peuvent toujours atteindre les communications entre les deux. Dans ce didacticiel, ils sont communication sur AS2 (application instruction 2). AS2 est un moyen populaire pour transporter des données entre les partenaires de communications professionnelles de l’entreprise.
- **Permanence des données**: une fois que le message a été reçu via AS2 puis Northwind souhaite conserver avant un traitement approfondi. Il peut utiliser un connecteur pour conserver les messages dans son espace de stockage Cloud. Dans ce didacticiel des objets BLOB Azure est en cours exploité comme le stockage cloud pour Northwind.
- **Création d’un processus d’entreprise**: dans un flux, plusieurs applications API peuvent être assemblées pour obtenir des résultats commerciaux comme illustré ici.


## <a name="before-you-begin"></a>Avant de commencer
Ce didacticiel suppose que vous possédez une connaissance de base des Services d’application Azure, savoir comment créer des applications API et combiner un flux.


## <a name="steps-to-achieve-the-business-scenario"></a>Procédure à suivre le scénario d’entreprise
**Créer et configurer les applications API requises**

1. Créer une instance du **Connecteur d’objets Blob Azure stockage**. Cette fonctionnalité nécessite les informations d’identification à un compte de stockage Azure. Vous assurer qu’elle est prête avant de commencer cette création.
2. Créer une instance de la **Gestion des partenaires commerciaux BizTalk**. Cette fonctionnalité nécessite une base de données SQL vide de la fonction. Vérifiez qu’elle est prête avant de commencer cette création.
3. Créer une instance du **Connecteur AS2**. Cette fonctionnalité nécessite également une base de données SQL vide de la fonction. Vérifiez qu’elle est prête avant de commencer cette création. En outre, si vous souhaitez archiver les messages dans le cadre d’AS2 traitement, vous pouvez fournir des informations d’identification pour un Blob Azure lors de sa création.
4. Configurer le service de plateforme sécurisée (gestion des partenaires commerciaux) créé à :  
    1. Naviguez jusqu'à l’instance du service plateforme sécurisée créé dans le cadre de la procédure ci-dessus.
    2. Utiliser l’option **partenaires** sous *composants* pour **Ajouter** un nouveau partenaire nommé **Contoso** et dans son profil ajouter l’identité AS2 requise.
    3. Utiliser l’option **partenaires** sous *composants* pour **Ajouter** un nouveau partenaire nommé **Northwind** et dans son profil ajouter l’identité AS2 requise.
    4. Utilisez l’option **accords** sous *composants* pour **Ajouter** un nouveau AS2 accord entre Northwind et Contoso. Northwind sera le partenaire hébergé ici, et Contoso sera le partenaire invité. Selon le cas configurer la signature, du chiffrement, compression et accusés de réception lors de la création de ce contrat. Au cas où les certificats doivent être utilisés, ils peuvent être téléchargés via l’option **les certificats** lors de l’exploration du service plateforme sécurisée qui est créé.


## <a name="create-a-flow--business-process"></a>Créer un flux / du processus métier
1. Créer un nouveau flux dans lequel la première étape consiste AS2. Faites glisser et déposez le **Connecteur AS2** et choisissez l’instance déjà créé. Cliquez sur déclencheur en tant que la fonctionnalité :  
    ![][1]  
2. Faites ensuite glisser et déposez **Azure stockage Blob connecteur** et choisissez l’instance déjà créé. Choisissez une action que la fonctionnalité et dans laquelle, sélectionnez **Blob télécharger** en tant que la fonctionnalité souhaitée. Configurer le cas échéant.
3. À présent créer/déployer le flux.


## <a name="message-processing--troubleshooting"></a>Traitement des messages et résolution des problèmes
1. Il est temps de tester le flux que nous avons déployé. Envoyer des que messages XML encapsulé dans AS2 (telles que le contrat AS2 créé ci-dessus) au point de terminaison AS2 exposé par l’instance AS2Connector que vous avez créé. Vous devrez peut-être configurer l’authentification pour le point de terminaison afin qu’il soit accessible au public.
2. Informations d’exécution sur le flux sont exposées en accédant au flux et puis détaillé de l’instance de flux qui a été exécuté
3. Pour plus d’informations de traitement AS2, naviguez jusqu'à l’instance AS2Connector impliqué et suivez en entrant dans le composant de suivi. Vous pouvez utiliser des filtres de limiter l’affichage aux informations que vous souhaitez.

![][2]

<!--Image references-->
[1]: ./media/app-service-logic-create-a-b2b-process/Flow.png
[2]: ./media/app-service-logic-create-a-b2b-process/Tracking.png
 

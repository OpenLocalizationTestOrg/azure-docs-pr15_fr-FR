<properties
   pageTitle="Créer des messages logique d’application à l’aide de VETR dans les applications de logique dans le Service d’application Azure | Microsoft Azure"
   description="Valider, coder et transformer les fonctionnalités de BizTalk XML services"
   services="logic-apps"
   documentationCenter=".net,nodejs,java"
   authors="rajeshramabathiran"
   manager="erikre"
   editor=""/>

<tags
   ms.service="logic-apps"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="04/20/2016"
   ms.author="rajram"/>


# <a name="create-eai-logic-app-using-vetr"></a>Créer l’application d’une logique de messages à l’aide de VETR

[AZURE.INCLUDE [app-service-logic-version-message](../../includes/app-service-logic-version-message.md)]

La plupart des scénarios d’intégration d’applications d’entreprise (messages) organisée servant d’intermédiaire données entre une source et une destination. Ces scénarios ont souvent un ensemble d’exigences courantes :

- Assurez-vous que les données provenant de différents systèmes sont correctement formatées.
- Effectuez « recherche » données entrant pour prendre des décisions.
- Convertir les données d’un format à un autre. Par exemple, convertir les données à partir de format des données d’un système CRM au format de données d’un système de planification.
- Acheminer des données à l’application souhaitée ou système.

Cet article vous explique un modèle d’intégration courantes : « médiation message à sens unique » ou VETR (valider, Enrich, transformation, itinéraire). Le modèle VETR médiation des données entre une entité source et une destination. En règle générale, la source et la destination des sources de données.

Pensez à un site Web qui accepte les commandes. Utilisateurs publient des commandes dans le système à l’aide de HTTP. Les coulisses, le système valide les données entrantes exactitude, convertir et persiste dans une file d’attente de Bus des services pour un traitement approfondi. Le système prend des commandes la file d’attente, digne dans un format particulier. Par conséquent, le flux de bout en bout est la suivante :

**HTTP** → **Valider** → **transformer** → **Bus des services**

![Flux de base VETR][1]

Les applications de l’API BizTalk suivants aider à créer ce modèle :

* **HTTP déclencheur** - Source à l’événement de déclenchement du message
* **Valider** - valide exactitude des données entrantes
* **Transformer** - transformations données entrant format au format requis par système en aval
* **Service Connecteur** - entité de Destination dans laquelle les données sont envoyées


## <a name="constructing-the-basic-vetr-pattern"></a>Construire le modèle de base VETR
### <a name="the-basics"></a>Concepts de base

Dans le portail Azure, sélectionnez **+ Nouveau**, sélectionnez **Web + Mobile**, puis **Application logique**. Sélectionnez un nom, emplacement, abonnement, groupe de ressources et l’emplacement qui vous convient. Groupes de ressources agissent comme conteneurs pour vos applications ; toutes les ressources de votre application atteindre le même groupe de ressources.

Ensuite, nous allons ajouter déclencheurs et actions.


## <a name="add-http-trigger"></a>Ajouter un déclencheur HTTP
1. Dans les **Modèles d’application logique**, sélectionnez **créer à partir de zéro**.
1. Sélectionnez **HTTP récepteur** dans la galerie pour créer un. Appelez-le **HTTP1**.
2. Définir la **Envoyer automatiquement les réponses ?** définissant sur false. Configurer l’action du déclencheur par paramètre _Méthode HTTP_ _POST_ et paramètre _URL Relative_ à _/OneWayPipeline_:  
    ![HTTP déclencheur][2]
3. Sélectionnez la coche verte pour terminer le déclencheur.

## <a name="add-validate-action"></a>Ajouter valider Action

À présent, nous allons entrer des actions qui s’exécutent lorsque le déclencheur s’exécute — c'est-à-dire que chaque fois qu’un appel est reçu sur le point de terminaison HTTP.

1. Ajouter **BizTalk XML validation** de la galerie et nommez-la _(Validate1)_ pour créer une instance.
2. Configurer un schéma XSD pour valider les messages entrants XML. Sélectionnez l’action _Valider_ et sélectionnez _triggers('httplistener').outputs. Contenu_ en tant que la valeur du paramètre _inputXml_ .

À présent, l’action valider est la première action après que l’auditeur HTTP : 

![Validation BizTalk XML][3]

De même, nous allons ajouter le reste des actions. 

## <a name="add-transform-action"></a>Ajouter une action de transformation
Nous allons configurez transformations pour centrer les données entrantes.

1. Ajouter **Le Service BizTalk transformer** de la galerie.
2. Pour configurer une transformation pour transformer les messages entrants XML, sélectionnez l’action **transformer** en tant que l’action à effectuer lorsque cette API est appelée. Sélectionnez ```triggers(‘httplistener’).outputs.Content``` comme valeur pour _inputXml_. *Map* est un paramètre facultatif étant donné que les données entrantes correspond à toutes les transformations configurées et celles qui correspondent au schéma sont appliquées.
3. Enfin, la transformation s’exécute uniquement si valider a réussi. Pour configurer cette condition, sélectionnez l’icône d’engrenage dans le coin supérieur droit, puis sélectionnez _Ajouter une condition soit remplie_. Définissez la condition à ```equals(actions('xmlvalidator').status,'Succeeded')```:  

![Transformations BizTalk][4]


## <a name="add-service-bus-connector"></a>Ajouter le Service Connecteur
Ensuite, nous allons ajouter la destination — une file d’attente Service Bus — pour écrire des données à.

1. Ajouter un **Connecteur Service** à partir de la galerie. Définissez le **nom** sur _Servicebus1_, définissez la **Chaîne de connexion** à la chaîne de connexion à votre instance de bus de service, la valeur **Nom de l’entité** _file d’attente_et ignorer le **nom de l’abonnement**.
2. Sélectionnez l’action **Envoyer un Message** et la valeur du champ de **contenu** pour l’action _actions('transformservice').outputs. OutputXml_.
3. Affectez au champ **Type de contenu** *application/xml*:  

![Bus des services][5]


## <a name="send-http-response"></a>Envoyer les réponses HTTP
Une fois que traitement du pipeline est terminé, renvoyer une réponse HTTP en cas de réussite et échec les étapes suivantes :

1. Ajoutez un **Récepteur HTTP** à partir de la galerie et sélectionnez l’action **Envoyer de réponse HTTP** .
2. Définissez **ID de réponse** pour envoyer le *Message*.
2. Définissez **Contenu réponse** sur *le traitement Pipeline est terminé*.
3. **Code d’état de réponse** à *200* pour indiquer HTTP 200 OK.
4. Sélectionnez la liste déroulante dans le coin supérieur droit, puis sélectionnez **Ajouter une condition soit remplie**.  Définir la condition à l’expression suivante :  
    ```@equals(actions('azureservicebusconnector').status,'Succeeded')```  <br/>
5. Répétez ces étapes pour envoyer une réponse HTTP en cas d’échec également. Modifier la **Condition** à l’expression suivante :  
```@not(equals(actions('azureservicebusconnector').status,'Succeeded'))``` <br/>
6. Sélectionnez **OK** puis **créer**.



## <a name="completion"></a>Saisie semi-automatique
Chaque fois qu’une personne envoie un message au point de terminaison HTTP, il déclenche l’application et effectue les actions que vous venez de créer. Pour gérer les ces applications logique vous créez, sélectionnez **Parcourir** dans le portail Azure, puis sélectionnez **Applications logique**. Sélectionnez votre application pour afficher plus d’informations.

Certaines rubriques utiles :

[Gérer et analyser vos applications API et les connecteurs](app-service-logic-monitor-your-connectors.md)  <br/>
[Surveiller des applications de votre logique](app-service-logic-monitor-your-logic-apps.md)

<!--image references -->
[1]: ./media/app-service-logic-create-EAI-logic-app-using-VETR/BasicVETR.PNG
[2]: ./media/app-service-logic-create-EAI-logic-app-using-VETR/HTTPListener.PNG
[3]: ./media/app-service-logic-create-EAI-logic-app-using-VETR/BizTalkXMLValidator.PNG
[4]: ./media/app-service-logic-create-EAI-logic-app-using-VETR/BizTalkTransforms.PNG
[5]: ./media/app-service-logic-create-EAI-logic-app-using-VETR/AzureServiceBus.PNG

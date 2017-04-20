Voici comment procéder pour utiliser le déclencheur **Service Bus - lors de la réception d’un message dans une file d’attente** pour démarrer un flux de travail de l’application logique lorsqu’un nouvel élément est envoyé à une file d’attente Bus des services.  

>[AZURE.NOTE]Vous devrez connectez-vous à l’aide de la chaîne de connexion de Service Bus si vous n’avez pas encore créé une connexion au Service Bus.  

1. Dans la zone de recherche dans le Concepteur d’applications logique, entrez **bus des services**. Sélectionnez ensuite le déclencheur **Service Bus - lors de la réception d’un message dans une file d’attente** .  
![Image de déclenchement Service Bus 1](./media/connectors-create-api-servicebus/trigger-1.png)   
- La boîte de dialogue **lors de la réception d’un message dans une file d’attente** s’affiche.  
![Image de déclenchement Service Bus 2](./media/connectors-create-api-servicebus/trigger-2.png)   
- Entrez le nom de la file d’attente Bus des services que vous voulez que le déclencheur à surveiller.   
![Image de déclenchement Bus des Services 3](./media/connectors-create-api-servicebus/trigger-3.png)   

À ce stade, votre application logique a été configurée avec un déclencheur. Lorsqu’un nouvel élément est reçu dans la file d’attente que vous avez sélectionné, le déclencheur se lance une exécution des autres déclencheurs et actions dans le flux de travail.    

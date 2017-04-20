<properties
   pageTitle="Scénario d’application logique : créer un déclencheur d’Azure fonctions Service Bus | Microsoft Azure"
   description="Utilisez les fonctions Azure pour créer un déclencheur Bus des services pour une application de logique"
   services="logic-apps,functions"
   documentationCenter=".net,nodejs,java"
   authors="jeffhollan"
   manager="dwrede"
   editor=""/>

<tags
   ms.service="logic-apps"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration"
   ms.date="05/23/2016"
   ms.author="jehollan"/>

# <a name="logic-app-scenario-create-an-azure-service-bus-trigger-by-using-azure-functions"></a>Scénario d’application logique : créer un déclencheur Bus des services Azure à l’aide de fonctions Azure

Vous pouvez utiliser les fonctions Azure pour créer un déclencheur pour une application logique lorsque vous avez besoin déployer un récepteur longue ou une tâche. Par exemple, vous pouvez créer une fonction qui écoutent une file d’attente et alors déclencher immédiatement une application logique comme un déclencheur d’émission.

## <a name="build-the-logic-app"></a>Créer l’application logique

Dans cet exemple, vous avez une fonction en cours d’exécution pour chaque application logique qui doit être déclenchée. Commencez par créer une application logique qui comporte un déclencheur de demande HTTP. La fonction appelle ce point de terminaison réception d’un message file d’attente.  

1. Créer une nouvelle application logique ; Sélectionnez le déclencheur **manuel - lorsqu’une demande HTTP correctement après réception** .  
   Si vous le souhaitez, vous pouvez spécifier un schéma JSON à utiliser avec le message file d’attente à l’aide d’un outil comme [jsonschema.net](http://jsonschema.net). Collez le schéma dans le déclencheur. Ceci permet du Concepteur de comprendre la forme des données et le plus facilement flux propriétés par le biais du flux de travail.
1. Ajouter des étapes supplémentaires qui doivent être effectuées après la réception d’un message file d’attente. Par exemple, envoyer un message électronique via Office 365.  
1. Enregistrer l’application logique pour générer l’URL de rappel du déclencheur de cette application logique. L’URL s’affiche sur la carte de déclencheur.

![Le rappel URL apparaît sur la carte de déclencheur][1]

## <a name="build-the-function"></a>Créer la fonction

Ensuite, vous devez créer une fonction qui va agir en tant que le déclencheur écouter la file d’attente.

1. Dans le [portail de fonctions Azure](https://functions.azure.com/signin), sélectionnez **Nouvelle fonction**, puis le modèle **ServiceBusQueueTrigger - c#** .

    ![Portail de fonctions Azure][2]

2. Configurer la connexion à la file d’attente Bus des services (qui utilise le Kit de développement Azure Service Bus `OnMessageReceive()` récepteur).
3. Écrire une fonction simple pour appeler le point de terminaison de l’application logique (créé précédemment) en utilisant le message file d’attente en tant que déclencheur. Voici un exemple complet d’une fonction. L’exemple utilise un `application/json` type de contenu de message, mais vous pouvez le modifier si nécessaire.

   ```
   using System;
   using System.Threading.Tasks;
   using System.Net.Http;
   using System.Text;

   private static string logicAppUri = @"https://prod-05.westus.logic.azure.com:443/.........";

   public static void Run(string myQueueItem, TraceWriter log)
   {

       log.Info($"C# ServiceBus queue trigger function processed message: {myQueueItem}");
       using (var client = new HttpClient())
       {
           var response = client.PostAsync(logicAppUri, new StringContent(myQueueItem, Encoding.UTF8, "application/json")).Result;
       }
   }
   ```

Pour tester, ajoutez un message file d’attente via un outil comme [Service Bus Explorer](https://github.com/paolosalvatori/ServiceBusExplorer). Voir l’application logique fire immédiatement après que la fonction reçoit le message.

<!-- Image References -->
[1]: ./media/app-service-logic-scenario-function-sb-trigger/manualTrigger.PNG
[2]: ./media/app-service-logic-scenario-function-sb-trigger/newQueueTriggerFunction.PNG

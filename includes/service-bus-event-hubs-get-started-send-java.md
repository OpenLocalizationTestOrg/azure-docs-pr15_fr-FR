## <a name="send-messages-to-event-hubs"></a>Envoyer des messages à Hubs d’événement

La bibliothèque de client Java pour événement Hubs est disponible pour une utilisation dans les projets Maven à partir du [Référentiel Central Maven](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-eventhubs%22)et peut être référencée à l’aide de la déclaration de dépendance suivante à l’intérieur de votre fichier de projet Maven :    

``` XML
<dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>azure-eventhubs</artifactId>
    <version>{VERSION}</version>
</dependency>
```
 
Pour des différents types d’environnements de génération, vous pouvez obtenir explicitement les plus récentes JAR fichiers à partir du [Référentiel Central Maven](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-eventhubs%22) ou à partir [du point de distribution release sur GitHub](https://github.com/Azure/azure-event-hubs/releases).  

Pour un éditeur d’événements simple, importer les packages *com.microsoft.azure.eventhubs* pour les classes client événement Hubs et *com.microsoft.azure.servicebus* pour les classes utilitaire tels que des exceptions courantes qui sont partagées avec le client de messagerie Bus des services Azure. 

Pour l’exemple suivant, vous devez tout d’abord créer un projet Maven pour une application console/shell dans votre environnement de développement Java favori. La classe sera appelée ```Send```.     

``` Java

import java.io.IOException;
import java.nio.charset.*;
import java.util.*;
import java.util.concurrent.ExecutionException;

import com.microsoft.azure.eventhubs.*;
import com.microsoft.azure.servicebus.*;

public class Send
{
    public static void main(String[] args) 
            throws ServiceBusException, ExecutionException, InterruptedException, IOException
    {
```

Remplacez l’espace de noms et les noms Hub de l’événement avec les valeurs utilisées lorsque vous avez créé le Hub de l’événement.

``` Java
    final String namespaceName = "----ServiceBusNamespaceName-----";
    final String eventHubName = "----EventHubName-----";
    final String sasKeyName = "-----SharedAccessSignatureKeyName-----";
    final String sasKey = "---SharedAccessSignatureKey----";
    ConnectionStringBuilder connStr = new ConnectionStringBuilder(namespaceName, eventHubName, sasKeyName, sasKey);
```

Ensuite, créez un événement au singulier en transformant une chaîne en ses octets le codage UTF-8. Nous puis créer une nouvelle instance de client événement Hubs à partir de la chaîne de connexion et envoyer le message.   

``` Java 
                
    byte[] payloadBytes = "Test AMQP message from JMS".getBytes("UTF-8");
    EventData sendEvent = new EventData(payloadBytes);
    
    EventHubClient ehClient = EventHubClient.createFromConnectionStringSync(connStr.toString());
    ehClient.sendSync(sendEvent);
    }
}

``` 

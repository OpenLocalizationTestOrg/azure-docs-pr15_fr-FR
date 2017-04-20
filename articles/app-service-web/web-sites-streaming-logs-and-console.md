<properties 
    pageTitle="Console et des journaux de diffusion en continu" 
    description="Les journaux de diffusion en continu et présentation de la console" 
    authors="btardif" 
    manager="wpickett" 
    editor="" 
    services="app-service\web" 
    documentationCenter=""/>

<tags 
    ms.service="app-service-web" 
    ms.workload="web" 
    ms.tgt_pltfrm="na" 
    ms.devlang="multiple" 
    ms.topic="article" 
    ms.date="10/12/2016" 
    ms.author="byvinyal"/>

# <a name="streaming-logs-and-the-console"></a>Les journaux de diffusion en continu et la Console

## <a name="streaming-logs"></a>Journaux de diffusion en continu

Le **portail Azure** fournit une visionneuse journal diffusion en continu intégrée qui vous permet d’afficher les événements de suivi à partir de vos applications de **Service d’application** en temps réel.  

Configuration de cette fonctionnalité requiert quelques étapes simples :

- Écrire traces dans votre code
- Activer **les journaux de Diagnostic** d’Application pour votre application
- Afficher le flux à partir de l’interface utilisateur de **Diffusion en continu les journaux** intégrée dans le **portail Azure**.

### <a name="how-to-write-traces-in-your-code"></a>Comment rédiger traces dans votre code ###

Il est facile d’écriture traces dans votre code.  En c#, il est aussi simple que d’écrire le code suivant :

`````````````````````````
Trace.TraceInformation("My trace statement");
`````````````````````````

`````````````````````````
Trace.TraceWarning("My warning statement");
`````````````````````````

`````````````````````````
Trace.TraceError("My error statement");
`````````````````````````

La classe Trace sont situées dans l’espace de noms System.Diagnostics.

Dans une application node.js, vous pouvez écrire ce code pour obtenir le même résultat :

`````````````````````````
console.log("My trace statement").
`````````````````````````

### <a name="how-to-enable-and-view-the-streaming-logs"></a>Comment activer et afficher la diffusion en continu les journaux
![][BrowseSitesScreenshot]Diagnostics sont activés par l’application. Commencez par parcourir au site que vous voulez activer cette fonctionnalité.  
  
![][DiagnosticsLogs]À partir du menu Paramètres, faites défiler jusqu'à la section de **surveillance** et cliquez sur **Les journaux de Diagnostic (1)**. Puis **(2) activer** **L’Application enregistre (système de fichiers)** ou **L’Application enregistre (blob)** l’option **niveau** permet de modifier le niveau de gravité de traces à capturer. Si vous essayez simplement pour vous familiariser avec la fonctionnalité, définissez le niveau de **commentaires** pour vous assurer que toutes vos instructions de traçage sont collectées.

Cliquez sur **Enregistrer** dans la partie supérieure de la cuillère et que vous êtes prêt afficher les journaux.

>[AZURE.NOTE] Plus le **niveau de gravité** plus les ressources sont consommées au journal et les autres traces sont produites. Vérifiez que le **niveau de gravité** est configuré sur le niveau de détail approprié pour un site de trafic élevé ou de production. 

![][StreamingLogsScreenshot]Pour afficher la **diffusion en continu les journaux** au sein du portail Azure, cliquez sur **flux de journal (1)** également dans la section **analyse** du menu Paramètres. Si votre application écrit activement des instructions de traçage, vous devez les voir dans la **diffusion en continu (2) ouvre une interface utilisateur** en temps quasi réel.

## <a name="console"></a>Console
Le **portail Azure** offre un accès console dans votre application. Vous pouvez explorer le système de fichiers de votre application et exécuter des scripts powershell/cmd. Vous êtes lié par les mêmes autorisations définir en tant que votre code de l’application en cours d’exécution lors de l’exécution de commandes de la console. Accès à des répertoires protégés ou exécuter des scripts nécessitant des autorisations élevées est bloqué.  

![][ConsoleScreenshot]À partir du menu Paramètres, faites défiler vers le bas jusqu'à la section **Outils de développement** et cliquez sur **Console (1)** et la **console (2)** de l’interface utilisateur s’ouvre à droite.

Pour vous familiariser avec la **console**, essayez les commandes de base telles que :

`````````````````````````
dir
`````````````````````````

`````````````````````````
cd
`````````````````````````

<!-- Images. -->
[DiagnosticsLogs]: ./media/web-sites-streaming-logs-and-console/diagnostic-logs.png
[BrowseSitesScreenshot]: ./media/web-sites-streaming-logs-and-console/browse-sites.png
[StreamingLogsScreenshot]: ./media/web-sites-streaming-logs-and-console/streaming-logs.png
[ConsoleScreenshot]: ./media/web-sites-streaming-logs-and-console/console.png

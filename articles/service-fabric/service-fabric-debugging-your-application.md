<properties
   pageTitle="Déboguer votre application dans Visual Studio | Microsoft Azure"
   description="Améliorez la fiabilité et les performances de vos services en développement et de leur débogage dans Visual Studio sur un cluster de développement local."
   services="service-fabric"
   documentationCenter=".net"
   authors="vturecek"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="06/21/2016"
   ms.author="vturecek;mikhegn"/>

# <a name="debug-your-service-fabric-application-by-using-visual-studio"></a>Déboguer votre application de Service tissu à l’aide de Visual Studio

## <a name="debug-a-local-service-fabric-application"></a>Déboguer une application de Service TISSU locale

Vous pouvez gagner du temps et argent en déployant et débogage de votre application Azure Service tissu dans un cluster de développement ordinateur local. Visual Studio peut déployer l’application sur le cluster local et vous connecter automatiquement le débogueur à toutes les instances de votre application.

1. Démarrer un cluster de développement local en suivant les étapes de [la configuration de votre environnement de développement tissu de Service](service-fabric-get-started.md).

2. Appuyez sur **F5** ou cliquez sur **Déboguer** > **Démarrer le débogage**.

    ![Démarrer le débogage d’une application][startdebugging]

3. Définir les points d’arrêt dans votre code et l’exécution de l’application en cliquant sur les commandes dans le menu **Déboguer** .

    > [AZURE.NOTE] Visual Studio se connecte à toutes les instances de votre application. Pendant que vous êtes Parcourir du code, les points d’arrêt peut-être obtenir atteint par plusieurs processus, ce qui crée de sessions simultanées. Essayez de désactiver les points d’arrêt après que qu’ils vous touchez, à l’aide de chaque point d’arrêt conditionnel sur l’ID de thread ou à l’aide des événements de diagnostic.

4. La fenêtre **Des événements de Diagnostic** s’ouvrent automatiquement afin d’afficher des événements de diagnostic en temps réel.

    ![Afficher les événements de diagnostic en temps réel][diagnosticevents]

5. Vous pouvez également ouvrir la fenêtre **Des événements de Diagnostic** dans l’Explorateur de Cloud.  Sous **Tissu de Service**, avec le bouton droit n’importe quel nœud et choisissez **Affichage Streaming Traces**.

    ![Ouvrir la fenêtre des événements de diagnostic][viewdiagnosticevents]

    Si vous voulez filtrer vos traces à une application ou un service spécifique, il suffit d’activer traces diffusion en continu sur ce service spécifique ou cette application.

6. Les événements de diagnostic peuvent être vues dans le fichier **ServiceEventSource.cs** généré automatiquement et sont appelées à partir de code de l’application.

    ```csharp
    ServiceEventSource.Current.ServiceMessage(this, "My ServiceMessage with a parameter {0}", result.Value.ToString());
    ```

7. La fenêtre **Des événements de Diagnostic** prend en charge le filtrage et la pause inspectant des événements en temps réel.  Le filtre est une recherche de chaîne simple de ce message, y compris son contenu.

    ![Filtrer, interrompre et reprendre ou examiner des événements en temps réel][diagnosticeventsactions]

8. Services de débogage sont identique au débogage d’une autre application. Vous définirez en règle générale, les points d’arrêt via Visual Studio pour le débogage facile. Bien que Collections fiable répliquer entre plusieurs nœuds, ils toujours implémentent IEnumerable. Cela signifie que vous pouvez utiliser l’affichage des résultats dans Visual Studio pendant le débogage pour voir ce que vous avez enregistré à l’intérieur. Définissez simplement un point d’arrêt n’importe où dans votre code.

    ![Démarrer le débogage d’une application][breakpoint]

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->

## <a name="debug-a-remote-service-fabric-application"></a>Déboguer une application de Service tissu à distance

Si vos applications de Service tissu sont exécutent sur un cluster de Service tissu dans Azure, vous ne pouvez déboguer à distance ces éléments, directement à partir de Visual Studio.

> [AZURE.NOTE] La fonctionnalité requiert [Service TISSU SDK 2.0](http://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-VS2015) et [Azure SDK pour .NET 2,9](https://azure.microsoft.com/downloads/).    

<!-- -->
> [AZURE.WARNING] Le débogage distant est destiné pour les scénarios de développement/test et ne pas à utiliser dans les environnements de production, en raison de l’impact sur les applications en cours d’exécution.

1. Accédez à votre cluster dans **l’Explorateur de nuage**, avec le bouton droit et sélectionnez **Activer le débogage**

    ![Activer le débogage distant][enableremotedebugging]

    Cela sera coup le processus d’activation de l’extension de débogage à distance sur vos nœuds de cluster, ainsi que les configurations requises réseau.

2. Cliquez sur le nœud de cluster dans **l’Explorateur de Cloud**, puis sélectionnez **Joindre débogueur**

    ![Attacher débogueur][attachdebugger]

3. Dans la boîte de dialogue **Attacher au processus** , choisissez le processus que vous souhaitez déboguer, puis cliquez sur **joindre**

    ![Choisissez le processus][chooseprocess]

    Le nom du processus que vous souhaitez joindre, est égal au nom de votre projet assemblage service.

    Le débogueur s’attache à tous les nœuds du processus en cours d’exécution.
    - Dans le cas où vous soyez débogage d’un service sans état, toutes les instances du service sur tous les nœuds font partie de la session de débogage.
    - Si vous êtes débogage d’un service dynamique, réplica principal de n’importe quelle partition seront active et par conséquent capturées par le débogueur. Si le réplica principal se déplace pendant la session de débogage, le traitement de ce réplica sera toujours partie de la session de débogage.
    - Pour intercepter uniquement les partitions pertinentes ou instances d’un service donné, vous pouvez utiliser des points d’arrêt conditionnels saut uniquement une partition spécifique ou une instance.

    ![Point d’arrêt conditionnel][conditionalbreakpoint]

    > [AZURE.NOTE] Actuellement nous ne prennent pas en charge débogage un cluster de Service tissu avec plusieurs instances du même nom exécutable service.

4. Une fois que vous avez terminé de débogage de votre application, vous pouvez désactiver l’extension de débogage à distance en double-cliquant sur le cluster dans **l’Explorateur de Cloud** et cliquez sur **Désactiver le débogage**

    ![Désactiver le débogage à distance][disableremotedebugging]

## <a name="streaming-traces-from-a-remote-cluster-node"></a>Diffusion en continu traces à partir d’un nœud de cluster distant

Vous pouvez également sur les traces flux directement à partir d’un nœud de cluster distant pour Visual Studio. Cette fonctionnalité permet de flux ETW tracer des événements, produites sur un nœud du Service TISSU directement dans Visual Studio.

> [AZURE.NOTE] La fonctionnalité requiert [Service TISSU SDK 2.0](http://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-VS2015) et [Azure SDK pour .NET 2,9](https://azure.microsoft.com/downloads/).

<!-- -->
> [AZURE.WARNING]Diffusion en continu traces est destinée pour les scénarios de développement/test et ne pas à utiliser dans les environnements de production, en raison de l’impact sur les applications en cours d’exécution.
> Dans un scénario de production, vous devez compter sur transfert d’événements à l’aide des Diagnostics Azure.

1. Accédez à votre cluster dans **l’Explorateur de nuage**, avec le bouton droit et sélectionnez **Activer les Traces de diffusion en continu**

    ![Activer les traces de diffusion en continu à distance][enablestreamingtraces]

    Cela sera coup le processus d’activation de l’extension traces diffusion en continu sur vos nœuds de cluster, ainsi que les configurations requises réseau.

2. Développer l’élément de **nœuds** dans le **Cloud Explorer**, cliquez sur le nœud que vous voulez diffuser en continu les traces à partir de, puis sélectionnez **Traces de diffusion en continu de vue**

    ![Affichage à distance en continu traces][viewremotestreamingtraces]

    Répétez l’étape 2 pour autant de nœuds que vous souhaitez voir traces à partir de. Chaque flux de nœuds apparaît dans une fenêtre dédiée.

    Vous pouvez désormais voir les traces émises par tissu de Service et vos services. Si vous voulez filtrer les événements pour afficher uniquement une application spécifique, tapez simplement le nom de l’application dans le filtre.

    ![Affichage de la diffusion en continu de traces][viewingstreamingtraces]

4. Une fois que vous avez fini traces diffusion en continu de votre cluster, vous pouvez désactiver traces de diffusion en continu à distance, par clic droit sur le cluster dans **l’Explorateur de Cloud** puis cliquez sur **Désactiver les Traces de diffusion en continu**

    ![Désactiver les traces de diffusion en continu à distance][disablestreamingtraces]

## <a name="next-steps"></a>Étapes suivantes

- [Test un service tissu de Service](service-fabric-testability-overview.md).
- [Gestion de vos applications de Service tissu dans Visual Studio](service-fabric-manage-application-in-visual-studio.md).

<!--Image references-->
[startdebugging]: ./media/service-fabric-debugging-your-application/startdebugging.png
[diagnosticevents]: ./media/service-fabric-debugging-your-application/diagnosticevents.png
[viewdiagnosticevents]: ./media/service-fabric-debugging-your-application/viewdiagnosticevents.png
[diagnosticeventsactions]: ./media/service-fabric-debugging-your-application/diagnosticeventsactions.png
[breakpoint]: ./media/service-fabric-debugging-your-application/breakpoint.png
[enableremotedebugging]: ./media/service-fabric-debugging-your-application/enableremotedebugging.png
[attachdebugger]: ./media/service-fabric-debugging-your-application/attachdebugger.png
[chooseprocess]: ./media/service-fabric-debugging-your-application/chooseprocess.png
[conditionalbreakpoint]: ./media/service-fabric-debugging-your-application/conditionalbreakpoint.png
[disableremotedebugging]: ./media/service-fabric-debugging-your-application/disableremotedebugging.png
[enablestreamingtraces]: ./media/service-fabric-debugging-your-application/enablestreamingtraces.png
[viewingstreamingtraces]: ./media/service-fabric-debugging-your-application/viewingstreamingtraces.png
[viewremotestreamingtraces]: ./media/service-fabric-debugging-your-application/viewremotestreamingtraces.png
[disablestreamingtraces]: ./media/service-fabric-debugging-your-application/disablestreamingtraces.png

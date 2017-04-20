<properties
    pageTitle="Suivre le flux dans une Application de Services de Cloud avec Azure Diagnostics | Microsoft Azure"
    description="Ajouter le suivi des messages à l’application Azure pour aider au débogage, mesurer les performances, surveillance, l’analyse du trafic et plus."
    services="cloud-services"
    documentationCenter=".net"
    authors="rboucher"
    manager="jwhit"
    editor=""/>

<tags
    ms.service="cloud-services"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="02/20/2016"
    ms.author="robb"/>



# <a name="trace-the-flow-of-a-cloud-services-application-with-azure-diagnostics"></a>Suivre le flux d’une application de Services de Cloud avec Diagnostics Azure

Suivi est une méthode vous permettant de contrôler l’exécution de votre application en cours d’exécution. Vous pouvez utiliser les classes [System.Diagnostics.Trace](https://msdn.microsoft.com/library/system.diagnostics.trace.aspx)et [System.Diagnostics.Debug](https://msdn.microsoft.com/library/system.diagnostics.debug.aspx) [System.Diagnostics.TraceSource](https://msdn.microsoft.com/library/system.diagnostics.tracesource.aspx) pour enregistrer les informations relatives aux erreurs et l’exécution d’applications dans les journaux, des fichiers texte ou d’autres appareils pour une analyse ultérieure. Pour plus d’informations sur le traçage, consultez [traçage et instrumentation d’Applications](https://msdn.microsoft.com/library/zs6s4h68.aspx).


## <a name="use-trace-statements-and-trace-switches"></a>Utilisez les instructions de traçage et commutateurs de traçage

Suivi de mettre en œuvre dans votre application de Services Cloud en ajoutant le [DiagnosticMonitorTraceListener](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.diagnostics.diagnosticmonitortracelistener.aspx) à la configuration d’application et effectuer des appels à System.Diagnostics.Trace ou System.Diagnostics.Debug code de l’application. Utiliser le fichier de configuration *app.config* pour les rôles de travail et *web.config* pour les rôles web. Lorsque vous créez un nouveau service hébergé à l’aide d’un modèle de Visual Studio, Diagnostics Azure sont automatiquement ajoutés au projet et le DiagnosticMonitorTraceListener est ajoutée au fichier de configuration appropriées pour les rôles que vous ajoutez.

Pour plus d’informations sur le placement des instructions de traçage, consultez [Comment : ajouter des instructions de traçage au Code de l’Application](https://msdn.microsoft.com/library/zd83saa2.aspx).

En plaçant les [Commutateurs de traçage](https://msdn.microsoft.com/library/3at424ac.aspx) dans votre code, vous pouvez contrôler si le traçage se produit et jusqu'à quel point. Cela vous permet de contrôler le statut de votre application dans un environnement de production. Ceci est particulièrement important dans une application de gestion qui utilise plusieurs composants exécutant sur plusieurs ordinateurs. Pour plus d’informations, voir [Comment : configurer des commutateurs de traçage](https://msdn.microsoft.com/library/t06xyy08.aspx).

## <a name="configure-the-trace-listener-in-an-azure-application"></a>Configurer le récepteur de suivi des messages dans une application Azure

Trace, débogage et TraceSource, nécessitent configuration « récepteurs » pour collecter et enregistrer les messages qui sont envoyés. Récepteurs collectent, stockent et acheminer les messages de traçage. Ils dirigent la sortie de traçage vers une cible appropriée, tel qu’un journal, une fenêtre ou un fichier texte. Diagnostics de Windows Azure utilise la classe [DiagnosticMonitorTraceListener](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.diagnostics.diagnosticmonitortracelistener.aspx) .

Pour effectuer la procédure suivante, vous devez initialisation du moniteur diagnostic Azure. Pour ce faire, voir [Activer les Diagnostics de Microsoft Azure](cloud-services-dotnet-diagnostics.md).

Notez que si vous utilisez les modèles qui sont fournis par Visual Studio, la configuration de que l’auditeur est ajoutée automatiquement à votre place.


### <a name="add-a-trace-listener"></a>Ajouter un récepteur de suivi

1. Ouvrez le fichier web.config ou app.config pour votre rôle.
2. Ajoutez le code suivant dans le fichier. Modifiez l’attribut de Version pour utiliser le numéro de version de l’assembly que vous faites référence. La version d’assembly ne change pas nécessairement à chaque version Azure SDK sauf s’il existe des mises à jour vers celui-ci.

    ```
    <system.diagnostics>
        <trace>
            <listeners>
                <add type="Microsoft.WindowsAzure.Diagnostics.DiagnosticMonitorTraceListener,
                  Microsoft.WindowsAzure.Diagnostics,
                  Version=2.8.0.0,
                  Culture=neutral,
                  PublicKeyToken=31bf3856ad364e35"
                  name="AzureDiagnostics">
                  <filter type="" />
                </add>
            </listeners>
        </trace>
    </system.diagnostics>
    ```
    >[AZURE.IMPORTANT] Vérifiez que vous disposez d’une référence de projet à l’assembly Microsoft.WindowsAzure.Diagnostics. Mettre à jour le numéro de version dans le fichier xml ci-dessus pour correspondre à la version de l’assembly Microsoft.WindowsAzure.Diagnostics référencé.

3. Enregistrez le fichier de configuration.

Pour plus d’informations sur les récepteurs, voir [Suivi des récepteurs](https://msdn.microsoft.com/library/4y5y10s7.aspx).

Une fois que vous effectuez les étapes pour ajouter le récepteur, vous pouvez ajouter des instructions de traçage à votre code.


### <a name="to-add-trace-statement-to-your-code"></a>Pour ajouter des instructions de traçage à votre code

1. Ouvrez un fichier source pour votre application. Par exemple, le <RoleName>fichier .cs pour le rôle de collaborateur ou web.
2. Ajoutez les éléments suivants à l’aide d’instruction si elle n’a pas déjà été ajouté :
    ```
        using System.Diagnostics;
    ```
3. Ajoutez des instructions de repérer l’endroit où vous souhaitez capturer des informations sur l’état de votre application. Vous pouvez utiliser plusieurs méthodes pour mettre en forme le résultat de l’instruction Trace. Pour plus d’informations, voir [Comment : ajouter des instructions de traçage au Code de l’Application](https://msdn.microsoft.com/library/zd83saa2.aspx).
4. Enregistrez le fichier source.

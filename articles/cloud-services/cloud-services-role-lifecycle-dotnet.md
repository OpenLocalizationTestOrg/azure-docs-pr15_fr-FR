<properties 
pageTitle="Gérer les événements de cycle de vie de Service Cloud | Microsoft Azure" 
description="Découvrez comment les méthodes de cycle de vie d’un rôle Service Cloud peuvent être utilisées dans .NET" 
services="cloud-services" 
documentationCenter=".net" 
authors="Thraka" 
manager="timlt" 
editor=""/>
<tags 
ms.service="cloud-services" 
ms.workload="tbd" 
ms.tgt_pltfrm="na" 
ms.devlang="na" 
ms.topic="article" 
ms.date="09/06/2016" 
ms.author="adegeo"/>

# <a name="customize-the-lifecycle-of-a-web-or-worker-role-in-net"></a>Personnaliser le cycle de vie d’un rôle Web ou collaborateur dans .NET

Lorsque vous créez un rôle de collaborateur, vous étendez la classe [RoleEntryPoint](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.aspx) qui fournit des méthodes vous permettant de remplacement qui vous permettent de répondent aux événements du cycle de vie. Pour les rôles web ce cours est facultative, vous devez l’utiliser pour répondre aux événements du cycle de vie.

## <a name="extend-the-roleentrypoint-class"></a>Étendre la classe RoleEntryPoint

La classe [RoleEntryPoint](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.aspx) inclut des méthodes qui sont appelées par Azure lorsqu’il **démarre**, **s’exécute**ou **s’arrête** un rôle web ou de travail. Vous pouvez éventuellement substituer ces méthodes pour gérer l’initialisation de rôle, séquences d’arrêt de rôles ou thread de l’exécution du rôle. 

Lors de l’extension **RoleEntryPoint**, vous devez tenir compte des comportements des méthodes suivantes :

-   Les méthodes [OnStart](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.onstart.aspx) et [OnStop](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.onstop.aspx) renvoient une valeur booléenne, il est possible de retourner **false** à partir de ces méthodes.

     Si votre code retourne la **valeur false**, le processus de rôle est brusquement arrêté, sans exécuter une séquence d’arrêt qu’en place. En règle générale, vous devez éviter retourne la **valeur false** à partir de la méthode **OnStart** .
     
-   Une exception non gérée au sein d’une surcharge d’une méthode **RoleEntryPoint** est considérée comme une exception non gérée.

     Si une exception se produit au sein d’une des méthodes du cycle de vie, Azure déclenche l’événement [UnhandledException](https://msdn.microsoft.com/library/system.appdomain.unhandledexception.aspx) et puis le processus est arrêté. Une fois votre rôle a été effectuée en mode hors connexion, il sera redémarré par Azure. Lorsqu’une exception non gérée se produit, l’événement [arrêt](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleenvironment.stopping.aspx) n’est pas déclenché, et la méthode **OnStop** n’est pas appelée.

Si votre rôle ne démarre pas ou recyclage entre l’initialisation, occupé (e) et les États arrêt, votre code peut lever une exception non gérée au sein de l’un des événements du cycle de vie chaque fois que le rôle redémarre. Dans ce cas, utilisez l’événement [UnhandledException](https://msdn.microsoft.com/library/system.appdomain.unhandledexception.aspx) pour déterminer l’origine de l’exception et gérer de façon appropriée. Votre rôle peut également renvoyer à partir de la méthode [Run](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx) , qui provoque le rôle de redémarrer. Pour plus d’informations sur les États de déploiement, voir [Problèmes qui Cause les rôles communs à Corbeille](cloud-services-troubleshoot-common-issues-which-cause-roles-recycle.md).

> [AZURE.NOTE] Si vous utilisez les **Outils Azure pour Microsoft Visual Studio** pour développer votre application, les modèles de projet rôle étendent automatiquement la classe **RoleEntryPoint** pour vous, dans les fichiers *WebRole.cs* et *WorkerRole.cs* .

## <a name="onstart-method"></a>Méthode OnStart

La méthode **OnStart** est appelée lorsque votre instance de rôle est mis en ligne par Azure. Le code OnStart est en cours d’exécution, l’instance de rôle est marqué comme **occupé (e)** et aucun trafic externe ne sera dirigé vers elle par l’équilibrage de charge. Vous pouvez ignorer cette méthode pour effectuer le travail de l’initialisation, telles que la mise en œuvre des gestionnaires d’événements et démarrage [Azure Diagnostics](cloud-services-how-to-monitor.md).

Si **OnStart** renvoie la **valeur true**, l’instance est correctement initialisée et Azure appelle la méthode **RoleEntryPoint.Run** . Si **OnStart** renvoie la **valeur false**, le rôle termine immédiatement, sans exécuter les séquences d’arrêt planifié.

L’exemple suivant montre comment substituer la méthode **OnStart** . Cette méthode configure et démarre un moniteur diagnostic lorsque l’instance de rôle démarre et configure un transfert de données de journalisation pour un compte de stockage :

```csharp
public override bool OnStart()
{
    var config = DiagnosticMonitor.GetDefaultInitialConfiguration();

    config.DiagnosticInfrastructureLogs.ScheduledTransferLogLevelFilter = LogLevel.Error;
    config.DiagnosticInfrastructureLogs.ScheduledTransferPeriod = TimeSpan.FromMinutes(5);

    DiagnosticMonitor.Start("DiagnosticsConnectionString", config);

    return true;
}
```

## <a name="onstop-method"></a>Méthode OnStop

La méthode **OnStop** est appelée après qu’une instance de rôle a été déconnectée par Azure et avant la fin du processus. Vous pouvez ignorer cette méthode pour appeler un code requis pour votre instance de rôle à arrêtée.

> [AZURE.IMPORTANT] Code en cours d’exécution dans la méthode **OnStop** a une durée limitée pour terminer lorsqu’elle est appelée pour des raisons autre qu’un arrêt initialisé par l’utilisateur. Une fois cette durée écoulée, le processus se termine, afin que vous devez vous assurer que le code dans la méthode **OnStop** a la possibilité s’exécutent rapidement ou tolère ne pas en cours d’exécution jusqu'à la fin. La méthode **OnStop** est appelée après que l’événement **arrêt** est élevé.


## <a name="run-method"></a>Méthode Run

Vous pouvez ignorer la méthode **exécuter** pour implémenter un thread longue pour votre instance de rôle.

Substitution de la méthode **exécuter** n’est pas obligatoire ; l’implémentation par défaut démarre un thread qui est en veille indéfiniment. Si vous remplacez la méthode **Run** , votre code doit bloquer indéfiniment. Si la méthode **Run** retourne une valeur, le rôle est automatiquement normalement suppression initiale ; en d’autres termes, Azure déclenche l’événement **arrêt** et appelle la méthode **OnStop** afin que vos séquences d’arrêt peuvent être exécutées avant le rôle est considérée en mode hors connexion.


### <a name="implementing-the-aspnet-lifecycle-methods-for-a-web-role"></a>Mise en œuvre les méthodes de cycle de vie ASP.NET pour un rôle web

Vous pouvez utiliser les méthodes de cycle de vie ASP.NET, en plus de ceux fournis par la classe **RoleEntryPoint** , pour gérer l’initialisation et les séquences de fermeture pour un rôle web. Si vous êtes porter une application ASP.NET existante vers Azure, cela peut être utile à des fins de compatibilité. Les méthodes de cycle de vie ASP.NET sont appelées dans les méthodes **RoleEntryPoint** . La **Application\_démarrer** méthode est appelée après la méthode **RoleEntryPoint.OnStart** se termine. La **Application\_fin** méthode est appelée avant la méthode **RoleEntryPoint.OnStop** est appelée.

## <a name="next-steps"></a>Étapes suivantes
Découvrez comment [créer un package de service cloud](cloud-services-model-and-package.md).
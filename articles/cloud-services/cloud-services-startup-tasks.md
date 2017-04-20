<properties 
pageTitle="Exécuter des tâches de démarrage dans les Services en nuage Azure | Microsoft Azure" 
description="Tâches de démarrage vous permettent de préparer votre environnement de service cloud pour votre application. Cela vous montre comment fonctionnent les tâches de démarrage et comment les rendre" 
services="cloud-services" 
documentationCenter="" 
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



# <a name="how-to-configure-and-run-startup-tasks-for-a-cloud-service"></a>Comment configurer et exécuter des tâches de démarrage d’un service cloud

Vous pouvez utiliser les tâches de démarrage pour effectuer des opérations avant le début d’un rôle. Vous souhaiterez peut-être effectuer les opérations incluent l’installation d’un composant, l’inscription des composants COM, attribuer des clés de Registre ou de démarrer un processus long terme.

>[AZURE.NOTE] Tâches de démarrage ne sont pas applicables aux Machines virtuelles, et seulement à Cloud Service Web et les rôles de travail.

## <a name="how-startup-tasks-work"></a>Comment fonctionnent les tâches de démarrage

Tâches de démarrage sont les actions exécutées avant vos rôles commencent et où sont définis dans le fichier [ServiceDefinition.csdef] à l’aide de l’élément de [tâche] dans l’élément de [démarrage] . Forum tâches de démarrage sont des fichiers de commandes, mais elles peuvent également être applications console, ou les fichiers de commandes Démarrer des scripts PowerShell.

Variables d’environnement passent des informations dans une tâche de démarrage et stockage local peut être utilisé pour passer des informations d’une tâche de démarrage. Par exemple, une variable d’environnement pouvez spécifier le chemin d’accès pour un programme que vous voulez installer et fichiers puissent être écrits sur le stockage local peut ensuite être lu ultérieurement par vos rôles.

Votre tâche au démarrage peut consigner informations et les erreurs dans le répertoire spécifié par la variable d’environnement **TEMP** . Lors de la tâche au démarrage, la variable d’environnement **TEMP** résout à la *c :\\ressources\\temp\\[guid]. [ rolename]\\RoleTemp* répertoire lors de l’exécution sur le nuage.

Tâches de démarrage peuvent également être exécutés plusieurs fois entre redémarrage. Par exemple, la tâche au démarrage s’exécutera chaque fois que le rôle réutilise et recyclages rôle ne peuvent pas inclure toujours un redémarrage. Tâches de démarrage doivent être écrits de façon à ce qu’ils puissent exécuter plusieurs fois sans problème.

Tâches de démarrage doivent se terminer par un **niveau d’erreur** (ou code de sortie) égale à zéro pour le processus de démarrage pour terminer. Si une tâche de démarrage se termine par un zéro **niveau d’erreur**, le rôle ne démarre pas.


## <a name="role-startup-order"></a>Ordre de démarrage de rôle

Le tableau suivant présente la procédure de démarrage de rôle dans Azure :

1. L’instance est marquée comme étant de **départ** et ne reçoit pas le trafic.

2. Toutes les tâches de démarrage sont exécutées en fonction de leur attribut **taskType** .
    - Les tâches **simples** sont exécutées synchrone, une à la fois.
    - Les tâches **et **d’arrière-plan** ** sont démarrés asynchrone, parallèle à la tâche de démarrage.  
       
    > [AZURE.WARNING] IIS n’est pas entièrement configuré lors de l’étape de tâche de démarrage du processus de démarrage, données spécifiques à un rôle peut ne pas être disponibles. Tâches de démarrage nécessitant des données spécifiques à un rôle doivent utiliser [Microsoft.WindowsAzure.ServiceRuntime.RoleEntryPoint.OnStart](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.onstart.aspx).

3. Le processus d’hôte rôle est démarré et le site est créé dans IIS.

4. La méthode [Microsoft.WindowsAzure.ServiceRuntime.RoleEntryPoint.OnStart](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.onstart.aspx) est appelée.

5. L’instance est marquée comme **prêt** et le trafic est acheminé vers l’instance.

6. La méthode [Microsoft.WindowsAzure.ServiceRuntime.RoleEntryPoint.Run](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx) est appelée.


## <a name="example-of-a-startup-task"></a>Exemple d’une tâche de démarrage

Tâches de démarrage sont définis dans le fichier [ServiceDefinition.csdef] , dans l’élément de **tâche** . L’attribut de **ligne de commande** indique le nom et les paramètres du fichier de commandes démarrage ou commande de la console, l’attribut **executionContext** indique le niveau de la tâche au démarrage privilège et l’attribut **taskType** Spécifie le mode d’exécution de la tâche.

Dans cet exemple, une variable d’environnement **MyVersionNumber**, est créée pour la tâche au démarrage et définissez la valeur «**1.0.0.0**».

**ServiceDefinition.csdef**:

```xml
<Startup>
    <Task commandLine="Startup.cmd" executionContext="limited" taskType="simple" >
        <Environment>
            <Variable name="MyVersionNumber" value="1.0.0.0" />
        </Environment>
    </Task>
</Startup>
```

Dans l’exemple suivant, le fichier de commandes **Startup.cmd** écrit la ligne « la version actuelle est 1.0.0.0 » dans le fichier StartupLog.txt dans le répertoire spécifié par la variable d’environnement TEMP. La `EXIT /B 0` ligne fait en sorte que la tâche démarrage se termine par un **niveau d’erreur** de zéro.

```cmd
ECHO The current version is %MyVersionNumber% >> "%TEMP%\StartupLog.txt" 2>&1
EXIT /B 0
```

> [AZURE.NOTE] Dans Visual Studio, la propriété **Copier dans le répertoire de sortie** pour votre fichier de commandes démarrage doit être définie en **Toujours copier** pour vous assurer que votre fichier de commandes démarrage est correctement déployée à votre projet sur Azure (**approot\\emplacement** pour les rôles Web et **approot** pour les rôles de travail).

## <a name="description-of-task-attributes"></a>Description des attributs de tâche

La section suivante décrit les attributs de l’élément de **tâche** dans le fichier [ServiceDefinition.csdef] :

**ligne de commande** - spécifie la ligne de commande pour la tâche au démarrage :

- La commande avec des paramètres de ligne de commande facultatifs, qui commence à la tâche de démarrage.
- Forum il s’agit du nom de fichier d’un fichier de commandes .cmd ou .bat.
- La tâche est relatif la AppRoot\\dossier Bin pour le déploiement. Variables d’environnement ne sont pas développées pour déterminer le chemin d’accès et le fichier de la tâche. Si expansion environnement est requise, vous pouvez créer un script .cmd petite qui appelle votre tâche au démarrage.
- Peut être une application console ou un fichier de commandes qui démarre un [script PowerShell](cloud-services-startup-tasks-common.md#create-a-powershell-startup-task).

**executionContext** - indique le niveau de privilège de la tâche de démarrage. Le niveau de privilèges peut être limité ou élevé :

- **limités**  
La tâche au démarrage s’exécute avec les mêmes privilèges que le rôle. Lorsque l’attribut **executionContext** pour l’élément [Runtime] est également **limitée**, des privilèges utilisateur sont utilisés.

- **avec élévation de privilèges**  
La tâche au démarrage s’exécute en tant qu’administrateur. Cela permet de tâches de démarrage pour installer des programmes, apporter des modifications de configuration de IIS, effectuez les modifications du Registre et d’autres tâches de niveau administrateur, sans augmenter le niveau de privilèges du rôle lui-même.  

> [AZURE.NOTE] Le niveau de privilège d’une tâche de démarrage n’a pas besoin des mêmes que le rôle lui-même.

**taskType** - spécifie la façon dont une tâche de démarrage est exécutée.

- **simple**  
Les tâches sont exécutées synchrone, une à la fois, dans l’ordre indiqué dans le fichier [ServiceDefinition.csdef] . Lorsqu’une tâche de démarrage **simple** se termine par un **niveau d’erreur** de zéro, la prochaine tâche démarrage **simple** est exécutée. S’il n’y a aucune tâche démarrage **simple** plus à exécuter, puis le rôle lui-même sera démarré.   

    > [AZURE.NOTE] Si la tâche **simple** se termine par un zéro **niveau d’erreur**, l’instance est bloquée. Tâches de démarrage ultérieures **simple** et le rôle lui-même ne démarre pas.

    Pour vous assurer que votre fichier de commandes se termine par un **niveau d’erreur** de zéro, exécutez la commande `EXIT /B 0` à la fin de votre processus de fichiers par lots.

- **arrière-plan**  
Tâches sont exécutées en mode asynchrone, en parallèle avec le démarrage du rôle.

- **premier plan**  
Tâches sont exécutées en mode asynchrone, en parallèle avec le démarrage du rôle. La différence entre un **premier plan** et une tâche **en arrière-plan** clée est qu’une tâche de **premier plan** empêche le rôle de recyclage ou l’arrêt jusqu'à ce que la tâche est terminée. Les tâches **en arrière-plan** n’ont pas cette restriction.

## <a name="environment-variables"></a>Variables d’environnement

Variables d’environnement constituent un moyen pour passer des informations à une tâche de démarrage. Par exemple, vous pouvez placer le chemin d’accès à un blob qui contient un programme d’installation, ou les numéros de port qui utilise votre rôle ou les paramètres aux fonctionnalités de contrôle de votre tâche au démarrage.

Il existe deux types de variables d’environnement pour les tâches de démarrage ; variables d’environnement statique et variables d’environnement en fonction de membres de la classe [RoleEnvironment] . Les deux se trouvent dans la section [environnement] du fichier [ServiceDefinition.csdef] et utilisent tous deux l’attribut [Variable] de l’élément et le **nom** .

Variables d’environnement statique utilise l’attribut **value** de l’élément [Variable] . L’exemple ci-dessus crée la variable d’environnement **MyVersionNumber** qui contient une valeur statique «**1.0.0.0**». Un autre exemple serait de créer une variable d’environnement **StagingOrProduction** que vous pouvez définir manuellement à des valeurs de «**mise en attente**» ou «**production**» pour effectuer des actions de démarrage différent en fonction de la valeur de la variable d’environnement **StagingOrProduction** .

Variables d’environnement en fonction de membres de la classe RoleEnvironment n’utilisent pas l’attribut **value** de l’élément [Variable] . En revanche, l’élément enfant [RoleInstanceValue] , avec la valeur de l’attribut **XPath** appropriée, sont utilisés pour créer une variable d’environnement basée sur un membre spécifique de la classe [RoleEnvironment] . Vous pouvez trouver des valeurs pour l’attribut **XPath** accéder aux différentes valeurs [RoleEnvironment] [ici](cloud-services-role-config-xpath.md).



Par exemple, pour créer une variable d’environnement qui est «**true**» lors de l’instance s’exécute dans l’émulateur de calcul et «**false**» lors de l’exécution dans le cloud, utilisez les éléments [Variable] et [RoleInstanceValue] suivants :

```xml
<Startup>
    <Task commandLine="Startup.cmd" executionContext="limited" taskType="simple">
        <Environment>
    
            <!-- Create the environment variable that informs the startup task whether it is running
                in the Compute Emulator or in the cloud. "%ComputeEmulatorRunning%"=="true" when
                running in the Compute Emulator, "%ComputeEmulatorRunning%"=="false" when running
                in the cloud. -->
    
            <Variable name="ComputeEmulatorRunning">
                <RoleInstanceValue xpath="/RoleEnvironment/Deployment/@emulated" />
            </Variable>
    
        </Environment>
    </Task>
</Startup>
```

## <a name="next-steps"></a>Étapes suivantes
Découvrez comment effectuer certaines [tâches courantes de démarrage](cloud-services-startup-tasks-common.md) avec votre Service Cloud.

[Package](cloud-services-model-and-package.md) votre Service Cloud.  


[ServiceDefinition.csdef]: cloud-services-model-and-package.md#csdef
[Tâche]: https://msdn.microsoft.com/library/azure/gg557552.aspx#Task
[Démarrage]: https://msdn.microsoft.com/library/azure/gg557552.aspx#Startup
[Runtime]: https://msdn.microsoft.com/library/azure/gg557552.aspx#Runtime
[Environnement]: https://msdn.microsoft.com/library/azure/gg557552.aspx#Environment
[Variable]: https://msdn.microsoft.com/library/azure/gg557552.aspx#Variable
[RoleInstanceValue]: https://msdn.microsoft.com/library/azure/gg557552.aspx#RoleInstanceValue
[RoleEnvironment]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleenvironment.aspx
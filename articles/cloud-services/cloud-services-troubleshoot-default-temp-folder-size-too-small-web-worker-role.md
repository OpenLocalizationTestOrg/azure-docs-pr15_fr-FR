<properties
   pageTitle="Taille du dossier TEMP par défaut est trop petite pour un rôle | Microsoft Azure"
   description="Un rôle de service cloud comporte une quantité limitée d’espace pour le dossier TEMP. Cet article fournit des suggestions éviter insuffisant."
   services="cloud-services"
   documentationCenter=""
   authors="simonxjx"
   manager="felixwu"
   editor=""
   tags="top-support-issue"/>
<tags
   ms.service="cloud-services"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="tbd"
   ms.date="10/12/2016"
   ms.author="v-six" />

# <a name="default-temp-folder-size-is-too-small-on-a-cloud-service-webworker-role"></a>Taille du dossier TEMP par défaut est trop petite sur un rôle de collaborateur/web service cloud

Le répertoire temporaire par défaut d’un rôle de collaborateur ou web de service cloud a une taille maximale de 100 Mo, ce qui peut devenir plein à un moment donné. Cet article décrit comment faire pour éviter de manquer d’espace pour le dossier temporaire.

[AZURE.INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="why-do-i-run-out-of-space"></a>Pourquoi manquer d’espace ?

Les variables d’environnement Windows standards TEMP et TMP sont disponibles pour le code est exécuté dans votre application. TEMP et TMP pointent vers un répertoire unique qui a une taille maximale de 100 Mo. Toutes les données qui sont stockées dans ce répertoire ne sont pas conservées du cycle de vie du service cloud ; Si les instances de rôle dans un service cloud sont suppression initiale, le répertoire est supprimé.

## <a name="suggestion-to-fix-the-problem"></a>Suggestions pour résoudre le problème

Implémentez l’une des alternatives suivantes :

- Configurer une ressource de stockage local et accéder directement au lieu d’utiliser TEMP ou TMP. Pour accéder à une ressource de stockage local du code qui s’exécute au sein de votre application, appelez la méthode [RoleEnvironment.GetLocalResource](https://msdn.microsoft.com/library/microsoft.windowsazure.serviceruntime.roleenvironment.getlocalresource.aspx) . 

- Configurer une ressource de stockage local, puis pointez sur les répertoires TEMP et TMP pointe vers le chemin d’accès de la ressource de stockage local. Cette modification doit être effectuée dans la méthode [RoleEntryPoint.OnStart](https://msdn.microsoft.com/library/microsoft.windowsazure.serviceruntime.roleentrypoint.onstart.aspx) .

L’exemple suivant montre comment modifier les répertoires cibles pour TEMP et TMP au sein de la méthode OnStart :


```csharp
using System;
using Microsoft.WindowsAzure.ServiceRuntime;

namespace WorkerRole1
{
    public class WorkerRole : RoleEntryPoint
    {
        public override bool OnStart()
        {
            // The local resource declaration must have been added to the
            // service definition file for the role named WorkerRole1:
            //
            // <LocalResources>
            //    <LocalStorage name="CustomTempLocalStore"
            //                  cleanOnRoleRecycle="false"
            //                  sizeInMB="1024" />
            // </LocalResources>

            string customTempLocalResourcePath =
            RoleEnvironment.GetLocalResource("CustomTempLocalStore").RootPath;
            Environment.SetEnvironmentVariable("TMP", customTempLocalResourcePath);
            Environment.SetEnvironmentVariable("TEMP", customTempLocalResourcePath);

            // The rest of your startup code goes here…

            return base.OnStart();
        }
    }
}
```

## <a name="next-steps"></a>Étapes suivantes

Lisez un blog qui décrit [Comment faire pour augmenter la taille du dossier temporaire Azure Web rôle ASP.NET](http://blogs.msdn.com/b/kwill/archive/2011/07/18/how-to-increase-the-size-of-the-windows-azure-web-role-asp-net-temporary-folder.aspx).

Afficher d’autres [articles de résolution des problèmes](/?tag=top-support-issue&product=cloud-services) pour les services en nuage.

Pour savoir comment résoudre les problèmes de rôle du service cloud à l’aide de données de diagnostic ordinateur PaaS Azure, permet d’afficher [la série de blog de Kevin Williamson](http://blogs.msdn.com/b/kwill/archive/2013/08/09/windows-azure-paas-compute-diagnostics-data.aspx).

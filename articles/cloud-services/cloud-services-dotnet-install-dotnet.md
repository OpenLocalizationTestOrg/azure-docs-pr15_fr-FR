<properties
   pageTitle="Installer .NET sur un rôle de Service Cloud | Microsoft Azure"
   description="Cet article décrit comment installer manuellement .NET framework sur Cloud Service Web et les rôles de travail"
   services="cloud-services"
   documentationCenter=".net"
   authors="thraka"
   manager="timlt"
   editor=""/>

<tags
   ms.service="cloud-services"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/10/2016"
   ms.author="adegeo"/>

# <a name="install-net-on-a-cloud-service-role"></a>Installer .NET sur un rôle de Service Cloud 

Cet article décrit comment installer .NET framework sur Cloud Service Web et les rôles de travail. Vous pouvez utiliser ces étapes pour installer .NET 4.6.1 sur le 4 Azure invité OS famille. Pour les dernières informations sur les versions de système d’exploitation invité voir [Azure mises à jour de système d’exploitation invité et matrice de compatibilité SDK](cloud-services-guestos-update-matrix.md).

Le processus d’installation .NET sur vos rôles web et de travail implique, y compris le package d’installation .NET dans le cadre de votre projet Cloud et lancer le programme d’installation dans le cadre du rôle tâches de démarrage.  

## <a name="add-the-net-installer-to-your-project"></a>Ajouter le programme d’installation .NET à votre projet
- Télécharger le web installer pour .NET framework que vous voulez installer
    - [.NET 4.6.1 web programme d’installation](http://go.microsoft.com/fwlink/?LinkId=671729)
- Pour un rôle Web
  1. Dans **L’Explorateur de solutions**, sous de **rôles** dans le projet de service cloud avec le bouton droit cliquez sur le rôle et sélectionnez **Ajouter > Nouveau dossier**. Créer un dossier nommé *bin*
  2. Cliquez avec le bouton droit sur le dossier **bin** et sélectionnez **Ajouter > élément existant**. Sélectionnez le programme d’installation .NET et l’ajouter à la Corbeille.
- Pour un rôle de collaborateur
  1. Cliquez avec le bouton droit sur le rôle et sélectionnez **Ajouter > élément existant**. Sélectionnez le programme d’installation .NET et ajoutez-le au rôle. 

Les fichiers ajoutés le dossier contenu du rôle de cette façon est automatiquement ajoutée au package service cloud et déployée sur un emplacement cohérent sur l’ordinateur virtuel. Répétez cette procédure pour tous les rôles web et de travail dans votre Service Cloud pour tous les rôles d’avoir une copie du programme d’installation.

> [AZURE.NOTE] Vous devez installer .NET 4.6.1 sur votre rôle Service Cloud même si votre application cible .NET 4.6. Le système d’exploitation Azure invité comprend des mises à jour [3098779](https://support.microsoft.com/kb/3098779) et [3097997](https://support.microsoft.com/kb/3097997). L’installation .NET 4.6 sur ces mises à jour peut entraîner des problèmes lors de l’exécution de vos applications .NET, afin que vous devez installer directement .NET 4.6.1 au lieu de .NET 4.6. Pour plus d’informations, voir [3118750 Ko](https://support.microsoft.com/kb/3118750).

![Contenu du rôle avec des fichiers d’installation][1]

## <a name="define-startup-tasks-for-your-roles"></a>Définir des tâches de démarrage pour vos rôles
Tâches de démarrage permettent d’effectuer des opérations avant le début d’un rôle. Installation du .NET Framework dans le cadre de la tâche de démarrage Vérifiez que le framework est installé avant l’exécution d’un code de votre application. Pour plus d’informations sur le démarrage des tâches, consultez : [Exécuter des tâches de démarrage dans Azure](cloud-services-startup-tasks.md). 

1. Ajoutez le code suivant dans le fichier *ServiceDefinition.csdef* sous le nœud **WebRole** ou **WorkerRole** pour tous les rôles :
    
    ```xml
    <LocalResources>
      <LocalStorage name="NETFXInstall" sizeInMB="1024" cleanOnRoleRecycle="false" />
    </LocalResources>    
    <Startup>
      <Task commandLine="install.cmd" executionContext="elevated" taskType="simple">
        <Environment>
          <Variable name="PathToNETFXInstall">
            <RoleInstanceValue xpath="/RoleEnvironment/CurrentInstance/LocalResources/LocalResource[@name='NETFXInstall']/@path" />
          </Variable>
          <Variable name="ComputeEmulatorRunning">
            <RoleInstanceValue xpath="/RoleEnvironment/Deployment/@emulated" />
          </Variable>
        </Environment>
      </Task>
    </Startup>
    ```

    La configuration ci-dessus s’exécute commande de console *install.cmd* avec des privilèges d’administrateur afin qu’il peut installer le .NET framework. La configuration crée également un LocalStorage avec le nom *NETFXInstall*. Le script de démarrage définira le dossier temp pour utiliser cette ressource de stockage local afin que le programme d’installation .NET framework sera téléchargé et installé à partir de cette ressource. Il est important de définir la taille de cette ressource au moins 1024 Mo pour garantir que l’infrastructure ne s’installe correctement. Pour plus d’informations sur le démarrage de tâches, consultez : [tâches de démarrage de Service Cloud courantes](cloud-services-startup-tasks-common.md) 

2. Créer un fichier **install.cmd** et ajoutez-la à tous les rôles par clic droit sur le rôle, en sélectionnant **Ajouter > élément existant...**. Ainsi, tous les rôles disposez à présent le fichier d’installation .NET ainsi que le fichier install.cmd.
    
    ![Contenu du rôle avec tous les fichiers][2]

    > [AZURE.NOTE] Utiliser un éditeur de texte simple tel que le bloc-notes pour créer ce fichier. Si vous utilisez Visual Studio pour créer un fichier texte et renommez-le à « .cmd » le fichier peut contenir toujours une marque d’ordre octet UTF-8 et la première ligne du script en cours d’exécution provoquera une erreur. Si vous deviez utiliser Visual Studio pour créer le fichier quitter Ajouter un REM (Remarque) à la première ligne du fichier afin qu’elle est ignorée lorsque vous exécutez. 

3. Ajoutez le script suivant dans le fichier **install.cmd** :

    ```
    REM Set the value of netfx to install appropriate .NET Framework. 
    REM ***** To install .NET 4.5.2 set the variable netfx to "NDP452" *****
    REM ***** To install .NET 4.6 set the variable netfx to "NDP46" *****
    REM ***** To install .NET 4.6.1 set the variable netfx to "NDP461" *****
    REM ***** To install .NET 4.6.2 set the variable netfx to "NDP462" *****
    set netfx="NDP461"
    
    REM ***** Set script start timestamp *****
    set timehour=%time:~0,2%
    set timestamp=%date:~-4,4%%date:~-10,2%%date:~-7,2%-%timehour: =0%%time:~3,2%
    set "log=install.cmd started %timestamp%."
    
    REM ***** Exit script if running in Emulator *****
    if %ComputeEmulatorRunning%=="true" goto exit
    
    REM ***** Needed to correctly install .NET 4.6.1, otherwise you may see an out of disk space error *****
    set TMP=%PathToNETFXInstall%
    set TEMP=%PathToNETFXInstall%
    
    REM ***** Setup .NET filenames and registry keys *****
    if %netfx%=="NDP462" goto NDP462
    if %netfx%=="NDP461" goto NDP461
    if %netfx%=="NDP46" goto NDP46
        set "netfxinstallfile=NDP452-KB2901954-Web.exe"
        set netfxregkey="0x5cbf5"
        goto logtimestamp
    
    :NDP46
    set "netfxinstallfile=NDP46-KB3045560-Web.exe"
    set netfxregkey="0x6004f"
    goto logtimestamp
    
    :NDP461
    set "netfxinstallfile=NDP461-KB3102438-Web.exe"
    set netfxregkey="0x6040e"
    goto logtimestamp
    
    :NDP462
    set "netfxinstallfile=NDP462-KB3151802-Web.exe"
    set netfxregkey="0x60632"
    
    :logtimestamp
    REM ***** Setup LogFile with timestamp *****
    md "%PathToNETFXInstall%\log"
    set startuptasklog="%PathToNETFXInstall%log\startuptasklog-%timestamp%.txt"
    set netfxinstallerlog="%PathToNETFXInstall%log\NetFXInstallerLog-%timestamp%"
    echo %log% >> %startuptasklog%
    echo Logfile generated at: %startuptasklog% >> %startuptasklog%
    echo TMP set to: %TMP% >> %startuptasklog%
    echo TEMP set to: %TEMP% >> %startuptasklog%
    
    REM ***** Check if .NET is installed *****
    echo Checking if .NET (%netfx%) is installed >> %startuptasklog%
    set /A netfxregkeydecimal=%netfxregkey%
    set foundkey=0
    FOR /F "usebackq skip=2 tokens=1,2*" %%A in (`reg query "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\NET Framework Setup\NDP\v4\Full" /v Release 2^>nul`) do @set /A foundkey=%%C
    echo Minimum required key: %netfxregkeydecimal% -- found key: %foundkey% >> %startuptasklog%
    if %foundkey% GEQ %netfxregkeydecimal% goto installed
    
    REM ***** Installing .NET *****
    echo Installing .NET with commandline: start /wait %~dp0%netfxinstallfile% /q /serialdownload /log %netfxinstallerlog%  /chainingpackage "CloudService Startup Task" >> %startuptasklog%
    start /wait %~dp0%netfxinstallfile% /q /serialdownload /log %netfxinstallerlog% /chainingpackage "CloudService Startup Task" >> %startuptasklog% 2>>&1
    if %ERRORLEVEL%== 0 goto installed
        echo .NET installer exited with code %ERRORLEVEL% >> %startuptasklog%   
        if %ERRORLEVEL%== 3010 goto restart
        if %ERRORLEVEL%== 1641 goto restart
        echo .NET (%netfx%) install failed with Error Code %ERRORLEVEL%. Further logs can be found in %netfxinstallerlog% >> %startuptasklog%
    
    :restart
    echo Restarting to complete .NET (%netfx%) installation >> %startuptasklog%
    EXIT /B %ERRORLEVEL%
    
    :installed
    echo .NET (%netfx%) is installed >> %startuptasklog%
    
    :end
    echo install.cmd completed: %date:~-4,4%%date:~-10,2%%date:~-7,2%-%timehour: =0%%time:~3,2% >> %startuptasklog%
    
    :exit
    EXIT /B 0
    ```
        
    Le script d’installation vérifie si la version .NET framework spécifiée est déjà installée sur l’ordinateur par interroger le Registre. Si la version .NET n’est pas installée, puis .net Web Installer est lancé. Pour aider à résoudre les éventuels problèmes le script consigne toutes les activités dans un fichier nommé *startuptasklog-(currentdatetime) .txt* stockées dans le stockage local *InstallLogs* .

    > [AZURE.NOTE] Le script contient toujours comment installer .NET 4.5.2 ou 4.6 .NET d’assurer la continuité. Il est inutile d’installer manuellement .NET 4.5.2 qu’elle est déjà disponible sur le système d’exploitation de Azure invité. Au lieu de l’installation .NET 4.6 vous devez installer directement .NET 4.6.1 en raison de [3118750 Ko](https://support.microsoft.com/kb/3118750).
      

## <a name="configure-diagnostics-to-transfer-the-startup-task-logs-to-blob-storage"></a>Configurer des diagnostics pour transférer les journaux des tâches démarrage stockage BLOB 
Pour simplifier le dépannage des problèmes d’installation, vous pouvez configurer Azure Diagnostics pour transférer des fichiers journaux générés par le script de démarrage ou le programme d’installation .NET au stockage d’objets blob. Avec cette approche, vous pouvez afficher les journaux en simplement télécharger les fichiers journaux de stockage d’objets blob au lieu de devoir Bureau à distance dans le rôle.

Pour configurer des diagnostics ouvrir le *diagnostics.wadcfgx* et ajoutez le code suivant sous le nœud **répertoires** : 

```xml 
<DataSources>
 <DirectoryConfiguration containerName="netfx-install">
  <LocalResource name="NETFXInstall" relativePath="log"/>
 </DirectoryConfiguration>
</DataSources>
```

Cela configurera azure diagnostics pour transférer tous les fichiers dans le répertoire *log* sous la ressource *NETFXInstall* sur le compte de stockage diagnostics dans le conteneur blob *netfx installer* .

## <a name="deploying-your-service"></a>Déploiement de votre service 
Lorsque vous déployez votre service les tâches de démarrage exécute et installe .NET framework s’il n’est pas déjà installé. Vos rôles seront dans l’état occupé (e) tandis que l’infrastructure n’installe et peut même redémarrer si l’installation de framework l’exige. 

## <a name="additional-resources"></a>Ressources supplémentaires

- [Installation du .NET Framework][]
- [Comment : déterminer les Versions du .NET Framework sont installées][]
- [Résolution des problèmes de .NET Framework Installations][]

[Comment : déterminer les Versions du .NET Framework sont installées]: https://msdn.microsoft.com/library/hh925568.aspx
[Installation du .NET Framework]: https://msdn.microsoft.com/library/5a4x27ek.aspx
[Résolution des problèmes de .NET Framework Installations]: https://msdn.microsoft.com/library/hh925569.aspx

<!--Image references-->
[1]: ./media/cloud-services-dotnet-install-dotnet/rolecontentwithinstallerfiles.png
[2]: ./media/cloud-services-dotnet-install-dotnet/rolecontentwithallfiles.png

 

<properties
pageTitle="Activer la connexion Bureau à distance pour un rôle dans les Services de Cloud Azure à l’aide de PowerShell"
description="Comment configurer votre application de service cloud azure à l’aide de PowerShell pour autoriser les connexions Bureau à distance"
services="cloud-services"
documentationCenter=""
authors="thraka"
manager="timlt"
editor=""/>
<tags
ms.service="cloud-services"
ms.workload="tbd"
ms.tgt_pltfrm="na"
ms.devlang="na"
ms.topic="article"
ms.date="08/05/2016"
ms.author="adegeo"/>

# <a name="enable-remote-desktop-connection-for-a-role-in-azure-cloud-services-using-powershell"></a>Activer la connexion Bureau à distance pour un rôle dans les Services de Cloud Azure à l’aide de PowerShell

>[AZURE.SELECTOR]
- [Portail classique Azure](cloud-services-role-enable-remote-desktop.md)
- [PowerShell](cloud-services-role-enable-remote-desktop-powershell.md)
- [Visual Studio](../vs-azure-tools-remote-desktop-roles.md)


Bureau à distance vous permet d’accéder au bureau d’un rôle en cours d’exécution dans Azure. Vous pouvez utiliser une connexion Bureau à distance pour résoudre les problèmes et diagnostiquer les problèmes avec votre application en cours d’exécution.

Cet article décrit comment activer le Bureau à distance sur votre rôles de Service Cloud à l’aide de PowerShell. Découvrez [comment installer et configurer Azure PowerShell](../powershell-install-configure.md) pour la configuration requise pour cet article. PowerShell utilise l’Extension de bureau à distance afin de l’activer Bureau à distance après le déploiement de l’application.


## <a name="configure-remote-desktop-from-powershell"></a>Configurer le Bureau à distance à partir de PowerShell

L’applet de commande [Set-AzureServiceRemoteDesktopExtension](https://msdn.microsoft.com/library/azure/dn495117.aspx) vous permet d’activer tous les rôles de déploiement de votre service cloud ou de bureau à distance sur les rôles spécifiés. L’applet de commande permet de spécifier le nom d’utilisateur et mot de passe pour l’utilisateur de bureau à distance via le paramètre *d’informations d’identification* qui accepte un objet PSCredential.

Si vous utilisez PowerShell de façon interactive, vous pouvez facilement définir l’objet PSCredential en appelant l’applet de commande [Get-informations d’identification](https://technet.microsoft.com/library/hh849815.aspx) .

```
$remoteusercredentials = Get-Credential
```

Cette commande affiche une boîte de dialogue vous permettant d’entrer le nom d’utilisateur et mot de passe de l’utilisateur à distance de façon sécurisée.

Dans la mesure où PowerShell permet de scénarios d’automatisation, vous pouvez également configurer l’objet **PSCredential** d’une manière qui ne nécessite pas interaction de l’utilisateur. Tout d’abord, vous devez définir un mot de passe sécurisé. Vous devez commencer avec spécifiant un mot de passe au format texte brut convertir en une chaîne sécurisée à l’aide de [ConvertTo-SecureString](https://technet.microsoft.com/library/hh849818.aspx). Vous devez ensuite convertir cette chaîne sécurisée en chaîne standard chiffrée à l’aide de [ConvertFrom-SecureString](https://technet.microsoft.com/library/hh849814.aspx). Désormais, vous pouvez enregistrer cette chaîne standard chiffrée vers un fichier à l’aide [Du contenu de l’ensemble](https://technet.microsoft.com/library/ee176959.aspx).

Vous pouvez également créer un fichier de mot de passe sécurisé afin que vous n’êtes pas obligé de taper le mot de passe à chaque fois. En outre, un fichier de mot de passe sécurisé est supérieur à celle d’un fichier au format texte brut. Utiliser la session PowerShell suivante pour créer un fichier de mot de passe sécurisé :

```
ConvertTo-SecureString -String "Password123" -AsPlainText -Force | ConvertFrom-SecureString | Set-Content "password.txt"
```

>[AZURE.IMPORTANT] Lorsque vous définissez le mot de passe, vérifiez que vous remplissez les [exigences de complexité](https://technet.microsoft.com/library/cc786468.aspx).

Pour créer l’objet d’informations d’identification à partir du fichier de mot de passe sécurisé, vous devez lire le contenu du fichier et les reconvertir en une chaîne sécurisée à l’aide de [ConvertTo-SecureString](https://technet.microsoft.com/library/hh849818.aspx).

L’applet de commande [Set-AzureServiceRemoteDesktopExtension](https://msdn.microsoft.com/library/azure/dn495117.aspx) accepte également un paramètre *d’Expiration* , qui indique une **date/heure** à laquelle le compte d’utilisateur arrive à expiration. Par exemple, vous pouvez définir le compte à expiration quelques jours à partir de la date et l’heure.

Cet exemple montre comment PowerShell vous montre comment définir l’Extension de bureau à distance sur un service cloud :

```
$servicename = "cloudservice"
$username = "RemoteDesktopUser"
$securepassword = Get-Content -Path "password.txt" | ConvertTo-SecureString
$expiry = $(Get-Date).AddDays(1)
$credential = New-Object System.Management.Automation.PSCredential $username,$securepassword
Set-AzureServiceRemoteDesktopExtension -ServiceName $servicename -Credential $credential -Expiration $expiry
```
Vous pouvez également spécifier l’emplacement de déploiement et les rôles que vous souhaitez activer le Bureau à distance sur. Si ces paramètres ne sont pas indiqués, l’applet de commande permet de bureau à distance sur tous les rôles dans l’emplacement de déploiement de **Production** .

L’extension de bureau à distance est associée à un déploiement. Si vous créez un nouveau déploiement pour le service, vous devez activer Bureau à distance sur ce déploiement. Si vous voulez toujours utiliser Bureau à distance activée, vous devez envisager intégrant les scripts PowerShell dans votre flux de travail de déploiement.


## <a name="remote-desktop-into-a-role-instance"></a>Bureau à distance dans une instance de rôle
L’applet de commande [Get-AzureRemoteDesktopFile](https://msdn.microsoft.com/library/azure/dn495261.aspx) est utilisé pour le Bureau à distance dans une instance de rôle spécifique de votre service cloud. Vous pouvez utiliser le paramètre *LocalPath* à télécharger le fichier RDP localement. Ou vous pouvez utiliser le paramètre *lancer* directement lancer la boîte de dialogue Connexion Bureau à distance pour accéder à l’instance de rôle du service cloud.

```
Get-AzureRemoteDesktopFile -ServiceName $servicename -Name "WorkerRole1_IN_0" -Launch
```


## <a name="check-if-remote-desktop-extension-is-enabled-on-a-service"></a>Vérifier si l’extension de bureau à distance est activée sur un service
L’applet de commande [Get-AzureServiceRemoteDesktopExtension](https://msdn.microsoft.com/library/azure/dn495261.aspx) affiche que Bureau à distance est activé ou désactivé sur un déploiement de service. L’applet de commande renvoie le nom d’utilisateur pour l’utilisateur de bureau à distance et les rôles que l’extension de bureau à distance est activée pour. Par défaut, cela se produit sur l’emplacement de déploiement et vous pouvez choisir d’utiliser l’emplacement intermédiaire à la place.

```
Get-AzureServiceRemoteDesktopExtension -ServiceName $servicename
```

## <a name="remove-remote-desktop-extension-from-a-service"></a>Supprimer l’extension de bureau à distance à partir d’un service
Si vous avez déjà activé l’extension du Bureau à distance sur un déploiement et que vous devez mettre à jour les paramètres du Bureau à distance, supprimez tout d’abord l’extension. Et activer à nouveau avec les nouveaux paramètres. Par exemple, si vous souhaitez définir un nouveau mot de passe pour le compte d’utilisateur à distance ou le compte expiré. Cela est nécessaire dans les déploiements existants qui ont l’extension de bureau à distance activée. Pour les nouveaux déploiements, vous pouvez simplement appliquer l’extension directement.

Pour supprimer l’extension de bureau à distance à partir du déploiement, vous pouvez utiliser l’applet de commande [Supprimer AzureServiceRemoteDesktopExtension](https://msdn.microsoft.com/library/azure/dn495280.aspx) . Vous pouvez également spécifier l’emplacement de déploiement et le rôle à partir de laquelle vous voulez supprimer l’extension du Bureau à distance.

```
Remove-AzureServiceRemoteDesktopExtension -ServiceName $servicename -UninstallConfiguration
```

>[AZURE.NOTE] Pour supprimer complètement la configuration de l’extension, vous devez appeler l’applet de commande *Supprimer* avec le paramètre **UninstallConfiguration** .
>
>Le paramètre **UninstallConfiguration** est désinstallé toute configuration extension qui est appliquée au service. Chaque configuration de l’extension est associée à la configuration du service. Appel de l’applet de commande *Supprimer* sans **UninstallConfiguration** annule association entre le <mark>déploiement</mark> de la configuration d’extension, supprimant ainsi efficacement l’extension. Toutefois, la configuration de l’extension reste associée au service.



## <a name="additional-resources"></a>Ressources supplémentaires

[Comment faire pour configurer les Services en nuage](cloud-services-how-to-configure.md)

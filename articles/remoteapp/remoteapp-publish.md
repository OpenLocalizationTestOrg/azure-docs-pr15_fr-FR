<properties
    pageTitle="Publier une application dans Azure RemoteApp | Microsoft Azure"
    description="Découvrez comment publier des applications et des ressources dans Azure RemoteApp."
    services="remoteapp"
    documentationCenter=""
    authors="lizap"
    manager="mbaldwin" />

<tags
    ms.service="remoteapp"
    ms.workload="tbd"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/15/2016"
    ms.author="elizapo" />


# <a name="how-to-publish-an-app-in-remoteapp"></a>Comment publier une application dans RemoteApp

> [AZURE.IMPORTANT]
> RemoteApp Azure a été interrompu. Lire l' [annonce](https://go.microsoft.com/fwlink/?linkid=821148) pour plus d’informations.

Après avoir créé votre collection de sites RemoteApp, vous devez publier les applications ou les ressources que vous souhaitez rendre disponibles pour vos utilisateurs. Les images de modèle fournis avec votre abonnement n’ont que quelques applications publiées par défaut, pour partager des autres applications, vous devez les publier.

> [AZURE.NOTE] Vous devez mettre à jour une application ? Vous devez mettre à [jour l’image](remoteapp-update.md) tout d’abord.

Sous l’onglet **publication** dans le portail, cliquez sur **Publier**. Vous pouvez ajouter une application à partir du menu **Démarrer** de l’image de votre modèle ou fournir le chemin d’accès à l’endroit où l’application est installée sur l’image du modèle. Si vous choisissez d’ajouter dans le menu **Démarrer** , choisissez l’application à publier à partir de la liste. Si vous décidez d’indiquer le chemin d’accès à l’application, entrez un nom pour l’application et le chemin d’accès à l’application. Utiliser des variables dans le chemin d’accès - par exemple, « %SystemDrive% » au lieu de « c :\".

> [AZURE.NOTE] Si vous voulez ajouter votre application dans le menu **Démarrer** , vous devez avoir *ajouté cette application à la * *Démarrer* * menu sur votre image du modèle.* Dans le cas contraire, RemoteApp uniquement verrez quels *est* dans le menu **Démarrer** , et vous sera perdu. 

>Pour vous assurer que votre application se trouve dans le menu **Démarrer** , placez un fichier de raccourci - **.lnk** - dans le dossier %systemdrive%\ProgramData\Microsoft\Windows\Start Menu\Programs.

> Si vous avez oublié d’ajouter l’application dans le menu **Démarrer** lorsque vous avez créé le modèle, choisissez d’ajouter le chemin d’accès à l’application. (Ou recréer votre image du modèle, mais qui est un peu plus de travail).


 
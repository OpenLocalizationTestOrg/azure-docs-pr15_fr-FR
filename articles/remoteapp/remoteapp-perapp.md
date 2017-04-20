<properties
   pageTitle="Publier des applications à des utilisateurs individuels dans une collection de sites Azure RemoteApp (Preview) | Microsoft Azure"
   description="Découvrez comment vous pouvez publier des applications à des utilisateurs individuels, au lieu d’en fonction des groupes dans Azure RemoteApp."
   services="remoteapp-preview"
   documentationCenter=""
   authors="piotrci"
   manager="mbaldwin"
   editor=""/>

<tags
   ms.service="remoteapp"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="compute"
   ms.date="08/15/2016"
   ms.author="piotrci"/>

# <a name="publish-applications-to-individual-users-in-an-azure-remoteapp-collection-preview"></a>Publier des applications à des utilisateurs individuels dans une collection de sites Azure RemoteApp (Preview)

> [AZURE.IMPORTANT]
> RemoteApp Azure a été interrompu. Lire l' [annonce](https://go.microsoft.com/fwlink/?linkid=821148) pour plus d’informations.

Cet article explique comment publier des applications à des utilisateurs individuels dans une collection de sites RemoteApp Azure. Il s’agit de nouvelles fonctionnalités dans Azure RemoteApp, actuellement dans « preview privé » et disponible uniquement pour sélectionner les premiers à des fins d’évaluation.

Azure RemoteApp n'activés qu’un seul moyen d’applications « publication » : l’administrateur serait publier des applications à partir de l’image et qu’ils sont visibles par tous les utilisateurs de la collection de sites.

Un scénario courant consiste à inclure un grand nombre d’applications dans une seule image et déployer une collection de sites afin de réduire les coûts de gestion. Toutes les applications souvent ne soient appliquent à tous les utilisateurs : les administrateurs préférez publier des applications à des utilisateurs individuels afin de ne verra pas les applications inutiles dans son application de flux.

Ceci est désormais possible dans Azure RemoteApp – actuellement comme une fonction d’aperçu limité. Voici un bref résumé des nouvelles fonctionnalités :

1. Une collection de sites peut être défini à l’un des deux modes :
 
  - la cellule d’origine « mode de collection de sites », où tous les utilisateurs dans une collection de sites peuvent voir toutes les applications publiées. Il s’agit du mode par défaut.
  - le nouveau « application mode », où les utilisateurs ne voient que des applications qui ont été explicitement leur attribué

2. Pour le moment le mode d’application ne peut être activé à l’aide des applets de commande PowerShell de RemoteApp Azure.

  - Lorsque la valeur en mode d’applications, attribution utilisateur dans la collection de sites ne peuvent être gérée à partir du portail Azure. Attribution de l’utilisateur doit être géré tout au long applets de commande PowerShell.

3. Les utilisateurs voient uniquement les applications publiées directement. Cependant, il peut encore possible pour un utilisateur lancer d’autres applications disponibles sur l’image en y accédant directement dans le système d’exploitation.
  - Cette fonctionnalité ne fournit pas un verrouillage sécurisé d’applications ; Il limite uniquement visibilité dans l’application de flux.
  - Si vous avez besoin isoler les utilisateurs à partir d’applications, vous devrez utiliser collections distinctes pour que.

## <a name="how-to-get-azure-remoteapp-powershell-cmdlets"></a>Comment obtenir les applets de commande PowerShell de RemoteApp Azure

Pour essayer la nouvelle fonctionnalité d’aperçu, vous devrez utiliser les applets de commande PowerShell Azure. Il n’est actuellement pas possible d’utiliser le portail de gestion Azure pour activer le nouveau mode publication d’application.

Tout d’abord, vérifiez que vous avez installé le [module Azure PowerShell](../powershell-install-configure.md) .

Lancer la console PowerShell en mode administrateur, puis exécutez l’applet de commande suivante :

        Add-AzureAccount

Il vous invite à entrer votre nom d’utilisateur Azure et le mot de passe. Une fois connecté, il se peut que vous ne pourrez pas exécuter les applets de commande Azure RemoteApp à vos abonnements Azure.

## <a name="how-to-check-which-mode-a-collection-is-in"></a>Comment vérifier le mode une collection est dans

Exécutez l’applet de commande suivante :

        Get-AzureRemoteAppCollection <collectionName>

![Vérifier le mode de collection de sites](./media/remoteapp-perapp/araacllelvel.png)

La propriété AclLevel peut avoir les valeurs suivantes :

- Collection de sites : du mode publication d’origine. Tous les utilisateurs voient que tous publiés applications.
- Application : le nouveau mode de publication. Les utilisateurs voient uniquement les applications publiées directement.

## <a name="how-to-switch-to-application-publishing-mode"></a>Comment passer en mode publication d’application

Exécutez l’applet de commande suivante :

        Set-AzureRemoteAppCollection -CollectionName -AclLevel Application

État de publication application est préservé : initialement tous les utilisateurs verront toutes les applications publiées d’origine.

## <a name="how-to-list-users-who-can-see-a-specific-application"></a>Comment obtenir la liste des utilisateurs qui peuvent voir une application spécifique

Exécutez l’applet de commande suivante :

        Get-AzureRemoteAppUser -CollectionName <collectionName> -Alias <appAlias>

Répertorie tous les utilisateurs qui peuvent voir l’application.

Remarque : Vous pouvez voir les alias application (appelés « application alias » dans la syntaxe ci-dessus) en exécutant Get-AzureRemoteAppProgram - CollectionName <collectionName>.

## <a name="how-to-assign-an-application-to-a-user"></a>Comment attribuer une application à un utilisateur

Exécutez l’applet de commande suivante :

        Add-AzureRemoteAppUser -CollectionName <collectionName> -UserUpn <user@domain.com> -Type <OrgId|MicrosoftAccount> -Alias <appAlias>

L’utilisateur s’affiche l’application dans le client Azure RemoteApp et est en mesure de vous y connecter.

## <a name="how-to-remove-an-application-from-a-user"></a>Comment supprimer une application d’un utilisateur

Exécutez l’applet de commande suivante :

        Remove-AzureRemoteAppUser -CollectionName <collectionName> -UserUpn <user@domain.com> -Type <OrgId|MicrosoftAccount> -Alias <appAlias>

## <a name="providing-feedback"></a>Fournir des commentaires
Nous vous remercions de vos commentaires et suggestions concernant cette fonctionnalité Aperçu. Remplissez l' [enquête](http://www.instant.ly/s/FDdrb) pour nous faire connaître votre avis.

## <a name="havent-had-a-chance-to-try-the-preview-feature"></a>N’ont pas eu le temps pour essayer de la fonctionnalité Aperçu ?
Si vous n’avez pas encore pratiqués dans l’aperçu, utilisez cette [enquête](http://www.instant.ly/s/AY83p) pour demander l’accès.

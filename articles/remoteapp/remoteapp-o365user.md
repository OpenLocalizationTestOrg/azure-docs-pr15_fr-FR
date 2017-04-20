
<properties 
    pageTitle="Comment utiliser RemoteApp Azure avec les comptes d’utilisateur Office 365 | Microsoft Azure"
    description="Découvrez comment utiliser RemoteApp Azure avec mes comptes d’utilisateur Office 365"
    services="remoteapp"
    documentationCenter="" 
    authors="piotrci" 
    manager="mbaldwin" />

<tags 
    ms.service="remoteapp" 
    ms.workload="compute" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="08/15/2016" 
    ms.author="elizapo" />



# <a name="how-to-use-azure-remoteapp-with-office-365-user-accounts"></a>Comment utiliser RemoteApp Azure avec les comptes d’utilisateur Office 365

> [AZURE.IMPORTANT]
> RemoteApp Azure a été interrompu. Lire l' [annonce](https://go.microsoft.com/fwlink/?linkid=821148) pour plus d’informations.

Si vous avez un abonnement Office 365 vous avez Azure Active Directory qui stocke vos noms d’utilisateur et les mots de passe permettant d’accéder aux services Office 365. Par exemple, lorsque vos utilisateurs activer Office 365 ProPlus qu’ils s’authentifier Azure AD pour vérifier les licences. La plupart des clients souhaitent utiliser le même répertoire avec Azure RemoteApp.

Si vous déployez Azure RemoteApp vous utilisez probablement un abonnement Azure associé à une publicité Azure différente. Pour pouvoir utiliser votre annuaire Office 365, vous devrez passer de l’abonnement Azure dans ce répertoire.

Pour plus d’informations sur le déploiement des applications clientes Office 365, voir [comment utiliser votre abonnement Office 365 avec Azure RemoteApp](remoteapp-officesubscription.md).
 
## <a name="phase-1-register-your-free-office-365-azure-active-directory-subscription"></a>Phase 1 : Enregistrer votre abonnement Office 365 Azure Active Directory gratuit
Si vous utilisez le portail classique Azure, suivez les étapes [inscrire votre abonnement Azure Active Directory gratuit](https://technet.microsoft.com/library/dn832618.aspx) pour obtenir l’accès administratif à votre annonce Azure via le portail de gestion Azure. En tant que le résultat de ce processus, vous devriez pouvoir se connecter au portail Azure et de voir votre annuaire il – à ce stade vous ne verrez pas beaucoup plus étant donné que l’abonnement Azure complète que vous utilisez avec Azure RemoteApp est dans un répertoire différent.

N’oubliez pas le nom et le mot de passe du compte d’administrateur que vous avez créé dans cette étape – ils seront nécessaires à la Phase 2.

Si vous utilisez le portail Azure, Découvrez [comment enregistrer et activer un gratuit Azure Active Directory à l’aide du portail Office 365](http://azureblogger.com/2016/01/how-to-register-and-activate-a-free-azure-active-directory-using-office-365-portal/).

## <a name="phase-2-change-the-azure-ad-associated-with-your-azure-subscription"></a>Phase 2 : Modifier l’annonce Azure associé à votre abonnement Azure.
Nous allons modifier votre abonnement Azure à partir de son répertoire actuel dans le répertoire Office 365 que nous travaillées avec à la Phase 1.

Suivez les instructions décrites dans [Modifier le client Azure Active Directory dans Azure RemoteApp](remoteapp-changetenant.md). Une attention particulière aux étapes suivantes :

- Étape 1 # : Si vous avez déployé Azure RemoteApp (ARA) dans cet abonnement, vérifiez que vous supprimez tous les comptes d’utilisateurs Azure AD à partir de toutes les collections de ARA tout d’abord, avant d’essayer une autre valeur. Par ailleurs, vous pouvez envisager la suppression de toutes les collections existantes.
- Étape #2 : Il s’agit une étape critique. Vous devez utiliser un compte Microsoft (par exemple, @outlook.com) tant qu’administrateur de Service de l’abonnement ; c’est parce que nous ne pouvons pas ont des comptes d’utilisateurs à partir d’Azure Active Directory existant lié à l’abonnement – dans ce cas, nous ne pourrez pas déplacer vers une autre annonce Azure.
- Étape 4 # : Lorsque vous ajoutez un répertoire existant, le système vous demande à se connecter avec le compte d’administrateur pour ce répertoire. Vérifiez que vous utilisez le compte d’administrateur de la Phase 1.
- Étape 5 # : Changez le répertoire parent de l’abonnement à Office 365 dans l’annuaire. Le résultat final doit être que sous Paramètres -> abonnements votre abonnement répertorie l’annuaire Office 365. 
![Remplacer le dossier parent de l’abonnement](./media/remoteapp-o365user/settings.png)
 

À ce stade votre abonnement Azure RemoteApp est associé à votre installation d’Office 365 Azure AD ; Vous pouvez utiliser les comptes d’utilisateur Office 365 existants avec Azure RemoteApp !






<properties 
    pageTitle="Azure AD + Active Directory requise pour Azure RemoteApp | Microsoft Azure" 
    description="Apprenez à configurer Active Directory pour l’utiliser avec Azure RemoteApp." 
    services="remoteapp" 
    documentationCenter="" 
    authors="lizap" 
    manager="mbaldwin" />

<tags 
    ms.service="remoteapp" 
    ms.workload="compute" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="08/15/2016" 
    ms.author="elizapo" />



# <a name="azure-ad--active-directory-requirements-for-azure-remoteapp"></a>Azure AD + Active Directory requise pour Azure RemoteApp

> [AZURE.IMPORTANT]
> RemoteApp Azure a été interrompu. Lire l' [annonce](https://go.microsoft.com/fwlink/?linkid=821148) pour plus d’informations.


Pour la collection de hybride RemoteApp Azure ou pour une collection de cloud que vous souhaitez établir une fédération AD Connect, vous devez effectuer les opérations suivantes.

### <a name="connect-azure-ad-and-active-directory"></a>Se connecter Azure AD et Active Directory

Si vous voulez vous connecter votre client Azure AD et vos environnements Active Directory local, utilisez AD se connecter. Il faudra vous seul [4 clics](https://blogs.technet.microsoft.com/enterprisemobility/2014/08/04/connecting-ad-and-azure-ad-only-4-clicks-with-azure-ad-connect/) pour vous connecter deux annuaires.

Remarque - la synchronisation d’annuaires est requis pour les collections hybride.

### <a name="make-sure-your-domaincom-match"></a>Vérifiez que votre "@domain.com" correspond à
Avant de commencer, vérifiez que le nom UPN pour votre forêt locale correspond le suffixe de votre domaine Azure AD. 

Une fois que vous configurez le suffixe UPN dans Azure AD, tous les utilisateurs qui se connectent à Azure RemoteApp connecterez comme “user@ <the suffix you set up>». Vérifiez que les utilisateurs peuvent également se connecter à l’aide de la même user@suffix dans le domaine local. Dans certains cas, vous pouvez configurer un nom de domaine dans Azure AD tout en spécifiant un suffixe de domaine différent pour l’utilisateur montant Dans ce cas, vos utilisateurs ne pourront pas vous connecter à n’importe quel ordinateurs à un domaine ou ressources via Azure RemoteApp.

Par exemple, si vous configurez votre suffixe UPN dans AAD en tant que contoso.com, mais certains utilisateurs locaux/AD sont configurés pour vous connecter à l’aide @contoso.uk, puis ces utilisateurs ne pourront pas à vous connecter correctement à la collection ARA. Utilisateurs UPN dans AAD et AD doivent être identique pour la connexion soit possible »

### <a name="create-objects-for-azure-remoteapp"></a>Créer des objets pour Azure RemoteApp
Vous devez également créer des objets Active Directory les locaux suivants :

- Un compte de service pour fournir un accès aux ressources du domaine pour les programmes RemoteApp en joignant les points de terminaison RDSH sur le domaine local.
- Une unité d’organisation (OU) de contenir des objets machine RemoteApp. Utilisation de l’unité d’organisation est recommandée (mais pas obligatoire) à isoler les comptes et les stratégies que vous utiliserez avec RemoteApp.

Vous devez ces deux objets lorsque vous créez votre collection RemoteApp, veillez donc à effectuer ces étapes, au préalable.
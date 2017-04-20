<properties 
    pageTitle="Quel type de collection de sites devez-vous pour RemoteApp Azure ? | Microsoft Azure" 
    description="En savoir plus sur les types de collections disponibles avec Azure RemoteApp." 
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



# <a name="what-kind-of-collection-do-you-need-for-azure-remoteapp"></a>Quel type de collection de sites devez-vous pour RemoteApp Azure ?

> [AZURE.IMPORTANT]
> RemoteApp Azure a été interrompu. Lire l' [annonce](https://go.microsoft.com/fwlink/?linkid=821148) pour plus d’informations.

Azure RemoteApp vous permet de partager des applications et des ressources avec des utilisateurs sur n’importe quel appareil. Nous le faire en créant des collections de sites pour stocker les applications et les ressources, et vous partagez ces collections avec des utilisateurs. Il existe 2 options autre collection de sites, avec différente options réseau et l’authentification - qui vous convient ?

Examinons les différents aspects liés aux et les choix que vous devez prendre tirer le meilleur parti de votre collection de sites RemoteApp Azure. 


## <a name="quick-differences-between-the-collection-types"></a>Rapides différences entre les types de collection de sites

|           | Cloud | Environnement hybride |
|-----------|-------|--------|
|Utiliser un VNET existant| Oui| Oui|
|Nécessite des comptes connectés Active Directory (DirSync)| N°| Oui|
|Nécessite la jointure de domaines| N°| Oui|
|Requiert contrôleur de domaine accessible à VNET| N°| Oui|

## <a name="cloud-collections"></a>Collections de cloud
- Rapide créer - la collection est configurée rapidement, ce qui signifie que vos applications accéder aux utilisateurs plus rapides.
- Former vos propres applications ou partager notre. Vous pouvez utiliser une image personnalisée (généré à partir d’un ordinateur virtuel Azure) ou l’une des images inclus avec votre abonnement.
- Vous n’avez pas besoin de configurer une connexion entre votre collection de sites et de votre domaine local.
- Mais vous pouvez éventuellement utiliser votre propre VNET Azure permet d’accéder à votre environnement local pour le partage de données ou d’utiliser l’authentification Windows non en ressources, telles que SQL Server (à l’aide de l’authentification de base de données).


OK, comment en créer un ?

- Cloud uniquement ? Créer avec l’option **Créer rapide** dans le portail.
- Cloud + VNET ? Créer à l’aide de l’option **créer avec VNET** mais choisissez ne pas participer à un domaine.

## <a name="hybrid-collections"></a>Collections hybride
- Fournir un accès complet au réseau local + VNET Azure.
- Inclut l’accès de jointure de domaine pour les applications et les données. Les applications à distance peuvent authentification par rapport à votre locale d’Active Directory : ils peuvent accéder aux ressources dans votre domaine.
- Activer l’analyse avancée et gestion des solutions de System Center existantes et des stratégies de groupe Windows (grâce à une image personnalisée basée sur Windows Server 2012 R2)
- Prend en charge [ExpressRoute](https://azure.microsoft.com/services/expressroute/) pour connecter votre VNET Azure à votre VNET local.

Créer à l’aide de l’option **créer avec VNET** et décidez de rejoindre un domaine.

## <a name="authentication-options"></a>Options d’authentification
RemoteApp Azure prend en charge les comptes Microsoft et Azure Active Directory, mais pas toutes les collections prennent en charge toutes les méthodes. 

| Type de compte                      |                                                             | Cloud | Cloud + VNET | Environnement hybride |
|-----------------------------------|-------------------------------------------------------------|-------|--------------|--------|
| Compte Microsoft                 |                                                             | Oui   | Oui          | N°     |
| Azure Active Directory (AD Azure) |                                                             |       |              |        |
|                                   | Azure AD uniquement                                               | Oui   | Oui          | N°     |
|                                   | Se connecter AD avec la synchronisation de mot de passe                               | Oui   | Oui          | Oui    |
|                                   | Se connecter AD sans synchronisation de mot de passe                            | Oui   | Oui          | N°     |
|                                   | AD se connecter avec AD FS                                       | Oui   | Oui          | Oui    |
|                                   | 3e pris en charge Azure fournisseurs tiers (par exemple, Ping) | Oui   | Oui          | Oui    |
| Authentification multifacteur       |                                                             | Oui   | Oui          | Oui    |



### <a name="cloud-and-cloud--vnet"></a>Cloud et Cloud + VNET 
Avec les collections de cloud, vous pouvez utiliser des comptes Microsoft, les comptes Azure Active Directory ou un mélange des deux. Utiliser les comptes qui fonctionnent le mieux pour vos utilisateurs.

Il n’existe aucune spécifique est requise pour l’utilisation de comptes Microsoft. 

Si vous souhaitez utiliser les comptes Azure Active Directory, vous devez vous assurer que votre client Azure AD correspond à celui associé à votre abonnement. Lorsque vous avez créé votre abonnement Azure RemoteApp, le client Azure AD que vous utilisiez a été automatiquement associé à votre abonnement. Tout utilisateur Azure AD que vous octroyer des autorisations doit être ce même client. Si nécessaire, vous pouvez [Modifier le client Azure AD](remoteapp-changetenant.md) associé à votre abonnement.
 
### <a name="hybrid-or-cloud--azure-ad--ad"></a>Hybride (ou cloud + Azure AD + AD)

À l’aide d’Azure AD + local Active Directory est requis pour une collection de sites hybride. Vous devez utiliser AD Connect pour intégrer les deux annuaires. Mais vous avez certains choix lorsqu’il s’agit de la configuration AD Connect. 

Il existe 2 AD Connect scénarios - utilisation de la synchronisation de mot de passe ou à l’aide de la fédération AD. Consultez les [informations de connexion AD](../active-directory/active-directory-aadconnect.md) pour déterminer lequel de ces fonctionne de façon optimale pour vous.

Vous pouvez également utiliser Azure AD + AD une collection de cloud. Vérifiez que vous suivez la même procédure de configuration.

Consultez [Azure AD + Active Directory requise pour Azure RemoteApp](remoteapp-ad.md) pour connaître les étapes nécessaires pour configurer Azure AD et Active Directory.

## <a name="go-create-your-collection"></a>Accédez à créer votre collection !
OK, je pense que nous avons trouvé à présent, il est une chose qui reste à faire : créer votre première collection RemoteApp Azure.

[Créer une collection de cloud](remoteapp-create-cloud-deployment.md) ou [créer une collection hybride](remoteapp-create-hybrid-deployment.md) - obtenir simple création.

<properties
    pageTitle="Ajouter un utilisateur à votre collection RemoteApp Azure | Microsoft Azure"
    description="Découvrez comment ajouter des utilisateurs à votre collection de sites RemoteApp Azure"
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

# <a name="how-to-add-a-user-to-your-azure-remoteapp-collection"></a>Comment ajouter un utilisateur à votre collection de sites RemoteApp Azure

> [AZURE.IMPORTANT]
> RemoteApp Azure a été interrompu. Lire l' [annonce](https://go.microsoft.com/fwlink/?linkid=821148) pour plus d’informations.

Avant de vos utilisateurs peuvent voir et utiliser vos applications dans Azure RemoteApp, vous devez leur accorder l’accès à votre collection de sites. Il s’agit de la partie facile : sous l’onglet de **L’accès utilisateur** , entrez les informations de compte pour l’utilisateur, puis cliquez sur la coche.

Les informations de compte avez-vous besoin ? Qui dépend du type de collection de sites que vous avez créé (cloud ou hybride) et que vous utilisez Office 365 ProPlus dans cette collection.

## <a name="supported-user-identities"></a>Identités d’utilisateurs pris en charge

Les différents types de collection (cloud et hybride) prend en charge à l’aide de diverses identités d’utilisateur pour l’accès aux applications.  

Pour une collection hybride de RemoteApp, vous devez configurer une infrastructure de domaine Active Directory sur site et un client Azure Active Directory avec intégration d’annuaire (et éventuellement authentification unique). En outre, vous devez créer certains objets Active Directory dans le répertoire local.  

Pour une collection de cloud de RemoteApp, tout utilisateur qui a Azure Active Directory prend en charge des identités peut être accordée l’accès utilisateur aux RemoteApp à inclure Accounts Microsoft.  Voir le tableau ci-dessous.

Les utilisateurs d’Office 365 sont utilisateurs Azure Active Directory. Si elle a hybride Azure Active Directory, annuaire synchronisé comptes, ils peuvent accéder utilisateur dans un déploiement hybride RemoteApp.   

Vous pouvez utiliser ce tableau comme une référence rapide pour lequel identité est pris en charge dans votre collection de sites et quels sont les exigences d’Active Directory.

|Comptes d’utilisateur |Cloud   |Environnement hybride|
|--------------|--------|------|
|Compte Microsoft|     Oui|    N°|
|Azure Active Directory (AD Azure)| | |
|Azure cloud AD uniquement    |Oui    |N° |
|ADsync avec la synchronisation de mot de passe  |Oui    |Oui    |
|ADsync sans synchronisation de mot de passe|  Oui |N° |
|ADsync avec AD FS  |Oui    |Oui    |
|[3e partie Azure pris en charge les fournisseurs d’identité](https://msdn.microsoft.com/library/azure/jj679342.aspx)  (exemple Ping) |Oui    |Oui|
|Authentification multifacteur    |Oui    |Oui    |

Consultez [plus d’informations](remoteapp-ad.md) sur la configuration d’Active Directory pour RemoteApp.


> [AZURE.NOTE] Les utilisateurs Azure Active Directory doivent être à partir du client associé à votre abonnement. (Vous pouvez afficher et modifier votre abonnement sous l’onglet **paramètres** dans le portail. Voir [Modifier le client Azure Active Directory utilisé par RemoteApp](remoteapp-changetenant.md) pour plus d’informations.)

## <a name="office-365-proplus-user-account-information"></a>Informations de compte Office 365 ProPlus utilisateur
Si vous utilisez l’image du modèle Office 365 ProPlus dans votre collection de sites *ou* si vous avez créé une image personnalisée qui utilise Office 365, vous pouvez uniquement ajouter des utilisateurs Azure Active Directory qui ont des abonnements Office 365 pour le domaine par défaut de votre abonnement. Pour plus d’informations, reportez-vous [à l’aide d’Office 365 avec Azure RemoteApp](remoteapp-o365.md) .

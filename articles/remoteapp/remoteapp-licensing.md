<properties
    pageTitle="Gestion des licences Azure RemoteApp | Microsoft Azure"
    description="Apprenez comment fonctionnement la gestion des licences dans Azure RemoteApp."
    services="remoteapp"
    documentationCenter=""
    authors="lizap"
    manager="mbaldwin" />

<tags
    ms.service="remoteapp"
    ms.workload="compute"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="08/15/2016"
    ms.author="elizapo" />


# <a name="how-does-licensing-work-in-azure-remoteapp"></a>Gestion des licences fonctionnement dans Azure RemoteApp ?

> [AZURE.IMPORTANT]
> RemoteApp Azure a été interrompu. Lire l' [annonce](https://go.microsoft.com/fwlink/?linkid=821148) pour plus d’informations.

Afin que vous avez configuré votre service RemoteApp Azure, créé vos modèles et que vous êtes prêt à publier des applications à vos utilisateurs. Mais il reste une dernière chose à identifier : gestion des licences. Simplement comment fonctionne licence pour RemoteApp et pour les applications que vous partagez par le biais RemoteApp ?

RemoteApp ne nécessite pas les licences Windows ou les licences d’accès client de bureau à distance. Votre abonnement prend en charge le côté RemoteApp lui-même. (Consultez les détails de la [tarification](https://azure.microsoft.com/pricing/details/remoteapp)).

Si vous utilisez l’une des images inclus dans votre abonnement, vous pouvez partager une des applications installées sur cette image sans avoir besoin d’une licence distincte. Par exemple, si vous utilisez l’image du modèle Windows Server 2012 R2 pour créer votre collection de sites, vous pouvez partager System Center Endpoint Protection avec d’autres utilisateurs. Les seules exceptions à cette règle sont Office 365 ProPlus, qui nécessite un abonnement spécifique, et Office 2013, qui ne peut pas être partagé dans une collection de production.

Si vous souhaitez utiliser l’image du modèle Office 365 fournie avec RemoteApp, vous devez disposer d’un plan Office 365 ProPlus *existant* . Il en est de même pour n’importe quelle application Office 365 que vous publiez à l’aide d’un modèle personnalisé. Vous devez activer les applications avec vos propres abonnement. C’est vrai pour les abonnements d’évaluation et payantes. Si vous souhaitez utiliser l’image du modèle Office 365 pendant la période d’évaluation, *et vous n’avez pas déjà un abonnement*, accédez à la page Office 365 [s’inscrire](https://go.microsoft.com/fwlink/p/?LinkID=403802) pour un abonnement d’évaluation. Voir [comment RemoteApp et Office fonctionnent ensemble ?](remoteapp-o365.md) pour plus d’informations.

Si, pendant la période d’évaluation, vous ne voulez pas obtenir un abonnement d’évaluation d’Office 365, utilisez le Office 2013 Professionnel Plus modèle d’image qui est fourni avec RemoteApp. Cette image du modèle peut uniquement être utilisée pour les 30 jours et ne peut pas être convertie en une collection de sites payant.

Pour les autres applications, vous devez vous assurer que vous disposez de la licence de partager l’application.

Qui a du sens, droite ? Vous pouvez publier une application que vous êtes légalement habilité à partager. Et vous devez vous assurer que vous êtes vraiment habilité à partager vos programmes.

Veuillez noter que vous ne pouvez pas utiliser un contrat de licence d’accès client ou de licence en Volume d’une collection de cloud. Vous *pouvez* utiliser un contrat de licence en Volume pour activer des applications dans votre collection de sites hybride (à l’exception d’Office). Vous devez uniquement les installer sur votre image du modèle à partir du support de licence en Volume. Suivre les informations à partir de votre revendeur d’applications pour installer des licences dans un environnement de bureau à distance.

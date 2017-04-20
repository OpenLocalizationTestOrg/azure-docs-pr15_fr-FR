
<properties
    pageTitle="Utiliser Office avec Azure RemoteApp | Microsoft Azure" 
    description="Découvrez comment Office et Azure RemoteApp collaborer"
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

# <a name="using-office-with-azure-remoteapp"></a>Utiliser Office avec Azure RemoteApp

> [AZURE.IMPORTANT]
> RemoteApp Azure a été interrompu. Lire l' [annonce](https://go.microsoft.com/fwlink/?linkid=821148) pour plus d’informations.

Vous avez deux possibilités pour héberger les applications Office dans Azure RemoteApp : Office 365 ProPlus ou version d’évaluation d’Office 2013 Professional Plus.

**Bonjour, saviez-vous que nous avons une nouvelle mieux article qui remplacera bientôt cela ? Découvrez [comment utiliser votre abonnement Office 365 avec Azure RemoteApp](remoteapp-officesubscription.md). Il traite toutes les informations que vous avez besoin pour utiliser Office 365 + RemoteApp Azure.**

## <a name="office-365-proplus"></a>Office 365 ProPlus
Vous pouvez créer une collection de RemoteApp à l’aide de l’image du modèle Office 365 ProPlus. Cette option vous permet d’étendre votre service Office 365 à RemoteApp. Vous devez disposer une offre d’abonnement existant et vos utilisateurs doivent être une licence pour le service Office 365 ProPlus, individuellement ou via Office 365 offres de service.

RemoteApp prend en charge l’activation de l’ordinateur Office 365 partagé. Lorsque vous activez l’activation de l’ordinateur partagé et utilisez l' [outil déploiement d’Office](http://www.microsoft.com/download/details.aspx?id=36778) pour l’installation, Office 365 ProPlus installe sans être activé. Si un utilisateur signe dans une collection de sites qui contient Office 365, Office vérifie si l’utilisateur a été mis en service pour Office 365 ProPlus. Si Oui, Office active temporairement Office 365 ProPlus - cette activation persiste jusqu'à ce que ce signe utilisateurs déconnecter le service.

Pour utiliser l’activation de l’ordinateur Office 365 partagé, vous devez créer un [modèle personnalisé](remoteapp-create-custom-image.md) et installer Office 365 ProPlus, suivre [les instructions suivantes](https://technet.microsoft.com/library/dn782858.aspx).

Vous pouvez gérer les licences d’Office 365 de vos utilisateurs sur le [Portail d’administration d’Office 365](https://portal.office365.com/). En savoir plus sur les [offres de service Office 365](http://technet.microsoft.com/library/office-365-plan-options.aspx).  


## <a name="office-2013-professional-plus-trial"></a>Version d’évaluation d’Office 2013 Professionnel Plus
Au cours d’une version d’évaluation de 30 jours de RemoteApp, vous pouvez utiliser l’image du modèle Professionnel Plus (version d’évaluation) Office 2013 pour créer une collection de RemoteApp. Vous pouvez affecter des utilisateurs à cette collection d’évaluation à l’aide de leurs comptes de travail Azure Active Directory ou les comptes Microsoft. Aucun abonnement supplémentaire n’est requise.

Il s’agit d’une option intéressante aperçu du produit et d’obtenir un bon sentiment pour Office dans RemoteApp. Toutefois, cette option est prévu pour l’évaluation et le test uniquement. Collections RemoteApp créées à l’aide de l’image du modèle Professionnel Plus (version d’évaluation) Office 2013 ne peut pas être transférées vers le mode de production et il seront désactivées à la fin de la période d’évaluation.

## <a name="switching-from-trial-to-production"></a>Passer de la version d’évaluation à production
Lorsque vous démarrez votre version d’évaluation gratuite de 30 jours, une note dans la section RemoteApp du portail vous signale combien il vous reste dans la version d’évaluation avant que vous avez besoin pour effectuer la transition à un compte payant. Vous pouvez activer votre compte et basculer vers le mode de production via le lien dans cette note.

Lorsque vous activez votre compte, cela affecte toutes les collections de RemoteApp dans votre compte.

- Collections de sites qui exécutent Windows Server 2012 R2 ou les images de modèle Office 365 ProPlus passera à production en toute transparence. Toutes les données et paramètres utilisateur, y compris les sessions en cours, restent intactes.
- Si vous avez téléchargé des images de modèle personnalisé, collections à l’aide de ces images également passera en toute transparence.
- L’image du modèle Professionnel Plus (version d’évaluation) Office 2013 est destiné d’évaluation uniquement. Collections en cours d’exécution avec cette image du modèle ne peut pas être transférées vers production. Ils mettra dans l’état « désactivé ».


Si vous ne pas passer au mode de production à l’expiration de votre version d’évaluation, vos collections RemoteApp seront désactivées. Ne vous inquiétez pas : les paramètres et les données des utilisateurs sont enregistrées par un autre 90 jours, afin de pouvoir toujours activer votre service et passer au mode de production sans perte de données.

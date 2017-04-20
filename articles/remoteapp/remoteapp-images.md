<properties
    pageTitle="Nouveautés dans les images des modèles RemoteApp Azure ? | Microsoft Azure"
    description="En savoir plus sur les images des modèles inclus avec Azure RemoteApp."
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

# <a name="what-is-in-the-azure-remoteapp-template-images"></a>Nouveautés dans les images des modèles RemoteApp Azure ?

> [AZURE.IMPORTANT]
> RemoteApp Azure a été interrompu. Lire l' [annonce](https://go.microsoft.com/fwlink/?linkid=821148) pour plus d’informations.

Votre abonnement Azure RemoteApp inclut trois images de modèle :


- Windows Server 2012
- Microsoft Office 365 ProPlus (abonnement Office 365 obligatoire)
- Microsoft Office Professionnel Plus 2013 (version d’évaluation uniquement)

> [AZURE.IMPORTANT]Votre abonnement Azure RemoteApp vous accorde le qu'accès au logiciel dans les images, à l’exception Office 365 ProPlus, qui nécessite un abonnement spécifique et Office 2013, qui ne peuvent pas être utilisés en production. Cela signifie que vous pouvez partager des programmes ou des applications sur les images des modèles avec d’autres utilisateurs. Par exemple, si vous créez une collection de sites qui utilise l’image de Windows Server 2012 R2, vous pouvez publier System Center Endpoint Protection pour les utilisateurs d’accéder à via RemoteApp.
>
> Consultez les [Détails de la licences RemoteApp](remoteapp-licensing.md) pour plus d’informations. Et informations de la gestion des licences [à l’aide de Microsoft Office avec Azure RemoteApp](remoteapp-o365.md) pour le bureau.

Poursuivez votre lecture pour plus d’informations sur ce que contient chaque image.

## <a name="windows-server-2012-r2--the-vanilla-image"></a>Windows Server 2012 R2 (« l’image classique »)
Cette image est basée sur le système d’exploitation Microsoft Windows Server 2012 R2 centre de données et comporte les rôles et les fonctionnalités installées pour la configuration requise pour les images de modèle Azure RemoteApp suivants :


- .NET framework 4.5, 3.5.1, 3.5
- Expérience utilisateur
- Services d’encre et l’écriture manuscrite
- Media Foundation
- Hôte de la Session Bureau à distance
- Windows PowerShell 4.0
- Windows PowerShell ISE
- Prise en charge WoW64

Cette image est également les applications suivantes :

- Adobe Flash Player
- Microsoft Silverlight
- Microsoft System Center 2012 Endpoint Protection
- Microsoft Windows Media Player


## <a name="microsoft-office-365-proplus-subscription-required"></a>Microsoft Office 365 ProPlus (abonnement obligatoire)
Office 365 est l’application plus demandée, afin que nous avons créé une image « personnalisée » pour travailler avec.

Cette image est une extension de l’image classique et comprend les éléments suivants de Microsoft Office 365 ProPlus installés ainsi que les composants décrits dans l’image de Windows Server 2012 R2 :


- Access
- Excel
- Lync
- OneNote
- OneDrive entreprise (Notez que l’agent de synchronisation n’est pas pris en charge pour une utilisation avec Azure RemoteApp)
- Outlook
- PowerPoint
- Word
- Outils de vérification linguistique Microsoft Office

L’image inclut également Visio Pro et Project Pro.

Et les applications suivantes, ainsi que :

- SQL Native client
- Pilote ODBC
- Exploration de données SQL Server client
- MasterDataServices client
- Microsoft Publisher
- PowerQuery
- Carte Power Map


Toutes les fonctionnalités des applications Office 365 ProPlus sont disponible uniquement pour les utilisateurs qui ont une offre Office 365 ProPlus. Pour plus d’informations sur Office 365 offres d’abonnement à voir les [offres de service Office 365](http://technet.microsoft.com/library/office-365-plan-options.aspx). Vous avez des questions ? Consultez les informations [Office 365 + RemoteApp](remoteapp-o365.md) . Consultez également l’article nouveau, [l’utilisation de votre abonnement Office 365 avec Azure RemoteApp](remoteapp-officesubscription.md).

Notez que vous devez obtenir une licence Office 365 ProPlus, Visio Pro et Project Pro séparément, ils ont chacun leur propre licence.

## <a name="microsoft-office-2013-professional-plus-trial-only"></a>Microsoft Office Professionnel Plus 2013 (version d’évaluation uniquement)
Pendant la période d’évaluation gratuite, vous pouvez tester le service avec l’image Office 2013.

Cette image est une extension de l’image classique et comprend les éléments suivants de Microsoft Office Professionnel Plus 2013 installés ainsi que les composants décrits dans l’image de Windows Server 2012 R2 :


- Access
- Excel
- Lync
- OneNote
- OneDrive entreprise (Notez que l’agent de synchronisation n’est pas pris en charge pour une utilisation avec Azure RemoteApp)
- Outlook
- PowerPoint
- Projet
- Visio
- Word
- Outils de vérification linguistique Microsoft Office

> [AZURE.IMPORTANT]**Informations légales :** Cette image n’inclut pas une licence Microsoft Office et *ne peuvent pas être utilisés pour la production*. Le Office Professionnel Plus 2013 image est destiné d’évaluation uniquement. Si vous voulez utiliser les applications Office dans Azure RemoteApp de production, vous devez utiliser l’image d’Office 365 ProPlus. Pour plus d’informations sur la gestion des licences Office, reportez-vous [à l’aide d’Office 365 avec Azure RemoteApp](remoteapp-o365.md)

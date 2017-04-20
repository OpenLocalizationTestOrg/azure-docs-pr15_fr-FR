
<properties
    pageTitle="Migrer les données utilisateur à partir d’Azure RemoteApp | Microsoft Azure"
    description="Découvrez comment migrer vos données utilisateur et déconnexion RemoteApp Azure."
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



# <a name="how-to-migrate-data-into-and-out-of-azure-remoteapp"></a>Comment migrer les données dans et se déconnecter de RemoteApp Azure

> [AZURE.IMPORTANT]
> RemoteApp Azure a été interrompu. Lire l' [annonce](https://go.microsoft.com/fwlink/?linkid=821148) pour plus d’informations.

Vous pouvez utiliser de nombreuses différents outils et méthodes pour transférer des [données utilisateur](remoteapp-upd.md) dans et se déconnecter de RemoteApp Azure. Voici quelques méthodes :

- Copier et coller à l’aide de partage du Presse-papiers
- Copier des fichiers et des données à un serveur de fichiers
- Copier des fichiers sur SkyDrive Pro via un navigateur
- Copier des fichiers à l’aide de la redirection

>[AZURE.NOTE] 
> Vous ne pouvez pas activer la OneDrive pour les entreprises ou grand public agents de synchronisation - elles [ne sont pas prises en charge](remoteapp-onedrive.md) dans Azure RemoteApp.

## <a name="use-copy-and-paste-in-file-explorer"></a>Utilisez copier-coller dans l’Explorateur de fichiers

Copier et coller en utilisant le Presse-papiers est activée [par défaut](remoteapp-redirection.md)l’option RemoteApp les déploiements. Cela permet aux utilisateurs de copier des fichiers entre leurs applications de PC et RemoteApp locales. Souvent, tout au long de l’utilisation des applications dans RemoteApp normal, utilisateurs ont enregistrées fichiers à leurs UPDs - déplacement que données déconnecter RemoteApp soient simples :

1. [Publier l’Explorateur de fichiers sous forme d’une application](remoteapp-publish.md) dans une collection de sites RemoteApp. (Notez qu’il s’agit d’une tâche administrative).
2. Diriger vos utilisateurs pour lancer l’application de l’Explorateur de fichiers que vous avez publié et qui permet de copier et coller des fichiers à la fois dans leur UDP et se déconnecter de celle-ci.

## <a name="upload-files-and-data-to-a-file-server-by-using-standard-network-file-copy"></a>Télécharger des fichiers et les données sur un serveur de fichiers à l’aide de la copie de fichiers réseau standard

Souvent, les organisations utilisent les serveurs de fichiers pour stocker des données générales. Si vous connaissez le nom du serveur ou l’emplacement, vos utilisateurs pouvant parcourir le réseau local pour le serveur, puis copiez ses fichiers, bien qu’ils avaient ci-dessus. Vous souhaiterez à nouveau publier l’Explorateur de fichiers sur RemoteApp et partagez-le avec d’autres utilisateurs.

>[AZURE.NOTE] 
> Le serveur de fichiers doit se trouver sur le réseau pouvant être routé RemoteApp a été déployé dans.

## <a name="copy-files-to-onedrive-for-business"></a>Copier des fichiers sur SkyDrive Pro
Bien que vous ne pouvez pas activer l’agent de synchronisation d’entreprise dans RemoteApp OneDrive, vous pouvez copier les fichiers à partir de votre UDP sur SkyDrive Pro via un navigateur. 

1. Publier l’Explorateur de fichiers sur RemoteApp et puis demander aux utilisateurs d’accéder aux fichiers par le biais cette application. 
2. Il est plus facile de transférer des fichiers si elles sont compressées, afin que les utilisateurs doivent créer un fichier .zip qui contient tous les fichiers à déplacer vers OneDrive entreprise.
3. Demander aux utilisateurs d’accéder au portail Office 365, puis accédez à OneDrive et téléchargez le fichier .zip.

## <a name="copy-files-by-using-drive-redirection"></a>Copier des fichiers à l’aide de la redirection du lecteur

Si vous avez activé [la redirection de lecteur](remoteapp-redirection.md), vous avez déjà créé un lecteur mappé pour vos utilisateurs. Dans ce cas, ils peuvent zip ses fichiers sur le lecteur redirigé et puis les enregistrer sur leur ordinateur local.
<properties 
    pageTitle="RemoteApp Azure - comment la bande passante réseau et qualité de l’expérience travail ensemble ? | Microsoft Azure"
    description="Découvrez comment la bande passante réseau dans Azure RemoteApp peut avoir un impact sur la qualité de l’utilisateur de votre expérience."
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

# <a name="azure-remoteapp---how-do-network-bandwidth-and-quality-of-experience-work-together"></a>RemoteApp Azure - comment la bande passante réseau et qualité de l’expérience travail ensemble ?

> [AZURE.IMPORTANT]
> RemoteApp Azure a été interrompu. Lire l' [annonce](https://go.microsoft.com/fwlink/?linkid=821148) pour plus d’informations.

Lorsque vous examinez la [bande passante réseau globale](remoteapp-bandwidth.md) requis pour Azure RemoteApp, gardez à l’esprit les facteurs suivants : il s’agit des tous partie d’un système dynamique a un impact sur l’expérience utilisateur globale. 

- **Bande passante réseau disponible et conditions actuelles du réseau** - un jeu de paramètres (perte de latence, gigue) sur le même réseau à un moment donné peut avoir un impact sur l’application de diffusion en continu, ce qui signifie une réduction expérience globale des utilisateurs. La bande passante disponible dans votre réseau est une fonction de l’encombrement, perte aléatoire latence étant donné que tous ces paramètres affectent le mécanisme de contrôle encombrement, qui à son tour contrôles la vitesse de transmission pour éviter les conflits.  Par exemple, un réseau avec perte de données ou un réseau avec une latence élevée s’améliorer l’expérience utilisateur incorrectes même sur un réseau avec la bande passante de 1 000 Mo. La perte et la latence varient en fonction du nombre d’utilisateurs qui se trouvent sur le même réseau et ce que font les utilisateurs (par exemple, visionner des vidéos, le téléchargement ou le chargement de fichiers volumineux, l’impression).
- **Scénario d’utilisation** - l’expérience dépend de ce que font les utilisateurs en tant qu’individus et en tant que groupe sur le même réseau. Par exemple, la lecture d’une diapositive nécessite une seule image à mettre à jour ; Si l’utilisateur skims et défile sur le contenu d’un document de texte, dont elles ont besoin d’un nombre plus élevé d’images à être mis à jour par seconde. La communication en arrière sur le serveur dans ce scénario susceptibles de consommer davantage de bande passante réseau. Envisagez également un exemple extrême : plusieurs utilisateurs sont visionner des vidéos haute définition (par exemple, résolution 4 Ko), en maintenant les téléconférences HD, jeux 3D vidéo ou travailler sur des systèmes de CAO. Tous ces éléments peuvent rendre même un réseau de bande passante réellement élevé pratiquement inutilisable.
- **Résolution d’écran et le nombre d’écrans** - davantage de bande passante réseau est requis pour la mise à jour complète des écrans plus grandes que les petits écrans. La technologie sous-jacente ne fait du bon travail d’encodage et de transmettre uniquement les régions des écrans qui ont été mis à jour, mais de temps, l’écran entier doit être mis à jour. Lorsque l’utilisateur a un écran de résolution supérieur (par exemple résolution 4 Ko), cette mise à jour nécessite davantage de bande passante réseau à un écran avec une résolution inférieure (par exemple, 1024x768px). Cette même logique s’applique si vous utilisez plusieurs écrans pour la redirection. La bande passante doit augmenter le nombre d’écrans.
- **Redirection Presse-papiers et appareil** - il s’agit d’un problème pas très évident, mais dans de nombreux cas si un utilisateur stocke une grande partie des données dans le Presse-papiers, il faut un peu de temps pour ces informations transférer à partir du client de bureau à distance sur le serveur. L’expérience en aval peut être affecté par l’expérience d’envoi en amont le contenu du Presse-papiers. Vaut pour la redirection de périphérique - si un scanneur ou une webcam génère un grand nombre de données qui doivent être envoyées en amont sur le serveur ou une imprimante a besoin recevoir un document de grande taille, ou stockage local doit être disponible pour une application en cours d’exécution dans le cloud pour copier un fichier volumineux, les utilisateurs peuvent remarquer perte d’images ou temporairement vidéo « figée », car les données nécessaires pour la redirection de périphérique augmente la bande passante réseau besoins. 

Lorsque vous évaluez vos besoins de bande passante réseau, veillez à prendre en compte toutes ces facteurs ne fonctionne comme un système.

Maintenant, revenez à l' [article de la bande passante réseau principal](remoteapp-bandwidth.md)ou passer au test de votre [bande passante réseau](remoteapp-bandwidthtests.md).

## <a name="learn-more"></a>Pour en savoir plus
- [Utilisation de la bande passante réseau Azure RemoteApp d’estimation](remoteapp-bandwidth.md)

- [Azure RemoteApp - tester votre utilisation de la bande passante réseau avec quelques scénarios courants](remoteapp-bandwidthtests.md)

- [Azure RemoteApp la bande passante réseau - instructions générales (si vous ne pouvez pas tester vos propres)](remoteapp-bandwidthguidelines.md)
<properties 
    pageTitle="Azure RemoteApp la bande passante réseau - instructions générales | Microsoft Azure"
    description="Comprendre des instructions de bande passante réseau simple pour vos collections de RemoteApp Azure et les applications."
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
    
# <a name="azure-remoteapp-network-bandwidth---general-guidelines-if-you-cant-test-your-own"></a>Azure RemoteApp la bande passante réseau - instructions générales (si vous ne pouvez pas tester vos propres)

> [AZURE.IMPORTANT]
> RemoteApp Azure a été interrompu. Lire l' [annonce](https://go.microsoft.com/fwlink/?linkid=821148) pour plus d’informations.

Si vous n’avez pas l’heure ou la possibilité d’exécuter les [tests de bande passante réseau](remoteapp-bandwidthtests.md) pour Azure RemoteApp, voici quelques conseils assez génériques qui peuvent vous aider à évaluer la bande passante réseau par utilisateur.

Si vous avez un mélange de ces scénarios, n’est pas recommandé rien inférieure (ou égal à) 10 Mo/s en tant que la bande passante réseau MINIMUM pour les applications modernes connecté à Internet dans un environnement à distance. (Même si, comme indiqué, Ceci garantit pas une meilleure qualité de l’expérience utilisateur moyenne.)

## <a name="complex-powerpoint-simple-powerpoint"></a>PowerPoint complexe, PowerPoint simple

Azure RemoteApp peut mieux sur LAN 100 Mo. Le profil de réseau 10 Mo/s, lorsque gigue au-dessus de 120 ms est supérieure à 5 %, l’utilisateur verra une expérience moyenne. À 1 Mo/s que les différents sont éblouissants - par exemple, dans un diaporama, l’utilisateur peut ne pas afficher transitions animées à, car les images sont ignorées.

## <a name="internet-explorer-mixed-pdf-pdf-text"></a>Internet Explorer, mixte PDF, PDF, du texte

Profil de réseau 10 Mo/s est près de réseau local dans la plupart des aspects. 1 Mo/s constituent une expérience OK, bien qu’il peut y avoir certains gigue lorsqu’un utilisateur fait défiler bien qu’il existe des images à l’écran.

## <a name="flash-video-youtube"></a>Flash video (YouTube)

100 Mo/s LAN fournit la meilleure expérience, tandis que 10 Mo/s est acceptable (c'est-à-dire nous faire face à la fréquence d’images mais gigue augmente). À 1 Mo/s, gigue est très haute et visibles.

## <a name="word-typing-word-remote-input"></a>Word frappe (Word entrée à distance)
Il s’agit d’un scénario de l’utilisation de faible bande passante. À 256 KB/s nous fournissons aussi bien d’une expérience en tant que réseau local.

## <a name="learn-more"></a>Pour en savoir plus
- [Utilisation de la bande passante réseau Azure RemoteApp d’estimation](remoteapp-bandwidth.md)

- [RemoteApp Azure - comment la bande passante réseau et qualité de l’expérience travail ensemble ?](remoteapp-bandwidthexperience.md)

- [Azure RemoteApp - tseting votre utilisation de la bande passante réseau avec quelques scénarios courants](remoteapp-bandwidthtests.md)
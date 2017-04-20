
<properties 
    pageTitle="Estimer l’utilisation de la bande passante réseau RemoteApp Azure | Microsoft Azure"
    description="En savoir plus sur la bande passante réseau pour vos collections RemoteApp Azure et les applications."
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

# <a name="estimate-azure-remoteapp-network-bandwidth-usage"></a>Utilisation de la bande passante réseau Azure RemoteApp d’estimation 

> [AZURE.IMPORTANT]
> RemoteApp Azure a été interrompu. Lire l' [annonce](https://go.microsoft.com/fwlink/?linkid=821148) pour plus d’informations.

RemoteApp Azure utilise le protocole RDP (Remote Desktop) pour communiquer entre les applications qui s’exécutent dans le cloud Azure et vos utilisateurs. Cet article fournit des instructions de base que vous pouvez utiliser pour estimer que l’utilisation de réseau et potentiellement évaluer l’utilisation de la bande passante réseau par utilisateur RemoteApp Azure.

Estimation de l’utilisation de la bande passante par utilisateur est très complexe et nécessite l’exécution de plusieurs applications simultanément dans des scénarios multitâche où les applications peuvent avoir un impact sur les performances en fonction de leur à la demande pour la bande passante réseau. Même le type de client de bureau à distance (par exemple, le client Mac et HTML5 client) peut entraîner des résultats différents de bande passante. Pour vous aider à effectuer ces problèmes, nous allons décomposer les scénarios d’utilisation en plusieurs des catégories courantes pour répliquer des scénarios réels. (Où la réalité est, bien entendu, un mélange des catégories et diffère par l’utilisateur.)

Nous aller plus loin - note admettant RDP fournit une bonne expérience excellente pour la plupart des scénarios d’utilisation sur les réseaux avec une latence en dessous de 120 ms et la bande passante plus de 5 Mo - cela est basé sur la possibilité de RDP pour ajuster dynamiquement à l’aide de la bande passante réseau disponibles et la bande passante estimée de l’application doit. Cet article va au-delà de celles « la plupart des scénarios d’utilisation » pour rechercher sur le bord, où scénarios commencent à dérouler et expérience utilisateur commence à se dégrader.

Extraire les articles suivants pour obtenir des informations, y compris des facteurs à prendre en considération, recommandations baseline, et ce que nous contenait pas dans nos estimations.

- [Comment la bande passante réseau et qualité de l’expérience travail ensemble ?](remoteapp-bandwidthexperience.md)
- [Tester votre utilisation de la bande passante réseau avec quelques scénarios courants](remoteapp-bandwidthtests.md)
- [Si vous n’avez pas l’heure ou la possibilité de tester les instructions rapides](remoteapp-bandwidthguidelines.md)


## <a name="what-are-we-not-including"></a>Ce que nous pas ajouterons ?

Lorsque vous passez en revue les tests proposées et des solutions recommandées globales (et il est vrai que génériques), n’oubliez pas qu’il existe plusieurs facteurs qui nous n’a pas compte. Par exemple, les problèmes d’expérience utilisateur fournies par la nature asymétrique de téléchargement et téléchargement la bande passante. La nature asymétrique de la plupart des réseaux Wi-Fi en outre influe sur les performances et estiment expérience utilisateur. Pour les scénarios interactifs le trafic en aval peut avoir une priorité inférieur à l’en amont, ce qui peut augmenter le nombre d’images vidéo ou audio perdues et donc un impact sur encore l’impression utilisateur de l’expérience de diffusion en continu. Vous pouvez exécuter votre propre expériences pour voir ce qui est bon pour votre cas d’utilisation spécifique et le réseau.

Bien que nous discuter la redirection de périphérique, nous n’a pas eu en considération l’impact de la bande passante du trafic réseau dû au fait des équipements reliés, comme stockage, imprimantes, scanneur, webcams et autres périphériques USB. L’effet de ces appareils dépasse les besoins de bande passante temporairement généralement et disparaît lorsque la tâche est terminée. Mais si terminé fréquemment, qui exigent la bande passante peut être très visible.

Nous ne traitent pas comment un utilisateur peut avoir un impact sur d’autres utilisateurs au sein du même réseau. Par exemple, un utilisateur par d’autres programmes vidéo 4K sur un réseau 100 Mo/s susceptibles de dégrader considérablement d’autres utilisateurs sur ce même réseau que vous essayez d’effectuer la même tâche. Il arrive malheureusement progressivement plus difficile à déterminer l’impact d’utilisation simultanée pour donner une recommandation courantes ou complète sur la façon dont le système exécute en agrégat. Tout ce que nous pouvons dire est que la technologie sous-jacente protocole sera tirer le meilleur parti de la bande passante réseau disponible, mais elle n’est pas ses limites.
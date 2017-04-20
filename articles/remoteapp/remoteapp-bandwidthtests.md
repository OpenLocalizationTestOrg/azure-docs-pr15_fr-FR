<properties 
    pageTitle="Azure RemoteApp - tester votre utilisation de la bande passante réseau avec quelques scénarios courants | Microsoft Azure"
    description="Découvrez qu’en est-il des scénarios d’utilisation courants qui peuvent vous aider à identifier vos besoins de bande passante réseau pour Azure RemoteApp."
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
    
# <a name="azure-remoteapp---testing-your-network-bandwidth-usage-with-some-common-scenarios"></a>Azure RemoteApp - tester votre utilisation de la bande passante réseau avec quelques scénarios courants

> [AZURE.IMPORTANT]
> RemoteApp Azure a été interrompu. Lire l' [annonce](https://go.microsoft.com/fwlink/?linkid=821148) pour plus d’informations.

Comme indiqué dans [l’utilisation de la bande passante réseau estimation Azure RemoteApp](remoteapp-bandwidth.md), la meilleure façon de comprendre l’impact d’Azure RemoteApp à votre réseau pour exécuter certains l’utilisation des tests. Exécuter les tests pour une période donnée et mesurer la bande passante nécessaire pour chaque scénario. Si vous avez la possibilité, vous pouvez également mesurer le paquet perte et réseau gigue de réseaux pour comprendre les modèles réseau qui seront créés dans votre environnement spécifique.

    
Lors de l’évaluation de l’utilisation de la bande passante, n’oubliez pas que l’utilisation varie entre différents utilisateurs au sein de votre société. Par exemple, les auteurs et les lecteurs de texte généralement consomment moins de bande passante que les utilisateurs qui travaillent avec la vidéo. Pour obtenir de meilleurs résultats, étudier vos besoins d’utilisateur et créer un mélange des scénarios suivants qui correspond le mieux les utilisateurs de votre entreprise. N’oubliez pas de [consulter les facteurs expérience utilisateur et l’utilisation de la bande passante impact](remoteapp-bandwidthexperience.md) - qui vous aidera à identifier les tests idéales.

Tout d’abord en savoir plus sur les tests, sélectionnez votre combinaison et avant de les exécuter. Vous pouvez utiliser le tableau ci-dessous pour faciliter le suivi des performances.

>[AZURE.NOTE] Si vous ne pouvez pas faire vos propres tests de réseau, ou vous n’avez pas le temps pour le faire, consultez nos [estimations de bande passante réseau simple/recommandations](remoteapp-bandwidthguidelines.md). Kilométrage peut varier, cependant, afin que si vous *pouvez* exécuter votre propre tests, vous ne devez.


## <a name="the-usage-tests"></a>Les tests de l’utilisation
Chacun de ces tests exécuté pour différentes quantités de temps et test des fonctions/fonctionnalités qui utilisent la bande passante réseau. N’oubliez pas de choisir la combinaison de test qui correspond le mieux à vos utilisateurs individuels.
 
### <a name="executivecomplex-powerpoint---run-for-900-1000-seconds"></a>Responsable/complexe PowerPoint - exécuter pour 900-1000 secondes

Un utilisateur présente entre les diapositives de haute fidélité de 45 à 50 à l’aide de Microsoft Office PowerPoint en mode plein écran. Les diapositives doivent contenir des images, transitions (comportant des animations) et des arrière-plans avec dégradé de couleurs qui sont courantes pour votre société. L’utilisateur doit passer au moins 20 secondes sur chaque diapositive.
    
Ce scénario crée le trafic rafales, lorsqu’une diapositive effectue une transition à la diapositive suivante dans la présentation.
    
### <a name="simple-powerpoint---run-for-620-seconds"></a>PowerPoint simple - exécuter pour ~ 620 secondes

Un utilisateur présente un fichier PowerPoint simple avec environ 30 diapositives à l’aide de Microsoft Office PowerPoint en mode plein écran. Les diapositives sont plus textuelles que dans le scénario de responsable/complexe PowerPoint et ont plus simple arrière-plans et images (diagrammes en noir). 
    
### <a name="internet-explorer---run-for-250-seconds"></a>Internet Explorer - exécuter pour ~ 250 secondes

Un utilisateur navigue sur le web à l’aide d’Internet Explorer. L’utilisateur accède, faire défiler un mélange de texte, des images et des schémas. Les pages web qui se trouve sur le lecteur de disque local du serveur hôte de Session Bureau à distance (hôte de Session RD) comme un. Fichier MHT. L’utilisateur fait défiler à l’aide de la Page vers le haut, vers le bas de Page, vers le haut et vers le bas clés, avec des intervalles différents pour chaque type de touche/de défilement :
    
    - Vers le bas - 250 frappes très 500 ms
    - Pg. préc - 36 frappes toutes les 1 000 ms
    - Bas - 75 frappes chaque 100 ms
    - Page suivante - 20 touches toutes les 500 ms
    - Haut - 120 touches chaque 300 ms
    
### <a name="pdf-document---simple---run-for-610-seconds"></a>Document PDF - simple - exécuter pour ~ 610 secondes
Un utilisateur lit et recherche dans un document PDF de diverses façons à l’aide d’Adobe Acrobat Reader. Le document doit comporter des tables, diagrammes simples et plusieurs polices de texte. Le document consiste des pages 35-40. L’utilisateur fait défiler vers l’arrière à des taux différents deux et transfère à quatre zoom différentes tailles (Ajuster à la page, ajuster à la largeur, 100 % et l’autre de votre choix). Faire le bon choix garantit que le texte (police) s’affiche dans différentes tailles. Défilement est vers le bas à l’aide de la Page précédente, Page vers le bas, vers le haut et vers le bas clés, avec des intervalles pour chaque défilement.

### <a name="pdf-document---mixed---run-for-320-seconds"></a>Exécuter - mixte - ~ 320 secondes de document PDF
Un utilisateur lit et recherche dans un document PDF de diverses façons à l’aide d’Adobe Acrobat Reader. Le document se compose des images de haute qualité (y compris les photographies), des tables, des diagrammes simples et plusieurs polices de texte. L’utilisateur fait défiler vers l’arrière à des taux différents deux et transfère à quatre zoom différentes tailles (Ajuster à la page, ajuster à la largeur, 100 % et l’autre de votre choix). Faire le bon choix garantit que le texte (police) s’affiche dans différentes tailles. Défilement est vers le bas à l’aide de la Page précédente, Page vers le bas, vers le haut et vers le bas clés, avec des intervalles pour chaque défilement.

### <a name="flash-video-playback---run-for-180-seconds"></a>Lecture de la vidéo Flash - exécuter pour ~ 180 secondes
Un utilisateur affiche une vidéo codée au format Adobe Flash incorporée dans une page web. La page web sont stockées dans le disque dur local du serveur hôte de la Session Bureau à distance. Lecture de la vidéo dans Internet Explorer par un lecteur incorporé plug-in.

Ce scénario émule utilisateurs affichant des pages web enrichies contenu contenant multimédia. La plupart des données doit bo via VOBR.

### <a name="word-remote-typing---run-for-1800-seconds"></a>Word tapant à distance - exécuter pour ~ 1800 secondes
Un utilisateur tape un document via une session RDP. Combinaisons de touches sont envoyés à partir du client via la session RDP à un document dans Microsoft Word en cours d’exécution dans la session distante. Le taux de frappe est un caractère chaque ms 250 (total 7050 caractères). 

Il s’agit d’un des scénarios plus courants pour un utilisateur expérimenté. Ce scénario teste la réactivité d’un utilisateur à taper dans un traitement de travail moderne. Ce scénario est sensible à la même les modifications mineures dans la bande passante.

## <a name="tracking-the-test-results"></a>Les résultats des tests de suivi

Vous pouvez utiliser le tableau suivant pour évaluer les scénarios dans votre environnement. Les données fournies ci-dessous sont seulement les illustration : il peut être très différent de ce que vous observez. 

Pour simplifier, nous part du principe que tous les scénarios sont testés à l’aide de la même résolution d’écran de 1920 x 1080 pixels et transports TCP sur un réseau avec une latence (retard) en dessous de 200 ms et réseau gigue dans la marque de 120 ms + d’environ 1 %.

À propos de la table :
- **Expérience moyenne** contient la bande passante réseau où la productivité des utilisateurs n’est pas considérablement affectée mais pas occasionnels problèmes audio ou vidéo. Le système est en mesure de récupérer rapidement en tirant parti de la logique dynamique. La tentative d’estimations de bande passante réseau afin de garantir la qualité de l’expérience utilisateur.
 - **Noticeable problèmes (point d’arrêt)** contient la bande passante réseau où les utilisateurs peuvent remarquer problèmes significatives dans leur expérience et leur productivité varient pour les périodes de temps mesurables. À ce stade les algorithmes RDP ont du mal et ne peut pas garantir la qualité de l’utilisateur de l’expérience en raison de bande passante insuffisante.
 - **Recommandé** contient la bande passante réseau recommandée pour une expérience de bonne ou excellente. Il s’agit généralement une étape supérieure à la valeur dans la colonne correspondante **moyenne rencontrer** .
 - **Notes** inclure des observations et commentaires.
 
| Test                  | Expérience moyenne | Problèmes visibles (point d’arrêt) | Bande passante réseau recommandés | Notes                                                              |
|-----------------------|--------------------|---------------------------------|-------------------------------|--------------------------------------------------------------------|
| Responsable/complexe dans PowerPoint | 10 Mo/s             | 1 Mo/s                           | > 10 Mo/s, 100 Mo/s préféré    | À 1 Mo/s d’animations sont perdues                                   |
| PPT simple            | 5 Mo/s              | 256 Ko/s                         | 10 Mo/s                        | À 256 KB/s les diapositives chargement avec retard                   |
| Internet Explorer     | 10 Mo/s             | 1 Mo/s                           | > 10 Mo/s, 100 Mo/s préféré    | Au 1 Mo/s vidéos web sont floues et vidéo hachée, rapide défilement rencontre des problèmes |
| PDF simple            | 1 Mo/s              | 256 Ko/s                         | 5 Mo/s                         | À 256 KB/s prend un certain temps de chargement de la page                       |
| PDF mixte             | 1 Mo/s             | 256 Ko/s                         | 5 Mo/s                         | À 256 KB/s la page prend beaucoup de temps de chargement    |
| Instantané de lecture vidéo  | 10 Mo/s             | 1 Mo/s                           | > 10 Mo/s, 100 Mo/s préféré    | À 1 Mo/s la vidéo est texture et certains cadres sont abandonnées           |
| Word tapant à distance    | 256 Ko/s            | 128 Ko/s                         | 1 Mo/s                         | À 256 KB/s utilisateur peut remarquer l’intervalle entre les séquences de touches             |

Pour évaluer la bande passante par utilisateur, créez un mélange des scénarios ci-dessus et la proportion correspondante de bande passante réseau requise. Sélectionnez le nombre le plus élevé nécessaire pour vos scénarios. Dans la mesure où les utilisateurs utilisent pratiquement jamais le seul système, pensez à quelques réservation pour les utilisateurs qui travaillent simultanément sur le même réseau.
     
## <a name="learn-more"></a>Pour en savoir plus
- [Utilisation de la bande passante réseau Azure RemoteApp d’estimation](remoteapp-bandwidth.md)

- [RemoteApp Azure - comment la bande passante réseau et qualité de l’expérience travail ensemble ?](remoteapp-bandwidthexperience.md)

- [Azure RemoteApp la bande passante réseau - instructions générales (si vous ne pouvez pas tester vos propres)](remoteapp-bandwidthguidelines.md)
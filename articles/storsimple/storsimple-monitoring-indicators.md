<properties 
    pageTitle="Indicateurs de suivi des StorSimple | Microsoft Azure" 
    description="Décrit les voyants (LED) et alarmes sonores utilisés pour surveiller l’état de l’appareil StorSimple."
    services="storsimple"
    documentationCenter="NA"
    authors="alkohli"
    manager="carmonm"
    editor="" />
 <tags 
    ms.service="storsimple"
    ms.devlang="NA"
    ms.topic="article"
    ms.tgt_pltfrm="NA"
    ms.workload="TBD"
    ms.date="08/18/2016"
    ms.author="alkohli" />

# <a name="use-storsimple-monitoring-indicators-to-manage-your-device"></a>StorSimple indicateurs de suivi permet de gérer votre appareil   

## <a name="overview"></a>Vue d’ensemble

Votre appareil StorSimple inclut voyants (LED) et alarmes que vous pouvez utiliser pour contrôler les modules et le statut général de l’appareil StorSimple. Les indicateurs de suivi sont accessibles sous les composants matériels d’encadrement principal de l’appareil et du boîtier EBOD. Les indicateurs de suivi peuvent être voyants ou alarmes sonores.

Il existe trois états LED utilisées pour indiquer l’état d’un module : vert, vert, rouge-orange ou rouge-orange et clignote.  

- Vert voyants représentent un état d’exploitation correct.  
- Vert pour représenter les voyants rouge-orange et clignote la présence de conditions non critiques qui peut nécessiter l’intervention de l’utilisateur.  
- Voyants orange et rouge indiquent qu’il existe une défaillance critique présente dans le module.  

Le reste de cet article décrit les différents voyants surveillance, leur emplacement sur le périphérique StorSimple, l’état de l’appareil basée sur les États LED et les alarmes sonores associés.

## <a name="front-panel-indicator-leds"></a>Voyants du panneau avant

Le panneau avant, également connu sous le *Panneau opérations* ou le *Panneau de configuration opérations*, affiche l’état agrégation de tous les modules dans le système. Le panneau avant est identique sur le StorSimple principal et le boîtier EBOD et illustré ci-dessous.  

   ![Panneau avant appareil][1]
 
Le panneau avant contient les indicateurs suivants :  

1. Bouton Muet
2. Indicateur de Power LED (vert, rouge-orange)
3. Indicateur d’erreur de module conduit (sur rouge-orange/désactivé)
4. Créé par un indicateur d’erreur logiques (sur rouge-orange/désactivé
5. Affichage de l’identifiant unité  

La seule différence entre le panneau avant voyants pour l’appareil et celles de l’encadrement EBOD est le **Numéro d’Identification système unité** figurant sur le panneau d’affichage LED. L’ID d’unité par défaut affiché sur le périphérique est **00**, tandis que l’ID d’unité par défaut affiché sur le boîtier EBOD est **01**. Cela vous permet de distinguer rapidement l’appareil et du boîtier EBOD lorsque l’appareil est activé. Si votre appareil est désactivé, utilisez les informations fournies dans [Activer un nouvel appareil](storsimple-turn-device-on-or-off.md#turn-on-a-new-device) de différencier le périphérique de l’encadrement EBOD.  

## <a name="front-panel-led-status"></a>État des voyants du panneau avant  

Utilisez le tableau suivant pour identifier le statut indiqué par les voyants sur le panneau avant de l’appareil ou le boîtier EBOD.  

|Puissance du système | Erreur de module | Erreur de logique | Alarme | État|
|-------------|---------------|-----------------|-------|-------|
|Rouge-orange | DÉSACTIVER     | DÉSACTIVER | N/A | Alimentation perdu, fonctionne sur power sauvegarde ou alimentation sur et le contrôleur de modules ont été supprimés.|
|Vert | ACTIVÉ | ACTIVÉ | N/A | État de test opérations Panneau de configuration sous tension 5 (s)|
|Vert | DÉSACTIVER | DÉSACTIVER | N/A | Sous tension, toutes les fonctions bonne|
|Vert | ACTIVÉ |N/A | PCM voyants, voyants de défaillance ventilateurs | PCM faute, défaillance ventilateurs, au-dessus ou au-dessous de température|
| Vert | ACTIVÉ | N/A | Module e/s voyants  | Faute module contrôleur|
| Vert | ACTIVÉ | N/A | N/A | Erreur de logique encadrement|
| Vert | Flash | N/A | Statut du module LED sur module contrôleur. PCM voyants, voyants de défaillance ventilateurs | Type de module contrôleur inconnu installé, défaillances du bus I2C, erreur de configuration de données (VPD) contrôleur module produit indispensables |

## <a name="power-cooling-module-pcm-indicator-leds"></a>Power refroidissement voyants de module (PCM)   

Vous trouverez Power refroidissement module (PCM) voyants à l’arrière d’un boîtier principal ou encadrement EBOD sur chaque module PCM. Cette rubrique explique comment utiliser les voyants suivants pour surveiller l’état de votre périphérique StorSimple.  

- Voyants PCM pour le boîtier principal
- Voyants PCM pour le boîtier EBOD

## <a name="pcm-leds-for-the-primary-enclosure"></a>Voyants PCM pour le boîtier principal  

Le périphérique StorSimple a un module PCM 764W avec une batterie supplémentaire. L’illustration suivante montre le panneau LED du périphérique.  

   ![Voyants PCM sur le boîtier principal][2]

Légende LED :

1. Coupure de courant CA
2. Échec de ventilateurs
3. Erreur de batterie
4. OK PCM
5. Échec du contrôleur de domaine
6. Bonne batterie  

L’état de la PCM est indiquée dans le panneau des voyants. Le panneau de PCM LED du périphérique a six voyants. Quatre de ces voyants afficher l’état de l’alimentation et les ventilateurs. Les deux voyants restants indiquent l’état du module batterie de sauvegarde dans la PCM. Vous pouvez utiliser les tableaux suivants pour déterminer l’état de la PCM.  

### <a name="pcm-indicator-leds-for-power-supply-and-fan"></a>PCM voyants d’alimentation des ventilateurs
| État | PCM OK (vert) | Fail AC (orange) | Fail ventilateurs (orange) | Fail DC (orange) |
|--------|----------------|-----------------------|------------------|----------------------|
| Aucune alimentation (boîtier) | DÉSACTIVER | DÉSACTIVER | DÉSACTIVER | DÉSACTIVER|
| Aucune alimentation (cette PCM uniquement) | DÉSACTIVER | ACTIVÉ | DÉSACTIVER | ACTIVÉ |
| AC présenter activé PCM - OK     | ACTIVÉ | DÉSACTIVER | DÉSACTIVER | DÉSACTIVER |
| Fail PCM (ventilateurs fail) | DÉSACTIVER | DÉSACTIVER | ACTIVÉ | N/A |
| Défaillance PCM (sur a, sur tension, sur en cours) | DÉSACTIVER | ACTIVÉ | ACTIVÉ | ACTIVÉ |
| PCM (ventilateurs déconnecter tolérance) | ACTIVÉ | DÉSACTIVER | DÉSACTIVER | ACTIVÉ |
| Mode veille | Signaux | DÉSACTIVER | DÉSACTIVER | DÉSACTIVER |
| Téléchargement du microprogramme PCM | DÉSACTIVER | Signaux | Signaux | Signaux |

### <a name="pcm-indicator-leds-for-the-backup-battery"></a>PCM voyants pour la batterie de secours  

| État | Bonne batterie (vert) | Erreur de batterie (orange) |
|--------|----------------------|-----------------------|
| N’existe pas de batterie | DÉSACTIVER | DÉSACTIVER |
| Batterie présenter et imputé | ACTIVÉ | DÉSACTIVER |
| Rejet de chargement ou de maintenance de batterie | Signaux | DÉSACTIVER |
| Erreur « logiciel » de batterie (récupérable) | DÉSACTIVER | Signaux |
| Erreur « dur » de batterie (non récupérables) | DÉSACTIVER | ACTIVÉ |
| Batterie disarmed | Signaux | DÉSACTIVER |

## <a name="pcm-leds-for-the-ebod-enclosure"></a>Voyants PCM pour le boîtier EBOD  

Le boîtier EBOD a un PCM 580 w et aucune batterie supplémentaire. Le panneau de configuration PCM pour le boîtier EBOD a voyants uniquement pour les blocs d’alimentation et les ventilateurs. L’illustration suivante montre ces voyants.

   ![Voyants PCM sur le boîtier EBOD][3] 
 
Vous pouvez utiliser le tableau suivant pour déterminer l’état de la PCM.  

| État | PCM OK (vert) | Fail AC (orange) | Fail ventilateurs (orange) | Fail DC (orange) |
|--------|---------------|------------------------|------------------|----------------------|
| Aucune alimentation (boîtier) | DÉSACTIVER | DÉSACTIVER | DÉSACTIVER | DÉSACTIVER |
| Aucune alimentation (cette PCM uniquement) | DÉSACTIVER | ACTIVÉ | DÉSACTIVER | ACTIVÉ |
| AC présenter PCM activé – OK | ACTIVÉ | DÉSACTIVER | DÉSACTIVER | DÉSACTIVER |
| Fail PCM (ventilateurs fail) | DÉSACTIVER | DÉSACTIVER | ACTIVÉ | X |
| Défaillance PCM (sur a, sur tension, sur en cours | DÉSACTIVER | ACTIVÉ | ACTIVÉ | ACTIVÉ |
| PCM (ventilateurs déconnecter tolérance) | ACTIVÉ | DÉSACTIVER | DÉSACTIVER | ACTIVÉ |
| Modèle de secours | Signaux | DÉSACTIVER | DÉSACTIVER | DÉSACTIVER |
| Téléchargement du microprogramme PCM | DÉSACTIVER | Signaux | Signaux | Signaux |

## <a name="controller-module-indicator-leds"></a>Contrôleur module voyants  

Le périphérique StorSimple contient voyants pour le contrôleur principal et les modules contrôleur EBOD.   

### <a name="monitoring-leds-for-the-primary-controller"></a>Surveillance voyants pour le contrôleur principal
L’illustration suivante vous permet d’identifier les voyants sur le contrôleur principal. (Tous les composants sont répertoriés afin de faciliter l’orientation).  

   ![Surveillance voyants - contrôleur principal][4]
 
Utilisez le tableau suivant pour déterminer si le module contrôleur fonctionne correctement.  

### <a name="controller-indicator-leds"></a>Indicateur de contrôleur voyants  

| LED | Description                                                                            
|---- | ----------- |
| ID LED (bleu) | Indique que le module est identifié. Si le bleu LED clignote sur un contrôleur en cours d’exécution, puis le contrôleur est le contrôleur actif et l’autre est le contrôleur de secours. Pour plus d’informations, voir [identifier le contrôleur actif sur votre appareil](storsimple-controller-replacement.md#identify-the-active-controller-on-your-device). |
| Défaillance LED (orange) | Indique une erreur dans le contrôleur.        
| LED OK (vert) | Vert fixe indique que le contrôleur est OK. Signaux vert indique un erreur de configuration VPD contrôleur. |
| Activité de sa voyants (vert) | Vert fixe indique une connexion sans activité actuelle. Signaux vert indique la connexion comporte des activités en cours. |
| Voyants d’état Ethernet | Droite indique l’activité de lien/réseau : (vert et fixe) lien actif, (vert et clignote) activité réseau. Gauche indique la vitesse du réseau : (jaune) 1000 Mo/s, 100 Mo/s (vert) et (désactivé) 10 Mo/s. Selon le modèle de composant, cette lumière peut clignoter même si l’interface réseau n’est pas activée. |
| Voyants de billet | Indique la progression de démarrage lorsque celui-ci est activé. Si le périphérique StorSimple ne démarre pas, cette LED aidera Support Microsoft à identifier le point dans le processus de démarrage à laquelle l’erreur s’est produite. |

>[AZURE.IMPORTANT] 
Si l’erreur LED est allumé, présente un problème avec le module contrôleur qui peut être résolu en redémarrant le contrôleur. Contactez le Support Microsoft si le redémarrage le contrôleur ne résout pas ce problème.  


### <a name="monitoring-leds-for-the-ebod-ebod-enclosure"></a>Surveillance voyants pour la EBOD (encadrement EBOD)  

Chaque contrôleur Go/s SAS EBOD 6 a voyants qui indiquent son statut comme indiqué dans l’illustration suivante.  

  ![Surveillance voyants - boîtier EBOD][5]

Utilisez le tableau suivant pour déterminer si le module de contrôleur EBOD fonctionne normalement.  

### <a name="ebod-controller-module-indicator-leds"></a>Indicateur du module EBOD contrôleur voyants  

|État | Module e/s OK (vert) | Erreur de module e/s (orange) | Activité de port hôte (vert) |
|-------|----------------------|-------------------------------|----------------------------|
| Module contrôleur OK | ACTIVÉ | DÉSACTIVER | - |
| Erreur de module contrôleur | DÉSACTIVER | ACTIVÉ | - |
| Aucune connexion au port hôte externe | - | - | DÉSACTIVER |
| Connexion au port hôte externe – aucune activité | - | - | ACTIVÉ |
| Connexion au port hôte externe - activité | - | - | Signaux |
| Erreur du module contrôleur de métadonnées | Signaux | - | - |

## <a name="disk-drive-indicator-leds-for-the-primary-enclosure-and-ebod-enclosure"></a>Lecteur de disque voyants pour le boîtier principal et un boîtier EBOD

Le périphérique StorSimple a disques situés dans le boîtier principal et le boîtier EBOD. Chaque lecteur de disque contient surveillance voyants, comme décrit dans cette section. 

Pour les lecteurs de disques, l’état du lecteur est indiqué par un vert voyants et un rouge-orange monter à l’avant de chaque module carrier lecteur. L’illustration suivante montre ces voyants.

  ![Voyants du lecteur de disque][6]
 
Utilisez le tableau suivant pour déterminer l’état de chaque lecteur de disque, qui à son tour affecte le panneau global avant voyants.  

### <a name="disk-drive-indicator-leds-for-the-ebod-enclosure"></a>Lecteur de disque voyants pour le boîtier EBOD  

| État | Activité OK LED (vert) | Défaillance LED (rouge-orange) | Associé à panneau de configuration opérations LED |
|-------|--------------------------|----------------------|-------------------------|
| Aucun lecteur installé | DÉSACTIVER | DÉSACTIVER | Aucun |
| Lecteur installé et opérationnel | Signaux/désactivation avec une activité | X | Aucun |
| Ensemble d’identité périphérique SCSI encadrement Services (SES) | ACTIVÉ | Signaux 1 seconde/1 deuxième désactiver | Aucun |
| Jeu de SES appareil pannes bit | ACTIVÉ | ACTIVÉ | Défaillance logique (rouge) |
| Coupure de circuit de contrôle | DÉSACTIVER | ACTIVÉ | Erreur de module (rouge) |

## <a name="audible-alarms"></a>Alarmes sonores  

Un périphérique StorSimple contient alarmes sonores associés à l’encadrement principale et le boîtier EBOD. Une alarme sonore se trouve sur le panneau avant (également connu sous le panneau de configuration opérations) de ces deux pièces jointes. L’alarme sonore indique lorsqu’une condition d’erreur est présente. Les conditions suivantes activera l’alarme :  

- Défaillance ventilateurs ou défaillance
- Alimentation hors plage
- Au-dessus ou au-dessous de condition température
- Saturation thermique
- Pannes système
- Erreur de logique
- Défaillance de l’alimentation
- Suppression d’une puissance module de (PCM)  

Le tableau suivant décrit les différents États d’alarme.  

### <a name="alarm-states"></a>États d’alarme  

| État de l’alarme | Action | Action avec bouton Muet |
|------------|---------|---------------------------------|
| S0 | En mode normal : en mode silencieux | Émettre un signal sonore à deux reprises |
| S1 | Mode pannes : 1 seconde/1 deuxième désactiver | Transition vers S2 ou S3 (voir notes) |
| S2 | Rappeler mode : intermittent sonore | Aucun |
| S3 | Mode désactivé : en mode silencieux | Aucun |
| S4 | Mode de défaillance critique : alarme continue | Non disponible : muet pas active |

> [AZURE.NOTE] 

>  - Dans l’état d’alarme S1, si vous n’appuyez sur Activer en 2 minutes, l’état est automatiquement transitions S2 ou S3.  
>  - Alarme états S1 S4 revenir à S0 après que la condition d’erreur est désactivée.  
>  - État de défaillance critique S4 peut être entré à partir de n’importe quel autre état.  

Vous pouvez désactiver le son de l’alarme sonore en appuyant sur le bouton Muet sur le panneau d’opérations. Maîtriser automatique se produit après deux minutes si le commutateur muet n’est pas activé manuellement. Lorsque l’alarme est coupé, il continuent à son avec signaux sonores intermittents courts pour indiquer qu’il existe toujours un problème. L’alarme seront en mode silencieux lors de tous les problèmes sont désactivées.  

Le tableau suivant décrit les différentes conditions d’alarme.  

### <a name="alarm-conditions"></a>Conditions d’alarme  

| État | Gravité | Alarme | Opérations du panneau LED |
|--------|---------|--------|----------------|
| Alerte PCM – coupure de courant contrôleur de domaine à partir d’un seul PCM | Défaillance – aucune perte de redondance | S1 | Erreur de module|
|Alerte PCM – coupure de courant contrôleur de domaine à partir d’un seul PCM | Défaillance – perte de redondance | S1 | Erreur de module |
| Échec de ventilateurs PCM | Défaillance – perte de redondance | S1 | Erreur de module |
| Module SB détecté défaillance PCM | Défaillance | S1 | Erreur de module |
| PCM supprimé | Erreur de configuration | Aucun | Erreur de module |
| Erreur de configuration du boîtier | Défaillance – critique | S1 | Erreur de module |
| Alerte de température basse avertissement | Avertissement | S1 | Erreur de module |
| Alerte de température haute avertissement | Avertissement | S1 | Erreur de module |
| Sur alarme température | Défaillance – critique | S1 | Erreur de module |
| Échec de bus I2C | Défaillance – perte de redondance | S1 | Erreur de module |
| Opérations du panneau erreur de communication (I2C) | Défaillance – critique     | S1 | Erreur de module |
| Erreur du contrôleur | Défaillance – critique | S1 | Erreur de module |
| Erreur de module SB interface | Défaillance – critique | S1 | Erreur de module |
| Défaillance du module SB interface – aucun module fonctionne restant | Défaillance – critique | S4 | Erreur de module |
| Module d’interface SB supprimé | Avertissement | Aucun | Erreur de module |
| Lecteur power contrôle défaillance | Avertissement – aucune perte de l’alimentation du disque | S1 | Erreur de module |
| Lecteur power contrôle défaillance | Défaillance – critique ; perte de l’alimentation du disque | S1 | Erreur de module |
| Lecteur supprimé | Avertissement | Aucun | Erreur de module |
| Puissance insuffisante disponible | Avertissement | Aucun | Erreur de module |

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur [l’état et composants matériels StorSimple](storsimple-monitor-hardware-status.md).

[1]: ./media/storsimple-monitoring-indicators/storsimple-monitoring-indicators-IMAGE01.png
[2]: ./media/storsimple-monitoring-indicators/storsimple-monitoring-indicators-IMAGE02.png
[3]: ./media/storsimple-monitoring-indicators/storsimple-monitoring-indicators-IMAGE03.png
[4]: ./media/storsimple-monitoring-indicators/storsimple-monitoring-indicators-IMAGE04.png
[5]: ./media/storsimple-monitoring-indicators/storsimple-monitoring-indicators-IMAGE05.png
[6]: ./media/storsimple-monitoring-indicators/storsimple-monitoring-indicators-IMAGE06.png

 

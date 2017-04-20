<properties
   pageTitle="Installez votre périphérique 8100 StorSimple | Microsoft Azure"
   description="Décrit comment décompresser, montage en rack et câble votre appareil 8100 StorSimple avant de déployer et configurer le logiciel."
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
   ms.date="08/17/2016"
   ms.author="alkohli" />

# <a name="unpack-rack-mount-and-cable-your-storsimple-8100-device"></a>Décompressez, montage en rack et un câble votre appareil StorSimple 8100

## <a name="overview"></a>Vue d’ensemble

Votre 8100 Microsoft Azure StorSimple est un appareil racks boîtier unique. Ce didacticiel explique comment décompresser, montage en rack et périphérique câble le8100 StorSimple avant de configurer et déployer le périphérique StorSimple.

## <a name="unpack-your-storsimple-8100-device"></a>Décompresser le périphérique StorSimple 8100

Les étapes suivantes fournissent des instructions claires et détaillées comment décompresser votre périphérique de stockage StorSimple 8100. Cet appareil est fourni dans une seule boîte.

### <a name="prepare-to-unpack-your-device"></a>Préparer la décompresser votre appareil

Avant de décompresser votre appareil, passez en revue les informations suivantes.

![Icône d’avertissement](./media/storsimple-safety/IC740879.png)![icône lourds](./media/storsimple-8100-hardware-installation/HCS_HeavyWeight_Icon.png) **Avertissement !**

1. Vérifiez que vous disposez de deux personnes disponibles pour gérer l’épaisseur du boîtier si vous gestion des manuellement. Un boîtier entièrement configuré peut évaluer jusqu'à 32 kg (70 kg.).
1. Placez la zone sur une surface plane et niveau.

Ensuite, procédez comme suit pour décompresser votre appareil.

#### <a name="to-unpack-your-device"></a>Pour décompresser votre appareil

1. Vérifiez que la zone et la mousse emballage pour crushes, morceaux, l’eau ou tout autre dommage évidente. Si la zone ou l’emballage est fortement endommagé, ne s’ouvrent pas la zone. Veuillez [contacter le support technique Microsoft](storsimple-contact-microsoft-support.md) pour vous aider à déterminer si le périphérique est bon fonctionnement.

2. Décompresser la zone. L’image suivante montre l’affichage de votre appareil StorSimple décompressé.

     ![Décompresser le périphérique de stockage](./media/storsimple-8100-hardware-installation/HCSUnpackyour2Udevice.png)

    **Affichage décompressé de votre périphérique de stockage**

     Étiquette | Description
     ----- | -------------
     1     | Boîte d’emballage
     2     | MOUSSE bas
     3     | APPAREIL
     4     | MOUSSE supérieure
     5     | Zone LAP


3. Après avoir décompressé la zone, vérifiez que vous avez :

   - 1 périphérique boîtier unique
   - 2 câbles d’alimentation
   - câble Ethernet 1 croisé
   - 2 câbles console série
   - 1 convertisseur série USB pour l’accès en série
   - 1 tournevis de T10 falsification
   - QSFP 4-à-SFP + cartes pour une utilisation avec 10 interfaces de réseau de solution
   - montage en rack 1 kit (2 rails côté avec matériel de montage)
   - Prise en main documentation

    Si vous n’avez pas reçu un des éléments ci-dessus, [contactez le support technique Microsoft](storsimple-contact-microsoft-support.md).

L’étape suivante consiste à votre appareil de montage en rack.

## <a name="rack-mount-your-storsimple-8100-device"></a>Votre appareil 8100 StorSimple de montage en rack

Suivez la procédure suivante pour installer votre dispositif de stockage StorSimple 8100 en rack 19 pouces standard avec avant et arrière billets. Le périphérique 8100 StorSimple a un seul boîtier principal.

L’installation se compose de plusieurs étapes, chacun d'entre eux est décrit dans les procédures suivantes.

> [AZURE.IMPORTANT]
Appareils StorSimple doivent être racks pour fonctionner correctement.

### <a name="prepare-the-site"></a>Préparer le site

Le périphérique doit être installé dans un rack 19 pouces standard qui a avant et arrière billets. Utilisez la procédure suivante pour préparer l’installation en rack.

#### <a name="to-prepare-the-site-for-rack-installation"></a>Pour préparer le site montage en rack

1. Assurez-vous que l’appareil est placé en toute sécurité sur un travail plat, stable et niveau surface (ou similaire).

2. Vérifiez que le site où vous souhaitez configurer a alimentation standard à partir d’une source indépendante ou une rack unité alimentation () avec une alimentation (sans coupure).

3. Vérifiez que cet emplacement 2U une est disponible sur le rack dans lequel vous souhaitez monter le périphérique.

![Icône d’avertissement](./media/storsimple-safety/IC740879.png)![icône lourds](./media/storsimple-8100-hardware-installation/HCS_HeavyWeight_Icon.png) **Avertissement !**

Vérifiez que vous disposez de deux personnes disponibles pour gérer l’épaisseur si vous gérez l’installation du périphérique manuellement. Un boîtier entièrement configuré peut évaluer jusqu'à 32 kg (70 kg.).

### <a name="rack-prerequisites"></a>Conditions préalables en rack

Le boîtier 8100 est conçu pour l’installation en rack 19 pouces standard armoire avec :

- Profondeur minimale de pouces 27.84 rack valider pour enregistrer.
- Poids maximal de 32 kg pour l’appareil
- Pression précédent maximale de 5 Pascal (colonne d’eau 0,5 mm).

### <a name="rack-mounting-rail-kit"></a>Kit de rails de montage en rack

Un ensemble de rails de montage est fourni pour une utilisation avec le rack 19 pouces. Les rails ont été testés pour gérer l’épaisseur d’encadrement maximale. Ces rails permet également d’installation de plusieurs pièces jointes sans aucune perte d’espace dans le rack.


#### <a name="to-install-the-device-on-the-rails"></a>Pour installer le périphérique sur les rails

2. Exécuter cette étape uniquement si rails internes ne sont pas installés sur votre appareil. En règle générale, les rails internes sont installés en usine. Si rails ne sont pas installés, puis installez les diapositives rail gauche et droite sur les côtés du châssis du boîtier. Ils joindre à l’aide de six vis métriques de chaque côté. Pour faciliter l’orientation, les diapositives rail sont signalés **LH – avant** et **RH – premier plan**, et la fin est apposée vers l’arrière du boîtier dispose une extrémité conique.<br/>

    ![Attacher des diapositives rail à châssis boîtier](./media/storsimple-8100-hardware-installation/HCSAttachingRailSlidestoEnclosureChassis.png)
    **diapositives de rail interne attachement sur les côtés de l’encadrement**

       Étiquette | Description
       ----- | -----------
       1     | M 3 x 4 vis bouton
       2     | Diapositives châssis

3. Joindre l’externe rail gauche et assemblys externe droite aux membres verticale armoire rack. Les crochets sont signalés **LH**, **RH**et **cette face vers le haut** pour mener à bien l’orientation appropriée.

4. Recherchez les chevilles rail à l’avant et arrière de l’ensemble de rails. Le rail pour tienne entre les publications rack et insérez les chevilles en avant et arrière billet membre verticale holes. N’oubliez pas que l’ensemble de rails est niveau.

5. Utiliser deux vis métriques fournies pour fixer le rail au rack membres verticales. Utilisez une vis de premier plan et l’autre à l’arrière.

6. Répétez ces étapes pour l’autre assemblage rail.<br/>

     ![Joindre des diapositives rail dans rack](./media/storsimple-8100-hardware-installation/HCSAttachingRailSlidestoRackCabinet.png)

    **Insertion d’assemblys rail externe dans le rack**

     Étiquette | Description
     ----- | -----------
     1     | Verrouillage vis
     2     | Rack avant carrée trou billet vis
     3     | Codes confidentiels emplacement avant de rail gauche
     4     | Verrouillage vis
     5     | Codes confidentiels emplacement arrière rail gauche


### <a name="mounting-the-device-in-the-rack"></a>Montage du périphérique dans le rack

À l’aide de rails du rack viennent d’être installés, procédez comme suit pour monter le périphérique dans le rack.

#### <a name="to-mount-the-device"></a>Pour monter le périphérique

1. Avec un assistant, lever le boîtier et alignez-le sur les rails de montage en rack.

2. Insérez avec soin le périphérique dans les rails et puis appuyez sur le périphérique complètement dans le rack armoire.<br/>

    ![Dispositif d’insertion dans le rack](./media/storsimple-8100-hardware-installation/HCSInsertingDeviceintheRack.png)

    **Montage du périphérique dans le rack**


3. Supprimer le texte en majuscules bord gauche et droite en tirant sur le texte en majuscules gratuit. Le texte en majuscules bord simplement aligne sur les brides.

5. Sécurisez le boîtier en rack en installant une vis cruciforme fournie par le biais chaque bord, gauche et droite.

4. Installer le texte en majuscules bord en appuyant sur les en position et l’alignement les en place.<br/>

     ![Verr. MAJ bord l’installation](./media/storsimple-8100-hardware-installation/HCSInstallingFlangeCaps.png)

    **Installer le texte en majuscules de bord**

     Étiquette | Description
     ----- | -----------
     1     | Vis d’ancrage encadrement

L’étape suivante consiste pour connecter votre périphérique d’alimentation, réseau et accès série.

## <a name="cable-your-storsimple-8100-device"></a>Câble votre appareil StorSimple 8100

Les procédures suivantes expliquent comment faire pour connecter votre périphérique 8100 StorSimple d’alimentation, réseau et connexions série.

### <a name="prerequisites"></a>Conditions préalables

Avant de commencer le câblage de votre appareil, vous devez :

- Votre périphérique de stockage, complètement décompressé et en rack.

- 2 câbles power fourni avec votre appareil

- Accès aux 2 unités de Distribution Power (recommandé).

- Câbles réseau

- Fourni câbles série

- Convertisseur USB en série avec le pilote approprié est installé sur votre PC (si nécessaire)

- Fourni QSFP 4-à-SFP + cartes pour une utilisation avec 10 interfaces de réseau de solution

- [Matériel pris en charge pour les interfaces réseau solution 10 sur votre appareil StorSimple](storsimple-supported-hardware-for-10-gbe-network-interfaces.md)


### <a name="power-cabling"></a>Câblage Power

Votre appareil comprend redondants Power et Modules ventilation (PCM pour). Deux PCMs doivent être installées et connectées à plusieurs sources d’alimentation pour optimiser la disponibilité.

Effectuez les opérations suivantes pour votre appareil pour power du câble.

[AZURE.INCLUDE [storsimple-cable-8100-for-power](../../includes/storsimple-cable-8100-for-power.md)]

### <a name="network-cabling"></a>Câblage réseau

Votre périphérique est une configuration active-de réserve : à un moment donné, un module contrôleur est actif, toutes les opérations de disque et de réseau lors de l’autre module contrôleur de traitement en veille. Si un contrôleur échoue, le contrôleur de secours est activé immédiatement et continue tout le disque et les opérations de réseau.

Pour prendre en charge ce basculement contrôleur redondantes, vous devez câble réseau de votre appareil, comme décrit dans les étapes suivantes.

#### <a name="to-cable-for-network-connection"></a>Câble de connexion réseau

1. Votre appareil dispose d’interfaces réseau six sur chaque contrôleur : quatre 1 Go/s, et les ports de deux 10 Go/s Ethernet. Identifier les ports de données diverses sur fond de panier de votre appareil.

    ![Carte d’insertion d’un périphérique 8100](./media/storsimple-8100-hardware-installation/HCSBackplaneof2UDevicewithPortsLabeled.jpg)

    **Retour du périphérique affichant les ports de données**

     Étiquette   | Description
     ------- | -----------
     0,1,4,5 |  Interfaces de réseau solution 1
     2, 3     | 10 interfaces de réseau de solution
     6       | Ports série

2. Afficher le diagramme suivant pour câblage réseau. (La configuration réseau minimale est indiquée par traits pleins bleus. Configuration supplémentaire requise pour les performances et la disponibilité est indiquée par des lignes pointillées.)


    ![Câble votre appareil 2U réseau](./media/storsimple-8100-hardware-installation/HCSCableYour2UDeviceforNetwork.png)

    **Réseau de votre appareil**


  	|Étiquette | Description |
  	|----- | ----------- |
  	| A    | Réseau local avec accès à Internet |
  	| B    | Contrôleur de 0 |
  	| C    | PCM 0 |
  	| D    | Contrôleur de 1 |
  	| E    | PCM 1 |
  	| F, G | Hosts |
  	| 0-5  | Interfaces réseau |



Lorsque l’appareil, de câblage requiert la configuration minimale requise :


- Au moins deux interfaces réseau connecté sur chaque contrôleur avec une pour l’accès au cloud et l’autre pour iSCSI. Les données 0 port est automatiquement activé et configuré via la console série du périphérique. En dehors de données 0, un autre port de données doit également être configurés à partir du portail classique Azure. Dans ce cas, connecter des données 0 port pour le réseau principal (réseau avec accès à Internet). Les autres ports de données pouvant être connectés au segment SAN/iSCSI LAN (réseau local virtuel) du réseau, en fonction du rôle initial.

- Interfaces identiques sur chaque contrôleur connecté au réseau même pour garantir la disponibilité si un basculement contrôleur se produit. Par exemple, si vous choisissez de connecter les données 0 et 3 une le contrôleur de données, vous devez connecter les données correspondantes 0 et données 3 sur l’autre contrôleur.

N’oubliez pas de disponibilité et les performances :


- Si possible, configurez une paire de l’interface de réseau pour l’accès au cloud (1 solution) et une autre paire pour iSCSI (10 GbE recommandé) sur chaque contrôleur.

- Si possible, connectez-vous interfaces réseau à partir de chaque contrôleur à deux commutateurs différents pour assurer la disponibilité par rapport à une défaillance du commutateur. L’illustration montre les deux 10 interfaces réseau solution, données 2 et 3 de données, à partir de chaque contrôleur connectée à deux commutateurs différents.

Pour plus d’informations, consultez les **interfaces réseau** selon les [besoins de disponibilité pour votre appareil StorSimple](storsimple-system-requirements.md#high-availability-requirements-for-storsimple).

>[AZURE.NOTE] Si vous utilisez SFP + s’avec 10 interfaces réseau solution, utilisez la QSFP fournie-SFP + cartes. Pour plus d’informations, accédez au [matériel pris en charge pour les interfaces réseau solution 10 sur votre appareil StorSimple](storsimple-supported-hardware-for-10-gbe-network-interfaces.md).



### <a name="serial-port-cabling"></a>Port série câblage

Effectuez les opérations suivantes pour les ports série du câble.

#### <a name="to-cable-for-serial-connection"></a>Câble de connexion en série

1. Votre appareil dispose d’un port série sur chaque contrôleur est identifié par une icône représentant une clé à molette. Reportez-vous à l’illustration dans la section [câblage réseau](#network-cabling) pour identifier les ports série sur la carte d’insertion de votre appareil.

2. Identifiez le contrôleur actif dans votre panier appareil. Un LED clignote bleu indique que le contrôleur est actif.

3. Utilisez les câbles séries fournies (si nécessaire, le convertisseur USB série pour votre ordinateur portable) et vous connecter votre console ou votre ordinateur (avec émulation terminal à l’appareil) sur le port série du contrôleur actif.

4. Installer les pilotes USB de série (fournies avec le périphérique) sur votre ordinateur.

5. Configurer la connexion série comme suit : 115 200 transmission, 8 bits de données, 1 bit d’arrêt, aucune disparités qui existent et contrôle de flux définie sur aucune.

6. Vérifiez que la connexion fonctionne en appuyant sur entrée de la console. Un menu de console série doit apparaître.

>[AZURE.NOTE] **Gestion des pannes**: lorsque l’appareil est installé dans un centre de données à distance ou dans une salle informatique avec un accès limité, assurez-vous que les connexions séries sur les deux contrôleurs sont toujours connectées à un commutateur en série ou appareil similaire. Cela permet hors de télécommande et les opérations d’assistance s’il existe des interruptions de réseau ou défaillances inattendues.

Votre appareil est maintenant câblée pour power, accès réseau et connectivité en série. L’étape suivante consiste à configurer le logiciel et déployer votre appareil.

## <a name="next-steps"></a>Étapes suivantes

Découvrez comment [déployer et configurer votre appareil StorSimple local](storsimple-deployment-walkthrough.md).

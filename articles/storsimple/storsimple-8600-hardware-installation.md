<properties
   pageTitle="Installez votre périphérique 8600 StorSimple | Microsoft Azure"
   description="Décrit comment décompresser, montage en rack et câble votre appareil 8600 StorSimple avant de déployer et configurer le logiciel."
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
   ms.date="10/24/2016"
   ms.author="alkohli" />

# <a name="unpack-rack-mount-and-cable-your-storsimple-8600-device"></a>Décompressez, montage en rack et un câble votre appareil 8600 StorSimple

## <a name="overview"></a>Vue d’ensemble
Votre 8600 Microsoft Azure StorSimple est un appareil encadrement double et est constitué d’un serveur principal et un encadrement EBOD. Ce didacticiel explique comment décompresser, montage en rack, périphérique câble le 8600 StorSimple avant de configurer le logiciel StorSimple.

## <a name="unpack-your-storsimple-8600-device"></a>Décompresser le périphérique StorSimple 8600

Les étapes suivantes fournissent des instructions claires et détaillées comment décompresser le périphérique de stockage StorSimple 8600. Cet appareil est fourni dans deux zones, un pour le boîtier principal et un autre pour le boîtier EBOD. Ces deux zones sont placées dans une seule boîte.

### <a name="prepare-to-unpack-your-device"></a>Préparer la décompresser votre appareil

Avant de décompresser votre appareil, passez en revue les informations suivantes.


![Icône d’avertissement](./media/storsimple-safety/IC740879.png)![icône lourds](./media/storsimple-8600-hardware-installation/HCS_HeavyWeight_Icon.png) **Avertissement !**

1. Vérifiez que vous disposez de deux personnes disponibles pour gérer l’épaisseur du périphérique si vous gestion des manuellement. Un boîtier entièrement configuré peut évaluer jusqu'à 32 kg (70 kg.).
1. Placez la zone sur une surface plane et niveau.

Ensuite, procédez comme suit pour décompresser votre appareil.

#### <a name="to-unpack-your-device"></a>Pour décompresser votre appareil

1. Vérifiez que la zone et la mousse emballage pour crushes, morceaux, l’eau ou tout autre dommage évidente. Si la zone ou l’emballage est fortement endommagé, ne s’ouvrent pas la zone. Veuillez [contacter le support technique Microsoft](storsimple-contact-microsoft-support.md) pour vous aider à déterminer si le périphérique est bon fonctionnement.

2. Ouvrir la boîte d’externe, puis prenez déconnecter les deux cases correspondant à la langue principale et les pièces jointes EBOD. Vous pouvez maintenant décompresser le principal et les pièces jointes EBOD. La figure suivante illustre la vue décompressée de l’un des pièces jointes.

    ![Décompresser le périphérique de stockage](./media/storsimple-8600-hardware-installation/HCSUnpackyour4Udevice.png)

    **Affichage décompressé de votre périphérique de stockage**

     Étiquette | Description
     ----- | -------------
     1     | Boîte d’emballage
     2     | Câbles associations de sécurité (dans la barre d’état accessoires et câbles)
     3     | MOUSSE bas
     4     | APPAREIL
     5     | MOUSSE supérieure
     6     | Zone LAP

3. Après la décompression les deux cases, vérifiez que vous avez :

  - 1 encadrement principal (le boîtier principal et un boîtier EBOD sont en deux zones distinctes)
  - 1 encadrement EBOD
  - 4 câbles d’alimentation, 2 dans chaque zone
  - 2 câbles associations de sécurité (pour vous connecter à l’encadrement principal à boîtier EBOD)
  - câble Ethernet 1 croisé
  - 2 câbles console série
  - 1 convertisseur série USB pour l’accès en série
  - QSFP 4-à-SFP + cartes pour une utilisation avec 10 interfaces de réseau de solution
  - 2 rack kits de montage (4 rails de côté avec matériel, 2 pour le boîtier principal et un boîtier EBOD de montage), 1 dans chaque zone
  - Prise en main de documentation

    Si vous n’avez pas reçu un des éléments ci-dessus, [contactez le support technique Microsoft](storsimple-contact-microsoft-support.md).  

L’étape suivante consiste à votre appareil de montage en rack.

## <a name="rack-mount-your-storsimple-8600-device"></a>Votre appareil 8600 StorSimple de montage en rack

Suivez la procédure suivante pour installer votre dispositif de stockage StorSimple 8600 en rack 19 pouces standard avec avant et arrière billets. Cet appareil est fourni avec deux pièces jointes : un boîtier principal et un encadrement EBOD. Ces deux éléments doivent être racks.

L’installation se compose de plusieurs étapes, chacun d'entre eux est décrit dans les procédures suivantes.

> [AZURE.IMPORTANT]
Appareils StorSimple doivent être racks pour fonctionner correctement.



### <a name="site-preparation"></a>Préparation du site

Les pièces jointes doivent être installés dans un rack 19 pouces standard qui a avant et arrière billets. Utilisez la procédure suivante pour préparer l’installation en rack.

#### <a name="to-prepare-the-site-for-rack-installation"></a>Pour préparer le site montage en rack

1. Assurez-vous que le principal et les pièces jointes EBOD sont repos en toute sécurité sur une surface plate, stable et niveau travail (ou similaires).

2. Vérifiez que le site où vous souhaitez configurer a alimentation standard à partir d’une source indépendante ou un unité de Distribution d’alimentation (PDU) en rack avec une alimentation (sans coupure).

3. Vérifiez que cet emplacement (2 X 2U) une 4U est disponible sur le rack dans lequel vous souhaitez monter les pièces jointes.

![Icône d’avertissement](./media/storsimple-safety/IC740879.png)![icône lourds](./media/storsimple-8600-hardware-installation/HCS_HeavyWeight_Icon.png) **Avertissement !**

 Vérifiez que vous disposez de deux personnes disponibles pour gérer l’épaisseur si vous gérez l’installation du périphérique manuellement. Un boîtier entièrement configuré peut évaluer jusqu'à 32 kg (70 kg.).

### <a name="rack-prerequisites"></a>Conditions préalables en rack

Les pièces jointes sont conçues pour l’installation en rack 19 pouces standard armoire avec :

- Profondeur minimale de pouces 27.84 rack valider pour enregistrer
- Poids maximal de 32 kg pour l’appareil
- Pression maximale de 5 Pascal (colonne d’eau 0,5 mm)

### <a name="rack-mounting-rail-kit"></a>Kit de rails de montage en rack

Un ensemble de montage rails est fourni pour une utilisation avec le rack 19 pouces. Les rails ont été testés pour gérer l’épaisseur d’encadrement maximale. Ces rails permet également d’installation de plusieurs pièces jointes sans perte de l’espace dans le rack. Commencez par installer le boîtier EBOD.

#### <a name="to-install-the-ebod-enclosure-on-the-rails"></a>Pour installer le boîtier EBOD sur les rails

2. Exécuter cette étape uniquement si rails internes ne sont pas installés sur votre appareil. En règle générale, les rails internes sont installés en usine. Si rails ne sont pas installés, puis installez les diapositives rail gauche et droite sur les côtés du châssis du boîtier. Ils joindre à l’aide de six vis métriques de chaque côté. Pour faciliter l’orientation, les diapositives rail sont signalés **LH – avant** et **RH – premier plan**, et la fin est apposée vers l’arrière du boîtier dispose une extrémité conique.

    ![Attacher des diapositives rail à châssis boîtier](./media/storsimple-8600-hardware-installation/HCSAttachingRailSlidestoEnclosureChassis.png)

    **Joindre des diapositives rail sur les côtés de l’encadrement**

    Étiquette | Description
    ----- | -----------
    1     | M 3 x 4 vis bouton
    2     | Diapositives châssis

3. Joindre la rail gauche et assemblys droite aux membres verticale armoire rack. Les crochets sont signalés **LH**, **RH**et **cette face vers le haut** pour mener à bien orientation appropriée.

4. Recherchez les chevilles rail à l’avant et arrière de l’ensemble de rails. Le rail pour tienne entre les publications rack et insérez les chevilles en avant et holes de membre verticale rack arrière billet. N’oubliez pas que l’ensemble de rails est niveau.

5. Fixer le rail au rack membres verticales en utilisant deux vis métrique. Utilisez une vis de premier plan et l’autre à l’arrière.

6. Répétez ces étapes pour l’autre assemblage rail.

     ![Joindre des diapositives rail dans rack](./media/storsimple-8600-hardware-installation/HCSAttachingRailSlidestoRackCabinet.png)

    **Insertion d’assemblys rail dans le rack**

     Étiquette | Description
     ----- | -----------
     1     | Verrouillage vis
     2     | Rack avant carrée trou billet vis
     3     | Codes confidentiels rail avant emplacement à gauche
     4     | Verrouillage vis
     5     | Codes confidentiels emplacement rail arrière gauche

### <a name="mounting-the-ebod-enclosure-in-the-rack"></a>Montage du boîtier EBOD dans le rack

À l’aide de rails du rack viennent d’être installés, procédez comme suit pour monter le boîtier EBOD dans le rack.

#### <a name="to-mount-the-ebod-enclosure"></a>Monter le boîtier EBOD

1. Avec un assistant, lever le boîtier et alignez-le sur les rails de montage en rack.

2. Insérer avec soin le boîtier dans les rails et puis insérez-la complètement dans le rack armoire.

    ![Dispositif d’insertion dans le rack](./media/storsimple-8600-hardware-installation/HCSInsertingDeviceintheRack.png)

    **Montage du boîtier en rack**

3. Supprimer le texte en majuscules bord gauche et droite en tirant sur le texte en majuscules gratuit. Le texte en majuscules bord simplement aligne sur les brides.

4. Sécurisez le boîtier dans le rack en installant une vis cruciforme fournie par le biais chaque bord, gauche et droite.

4. Installer le texte en majuscules bord en appuyant sur les en position et l’alignement les à l’emplacement souhaité.

     ![Verr. MAJ bord l’installation](./media/storsimple-8600-hardware-installation/HCSInstallingFlangeCaps.png)

    **Installer le texte en majuscules de bord**

     Étiquette | Description
     ----- | -----------
     1     | Vis d’ancrage encadrement


### <a name="mounting-the-primary-enclosure-in-the-rack"></a>Montage du boîtier principal dans le rack

Une fois que vous avez terminé le boîtier EBOD de montage, vous devrez monter le boîtier principal suivant les mêmes étapes.

> [AZURE.NOTE]
>
> - Il est possible d’avoir plusieurs emplacements vides dans le rack entre le boîtier principal et le boîtier EBOD.
> - Utilisez le câble sa fournies 2m pour connecter le boîtier principal au boîtier EBOD.
> - Il n’existe aucune contrainte sur la position relative de l’unité de tête à l’unité EBOD. Par conséquent, le boîtier principal peut être placé dans l’emplacement du haut et le boîtier EBOD ci-dessous, ou vice versa.

L’étape suivante consiste pour connecter votre périphérique d’alimentation, réseau et accès série.

## <a name="cable-your-storsimple-8600-device"></a>Câble votre appareil 8600 StorSimple

Les procédures suivantes expliquent comment faire pour connecter votre périphérique 8600 StorSimple d’alimentation, réseau et connexions série.

### <a name="prerequisites"></a>Conditions préalables

Avant de commencer à câble votre appareil, vous devez :

- Votre boîtier principal et le boîtier EBOD, complètement décompressé
- 4 câbles d’alimentation (2 chaque pour le serveur principal et le boîtier EBOD) fourni avec votre appareil
- 2 câbles associations de sécurité fournis avec le périphérique pour vous connecter le boîtier EBOD à l’encadrement principal
- Accès à 2 Power Distribution unités (recommandé)
- Câbles réseau
- Fourni câbles série
- Convertisseur série USB avec le pilote approprié est installé sur votre PC (si nécessaire)
- Fourni QSFP 4-à-SFP + cartes pour une utilisation avec 10 interfaces de réseau de solution
- [Matériel pris en charge pour les interfaces réseau solution 10 sur votre appareil StorSimple](storsimple-supported-hardware-for-10-gbe-network-interfaces.md)

### <a name="sas-and-power-cabling"></a>Associations de sécurité et de Power câblage

Votre appareil dispose d’un boîtier principal et un encadrement EBOD. Cette fonctionnalité nécessite l’intensité d’utilisation être relié se pour power et la connectivité de série joint SCSI (sa).

Lorsque vous configurez cet appareil pour la première fois, exécutez les étapes pour les associations de sécurité câblage tout d’abord et puis suivez les étapes pour power câblage.

[AZURE.INCLUDE [storsimple-cable-8600-for-SAS](../../includes/storsimple-sas-cable-8600.md)]

[AZURE.INCLUDE [storsimple-cable-8600-for-power](../../includes/storsimple-cable-8600-for-power.md)]

### <a name="network-cabling"></a>Câblage réseau

Votre appareil est dans une configuration active-de réserve : à un moment donné, un module contrôleur est actif, toutes les opérations de disque et de réseau lors de l’autre module contrôleur de traitement en veille. En cas d’échec de contrôleur, le contrôleur de secours immédiatement active et continue toutes les opérations de mise en réseau et de disque.

Pour prendre en charge ce basculement contrôleur redondantes, vous devez câble réseau de votre appareil, comme indiqué dans les étapes suivantes.

#### <a name="to-cable-for-network-connection"></a>Câble de connexion réseau

1. Votre appareil dispose d’interfaces réseau six sur chaque contrôleur : quatre 1 Go/s et deux 10 Go/s Ethernet ports. Reportez-vous à l’illustration suivante pour identifier les ports de données sur la carte d’insertion de votre appareil.

     ![Carte d’insertion d’un périphérique 8600](./media/storsimple-8600-hardware-installation/HCSBackplaneof2UDevicewithPortsLabeled.jpg)

    **Retour de votre appareil montrant les ports de données**

     Étiquette   | Description
     ------- | -----------
     0,1,4,5 |  Interfaces de réseau solution 1
     2, 3     | 10 interfaces de réseau de solution
     6       | Ports série



1. Afficher le diagramme suivant pour câblage réseau. (La configuration réseau minimale est indiquée par traits pleins bleus. Disponibilité et les performances, configuration supplémentaire requise est indiquée par des lignes pointillées.)

![Câble votre appareil 4U réseau](./media/storsimple-8600-hardware-installation/HCSCableYour4UDeviceforNetwork.png)

**Réseau de votre appareil**

Étiquette | Description
----- | -----------
A    | Réseau local avec accès à Internet
B    | Contrôleur de 0
C    | PCM 0
D    | Contrôleur de 1
E    | PCM 1
F    | Contrôleur EBOD 0
G    | Contrôleur de EBOD 1
H, J’AI  | Hôtes (par exemple, les serveurs de fichiers)
0-5  | Interfaces réseau
6    | Boîtier principal
7    | Encadrement EBOD

Lorsque l’appareil, de câblage requiert la configuration minimale requise :


- Au moins deux interfaces réseau connecté sur chaque contrôleur avec une pour l’accès au cloud et l’autre pour iSCSI. Les données 0 port est automatiquement activé et configuré via la console série du périphérique. En dehors de données 0, un autre port de données doit également être configurés à partir du portail classique Azure. Dans ce cas, connecter des données 0 port pour le réseau principal (réseau avec accès à Internet). Les autres ports de données pouvant être connectés au segment SAN/iSCSI LAN (réseau local virtuel) du réseau, en fonction du rôle initial.

- Interfaces identiques sur chaque contrôleur connecté au réseau même pour garantir la disponibilité si un basculement contrôleur se produit. Par exemple, si vous choisissez de connecter les données 0 et 3 une le contrôleur de données, vous devez connecter les données correspondantes 0 et données 3 sur l’autre contrôleur.

N’oubliez pas de disponibilité et les performances :


- Si possible, configurez une paire de l’interface de réseau pour l’accès au cloud (1 solution) et une autre paire pour iSCSI (10 GbE recommandé) sur chaque contrôleur.

- Si possible, connectez-vous interfaces réseau à partir de chaque contrôleur à deux commutateurs différents pour assurer la disponibilité par rapport à une défaillance du commutateur. L’illustration montre les deux 10 interfaces réseau solution, données 2 et 3 de données, à partir de chaque contrôleur connectée à deux commutateurs différents. Pour plus d’informations, consultez les **interfaces réseau** selon les [besoins de disponibilité pour votre appareil StorSimple](storsimple-system-requirements.md#high-availability-requirements-for-storsimple).

>[AZURE.NOTE] Si vous utilisez SFP + s’avec 10 interfaces réseau solution, utilisez la QSFP fournie-SFP + cartes. Pour plus d’informations, accédez au [matériel pris en charge pour les interfaces réseau solution 10 sur votre appareil StorSimple](storsimple-supported-hardware-for-10-gbe-network-interfaces.md).

### <a name="serial-port-cabling"></a>Port série câblage

Effectuez les opérations suivantes pour les ports série du câble.

#### <a name="to-cable-for-serial-connection"></a>Câble de connexion en série

1. Votre appareil dispose d’un port série sur chaque contrôleur est identifié par une icône représentant une clé à molette. Pour rechercher les ports série, reportez-vous à l’illustration montrant les données ports au dos de votre appareil.

2. Identifiez le contrôleur actif dans votre panier appareil. Un LED clignote bleu indique que le contrôleur est actif.

3. Utilisez le câble série fourni (si nécessaire, le convertisseur USB série pour votre ordinateur portable) et se connecter votre console ou votre ordinateur (avec émulation terminal à l’appareil) sur le port série du contrôleur actif.

4. Installer les pilotes USB de série (fournies avec le périphérique) sur votre ordinateur.

5. Configurer la connexion série comme suit :
   - transmission 115 200
   - 8 bits de données
   - 1 bit d’arrêt
   - Aucune disparités qui existent
   - Contrôle de flux définie sur **Aucune**

6. Vérifiez que la connexion fonctionne en appuyant sur entrée de la console. Un menu de console série doit apparaître.

> [AZURE.NOTE] **Gestion des pannes :** Lorsque l’appareil est installé dans un centre de données à distance ou dans une salle informatique avec un accès limité, assurez-vous que les connexions séries sur les deux contrôleurs sont toujours connectées à un commutateur en série ou appareil similaire. Cela permet de hors télécommande et opérations de prise en charge en cas d’interruption du réseau ou défaillances inattendues.

Vous avez terminé de câblage votre périphérique d’alimentation, accès réseau et connexion série. L’étape suivante consiste à configurer le logiciel sur votre appareil.

## <a name="next-steps"></a>Étapes suivantes

Vous êtes maintenant prêt à [déployer et configurer votre appareil StorSimple local](storsimple-deployment-walkthrough-u2.md).

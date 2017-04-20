<properties 
   pageTitle="Activer ou désactiver le périphérique StorSimple | Microsoft Azure"
   description="Explique comment activer un nouvel appareil StorSimple et activer un périphérique qui a été arrêté ou coupure désactiver un périphérique en cours d’exécution."
   services="storsimple"
   documentationCenter=""
   authors="alkohli"
   manager="carmonm"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD"
   ms.date="08/23/2016"
   ms.author="alkohli" />

# <a name="turn-your-storsimple-device-on-or-off"></a>Activer ou désactiver le périphérique StorSimple 

## <a name="overview"></a>Vue d’ensemble

Arrêt d’un périphérique Microsoft Azure StorSimple n’est pas obligatoire dans le cadre de fonctionnement normal du système. Toutefois, vous devrez peut-être activer un nouvel appareil ou un appareil devant être arrêté. Généralement, un arrêt est requise dans les cas dans lesquels vous devez remplacer matériel défectueux, physiquement déplacer une unité ou mettre un périphérique hors service. Ce didacticiel décrit la procédure requise pour l’activation et d’arrêt de votre appareil StorSimple dans différents scénarios.

Le tableau suivant répertorie les différents scénarios d’activation et d’arrêt de votre appareil StorSimple et fournit des liens vers les procédures appropriées.

|Scénario|Rubriques de référence|
|:-------|:---------------|
|Activer un nouvel appareil|[Activer un nouvel appareil](#turn-on-a-new-device)<ul><li>[Nouvel appareil avec boîtier principal uniquement](#new-device-with-primary-enclosure-only)</li><li>[Nouvel appareil avec boîtier EBOD](#new-device-with-ebod-enclosure)</li></ul>|
|Activer un appareil après l’arrêt|[Activer un appareil après l’arrêt](#turn-on-a-device-after-shutdown)<ul><li>[Appareil avec boîtier principal uniquement](#device-with-primary-enclosure-only)</li><li>[Appareil avec boîtier EBOD](#device-with-ebod-enclosure)</li></ul>|
|Activer un appareil après une coupure|[Activer un appareil après une coupure](#turn-on-a-device-after-a-power-loss)<ul><li>[Appareil avec boîtier principal uniquement](#8100)</li><li>[Appareil avec boîtier EBOD](#8600)</li></ul>|
|Activer un appareil après le boîtier principal et EBOD connexion est perdue|[Activer un appareil après le serveur principal et EBOD encadrement connexion est perdue](#turn-on-a-device-after-the-primary-and-ebod-enclosure-connection-is-lost)|
|Arrêter un périphérique en cours d’exécution|[Désactiver un périphérique en cours d’exécution](#turn-off-a-running-device)<ul><li>[Appareil avec boîtier principal uniquement](#8100a)</li><li>[Appareil avec boîtier EBOD](#8600a)</li></ul>|

## <a name="turn-on-a-new-device"></a>Activer un nouvel appareil

Les étapes pour activer un périphérique StorSimple pour la première fois diffèrent selon que l’appareil est un 8100 ou un modèle 8600. La 8100 contient un seul boîtier principal, tandis que le 8600 est un appareil double encadrement avec un boîtier principal et un encadrement EBOD. Les étapes détaillées pour les deux modèles sont décrites dans les sections suivantes.

- [Nouvel appareil avec boîtier principal uniquement](#new-device-with-primary-enclosure-only)

- [Nouvel appareil avec boîtier EBOD](#new-device-with-ebod-enclosure)

### <a name="new-device-with-primary-enclosure-only"></a>Nouvel appareil avec boîtier principal uniquement

Le modèle 8100 StorSimple est un appareil boîtier unique. Votre appareil comprend redondants Power et Modules ventilation (PCM pour). Deux PCMs doivent être installées et connectées à plusieurs sources d’alimentation pour optimiser la disponibilité.

Effectuez les opérations suivantes pour votre appareil pour power du câble.

[AZURE.INCLUDE [storsimple-cable-8100-for-power](../../includes/storsimple-cable-8100-for-power.md)]

>[AZURE.NOTE]Pour le programme d’installation complète des périphériques et les instructions de câblage, accédez à [installer votre appareil StorSimple 8100](storsimple-8100-hardware-installation.md). Vérifiez que vous suivez les instructions.

### <a name="new-device-with-ebod-enclosure"></a>Nouvel appareil avec boîtier EBOD

Le modèle 8600 StorSimple possède un boîtier principal et un encadrement EBOD. Cette fonctionnalité nécessite l’intensité d’utilisation être relié se pour power et la connectivité de série joint SCSI (sa).

Lorsque vous configurez cet appareil pour la première fois, exécutez les étapes pour les associations de sécurité câblage tout d’abord et puis suivez les étapes pour power câblage.

[AZURE.INCLUDE [storsimple-sas-cable-8600](../../includes/storsimple-sas-cable-8600.md)]

[AZURE.INCLUDE [storsimple-cable-8600-for-power](../../includes/storsimple-cable-8600-for-power.md)]

>[AZURE.NOTE]Pour le programme d’installation complète des périphériques et les instructions de câblage, accédez à [installer votre appareil StorSimple 8600](storsimple-8600-hardware-installation.md). Vérifiez que vous suivez les instructions.

## <a name="turn-on-a-device-after-shutdown"></a>Activer un appareil après l’arrêt

Les étapes pour activer un périphérique StorSimple une fois qu’il a été arrêté sont différentes selon que l’appareil est un 8100 ou un modèle 8600. La 8100 contient un seul boîtier principal, tandis que le 8600 est un appareil double encadrement avec un boîtier principal et un encadrement EBOD.

- [Appareil avec boîtier principal uniquement](#device-with-primary-enclosure-only)

- [Appareil avec boîtier EBOD](#device-with-ebod-enclosure)

### <a name="device-with-primary-enclosure-only"></a>Appareil avec boîtier principal uniquement

Après un arrêt, procédez comme suit pour activer un périphérique StorSimple avec un boîtier principal et un aucun boîtier EBOD.

#### <a name="to-turn-on-a-device-with-a-primary-enclosure-only"></a>Pour activer un périphérique avec un boîtier principal uniquement

1. Assurez-vous que la puissance bascule sur les deux Power et refroidissement Modules (PCM pour) sont en cours de la position désactivé. Si les commutateurs ne sont pas dans la position désactivé, puis les retourner sur la position désactivé et attendez les lumières arrêt.

2. Activer le périphérique en le retournant les commutateurs power sur les deux PCMs à position activé. Le périphérique doit activer.

3. Vérifiez les points suivants pour vérifier que l’appareil est entièrement sur :

    1. Les voyants OK dans les deux modules PCM apparaissent en verts.

    2. Les voyants d’état sur les deux contrôleurs sont vert.

    3. Le bleu LED une le contrôleur de clignote, qui indique que le contrôleur est actif.

    Si une de ces conditions n’est pas remplie, votre appareil n’est pas correct. Veuillez [contacter le support technique Microsoft](storsimple-contact-microsoft-support.md).

### <a name="device-with-ebod-enclosure"></a>Appareil avec boîtier EBOD

Après un arrêt, procédez comme suit pour activer un périphérique StorSimple avec un boîtier principal et un encadrement EBOD. Effectuer exactement comme décrit chaque étape dans l’ordre. Cela peut provoquer la perte de données.

#### <a name="to-turn-on-a-device-with-a-primary-and-an-ebod-enclosure"></a>Pour activer un appareil doté d’un serveur principal et un encadrement EBOD

1. Vérifiez que le boîtier EBOD est connecté à l’encadrement principal. Pour plus d’informations, voir [installer votre appareil StorSimple 8600](storsimple-8600-hardware-installation.md).

2. Assurez-vous que la puissance et refroidissement Modules (PCM pour) sur les EBOD et les pièces jointes principales sont dans la position désactivé. Si les commutateurs ne sont pas dans la position désactivé, puis les retourner sur la position désactivé et attendez les lumières arrêt.

3. Activer le boîtier EBOD tout d’abord en désactivant la puissance bascule sur les deux PCMs en position activé. Les voyants PCM doivent être verts. Un contrôleur EBOD vert LED sur cette unité indique que le boîtier EBOD est activé.

4. Activer le boîtier principal en inversant les commutateurs power sur les deux PCMs à position activé. L’ensemble du système doit apparaître.

5. Vérifiez que les associations de sécurité voyants sont verts, qui permet de garantir que la connexion entre le boîtier EBOD et le boîtier principal est préférable.

## <a name="turn-on-a-device-after-a-power-loss"></a>Activer un appareil après une coupure

Une coupure de courant ou l’interruption peut arrêter un périphérique StorSimple. La coupure de courant peut se produire dans un des blocs d’alimentation ou les deux blocs d’alimentation. Les étapes de récupération sont différentes selon que l’appareil apparaît un modèle 8100 ou 8600. La 8100 contient un seul boîtier principal, tandis que le 8600 est un appareil double encadrement avec un boîtier principal et un encadrement EBOD. Cette section décrit la procédure de récupération pour chaque scénario.

- [Appareil avec boîtier principal uniquement](#8100)

- [Appareil avec boîtier EBOD](#8600)

### <a name="device-with-primary-enclosure-only-a-name8100"></a>Appareil avec boîtier principal uniquement<a name="8100">

Le système peut continuer son fonctionnement normal s’il existe coupure à un de ses blocs d’alimentation. Toutefois, pour optimiser la disponibilité du périphérique, restaurer power à l’alimentation dès que possible.

S’il existe une coupure de courant ou coupure de courant dans les deux blocs d’alimentation, le système s’arrêtera de manière ordonnée et contrôlée. Lorsque la puissance est restaurée, le système automatiquement activera.

### <a name="device-with-ebod-enclosure-a-name8600"></a>Appareil avec boîtier EBOD<a name="8600">

#### <a name="power-loss-on-one-power-supply"></a>Fournir une coupure d’alimentation une power

Le système peut continuer son fonctionnement normal s’il existe coupure à un de ses blocs d’alimentation sur le boîtier principal ou le boîtier EBOD. Toutefois, pour optimiser la disponibilité du périphérique, restaurez power à l’alimentation dès que possible.

#### <a name="power-loss-on-both-power-supplies-on-primary-and-ebod-enclosures"></a>Coupure sur les deux blocs d’alimentation sur principal et pièces jointes EBOD

S’il existe une coupure de courant ou coupure de courant dans les deux blocs d’alimentation, le boîtier EBOD s’arrêtera immédiatement et le boîtier principal s’arrêtera de manière ordonnée et contrôlée. Lorsque l’alimentation est restaurée, l’application commencera automatiquement.

Si la puissance est désactivée manuellement, puis procédez comme suit pour restaurer power sur le système.

1. Activer le boîtier EBOD.

2. Une fois le boîtier EBOD est activé, activez le boîtier principal.

### <a name="power-loss-on-both-power-supplies-on-ebod-enclosure"></a>Coupure sur les deux blocs d’alimentation sur encadrement EBOD

Lorsque vous configurez votre câbles, vous devez vous assurer que la EBOD seul n’est jamais connecté à une PDU distincte. Si la EBOD et encadrement primaire échouent en même temps, le système permet de récupérer.

Si seul le boîtier EBOD échoue sur les deux blocs d’alimentation, le système ne récupère pas automatiquement. Procédez comme suit pour activer le système et de rétablir un état :

1. Si le boîtier principal est activé, désactivez Power et Modules ventilation (PCM pour).

2. Attendez quelques minutes pour que le système d’arrêté.

3. Activer le boîtier EBOD.

4. Une fois le boîtier EBOD est activé, activez le boîtier principal.

## <a name="turn-on-a-device-after-the-primary-and-ebod-enclosure-connection-is-lost"></a>Activer un appareil après le serveur principal et EBOD encadrement connexion est perdue

Si la connexion est perdue entre le contrôleur de secours et le contrôleur EBOD correspondant, le périphérique continue de fonctionner. Si la connexion entre le contrôleur active système et le EBOD correspondant est perdue, reprise doit avoir lieu et l’appareil doit continuer à fonctionner comme d’habitude.

Lorsque les deux câbles série joint SCSI (sa) sont supprimés ou la connexion entre le boîtier EBOD et le boîtier principal est interrompue, le périphérique fonctionnent plus. À ce stade, procédez comme suit.

### <a name="to-turn-on-the-device-after-connection-is-lost"></a>Pour activer l’appareil une fois la connexion est perdue

1. Accéder à l’arrière-plan de l’appareil.

2. Si la connexion de câble associations de sécurité entre le boîtier EBOD et le boîtier principal est interrompue, couloir sa tous les voyants sur le boîtier EBOD seront désactiver.

3. Arrêtez Power et Modules ventilation (PCM pour) sur le boîtier EBOD et le boîtier principal.

4. Attendez que désactiver toutes les lumières DOS d’à la fois les pièces jointes.

5. Réinsérez les câbles associations de sécurité et vérifiez qu’il existe une bonne connexion entre le boîtier EBOD et le boîtier principal.

6. Activer le boîtier EBOD tout d’abord en désactivant les deux PCM bascule en position activé.

7. Assurez-vous que le boîtier EBOD est activé en vérifiant que le vert est activé.

8. Activer le boîtier principal.

9. Assurez-vous que le boîtier principal est activé en vérifiant que la LED contrôleur vert est activé.

10. Vérifiez que la connexion d’encadrement EBOD avec le boîtier principal est préférable en vérifiant que le couloir sa voyants (quatre par contrôleur EBOD) sont tous les ON.

>[AZURE.IMPORTANT] Si les câbles associations de sécurité sont défectueux ou la connexion entre le boîtier EBOD et le boîtier principal est cela ne vous convient, quand vous activez le système, il passe en mode de récupération. Dans ce cas, veuillez [contacter le support technique Microsoft](storsimple-contact-microsoft-support.md) .

## <a name="turn-off-a-running-device"></a>Désactiver un périphérique en cours d’exécution

Un périphérique StorSimple en cours d’exécution devrez peut-être arrêter si elle est déplacé, hors service, ou dispose d’un composant ne fonctionne pas correctement qui doit être remplacée. Les étapes sont différentes selon que l’appareil StorSimple est un 8100 ou un modèle 8600. La 8100 contient un seul boîtier principal, tandis que le 8600 est un appareil double encadrement avec un boîtier principal et un encadrement EBOD. Cette section décrit les étapes pour arrêter un périphérique en cours d’exécution.

- [Appareil avec boîtier principal](#8100a)

- [Appareil avec boîtier EBOD](#8600a)

### <a name="device-with-primary-enclosure-a-name8100a"></a>Appareil avec boîtier principal<a name="8100a"> 

Pour arrêter le périphérique de manière ordonnée et contrôlée, vous pouvez le faire via le portail Azure classique ou via le Windows PowerShell pour StorSimple. 

>[AZURE.IMPORTANT] Ne pas s’arrêtent un périphérique en cours d’exécution en utilisant le bouton power au dos de l’appareil.
>
>Avant d’arrêter le périphérique, assurez-vous que tous les composants du périphérique fonctionnent correctement. Dans le portail classique Azure, accédez à **appareils** > **Maintenance** > **État du matériel**et vérifiez que l’état de tous les composants est vert. C’est vrai uniquement pour un système correct. Si le système est en cours arrêté pour remplacer un composant ne fonctionne pas correctement, vous verrez un échec (rouge) ou est détérioré état (jaune) pour le composant correspondant dans l' **État du matériel**.

Une fois que vous accédez à Windows PowerShell pour StorSimple ou le portail classique Azure, suivez les étapes de [Arrêter un périphérique StorSimple](storsimple-manage-device-controller.md#shut-down-a-storsimple-device). 

### <a name="device-with-ebod-enclosure-a-name8600a"></a>Appareil avec boîtier EBOD<a name="8600a">

>[AZURE.IMPORTANT] Avant d’arrêter le boîtier principal et le boîtier EBOD, assurez-vous que tous les composants de l’appareil sont correct. Dans le portail classique Azure, accédez à **appareils** > **Maintenance** > **État du matériel**et vérifiez que tous les composants fonctionnent correctement.

#### <a name="to-shut-down-a-running-device-with-ebod-enclosure"></a>Pour arrêter un périphérique en cours d’exécution avec le boîtier EBOD

1. Suivez toutes les étapes indiquées dans [Arrêter un périphérique StorSimple](storsimple-manage-device-controller.md#shut-down-a-storsimple-device) pour le boîtier principal.

2. Une fois le boîtier principal est arrêté, arrêter la EBOD en le retournant désactiver commutateurs Power et Module refroidissement (PCM).

3. Pour vérifier que le EBOD est arrêté, vérifiez que tous les voyants à l’arrière du boîtier EBOD sont désactivés.

>[AZURE.NOTE] Que les câbles associations de sécurité sont utilisées pour connecter le boîtier EBOD au boîtier primaire ne doivent pas être supprimés jusqu'à une fois que le système est arrêté.

## <a name="next-steps"></a>Étapes suivantes

[Contacter le support technique Microsoft](storsimple-contact-microsoft-support.md) si vous rencontrez des problèmes lors de l’activation ou l’arrêt d’un périphérique StorSimple.


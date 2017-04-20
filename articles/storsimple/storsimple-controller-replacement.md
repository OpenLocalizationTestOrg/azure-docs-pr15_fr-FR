<properties 
   pageTitle="Remplacer un contrôleur de matériel StorSimple | Microsoft Azure"
   description="Explique comment supprimer et le remplacer un ou deux modules contrôleur sur votre appareil StorSimple."
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
   ms.date="08/17/2016"
   ms.author="alkohli" />

# <a name="replace-a-controller-module-on-your-storsimple-device"></a>Remplacement d’un module contrôleur sur votre appareil StorSimple

## <a name="overview"></a>Vue d’ensemble

Ce didacticiel explique la procédure pour supprimer et remplacer un ou deux modules contrôleur dans un périphérique StorSimple. Il traite également la logique sous-jacente pour les scénarios de remplacement contrôleur simple et double.

>[AZURE.NOTE] Avant d’effectuer une autre contrôleur, nous vous recommandons de toujours mettre votre microprogramme du contrôleur vers la dernière version.
>
>Pour éviter tout dommage sur votre appareil StorSimple, n’éjectez pas le contrôleur jusqu'à ce que les voyants sont affichent sous forme d’une des opérations suivantes :
>
>- Les lumières sont désactivé.
>- LED 3, ![icône avec une coche verte](./media/storsimple-controller-replacement/HCS_GreenCheckIcon.png), et ![croix rouge avec une icône](./media/storsimple-controller-replacement/HCS_RedCrossIcon.png) sont clignote, et LED 0 et 7 LED sont **activé**.

Le tableau suivant montre les scénarios de remplacement contrôleur pris en charge.

|Casse|Scénario de remplacement|Procédure applicable|
|:---|:-------------------|:-------------------|
|1|Un contrôleur est dans un état d’échec, l’autre contrôleur est correct et active.|[Remplacement d’un contrôleur unique](#replace-a-single-controller), qui décrit la [logique derrière un remplacement contrôleur unique](#single-controller-replacement-logic), ainsi que les [étapes de remplacement](#single-controller-replacement-steps).|
|2|À la fois les contrôleurs n’ont pas pu et nécessitent de remplacement. Châssis, disques, boîtier and.disk fonctionnent correctement.|[Remplacement de contrôleur double](#replace-both-controllers), qui décrit la [logique derrière le texte de remplacement souhaité contrôleur double](#dual-controller-replacement-logic), ainsi que les [étapes de remplacement](#dual-controller-replacement-steps). |
|3|Contrôleurs dans le même appareil ou à partir d’appareils différents sont échangées. Châssis, des disques et des pièces jointes disque fonctionnent correctement.|Un message d’alerte incompatibilité emplacement apparaît.|
|4|Il manque un contrôleur et l’autre contrôleur échoue.|[Remplacement de contrôleur double](#replace-both-controllers), qui décrit la [logique derrière le texte de remplacement souhaité contrôleur double](#dual-controller-replacement-logic), ainsi que les [étapes de remplacement](#dual-controller-replacement-steps).|
|5|Un ou deux contrôleurs ont échoué. Vous ne peut pas accéder à l’appareil via la console série ou d’accès distant Windows PowerShell.|[Contacter le support technique Microsoft](storsimple-contact-microsoft-support.md) pour obtenir une procédure remplacement contrôleur manuel.|
|6|Les contrôleurs ont un autre numéro de version, qui peut être dû à :<ul><li>Contrôleurs ont une autre version logicielle.</li><li>Contrôleurs ont une version microprogramme différents.</li></ul>|Si les versions du logiciel contrôleur sont différentes, la logique de remplacement qui détecte et met à jour la version du logiciel sur le contrôleur de remplacement.<br><br>Si les versions de microprogramme contrôleur sont différentes et l’ancienne version de microprogramme est **pas** automatiquement mise à niveau, un message d’alerte s’affichent dans le portail classique Azure. Vous devez rechercher les mises à jour et installer les mises à jour de microprogramme.</br></br>Si les versions de microprogramme contrôleur sont différentes et l’ancienne version de microprogramme est automatiquement mise à niveau, la logique de remplacement contrôleur permettra de le détecter et après que le contrôleur démarre, le microprogramme sera mis à jour automatiquement.|

Vous devez supprimer un module contrôleur si elle a échoué. Un ou deux modules contrôleur peuvent échouer, ce qui peut provoquer un seul contrôleur remplacement ou de remplacement contrôleur double. Pour les procédures de remplacement et la logique derrière les, voir les rubriques suivantes :

- [Remplacer un seul contrôleur](#replace-a-single-controller)
- [Remplacez les deux contrôleurs](#replace-both-controllers)
- [Supprimer un contrôleur](#remove-a-controller)
- [Insérer un contrôleur](#insert-a-controller)
- [Identifiez le contrôleur actif sur votre appareil](#identify-the-active-controller-on-your-device)

>[AZURE.IMPORTANT] Avant la suppression et remplacement d’un contrôleur, passez en revue les informations de sécurité dans le [remplacement des composants matériels StorSimple](storsimple-hardware-component-replacement.md).

## <a name="replace-a-single-controller"></a>Remplacer un seul contrôleur

Lors d’une des deux contrôleurs sur le périphérique Microsoft Azure StorSimple n’a pas pu, fonctionne mal ou est manquant, vous devez remplacer un seul contrôleur. 

### <a name="single-controller-replacement-logic"></a>Logique de remplacement contrôleur unique

Dans le remplacement d’un contrôleur unique, vous devez tout d’abord supprimer le contrôleur a échoué. (Le contrôleur restant dans l’appareil est le contrôleur actif). Lorsque vous insérez le contrôleur de remplacement, les actions suivantes se produisent :

1. Le contrôleur de remplacement commence immédiatement à communiquer avec le périphérique StorSimple.

2. Un instantané du disque dur virtuel (disque dur virtuel) pour le contrôleur actif est copié sur le contrôleur de remplacement.

3. L’instantané est modifié afin que lorsque le contrôleur de remplacement démarre à partir de ce disque dur virtuel, il sera reconnu comme contrôleur de secours.

4. Lorsque les modifications sont terminées, le contrôleur de remplacement démarre en tant que le contrôleur de secours.

5. Lorsque les deux contrôleurs en cours d’exécution, le cluster est en ligne.

### <a name="single-controller-replacement-steps"></a>Étapes de remplacement contrôleur unique

Procédez comme suit si un contrôleur de dans votre appareil Microsoft Azure StorSimple échoue. (L’autre contrôleur doit être active et en cours d’exécution. Si les deux contrôleurs échouent ou un dysfonctionnement, passez aux [étapes de remplacement contrôleur double](#dual-controller-replacement-steps).)

>[AZURE.NOTE] Il peut prendre 30 à 45 minutes pour le contrôleur de redémarrer et de récupérer complètement à partir de la procédure de remplacement d’un contrôleur unique. La durée totale de la procédure entière, y compris en joignant les câbles est environ 2 heures.

#### <a name="to-remove-a-single-failed-controller-module"></a>Pour supprimer un module contrôleur échec unique

1. Dans le portail classique Azure, accédez au service Manager StorSimple, cliquez sur l’onglet **périphériques** , puis cliquez sur le nom de l’appareil que vous souhaitez surveiller.

2. Accédez à **Maintenance > état du matériel**. L’état du contrôleur de 0 ou 1 contrôleur doit être rouge, qui indique une défaillance.

    >[AZURE.NOTE] Le contrôleur échec dans une autre contrôleur unique est toujours un contrôleur de secours.

3. Utilisez Figure 1 et le tableau suivant pour rechercher le module contrôleur a échoué.  

    ![Carte d’insertion de modules du boîtier principal appareil](./media/storsimple-controller-replacement/IC740994.png)

    **Figure 1** Verso StorSimple appareil

  	|Étiquette|Description|
  	|:----|:----------|
  	|1|PCM 0|
  	|2|PCM 1|
  	|3|Contrôleur de 0|
  	|4|Contrôleur de 1|

4. Sur le contrôleur échec, supprimez tous les câbles réseau connecté ports de données. Si vous utilisez un modèle 8600, supprimez également les câbles associations de sécurité qui se connectent le contrôleur sur le contrôleur de EBOD.

5. Suivez les étapes de [Supprimer un contrôleur](#remove-a-controller) pour supprimer le contrôleur a échoué. 

6. Installez le remplacement usine dans le même emplacement à partir duquel le contrôleur échec a été supprimé. Cela déclenche la logique de remplacement contrôleur unique. Pour plus d’informations, voir [contrôleur simple remplacement logique](#single-controller-replacement-logic).

7. Tandis que la logique de remplacement contrôleur unique progresse en arrière-plan, vous reconnecter que les câbles. Veillez à vous connecter que les câbles de la même manière qu’ils ont été connectés avant le remplacement.

8. Après le redémarrage de celui-ci, vérifiez l' **état du contrôleur** et l' **état de Cluster** dans le portail classique Azure pour vérifier que le contrôleur est dans un état correct et en veille.

>[AZURE.NOTE] Si vous analysez le périphérique via la console série, vous pouvez voir redémarre plusieurs fois pendant que le contrôleur est récupération à partir de la procédure de remplacement. Lorsque le menu de console série s’affiche, vous savez que le remplacement est terminé. Si le menu n’apparaît pas dans les deux heures du démarrage le remplacement contrôleur, veuillez [contacter le support technique Microsoft](storsimple-contact-microsoft-support.md).

## <a name="replace-both-controllers"></a>Remplacez les deux contrôleurs

Lorsque les deux contrôleurs sur le périphérique Microsoft Azure StorSimple n’ont pas pu, fonctionnent incorrectement ou sont manquantes, vous devez remplacer les deux contrôleurs. 

### <a name="dual-controller-replacement-logic"></a>Logique de remplacement contrôleur double

Dans le remplacement contrôleur double, vous commencez par supprimez les deux contrôleurs échecs, puis insérez remplacements. Lorsque les contrôleurs de deux remplacement sont insérés, les actions suivantes se produisent :

1. Le contrôleur de remplacement à l’emplacement 0 vérifie les points suivants :
 
   1. Il utilise les versions actuelles du microprogramme et du logiciel ?

   2. S’agit-il d’une partie du cluster ?

   3. Est le contrôleur homologue en cours d’exécution et est-il groupé ?
                            
    Si aucune de ces conditions sont vraies, le contrôleur de recherche la plus récente sauvegarde quotidienne (située dans le **nonDOMstorage** sur le lecteur S). Le contrôleur de copie le dernier instantané de ce dernier à partir de la sauvegarde.

2. Le contrôleur dans l’emplacement 0 utilise l’instantané vers l’image elle-même.

3. Pendant ce temps, le contrôleur dans l’emplacement 1 attend contrôleur 0 pour terminer la création d’images et démarrer.

4. Après le démarrage de contrôleur 0, contrôleur 1 détecte le cluster créé par contrôleur de 0, qui déclenche la logique de remplacement contrôleur unique. Pour plus d’informations, voir [contrôleur simple remplacement logique](#single-controller-replacement-logic).

5. Par la suite, les deux contrôleurs exécutera et le cluster arriveront en ligne.

>[AZURE.IMPORTANT] Suivre un remplacement contrôleur double, une fois que l’appareil StorSimple est configuré, il est essentiel que vous prenez un manuel de sauvegarde du périphérique. Sauvegardes quotidiennes de la configuration de périphérique ne sont pas déclenchés jusqu'à après que 24 heures se sont écoulées. Travailler avec [Prise en charge Microsoft](storsimple-contact-microsoft-support.md) pour vous un manuel de sauvegarde de votre appareil.

### <a name="dual-controller-replacement-steps"></a>Étapes de remplacement contrôleur double

Ce flux de travail est requis lorsque les deux contrôleur de dans votre appareil Microsoft Azure StorSimple ont échoué. Cela peut se produire dans un centre de données dans lequel le système de refroidissement cesse de fonctionner, et par conséquent, les deux contrôleurs échouent dans un laps de temps. Selon que le périphérique StorSimple est activé ou désactiver, et que vous utilisiez un 8600 ou un modèle 8100, une autre série d’étapes est requise.

>[AZURE.IMPORTANT] 45 minutes peut prendre 1 heure pour le contrôleur de redémarrer et de récupérer complètement à partir d’une procédure de remplacement contrôleur double. La durée totale de la procédure entière, y compris en joignant les câbles est environ 2,5 heures.

#### <a name="to-replace-both-controller-modules"></a>Pour remplacer les deux modules contrôleur

1. Si le périphérique est désactivé, ignorez cette étape et passez à l’étape suivante. Si l’appareil est activé, désactiver le périphérique.
                                        
    1. Si vous utilisez un modèle 8600, désactiver le boîtier principal première, puis activez désactiver le boîtier EBOD.

    2. Patientez jusqu'à ce que l’appareil est arrêté complètement. Tous les voyants à l’arrière-plan de l’appareil est déconnecté.

2. Supprimez tous les câbles réseau qui sont connectés aux ports de données. Si vous utilisez un modèle 8600, supprimez également les câbles associations de sécurité qui se connectent le boîtier primaire à l’encadrement EBOD.

3. Supprimer les deux contrôleurs de l’appareil StorSimple. Pour plus d’informations, voir [Supprimer un contrôleur](#remove-a-controller).

4. Insérez le remplacement d’usine pour contrôleur 0 tout d’abord, puis insérez contrôleur 1. Pour plus d’informations, voir [Insérer un contrôleur](#insert-a-controller). Cela déclenche la logique de remplacement contrôleur double. Pour plus d’informations, voir [contrôleur double remplacement logique](#dual-controller-replacement-logic).

5. Tandis que la logique de remplacement contrôleur progresse en arrière-plan, vous reconnecter que les câbles. Veillez à vous connecter que les câbles de la même manière qu’ils ont été connectés avant le remplacement. Consultez les instructions détaillées pour votre modèle dans la section de votre appareil câble [d’installer votre appareil StorSimple 8100](storsimple-8100-hardware-installation.md) ou [votre appareil StorSimple 8600](storsimple-8600-hardware-installation.md).

6. Activer le périphérique StorSimple. Si vous utilisez un modèle 8600 :

    1. Vérifiez que le boîtier EBOD est activé sur premier.

    2. Patientez jusqu'à ce que le boîtier EBOD est en cours d’exécution.

    3. Activer le boîtier principal.

    4. Après que le premier contrôleur redémarre et est dans un état correct, le système exécutera.

    >[AZURE.NOTE] Si vous analysez le périphérique via la console série, vous pouvez voir redémarre plusieurs fois pendant que le contrôleur est récupération à partir de la procédure de remplacement. Lorsque le menu de console série s’affiche, vous savez que le remplacement est terminé. Si le menu n’apparaît pas dans les heures 2,5 du démarrage le remplacement contrôleur, veuillez [contacter le support technique Microsoft](storsimple-contact-microsoft-support.md).

## <a name="remove-a-controller"></a>Supprimer un contrôleur

Utilisez la procédure suivante pour supprimer un module contrôleur défectueux à partir de votre appareil StorSimple.

>[AZURE.NOTE] Les illustrations suivantes sont appliquent aux contrôleur 0. Contrôleur 1, ces seraient être inversées.

#### <a name="to-remove-a-controller-module"></a>Pour supprimer un module contrôleur

1. Comprendre le verrou du module entre le pouce et l’index.

2. Ajustez légèrement le pouce et un index pour relâchez le verrou contrôleur.

    ![Verrouillage contrôleur libération](./media/storsimple-controller-replacement/IC741047.png)

    **Figure 2** Verrouillage contrôleur libération

2. Utiliser le verrou comme une poignée le contrôleur de châssis des diapositives.

    ![Contrôleur coulissante châssis](./media/storsimple-controller-replacement/IC741048.png)

    **Figure 3** Glissement du contrôleur de châssis

## <a name="insert-a-controller"></a>Insérer un contrôleur

Utilisez la procédure suivante pour installer un module contrôleur fourni par défaut après avoir supprimé un module défectueux à partir de votre appareil StorSimple.

#### <a name="to-install-a-controller-module"></a>Installer un module contrôleur

1. Vérifiez si les dommages aux connecteurs interface. Ne pas installer le module si une des fiches de la connexion est endommagée ou pliée.

2. Faites glisser le module de contrôleur dans le boîtier alors que le verrou est entièrement publié. 

    ![Contrôleur coulissante dans boîtier](./media/storsimple-controller-replacement/IC741053.png)

    **Figure 4** Contrôleur coulissante dans le boîtier

3. Avec le module contrôleur inséré, commencer la clôture le verrou tout en continuant à distribuer le module contrôleur dans le boîtier. Le verrou s’active guider le contrôleur à l’emplacement souhaité.

    ![Fermeture de verrouillage contrôleur](./media/storsimple-controller-replacement/IC741054.png)

    **Figure 5** Fermer le verrou contrôleur

4. Vous avez terminé lorsque le verrou se mette en place. Le LED **OK** doit apparaître.  

    >[AZURE.NOTE] Il peut prendre jusqu'à 5 minutes pour le contrôleur et la LED activer.

5. Pour vérifier que le remplacement a réussi, dans le portail classique Azure, accédez à **appareils** > **Maintenance** > **État du matériel**et vous assurer que les contrôleur de 0 et 1 sont exact (état est vert).

## <a name="identify-the-active-controller-on-your-device"></a>Identifiez le contrôleur actif sur votre appareil

Il existe de nombreux cas, par exemple appareil première inscription ou contrôleur de remplacement, qui nécessitent localiser le contrôleur actif sur un appareil StorSimple. Le contrôleur actif traite toutes les opérations microprogramme et mise en réseau disque. Vous pouvez utiliser une des méthodes suivantes pour identifier le contrôleur actif :

- [Utiliser le portail classique Azure pour identifier le contrôleur actif](#use-the-azure-classic-portal-to-identify-the-active-controller)

- [Utiliser Windows PowerShell pour StorSimple pour identifier le contrôleur actif](#use-windows-powershell-for-storsimple-to-identify-the-active-controller)

- [Vérifiez le périphérique physique pour identifier le contrôleur actif](#check-the-physical-device-to-identify-the-active-controller)

Chacune de ces procédures est décrite ci-après.

### <a name="use-the-azure-classic-portal-to-identify-the-active-controller"></a>Utiliser le portail classique Azure pour identifier le contrôleur actif

Dans le portail classique Azure, accédez à **appareils** > **Maintenance**, puis faites défiler jusqu'à la section **contrôleurs** . Ici, vous pouvez vérifier quel contrôleur est actif.

![Identifier contrôleur active dans Azure portal classique](./media/storsimple-controller-replacement/IC752072.png)

**Figure 6** Azure portal classique montrant le contrôleur actif

### <a name="use-windows-powershell-for-storsimple-to-identify-the-active-controller"></a>Utiliser Windows PowerShell pour StorSimple pour identifier le contrôleur actif

Lorsque vous accédez à votre appareil via la console série, un message de bannière s’affiche. Le message de bannière contient des informations de base des périphériques tels que le modèle de nom, la version du logiciel installée et état du contrôleur vous accédez à. L’image suivante montre un exemple d’un message de bannière :

![Message de bannière en série](./media/storsimple-controller-replacement/IC741098.png)

**Figure 7** Bannière contrôleur message affichant 0 comme actif

Vous pouvez utiliser le message de bannière pour déterminer si le contrôleur que vous êtes connecté à est actif ou passif.

### <a name="check-the-physical-device-to-identify-the-active-controller"></a>Vérifiez le périphérique physique pour identifier le contrôleur actif

Pour identifier le contrôleur actif sur votre appareil, recherchez la LED bleu au-dessus de la voie 5 de données à l’arrière du boîtier principal.

Si cette LED clignote, le contrôleur est actif et l’autre contrôleur est en mode veille. Utiliser les tâches et les tableaux suivants comme une aide.

![APPAREIL principal encadrement panier avec dataports](./media/storsimple-controller-replacement/IC741055.png)

**Figure 8** Verso boîtier principal avec ports de données et voyants de surveillance

|Étiquette|Description|
|:----|:----------|
|1-6|DONNÉES 0-5 ports réseau|
|7|LED bleu|


## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur le [remplacement des composants matériels StorSimple](storsimple-hardware-component-replacement.md).

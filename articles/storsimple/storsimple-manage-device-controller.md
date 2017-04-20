<properties
   pageTitle="Gérer les contrôleurs de périphérique StorSimple | Microsoft Azure"
   description="Découvrez comment arrêter, redémarrer, arrêter ou réinitialiser votre contrôleurs de périphérique StorSimple."
   services="storsimple"
   documentationCenter=""
   authors="alkohli"
   manager="carmonm"
   editor="" />
<tags
   ms.service="storsimple"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/11/2016"
   ms.author="alkohli" />

# <a name="manage-your-storsimple-device-controllers"></a>Gérer vos contrôleurs appareil StorSimple

## <a name="overview"></a>Vue d’ensemble

Ce didacticiel décrit les différentes opérations pouvant être effectuées sur vos contrôleurs de périphérique StorSimple. Les contrôleurs de votre appareil StorSimple sont contrôleurs redondants (pairs) dans une configuration active-passive. À un moment donné, qu’un seul contrôleur est actif et traite toutes les opérations de disque et réseau. L’autre contrôleur est en mode passive. Si le contrôleur actif échoue, le contrôleur passif devient automatiquement actif.

Ce didacticiel inclut des instructions détaillées pour gérer les contrôleurs de périphérique en utilisant le :

- Section **contrôleurs** de la page de **Maintenance** dans le service Manager StorSimple
- Windows PowerShell pour StorSimple.

Nous vous recommandons que lorsque vous gérez les contrôleurs appareil via le service Manager StorSimple. Si une action peut uniquement être effectuée à l’aide de Windows PowerShell pour StorSimple, le didacticiel effectue une note de celle-ci.

Après la lecture de ce didacticiel, vous pourrez :

- Redémarrer ou arrêter un contrôleur de matériel StorSimple
- Arrêter un périphérique StorSimple
- Restaurez votre appareil StorSimple par défaut


## <a name="restart-or-shut-down-a-single-controller"></a>Redémarrer ou arrêter un contrôleur unique

Contrôleur redémarrage ou arrêt n’est pas obligatoire dans le cadre de fonctionnement normal du système. Opérations d’arrêt d’un contrôleur d’appareil unique sont communes uniquement dans les cas où un composant de matériel du périphérique défectueux doit être remplacé. Redémarrage d’un contrôleur peut-être également être requis dans une situation où les performances sont affectées par l’utilisation excessive de la mémoire ou d’un contrôleur ne fonctionne pas correctement. Vous devrez également redémarrer un contrôleur après le remplacement d’un contrôleur réussie, si vous souhaitez activer et tester le contrôleur de remplacement.

Le redémarrage d’un appareil n’est pas perturbent initiateurs connectés, en supposant que le contrôleur passif est disponible. Si un contrôleur passif n’est pas disponible ou activés désactivé, puis redémarrer le contrôleur actif, peut entraîner l’interruption de service ou d’interruption.

> [AZURE.IMPORTANT]

> - **Un contrôleur en cours d’exécution ne doit jamais être supprimé physiquement en tant que ce serait entraîner une perte de redondance et un risque accru d’indisponibilité.**

> - La procédure suivante s’applique uniquement à l’appareil physique StorSimple. Pour savoir comment démarrer, arrêter et redémarrer le périphérique virtuel, voir [utiliser avec le périphérique virtuel](storsimple-virtual-device-u2.md#work-with-the-storsimple-virtual-device).

Vous pouvez redémarrer ou arrêter un contrôleur d’appareil unique à l’aide du portail classique Azure du service Manager StorSimple ou Windows PowerShell pour StorSimple

Pour gérer vos contrôleurs appareil à partir du portail classique Azure, procédez comme suit.

#### <a name="to-restart-or-shut-down-a-controller-in-classic-portal"></a>Pour redémarrer ou arrêter un contrôleur de portail classique

1. Accédez à **appareils > Maintenance**.

1. Accédez à **État du matériel** et vérifiez que l’état à la fois le contrôleur sur votre appareil d’est **Sain**.

    ![Vérifier le contrôleur de périphérique StorSimple est correct](./media/storsimple-manage-device-controller/IC766017.png)

1. À partir du bas de la page de **Maintenance** , cliquez sur **Gérer les contrôleurs**.

    ![Gérer les contrôleurs de périphérique StorSimple](./media/storsimple-manage-device-controller/IC766018.png)</br>

    >[AZURE.NOTE] Si vous ne voyez pas **Gérer les contrôleurs**, vous devez installer les mises à jour. Pour plus d’informations, voir [mettre à jour votre périphérique StorSimple](storsimple-update-device.md).

1. Dans la boîte de dialogue **Modifier les paramètres du contrôleur** , procédez comme suit :
    1. Dans la liste déroulante **Sélectionner contrôleur** , sélectionnez le contrôleur que vous voulez gérer. Les options sont contrôleur de 0 et 1 contrôleur. Ces contrôleurs sont également identifiés comme actif ou passif.

        >[AZURE.NOTE] Un contrôleur ne peuvent pas être géré s’il est indisponible ou activés désactivé, et il n’apparaît pas dans la liste déroulante.

    2. Dans la liste déroulante **Sélectionner une Action** , choisissez **redémarrer contrôleur** ou **Arrêter contrôleur**.

        ![Redémarrez StorSimple appareil passif contrôleur](./media/storsimple-manage-device-controller/IC766020.png)
    3. Cliquez sur l’icône de vérification ![Icône de vérification](./media/storsimple-manage-device-controller/IC740895.png).

Cela sera redémarrer ou arrêter le contrôleur. Le tableau ci-dessous résume les détails sur ce qui survient selon les sélections que vous avez apportées dans la boîte de dialogue **Modifier les paramètres du contrôleur** .  


|Sélection #|Si vous choisissez de...|Cela se produit.|
|---|---|---|
|1.|Redémarrez le contrôleur passif.|Une tâche est créée pour redémarrer le contrôleur, et vous serez averti une fois que le travail est créé avec succès. Cela ouvrira le redémarrage contrôleur. Vous pouvez surveiller le processus de redémarrage en accédant à **Service > tableau de bord > Afficher les journaux d’opération** puis en filtrant par paramètres spécifiques à votre service.|
|2.|Redémarrez le contrôleur actif.|Vous verrez l’avertissement suivant : « Si vous redémarrez le contrôleur actif, le périphérique échouera sur le contrôleur passif. Voulez-vous continuer ? » </br>Si vous choisissez d’effectuer cette opération, les étapes suivantes seront identiques à ceux qui sont utilisés pour redémarrer le contrôleur passif (voir Sélection 1).|
|3.|Arrêter le contrôleur passif.|Vous verrez le message suivant : « une fois l’arrêt terminée, vous avez besoin d’appuyer sur le bouton power votre manette pour l’activer. Êtes-vous sûr que vous voulez arrêter ce contrôleur ? » </br>Si vous choisissez d’effectuer cette opération, étapes suivantes sera identiques à celle utilisée pour redémarrer le contrôleur passif (voir Sélection 1).|
|4.|Arrêter le contrôleur actif.|Vous verrez le message suivant : « une fois l’arrêt terminée, vous avez besoin d’appuyer sur le bouton power votre manette pour l’activer. Êtes-vous sûr que vous voulez arrêter ce contrôleur ? » </br>Si vous choisissez d’effectuer cette opération, les étapes suivantes seront identiques à ceux qui sont utilisés pour redémarrer le contrôleur passif (voir Sélection 1).|


#### <a name="to-restart-or-shut-down-a-controller-in-windows-powershell-for-storsimple"></a>Pour redémarrer ou arrêter un contrôleur dans Windows PowerShell pour StorSimple
Procédez comme suit pour arrêter ou reprendre un seul contrôleur sur votre appareil StorSimple à partir du portail classique Azure.


1. Accéder à l’appareil à l’aide de la console série ou un présents depuis un ordinateur distant. Connectez-vous au contrôleur de 0 ou 1 contrôleur en suivant les étapes décrites dans [Utiliser PuTTY pour vous connecter à la console série appareil](storsimple-deployment-walkthrough.md#use-putty-to-connect-to-the-device-serial-console).

1. Dans le menu de la console série, choisissez l’option 1, **vous connecter avec un accès complet**.

1. Dans le message de bannière, prenez note du contrôleur vous êtes connecté à (contrôleur de 0 ou 1 contrôleur) et s’il est actif ou le passif contrôleur (en attente).
    - Pour arrêter un contrôleur unique, à l’invite, tapez :

        `Stop-HcsController`

        Cela s’arrête le contrôleur que vous êtes connecté à. Si vous arrêtez le contrôleur actif, puis il échouera sur le contrôleur passif avant son arrêt.
    - Pour reprendre un contrôleur, à l’invite, tapez :

        `Restart-HcsController`

        Cela va redémarrer le contrôleur que vous êtes connecté à. Si vous redémarrez le contrôleur actif, il échouera sur le contrôleur passif avant le redémarrage.


## <a name="shut-down-a-storsimple-device"></a>Arrêter un périphérique StorSimple

Cette section explique comment arrêter cumulé ou un appareil StorSimple échec depuis un ordinateur distant. Un périphérique est désactivé après l’avoir arrêté à la fois les contrôleurs appareil. Un arrêt de l’appareil est terminé lorsque le périphérique est physiquement déplacé ou est hors service.

> [AZURE.IMPORTANT] Avant de fermer le périphérique, vérifiez le fonctionnement des composants de l’appareil. Accédez à **appareils > Maintenance > état du matériel** et vérifiez que l’état des voyants de tous les composants est verte. Uniquement un périphérique exact aura un état vert. Si votre appareil est en cours arrêté pour remplacer un composant ne fonctionne pas correctement, vous verrez un échec (rouge) ou un état qui fonctionnement différemment (jaune) pour l’ou les composants respectif.

#### <a name="to-shut-down-a-storsimple-device"></a>Pour arrêter un périphérique StorSimple

1. Utilisez la procédure [redémarrer ou arrêter un contrôleur](#restart-or-shut-down-a-single-controller) pour identifier et arrêter le contrôleur passif sur votre appareil. Vous pouvez effectuer cette opération dans le portail classique Azure ou dans Windows PowerShell pour StorSimple.
2. Répétez l’étape ci-dessus pour arrêter le contrôleur actif.
3. Maintenant, vous devrez examiner le plan arrière de l’appareil. Une fois que les deux contrôleurs sont complètement arrêtés, les voyants d’état sur les deux contrôleurs doivent clignoter rouge. Si vous voulez désactiver le périphérique pour l’instant, retourner les commutateurs power d’alimentation et Modules ventilation (PCM pour) sur la position désactivé. Ceci doit désactiver le périphérique.


<!--#### To shut down a StorSimple device in Windows PowerShell for StorSimple

1. Connect to the serial console of the StorSimple device by following the steps in [Use PuTTY to connect to the device serial console](storsimple-deployment-walkthrough.md#use-putty-to-connect-to-the-serial-console).

1. In the serial console menu, verify from the banner message that the controller you are connected to is the passive controller. If you are connected to the active controller, disconnect from this controller and connect to the other controller.

1. In the serial console menu, choose option 1, **log in with full access**.

1. At the prompt, type:

    `Stop-HCSController`

    This should shut down the current controller. To verify whether the shutdown has finished, check the back of the device. The controller status LED should be solid red.

1. Repeat steps 1 through 4 to connect to the active controller and then shut it down.

1. After both the controllers are completely shut down, the status LEDs on both should be blinking red. If you need to turn off the device completely at this time, flip the power switches on both Power and Cooling Modules (PCMs) to the OFF position.-->

## <a name="reset-the-device-to-factory-default-settings"></a>Réinitialisation du dispositif aux paramètres par défaut

> [AZURE.IMPORTANT] Si vous avez besoin réinitialiser votre appareil paramètres par défaut, contactez le Support Microsoft. La procédure décrite ci-dessous doit être utilisée uniquement conjointement avec Support Microsoft.

Cette procédure explique comment réinitialiser votre appareil Microsoft Azure StorSimple paramètres par défaut à l’aide de Windows PowerShell pour StorSimple.
Réinitialiser un appareil supprime toutes les données et des paramètres de l’ensemble du cluster par défaut.

Procédez comme suit pour réinitialiser votre périphérique Microsoft Azure StorSimple paramètres par défaut :

### <a name="to-reset-the-device-to-default-settings-in-windows-powershell-for-storsimple"></a>Pour rétablir le périphérique par défaut dans Windows PowerShell pour StorSimple

1. Accéder à l’appareil via sa console en série. Vérifiez le message de bannière pour vous assurer que vous êtes connecté sur le contrôleur Active.

1. Dans le menu de la console série, choisissez l’option 1, **vous connecter avec un accès complet**.

1. À l’invite, tapez la commande suivante pour réinitialiser l’intégralité du cluster, supprimant tous les paramètres de données, les métadonnées et contrôleur :

    `Reset-HcsFactoryDefault`

    Pour rétablir à la place un seul contrôleur, utilisez l’applet de commande [Réinitialiser HcsFactoryDefault](http://technet.microsoft.com/library/dn688132.aspx) avec la `-scope` paramètre.)

    Le système redémarre plusieurs fois. Vous serez informé lorsque la réinitialisation est terminée avec succès. Selon le modèle du système, il peut prendre 45 à 60 minutes pour un périphérique 8100 et 60 et 90 minutes pour un 8600 pour terminer le processus.

    > [AZURE.TIP]

    > - Si vous utilisez mise à jour 1.2 ou version antérieure utiliser la `–SkipFirmwareVersionCheck` paramètre permet d’ignorer la vérification de la version du microprogramme (dans le cas contraire, vous verrez une erreur d’incompatibilité microprogramme : usine par défaut ne peut pas continuer en raison d’une incompatibilité dans les versions de microprogramme. ).

    > - La procédure de réinitialisation usine peut échouer pour les appareils StorSimple qui n’exécutent pas la mise à jour 1 ou 1.1 dans le portail pour le gouvernement et ont effectué un remplacement réussie contrôleur simples ou doubles (avec contrôleur de remplacement qui ont été expédiés avec un logiciel de mise à jour partielle 1). Cela se produit lorsque l’usine réinitialisez image est validée pour la présence d’un fichier SHA1 sur le contrôleur qui n’existe pas de mise à jour partielle 1 logiciel. Si vous voyez que cette usine réinitialiser échec, contactez le Support Microsoft pour vous aider lors des étapes suivantes. Ce problème n’apparaît pas avec les contrôleurs de remplacement qui ont été expédiés en usine mise à jour 1 ou ultérieure du logiciel.


## <a name="questions-and-answers-about-managing-device-controllers"></a>Questions et réponses sur la gestion des contrôleurs de périphérique

Dans cette section, nous avons résumé certaines des questions plus fréquentes concernant la gestion des contrôleurs de périphérique StorSimple.

**QUESTIONS/RÉPONSES.** Que se passe-t-il si les deux le contrôleur sur mon appareil est correct et activés sur et j’ai redémarrer ou arrêter le contrôleur actif ?

**A.** Si les deux le contrôleur sur votre appareil est correct et activés, vous serez invité à confirmer. Vous pouvez choisir de :

- **Redémarrez le contrôleur actif** – vous serez informé que le redémarrage d’un contrôleur active provoquera le périphérique basculer vers le contrôleur passif. Le contrôleur va redémarrer.

- **Arrêter un contrôleur actif** – vous serez averti qu’arrêt d’un contrôleur active générera de temps d’arrêt. Vous avez également besoin d’appuyer sur le bouton power sur le périphérique pour l’activer sur le contrôleur.

**QUESTIONS/RÉPONSES.** Que se passe-t-il si le contrôleur passif sur mon appareil est indisponible ou inactif et j’ai redémarrer ou arrêter le contrôleur actif ?

**A.** Si le contrôleur passif sur votre appareil est indisponible ou activés désactivé et que vous souhaitez :

- **Redémarrez le contrôleur actif** – vous serez averti que poursuivre l’opération provoquera une interruption temporaire du service, et vous serez invité à confirmer.

- **Arrêter un contrôleur actif** – vous serez averti que poursuivre l’opération générera de temps d’arrêt, et que vous devez appuyez sur le bouton power sur un ou deux contrôleurs pour activer le périphérique. Vous demandera confirmation.

**QUESTIONS/RÉPONSES.** Quand ne répond le contrôleur redémarrage ou l’arrêt ne parvient pas à la progression ?

**A.** Redémarrer ou arrêter un contrôleur peut échouer si :

- Une mise à jour de l’appareil est en cours.

- Redémarrage d’un contrôleur est déjà en cours.

- Un arrêt du contrôleur est déjà en cours.

**QUESTIONS/RÉPONSES.** Comment pouvez vous savez pas si un contrôleur a été redémarré ou arrêté ?

**A.** Vous pouvez vérifier l’état du contrôleur dans la page Maintenance. L’état du contrôleur indique si un contrôleur a été redémarré ou arrêté. En outre, la page alertes contiendra une alerte d’information si le contrôleur a été redémarré ou arrêté. Les opérations de redémarrage et d’arrêt du contrôleur sont également enregistrées dans les journaux d’opération. Pour plus d’informations sur les journaux d’opération, accédez à [Afficher les journaux d’opération](storsimple-service-dashboard.md#view-the-operations-logs).

**QUESTIONS/RÉPONSES.** Existe-t-il un impact sur les e/s résulte contrôleur basculement ?

**A.** Les connexions TCP entre les initiateurs et contrôleur active seront ramenées à la suite de basculement contrôleur, mais seront rétablies lorsque le contrôleur passif suppose opération. Il peut y avoir une pause temporaire (moins de 30 secondes) dans l’activité e/s entre initiateurs et le périphérique au cours de cette opération.

**QUESTIONS/RÉPONSES.** Comment revenir mon contrôleur d’après avoir été arrêté et supprimé du service ?

**A.** Pour renvoyer un contrôleur de service, vous devez l’insérer dans le boîtier comme décrit dans la zone [Remplacer un module contrôleur sur votre appareil StorSimple](storsimple-controller-replacement.md).

## <a name="next-steps"></a>Étapes suivantes

- Si vous rencontrez des problèmes avec votre contrôleurs de périphérique StorSimple que vous ne pouvez pas résoudre en appliquant les procédures décrites dans ce didacticiel, [contactez le support technique Microsoft](storsimple-contact-microsoft-support.md).

- Pour en savoir plus sur l’utilisation du service Manager StorSimple, accédez à [utiliser le service Manager StorSimple pour administrer votre appareil StorSimple](storsimple-manager-service-administration.md).

<properties
   pageTitle="Mettre à jour votre périphérique StorSimple | Microsoft Azure"
   description="Explique comment utiliser la fonctionnalité de mise à jour StorSimple pour installer les correctifs et mises à jour en mode normal et maintenance."
   services="storsimple"
   documentationCenter="NA"
   authors="SharS"
   manager="carmonm"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD"
   ms.date="06/28/2016"
   ms.author="v-sharos" />

# <a name="update-your-storsimple-8000-series-device"></a>Mettre à jour votre périphérique série 8000 StorSimple

## <a name="overview"></a>Vue d’ensemble

Les fonctionnalités de mises à jour StorSimple permettent de facilement conserver votre appareil StorSimple à jour. Selon le type de mise à jour, vous pouvez appliquer des mises à jour à l’appareil via le portail Azure classique ou via l’interface Windows PowerShell. Ce didacticiel décrit les types de mise à jour et comment installer chacun d’eux.

Vous pouvez appliquer les deux types de mises à jour de l’appareil : 

- Standard (ou en mode Normal) mises à jour
- Mises à jour du mode maintenance

Vous pouvez installer des mises à jour régulières via le portail classique Azure ou Windows PowerShell ; Toutefois, vous devez utiliser Windows PowerShell pour installer les mises à jour du mode de Maintenance. 

Chaque type de mise à jour est décrite séparément, en dessous.

### <a name="regular-updates"></a>Mises à jour régulières

Mises à jour régulières sont mises à jour sans interruption de service qui peuvent être installés lorsque l’appareil est en mode Normal. Ces mises à jour sont appliquées à chaque contrôleur de périphérique via le site Web Microsoft Update. 

> [AZURE.IMPORTANT] Basculement contrôleur peut se produire pendant le processus de mise à jour. Toutefois, cela n’affecte pas la disponibilité du système ou l’opération.

- Pour plus d’informations sur l’installation des mises à jour régulières via le portail classique Azure, voir [installer mises à jour régulières via la portal(#install-regular-updates-via-the-azure-classic-portal) classique Azure.

- Vous pouvez également installer les mises à jour régulières via Windows PowerShell pour StorSimple. Pour plus d’informations, voir [installer des mises à jour régulières via Windows PowerShell pour StorSimple](#install-regular-updates-via-windows-powershell-for-storsimple).

### <a name="maintenance-mode-updates"></a>Mises à jour du mode maintenance

Mises à jour du Mode maintenance sont mises à jour sans interruption de service tels que les mises à niveau de microprogramme de disque. Ces mises à jour nécessitent le périphérique pour être mis en mode Maintenance. Pour plus d’informations, voir [étape 2 : mode Maintenance Entrez](#step2). Vous ne pouvez pas utiliser le portail classique Azure pour installer les mises à jour du mode de Maintenance. À la place, vous devez utiliser Windows PowerShell pour StorSimple. 

Pour plus d’informations sur l’installation des mises à jour du mode de Maintenance, voir [mises à jour du mode Maintenance installer via Windows PowerShell pour StorSimple](#install-maintenance-mode-updates-via-windows-powershell-for-storsimple).

> [AZURE.IMPORTANT] Mises à jour du mode maintenance doivent être appliquées séparément pour chaque contrôleur. 

## <a name="install-regular-updates-via-the-azure-classic-portal"></a>Installer les mises à jour régulières via le portail classique Azure

Vous pouvez utiliser le portail classique Azure pour appliquer des mises à jour sur votre appareil StorSimple.

[AZURE.INCLUDE [storsimple-install-updates-manually](../../includes/storsimple-install-updates-manually.md)]

## <a name="install-regular-updates-via-windows-powershell-for-storsimple"></a>Installer des mises à jour régulières via Windows PowerShell pour StorSimple

Par ailleurs, vous pouvez utiliser Windows PowerShell pour StorSimple pour appliquer des mises à jour standard (mode Normal).

> [AZURE.IMPORTANT] Bien que vous pouvez installer des mises à jour régulières à l’aide de Windows PowerShell pour StorSimple, nous vous recommandons vivement de que vous installez les mises à jour régulières via le portail classique Azure. À partir de mise à jour 1, vérifications préalables à effectuer avant d’installer des mises à jour à partir du portail. Ces vérifications préalables sont des échecs de préemption et assurer une meilleure expérience. 

[AZURE.INCLUDE [storsimple-install-regular-updates-powershell](../../includes/storsimple-install-regular-updates-powershell.md)]

## <a name="install-maintenance-mode-updates-via-windows-powershell-for-storsimple"></a>Installer des mises à jour du mode Maintenance via Windows PowerShell pour StorSimple

Windows PowerShell pour StorSimple vous permet d’appliquer des mises à jour du mode Maintenance sur votre appareil StorSimple. Toutes les demandes d’e/s sont suspendus dans ce mode. Services tels que la quantité de mémoire vive non volatiles (NVRAM) ou le service de cluster sont également arrêté. Les deux contrôleurs sont redémarrés lorsque vous entrez ou quittez ce mode. Lorsque vous quittez ce mode, tous les services va reprendre et doivent être correct. (Cela peut prendre quelques minutes).

Si vous devez appliquer des mises à jour du mode de Maintenance, vous recevrez une alerte via le portail classique Azure que vous avez mises à jour qui doivent être installés. Cette alerte inclut des instructions pour l’utilisation de Windows PowerShell pour StorSimple pour installer les mises à jour. Une fois que vous mettez à jour votre appareil, utilisez la même procédure pour modifier le périphérique en mode normal. Pour obtenir des instructions détaillées, voir [étape 4 : mode Maintenance quitter](#step4).

> [AZURE.IMPORTANT] 
> 
> - Avant d’entrer en mode Maintenance, vérifiez que les deux contrôleurs appareil sont correct en vérifiant l' **État du matériel** dans la page de **Maintenance** dans le portail classique Azure. Si le contrôleur n’est pas correct, contactez le Support Microsoft pour les étapes suivantes. Pour plus d’informations, accédez à contacter le support technique Microsoft. 
> - Lorsque vous êtes en mode Maintenance, vous devez appliquer la mise à jour tout d’abord sur un seul contrôleur, puis sur l’autre contrôleur.

### <a name="step-1-connect-to-the-serial-console-a-namestep1"></a>Étape 1 : Se connecter à la console série<a name="step1">

Tout d’abord, utilisez une application telle que PuTTY pour accéder à la console en série. La procédure suivante explique comment utiliser PuTTY pour vous connecter à la console série.

[AZURE.INCLUDE [storsimple-use-putty](../../includes/storsimple-use-putty.md)]

### <a name="step-2-enter-maintenance-mode-a-namestep2"></a>Étape 2 : Entrer en mode Maintenance<a name="step2">

Une fois que vous vous connectez à la console, déterminer s’il existe des mises à jour d’installation et passer en mode Maintenance pour les installer.

[AZURE.INCLUDE [storsimple-enter-maintenance-mode](../../includes/storsimple-enter-maintenance-mode.md)]

### <a name="step-3-install-your-updates-a-namestep3"></a>Étape 3 : Installer les mises à jour<a name="step3">

Ensuite, installez les mises à jour.

[AZURE.INCLUDE [storsimple-install-maintenance-mode-updates](../../includes/storsimple-install-maintenance-mode-updates.md)]
 
### <a name="step-4-exit-maintenance-mode-a-namestep4"></a>Étape 4 : Mode de Maintenance de quitter<a name="step4">

Pour finir, quitter le mode Maintenance.

[AZURE.INCLUDE [storsimple-exit-maintenance-mode](../../includes/storsimple-exit-maintenance-mode.md)]

## <a name="install-hotfixes-via-windows-powershell-for-storsimple"></a>Installer les correctifs par le biais de Windows PowerShell pour StorSimple

Contrairement aux mises à jour de Microsoft Azure StorSimple, correctifs sont installés à partir d’un dossier partagé. Comme avec les mises à jour, il existe deux types de correctifs : 

- Correctifs normal 
- Correctifs de mode maintenance  

Les procédures suivantes expliquent comment utiliser Windows PowerShell pour StorSimple pour installer standard et correctifs de mode de Maintenance.

[AZURE.INCLUDE [storsimple-install-regular-hotfixes](../../includes/storsimple-install-regular-hotfixes.md)]

[AZURE.INCLUDE [storsimple-install-maintenance-mode-hotfixes](../../includes/storsimple-install-maintenance-mode-hotfixes.md)]

## <a name="what-happens-to-updates-if-you-perform-a-factory-reset-of-the-device"></a>Qu’arrive-t-il aux mises à jour si vous exécutez une réinitialisation usine du périphérique ?

Si un appareil est réinitialisé paramètres par défaut, toutes les mises à jour sont perdues. Une fois que l’appareil usine réinitialiser est enregistré et configuré, vous avez besoin installer manuellement des mises à jour via le portail classique Azure et/ou Windows PowerShell pour StorSimple. Pour plus d’informations sur usine réinitialiser, voir [Réinitialiser le périphérique paramètres par défaut](storsimple-manage-device-controller.md#reset-the-device-to-factory-default-settings).

## <a name="next-steps"></a>Étapes suivantes

- En savoir plus sur [l’utilisation de Windows PowerShell pour StorSimple administrer votre appareil StorSimple](storsimple-windows-powershell-administration.md).
- En savoir plus sur [l’utilisation du service Manager StorSimple pour administrer votre périphérique StorSimple](storsimple-manager-service-administration.md).

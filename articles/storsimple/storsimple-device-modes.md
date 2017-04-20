<properties 
   pageTitle="Modifier le mode de périphérique StorSimple | Microsoft Azure"
   description="Décrit les modes de périphérique StorSimple et explique comment utiliser Windows PowerShell pour StorSimple pour changer le mode d’appareil."
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
   ms.date="06/17/2016"
   ms.author="alkohli" />

# <a name="change-the-device-mode-on-your-storsimple-device"></a>Modifier le mode d’appareil sur votre appareil StorSimple

Cet article fournit une brève description des modes dans lesquels votre appareil StorSimple peut fonctionner. Votre appareil StorSimple peut fonctionner en trois modes : normal, maintenance et récupération. 

Après la lecture de cet article, vous saurez :

- Quels les modes StorSimple appareil sont
- Comment identifier le mode de l’appareil StorSimple se trouve dans
- Comment changer de normal en mode maintenance et *vice versa*


Les tâches de gestion ci-dessus peuvent uniquement être effectuées via l’interface Windows PowerShell de votre appareil StorSimple.

## <a name="about-storsimple-device-modes"></a>Informations sur les modes d’appareil StorSimple

Votre appareil StorSimple peut fonctionner en mode normal, maintenance ou la récupération. Chacun de ces modes est brièvement décrite ci-dessous.

### <a name="normal-mode"></a>En mode normal

Il s’agit du mode de fonctionnement normal pour un appareil StorSimple préconfiguré. Par défaut, votre appareil doit être en mode normal.

### <a name="maintenance-mode"></a>Mode maintenance

Le périphérique StorSimple peut parfois doivent être placés en mode maintenance. Ce mode permet d’effectuer la maintenance de l’appareil et installer des mises à jour sans interruption de service, tel que ceux liés au microprogramme de disque.

Vous pouvez placer le système en mode maintenance uniquement par le biais de Windows PowerShell pour StorSimple. Toutes les demandes d’e/s sont suspendus dans ce mode. Services tels que la quantité de mémoire vive non volatiles (NVRAM) ou le service de cluster sont également arrêté. Les deux contrôleurs sont redémarrés lorsque vous entrez ou quittez ce mode. Lorsque vous quittez le mode de maintenance, tous les services va reprendre et doivent être correct. Cela peut prendre quelques minutes.

>[AZURE.NOTE]Mode maintenance **est uniquement prise en charge sur un appareil fonctionnent correctement. Il n’est pas pris en charge sur un appareil dans lequel une ou les deux le contrôleur de ne fonctionnent pas.**
</br>

### <a name="recovery-mode"></a>Mode de récupération

Mode de récupération peut être décrite « Windows en mode sans échec avec prise en charge de réseau ». Mode de récupération fait appel à l’équipe de Support Microsoft et permet d’effectuer des diagnostics sur le système. L’objectif principal du mode de récupération consiste à récupérer les journaux système.

Si votre système passe en mode de récupération, vous devez contacter le Support Microsoft pour obtenir la procédure suivante. Pour plus d’informations, accédez à [Contacter le support technique Microsoft](storsimple-contact-microsoft-support.md).

>[AZURE.NOTE] **Vous ne pouvez pas placer l’appareil en mode de récupération. Si l’appareil est dans un état incorrect, en mode de récupération tente d’obtenir le périphérique à l’état dans lequel personnel de Support Microsoft permet de détecter.**

## <a name="determine-storsimple-device-mode"></a>Déterminer le mode de périphérique StorSimple

#### <a name="to-determine-the-current-device-mode"></a>Pour déterminer le mode de périphérique en cours

1. Connectez-vous à la console série appareil en suivant les étapes décrites dans [Utiliser PuTTY pour vous connecter à la console série appareil](storsimple-deployment-walkthrough.md#use-putty-to-connect-to-the-device-serial-console).

2. Examinez le message de bannière dans le menu de la console série du périphérique. Ce message indique explicitement si l’appareil est en mode maintenance ou de récupération. Si le message ne contient pas d’informations spécifiques relatifs au mode système, l’appareil est en mode normal.

## <a name="change-the-storsimple-device-mode"></a>Modifier le mode de périphérique StorSimple 

Vous pouvez placer l’appareil StorSimple en mode maintenance (à partir du mode normal) pour effectuer la maintenance ou installer des mises à jour du mode de maintenance. Procédez comme suit pour entrer ou quitter le mode maintenance.

> [AZURE.IMPORTANT] Avant d’entrer en mode maintenance, vérifiez que les deux contrôleurs appareil sont correct en accédant à l' **État du matériel** dans la page de **Maintenance** dans le portail classique Azure. Si un ou deux contrôleurs ne sont pas correct, contactez le Support Microsoft pour les étapes suivantes. Pour plus d’informations, accédez à [Contacter le support technique Microsoft](storsimple-contact-microsoft-support.md).

#### <a name="to-enter-maintenance-mode"></a>Pour passer en mode maintenance

1. Connectez-vous à la console série appareil en suivant les étapes décrites dans [Utiliser PuTTY pour vous connecter à la console série appareil](storsimple-deployment-walkthrough.md#use-putty-to-connect-to-the-device-serial-console).

2. Dans le menu de la console série, choisissez l’option 1, **vous connecter avec un accès complet**. Lorsque vous y êtes invité, fournir le **mot de passe administrateur**. Le mot de passe : `Password1`.

3. À l’invite de commandes, tapez 

    `Enter-HcsMaintenanceMode`

4. Vous verrez un message d’avertissement vous informant que mode maintenance s’interrompre toutes les requêtes serveur la connexion au portail classique Azure et vous serez invité à confirmer. Tapez **Y** pour passer en mode maintenance.

5. Les deux contrôleurs va redémarrer. Lorsque le redémarrage est terminé, un autre message s’affiche indiquant que l’appareil est en mode maintenance.


#### <a name="to-exit-maintenance-mode"></a>Pour quitter le mode maintenance

1. Connectez-vous à la console série appareil. Vérifiez à partir du message de bannière votre appareil est en mode maintenance.

2. À l’invite de commandes, tapez :

    `Exit-HcsMaintenanceMode`

3. Un message d’avertissement et un message de confirmation seront affiche. Tapez **Y** pour quitter le mode maintenance.

4. Les deux contrôleurs va redémarrer. Lorsque le redémarrage est terminé, un autre message s’affiche indiquant que l’appareil est en mode normal.


## <a name="next-steps"></a>Étapes suivantes

Découvrez comment [appliquer des mises à jour en mode normal et la maintenance](storsimple-update-device.md) sur votre appareil StorSimple.


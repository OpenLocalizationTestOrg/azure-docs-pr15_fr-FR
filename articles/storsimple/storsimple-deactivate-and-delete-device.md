<properties 
   pageTitle="Désactiver et supprimer un appareil StorSimple | Microsoft Azure"
   description="Explique comment supprimer StorSimple appareil de service par tout d’abord la désactivation, puis leur suppression."
   services="storsimple"
   documentationCenter=""
   authors="SharS"
   manager="carmonm"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/18/2016"
   ms.author="anoobbacker" />

# <a name="deactivate-and-delete-a-storsimple-device"></a>Désactiver et supprimer un appareil StorSimple

## <a name="overview"></a>Vue d’ensemble

Vous souhaiterez peut-être mettre un périphérique StorSimple hors service (par exemple, si vous remplacez ou la mise à niveau de votre appareil ou si vous n’utilisez plus StorSimple). Si c’est le cas, vous devez désactiver le périphérique avant que vous pouvez le supprimer. Désactivation interrompt la connexion entre le périphérique et le service Manager StorSimple correspondant. Ce didacticiel explique comment supprimer un appareil StorSimple service en commençant par la désactiver, puis en le supprimant. 

Lorsque vous désactivez un périphérique, toutes les données qui ont été stockées localement sur le périphérique ne seront plus accessibles. Uniquement les données associées à l’appareil qui ont été stockée dans le cloud peuvent être récupérées.  

>[AZURE.WARNING] Désactivation est une opération définitive et ne peut pas être annulée. Un périphérique désactivé ne peut pas être enregistré par le service Manager StorSimple, sauf si elle est réinitialisez tout d’abord par l’usine. 
>
>Usine réinitialiser processus supprime toutes les données qui ont été stockées localement sur votre appareil. Par conséquent, il est essentiel de prendre un instantané de cloud de toutes vos données avant de le désactiver un appareil. Cela vous permettra à récupérer toutes les données ultérieurement.

Ce didacticiel décrit comment :

- Désactiver un périphérique et supprimer des données
- Désactiver un périphérique, mais de conserver les données

Cette rubrique explique également comment désactivation et suppression fonctionne sur un périphérique virtuel StorSimple.

>[AZURE.NOTE] Avant de désactiver un périphérique StorSimple physique ou virtuel, veillez à arrêter ou supprimer des clients et des hôtes qui dépendent de votre appareil.

## <a name="deactivate-and-delete-data"></a>Désactiver et supprimer des données

Si vous êtes intéressé par la suppression du périphérique complètement et ne souhaitez pas conserver les données sur le périphérique, puis effectuez les étapes suivantes.

#### <a name="to-deactivate-the-device-and-delete-the-data"></a>Pour désactiver le périphérique et supprimer les données  

1. Avant la désactivation d’un appareil, vous devez supprimer tout le volume conteneurs (et les volumes) associée à l’appareil. Vous pouvez supprimer des conteneurs de volume uniquement une fois que vous avez supprimé les sauvegardes associées.

2. Désactiver le périphérique comme suit :

    1. Dans la page **périphériques** de service Manager StorSimple, sélectionnez le périphérique que vous souhaitez désactiver et, dans la partie inférieure de la page, cliquez sur **désactiver**.

    2. Un message de confirmation s’affiche. Cliquez sur **Oui** pour continuer. Le processus de désactiver démarre et prendre quelques minutes.

3. Après la désactivation, vous pouvez supprimer complètement l’appareil. Suppression d’un appareil supprime de la liste des appareils connectés au service. Le service puis ne peut plus gérer le périphérique supprimé. Procédez comme suit pour supprimer l’appareil :

    1. Dans la page **périphériques** de service Manager StorSimple, sélectionnez un périphérique désactivé que vous souhaitez supprimer.

    2. Dans la partie inférieure de la page, cliquez sur **Supprimer**.

    3. Vous demandera confirmation. Cliquez sur **Oui** pour continuer.

    Il peut prendre quelques minutes pour le périphérique à supprimer.

## <a name="deactivate-and-retain-data"></a>Désactiver et conserver les données

Si vous intéressent la suppression du périphérique mais que vous souhaitez conserver les données, puis effectuez les étapes suivantes.

####<a name="to-deactivate-a-device-and-retain-the-data"></a>Pour désactiver un périphérique, mais de conserver les données 

1. Désactiver le périphérique. Tous les conteneurs de volume et les instantanés de l’appareil sont conservés.

    1. Dans la page **périphériques** de service Manager StorSimple, sélectionnez le périphérique que vous souhaitez désactiver et, dans la partie inférieure de la page, cliquez sur **désactiver**.

    2. Un message de confirmation s’affiche. Cliquez sur **Oui** pour continuer. Le processus de désactiver démarre et prendre quelques minutes.

2. Vous pouvez maintenant basculer vers les conteneurs de volume et les instantanés associés. Pour plus d’informations, accédez à [basculement et récupération d’urgence pour votre appareil StorSimple](storsimple-device-failover-disaster-recovery.md).

3. Après avoir désactivation et basculement, vous pouvez supprimer complètement l’appareil. Suppression d’un appareil supprime de la liste des appareils connectés au service. Le service puis ne peut plus gérer le périphérique supprimé. Procédez comme suit pour supprimer l’appareil :
 
    1. Dans la page **périphériques** de service Manager StorSimple, sélectionnez un périphérique désactivé que vous souhaitez supprimer.

    2. Dans la partie inférieure de la page, cliquez sur **Supprimer**.

    3. Vous demandera confirmation. Cliquez sur **Oui** pour continuer.

    Il peut prendre quelques minutes pour le périphérique à supprimer.

## <a name="deactivate-and-delete-a-virtual-device"></a>Désactiver et supprimer un périphérique virtuel

Pour un périphérique virtuel StorSimple, désactivation libère la machine virtuelle. Vous pouvez ensuite supprimer la machine virtuelle et les ressources créés lorsqu’il a été mis en service. Une fois le périphérique virtuel est désactivé, il ne peut pas être restauré à son état précédent. 

Désactivation des résultats dans les actions suivantes :

- Le périphérique virtuel StorSimple est supprimé.

- L’OSDisk et des disques de données créé pour le périphérique virtuel StorSimple sont supprimés.

- Le Service hébergé et le réseau virtuel qui ont été créées au cours de la mise en service sont conservés. Si vous n’utilisez pas ces entités, vous devez les supprimer manuellement.

- Instantanés cloud créés par le périphérique virtuel StorSimple sont conservés.

## <a name="next-steps"></a>Étapes suivantes
- Pour restaurer le périphérique désactivé par défaut, accédez à [Réinitialiser le périphérique paramètres par défaut](storsimple-manage-device-controller.md#reset-the-device-to-factory-default-settings).

- Assistance technique, [contactez le support technique Microsoft](storsimple-contact-microsoft-support.md).

- Pour plus d’informations sur l’utilisation du service Manager StorSimple, accédez à [utiliser le service Manager StorSimple pour administrer votre périphérique StorSimple](storsimple-manager-service-administration.md). 

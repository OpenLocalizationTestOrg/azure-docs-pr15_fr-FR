<properties 
   pageTitle="Remplacer le châssis sur un appareil StorSimple | Microsoft Azure"
   description="Explique comment supprimer et le remplacer le châssis pour votre StorSimple primaire encadrement ou encadrement EBOD."
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

# <a name="replace-the-chassis-on-your-storsimple-device"></a>Remplacer le châssis sur votre appareil StorSimple

## <a name="overview"></a>Vue d’ensemble

Ce didacticiel explique la procédure pour supprimer et remplacer un châssis dans un périphérique série 8000 StorSimple. Le modèle 8100 StorSimple est un appareil boîtier unique (un seul du boîtier), tandis que le 8600 est un appareil encadrement double (deux du boîtier). Pour un modèle 8600, il existe potentiellement deux châssis qui peuvent échouer dans l’appareil : le châssis pour le boîtier principal ou le châssis pour le boîtier EBOD.

Dans les deux cas, le châssis de remplacement qui est fourni par Microsoft est vide. Aucune alimentation et refroidissement Modules (PCM) pour, modules contrôleur, état unie disques (SSDs), lecteurs de disques durs (disques durs) ou des modules EBOD ne seront inclus.

>[AZURE.IMPORTANT] Avant de retrait et le remplacement du châssis, passez en revue les informations de sécurité dans le [remplacement des composants matériels StorSimple](storsimple-hardware-component-replacement.md).

## <a name="remove-the-chassis"></a>Supprimer le châssis

Procédez comme suit pour supprimer le châssis sur votre appareil StorSimple.

#### <a name="to-remove-a-chassis"></a>Pour supprimer un châssis

1. Assurez-vous que l’appareil StorSimple est fermée et déconnecté de toutes les sources d’alimentation.

2. Supprimer tous les réseau et câbles associations de sécurité, le cas échéant.

3. Supprimer l’unité de rack.

4. Supprimez tous les lecteurs et notez les emplacements à partir de laquelle ils sont supprimés. Pour plus d’informations, voir [Supprimer le lecteur de disque](storsimple-disk-drive-replacement.md#remove-the-disk-drive).

5. Sur le boîtier EBOD (s’il s’agit le châssis qui a échoué), supprimez les modules de contrôleur EBOD. Pour plus d’informations, voir [Supprimer un contrôleur de EBOD](storsimple-ebod-controller-replacement.md#remove-an-ebod-controller). 

    Sur le boîtier principal (s’il s’agit le châssis qui a échoué), supprimez les contrôleurs et notez les emplacements à partir de laquelle ils sont supprimés. Pour plus d’informations, voir [Supprimer un contrôleur](storsimple-controller-replacement.md#remove-a-controller).

## <a name="install-the-chassis"></a>Installer le châssis

Procédez comme suit pour installer le châssis sur votre appareil StorSimple.

#### <a name="to-install-a-chassis"></a>Pour installer un châssis

1. Monter le châssis en rack. Pour plus d’informations, voir [votre appareil 8100 StorSimple de montage en Rack](storsimple-8100-hardware-installation.md#rack-mount-your-storsimple-8100-device) ou [votre appareil 8600 StorSimple de montage en Rack](storsimple-8600-hardware-installation.md#rack-mount-your-storsimple-8600-device).

2. Après que le châssis montage en rack, installez les modules contrôleur dans la même position qu’ils ont été précédemment installés dans.

3. Installer les lecteurs dans les mêmes positions et qu’ils ont été précédemment installés dans des emplacements.

    >[AZURE.NOTE] Nous vous recommandons que vous installez les SSDs dans les emplacements tout d’abord, puis les disques durs.

2. Avec le périphérique monté en rack et les composants sont installés, vous connecter votre périphérique aux sources d’alimentation et activer l’appareil. Pour plus d’informations, voir [câble votre appareil StorSimple 8100](storsimple-8100-hardware-installation.md#cable-your-storsimple-8100-device) ou [votre appareil StorSimple 8600](storsimple-8600-hardware-installation.md#cable-your-storsimple-8600-device).

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur le [remplacement des composants matériels StorSimple](storsimple-hardware-component-replacement.md).


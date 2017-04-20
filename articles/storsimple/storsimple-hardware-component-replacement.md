<properties 
   pageTitle="Remplacement des composants matériels StorSimple | Microsoft Azure"
   description="Décrit comment remplacer en toute sécurité le PCMs, batterie, contrôleur modules, EBOD contrôleurs, disques et châssis d’un périphérique StorSimple."
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
   ms.date="10/11/2016"
   ms.author="alkohli" />

# <a name="storsimple-hardware-component-replacement"></a>Remplacement des composants matériels StorSimple

## <a name="overview"></a>Vue d’ensemble

Les didacticiels de remplacement de composant matériel décrivent les composants matériels de votre appareil série Microsoft Azure StorSimple 8000 et les étapes nécessaires à supprimer et les remplacer. Cet article décrit les icônes de sécurité fournit des pointeurs vers les didacticiels détaillées et répertorie les composants remplacés.

>[AZURE.IMPORTANT] Avant d’essayer de supprimer ou remplacer n’importe quel composant StorSimple, vérifiez que vous passez en revue les [conventions des icônes de sécurité des messages](#safety-icon-conventions) et autres [mesures de sécurité](storsimple-safety.md).
 
### <a name="safety-icon-conventions"></a>Conventions des icônes de sécurité

Le tableau suivant décrit les icônes de sécurité des messages dans les didacticiels. Faites attention à ces icônes de sécurité tout au long de la procédure pour supprimer et remplacer des composants de l’appareil.

| Icône | Texte | Informations supplémentaires |
|:---- |:---- |:-----------|
|![Icône d’avertissement](./media/storsimple-hardware-component-replacement/Warning.png)| **DANGER !** | Indique une situation dangereuse que si ne pas évitée, provoquera corporels graves. Ce mot signal est limité aux situations extrêmes.|
|![Icône d’avertissement](./media/storsimple-hardware-component-replacement/Warning.png)| **AVERTISSEMENT !** | Indique une situation dangereuse qui, si ne pas évitée, peut entraîner corporels graves.|
|![Icône d’avertissement](./media/storsimple-hardware-component-replacement/Caution.png)| **ATTENTION !** |Indique une situation dangereuse que si ne pas évitée, peut entraîner un préjudice mineur ou modéré.|
|![Icône de notification](./media/storsimple-hardware-component-replacement/NoticeIcon.png)| **REMARQUE :** | Indique les informations considérés comme important, mais non liées à risque.|
|![Icône électrocution](./media/storsimple-hardware-component-replacement/Electric.png) | **Électrocution risque** | Indique haute tension.|
|![Icône du poids](./media/storsimple-hardware-component-replacement/Weight.png)| **Poids**| |
|![Aucune icône d’éléments dont la maintenance de l’utilisateur](./media/storsimple-hardware-component-replacement/NoUserServiceableParts.png)| **Aucun éléments dont la maintenance utilisateur** | Ne peuvent pas accéder à moins d’avoir correctement formé.|
|![Icône d’instructions de lecture](./media/storsimple-hardware-component-replacement/ReadInstructions.png)|**Lire en premier de toutes les Instructions**| |
|![Icône de risque de Conseil](./media/storsimple-hardware-component-replacement/TipHazard.png)|**Conseil risque**| |

### <a name="before-you-begin"></a>Avant de commencer

Familiarisez-vous avec les informations de sécurité des messages sur votre appareil et la sécurité les icônes utilisés dans ce didacticiel. Pour plus d’informations, accédez à [installer en toute sécurité et à utiliser votre appareil StorSimple](storsimple-safety.md) . Veillez à prendre les [précautions à prendre](storsimple-safety.md#handling-precautions) avant d’avoir géré votre appareil StorSimple. 

Avant d’essayer de remplacer un composant, tenez compte des informations suivantes.

![Icône d’avertissement](./media/storsimple-hardware-component-replacement/Warning.png) ![icône électrocution](./media/storsimple-hardware-component-replacement/Electric.png) **Avertissement !** 

- Connexion à la terre correctement à l’aide un électrostatiques ou un fond antistatique lors de la gestion des modules et les composants de votre périphérique StorSimple.

- Ne touchez pas les circuits. Utilisez les poignées fournies et les guides pendant le traitement des composants qui peuvent avoir exposé circuits.

![Icône d’avertissement](./media/storsimple-hardware-component-replacement/Warning.png) ![remarquer icône](./media/storsimple-hardware-component-replacement/NoticeIcon.png) **avis :**

Lorsque vous remplacez un module, **jamais laisser un emplacement vide dans l’arrière du boîtier**. Obtenir un module vide ou remplacement avant de supprimer le composant de problème.

## <a name="hardware-component-replacement-procedures"></a>Procédures de remplacement de composant matériel

Votre appareil série 8000 StorSimple se compose de plusieurs modules plug-in dans le moniteur principal et/ou les pièces jointes EBOD. La 8100 contient un seul boîtier principal, tandis que le 8600 est un appareil encadrement double avec un boîtier principal et un encadrement EBOD.

Principaux composants matériels dans votre appareil sont indiquées dans les tableaux suivants. Cliquez sur le lien dans la colonne de la **procédure de remplacement** pour atteindre le didacticiel associé.

|Composants|# Présenter|Module plug-in ?|Procédure de remplacement
|:---------|:--------|:--------------|:---------------------|
| Châssis|1|N°|[Remplacer le châssis sur votre appareil StorSimple](storsimple-chassis-replacement.md) |
|Contrôleurs principaux|2|Oui| [Remplacement d’un module contrôleur sur votre appareil StorSimple](storsimple-controller-replacement.md) |
|764W Power et Modules (PCM pour) ventilation|2|Oui| [Remplacer un Power et Module sur votre appareil StorSimple](storsimple-power-cooling-module-replacement.md) |
|Batterie de secours|2|Oui| [Remplacez le module de batterie de secours sur votre appareil StorSimple](storsimple-battery-replacement.md) |
|Lecteurs de disques|12|Oui| [Remplacer un lecteur de disque sur votre appareil StorSimple](storsimple-disk-drive-replacement.md) |

**Tableau 1** Composants matériels du boîtier principal

Le boîtier principal et le boîtier EBOD diffèrent dans leurs modules e/s. En outre, les PCMs ont puissance différente. Les PCMs dans le boîtier principal sont 764 W, tandis que celles de l’encadrement EBOD sont 580 W. Les PCMs dans le boîtier principal également contient un module de batterie de secours.

|Composants|# Présenter|Module plug-in ?| Procédure de remplacement
|:---------|:--------|:--------------|:---------------------|
|Châssis|1|N°| [Remplacer le châssis sur votre appareil StorSimple](storsimple-chassis-replacement.md) |
|Contrôleurs EBOD|2|Oui| [Remplacer un contrôleur EBOD sur votre appareil StorSimple](storsimple-ebod-controller-replacement.md) |
|Power 580 w et Modules (PCM pour) ventilation|2|Oui| [Remplacer un Power et Module sur votre appareil StorSimple](storsimple-power-cooling-module-replacement.md) |
|Lecteurs de disques|12|Oui| [Remplacer un lecteur de disque sur votre appareil StorSimple](storsimple-disk-drive-replacement.md) |

**Tableau 2** Composants matériels du boîtier EBOD

Les modules plug-in sur l’appareil sont mises en surbrillance dans suivante avant et arrière diagrammes. Vous pouvez utiliser ces diagrammes pour déterminer l’emplacement de différents modules plug-in si un remplacement est requis. Le diagramme avant affiche les lecteurs de disques et les diagrammes arrière de l’encadrement EBOD et le diaporama principal encadrement modules plug-in.

![Frontplane d’appareil avec disques](./media/storsimple-hardware-component-replacement/IC741028.png)

**Figure 1** Avant de l’appareil

|Étiquette|Description|
|:----|:----------|
|0 - 11|Lecteurs de disques (total de 12)|

Le boîtier principal et le boîtier EBOD ont des modules lecteur carrier. Le châssis héberge douze 3,5" disques organisés dans un format de 3 par 4.

![Carte d’insertion de modules du boîtier principal appareil](./media/storsimple-hardware-component-replacement/IC740994.png)

**Figure 2** À l’arrière du boîtier principal

|Étiquette|Description|
|:----|:----------|
|1|PCM 0|
|2|PCM 1|
|3|Contrôleur de 0|
|4|Contrôleur de 1|

![Carte d’insertion de modules plug-in du boîtier EBOD appareil](./media/storsimple-hardware-component-replacement/IC769599.png)

**Figure 3** À l’arrière du boîtier EBOD

|Étiquette|Description|
|:----|:----------|
|1|PCM 0|
|2|PCM 1|
|3|Contrôleur EBOD 0|
|4|Contrôleur de EBOD 1|

## <a name="field-replaceable-units"></a>Remplaçables

Les unités suivantes champ remplaçable (unités) sont disponibles pour votre appareil StorSimple :

- Châssis (y compris le panneau d’activités intégrées)

- 764 W CA PCM

- 580 W CA PCM

- Disque dur avec module carrier lecteur

- Module contrôleur

- Module contrôleur EBOD

- Module de sauvegarde de batterie

- Kit de rails de montage en rack

Veuillez [contacter le support technique Microsoft](storsimple-contact-microsoft-support.md) pour commander un de ces unités de remplacement.

## <a name="next-steps"></a>Étapes suivantes

Passez en revue toutes les [informations de sécurité](storsimple-safety.md) avant d’essayer de remplacer un composant matériel StorSimple.

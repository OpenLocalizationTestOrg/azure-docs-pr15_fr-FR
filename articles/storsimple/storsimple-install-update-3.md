<properties
   pageTitle="Installer la mise à jour 3 sur votre appareil StorSimple | Microsoft Azure"
   description="Explique comment installer StorSimple 8000 série 3 sur votre appareil série 8000 StorSimple."
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
   ms.date="10/05/2016"
   ms.author="alkohli" />

# <a name="install-update-3-on-your-storsimple-device"></a>Installer la mise à jour 3 sur votre appareil StorSimple

## <a name="overview"></a>Vue d’ensemble

Ce didacticiel décrit l’installation de mise à jour 3 sur un appareil StorSimple exécute une version antérieure du logiciel via le portail classique Azure et en utilisant la méthode correctif. La méthode correctif est utilisée lorsqu’une passerelle est configurée sur une interface réseau différente de données 0 du périphérique StorSimple et que vous tentez de mettre à jour à partir d’une version du logiciel 1 avant mise à jour.

Mise à jour 3 inclut le logiciel de périphérique, pilote LSI et microprogramme, Storport et Spaceport mis à jour. Si la mise à jour à partir de la mise à jour 2 ou une version antérieure, vous serez également nécessaire pour appliquer iSCSI, WMI et dans certains cas, les mises à jour de microprogramme de disque. Le logiciel du périphérique, WMI, iSCSI, LSI du pilote, Spaceport et Storport correctifs sont mises à jour sans interruption de service et peuvent être appliqués via le portail classique Azure. Les mises à jour de microprogramme de disque sont mises à jour sans interruption de service et ne peuvent être appliqués via l’interface Windows PowerShell du périphérique. 

> [AZURE.IMPORTANT]

> - Un ensemble de vérifications préalables manuels et automatiques sont effectuées avant l’installation pour déterminer l’état de l’appareil en termes de matériel état et la connectivité réseau. Ces vérifications préalables sont effectuées uniquement si vous appliquez les mises à jour à partir du portail classique Azure.
> - Nous vous conseillons d’installer les mises à jour du pilote et le logiciel via le portail classique Azure. Vous ne devez pas dépasser à l’interface Windows PowerShell du périphérique (pour installer les mises à jour) si la vérification de la mise à jour partielle passerelle échoue dans le portail. En fonction de la version que vous mettez à jour à partir de, les mises à jour peuvent prendre 1,5-2,5 heures pour installer. Les mises à jour du mode maintenance doivent être installés via l’interface Windows PowerShell du périphérique. Mises à jour du mode maintenance sont mises à jour sans interruption de service, il seront traduit par un temps vers le bas pour votre appareil.
> - Si le Gestionnaire d’instantanés StorSimple facultatif en cours d’exécution, vérifiez que vous avez mis à niveau votre version du Gestionnaire de capture instantanée à 2 mise à jour avant la mise à jour de l’appareil.

[AZURE.INCLUDE [storsimple-preparing-for-update](../../includes/storsimple-preparing-for-updates.md)]

## <a name="install-update-3-via-the-azure-classic-portal"></a>Installer la mise à jour 3 via le portail classique Azure

Procédez comme suit pour mettre à jour votre appareil à [mettre à jour 3](storsimple-update3-release-notes.md).


> [AZURE.NOTE]
Si vous appliquez une mise à jour 2 ou version ultérieure (y compris 2.1 mise à jour), Microsoft sera en mesure d’extraire des informations de diagnostic supplémentaires à partir de l’appareil. Par conséquent, lorsque notre équipe opérations identifie les appareils que vous rencontrent des problèmes, nous sont mieux équipés pour recueillir des informations à partir de l’appareil et diagnostiquer les problèmes. En acceptant mise à jour 2 ou version ultérieure, vous permettez de fournir un ce support dynamique.

[AZURE.INCLUDE [storsimple-install-update2-via-portal](../../includes/storsimple-install-update2-via-portal.md)]

12. Vérifiez que votre appareil exécute **StorSimple 8000 série mise à jour 3 (6.3.9600.17759)**. La **dernière mise à jour date** doivent également être modifiées. 

    Si vous mettez à jour à partir d’une version antérieure à 2 mise à jour, vous verrez également que les mises à jour du mode Maintenance sont disponibles (ce message peut continuer à afficher dans un délai de 24 heures après avoir installé les mises à jour).

    Mises à jour du mode maintenance sont mises à jour sans interruption de service qui résultat dans le temps d’arrêt de l’appareil et ne peuvent être appliqués via l’interface Windows PowerShell de votre appareil. Dans certains cas, lorsque vous exécutez la mise à jour 1.2, votre microprogramme de disque peut-être déjà à jour, auquel cas vous n’avez pas besoin d’installer des mises à jour du mode de maintenance.

    Si vous êtes mise à jour à partir de la mise à jour 2 ou version ultérieure, votre appareil doit maintenant être à jour. Vous pouvez ignorer les étapes restantes.

13. Téléchargez les mises à jour du mode maintenance à l’aide de la procédure décrite dans [pour télécharger des correctifs](#to-download-hotfixes) pour rechercher et télécharger KB3121899, quelles installations disque microprogramme mises à jour (les autres mises à jour doivent déjà être installés maintenant).

13. Suivez la procédure décrite dans [installer et vérifier les correctifs en mode maintenance](#to-install-and-verify-maintenance-mode-hotfixes) pour installer les mises à jour du mode de maintenance. 

  

## <a name="install-update-3-as-a-hotfix"></a>Installer la mise à jour 3 en tant que correctif

Utilisez cette procédure si vous ne parvenez pas la case à cocher passerelle lorsque vous tentez d’installer les mises à jour via le portail classique Azure. La vérification échoue que vous avez une passerelle affectée à une interface de 0 réseau de données non- et votre appareil exécute une version du logiciel avant la mise à jour 1.

Les versions de logiciel pouvant être mis à niveau à l’aide de la méthode correctif sont :

- Mettre à jour de 0,1, 0,2, 0,3
- Mettre à jour 1, 1.1, 1.2
- Mettre à jour 2, 2.1 et 2.2 

> [AZURE.IMPORTANT]
>
> - Si votre appareil exécute la version Release (disponibilité générale), contactez le [Support technique Microsoft](storsimple-contact-microsoft-support.md) pour vous aider à la mise à jour.

La méthode correctif comprend les trois étapes suivantes :

1.  Téléchargez les correctifs à partir du catalogue Microsoft Update.

2.  Installer et vérifiez que les correctifs de mode normal.

3.  Installer et vérifiez que le correctif en mode maintenance (uniquement lorsque vous mettez à jour contre les logiciels 2 mise à jour partielle).


#### <a name="download-updates-for-your-device"></a>Télécharger des mises à jour de votre appareil

**Si votre appareil exécute 2.1 mise à jour ou 2.2**, vous devez télécharger et installer les correctifs suivants dans l’ordre prescrit :

| Commande  | BASE DE CONNAISSANCES        | Description                    | Type de mise à jour  | Moment de l’installation |
|--------|-----------|-------------------------|------------- |-------------|
| 1.      | KB3186843 | Mise à jour logicielle & #42 ;  |  Normal <br></br>Sans interruption de service     | ~ 45 minutes |
| 2.      | KB3186859 | Microprogramme et pilote LSI             |  Normal <br></br>Sans interruption de service      | ~ 20 min |
| 3.      | KB3121261 | Correctif Storport et Spaceport </br> Windows Server 2012 R2 |  Normal <br></br>Sans interruption de service      | ~ 20 min |

& #42 ;  Mise à jour *note, logiciel se compose de deux fichiers binaires : mise à jour du logiciel de périphérique précédés `all-hcsmdssoftwareupdate` et l’agent Cis et Mds précédés `all-cismdsagentupdatebundle`. La mise à jour du logiciel de périphérique doit être installé avant l’agent Cis et Mds. Vous devez également redémarrer le contrôleur actif via la `Restart-HcsController` mettre à jour de l’applet de commande après avoir appliqué l’agent Cis et Mds (et avant d’appliquer le restant les mises à jour).* 


**Si votre appareil exécute mise à jour de 0,1, 0,2, 0,3, 1.0, 1.1, 1.2 ou 2.0**, vous devez télécharger et installer les correctifs suivants en plus des logiciels, pilote LSI et mises à jour (comme illustrés dans le tableau ci-dessus), dans l’ordre prescrit :

| Commande  | BASE DE CONNAISSANCES        | Description                    | Type de mise à jour  | Moment de l’installation |
|--------|-----------|-------------------------|------------- |-------------|
| 4.      | KB3146621 | package iSCSI | Normal <br></br>Sans interruption de service  | ~ 20 min |
| 5.      | KB3103616 | Package WMI |  Normal <br></br>Sans interruption de service      | ~ min 12 |


<br></br>

**Si votre appareil est en cours d’exécution versions 0,2, 0,3, 1.0, 1.1 et 1.2**, vous devrez également installer les mises à jour de microprogramme disque en haut de toutes les mises à jour indiqués dans les tableaux précédents. Vous pouvez vérifier si vous avez besoin des mises à jour de microprogramme de disque en exécutant la `Get-HcsFirmwareVersion` applet de commande. Si vous exécutez ces versions microprogramme : `XMGG`, `XGEG`, `KZ50`, `F6C2`, `VR08`, puis vous n’avez pas besoin d’installer ces mises à jour.


| Commande  | BASE DE CONNAISSANCES        | Description                    | Type de mise à jour  | Moment de l’installation |
|--------|-----------|-------------------------|------------- |-------------|
| 6.      | KB3121899 | Microprogramme de disque              |  Maintenance <br></br>Interruption de service      | ~ 30 minutes |
 
<br></br>

> [AZURE.IMPORTANT]
>
> - Cette procédure doit être effectuée qu’une seule fois pour appliquer la mise à jour 3. Vous pouvez utiliser le portail classique Azure pour appliquer des mises à jour ultérieures.
> - Si la mise à jour à partir de la mise à jour 2.2, l’heure d’installation total est éloigné de heures 1.1.
> - Avant d’utiliser cette procédure pour appliquer la mise à jour, assurez-vous qu’à la fois les contrôleurs appareil sont en ligne et tous les composants matériels fonctionnent correctement.

Procédez comme suit pour télécharger et installer les correctifs.

[AZURE.INCLUDE [storsimple-install-update3-hotfix](../../includes/storsimple-install-update3-hotfix.md)]

[AZURE.INCLUDE [storsimple-install-troubleshooting](../../includes/storsimple-install-troubleshooting.md)]

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur la [version mise à jour 3](storsimple-update3-release-notes.md).

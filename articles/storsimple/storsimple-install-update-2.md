<properties
   pageTitle="Installer des mises à jour 2 sur votre appareil StorSimple | Microsoft Azure"
   description="Explique comment installer StorSimple 8000 série 2 sur votre appareil série 8000 StorSimple."
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
   ms.date="09/21/2016"
   ms.author="alkohli" />

# <a name="install-update-2-on-your-storsimple-device"></a>Installer des mises à jour 2 sur votre appareil StorSimple

## <a name="overview"></a>Vue d’ensemble

Ce didacticiel décrit l’installation de mise à jour 2 sur un appareil StorSimple qui exécute une version antérieure du logiciel via le portail classique Azure. Le didacticiel présente également les étapes requises pour la mise à jour lorsqu’une passerelle est configurée sur une interface réseau différente de données 0 du périphérique StorSimple et que vous voulez mettre à jour à partir d’une version mise à jour partielle du logiciel 1.

Mise à jour 2 comprend les mises à jour logicielles de périphérique, mises à jour des pilotes LSI et mises à jour de microprogramme de disque. Le périphérique logiciel et les mises à jour LSI sont mises à jour sans interruption de service et peuvent être appliqués via le portail classique Azure. Les mises à jour de microprogramme de disque sont mises à jour sans interruption de service et ne peuvent être appliqués via l’interface Windows PowerShell du périphérique.

> [AZURE.IMPORTANT]

> -  Vous ne voyez ne peut-être pas 2 mise à jour immédiatement, étant donné que nous avons une mise en place progressive les mises à jour. Rechercher les mises à jour en quelques jours à nouveau en tant que cette mise à jour ne sera bientôt plus disponible.
> - Un ensemble de vérifications préalables manuels et automatiques sont effectuées avant l’installation pour déterminer l’état de l’appareil en termes de matériel état et la connectivité réseau. Ces vérifications préalables sont effectuées uniquement si vous appliquez les mises à jour à partir du portail classique Azure.
> - Nous vous conseillons d’installer les mises à jour du pilote et le logiciel via le portail classique Azure. Vous ne devez pas dépasser à l’interface Windows PowerShell du périphérique (pour installer les mises à jour) si la vérification de la mise à jour partielle passerelle échoue dans le portail. Les mises à jour peuvent prendre heures 4 à 7 pour installer (y compris les mises à jour Windows). Les mises à jour du mode maintenance doivent être installés via l’interface Windows PowerShell du périphérique. Mises à jour du mode maintenance sont mises à jour sans interruption de service, il seront traduit par un temps vers le bas pour votre appareil.
> - Si le Gestionnaire d’instantanés StorSimple facultatif en cours d’exécution, vérifiez que vous avez mis à niveau votre version du Gestionnaire de capture instantanée à 2 mise à jour avant la mise à jour de l’appareil.

[AZURE.INCLUDE [storsimple-preparing-for-update](../../includes/storsimple-preparing-for-updates.md)]

## <a name="install-update-2-via-the-azure-classic-portal"></a>Installer des mises à jour 2 via le portail classique Azure

Procédez comme suit pour mettre à jour votre appareil à [mettre à jour 2](storsimple-update2-release-notes.md).


> [AZURE.NOTE]
Mise à jour 2 permet à Microsoft extraire des informations de diagnostic supplémentaires à partir de l’appareil. Par conséquent, lorsque notre équipe opérations identifie les appareils que vous rencontrent des problèmes, nous sont mieux équipés pour recueillir des informations à partir de l’appareil et diagnostiquer les problèmes. En acceptant 2 mise à jour, vous permettez de fournir un ce support dynamique.

[AZURE.INCLUDE [storsimple-install-update2-via-portal](../../includes/storsimple-install-update2-via-portal.md)]

12. Vérifiez que votre appareil fonctionne **StorSimple 8000 série mise à jour 2 (6.3.9600.17673)**. La **dernière mise à jour date** doivent également être modifiées. Vous verrez également que les mises à jour du mode Maintenance sont disponibles (ce message peut continuer à afficher dans un délai de 24 heures après avoir installé les mises à jour).

    Mises à jour du mode maintenance sont mises à jour sans interruption de service qui résultat dans le temps d’arrêt de l’appareil et ne peuvent être appliqués via l’interface Windows PowerShell de votre appareil. Dans certains cas, lorsque vous exécutez la mise à jour 1.2, votre microprogramme de disque peut-être déjà à jour, auquel cas vous n’avez pas besoin d’installer des mises à jour du mode de maintenance.

13. Téléchargez les mises à jour du mode maintenance à l’aide de la procédure décrite dans [pour télécharger des correctifs](#to-download-hotfixes) pour rechercher et télécharger KB3121899, quelles installations disque microprogramme mises à jour (les autres mises à jour doivent déjà être installés maintenant).

13. Suivez la procédure décrite dans [installer et vérifier les correctifs en mode maintenance](#to-install-and-verify-maintenance-mode-hotfixes) pour installer les mises à jour du mode de maintenance.


## <a name="install-update-2-as-a-hotfix"></a>Installer des mises à jour 2 en tant que correctif

Utilisez cette procédure si vous ne parvenez pas la case à cocher passerelle lorsque vous tentez d’installer les mises à jour via le portail classique Azure. La vérification échoue que vous avez une passerelle affectée à une interface de 0 réseau de données non- et votre appareil exécute une version du logiciel avant la mise à jour 1.

Les versions de logiciel pouvant être mis à niveau à l’aide de la méthode correctif sont mise à jour de 0,1, Update 0,2 et mise à jour 0,3, mise à jour 1, 1.1 mise à jour et mise à jour 1.2. La méthode correctif comprend les trois étapes suivantes :

- Téléchargez les correctifs à partir du catalogue Microsoft Update.
- Installer et vérifiez que les correctifs de mode normal.
- Installer et vérifiez que le correctif en mode maintenance.

Pour installer la mise à jour 2 en tant que correctif, vous devez télécharger et installer les correctifs suivants :

| Commande  | BASE DE CONNAISSANCES        | Description                    | Type de mise à jour  |
|--------|-----------|-------------------------|------------- |
| 1      | KB3121901 | Mise à jour logicielle         |  Normal     |
| 2      | KB3121900 | Pilote LSI              |  Normal     |
| 3      | KB3080728 | Correctif Storport </br> Windows Server 2012 R2 |  Normal     |
| 4      | KB3090322 | Correctif spaceport </br> Windows Server 2012 R2 |  Normal     |
| 5      | KB3121899 | Microprogramme de disque           | Maintenance  |


> [AZURE.IMPORTANT]
>
> - Si votre appareil exécute la version Release (disponibilité générale), contactez le [Support technique Microsoft](storsimple-contact-microsoft-support.md) pour vous aider à la mise à jour.
> - Cette procédure doit être effectuée qu’une seule fois pour appliquer la mise à jour 2. Vous pouvez utiliser le portail classique Azure pour appliquer des mises à jour ultérieures.
> - Chaque installation du correctif peut prendre environ 20 minutes. Moment de l’installation total est éloigné de 2 heures.
> - Avant d’utiliser cette procédure pour appliquer la mise à jour, assurez-vous que les deux contrôleurs appareil sont en ligne et tous les composants matériels fonctionnent correctement.

Procédez comme suit pour appliquer cette mise à jour en tant que correctif.

[AZURE.INCLUDE [storsimple-install-update2-hotfix](../../includes/storsimple-install-update2-hotfix.md)]

[AZURE.INCLUDE [storsimple-install-troubleshooting](../../includes/storsimple-install-troubleshooting.md)]



## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur la [version 2 mise à jour](storsimple-update2-release-notes.md).

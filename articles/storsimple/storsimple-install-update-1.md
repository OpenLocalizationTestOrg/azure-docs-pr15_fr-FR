<properties
   pageTitle="Installer la mise à jour 1.2 sur votre appareil StorSimple | Microsoft Azure"
   description="Explique comment installer StorSimple 8000 série mise à jour 1.2 sur votre appareil série 8000 StorSimple."
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
   ms.date="08/22/2016"
   ms.author="alkohli" />

# <a name="install-update-12-on-your-storsimple-device"></a>Installer la mise à jour 1.2 sur votre appareil StorSimple

## <a name="overview"></a>Vue d’ensemble

Ce didacticiel décrit l’installation de mise à jour 1.2 sur un appareil StorSimple exécutant une version du logiciel avant la mise à jour 1. Le didacticiel couvre également les étapes supplémentaires requises pour la mise à jour lorsqu’une passerelle est configurée sur une interface réseau différente de données 0 du périphérique StorSimple.

Mise à jour 1.2 comprend les mises à jour logicielles de périphérique, mises à jour des pilotes LSI et mises à jour de microprogramme de disque. Le logiciel et les mises à jour des pilotes LSI sont mises à jour sans interruption de service et peuvent être appliqués via le portail classique Azure. Les mises à jour de microprogramme de disque sont mises à jour sans interruption de service et ne peuvent être appliqués via l’interface Windows PowerShell du périphérique.

Selon la version de votre appareil est en cours d’exécution, vous pouvez déterminer si mise à jour 1.2 seront appliquées. Vous pouvez vérifier la version du logiciel de votre appareil, accédez à la section **coup de œil rapide** de votre **tableau de bord**.

</br>

| Si la version du logiciel en cours d’exécution...   | Que se passe-t-il dans le portail ?                              |
|---------------------------------|--------------------------------------------------------------|
| Version – disponibilité générale                    | Si vous exécutez la version d’origine (disponibilité générale), ne s’appliquent pas cette mise à jour. Veuillez [contacter le support technique Microsoft](storsimple-contact-microsoft-support.md) pour mettre à jour votre appareil.|
| Mise à jour de 0,1                      | Portail applique la mise à jour 1.2.                                |
| Mise à jour 0,2                      | Portail applique la mise à jour 1.2.                                |
| Mise à jour 0,3                      | Portail applique la mise à jour 1.2.                                |
| Mise à jour 1                        | Cette mise à jour ne sera pas disponible.                           |
| Mettre à jour 1.1                      | Cette mise à jour ne sera pas disponible.                           |

</br>

> [AZURE.IMPORTANT]

> -  Vous ne voyez ne peut-être pas 1.2 mise à jour immédiatement, étant donné que nous avons une mise en place progressive les mises à jour. Rechercher les mises à jour en quelques jours à nouveau en tant que cette mise à jour ne sera bientôt plus disponible.
> - Cette mise à jour inclut un ensemble de vérifications préalables manuels et automatiques pour déterminer l’état de l’appareil en termes de matériel état et la connectivité réseau. Ces vérifications préalables sont effectuées uniquement si vous appliquez les mises à jour à partir du portail classique Azure.
> - Nous vous conseillons d’installer les mises à jour du pilote et le logiciel via le portail classique Azure. Vous ne devez pas dépasser à l’interface Windows PowerShell du périphérique (pour installer les mises à jour) si la vérification de la mise à jour partielle passerelle échoue dans le portail. Les mises à jour peuvent prendre 5 à 10 h pour installer (y compris les mises à jour Windows). Les mises à jour du mode maintenance doivent être installés via l’interface Windows PowerShell du périphérique. Mises à jour du mode maintenance sont mises à jour sans interruption de service, il seront traduit par un temps vers le bas pour votre appareil.

[AZURE.INCLUDE [storsimple-preparing-for-update](../../includes/storsimple-preparing-for-updates.md)]

## <a name="install-update-12-via-the-azure-classic-portal"></a>Installer la mise à jour 1.2 via le portail classique Azure

Procédez comme suit pour mettre à jour votre appareil à la [version 1.2 mise à jour](storsimple-update1-release-notes.md). Utilisez cette procédure uniquement si vous avez une passerelle est configurée sur l’interface de 0 réseau de données sur votre appareil.

[AZURE.INCLUDE [storsimple-install-update2-via-portal](../../includes/storsimple-install-update2-via-portal.md)]

12. Vérifiez que votre appareil fonctionne **StorSimple 8000 série mise à jour 1.2 (6.3.9600.17584)**. La **dernière mise à jour date** doivent également être modifiées. Vous verrez également que les mises à jour du mode Maintenance sont disponibles (ce message peut continuer à afficher dans un délai de 24 heures après avoir installé les mises à jour).

    Mises à jour du mode maintenance sont mises à jour sans interruption de service qui résultat dans le temps d’arrêt de l’appareil et ne peuvent être appliqués via l’interface Windows PowerShell de votre appareil.

    ![Page de maintenance] (./media/storsimple-install-update-1/InstallUpdate12_10M.png "Page de maintenance")

13. Téléchargez les mises à jour du mode maintenance à l’aide de la procédure décrite dans [pour télécharger des correctifs]( #to-download-hotfixes) pour rechercher et télécharger KB3063416, quelles installations disque microprogramme mises à jour (les autres mises à jour doivent déjà être installés maintenant).

13. Suivez la procédure décrite dans [installer et vérifier les correctifs en mode maintenance](#to-install-and-verify-maintenance-mode-hotfixes) pour installer les mises à jour du mode de maintenance.

14. Dans le portail classique Azure, accédez à la page de **Maintenance** et en bas de la page, cliquez sur **Rechercher les mises à jour** pour vérifier les mises à jour Windows, puis cliquez sur **Installer les mises à jour**. Vous avez terminé une fois que toutes les mises à jour sont installées correctement.



## <a name="install-update-12-on-a-device-that-has-a-gateway-configured-for-a-non-data-0-network-interface"></a>Installer 1.2 mise à jour sur un appareil qui dispose d’une passerelle configurée pour une interface de réseau 0 non données

Vous devez utiliser cette procédure uniquement si vous ne parvenez pas la case à cocher passerelle lorsque vous tentez d’installer les mises à jour via le portail classique Azure. La vérification échoue que vous avez une passerelle affectée à une interface de 0 réseau de données non- et votre appareil exécute une version du logiciel avant la mise à jour 1. Si votre appareil ne dispose pas d’une passerelle sur une interface de réseau 0 données non, vous pouvez mettre à jour votre appareil directement à partir du portail classique Azure. Voir [Installer 1.2 via le portail Azure classique de mettre à jour](#install-update-1.2-via-the-azure-classic-portal).

Les versions du logiciel pouvant être mis à niveau à l’aide de cette méthode sont mise à jour de 0,1, mise à jour 0,2 et mise à jour 0,3.


> [AZURE.IMPORTANT]
>
> - Si votre appareil exécute la version Release (disponibilité générale), contactez le [Support technique Microsoft](storsimple-contact-microsoft-support.md) pour vous aider à la mise à jour.
> - Cette procédure doit être effectuée qu’une seule fois pour appliquer la mise à jour 1.2. Vous pouvez utiliser le portail classique Azure pour appliquer des mises à jour ultérieures.

Si votre appareil est en cours d’exécution avant mise à jour 1 logiciel et il dispose d’une passerelle définir une interface de réseau autres que celles de 0, vous pouvez appliquer 1.2 mise à jour de deux façons suivantes :

- **Option 1**: télécharger la mise à jour et l’appliquer à l’aide de la `Start-HcsHotfix` applet de commande à partir de l’interface Windows PowerShell du périphérique. Il s’agit de la méthode recommandée. **N’utilisez pas cette méthode pour appliquer la mise à jour 1.2 si votre appareil exécute Update 1.0 ou 1.1 mise à jour.**

- **Option 2**: supprimez la configuration de la passerelle et installez la mise à jour directement à partir du portail classique Azure.


Vous trouverez des instructions détaillées pour chacun d'entre eux dans les sections suivantes.

## <a name="option-1-use-windows-powershell-for-storsimple-to-apply-update-12-as-a-hotfix"></a>Option 1 : Utiliser Windows PowerShell pour StorSimple appliquer la mise à jour 1.2 en tant que correctif

Vous devez utiliser cette procédure uniquement si vous exécutez la mise à jour de 0,1, 0,2, 0,3 et si votre vérification de la passerelle a échoué lors de l’installation des mises à jour à partir du portail classique Azure. Si vous exécutez la version finale (disponibilité générale), veuillez le [Support technique Microsoft](storsimple-contact-microsoft-support.md) pour mettre à jour votre appareil.

Pour installer la mise à jour 1.2 en tant que correctif, vous devez télécharger et installer les correctifs suivants :

| Commande  | BASE DE CONNAISSANCES        | Description             | Type de mise à jour  |
|--------|-----------|-------------------------|------------- |
| 1      | KB3063418 | Mise à jour logicielle         |  Normal     |
| 2      | KB3043005 | Mise à jour du contrôleur LSI sa |  Normal     |
| 3      | KB3063416 | Microprogramme de disque           | Maintenance  |

Avant d’utiliser cette procédure pour appliquer la mise à jour, vérifiez que :

- Les deux contrôleurs appareil sont en ligne.

Procédez comme suit pour appliquer la mise à jour 1.2. **Les mises à jour peuvent prendre environ 2 heures (environ 30 minutes pour les logiciels, 30 minutes pour pilote, 45 minutes pour microprogramme de disque).**

[AZURE.INCLUDE [storsimple-install-update-option1](../../includes/storsimple-install-update-option1.md)]


## <a name="option-2-use-the-azure-classic-portal-to-apply-update-12-after-removing-the-gateway-configuration"></a>Option 2 : Utiliser le portail classique Azure pour appliquer la mise à jour 1.2 après la suppression de la configuration de la passerelle

Cette procédure s’applique uniquement aux appareils StorSimple qui exécutent une version du logiciel avant la mise à jour 1 et qui ont une passerelle définie sur une interface réseau différente de données 0. Vous devrez désactivez le paramètre de passerelle avant d’appliquer la mise à jour.

La mise à jour peut prendre quelques heures. Si vos hôtes se trouvent dans différents sous-réseaux, suppression de la configuration de passerelle sur les interfaces iSCSI peut entraîner d’indisponibilité. Nous vous recommandons de configurer des données 0 pour le trafic iSCSI pour réduire le temps d’arrêt.

Effectuez les opérations suivantes pour désactiver l’interface réseau avec la passerelle, puis appliquez la mise à jour.

[AZURE.INCLUDE [storsimple-install-update-option2](../../includes/storsimple-install-update-option2.md)]

[AZURE.INCLUDE [storsimple-install-troubleshooting](../../includes/storsimple-install-troubleshooting.md)]


## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur la [version 1.2 mise à jour](storsimple-update1-release-notes.md).

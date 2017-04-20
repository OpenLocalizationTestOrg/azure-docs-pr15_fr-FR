<properties 
   pageTitle="Remplacer un PCM sur votre appareil StorSimple | Microsoft Azure"
   description="Explique comment supprimer et le remplacer la puissance et refroidissement Module (PCM) sur votre appareil StorSimple"
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
   ms.date="08/18/2016"
   ms.author="alkohli" />

# <a name="replace-a-power-and-cooling-module-on-your-storsimple-device"></a>Remplacer un Power et Module sur votre appareil StorSimple

## <a name="overview"></a>Vue d’ensemble

La puissance et refroidissement Module (PCM) sur votre appareil Microsoft Azure StorSimple comprend une alimentation et ventilateurs contrôlée via le moniteur principal et les pièces jointes EBOD. Il n'existe qu’un seul modèle de PCM est certifiée pour chaque boîtier. Le boîtier principal n’est certifié pour un 764 PCM W et le boîtier EBOD est certifié un PCM W 580. Bien que les PCMs pour le boîtier principal et le boîtier EBOD sont différents, la procédure de remplacement est identique.

Ce didacticiel décrit comment :

- Supprimer un PCM
- Installer une autre PCM

>[AZURE.IMPORTANT] Avant de retrait et le remplacement un PCM, passez en revue les informations de sécurité dans le [remplacement des composants matériels StorSimple](storsimple-hardware-component-replacement.md).

## <a name="before-you-replace-a-pcm"></a>Avant de remplacer une PCM

Tenir compte des points suivants avant de remplacer votre PCM :

- Si l’alimentation de la PCM échoue, laissez installé le module défectueux, mais supprimez le cordon d’alimentation. Les ventilateurs continuent à recevoir power à partir de l’encadrement et continuer à fournir refroidissement. Si les ventilateurs échoue, le PCM doit être remplacé immédiatement.

- Avant de supprimer le PCM, coupez l’alimentation de la PCM en désactivant le commutateur principal (le cas échéant) ou en supprimant physiquement le cordon d’alimentation. Ainsi, un avertissement s’affiche à votre système qu’un arrêt power est imminent.

- Vérifiez que l’autres PCM est fonctionnel pour le fonctionnement du système continue avant de remplacer le PCM défectueux. Un PCM défectueux doit être remplacé par un PCM opérationnel dès que possible.

- Remplacement des modules PCM ne prend que quelques minutes, mais il doit être terminé dans les 10 minutes de la suppression de l’échec PCM pour empêcher la surchauffe.

- Notez que les modules PCM W 764 remplacement usine ne contiennent pas le module de batterie de secours. Vous devrez supprimer la batterie de votre PCM défectueux et puis insérez-le dans le module de remplacement avant d’effectuer le remplacement. Pour plus d’informations, voir comment [Supprimer et insérer un module de batterie de secours](storsimple-battery-replacement.md).


## <a name="remove-a-pcm"></a>Supprimer un PCM

Suivez ces instructions lorsque vous êtes prêt à supprimer une puissance et refroidissement Module (PCM) à partir de votre appareil Microsoft Azure StorSimple.

>[AZURE.NOTE] Avant de supprimer votre PCM, vérifiez que vous disposez d’un remplacement correcte (764 W pour le boîtier principal) ou 580 W pour le boîtier EBOD.

#### <a name="to-remove-a-pcm"></a>Pour supprimer un PCM

1. Dans le portail Azure classique, cliquez sur **périphériques** > **Maintenance** > **État du matériel**. Vérifier l’état des composants PCM sous **Composants partagés** pour identifier PCM n’a pas pu :

     - Si une alimentation dans PCM 0 a échoué, l’état de **L’alimentation dans PCM 0** sera rouge.

     - Si une alimentation PCM 1 a échoué, l’état de **L’alimentation 1 PCM** sera rouge.

     - Si les ventilateurs PCM 1 a échoué, l’état de **refroidissement 0 pour PCM 0** ou **1 refroidissement pour PCM 0** sera rouge.

2. Recherchez l’échec PCM au dos du boîtier principal. Si vous utilisez un modèle 8600, identifier le boîtier principal en consultant le numéro d’Identification unité système apparaît dans le panneau d’affichage LED avant. L’ID unité affiché sur le boîtier principal par défaut est **00**, tandis que l’ID d’unité affiché sur le boîtier EBOD par défaut est **01**. Le diagramme et les tableaux suivants expliquent le panneau avant de l’affichage LED.

    ![ID du système dans le panneau d’opérations avant](./media/storsimple-power-cooling-module-replacement/IC740991.png)

     **Figure 1** Panneau avant de l’appareil  

  	|Étiquette|Description|
  	|:---|:-----------|
  	|1|Bouton Muet|
  	|2|Puissance du système|
  	|3|Erreur de module|
  	|4|Erreur de logique|
  	|5|Affichage de l’identifiant unité|

3. L’indicateur de surveillance voyants à l’arrière-plan du boîtier principal peut également servir à identifier le PCM défectueux. Consultez le diagramme et le tableau pour mieux comprendre comment utiliser les voyants pour localiser le câblage PCM suivants. Par exemple, si la LED correspondant à **Ventilateurs échouer** est allumé, il est défectueux. De même, si la LED correspondant aux **AC Fail** est allumé, l’alimentation a échoué. 

    ![Carte d’insertion de périphérique PCM surveillance voyants](./media/storsimple-power-cooling-module-replacement/IC740992.png)

     **Figure 2** Verso PCM avec voyants

  	|Étiquette|Description|
  	|:---|:-----------|
  	|1|Coupure de courant CA|
  	|2|Échec de ventilateurs|
  	|3|Erreur de batterie|
  	|4|OK PCM|
  	|5|Coupure de courant continu|
  	|6|Exact de la batterie|

4. Consultez le diagramme suivant de l’arrière-plan de l’appareil StorSimple de localiser le module PCM a échoué. PCM 0 est gauche et PCM 1 se trouve à droite. Le tableau suivant décrit les modules.

     ![Carte d’insertion de modules du boîtier principal appareil](./media/storsimple-power-cooling-module-replacement/IC740994.png)

     **Figure 3** Verso appareil avec modules plug-in 

  	|Étiquette|Description|
  	|:---|:-----------|
  	|1|PCM 0|
  	|2|PCM 1|
  	|3|Contrôleur de 0|
  	|4|Contrôleur de 1|

5. Désactiver le PCM défectueux et se déconnecter d’alimentation. Vous pouvez maintenant supprimer le PCM.

6. Comprendre le verrou et le côté de la poignée PCM entre le pouce et l’index, puis appuyez sur les tâches pour ouvrir la poignée.

    ![Poignée de PCM ouverture](./media/storsimple-power-cooling-module-replacement/IC740995.png)

    **Figure 4** Ouverture de la poignée PCM

7. Saisissez la poignée et supprimez le PCM.

    ![Suppression d’un périphérique PCM](./media/storsimple-power-cooling-module-replacement/IC740996.png)

    **Figure 5** Suppression de la PCM

## <a name="install-a-replacement-pcm"></a>Installer une autre PCM

Suivez ces instructions pour installer un PCM dans votre appareil StorSimple. Vérifiez que vous avez inséré le module de batterie de sauvegarde avant d’installer le remplacement PCM (s’applique à 764 PCMs W uniquement). Pour plus d’informations, voir comment [Supprimer et insérer un module de batterie de secours](storsimple-battery-replacement.md).

#### <a name="to-install-a-pcm"></a>Pour installer un PCM

1. Vérifiez que vous avez le remplacement correct PCM pour cette pièce jointe. Le boîtier primaire doit être une 764 PCM W et le boîtier EBOD doit un PCM W 580. Vous ne devez pas essayer d’utiliser le PCM 580 W dans le boîtier principal ou le PCM W 764 du boîtier EBOD. L’image suivante montre l’emplacement d’identifier ces informations sur l’étiquette apposée sur le PCM.

    ![APPAREIL PCM étiquette](./media/storsimple-power-cooling-module-replacement/IC740973.png)

    **Figure 6** Étiquette PCM

2. Vérifiez si le boîtier, en veillant aux connecteurs. 
                                        
    >[AZURE.NOTE] **Ne pas installer le module si les fiches de connexion sont pliées.**

3. Avec la poignée PCM en position ouverte, faites glisser le module dans le boîtier.

    ![L’installation du périphérique PCM](./media/storsimple-power-cooling-module-replacement/IC740975.png)

    **Figure 7** L’installation de la PCM

4. Fermez manuellement la poignée PCM. Vous devez entendre un clic comme le verrou poignée s’engage. 
                                        
    >[AZURE.NOTE] Pour vous assurer que les chevilles connecteur ont activé, vous pouvez légèrement bar sur la poignée sans relâcher le verrou. Si le PCM coulisse, cela signifie que le verrou est fermé avant les connecteurs engagé.

5. Connectez les câbles d’alimentation à la source d’alimentation et au PCM.

6. Sécuriser la déformation balles relief. 

7. Activer le PCM.

8. Vérifiez que le remplacement a réussi : dans le portail Azure classique de votre service Manager StorSimple, accédez à **appareils** > **Maintenance** > **État du matériel**. Sous **Composants partagés**, l’état de la PCM doit être vert. 
                                        
    >[AZURE.NOTE] Il peut prendre quelques minutes pour le remplacement PCM initialisation complètement.

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur le [remplacement des composants matériels StorSimple](storsimple-hardware-component-replacement.md).

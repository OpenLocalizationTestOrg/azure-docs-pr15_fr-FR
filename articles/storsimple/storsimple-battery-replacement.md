<properties 
   pageTitle="Remplacer la batterie sur un appareil StorSimple | Microsoft Azure"
   description="Décrit comment supprimer, remplacer et mettre à jour le module de batterie de secours sur votre appareil StorSimple."
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

# <a name="replace-the-backup-battery-module-on-your-storsimple-device"></a>Remplacez le module de batterie de secours sur votre appareil StorSimple

## <a name="overview"></a>Vue d’ensemble

L’encadrement primaire Power et refroidissement Module (PCM) sur votre appareil Microsoft Azure StorSimple possède un module de batterie supplémentaire. Ce module fournit power afin que l’appareil StorSimple peut enregistrer des données s’il existe coupure de courant au boîtier principal. Cette batterie est appelé le *module de batterie de secours*. Le module de batterie de secours existe uniquement pour le boîtier principal dans votre appareil StorSimple (le boîtier EBOD ne contient pas un module de sauvegarde de batterie). 

Ce didacticiel décrit comment :

- Supprimer le module de batterie de secours 
- Installer un nouveau module de batterie de secours
- Mettre à jour le module de batterie de secours

>[AZURE.IMPORTANT] Avant la suppression et remplacement d’un module de batterie de secours, passez en revue les informations de sécurité dans [Présentation de remplacement des composants matériels StorSimple](storsimple-hardware-component-replacement.md).

## <a name="remove-the-backup-battery-module"></a>Supprimer le module de batterie de secours

Le module de batterie de secours pour votre appareil StorSimple est une unité champ eux-mêmes. Avant qu’il est installé dans le PCM, le module de batterie doit être stocké dans son emballage d’origine. Procédez comme suit pour supprimer la batterie de secours.

#### <a name="to-remove-the-backup-battery-module"></a>Pour supprimer le module de batterie de secours

1. Dans le portail classique Azure, accédez à **appareils** > **Maintenance** > **État du matériel**. Sous **Composants partagés**, examinez l’état de la batterie.

2. Identifier le PCM dans lequel la batterie a échoué. Figure 1 illustre l’arrière du périphérique StorSimple.

    ![Carte d’insertion de Modules du boîtier principal appareil](./media/storsimple-battery-replacement/IC740994.png)

    **Figure 1** Verso du périphérique principal affichant les modules PCM et contrôleur

  	|Étiquette|Description|
  	|:----|:----------|
  	|1|PCM 0|
  	|2|PCM 1|
  	|3|Contrôleur de 0|
  	|4|Contrôleur de 1|

    Comme indiqué par un nombre 3 dans la Figure 2, l’indicateur de surveillance LED sur PCM 0 qui correspond à **Défaillance de batterie** doit être allumé.

    ![Carte d’insertion d’appareil PCM surveillance indicateurs lumineux](./media/storsimple-battery-replacement/IC740992.png)

    **Figure 2** Verso PCM affichant l’indicateur de surveillance voyants

  	|Étiquette|Description|
  	|:---|:-----------|
  	|1|Coupure de courant CA|
  	|2|Échec de ventilateurs|
  	|3|Erreur de batterie|
  	|4|OK PCM|
  	|5|Coupure de courant continu|
  	|6|Exact de la batterie|

3. Pour supprimer le PCM avec une batterie échec, suivez les étapes décrites dans [Supprimer un PCM](storsimple-power-cooling-module-replacement.md#remove-a-pcm).

4. Grâce à PCM supprimé, lever et faire pivoter la poignée du module batterie vers le haut comme indiqué dans l’illustration suivante et extraire jusqu'à supprimer la batterie.

    ![Retrait de la batterie de PCM](./media/storsimple-battery-replacement/IC741019.png)

    **Figure 3** Retrait de la batterie à partir de la PCM

5. Placez le module dans la champ eux-mêmes unité emballage.

6. Retourner l’unité défectueuse à Microsoft pour NOMPROPRE gestion et de maintenance.

## <a name="install-a-new-backup-battery-module"></a>Installer un nouveau module de batterie de secours

Procédez comme suit pour installer le module de batterie de remplacement dans le PCM du boîtier principal de votre périphérique StorSimple.

#### <a name="to-install-the-battery-module"></a>Pour installer le module de batterie

1. Placez le module de batterie de secours dans l’orientation appropriée dans le PCM.

2. Appuyez sur la poignée de module de batterie extrême pour insérer le lien.

3. Remplacez le PCM du boîtier primaire en suivant les instructions dans la zone [Remplacer un Power et Module sur votre appareil StorSimple](storsimple-power-cooling-module-replacement.md).

4. Une fois le remplacement terminée, accédez à **appareils** > **Maintenance** > **État du matériel** dans le portail classique Azure. Vérifiez l’état de la batterie pour vous assurer que l’installation a réussi. Un statut vert indique que la batterie est correcte.

## <a name="maintain-the-backup-battery-module"></a>Mettre à jour le module de batterie de secours

Dans votre appareil StorSimple, le module de batterie de secours offre power sur le contrôleur de pendant un événement de perte d’alimentation. Il permet au périphérique StorSimple enregistrer les données critiques avant l’arrêt d’une manière contrôlée. Avec deux batteries complètement chargées dans les PCMs, le système peut gérer deux événements perte consécutives.

Dans le portail classique Azure, l' **État du matériel** dans la page **Maintenance** indique si la batterie est défectueux ou la fin de la vie approche. L’état de la batterie est indiqué par **batterie en PCM 0** ou **batterie en 1 PCM** sous **Composants partagés**. Cette page affiche un état **dégradé** approchant de fin de vie et atteint **Échec** de fin de vie. 

>[AZURE.NOTE] La batterie peut signaler **Échec** quand il doit simplement être facturés.
 
Si l’état **dégradé** s’affiche, nous vous recommandons du plan d’action suivant :

- Le système a peut-être rencontré une coupure récents ou les batteries peuvent être en cours de maintenance périodique. Observez le système de 12 heures avant de poursuivre.

    - Si l’état est toujours **dégradé** après 12 heures d’une connexion continue à alimentation avec les contrôleurs et PCMs en cours d’exécution, la batterie doit être remplacée. Veuillez [contacter le support technique Microsoft](storsimple-contact-microsoft-support.md) pour un module de batterie de secours de remplacement.

    - Si l’état est OK après 12 heures, la batterie est opérationnelle, et elle nécessaire uniquement des frais de maintenance.

- Si aucune n’a été une coupure de courant associé et le PCM est allumé et connecté au secteur, la batterie doit être remplacée. [Contacter le support technique Microsoft](storsimple-contact-microsoft-support.md) pour commander un module de batterie de secours de remplacement.

>[AZURE.IMPORTANT] Dispose de la batterie échec en fonction des réglementations nationale et régionale. 

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur le [remplacement des composants matériels StorSimple](storsimple-hardware-component-replacement.md).

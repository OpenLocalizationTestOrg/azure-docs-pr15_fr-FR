<properties 
   pageTitle="Remplacer un contrôleur StorSimple EBOD | Microsoft Azure"
   description="Explique comment supprimer et le remplacer un ou deux contrôleurs EBOD sur un appareil StorSimple 8600."
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

# <a name="replace-an-ebod-controller-on-your-storsimple-device"></a>Remplacer un contrôleur EBOD sur votre appareil StorSimple

## <a name="overview"></a>Vue d’ensemble

Ce didacticiel explique la procédure de remplacement d’un module de contrôleur EBOD défectueux sur votre appareil Microsoft Azure StorSimple. Pour remplacer un module contrôleur EBOD, vous devez :

- Supprimer le contrôleur EBOD défectueux
- Installer un nouveau contrôleur EBOD

Avant de commencer, tenez compte des informations suivantes :

- Modules EBOD vides doivent être insérés dans tous les emplacements inutilisés. Le boîtier ne sera pas refroidir correctement si un emplacement n’est pas ouvert.

- Le contrôleur de EBOD est chaud et peut être supprimé ou remplacé. Ne pas supprimer un module défectueux jusqu'à ce que le texte de remplacement souhaité. Lorsque vous lancez le processus de remplacement, vous devez la terminer dans les 10 minutes.

>[AZURE.IMPORTANT] Avant d’essayer de supprimer ou remplacer n’importe quel composant StorSimple, vérifiez que vous passez en revue les [conventions des icônes de sécurité des messages](storsimple-safety.md#safety-icon-conventions) et autres [mesures de sécurité](storsimple-safety.md).

## <a name="remove-an-ebod-controller"></a>Supprimer un contrôleur de EBOD

Avant de remplacer le module de contrôleur EBOD Échec de votre appareil StorSimple, assurez-vous que l’autre module EBOD contrôleur est actif et en cours d’exécution. La procédure suivante et la table marche à suivre supprimer le module de contrôleur EBOD.

#### <a name="to-remove-an-ebod-module"></a>Pour supprimer un module EBOD

1. Ouvrez le portail classique Azure.

2. Accédez à **appareils** > **Maintenance** > **État du matériel**et vérifiez que l’état de la LED du module contrôleur EBOD active est vert et la LED du module contrôleur EBOD échec est rouge.

3. Recherchez le module contrôleur EBOD défectueux à l’arrière de l’appareil.

4. Supprimez les câbles qui connecter le module contrôleur EBOD au contrôleur avant de commencer le module EBOD déconnecter le système.

5. Notez le port sa exact du module contrôleur EBOD qui a été connecté au contrôleur. Vous devrez restaurer le système à cette configuration une fois que vous remplacez le module EBOD. 

    >[AZURE.NOTE] En règle générale, il s’agit A Port, ce qui est appelé **hôte dans** l’illustration suivante.

    ![Contrôleur de panier de EBOD](./media/storsimple-ebod-controller-replacement/IC741049.png)

     **Figure 1** Verso module EBOD

  	|Étiquette|Description|
  	|:----|:----------|
  	|1|Défaillance LED|
  	|2|Power LED|
  	|3|Connecteurs associations de sécurité|
  	|4|Voyants associations de sécurité|
  	|5|Ports série pour une utilisation en usine uniquement|
  	|6|Port (hôte dans)|
  	|7|Port B (hôte arrière)|
  	|8|Port C (utilisation en usine uniquement)|

## <a name="install-a-new-ebod-controller"></a>Installer un nouveau contrôleur EBOD

La procédure et table suivantes expliquent comment installer un module de contrôleur EBOD dans votre appareil StorSimple.

#### <a name="to-install-an-ebod-controller"></a>Pour installer un contrôleur de EBOD

1. Vérifiez le périphérique EBOD est endommagé, en particulier au connecteur interface. N’installez pas le nouveau contrôleur de EBOD si les codes confidentiels sont pliées.

2. Avec les taquets en position ouverte, faites glisser le module dans le boîtier jusqu'à ce que les taquets engager.

    ![Installation EBOD contrôleur](./media/storsimple-ebod-controller-replacement/IC741050.png)

    **Figure 2**  Installez le module de contrôleur EBOD

3. Fermez le taquet. Vous devez entendre un clic comme le verrou s’engage.

    ![Verrouillage EBOD libération](./media/storsimple-ebod-controller-replacement/IC741047.png)

    **Figure 3**  Fermer le verrou du module EBOD

4. Reconnectez les câbles. Utiliser la configuration exacte qui était présente avant le remplacement. Consultez le diagramme et le tableau pour plus d’informations sur la connexion que les câbles suivants.

    ![Câble votre appareil 4U pour power](./media/storsimple-ebod-controller-replacement/IC770723.png)

    **Figure 4**. Reconnexion des câbles

  	|Étiquette|Description|
  	|:----|:----------|
  	|1|Boîtier principal|
  	|2|PCM 0|
  	|3|PCM 1|
  	|4|Contrôleur de 0|
  	|5|Contrôleur de 1|
  	|6|Contrôleur EBOD 0|
  	|7|Contrôleur de EBOD 1|
  	|8|Encadrement EBOD|
  	|9|Unités de Distribution de puissance|

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur le [remplacement des composants matériels StorSimple](storsimple-hardware-component-replacement.md).

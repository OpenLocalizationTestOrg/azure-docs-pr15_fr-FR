<properties 
   pageTitle="Web tableau virtuel StorSimple administration de l’interface utilisateur | Microsoft Azure"
   description="Décrit comment effectuer des tâches d’administration de base des périphériques via l’interface utilisateur du web tableau virtuel StorSimple."
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
   ms.date="04/07/2016"
   ms.author="alkohli" />

# <a name="use-the-web-ui-to-administer-your-storsimple-virtual-array"></a>Utiliser l’interface utilisateur Web pour administrer votre tableau virtuel StorSimple

![flux de processus de configuration](./media/storsimple-ova-web-ui-admin/manage4.png)

## <a name="overview"></a>Vue d’ensemble

Les didacticiels dans cet article s’appliquent à Microsoft Azure StorSimple virtuel tableau (également connu sous le StorSimple local périphérique virtuel) qui exécutent la version officielle (disponibilité générale) de mars 2016. Cet article présente certains des flux de travail complexes et tâches de gestion qui peuvent être exécutées sur le tableau virtuel StorSimple. Vous pouvez gérer le tableau virtuel StorSimple en utilisant le Gestionnaire de StorSimple service interface utilisateur (appelées du portail de l’interface utilisateur) et l’interface utilisateur web local pour l’appareil. Cet article se concentre sur les tâches que vous pouvez effectuer à l’aide de l’interface utilisateur web.

Cet article inclut les didacticiels suivants :

- Obtenir la clé de chiffrement des données de service
- Résoudre les erreurs d’installation de l’interface utilisateur web
- Générer un package de journal
- Arrêter ou redémarrer votre appareil

## <a name="get-the-service-data-encryption-key"></a>Obtenir la clé de chiffrement des données de service

Une clé de chiffrement des données de service est générée lorsque vous enregistrez votre appareil première avec le service Manager StorSimple. Cette clé est alors requis avec la clé d’inscription de service pour enregistrer des périphériques supplémentaires avec le service Manager StorSimple.

Si vous avez égaré votre clé de chiffrement de données de service et la nécessité de le retrouver, effectuez les opérations suivantes étapes décrites dans l’interface utilisateur du périphérique web local enregistré auprès de votre service.

#### <a name="to-get-the-service-data-encryption-key"></a>Pour obtenir la clé de chiffrement des données de service

1. Se connecter à l’interface utilisateur web local. Accédez à **Configuration** > **paramètres Cloud**.
  

2. Dans la partie inférieure de la page, cliquez sur **obtenir la clé de chiffrement de données de service**. Une clé s’affichent. Copiez et enregistrez cette touche.
    
    ![obtenir la clé de chiffrement de données de service 1](./media/storsimple-ova-web-ui-admin/image27.png)
   


## <a name="troubleshoot-web-ui-setup-errors"></a>Résoudre les erreurs d’installation de l’interface utilisateur web

Dans certains cas lorsque vous configurez le périphérique via le site web local l’interface utilisateur, vous pouvez rencontrer des erreurs. Pour diagnostiquer et résoudre les problèmes de ces erreurs, vous pouvez exécuter les tests de diagnostic.

#### <a name="to-run-the-diagnostic-tests"></a>Pour exécuter les tests de diagnostic

1. Dans l’interface utilisateur web local, accédez à **dépannage** > **les tests de Diagnostic**.

    ![exécuter les diagnostics 1](./media/storsimple-ova-web-ui-admin/image29.png)

2. Dans la partie inférieure de la page, cliquez sur **Exécuter les Tests de Diagnostic**. Cela ouvrira tests pour diagnostiquer les éventuels problèmes avec votre réseau, un appareil, un proxy web, heure ou paramètres de cloud. Vous serez informé que l’appareil est en cours d’exécution des tests.

3. Une fois les tests terminées, les résultats s’afficheront. L’exemple suivant montre les résultats des tests de diagnostic. Notez que les paramètres de proxy web n’ont pas configurés sur cet appareil, et par conséquent, le test de proxy web n’a pas été exécuté. Tous les tests de paramètres réseau, serveur DNS et paramètres horaires ont réussi.

    ![exécuter les diagnostics 2](./media/storsimple-ova-web-ui-admin/image30.png)

## <a name="generate-a-log-package"></a>Générer un package de journal

Un package de journal se compose de tous les journaux pertinents qui peuvent vous aider à Support Microsoft à résoudre les problèmes de n’importe quel appareil. Dans cette version, un package de journal peut être généré via l’interface utilisateur web local.

#### <a name="to-generate-the-log-package"></a>Pour générer le package de journal

1. Dans l’interface utilisateur web local, accédez à **dépannage** > **journaux système**.

    ![générer un package de journal 1](./media/storsimple-ova-web-ui-admin/image31.png)

2. Dans la partie inférieure de la page, cliquez sur **créer un package journal**. Un package de journaux du système sera créé. Cela prendra quelques minutes.

    ![générer un package de journal 2](./media/storsimple-ova-web-ui-admin/image32.png)

    Vous serez informé une fois que le package est créé avec succès, et la page est mise à jour pour indiquer l’heure et date de création du package.

    ![générer un package de journal 3](./media/storsimple-ova-web-ui-admin/image33.png)

3. Cliquez sur **Téléchargez le package de journal**. Un package zip est téléchargé sur votre système.

    ![générer un package de journal 4](./media/storsimple-ova-web-ui-admin/image34.png)

4. Vous pouvez décompressez le package téléchargé journal et afficher les fichiers journaux système.

## <a name="shut-down-and-restart-your-device"></a>Fermez et redémarrez votre appareil

Vous pouvez arrêter ou redémarrer votre appareil virtuel à l’aide de l’interface utilisateur web local. Nous recommandé avant de redémarrer, prendre les volumes ou les partages en mode hors connexion sur l’hôte, puis sur l’appareil. Cela permet de minimiser les risques d’altération des données. 

#### <a name="to-shut-down-your-virtual-device"></a>Pour arrêter votre périphérique virtuel

1. Dans l’interface utilisateur web local, accédez à la **Maintenance** > **paramètres d’alimentation**.

2. Dans la partie inférieure de la page, cliquez sur **Arrêter**.

    ![arrêt du périphérique 1](./media/storsimple-ova-web-ui-admin/image36.png)

3. Un avertissement s’affiche pour indiquer qu’un arrêt du périphérique interrompt tout IO qui étaient en cours, ce qui entraîne un temps d’arrêt. Cliquez sur l’icône de vérification ![vérifier l’icône](./media/storsimple-ova-web-ui-admin/image3.png).

    ![Avertissement d’arrêt appareil](./media/storsimple-ova-web-ui-admin/image37.png)

    Vous serez informé responsabilité l’arrêt.

    ![arrêt du périphérique en main](./media/storsimple-ova-web-ui-admin/image38.png)

    Le périphérique va s’arrêter. Si vous voulez commencer votre appareil, vous devrez effectuer cette tâche via le Gestionnaire Hyper-V.

#### <a name="to-restart-your-virtual-device"></a>Pour redémarrer votre appareil virtuel

1. Dans l’interface utilisateur web local, accédez à la **Maintenance** > **paramètres d’alimentation**.

2. Dans la partie inférieure de la page, cliquez sur **redémarrer**.

    ![redémarrage du périphérique](./media/storsimple-ova-web-ui-admin/image36.png)

3. Un avertissement s’affiche indiquant que le redémarrage de l’appareil interrompt tout IOs qui étaient en cours, ce qui entraîne un temps d’arrêt. Cliquez sur l’icône de vérification ![vérifier l’icône](./media/storsimple-ova-web-ui-admin/image3.png).

    ![avertissement relatif au redémarrage](./media/storsimple-ova-web-ui-admin/image37.png)

    Vous serez informé responsabilité le redémarrage.

    ![redémarrage](./media/storsimple-ova-web-ui-admin/image39.png)

    Lors du redémarrage est en cours, vous perdez la connexion à l’interface utilisateur. Vous pouvez surveiller le redémarrage en actualisant régulièrement l’interface utilisateur. Par ailleurs, vous pouvez surveiller l’état de redémarrer l’appareil via le Gestionnaire Hyper-V.

## <a name="next-steps"></a>Étapes suivantes

Découvrez comment [utiliser le service StorSimple Manager pour gérer votre appareil](storsimple-manager-service-administration.md).

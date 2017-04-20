<properties
   pageTitle="Gérer vos volumes StorSimple (U2) | Microsoft Azure"
   description="Explique comment ajouter, modifier, surveiller et supprimer des volumes StorSimple et mettez-les hors ligne si nécessaire."
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
   ms.workload="NA"
   ms.date="10/28/2016"
   ms.author="alkohli" />

# <a name="use-the-storsimple-manager-service-to-manage-volumes-update-2"></a>Utiliser le service StorSimple Manager pour gérer les volumes (mise à jour 2)

[AZURE.INCLUDE [storsimple-version-selector-manage-volumes](../../includes/storsimple-version-selector-manage-volumes.md)]

## <a name="overview"></a>Vue d’ensemble

Ce didacticiel explique comment utiliser le service Manager StorSimple pour créer et gérer des volumes sur le périphérique de StorSimple et un périphérique virtuel StorSimple avec 2 mise à jour est installé.

Le service Manager StorSimple est une extension dans le portail classique Azure qui vous permet de gérer votre solution StorSimple à partir d’une interface web unique. En plus de la gestion des volumes, vous pouvez utiliser le service Manager StorSimple pour créer et gérer les services StorSimple, afficher et gérer les appareils mobiles, afficher les alertes et afficher et gérer les stratégies de sauvegarde et le catalogue de sauvegarde.

## <a name="volume-types"></a>Types de volume

StorSimple volumes peuvent être :

- **Localement épinglées volumes**: les données dans ces volumes restent sur le périphérique StorSimple local à tout moment.
- **Volumes hiérarchisé**: des données dans ces volumes pouvant renverser dans le cloud.

Un volume archivage est un type de volume hiérarchisé. La taille de segment déduplication plus grande utilisée pour les volumes archivage permet de transférer des segments plus importants de données dans le cloud. 

Si nécessaire, vous pouvez modifier le volume type locale, hiérarchisé ou à partir de plusieurs niveaux locaux. Pour plus d’informations, accédez à [Modifier le type de volume](#change-the-volume-type).

### <a name="locally-pinned-volumes"></a>Volumes localement épinglés

Volumes localement épinglés sont entièrement générés volumes que n'effectuer pas les données de couche dans le cloud, garantissant local garantit de données principales, indépendamment de la connectivité de cloud. Les données des volumes localement épinglés ne sont pas dédupliquées et compressées. Toutefois, des instantanés des volumes localement épinglés sont dédupliqués. 

Volumes localement épinglés sont entièrement mis en service ; Par conséquent, vous devez disposer de suffisamment d’espace sur votre appareil lorsque vous les créez. Vous pouvez prévoir localement épinglés volumes jusqu'à une taille maximale de 8 To sur le périphérique StorSimple 8100 et 20 To ou sur l’appareil 8600. StorSimple se réserve l’espace restant locale d’instantanés, les métadonnées et traitement des données sur le périphérique. Vous pouvez augmenter la taille d’un volume localement épinglée à l’espace maximum disponible, mais vous ne pouvez pas diminuer la taille d’un volume une fois créé.

Lorsque vous créez un volume localement épinglé, l’espace disponible pour la création des volumes hiérarchisés est réduite. L’inverse est également vrai : Si vous avez des volumes hiérarchisés existants, l’espace disponible pour créer localement épinglées volumes sera inférieur les limites maximales indiquées ci-dessus. Pour plus d’informations sur les volumes locaux, consultez les [questions fréquemment posées sur les volumes localement épinglés](storsimple-local-volume-faq.md).   

### <a name="tiered-volumes"></a>Volumes hiérarchisés

Volumes hiérarchisés sont des volumes légèrement mis en service dans lequel les données fréquemment consultées restent locales sur l’appareil et moins de données fréquemment utilisés sont automatiquement hiérarchisées vers le cloud. Contrôle de l’approvisionnement est une technologie de virtualisation dans laquelle stockage disponible apparaît dépassement ressources physiques. Au lieu de réservation d’espace de stockage suffisant à l’avance, StorSimple utilise le contrôle de l’approvisionnement pour allouer simplement suffisamment d’espace pour répondre aux exigences actuelles. La nature élastique de stockage cloud facilite cette approche car StorSimple pouvez augmenter ou diminuer le stockage cloud pour répondre à la demande de modification.

Si vous utilisez le volume hiérarchisé pour les données d’archivage, la case à cocher **utiliser ce volume de données archivage moins fréquemment consultées** modifie la taille de segment déduplication pour le volume de votre à 512 Ko. Si vous ne sélectionnez pas cette option, le volume hiérarchisé correspondant utilise une taille de segment de 64 Ko. Une plus grande taille de segment déduplication permet au périphérique afin d’accélérer le transfert de données d’archivage volumineux dans le cloud.

>[AZURE.NOTE] Archivage volumes créées avec une version 2 avant mise à jour de StorSimple seront importées comme hiérarchisé avec la case à cocher archivage.

### <a name="provisioned-capacity"></a>Capacité de mise en service

Consultez le tableau suivant pour une capacité maximale généré pour chaque type d’appareil et le volume. (Notez que les volumes localement épinglés ne sont pas disponibles sur un appareil virtuel.)

|             | Taille maximale hiérarchisé du volume | Maximum localement épinglées taille du volume |
|-------------|----------------------------|------------------------------------|
| **Périphériques physiques** |       |       |
| 8100                 | 64 TO | 8 TO |
| 8600                 | 64 TO | 20 TO |
| **Périphériques virtuels**  |       |       |
| 8010                | 30 TO | N/A   |
| 8020               | 64 TO | N/A   |

## <a name="the-volumes-page"></a>La page Volumes

La page **Volumes** permet de gérer les volumes de stockage qui sont configurés sur le périphérique Microsoft Azure StorSimple pour vos initiateurs (serveurs). Il affiche la liste des volumes sur votre appareil StorSimple.

 ![Page volumes](./media/storsimple-manage-volumes-u2/VolumePage.png)

Un volume est constitué d’une série d’attributs :

- **Nom du volume** – un nom descriptif qui doit être unique et vous aide à identifier le volume. Ce nom est également utilisé dans les rapports d’analyse lorsque vous filtrez sur un volume spécifique.

- **État** – peut être en ligne ou hors connexion. Si le volume est en mode hors connexion, il n’est pas visible aux initiateurs (serveurs) qui sont autorisés à accéder à utiliser le volume.

- **Capacité** – indique le volume total de données pouvant être stockés par l’initiateur (serveur). Volumes localement épinglées entièrement sa mise en service et se trouvent sur le périphérique StorSimple. Volumes hiérarchisés sont légèrement sa mise en service et les données sont dédupliquées. Avec les volumes exactement générés, votre appareil n’allouer préalable capacité de stockage physique en interne ou sur le nuage en fonction de la capacité du volume configuré. La capacité du volume est attribuée et utilisée à la demande.

- **Type** – indique si le volume est **hiérarchisé** (par défaut) ou **épinglés localement**.

- **Sauvegarde** – indique si une stratégie de sauvegarde par défaut existe pour le volume.

- **Accès** – spécifie les initiateurs (serveurs) qui sont autorisés à accéder à ce volume. Initiateurs qui ne sont pas membres d’enregistrement de contrôle d’accès (blocage) associé le volume ne verront pas le volume.

- **Surveillance** – spécifie ou non un volume est en cours d’analyse. Un volume aura surveillance activé par défaut lors de sa création. Surveillance entraîne, cependant, être désactivée pour un cloner volume. Pour activer le contrôle pour un volume, suivez les instructions dans le [moniteur un volume](#monitor-a-volume). 

Suivez les instructions dans ce didacticiel pour effectuer les tâches suivantes :

- Ajouter un volume 
- Modifier un volume 
- Modifier le type de volume
- Supprimer un volume 
- Mettre un volume hors connexion 
- Analyser un volume 

## <a name="add-a-volume"></a>Ajouter un volume

Vous avez [créé un volume](storsimple-deployment-walkthrough-u2.md#step-6-create-a-volume) lors du déploiement de votre solution StorSimple. Ajout d’un volume est une procédure similaire.

#### <a name="to-add-a-volume"></a>Pour ajouter un volume

1. Dans la page **appareils** , sélectionnez le périphérique, double-cliquez dessus, puis cliquez sur l’onglet **Conteneurs de Volume** .

2. Sélectionnez un conteneur de volume dans la liste et double-cliquez dessus pour accéder aux volumes associés au conteneur.

3. Cliquez sur **Ajouter** en bas de la page. Ajouter qu'un Assistant volume démarre.

     ![Assistant Ajout de volume paramètres de base](./media/storsimple-manage-volumes-u2/TieredVolEx.png)

4. Dans l’Assistant Ajouter un volume, sous **Paramètres de base**, procédez comme suit :

  1. Fournir un **nom** pour le volume.
  2. Sélectionnez un **Type d’utilisation** dans la liste déroulante. Pour les charges de travail nécessitant des données soient disponibles localement sur le périphérique à tout moment, sélectionnez **Épinglé localement**. Pour tous les autres types de données, sélectionnez **hiérarchisé**. (**Hiérarchisé** est la valeur par défaut).
  3. Si vous avez sélectionné **hiérarchisé** à l’étape 2, vous pouvez sélectionner la case à cocher **utiliser ce volume de données archivage moins fréquemment consultées** pour configurer un volume archivage.
  4. Entrez la **Capacité de sa mise en service** pour le volume en Go ou to. Voir [Provisioned capacité](#provisioned-capacity) taille maximale pour chaque type d’appareil et le volume. Examinez la **Capacité disponible** pour déterminer la quantité de stockage est actuellement disponible sur votre appareil.

5. Cliquez sur l’icône de flèche![Icône de flèche](./media/storsimple-manage-volumes-u2/HCS_ArrowIcon.png). Si vous configurez un volume épinglé localement, vous verrez le message suivant.

    ![Modifier des messages de type de Volume](./media/storsimple-manage-volumes-u2/LocalVolEx.png)
   
5. Cliquez sur l’icône de flèche ![icône de flèche](./media/storsimple-manage-volumes-u2/HCS_ArrowIcon.png)à nouveau pour accéder à la page **Paramètres supplémentaires** .

    ![Ajouter d’autres paramètres de l’Assistant Volume](./media/storsimple-manage-volumes-u2/AddVolume2.png)<br>

6. Sous **Paramètres supplémentaires**, ajoutez un nouvel enregistrement de contrôle d’accès (blocage) :
  
  1. Sélectionnez un enregistrement du contrôle d’accès (blocage) dans la liste déroulante. Vous pouvez également ajouter un nouveau blocage. ACRs déterminent quels hôtes peuvent accéder à vos volumes en comparant l’hôte de nom qualifié avec répertoriés dans l’enregistrement. Si vous ne spécifiez pas un blocage, vous verrez le message suivant.

        ![Spécifier le blocage](./media/storsimple-manage-volumes-u2/SpecifyACR.png)

  2. Nous vous conseillons de sélectionner la case à cocher **activer une sauvegarde par défaut pour ce volume** .
  3. Cliquez sur l’icône de vérification ![Icône de vérification](./media/storsimple-manage-volumes-u2/HCS_CheckIcon.png) Pour créer le volume avec les paramètres spécifiés.

Le volume de votre nouvelle est maintenant prêt à utiliser.

>[AZURE.NOTE] Si vous créez un volume localement épinglé, puis créez un autre volume localement épinglé immédiatement par la suite, la création d’un volume les tâches sont exécutées dans un ordre séquentiel. La première tâche de création de volume doit se terminer avant que le travail de création de volume suivant puisse commencer.

## <a name="modify-a-volume"></a>Modifier un volume

Modifier un volume lorsque vous avez besoin développer ou modifier les hôtes y accèdent le volume.

> [AZURE.IMPORTANT] 
>
> - Si vous modifiez la taille du volume sur le périphérique, la taille du volume doit être modifié sur l’hôte également. 
> - Les étapes côté hôte décrites ici sont appliquent aux Windows Server 2012 (2012R2). Procédures pour Linux ou d’autres systèmes d’exploitation hôte sera différents. Reportez-vous aux instructions du système d’exploitation hôte lorsque vous modifiez le volume sur un hôte exécutant un autre système d’exploitation. 

#### <a name="to-modify-a-volume"></a>Pour modifier un volume

1. Dans la page **appareils** , sélectionnez le périphérique, double-cliquez dessus, puis cliquez sur l’onglet **Conteneurs de Volume** .

2. Sélectionnez un conteneur de volume dans la liste et double-cliquez dessus pour afficher les volumes associés au conteneur.

3. Sélectionnez un volume, puis dans la partie inférieure de la page, cliquez sur **Modifier**. L’Assistant Modification de volume démarre.

4. Dans l’Assistant Modification de volume, sous **Paramètres de base**, vous pouvez procédez comme suit :

  - Modifier le **nom**.
  - Convertir le **Type d’utilisation** de localement épinglées à plusieurs niveaux ou à partir de plusieurs niveaux à localement épinglées (pour plus d’informations, consultez [Modifier le type de volume](#change-the-volume-type) ).
  - Augmenter **capacité de sa mise en service**. La **Capacité de sa mise en service** peut uniquement être augmentée. Vous ne pouvez pas réduire un volume après sa création.

5. Sous **Paramètres supplémentaires**, vous pouvez modifier le blocage autant que le volume est en mode hors connexion. Si le volume est en ligne, vous devez mettre hors connexion tout d’abord. Avant de modifier le blocage, consultez les étapes de [prendre un volume en mode hors connexion](#take-a-volume-offline) .

    > [AZURE.NOTE] Vous ne pouvez pas modifier l’option **activer une sauvegarde par défaut** pour le volume.

6. Enregistrez vos modifications en cliquant sur l’icône ![icône de vérification](./media/storsimple-manage-volumes-u2/HCS_CheckIcon.png). Le portail classique Azure affiche un message de volume mise à jour. Il affiche un message de réussite lorsque le volume a été mis à jour avec succès.

7. Si vous développez un volume, procédez comme suit sur votre ordinateur hôte Windows :

   1. Accédez à la **gestion de l’ordinateur** ->**Gestion des disques**.
   2. Cliquez sur **Gestion des disques** et sélectionnez **Analyser les disques**.
   3. Dans la liste des disques, sélectionnez le volume mis à jour, avec le bouton droit et puis sélectionnez **Étendre le Volume**. L’Assistant étendre le Volume démarre. Cliquez sur **suivant**.
   4. Exécuter l’Assistant en acceptant les valeurs par défaut. Une fois l’Assistant terminé, le volume doit indiquer la taille accrue.

    >[AZURE.NOTE] Si vous développez un volume localement épinglé, puis un autre localement épinglée volume immédiatement par la suite, les tâches d’extension volume exécutent dans un ordre séquentiel. La première tâche d’extension volume doit se terminer avant que la tâche d’extension volume suivante puisse commencer.

![Vidéo disponible](./media/storsimple-manage-volumes-u2/Video_icon.png) **vidéo disponible**

Pour visionner une vidéo qui montre comment développer un volume, cliquez sur [ici](https://azure.microsoft.com/documentation/videos/expand-a-storsimple-volume/).

## <a name="change-the-volume-type"></a>Modifier le type de volume

Vous pouvez modifier le type de volume à partir de plusieurs niveaux épinglée localement ou à partir de localement par épinglées à plusieurs niveaux. Toutefois, cette conversion ne doit pas être une occurrence fréquente. Certaines raisons de la conversion d’un volume de hiérarchisé localement épinglée sont :

- Garanties locales concernant la disponibilité des données et de performances
- Suppression des problèmes de connectivité cloud et de latence cloud.

En règle générale, il s’agit des petits volumes existants auquel vous souhaitez accéder fréquemment. Un volume localement épinglé est entièrement sa mise en service lorsqu’elle est créée. Si vous convertissez un volume hiérarchisé vers un volume localement épinglé, StorSimple vérifie que vous disposez d’un espace suffisant sur votre appareil avant le début de la conversion. Si vous avez un espace insuffisant, vous recevrez un message d’erreur et l’opération sera annulée. 

> [AZURE.NOTE] Avant de commencer une conversion de niveaux épinglée localement, assurez-vous que vous considérez comme l’espace disque de vos autres charges de travail. 

Vous souhaiterez peut-être modifier un volume localement épinglé à un volume hiérarchisé si vous avez besoin d’espace supplémentaire pour la mise en service des autres volumes. Lorsque vous convertissez le volume localement épinglé à plusieurs niveaux, la capacité disponible sur les augmentations appareil par la taille de la capacité finale. Si les problèmes de connectivité empêchent la conversion d’un volume à partir du type local au type de plusieurs niveaux, le volume local présentera les propriétés d’un volume hiérarchisé jusqu'à ce que la conversion est terminée. C’est parce que certaines données peuvent avoir répandu dans le cloud. Ces données projections continuent à occuper un espace local sur le périphérique ne peut pas être libéré jusqu'à ce que l’opération est redémarrée et terminée.

>[AZURE.NOTE] Conversion d’un volume peut prendre du temps et vous ne pouvez pas annuler une conversion après son démarrage. Le volume reste en ligne pendant la conversion et vous pouvez effectuer des sauvegardes, mais vous ne pouvez pas développer ou restaurer le volume alors que la conversion est en cours.  

Conversion d’un hiérarchisé vers un volume localement épinglé peut affecter les performances de l’unité. En outre, les facteurs suivants peuvent augmenter le temps que nécessaire pour terminer la conversion :

- Il est bande passante insuffisante.

- Il n’existe aucune sauvegarde.

Pour réduire les effets des facteurs suivants :

- Passez en revue vos stratégies de bande passante et vérifiez qu’il existe une 40 Mbps la bande passante dédiée.
- Planifier la conversion en dehors des heures.
- Prendre un instantané de cloud avant de commencer la conversion.

Si vous convertissez plusieurs volumes (prenant en charge de différentes charges de travail), vous devez hiérarchiser la conversion du volume afin que les volumes de priorité supérieure sont converties tout d’abord. Par exemple, vous devez convertir volumes hébergeant des machines virtuelles (machines virtuelles) ou les volumes avec les charges de travail SQL avant de convertir des volumes avec les charges de travail de partage de fichier.

#### <a name="to-change-the-volume-type"></a>Pour modifier le type de volume

1. Dans la page **appareils** , sélectionnez le périphérique, double-cliquez dessus, puis cliquez sur l’onglet **Conteneurs de Volume** .

2. Sélectionnez un conteneur de volume dans la liste et double-cliquez dessus pour afficher les volumes associés au conteneur.

3. Sélectionnez un volume, puis dans la partie inférieure de la page, cliquez sur **Modifier**. L’Assistant Modification de volume démarre.

4. Dans la page **Paramètres de base** , modifiez le type d’utilisation en sélectionnant le nouveau type dans la liste déroulante **Type d’utilisation** .

    - Si vous modifiez le type de mise en attente **localement**, StorSimple vérifie s’il existe une capacité suffisante.
    - Si vous changez le type **hiérarchisé** et ce volume sera utilisé pour les données d’archivage, activez la case à cocher **utiliser ce volume de données archivage moins fréquemment consultées** .

        ![Case à cocher archive](./media/storsimple-manage-volumes-u2/ModifyTieredVolEx.png)

5. Cliquez sur l’icône de flèche ![icône de flèche](./media/storsimple-manage-volumes-u2/HCS_ArrowIcon.png) pour accéder à la page **Paramètres supplémentaires** . Si vous configurez un volume localement épinglé, le message suivant s’affiche.

    ![Modifier des messages de type de Volume](./media/storsimple-manage-volumes-u2/ModifyLocalVolEx.png)

6. Cliquez sur l’icône de flèche ![icône de flèche](./media/storsimple-manage-volumes-u2/HCS_ArrowIcon.png) Pour continuer.

7. Cliquez sur l’icône de vérification ![Icône de vérification](./media/storsimple-manage-volumes-u2/HCS_CheckIcon.png) Pour démarrer le processus de conversion. Le portail Azure affiche un message de volume mise à jour. Il affiche un message de réussite lorsque le volume a été mis à jour avec succès.

## <a name="take-a-volume-offline"></a>Mettre un volume hors connexion

Vous devrez peut-être mettre un volume hors connexion lorsque vous envisagez de le modifier ou supprimer. Lorsqu’un volume est en mode hors connexion, il n’est pas disponible pour l’accès en lecture / écriture. Vous devez prendre le volume en mode hors connexion sur l’hôte, ainsi que sur l’appareil. 

#### <a name="to-take-a-volume-offline"></a>Mettre un volume hors connexion

1. Vérifiez que le volume en question n’est pas en cours d’utilisation avant de passer en mode hors connexion.

2. Prendre le volume en mode hors connexion sur l’hôte du premier. Cela supprime tout risque d’altération des données sur le volume. Pour obtenir la procédure spécifique, reportez-vous aux instructions pour votre système d’exploitation hôte.

3. Une fois que l’hôte est en mode hors connexion, prendre le volume sur le périphérique en mode hors connexion, effectuez les opérations suivantes :

  1. Dans la page **appareils** , sélectionnez le périphérique, double-cliquez dessus, puis cliquez sur l’onglet **Conteneurs de Volume** . L’onglet **Conteneurs Volume** répertorie sous forme de tous les conteneurs de volume qui sont associées à l’appareil.
  2. Sélectionnez un conteneur de volume, puis cliquez dessus pour afficher la liste de tous les volumes dans le conteneur.
  3. Sélectionnez un volume, puis cliquez sur **Déconnecter**.
  4. Lorsque vous êtes invité à confirmer, cliquez sur **Oui**. Le volume doit maintenant être en mode hors connexion.

    Une fois un volume est en mode hors connexion, l’option **Mettre en ligne** devient disponible.

> [AZURE.NOTE] La commande **Déconnecter** envoie une demande à l’appareil mettre le volume hors connexion. Si hôtes utilisent encore le volume, cela se traduit par des liens rompus, mais déconnecter le volume ne peut être. 

## <a name="delete-a-volume"></a>Supprimer un volume

> [AZURE.IMPORTANT] Vous pouvez supprimer un volume uniquement s’il est en mode hors connexion.

Procédez comme suit pour supprimer un volume.

#### <a name="to-delete-a-volume"></a>Pour supprimer un volume

1. Dans la page **appareils** , sélectionnez le périphérique, double-cliquez dessus, puis cliquez sur l’onglet **Conteneurs de Volume** .

2. Sélectionnez le conteneur de volume qui comporte le volume que vous voulez supprimer. Cliquez sur le conteneur de volume pour accéder à la page **Volumes** .

3. Tous les volumes associés à ce conteneur sont affichés dans un format tabulaire. Vérifiez l’état du volume que vous voulez supprimer. Si le volume que vous voulez supprimer n’est pas en mode hors connexion, mettez hors connexion tout d’abord, suivant les étapes de [prendre un volume en mode hors connexion](#take-a-volume-offline).

4. Une fois que le volume est en mode hors connexion, cliquez sur **Supprimer** dans la partie inférieure de la page.

5. Lorsque vous êtes invité à confirmer, cliquez sur **Oui**. Le volume va être supprimé et la page **Volumes** affiche la liste des volumes dans le conteneur mis à jour.

    >[AZURE.NOTE]Si vous supprimez un volume localement épinglé, l’espace disponible pour les nouveaux volumes ne peut pas être mis à jour immédiatement. Le Manager StorSimple Service met à jour régulièrement l’espace local disponible. Nous vous recommandons de que vous attendez quelques minutes avant d’essayer de créer le nouveau volume.<br> En outre, si vous supprimez un volume localement épinglé, puis supprimez un autre localement épinglée volume immédiatement par la suite, la suppression de volume les tâches sont exécutées dans un ordre séquentiel. La première tâche de suppression de volume doit se terminer avant que la tâche de suppression en volume suivante puisse commencer.
 
## <a name="monitor-a-volume"></a>Analyser un volume

Volume surveillance vous permet de collecter des statistiques je/O-liés pour un volume. Analyse est activée par défaut pour les 32 premiers volumes que vous créez. Suivi des volumes supplémentaires est désactivée par défaut. Suivi des volumes clonés est également désactivée par défaut.

Procédez comme suit pour activer ou désactiver l’analyse d’un volume.

#### <a name="to-enable-or-disable-volume-monitoring"></a>Pour activer ou désactiver le contrôle de volume

1. Dans la page **appareils** , sélectionnez le périphérique, double-cliquez dessus, puis cliquez sur l’onglet **Conteneurs de Volume** .

2. Sélectionnez le conteneur de volume dans lequel se trouve le volume, puis cliquez sur le conteneur de volume pour accéder à la page **Volumes** .

3. Tous les volumes associés à ce conteneur sont répertoriées dans l’affichage sous forme de tableau. Cliquez sur et sélectionnez le volume ou cloner volume.

4. Dans la partie inférieure de la page, cliquez sur **Modifier**.

5. Dans l’Assistant Modifier le Volume, sous **Paramètres de base**, sélectionnez **Activer** ou **désactiver** dans la liste déroulante de **surveillance** .

## <a name="next-steps"></a>Étapes suivantes

- Découvrez comment [cloner un volume StorSimple](storsimple-clone-volume.md).

- Découvrez comment [utiliser le service du Gestionnaire de StorSimple pour administrer votre périphérique StorSimple](storsimple-manager-service-administration.md).

 

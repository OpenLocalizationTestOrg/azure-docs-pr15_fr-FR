<properties
   pageTitle="Basculement de récupération et appareil urgence pour votre tableau virtuel StorSimple"
   description="En savoir plus sur la façon de basculer votre tableau virtuel StorSimple."
   services="storsimple"
   documentationCenter="NA"
   authors="alkohli"
   manager="carmonm"
   editor=""/>

<tags
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="06/07/2016"
   ms.author="alkohli"/>

# <a name="disaster-recovery-and-device-failover-for-your-storsimple-virtual-array"></a>Basculement de récupération et appareil urgence pour votre tableau virtuel StorSimple


## <a name="overview"></a>Vue d’ensemble

Cet article décrit la récupération d’urgence pour votre tableau virtuel Microsoft Azure StorSimple (également connu sous le StorSimple local périphérique virtuel), y compris les étapes détaillées requises pour basculer vers un autre périphérique virtuel en cas de sinistre. Un basculement permet de migrer vos données à partir d’un appareil *source* dans le centre de données vers un autre périphérique *cible* situé dans le même ou un autre emplacement géographique. Le basculement d’appareil est pour l’ensemble de l’unité. Pendant le basculement, les données de cloud pour le périphérique source changent la propriété à celle de l’équipement cible.

Basculement entre périphériques orchestré via la fonctionnalité de reprise après sinistre et est lancée à partir de la page **appareils** . Cette page classifie tous les appareils StorSimple connectés à votre service Manager StorSimple. Pour chaque périphérique, le nom convivial, état, capacité maximale et mis en service, type et modèle sont affichés.

![](./media/storsimple-ova-failover-dr/image15.png)

Cet article s’appliquent aux tableaux virtuel StorSimple uniquement. Pour basculer sur un appareil 8000 série, accédez à [basculement et sinistre de votre appareil StorSimple](storsimple-device-failover-disaster-recovery.md).


## <a name="what-is-disaster-recovery"></a>Qu’est reprise ?

Dans un scénario de reprise urgence, le périphérique principal cesse de fonctionner. Dans ce cas, vous pouvez déplacer les données de cloud associées au périphérique a échoué vers un autre périphérique en utilisant le périphérique principal comme *source* et spécifie un autre périphérique comme *cible*. Ce processus est appelé le *basculement*. Pendant le basculement, tous les volumes ou les partages du périphérique source modifient la propriété et sont transférés à l’appareil cible. Aucun filtrage des données n’est autorisée.

DR équivaut à une restauration périphérique complet à l’aide de la hiérarchisation du basée sur une carte thermique et de suivi. Une carte thermique est définie en attribuant une valeur de chaleur aux données basé sur lire et écrire des modèles. Cette chaleur mapper puis niveaux les segments de données les plus faibles thermique dans le cloud tout d’abord en conservant les segments de données thermique haute (plus utilisé) au niveau local. Pendant un DR, la carte thermique est utilisée pour restaurer et réalimenter les données à partir du cloud. Le périphérique extrait tous les volumes/partages dans la dernière sauvegarde récente (comme déterminé en interne) et effectue une restauration à partir de cette sauvegarde. L’ensemble du processus DR est déclenché par l’appareil.


## <a name="prerequisites-for-device-failover"></a>Conditions préalables pour le basculement d’appareil


### <a name="prerequisites"></a>Conditions préalables

Pour le basculement de n’importe quel appareil, les conditions préalables suivantes doivent être satisfaites :

- Le périphérique source doit se trouver dans un état **désactivé** .

- Le périphérique cible doit afficher sous la forme **Active** dans le portail classique Azure. Vous avez besoin configurer un périphérique virtuel cible de la capacité même ou une version ultérieure. Vous devez ensuite utiliser l’interface utilisateur web local pour configurer et inscrire correctement le périphérique virtuel.

    > [AZURE.IMPORTANT] N’essayez pas de configurer le périphérique virtuel inscrit via le service en cliquant sur **le programme d’installation complet**. Aucune configuration de périphérique ne doit être effectuée via le service.

- Le périphérique source et cible doivent être du même type. Vous pouvez uniquement basculer vers un périphérique virtuel configuré comme un serveur de fichiers vers un autre serveur de fichiers. Il en est de même pour un serveur iSCSI.

- Pour un serveur de fichiers DR, nous vous recommandons de rejoindre le périphérique cible au même domaine que celui de la source afin que les autorisations de partage sont automatiquement résolues. Uniquement le basculement d’un équipement cible dans le même domaine est pris en charge dans cette version.

### <a name="other-considerations"></a>Autres considérations

- Nous vous recommandons de prendre les volumes ou les partages sur le périphérique source en mode hors connexion.

- S’il s’agit d’un basculement planifié, nous vous recommandons d’effectuer une sauvegarde de l’appareil et de poursuivre le basculement pour limiter la perte de données. S’il s’agit d’un basculement non planifié, la sauvegarde la plus récente servira à restaurer l’appareil.

- Les périphériques cibles disponibles pour DR sont dotées de la capacité égale ou supérieure par rapport à l’appareil source. Les appareils que vous êtes connectés à votre service, mais ne remplissent pas les critères de suffisamment d’espace ne sera pas disponibles en tant que périphériques cible.

### <a name="dr-prechecks"></a>DR prechecks

Avant le début de la DR, prechecks sont effectuées sur l’appareil. Ces vérifications permettent de vous assurer qu’aucune erreur se produit lorsque DR commence. Les prechecks sont les suivantes :

- Validation du compte de stockage

- Vérification de la connectivité cloud vers Azure

- Vérification de l’espace disponible sur le périphérique cible

- Vérifier si un périphérique iSCSI serveur source possède des noms de blocage valides, nom qualifié (ne dépasse pas 220 caractères) et mot de passe CHAP (12 et 16 caractères) correspondant aux volumes

Si d’échec des prechecks ci-dessus, vous ne pouvez pas poursuivre la DR. Vous avez besoin résoudre les problèmes, puis recommencez DR.

Une fois la DR a bien été effectuée, la propriété des données cloud sur le périphérique source est transférée vers le périphérique cible. Le périphérique source puis n’est plus disponible dans le portail. Accès à tous les volumes/partages sur le périphérique source est bloqué et l’équipement devient actif.

> [AZURE.IMPORTANT]
> 
> Bien que l’appareil n’est plus disponible, la machine virtuelle que vous avez configuré sur le système hôte utilise toujours des ressources. Une fois la DR terminée avec succès, vous pouvez supprimer cette machine virtuelle à partir de votre système d’hôte.

## <a name="fail-over-to-a-virtual-array"></a>Basculer vers un tableau virtuel

Nous vous recommandons de que vous disposez d’un autre tableau virtuel StorSimple sa mise en service, configuré via l’interface utilisateur web local et enregistré auprès du service Manager StorSimple avant d’exécuter cette procédure.


> [AZURE.IMPORTANT]
> 
> - Vous n’êtes pas autorisé à basculer d’un appareil série 8000 StorSimple vers un périphérique virtuel 1200.
> - Vous pouvez basculer sur à partir d’un périphérique virtuel FIPS Federal Information Processing Standard () activé déployé dans le portail pour le gouvernement pour un périphérique virtuel Azure portail classique. L’inverse est également vrai.

Effectuez les opérations suivantes pour rétablir l’appareil périphérique virtuel StorSimple cible.

1. Prendre des volumes/partages en mode hors connexion sur l’hôte. Reportez-vous aux instructions du système d’exploitation spécifique sur l’hôte de prendre les volumes/partages en mode hors connexion. Si pas déjà en mode hors connexion, vous devez prendre tous les volumes/partages en mode hors connexion sur le périphérique en accédant à **appareils > partages** (ou **appareil > Volumes**). Sélectionnez un partage/volume, puis cliquez sur **Déconnecter** en bas de la page. Lorsque vous êtes invité à confirmer, cliquez sur **Oui**. Répétez cette procédure pour tous les partages/volumes sur l’appareil.

2. Dans la page **appareils mobiles** , sélectionnez le périphérique source pour le basculement, puis cliquez sur **désactiver**. 
    ![](./media/storsimple-ova-failover-dr/image16.png)

3. Vous demandera confirmation. Désactivation de l’appareil est un processus permanent qui ne peuvent pas être annulé. Vous sera également relancée à prendre vos actions/volumes en mode hors connexion sur l’hôte.

    ![](./media/storsimple-ova-failover-dr/image18.png)

3. La désactivation commence dès la confirmation. Une fois la désactivation terminée avec succès, vous serez informé.

    ![](./media/storsimple-ova-failover-dr/image19.png)

4. Dans la page **appareils** , l’état du périphérique remplacera maintenant pour **désactivé**.

    ![](./media/storsimple-ova-failover-dr/image20.png)

5. Sélectionnez le périphérique désactivé et en bas de la page, cliquez sur **Basculer**.

6. Dans l’Assistant de basculement confirmer qui s’ouvre, procédez comme suit :

    1. Dans la liste déroulante des appareils disponibles, choisissez un **équipement.** Uniquement les périphériques dont est suffisant sont affichent dans la liste déroulante.

    2. Passez en revue les détails associés à l’appareil source tels que nom de l’appareil et les noms des actions ayant basculé capacité totale.

        ![](./media/storsimple-ova-failover-dr/image21.png)

7. Vérifiez **que j’accepte que le basculement est une opération définitive et une fois que le basculement a bien été effectuée, le périphérique source est supprimé**.

8. Cliquez sur l’icône du ![](./media/storsimple-ova-failover-dr/image1.png).


9. Un travail basculement sera lancé et vous serez averti. Cliquez sur **Afficher la tâche** pour contrôler le basculement.

    ![](./media/storsimple-ova-failover-dr/image22.png)

10. Dans la page **tâches** , vous verrez un travail basculement créé pour le périphérique de source. Ce travail effectue les prechecks DR.

    ![](./media/storsimple-ova-failover-dr/image23.png)

    Une fois les prechecks DR réussies, le travail basculement génèrera des travaux de restauration pour chaque partage/volume qui se trouve sur votre appareil source.

    ![](./media/storsimple-ova-failover-dr/image24.png)

11. Une fois le basculement terminé, accédez à la page **appareils** .

    un. Sélectionnez le périphérique virtuel StorSimple qui a été utilisé comme le périphérique cible pour le processus de basculement.

    b. Accédez à la page **partages** (ou **Volumes** si serveur iSCSI). Toutes les actions (volumes) à partir de l’ancien périphérique doivent maintenant être affichées.
    
    ![](./media/storsimple-ova-failover-dr/image25.png)

![](./media/storsimple-ova-failover-dr/video_icon.png)**Vidéo disponible**

Cette vidéo vous montre comment vous pouvez basculer vers un périphérique StorSimple local virtuel vers un autre périphérique virtuel.

> [AZURE.VIDEO storsimple-virtual-array-disaster-recovery]

## <a name="business-continuity-disaster-recovery-bcdr"></a>Continuité reprise (BCDR)

Un scénario de récupération (BCDR) entreprise continuité urgence se produit lorsque le centre de données Azure entière cesse de fonctionner. Cela peut affecter votre service Manager StorSimple et des dispositifs StorSimple associés.

S’il existe des appareils StorSimple qui ont été enregistrés juste avant qu’un incident s’est produite, ces appareils StorSimple devrez à supprimer. Après l’incident, vous pouvez recréer et configurer les périphériques.

## <a name="errors-during-dr"></a>Erreurs lors de le DR

**Panne de connectivité cloud pendant DR**

Si la connectivité cloud est interrompue que DR a déjà commencé et avant la restauration de l’appareil est terminée, le DR échouera et vous serez averti. Le périphérique cible qui a été utilisé pour DR est ensuite marqué comme *inutilisable.* Le même appareil cible ne peut pas être, utilisé pour DRs futures.

**Aucun équipement cible compatibles**

Si les périphériques cibles disponibles ne disposez pas de suffisamment d’espace, vous verrez une erreur à l’effet qu’il n’y a aucun équipement cible compatibles.

**Vérification préalable échecs**

Si un des prechecks n’est pas remplie, vous verrez échecs precheck.

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur la façon [d’administrer votre tableau virtuel StorSimple à l’aide de l’interface utilisateur web local](storsimple-ova-web-ui-admin.md).

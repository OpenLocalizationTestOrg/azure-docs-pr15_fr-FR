<properties 
   pageTitle="Gérer les enregistrements de contrôle d’accès pour le tableau virtuel StorSimple | Microsoft Azure"
   description="Décrit comment gérer les enregistrements de contrôle d’accès (ACRs) pour déterminer quels hôtes peuvent se connecter à un volume sur le tableau virtuel StorSimple."
   services="storsimple"
   documentationCenter=""
   authors="alkohli"
   manager="carmonm"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="05/03/2016"
   ms.author="alkohli" />

# <a name="use-the-storsimple-manager-service-to-manage-access-control-records-for-the-storsimple-virtual-array"></a>Utiliser le service StorSimple Manager pour gérer les enregistrements de contrôle d’accès pour le tableau virtuel StorSimple 

## <a name="overview"></a>Vue d’ensemble

Enregistrements de contrôle d’accès (ACRs) permettent de spécifier les hôtes qui peuvent se connecter à un volume sur le tableau virtuel StorSimple (également connu sous le StorSimple local périphérique virtuel). ACRs sont définies sur un volume spécifique, contiennent les noms qualifiés iSCSI (IQN) des hôtes. Lorsqu’un hôte tente de se connecter à un volume, le périphérique vérifie le blocage associé à ce volume pour le nom du nom qualifié, et s’il existe une correspondance, la connexion est établie. La section **enregistrements de contrôle d’accès** dans la page **configurer** affiche tous les enregistrements de contrôle access avec les IQN correspondante des hôtes.

Ce didacticiel explique les tâches courantes relatives aux blocage suivantes :

- Obtenir le nom qualifié
- Ajouter un enregistrement du contrôle d’accès 
- Modifier un enregistrement du contrôle d’accès 
- Supprimer un enregistrement du contrôle d’accès 

> [AZURE.IMPORTANT] 
> 
> - Lorsque vous affectez un blocage à un volume, veillez à ce que le volume n’est pas simultanément accessible par plusieurs hôtes non groupé, car cela pourrait endommager le volume. 
> - Lorsque vous supprimez un blocage d’un volume, assurez-vous que l’hôte correspondant n’accède pas au volume, car la suppression peut entraîner une interruption en lecture / écriture.

## <a name="get-the-iqn"></a>Obtenir le nom qualifié

Effectuez les opérations suivantes pour obtenir le nom qualifié d’un hôte Windows exécutant Windows Server 2012.

[AZURE.INCLUDE [storsimple-get-iqn](../../includes/storsimple-get-iqn.md)]

## <a name="add-an-acr"></a>Ajouter un blocage

La page **Configuration** de service Manager StorSimple vous permet d’ajouter ACRs. En règle générale, vous allez associer un blocage à un volume.

Pour plus d’informations sur l’association d’un blocage avec un volume, accédez à [Ajouter un volume](storsimple-ova-deploy3-iscsi-setup.md#step-3-add-a-volume).

>[AZURE.IMPORTANT] 
> 
>Lorsque vous affectez un blocage à un volume, veillez à ce que le volume n’est pas simultanément accessible par plusieurs hôtes non groupé, car cela pourrait endommager le volume.
 
Procédez comme suit pour ajouter un blocage.

#### <a name="to-add-an-acr"></a>Pour ajouter un blocage

1. Dans la page d’accueil de service, sélectionnez votre service, double-cliquez sur le nom du service, puis cliquez sur l’onglet **Configuration** .

    ![onglet Configuration](./media/storsimple-ova-manage-acrs/acr1.png)

2. Dans la liste sous forme de tableau, sous **enregistrements de contrôle d’accès**, indiquez un **nom** pour votre blocage.

3. Sous **iSCSI nom**, indiquez le nom de nom qualifié de votre hôte Windows. 

4. Cliquez sur **Enregistrer** dans la partie inférieure de la page pour enregistrer le blocage nouvellement créé. Vous verrez le message de confirmation suivant.

    ![message de confirmation](./media/storsimple-ova-manage-acrs/acr2.png)

5. Cliquez sur l’icône de vérification ![vérifier l’icône](./media/storsimple-ova-manage-acrs/check-icon.png). Dans la liste sous forme de tableau est mise à jour pour refléter cet ajout.

## <a name="edit-an-acr"></a>Modifier un blocage

La page de **Configuration** dans le portail classique Azure vous permet de modifier ACRs. 

> [AZURE.NOTE] Vous devez modifier uniquement ces ACRs qui ne sont pas en cours d’utilisation. Pour modifier un blocage associé à un volume qui est actuellement en cours d’utilisation, vous devez tout d’abord prendre le volume en mode hors connexion.

Effectuez les opérations suivantes pour modifier un blocage.

#### <a name="to-edit-an-acr"></a>Pour modifier un blocage

1. Dans la page d’accueil de service, sélectionnez votre service, double-cliquez sur le nom du service, puis cliquez sur l’onglet **Configuration** .

2. Dans la liste des enregistrements de contrôle d’accès sous forme de tableau, pointez sur le blocage que vous souhaitez modifier.

3. Entrez un nouveau nom et/ou nom qualifié pour le blocage.

4. Cliquez sur **Enregistrer** dans la partie inférieure de la page pour enregistrer le blocage modifiée. Un message de confirmation s’affiche. 

5. Cliquez sur l’icône de vérification ![vérifier l’icône](./media/storsimple-ova-manage-acrs/check-icon.png). Dans la liste sous forme de tableau est mise à jour pour refléter ce changement.

## <a name="delete-an-access-control-record"></a>Supprimer un enregistrement du contrôle d’accès

La page de **Configuration** dans le portail classique Azure vous permet de supprimer ACRs. 

> [AZURE.NOTE] 
> 
> - Vous devez supprimer uniquement ces ACRs qui ne sont pas en cours d’utilisation. Pour supprimer un blocage associé à un volume qui est actuellement en cours d’utilisation, vous devez tout d’abord prendre le volume en mode hors connexion.
> - Lorsque vous supprimez un blocage d’un volume, assurez-vous que l’hôte correspondant n’accède pas au volume, car la suppression peut entraîner une interruption en lecture / écriture.

Procédez comme suit pour supprimer un enregistrement du contrôle d’accès.

#### <a name="to-delete-an-access-control-record"></a>Pour supprimer un enregistrement de contrôle d’accès

1. Dans la page d’accueil de service, sélectionnez votre service, double-cliquez sur le nom du service, puis cliquez sur l’onglet **Configuration** .

2. Dans la liste des enregistrements de contrôle d’accès (ACRs) sous forme de tableau, pointez sur le blocage que vous souhaitez supprimer.

3. Une icône Supprimer (**x**) s’affichent dans la colonne de droite extrême pour le blocage que vous sélectionnez. Cliquez sur l’icône **x** pour supprimer le blocage. Vous verrez le message de confirmation suivant.

    ![message de confirmation](./media/storsimple-ova-manage-acrs/acr3.png)

5. Cliquez sur l’icône de vérification ![vérifier l’icône](./media/storsimple-ova-manage-acrs/check-icon.png). Dans la liste sous forme de tableau est mise à jour pour refléter la suppression.

## <a name="next-steps"></a>Étapes suivantes

- Plus d’informations sur [l’Ajout des volumes et configuration ACRs](storsimple-ova-deploy3-iscsi-setup.md#step-3-add-a-volume).

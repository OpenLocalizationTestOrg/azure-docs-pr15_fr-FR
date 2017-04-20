<properties 
   pageTitle="Gérer votre compte de stockage StorSimple | Microsoft Azure"
   description="Explique comment vous pouvez utiliser la page Gestionnaire StorSimple configurer pour ajouter, modifier, supprimer ou faire pivoter les clés de sécurité pour un compte de stockage."
   services="storsimple"
   documentationCenter="NA"
   authors="SharS"
   manager="carmonm"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD"
   ms.date="04/29/2016"
   ms.author="v-sharos" />

# <a name="use-the-storsimple-manager-service-to-manage-your-storage-account"></a>Utiliser le service StorSimple Manager pour gérer votre compte de stockage

## <a name="overview"></a>Vue d’ensemble

La page **configurer** présente tous les paramètres de service global pouvant être créées dans le service Manager StorSimple. Ces paramètres peuvent être appliqués à tous les appareils connectés au service et incluent :

- Comptes de stockage 
- Modèles de bande passante 
- Enregistrements de contrôle d’accès 

Ce didacticiel décrit comment vous pouvez utiliser la page **configurer** pour ajouter, modifier ou supprimer des comptes de stockage ou faire pivoter les clés de sécurité pour un compte de stockage.

 ![Configurer la page](./media/storsimple-manage-storage-accounts/HCS_ConfigureService.png)  

Comptes de stockage contiennent les informations d’identification que le périphérique utilise pour accéder à votre compte de stockage auprès de votre fournisseur de service cloud. Pour les comptes de stockage de Microsoft Azure, il s’agit des informations d’identification telles que le nom du compte et la clé primaire access. 

Dans la page **configurer** , tous les comptes de stockage qui sont créés pour l’abonnement de facturation sont affichés dans un format tabulaire contenant les informations suivantes :

- **Nom** : le nom unique affecté au compte lors de sa création.
- **Le protocole SSL activé** – si le SSL est activée et communication appareil-nuage est sur le canal sécurisé.
- **Utilisé par** : le nombre de volumes à l’aide du compte de stockage.

Tâches courantes relatives aux comptes de stockage qui peuvent être effectuées dans la page **configurer** sont :

- Ajouter un compte de stockage 
- Modifier un compte de stockage 
- Supprimer un compte de stockage 
- Rotation des clés de comptes de stockage 

## <a name="types-of-storage-accounts"></a>Types de comptes de stockage

Il existe trois types de comptes de stockage qui peuvent être utilisés avec votre appareil StorSimple.

- **Comptes de stockage généré** – comme le nom l’indique, ce type de compte de stockage est généré automatiquement lorsque vous créez le service. Pour en savoir plus sur la création de ce compte de stockage, voir [étape 1 : créer un nouveau service](storsimple-deployment-walkthrough-u1.md#step-1-create-a-new-service) dans [déployer votre appareil StorSimple local](storsimple-deployment-walkthrough.md). 
- **Comptes de stockage dans l’abonnement au service** , voici les comptes de stockage Azure qui sont associés à l’abonnement même que celle du service. Pour en savoir plus sur le stockage de fonctionnement de ces comptes sont créés, voir [à propos des comptes d’espace de stockage Azure](../storage/storage-create-storage-account.md). 
- **Comptes de stockage en dehors de l’abonnement au service** – il s’agit des comptes de stockage Azure qui ne sont pas associés à votre service et probablement existantes avant du service a été créé.

## <a name="add-a-storage-account"></a>Ajouter un compte de stockage

Vous pouvez ajouter un compte de stockage en fournissant un nom convivial unique et les informations d’accès qui sont liées à du compte de stockage (avec le fournisseur de services cloud spécifié). Vous avez également la possibilité d’activer le mode de secure sockets layer (SSL) pour créer un canal sécurisé pour les communications réseau entre votre appareil et le cloud.

Vous pouvez créer plusieurs comptes pour un fournisseur de services de cloud donnée. N’oubliez pas, cependant, qu’après la création d’un compte de stockage, vous ne pouvez pas modifier le fournisseur de services cloud.

Tandis que le compte de stockage est enregistré, le service tente de communiquer avec votre fournisseur de services cloud. Les informations d’identification et le matériel d’accès que vous fournissez seront authentifiés pour le moment. Un compte de stockage est créé uniquement si l’authentification réussit. Si l’authentification échoue, un message d’erreur s’affichera.

Gestionnaire de ressources stockage comptes créé dans Azure portal est également pris en charge avec StorSimple. Les comptes de stockage du Gestionnaire de ressources seront afficheront pas dans la liste déroulante de sélection lorsque vous tentez de créer un conteneur de volume, seuls les comptes de stockage créés dans le portail classique Azure s’affiche. Comptes de stockage Gestionnaire de ressources doit être ajouté à l’aide de la procédure pour ajouter un compte de stockage décrit ci-dessous.

> [AZURE.NOTE] La procédure d’ajout d’un compte de stockage varie selon la version du logiciel StorSimple que vous utilisez. Veillez à suivre la procédure appropriée pour votre version StorSimple.


[AZURE.INCLUDE [add-a-storage-account-update1](../../includes/storsimple-configure-new-storage-account-u1.md)]

[AZURE.INCLUDE [add-a-storage-account](../../includes/storsimple-configure-new-storage-account.md)]

## <a name="edit-a-storage-account"></a>Modifier un compte de stockage

Vous pouvez modifier un compte de stockage utilisé par un conteneur de volume. Si vous modifiez un compte de stockage est actuellement en cours d’utilisation, le seul champ disponible pour la modification est la touche d’accès rapide pour le compte de stockage. Vous pouvez fournir la nouvelle touche d’accès de stockage et enregistrer les paramètres de mise à jour.

#### <a name="to-edit-a-storage-account"></a>Pour modifier un compte de stockage

1. Dans la page d’accueil de service, sélectionnez votre service, double-cliquez sur le nom du service, puis cliquez sur **configurer**.

2. Cliquez sur **Ajouter/modifier des comptes de stockage**.

3. Dans la boîte de dialogue **Comptes de stockage Add/Edit** :

  1. Dans la liste déroulante des **Comptes de stockage**, choisissez un compte existant que vous voulez modifier. Cela peut également inclure les comptes de stockage qui ont été générées automatiquement lorsque le service a été créé tout d’abord.
  2. Si nécessaire, vous pouvez modifier la sélection **d’Activer le Mode SSL** .
  3. Vous pouvez choisir de faire pivoter vos touches d’accès rapide stockage compte. Pour plus d’informations sur la façon d’effectuer la rotation des clés, consultez [la rotation des clés de comptes de stockage](#key-rotation-of-storage-accounts) .
  4. Cliquez sur l’icône ![Vérifiez icône](./media/storsimple-manage-storage-accounts/HCS_CheckIcon.png) pour enregistrer les paramètres. Les paramètres seront mise à jour sur la page **configurer** . Cliquez sur **Enregistrer** pour enregistrer les paramètres nouvellement mis à jour.

     ![Modifier un compte de stockage](./media/storsimple-manage-storage-accounts/HCs_AddEditStorageAccount.png)
  
## <a name="delete-a-storage-account"></a>Supprimer un compte de stockage

> [AZURE.IMPORTANT] Vous pouvez supprimer un compte de stockage uniquement si elle n’est pas utilisé par un conteneur de volume. Si un compte de stockage est utilisé par un conteneur de volume, commencez par supprimer le conteneur de volume, puis supprimez le compte de stockage associé.

#### <a name="to-delete-a-storage-account"></a>Pour supprimer un compte de stockage

1. Dans la page de lancement du service Manager StorSimple, sélectionnez votre service, double-cliquez sur le nom du service, puis cliquez sur **configurer**.

2. Dans la liste sous forme de tableau des comptes de stockage, pointez sur le compte que vous souhaitez supprimer.

3. Une icône Supprimer (**x**) s’affichent dans la colonne extrême droite pour ce compte de stockage. Cliquez sur l’icône **x** pour supprimer les informations d’identification.

4. Lorsque vous êtes invité à confirmer, cliquez sur **Oui** pour confirmer la suppression. Dans la liste sous forme de tableau est mise à jour pour refléter les modifications.

## <a name="key-rotation-of-storage-accounts"></a>Rotation des clés de comptes de stockage

Pour des raisons de sécurité, la rotation des clés est souvent une obligation centres de données. 

> [AZURE.NOTE] Les informations de la rotation des clés suivantes et la procédure de rotation s’appliquent aux comptes de stockage de Microsoft Azure uniquement. Si vous utilisez un autre fournisseur de service cloud, vous pouvez gérer les clés de compte de stockage par le biais du tableau de bord de ce fournisseur.
 
Chaque abonnement Microsoft Azure peut avoir un ou plusieurs comptes de stockage associé. L’accès à ces comptes est contrôlée par l’abonnement et clés d’accès pour chaque compte de stockage. 

Lorsque vous créez un compte de stockage, Microsoft Azure génère deux touches d’accès de stockage de 512 bits qui sont utilisés pour l’authentification lorsque le compte de stockage est accessible. Deux touches d’accès de stockage permet de régénérer les touches avec sans interruption à votre service de stockage ou l’accès à ce service. La clé est actuellement en cours d’utilisation est la clé *primaire* et la clé de sauvegarde correspond à la clé *secondaire* . Une de ces deux clés doit être fournie lorsque votre périphérique Microsoft Azure StorSimple accède à votre fournisseur de service de stockage cloud.

## <a name="what-is-key-rotation"></a>Quelle est la rotation des clés ?

En règle générale, applications utilisent uniquement une des clés pour accéder à vos données. Après un certain temps, vous pouvez avoir vos applications basculer vers à l’aide de la deuxième clé. Une fois que vous avez changé vos applications à la clé secondaire, vous pouvez supprimer la première clé et puis générer une nouvelle clé. Les deux clés de cette façon permet votre aux applications un accès aux données sans subir d’interruption de service.

Les clés de compte de stockage sont toujours stockées dans le service dans un formulaire chiffré. Toutefois, il peuvent être réinitialisés via le service Manager StorSimple. Le service peut obtenir la clé primaire et clé secondaire pour tous les comptes de stockage dans le même abonnement, y compris les comptes créés dans le service de stockage, ainsi que le stockage par défaut comptes générées lorsque le service Manager StorSimple a été le premier créés. Le service Manager StorSimple sera toujours afficher ces touches à partir du portail classique Azure et ensuite les stocker de manière chiffrée.

## <a name="rotation-workflow"></a>Flux de travail de rotation

Un administrateur Microsoft Azure peut régénérer ou modifier la clé primaire ou secondaire en accédant directement au compte de stockage (via le service de stockage de Microsoft Azure). Le service Manager StorSimple ne voit pas automatiquement cette modification.

Pour informer le service Manager StorSimple de la modification, vous avez besoin pour accéder au service Manager StorSimple, accéder au compte de stockage et puis synchronisez la clé primaire ou secondaire (selon lequel d'entre eux a été modifiés). Le service puis obtient la dernière clé, chiffre les clés et envoie la clé chiffrée à l’appareil.

#### <a name="to-synchronize-keys-for-storage-accounts-in-the-same-subscription-as-the-service-azure-only"></a>Pour synchroniser les clés pour les comptes de stockage dans le même abonnement en tant que le service (Azure uniquement)

1. Dans la page **Services** , cliquez sur l’onglet **configurer** .

2. Cliquez sur **Ajouter/modifier des comptes de stockage**.

3. Dans la boîte de dialogue, procédez comme suit :

  1. Sélectionnez le compte de stockage avec la clé que vous voulez synchroniser. Les touches de compte de stockage sont chiffrés lorsqu’elles sont affichées.
  2. Dans le service StorSimple Manager, vous devez mettre à jour la clé qui a été modifiée précédemment dans le service de stockage de Microsoft Azure. Si la clé primaire access a été modifiée (régénérée), cliquez sur **synchroniser la clé primaire**. Si la clé secondaire a été modifiée, cliquez sur **synchroniser clé secondaire**.

    ![synchroniser clés](./media/storsimple-manage-storage-accounts/HCS_KeyRotationStorageAccountSameSubscriptionAsService.png)

#### <a name="to-synchronize-keys-for-storage-accounts-outside-of-the-service-subscription"></a>Pour synchroniser les clés pour les comptes de stockage en dehors de l’abonnement au service

1. Dans la page **Services** , cliquez sur l’onglet **configurer** .

2. Cliquez sur **Ajouter/modifier des comptes de stockage**.

3. Dans la boîte de dialogue, procédez comme suit :

  1. Sélectionnez le compte de stockage avec la touche d’accès rapide que vous souhaitez mettre à jour.
  2. Vous devez mettre à jour la touche d’accès de stockage dans le service Manager StorSimple. Dans ce cas, vous pouvez voir la touche d’accès de stockage. Entrez la nouvelle clé dans la zone y **Touche d’accès de compte de stockage**. 
  3. Enregistrez vos modifications. Votre clé d’accès de stockage compte doit maintenant être mis à jour.

## <a name="next-steps"></a>Étapes suivantes

- En savoir plus sur la [sécurité StorSimple](storsimple-security.md).
- En savoir plus sur [l’utilisation du service Manager StorSimple pour administrer votre périphérique StorSimple](storsimple-manager-service-administration.md).

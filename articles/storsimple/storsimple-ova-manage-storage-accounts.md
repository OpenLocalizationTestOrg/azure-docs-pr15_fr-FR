<properties 
   pageTitle="Gérer votre compte de stockage StorSimple | Microsoft Azure"
   description="Explique comment vous pouvez utiliser la page Gestionnaire StorSimple configurer pour ajouter, modifier, supprimer ou faire pivoter les clés de sécurité pour un compte de stockage associée à la matrice virtuel StorSimple."
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
   ms.date="09/29/2016"
   ms.author="alkohli" />

# <a name="use-the-storsimple-manager-service-to-manage-storage-accounts-for-storsimple-virtual-array"></a>Utiliser le service StorSimple Manager pour gérer les comptes de stockage pour tableau virtuel StorSimple

## <a name="overview"></a>Vue d’ensemble

La page **configurer** présente les paramètres du service global pouvant être créées dans le service Manager StorSimple. Ces paramètres peuvent être appliqués à tous les appareils connectés au service et incluent :

- Comptes de stockage 
- Enregistrements de contrôle d’accès 

Ce didacticiel décrit comment vous pouvez utiliser la page **configurer** pour ajouter, modifier ou supprimer des comptes de stockage pour votre tableau virtuel StorSimple. Les informations contenues dans ce didacticiel s’applique uniquement à la matrice virtuel StorSimple mars 2016 disponibilité générale version du logiciel en cours d’exécution.

 ![Configurer la page](./media/storsimple-ova-manage-storage-accounts/configure_service_page.png)  

Comptes de stockage contiennent les informations d’identification que le périphérique utilise pour accéder à votre compte de stockage auprès de votre fournisseur de service cloud. Pour les comptes de stockage de Microsoft Azure, il s’agit des informations d’identification telles que le nom du compte et la clé primaire access. 

Dans la page **configurer** , tous les comptes de stockage qui sont créés pour l’abonnement de facturation sont affichés dans un format tabulaire contenant les informations suivantes :

- **Nom** : le nom unique affecté au compte lors de sa création.
- **Le protocole SSL activé** – si le SSL est activée et communication appareil-nuage est sur le canal sécurisé.

Tâches courantes relatives aux comptes de stockage qui peuvent être effectuées dans la page **configurer** sont :

- Ajouter un compte de stockage 
- Modifier un compte de stockage 
- Supprimer un compte de stockage 


## <a name="types-of-storage-accounts"></a>Types de comptes de stockage

Il existe trois types de comptes de stockage qui peuvent être utilisés avec votre appareil StorSimple.

- **Comptes de stockage généré** – comme le nom l’indique, ce type de compte de stockage est généré automatiquement lorsque vous créez le service. Pour en savoir plus sur la création de ce compte de stockage, voir [créer un nouveau service](storsimple-ova-manage-service.md#create-a-service). 
- **Comptes de stockage dans l’abonnement au service** , voici les comptes de stockage Azure qui sont associés à l’abonnement même que celle du service. Pour en savoir plus sur le stockage de fonctionnement de ces comptes sont créés, voir [à propos des comptes d’espace de stockage Azure](../storage/storage-create-storage-account.md). 
- **Comptes de stockage en dehors de l’abonnement au service** – il s’agit des comptes de stockage Azure qui ne sont pas associés à votre service et probablement existantes avant du service a été créé.

Chaque tableau virtuel StorSimple crée un conteneur (avec un hcs préfixe) dans le compte de stockage associé. Ce conteneur comporte toutes les données de cloud pour votre appareil. Ne supprimez pas ce conteneur en accédant à via le service de stockage Azure que cette action entraîne la perte de données.

## <a name="add-a-storage-account"></a>Ajouter un compte de stockage

Vous pouvez ajouter un compte de stockage pour votre configuration du service Manager StorSimple en fournissant un nom convivial unique et les informations d’identification d’accès qui sont liées à du compte de stockage. Vous avez également la possibilité d’activer le mode de secure sockets layer (SSL) pour créer un canal sécurisé pour les communications réseau entre votre appareil et le cloud.

Vous pouvez créer plusieurs comptes pour un fournisseur de services de cloud donnée. Tandis que le compte de stockage est enregistré, le service tente de communiquer avec votre fournisseur de services cloud. Les informations d’identification et le matériel d’accès que vous fournissez seront authentifiés pour le moment. Un compte de stockage est créé uniquement si l’authentification réussit. Si l’authentification échoue, un message d’erreur s’affichera.

Gestionnaire de ressources stockage comptes créé dans Azure portal est également pris en charge avec StorSimple. Les comptes de stockage du Gestionnaire de ressources ne s’affiche pas dans la liste déroulante de sélection, seul le stockage comptes créés dans le portail classique Azure seront affiche. Comptes de stockage Gestionnaire de ressources doit être ajouté à l’aide de la procédure pour ajouter un compte de stockage, comme décrit ci-dessous.

La procédure d’ajout d’un compte de stockage classique Azure est décrite ci-dessous.

[AZURE.INCLUDE [add-a-storage-account](../../includes/storsimple-ova-configure-new-storage-account.md)]

## <a name="edit-a-storage-account"></a>Modifier un compte de stockage

Vous pouvez modifier un compte de stockage utilisé par votre appareil. Si vous modifiez un compte de stockage est actuellement en cours d’utilisation, les champs disponibles pour la modification sont touche d’accès rapide et le mode SSL pour le compte de stockage. Vous pouvez fournir nouveau stockage touche d’accès rapide ou modifier la sélection du **mode activer SSL** et enregistrer les paramètres de mise à jour.

#### <a name="to-edit-a-storage-account"></a>Pour modifier un compte de stockage

1. Dans la page d’accueil de service, sélectionnez votre service, double-cliquez sur le nom du service, puis cliquez sur **configurer**.

2. Cliquez sur **Ajouter/modifier des comptes de stockage**.

3. Dans la boîte de dialogue **Comptes de stockage Add/Edit** :

  1. Dans la liste déroulante des **Comptes de stockage**, choisissez un compte existant que vous voulez modifier. 
  2. Si nécessaire, vous pouvez modifier la sélection **d’Activer le Mode SSL** .
  3. Vous pouvez choisir de régénérer les touches d’accès votre compte de stockage. Pour plus d’informations, voir [Régénérer les clés de compte de stockage](storage-create-storage-account.md#manage-your-storage-access-keys). Fournir la nouvelle clé de compte de stockage. Pour un compte de stockage Azure, il s’agit de la clé primaire access. 
  4. Cliquez sur l’icône ![Vérifiez icône](./media/storsimple-ova-manage-storage-accounts/checkicon.png) pour enregistrer les paramètres. Les paramètres seront mise à jour sur la page **configurer** . 
  5. Dans la partie inférieure de la page, cliquez sur **Enregistrer** pour enregistrer les paramètres nouvellement mis à jour. 

     ![Modifier un compte de stockage](./media/storsimple-ova-manage-storage-accounts/modifyexistingstorageaccount.png)
  
## <a name="delete-a-storage-account"></a>Supprimer un compte de stockage

> [AZURE.IMPORTANT] Vous pouvez supprimer un compte de stockage uniquement si elle n’est pas en cours d’utilisation. Si un compte de stockage est en cours d’utilisation, vous serez informé.

#### <a name="to-delete-a-storage-account"></a>Pour supprimer un compte de stockage

1. Dans la page de lancement du service Manager StorSimple, sélectionnez votre service, double-cliquez sur le nom du service, puis cliquez sur **configurer**.

2. Dans la liste sous forme de tableau des comptes de stockage, pointez sur le compte que vous souhaitez supprimer.

3. Une icône Supprimer (**x**) s’affichent dans la colonne extrême droite pour ce compte de stockage. Cliquez sur l’icône **x** pour supprimer les informations d’identification.

4. Lorsque vous êtes invité à confirmer, cliquez sur **Oui** pour confirmer la suppression. Dans la liste sous forme de tableau est mise à jour pour refléter les modifications.

5. Dans la partie inférieure de la page, cliquez sur **Enregistrer** pour enregistrer les paramètres nouvellement mis à jour.


## <a name="next-steps"></a>Étapes suivantes

- Découvrez comment [administrer votre tableau virtuel StorSimple](storsimple-ova-web-ui-admin.md).

<properties 
   pageTitle="Déployer le service Manager StorSimple pour tableau virtuel StorSimple | Microsoft Azure"
   description="Explique comment créer et supprimer le service Manager StorSimple dans le portail classique Azure et explique comment gérer la clé d’inscription du service."
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
   ms.date="05/19/2016"
   ms.author="alkohli" />

# <a name="deploy-the-storsimple-manager-service-for-storsimple-virtual-array"></a>Déployer le service Manager StorSimple pour tableau virtuel StorSimple

## <a name="overview"></a>Vue d’ensemble

Le service StorSimple Manager s’exécute dans Microsoft Azure et se connecte à plusieurs appareils StorSimple. Après avoir créé le service, vous pouvez l’utiliser pour gérer les appareils à partir du portail classique Microsoft Azure en cours d’exécution dans un navigateur. Cela vous permet de surveiller tous les appareils connectés au service du Gestionnaire de StorSimple à partir d’un emplacement central unique, ce qui limite les tâches administratives.

La page de lancement StorSimple Gestionnaire répertorie tous les services que vous pouvez utiliser pour gérer vos périphériques de stockage StorSimple StorSimple Manager. Pour chaque service Manager StorSimple, les informations suivantes sont présentées dans la page Gestionnaire StorSimple :

- **Nom** – le nom qui a été affecté à votre service Manager StorSimple lorsqu’il a été créé. Le nom du service ne peuvent pas être modifié une fois que le service est créé.

- **État** – l’état du service, qui peut être **actif**, **Création**ou **en ligne**.

- **Emplacement** – l’emplacement géographique dans lequel l’appareil StorSimple sera déployée.

- **Abonnement** – l’abonnement de facturation associé à votre service.

Les tâches courantes qui peuvent être effectuées via la page Gestionnaire StorSimple sont :

- Création d’un service
- Supprimer un service
- Obtenir la clé d’inscription de service
- Régénérer la clé d’inscription de service

Ce didacticiel décrit comment effectuer chacune de ces tâches. Les informations contenues dans cet article sont applique uniquement aux tableaux virtuels StorSimple. Pour plus d’informations sur la série 8000 StorSimple, accédez à [déployer un service Manager StorSimple](storsimple-manage-service.md).

## <a name="create-a-service"></a>Création d’un service

Utilisez l’option **Création rapide** pour créer un service Manager StorSimple si vous voulez déployer votre périphérique StorSimple. Pour créer un service, vous devez disposer :

- Un abonnement avec un accord d’entreprise
- Un compte de stockage de Microsoft Azure actif
- Les informations de facturation utilisé pour la gestion des accès

Vous pouvez également choisir générer un compte de stockage par défaut lorsque vous créez le service.

Un seul service peut gérer plusieurs appareils. Toutefois, un appareil ne peut pas s’étalent sur plusieurs services. Une grande entreprise peut avoir plusieurs instances de service pour l’utiliser avec différents abonnements, les organisations ou même les emplacements de déploiement.  

> [AZURE.NOTE] Vous avez besoin des instances séparées de service StorSimple Manager pour gérer les périphériques série 8000 StorSimple et les matrices virtuel StorSimple.

Procédez comme suit pour créer un service.

[AZURE.INCLUDE [storsimple-ova-create-new-service](../../includes/storsimple-ova-create-new-service.md)]

## <a name="delete-a-service"></a>Supprimer un service

Avant de supprimer un service, vérifiez qu’aucune appareils connectés ne sont à l’utiliser. Si le service est en cours d’utilisation, désactivez les appareils connectés. L’opération de désactiver la connexion entre l’appareil et le service de serveur, mais conserver les données de l’appareil dans le cloud. 

> [AZURE.IMPORTANT] Après la suppression d’un service, l’opération ne peut pas être annulée. 

Procédez comme suit pour supprimer un service.

### <a name="to-delete-a-service"></a>Pour supprimer un service

1. Dans la page **service Manager StorSimple** , sélectionnez le service que vous souhaitez supprimer.

1. Cliquez sur **Supprimer** dans la partie inférieure de la page.

1. Cliquez sur **Oui** dans le message de confirmation. Il peut prendre quelques minutes pour que le service d’être supprimée.

## <a name="get-the-service-registration-key"></a>Obtenir la clé d’inscription de service

Une fois que vous avez créé un service, vous devrez inscrire votre appareil StorSimple auprès du service. Pour vous inscrire votre appareil StorSimple première, vous devrez la clé d’enregistrement de service. Pour enregistrer des périphériques supplémentaires avec un service StorSimple existant, vous avez besoin de la clé d’inscription et la clé de chiffrement des données de service (qui est générée lors de l’enregistrement sur le premier appareil). Pour plus d’informations sur la clé de chiffrement des données de service, voir [obtenir la clé de chiffrement des données de service à partir de l’interface utilisateur web local](storsimple-ova-web-ui-admin.md#get-the-service-data-encryption-key). 

Effectuez les opérations suivantes pour obtenir la clé d’enregistrement de service.

[AZURE.INCLUDE [storsimple-ova-get-service-registration-key](../../includes/storsimple-ova-get-service-registration-key.md)]

Conserver la clé d’inscription de service dans un emplacement sécurisé. Vous avez besoin de cette clé, ainsi que la clé de chiffrement des données de service, pour enregistrer des périphériques supplémentaires avec ce service. Après avoir recueilli la clé d’inscription du service, vous devrez configurer votre appareil à l’utiliser Windows PowerShell pour l’interface StorSimple.

## <a name="regenerate-the-service-registration-key"></a>Régénérer la clé d’inscription de service

Vous devez recréer une clé d’enregistrement de service si vous êtes amené à effectuer la rotation des clés ou si la liste des administrateurs de service a changé. Lorsque vous régénérez la clé, la nouvelle clé est utilisée uniquement pour l’enregistrement des périphériques suivants. Les appareils qui ont été déjà enregistrés ne sont pas affectés par ce processus.

Procédez comme suit pour régénérer une clé d’enregistrement de service.

### <a name="to-regenerate-the-service-registration-key"></a>Régénérer la clé d’inscription de service

1. Dans la page **service StorSimple Manager** , cliquez sur **Clé d’inscription**.

1. Dans la boîte de dialogue **Clé d’inscription du Service** , cliquez sur **Régénérer**.

1. Un message de confirmation s’affiche. Cliquez sur **OK** pour continuer avec la régénération.

1. Une nouvelle clé d’enregistrement de service s’affichent.

1. Copiez cette touche et l’enregistrer pour enregistrer les nouveaux périphériques grâce à ce service.

1. Cliquez sur l’icône de vérification ![Icône de vérification](./media/storsimple-ova-manage-service/image7.png) pour fermer la boîte de dialogue.


## <a name="next-steps"></a>Étapes suivantes

- Découvrez comment [commencer](storsimple-ova-deploy1-portal-prep.md) avec un tableau virtuel StorSimple.
    
- Découvrez comment faire pour [administrer votre périphérique StorSimple](storsimple-ova-web-ui-admin.md).

 

<properties 
   pageTitle="Tableau de bord de service Manager StorSimple - tableau virtuel | Microsoft Azure"
   description="Décrit le tableau de bord de service Manager StorSimple et explique comment l’utiliser pour surveiller l’intégrité de votre tableau virtuel StorSimple."
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
   ms.date="04/07/2016"
   ms.author="alkohli" />

# <a name="use-the-storsimple-manager-service-dashboard-for-the-storsimple-virtual-array"></a>Utiliser le tableau de bord de service Manager StorSimple pour le tableau virtuel StorSimple

## <a name="overview"></a>Vue d’ensemble

La page de tableau de bord de service Manager StorSimple fournit un résumé des StorSimple virtuel matrices (également appelé StorSimple locaux périphériques virtuels ou périphériques virtuels) qui sont connectés au service du Gestionnaire de StorSimple, mise en surbrillance ceux qui exigent une action d’un administrateur système. Ce didacticiel présente la page tableau de bord, explique le contenu de tableau de bord et la fonction et décrit les tâches que vous pouvez effectuer à partir de cette page.

![Tableau de bord de service](./media/storsimple-ova-service-dashboard/dashboard1.png)

Le tableau de bord de service Manager StorSimple affiche les informations suivantes :

- Dans la zone du **graphique** en haut de la page, vous pouvez voir les mesures pertinentes pour vos périphériques virtuels. Vous pouvez afficher le stockage principal utilisé sur tous les périphériques virtuels, ainsi que le stockage cloud utilisée par les périphériques virtuels sur une période donnée. Utilisez les contrôles dans le coin supérieur droit du graphique pour spécifier l’utilisation de références relative ou absolue et pour afficher une échelle de temps 1 semaine, 1 mois, 3 mois ou 1 an. Utiliser le contrôle de l’actualisation ![contrôle de l’actualisation](./media/storsimple-ova-service-dashboard/refresh-control.png) pour actualiser le graphique.

- **Vue d’ensemble de l’utilisation** affiche le stockage principal qui est mis en service et utilisé par tous les périphériques virtuels par rapport à l’espace de stockage total disponible sur tous les périphériques virtuels. **Provisioned** fait référence à la quantité de stockage qui est préparé allouée à utiliser et **utilisé (s)** fait référence à l’utilisation de partages ou volumes tels qu’ils apparaissent en les initiateurs qui sont connectés aux périphériques virtuels **capacité Max.** affiche la capacité maximale de tous les périphériques virtuels.

- La zone **alertes** fournit un instantané de toutes les alertes actives sur tous les périphériques virtuels, regroupés par gravité d’alerte. En cliquant sur le niveau de gravité s’ouvre la page **alertes** , inclus dans l’étendue pour afficher les alertes. Dans la page **alertes** , vous pouvez cliquer sur une alerte individuelle pour afficher des détails supplémentaires sur cette alerte, y compris les actions recommandées. Vous pouvez également effacer l’alerte si le problème est résolu.

- La zone **tâches** fournit un instantané de tâches récentes sur tous les périphériques virtuels qui sont connectés à votre service. Il existe des liens que vous pouvez utiliser pour examiner les tâches qui sont actuellement en cours et celles réussies ou échouées dans les dernières 24 heures. 

- La zone **coup de œil rapide** située à droite de la page fournit des informations utiles telles qu’état du service, nombre total de virtuels appareils connectés au service, l’emplacement du service et les détails de l’abonnement est associé au service. Vous trouverez également un lien vers le journal des opérations. Cliquez sur le lien pour afficher la liste de toutes les opérations de service Manager StorSimple terminées. 

Vous pouvez utiliser la page de tableau de bord de service Manager StorSimple pour initier les tâches suivantes :

- Obtenir la clé d’enregistrement de service.
- Afficher les journaux d’opération.

## <a name="get-the-service-registration-key"></a>Obtenir la clé d’inscription de service

La clé d’inscription de service est utilisée pour enregistrer un périphérique virtuel StorSimple avec le service Gestionnaire StorSimple, afin que l’appareil apparaît dans le portail classique Azure pour poursuivre les actions de gestion des. La clé est créée sur le premier appareil virtuel et partagée avec les autres périphériques virtuels. 

Cliquez sur **Clé d’inscription** (en bas de la page) pour ouvrir la boîte de dialogue **Clé d’enregistrement de Service** dans lequel vous pouvez copier la clé d’inscription du service en cours dans le Presse-papiers ou régénérer la clé d’enregistrement de service.

![clé d’enregistrement](./media/storsimple-ova-service-dashboard/service-dashboard3.png)

Régénérer la clé n’affecte pas les périphériques virtuels précédemment enregistrés : il affecte uniquement les périphériques virtuels qui sont enregistrés avec le service après la clé est régénérer.

Pour plus d’informations sur l’obtention de la clé d’inscription du service, accédez à [obtenir la clé d’enregistrement de service](storsimple-ova-manage-service.md#get-the-service-registration-key).

## <a name="view-the-operations-logs"></a>Afficher les journaux d’opérations

Vous pouvez afficher les journaux d’opération en cliquant sur le lien de journaux opération disponibles dans le volet **d’aperçu rapide** du tableau de bord. Vous accédez alors à la page de services de gestion, où vous pouvez filtrer et voir les journaux spécifiques à votre service Manager StorSimple.

![Journal des opérations](./media/storsimple-ova-service-dashboard/ops-log.png)

## <a name="next-steps"></a>Étapes suivantes

Découvrez comment [utiliser l’interface utilisateur pour administrer votre tableau virtuel StorSimple web local](storsimple-ova-web-ui-admin.md).
<properties
 pageTitle="Prise en main du planificateur Azure Azure portail | Microsoft Azure"
 description="Prise en main du planificateur Azure Azure portail"
 services="scheduler"
 documentationCenter=".NET"
 authors="derek1ee"
 manager="kevinlam1"
 editor=""/>
<tags
 ms.service="scheduler"
 ms.workload="infrastructure-services"
 ms.tgt_pltfrm="na"
 ms.devlang="dotnet"
 ms.topic="hero-article"
 ms.date="08/10/2016"
 ms.author="deli"/>

# <a name="get-started-with-azure-scheduler-in-azure-portal"></a>Prise en main du planificateur Azure Azure portail

Il est facile de créer des tâches planifiées dans le planificateur Azure. Dans ce didacticiel, vous allez découvrir comment créer une tâche. Vous allez également découvrir les fonctionnalités de gestion et de contrôle du planificateur.

## <a name="create-a-job"></a>Créer une tâche

1.  Se connecter au [portail Azure](https://portal.azure.com/).  

2.  Cliquez sur **+ Nouveau** > tapez _Planificateur_ dans la zone de recherche > activez le **Planificateur** dans les résultats > cliquez sur **créer**.

     ![][marketplace-create]

3.  Nous allons créer une tâche qui accède simplement http://www.microsoft.com/ avec une requête GET. Dans l’écran de la **Tâche du planificateur** , entrez les informations suivantes :

    1.  **Nom :**`getmicrosoft`  

    2.  **Abonnement :** Votre abonnement Azure   

    3.  **Travail Collection :** Sélectionnez une collection de travail existant, ou cliquez sur **Créer un nouveau** > entrez un nom.

4.  Ensuite, dans les **Paramètres des actions**, définissez les valeurs suivantes :

    1.  **Type d’action :**` HTTP`  

    2.  **Méthode :**`GET`  

    3.  **URL :**` http://www.microsoft.com`  

      ![][action-settings]

5.  Pour finir, nous allons définir une planification. La tâche peut être défini comme une tâche unique, mais nous allons sélectionner un calendrier de répétition :

    1. **Périodicité**:`Recurring`

    2. **Démarrer**: date du jour la

    3. **Se répète chaque**:`12 Hours`

    4. **Fin**: deux jours la date du  

      ![][recurrence-schedule]

6.  Cliquez sur **créer**

## <a name="manage-and-monitor-jobs"></a>Gérer et surveiller les tâches

Lorsqu’une tâche est créée, il apparaît dans le tableau de bord Azure principale. Cliquez sur la tâche et une nouvelle fenêtre s’ouvre avec les onglets suivants :

1.  Propriétés  

2.  Paramètres des actions  

3.  Planification  

4.  Historique

5.  Utilisateurs

    ![][job-overview]

### <a name="properties"></a>Propriétés

Ces propriétés en lecture seule décrivent les métadonnées de gestion de la tâche du planificateur.

   ![][job-properties]


### <a name="action-settings"></a>Paramètres des actions

En cliquant sur une tâche dans l’écran **tâches** vous autorise à configurer cette tâche. Cela vous permet de configurer des paramètres avancés, si vous n’avez pas les configurer dans l’Assistant de création rapide.

Pour tous les types d’actions, vous pouvez modifier la stratégie de nouvelles tentatives et l’action d’erreur.

Pour les types de tâche action HTTP et HTTPS, vous pouvez modifier la méthode à n’importe quel verbe HTTP autorisé. Vous pouvez également ajouter, supprimer ou modifier les en-têtes et les informations d’authentification de base.

Stockage file d’attente des types d’actions, vous pouvez modifier le compte de stockage, nom file d’attente, jeton associations de sécurité et corps.

Service bus des types d’actions, vous pouvez modifier l’espace de noms, chemin d’accès de la rubrique/file d’attente, paramètres d’authentification, type de transport, propriétés des messages et le corps du message.

   ![][job-action-settings]

### <a name="schedule"></a>Planification

Cela vous permet de reconfigurer l’Échéancier, si vous voulez modifier le calendrier que vous avez créé dans l’Assistant Création de rapide.

Il s’agit d’une opportunité pour générer des [planifications complexes et périodicité avancée dans votre travail](scheduler-advanced-complexity.md)

Vous pouvez modifier la date de début et heure, fréquence de répétition et la fin date et heure (si la tâche périodique.)

   ![][job-schedule]


### <a name="history"></a>Historique

L’onglet **historique** affiche les mesures sélectionnées pour chaque exécution de la tâche dans le système pour la tâche sélectionnée. Ces métriques fournissent des valeurs en temps réel en ce qui concerne l’état de votre planificateur :

1.  État  

2.  Plus d’informations  

3.  Nouvelles tentatives

4.  Occurrence : 1er, 2e, 3e, etc..

5.  Heure de début de l’exécution  

6.  Heure de fin de l’exécution

   ![][job-history]

Vous pouvez cliquer sur un exécuter pour afficher les **Détails de l’historique**, y compris la réponse complète pour chaque exécution. Cette boîte de dialogue vous permet également de copier la réponse dans le Presse-papiers.

   ![][job-history-details]

### <a name="users"></a>Utilisateurs

Azure contrôle d’accès basé sur un rôle (RBAC) permet de gestion des permissions accès Scheduler Azure. Pour apprendre à utiliser l’onglet utilisateurs, reportez-vous à [Azure_Role-Based le contrôle d’accès](../active-directory/role-based-access-control-configure.md)


## <a name="see-also"></a>Voir aussi

 [Quel est le planificateur ?](scheduler-intro.md)

 [Planificateur concepts, terminologie et hiérarchie d’entités](scheduler-concepts-terms.md)

 [Les offres et facturation dans le planificateur d’Azure](scheduler-plans-billing.md)

 [Découvrez comment créer des planifications complexes et leur périodicité avancée avec le planificateur d’Azure](scheduler-advanced-complexity.md)

 [Référence des API REST de planificateur](https://msdn.microsoft.com/library/mt629143)

 [Référence à des applets de commande PowerShell Scheduler](scheduler-powershell-reference.md)

 [Planificateur disponibilité et fiabilité](scheduler-high-availability-reliability.md)

 [Limites du planificateur, les codes d’erreur et les valeurs par défaut](scheduler-limits-defaults-errors.md)

 [Authentification sortante Scheduler](scheduler-outbound-authentication.md)


[marketplace-create]: ./media/scheduler-get-started-portal/scheduler-v2-portal-marketplace-create.png
[action-settings]: ./media/scheduler-get-started-portal/scheduler-v2-portal-action-settings.png
[recurrence-schedule]: ./media/scheduler-get-started-portal/scheduler-v2-portal-recurrence-schedule.png
[job-properties]: ./media/scheduler-get-started-portal/scheduler-v2-portal-job-properties.png
[job-overview]: ./media/scheduler-get-started-portal/scheduler-v2-portal-job-overview-1.png
[job-action-settings]: ./media/scheduler-get-started-portal/scheduler-v2-portal-job-action-settings.png
[job-schedule]: ./media/scheduler-get-started-portal/scheduler-v2-portal-job-schedule.png
[job-history]: ./media/scheduler-get-started-portal/scheduler-v2-portal-job-history.png
[job-history-details]: ./media/scheduler-get-started-portal/scheduler-v2-portal-job-history-details.png


[1]: ./media/scheduler-get-started-portal/scheduler-get-started-portal001.png
[2]: ./media/scheduler-get-started-portal/scheduler-get-started-portal002.png
[3]: ./media/scheduler-get-started-portal/scheduler-get-started-portal003.png
[4]: ./media/scheduler-get-started-portal/scheduler-get-started-portal004.png
[5]: ./media/scheduler-get-started-portal/scheduler-get-started-portal005.png
[6]: ./media/scheduler-get-started-portal/scheduler-get-started-portal006.png
[7]: ./media/scheduler-get-started-portal/scheduler-get-started-portal007.png
[8]: ./media/scheduler-get-started-portal/scheduler-get-started-portal008.png
[9]: ./media/scheduler-get-started-portal/scheduler-get-started-portal009.png
[10]: ./media/scheduler-get-started-portal/scheduler-get-started-portal010.png
[11]: ./media/scheduler-get-started-portal/scheduler-get-started-portal011.png
[12]: ./media/scheduler-get-started-portal/scheduler-get-started-portal012.png
[13]: ./media/scheduler-get-started-portal/scheduler-get-started-portal013.png
[14]: ./media/scheduler-get-started-portal/scheduler-get-started-portal014.png
[15]: ./media/scheduler-get-started-portal/scheduler-get-started-portal015.png

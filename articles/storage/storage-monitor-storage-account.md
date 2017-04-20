<properties
    pageTitle="Comment faire pour surveiller un compte de stockage | Microsoft Azure"
    description="Apprenez à surveiller un compte de stockage dans Azure à l’aide du portail Azure."
    services="storage"
    documentationCenter=""
    authors="robinsh"
    manager="carmonm"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/03/2016"
    ms.author="robinsh"/>

# <a name="monitor-a-storage-account-in-the-azure-portal"></a>Analyser un compte de stockage dans le portail Azure

## <a name="overview"></a>Vue d’ensemble

Vous pouvez surveiller votre compte de stockage à partir du [Portail Azure](https://portal.azure.com). Lorsque vous configurez votre compte de stockage pour la surveillance via le portail, le stockage Azure utilise [Analytique de stockage](http://msdn.microsoft.com/library/azure/hh343270.aspx) pour suivre les mesures pour votre compte et enregistrer les données de la demande.

> [AZURE.NOTE]Des frais supplémentaires sont associés de l’examen des données d’analyse dans le [Portail Azure](https://portal.azure.com). Pour plus d’informations, voir <a href="http://msdn.microsoft.com/library/azure/hh360997.aspx">Analytique de stockage et de facturation</a>. <br />

> Stockage de fichiers Azure prend métriques de stockage Analytique actuellement, mais ne prend pas en charge la journalisation. Vous pouvez activer métriques de stockage de fichiers Azure via le [Portail Azure](https://portal.azure.com).

> Comptes de stockage à un type de réplication de stockage Zone redondantes (ZRS) n’ont pas la métriques ou la fonction de journalisation pour le moment. 

> Pour un guide détaillé sur l’utilisation de stockage Analytique et autres outils pour identifier, diagnostiquer et résoudre les problèmes liés au stockage Azure, voir [moniteur, diagnostiquer et résoudre les problèmes de stockage de Microsoft Azure](storage-monitoring-diagnosing-troubleshooting.md).


## <a name="how-to-configure-monitoring-for-a-storage-account"></a>Comment : configurer l’analyse d’un compte de stockage

1. Dans le [Portail Azure](https://portal.azure.com), cliquez sur **espace de stockage**, puis cliquez sur le nom de compte de stockage pour ouvrir le tableau de bord.

2. Cliquez sur **configurer**, puis faites défiler jusqu'à les paramètres de **surveillance des mots clés** pour les objets blob, table et services file d’attente.

    ![MonitoringOptions](./media/storage-monitor-storage-account/Storage_MonitoringOptions.png)

3. **Surveillance**, définir le niveau de surveillance et la stratégie de rétention des données pour chaque service :

    -  Pour définir le niveau de surveillance, sélectionnez une des opérations suivantes :

      **Minimal** - métriques de collecte tels que des pourcentages pénétration/sortie, la disponibilité, latence et succès, qui sont regroupées pour les objets blob, table et services file d’attente.

      Outre les mesures minimales, **commentaires** - collecte sur le même jeu d’indicateurs pour chaque opération de stockage de l’API de Service de stockage Azure. Métrique détaillée activer analyse plus approfondie des problèmes qui se produisent pendant le fonctionnement des applications.

      **Désactiver** - désactive surveillance. Suivi des données existantes est conservé jusqu'à la fin de la période de rétention.

- Pour définir la stratégie de rétention des données, de **rétention (en jours)**, tapez le nombre de jours de données à conserver à partir de 1 à 365 jours. Si vous ne souhaitez pas définir une stratégie de rétention, entrez zéro. S’il n’existe aucune stratégie de rétention, c’est à vous permet de supprimer les données d’analyse. Nous vous recommandons de définir une stratégie de rétention basée sur la durée pendant laquelle vous souhaitez conserver les données analytique de stockage pour votre compte afin que les données analytique anciens et inutilisés peuvent être supprimées par système sans frais.

4. Lorsque vous avez terminé la configuration d’analyse, cliquez sur **Enregistrer**.

Vous devez commencer voir analyse de données dans le tableau de bord et la page **Moniteur** après environ une heure.

Jusqu'à ce que vous configurez l’analyse d’un compte de stockage, sans données d’analyse recueillies, ainsi que les graphiques de mesures dans la page de **Moniteur** et tableau de bord sont vides.

Une fois que vous définissez les niveaux de contrôle et les stratégies de rétention, vous pouvez choisir parmi les mesures disponibles pour contrôler le [Portail Azure](https://portal.azure.com)et les mesures à tracer sur graphiques indicateurs. Un ensemble de mesures par défaut s’affiche à chaque niveau de surveillance. Vous pouvez utiliser des **Indicateurs ajouter** pour ajouter ou supprimer des indicateurs dans la liste mesures.

Indicateurs sont stockées dans le compte de stockage de quatre tables nommées $MetricsTransactionsBlob, $MetricsTransactionsTable, $MetricsTransactionsQueue et $MetricsCapacityBlob. Pour plus d’informations, voir [à propos des indicateurs de stockage Analytique](http://msdn.microsoft.com/library/azure/hh343258.aspx).


## <a name="how-to-customize-the-dashboard-for-monitoring"></a>Comment : personnaliser le tableau de bord de surveillance

Dans le tableau de bord, vous pouvez choisir jusqu'à six mesures à tracer sur le graphique métriques de neuf indicateurs disponibles. Pour chaque service (blob, table et file d’attente), les mesures de disponibilité, le pourcentage de réussite et nombre Total de demandes sont disponibles. Les mesures disponibles dans le tableau de bord sont les mêmes pour surveillance minimales ou détaillée.

1. Dans le [Portail Azure](https://portal.azure.com), cliquez sur **espace de stockage**, puis cliquez sur le nom du compte de stockage pour ouvrir le tableau de bord.

2. Pour modifier les mesures qui sont tracées sur le graphique, effectuez l’une des actions suivantes :

    - Pour ajouter une nouvelle mesure au graphique, cliquez sur la case à cocher colorée en regard de l’en-tête métrique dans le tableau ci-dessous le graphique.

    - Pour masquer une métrique qui est tracée sur le graphique, désactivez la case à cocher colorée en regard de l’en-tête métrique.

        ![Monitoring_nmore](./media/storage-monitor-storage-account/storage_Monitoring_nmore.png)

3. Par défaut, le graphique affiche les tendances, afficher uniquement la valeur actuelle de chaque métrique (l’option **Relative** dans la partie supérieure du graphique). Pour afficher un axe Y afin que vous puissiez voir valeurs absolues, sélectionnez **absolue**.

4. Pour modifier la période la métrique graphique s’ouvre, sélectionnez 6 heures, 24 heures ou jours 7 dans la partie supérieure du graphique.


## <a name="how-to-customize-the-monitor-page"></a>Comment : personnaliser la page Moniteur

Dans la page **Moniteur** , vous pouvez afficher l’ensemble des mesures pour votre compte de stockage.

- Si votre compte de stockage a surveillance minimales configuré, indicateurs tels que pénétration/sortie, disponibilité, latence et des pourcentages succès sont regroupées depuis les objets blob, table et services file d’attente.

- Si votre compte de stockage a surveillance détaillée configuré, les mesures sont disponibles avec une résolution plus précie des opérations de stockage individuels outre les agrégats au niveau de service.

Utilisez les procédures suivantes pour choisir les métriques de stockage à afficher dans les graphiques d’indicateurs tableau qui s’affichent dans la page **Moniteur** . Ces paramètres ne concernent pas la collection de sites, le regroupement et le stockage des données dans le compte de stockage d’analyse.

## <a name="how-to-add-metrics-to-the-metrics-table"></a>Comment : ajouter des indicateurs à la table indicateurs


1. Dans le [Portail Azure](https://portal.azure.com), cliquez sur **espace de stockage**, puis cliquez sur le nom du compte de stockage pour ouvrir le tableau de bord.

2. Cliquez sur **Moniteur**.

    La page **Moniteur** s’ouvre. Par défaut, la table Indicateurs affiche un sous-ensemble des mesures qui sont disponibles pour le contrôle. L’illustration montre l’affichage du moniteur par défaut pour un compte de stockage grâce à la surveillance détaillée configuré pour tous les trois services. **Ajouter des indicateurs** permet de sélectionner les mesures que vous souhaitez contrôler dans toutes les mesures disponibles.

    ![Monitoring_VerboseDisplay](./media/storage-monitor-storage-account/Storage_Monitoring_VerboseDisplay.png)

    > [AZURE.NOTE] Prendre en compte les coûts lorsque vous sélectionnez les mesures. Il existe des transactions et des coûts de sortie associés à l’actualisation affiche surveillance. Pour plus d’informations, voir [Analytique de stockage et de facturation](http://msdn.microsoft.com/library/azure/hh360997.aspx).

3. Cliquez sur **Ajouter des indicateurs**.

    Les mesures d’agrégation disponibles dans surveillance minimales sont en haut de la liste. Si la case à cocher est activée, la mesure s’affiche dans la liste d’indicateurs.

    ![AddMetricsInitialDisplay](./media/storage-monitor-storage-account/Storage_AddMetrics_InitialDisplay.png)

4. Pointez sur le côté droit de la boîte de dialogue pour afficher une barre de défilement que vous pouvez faire glisser pour faire défiler les mesures supplémentaires dans l’affichage.

    ![AddMetricsScrollbar](./media/storage-monitor-storage-account/Storage_AddMetrics_Scrollbar.png)


5. Cliquez sur la flèche vers le bas par une métrique pour développer la liste des opérations que la métrique est dans l’étendue pour inclure. Sélectionnez chaque opération que vous souhaitez afficher dans le tableau indicateurs dans le [Portail Azure](https://portal.azure.com).

    Dans l’illustration suivante, le pourcentage d’erreur d’autorisation métrique a été développé.

    ![ExpandCollapse](./media/storage-monitor-storage-account/Storage_AddMetrics_ExpandCollapse.png)


6. Après avoir sélectionné indicateurs pour tous les services, cliquez sur OK (coche) pour mettre à jour de la configuration d’analyse. Les mesures sélectionnées sont ajoutés à la table indicateurs.

7. Pour supprimer une mesure de la table, cliquez sur la métrique pour le sélectionner, puis cliquez sur **Supprimer métrique**.

    ![DeleteMetric](./media/storage-monitor-storage-account/Storage_DeleteMetric.png)

## <a name="how-to-customize-the-metrics-chart-on-the-monitor-page"></a>Comment : personnaliser le graphique de mesures dans la page Moniteur

1. Dans la page **Moniteur** pour le compte de stockage, dans la table indicateurs, sélectionnez métrique jusqu'à 6 à tracer sur le graphique indicateurs. Pour sélectionner une mesure, cliquez sur la case à cocher sur le côté gauche. Pour supprimer une métrique du graphique, désactivez la case à cocher.

2. Pour basculer le graphique entre les valeurs relatives (valeur finale affiche uniquement) et les valeurs absolues (axe des Y affiché), sélectionnez **Relative** ou **absolue** dans la partie supérieure du graphique.

3.  Pour modifier le temps aller métriques graphique affiche, sélectionnez **6 heures**, **24 heures**ou **des 7 derniers jours** dans la partie supérieure du graphique.



## <a name="how-to-configure-logging"></a>Comment : configurer la journalisation

Pour chacun des services de stockage disponibles avec votre compte de stockage (blob, table et file d’attente), vous pouvez enregistrer les journaux de diagnostics pour des requêtes de lecture, les requêtes d’écriture et/ou supprimer les demandes et pouvez définir la stratégie de rétention des données pour chacun de ces services.

1. Dans le [Portail Azure](https://portal.azure.com), cliquez sur **espace de stockage**, puis cliquez sur le nom du compte de stockage pour ouvrir le tableau de bord.

2. Cliquez sur **configurer**et utilisez la flèche vers le bas sur le clavier pour faire défiler vers le bas jusqu'à la **journalisation**.

    ![Storagelogging](./media/storage-monitor-storage-account/Storage_LoggingOptions.png)


3. Pour chaque service (blob, table et file d’attente), configurez les éléments suivants :

    - Les types de demande pour vous connecter : des requêtes de lecture, écriture de requêtes et les demandes de supprimer.

    - Le nombre de jours pour conserver les données enregistrées. Entrer zéro est si vous ne souhaitez pas définir une stratégie de rétention. Si vous ne définissez pas d’une stratégie de rétention, il est jusqu'à vous permet de supprimer les journaux.

4. Cliquez sur **Enregistrer**.

Les journaux de diagnostics sont enregistrées dans un conteneur d’objets blob nommé $logs dans votre compte de stockage. Pour plus d’informations sur l’accès au conteneur $logs, voir [Journalisation Analytique stockage](http://msdn.microsoft.com/library/azure/hh343262.aspx).

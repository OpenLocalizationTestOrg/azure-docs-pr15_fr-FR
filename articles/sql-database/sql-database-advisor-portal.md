<properties 
   pageTitle="Gestionnaire de base de données SQL Azure à l’aide du portail Azure | Microsoft Azure" 
   description="Vous pouvez utiliser le Gestionnaire de base de données SQL Azure dans le portail Azure pour consulter et mettre en œuvre des recommandations pour vos bases de données SQL existant qui permet d’améliorer les performances des requêtes en cours." 
   services="sql-database" 
   documentationCenter="" 
   authors="stevestein" 
   manager="jhubbard" 
   editor="monicar"/>

<tags
   ms.service="sql-database"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="data-management" 
   ms.date="09/30/2016"
   ms.author="sstein"/>

# <a name="sql-database-advisor-using-the-azure-portal"></a>Conseiller de base de données SQL à l’aide du portail Azure

> [AZURE.SELECTOR]
- [Présentation de l’Assistant de base de données SQL](sql-database-advisor.md)
- [Portail](sql-database-advisor-portal.md)

Vous pouvez utiliser le Gestionnaire de base de données SQL Azure dans le portail Azure pour consulter et mettre en œuvre des recommandations pour vos bases de données SQL existant qui permet d’améliorer les performances des requêtes en cours.

## <a name="viewing-recommendations"></a>Affichage des recommandations

La page de recommandations est l’endroit où vous permet d’afficher les recommandations supérieure en fonction de leur impact potentiel pour améliorer les performances. Vous pouvez également afficher l’état des opérations historiques. Sélectionnez une recommandation ou un état pour afficher plus de détails.

Pour afficher et appliquer les recommandations, vous devez disposer des autorisations correctes [contrôle d’accès basé sur un rôle](../active-directory/role-based-access-control-configure.md) dans Azure. **Lecture**, autorisations **Collaborateur de base de données SQL** sont requises pour les recommandations relatives aux vues et le **propriétaire**, autorisations **Collaborateur de base de données SQL** sont requises pour exécuter des actions ; créer ou supprimer des index et annuler la création d’index.

1. Connectez-vous au [portail Azure](https://portal.azure.com/).
2. Cliquez sur **plusieurs services de** > **bases de données SQL**, puis sélectionnez votre base de données.
5. Cliquez sur **recommandation des performances** pour consulter des recommandations disponibles pour la base de données sélectionnée.

> [AZURE.NOTE] Pour obtenir des recommandations une base de données doit comporter sur un jour de l’utilisation et il doit être une activité. Il doit également être une activité cohérente. Le Gestionnaire de base de données SQL pouvez optimiser plus facilement des modèles de requête cohérente que possible pour aléatoires Records taches d’activité. Si les recommandations ne sont pas disponibles, la page **recommandation performances** doit fournir un message expliquant pourquoi.

![Recommandations](./media/sql-database-advisor-portal/recommendations.png)

Voici un exemple de recommandation « Résoudre le problème de schéma » dans le portail Azure.

![Résoudre le problème de schéma](./media/sql-database-advisor-portal/sql-database-advisor-schema-issue.png)

Recommandations sont triées par leur impact sur les performances de quatre catégories suivantes :

| Impact | Description |
| :--- | :--- |
| Élevé | Recommandations d’impact élevé doivent fournir les problèmes de performances plus significatif. |
| Moyenne | Impact moyen recommandations devraient améliorer les performances, mais pas sensiblement. |
| Min | Recommandations faible impact doivent fournir des performances supérieures à celles sans, mais offrent néanmoins quelques améliorations peuvent ne pas être significatives. 


### <a name="removing-recommendations-from-the-list"></a>Suppression de recommandations dans la liste

Si votre liste de recommandations contient des éléments que vous souhaitez supprimer de la liste, vous pouvez ignorer les recommandations suivantes :

1. Sélectionnez une recommandation dans la liste des **recommandations**.
2. Cliquez sur **Annuler** dans la carte de **Détails** .


Si vous le souhaitez, vous pouvez ajouter des éléments supprimés sur la liste des **recommandations** :

1. Dans la carte de **recommandations** cliquez sur **affichage ignorées**.
1. Sélectionner un élément ignoré dans la liste pour afficher les détails.
1. Si vous le souhaitez, cliquez sur **Annuler Annuler** pour ajouter l’index revenir à la liste principale des **recommandations**.



## <a name="applying-recommendations"></a>Application des recommandations

Conseiller de base de données SQL vous donne un contrôle total sur comment recommandations sont activés à l’aide d’une des trois options suivantes : 

- Appliquer les recommandations individuelles une à la fois.
- Activer le conseiller appliquer automatiquement les recommandations (s’applique actuellement à uniquement des recommandations d’index).
- Pour mettre en œuvre une recommandation manuellement, exécuter le script T-SQL recommandé par rapport à votre base de données.

Sélectionnez toute recommandation pour afficher les détails, puis cliquez sur **Afficher le script** pour passer en revue les détails de la création de la recommandation exactes.

La base de données reste en ligne tandis que le conseiller applique la recommandation--à l’aide du Gestionnaire de base de données SQL jamais prend une base de données en mode hors connexion.

### <a name="apply-an-individual-recommendation"></a>Appliquer une recommandation individuelle

Vous pouvez réviser et accepter des recommandations celui à la fois.

1. Dans la carte de **recommandations** , cliquez sur une recommandation.
2. Dans les **Détails de la** carte, cliquez sur **Appliquer**.

    ![Appliquer des recommandations](./media/sql-database-advisor-portal/apply.png)

### <a name="enable-automatic-index-management"></a>Activer la gestion automatique d’index

Vous pouvez définir le Gestionnaire de base de données SQL pour mettre en œuvre automatiquement les recommandations. Dès recommandations sont disponibles qu’ils seront automatiquement appliquées. Comme avec toutes les opérations d’index gérées par le service si l’impact sur les performances est négatif la recommandation est annulée.

1. Dans la carte de **recommandations** , cliquez sur **automatisation**:

    ![Paramètres du Gestionnaire](./media/sql-database-advisor-portal/settings.png)

2. Définir le conseiller pour automatiquement **créer** ou **Supprimer** index :

    ![Index recommandés](./media/sql-database-advisor-portal/automation.png)


### <a name="manually-run-the-recommended-t-sql-script"></a>Exécuter manuellement le script T-SQL recommandé

Sélectionnez toute recommandation, puis sur **Afficher le script**. Exécuter ce script sur votre base de données pour appliquer manuellement la recommandation.

*À partir d’index est exécutées manuellement n’est pas surveiller et validée pour impact sur les performances par le service* afin qu’il est suggérée surveiller ces index après sa création pour vérifier qu’ils fournissent des gains de performances et ajustent ou les supprimer si nécessaire. Pour plus d’informations sur la création d’index, voir [CREATE INDEX (Transact-SQL)](https://msdn.microsoft.com/library/ms188783.aspx).


### <a name="canceling-recommendations"></a>Annulation de recommandations

Recommandations qui se trouvent dans un état **en attente**, **vérification**ou **Success** peuvent être annulées. Recommandations dont l’état **d’exécution** ne peut pas être annulées.

1. Sélectionnez une recommandation dans la zone de **Réglage historique** pour ouvrir la carte de **Détails de recommandations** .
2. Cliquez sur **Annuler** pour annuler le processus de l’application de la recommandation.



## <a name="monitoring-operations"></a>Opérations de contrôle

Application d’une recommandation n’aient pas lieu instantanément. Le portail fournit des détails relatifs à l’état des opérations de recommandations. Un index pouvant être dans les états possibles sont les suivantes :

| État | Description |
| :--- | :--- |
| En attente | Appliquer des recommandations commande a été reçue et est planifiée pour l’exécution. |
| En cours d’exécution | La recommandation est appliquée. |
| Opération réussie | Recommandation a été appliquée. |
| Erreur | Une erreur s’est produite lors du processus de l’application de la recommandation. Cela peut être un problème temporaire, voire un schéma modifier à la table et le script n’est plus valide. |
| Pour revenir | La recommandation a été appliquée, mais a été considéré comme non performantes et est en cours de rétablissement automatiquement. |
| Restauré | La recommandation a été restaurée. |

Cliquez sur une recommandation en cours dans la liste pour afficher plus de détails :

![Index recommandés](./media/sql-database-advisor-portal/operations.png)


### <a name="reverting-a-recommendation"></a>Pour revenir une recommandation

Si vous avez utilisé le Gestionnaire d’appliquer la recommandation (ce qui signifie que vous n’avez pas exécuté manuellement le script T-SQL) il sera automatiquement rétablie il s’il détecte les problèmes de performances est négative. Si pour une raison quelconque, vous voulez simplement rétablir une recommandation procédez comme suit :


1. Sélectionnez une recommandation appliquée avec succès dans la zone de **réglage de l’historique** .
2. Cliquez sur **revenir** dans la carte de **Détails de recommandations** .

![Index recommandés](./media/sql-database-advisor-portal/details.png)


## <a name="monitoring-performance-impact-of-index-recommendations"></a>Impact sur les performances de recommandations d’index de surveillance

Une fois les recommandations correctement implémentées (pour l’instant, les opérations d’index et paramétrer uniquement les recommandations requêtes) vous pouvez cliquer sur **Requête analyse** dans la carte de détails recommandation pour ouvrir [l’Analyse des performances de requête](sql-database-query-performance.md) et afficher l’impact sur les performances de vos requêtes les plus fréquentes.

![Impact sur les performances de moniteur](./media/sql-database-advisor-portal/query-insights.png)



## <a name="summary"></a>Résumé

Conseiller de base de données SQL fournit des recommandations pour améliorer les performances de base de données SQL. En fournissant des scripts T-SQL, ainsi que des utilisateurs et entièrement automatique (actuellement index uniquement), le conseiller fournit une assistance utile dans l’optimisation de votre base de données et finalement améliore les performances de requête.



## <a name="next-steps"></a>Étapes suivantes

Surveillez vos recommandations et continuer à appliquer pour affiner les performances. Charges de travail de base de données sont dynamiques et changent en permanence. Gestionnaire de base de données SQL continuent à surveiller et fournir des recommandations qui peuvent éventuellement améliorer les performances de votre base de données. 

 - Voir [Conseiller de base de données SQL](sql-database-advisor.md) pour une vue d’ensemble du Conseiller de base de données SQL.
 - Voir [Analyse des performances de requête](sql-database-query-performance.md) pour en savoir plus sur l’affichage de l’impact sur les performances de vos requêtes les plus fréquentes.

## <a name="additional-resources"></a>Ressources supplémentaires

- [Magasin de requête](https://msdn.microsoft.com/library/dn817826.aspx)
- [CRÉER UN INDEX](https://msdn.microsoft.com/library/ms188783.aspx)
- [Contrôle d’accès basé sur un rôle](../active-directory/role-based-access-control-configure.md)







<properties 
   pageTitle="Aperçu de performances de requête de base de données SQL Azure" 
   description="Analyse des performances de requête identifie les requêtes d’utilisation du processeur la plupart pour une base de données SQL Azure." 
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
   ms.date="08/09/2016"
   ms.author="sstein"/>

# <a name="azure-sql-database-query-performance-insight"></a>Aperçu de performances de requête de base de données SQL Azure


Gestion et optimiser les performances de bases de données relationnelles sont une tâche complexe nécessitant des compétences importantes et un investissement en temps. Aperçu des performances de requête permet de passer moins de temps résolution des problèmes de performances de base de données en fournissant les éléments suivants :

- Plus claires sur votre utilisation des ressources (DTU) bases de données. 
- Les requêtes les plus fréquentes par nombre/durée/exécution de l’UC, potentiellement pouvant être réglé pour améliorer les performances.
- La fonctionnalité permettant d’Explorer les détails d’une requête, afficher le texte et l’historique de l’utilisation des ressources. 
- Annotations montrant les actions exécutées par le [Gestionnaire de base de données SQL Azure](sql-database-advisor.md) le réglage des performances  



## <a name="prerequisites"></a>Conditions préalables

- Aperçu des performances de requête est disponible uniquement avec V12 de base de données SQL Azure.
- Aperçu des performances de requête nécessite que [Requête Store](https://msdn.microsoft.com/library/dn817826.aspx) est actif sur votre base de données. Si la requête Store ne fonctionne pas, le portail vous invite à activer.

 
## <a name="permissions"></a>Autorisations

Les autorisations de [contrôle d’accès basé sur un rôle](../active-directory/role-based-access-control-configure.md) suivantes sont requises pour utiliser un aperçu de performances de requête : 

- Autorisations de **lecteur**, le **propriétaire**, **Collaboration**, **Collaboration de base de données SQL**ou **SQL Server collaboration** sont nécessaires pour afficher des graphiques et les requêtes beaucoup de ressources supérieure. 
- Autorisations de **propriétaire**, **collaborateur**, **Collaboration de base de données SQL**ou **SQL Server collaboration** sont nécessaires pour afficher le texte de la requête.



## <a name="using-query-performance-insight"></a>À l’aide d’un aperçu de performances de requête

Aperçu des performances de requête est facile à utiliser :

- Ouvrez [le portail Azure](https://portal.azure.com/) et recherche de base de données que vous souhaitez examiner. 
  - À partir du menu de gauche, sous prise en charge et résolution des problèmes, sélectionnez « Requête performances aperçu ».
- Dans le premier onglet, passez en revue la liste des requêtes les plus fréquentes consommateurs de ressources.
- Sélectionnez une requête individuelle pour afficher les détails.
- Ouvrez [Conseiller de base de données SQL Azure](sql-database-advisor.md) et vérifiez si les recommandations sont disponibles.
- Utilisez les curseurs ou effectuer un zoom avant icônes pour modifier l’intervalle observée.

    ![tableau de bord performances](./media/sql-database-query-performance/performance.png)

> [AZURE.NOTE] Quelques heures de données doit être capturé par banque de requête de base de données SQL fournir l’analyse des performances de requête. Si la base de données ne comporte aucune activité ou requête n’a pas été active pendant une période donnée, les graphiques sera vides lors de l’affichage pendant cette période. Vous pouvez activer requête Store à tout moment si elle n’est pas en cours d’exécution.   



## <a name="review-top-cpu-consuming-queries"></a>Passez en revue processeur supérieur par d’autres programmes requêtes

Dans le [portail](http://portal.azure.com) , procédez comme suit :

1. Accédez à une base de données SQL, cliquez sur **tous les paramètres** > **prise en charge + dépannage** > **un aperçu de performances de requête**. 

    ![Aperçu des performances de requête][1]

    Le mode de requêtes les plus fréquentes s’ouvre et les requêtes beaucoup de processeur supérieure sont répertoriées.

1. Cliquez sur autour du graphique pour plus d’informations.<br>La première ligne affiche globale DTU % pour la base de données, tandis que les barres affichent UC % utilisé par les requêtes sélectionnées au cours de l’intervalle sélectionné (par exemple, si la **semaine précédente** est sélectionnée chaque barre représente un jour).

    ![requêtes les plus fréquentes][2]

    La grille inférieure représente les informations agrégées pour les requêtes visibles.

  - ID de requête – identificateur unique de requête à l’intérieur de base de données.
  - Processeur par requête intervalle visible (dépend de la fonction d’agrégation).
  - Durée par requête (dépend de la fonction d’agrégation).
  - Nombre total d’exécutions d’une requête spécifique.

    Activez ou désactivez les requêtes individuelles pour inclure ou exclure les du graphique à l’aide de cases à cocher.

1. Si vos données deviennent obsolètes, cliquez sur le bouton **Actualiser** .
1. Vous pouvez utiliser les curseurs et effectuer un zoom avant de boutons pour modifier l’intervalle d’observation étudier pointes :  ![paramètres](./media/sql-database-query-performance/zoom.png)
1. Si vous le souhaitez, si vous voulez un affichage différent, vous pouvez sélectionner l’onglet **personnalisées** et définir :
  
  - Métrique (processeur, durée, nombre d’exécutions)
  - Intervalle de temps (dernières 24 heures, au-delà de la semaine, mois). 
  - Nombre de requêtes.
  - Fonction d’agrégation.

    ![Paramètres](./media/sql-database-query-performance/custom-tab.png)

## <a name="viewing-individual-query-details"></a>Affichage des détails de la requête individuelle

Pour afficher les détails de la requête :

1. Cliquez sur une requête dans la liste des requêtes les plus fréquentes.

    ![plus d’informations](./media/sql-database-query-performance/details.png)

1. La vue details s’ouvre et le nombre de consommation/durée / l’exécution des requêtes UC se décompose le temps.
1. Cliquez sur autour du graphique pour plus d’informations.
  - Graphique supérieure affiche ligne avec globale % DTU de base de données, et les barres sont % processeur consommé par la requête sélectionnée.
  - Le deuxième graphique affiche la durée totale par la requête sélectionnée.
  - Graphique bas affiche le nombre total d’exécutions par la requête sélectionnée.
    
    ![Détails de la requête][3]

1. Vous pouvez également utiliser les curseurs, boutons de zoom ou cliquez sur **paramètres** pour personnaliser l’affichage des données de requête, ou pour sélectionner une autre période.

## <a name="review-top-queries-per-duration"></a>Requêtes les plus fréquentes révision par durée

Dans la mise à jour récente de requête performances un aperçu, nous avons introduit deux nouvelles mesures qui peuvent vous aider à identifier les éventuels goulots : durée et l’exécution du nombre.<br>

Les requêtes longue ont le plus grand risque de verrouillage des ressources plus, bloquant d’autres utilisateurs et la limitation extensibilité élevées. Ils sont également les mieux adaptées pour l’optimisation.<br>

Pour identifier les requêtes longues :

1. Onglet **personnalisé** ouvrir dans un aperçu de performances de requête de base de données sélectionnée
1. Modifier les mesures à **durée**
1. Sélectionner le nombre de requêtes et intervalle d’observation
1. Sélectionnez la fonction d’agrégation
  - **Somme** additionne tous les temps d’exécution requête intervalle observation entière.
  - **Max** recherche requêtes de temps d’exécution qui a été maximale intervalle observation entière.
  - **Avg** trouve une durée d’exécution moyenne de toutes les exécutions de la requête et vous montrent haut déconnecter ces moyennes. 

    ![durée de requête][4]

## <a name="review-top-queries-per-execution-count"></a>Requêtes les plus fréquentes révision par nombre d’exécutions

Nombre élevé d’exécutions ne peut-être pas affecter de base de données elle-même et l’utilisation des ressources peut être faible, mais globale de l’application rencontrée lente.

Dans certains cas, nombre d’exécutions très haute peut entraîner pour augmenter d’aller-retour réseau. Aller-retour considérablement affecter les performances. Ils sont soumis à latence du réseau et de latence serveur en aval. 

Par exemple, de nombreux sites Web pilotées par les données intensément accéder à la base de données pour chaque demande de l’utilisateur. Tandis que le regroupement permet, le trafic réseau accrue et la charge sur le serveur de base de données de traitement de connexion peut affecter les performances.  Conseils généraux consiste à conserver aller-retour au maximum.

Pour identifier fréquemment exécutée requêtes requêtes (« amenés ») :

1. Onglet **personnalisé** ouvrir dans un aperçu de performances de requête de base de données sélectionnée
1. Modifier les mesures à **compter de l’exécution**
1. Sélectionner le nombre de requêtes et intervalle d’observation

    ![nombre de l’exécution des requêtes][5]

## <a name="understanding-performance-tuning-annotations"></a>Présentation des annotations réglage de performances 

Lors de l’exploration de votre charge de travail dans un aperçu de performances de requête, vous remarquerez peut-être des icônes avec une ligne verticale en haut du graphique.<br>

Ces icônes sont des annotations ; elles représentent les performances qui peuvent affecter les actions exécutées par le [Gestionnaire de base de données SQL Azure](sql-database-advisor.md). Annotation de pointage, vous obtenez des informations de base sur l’action :

![annotation de requête][6]

Si vous voulez en savoir plus ou appliquer recommandation advisor, cliquez sur l’icône. Détails de l’action s’ouvre. S’il s’agit d’une recommandation active, que vous pouvez appliquer directement absent (e) à l’aide de la commande.

![Détails de la requête d’annotation][7]

### <a name="multiple-annotations"></a>Plusieurs annotations. ###

Il est possible qu’en raison de niveau de zoom, annotations proches les uns des autres seront obtenir réduites en une seule. Cela sera représenté par une icône spéciale, cliquer dessus carte nouveau ouvert dans lequel la liste des regroupés annotations s’afficheront.
Corrélation des requêtes et des actions d’optimisation des performances peut vous aider à mieux comprendre votre charge de travail. 


##  <a name="optimizing-the-query-store-configuration-for-query-performance-insight"></a>Optimisation de la configuration de magasin de requête pour un aperçu de performances de requête

Lors de votre utilisation d’un aperçu de performances de requête, vous pouvez rencontrer les requête stocker les messages suivants :

- « Store requête n’est pas correctement configuré sur cette base de données. Cliquez ici pour en savoir plus ».
- « Store requête n’est pas correctement configuré sur cette base de données. Cliquez ici pour modifier les paramètres ». 

Ces messages s’affichent généralement lorsque banque de requête n’est pas en mesure de collecter les nouvelles données. 

Premier cas qui se produit lorsque banque de requête est en lecture seule et paramètres sont définis de façon optimale. Vous pouvez résoudre ce problème en augmentation de la taille du magasin de requête ou en désactivant le magasin de requête.

![bouton QdS][8]

Second cas se passe-t-il lors de la requête Store est désactivée ou paramètres ne sont pas définis de façon optimale. <br>Vous pouvez modifier la stratégie de rétention et la Capture et activer requête Store en exécutant des commandes ci-dessous ou directement à partir de portail :

![bouton QdS][9]

### <a name="recommended-retention-and-capture-policy"></a>Stratégie de rétention et capture recommandée

Il existe deux types de stratégies de rétention :

- Taille en fonction – si défini sur automatique il sera nettoyer données automatiquement quand près taille maximale est atteint.
- Basé sur le temps - par défaut, nous allons définir celle-ci à 30 jours, ce qui signifie que si la requête Store manque d’espace, il entraîne la suppression des informations de requête plues de 30 jours

Capturer stratégie peut être définie sur :

- **Tous les** : capture toutes les requêtes.
- **Automatique** – requêtes rares et les requêtes avec une durée de compilation et l’exécution non significatif sont ignorés. Seuils pour la durée d’exécution Nb, compilation et runtime sont déterminées en interne. Il s’agit de l’option par défaut.
- **Aucun** – requête Store arrête de capturer nouvelles requêtes, mais statistiques pour l’exécution des requêtes déjà capturées sont toujours collectés.
    
Nous vous recommandons de définir toutes les stratégies sur AUTO et propre stratégie à 30 jours :

    ALTER DATABASE [YourDB] 
    SET QUERY_STORE (SIZE_BASED_CLEANUP_MODE = AUTO);
        
    ALTER DATABASE [YourDB] 
    SET QUERY_STORE (CLEANUP_POLICY = (STALE_QUERY_THRESHOLD_DAYS = 30));
    
    ALTER DATABASE [YourDB] 
    SET QUERY_STORE (QUERY_CAPTURE_MODE = AUTO);

Augmenter la taille du magasin de requête. Cela peut être effectuée en vous connectant à une base de données et d’émission de la suite de requête :

    ALTER DATABASE [YourDB]
    SET QUERY_STORE (MAX_STORAGE_SIZE_MB = 1024);

Appliquer ces paramètres fera finalement Store requête collecte des nouvelles requêtes, mais si vous ne voulez pas attendre, vous pouvez effacer banque de requête. 
> [AZURE.NOTE] En cours d’exécution de requête suivante supprime toutes les informations actuelles dans le magasin de requête. 


    ALTER DATABASE [YourDB] SET QUERY_STORE CLEAR;


## <a name="summary"></a>Résumé

Aperçu des performances de requête permet d’identifier l’impact de votre charge de travail de requête et comment elle est liée à la consommation des ressources de base de données. Grâce à cette fonctionnalité, vous en savoir plus sur la partie supérieure par d’autres programmes requêtes et facilement identifier les corriger avant de devenir un problème.




## <a name="next-steps"></a>Étapes suivantes

Pour obtenir des recommandations supplémentaires sur l’amélioration des performances de votre base de données SQL, cliquez sur [recommandations](sql-database-advisor.md) sur la carte **d’Analyse des performances de requête** .

![Conseiller de performances](./media/sql-database-query-performance/ia.png)


<!--Image references-->
[1]: ./media/sql-database-query-performance/tile.png
[2]: ./media/sql-database-query-performance/top-queries.png
[3]: ./media/sql-database-query-performance/query-details.png
[4]: ./media/sql-database-query-performance/top-duration.png
[5]: ./media/sql-database-query-performance/top-execution.png
[6]: ./media/sql-database-query-performance/annotation.png
[7]: ./media/sql-database-query-performance/annotation-details.png
[8]: ./media/sql-database-query-performance/qds-off.png
[9]: ./media/sql-database-query-performance/qds-button.png


<properties 
   pageTitle="Conception de Solutions de Cloud pour sinistre à l’aide de Geo-réplication de base de données SQL | Microsoft Azure"
   description="Découvrez comment concevoir votre solution de cloud pour la reprise en choisissant le modèle de basculement approprié."
   services="sql-database"
   documentationCenter="" 
   authors="anosov1960" 
   manager="jhubbard" 
   editor="monicar"/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA" 
   ms.date="07/16/2016"
   ms.author="sashan"/>

# <a name="disaster-recovery-strategies-for-applications-using-sql-database-elastic-pool"></a>Stratégies de récupération pour les applications à l’aide du Pool élastique de base de données SQL 

Années que nous avons appris que les services en nuage ne sont pas incidents infaillibles et grave peut et se produit. Base de données SQL fournit un certain nombre de fonctionnalités permettent de fournir pour la continuité des activités de votre application lorsque ces incidents se produisent. Bases de données [des pools élastiques](sql-database-elastic-pool.md) et autonome prend en charge le même type de reprise. Cet article décrit plusieurs stratégies DR pour élastique ensembles que tirer parti de ces fonctionnalités continuité de base de données SQL.

Dans le cadre de cet article, nous utiliserons le modèle d’application SaaS fil canonique :

<i>Un nuage moderne basée web application dispositions une base de données SQL pour chaque utilisateur final. L’éditeur de logiciels compte un grand nombre de clients et par conséquent utilise plusieurs bases de données, appelés bases de données client. Étant donné que les bases de données client ont généralement des profils d’activités inattendus, l’éditeur de logiciels utilise un pool élastique pour émettre la base de données coût très prévisibles sur une période prolongée. Le pool élastique simplifie également la gestion des performances lors de l’activité des utilisateurs dépasse. Outre les bases de données client l’application utilise également plusieurs bases de données pour gérer les profils utilisateur, sécurité, collecte etc. des modèles d’utilisation. Disponibilité des clients individuels n’affecte pas la disponibilité de l’application dans son ensemble. Toutefois, la disponibilité et les performances de bases de données gestion est critique pour la fonction l’application et si les bases de données de gestion sont en mode hors connexion l’ensemble de l’application est en mode hors connexion.</i>  

Dans le reste du papier, nous allons étudier les stratégies DR couvrant des scénarios à partir des applications démarrage sensibles coût à celles avec des exigences de disponibilité strictes.  

## <a name="scenario-1-cost-sensitive-startup"></a>Scénario 1. Démarrage sensible des coûts

<i>Je suis une entreprise de démarrage et suis coût extrêmement sensible.  Je veux simplifier le déploiement et la gestion de l’application et je suis prêt à avoir un SLA limité pour les clients individuels. Mais je souhaite garantir l’application comme un entier n’est jamais en mode hors connexion.</i>

Pour satisfaire la condition simplicité, vous devez déployer toutes les bases de données client dans un pool élastique dans la région Azure de votre choix et déployer les bases de gestion des données en tant que bases de données répliquée geo autonome. La récupération d’urgence de clients, utilisez geo-restaurer, qui est fourni sans frais supplémentaires. Pour vous assurer la disponibilité des bases de données gestion, ils doivent être répliquées geo dans une autre région (étape 1). Les coûts liés à la configuration de la récupération d’urgence dans ce scénario est égal au coût total des bases de données secondaire. Cette configuration est illustrée dans l’illustration suivante.

![Figure 1](./media/sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool/diagram-1.png)

En cas de panne dans la région principale, les étapes de récupération pour faire apparaître votre application en ligne sont représentés par le diagramme suivant.

- Immédiatement basculement de la gestion des bases de données (2) à la région DR. 
- Modifier la chaîne de connexion de l’application vers la zone DR. Tous les nouveaux comptes et bases de données client seront créés dans la région DR. Les clients existants verront leurs données temporairement indisponibles.
- Créer le pool élastique avec la même configuration que le pool d’origine (3). 
- Geo-restaurer permet de créer des copies du client bases de données (4). Vous pouvez envisager de déclenchement de la restaure individuels par les connexions de l’utilisateur final ou utiliser certaines autres schéma priorité spécifique de cette application.

À ce stade votre application est en ligne dans la région DR, mais certains clients conséquences retard accéder à leurs données.

![Figure 2](./media/sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool/diagram-2.png)

Si l’interruption a été temporaire, il est possible que la zone primaire sera récupérée par Azure avant que toutes les restaure est terminés dans la région DR. Dans ce cas, vous devez organiser transfert de l’application à la région principale. Le processus prendra les étapes illustrées dans l’illustration suivante.
 
- Annuler toutes les requêtes geo-restaurer en suspens.   
- Basculement gestion des bases de données à la région principale (5). Remarque : Après restauration de la région l’anciens primaires sont devenus automatiquement secondaires. Maintenant ils bascule rôles à nouveau. 
- Modifier la chaîne de connexion de l’application pour qu’il pointe à la région principale. Maintenant tous les nouveaux comptes et bases de données client seront créés dans la région principale. Certains clients existants verront leurs données temporairement indisponibles.   
- Définir toutes les bases de données dans le pool DR en lecture seule pour vous assurer qu’ils ne peut pas être modifiés dans la région DR (6). 
- Pour chaque base de données dans le pool DR qui a changé depuis la restauration, renommer ou supprimer des bases de données correspondantes dans le pool principal (7). 
- Copier les bases de données mises à jour à partir du pool DR dans la liste principale (8). 
- Supprimer le pool DR (9)

À ce stade votre application sera en ligne dans la région principale avec toutes les bases de client disponibles dans la liste principale.

![Figure 3](./media/sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool/diagram-3.png)

La clé de **bénéficier** de cette stratégie est faible coûts liés au niveau redondance des données. Les sauvegardes sont effectuées automatiquement par le service de base de données SQL avec aucune réécriture d’application et sans frais supplémentaires.  Le coût est engagé uniquement lorsque les bases de données élastiques sont restaurées. Le **compromis** est que la récupération complète de toutes les bases de données client prend beaucoup de temps. Il dépend de total nombre de restaure lancera dans la région DR et taille globale des bases de données client. Même si vous hiérarchiser restaure des certains clients sur d’autres personnes, vous sera en concurrence avec tous les autres restaure est créés dans la même région comme le service arbitrage et limiter pour réduire l’impact global sur bases de données les clients existants. En outre, la restauration des bases de données client ne peut pas commencer tant que le pool élastique dans la région DR est créé.

## <a name="scenario-2-mature-application-with-tiered-service"></a>Scénario 2. Application mature avec service hiérarchisé 

<i>Je suis une application SaaS mature avec les offres de service hiérarchisé et SLA différents pour les clients d’évaluation et pour des clients payants. Pour les clients d’évaluation, j’ai réduire le coût autant que possible. Clients d’évaluation peuvent prendre le temps d’arrêt, mais je souhaite réduire sa probabilité. Pour les clients, les temps d’arrêt est un risque de vol. Et je souhaite que pour vous assurer que ce paiement clients peuvent toujours accéder à leurs données.</i> 

Pour prendre en charge ce scénario, vous devez séparer les clients d’évaluation des clients payants en les plaçant dans des pools élastiques distincts. Les clients d’évaluation aurait eDTU inférieur par client et inférieur SLA avec plus de temps récupération. Les clients payant susceptible de figurer dans une liste avec eDTU supérieur par client et un SLA plus élevé. Pour garantir le temps de récupération les plus faibles, bases de données clients payant client doivent être répliquées geo. Cette configuration est illustrée dans l’illustration suivante. 

![Figure 4](./media/sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool/diagram-4.png)

Comme dans le premier scénario, les bases de données de gestion seront tout à fait active afin que vous utilisez une base de données répliquée geo autonome de celui-ci (1). Ainsi, les performances prévisibles pour les nouveaux abonnements client, les mises à jour de profil et les autres opérations de gestion. La zone dans laquelle se trouvent les couleurs primaires des bases de données gestion sera la région principale et la région dans lequel les copies secondaires des bases de données gestion résident sera la région DR.

Bases de données client clients payant aurez bases de données actives dans le pool « payant » mis en service dans la région principale. Mettre en service un pool secondaire portant le même nom dans la région DR. Chaque client peuvent être répliquées geo vers le pool secondaire (2). Ceci permet une récupération rapide de toutes les bases de données client à l’aide de basculement. 

Si une panne se trouve dans la région principale, les étapes de récupération pour faire apparaître votre application en ligne sont illustrées dans l’illustration suivante :

![Figure 5](./media/sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool/diagram-5.png)

- Bascule immédiatement les bases de données de gestion de la région DR (3).
- Modifier la chaîne de connexion de l’application vers la zone DR. Maintenant tous les nouveaux comptes et bases de données client seront créés dans la région DR. Les clients d’évaluation existants verront leurs données temporairement indisponibles.
- Basculement bases de données du client payant au pool dans la région DR immédiatement restaurer leur disponibilité (4). Étant donné que le basculement est une modification au niveau des métadonnées rapide, vous pouvez envisager d’optimisation où le basculement individuels est déclenchés à la demande par les connexions de l’utilisateur final. 
- Si votre taille d’eDTU pool secondaire était inférieure à principal, car les bases de données secondaires requis uniquement la capacité de traiter les journaux modification alors qu’elles étaient secondaires, vous devez immédiatement augmenter la capacité du pool maintenant pour s’adapter à la charge de travail complète de toutes les installations (5). 
- Créer le nouveau pool élastique avec le même nom et la même configuration dans la région DR pour les bases de données du client d’évaluation (6). 
- Une fois le pool de clients d’évaluation est créé, utiliser geo restaurer pour restaurer les bases de données individuels client d’évaluation dans le nouveau pool (7). Vous pouvez envisager de déclenchement de la restaure individuels par les connexions de l’utilisateur final ou utiliser certaines autres schéma priorité spécifique de cette application.

À ce stade votre application est en ligne dans la région DR. Tous les clients ont accès à leurs données tandis que les clients d’évaluation conséquences retard accéder à leurs données.

Lorsque la région principale est restaurée par Azure *une fois que* vous avez restauré l’application dans la région DR que vous pouvez continuer à exécuter l’application dans cette zone ou vous pouvez décider d’être restauré dans la zone principale. Si la région principale est récupéré *avant* le processus de basculement est terminé, vous devez prendre en compte restauration immédiatement. Le retour arrière prendra les mesures illustrés dans l’illustration suivante : 
 
![Figure 6](./media/sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool/diagram-6.png)

- Annuler toutes les requêtes geo-restaurer en suspens.   
- Basculement gestion des bases de données (8). Après la restauration de la région l’ancien serveur principal était devenu automatiquement le moniteur secondaire. Il est maintenant à nouveau le serveur principal.  
- Basculement le client payant bases de données (9). De même, après restauration de la région les couleurs primaires anciens devient automatiquement secondaires. À présent qu’ils deviennent les couleurs primaires à nouveau. 
- Définir les bases de données d’évaluation restaurés qui ont été modifiées dans la région DR en lecture seule (10).
- Pour chaque base de données dans le pool de clients d’évaluation DR changé depuis la restauration, renommer ou supprimer la base de données correspondante dans la liste principale des clients d’évaluation (11). 
- Copier les bases de données mises à jour à partir du pool DR dans la liste principale (12). 
- Supprimer le pool DR (13) 

> [AZURE.NOTE] L’opération de basculement est asynchrone. Pour réduire la durée de récupération, il est important que vous exécutez commande de basculement les bases de données client par lots d’au moins 20 bases de données. 

La clé de **bénéficier** de cette stratégie est qu’elle fournit la plus élevé SLA pour les clients payant. Il garantit également que les nouvelles versions d’essai sont déblocage dès le pool DR d’évaluation est créé. Le **compromis** est que cette configuration augmente le coût total des bases de données client par le coût du pool DR secondaire pour les clients payants. En outre, si le pool secondaire a une taille différente, aux clients payants expérience inférieur performances après une défaillance jusqu'à ce que la mise à niveau du pool dans la région DR est terminée. 

## <a name="scenario-3-geographically-distributed-application-with-tiered-service"></a>Scénario 3. Application géographiquement avec service hiérarchisé

<i>J’ai une application SaaS mature avec les offres de service hiérarchisé. Je veux offrent un SLA très agressif à mes clients payants et réduire les risques en matière de l’impact lorsque défaillances dû au même brève interruption peut entraîner insatisfaction du client. Il est essentiel que les clients payant peuvent toujours accéder à leurs données. Les essais sont gratuites et accord n’est pas disponible pendant la période d’évaluation.</i> 

Pour prendre en charge ce scénario, vous devez disposer des trois pools élastiques distincts. Deux pools taille égale avec haute eDTUs par base de données doivent être mis en service dans deux régions différentes pour contenir les bases de données client clients payant. Le troisième pool contenant les clients d’évaluation aurait un eDTUs inférieur par base de données et être mis en service dans un de la région deux.

Pour garantir le temps de récupération les plus faibles lors d’arrêts client clients payant bases de données doivent être répliquées geo avec 50 % des bases de données principales dans chacune des deux régions. De même, chaque région aurait 50 % des bases de données secondaires. De cette façon si une région est en mode hors connexion, 50 % seulement des bases de données clients payant est affecté et aurait à basculement. Autres bases de données reste intactes. Cette configuration est illustrée dans le diagramme suivant :

![Figure 4](./media/sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool/diagram-7.png)

Comme dans les scénarios précédents, les bases de données de gestion seront tout à fait active afin que vous devez les configurer comme autonome répliquées geo bases de données (1). Ainsi, les performances prévisibles de l’abonnement du client, les mises à jour de profil et les autres opérations de gestion. Région A serait la région principale pour la gestion des bases de données et la zone B sera utilisée pour la récupération des bases de gestion des données.

Bases de données client clients payant seront également répliquées geo, mais principaux et secondaires fractionnement entre région A et B (2). Ainsi que les bases de données principale client affectés par l’interruption peut basculer vers l’autre région et deviennent disponibles. L’autre moitié des bases de données client n’est pas affecté du tout. 

Le diagramme suivant illustre la procédure de récupération à effectuer si une coupure se produit dans la région A.

![Figure 5](./media/sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool/diagram-8.png)

- Bascule immédiatement les gestion des bases de données à la zone B (3).
- Modifier la chaîne de connexion de l’application pour pointer vers les bases de données de gestion de région B. modifier la gestion des bases de données pour vous assurer que les nouveaux comptes et les bases de données client seront créés dans la région B et les bases de données client existant seront il n’y trouve ainsi. Les clients d’évaluation existants verront leurs données temporairement indisponibles.
- Basculement bases de données du client payant au pool de 2 dans la région B pour restaurer immédiatement leur disponibilité (4). Étant donné que le basculement est une modification au niveau des métadonnées rapide, vous pouvez envisager d’optimisation où le basculement individuels est déclenchés à la demande par les connexions de l’utilisateur final. 
- Depuis maintenant pool 2 contient uniquement bases de données primaires que la charge totale du pool augmente afin que vous devez immédiatement augmenter sa taille eDTU (5). 
- Créer le nouveau pool élastique avec le même nom et la même configuration dans la région B pour les bases de données du client d’évaluation (6). 
- Une fois la création du pool utiliser geo restaurer pour restaurer la base de données client d’évaluation individuels dans le pool (7). Vous pouvez envisager de déclenchement de la restaure individuels par les connexions de l’utilisateur final ou utiliser certaines autres schéma priorité spécifique de cette application.


> [AZURE.NOTE] L’opération de basculement est asynchrone. Pour réduire la durée de récupération, il est important que vous exécutez commande de basculement les bases de données client par lots d’au moins 20 bases de données. 

À ce stade votre application est en ligne dans la région B. Tous les clients ont accès à leurs données tandis que les clients d’évaluation conséquences retard accéder à leurs données.

Lors de la région A est restaurée vous devez décider si vous voulez utiliser région B pour les clients d’évaluation ou retour arrière à l’aide de la liste des clients d’évaluation de région A. Un seul critère peut être le % de bases de données client d’évaluation modifiés depuis la récupération. Quelle que soit la décision, vous devrez ré-équilibrer les clients payants entre deux pools. le diagramme suivant illustre le processus quand les bases de données client d’évaluation échouent région A.  
 
![Figure 6](./media/sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool/diagram-9.png)

- Annuler toutes les requêtes en suspens geo-restaurer au pool DR d’évaluation.   
- Basculement de la base de données de gestion (8). Après la restauration de la région, l’ancien serveur principal avait automatiquement est devenu le moniteur secondaire. Il est maintenant à nouveau le serveur principal.  
- Sélectionnez le client payant bases de données seront restauré sur pool 1 et lancer le basculement vers leurs secondaires (9). Après la restauration de la région toutes les bases de données dans le pool 1 est automatiquement devenu secondaires. Maintenant 50 % d'entre elles redevient primaires. 
- Réduire la taille du pool de 2 à l’eDTU d’origine (10).
- Jeu de tous les restauré d’évaluation bases de données dans la zone B en lecture seule (11).
- Pour chaque base de données dans le pool DR d’évaluation qui ont été modifiées depuis la récupération renommer ou supprimer la base de données correspondante dans le pool principal d’évaluation (12). 
- Copier les bases de données mises à jour à partir du pool DR dans la liste principale (13). 
- Supprimer le pool DR (14) 

Les principaux **avantages** de cette stratégie sont les suivantes :

- Il gère le SLA plus agressif aux clients facturés, car elle garantit qu’une panne ne peuvent pas avoir un impact sur plus de 50 % des bases de données client. 
- Il garantit que les nouvelles versions d’essai sont déblocage dès la trace DR pool est créée au cours de la récupération. 
- Il permet d’utiliser plus efficacement la capacité du pool comme 50 % des bases de données secondaires dans pool 1 et 2 de pool sont forcément moins actif puis les bases de données principales.

principal **compromis** sont les suivants :

- Les opérations CRUD sur les bases de données gestion aura une latence inférieure pour les utilisateurs finaux connectés à région A que pour les utilisateurs finaux connectés à région B comme elles seront exécutées par rapport à la partie principale de gestion des bases de données.
- Il demande une conception plus complexe de la base de données de gestion. Par exemple, chaque enregistrement client aurait doit contenir une balise emplacement qui doit être modifiée au cours de basculement et de restauration.  
- Aux clients payants rencontrés moins performant qu’habituellement jusqu'à ce que la mise à niveau du pool de région B est terminée. 

## <a name="summary"></a>Résumé

Cet article se concentre sur les stratégies de récupération pour le niveau de base de données utilisés par une application multi-cliente fil SaaS. La stratégie que vous choisissez doit être en fonction des besoins de l’application, telles que le modèle d’entreprise, le SLA souhaitées pour offrir à vos clients, budget contrainte etc.... Chaque stratégie décrite présente les avantages et les compromis afin que vous pouvez prendre une décision judicieuse. En outre, votre application spécifique inclura probablement autres composants Azure. Il est conseillé suivre leurs conseils continuité business et organiser la restauration de la couche de base de données avec eux. Pour en savoir plus sur la gestion de la récupération des applications de base de données dans Azure, reportez-vous à la [conception de solutions cloud récupération d’urgence](./sql-database-designing-cloud-solutions-for-disaster-recovery.md) .  


## <a name="next-steps"></a>Étapes suivantes

- Pour en savoir plus sur les sauvegardes de base de données SQL Azure automatisé, voir [sauvegardes automatisées de base de données SQL](sql-database-automated-backups.md)
- Pour une vue d’ensemble de la continuité de gestion et scénarios, voir [vue d’ensemble de la continuité de gestion](sql-database-business-continuity.md)
- Pour en savoir plus sur l’utilisation des sauvegardes automatisées pour la récupération, voir [restaurer une base de données à partir des sauvegardes exécutée par le service](sql-database-recovery-using-backups.md)
- Pour en savoir plus sur les options de récupération plus rapides, voir [Actif-Geo-réplication](sql-database-geo-replication-overview.md)  
- Pour en savoir plus sur l’utilisation des sauvegardes automatisées d’archivage, voir [Copier de base de données](sql-database-copy.md)

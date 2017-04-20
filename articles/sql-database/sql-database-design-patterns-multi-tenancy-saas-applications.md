<properties
   pageTitle="Créer des modèles pour les applications SaaS partagées et la base de données SQL Azure | Microsoft Azure"
   description="Cet article décrit la configuration requise et les modèles d’architecture données courantes de base de données partagé applications qui s’exécutent dans un environnement cloud devront prendre en considération et les compromis divers associés à ces modèles. Il également explique comment base de données SQL Azure, ses pools élastiques et outils élastiques, aider à répondre à ces exigences de manière sans compromis."
   keywords=""
   services="sql-database"
   documentationCenter=""
   authors="CarlRabeler"
   manager="jhubbard"
   editor=""/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="sqldb-design"
   ms.date="08/24/2016"
   ms.author="carlrab"/>

# <a name="design-patterns-for-multitenant-saas-applications-and-azure-sql-database"></a>Créer des modèles pour les applications SaaS partagées et la base de données SQL Azure

Dans cet article, vous pouvez en savoir plus sur la configuration requise et des données communes modèles d’architecture du logiciel partagé en tant qu’une applications de base de données de service (SaaS) qui s’exécutent dans un environnement cloud. Il explique également les facteurs que vous devez prendre en considération et les compromis de modèles de conception différents. Pools élastiques et élastiques outils de base de données SQL Azure peuvent vous aider à vos besoins sans compromettre les autres objectifs.

Les développeurs parfois choisir fonctionnant par rapport à leur intérêt à long terme lorsqu’ils conçoivent des modèles de location pour les niveaux de données des applications partagées. Au départ, au moins, un développeur peut noter facilité de développement et réduire les coûts cloud service fournisseur comme plus important que client isolement ou l’extensibilité d’une application. Ce choix peut entraîner les problèmes de satisfaction client et une correction de cours coûteuse ultérieurement.

Une application partagée est une application hébergée dans un environnement cloud et qui propose le même ensemble de services à des centaines ou des milliers de clients qui ne pas partager ou voir d’autres données. Un exemple est une application SaaS qui fournit des services aux clients dans un environnement hébergé sur le nuage.

## <a name="multitenant-applications"></a>Applications partagées

Dans les applications partagées, données et la charge de travail peuvent être facilement partition. Vous pouvez diviser des données et la charge de travail, par exemple, le long des frontières client, étant donné que la plupart des requêtes se produire dans les limites d’un client. Cette propriété est intrinsèque à dans les données et la charge de travail, et il privilégie les modèles d’application mentionnés dans cet article.

Les développeurs utiliser ce type d’application sur tout le spectre des applications basées sur le cloud, notamment :

- Applications de base de données partenaires qui sont en cours de transition dans le cloud en tant qu’applications SaaS
- Applications SaaS constituées pour le cloud notions fondamentales sur vers le haut
- Applications directes et des clients
- Applications d’entreprise not employé

SaaS applications qui ont été conçues pour le nuage et celles avec racines comme partenaire en général des applications de base de données sont partagé. Ces applications SaaS remettre une application de logiciel spécialisé en tant que service à leurs clients. Clients peuvent accéder au service d’application et avoir la propriété complète des données associées stockées dans le cadre de l’application. Mais pour tirer parti des avantages des SaaS, clients doivent renoncer contrôler leurs propres données. Ils approuver le fournisseur de services SaaS pour empêcher que leurs données approuvés et isolées de données des autres clients. Exemples de ce type d’application SaaS partagé sont MYOB, SnelStart et Salesforce.com. Chacune de ces applications peut être partitionnée selon les limites de client et prise en charge l’application de conception des motifs abordent dans cet article.

Les applications qui fournissent un service direct aux clients ou employés au sein d’une organisation (souvent appelés utilisateurs, plutôt que de clients) sont une autre catégorie sur le spectre application partagé. Les clients vous inscrire au service et ne possèdent pas les données que le fournisseur de services collecte et stocke. Fournisseurs de services ont des exigences réduites de conserver les données de leurs clients isolées autre au-delà des réglementations sur la confidentialité imposées par le gouvernement. Exemples de ce type d’application partagé côté client sont des fournisseurs de contenu multimédia comme Netflix, Spotify et Xbox LIVE. Autres exemples d’applications facilement partitionnables sont des clients, échelle d’Internet, applications, ou Internet des objets (IoT) en qui chaque client ou appareil peut servir de partition. Limites de partition peuvent séparer les utilisateurs et les appareils.

Partition applications pas toutes facilement par rapport à une seule propriété par exemple client, client, utilisateur ou appareil. Une ressource d’entreprise complexes planification d’application (planification), par exemple, a produits, des commandes et clients. Il comporte généralement un schéma complexe avec des milliers de tables hautement interconnectés.

Aucune stratégie partition unique peut s’appliquent à toutes les tables et travailler sur la charge de travail d’une application. Cet article se concentre sur partagé applications ayant des charges de travail et données facilement partitionnables.

## <a name="multitenant-application-design-trade-offs"></a>Application partagé création compromis

Le modèle de conception un développeur d’application partagé choisit généralement est basé sur un compte des facteurs suivants :

-   **Isolement client**. Le développeur doit s’assurer ne qu’aucun client indésirables d’accès aux données des autres clients. L’exigence d’isolement étend à d’autres propriétés, tels que protège extérieures bruits, pouvoir restaurer les données d’un client et mettre en œuvre personnalisations spécifiques au client.
-   **Coût de la ressource cloud**. Une application SaaS doit être coût à la concurrence. Un développeur application partagé peut choisir d’optimiser pour réduire le coût dans l’utilisation des ressources de cloud, tels que le stockage et calculer les coûts.
-   **Facilité de DevOps**. Un développeur d’application partagé doit incorporer protection isolement, mettre à jour et surveiller l’intégrité le schéma de base de données et les applications et résoudre les problèmes de client. La complexité de développement d’applications et le fonctionnement se traduit directement par une augmentation des coûts et défis avec satisfaction client.
-   **Extensibilité élevées**. La possibilité d’ajouter plusieurs clients et à la capacité pour les clients qui en ont besoin est impérative d’une opération réussie SaaS.

Chacun de ces facteurs a compromis par rapport à l’autre. Le cloud cher offrant peuvent ne pas offrir l’expérience de développement plus pratique. Il est important pour un développeur effectuer des choix informés sur ces options et leurs compromis lors du processus de conception d’application.

Un modèle de développement courants consiste à compresser plusieurs clients dans une ou plusieurs bases de données. Les avantages de cette approche sont un coût inférieur, car vous payez quelques bases de données et la simplicité relative de l’utilisation d’un nombre limité de bases de données. Mais au fil du temps, un développeur de l’application partagé SaaS pourra profiter que ce choix a inconvénients significative dans isolement client et extensibilité élevées. Si client isolement se transforme en important, des efforts supplémentaires sont nécessaire pour protéger des données client stockage partagé à partir de tout accès non autorisé ou bruits voisin. Cet effort supplémentaire peut-être renforcer de manière significative efforts de développement et des coûts de maintenance isolement. De même, si l’ajout de clients est requise, ce modèle de conception requiert généralement expertise redistribuer des données client sur bases de données à l’échelle correctement la couche de données d’une application.  

Client isolement fréquence est un élément essentiel dans les applications partagés SaaS qui répondent aux entreprises et aux organisations. Un développeur peut être tentée par avantages perçus simplicité et coût par client isolement et extensibilité élevées. Ce compromis peut s’avérer complexe et coûteux que le service se développe et client d’isolement requis sont les plus importantes et gérés au niveau des applications. Toutefois, dans les applications partagées qui fournissent un service direct, pour les consommateurs aux clients, isolement client peut être une priorité plus basse qu’optimisation pour les coûts de ressource cloud.

## <a name="multitenant-data-models"></a>Modèles de données partagé

Pratiques de conception communs de placement des données client suivent trois modèles distincts, illustrés Figure 1.


  ![Modèles de données d’application partagé](./media/sql-database-design-patterns-multi-tenancy-saas-applications/sql-database-multi-tenant-data-models.png)
 Figure 1 : pratiques de conception courants pour les modèles de données partagé

-   **Base de données par client**. Chaque client dispose de sa propre base de données. Toutes les données client spécifiques est limité à la base de données du client et isolé à partir d’autres clients et leurs données.
-   **Sharded de base de données partagée**. Plusieurs clients partagent parmi plusieurs bases de données. Un ensemble de clients distinct est affecté à chaque base de données à l’aide d’une stratégie de partition comme hachage, plage ou liste partition. Cette stratégie de distribution des données souvent est appelée ont.
-   **Base de données partagée - simples**. Une seule base de données parfois volumineux, contient des données pour toutes les installations, qui sont désambiguïsées dans une colonne ID client.

> [AZURE.NOTE] Un développeur d’applications peut-être choisir placer clients différents dans les schémas de base de données différente, puis utiliser le nom de schéma pour distinguer les différents clients. Nous ne recommandons pas cette approche, car elle requiert généralement l’utilisation de SQL dynamique, et il ne peut pas être efficace pour planifier la mise en cache. Dans la suite de cet article, nous concentrer sur l’approche table partagée pour cette catégorie d’application partagée.

## <a name="popular-multitenant-data-models"></a>Modèles de données partagé populaires

Il est important évaluer les différents types de modèles de données partagé en ce qui concerne l’application création compromis que nous avons déjà identifié. Ces facteurs aideront caractérisation les trois modèles de données partagé plus courants décrits précédemment et leur utilisation de base de données comme indiqué dans la Figure 2.

-   **Isolement**. Le niveau d’isolement entre les clients peut être une mesure de la quantité d’isolement client permet d’obtenir un modèle de données.
-   **Coût de la ressource cloud**. La quantité de partage de ressources entre les clients pouvez optimiser le coût d’une ressource cloud. Une ressource peut être définie comme le coût informatiques et de stockage.
-   **Coût DevOps**. La facilité de développement d’applications, le déploiement et la gestion réduit coût opération SaaS.  

Figure 2, l’axe des ordonnées indique le niveau d’isolement client. L’axe X indique le niveau de partage des ressources. Gris, flèche diagonale du milieu indique la direction de coûts DevOps, tendant à augmenter ou diminuer.

![Modèles de conception courants application partagé](./media/sql-database-design-patterns-multi-tenancy-saas-applications/sql-database-popular-application-patterns.png) Figure 2 : modèles de données partagé populaires

Le quart inférieur droit dans la Figure 2 montre un modèle d’application qui utilise un volumineuse, seule base de données partagée et la table partagé (ou schéma distinct) approche. Il est préférable de partage car tous les clients utilisent les mêmes ressources de base de données (processeur, mémoire, entrée/sortie) dans une seule base de données des ressources. Mais isolement client est limitée. Vous devrez peut-être effectuer des étapes supplémentaires pour protéger des clients de l’autre au niveau des applications. Ces étapes supplémentaires peuvent augmenter le coût DevOps de développement et de gestion de l’application. Extensibilité du est limité à l’échelle du matériel qui héberge la base de données.

Le quart inférieur gauche dans la Figure 2 illustre plusieurs clients sharded entre plusieurs bases de données (généralement, matériel différent échelle unités). Chaque base de données héberge un sous-ensemble de clients, qui résout le problème extensibilité élevées d’autres modèles. Si plus de capacité est requise pour plusieurs clients, vous pouvez facilement placer les clients sur les nouvelles bases de données allouées aux nouvelles unités d’échelle de matériel. Toutefois, la quantité de partage des ressources est réduite. Uniquement les clients placé sur la même unité d’échelle partager les ressources. Cette approche offre peu d’amélioration du produit pour isolement du client, car plusieurs clients sont toujours colocalisés sans protégées automatiquement à partir d’actions des autres. La complexité des applications reste élevée.

Le quart supérieur gauche dans la Figure 2 est la troisième approche. Il place les données de chaque client dans sa propre base de données. Cette approche a des propriétés de bonne client-isolement mais les limites de partage des ressources lorsque chaque base de données possède son propre des ressources dédiées. Cette approche est utile si tous les clients ont prévisibles charges de travail. Si les charges de travail client deviennent moins prévisibles, le fournisseur ne peut pas optimiser partage des ressources. Imprévisibilité est courante pour les applications SaaS. Le fournisseur doit être soit excédentaire mise en service pour répondre aux exigences ou ressources inférieurs. L’action entraîne des coûts supérieurs ou inférieure satisfaction client. Un niveau supérieur de la ressource partage entre les clients devient opportun pour que la solution plus économique. Augmenter le nombre de bases de données augmente également DevOps coût pour déployer et gérer l’application. Malgré ces inquiétudes, cette méthode fournit l’isolement meilleur et plus facile pour les clients.

Ces facteurs influencent également le modèle de conception qu'un client choisit :

-   **Propriété des données client**. Une application dans lequel les clients conservent la propriété de leurs propres données privilégie le modèle d’une seule base de données par client.
-   **Échelle**. Une application qui cible des centaines de milliers ou des millions de clients privilégie le partage approches comme ont de base de données. Toujours d’isolement requis peuvent poser des problèmes.
-   **Valeur et le modèle business**. Si une application par-client chiffre d’affaires si petites (moins un dollar), d’isolement requis deviennent moins critiques et une base de données partagée a du sens. Si le chiffre d’affaires par client est égale ou supérieure à quelques dollars, un modèle de base de données par client est plus pratique. Il peut être utile de réduire les coûts de développement.

Étant donné les compromis création illustrés Figure 2, un modèle partagé idéal doit incorporer des propriétés d’isolement bon client avec optimale des ressources partager entre les clients. Ce modèle s’adapte à la catégorie décrite dans le quart supérieur droit de la Figure 2.

## <a name="multitenancy-support-in-azure-sql-database"></a>Prise en charge multitenancy dans la base de données SQL Azure

Base de données SQL Azure prend en charge tous les modèles d’application partagé sous forme de plan dans la Figure 2. Avec des pools élastiques, prend également en charge un modèle d’application qui combine le partage de ressources et les avantages d’isolement de la base de données par client approchent (voir le quart supérieur droit dans la Figure 3). Outils de base de données élastique et des fonctions de base de données SQL aident à réduire les coûts pour développer et utiliser une application qui comporte de nombreuses bases de données (comme illustrés dans la zone ombrée dans la Figure 3). Ces outils peuvent vous aider à créer et gérer des applications qui utilisent les modèles de bases de données multiples.

![Modèles de base de données SQL Azure](./media/sql-database-design-patterns-multi-tenancy-saas-applications/sql-database-patterns-sqldb.png) Figure 3 : modèles d’application partagé dans la base de données SQL Azure

## <a name="database-per-tenant-model-with-elastic-pools-and-tools"></a>Modèle de base de données par client des pools élastiques et outils

Pools élastiques dans la base de données SQL combinent isolement client avec les ressources partage entre les bases de données client pour une meilleure prise en charge de l’approche de base de données par client. Base de données SQL est une solution de niveau de données pour les fournisseurs de SaaS qui créent des applications partagées. La charge de travail de partage de ressources entre les clients entraîne un décalage à partir de la couche d’application vers la couche de service de base de données. La complexité de la gestion et interroger à l’échelle sur bases de données est simplifiée travaux élastique, requête élastique, transactions élastiques et la bibliothèque de client élastique de base de données.

| Besoins de l’application | Fonctionnalités de base de données SQL |
| ------------------------ | ------------------------- |
| Isolement de client et le partage des ressources | [Pools élastiques](sql-database-elastic-pool.md): allouer un pool de ressources de base de données SQL et partager les ressources entre diverses bases de données. En outre, bases de données individuelles peuvent dessiner autant ressources à partir du pool nécessaire pour s’adapter à la capacité à la demande pointes en raison de changements dans les charges de travail client. Le pool élastique lui-même peut être mise à l’échelle vers le haut ou vers le bas selon vos besoins. Pools élastiques offrent également faciliter la facilité de gestion et de surveillance et résolution des problèmes au niveau du pool. |
| Facilité de DevOps entre bases de données | [Pools élastiques](sql-database-elastic-pool.md): comme indiqué précédemment.|
||[Requête élastique](sql-database-elastic-query-horizontal-partitioning.md): requête entre bases de données pour la création de rapports ou analyse croisée-client.|
||[Tâches élastiques](sql-database-elastic-jobs-overview.md): Package et déployer fiable les opérations de maintenance de base de données ou les modifications apportées au schéma de base de données à plusieurs bases de données.|
||[Transactions élastiques](sql-database-elastic-transactions-overview.md): processus passe à plusieurs bases de données de façon atomique et isolée. Transactions élastiques sont nécessaires lorsque des applications doivent garanties « tout ou rien » sur plusieurs opérations de base de données. |
||[Bibliothèque de client de base de données élastique](sql-database-elastic-database-client-library.md): gérer globales pour les données et mapper des clients aux bases de données. |

## <a name="shared-models"></a>Modèles partagés

Comme décrit plus haut, pour la plupart des fournisseurs SaaS, une approche de modèle partagé peut-être poser des problèmes avec les problèmes d’isolement client et complexité développement d’applications et la maintenance. Toutefois, pour les applications partagées qui fournissent un service directement aux consommateurs, client d’isolement requis peuvent ne pas être aussi élevée une priorité comme en réduisant les coûts. Ils peuvent être en mesure de compresser des clients dans une ou plusieurs bases de données à une haute densité pour réduire les coûts. Modèles de base de données partagée à l’aide d’une base de données unique ou plusieurs bases de données sharded peuvent entraîner l’efficacité supplémentaire de coût global et partage d’une ressource. Base de données SQL Azure fournit certaines fonctionnalités permettant aux clients de génération isolement pour améliorer la sécurité et la gestion à l’échelle dans la couche de données.

| Besoins de l’application | Fonctionnalités de base de données SQL |
| ------------------------ | ------------------------- |
| Fonctionnalités de sécurité d’isolement | [Sécurité au niveau de la ligne](https://msdn.microsoft.com/library/dn765131.aspx) |
|| [Schéma de base de données](https://msdn.microsoft.com/library/dd207005.aspx) |
| Facilité de DevOps entre bases de données | [Requête élastique](sql-database-elastic-query-horizontal-partitioning.md) |
|| [Tâches élastiques](sql-database-elastic-jobs-overview.md) |
|| [Transactions élastiques](sql-database-elastic-transactions-overview.md) |
|| [Bibliothèque de client de base de données élastique](sql-database-elastic-database-client-library.md) |
|| [Fractionnement de base de données élastique et fusionner](sql-database-elastic-scale-overview-split-and-merge.md) |

## <a name="summary"></a>Résumé

Client d’isolement requis sont importants pour la plupart des applications partagé SaaS. La meilleure option pour fournir isolement utilise intensément vers l’approche de base de données par client. Les deux autres approches nécessitent investissements dans des couches d’application complexe nécessitant une équipe de développement qualifiés pour fournir isolement, qui augmente considérablement les coûts et les risques. Si d’isolement requis ne sont pas prises en compte au plus tôt dans le développement de service, les rattrapage peut être encore plus élevé dans les modèles de deux premiers. Les principaux inconvénients associés au modèle de base de données par client sont liées aux coûts des ressources cloud accrue en raison de partage réduite et la maintenance et la gestion de bases de données. Les développeurs d’applications SaaS ont souvent des difficultés lorsqu’ils effectuent ces compromis.

Bien que les arbitrages principaux obstacles avec la plupart des fournisseurs de services cloud de base de données, base de données SQL Azure permet d’éliminer les obstacles avec son pool élastique et ses fonctionnalités de base de données élastique. Les développeurs SaaS peuvent combiner les caractéristiques d’isolement d’un modèle de base de données par client et optimiser le partage des ressources et les améliorations de la facilité de gestion de nombreuses bases de données à l’aide des pools élastiques et outils associés.

Fournisseurs d’application partagé qui n’avez pas besoin d’isolement client et qui peut compresser clients dans une base de données à une densité élevée peuvent arriver que données partagées présente le résultat dans l’efficacité supplémentaire de partage des ressources et réduisent le coût total. Outils de base de données élastique base de données SQL Azure, des bibliothèques d’ont et les fonctionnalités de sécurité aident les fournisseurs de SaaS créer et gérer des applications partagées.

## <a name="next-steps"></a>Étapes suivantes

[Prise en main des outils de base de données élastique](sql-database-elastic-scale-get-started.md) avec un exemple d’application qui montre la bibliothèque du client.

Créer un [tableau de bord personnalisé pool élastique pour SaaS](https://github.com/Microsoft/sql-server-samples/tree/master/samples/manage/azure-sql-db-elastic-pools-custom-dashboard) avec un exemple d’application qui utilise des pools élastiques pour une solution rentable, fichiers de base de données.

Utilisez les outils de base de données SQL Azure pour [migrer des bases de données existantes à l’échelle](sql-database-elastic-convert-to-use-elastic-tools.md).

Consulter notre didacticiel sur la [Création d’un pool élastique](sql-database-elastic-pool-create-portal.md).  

Découvrez comment [contrôler et gérer un pool élastique](sql-database-elastic-pool-manage-portal.md).

## <a name="additional-resources"></a>Ressources supplémentaires

- [Qu’est un pool élastique Azure ?](sql-database-elastic-pool.md)
- [Mise à l’échelle avec la base de données SQL Azure](sql-database-elastic-scale-introduction.md)
- [Applications partagées avec des outils de base de données élastique et sécurité au niveau de la ligne](sql-database-elastic-tools-multi-tenant-row-level-security.md)
- [Authentification dans les applications partagées à l’aide de Azure Active Directory et OpenID se connecter](../guidance/guidance-multitenant-identity-authenticate.md)
- [Application d’enquêtes Tailspin](../guidance/guidance-multitenant-identity-tailspin.md)
- [Démarrage rapide de solution](sql-database-solution-quick-starts.md)

## <a name="questions-and-feature-requests"></a>Questions et des demandes de fonctionnalités

Vous avez des questions, nous trouver sur le [forum de la base de données SQL](http://social.msdn.microsoft.com/forums/azure/home?forum=ssdsgetstarted). Ajouter une demande de fonctionnalité dans la [base de données SQL évaluations](https://feedback.azure.com/forums/217321-sql-database/).

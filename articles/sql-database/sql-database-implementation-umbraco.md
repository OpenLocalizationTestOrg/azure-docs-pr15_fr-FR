<properties
   pageTitle="Étude de cas Microsoft Azure - Umbraco de base de données SQL Azure | Microsoft Azure"
   description="Découvrez comment Umbraco utilise la base de données SQL pour rapidement la mise en service et services d’échelle pour des milliers de clients dans le cloud"
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
   ms.workload="NA"
   ms.date="09/22/2016"
   ms.author="carlrab"/>

# <a name="umbraco-uses-azure-sql-database-to-quickly-provision-and-scale-services-for-thousands-of-tenants-in-the-cloud"></a>Umbraco utilise la base de données SQL Azure pour rapidement des services de mise en service et les proportions des milliers de clients dans le cloud

![Umbraco Logo](./media/sql-database-implementation-umbraco/umbracologo.png)

Umbraco est un système de gestion de contenu open source populaires (CMS) qui peut s’exécutent rien à partir des sites de campagne ou une brochure petites complexes des applications pour les sociétés Fortune 500 et sites Web multimédia global. 

> « Nous avons tout à fait une grande Communauté de développeurs qui utilisent le système, avec plus de 100 000 développeurs sur nos forums et plus de 350 000 sites qui sont publiées, en cours d’exécution Umbraco. »

> — Umbraco Morten Christensen, responsable technique,

> [AZURE.VIDEO azure-sql-database-case-study-umbraco]

Pour simplifier les déploiements de client, Umbraco ajouté Umbraco-as-a-Service (Administered Address) : une offre logiciel-as-a-service (SaaS) qui évite d’avoir pour les déploiements en local, fournit la mise à l’échelle intégrés et supprime gestion aérienne en permettant aux développeurs pour vous concentrer sur la gestion de l’innovation plutôt que solution produit. Umbraco est en mesure de proposer tous les avantages en se basant sur le modèle de (PaaS) plateforme en tant que service flexible offert par Microsoft Azure.

Administered Address permet aux clients SaaS utiliser les fonctions Umbraco CMS qui se trouvaient auparavant déconnecter leur portée. Ces clients sont mis en service avec un environnement CMS travail incluant une base de données de production. Clients peuvent ajouter jusqu'à deux bases de données supplémentaires pour les environnements de développement et mise en attente, en fonction de leurs besoins. Lorsqu’un nouvel environnement est demandé, un processus automatisé affecte ce client une base de données automatiquement. La nouvelle base de données est prêt en secondes, étant donné que la base de données a déjà été avant sa mise en service par Umbraco à partir d’un pool élastique Azure des bases de données disponibles (voir Figure 1).


![Cycle de vie mise à disponibilité Umbraco](./media/sql-database-implementation-umbraco/figure1.png)

Figure 1. Mise en service du cycle de vie pour Umbraco en tant que Service (Administered Address)
 
##<a name="azure-elastic-pools-and-automation-simplify-deployments"></a>Azure pools élastiques et automatisation simplifient les déploiements

Avec la base de données SQL Azure et d’autres services Azure, clients Umbraco peuvent configurer eux-mêmes leurs environnements et Umbraco surveiller et gérer facilement les bases de données dans le cadre d’un flux de travail intuitive :

1.  Mise en service

    Umbraco conserve une capacité de 200 bases de données préalablement générés disponibles à partir des pools élastiques. Lorsqu’un nouveau client s’inscrit à Administered Address, Umbraco présente au client avec un nouvel environnement CMS en temps quasi réel en les affectant une base de données à partir du pool de disponibilité.

    Lorsqu’un pool de disponibilité atteint son seuil, un nouveau pool élastique est créé et nouvelles bases de données sont préalablement générés doivent être affectées aux clients selon vos besoins.

    Mise en œuvre est entièrement automatisée à l’aide de la gestion des bibliothèques de c# et files d’attente Bus des services Azure.

2.  Utiliser

    Clients utilisent un à trois environnements (pour production, mise en attente, et/ou développement), chaque avec sa propre base de données. Bases de données client sont des pools élastique de base de données, qui permet de Umbraco fournir qui optimise la mise à l’échelle sans avoir à remplacer mise en service.

    ![Vue d’ensemble du projet Umbraco](./media/sql-database-implementation-umbraco/figure2.png)

    ![Détails du projet Umbraco](./media/sql-database-implementation-umbraco/figure3.png)

    Figure 2. Site Web du client Umbraco-as-a-Service (Administered Address), affichant des informations détaillées et la vue d’ensemble du projet

    Base de données SQL Azure utilise les unités de Transaction de base de données (DTUs) pour représenter la puissance relative requise pour les transactions de base de données réelles. Pour les clients Administered Address, bases de données fonctionnent généralement à environ 10 DTUs, mais chacune a l’élasticité à l’échelle à la demande. Que signifie qu'administered Address permet de garantir que les utilisateurs disposent toujours les ressources nécessaires, même pendant les heures de pointe. Par exemple, pendant un événement sportif dimanche récent, un Administered Address expérimenté base de données client optimale jusqu'à 100 DTUs pendant la durée du jeu. Azure pools élastiques fait que Umbraco prendre en charge ce élevé à la demande sans une baisse des performances.

3.  Moniteur

    Umbraco moniteurs de base de données de l’activité à l’aide de tableaux de bord au sein du portail Azure, ainsi que des alertes par courrier électronique personnalisé.

4.  Récupération d’urgence

    Azure propose deux options de sinistre (DR) : réplication de Geo Active et Geo restaurer. L’option de DR société devez sélectionner dépend de ses [objectifs de continuité](sql-database-business-continuity.md).

    Geo-réplication Active fournit le niveau le plus rapide de réponse en cas d’indisponibilité. À l’aide de la réplication Geo Active, vous pouvez créer jusqu'à quatre secondaires lisibles sur des serveurs dans différentes régions, et vous pouvez lance le basculement à certaines des secondaires en cas de panne.

    Umbraco ne nécessite pas Geo réplication, mais il de tirer parti de restaurer Azure Geo pour garantir le temps d’arrêt minimum en cas de panne. Geo-restaurer s’appuie sur des sauvegardes de base de données dans le stockage Azure geo redondants. Qui permet aux utilisateurs de restaurer à partir d’une copie de sauvegarde lorsqu’il y a une panne dans la région principale.

5.  La mise en service

    Lorsqu’un environnement de projet est supprimé, les bases de données associées (développement, intermédiaire ou live) sont supprimées pendant le nettoyage de file d’attente Bus des services Azure. Ce processus automatisé restaure les bases de données inutilisées au pool d’élastique de disponibilité des bases de données de Umbraco, les rendre disponibles pour la mise en service futures tout en conservant une utilisation maximale.

##<a name="elastic-pools-allow-uaas-to-scale-with-ease"></a>Pools élastiques autoriser Administered Address à l’échelle en toute simplicité

En tirant parti des pools de base de données Azure élastique, Umbraco pouvez optimiser les performances de ses clients sans avoir à sur - ou la mise en service incomplète. Umbraco a actuellement près de 3 000 bases de données entre 19 pools élastique de base de données, la possibilité d’évoluer nécessaire pour prendre en compte les leurs clients 325 000 existants ou les nouveaux clients qui vous êtes prêts à déployer un CMS dans le cloud.

En fait, en fonction de Morten Christensen, responsable technique chez Umbraco, « Administered Address maintenant rencontre croissance d’environ 30 nouveaux clients par jour. Nos clients sont très la ligne d’être en mesure de mise en service de nouveaux projets dans secondes, publier instantanément mises à jour sur leurs sites live à partir d’un environnement de développement à l’aide de « déploiement en un clic » et apporter des modifications plus rapidement si elle trouve des erreurs. »

Si un client ne nécessite pas un environnement deuxième ou troisième plus, il peut simplement supprimer ces environnements. Qui libère des ressources qui peuvent être utilisées pour d’autres clients dans le cadre du pool Umbraco élastique de disponibilité des bases de données.

![Architecture de déploiement Umbraco](./media/sql-database-implementation-umbraco/figure4.png)

Figure 3. Architecture de déploiement Administered Address sur Microsoft Azure

##<a name="the-path-from-datacenter-to-cloud"></a>Le chemin d’accès à partir du centre de données vers le cloud

Lorsque les développeurs Umbraco a initialement décidé de passer à un modèle SaaS, ils auraient qu’ils devront un moyen rentable et format SVG pour générer le service.

> « Pools élastique de base de données sont parfaitement à notre offre SaaS, car nous pouvons composer capacité haut et vers le bas selon vos besoins. Mise en service est simple et rapide avec notre le programme d’installation, nous pouvons conserver l’utilisation au maximum. »

> — Umbraco Morten Christensen, responsable technique,

« Nous voulons notre temps sur la résolution des problèmes de nos clients, ne gestion pas d’infrastructure. Nous souhaitons accroître nos clients de tirer le meilleur parti » indique Niels Hartvig, fondateur de Umbraco. « Nous considérés comme initialement qui héberge les serveurs nous, mais la planification des capacités aurait dû cauchemar. » Par hasard, Umbraco n’utilise pas les administrateurs de base de données, qui met en évidence une proposition de valeur clé pour l’utilisation d’Administered Address.

Un objectif important pour les développeurs Umbraco était de fournir une méthode pour les clients Administered Address mise en service des environnements rapidement et sans limites de capacité. Mais fournissant un service dédié hébergé dans centres de données Umbraco serait disposer d’un nombre important de capacités excédentaires pour gérer la rupture de traitement. Qui serait ont vouliez ajouter infrastructure cluster considérable qui serait ont été régulièrement sous utilisé.

En outre, l’équipe de développement Umbraco voulu une solution lui permettant de les réutiliser autant de leur code existant que possible. En tant que développeur Umbraco Mikkel Madsen indique, « nous avons satisfaits avec les outils de développement Microsoft qui nous ont déjà étés familiers tels que Microsoft SQL Server, base de données SQL Microsoft Azure, ASP.net et Internet Information Services (IIS). Avant d’avoir à investir dans un IaaS ou un PaaS cloud solution, nous souhaitons vous assurer qu’il serait en charge nos outils Microsoft et plateformes, afin que nous n’avez pas à apporter des modifications importantes à notre code base. »

Pour répondre à tous ses critères, Umbraco trouvé un partenaire de nuage avec les qualifications suivantes :

-   Capacité et fiabilité suffisantes
-   Prise en charge des outils de développement Microsoft, afin que les ingénieurs Umbraco ne devra pas obligatoirement réinventer complètement leur environnement de développement
-   Présence dans l’ensemble des marchés géographiques dans lequel Administered Address concurrence (entreprises ont besoin pour vous assurer qu’ils peuvent accéder à leurs données rapidement et que leurs données sont stockées dans un emplacement qui répond à leurs exigences réglementaires régionaux)

##<a name="why-umbraco-chose-azure-for-uaas"></a>Pourquoi Umbraco a choisi Azure pour Administered Address

En fonction des Morten Christensen « après avoir étudié toutes les options de notre, nous avons sélectionné Azure, car il satisfait tous nos critères, à partir de la facilité de gestion et extensibilité élevées à vous familiariser et la rentabilité. Nous configurer les environnements sur machines virtuelles Azure, et chaque environnement dispose de sa propre instance de base de données SQL Azure, avec toutes les instances dans des pools élastique de base de données. En séparant les bases de données entre environnements live, de développement et de mise en attente, nous pouvons offrir à nos clients isolement performances robuste correspond à l’échelle, un gain considérable. »

Morten persiste, « avant, nous avons dû mise en service de serveurs pour les bases de données web manuellement. À présent, nous n’avons pas à y penser. Tout le contenu est automatisé — à partir de la mise en service de nettoyage. »

Morten est également satisfait avec les fonctionnalités de mise à l’échelle offertes par Azure. « Pools élastique de base de données sont parfaitement à notre offre SaaS, car nous pouvons composer capacité haut et vers le bas selon vos besoins. Mise en service est simple et rapide avec notre le programme d’installation, nous pouvons conserver l’utilisation au maximum. » États Morten, « la simplicité des pools élastiques, ainsi que l’assurance de DTUs basée sur le niveau de service, nous donne la possibilité de mise en service de nouvelles listes de ressources partagées à la demande. Récemment, un de nos clients plus grande pointu à 100 DTUs dans son environnement d’exploitation. À l’aide de Azure, notre pools élastiques fourni bases de données du client avec les ressources qui leur nécessaires en temps réel sans avoir à prévoir les exigences DTU. En bref, nos clients obtiennent l’heure autour de désactiver ils s’attendre et nous pouvons répondent à nos contrats de niveau de service de performances. »

Mikkel Madsen l’additionne : « Nous avons adopté l’algorithme Azure puissante qui se connecte un scénario SaaS courant (d’intégration nouveaux clients en temps réel à l’échelle) à notre modèle d’application (avant mise en service des bases de données, les deux développement et live) en haut de la technologie sous-jacente (à l’aide des files d’attente Azure Bus des services conjointement avec la base de données SQL Azure). »

##<a name="with-azure-uaas-is-exceeding-customer-expectations"></a>Avec Azure, Administered Address dépasse aux attentes du client

Depuis le choix d’Azure comme son partenaire cloud, Umbraco a été en mesure d’offrir Administered Address clients optimisation des performances de gestion de contenu, sans l’investissement de ressources informatiques nécessaire à partir d’une solution autonome. Comme Morten indique, « nous appréciez la convivialité de développeur et extensibilité élevées qui permet de créer Azure et nos clients sont sans attendre avec les fonctionnalités et la fiabilité. En général, qu’elle soit un excellent positif pour que nous puissions ! »
 
## <a name="more-information"></a>Plus d’informations

- Pour plus d’informations à propos des pools élastique Azure de la base de données, voir [pools élastique de base de données](sql-database-elastic-pool.md).

- Pour en savoir plus sur Bus des services Azure, voir [Bus des services Azure](https://azure.microsoft.com/services/service-bus/).

- Pour en savoir plus sur les rôles Web et les rôles de travail, voir [rôles de travail](../fundamentals-introduction-to-azure.md#compute). 

- Pour en savoir plus sur un réseau virtuel, voir [mise en réseau virtuel](https://azure.microsoft.com/documentation/services/virtual-network/).    

- Pour en savoir plus sur la sauvegarde et de restauration, voir [continuité des activités](sql-database-business-continuity.md).  

- Pour plus d’informations sur l’analyse des ppols, consultez [analyse des pools](sql-database-elastic-pool-manage-portal.md). 

- Pour en savoir plus sur Umbraco en tant que Service, voir [Umbraco](https://umbraco.com/cloud).


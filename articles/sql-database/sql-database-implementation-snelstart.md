<properties
   pageTitle="Étude de cas Microsoft Azure - Snelstart de base de données SQL Azure | Microsoft Azure"
   description="Découvrez comment SnelStart utilise la base de données SQL pour rapidement développé ses services professionnels à un rythme de 1 000 nouvelles Azure SQL bases de données par mois"
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
   ms.date="09/08/2016"
   ms.author="carlrab"/>

# <a name="with-azure-snelstart-has-rapidly-expanded-its-business-services-at-a-rate-of-1000-new-azure-sql-databases-per-month"></a>Avec Azure, SnelStart a développé rapidement ses services professionnels à un rythme de 1 000 nouvelles Azure SQL bases de données par mois

![SnelStartLogo](./media/sql-database-implementation-snelstart/snelstartlogo.png)

SnelStart rend logiciels populaires financier - et gestion d’entreprise pour les petites et moyennes entreprises (PME) dans les pays-bas. Son 55 000 clients sont pris en charge par une équipe de 110 employés, y compris une votre service informatique de 35. En déplaçant contre les logiciels de bureau à un logiciel-comme-a-service (SaaS) offrant basé sur Azure, SnelStart apportées le meilleur parti de services intégrés, automatisation de la gestion à l’aide d’environnement habituel en c# et optimisation des performances et extensibilité élevées par tous les autres entreprises au-dessus ou sous-mise en service à l’aide des pools élastique de base de données. À l’aide d’Azure fournit SnelStart la fluidité des clients mobiles entre en local et le nuage.

> [AZURE.VIDEO azure-sql-database-case-study-snelstart]

##<a name="why-snelstart-extended-services-from-the-desktop-to-the-cloud"></a>Pourquoi SnelStart services étendus à partir du bureau dans le cloud

> « Travailler avec Azure signifie que nous pouvons fournir des logiciels plus rapide, rapidement réagir aux demandes des clients et à l’échelle solutions lorsque demandes augmentent. »

> — Henry été, architecte logiciel

SnelStart exécuté une activité logicielle réussie ans, à l’aide d’un modèle de développement traditionnel : code du package, livrer et répétez. Dans le temps, le rythme des changements ont connu une hausse plus rapide et plus rapidement. Réglementations changées fréquemment, et à votre disposition pour traiter les documents financiers et de collaborer avec leurs comptables et pour le gouvernement pour répondre à ces modifications aux besoins des clients.

« Logiciel de livraison aux clients a été coûteux et limitation, « selon Henry été, architecte logiciel. « Production, d’emballage et les frais d’expédition limité la fréquence à laquelle nous pourrions version logiciel. Mises à jour de package pour transferts périodiques, nous avons dû, mais il était difficile à répondre aux besoins de nos clients. Nous pouvons jamais la garantie que nos clients mis à niveau vers la dernière version du produit. Par conséquent, nous avons dû prennent en charge plusieurs versions, faciliter les tâches de prise en charge très difficile à bien. »

Été ajoute, « nous voulu permet de mises à jour de programme et release à une accélération rythmer, afin que nous pourrions innovation plus rapidement et créer de nouveaux services pour nos clients. Nous avons également voulu un moyen d’automatiser des processus plus afin de simplifier nos clients business administration. »

Pour SnelStart, la solution était d’étendre ses services en devenir un fournisseur SaaS sur le nuage. La plateforme de base de données SQL Azure permis SnelStart y parvenir sans provoquer la charge informatique principale qui aurait nécessité une solution infrastructure-as-a-service (IaaS).

Le modèle de cloud permet également de SnelStart corriger les bogues et fournir des nouvelles fonctionnalités rapidement, sans avoir besoin de télécharger et mettre à niveau des logiciels des clients. En fonction d’été, « Using services cloud Azure permet rapidement agir sur la configuration requise pour la modification de tiers. Au lieu d’avoir à fournir une nouvelle version à des milliers de clients, nous pouvons adapter les informations envoyées à partir de notre application de bureau et nouveaux formats requis par des tiers. »

##<a name="a-modern-company-with-traditional-roots"></a>Une société moderne avec racines traditionnels

SnelStart d’une entreprise moderne, agile, haute technologie avec racines humble installation 1964, quand les créateurs démarré l’entreprise en tant que fabricant des composants de musical instrument. Cœur de l’entreprise de logiciel SnelStart démarré vraiment TREFILAGE dans les années 1980, pendant la prolifération de l’ordinateur personnel. La société nécessaires une meilleure alternative pour le logiciel de comptabilité disponible dans le temps, afin que l’opération était questions dans sa propre mains. En 1982, la société créé la structure de ce que deviendrait finalement logiciel de comptabilité SnelStart. À partir du début, le logiciel a été prisé pour sa simplicité et la vitesse — tellement afin que la société éventuellement modifié le focus et réinventé dans un éditeur de logiciels.

En 1995, SnelStart publié sa première application comptabilité pour Windows. L’application, basée sur les bases de données Microsoft Visual Basic 1.0 et Microsoft Access, a permis d’atteindre le client base plus de 5 000 utilisateurs.

Aujourd'hui, SnelStart est un fournisseur principal d’une ligne-de-métier et d’application métier administration destinées aux PME néerlandais et entrepreneurs indépendants. Comme Carlo Kuip, architecte informatique, dit, « notre objectif est de proposer 100-pour cent automation services business administration pour nos clients. »

##<a name="optimizing-performance-and-cost-with-elastic-pools"></a>Optimisation des performances et coût avec des pools élastiques

SnelStart a été une grande premiers des pools élastique de base de données. Pools élastiques à la société limiter les coûts et gérer des exigences de performances plus efficacement. En fonction d’été, « en utilisant des pools élastique de base de données, nous pouvons optimiser les performances en fonction des besoins de nos clients, sans remplacer la mise en service. Si nous disposions mise en service en fonction de charge pointe, il est assez coûteuse. En revanche, l’option pour partager les ressources entre plusieurs bases de données de faible utilisation permet de créer une solution qui fonctionne bien et est rentable. »

##<a name="azure-sql-databases-help-containerize-data-for-isolation-and-security"></a>Bases de données SQL Azure aide containerize des données d’isolement et de sécurité 

Base de données SQL Azure Active SnelStart facilement et en toute transparence déplacer clients locaux business administration données Azure. La base de données SQL Azure est un conteneur pratique qui propose isolement, une limite pour l’authentification, l’autorisation et des fonctionnalités de sauvegarde et restauration faciles. Bases de données fournissent un modèle conceptuel parfaitement pour l’administration de l’entreprise. Selon Carlo Kuip, architecte informatique, « éléments au sein de cette limite conteneur contiennent des données sensibles sont cruciales pour l’entreprise et stocker ces éléments dans une base de données isolé conserve les bien protégés. Nous pouvons gérer les autorisations au niveau de la base de données et même automatiser la gestion et distribution horizontale de ces bases de données sans exiger des administrateurs de base de données (bases de données) sur personnel. »

Data Warehouse SQL Azure joue également un rôle dans le scénario de sécurité et la gestion des SnelStart en aidant à l’entreprise de collecter des données de télémétrie, tels que détection et journalisation des activités utilisateur connectivité.

##<a name="azure-removes-overhead-so-that-developers-can-spend-more-time-delivering-value"></a>Azure supprime aérienne afin que les développeurs peuvent consacrer plus de temps valeur ajoutée 

Le modèle de plateforme Azure supprimé charges d’infrastructure et activé SnelStart automatiser les déploiements à l’aide de bibliothèques de gestion c#. Comme Kuip indique, « nous avons agrandir notre opérations en cours avec un petit personnel tout en augmentant les options de récupération après sinistre, vitesse et extensibilité élevées pour nos clients. Le passage au développement de services libéré des ressources pour vous concentrer sur les nouveaux services et fonctionnalités, au lieu de mettre à jour uniquement existant à suivre avec les nouvelles réglementations ou des codes de taxe. » Il ajoute, « par automatisation de la gestion et à l’aide de l’offre, nous ne pouvons fournir plus de valeur pour nos clients sans avoir à investir volumineux dans personnel SaaS ». Par exemple, en utilisant des pools de base de données Azure et souple, SnelStart a été en mesure d’ajouter une variété de nouvelles fonctionnalités, y compris l’intégration plus performantes grâce aux données client banques, facturation nouveaux services, contrôles en arrière-plan pour petites entreprises et services de messagerie.

> « Dans simplement les premiers mois de 2016, nous avons étendu notre déploiements de base de données SQL Azure d’environ 5 500 à plus de 12 000 et nous ajoutons actuellement plus de 1 000 bases de données par mois ».

> — Henry été, architecte logiciel

Gestion de la base de données est plus automatisée en utilisant la fonctionnalité de travaux élastique. Comme Kuip indique, « hautement sont les bienvenus la détection automatique des bases de données sur une instance [server] de base de données SQL. » Cela permet de SnelStart exécuter des opérations de gestion sur leurs clients dynamiquement croissant sans frais supplémentaires.

SnelStart développe également une API qui se comporte comme un intermédiaire entre les données client et les applications intégrées par des partenaires de logiciels tiers. En tant qu’États Kuip, « cette API permettra d’autres fournisseurs ajouter des fonctionnalités à nos logiciels, en supprimant la saisie de données pour les factures et autres documents par exemple. » Clients n’auront plus de taper manuellement des factures dans leur logiciel de comptabilité petites entreprises ; le logiciel SnelStart sera échanger directement les informations nécessaires. Cela permet aux clients à participer à leurs tâches d’administration des affaires avec un réseau d’informations découlant de transformation numérique dans le secteur.  

##<a name="how-azure-services-enable-saas-for-snelstart"></a>Comment activer les services Azure SaaS pour SnelStart

À l’aide de Azure, SnelStart peut servir ses clients et leurs comptables plus en toute transparence dans le cloud. Par exemple, les clients et les comptables peuvent partager des informations en accédant directement à client API du SnelStart, hébergé sur Azure. Kuip indique, « ces services réutilisables sont disponibles à notre destinée aux clients web apps, et ils fournissent des fonctions pour autoriser l’accès à l’administration d’entreprise pour les clients et aux logiciels tiers utilisés par nos clients et infrastructure courantes. Les exemples sont fournissant des fonctions de configuration des produits, la gestion des règles de pare-feu et la gestion des processus longs comme des sauvegardes. »

> Notre objectif est de proposer 100-pour cent automation services business administration pour nos clients. » 

> — Carlo Kuip, architecte informatique

En outre, les services web SnelStart autoriser les clients et comptables facilement accéder aux données dans des pools élastiques de base de données SQL Azure. Ce modèle SaaS, associé à élasticité de base de données et Azure Resource Manager, offre SnelStart fonctionnalités extensibilité du complément de chaque déploiement d’Azure. La mise en œuvre est entièrement automatisée à l’aide de bibliothèques de gestion c#.

![Architecture SnelStart](./media/sql-database-implementation-snelstart/figure1.png)

Figure 1. À partir de juin 2016, SnelStart contient plus de 11 000 bases de données et plus de 50 pools élastique de base de données
 
##<a name="simplicity-from-the-cloud"></a>Simplicité à partir du cloud

Depuis le passage à une solution Azure sur le nuage, SnelStart a été peut prendre en charge la croissance rapide client tout en offrant des services et fonctionnalités innovantes. En fonction d’été, « avec Azure, nous pouvons fournir quasi continue mises à jour pour nos clients, sans développer notre équipe d’opérations. Et nous obtenir toutes les autres Azure fonctionnalités importantes, comme extensibilité élevées et récupération d’urgence — regroupées dans. »

> « Quand nous ont été réellement sur Redmond... J’ai reçu un appel à partir d’un développeur dans pays-bas, diffuser me concernant un problème. Nous avons reçu Microsoft d’effectuer une modification dans leur environnement de production dans les 48 heures pour résoudre notre problème. »

> — Henry été, architecte logiciel

SnelStart apprécie également le partenariat fort qu'ils avez développé avec l’équipe de base de données SQL Microsoft Azure. En tant que Kuip États, « nous avons discussions sur les fonctionnalités et l’utilisation de la technologie, qui est une opération augmentation des deux côtés ».
L’objectif immédiat pour SnelStart consiste à cessent de ses clients satisfaits base. Comme auparavant indique, « Sans les limitations techniques et des ressources que nous avons rencontré comme un éditeur de logiciels, il n’existe aucune limite combien nous pouvons agrandir. »


## <a name="more-information"></a>Plus d’informations

- Pour plus d’informations à propos des pools élastique Azure de la base de données, voir [pools élastique de base de données](sql-database-elastic-pool.md).

- Pour en savoir plus sur les rôles Web et les rôles de travail, voir [rôles de travail](../fundamentals-introduction-to-azure.md#compute). 

- Pour en savoir plus sur le magasin de données SQL Azure, voir [Data Warehouse SQL](https://azure.microsoft.com/documentation/services/sql-data-warehouse/)

- Pour en savoir plus sur SnelStart, voir [SnelStart](http://www.snelstart.nl).



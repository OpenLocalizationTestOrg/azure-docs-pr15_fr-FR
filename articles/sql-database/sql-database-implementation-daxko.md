<properties
   pageTitle="Étude de cas Microsoft Azure - Daxko/CSI de base de données SQL Azure | Microsoft Azure"
   description="Découvrez comment Daxko/CSI utilise la base de données SQL pour accélérer son cycle de développement et améliorer ses services client et les performances"
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
   
# <a name="daxkocsi-used-azure-to-accelerate-its-development-cycle-and-to-enhance-its-customer-services-and-performance"></a>Daxko/CSI utilisées Azure afin d’accélérer son cycle de développement et améliorer ses services client et les performances

![Logo Daxko/CSI](./media/sql-database-implementation-daxko/csidaxkologo25.png)

Logiciel Daxko/CSI auxquels sont confrontées les compliqué : sa clientèle des centres de santé et loisirs a été croissant, grâce à la réussite de sa solution complète de logiciels d’entreprise, mais face aux besoins de l’infrastructure de ce client croissant base a été test de la société votre service informatique. La société était plus restreint augmentation du traitement opérations, en particulier pour la gestion de ses bases de données. Pire, cette surcharge opérations a été coupant en ressources de développement de nouvelles initiatives, telles que les nouvelles fonctionnalités de mobilité pour les logiciels de la société.

En fonction de David Molina, directeur du développement de produit en Daxko/CSI, Azure fourni CSI logiciel avec le modèle plateforme en tant que service (PaaS) qu’il est nécessaire pour simplifier la gestion de la base de données, augmenter extensibilité élevées et libérer des ressources pour vous concentrer sur logiciel au lieu d’opérations. « Base de données SQL azure a été une option intéressante pour nous. N’ayant ne pas à vous soucier de maintenir un serveur SQL Server, un cluster de basculement et toutes l’autres infrastructure doit a été idéale pour nous. »

Depuis une migration vers Azure, CSI logiciel doit personnel d’uniquement deux pour gérer plus de 600 bases de données client. La société utilise des pools élastiques de base de données SQL Azure pour déplacer des bases de données client en fonction de taille et vous avez besoin.

Molina persiste, « nos clients feutre immédiatement la modification. Avant le pools élastiques, ils devaient il peut arriver que des délais d’expiration et d’autres problèmes pendant les périodes de rupture. Avec des pools élastiques Azure, ils peuvent burst selon vos besoins et utiliser le logiciel sans problème. »

Outre l’amélioration des performances pour les clients, élastique Azure de base de données de libérer des ressources de CSI logicielles pour vous concentrer sur le développement de nouveaux services et fonctionnalités, au lieu de traiter les opérations et la gestion des pools. Ces ressources informatiques permis CSI logiciel améliorer ses logiciels d’entreprise offrant, SpectrumNG, pour vous aider à engager membres gym, améliorer l’efficacité du personnel et accorder l’accès mobile personnel et aux membres des tâches interactifs et des notifications en temps réel.

Azure a également permis de logiciel CSI accélérer et améliorer le développement et le cycle de qualité (q & a) en activant les options d’automatisation. Avec mise en œuvre Azure de la société, les gestionnaires de génération peuvent regrouper les composants en cliquant sur un bouton. Comme décrit Molina, « dans le cadre du cycle de publication, q & a peut désormais déployer dans un environnement de test dans Azure qui imite étroitement notre pile de production. Nous pouvons déployer générations immédiatement à notre environnement de développement pour valider les modifications. Qui est un grand positif pour nous, car nous n’avions pas disparités qui existent pour tester avant que. »

## <a name="offloading-to-the-cloud"></a>Décharger dans le cloud

Avant de déplacer dans le cloud, logiciel CSI avait développé avec succès sa propre infrastructure partagé dans un centre de données locale à Houston. La société développé, il auxquels sont confrontées les difficultés de croissance croissantes d’achat, mise en service et la maintenance de la configuration matérielle et logicielle nécessaire pour prendre en charge de ses clients toutes. INFORMATIQUE personnel pour gérer des opérations est devenu un autre critique qui conduit à une baisse des mise en service de nouvelles ressources et déploiement de nouveaux services aux clients.

Logiciel CSI recherché dans les options de cloud pour éliminer les temps système, afin qu’il puisse se concentrer sur son code, au lieu de ses opérations. L’entreprise a découvert que des fournisseurs cloud supérieure proposent uniquement infrastructure en tant que service (IaaS) solutions nécessitent toujours une grande votre service informatique pour gérer la pile IaaS. Mais au final, CSI logiciels a signalé que la solution Azure PaaS était le mieux à ses besoins. Explique Molina, « Azure obtient gêne, le logiciel matériel et du système afin que nous pouvons concentrer sur nos offres logiciel, tout en réduisant notre surcharge informatique. »

## <a name="making-the-transition-to-azure"></a>La transition vers Azure

Après avoir sélectionné Azure pour sa solution PaaS, CSI logiciel a commencé à migrer son infrastructure principale et les bases de données dans le cloud. Avant d’Azure, clients SpectrumNG nécessaire pour installer une application cliente communiqué avec un service Windows Communication Foundation (WCF) sur le serveur principal. En fonction de Molina, « bien que certains clients hébergés tout dans leurs propres centres de données, nous avons créé le produit pour être partagé. Nous tous les éléments dans un centre de données à Houston, hébergé à l’aide de SQL Server comme magasin de données.

« Notre produit offrant également inclus un membre not portail web (écrit en ASP.net), qui a été conçu étiquetées blanc pour correspondre à la présence du client sur le web et une API SOAP pour prendre en charge les pages en ligne et l’intégration de tiers. »

La migration vers le nuage n’a pas eu long pour l’architecture. En fonction de Molina, « La majorité de l’effort traité modifiant la façon que nous lire les informations du fichier de configuration de la, une modification de la chaîne de connexion centralisée et automatisation l’emballage, le chargement et déploiement de notre versions ».

Pour développer l’automatisation génération, CSI ingénieurs utilisé Azure PowerShell et API REST pour créer des packages et les télécharger dans un environnement intermédiaire pour mise à jour chaque nuit.
La transition globale à un déploiement sur le nuage Azure a commencé rapidement et facilement par l’équipe informatique de logiciel CSI. Explique Molina, « dans l’ensemble, nous avons rencontré un environnement de version bêta dans le cloud dans les trois ou quatre semaines prise de sur le projet. Qui a été un win étonnant pour nous. »

Après avoir configuré et l’environnement de test, logiciel CSI ont commencé migration clients. Pour les clients qui utilisent déjà logiciel CSI d’hébergement, la transition a été presque transparente. Pour les clients de migration à partir d’un déploiement local, la migration vers le cloud a eu peu de temps, mais n’a encore principalement difficulté pour les clients et les logiciels CSI.

Pour les de nouveaux clients, CSI logiciel votre service informatique utiliser le processus suivant pour intégrée pour Azure :

1.  Des scripts PowerShell Azure sont utilisées pour tourner une nouvelle base de données pour le client. tous les clients commencent sur un niveau premium pour garantir suffisamment débit initiale pour la transition.
2.  Si possible, CSI logiciel utilise l’Assistant Migration SQL Azure pour déplacer les données existantes sur une instance de base de données SQL Azure.
3.  Enfin, Microsoft SQL Server Integration Services (SSIS) sont utilisées pour rapprocher les différences dans les données ou d’effectuer tout nettoyage de données selon les besoins.

Aujourd'hui, environ 99 % des clients CSI logiciel sont hébergés dans Azure, sur quatre centres de données régionaux (Amérique du Nord centrale, Sud au centre, est et Ouest). En installant des centres de données dans votre région géographique de chaque client, latence est réduite au minimum.

## <a name="azure-elastic-database-pools-free-up-it-resources"></a>Pools de base de données Azure élastique libérer des ressources informatiques

Plusieurs fonctionnalités de Azure ont permis CSI logiciel MAJ ne soient infrastructure et opérations axées à la fonctionnalité et le développement axée. Par exemple le principal avantage a été à partir des pools élastique de base de données.

Logiciel CSI fournit actuellement environ 550 bases de données pour les clients. Avant de pools élastiques, il était difficile à gérer que de nombreuses bases de données dans une structure à couches. Responsables opérations devaient attribuer les niveaux de performances en fonction des besoins rupture de clients, ce qui obligatoire significative ressources informatiques aérienne. Avec des pools élastique de base de données, responsables peuvent affecter les clients un premium ou un pool standard, le cas échéant, puis déplacer des clients en fonction de la taille et besoin. Clients feutre les effets des pools d’une base de données élastique immédiatement ou presque ; avant pools élastiques, clients avaient des délais d’expiration et d’autres problèmes lors de l’utilisation de la rupture des périodes, mais avec des pools élastiques, clients peuvent rencontrer rupture de l’activité selon vos besoins, et ils peuvent continuer à utiliser SpectrumNG sans problème.

## <a name="azure-active-geo-replication-accelerates-reporting"></a>La réplication Geo Azure Active accélère la création de rapports

Plusieurs clients CSI logiciel sont également tirer parti de Azure Geo-réplication Active. Avec la réplication-Active Geo, jusqu'à quatre bases de données secondaires lisibles peut être configuré dans les régions du centre de données identiques ou différents. Logiciel CSI montre comment utiliser des Geo-réplication Active de deux manières : tout d’abord, les bases de données secondaires sont disponibles dans le cas d’une panne du centre de données ou l’incapacité à se connecter à la base de données principale ; et ensuite, les bases de données secondaires sont lisibles et peuvent être utilisés pour décharger les charges de travail en lecture seule tels que travaux de création de rapports. Certains clients CSI logiciel utiliser cet avantage pour accélérer la création de rapports de flux de travail.

## <a name="csi-software-application-logic-and-architecture"></a>Architecture et logique de l’application logiciel CSI

SpectrumNG utilise des rôles web. Étant donné que l’application est cliente multiples, un service WCF est utilisé pour gérer la demande de connexion initiale des clients. Comme Molina indique, « la demande identifie chaque client, ce qui nous permet ensuite de créer une chaîne de connexion à leurs bases de données faire tout ce que nous avons besoin de faire. »

Pour le niveau de son service web CSI logiciel utilise de son redimensionnement automatique Azure, en fonction de date et heure. Ressources disponibles sont automatiquement augmentées pour intégrer d’utilisation plus élevé pendant les heures, selon le fuseau horaire du centre de données régionaux. Ressources sont également définis à l’échelle vers le bas le week-end, lorsque les besoins des clients sont inférieurs.

     
![Architecture Daxko/CSI](./media/sql-database-implementation-daxko/figure1.png)

Figure 1. Un rôle de collaborateur services cloud dessine des données structurées de base de données SQL Azure et données semi-structurées stockage de tables. Les utilisateurs SpectrumNG interagissent avec rôle web de services de données via un nuage.

## <a name="using-web-apps-and-a-web-plan-tier-for-mobile-apps"></a>À l’aide des applications web et un niveau web plan d’applications mobiles

À l’aide de la base de données SQL Azure libérer des ressources pour CSI logiciel pour activer les nouvelles initiatives, y compris une plateforme mobile complète basée sur une API personnalisée hébergée dans les applications web Azure. La plateforme permet aux membres gym et le personnel à utiliser les appareils mobiles pour vérifier les plannings, classes du carnet d’et recevoir des messages.

La plateforme utilise architecture orientée services (SOA) pour tirer un seul composant, comme un système de point de vente (PDV) ou un système de ventes, déplacez-le vers un autre plan web à la volée et faire pivoter un service pour prendre en charge ce composant, tout en laissant tout le reste sur le plan web d’origine. Cette fonctionnalité offre souplesse CSI logiciel, et il permet de réduire les coûts.

## <a name="azure-lets-csi-software-developers-focus-on-apps-and-services"></a>Azure permet aux développeurs de logiciel CSI se concentrer sur les applications et services

Base de données SQL Azure n’est pas simplement une aubaine pour les clients SpectrumNG, partageant le service rapide et fiable, il est également un avantage principal de du logiciel CSI votre service informatique et les développeurs. Par décharger opérations à Azure dans le cloud, logiciel CSI ses frais pour les ressources et infrastructure réduits, considérablement accélérée et consulter ses cycles de développement et n’est plus besoins aux bases de données micromanage pour optimiser les performances de ses clients.

## <a name="more-information"></a>Plus d’informations

- Pour plus d’informations à propos des pools élastique Azure de la base de données, voir [pools élastique de base de données](sql-database-elastic-pool.md).

- Pour en savoir plus sur les outils de base de données et la même échelle élastique, voir [les outils de base de données élastique et la même échelle élastique](sql-database-elastic-scale-get-started.md).

- Pour plus d’informations sur la migration d’une base de données SQL Server, voir [L’Assistant Migration SQL Azure](sql-database-cloud-migrate-compatible-using-ssms-migration-wizard.md).

- Pour en savoir plus sur Geo-réplication Active, voir [Réplication de Geo Active](sql-database-geo-replication-overview.md).

- Pour en savoir plus sur les rôles Web et les rôles de travail, voir [rôles de travail](../fundamentals-introduction-to-azure.md#compute). 

- Pour en savoir plus sur Bus des services Azure, voir [Bus des services Azure](https://azure.microsoft.com/services/service-bus/).

- Pour en savoir plus sur échelle automatique, voir [mise à l’échelle des services cloud](../cloud-services/cloud-services-how-to-scale.md).

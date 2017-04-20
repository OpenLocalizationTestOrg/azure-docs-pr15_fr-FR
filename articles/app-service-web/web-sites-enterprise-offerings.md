<properties 
    pageTitle="Azure Application Service Web applications offres pour les entreprises" 
    description="Montre comment utiliser Azure Application Service Web Apps pour créer des solutions de site Web pour votre entreprise d’entreprise" 
    services="app-service\web" 
    documentationCenter="" 
    authors="apwestgarth" 
    manager="wpickett" 
    editor=""/>

<tags 
    ms.service="app-service-web" 
    ms.workload="web" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="07/29/2016" 
    ms.author="anwestg"/>

# <a name="azure-app-service-web-apps-offerings-for-enterprise-whitepaper"></a>Azure Application Service Web applications offres pour Enterprise livre blanc #

La nécessité de réduire les coûts et fournir des solutions informatiques plus rapides dans un environnement en rapide évolution crée de nouveaux défis pour les développeurs, les professionnels de l’informatique et les responsables. Les utilisateurs recherchent plus en plus de leurs applications web ligne d’entreprise (métier) rapide, injoignable et disponible à partir de n’importe quel appareil. En même temps, essayez d’entreprises capitalisez sur la productivité et l’efficacité provenant d’intégration à cloud et de services mobiles, cela peut être aussi simple que de l’authentification unique sur tous les appareils à l’aide d’Active Directory sur la collaboration dans Office 365 à l’aide de données extraites d’une application métier interne qui à son tour extrait les données à partir de l’implémentation de la société de force de vente. [Azure Application Service Web Apps](http://go.microsoft.com/fwlink/?LinkId=529714) est un service cloud d’entreprise pour développer, tester et en cours d’exécution web et les applications mobiles, API Web et sites Web génériques. Il peut être utilisé pour exécuter des sites Web d’entreprise, sites intranet, applications métiers et les campagnes marketing numériques sur un réseau global de centres de données optimisée pour les échelle et disponibilité, ainsi que la prise en charge pour l’intégration continue et pratiques DevOps moderne.  

Ce livre blanc met en évidence les fonctionnalités du service [Web Apps](/services/app-service/web/) spécialement des Applications Web métiers, détaillé migration des applications web existantes et déploiement de nouvelles applications web métier sur la plateforme en cours d’exécution. 

## <a name="audience"></a>Audience ##

Professionnels de l’informatique, architectes et responsables qui souhaitent pour migrer vers les cloud web les charges de travail en cours d’exécution en local. Charges de travail Web peuvent couvrir soit entreprise à employé ou entreprise aux applications web partenaires.

## <a name="introduction"></a>Introduction ##

Application Service Web Apps est une plate-forme idéale pour héberger les applications web internes et externes et services qu’il fournit une solution gérée, économique et hautement scalable qui vous permet de vous concentrer sur fournir une valeur commerciale pour vos utilisateurs au lieu de passer beaucoup de temps et argent maintenance et au support séparent les environnements. Applications Web offre une plateforme flexible sur laquelle vous voulez déployer vos applications web d’entreprise offre la possibilité de continuer à s’authentifier locaux Active Directory via l’intégration avec Microsoft Azure Active Directory, prise en charge des déploiements simples et rapides effectue utilisation de votre continue intégration et déploiement pratiques internes, lors de la mise à l’échelle automatiquement pour agrandir avec les besoins professionnels - tout sur une plateforme managée qui vous permet de se concentrer sur pas votre infrastructure et de votre application. 

## <a name="problem-definition"></a>Définition du problème ##

Le paysage informatique change rapidement, avec un déplacement en dehors des délais long pour qu’il utilise à la demande d’hébergement sur des serveurs traditionnels avec leurs coûts capitaux haute utilisation de services évoluer automatiquement pour gérer la charge. Services informatiques doivent être faire pour réduire les coûts et encombrement de l’infrastructure et la maintenance passer l’accent sur la réduction des investissements tout en augmentant souplesse. La fin de la durée de vie du anciennes plates-formes d’infrastructure, tels que Windows Server 2003, leader aux services informatiques de réviser migration cloud pour éviter les coûts d’investissement à long terme nouveau ont potentiels. Auparavant, les responsables de l’information seraient décisions d’achat pour les autres services ; Toutefois, plus CMOs et autres têtes unité d’entreprise sont prend un rôle plus actif dans comment leur budget est passé et quel est le retour sur investissement. Plus, les entreprises doivent leur personnel à être beaucoup plus mobiles que jamais avec les employés travaillant à distance, passez plus de temps avec les clients nécessitant un accès à la gestion des systèmes.

Entreprise doit modifier mensuelle, hebdomadaire, tous les jours. Les entreprises recherchent des instantanés échelle internationale avec les services mis à jour réguliers complètes des nouvelles fonctionnalités, fournies par un tiers ou en interne.  Dans certains cas, entreprises recherchées également pour les fonctionnalités d’isoler leurs applications et de l’accès aux ressources, tout en mettant utilisation des fonctionnalités de Cloud Public. Les utilisateurs ont attentes une version ultérieure, avec nombreux recourant à des services dans leur propre vie privée tels qu’Office 365. Ils veulent pouvoir accéder aux services riche fonctionnalité similaire, à jour, dans leur cycle de vie de travail. Pour faire face à cette demande, informatique doit apparence afin de l’entreprise pour activer cette via l’intégration avec des tiers et de sélection des services, sélection prudent de plateformes qui peut s’adapter aux besoins de l’entreprise, tout en étant également fiable avec un coût total de propriété réduit.

Les équipes de développement recherchées pour présenter des bénéfices immédiate assurant de nouvelles fonctionnalités régulièrement. Ils vous recherchez une plateforme rentable et fiable qui s’intègre à leur test outils et pratiques – développement, existant, release ; et travailler avec les services informatiques automatisation déploiement, gestion et alertes, tout dans le but d’aucune interruption de service.

<a href="highlevel" />
## <a name="high-level-solution"></a>Solution de niveau élevée ##

Structures et plates-formes web sont plus utilisés pour développer, tester et héberger des applications métier.  Avec une ligne par défaut de l’application métier, par exemple un système de dépenses des employés interne, souvent constitués uniquement d’une application web avec une base de données de sauvegarde pour stocker les données liées à l’application.

Application Service Web Apps est une bonne option pour héberger ces applications, offrant infrastructure scalable et fiable qui est géré et corrigé avec près zéro intervention manuelle et le temps d’arrêt. La plateforme Microsoft Azure offre de nombreuses options de stockage de données pour prendre en charge des applications web hébergées dans des applications Web à partir de base de données SQL Microsoft Azure, scalable relationnelles de base de données-en tant que-a-service géré, aux services populaires de nos partenaires tels que la base de données MySQL ClearDB et MongoDB.

Une approche alternative consiste à utiliser votre investissement existant en local. Dans le scénario d’exemple, un système de dépenses des employés, vous souhaiterez peut-être gérer une base de données au sein de votre propre infrastructure interne. Cela peut s’expliquer par l’intégration avec les systèmes internes (création de rapports, paie, facturation, etc.) ou pour répondre à une demande de gouvernance informatique.  Applications Web fournit plusieurs méthodes permettant de vous connecter à votre infrastructure local sur :

- [Environnements de Service d’application](app-service-app-service-environment-intro.md) - environnements de Service d’application (ASE) sont une nouvelle fonctionnalité Premium qui a été récemment ajouté à l’offre de Service d’application Microsoft Azure.  ASEs proposent un environnement entièrement isolé et dédié pour exécution en toute sécurité des applications de Service d’application Azure à l’échelle élevée tout en offrant isolement et accès réseau sécurisé   
- [Connexions hybride](../biztalk-services/integration-hybrid-connection-overview.md) – hybride connexions sont une fonctionnalité de Microsoft Azure BizTalk Services et activer les applications Web pour vous connecter à local ressources en toute sécurité, par exemple SQL Server, MySQL, API Web et services web personnalisée. 
- [L’intégration des réseaux virtuel](https://azure.microsoft.com/blog/2014/09/15/azure-websites-virtual-network-integration/) – intégration Web Apps avec réseau virtuel Azure vous permet de vous connecter votre application web à un réseau virtuel Azure qui à son tour pouvant être connectés à votre infrastructure local sur via un réseau VPN site à. 

Les diagrammes suivants représenter une solution globale exemple avec les options de connectivité de ressources locaux.  Le premier montre comment cela est possible à l’aide des fonctionnalités standard du Service d’application Azure et le second montre comment cela peut être effectuée à l’aide de l’offre, environnements de Service d’application premium.

Utilisation des fonctionnalités de Service d’application Standard :![](./media/web-sites-enterprise-offerings/on-premise-connectivity-solutions.png "Using Standard App Service Features")

À l’aide d’un environnement de Service d’application :![](./media/web-sites-enterprise-offerings/on-premise-connectivity-solutions-ASE.png "Using an App Service Environment")

## <a name="business-benefits"></a>Avantages de l’entreprise ##

Application Service Web Applications fournit un hôte des avantages d’entreprise qui permettent à votre fonction être beaucoup plus économique et plus flexible lors de la remise pour des besoins professionnels. 

### <a name="paas-model"></a>Modèle PaaS ###

Application Service Web Apps est basé sur une plateforme comme un modèle de Service qui fournit un certain nombre de l’épargne coût et l’efficacité.  N’avez plus besoin de passer des heures la gestion des machines virtuelles, correction des systèmes d’exploitation et architectures. Applications Web est un environnement automatiquement corrigé qui permet de vous recentrer sur la gestion de vos applications web et pas machines virtuelles, en laissant aux équipes gratuits être rentable supplémentaires.

Le modèle PaaS renforce Web Apps permet aux spécialistes de la méthodologie DevOps répondre à leurs objectifs. En tant que professionnel, cela signifie complètes de gestion et l’intégration dans l’ensemble de l’application cycle de vie, tels que développement, test, version, surveillance et gestion et prise en charge. 

Pour les équipes de développement, continue intégration et déploiement des flux de travail peut être configurées de Visual Studio Team Services, GitHub, TeamCity, Hudson ou BitBucket, l’activation de génération automatisé, de test et de déploiement de l’activation de lancement rapide cycles, tout en réduisant le frottement impliqués dans relâché dans l’infrastructure existante. Applications Web prennent également en charge la création de plusieurs test et mise en environnements pour votre version du flux de travail, ne sont plus devez-vous réserver ou allouer matériel pour effectuer ces opérations, vous pouvez créer des environnements autant que vous souhaitez et définissez vos propres promotion pour libérer des flux de travail. En tant que professionnel que vous pouvez décider de vers un emplacement de test du contrôle de source, procédez comme une série de tests et en cas de réussite promouvoir vers un emplacement de partage et enfin intervertir en production sans interruption de service, avec l’avantage que les applications web hébergées dans des applications Web sont préchargées et hot pour offrir l’expérience client.  En outre entreprises peuvent tirer parti de la test dans les capacités de Production d’application de Service Web Apps pour diriger une section du trafic vers un autre emplacement, valider les modifications, avant de basculer tout le trafic vers le nouveau déploiement ou le retour tout le trafic vers le déploiement précédent. 

Les équipes opérationnelles peuvent sentir qu’ils sont en position mieux possible de réagir à des problèmes avec n’importe lequel de leurs applications web hébergées dans des applications Web avec intégré dans les fonctionnalités d’analyse et les alertes. Doit équipes opérationnelles ont déjà investi dans analytique et les solutions de contrôle tels de Microsoft Visual Studio Application perspectives, nouveau Relic et AppDynamics. Ceux-ci sont également entièrement prises en charge dans des applications Web l’activation de la continuité et un environnement familier à partir de laquelle surveiller vos applications web.

Enfin, applications Web fournit des fonctionnalités pour sauvegarder automatiquement vos applications qu’et bases de données hébergés directement dans un conteneur de stockage d’objets Blob Azure. Vous offre une méthode simple apparaisse et très rentable avec lesquelles vous pouvez récupérer d’urgence, en réduisant la nécessité de complexe sur locaux matérielle et logicielle.

### <a name="ease-of-migration"></a>Facilité de Migration ###

Rotation et la maintenance matérielle est un élément essentiel pour les entreprises comme accélèrent les cycles de version pour les systèmes d’exploitation et le matériel. Vous disposez peut-être d’un nombre de serveurs Windows Server 2003 R2 bientôt à la fin de prise en charge dans 2015, mais ils sont toujours hébergeant des applications web clés pour votre entreprise ? Application Service Web Apps est un excellent candidat pour héberger les applications web et vous permet de rationaliser l’immobilier de matériel d’entreprise. Applications Web permet d’accéder à une plage de spécifications matérielles qui sont gérées et conservées dans le cadre du service, évite de prendre en compte les coûts de gestion et de remplacement dans le cadre de votre budget d’infrastructure.  Migration peut être aussi simple en tant que copie et collez opération à partir de votre déploiement existant vers les applications Web ou d’une migration plus complexe où à l’aide de l’Assistant de Migration Web applications valeur ajoutée. Applications web migrés calculez la gamme complète de services Azure, intégration des services supplémentaires aux applications web. Par exemple, vous pouvez envisager d’ajouter Azure Active Directory pour contrôler l’accès à votre application basée sur une association d’utilisateurs aux groupes de sécurité. Un autre exemple peut être Ajout pour améliorer les performances et réduire la latence, fournir les Services de Cache global une meilleure expérience utilisateur. 

### <a name="enterprise-class-hosting"></a>Hébergement de classe entreprise ###

Application Service Web Apps fournit une plateforme stable et fiable qui a fait ses preuves pour être en mesure de gérer un large éventail d’entreprise doit petites charges internes de développement et de test, de sites Web à l’échelle hautement trafic élevé. À l’aide des applications Web, vous faites utiliser la même plate-forme d’entreprise classe d’hébergement par Microsoft comme une société utilise pour les charges de travail web valeur élevée. Applications Web, ainsi que tous les services sur la plateforme Azure, sont conçues avec sécurité et conformité aux exigences réglementaires, telles que ISO (ISO/IEC 27001:2005) ; Voir [http://aka.ms/azurecompliance](/support/trust-center/compliance/)SOC1 et SOC2 SSAE 16/ISAE 3402 Attestations, BAA HIPAA, PCI et Fedramp, au cœur de chaque élément et la fonctionnalité, pour plus d’informations. 

Plateforme Microsoft Azure permet de contrôles de l’autorisation par rôle l’activation des niveaux d’entreprise du contrôle aux ressources d’applications Web. RBAC offre aux entreprises la puissance d’implémenter leurs propres stratégies de gestion des accès pour l’ensemble de leurs actifs dans l’environnement Azure, en affectant des utilisateurs à des groupes et en affectant à son tour les autorisations requises à ces groupes sur la ressource comme une application web. Pour plus d’informations sur RBAC dans Azure, voir [http://aka.ms/azurerbac](../active-directory/role-based-access-control-configure.md). En utilisant des applications Web, vous pouvez être que vos applications web sont déployées dans un environnement sécurisée et vous contrôlez totalement dans territoire sur lequel vos ressources sont déployés. 

Application Service environnements Azure [http://aka.ms/aseintro](http://aka.ms/aseintro) sont une nouvelle option de plan de service premium pour les entreprises souhaitant utiliser du Service d’application Azure et ils offrent un environnement entièrement isolé et dédié.  Cela permet aux clients d’entreprise déployer des applications qui peuvent tirer parti d’échelle très haute, tout en ayant également un contrôle total sur le trafic réseau entrant et sortant, puis ASEs permettent aux applications ont connexion sécurisée haut débit sur les réseaux virtuels aux ressources locales.

Application Service Web Apps peuvent également tirer pleinement parti de vos investissements locaux activé en offrant la possibilité de se reconnecter à vos ressources internes, telles que votre data warehouse ou un environnement SharePoint. Comme indiqué dans le [niveau élevé solution](#highlevel) vous pouvez apporter permet d’établir une connexion à sur infrastructure locaux et services de connexions hybride et la connectivité réseau virtuel.

### <a name="global-scale"></a>Échelle internationale ###

Application Service Web Apps est une plateforme globale et format SVG, l’activation de vos applications web à agrandir et s’adapter aux besoins de leur croissance rapidement et à l’aide minimale à long terme planification et coût. Dans classique sur des scénarios d’infrastructure local, expansion et augmentation de la demande à la fois localement et géographiquement seraient nécessitent une grande quantité de gestion, planification et de mise en service des dépenses et gérez l’infrastructure supplémentaire. Applications Web offre la possibilité à l’échelle de vos applications web avec le degré variable et le flux de vos besoins. Par exemple à l’aide de l’application dépenses par exemple, pour la plupart des mois vos utilisateurs sont légère aux utilisateurs de l’application, mais comme la date d’échéance chaque mois pour les envois de note de frais renseigner et augmentation de l’utilisation de votre application, applications Web a la capacité de mise en service automatiquement plus infrastructure pour votre application et puis une fois que l’utilisation de l’a cessé à nouveau il peut évoluer revenir à l’infrastructure de référence que vous définissez.

Applications Web est disponible globalement dans 24 centres de données dans le monde et croissants. Pour obtenir la liste la plus à jour des régions et un emplacement, voir [http://aka.ms/azlocations](http://aka.ms/azlocations). Les applications Web, votre entreprise peut facilement atteindre échelle et une portée internationale. Que votre société se développe dans de nouvelles régions, l’hôte dans des applications Web et les tableaux de bord application que vous utilisez de création de rapports peuvent être déployées facilement en centres de données supplémentaires et répondre à des utilisateurs locaux beaucoup plus rapidement à l’aide de la combinaison des applications Web et Azure le trafic gestionnaire, avec l’avantage de l’infrastructure scalable en dessous de pouvoir réduire et développer selon les besoins de la modification des bureaux régionaux.
 
## <a name="solution-details"></a>Détails de la solution ##

Examinons un exemple d’un scénario de migration d’application. Ce document présente les détails de comment fonctionnalités application Service Web Apps ensemble, fournir solution idéale et priorité.
 
Dans cet exemple la ligne d’application métier que nous aborderons est une application qui permet aux employés de présenter des leurs dépenses pour le remboursement de notes de frais. L’application est hébergée sur un Windows Server 2003 R2 IIS 6 en cours d’exécution et la base de données est une base de données SQL Server 2005. La raison pour laquelle nous avons choisi plus anciens server se trouve à la fin du Service pour Windows Server 2003 R2 et SQL Server 2005 venir, et nous avons [Outils](http://aka.ms/websitesmigration) et [conseils](http://aka.ms/websitesmigrationresources) pour déplacer automatiquement les charges de travail dans Azure. À l’esprit que le modèle utilisé dans cet exemple s’appliquent à une large vérité scénarios de migration. 

### <a name="migrate-existing-application"></a>Migration d’une Application existante ###

Étape 1 de la solution globale pour le déplacement d’une application métier aux applications Web consiste à identifier les ressources d’application existantes et l’architecture. L’exemple dans ce document est une application web ASP.NET hébergée sur un serveur IIS unique avec la base de données hébergée sur un serveur SQL séparé, comme le montre l’illustration suivante. Connexion d’employés au système à l’aide d’une combinaison nom d’utilisateur et mot de passe, entrez les détails de dépenses et de télécharger les copies numérisées de confirmations, dans la base de données, pour chaque élément de note de frais. 
 
![](./media/web-sites-enterprise-offerings/on-premise-app-example.png)

#### <a name="items-to-consider"></a>Éléments à prendre en considération ####

Lorsque application de migration à partir d’un environnement local, vous souhaiterez peut-être n’oubliez pas, peu de restrictions Web Apps. Voici quelques rubriques clés importants lors de la migration des applications web aux applications Web ([http://aka.ms/websitesmigrationresources](http://aka.ms/websitesmigrationresources)) :

-   Liaisons de port – Web Apps prend uniquement en charge le port 80 pour HTTP et le port 443 pour le trafic HTTPS. Si votre application utilise un autre port, puis migrer une fois l’application fera l’utilisation de ports 80 pour HTTP et le port 443 pour le trafic HTTPS. Il s’agit souvent un problème sans danger comme il est courant dans déploiement pour rendre utilisation des différents ports afin d’éviter l’utilisation de noms de domaine, notamment dans les environnements de développement et de test
-   Authentification – Web Apps prend en charge l’authentification anonyme par défaut et l’authentification par formulaire identifiée par une application. Applications Web peut proposer l’authentification Windows lorsque l’application est intégrée à Azure Active Directory et ADFS uniquement. Il s’agit d’une fonctionnalité qui est décrit plus en détail [ici](http://aka.ms/azurebizapp) 
-   GAC basé assemblys – Web Apps n’autorise pas le déploiement d’assemblys pour le Assembly Cache (global). Par conséquent, si l’application en cours de migration utilise cette fonctionnalité en local, envisagez de déplacer les assemblys dans le dossier bin de l’application.
-   IIS 5 – Le Mode de compatibilité Web Apps ne reconnaît pas le Mode de compatibilité IIS 5 et en tant que tel chaque instance Web Apps et toutes les applications web vers l’instance parent applications Web s’exécutent dans le même processus de travail au sein d’un pool d’applications unique.
-   Utilisation des bibliothèques COM – Web Apps n’autorise pas l’enregistrement des composants COM sur la plateforme. Par conséquent si l’application effectue utiliser des composants COM, ils devront être réécrits dans du code managé et déploiement avec l’application.
-   Filtres ISAPI – filtres ISAPI peuvent être prises en charge dans des applications Web. Ils doivent être déployés dans le cadre de l’application et enregistré dans le fichier web.config de l’application web. Pour plus d’informations, voir [http://aka.ms/azurewebsitesxdt](web-sites-transform-extend.md). 

Une fois que les rubriques suivantes ont été prises en compte, votre application web doit être prête pour le Cloud. Et ne vous inquiétez pas si certaines rubriques ne sont pas complètement remplies, l’outil de migration donne mieux à la migration. 

Les étapes du processus de migration sont pour créer une application Service web app et une base de données SQL Azure. Il existe plusieurs tailles d’instances des applications Web avec un nombre variable de cœurs de processeur et montants RAM disponibles pour les sélectionner selon vos besoins d’applications web. Pour plus d’informations et tarifs, voir [http://aka.ms/azurewebsitesskus](/pricing/details/websites/). De même, base de données SQL Microsoft Azure satisfait à tous les besoins d’une entreprise avec différents niveaux de service et de performances pour répondre aux besoins. Plus d’informations, consultez [http://aka.ms/azuresqldbskus](/pricing/details/sql-database/). Une fois créé, l’application est téléchargé dans l’application de Service Web Apps, soit via FTP ou WebDeploy, puis déplacez la base de données.

Dans cette migration la solution utilise la base de données SQL Azure, c'est-à-dire mais pas la seule base est pris en charge sur Azure. Sociétés peuvent également agrandir utilisation de MySQL, MongoDB, Azure DocumentDB et bien d’autres encore via les modules complémentaires que vous peuvent acheter sur [Azure Store](/marketplace/partner-program/). 

Lorsque vous créez une base de données SQL Azure un certain nombre d’options est disponible pour importer une base de données existante à partir d’un serveur local de générer un script d’une base de données existante à l’aide de [couche données Application exporter et importer](http://aka.ms/dacpac). 

La base de données application dépenses a été créé en créant une nouvelle base de données SQL Azure, connexion à la base de données à l’aide de SQL Server Management Studio, puis en exécutant un script afin de créer le schéma de base de données et y ajouter des données à partir de la base de données locale.

La dernière étape de cette première étape de la migration nécessite la mise à jour des chaînes de connexion à la base de données pour l’application. Il est possible via le portail Azure. Pour chaque application web vous pouvez modifier les paramètres spécifiques d’application, y compris les chaînes de connexion utilisées par l’application pour vous connecter à une base de données en cours d’utilisation.

### <a name="alternatives-to-using-azure-sql-database"></a>Alternatives à l’utilisation de base de données SQL Azure ###

La plateforme Azure propose des alternatives à l’utilisation de la base de données SQL Azure comme une base de données principale applications web, il s’agit pour activer c'est-à-dire différentes charges de travail Utilisez une NoSQL Solution ou pour activer la plateforme en fonction des besoins de données d’une entreprise. Par exemple, une entreprise peut contenir des données qui ne doivent pas être stockées hors site ou dans un environnement cloud public et par conséquent serait pour conserver l’utilisation de leur base de données locale.

#### <a name="connectivity-to-on-premises-resources"></a>Connectivité à sur les ressources locaux ####
Application Service Web Apps offre plusieurs options pour vous connecter à des ressources locaux, tels que des bases de données, permettant de réutiliser des infrastructure valeur élevée existante. Les options sont comme indiqué ci-dessous :

- Environnements de Service d’application sont isolés et créés au sein d’un sous réseau de réseau virtuel, ce qui permet l’environnement de communiquer avec des points de terminaison privés situés au sein du même réseau virtuel - [http://aka.ms/appserviceasenetworking](http://aka.ms/appserviceasenetworking)
- Applications virtuel Web l’intégration des réseaux prend en charge l’intégration entre les applications Web et un réseau virtuel Azure, ce qui permet d’accéder aux ressources en cours d’exécution dans votre réseau virtuel qui si connecté à votre réseau local sur site-à-site VPN, permet la connectivité directement vers votre sur des systèmes locaux.
- Connexions hybride sont une fonctionnalité des Services BizTalk Azure et fournissent un moyen facile de se connecter aux différents locaux ressources telles que SQL Server, MySQL, API Web HTTP et plus les Services Web personnalisés.

#### <a name="scale-and-resiliency"></a>Échelle et la résilience ####

Lorsqu’une entreprise grandit ses employés, via acquisitions ou croissance ORGANIQUE naturelle, donc trop doit web échelle d’applications pour répondre à ces exigences de nouveau. Bien aujourd'hui cette pratique est courante pour afficher une répartition davantage des équipes et les employés à distance, par exemple des sociétés avec bureaux aux États-Unis, Europe et Asie, avec un client mobile forces dans de nombreux territoires plus. Applications Web a la possibilité pour gérer les modifications élastiques de l’échelle parfaitement et automatiquement.

Application Service Web Apps permet aux applications web doit être configuré pour mettre à l’échelle automatiquement via le portail Azure, en fonction de deux vecteurs – planifiées des heures ou par l’utilisation du processeur. Mise à l’échelle de Web applications automatique fournit un moyen rentable et extrêmement flexible pour répondre aux changements supérieures de l’utilisation pour toutes les applications d’entreprise, à partir d’applications web comme nos de notes de frais système aux sites Web, quelle expérience un très résistant du trafic pendant une courte durée de la promotion de marketing. Pour plus d’informations et des conseils sur la mise à l’échelle de vos applications web à l’aide des applications Web, voir [comment échelle de sites Web](web-sites-scale.md).

Outre la possibilité de mise à l’échelle des applications Web, l’ensemble de la plate-forme permet de continuité des activités et la résilience grâce à la distribution possible des applications web et leurs ressources sur plusieurs centres de données et les régions géographiques.

## <a name="summary"></a>Résumé ##
Application Service Web Apps offre une solution flexible, rentable et répondre aux besoins de l’entreprise dans un environnement en rapide évolution dynamiques. Web Apps permet aux entreprises augmenter la productivité et l’efficacité en transformant utiliser d’une plateforme managée modernes DevOps et des fonctions mains réduites sur la gestion, tout en fournissant des fonctionnalités d’entreprise dans l’échelle, la résilience, sécurité et intégration avec les ressources locales.

## <a name="call-to-action"></a>Appel à l’Action ##
Pour plus d’informations sur le service, visitez [http://aka.ms/enterprisewebsites](/services/websites/enterprise/) où plus d’informations peuvent provenir et se Azure Application Service Web Apps pour une version d’évaluation d’aujourd'hui à [http://aka.ms/azuretrial](/pricing/free-trial/) pour évaluer le service et découvrez les avantages pour votre entreprise.

[AZURE.INCLUDE [app-service-web-whats-changed](../../includes/app-service-web-whats-changed.md)]

[AZURE.INCLUDE [app-service-web-try-app-service](../../includes/app-service-web-try-app-service.md)]
 
 

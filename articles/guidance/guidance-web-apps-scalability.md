<properties
   pageTitle="Application web Scalable | Architecture de référence Azure | Microsoft Azure"
   description="Amélioration extensibilité élevées dans une application web en cours d’exécution dans Microsoft Azure."
   services="app-service,app-service\web,sql-database"
   documentationCenter="na"
   authors="MikeWasson"
   manager="roshar"
   editor=""
   tags=""/>

<tags
   ms.service="guidance"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="07/12/2016"
   ms.author="mwasson"/>


# <a name="improving-scalability-in-a-web-application"></a>Amélioration extensibilité élevées dans une application web 

[AZURE.INCLUDE [pnp-RA-branding](../../includes/guidance-pnp-header-include.md)]

Cet article vous explique une architecture recommandée pour améliorer les performances dans une application web s’exécutant sur Microsoft Azure et extensibilité élevées. L’architecture s’appuie sur [architecture de référence Azure : application web base][basic-web-app]. Les recommandations et les considérations à partir de cet article s’appliquent à cette architecture également.

>[AZURE.NOTE] Azure comporte deux modèles de déploiement différents : Gestionnaire de ressources et classique. Cet article utilise le Gestionnaire de ressources, Microsoft recommande pour les nouveaux déploiements.

## <a name="architecture-diagram"></a>Diagramme d’architecture

![[0]][0]

L’architecture comprend les éléments suivants :

- **Groupe de ressources**. Un [groupe de ressources] [ resource-group] est un conteneur logique pour les ressources Azure. 

- ** [Dans le navigateur] [ app-service-web-app] ** et ** [application API][app-service-api-app]**. Une application moderne standard peut-être inclure un site Web et un ou plusieurs web RESTful API. Un site web API susceptibles d’être utilisé par les clients de navigateur via AJAX, par les applications clientes native ou par des applications côté serveur. Pour des raisons de conception web API, voir [recommandations pour la conception API][api-guidance].    

- **WebJob**. Utiliser [Azure WebJobs] [ webjobs] pour exécuter des tâches longues en arrière-plan. WebJobs peuvent s’exécuter sur un planning, en continu, ou en réponse à un déclencheur, tel que le placement d’un message dans une file d’attente. Un WebJob s’exécute en arrière-plan dans le contexte d’une application de Service d’application. 

- **File d’attente**. Dans l’architecture illustré ici, les files d’attente application tâches en arrière-plan en plaçant un message vers un [Espace de stockage Azure file d’attente] [ queue-storage] file d’attente. Le message de déclencher une fonction dans la WebJob. 

    Vous pouvez également utiliser des files d’attente Bus des services. Pour une comparaison, consultez [Azure files d’attente et les files d’attente Service Bus - comparées et différence entre et][queues-compared].

- **Cache**. Stocker des données semi-structurées statiques dans [Azure Redis Cache][azure-redis].  

- **CDN**. Utiliser le [Réseau de distribution de contenu Azure] [ azure-cdn] (CDN) en cache le contenu accessible au public, pour une latence inférieure et la livraison plus rapide de contenu.

- **Stockage des données.** Utiliser la [Base de données SQL Azure] [ sql-db] pour les données relationnelles. Pour des données non relationnelles, envisagez d’un magasin NoSQL, telles que le stockage de Table Azure ou [DocumentDB][documentdb].

- **Effectuer une recherche dans azure**. Utiliser [La recherche Azure] [ azure-search] pour ajouter la fonctionnalité de recherche, y compris les suggestions de recherche, recherche partielle et recherche spécifiques à une langue. Recherche Azure est généralement utilisée conjointement avec un autre magasin de données, notamment si la banque de données principale nécessite la cohérence stricte. Dans cette approche, vous voulez les données faisant autoritées dans l’autre magasin de données et placez l’index de recherche dans Azure rechercher. Recherche Azure permet également de consolider un index de recherche unique à partir de plusieurs banques de données.  

- **Messagerie/SMS**. Si votre application a besoin envoyer des messages électroniques ou SMS, utilisez un service tiers comme SendGrid ou Twilio, plutôt que la création de cette fonctionnalité directement dans l’application.

## <a name="recommendations"></a>Recommandations

### <a name="app-service-apps"></a>Applications de Service d’application 

Nous vous recommandons de créer l’application web et l’API web comme des applications de Service d’application distinctes. Ce modèle vous permet de les exécuter dans les plans de Service d’application distincts, qui à son tour vous permet de les faire évoluer indépendamment. Si vous n’avez pas besoin ce niveau d’extensibilité élevées en tout d’abord, vous pouvez déployer les applications dans le même plan et les déplacer dans des plans distincts plus tard, si nécessaire. (Pour les plans basique, Standard et Premium, vous êtes facturé pour les instances de machine virtuelle dans le plan, et non par l’application. Voir [tarification application Service][app-service-pricing])

Si vous envisagez d’utiliser les *Tables facile* ou les fonctionnalités *API facile* d’application Service Mobile applications, créer une application de Service d’application distincte à cet effet.  Ces fonctionnalités reposent sur une infrastructure d’application spécifique afin de les activer.

### <a name="webjobs"></a>WebJobs

Si la WebJob est beaucoup de ressources, envisagez de déploiement pour une application de Service d’application vide dans un plan de Service d’application distinct, permettent de fournir des instances dédiées pour la WebJob. Consultez l’article [conseils de travaux d’arrière-plan][webjobs-guidance].  

### <a name="cache"></a>Cache

Vous pouvez améliorer les performances et extensibilité élevées en utilisant [Azure Redis Cache] [ azure-redis] pour mettre en cache des données. Vous pouvez utiliser le Cache Redis pour :

- Données de transaction semi-structurées statique.

- État de la session.

- Sortie HTML. Cela peut être utile dans les applications qui un rendu HTML complexes. 

Pour plus d’instructions sur la conception d’une stratégie de mise en cache, voir le [Guide de mise en cache][caching-guidance].

### <a name="cdn"></a>CDN 

Utiliser [Azure CDN] [ azure-cdn] en contenu statique du cache. Le principal avantage d’un CDN consiste à réduire la latence pour les utilisateurs, car le contenu est mis en cache sur un *serveur de périphérie* géographiquement près de l’utilisateur. CDN peut également réduire la charge sur l’application, étant donné que ce trafic n’est pas géré par l’application.

- Si votre application se compose principalement de pages statiques, envisagez d’utiliser CDN pour mettre en cache de l’application entière. Voir [utiliser Azure CDN dans le Service d’application Azure][cdn-app-service].

- Dans le cas contraire, placer du contenu statique, comme des images, CSS et HTML des fichiers, dans le stockage Azure et utilisation CDN pour mettre en cache les fichiers. Voir [intégrer un compte de stockage avec CDN][cdn-storage-account].

> [AZURE.NOTE] Azure CDN ne peut pas fournir du contenu qui requiert une authentification.

Pour plus d’instructions, voir [recommandations pour la remise CDN (Content Network)][cdn-guidance]. 

### <a name="storage"></a>Espace de stockage

Les applications modernes traitent souvent grandes quantités de données. Afin de mettre à l’échelle pour le cloud, il est important de choisir le type de stockage approprié. Voici quelques recommandations de référence.  Pour plus d’instructions, voir [Évaluer capacité de stockage pour les Solutions de persistance Polyglot][polyglot-storage].

Ce que vous voulez stocker | Exemple | Stockage recommandée
--- | --- | ---
Fichiers | Images, des documents, des fichiers PDF | Stockage d’objets Blob Azure
Paires clé/valeur | Données de profil utilisateur recherchée par ID d’utilisateur | Stockage de Table Azure
Messages courts destinées à se déclencher un traitement approfondi | Demandes de commande | Stockage Azure file d’attente, file d’attente de Service Bus ou Service Bus rubrique
Données non relationnelles, avec un schéma flexible, nécessitant une interrogation de base | Catalogue de produits | Base de données de documents, tels que DocumentDB Azure, MongoDB ou CouchDB Apache
Prise en charge, schéma stricte et/ou la cohérence forte d’interrogation de données relationnelles, nécessitant plus riche | Inventaire des produits | Base de données SQL Azure

## <a name="scalability-considerations"></a>Considérations relatives à l’extensibilité élevées

### <a name="app-service-app"></a>Application de Service d’application

Si votre solution inclut plusieurs applications de Service d’application, pensez à déployer les pour séparer les offres de Service d’application. Cette approche vous permet de leur évolution séparément, car ils s’exécutent sur des instances distinctes. Pour plus d’informations sur la mise à l’échelle, consultez les [Considérations extensibilité élevées] [ basic-web-app-scalability] section dans l' [architecture d’application web base][basic-web-app].

De même, envisagez de placer un WebJob dans sa propre plan, afin que les tâches en arrière-plan ne s’exécutent sur les mêmes instances qui gèrent les demandes HTTP.  

### <a name="sql-database"></a>Base de données SQL

Augmenter extensibilité élevées d’une base de données SQL *ont* la base de données &mdash; , partition horizontalement la base de données. Ont vous permet de faire évoluer horizontalement, la base de données à l’aide des [Outils de base de données élastique][sql-elastic]. Potentiels d’ont les avantages suivants :

- Une meilleure débit des transactions.

- Requêtes peuvent s’exécuter plus vite sur un sous-ensemble de données. 

### <a name="azure-search"></a>Recherche Azure

Recherche Azure supprime la charge d’effectuer des recherches de données complexes à partir de la banque de données principale, et il peut évoluer pour gérer la charge. Voir les [niveaux d’échelle des ressources pour les requêtes et les charges de travail d’indexation dans Azure recherche][azure-search-scaling].

## <a name="security-considerations"></a>Considérations sur la sécurité

### <a name="cross-origin-resource-sharing-cors"></a>Ressources d’origine croisée partage (CORS)

Si vous créez un site Web et API web comme applications distinctes, le site Web ne peut pas passer des appels de AJAX côté client à l’API, sauf si vous activez CORS. 

> [AZURE.NOTE] Sécurité du navigateur empêche une page web d’apporter des demandes AJAX vers un autre domaine. Cette limitation s’appelle la stratégie de même origine et un site malveillant empêche la lecture des données sentitive à partir d’un autre site. CORS est une norme W3C qui permet à un serveur alléger la stratégie de même origine et autoriser certaines demandes origine croisée lors du refus d’autres personnes.

Services d’application prend en charge pour CORS, sans avoir à écrire un code d’application. Voir [consommer une application API JavaScript à l’aide de CORS][cors]. Ajouter le site Web à la liste des origines autorisés pour l’API. 

### <a name="sql-database-encryption"></a>Chiffrement de base de données SQL

Utilisation [Du chiffrement des données Transparent] [ sql-encryption] si vous avez besoin chiffrer les données au repos dans la base de données. Cette fonctionnalité effectue en temps réel chiffrer et déchiffrer une base de données entière (y compris les copies de sauvegarde et les fichiers journaux des transactions), sans avoir à modifier à l’application. Chiffrement a ajoute une latence, il est recommandé de séparer les données qui doivent être sécurisé dans sa propre base de données et activer le chiffrement uniquement pour cette base de données.  

## <a name="next-steps"></a>Étapes suivantes

- Pour augmenter la disponibilité déployer l’application dans plusieurs régions et utiliser le [Gestionnaire de trafic Azure] [ tm] pour le basculement. Pour plus d’informations, voir [architecture de référence Azure : application Web avec disponibilité][web-app-multi-region].    

<!-- links -->

[api-guidance]: ../best-practices-api-design.md
[app-service-web-app]: ../app-service-web/app-service-web-overview.md
[app-service-api-app]: ../app-service-api/app-service-api-apps-why-best-platform.md
[app-service-pricing]: https://azure.microsoft.com/en-us/pricing/details/app-service/
[azure-cdn]: https://azure.microsoft.com/en-us/services/cdn/
[azure-redis]: https://azure.microsoft.com/en-us/services/cache/
[azure-search]: https://azure.microsoft.com/en-us/documentation/services/search/
[azure-search-scaling]: ../search/search-capacity-planning.md
[background-jobs]: ../best-practices-background-jobs.md
[basic-web-app]: guidance-web-apps-basic.md
[basic-web-app-scalability]: guidance-web-apps-basic.md#scalability-considerations 
[caching-guidance]: ../best-practices-caching.md
[cdn-app-service]: ../app-service-web/cdn-websites-with-cdn.md
[cdn-storage-account]: ../cdn/cdn-create-a-storage-account-with-cdn.md
[cdn-guidance]: ../best-practices-cdn.md
[cors]: ../app-service-api/app-service-api-cors-consume-javascript.md
[documentdb]: https://azure.microsoft.com/en-us/documentation/services/documentdb/
[polyglot-storage]: https://github.com/mspnp/azure-guidance/blob/master/Polyglot-Solutions.md
[queue-storage]: ../storage/storage-dotnet-how-to-use-queues.md
[queues-compared]: ../service-bus-messaging/service-bus-azure-and-service-bus-queues-compared-contrasted.md
[resource-group]: ../resource-group-overview.md
[sql-db]: https://azure.microsoft.com/en-us/documentation/services/sql-database/
[sql-elastic]: ../sql-database/sql-database-elastic-scale-introduction.md
[sql-encryption]: https://msdn.microsoft.com/en-us/library/dn948096.aspx
[tm]: https://azure.microsoft.com/en-us/services/traffic-manager/
[web-app-multi-region]: ./guidance-web-apps-multi-region.md
[webjobs-guidance]: ../best-practices-background-jobs.md
[webjobs]: ../app-service/app-service-webjobs-readme.md
[0]: ./media/blueprints/paas-web-scalability.png "Application Web dans Azure avec extensibilité élevées améliorée"
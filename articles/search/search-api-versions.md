<properties
   pageTitle="Versions de l’API de recherche Azure | Microsoft Azure | API de recherche"
   description="Stratégie de version pour Azure recherche reste API et la bibliothèque de client dans le Kit de développement .NET."
   services="search"
   documentationCenter=""
   authors="brjohnstmsft"
   manager="pablocas"
   editor=""/>

<tags
   ms.service="search"
   ms.devlang="dotnet"
   ms.workload="search"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.date="08/16/2016"
   ms.author="brjohnst"/>

# <a name="api-versions-in-azure-search"></a>Versions de l’API dans recherche Azure

Recherche Azure concernant reporte les mises à jour de la fonctionnalité de façon régulière. Parfois, mais pas toujours, ces mises à jour nécessitent nous publier une nouvelle version de notre API afin du pour conserver la compatibilité descendante. Publication d’une nouvelle version permet de contrôler quand et comment intégrer mises à jour de service de recherche dans votre code.

En règle générale, nous essaie de publier des nouvelles versions uniquement lorsque cela est nécessaire, dans la mesure où elle peut impliquer des efforts à mettre à niveau votre code pour utiliser une nouvelle version de l’API. Nous finaux publient uniquement une nouvelle version si nous avons besoin de modifier certains aspects de l’API de façon à ce sauts de compatibilité descendante. Cela peut se produire en raison des solutions aux fonctionnalités existantes, ou en raison de nouvelles fonctionnalités qui changent existant surface d’API.

Nous suivent la même règle les mises à jour du Kit de développement logiciel. Le Kit de développement de recherche Azure respecte les règles de [contrôle de version sémantique](http://semver.org/) , ce qui signifie que sa version comprend trois parties : principal, secondaire et créer nombre (par exemple, 1.1.0). Nous sera lancé une nouvelle version majeure du Kit de développement uniquement en cas de modifications avec les versions précédentes. Les mises à jour de la fonctionnalité insécable, nous incrémente la version mineure et pour corriger les bogues nous augmente uniquement le numéro de version.

##<a name="snapshot-of-current-versions"></a>Instantané de versions actuelles 

Sous est un instantané de la version actuelle de toutes les interfaces de programmation pour Azure recherche. Vous trouverez les calendriers et autres détails dans les sections suivantes de ce document.

Interfaces|Dernière version principale|État
----------|-------------------------|------
[KIT DE DÉVELOPPEMENT .NET](https://msdn.microsoft.com/library/azure/dn951165.aspx)|1.1|En règle générale disponible, publié février 2016
[Aperçu du Kit de développement .NET](https://msdn.microsoft.com/library/mt761536%28v=azure.103%29.aspx)|Aperçu de 2.0|Aperçu, publié août 2016
[API REST de service](https://msdn.microsoft.com/library/azure/dn798935.aspx)|2015-02-28|Généralement disponibles
[Aperçu de l’API REST service](search-api-2015-02-28-preview.md)|2015-02-28-aperçu|Aperçu
[Gestion des API REST](https://msdn.microsoft.com/library/azure/dn832684.aspx)|2015-08-19|Généralement disponibles

Pour l’API REST, y compris la `api-version` sur chaque appel est nécessaire. Cela facilite la cibler une version spécifique, par exemple un aperçu de l’API. L’exemple suivant illustre comment la `api-version` paramètre est spécifié :

    GET https://adventure-works.search.windows.net/indexes/bikes?api-version=2015-02-28

> [AZURE.NOTE] Bien que chaque demande a un `api-version`, nous vous recommandons d’utiliser la même version pour toutes les demandes de l’API. Cela est particulièrement vrai lors de la nouvelle API versions introduire attributs ou opérations qui ne sont pas reconnues par des versions antérieures. Combinaison de versions d’API peut avoir des effets inattendus et doit être évitée.
> 
L’API REST de Service et API REST gestion sont créée indépendamment les uns des autres. Les similitudes entre dans les numéros de version est co-création secondaire.

Généralement disponibles (ou disponibilité générale) API peut être utilisé dans production et sont soumis à des contrats de niveau de service Azure. Versions d’évaluation ont expériences fonctionnalités qui ne sont pas toujours migrées vers une version disponible. **Nous vous déconseillons fortement l’option Aperçu API dans les applications de production.**

##<a name="sdk-version-roadmap"></a>Feuille de route SDK version

Chaque version du Kit de développement .NET cible une version particulière de l’API REST de Service. Fonctionnalités sont transférées dans l’API REST tout d’abord et ensuite implémentées dans le Kit de développement.

Le Kit de développement .NET est désormais disponible et travail est déjà en cours sur la version suivante. Le tableau suivant recherche en avance pour les versions futures du Kit de développement afin que vous ayez une idée de ce qui se passe ensuite.

Version du Kit de développement .NET|Version de l’API REST|Fonctionnalités|ÊTA
----------------|----------------|--------|---
1.1|2015-02-28|Syntaxe de requête Lucene|Février 2016
Aperçu de 2.0|2015-02-28-aperçu|Analyseurs personnalisés, Blob Azure et indexeurs de tableau, les mappages de champs, ETags|Août 2016
2.x|Nouvelle version de l’API de disponibilité générale|Identique 2.0 preview|Au plus tôt 4e 2016

##<a name="about-preview-and-generally-available-versions"></a>À propos des versions Preview et en règle générale

Recherche Azure toujours des mises à jour de fonctionnalités expériences via l’API REST tout d’abord, puis par le biais du Kit de développement .NET version précommerciale.

Fonctionnalités ne sont pas nécessairement être migrées vers une version disponible. Alors que les fonctionnalités dans une version de disponibilité générale sont considérés comme stable et peu susceptibles de changer à l’exception des améliorations et correctifs de compatibilité descendante petites, fonctionnalités sont disponibles pour tester et essais, dans le but de recueillir les commentaires sur la fonctionnalité conception et implémentation. 

Toutefois, étant donné que les fonctionnalités peuvent être modifiées, nous vous déconseillons écrire du code de production qui prennent une dépendance sur les versions d’évaluation. Si vous utilisez une ancienne version preview, nous vous recommandons de migrer vers la version de (disponibilité générale) généralement disponible. 

Pour le Kit de développement .NET : conseils pour la migration de code sont accessibles à [mettre à niveau le Kit de développement .NET](search-dotnet-sdk-migration.md).

Disponibilité générale signifie que Azure recherche est désormais sous le niveau accord de service (). Vous trouverez le SLA à [Azure des contrats de niveau de Service de recherche](https://azure.microsoft.com/support/legal/sla/search/v1_0/).


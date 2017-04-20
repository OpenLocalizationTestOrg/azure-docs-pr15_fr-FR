<properties
    pageTitle="Choisissez une référence SKU ou tarifs niveau pour la recherche Azure | Microsoft Azure"
    description="Recherche Azure peut être mis en service à ces références SKU : gratuit, basique et Standard, où Standard est disponible dans différentes configurations de ressources et les niveaux de capacité."
    services="search"
    documentationCenter=""
    authors="HeidiSteen"
    manager="jhubbard"
    editor=""
    tags="azure-portal"/>

<tags
    ms.service="search"
    ms.devlang="NA"
    ms.workload="search"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.date="10/24/2016"
    ms.author="heidist"/>

# <a name="choose-a-sku-or-pricing-tier-for-azure-search"></a>Choisissez une référence SKU ou tarifs niveau pour la recherche d’Azure

Dans Rechercher Azure, un [service est mis en service](search-create-service-portal.md) auprès d’un niveau de tarification spécifique ou référence (SKU). Les options incluent **libre**, **base**ou **Standard**, où **Standard** est disponible dans plusieurs configurations et les capacités. 

L’objectif de cet article est pour vous aider à choisir un niveau. Si une capacité de s’avère pour être trop bas, vous devez configurer un nouveau service au niveau supérieur, puis recharger votre index. Il n’existe aucune mise à niveau du service même à partir d’une référence à un autre. 

> [AZURE.NOTE] Après avoir choisi un niveau et la [mise en service un service de recherche](search-create-service-portal.md), vous pouvez augmenter réplica et partition compte au sein du service. Pour des instructions, voir [niveaux d’échelle des ressources pour les requêtes et les charges de travail d’indexation](search-capacity-planning.md).

## <a name="how-to-approach-a-pricing-tier-decision"></a>Comment approche une décision couche tarification

Dans Rechercher Azure, la couche détermine capacité, disponibilité des fonctionnalités pas. En règle générale, les fonctionnalités sont disponibles à tous les niveaux, y compris les fonctionnalités. La seule exception n’est aucun pris en charge indexeurs en HD S3.

> [AZURE.TIP] Nous vous recommandons de vous devez toujours configurer un service **gratuit** (une par abonnement, sans date d’expiration) afin que sa disponibles pour les projets légers. Utiliser le service **gratuit** de test et d’évaluation ; créer un deuxième service facturable à la couche **Standard** ou de **base** de production ou plus grandes charges de travail de test.

Capacité et les coûts de l’exécution du service accédez dans pair. Informations dans cet article peuvent vous aider à décider de référence (SKU) offre le bon équilibre, mais pour une des qu’elle soit utile, vous devez au moins estimations sur les éléments suivants :

- Nombre et la taille des index que vous prévoyez de créer
- Nombre et la taille des documents à télécharger
- Une idée du volume de requête, en termes de requêtes par seconde (QPS)

Nombre et la taille sont importants car limites maximales accessibles via une limite maximale sur le nombre d’index ou de documents dans un service, ou sur les ressources (stockage ou réplicas) utilisés par le service. La limite réelle de votre service est selon ce qui est utilisé en premier : ressources ou des objets.

Des estimations en main, les étapes suivantes doivent simplifier le processus :

- **Étape 1** Passez en revue les descriptions de référence (SKU) ci-dessous pour en savoir plus sur les options disponibles.
- **Étape 2** Répondre aux questions ci-dessous pour être soumis à une décision préliminaire.
- **Étape 3** Finalisez votre décision en consultant limites physiques sur le stockage et les prix.

## <a name="sku-descriptions"></a>Descriptions de référence (SKU)

Le tableau suivant fournit des descriptions de chaque niveau. 

Niveau|Scénarios principaux
----|-----------------
**Gratuit**|Un service partagé, sans frais, utilisée pour évaluation, enquête ou petites charges de travail. Car il est partagé avec d’autres abonnés, débit de requêtes et l’indexation varie en fonction qui utilise le service. Capacité est petite (50 Mo ou 3 index avec les 10 000 documents chacune).
**Base**|Charges de travail petite sur du matériel dédié. Hautement disponible. La capacité est jusqu'à 3 doubles et 1 partition (2 Go).
**S1**|1 standard prend en charge les combinaisons flexibles des partitions (12) et des duplications (12), utilisées pour les charges de travail moyennes sur du matériel dédié. Vous pouvez affecter des partitions et duplications dans combinaisons pris en charge par un nombre maximal de 36 unités facturables recherche. À ce niveau, partitions sont 25 Go et QPS est environ 15 requêtes par seconde.
**S2**|2 standard s’exécute plus grandes charges de travail en utilisant les mêmes unités 36 recherche que S1 mais de doubles et de taille des partitions plus grandes. À ce niveau, partitions sont 100 Go et QPS est environ 60 requêtes par seconde.
**S3**|3 standard s’exécute proportionnellement plus grandes charges de travail sur les systèmes de fin supérieurs, en configurations de jusqu'à 12 partitions ou 12 réplicas unités de recherche sous 36. À ce niveau, partitions sont 200 Go et QPS est plus de 60 requêtes par seconde. 
**S3 HD**|Standard 3 haute densité est conçu pour un grand nombre d’index plus petits. Vous pouvez avoir jusqu'à 3 partitions, à 200 Go. QPS est plus de 60 requêtes par seconde. 

> [AZURE.NOTE] Réplica et partition maximales sont facturées absence en tant qu’unités de recherche (36 unité maximum par service), qui impose une limite inférieure efficace à ce que la valeur maximale implique à la valeur nominale. Par exemple, pour utiliser le nombre maximal de 12 réplicas, vous pouvez avoir au plus 3 partitions (12 * 3 = 36 unités). De même, pour utiliser des partitions maximales, réduisez réplicas à 3. Voir [niveaux d’échelle des ressources pour les requêtes et les charges de travail d’indexation dans Azure recherche](search-capacity-planning.md) pour un graphique sur les combinaisons autorisées.

## <a name="review-limits-per-tier"></a>Limites de révision par niveau

Le tableau suivant est un sous-ensemble des limites des [Limites de Service de recherche Azure](search-limits-quotas-capacity.md). Il répertorie les facteurs plus susceptibles d’avoir un impact sur une décision de référence (SKU). Vous pouvez faire référence à ce graphique lorsque vous examinez les questions ci-dessous.

Ressource|Gratuit|Base|S1|S2|S3 |S3 HD
---|---|---|---|----|---|----
Niveau accord de service)|Aucune <sup>1</sup> |Oui |Oui  |Oui |Oui  |Oui 
Limites d’index|3|5|50|200|200|1000 <sup>2</sup>
Limites de document|10 000 total|1 million par service|15 millions de partition |60 millions par partition|120 millions par partition |1 million d’index
Partitions maximales|N/A |1 |12  |12 |12|3 <sup>2</sup>
Taille de la partition|50 Mo au total|2 Go par service|25 Go par partition |100 Go par partition (jusqu'à 1,2 To maximum par service)|200 Go par partition (jusqu'à 2,4 To maximum par service)|200 Go (avec un maximum de 600 Go par service)
Réplicas maximales|N/A |3 |12 |12 |12|12
Requêtes par seconde|N/A|~ 3 par réplica|~ 15 par réplica|~ 60 par réplica|> 60 par réplica|> 60 par réplica

<sup>1</sup> gratuit, les références SKU Preview ne proviennent pas avec SLA. SLA est appliquées une fois qu’une référence SKU est généralement disponible.

<sup>2</sup> S3 et S3 HD bénéficient infrastructure identiques haute capacité mais chacun d'entre eux atteint la limite maximale de différentes manières. S3 cible un petit nombre d’index de très grande taille. Par conséquent, sa limite maximale est lié aux ressources (2,4 To pour chaque service). S3 HD cible un grand nombre d’index très petits. À 1 000 index, S3 HD atteint ses limites sous forme de contraintes d’index. Si vous êtes un client S3 HD qui requiert plus de 1 000 index, contactez le Support Microsoft pour plus d’informations sur la marche à suivre.

## <a name="eliminate-skus-that-dont-meet-requirements"></a>Éliminer les références SKU qui ne répond pas aux exigences 

Les questions suivantes peuvent vous aider à atteindrez la bonne décision de référence (SKU) de votre charge de travail.

1. Offrez-vous une configuration requise **(Contrat de niveau Service)** ? Affiner la décision de référence (SKU) de base ou Aperçu non Standard.
2. **Index combien** avez-vous besoin ? Un des principaux variables conception en une décision de référence (SKU) est le nombre d’index pris en charge par chaque référence (SKU). Prise en charge de l’index est sensiblement différentes niveaux dans les niveaux inférieurs tarification. Configuration requise pour le nombre d’index peut être une déterminant primaire d’une décision de référence (SKU).
3. **Le nombre de documents** est chargé dans chaque index ? Le nombre et la taille des documents déterminera la taille éventuelle de l’index. En supposant que vous pouvez estimer la taille prévue de l’index, vous pouvez comparer ce numéro par rapport à la taille de partition par référence (SKU), étendu par le nombre de partitions requis pour stocker un index de cette taille. 
4. **Quelle est la charge de requête attendu**? Une fois les besoins en stockage sont compris, envisagez de charges de travail de requête. S2 et les deux références SKU S3 proposent près équivalent débit, mais exigences SLA seront écarter les références SKU (version préliminaire). 
5. Si vous envisagez d’utiliser la couche S2 ou S3, déterminez si vous avez besoin [d’indexeurs](search-indexer-overview.md). Indexeurs ne sont pas encore disponibles pour la couche HD S3. Autre solution consiste à utiliser un modèle d’émission des mises à jour de l’index, où vous écrivez du code d’application pour envoyer une série de données pour un index.

Une référence SKU spécifique ou l’arrière de la plupart des clients pouvez règle en fonction de leurs réponses aux questions ci-dessus. Si vous toujours ne savez pas quel référence (SKU) pour accéder à, vous pouvez publier des questions sur les forums MSDN ou StackOverflow ou contacter le support technique Azure pour obtenir des instructions.

## <a name="decision-validation-does-the-sku-offer-sufficient-storage-and-qps"></a>Décision de validation : la référence SKU offre un espace suffisant et QPS ?

La dernière étape, revoir les [tarifs de page](https://azure.microsoft.com/pricing/details/search/) et les [sections par service et par index dans les limites de Service](search-limits-quotas-capacity.md) pour vérifiez vos estimations sur les limites de service et d’abonnement. 

Si le prix ou le stockage exigences sont en dehors des limites, vous souhaiterez peut-être refactoriser les charges de travail entre plusieurs services de plus petite taille (par exemple). Au niveau de la plus granulaires, vous pourriez recréez index pour être plus petit, ou utilisez les filtres pour améliorer l’efficacité des requêtes.

> [AZURE.NOTE] Besoins de stockage peuvent être augmentées excessive si documents contiennent des données superflues. Dans l’idéal, documents contiennent uniquement les données que vous pourrez recherches ou les métadonnées. Données binaires sont non interrogeables et doivent être stockées séparément (peut-être dans un stockage de table ou blob Azure) avec un champ dans l’index pour stocker une référence URL aux données externes. La taille maximale d’un document individuel est 16 Mo (ou moins se que vous êtes en bloc télécharger plusieurs documents dans une requête). Pour plus d’informations, voir [limites de Service de recherche Azure](search-limits-quotas-capacity.md) .

## <a name="next-step"></a>Étape suivante

Une fois que vous savez qui référence (SKU) est la meilleure solution, poursuivez comme suit :

- [Créer un service de recherche dans le portail](search-create-service-portal.md)
- [Modifier la répartition des partitions et duplications à l’échelle de votre service](search-capacity-planning.md)


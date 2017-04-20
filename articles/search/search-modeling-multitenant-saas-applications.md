<properties
    pageTitle="Modélisation donc le partage dans recherche Azure | Microsoft Azure | Service de recherche cloud hébergé"
    description="En savoir plus sur les modèles de conception courants pour les applications SaaS partagées lors de l’utilisation de recherche Azure."
    services="search"
    manager="jhubbard"
    authors="ashmaka"
    documentationCenter=""/>

<tags
    ms.service="search"
    ms.devlang="NA"
    ms.workload="search"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.date="10/26/2016"
    ms.author="ashmaka"/>

# <a name="design-patterns-for-multitenant-saas-applications-and-azure-search"></a>Concevoir des modèles pour les applications SaaS partagées et recherche Azure

Une application partagée est celle qui fournit les mêmes services et fonctionnalités à un nombre quelconque de clients qui ne peuvent pas voir ou partager les données de n’importe quel autre client. Ce document aborde les stratégies d’isolement client pour les applications partagées créées avec la recherche Azure.

## <a name="azure-search-concepts"></a>Concepts de recherche Azure
Une solution de recherche sous forme de service, recherche Azure permet aux développeurs ajouter des expériences recherche riche aux applications sans gestion de n’importe quelle infrastructure ou devenir un expert dans Rechercher. Données sont téléchargées sur le service et ensuite stockées dans le cloud. À l’aide de requêtes simples à l’API de recherche Azure, les données peuvent être modifiées et recherchées. Vous trouverez une vue d’ensemble du service dans [cet article](http://aka.ms/whatisazsearch). Avant d’aborder des modèles de conception, il est important de comprendre quelques concepts dans Azure recherche.

### <a name="search-services-indexes-fields-and-documents"></a>Services de recherche, des index, des champs et des documents
Lorsque vous utilisez recherche Azure, un abonne à un _service de recherche_. Comme les données à télécharger vers recherche Azure, il est stocké dans un _index_ dans le service de recherche. Il peut être un nombre d’index au sein d’un service unique. Pour utiliser les concepts familiers de bases de données, le service de recherche peut être comparé à une base de données tandis que les index au sein d’un service peuvent être comparées à des tables dans une base de données.

Chaque index au sein d’un service de recherche possède son propre schéma, qui est défini par un nombre de _champs_de personnalisable. Données sont ajoutées à un index de recherche Azure sous la forme de _documents_. Chaque document doit être téléchargé à un index particulier et doit tenir schéma de cet index. Lorsque vous recherchez des données à l’aide de la recherche Azure, les requêtes de recherche en texte intégral sont exécutées sur un index particulier.  Pour comparer ces concepts à celles d’une base de données, champs peuvent être comparées à des colonnes dans un tableau et documents peuvent être comparées à des lignes.

### <a name="scalability"></a>Extensibilité élevées
N’importe quel service de recherche Azure dans le Standard [tarifs couche](https://azure.microsoft.com/pricing/details/search/) peut mettre à l’échelle à deux dimensions : stockage et disponibilité.
* _Partitions_ peuvent être ajoutés pour augmenter l’espace de stockage d’un service de recherche.
* _Doubles_ peuvent être ajoutés à un service pour augmenter le débit des requêtes un service de recherche peut gérer.

Ajout et suppression des partitions et duplications au permettra la capacité du service de recherche pour agrandir avec la quantité de données et les demandes d’application de trafic. Dans l’ordre d’un service de recherche obtenir une lecture [SLA](https://azure.microsoft.com/support/legal/sla/search/v1_0/), il nécessite deux réplicas. Dans l’ordre d’un service obtenir une en lecture / écriture [SLA](https://azure.microsoft.com/support/legal/sla/search/v1_0/), il nécessite trois répliques.


### <a name="service-and-index-limits-in-azure-search"></a>Limites de service et index de recherche Azure
Il existe quelques différents [niveaux de tarification](https://azure.microsoft.com/pricing/details/search/) dans Azure recherche, chacun des niveaux avec différentes [limites et les quotas](search-limits-quotas-capacity.md). Certaines de ces limites sont au niveau du service, certaines sont au niveau de l’index et certains sont au niveau de la partition.


|                                  | Base     | Standard1   | Standard2   | Standard3   | HD Standard3  |
|----------------------------------|-----------|-------------|-------------|-------------|---------------|
| Duplications maximales par Service     | 3         | 12          | 12          | 12          | 12            |
| Partitions maximales par Service   | 1         | 12          | 12          | 12          | 1             |
| Unités de recherche maximale (réplicas * Partitions) par le Service | 3         | 36          | 36          | 36          | 36 (max 3 partitions)            |
| Maximum de Documents par Service    | 1 million | millions de 180 | 720 millions | 1.4 milliards | 600 millions   |
| Stockage maximum par Service      | 2 GO      | 300 GO      | 1.2 TO      | 2.4 TO      | 600 GO        |
| Maximum de Documents par Partition  | 1 million | 15 millions  | 60 millions  | 120 millions | 200 millions   |
| Stockage maximum par Partition    | 2 GO      | 25 GO       | 100 GO      | 200 GO      | 200 GO        |
| Index maximales par Service      | 5         | 50          | 200         | 200         | 3000 (max 1000 index/partition)          |


#### <a name="s3-high-density"></a>S3 Haute densité »
Dans le niveau de tarification de la recherche Azure S3, il existe une option pour le mode haute densité (HD) conçue spécifiquement pour les scénarios partagés. Dans de nombreux cas, il est nécessaire prendre en charge un grand nombre de clients plus petits sous un seul service pour obtenir les avantages de la simplicité et la rentabilité.

S3 HD autorise les nombreux index petites être pleine sous la gestion d’un service de recherche unique par vos commerciaux la fonctionnalité permettant d’évoluer index à l’aide des partitions pour avoir la possibilité d’héberger plusieurs index dans un seul service.

Concrètement, un service S3 pourrait avoir entre 1 et 200 index qui ensemble peuvent héberger jusqu'à 1.4 milliards de documents. Un HD S3 quant à eux permet des index individuels accéder uniquement jusqu'à 1 million de documents, mais elle peut gérer jusqu'à 1000 index par partition (jusqu'à 3000 par service) avec un nombre total de document de 200 millions par partition (jusqu'à 600 millions par service).



## <a name="considerations-for-multitenant-applications"></a>Considérations relatives aux applications partagées
Applications partagées doivent répartir efficacement les ressources entre les clients tout en conservant un niveau de confidentialité entre les clients différents. Il existe quelques considérations lors de la conception de l’architecture pour ce type d’application :

* _Isolement du client :_ Les développeurs applications ont besoin de prendre des mesures appropriées pour garantir que les non initiés ont non autorisé ou indésirables d’accès aux données d’autres clients. Au-delà de la perspective de confidentialité des données, client isolement stratégies nécessitent une gestion efficace de ressources partagées et de protection contre les bruits voisin.
* _Coût des ressources en nuage :_ Comme avec toute autre application, les solutions logicielles doivent rester coût concurrentielle en tant que composant d’une application partagé.
* _Simplifie les opérations de :_ Lorsque vous développez une architecture partagée, l’impact sur les opérations et la complexité de l’application est un facteur important. Recherche Azure a un [SLA 99,9 %](https://azure.microsoft.com/support/legal/sla/search/v1_0/).
* _Envergure internationale :_ Applications partagées peuvent nécessiter traiter efficacement les clients qui sont distribuées dans le monde.
* _Extensibilité élevées :_ Les développeurs d’applications doivent prendre en considération leur rapprochement entre maintenir un niveau suffisamment faible de la complexité des applications et la conception de l’application à l’échelle avec le nombre de clients et la taille des données et la charge de travail des clients.

Recherche Azure offre quelques limites qui peuvent servir à isoler les données des clients et la charge de travail.

## <a name="modeling-multitenancy-with-azure-search"></a>Modélisation donc le partage avec la recherche Azure
Dans le cas d’un scénario partagé, développeur de l’application consomme un ou plusieurs services de recherche et division de leurs clients entre les services, index ou les deux. Recherche Azure comporte quelques modèles courants lors de la modélisation un scénario partagé :

1. _Index par client :_ Chaque client possède son propre index dans un service de recherche est partagé avec d’autres clients.
1. _Service par client :_ Chaque client possède son propre service de recherche Azure dédié, offrant plus haut niveau de séparation des données et la charge de travail.
1. _Mélange des deux :_ Plus grandes et plus actives clients affectés services dédiés tandis que les clients plus petits sont affectés index individuels dans les services partagés.

## <a name="1-index-per-tenant"></a>1. à indexer par client
![Une image du modèle d’index par client](./media/search-modeling-multitenant-saas-applications/azure-search-index-per-tenant.png)

Dans un modèle d’index par client, plusieurs clients occupent un service de recherche Azure unique où chaque client possède son propre index.

Clients isoler les données, car toutes les requêtes de recherche et opérations document émises au niveau de l’index de recherche Azure. Dans la couche d’application, vous trouverez la sensibilisation qu’il soit nécessaire pour diriger le trafic des clients différents pour les index corrects lors également Gestion des ressources au niveau du service sur toutes les installations.

Un attribut essentiel du modèle d’index par client est la possibilité pour le développeur de l’application à surabonner la capacité d’un service de recherche entre les clients de l’application. Si les clients ont une distribution irrégulière de charge de travail, la combinaison optimale de clients peut être distribuée sur à partir d’index d’un service de recherche pour s’adapter à un certain nombre de clients hautement actives, beaucoup de ressources lors du traitement d’une longue traîne de clients moins actifs simultanément. Le compromis est l’incapacité du modèle de traiter les situations dans lesquelles chaque client est simultanément très actif.

Le modèle d’index par client fournit la base d’un modèle des coûts variables, qui ont un service de recherche Azure entière est acheté tout se déroule correctement et complété par la suite de clients. Cela permet de capacité inutilisée à désigner pour les comptes gratuits et essais.

Pour les applications d’envergure internationale, le modèle d’index par client peut ne pas être le plus efficace. Si les clients d’une application sont distribuées dans le monde, un service distinct peut être nécessaire pour chaque région qui peut dupliquer des coûts sur chacun d’eux.

Recherche Azure permet à l’échelle de l’index individuels et le nombre total d’index d’agrandir. Si un prix appropriés couche est choisie, partitions et duplications peuvent être ajoutées au service de recherche entière lorsqu’un index individuel au sein du service devient trop volumineux en termes de stockage ou le trafic.

Si le nombre total d’index devient trop volumineux pour un seul service, un autre service doit être configuré pour prendre en compte les nouveaux clients. Si les index que vous déplacer entre les services de recherche lorsque des nouveaux services sont ajoutés, les données à partir de l’index doit être manuellement copiée à partir d’un index à l’autre comme Azure recherche ne permet pas d’un index à déplacer.


## <a name="2-service-per-tenant"></a>2. Service par client
![Une image du modèle de service par client](./media/search-modeling-multitenant-saas-applications/azure-search-service-per-tenant.png)

Dans une architecture de service par client, chaque client possède son propre service de recherche.

Dans ce modèle, l’application permet d’obtenir le niveau d’isolement pour ses installations maximal. Chaque service a dédié stockage et le débit de demande de recherche, ainsi que des clés de l’API séparées.

Pour les applications où chaque client comporte un encombrement de grande taille ou la charge de travail a peu de variabilité à partir d’un client pour un client, le modèle de service par client est un choix efficace comme ressources ne sont pas partagées entre les charges de travail des clients différents.

Un service par modèle client offre également l’avantage d’un modèle de coût fixe, prévisibles. Il n’existe aucune un investissement dans un service de l’intégralité de la recherche jusqu'à ce qu’un client pour le remplir, mais le coût par client est supérieur à un modèle d’index par client.

Le modèle de service par client est un choix efficace pour les applications d’envergure internationale. De clients géographiquement, il est facile d’avoir service de chaque client dans la région appropriée.

Les défis liés à ce modèle de mise à l’échelle surviennent lorsque des clients dépassent leur service. Recherche Azure ne prend pas actuellement en charge la mise à niveau le niveau de tarification d’un service de recherche, afin que toutes les données devra être copiés manuellement vers un nouveau service.

## <a name="3-mixing-both-models"></a>3. mélange de deux modèles
Un autre modèle de modélisation donc le partage est une combinaison de stratégies index par client et service par client.

Par mélange les deux modèles, clients plus grandes d’une application peuvent occuper dédiés services pendant la longue traîne de clients moins actifs, plus petits peut occuper index dans un service partagé. Ce modèle garantit que les clients plus grandes ont des performances élevées à partir du service tout en protégeant les clients plus petits à partir de n’importe quel voisin bruits.

Toutefois, la mise en œuvre cette stratégie repose prospective à prévoir les clients nécessite un service dédié par rapport à un index dans un service partagé. La complexité des applications augmente avec la nécessité de gérer les deux de ces modèles multitenancy.

## <a name="achieving-even-finer-granularity"></a>Atteindre le même précise
Les modèles de conception ci-dessus pour modéliser des scénarios partagés dans Azure recherche part du principe une étendue uniforme, où chaque client est une instance ensemble d’une application. Toutefois, les applications peuvent gérer parfois plusieurs zones plus petites.

Si les modèles de service par client et index par client ne sont pas suffisamment petites étendues, il est possible de modéliser un index pour atteindre un degré encore plu de précision.

Pour avoir un index unique se comportent différemment pour les points de terminaison clients différents, un champ peut être ajouté à un index afin de les désigner une certaine valeur pour chaque client possible. Chaque fois qu’un client appelle recherche Azure pour consulter ou modifier un index, le code de l’application cliente spécifie la valeur appropriée pour ce champ à l’aide de la fonction de [filtre](https://msdn.microsoft.com/library/azure/dn798921.aspx) de la recherche Azure au moment de la requête.

Cette méthode peut être utilisée pour obtenir une fonctionnalité de comptes d’utilisateurs distincts, niveaux d’autorisation distincts et même complètement séparer les applications.

## <a name="next-steps"></a>Étapes suivantes
Recherche Azure est un choix intéressant pour de nombreuses applications, [en savoir plus sur les fonctionnalités robuste du service](http://aka.ms/whatisazsearch). Lorsque vous évaluez les différents modèles de conception pour les applications partagées, vous pouvez les [divers niveaux de tarification](https://azure.microsoft.com/pricing/details/search/) et [limites de service](search-limits-quotas-capacity.md) pour adapter meilleures Azure la recherche pour afficher l’intégralité des charges de travail et des architectures de toutes tailles.

Des questions sur la recherche de Azure et scénarios partagés peuvent être dirigées vers azuresearch_contact@microsoft.com.

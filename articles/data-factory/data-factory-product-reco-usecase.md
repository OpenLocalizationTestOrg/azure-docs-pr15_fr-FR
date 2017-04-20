<properties 
    pageTitle="Cas d’utilisation des données usine - recommandations de produits" 
    description="En savoir plus sur un cas d’utilisation implémenté à l’aide d’Azure Data Factory ainsi que d’autres services." 
    services="data-factory" 
    documentationCenter="" 
    authors="sharonlo101" 
    manager="jhubbard" 
    editor="monicar"/>

<tags 
    ms.service="data-factory" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/01/2016" 
    ms.author="shlo"/>

# <a name="use-case---product-recommendations"></a>Utiliser la casse - recommandations de produits 

Azure Data Factory est un des nombreux services utilisés pour implémenter l’Intelligence Cortana Suite d’accélérateurs de solutions.  Voir [Cortana Intelligence Suite](http://www.microsoft.com/cortanaanalytics) page pour plus d’informations sur cette suite. Dans ce document, nous décrire un cas d’utilisation courants utilisateurs Azure déjà résolu et implémentée à l’aide d’Azure Data Factory et autres services de composant Intelligence Cortana.

## <a name="scenario"></a>Scénario

Magasins en ligne voulez fréquemment inciter leurs clients d’acheter des produits en les présentant aux produits qu’ils sont susceptibles de vous intéresser et par conséquent plus susceptibles d’acheter. Pour ce faire, magasins en ligne vous avez besoin personnaliser l’expérience utilisateur de leur utilisateur à l’aide personnalisée de produits recommandés pour un utilisateur spécifique. Ces recommandations personnalisées sont en fonction de leur en cours et historiques les données de comportement, informations sur le produit, marques nouvellement introduites et produit et segmentation des données client.  En outre, elles peuvent fournir les recommandations de produits utilisateur basées sur l’analyse du comportement de l’utilisation globale de tous ses utilisateurs combinées.

L’objectif de ces revendeurs consiste à optimiser pour les conversions de click-vente utilisateur et gagnez affaires supérieure.  Ils atteignent cette conversion en fournissant des recommandations produit contextuelle fondées sur des comportements basées sur les centres d’intérêt client et actions. Pour ce cas d’utilisation, nous utilisons magasins en ligne comme un exemple d’activités qui souhaitent optimiser pour leurs clients. Toutefois, ces principes s’appliquent à toute entreprise qui souhaite recours à une de ses clients autour de ses produits et services et d’améliorer l’expérience d’achat leurs clients avec personnalisée de produits recommandés.

## <a name="challenges"></a>Défis

Il existe a nombreux défis auxquels sont confrontées les magasins en ligne lorsque vous tentez de mettre en œuvre ce type de cas d’utilisation. 

Tout d’abord, les données de différentes tailles et formes doivent être transférées vers plusieurs sources de données, à la fois en local et dans le cloud. Ces données incluent les données de produit, les données de comportement de client historiques et données utilisateur lorsque l’utilisateur navigue sur le site de vente en ligne. 

Conseils relatifs aux produits personnalisé, deuxième doit être raisonnablement et précisément calculée et prédites. En plus de produit, marque et données comportement et le navigateur client, magasins en ligne doivent également inclure les commentaires des clients sur ses achats antérieurs à prendre en compte la détermination des meilleures recommandations produit pour l’utilisateur. 

Enfin, les recommandations doivent être immédiatement du livrable à l’utilisateur pour fournir une navigation transparente et achat expérience et fournir les recommandations les plus récentes et pertinentes. 

Enfin, revendeurs doivent mesurer l’efficacité de leur approche par le suivi de vente incitative globale croisées succès ventes conversion en un clic et ajuster à leurs recommandations futures.

## <a name="solution-overview"></a>Présentation de la solution

Cas d’utilisation de cet exemple a été résolu et implémenté par les utilisateurs réel Azure à l’aide de Azure Data Factory et autres services de composants Cortana Intelligence, y compris [HDInsight](https://azure.microsoft.com/services/hdinsight/) et [Power BI](https://powerbi.microsoft.com/).

Le détaillant en ligne utilise un magasin d’objets Blob Azure, un local SQL server, base de données SQL Azure et un Datamart relationnelle comme leurs options de stockage de données dans l’ensemble du flux de travail.  Le magasin d’objets blob contient des informations relatives aux clients, les données de comportement de client et données d’informations sur les produits. Les données d’informations produit comportant des informations de marque de produit et un produit de catalogue stockées localement dans un data warehouse SQL. 

Toutes les données sont combinées et transférées dans un système de recommandation de produit pour fournir des recommandations personnalisées basées sur les centres d’intérêt client et actions, tandis que l’utilisateur accède produits dans le catalogue sur le site Web. Les clients consultez également produits qui sont associés au produit qu’ils sont consultant basé sur des modèles d’utilisation globale de site Web qui ne sont pas liées à un utilisateur.

![diagramme de cas d’utilisation](./media/data-factory-product-reco-usecase/diagram-1.png)

Gigaoctets de fichiers journaux web brutes sont générés tous les jours à partir du site Web du détaillant en ligne en tant que fichiers semi-structurées. Les fichiers journaux web brute et les informations du catalogue clients et les produits est régulièrement intégré dans un stockage d’objets Blob Azure à l’aide de déplacement de données globalement déployé d’usine de données en tant que service. Les fichiers journaux brutes pour le jour sont partagés (par année et mois) de stockage blob de stockage à long terme.  [Azure HDInsight](https://azure.microsoft.com/services/hdinsight/) est utilisé pour les fichiers journaux brutes dans le magasin d’objets blob de partition et traiter les journaux ingérés à l’échelle à l’aide des scripts Hive et cochon. Le web partitionné les journaux de données est ensuite traitée pour extraire les entrées nécessaires pour une machine recommandation système pour générer les recommandations de produits personnalisées de la formation.

Le système de recommandation utilisé pour l’apprentissage dans cet exemple de l’ordinateur est une machine ouvrir la source plateforme de recommandation de [Apache Mahout](http://mahout.apache.org/)d’apprentissage.  Modèle personnalisé ni [Apprentissage automatique Azure](https://azure.microsoft.com/services/machine-learning/) peut être appliqué au scénario.  Le modèle Mahout est utilisé pour prévoir les similitudes entre les éléments sur le site Web basé sur des modèles d’utilisation générale et générer les recommandations personnalisées en fonction de l’utilisateur individuel.

Enfin, le jeu de résultats de conseils relatifs aux produits personnalisée est déplacé vers un Datamart relationnelle pour la consommation par le site Web revendeur.  Le jeu de résultats pourrait également être accessibles directement à partir de stockage d’objets blob par une autre application ou déplacé vers banques supplémentaires pour les autres consommateurs et exemples d’utilisation.

## <a name="benefits"></a>Avantages

En optimisant leur stratégie recommandation produit et alignement avec les objectifs, la solution satisfait promotions du détaillant en ligne et marketing objectifs. En outre, ils ont pu effectuent et de gérer le flux de travail de recommandation produit de manière efficace, fiable et économique. L’approche simplifié pour leur mise à jour de leur modèle et ajuster son efficacité basée sur les mesures de ventes succès de conversion en un clic. À l’aide de Azure Data Factory, ils ont pu abandonner leur gestion des ressources de cloud manuel beaucoup de temps et coûteux et accéder à la gestion des ressources à la demande cloud. Par conséquent, ils ont été en mesure de gagner du temps, argent et réduire le temps de déploiement de solution. Vues de lignage des données et l’état du service opérationnelles est devenu faciles à visualiser et résoudre les problèmes avec intuitive Data Factory surveiller et de gérer l’interface utilisateur disponible à partir du portail Azure. Leur solution peut désormais être planifiée et gérée afin que les données terminée sont fiable produites remies aux utilisateurs et des données et dépendances de traitement sont gérées automatiquement sans intervention humaine.

Grâce à cette expérience d’achat personnalisée, détaillant en ligne créé un client plus concurrence, attrayant rencontrer et par conséquent, augmenter la satisfaction client et globale.



  
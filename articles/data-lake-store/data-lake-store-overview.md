<properties
   pageTitle="Vue d’ensemble du magasin de Lake données Azure | Microsoft Azure"
   description="Comprendre les nouveautés Azure données Lake Store et la valeur fournie sur d’autres magasins de données"
   services="data-lake-store"
   documentationCenter=""
   authors="nitinme"
   manager="jhubbard"
   editor="cgronlun"/>

<tags
   ms.service="data-lake-store"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="10/28/2016"
   ms.author="nitinme"/>

# <a name="overview-of-azure-data-lake-store"></a>Vue d’ensemble Azure Lake de magasin de données

Azure Data Lake Store est un référentiel hyper-échelle échelle de l’entreprise pour les charges de travail analytique données volumineuses. Azure données Lake vous permet de capturer les données de n’importe quelle vitesse taille, le type et acquisition dans un emplacement unique pour opérationnelles et exploratoires analytique.

> [AZURE.TIP] Utiliser les [données Lake Store rubriques d’apprentissage](https://azure.microsoft.com/documentation/learning-paths/data-lake-store-self-guided-training/) pour commencer à Explorer le service banque de Lake données Azure.

Magasin de Lake données Azure sont accessibles à partir de Hadoop (disponible avec cluster HDInsight) à l’aide de l’API REST WebHDFS compatible. Il est conçu pour activer analytique sur les données stockées et est réglé pour performance pour des scénarios données analytique. Prêts à l’emploi, il inclut toutes les fonctionnalités d’échelle de l’entreprise : sécurité, gestion, extensibilité élevées, la fiabilité et disponibilité — essentiels dans les cas d’utilisation entreprise réel.


![Lake de données Azure](./media/data-lake-store-overview/data-lake-store-concept.png)

Voici quelques-unes des fonctionnalités clées de la Lake données Azure.

### <a name="built-for-hadoop"></a>Conçu pour Hadoop

Le magasin Lake de données Azure est un système de fichiers Apache Hadoop compatible avec le système de fichier distribué Hadoop (HDFS) et fonctionne avec un réseau Hadoop.  Vos applications HDInsight ou les services qui utilisent l’API WebHDFS existantes peuvent intégrer facilement avec données Lake Store. Données Lake Store expose également une interface reste WebHDFS compatible pour les applications

Données stockées dans données Lake Store peuvent être facilement analysées à l’aide de structures analytiques Hadoop comme MapReduce ou Hive. Microsoft Azure HDInsight clusters peuvent être mis en service et configurés pour accéder directement aux données stockées dans données Lake Store.

### <a name="unlimited-storage-petabyte-files"></a>Stockage illimité, fichiers pétaoctet

Magasin de Lake données Azure offre un stockage illimité et est approprié pour le stockage d’une variété de données pour analytique. Limites applicables à n’importe quel compte tailles, tailles de fichiers ou la quantité de données pouvant être stockés dans un lake données sans imposer. Fichiers individuels peuvent varier de kilo-octets à po taille rend avère très intéressante pour stocker tout type de données. Les données sont stockées durablement en effectuer plusieurs copies et n’est pas limité sur la durée pour laquelle les données peuvent être stockées dans le lake de données.

### <a name="performance-tuned-for-big-data-analytics"></a>Performances optimisées pour analytique de données volumineux

Magasin de Lake données Azure intégré pour l’exécution de grande échelle analytiques systèmes nécessitant massif débit pour interroger et analysez de grandes quantités de données. La lake de données double parties d’un fichier sur un nombre de serveurs de stockage individuels. Cela améliore le débit de lecture lors de la lecture du fichier en parallèle permettant d’effectuer analytique de données.


### <a name="enterprise-ready-highly-available-and-secure"></a>Prêt pour les entreprises : Hautement disponible et sécurisée

Magasin de Lake données Azure fournit la fiabilité et disponibilité des normes industrielles. Vos ressources de données sont stockés durablement en créant des copies redondantes pour vous protéger contre les échecs inattendus. Les entreprises peuvent servir Azure données Lake dans leurs solutions essentiel de leur plate-forme de données existante.

Données Lake Store fournit également une échelle de l’entreprise sécurité pour les données stockées. Pour plus d’informations, voir [sécurisation des données Azure données Lake Store](#DataLakeStoreSecurity).


### <a name="all-data"></a>Toutes les données

Magasin de Lake données Azure peuvent stocker des données dans son format d’origine, en l’état, sans avoir besoin de transformations préalables. Données Lake Store ne nécessite pas un schéma à être définie avant que les données sont chargées, de quitter jusqu'à l’infrastructure analytique individuel pour interpréter les données et définir un schéma au moment de l’analyse. La possibilité stocker des fichiers de tailles arbitraires et les formats rend possible pour données Lake Store à gérer des données structurées, semi-structurées et non structurées.

Conteneurs Azure données Lake Store pour les données sont essentiellement les dossiers et fichiers. Vous travaillez sur les données stockées à l’aide de Powershell Azure SDK et Azure portail. Dans la mesure où vous stockez vos données dans le magasin de l’utilisation de ces interfaces et les conteneurs appropriés, vous pouvez stocker n’importe quel type de données. Données Lake Store n’effectue aucun traitement spécial des données en fonction du type de données qu’elle contient.


## <a name="DataLakeStoreSecurity"></a>Protection des données Azure données Lake Store

Magasin de Lake données Azure utilise Azure Active Directory pour l’authentification et contrôle d’accès des listes pour gérer l’accès à vos données.

| Fonctionnalité                                 | Description                              |
|-----------------------------------------|------------------------------------------|
| Authentification | Magasin de Lake données Azure s’intègre avec Azure Active Directory (DAS) pour la gestion des identités et des accès pour toutes les données stockées dans Azure données Lake sécurisée. Grâce à l’intégration, avantages Lake de données Azure à partir de toutes les fonctionnalités AAD dont l’authentification multifacteur, accès conditionnel, le contrôle d’accès basé sur un rôle, contrôle de l’utilisation des applications de sécurité de surveillance et d’alerte, etc. Magasin de Lake données Azure prend en charge le protocole OAuth 2.0 pour l’authentification avec dans l’interface REST. |
| Contrôle d’accès                          | Magasin de Lake données Azure fournit un contrôle d’accès en prenant en charge autorisations POSIX-style exposées par le protocole WebHDFS. Dans la version actuelle, les utilisateurs peuvent être activés sur le dossier racine, les sous-dossiers, ainsi que les fichiers individuels. Les utilisateurs que vous appliquez au dossier racine seront également applicables à tous les enfants dossiers/fichiers également.|

Vous voulez en savoir plus sur la sécurisation de données dans les données Lake Store. Suivez les liens ci-dessous.

* Pour obtenir des instructions sur la façon de sécuriser les données dans les données Lake Store, voir [sécurisation des données Azure données Lake Store](data-lake-store-secure-data.md).
* Vous préférez vidéos ? [Regardez cette vidéo](https://mix.office.com/watch/1q2mgzh9nn5lx) sur la façon de sécuriser les données stockées dans le magasin de Lake de données.

## <a name="applications-compatible-with-azure-data-lake-store"></a>Applications compatibles avec Azure données Lake Store

Azure Data Lake Store est compatible avec les composants source plus ouverts dans le réseau Hadoop. Il est également façon optimisée avec d’autres services Azure. Données Lake Store fait désormais une option qui vous convient à vos besoins de stockage de données. Suivez les liens ci-dessous pour en savoir plus sur comment les données Lake Store peut être utilisé à la fois avec les composants d’ouvrir la source, ainsi que d’autres services Azure.

* Voir les [Applications et services compatibles avec Azure données Lake Store](data-lake-store-compatible-oss-other-applications.md) pour obtenir la liste des applications libres interopérabilitées avec les données Lake Store.
* Consultez l' [intégration avec d’autres services Azure](data-lake-store-integrate-with-other-services.md) pour mieux comprendre comment les données Lake magasin peut être utilisé avec d’autres services Azure pour activer un large éventail de scénarios.
* Voir [scénarios d’utilisation des données Lake Store](data-lake-store-data-scenarios.md) pour apprendre à utiliser les données Lake Store dans des scénarios tels que traiter les données de traitement des données, le téléchargement des données et visualisation des données.

## <a name="what-is-azure-data-lake-store-file-system-adl"></a>Quel est le système de fichiers Azure données Lake Store (adl : / /) ?

Données Lake Store sont accessibles via le nouveau système de fichiers, la AzureDataLakeFilesystem (adl : / /), dans les environnements Hadoop (disponibles avec cluster HDInsight). Applications et services qui utilisent adl : / / sont en mesure de tirer parti de davantage l’optimisation des performances qui ne sont pas disponibles dans WebHDFS. Par conséquent, données Lake Store vous offre la possibilité soit bénéficier les meilleures performances avec l’option d’utilisation adl recommandée : / / ou mettre à jour le code existant en continuant à utiliser l’API WebHDFS directement. Azure HDInsight exploite entièrement la AzureDataLakeFilesystem afin d’améliorer les performances sur magasin Lake de données.

Vous pouvez accéder à vos données dans le magasin de Lake de données à l’aide `adl://<data_lake_store_name>.azuredatalakestore.net`. Pour plus d’informations sur la façon d’accéder aux données du magasin de données Lake, voir [Afficher les propriétés des données stockées](data-lake-store-get-started-portal.md#properties)

## <a name="how-do-i-start-using-azure-data-lake-store"></a>Comment démarrer à l’aide de magasin de Lake données Azure ?

Voir [prise en main du Store Lake de données à l’aide du portail Azure](data-lake-store-get-started-portal.md), sur la mise en service d’un magasin Lake de données à l’aide du portail Azure. Une fois que vous avez configuré Lake de données Azure, vous pouvez apprendre à utiliser les offres de données volumineux comme Azure données Lake Analytique ou Azure HDInsight avec données Lake Store. Vous pouvez également créer une application .NET pour créer un compte Azure données Lake Store et effectuer les opérations, telles que les données de téléchargement, télécharger des données, etc..

- [Prise en main des données Azure Lake Analytique](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
- [Utiliser Azure HDInsight avec données Lake Store](data-lake-store-hdinsight-hadoop-use-portal.md)
- [Prise en main Azure données Lake Store à l’aide du Kit de développement .NET](data-lake-store-get-started-net-sdk.md)


## <a name="data-lake-store-videos"></a>Vidéos de données Lake Store

Si vous préférez visionner des vidéos pour en savoir plus, données Lake Store fournit des vidéos sur une plage de fonctionnalités.

* [Créer un compte Azure données Lake Store](https://mix.office.com/watch/1k1cycy4l4gen)
* [Utilisez l’Explorateur de données pour gérer les données dans le magasin de Lake données Azure](https://mix.office.com/watch/icletrxrh6pc)
* [Connecter des données Azure Lake Analytique au magasin Lake des données Azure](https://mix.office.com/watch/qwji0dc9rx9k)
* [Accès Azure données Lake Store via données Lake Analytique](https://mix.office.com/watch/1n0s45up381a8)
* [Se connecter Azure HDInsight au magasin Lake des données Azure](https://mix.office.com/watch/l93xri2yhtp2)
* [Accès Azure données Lake Store via Hive et cochon](https://mix.office.com/watch/1n9g5w0fiqv1q)
* [Permet de copier des données vers et depuis Azure données Lake Store DistCp (Hadoop Distributed copie)](https://mix.office.com/watch/1liuojvdx6sie)
* [Permet de déplacer des données entre sources relationnelles et Azure Data Lake Store Sqoop Apache](https://mix.office.com/watch/1butcdjxmu114)
* [Orchestration de données à l’aide d’Azure Data Factory pour Azure données Lake Store](https://mix.office.com/watch/1oa7le7t2u4ka)
* [Protection des données dans le magasin de Lake données Azure](https://mix.office.com/watch/1q2mgzh9nn5lx)




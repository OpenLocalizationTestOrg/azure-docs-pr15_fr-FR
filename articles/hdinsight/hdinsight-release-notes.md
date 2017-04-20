<properties
    pageTitle="Notes de publication pour composants Hadoop sur Azure HDInsight | Microsoft Azure"
    description="Dernières notes de publication et les versions des composants Hadoop pour Azure HDInsight. Obtenir des conseils de développement et détails pour Hadoop, vague d’Apache et HBase."
    services="hdinsight"
    documentationCenter=""
    editor="cgronlun"
    manager="jhubbard"
    authors="nitinme"
    tags="azure-portal"/>

<tags
    ms.service="hdinsight"
    ms.workload="big-data"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/27/2016"
    ms.author="nitinme"/>


# <a name="release-notes-for-hadoop-components-on-azure-hdinsight"></a>Notes de publication pour les composants Hadoop sur Azure HDInsight

## <a name="notes-for-10262016-release-of-r-server-on-hdinsight"></a>Notes de version 26/10/2016 du serveur R sur HDInsight

- Serveur R sur la configuration de cluster HDInsight a été rationalisée.
- R Server sur HDInsight est désormais disponible en tant que HDInsight normal « R Server » cluster type et n’est plus installé comme application HDInsight séparée. Le nœud de bord et les fichiers binaires du serveur R sont désormais mis en service dans le cadre du déploiement cluster Server R. Cela améliore la vitesse et la fiabilité de mise en service. Modèle de tarification pour serveur R est mis à jour en conséquence.
- Prix de type cluster serveur R est désormais basée sur prix couche Standard plus prix de surcharge serveur R. Niveau Premium est désormais réservée aux fonctionnalités Premium disponibles parmi les différents types de cluster différent et ne sera pas utilisé pour le type de cluster serveur R. Cette modification n’affecte pas tarifs efficace du serveur R, il modifie uniquement la façon dont les frais sont présentés dans la facture. Tous les clusters serveur R existants continueront à fonctionner et modèles processeur continueront à fonctionner jusqu'à avis amortissement. **Il est recommandé pour mettre à jour vos déploiements par script pour utiliser le nouveau modèle de processeur.**


## <a name="notes-for-08302016-release-of-r-server-on-hdinsight"></a>Notes 30/08/2016 version du serveur R sur HDInsight

Les numéros de version complète des clusters HDInsight basé sur Linux déployés avec cette version :

|HDI |Version de cluster HDI   |HDP |HDP génération   |Génération de Ambari |
|----|----------------------|----|------------|-------------|
|3.2 |3.2.1000.0.8268980    |2.2 |2.2.9.1-19  |2.2.1.12-4   |
|3.3 |3.3.1000.0.8268980    |2.3 |2.3.3.1-25  |2.2.1.12-4   |
|3.4 |3.4.1000.0.8269383    |2.4 |2.4.2.4-5   |2.2.1.12-4   |

Les numéros de version complète des clusters HDInsight fonctionnant sous Windows déployés avec cette version :

|HDI |Version de cluster HDI   |HDP |HDP génération     |
|----|----------------------|----|--------------|
|2.1 |2.1.10.1033.2559206   |1.3 |1.3.12.0-01795|
|3.0 |3.0.6.1033.2559206    |2.0 |2.0.13.0-2117 |
|3.1 |3.1.4.1033.2559206    |2.1 |2.1.16.0-2374 |
|3.2 |3.2.7.1033.2559206    |2.2 |2.2.9.1-11    |
|3.3 |3.3.0.1033.2559206    |2.3 |2.3.3.1-25    |

## <a name="notes-for-08172016-release-of-r-server-on-hdinsight"></a>Notes 17/08/2016 version du serveur R sur HDInsight

- R Server 8.0.5 – principalement une version de résolution du bogue. Consultez les [Notes de publication R Server](https://msdn.microsoft.com/microsoft-r/notes/r-server-notes) pour plus d’informations. 
- Package AzureML sur le nœud de bord – [ce package R](https://cran.r-project.org/web/packages/AzureML/vignettes/getting_started.html) permet de modèles R sont publiées et consommées dans un service web ML Azure.  Consultez la section [« effectuent d’un modèle »](hdinsight-hadoop-r-server-overview.md#operationalize-a-model) de notre article [« vue d’ensemble de R Server sur HDInsight »](hdinsight-hadoop-r-server-overview.md) pour plus d’informations.
- Dépendances Linux [supérieur packages R les plus populaires 100](https://github.com/metacran/cranlogs) – ces Linux dépendances du package sont désormais préinstallés.  
- Option pour utiliser le mis en pension CRAN lors de l’ajout de R packages les nœuds de données. Consultez la section [« Installer R packages »](hdinsight-hadoop-r-server-get-started.md#install-r-packages) de notre article [« Commencer à utiliser serveur R sur HDInsight »](hdinsight-hadoop-r-server-get-started.md) pour plus d’informations.
- Améliorer la fiabilité de serveur R mise en service lors de la création de clusters.


## <a name="notes-for-08012016-release-of-hdinsight"></a>Notes d’HDInsight de virtualisation 08/01/2016

Les numéros de version complète des clusters HDInsight basé sur Linux déployés avec cette version :

|HDI |Version de cluster HDI   |HDP |HDP génération   |Génération de Ambari |
|----|----------------------|----|------------|-------------|
|3.2 |3.2.1000.0.8028416    |2.2 |2.2.9.1-19  |2.2.1.12-4   |
|3.3 |3.3.1000.0.8028416    |2.3 |2.3.3.1-25  |2.2.1.12-4   |
|3.4 |3.4.1000.0.8053402    |2.4 |2.4.2.4-5   |2.2.1.12-4   |

Les numéros de version complète des clusters HDInsight fonctionnant sous Windows déployés avec cette version :

|HDI |Version de cluster HDI   |HDP |HDP génération     |
|----|----------------------|----|--------------|
|2.1 |2.1.10.1005.2488842   |1.3 |1.3.12.0-01795|
|3.0 |3.0.6.1005.2488842    |2.0 |2.0.13.0-2117 |
|3.1 |3.1.4.1005.2488842    |2.1 |2.1.16.0-2374 |
|3.2 |3.2.7.1005.2488842    |2.2 |2.2.9.1-11    |
|3.3 |3.3.0.1005.2488842    |2.3 |2.3.3.1-25    |

Cette version contient les mises à jour suivants.

| Titre                                           | Description                                          | Zone concerné (par exemple, Service, composant ou SDK) | Type de cluster (par exemple explosion, Hadoop, HBase ou vague de) | JIRA (le cas échéant) |
|-------------------------------------------------|------------------------------------------------------|---------------------------------------------------------|-----------------------------------------------------|----------------------|
| Modifications apportées aux clusters 3.4 HDInsight | La valeur par défaut pour les configurations hive suivantes sont modifiées pour de meilleures performances <ul><li>`hive.vectorized.execution.reduce.enabled=true`</li><li>`hive.tez.min.partition.factor=1f`</li><li>`hive.tez.max.partition.factor=3f`</li><li>`tez.shuffle-vertex-manager.min-src-fraction=0.9`</li><li>`tez.shuffle-vertex-manager.max-src-fraction=0.95`</li><li>`tez.runtime.shuffle.connect.timeout= 30000`</li></ul>| Service    | Tous les| N/A|
| Correctifs suivants sont inclus dans cette version | HIVE 13632, HIVE-12897,HIVE-12907,HIVE-12908,HIVE-12988,HIVE-13510,HIVE-13572,HIVE-13716,HIVE-13726,HIVE-12505,HIVE-13632,HIVE-13661,HIVE-13705,HIVE-13743,HIVE-13810,HIVE-13857,HIVE-13902,HIVE-13911,HIVE-13933| Service    | Tous les| N/A

## <a name="notes-for-07142016-release-of-hdinsight"></a>Notes d’HDInsight de virtualisation 14/07/2016

Les numéros de version complète des clusters HDInsight basé sur Linux déployés avec cette version :

|HDI |Version de cluster HDI   |HDP |HDP génération   |Génération de Ambari |
|----|----------------------|----|------------|-------------|
|3.2 |3.2.1000.0.7932505    |2.2 |2.2.9.1-11  |2.2.1.12-2   |
|3.3 |3.3.1000.0.7932505    |2.3 |2.3.3.1-18  |2.2.1.12-2   |
|3.4 |3.4.1000.0.7933003    |2.4 |2.4.2.0     |2.2.1.12-2   |

Les numéros de version complète des clusters HDInsight fonctionnant sous Windows déployés avec cette version :

|HDI |Version de cluster HDI   |HDP |HDP génération     |
|----|----------------------|----|--------------|
|2.1 |2.1.10.989.2441725    |1.3 |1.3.12.0-01795|
|3.0 |3.0.6.989.2441725     |2.0 |2.0.13.0-2117 |
|3.1 |3.1.4.989.2441725     |2.1 |2.1.16.0-2374 |
|3.2 |3.2.7.989.2441725     |2.2 |2.2.9.1-11    |
|3.3 |3.3.0.989.2441725     |2.3 |2.3.3.1-21    |

## <a name="notes-for-07072016-release-of-hdinsight"></a>Notes d’HDInsight de virtualisation 07/07/2016

Les numéros de version complète des clusters HDInsight basé sur Linux déployés avec cette version :

|HDI |Version de cluster HDI   |HDP |HDP génération   |
|----|----------------------|----|------------|
|3.2 |3.2.1000.0.7864996    |2.2 |2.2.9.1-11  |
|3.3 |3.3.1000.0.7864996    |2.3 |2.3.3.1-18  |
|3.4 |3.4.1000.0.7861906    |2.4 |2.4.2.0     |

Les numéros de version complète des clusters HDInsight fonctionnant sous Windows déployés avec cette version :

|HDI |Version de cluster HDI   |HDP |HDP génération     |
|----|----------------------|----|--------------|
|2.1 |2.1.10.977.2413853    |1.3 |1.3.12.0-01795|
|3.0 |3.0.6.977.2413853     |2.0 |2.0.13.0-2117 |
|3.1 |3.1.4.977.2413853     |2.1 |2.1.16.0-2374 |
|3.2 |3.2.7.977.2413853     |2.2 |2.2.9.1-11    |
|3.3 |3.3.0.977.2413853     |2.3 |2.3.3.1-21    |

Cette version contient les mises à jour suivants.

| Titre                                           | Description                                          | Zone concerné (par exemple, Service, composant ou SDK) | Type de cluster (par exemple explosion, Hadoop, HBase ou vague de) | JIRA (le cas échéant) |
|-------------------------------------------------|------------------------------------------------------|---------------------------------------------------------|-----------------------------------------------------|----------------------|
| [Outils de HDInsight pour IntelliJ](hdinsight-apache-spark-intellij-tool-plugin.md) | Plug-in IntelliJ idée pour les clusters HDInsight Spark est maintenant intégré à Azure le Kit de ressources pour IntelliJ. Il prend en charge Azure SDK v2.9.1, dernière Java SDK et inclut toutes les fonctionnalités de la version autonome de HDInsight Plugin pour IntelliJ.| Outils    | Explosion| N/A|
| [Outils HDInsight pour Éclipse](hdinsight-apache-spark-eclipse-tool-plugin.md) | Kit de ressources Azure pour Eclipse prend désormais en charge les clusters HDInsight Spark. Il active les fonctionnalités suivantes. <ul><li>Créer et d’écrire une application explosion facilement dans Scala et Java avec première classe prise en charge IntelliSense, mise en forme automatique, la vérification des erreurs, etc. de création.</li><li>Testez l’application explosion localement.</li><li>Soumettre les tâches au cluster HDInsight Spark et récupérer les résultats.</li><li>Connectez-vous à Azure et accéder à tous les groupes d’explosion associés à vos abonnements Azure.</li><li>Accédez à toutes les ressources de stockage associée de votre cluster HDInsight Spark.</li></ul>| Outils    | Explosion| N/A

À partir de cette version, nous avons modifié la stratégie de correction du système d’exploitation invité pour les clusters basés sur Linux HDInsight. L’objectif de la nouvelle stratégie consiste à réduire le nombre de redémarrage en raison de la correction de manière significative. La nouvelle stratégie continuent à machines virtuelles correctif (machines virtuelles) sur Linux clusters chaque lundi ou un jeudi commençant à 12 : 00 UTC de manière décalé sur les nœuds dans un cluster donné. Toutefois, n’importe quel ordinateur virtuel donné redémarre uniquement au maximum une fois tous les 30 jours en raison de correctifs de système d’exploitation invité. En outre, le premier redémarrage pour un cluster nouvellement créé se produit pas plus tôt que 30 jours après la date de création de cluster.

>[AZURE.NOTE] Ces modifications s’appliquent uniquement aux clusters nouvellement créés ou égale à cette version.

## <a name="notes-for-06062016-release-of-hdinsight"></a>Notes d’HDInsight de virtualisation 06/06/2016

Les numéros de version complète des clusters HDInsight déployés avec cette version :

|HDP    |Version HDI    |Version d’explosion  |Numéro de version Ambari    |Numéro de version HDP|
|-------|---------------|---------------|-----------------------|----------------|
|2.3    |3.3.1000.0.7702215|    1.5.2|  2.2.1.8-2|  2.3.3.1-18|
|2.4    |3.4.1000.0.7702224|    1.6.1|  2.2.1.8-2|  2.4.2.0|


Cette version contient les mises à jour suivants.

| Titre                                           | Description                                          | Zone concerné (par exemple, Service, composant ou SDK) | Type de cluster (par exemple explosion, Hadoop, HBase ou vague de) | JIRA (le cas échéant) |
|-------------------------------------------------|------------------------------------------------------|---------------------------------------------------------|-----------------------------------------------------|----------------------|
| Explosion sur HDInsight est généralement disponible | Cette version apporte des améliorations dans disponibilité, extensibilité élevées et la productivité pour ouvrir source explosion Apache sur HDInsight. <ul><li>Leaders disponibilité SLA de 99,9 % qui rend adapté aux charges de l’entreprise.</li><li>Couche de stockage de fichiers à l’aide de Azure données Lake Store.</li><li>Outils de productivité pour chaque phase de développement et l’exploration de données. Blocs-notes Jupyter avec noyau explosion personnalisé activer exploration de données interactive, l’intégration avec des tableaux de bord BI, tels que Power BI et Tableau Qlik est préférable pour le partage de données rapides et continue de création de rapports, plug-in IntelliJ est fiable option permettant à long terme code objet développement et le débogage.</li></ul>| Service    | Explosion| N/A|
| Outils de HDInsight pour IntelliJ | Il s’agit d’un plug-in IntelliJ idée pour les clusters HDInsight Spark. Il active les fonctionnalités suivantes.<ul><li>Créer et d’écrire une application explosion facilement dans Scala et Java avec première classe prise en charge IntelliSense, mise en forme automatique, la vérification des erreurs, etc. de création.</li><li>Testez l’application explosion localement.</li><li>Soumettre les tâches au cluster HDInsight Spark et récupérer les résultats.</li><li>Connectez-vous à Azure et accéder à tous les groupes d’explosion associés à vos abonnements Azure.</li><li>Accédez à toutes les ressources de stockage associée de votre cluster HDInsight Spark.</li><li>Accédez à toutes les informations de projet pour votre cluster HDInsight Spark et l’historique des travaux.</li><li>Déboguer des travaux d’explosion à distance à partir de votre ordinateur de bureau.</li></ul>| Outils    | Explosion| N/A

## <a name="notes-for-05132016-release-of-hdinsight"></a>Notes d’HDInsight de virtualisation 05/13/2016

Les numéros de version complète des clusters HDInsight déployés avec cette version :

* HDInsight (Windows) 2.1.10.875.2159884 (1.3.12.0-01795 HDP - inchangée)
* HDInsight (Windows) 3.0.6.875.2159884 (2.0.13.0-2117 HDP - inchangée)
* HDInsight (Windows) 3.1.4.922.2266903 (2.1.15.0-2374 HDP - inchangée)
* HDInsight (Windows) 3.2.7.922.2266903 (HDP 2.2.9.1-11)
* HDInsight (Windows) 3.3.0.922.2266903 (HDP 2.3.3.1-18)
* HDInsight (Linux) 3.2.1000.0.7565644 (HDP 2.2.9.1-11)
* HDInsight (Linux) 3.3.1000.0.7565644 (HDP 2.3.3.1-18)
* HDInsight (Linux) 3.4.1000.0.7548380 (HDP 2.4.2.0)

Cette version contient les mises à jour suivants.

| Titre                                           | Description                                          | Zone concerné (par exemple, Service, composant ou SDK) | Type de cluster (par exemple explosion, Hadoop, HBase ou vague de) | JIRA (le cas échéant) |
|-------------------------------------------------|------------------------------------------------------|---------------------------------------------------------|-----------------------------------------------------|----------------------|
| Service de mise à jour de version et autres correctifs | Cette version mises à jour la version d’explosion HDInsight cluster 1.6.1 et correctifs autres bogues| Service    | Explosion| N/A

## <a name="notes-for-04112016-release-of-hdinsight"></a>Notes de version 11/04/2016 de HDInsight

Les numéros de version complète des clusters HDInsight déployés avec cette version :

* HDInsight (Windows) 2.1.10.889.2191206 (1.3.12.0-01795 HDP - inchangée)
* HDInsight (Windows) 3.0.6.889.2191206 (2.0.13.0-2117 HDP - inchangée)
* HDInsight (Windows) 3.1.4.889.2191206 (2.1.15.0-2374 HDP - inchangée)
* HDInsight (Windows) 3.2.7.889.2191206 (HDP 2.2.9.1-10)
* HDInsight (Windows) 3.3.0.889.2191206 (2.3.3.1-16 HDP-ne change pas)
* HDInsight (Linux) 3.2.1000.0.7339916 (HDP 2.2.9.1-10)
* HDInsight (Linux) 3.3.1000.0.7339916 (HDP 2.3.3.1-16)
* HDInsight (Linux) 3.4.1000.0.7338911 (HDP 2.4.1.1-3)
* KIT DE DÉVELOPPEMENT LOGICIEL 1.5.8

Cette version contient les mises à jour suivants.

| Titre                                           | Description                                          | Zone concerné (par exemple, Service, composant ou SDK) | Cluster Type (par exemple, Hadoop, HBase ou vague de) | JIRA (le cas échéant) |
|-------------------------------------------------|------------------------------------------------------|---------------------------------------------------------|-----------------------------------------------------|----------------------|
| Problèmes de mise à niveau metastore personnalisé 3.4 HDI | Création d’un cluster échoue si vous avez utilisé un metastore personnalisé qui a été précédemment utilisé dans une version antérieure d’un autre cluster HDInsight. Il s’agissait en raison d’une erreur de script mise à niveau est désormais résolue| Création d’un cluster    | Tous les | N/A
| Récupération de blocage Livy | Fournit la résilience état travail pour n’importe quelle tâche déposé par le biais Livy | Fiabilité | Explosion sous Linux| N/A
| Contenu Jupyter HA | Offre la possibilité d’enregistrer et charger Jupyter bloc-notes contenu vers et depuis le compte de stockage associé au cluster. Pour plus d’informations, voir [noyaux disponibles pour les blocs-notes Jupyter](hdinsight-apache-spark-jupyter-notebook-kernels.md).| Blocs-notes | Explosion sous Linux| N/A
| Suppression du hiveContext dans les blocs-notes Jupter | Utiliser `%%sql` magique plutôt que `%%hive` magie. SqlContext équivaut à hiveContext. Pour plus d’informations, voir [noyaux disponibles pour les blocs-notes Jupyter](hdinsight-apache-spark-jupyter-notebook-kernels.md)| Blocs-notes    | Clusters explosion sur Linux| N/A
| Amortissement d’anciennes versions d’explosion | Version plus ancienne explosion 1.3.1 sont supprimée à partir du service sur 5/31 | Service | Clusters explosion sur Windows | N/A

## <a name="notes-for-03292016-release-of-hdinsight"></a>Notes d’HDInsight de virtualisation 29/03/2016

Les numéros de version complète des clusters HDInsight déployés avec cette version :

* HDInsight (Windows) 2.1.10.875.2159884 (1.3.12.0-01795 HDP - inchangée)
* HDInsight (Windows) 3.0.6.875.2159884 (2.0.13.0-2117 HDP - inchangée)
* HDInsight (Windows) 3.1.4.875.2159884 (2.1.15.0-2374 HDP - inchangée)
* HDInsight (Windows) 3.2.7.875.2159884 (2.2.9.1-7 HDP - inchangée)
* HDInsight (Windows) 3.3.0.875.2159884 (HDP 2.3.3.1-16)
* HDInsight (Linux) 3.2.1000.0.7193255 (2.2.9.1-8 HDP - inchangée)
* HDInsight (Linux) 3.3.1000.0.7193255 (2.3.3.1-7 HDP - inchangée)
* HDInsight (Linux) 3.4.1000.0.7195842 (HDP 2.4.1.0-327)
* KIT DE DÉVELOPPEMENT LOGICIEL 1.5.8

Cette version contient les mises à jour suivants.

| Titre                                           | Description                                          | Zone concerné (par exemple, Service, composant ou SDK) | Cluster Type (par exemple, Hadoop, HBase ou vague de) | JIRA (le cas échéant) |
|-------------------------------------------------|------------------------------------------------------|---------------------------------------------------------|-----------------------------------------------------|----------------------|
| Version 3.4 HDInsight ajoutée et versions HDP mises à jour pour tous les clusters HDInsight | Dans cette version, nous ont ajouté v3.4 HDInsight (basé sur HDP 2.4) et ont également mis à jour d’autres versions HDP. Notes de publication HDP 2.4 sont disponibles [ici](http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.4.0/bk_HDP_RelNotes/content/ch_relnotes_v240.html) et plus d’informations sur HDInsight versions peuvent être trouvés [ici](hdinsight-component-versioning.md).| Service    | Tous les clusters Linux| N/A
| HDInsight Premium | HDInsight est désormais disponible en deux catégories - Standard et Premium. HDInsight Premium est actuellement en mode Aperçu avant et disponible uniquement pour les Hadoop et explosion clusters sous Linux. Pour plus d’informations, voir [ici](hdinsight-component-versioning.md#hdinsight-standard-and-hdinsight-premium).| Service    | Hadoop et explosion sous Linux| N/A
| Serveur Microsoft R | HDInsight Premium fournit Microsoft R Server pouvant être inclus avec clusters Hadoop et explosion sur Linux. Pour plus d’informations, voir [Vue d’ensemble de R Server sur HDInsight](hdinsight-hadoop-r-server-overview.md).| Service    | Hadoop et explosion sous Linux| N/A
| Explosion 1.6.0 | 3.4 HDInsight clusters incluent désormais explosion 1.6.0| Service    | Clusters explosion sur Linux| N/A
| Améliorations de bloc-notes Jupyter | Blocs-notes Jupyter disponibles avec les clusters explosion fournissent maintenant explosion supplémentaire noyaux. Ils incluent également des améliorations telles que l’utilisation de %% magie, automatique visualisation et l’intégration avec les bibliothèques de visualisation Python (par exemple, matplotlib). Pour plus d’informations, voir [noyaux disponibles pour les blocs-notes Jupyter](hdinsight-apache-spark-jupyter-notebook-kernels.md). | Service | Clusters explosion sur Linux | N/A

## <a name="notes-for-03222016-release-of-hdinsight"></a>Notes d’HDInsight de virtualisation 22/03/2016

Les numéros de version complète des clusters HDInsight déployés avec cette version :

* HDInsight (Windows) 2.1.10.875.2159884 (1.3.12.0-01795 HDP - inchangée)
* HDInsight (Windows) 3.0.6.875.2159884 (2.0.13.0-2117 HDP - inchangée)
* HDInsight (Windows) 3.1.4.875.2159884 (2.1.15.0-2374 HDP - inchangée)
* HDInsight (Windows) 3.2.7.875.2159884 (2.2.9.1-7 HDP - inchangée)
* HDInsight (Windows) 3.3.0.875.2159884 (HDP 2.3.3.1-16)
* HDInsight (Linux) 3.2.1000.0.7193255 (2.2.9.1-8 HDP - inchangée)
* HDInsight (Linux) 3.3.1000.0.7193255 (2.3.3.1-7 HDP - inchangée)
* KIT DE DÉVELOPPEMENT LOGICIEL 1.5.8

Cette version contient les mises à jour suivants.

| Titre                                           | Description                                          | Zone concerné (par exemple, Service, composant ou SDK) | Cluster Type (par exemple, Hadoop, HBase ou vague de) | JIRA (le cas échéant) |
|-------------------------------------------------|------------------------------------------------------|---------------------------------------------------------|-----------------------------------------------------|----------------------|
| Versions HDInsight mises à jour pour tous les clusters HDInsight | Dans cette version, nous avons versions mises à jour HDInsight pour tous les clusters HDInsight| Service    | Tous les| N/A


## <a name="notes-for-03102016-release-of-hdinsight"></a>Notes d’HDInsight de virtualisation 10/03/2016

Les numéros de version complète des clusters HDInsight déployés avec cette version :

* HDInsight (Windows) 2.1.10.859.2123216 (1.3.12.0-01795 HDP - inchangée)
* HDInsight (Windows) 3.0.6.859.2123216 (2.0.13.0-2117 HDP - inchangée)
* HDInsight (Windows) 3.1.4.859.2123216 (2.1.15.0-2374 HDP - inchangée)
* HDInsight (Windows) 3.2.7.859.2123216 (HDP 2.2.9.1-7)
* HDInsight (Windows) 3.3.0.859.2123216 (2.3.3.1-5 HDP - inchangée)
* HDInsight (Linux) 3.2.1000.7076817 (HDP 2.2.9.1-8)
* HDInsight (Linux) 3.3.1000.7076817 (HDP 2.3.3.1-7)
* KIT DE DÉVELOPPEMENT LOGICIEL 1.5.8

Cette version contient les mises à jour suivants.

| Titre                                           | Description                                          | Zone concerné (par exemple, Service, composant ou SDK) | Cluster Type (par exemple, Hadoop, HBase ou vague de) | JIRA (le cas échéant) |
|-------------------------------------------------|------------------------------------------------------|---------------------------------------------------------|-----------------------------------------------------|----------------------|
| Versions HDInsight mises à jour pour tous les clusters HDInsight | Dans cette version, nous avons versions mises à jour HDInsight pour tous les clusters HDInsight| Service    | Tous les| N/A

## <a name="notes-for-01272016-release-of-hdinsight"></a>Notes d’HDInsight de virtualisation 27/01/2016

Les numéros de version complète des clusters HDInsight déployés avec cette version :

* HDInsight (Windows) 2.1.10.817.2028315 (1.3.12.0-01795 HDP - inchangée)
* HDInsight (Windows) 3.0.6.817.2028315 (2.0.13.0-2117 HDP - inchangée)
* HDInsight (Windows) 3.1.4.817.2028315 (2.1.15.0-2374 HDP - inchangée)
* HDInsight (Windows) 3.2.7.817.2028315 (HDP 2.2.9.1-1)
* HDInsight (Windows) 3.3.0.817.2028315 (2.3.3.1-5 HDP - inchangée)
* HDInsight (Linux) 3.2.1000.4072335 (HDP 2.2.9.1-1)
* HDInsight (Linux) 3.3.1000.4072335 (HDP 2.3.3.1-1)
* KIT DE DÉVELOPPEMENT LOGICIEL 1.5.8

Cette version contient les mises à jour suivants.

| Titre                                           | Description                                          | Zone concerné (par exemple, Service, composant ou SDK) | Cluster Type (par exemple, Hadoop, HBase ou vague de) | JIRA (le cas échéant) |
|-------------------------------------------------|------------------------------------------------------|---------------------------------------------------------|-----------------------------------------------------|----------------------|
| Versions HDInsight mises à jour pour tous les clusters HDInsight | Dans cette version, nous avons versions mises à jour HDInsight pour tous les clusters HDInsight| Service    | Tous les| N/A

## <a name="notes-for-12022015-release-of-hdinsight"></a>Notes d’HDInsight de virtualisation 02/12/2015

Les numéros de version complète des clusters HDInsight déployés avec cette version :

* HDInsight (Windows) 2.1.10.763.1931434 (1.3.12.0-01795 HDP - inchangée)
* HDInsight (Windows) 3.0.6.763.1931434 (2.0.13.0-2117 HDP - inchangée)
* HDInsight (Windows) 3.1.4.763.1931434 (2.1.15.0-2374 HDP - inchangée)
* HDInsight (Windows) 3.2.7.763.1931434 (2.2.7.1-34 HDP - inchangée)
* HDInsight (Windows) 3.3.1000.0 (HDP 2.3.3.1-5)
* HDInsight (Linux) 3.2.1000.0.6392801 (2.2.7.1-34 HDP - inchangée)
* HDInsight (Linux) 3.3.1000.0 (HDP 2.3.3.0-3039)
* KIT DE DÉVELOPPEMENT LOGICIEL 1.5.8

Cette version contient les mises à jour suivants.

| Titre                                           | Description                                          | Zone concerné (par exemple, Service, composant ou SDK) | Cluster Type (par exemple, Hadoop, HBase ou vague de) | JIRA (le cas échéant) |
|-------------------------------------------------|------------------------------------------------------|---------------------------------------------------------|-----------------------------------------------------|----------------------|
| Version 3.3 HDInsight ajoutée et versions HDP mises à jour pour tous les clusters HDInsight | Dans cette version, nous ont ajouté v3.3 HDInsight (basé sur HDP 2.3) et ont également mis à jour d’autres versions HDP. Notes de publication HDP 2.3 sont disponibles [ici](http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.3.0/bk_HDP_RelNotes/content/ch_relnotes_v230.html) et plus d’informations sur HDInsight versions peuvent être trouvés [ici](hdinsight-component-versioning.md).| Service    | Tous les| N/A

## <a name="notes-for-11302015-release-of-hdinsight"></a>Notes d’HDInsight de virtualisation 30/11/2015

Les numéros de version complète des clusters HDInsight déployés avec cette version :

* HDInsight (Windows) 2.1.10.757.1923908 (1.3.12.0-01795 HDP - inchangée)
* HDInsight (Windows) 3.0.6.757.1923908 (2.0.13.0-2117 HDP - inchangée)
* HDInsight (Windows) 3.1.4.757.1923908 (2.1.15.0-2374 HDP - inchangée)
* HDInsight (Windows) 3.2.7.757.1923908 (HDP 2.2.7.1-34)
* HDInsight (Linux) 3.2.1000.0.6392801 (HDP 2.2.7.1-34)
* KIT DE DÉVELOPPEMENT LOGICIEL 1.5.8

Cette version contient les mises à jour suivants.

| Titre                                           | Description                                          | Zone concerné (par exemple, Service, composant ou SDK) | Cluster Type (par exemple, Hadoop, HBase ou vague de) | JIRA (le cas échéant) |
|-------------------------------------------------|------------------------------------------------------|---------------------------------------------------------|-----------------------------------------------------|----------------------|
| Versions HDInsight mises à jour pour tous les clusters HDInsight et versions HDP pour HDInsight 3,2 clusters (Windows et Linux) | Dans cette version, versions HDInsight et HDP ont été mis à jour | Service    | Tous les| N/A


## <a name="notes-for-10272015-release-of-hdinsight"></a>Notes d’HDInsight de virtualisation 27/10/2015

Les numéros de version complète des clusters HDInsight déployés avec cette version :

* HDInsight (Windows) 2.1.10.726.1866228 (1.3.12.0-01795 HDP - inchangée)
* HDInsight (Windows) 3.0.6.726.1866228 (2.0.13.0-2117 HDP - inchangée)
* HDInsight (Windows) 3.1.4.726.1866228 (2.1.15.0-2374 HDP - inchangée)
* HDInsight (Windows) 3.2.7.726.1866228 (HDP 2.2.7.1-33)
* HDInsight (Linux) 3.2.1000.0.6035701 (HDP 2.2.7.1-33)
* KIT DE DÉVELOPPEMENT LOGICIEL 1.5.8

Cette version contient les mises à jour suivants.

| Titre                                           | Description                                          | Zone concerné (par exemple, Service, composant ou SDK) | Cluster Type (par exemple, Hadoop, HBase ou vague de) | JIRA (le cas échéant) |
|-------------------------------------------------|------------------------------------------------------|---------------------------------------------------------|-----------------------------------------------------|----------------------|
| Versions HDInsight mises à jour pour tous les clusters HDInsight (Windows et Linux) | Dans cette version, versions HDInsight et HDP ont été mis à jour | Service    | Tous les| N/A
| Fixe Jupyter pour Windows explosion clusters avec les majuscules clusters | Clusters incluant des noms DNS spécifiés en majuscules avaient des problèmes avec des blocs-notes Jupyter en raison d’une vérification de demande d’origine. Le correctif a été pour modifier le nom DNS pour une configuration de Jupyter en minuscules. | Service    | HDInsight explosion (Windows)| N/A


## <a name="notes-for-10202015-release-of-hdinsight"></a>Notes d’HDInsight de virtualisation 20/10/2015

Les numéros de version complète des clusters HDInsight déployés avec cette version :

* HDInsight 2.1.10.716.1846990 (Windows) (1.3.12.0-01795 HDP - inchangée)
* HDInsight 3.0.6.716.1846990 (Windows) (2.0.13.0-2117 HDP - inchangée)
* HDInsight 3.1.4.716.1846990 (Windows) (HDP 2.1.16.0-2374)
* HDInsight 3.2.7.716.1846990 (Windows) (HDP 2.2.7.1-0004)
* HDInsight 3.2.1000.0.5930166 (Linux) (HDP 2.2.7.1-0004)
* KIT DE DÉVELOPPEMENT LOGICIEL 1.5.8

Cette version contient les mises à jour suivants.

| Titre                                           | Description                                          | Zone concerné (par exemple, Service, composant ou SDK) | Cluster Type (par exemple, Hadoop, HBase ou vague de) | JIRA (le cas échéant) |
|-------------------------------------------------|------------------------------------------------------|---------------------------------------------------------|-----------------------------------------------------|----------------------|
| Version HDP par défaut modifiée en HDP 2.2 | La version par défaut pour les clusters HDInsight Windows est modifiée en HDP 2.2. HDInsight version 3.2 (HDP 2.2) est généralement disponible dans depuis février 2015. Cette modification inverse uniquement la version de cluster par défaut, lors de la sélection explicite n’a pas été effectuée lors de la mise en service le cluster à l’aide du portail Azure, applets de commande PowerShell ou le Kit de développement. | Service    | Tous les| N/A                  |
|Modifications apportées au format de nom machine virtuelle pour le déploiement de plusieurs HDInsight sur groupes de Linux dans un seul réseau virtuel | Prise en charge pour le déploiement de plusieurs groupes de HDInsight Linux dans un seul réseau virtuel est ajouté dans cette version. Dans le cadre du présent, le format des noms de machine virtuelle du cluster a changé de headnode\*, workernode\* et zookeepernode\* à hn\*, wn\*et zk\* respectivement. Il n’est pas recommandé d’effectuer une dépendance directe sur le format des noms de machine virtuelle, car il s’agit peuvent être modifiées. Utilisez « hostname -f » sur l’ordinateur local ou Ambari APIs pour déterminer la liste des hôtes et le mappage des composants à des hôtes. Vous trouverez plus d’informations sur [https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/hosts.md](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/hosts.md) et [https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/host-components.md](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/host-components.md). | Service | HDInsight clusters sur Linux | N/A |
| Modifications de configuration | Pour les clusters 3.1 HDInsight, les configurations suivantes sont désormais activées : <ul><li>tez.yarn.ATS.Enabled et yarn.log.server.url. Cela permet au serveur chronologie d’applications et le serveur de journal peut présenter les journaux.</li></ul>Pour les clusters 3,2 HDInsight, les configurations suivantes ont été modifiées : <ul><li>MapReduce.fileoutputcommitter.Algorithm.version a été défini à 2. Cela permet d’utiliser la version V2 de la FileOutputCommitter.</li></ul> | Service | Tous les | N/A |


## <a name="notes-for-09092015-release-of-hdinsight"></a>Notes d’HDInsight de virtualisation 09/09/2015

Les numéros de version complète des clusters HDInsight déployés avec cette version :

* HDInsight 2.1.10.675.1768697 (1.3.12.0-01795 HDP - inchangée)
* HDInsight 3.0.6.675.1768697 (2.0.13.0-2117 HDP - inchangée)
* HDInsight 3.1.4.675.1768697 (2.1.15.0-2334 HDP - inchangée)
* HDInsight 3.2.6.675.1768697 (2.2.6.1-0012 HDP - inchangée)
* KIT DE DÉVELOPPEMENT LOGICIEL 1.5.8

Cette version contient les mises à jour suivants.

| Titre                                           | Description                                          | Zone concerné (par exemple, Service, composant ou SDK) | Cluster Type (par exemple, Hadoop, HBase ou vague de) | JIRA (le cas échéant) |
|-------------------------------------------------|------------------------------------------------------|---------------------------------------------------------|-----------------------------------------------------|----------------------|
| Versions HDInsight mises à jour pour tous les clusters HDInsight | Dans cette version, versions HDInsight ont été mis à jour | Service    | Tous les| N/A                  |

## <a name="notes-for-07312015-release-of-hdinsight"></a>Notes d’HDInsight de virtualisation 31/07/2015

Les numéros de version complète des clusters HDInsight déployés avec cette version :

* HDInsight 2.1.10.640.1695824 (1.3.12.0-01795 HDP - inchangée)
* HDInsight 3.0.6.640.1695824 (2.0.13.0-2117 HDP - inchangée)
* HDInsight 3.1.4.640.1695824 (2.1.15.0-2334 HDP - inchangée)
* HDInsight 3.2.6.640.1695824 (2.2.6.1-0012 HDP - inchangée)
* KIT DE DÉVELOPPEMENT LOGICIEL 1.5.8

Cette version contient les mises à jour suivants.

| Titre                                           | Description                                          | Zone concerné (par exemple, Service, composant ou SDK) | Cluster Type (par exemple, Hadoop, HBase ou vague de) | JIRA (le cas échéant) |
|-------------------------------------------------|------------------------------------------------------|---------------------------------------------------------|-----------------------------------------------------|----------------------|
| Corriger explosion cluster nœud redéfinition d’image du flux de travail | Corrige un bogue qui a été à l’origine d’explosion nœuds pour récupérer pas après la nouvelle image | Service    | Explosion| N/A                  |


## <a name="notes-for-07312015-release-of-hdinsight"></a>Notes d’HDInsight de virtualisation 31/07/2015

Les numéros de version complète des clusters HDInsight déployés avec cette version :

* HDInsight 2.1.10.635.1684502 (1.3.12.0-01795 HDP - inchangée)
* HDInsight 3.0.6.635.1684502 (2.0.13.0-2117 HDP - inchangée)
* HDInsight 3.1.4.635.1684502 (2.1.15.0-2334 HDP - inchangée)
* HDInsight 3.2.6.635.1684502 (2.2.6.1-0012 HDP - inchangée)
* KIT DE DÉVELOPPEMENT LOGICIEL 1.5.8

Cette version contient les mises à jour suivants.

| Titre                                           | Description                                          | Zone concerné (par exemple, Service, composant ou SDK) | Cluster Type (par exemple, Hadoop, HBase ou vague de) | JIRA (le cas échéant) |
|-------------------------------------------------|------------------------------------------------------|---------------------------------------------------------|-----------------------------------------------------|----------------------|
| Versions HDInsight mises à jour pour tous les clusters HDInsight | Dans cette version, versions HDInsight ont été mis à jour | Service    | Tous les| N/A                  |


## <a name="notes-for-07072015-release-of-hdinsight"></a>Notes d’HDInsight de virtualisation 07/07/2015

Les numéros de version complète des clusters HDInsight déployés avec cette version :

* HDInsight 2.1.10.610.1630216 (1.3.12.0-01795 HDP - inchangée)
* HDInsight 3.0.6.610.1630216 (2.0.13.0-2117 HDP - inchangée)
* HDInsight 3.1.4.610.1630216 (2.1.15.0-2334 HDP - inchangée)
* HDInsight 3.2.4.610.1630216 (HDP 2.2.6.1-0012)
* KIT DE DÉVELOPPEMENT LOGICIEL 1.5.8


Cette version contient les mises à jour suivants.

| Titre                                           | Description                                          | Zone concerné (par exemple, Service, composant ou SDK) | Cluster Type (par exemple, Hadoop, HBase ou vague de) | JIRA (le cas échéant) |
|-------------------------------------------------|------------------------------------------------------|---------------------------------------------------------|-----------------------------------------------------|----------------------|
| Versions HDP mises à jour pour les clusters 3,2 HDInsight | Dans cette version, 3,2 HDInsight déploie HDP 2.2.6.1-0012 | Service    | Tous les                                                 | N/A                  |


## <a name="notes-for-06262015-release-of-hdinsight"></a>Notes d’HDInsight de virtualisation 26/06/2015

Les numéros de version complète des clusters HDInsight déployés avec cette version :

* HDInsight 2.1.10.601.1610731 (1.3.12.0-01795 HDP - inchangée)
* HDInsight 3.0.6.601.1610731 (2.0.13.0-2117 HDP - inchangée)
* HDInsight 3.1.4.601.1610731 (2.1.15.0-2334 HDP - inchangée)
* HDInsight 3.2.4.601.1610731 (HDP 2.2.6.1-0011)
* KIT DE DÉVELOPPEMENT LOGICIEL 1.5.8


Cette version contient les mises à jour suivants.

<table border="1">
<tr>
<th>Titre</th>
<th>Description</th>
<th>Zone concerné (par exemple, Service, composant ou SDK)</p></th>
<th>Cluster Type (par exemple, Hadoop, HBase ou vague de)</th>
<th>JIRA (le cas échéant)</th>
</tr>


<tr>
<td>Versions HDP mises à jour pour les clusters 3,2 HDInsight</td>
<td>Dans cette version, 3,2 HDInsight déploie HDP points 2.2.6.1</td>
<td>Service</td>
<td>Tous les</td>
<td>N/A</td>
</tr>

</table>

## <a name="notes-for-06182015-release-of-hdinsight"></a>Notes d’HDInsight de virtualisation 18/06/2015

Les numéros de version complète des clusters HDInsight déployés avec cette version :

* HDInsight 2.1.10.596.1601657 (1.3.12.0-01795 HDP - inchangée)
* HDInsight 3.0.6.596.1601657 (2.0.13.0-2117 HDP - inchangée)
* HDInsight 3.1.4.596.1601657 (HDP 2.1.15.0-2334)
* HDInsight 3.2.4.596.1601657 (HDP 2.2.6.1-0002)
* KIT DE DÉVELOPPEMENT LOGICIEL 1.5.8


Cette version contient les mises à jour suivants.

<table border="1">
<tr>
<th>Titre</th>
<th>Description</th>
<th>Zone concerné (par exemple, Service, composant ou SDK)</p></th>
<th>Cluster Type (par exemple, Hadoop, HBase ou vague de)</th>
<th>JIRA (le cas échéant)</th>
</tr>


<tr>
<td>Ports HTTPS supplémentaires ouvert</td>
<td>Le service cloud s’ouvre 5 ports 8001 à 8005 sur le cluster ex. https://<clustername>.azurehdinsight.net:8001/. Demandes à ces URL sont authentifiés à l’aide du même mécanisme de mot de passe de l’authentification de base en tant que le port 443. Ces ports lier à la même voie la headnode active. Actions de script peuvent être utilisées pour effectuer des services client écoute de ces derniers sur la headnode et un itinéraire à l’extérieur du cluster.</td>
<td>Service de nuage</td>
<td>Tous les</td>
<td>N/A</td>
</tr>

<tr>
<td>Problème de la réorganisation MapReduce intermittent pour 3.2 HDInsight</td>
<td>Correction d’une condition de concurrence rares, intermittent dans la réorganisation de réduire la carte sur les grands clusters résultant dans échecs de tâche occasionnelle. Pour plus d’informations, consultez <a href="https://issues.apache.org/jira/browse/MAPREDUCE-6361" target="_blank">MAPREDUCE 6361</a> .</td>
<td>Hadoop Core</td>
<td>Tous les</td>
<td><a href="https://issues.apache.org/jira/browse/MAPREDUCE-6361" target="_blank">MAPREDUCE 6361</a></td>
</tr>

<tr>
<td>Atteindre la dernière Java Azure SDK 2.2 pour HDInsight 3,2</td>
<td>Déplacé vers la version la plus récente du Kit de développement Azure pour Java utilisée par le pilote WASB. La dernière version du SDK comporte quelques solutions et les notes de la même sont disponibles dans la page https://github.com/Azure/azure-storage-java/blob/master/ChangeLog.txt.</td>
<td>Hadoop Core</td>
<td>Tous les</td>
<td><a href="https://issues.apache.org/jira/browse/HADOOP-11959" target="_blank">HADOOP 11959</a></td>
</tr>

<tr>
<td>Atteindre HDP 2.1.15 pour les clusters 3.1 HDInsight</td>
<td>Notes de publication Hortonworks pour la mise à jour sont disponibles <a href="http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.1.15-Win/bk_releasenotes_HDP-Win/content/ch_relnotes-HDP-2.1.15.html" target="_blank">ici</a>.</td>
<td>HDP</td>
<td>Tous les</td>
<td>N/A</td>
</tr>

</table>

## <a name="notes-for-06042015-release-of-hdinsight"></a>Notes d’HDInsight de virtualisation 04/06/2015

Les numéros de version complète des clusters HDInsight déployés avec cette version :

* HDInsight 2.1.10.583.1575584 (1.3.12.0-01795 HDP - inchangée)
* HDInsight 3.0.6.583.1575584 (2.0.13.0-2117 HDP - inchangée)
* HDInsight 3.1.3.583.1575584 (2.1.12.1-0003 HDP - inchangée)
* HDInsight 3.2.4.583.1575584 (HDP 2.2.6.1-1)
* KIT DE DÉVELOPPEMENT LOGICIEL 1.5.8


Cette version contient les mises à jour suivants.

<table border="1">
<tr>
<th>Titre</th>
<th>Description</th>
<th>Zone concerné (par exemple, Service, composant ou SDK)</p></th>
<th>Cluster Type (par exemple, Hadoop, HBase ou vague de)</th>
<th>JIRA (le cas échéant)</th>
</tr>


<tr>
<td>Résoudre l’erreur 502 Passerelle incorrecte clusters vague</td>
<td>Cette version résout un bogue qui peuvent affecter l’envoi de travail API qui a provoqué le site Web soit vers le bas après un redémarrage.</td>
<td>Service</td>
<td>Vague d'</td>
<td>N/A</td>
</tr>

</table>

## <a name="notes-for-06012015-release-of-hdinsight"></a>Notes d’HDInsight de virtualisation 01/06/2015

Les numéros de version complète des clusters HDInsight déployés avec cette version :

* HDInsight 2.1.10.577.1563827 (1.3.12.0-01795 HDP - inchangée)
* HDInsight 3.0.6.577.1563827 (2.0.13.0-2117 HDP - inchangée)
* HDInsight 3.1.3.577.1563827 (2.1.12.1-0003 HDP - inchangée))
* HDInsight 3.2.4.577.1563827 (2.2.6.0-2800 HDP - inchangée)
* KIT DE DÉVELOPPEMENT LOGICIEL 1.5.8


Cette version contient les mises à jour suivants.

<table border="1">
<tr>
<th>Titre</th>
<th>Description</th>
<th>Zone concerné (par exemple, Service, composant ou SDK)</p></th>
<th>Cluster Type (par exemple, Hadoop, HBase ou vague de)</th>
<th>JIRA (le cas échéant)</th>
</tr>


<tr>
<td>Correctifs divers</td>
<td>Cette version résout bogues liés à la mise en service de cluster.</td>
<td>Service</td>
<td>Tous les types de cluster</td>
<td>N/A</td>
</tr>

</table>

## <a name="notes-for-05272015-release-of-hdinsight"></a>Notes d’HDInsight de virtualisation 27/05/2015

Les numéros de version complète des clusters HDInsight déployés avec cette version :

* HDInsight 3.2.4.570.1554102 (HDP 2.2.6.0-2800)
* Autres versions de cluster et le Kit de développement logiciel ne sont pas déployées dans le cadre de cette version.


Cette version contient les mises à jour suivants.

<table border="1">
<tr>
<th>Titre</th>
<th>Description</th>
<th>Zone concerné (par exemple, Service, composant ou SDK)</p></th>
<th>Cluster Type (par exemple, Hadoop, HBase ou vague de)</th>
<th>JIRA (le cas échéant)</th>
</tr>


<tr>
<td>Mise à jour 2.2 HDP</td>
<td>Cette version de HDInsight 3,2 contient HDP 2.2.6 et offre plusieurs correctifs importantes à HDInsight. Les notes de publication complète est disponible dans <a href="http://dev.hortonworks.com.s3.amazonaws.com/HDPDocuments/HDP2/HDP-2.2.6/HDP_RelNotes_v226/index.html">HDP 2.2.6 Notes de publication</a>.</td>
<td>HDP</td>
<td>Tous les types de cluster</td>
<td>N/A</td>
</tr>

<tr>
<td>Modifier la configuration de mémoire par défaut fils conteneur</td>
<td>Dans cette mise à jour, la mémoire disponible aux conteneurs fils (yarn.nodemanager.resource.memory Mo et yarn.scheduler.maximum-allocation-Mo) par défaut lancée par le Gestionnaire de nœud, est augmentée à 5632 Mo. Précédemment cela a été réduit à 4608 Mo, mais en fonction de différents est exécuté, la nouvelle valeur doit offrir mieux la fiabilité et les performances à la plupart des tâches, par conséquent, est une valeur par défaut une meilleure. Comme habituel, si vous une dépendance critique sur cette configuration de la mémoire, définissez-la explicitement lors de la création du cluster.</td>
<td>HDP</td>
<td>Tous les types de cluster</td>
<td>N/A</td>
</tr>

<tr>
<td>Disparités qui existent de configuration de la valeur par défaut pour les clusters HBase et vague d'</td>
<td>Cette mise à jour restaure clusters Hbase et vague de pour utiliser les mêmes valeurs de configurations fils comme Hadoop clusters. Pour ce faire pour les disparités qui existent pour tous les types de cluster.</td>
<td>HDP</td>
<td>HBase, vague d'</td>
<td>N/A</td>
</tr>

</table>

## <a name="notes-for-05202015-release-of-hdinsight"></a>Notes d’HDInsight de virtualisation 20/05/2015

Les numéros de version complète des clusters HDInsight déployés avec cette version :

* HDInsight 2.1.10.564.1542093 (1.3.12.0-01795 HDP - inchangée)
* HDInsight 3.0.6.564.1542093 (2.0.13.0-2117 HDP - inchangée)
* HDInsight 3.1.3.564.1542093 (HDP 2.1.12.1-0003)
* HDInsight 3.2.4.564.1542093 (HDP 2.2.4.6-2)
* KIT DE DÉVELOPPEMENT LOGICIEL 1.5.8

Cette version contient les mises à jour suivants.

<table border="1">
<tr>
<th>Titre</th>
<th>Description</th>
<th>Zone concerné (par exemple, Service, composant ou SDK)</p></th>
<th>Cluster Type (par exemple, Hadoop, HBase ou vague de)</th>
<th>JIRA (le cas échéant)</th>
</tr>


<tr>
<td>Prise en charge SCP.NET EventHub</td>
<td>Les packages cluster mis à jour pour vague d’HDInsight permettent de nouvelles fonctionnalités SCP.NET. Vous a maintenant accès aux nouvelles API dans le Générateur de topologies qui le rendent plus facile à utiliser EventHubSpout ou Java becs verseurs amovibles. Vous devez mettre à jour votre client SCP.NET SDK pour travailler avec les nouveaux clusters comme le contrats ont été mis à jour. Pour plus d’informations sur les nouvelles notes API, l’utilisation et release (y compris les correctifs) reportez-vous au fichier Lisezmoi inclus dans le package nuget SCP.NET.</td>
<td>VS des outils</td>
<td>Renverser HDInsight 3,2 clusters</td>
<td>N/A</td>
</tr>

<tr>
<td>Mise à jour du pilote JDBC</td>
<td>Mise à jour le pilote vers la version de SQL Server pris en charge dans sqljdbc_4.1.5605.100.</td>
<td>Metastore</td>
<td>Tous les</td>
<td>N/A</td>
</tr>
</table>

## <a name="notes-for-04272015-release-of-hdinsight"></a>Notes d’HDInsight de virtualisation 27/04/2015

Les numéros de version complète des clusters HDInsight déployés avec cette version :

* HDInsight 2.1.10.537.1486660 (1.3.12.0-01795 HDP - inchangée)
* HDInsight 3.0.6.537.1486660 (2.0.13.0-2117 HDP - inchangée)
* HDInsight 3.1.3.537.1486660 (2.1.12.0-2329 HDP - inchangée)
* HDInsight 3.2.3.537.1486660 (HDP 2.2.2.2-4)
* KIT DE DÉVELOPPEMENT LOGICIEL 1.5.8

Cette version contient les mises à jour suivants.

<table border="1">
<tr>
<th>Titre</th>
<th>Description</th>
<th>Zone concerné (par exemple, Service, composant ou SDK)</p></th>
<th>Cluster Type (par exemple, Hadoop, HBase ou vague de)</th>
<th>JIRA (le cas échéant)</th>
</tr>


<tr>
<td>Corriger dépendance DLL</td>
<td>Supprime la dépendance par rapport à HDInsight infrastructure des tests unitaires.</td>
<td>KIT DE DÉVELOPPEMENT</td>
<td>Hadoop</td>
<td>N/A</td>
</tr>

<tr>
<td>Résolution du bogue pour condition de course</td>
<td>Un cluster Créer demande maintenant attend sur place demande soit acceptée avant d’interrogation de l’état</td>
<td>KIT DE DÉVELOPPEMENT</td>
<td>Hadoop</td>
<td>N/A</td>
</tr>
</table>

## <a name="notes-for-04142015-release-of-hdinsight"></a>Notes d’HDInsight de virtualisation 14/04/2015

Les numéros de version complète des clusters HDInsight déployés avec cette version :

* HDInsight 2.1.10.521.1453250 (1.3.12.0-01795 HDP - inchangée)
* HDInsight 3.0.6.521.1453250 (2.0.13.0-2117 HDP - inchangée)
* HDInsight 3.1.3.521.1453250 (2.1.12.0-2329 HDP - inchangée)
* HDInsight 3.2.3.525.1459730 (HDP 2.2.2.2-2)
* KIT DE DÉVELOPPEMENT LOGICIEL 1.5.6

Cette version contient les mises à jour suivants.

<table border="1">
<tr>
<th>Titre</th>
<th>Description</th>
<th>Zone concerné (par exemple, Service, composant ou SDK)</p></th>
<th>Cluster Type (par exemple, Hadoop, HBase ou vague de)</th>
<th>JIRA (le cas échéant)</th>
</tr>


<tr>
<td>Correctifs tez</td>
<td>Correctifs pour Apache TEZ 2214 et TEZ 1923 sont inclus dans cette version de HDI 3,2. Ceux-ci sont spécifiquement nécessaires pour certaines requêtes Hive sur Tez qui nécessitent réorganisation une quantité importante de données.
</td>
<td>HDP</td>
<td>Hadoop</td>
<td><a href="https://issues.apache.org/jira/browse/TEZ-2214">TEZ 2214</a></br><a href="https://issues.apache.org/jira/browse/TEZ-1923">TEZ 1923</a></td>
</tr>
</table>

## <a name="notes-for-04062015-release-of-hdinsight"></a>Notes d’HDInsight de virtualisation 04/06/2015

Les numéros de version complète des clusters HDInsight déployés avec cette version :

* HDInsight 2.1.10.521.1453250 (1.3.12.0-01795 HDP - inchangée)
* HDInsight 3.0.6.521.1453250 (2.0.13.0-2117 HDP - inchangée)
* HDInsight 3.1.3.521.1453250 (2.1.12.0-2329 HDP - inchangée)
* HDInsight 3.2.3.521.1453250 (HDP 2.2.2.2-1)
* KIT DE DÉVELOPPEMENT LOGICIEL 1.5.6

Cette version contient les mises à jour suivants.

<table border="1">
<tr>
<th>Titre</th>
<th>Description</th>
<th>Zone concerné (par exemple, Service, composant ou SDK)</p></th>
<th>Cluster Type (par exemple, Hadoop, HBase ou vague de)</th>
<th>JIRA (le cas échéant)</th>
</tr>


<tr>
<td>Kit de développement .NET HDInsight 1.5.6</td>
<td>Mises à jour pour supprimer certaines classes internes pour HDInsight sous Linux.</td>
<td>KIT DE DÉVELOPPEMENT</td>
<td>Hadoop</td>
<td>N/A</td>
</tr>

<tr>
<td>Bibliothèque de Avro 1.5.6</td>
<td><b>KnownTypeAttribute</b> ajouté pour méthode <b>GetAllKnownTypes</b>. NullReferenceException fixe lorsqu’un type est null pour GetAllKnownTypes méthode.</td>
<td>KIT DE DÉVELOPPEMENT</td>
<td>Hadoop</td>
<td>N/A</td>
</tr>

<tr>
<td>Correctifs</td>
<td>Différents correctifs au service</td>
<td>Service</td>
<td>Tous les</td>
<td>N/A</td>
</tr>

</table>
<br>

## <a name="notes-for-04012015-release-of-hdinsight"></a>Notes d’HDInsight de virtualisation 04/01/2015

Les numéros de version complète des clusters HDInsight déployés avec cette version :

* HDInsight 2.1.10.513.1431705 (HDP 1.3.12.0-01795)
* HDInsight 3.0.6.513.1431705 (HDP 2.0.13.0-2117)
* HDInsight 3.1.3.513.1431705 (HDP 2.1.12.0-2329)
* HDInsight 3.2.3.513.1431705 (HDP 2.2.2.1-2600)
* KIT DE DÉVELOPPEMENT LOGICIEL 1.5.5

Cette version contient les mises à jour suivants.

<table border="1">
<tr>
<th>Titre</th>
<th>Description</th>
<th>Zone concerné (par exemple, Service, composant ou SDK)</p></th>
<th>Cluster Type (par exemple, Hadoop, HBase ou vague de)</th>
<th>JIRA (le cas échéant)</th>
</tr>


<tr>
<td>Possibilité d’activer/désactiver les informations d’identification de bureau à distance sur les clusters Windows par le biais du .NET</td>
<td>Prise en charge par programme pour activer ou désactiver les informations d’identification RDP sur les clusters Windows.</td>
<td>KIT DE DÉVELOPPEMENT</td>
<td>Tous les</td>
<td>N/A</td>
</tr>

<tr>
<td>Possibilité d’activer les informations d’identification de bureau à distance sur clusters pendant qu’ils sont en cours mis en service</td>
<td>Prise en charge par programmation d’activation des informations d’identification du Bureau à distance comme le cluster est créé. Cela supprime le processus en deux étapes pour tout d’abord mise en service le cluster et puis l’activation du Bureau à distance.</td>
<td>KIT DE DÉVELOPPEMENT</td>
<td>Tous les</td>
<td>N/A</td>
</tr>

<tr>
<td>Python mis à niveau de point 2.7.8</td>
<td>Python mis à niveau sur HDInsight Clusters Python point 2.7.8, qui contient certains sécurité importantes solutions pour les versions HDInsight 2.1, 3.0, 3.1 et 3.2</td>
<td>Service</td>
<td>Tous les</td>
<td>N/A</td>
</tr>

<tr>
<td>Modification de la configuration fils</td>
<td>Modifié fils configuration yarn.resourcemanager.max terminée-applications à 1000 pour tous les types de cluster pour HDInsight versions 3.1 et 3.2. Cette valeur contrôle uniquement la liste des applications terminées dans l’interface utilisateur fils. Pour obtenir des informations sur les applications qui ont été soumis avant la liste d’applications apparaît dans l’interface utilisateur, vous pouvez accéder directement au serveur de l’historique.</td>
<td>FILS</td>
<td>Tous les</td>
<td>N/A</td>
</tr>

<tr>
<td>Redimensionnement des nœuds dans un cluster HBase</td>
<td>HBase clusters autorisent maintenant redimensionnement des nœuds (haut et bas) pour les versions HDInsight 3.1 et 3.2</td>
<td>Service</td>
<td>HBase</td>
<td>N/A</td>
</tr>

<tr>
<td>Mise à niveau JDBC</td>
<td>Pilote SQL JDBC est mis à niveau vers la version sqljdbc_4.0.2206.100 pour HDInsight version 3.2. Cette version contient des améliorations importantes de la sécurité.</td>
<td>HDP</td>
<td>Tous les</td>
<td>N/A</td>
</tr>

<tr>
<td>Mise à jour de la configuration machine virtuelle Java</td>
<td>Mise à jour machine virtuelle Java configuration networkaddress.cache.ttl à 300 secondes de la valeur par défaut de -1 pour les versions HDInsight 3.1 et 3.2. Cette valeur contrôle la stratégie de mise en cache pour les recherches de nom réussi à partir du nom. Cela résout un bogue lié à faire croître et réduction HBase clusters.</td>
<td>Service</td>
<td>HBase</td>
<td>N/A</td>
</tr>

<tr>
<td>Mise à niveau vers le stockage Azure SDK pour Java 2.0</td>
<td>HDInsight version 3.2 est mis à niveau pour utiliser la dernière version de l’espace de stockage Azure SDK pour Java. Cet onglet contient plusieurs correctifs importantes sur la 0.6.0 actuelle version.</td>
<td>HDP</td>
<td>Tous les</td>
<td>N/A</td>
</tr>

<tr>
<td>Mise à niveau vers code source WASB Apache</td>
<td>HDInsight version 3.2 maintenant utilise plus tard de code pour le pilote de système de fichiers WASB Hadoop Apache. Avec cette modification, le pilote WASB est maintenant présente sous la forme d’un fichier séparé jar. Ceci est un changement d’emballage purement et ne contient-elle pas les modifications apportées au comportement du pilote WASB. Le nom de ce fichier est hadoop-azure-2.6.0.jar.</td>
<td>HDP</td>
<td>Tous les</td>
<td>N/A</td>
</tr>

<tr>
<td>JAR mises à jour du nom de fichier dans 3,2 HDInsight</td>
<td>Cette mise à jour à HDInsight version 3.2 contient plusieurs correctifs et quelques bocaux interne empaquetés dans le cadre de HDP ont été mis à niveau. Notez que ces fichiers JAR sont internes pour le package HDP et non en direct par des applications clientes. Applications doivent empaqueter leur propre version de la bocaux afin que d’une mise à niveau vers les fichiers JAR internes HDP n’arrêtent pas les applications du client.</td>
<td>HDP</td>
<td>Tous les</td>
<td>N/A</td>
</tr>

</table>
<br>

## <a name="notes-for-03032015-release-of-hdinsight"></a>Notes d’HDInsight de virtualisation 03/03/2015

Les numéros de version complète des clusters HDInsight déployés avec cette version :

* HDInsight 2.1.10.488.1375841 (1.3.9.0-01351 HDP - inchangée)
* HDInsight 3.0.6.488.1375841 (2.0.9.0-2097 HDP - inchangée)
* HDInsight 3.1.3.488.1375841 (2.1.10.0-2290 HDP - inchangée)
* HDInsight 3.2.3.488.1375841 (HDP-2.2.10.0-2340 - inchangée)
* Kit de développement logiciel 1.5.0 (inchangé)

Cette version contient la mise à jour suivante.

<table border="1">
<tr>
<th>Titre</th>
<th>Description</th>
<th>Zone concerné (par exemple, service, composant ou SDK)</p></th>
<th>Cluster Type (par exemple, Hadoop, HBase ou vague de)</th>
<th>JIRA</th>
</tr>


<tr>
<td>Améliorations de la fiabilité</td>
<td>Nous avons apporté correctifs autorisent le service à l’échelle mieux avec l’augmentation de la charge en ce qui concerne la création d’un cluster.</td>
<td>Service</td>
<td>Tous les</td>
<td>N/A</td>
</tr>



</table>
<br>

## <a name="notes-for-02182015-release-of-hdinsight"></a>Notes d’HDInsight de virtualisation 18/02/2015

Les numéros de version complète des clusters HDInsight déployés avec cette version :

* HDInsight 2.1.10.471.1342507 (1.3.9.0-01351 HDP - inchangée)
* HDInsight 3.0.6.471.1342507 (2.0.9.0-2097 HDP - inchangée)
* HDInsight 3.1.3.471.1342507 (2.1.10.0-2290 HDP - inchangée)
* HDInsight 3.2.3.471.1342507 (HDP-2.2.10.0-2340)
* KIT DE DÉVELOPPEMENT LOGICIEL 1.5.0

Cette version contient les mises à jour suivants.

<table border="1">
<tr>
<th>Titre</th>
<th>Description</th>
<th>Zone concerné (par exemple, Service, composant ou SDK)</p></th>
<th>Cluster Type (par exemple, Hadoop, HBase ou vague de)</th>
<th>JIRA (le cas échéant)</th>
</tr>


<tr>
<td>HDInsight 3,2 clusters</td>
<td>Hadoop 2.6/HDP2.2 est disponible avec les clusters 3,2 HDInsight. Il contient les principales mises à jour à tous les composants open source. Pour plus d’informations, consultez quelles sont les nouveautés dans HDInsight et <a href ="http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.2.0/HDP_2.2.0_Release_Notes_20141202_version/index.html" target="_blank">notes de mise à jour de HDP 2.2.0.0</a>.</td>
<td>Logiciels libres</td>
<td>Tous les</td>
<td>N/A</td>
</tr>

<tr>
<td>HDinsight sous Linux (Preview)</td>
<td>Vous pouvez déployer clusters s’exécutant sur Ubuntu Linux. Pour plus d’informations, voir prise en main HDInsight sur Linux.</td>
<td>Service</td>
<td>Hadoop</td>
<td>N/A</td>
</tr>

<tr>
<td>Disponibilité vague de général</td>
<td>Apache vague clusters sont généralement disponibles. Pour plus d’informations, voir prise en main à l’aide de vague de dans HDInsight.</td>
<td>Service</td>
<td>Vague d'</td>
<td>N/A</td>
</tr>

<tr>
<td>Machine virtuelle tailles</td>
<td>Azure HDInsight est disponible sur plusieurs types de machine virtuelle et formats. HDInsight peut utiliser A2 à A7 tailles intégrées à des fins générales ; Nœuds de série D qui mettent en SSD (SSDs) et 60-pour cent processeurs plus rapides ; et tailles A8 et A9 ayant InfiniBand prise en charge de mise en réseau rapide.</td>
<td>Service</td>
<td>Tous les</td>
<td>N/A</td>
</tr>

<tr>
<td>Mise à l’échelle de cluster</td>
<td>Vous pouvez modifier le nombre de nœuds de données pour un cluster de HDInsight en cours d’exécution sans avoir à supprimer ou le recréer. Pour l’instant, seuls les types de cluster Hadoop requête et vague d’Apache ont cette possibilité, mais la prise en charge des HBase Apache type cluster est disponible pour le suivi. Pour plus d’informations, consultez gérer HDInsight clusters.</td>
<td>Service</td>
<td>Hadoop, vague d'</td>
<td>N/A</td>
</tr>

<tr>
<td>Outils Studio visuels</td>
<td>En plus des outils complète pour vague d’Apache, les outils d’Apache ruche dans Visual Studio a été mis à jour pour inclure saisie semi-automatique, la validation locale et prise en charge de débogage améliorée. Pour plus d’informations, voir prise en main HDInsight Hadoop outils pour Visual Studio.</td>
<td>Outils</td>
<td>Hadoop</td>
<td>N/A</td>
</tr>

<tr>
<td>Connecteur Hadoop pour DocumentDB</td>
<td>Avec le connecteur Hadoop pour DocumentDB, vous pouvez effectuer des agrégations complexes, l’analyse et manipulations sur vos documents JSON sans schéma de stockage provenant de plusieurs collections DocumentDB ou des comptes de base de données. Pour plus d’informations et un didacticiel, voir tâches Hadoop exécuter à l’aide de DocumentDB et HDInsight.</td>
<td>Service</td>
<td>Hadoop</td>
<td>N/A</td>
</tr>

<tr>
<td>Correctifs</td>
<td>Nous avons apporté différents correctifs secondaires pour les services HDInsight. Aucune modification de comportement des clients n’est prévue.</td>
<td>Service</td>
<td>Tous les</td>
<td>N/A</td>
</tr>

</table>
<br>

## <a name="notes-for-02062015-release-of-hdinsight"></a>Notes d’HDInsight de virtualisation 02/06/2015

Les numéros de version complète des clusters HDInsight déployés avec cette version :

* HDInsight 2.1.10.463.1325367 (1.3.9.0-01351 HDP - inchangée)
* HDInsight 3.0.6.463.1325367 (2.0.9.0-2097 HDP - inchangée)
* HDInsight 3.1.2.463.1325367 (HDP 2.1.10.0-2290)
* KIT DE DÉVELOPPEMENT LOGICIEL N/A

Cette version contient les mises à jour suivants.

<table border="1">
<tr>
<th>Titre</th>
<th>Description</th>
<th>Zone concerné (par exemple, Service, composant ou SDK)</p></th>
<th>Cluster Type (par exemple, Hadoop, HBase ou vague de)</th>
<th>JIRA (le cas échéant)</th>
</tr>


<tr>
<td>Correctifs</td>
<td>Nous avons apporté différents correctifs secondaires pour les services HDInsight. Aucune modification de comportement des clients n’est prévue.</td>
<td>Service</td>
<td>Tous les</td>
<td>N/A</td>
</tr>

<tr>
<td>Mise à jour de la maintenance HDP 2.1</td>
<td>3.1 HDInsight est mis à jour pour déployer HDP 2.1.10.0. Pour plus d’informations, voir <a href ="http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.1.10/bk_releasenotes_hdp_2.1/content/ch_relnotes-HDP-2.1.10.html" target="_blank">les Notes HDP-2.1.10</a>. </td>
<td>Logiciels libres</td>
<td>Tous les</td>
<td>N/A</td>
</tr>

<tr>
<td>Mises à jour binaires HDP</td>
<td>Il existe quelques fichiersJAR dans HBase pour les fichiers noms ont été mis à jour. Ces fichiers JAR sont utilisées en interne par HBase, afin que les clients ont une dépendance sur les noms de ces fichiers JAR n’est pas attendu. Parmi lesquels :
<ul>
<li>./lib/jetty-6.1.26.hwx.jar</li>
<li>./lib/jetty-sslengine-6.1.26.hwx.jar</li>
<li>./lib/jetty-util-6.1.26.hwx.jar</li>
</ul>
</td>
<td>Logiciels libres</td>
<td>HBase</td>
<td>N/A</td>
</tr>

</table>
<br>

## <a name="notes-for-1292015-release-of-hdinsight"></a>Notes d’HDInsight de virtualisation 29/1/2015

Les numéros de version complète des clusters HDInsight déployés avec cette version :

* HDInsight 2.1.10.455.1309616 (1.3.9.0-01351 HDP - inchangée)
* HDInsight 3.0.6.455.1309616 (2.0.9.0-2097 HDP - inchangée)
* HDInsight 3.1.2.455.1309616 (2.1.9.0-2196 HDP - inchangée)
* KIT DE DÉVELOPPEMENT LOGICIEL N/A

Cette version contient la mise à jour suivante.

<table border="1">
<tr>
<th>Titre</th>
<th>Description</th>
<th>Zone concerné (par exemple, Service, composant ou SDK)</p></th>
<th>Cluster Type (par exemple, Hadoop, HBase ou vague de)</th>
<th>JIRA (le cas échéant)</th>
</tr>


<tr>

<td>Correctifs</td>
<td>Nous avons apporté quelques importantes correctifs améliorer la fiabilité des groupes HDInsight Azure mises à niveau.</td>
<td>Service</td>
<td>Tous les</td>
<td>N/A</td>
</tr>



</table>
<br>

## <a name="notes-for-152015-release-of-hdinsight"></a>Notes d’HDInsight de virtualisation 1/5/2015

Les numéros de version complète des clusters HDInsight déployés avec cette version :

* HDInsight 2.1.10.420.1246118 (1.3.9.0-01351 HDP - inchangée)
* HDInsight 3.0.6.420.1246118 (2.0.9.0-2097 HDP - inchangée)
* HDInsight 3.1.2.420.1246118 (2.1.9.0-2196 HDP - inchangée)


Cette version contient les mises à jour suivants.

<table border="1">
<tr>
<th>Titre</th>
<th>Description</th>
<th>Composant</th>
<th>Type de cluster</th>
<th>JIRA (le cas échéant)</th>
</tr>


<tr>
<td>Exemples d’analyse des tendances Twitter et vidéo basé sur Mahout recommandation</td>
<td><p>Dans cette version, la console HDInsight requête comporte deux échantillons supplémentaires :</p>

<p><b>Analyse des tendances Twitter</b><br>
API publics fournis par sites semblables Twitter sont une source de données pour l’analyse et la compréhension des tendances populaires utile. Dans ce didacticiel, découvrez comment utiliser Hive pour obtenir une liste d’utilisateurs Twitter qui a envoyé les tweets la plupart des contenant un mot particulier. </p>

<p><b>Recommandation mahout vidéo</b><br>
Apache Mahout est une machine Apache Hadoop bibliothèque d’apprentissage. Mahout contient des algorithmes pour le traitement des données (par exemple, le filtrage, classement et cluster). Dans ce didacticiel, utilisez un moteur de recommandation pour générer des recommandations film basées sur les vidéos que vos amis ont vu.</p></td>
<td>Console de requête</td>
<td>Hadoop</td>
<td>N/A</td>
</tr>

<tr>
<td>Modifier la valeur par défaut pour une configuration Hive : hive.auto.convert.join.noconditionaltask.size</td>
<td><p>Cette configuration taille s’applique aux jointures carte automatiquement converties. La valeur représente la somme des tailles de tables qui peuvent être convertis en cartes hachage qui tient en mémoire. Dans une version antérieure, cette valeur est augmenté de la valeur par défaut de 10 Mo pour 128 Mo. Toutefois, la nouvelle valeur de 128 Mo a été à l’origine de travaux échec échéance manque de mémoire. Cette version rétablit la valeur par défaut à 10 Mo. Clients peuvent toujours choisir de remplacer cette valeur lors de la création de cluster, étant donné leurs requêtes et les tailles de tables. Pour plus d’informations sur ce paramètre et comment la substituer, voir <a href="http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.0.0.2/ds_Hive/optimize-joins.html#JoinOptimization-OptimizeAutoJoinConversion" target="_blank">Optimiser la Conversion automatique rejoindre</a> dans la documentation Hortonworks. </p></td>
<td>Hive</td>
<td>Hadoop, Hbase</td>
<td>N/A</td>
</tr>

</table>
<br>

## <a name="notes-for-12232014-release-of-hdinsight"></a>Notes d’HDInsight de virtualisation 23/12/2014

Les numéros de version complète des clusters HDInsight déployés avec cette version sont :

* HDInsight 2.1.10.420.1246783 (version HDP inchangée)
* HDInsight 3.0.6.420.1246783 (version HDP inchangée)
* HDInsight 3.1.1.420.1246783 (version HDP inchangée)

Cette version contient la mise à jour suivante.

<table border="1">
<tr>
<th>Titre</th>
<th>Description</th>
<th>Composant</th>
<th>Type de cluster</th>
<th>JIRA (le cas échéant)</th>
</tr>


<tr>
<td>Échecs de création de cluster intermittente en raison de charge excessive</td>
<td><p>Algorithme amélioré pour le téléchargement des packages HDP lors de la création de cluster Active la gestion plus performantes grâce d’échecs en raison de charge excessive.</p></td>
<td>Service</td>
<td>Vague d’Hadoop, Hbase,</td>
<td>N/A</td>
</tr>



</table>
<br>

## <a name="notes-for-12182014-release-of-hdinsight"></a>Notes d’HDInsight de virtualisation 18/12/2014

Cette version contient la mise à jour du composant suivant.

<table border="1">
<tr>
<th>Titre</th>
<th>Description</th>
<th>Composant</th>
<th>Type de cluster</th>
<th>JIRA (le cas échéant)</th>
</tr>

<tr>
<td><a href = "hdinsight-hadoop-customize-cluster.md" target="_blank">Personnalisation de cluster général Avalability</a></td>
<td><p>Personnalisation offre la possibilité que vous pouvez personnaliser vos clusters Azure HDInsight avec les projets qui sont disponibles sur le réseau Hadoop Apache. Avec cette nouvelle fonctionnalité, vous pouvez tester et déployer des projets Hadoop à Azure HDInsight. Cette option est activée via la fonctionnalité de **Script Action** , ce qui peut modifier Hadoop clusters de manière arbitraire à l’aide des scripts personnalisés. Cette personnalisation est disponible sur tous les types de clusters HDInsight Hadoop, HBase et vague d’inclus. Pour illustrer la puissance de cette fonctionnalité, nous avons présentées la procédure pour installer le populaires <a href = "hdinsight-hadoop-spark-install.md" target="_blank">explosion</a>, <a href = "hdinsight-hadoop-r-scripts.md" target="_blank">R</a>, <a href = "hdinsight-hadoop-solr-install.md" target="_blank">mode série sur LAN.r</a>et <a href = "hdinsight-hadoop-giraph-install.md" target="_blank">Giraph</a> modules. Cette version ajoute également la possibilité pour les clients spécifier l’action de script personnalisé via le portail Azure, fournit des instructions et des recommandations sur la façon de créer des actions de script personnalisé à l’aide des méthodes d’assistance et fournit des instructions sur la façon de tester l’action de script. </p></td>
<td>Disponibilité de la fonctionnalité général</td>
<td>Tous les</td>
<td>N/A</td>
</tr>


</table>
<br>

## <a name="notes-for-12052014-release-of-hdinsight"></a>Notes d’HDInsight de virtualisation 05/12/2014

Les numéros de version complète des clusters HDInsight déployés avec cette version sont :

* HDInsight 2.1.9.406.1221105 (HDP 1.3.9.0-01351)
* HDInsight 3.0.5.406.1221105 (HDP 2.0.9.0-2097)
* HDInsight 3.1.1.406.1221105 (HDP 2.1.9.0-2196)
* Kit de développement logiciel HDInsight n/a

Cette version contient les mises à jour du composant suivant.

<table border="1">
<tr>
<th>Titre</th>
<th>Description</th>
<th>Composant</th>
<th>Type de cluster</th>
<th>JIRA (le cas échéant)</th>
</tr>

<tr>
<td>Résolution du bogue : erreur intermittente lors de l’ajout d’un nombre important de partitions à une table dans une DDL ruche </td>
<td><p>S’il existe une erreur de connexion intermittents avec la base de données metastore Hive lorsque vous ajoutez un grand nombre de partitions à une table Hive, le DDL ruche peut échouer. L’instruction suivante s’affiche dans le journal des erreurs Hive si cette erreur se produit : </p><p>« Erreur [main] : ql. Pilote (SessionState.java:printError(547)) - a échoué : erreur d’exécution, code de retour 1 de org.apache.hadoop.hive.ql.exec.DDLTask. MetaException (message:java.lang.RuntimeException : commitTransaction était appelée mais openTransactionCalls = 0. Cela probablement indique qu’il existe des appels ajustées à openTransaction/commitTransaction) »</p></td>
<td>Hive</td>
<td>Hadoop, Hbase</td>
<td>HIVE-482 (c’est un JIRA interne, afin qu’il ne peut pas être mis entre guillemets avec l’extérieur. Noter ici pour référence.)</td>
</tr>

<tr>
<td>Résolution du bogue : occasionnel se bloquer dans la Console de requête HDInsight</td>
<td>Dans ce cas, l’instruction suivante peut être vues dans le journal WebHCat pour le travail Lanceur WebHCat : <p>« org.apache.hive.hcatalog.templeton.CatchallExceptionMapper | org.apache.hadoop.ipc.RemoteException(org.apache.hadoop.yarn.exceptions.YarnRuntimeException) : fichier d’historique {wasb url vers le fichier d’historique} n’a pas pu être charger »</p></td>
<td>WebHCat</td>
<td>Hadoop</td>
<td>HIVE-482 (c’est un JIRA interne, afin qu’il ne peut pas être mis entre guillemets avec l’extérieur. Noter ici pour référence.)</td>
</tr>

<tr>
<td>Résolution du bogue : occasionnels Pique-notes dans la latence des requêtes Hbase</td>
<td>Dans ce cas, les utilisateurs remarquerez un occasionnellement Pique-notes de 3 secondes dans la latence des requêtes Hbase. </td>
<td>HDInsight Cluster passerelle</td>
<td>HBase</td>
<td>N/A</td>
</tr>

<tr>
<td>Modification du nom de fichier JAR HDP</td>
<td>Pour les HDI cluster version 3.0, là quelques modifications apportées aux fichiers JAR internes installés par HDP. jetée 6.1.26.jar a été remplacé par jetée 6.1.26.hwx.jar. jetée-util-6.1.26.jar a été remplacé par jetée-util-6.1.26.hwx.jar. Ces modifications s’appliquent aux projets Hadoop, Mahout, WebHCat et Oozie.</td>
<td>Hadoop, Mahout, WebHCat, Oozie</td>
<td>Hadoop, HBase</td>
<td>N/A</td>
</tr>

</table>
<br>


## <a name="notes-for-11212014-release-of-hdinsight"></a>Notes de version 11/21/2014 de HDInsight

Les numéros de version complète des clusters HDInsight déployés avec cette version sont :

* HDInsight 2.1.9.382.1169709 (aucune modification 11/14/2014)
* HDInsight 3.0.5.382.1169709 (aucune modification 11/14/2014)
* HDInsight 3.1.1.382.1169709 (aucune modification 11/14/2014)
* Kit de développement logiciel HDINsight 1.4.0

Cette version contient les mises à jour du composant suivant.

<table border="1">
<tr>
<th>Titre</th>
<th>Description</th>
<th>Composant</th>
<th>Type de cluster</th>
<th>JIRA (le cas échéant)</th>
</tr>

<tr>
<td>L’accès aux journaux d’application</td>
<td>Possibilité pour énumérer par programme des applications qui ont été exécutées sur vos clusters et télécharger les journaux spécifiques à l’application ou spécifique au conteneur appropriés pour déboguer des applications posant problème.</td>
<td>KIT DE DÉVELOPPEMENT</td>
<td>Hadoop</td>
<td>N/A</td>
</tr>

<tr>
<td>Possibilité de spécifier le nom de la région dans IHdInsightClient.DeleteCluster </td>
<td>Le Kit de développement Azure HDInsight offre la possibilité de spécifier un nom de région lorsque vous utilisez **DeleteCluster**. Cela permet de débloquer les clients qui avaient deux personnes avec le même nom dans différentes régions et n’avaient pas pu supprimer une d’elles.</td>
<td>KIT DE DÉVELOPPEMENT</td>
<td>Tous les</td>
<td>N/A</td>
</tr>

<tr>
<td>ClusterDetails.DeploymentId</td>
<td>L’objet **ClusterDetails** renvoie un champ **DeploymentID** qui représente un identificateur unique pour le cluster. Elle est garantie pour être unique au sein de tentatives de création de cluster avec les mêmes noms.</td>
<td>KIT DE DÉVELOPPEMENT</td>
<td>Tous les</td>
<td>N/A</td>
</tr>
</table>
<br>

## <a name="notes-for-11142014-release-of-hdinsight"></a>Notes de version 11/14/2014 de HDInsight

Les numéros de version complète des clusters HDInsight déployés avec cette version sont :

* HDInsight 2.1.9.382.1169709
* HDInsight 3.0.5.382.1169709
* HDInsight 3.1.1.382.1169709

Cette version contient les nouvelles fonctionnalités suivantes, mises à jour de composant et correctifs.

<table border="1">
<tr>
<th>Titre</th>
<th>Description</th>
<th>Composant</th>
<th>Type de cluster</th>
<th>JIRA (le cas échéant)</th>
</tr>

<tr>
<td>Action de script (Preview)</td>
<td>Aperçu de la fonctionnalité de personnalisation cluster qui autorise la modification des Hadoop cluster de manière arbitraire à l’aide des scripts personnalisés. Grâce à cette fonctionnalité, les utilisateurs peuvent expérimenter et déployer des projets qui sont disponibles à partir du réseau Apache Hadoop aux clusters Azure HDInsight. Cette fonctionnalité de personnalisation est disponible sur tous les types de clusters HDInsight, y compris Hadoop, HBase et vague de.</td>
<td>Nouvelle fonctionnalité</td>
<td>Tous les</td>
<td>N/A</td>
</tr>

<tr>
<td>Analyse des fichiers journaux des travaux prédéfinis pour le stockage et les sites Web Azure</td>
<td>La Console de requête HDInsight a une galerie mise en route qui prend en charge les solutions qui fonctionnent sur vos données ou sur les exemples de données.
<p>**Solutions qui travailler sur vos données**:<br>
Nous avons créé travaux pour certains scénarios les plus courants analyse des données à fournir un point de départ pour créer vos propres solutions. Vous pouvez utiliser vos données avec la tâche pour voir comment elle fonctionne. Lorsque vous êtes prêt, utilisez ensuite ce que vous avez appris à créer une solution qui prend comme modèle le travail prédéfini.</p>
<p>**Solutions qui travailler sur des exemples de données**:<br>
Apprenez à manipuler HDInsight à l’étude de quelques scénarios de base (par exemple, l’analyse des journaux web et des données de capteur). Vous allez apprendre comment utiliser HDInsight pour analyser les données et comment vous pouvez vous connecter d’autres services et d’applications à ces données. Visualisation des données en vous connectant à Microsoft Excel fournit un exemple de la puissance de cette approche.</p></td>
<td>Console de requête</td>
<td>Hadoop</td>
<td>N/A</td>
</tr>

<tr>
<td>Correctif de perte de mémoire dans Templeton</td>
<td>Une perte de mémoire dans Templeton affectée clients qui avaient longues clusters ou ont été l’envoi de 100 s de tâche de demande qu'une seconde a été résolue. Le problème représenté sous Templeton 5xx erreurs et la solution de contournement a été redémarrer le service. La solution de contournement n’est plus nécessaire.</td>
<td>Templeton</td>
<td>Tous les</td>
<td>https://issues.Apache.org/jira/Browse/HADOOP-11248</td>
</tr>
</table>
<br>


**Remarque**: pour illustrer les nouvelles fonctionnalités mises à disposition par personnalisation cluster, les procédures d’installation modules explosion et R sur un cluster à l’aide d’une Action de Script ont été présentées. Pour plus d’informations, voir :

* [Installer et utiliser explosion 1.0 sur clusters HDInsight](hdinsight-hadoop-spark-install.md)
* [Installer et utiliser R sur HDInsight Hadoop clusters](hdinsight-hadoop-r-scripts.md)




## <a name="notes-for-11072014-release-of-hdinsight"></a>Notes de version 11/07/2014 de HDInsight

Les numéros de version complète des clusters HDInsight déployées avec cette version sont :

* HDInsight 2.1 2.1.9.374.1153876
* HDInsight 3.0 3.0.5.374.1153876
* HDInsight 3.1 3.1.1.374.1153876

Cette version contient les mises à jour du composant suivant.

<table border="1">
<tr>
<th>Titre</th>
<th>Description</th>
<th>Composant</th>
<th>Type de cluster</th>
<th>JIRA (le cas échéant)</th>
</tr>

<tr>
<td>HDP 2.1.7</td>
<td>Cette version est basée sur Hortonworks données plateforme (HDP) 2.1.7. Pour plus d’informations, voir les <a href="http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.1.7-Win/bk_releasenotes_HDP-Win/content/ch_relnotes-HDP-2.1.7.html" target="_blank">Notes de publication pour HDP 2.1.7</a>.</td>
<td>HDP</td>
<td>Tous les</td>
<td>N/A</td>
</tr>

<tr>
<td>FILS chronologie Server</td>
<td>Le serveur de chronologie fils (également connu sous le générique historique serveur d’applications) a été activé par défaut. Le serveur chronologie fournit des informations génériques à propos des applications complète (par exemple, ID de l’application, nom de l’application, état de l’application, heure de dépôt d’application et temps d’exécution d’application).

Ces informations application peuvent être récupérées du nœud de tête en accédant à l’URI http://headnodehost:8188 ou en exécutant la commande fils : fils application – appStates – la liste tous les.

Ces informations peuvent également être récupérées à distance si une API REST en https://{ClusterDnsName}. azurehdinsight.NET/ws/v1/applicationhistory/.

Pour plus d’informations, voir <a href="http://hadoop.apache.org/docs/r2.4.0/hadoop-yarn/hadoop-yarn-site/TimelineServer.html" target="_blank">Fils de barre de Planning Server</a>.</td>
<td>Service, fils</td>
<td>Hadoop, HBase</td>
<td>N/A</td>
</tr>

<tr>
<td>ID de déploiement du cluster</td>
<td>À partir du Kit de développement logiciel version 1.3.3.1.5426.29232, les utilisateurs peuvent accéder à un ID unique pour chaque cluster, ce qui est émis HDInsight. Cela permet aux clients d’identifier des instances uniques de clusters lorsqu’un nom DNS est réutilisé horizontalement créer ou supprimer des scénarios.</td>
<td>KIT DE DÉVELOPPEMENT</td>
<td>Tous les</td>
<td>N/A</td>
</tr>
</table>
<br>

**Remarque**: les bogues, empêchant le numéro de version complète à partir de s’affiche-t-elle pas dans le portail ou renvoyés par le Kit de développement ou par Windows PowerShell a été résolu dans cette version.

## <a name="notes-for-10152014-release"></a>Notes de version 15/10/2014

Cette version du correctif fixé à une perte de mémoire Templeton que les utilisateurs lourds de Templeton affectés. Dans certains cas, les utilisateurs qui exercer Templeton intensément seraient Voir erreurs représentés sous 500 codes d’erreur, car les demandes aurait pas assez de mémoire pour exécuter. La solution de contournement pour ce problème a été pour redémarrer le service Templeton. Ce problème a été résolu.


## <a name="notes-for-1072014-release"></a>Notes de version 7/10/2014

* Lors de l’utilisation de point de terminaison Ambari, « https://{clusterDns}.azurehdinsight.net/ambari/api/v1/clusters/{clusterDns}.azurehdinsight.net/services/{servicename}/components/{componentname} », le champ *host_name* renvoie le nom de domaine complet (FQDN) du nœud au lieu d’uniquement le nom d’hôte. Par exemple, au lieu de renvoyer «**headnode0**», vous obtenez le nom de domaine complet «**headnode0. {} ClusterDNS} .azurehdinsight .net**». Cette modification a été nécessaire pour faciliter des scénarios où plusieurs types de cluster (par exemple, HBase et Hadoop) peuvent être déployées dans un réseau virtuel. Cela se produit, par exemple, lorsque vous utilisez HBase comme une plateforme principale pour Hadoop.

* Nous vous fournissons nouveaux paramètres de la mémoire pour le déploiement par défaut du cluster HDInsight. Paramètres de mémoire précédent par défaut du ne pas correctement compte pour connaître les instructions pour le nombre de cœurs du processeur en cours de déploiement. Ces nouveaux paramètres de mémoire doivent fournir les meilleures valeurs par défaut (telles que Hortonworks recommandations). Pour apporter ces modifications, reportez-vous à la documentation de référence SDK sur la modification de la configuration du cluster. Les nouveaux paramètres de mémoire qui sont utilisées par le cluster par défaut 4 processeur core (conteneur 8) HDInsight sont répertoriés dans le tableau suivant. (Les valeurs utilisées avant cette version sont également fournis cités entre parenthèses ci-dessus).

<table border="1">
<tr><th>Composant</th><th>Allocation de mémoire</th></tr>
<tr><td> allocation de yarn.Scheduler.minimum</td><td>768 Mo (précédemment 512 Mo)</td></tr>
<tr><td> allocation de yarn.Scheduler.maximum</td><td>Bande 6144 Mo (inchangé)</td></tr>
<tr><td>yarn.NodeManager.Resource.Memory</td><td>Bande 6144 Mo (inchangé)</td></tr>
<tr><td>MapReduce.Map.Memory</td><td>768 Mo (précédemment 512 Mo)</td></tr>
<tr><td>MapReduce.Map.Java.OPTS</td><td>choisit =-Xmx512m (précédemment - Xmx410m)</td></tr>
<tr><td>MapReduce.reduce.Memory</td><td>1 536 Mo (précédemment 1024 Mo)</td></tr>
<tr><td>MapReduce.reduce.Java.OPTS</td><td>choisit =-Xmx1024m (précédemment - Xmx819m)</td></tr>
<tr><td>yarn.app.MapReduce.AM.Resource</td><td>768 Mo (précédemment 1024 Mo)</td></tr>
<tr><td>yarn.app.MapReduce.AM.Command</td><td>choisit =-Xmx512m (précédemment - Xmx819m)</td></tr>
<tr><td>MapReduce.Task.IO.Sort</td><td>256 Mo (précédemment 200 Mo)</td></tr>
<tr><td>tez.AM.Resource.Memory</td><td>1 536 Mo (inchangé)</td></tr>

</table><br>

Pour plus d’informations sur les paramètres de configuration de mémoire utilisés par fils et MapReduce sur la plateforme de données Hortonworks qui est utilisé par HDInsight, voir [Déterminer les paramètres de Configuration de la mémoire de HDP](http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.1-latest/bk_installing_manually_book/content/rpm-chap1-11.html). Hortonworks également un outil pour calculer les paramètres de la mémoire appropriée.

En ce qui concerne la session PowerShell Azure et le message d’erreur HDInsight SDK : «*Cluster n’est pas configuré pour l’accès aux services HTTP*» :

* Cette erreur est un [problème de compatibilité](https://social.msdn.microsoft.com/Forums/azure/a7de016d-8de1-4385-b89e-d2e7a1a9d927/hdinsight-powershellsdk-error-cluster-is-not-configured-for-http-services-access?forum=hdinsight) connu pouvant se produire en raison d’une différence de la version de la HDInsight SDK ou PowerShell Azure et la version du cluster. Clusters créés sur 15/8 ou version ultérieure ont prise en charge de la nouvelle fonctionnalité de mise à disponibilité dans les réseaux virtuels. Mais cette fonctionnalité n’est pas correctement interprétée par des versions antérieures des PowerShell Azure HDInsight SDK. Le résultat est une défaillance dans certaines opérations de soumission la tâche. Si vous utilisez des API SDK HDInsight ou des applets de commande PowerShell Azure (**Utilisation AzureRmHDInsightCluster** ou **Appeler AzureRmHDInsightHiveJob**) à soumettre des tâches, ces opérations peuvent échouer avec le message d’erreur «*Cluster <clustername> n’est pas configuré pour l’accès aux services HTTP*. » Ou (selon l’opération), vous pouvez obtenir d’autres messages d’erreur, par exemple «*Impossible de se connecter au cluster*».

* Ces problèmes de compatibilité sont résolus dans les versions plus récentes des HDInsight SDK et Azure PowerShell. Nous vous recommandons de mettre à jour le HDInsight SDK version 1.3.1.6 ou une version ultérieure et Azure outils PowerShell vers la version 0.8.8 ou version ultérieure. Vous pouvez accéder à la dernière SDK HDInsight à partir de [](http://nuget.codeplex.com/wikipage?title=Getting%20Started) et les outils de PowerShell Azure comment [installer et configurer Azure PowerShell](../powershell-install-configure.md).



## <a name="notes-for-9122014-release-of-hdinsight-31"></a>Notes 9/12/2014 version 3.1 HDInsight

* Cette version est basée sur Hortonworks données plateforme (HDP) 2.1.5. Pour obtenir la liste des bogues résolus dans cette version, consultez la page [fixe dans cette version](http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.1.5/bk_releasenotes_hdp_2.1/content/ch_relnotes-hdp-2.1.5-fixed.html) sur le site Hortonworks.
* Dans le dossier de bibliothèques cochon, le fichier « avro-mapred-1.7.4.jar » a été modifié pour « avro-mapred-1.7.4-hadoop2.jar. » Le contenu de ce fichier contient un correctif bogue mineur qui est insécable. Il est recommandé que les clients ne pas émettre d’une dépendance directe sur le nom du fichier JAR pour éviter les sauts lorsque les fichiers sont renommés.


## <a name="notes-for-8212014-release"></a>Notes de publication 21/8/2014

* Nous ajoutons la configuration WebHCat suivante (HIVE-7155), qui définit la limite de mémoire par défaut pour une tâche de contrôleur Templeton à 1 Go. (La valeur par défaut précédente était 512 Mo).

     templeton.mapper.Memory.MB (= 1024)

    * Cette modification concerne l’erreur suivante dont certaines requêtes Hive avaient exécuté en raison des limites de mémoire inférieurs : « Conteneur est en cours d’exécution au-delà des limites de mémoire physique. »
    * Pour rétablir les paramètres par défaut anciens, vous pouvez définir cette valeur sur 512 via PowerShell Azure au moment de la création de cluster à l’aide de la commande suivante :

        AzureRmHDInsightConfigValues ajouter-Core@{"templeton.mapper.memory.mb"="512";}


* Le nom d’hôte du rôle soigneur est passé à *soigneur*. Cela affecte la résolution de noms au sein du cluster, mais elle n’affecte pas API REST externes. Si vous avez des composants qui utilisent le nom d’hôte *zookeepernode* , vous devez mettre à jour pour utiliser le nouveau nom. Les nouveaux noms pour les nœuds soigneur trois sont :
    * zookeeper0
    * zookeeper1
    * zookeeper2
* Matrice de prise en charge des versions HBase est mis à jour. Uniquement HDInsight version 3.1 (HBase version 0,98) est prise en charge de production pour HBase les charges de travail. Version 3.0 (qui n’est accessible pour preview) n'est pas prise en charge déplacement vers l’avant.

## <a name="notes-about-clusters-created-prior-to-8152014"></a>Remarques sur les clusters créés avant la 8/15/2014

Un message d’erreur Azure PowerShell ou HDInsight SDK « Cluster <clustername> n’est pas configuré pour l’accès aux services HTTP » (ou en fonction de l’opération, d’autres messages d’erreur tel que : « Impossible de se connecter au cluster ») peuvent être rencontrées en raison d’une différence de version entre Azure PowerShell ou le HDInsight SDK et un cluster. Clusters créés sur 15/8 ou version ultérieure ont prise en charge de la nouvelle fonctionnalité de mise à disponibilité dans les réseaux virtuels. Cette fonctionnalité n’est pas correctement interprétée par des versions antérieures de la session PowerShell Azure ou le SDK HDInsight, qui se traduit par des échecs d’opérations de soumission la tâche. Si vous utilisez HDInsight SDK API ou PowerShell Azure applets de commande (par exemple, utilisez AzureRmHDInsightCluster ou appeler AzureRmHDInsightHiveJob) à soumettre des tâches, ces opérations peuvent échouer avec l’un des messages d’erreur décrits.

Ces problèmes de compatibilité sont résolus dans les versions plus récentes des HDInsight SDK et Azure PowerShell. Nous vous recommandons de mettre à jour le HDInsight SDK version 1.3.1.6 ou une version ultérieure et Azure outils PowerShell vers la version 0.8.8 ou version ultérieure. Vous pouvez accéder au SDK HDInsight plus récente à partir de [NuGet][nuget-link]. Vous pouvez accéder aux outils PowerShell Azure à l’aide de [Microsoft Web Platform Installer][webpi-link].


## <a name="notes-for-7282014-release"></a>Notes de version 7/28/2014

* **HDInsight disponible dans les nouvelles zones**: nous développé présence géographique HDInsight à trois zones. Clients HDInsight pouvant créer des clusters dans ces régions :
    * Asie de l’est
    * États-Unis centre nord
    * États-Unis centre sud
* HDInsight version 1,6 (HDP 1.1 et Hadoop 1.0.3) et HDInsight version 2.1 (HDP1.3 et Hadoop 1.2) sont supprimées à partir du portail Azure. Vous pouvez continuer à créer des clusters Hadoop pour ces versions à l’aide de l’applet de commande PowerShell Azure, [Nouveau AzureRmHDInsightCluster](http://msdn.microsoft.com/library/dn593744.aspx) ou en utilisant le [Kit de développement logiciel HDInsight](http://msdn.microsoft.com/library/azure/dn469975.aspx). Reportez-vous à la page de [contrôle de version HDInsight composant](hdinsight-component-versioning.md) pour plus d’informations.
* Modifications de la plateforme de données Hortonworks (HDP) dans cette version :

<table border="1">
<tr><th>HDP</th><th>Modifications</th></tr>
<tr><td>HDP 1.3 / HDI 2.1</td><td>Aucune modification</td></tr>
<tr><td>HDP 2.0 / HDI 3.0</td><td>Aucune modification</td></tr>
<tr><td>HDP 2.1 / HDI 3.1</td><td>soigneur : ['3.4.5.2.1.3.0-1948'] -> ['3.4.5.2.1.3.2-0002']</td></tr>


</table><br>

## <a name="notes-for-6242014-release"></a>Notes de publication 24/6/2014

Cette version contient des améliorations au service HDInsight :

* **Disponibilité HDP 2.1**: 3.1 HDInsight (qui contient HDP 2.1) est généralement disponibles et la version par défaut pour les nouveaux clusters.
* **HBase – améliorations portails Azure**: nous faisons HBase clusters disponibles dans l’aperçu. Vous pouvez créer HBase clusters à partir du portail en quelques clics. 

Avec HBase, vous pouvez créer une variété de charges de travail en temps réel sur HDInsight, des sites Web interactifs qui fonctionnent avec les grands ensembles de données aux services le stockage de données capteur et télémétrie provenant de millions de points de terminaison. L’étape suivante serait pour analyser les données de ces charges de travail avec les tâches Hadoop, et cela est possible dans HDInsight via PowerShell Azure et le tableau de bord cluster Hive.

### <a name="apache-mahout-preinstalled-on-hdinsight-31"></a>Mahout Apache préinstallé sur 3.1 HDInsight

 [Mahout](http://hortonworks.com/hadoop/mahout/) est préinstallé sur clusters HDInsight 3.1 Hadoop, afin de pouvoir exécuter les tâches, Mahout sans qu’il soit nécessaire pour une configuration cluster supplémentaires. Par exemple, vous pouvez à distance dans un cluster Hadoop à l’aide du protocole RDP (Remote Desktop) et sans des étapes supplémentaires, vous pouvez exécuter la commande Hello World Mahout suivante :

        mahout org.apache.mahout.classifier.df.tools.Describe -p /user/hdp/glass.data -f /user/hdp/glass.info -d I 9 N L  

        mahout org.apache.mahout.classifier.df.BreimanExample -d /user/hdp/glass.data -ds /user/hdp/glass.info -i 10 -t 100

Pour obtenir une explication plus complète de cette procédure, voir la documentation de l' [Exemple Breiman](https://mahout.apache.org/users/classification/breiman-example.html) sur le site Web Apache Mahout.


### <a name="hive-queries-can-use-tez-in-hdinsight-31"></a>Requêtes Hive utilisables Tez dans 3.1 HDInsight

Hive 0,13 est disponible dans HDInsight 3.1, et il est en mesure de l’exécution des requêtes à l’aide de Tez, qui peut être exploitée pour améliorer de manière significative les performances.
Tez n’est pas activer par défaut pour les requêtes Hive. Pour l’utiliser, vous devez choisir. Vous pouvez activer Tez en exécutant l’extrait de code suivantes :

        set hive.execution.engine=tez;
        select sc_status, count(*), histogram_numeric(sc_bytes,5) from website_logs_orc_local group by sc_status;

Hortonworks a publié une description détaillée des améliorations des performances de requête Hive avec Tez comme remis dans les tests d’évaluation standards. Pour plus d’informations, voir [analyse comparative des 13 ruche Apache pour Enterprise Hadoop](http://hortonworks.com/blog/benchmarking-apache-hive-13-enterprise-hadoop/).

Pour plus d’informations sur l’utilisation de Hive avec Tez, voir [ruche sur Tez](https://cwiki.apache.org/confluence/display/Hive/Hive+on+Tez).

###<a name="global-availability"></a>Disponibilité globale
Dans la version de HDInsight sur 2.2 Hadoop, Microsoft a été HDInsight disponible dans toutes les principales zones géographiques où Azure est disponible. Plus précisément, ouest Europe et Asie du Sud-est centres de données ont été mis en ligne. Cela permet aux clients localiser clusters dans un centre de données près et potentiellement dans une zone de similaires aux exigences de conformité.


###<a name="dos--donts-between-cluster-versions"></a>Faire & n’entre les versions cluster

**Metastores Oozie utilisés avec un cluster 3.1 HDInsight ne sont pas compatibles avec les clusters HDInsight 2.1, et elles ne peuvent pas être utilisés avec cette version précédente**.

Une base de données de metastore Oozie personnalisé déployée avec un cluster 3.1 HDInsight ne peut pas être réutilisé avec un cluster 2.1 HDInsight. C’est le cas même si le metastore a été créée avec un cluster 2.1 HDInsight. Ce scénario n’est pas pris en charge, car le schéma metastore mis à jour lorsqu’il est utilisé avec un cluster 3.1 HDInsight, afin qu’il n’est plus compatible avec la metastore requis par les groupes 2.1 HDInsight. Toute tentative de réutiliser un metastore Oozie qui a été utilisé avec un cluster 3.1 HDInsight rend le cluster HDInsight 2.1 inutiles.

**Oozie metastores ne peut pas être partagé entre clusters.**

Oozie metastores sont joints à clusters spécifiques, et qu’ils ne peuvent pas être partagés entre clusters.

###<a name="breaking-changes"></a>Annulation de modifications

**Syntaxe du préfixe**: uniquement les « wasbs : / / « syntaxe est prise en charge dans HDInsight 3.1 et 3.0 clusters. L’ancien « asv : / / « syntaxe est prise en charge dans HDInsight 2.1 et 1,6 clusters, mais il n’est pas pris en charge dans HDInsight 3.1 ou 3.0 groupes. Cela signifie que toutes les tâches soumis à une 3.1 HDInsight ou cluster 3.0 utilisent de manière explicite les « asv : / / « syntaxe échouera. Le « wasbs : / / « syntaxe doit être utilisé à la place. En outre, les travaux soumis à n’importe quel 3.1 HDInsight ou 3.0 groupes qui sont créés avec un metastore existant qui contient des références explicites aux ressources à l’aide de la « asv : / / « syntaxe échouera. Ces metastores doivent être recréés à l’aide de la « wasbs : / / « syntaxe pour les ressources d’adresse.


**Ports**: les ports utilisés par le service HDInsight ont changé. Les numéros de port qui ont été utilisés ont été au sein de la plage de ports éphémères du système d’exploitation Windows. Ports sont affectés automatiquement à partir d’une plage éphémère prédéfinie pour les communications basées sur le protocole courtes Internet. La nouvelle série de numéros de port de service plateforme de données Hortonworks (HDP) autorisés sont en dehors de cette plage pour éviter de rencontrer des conflits qui pourraient se poser avec les ports utilisés par les services qui s’exécutent sur le nœud principal. Les nouveaux numéros de port n’entraînent pas les modifications de dernière minute. Les numéros utilisés sont les suivants :

 **HDInsight 1,6 (HDP 1.1)**
<table border="1">
<tr><th>Nom</th><th>Valeur</th></tr>
<tr><td>DFS.http.Address</td><td>namenodehost:30070</td></tr>
<tr><td>DFS.datanode.Address</td><td>0.0.0.0:30010</td></tr>
<tr><td>DFS.datanode.http.Address</td><td>0.0.0.0:30075</td></tr>
<tr><td>DFS.datanode.IPC.Address</td><td>0.0.0.0:30020</td></tr>
<tr><td>DFS.Secondary.http.Address</td><td>0.0.0.0:30090</td></tr>
<tr><td>mapred.job.Tracker.http.Address</td><td>jobtrackerhost:30030</td></tr>
<tr><td>mapred.Task.Tracker.http.Address</td><td>0.0.0.0:30060</td></tr>
<tr><td>MapReduce.History.Server.http.Address</td><td>0.0.0.0:31111</td></tr>
<tr><td>templeton.port</td><td>30111</td></tr>
</table><br>

 **HDInsight 3.1 et 3.0 (HDP 2.1 et 2.0)**
<table border="1">
<tr><th>Nom</th><th>Valeur</th></tr>
<tr><td>adresse de DFS.namenode.http</td><td>namenodehost:30070</td></tr>
<tr><td>adresse de DFS.namenode.HTTPS</td><td>headnodehost:30470</td></tr>
<tr><td>DFS.datanode.Address</td><td>0.0.0.0:30010</td></tr>
<tr><td>DFS.datanode.http.Address</td><td>0.0.0.0:30075</td></tr>
<tr><td>DFS.datanode.IPC.Address</td><td>0.0.0.0:30020</td></tr>
<tr><td>adresse de DFS.namenode.Secondary.http</td><td>0.0.0.0:30090</td></tr>
<tr><td>yarn.NodeManager.WebApp.Address</td><td>0.0.0.0:30060</td></tr>
<tr><td>templeton.port</td><td>30111</td></tr>
</table><br>

###<a name="dependencies"></a>Dépendances

Les dépendances suivantes ont été ajoutés dans HDInsight 3.x (HDP2.x) :

* guice servlet
* cœur optiq
* javax.inject
* activation
* jsr305
* geronimo-jaspic_1.0_spec
* juil-slf4j
* Java xmlbuilder
* ant
* Commons compilateur
* api jdo
* Commons math3
* paranamer
* implémentation de la jaxb
* stringtemplate
* eigenbase xom
* Jersey servlet
* Commons exécution
* api jaxb
* jetée-all-serveur
* janino
* xercesImpl
* optiq avatica
* jta
* propriétés d’eigenbase
* une super-all
* cœur hamcrest
* courrier
* linq4j
* jpam
* Jersey client
* aopalliance
* geronimo-annotation_1.0_spec
* Lanceur d’ant
* Jersey guice
* API XML
* api stax
* ASM commons
* ASM arborescence
* wadl
* geronimo-jta_1.1_spec
* guice
* tous les leveldbjni
* vitesse
* larguer
* rapide java
* jetée-all
* Commons dbcp

Les dépendances suivantes ne sont plus présents dans HDInsight 3.x (HDP2.x) :

* jdeb
* KFS
* SQLUltraLite
* lierre
* aspectjrt
* JSON
* principaux
* api jdo2
* avro mapred
* datanucleus-dispositif d’amélioration
* JSP
* api de journalisation Commons
* Commons mathématiques
* JavaEWAH
* aspectjtools
* javolution
* hdfsproxy
* hbase
* rapide

###<a name="version-changes"></a>Modifications de version

Les modifications de version suivantes ont été apportées entre HDInsight 2.x (HDP1.x) et HDInsight 3.x (HDP2.x) :

* les mesures de cœur : ['2.1.2'] -> ['3.0.0']
* derbynet : ['10.4.2.0'] -> ['10.10.1.1']
* datanucleus : [' SGBDR-3.0.8'] -> [' SGBDR-3.2.9']
* JASPER compilateur : ['5.5.12'] -> ['5.5.23']
* log4j : ['1.2.15', '1.2.16'] -> ['1.2.16', '1.2.17']
* derbyclient : ['10.4.2.0'] -> ['10.10.1.1']
* httpcore : ['4.2.4'] -> ['4.2.5']
* hsqldb : ['1.8.0.10'] -> ['2.0.0']
* jets3t : ['réglementaires 0.6.1'] -> ['0.9.0']
* protobuf java : ['2.4.1'] -> ['2.5.0']
* Derby : ['10.4.2.0'] -> ['10.10.1.1']
* JASPER : [' exécution-5.5.12'] -> [' exécution-5.5.23']
* processus de Commons : ['1.0.1'] -> ['1.0.13']
* cœur datanucleus : ['3.0.9'] -> ['3.2.10']
* datanucleus-api-jdo : ['3.0.7'] -> ['3.2.6']
* soigneur : ['3.4.5.1.3.9.0-01320'] -> ['3.4.5.2.1.3.0-1948']
* bonecp : ['0.7.1.RELEASE'] -> ['
* 0.8.0.RELEASE']


### <a name="drivers"></a>Axes stratégiques
Le pilote Connnectivity de base de données Java (JDBC) pour SQL Server est utilisé en interne par HDInsight et n’est pas utilisé pour les opérations externes. Si vous voulez vous connecter à HDInsight à l’aide de base de données ODBC (Open Connectivity), utilisez le pilote ODBC ruche Microsoft. Pour plus d’informations, voir [Connecter Excel à HDInsight avec le pilote ODBC ruche de Microsoft](hdinsight-connect-excel-hive-odbc-driver.md).


### <a name="bug-fixes"></a>Correctifs

Dans cette version, nous avons actualisé les versions suivantes HDInsight avec plusieurs correctifs :

* HDInsight 2.1 (HDP 1.3)
* HDInsight 3.0 (HDP 2.0)
* HDInsight 3.1 (HDP 2.1)


## <a name="hortonworks-release-notes"></a>Notes de publication Hortonworks

Notes de publication pour les plateformes de données Hortonworks (HDPs) qui sont utilisés par les clusters de version HDInsight sont disponibles aux emplacements suivants :

* HDInsight version 3.1 utilise une distribution Hadoop basée sur la [Plateforme de données Hortonworks 2.1.7][hdp-2-1-7]. Il s’agit le cluster Hadoop par défaut créé lors de l’utilisation du portail Azure après 7/11/2014. 3.1 HDInsight clusters créés avant la 7/11/2014 ont été basés sur la [Plateforme de données Hortonworks 2.1.1][hdp-2-1-1]

* HDInsight version 3.0 utilise une distribution Hadoop basé sur le [Hortonworks données plateforme 2.0][hdp-2-0-8].

* HDInsight version 2.1 utilise une distribution Hadoop basée sur la [Hortonworks données plateforme 1.3][hdp-1-3-0].

* HDInsight version 1,6 utilise une distribution Hadoop basé sur le [Hortonworks données plateforme 1.1][hdp-1-1-0].

[hdp-2-1-7]: http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.1.7-Win/bk_releasenotes_HDP-Win/content/ch_relnotes-HDP-2.1.7.html

[hdp-2-1-1]: http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.1.1/bk_releasenotes_hdp_2.1/content/ch_relnotes-hdp-2.1.1.html

[hdp-2-0-8]: http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.0.8.0/bk_releasenotes_hdp_2.0/content/ch_relnotes-hdp2.0.8.0.html

[hdp-1-3-0]: http://docs.hortonworks.com/HDPDocuments/HDP1/HDP-1.3.0/bk_releasenotes_hdp_1.x/content/ch_relnotes-hdp1.3.0_1.html

[hdp-1-1-0]: http://docs.hortonworks.com/HDPDocuments/HDP1/HDP-Win-1.1/bk_releasenotes_HDP-Win/content/ch_relnotes-hdp-win-1.1.0_1.html

[nuget-link]: https://www.nuget.org/packages/Microsoft.WindowsAzure.Management.HDInsight/

[webpi-link]: http://go.microsoft.com/?linkid=9811175&clcid=0x409

[hdinsight-install-spark]: ../hdinsight-hadoop-spark-install/
[hdinsight-r-scripts]: ../hdinsight-hadoop-r-scripts/
 

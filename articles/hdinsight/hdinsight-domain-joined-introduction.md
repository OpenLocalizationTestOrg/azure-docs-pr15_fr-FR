<properties
    pageTitle="Sécuriser vue d’ensemble HDInsight | Microsoft Azure"
    description="Obtenir des informations..."
    services="hdinsight"
    documentationCenter=""
    authors="saurinsh"
    manager="jhubbard"
    editor="cgronlun"
    tags="azure-portal"/>

<tags
    ms.service="hdinsight"
    ms.devlang="na"
    ms.topic="hero-article"
    ms.tgt_pltfrm="na"
    ms.workload="big-data"
    ms.date="10/24/2016"
    ms.author="saurinsh"/>

# <a name="introduce-domain-joined-hdinsight-clusters-preview"></a>Introduire clusters HDInsight à un domaine (Preview)

Azure HDInsight jusqu'à ce qu’aujourd'hui pris en charge uniquement un administrateur local seul utilisateur. Cela a fonctionné parfaitement sur plus petites équipes d’application ou les services. Hadoop en fonction de charges de travail acquises popularité plus dans le secteur de l’entreprise, qu’il soit nécessaire pour les capacités de niveau entreprise comme active directory basé l’authentification, prise en charge multi-utilisateurs et contrôle d’accès basé sur un rôle est devenu plus important. Utilisez des clusters HDInsight à un domaine, vous pouvez créer un cluster HDInsight joint à un domaine Active Directory, configurez une liste d’employés de l’entreprise qui peuvent s’authentifier via Azure Active Directory pour vous connecter à HDInsight cluster. Tout le monde à l’extérieur de l’entreprise ne peut pas se connecter ou d’accéder au cluster HDInsight. L’administrateur d’entreprise peut configurer le contrôle d’accès basé sur les rôles de sécurité Hive à l’aide de [Apache Ranger](http://hortonworks.com/apache/ranger/), et donc limiter l’accès aux données seulement autant que nécessaire. Enfin, l’administrateur peut auditer l’accès aux données par des employés et toute modification apportée aux stratégies de contrôle d’accès, d'où un haut degré de gouvernance de ressources de l’entreprise.

[AZURE.NOTE]> Les nouvelles fonctionnalités décrites dans cet aperçu sont disponibles uniquement sur les clusters basés sur Linux HDInsight pour la charge de travail Hive. Les autres charges de travail, tels que HBase, explosion, vague d’et Kafka, seront activés dans les versions ultérieures. 

## <a name="benefits"></a>Avantages

Sécurité de l’entreprise contient quatre piliers volumineux – sécurité du périmètre, l’authentification, l’autorisation et le chiffrement.

![Domaine rejoint HDInsight clusters piliers avantages](./media/hdinsight-domain-joined-introduction/hdinsight-domain-joined-four-pillars.png).

### <a name="perimeter-security"></a>Sécurité du périmètre

Sécurité du périmètre dans HDInsight est obtenue à l’aide de réseaux virtuels et le service passerelle. Aujourd'hui, un administrateur d’entreprise peut créer un cluster HDInsight à l’intérieur d’un réseau virtuel et utiliser des groupes de sécurité réseau (règles de pare-feu entrants ou sortants) pour limiter l’accès au réseau virtuel. Seules les adresses IP définis dans les règles de pare-feu entrantes seront en mesure de communiquer avec le cluster HDInsight, et donc la sécurisation du périmètre des formes. Une autre couche de sécurité du périmètre est obtenue à l’aide du service passerelle. La passerelle est le service qui sert de première ligne de défense pour toute demande entrant pour le cluster HDInsight. Il accepte la demande, qu’il valide et uniquement puis autorise la demande passer à d’autres nœuds dans un cluster, fournissent ainsi la sécurité du périmètre à d’autres nœuds de nom et les données dans le cluster.

### <a name="authentication"></a>Authentification

Avec cette version d’évaluation, un administrateur d’entreprise peut configurer un cluster HDInsight à un domaine, dans un [réseau virtuel](https://azure.microsoft.com/services/virtual-network/). Les nœuds du cluster HDInsight seront joint au domaine géré par l’entreprise. Pour ce faire à l’aide de [Azure Active Directory Domain Services](https://technet.microsoft.com/library/cc770946.aspx). Tous les nœuds dans le cluster sont joints à un domaine qui gère l’entreprise. Avec ce programme d’installation, les employés entreprise peuvent vous connecter aux nœuds du cluster à l’aide de leurs informations d’identification de domaine. Qu’ils peuvent également utiliser leurs informations d’identification de domaine pour vous authentifier avec d’autres points de terminaison approuvés, tels que teinte affichages Ambari, ODBC, JDBC, PowerShell et API REST pour interagir avec le cluster. L’administrateur a un contrôle total sur limitant le nombre d’utilisateurs qui interagissent avec le cluster via ces points de terminaison.

### <a name="authorization"></a>Autorisation

Une meilleure pratique suivie de la plupart des entreprises est que pas chaque employé ait accès à toutes les ressources d’entreprise. De même, avec cette version, l’administrateur peut définir des stratégies de contrôle d’accès basé sur les rôles pour les ressources de cluster. Par exemple, l’administrateur peut configurer [Apache Ranger](http://hortonworks.com/apache/ranger/) pour définir des stratégies de contrôle d’accès pour Hive. Cette fonctionnalité garantit que les employés seront en mesure d’accéder uniquement autant de données dont elles ont besoin pour réussir dans leur travail. AccèsSSH au cluster est également limité uniquement à l’administrateur.


### <a name="auditing"></a>L’audit

Ainsi que de protéger les ressources de cluster HDInsight des utilisateurs non autorisés et protéger les données, de tous les accès aux ressources cluster et les données d’audit est nécessaire pour effectuer le suivi de l’accès non autorisé ou accidentel des ressources. Avec cet aperçu, l’administrateur peut afficher et signaler tout accès aux ressources de cluster HDInsight et aux données. L’administrateur peut également afficher et de consigner toutes les modifications pour les stratégies de contrôle d’accès dans les points de terminaison Ranger Apache pris en charge. Un cluster HDInsight à un domaine utilise l’interface utilisateur de familiers Apache Ranger pour effectuer une recherche dans les journaux d’audit. Sur le serveur principal, Ranger utilise [Mode série sur LAN.r Apache]( http://hortonworks.com/apache/solr/) pour stocker et rechercher les journaux.

### <a name="encryption"></a>Chiffrement

Protection des données est importante pour la sécurité de l’organisation réunion et aux exigences de conformité, et ainsi que de limiter l’accès aux données des employés non autorisés, il doit également être sécurisé en la chiffrant. Les banques de données pour clusters, Azure stockage Blob et stockage Lake des données Azure HDInsight prend en charge côté serveur transparent [le chiffrement des données](../storage/storage-service-encryption.md) au repos. Sécurisé HDInsight clusters fonctionne en toute transparence avec ce chiffrement côté serveur de données en fonction du reste.

## <a name="next-steps"></a>Étapes suivantes

- Pour configurer un cluster HDInsight à un domaine, consultez [clusters HDInsight configurer à un domaine](hdinsight-domain-joined-configure.md).
- Pour gérer un clusters HDInsight à un domaine, voir [clusters HDInsight gérer à un domaine](hdinsight-domain-joined-manage.md).
- Pour configurer des stratégies Hive et exécuter des requêtes Hive, consultez [configurer ruche stratégies pour les clusters HDInsight à un domaine](hdinsight-domain-joined-run-hive.md).
- Pour l’exécution des requêtes Hive à l’aide de SSH sur clusters HDInsight à un domaine, voir [Utiliser SSH avec basé sur Linux Hadoop sur HDInsight de Linux, Unix ou OS X](hdinsight-hadoop-linux-use-ssh-unix.md#connect-to-a-domain-joined-hdinsight-cluster).
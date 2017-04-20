<properties
    pageTitle="Plateformes Analytique : comparaison Apache vague à flux Analytique | Microsoft Azure"
    description="Obtenez des conseils en choisissant une plateforme analytique cloud à l’aide d’une comparaison Apache vague à flux Analytique. Comprendre les fonctionnalités et les différences."
    keywords="plateforme analytique, plateformes analytique, plateforme analytique en nuage, comparaison vague"
    services="stream-analytics"
    documentationCenter=""
    authors="jeffstokes72"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="stream-analytics"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="big-data"
    ms.date="09/26/2016"
    ms.author="jeffstok"/>

# <a name="help-choosing-a-streaming-analytics-platform-apache-storm-comparison-to-azure-stream-analytics"></a>Aide en choisissant une plateforme analytique en continu : comparaison Apache vague à Azure flux Analytique

Obtenez des conseils en choisissant une plateforme analytique cloud à l’aide de cette comparaison Apache vague à Azure flux Analytique. Comprendre les exemples d’utilisation de la valeur ajoutée de flux Analytique et Apache vague d’un service géré sur Azure HDInsight, afin que vous pouvez choisir la solution idéale pour votre entreprise.

Les deux plateformes analytique offrent des avantages d’une solution PaaS, il existe quelques fonctionnalités majeures distinctives qui les différencient. Fonctionnalités ainsi que les limites de ces services sont présentées ci-après afin de vous placer sur la solution que vous avez besoin pour atteindre vos objectifs.

## <a name="storm-comparison-to-stream-analytics-general-features"></a>Renverser comparaison par rapport au flux Analytique : fonctions générales ##
<table border="1" cellspacing="0" cellpadding="0">
    <tbody>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong> </strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
                    <strong>Flux de données Azure Analytique</strong>
                </p>
            </td>
            <td width="246" valign="top">
                <p>
                    <strong>Vague d’Apache sur HDInsight</strong>
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Ouvrir la Source</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Non, Azure flux Analytique est un propriétaire Microsoft offrant.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
Oui, vague d’Apache est une technologie Apache sous licence.
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Microsoft pris en charge</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Oui </p>
            </td>
            <td width="246" valign="top">
                <p>
Oui </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Configuration matérielle requise</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Il n’existe aucune configuration matérielle requise. Azure Analytique de flux de données est un Service Azure.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
Il n’existe aucune configuration matérielle requise. Vague d’Apache est un Service Azure.
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Unité de niveau supérieure</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Avec les clients Azure flux Analytique déployer et surveiller les tâches de diffusion en continu.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
Avec vague d’Apache sur HDInsight clients déploiement et surveiller un cluster entier, qui peut héberger plusieurs tâches vague ainsi que d’autres charges de travail (lot inclus).
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Prix</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Flux Analytique est-il en volume de données traitées et le nombre d’unités de diffusion en continu (par heure que la tâche est en cours d’exécution) requise.
                </p>
                <p>
                    <a href="http://azure.microsoft.com/en-us/pricing/details/stream-analytics/">Tarifs davantage d’informations sont accessibles ici.</a>
                </p>
            </td>
            <td width="246" valign="top">
                <p>
Pour vague d’Apache sur HDInsight, l’unité d’achat est basé sur un cluster et est facturée en fonction du temps que le cluster est en cours d’exécution, indépendamment des travaux de déploiement.
                </p>
                <p>
                    <a href="http://azure.microsoft.com/en-us/pricing/details/hdinsight/">Tarifs davantage d’informations sont accessibles ici.</a>
                </p>
            </td>
        </tr>
    </tbody>
</table>
##Co-création sur chaque plate-forme analytique##
<table border="1" cellspacing="0" cellpadding="0">
    <tbody>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong> </strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
                    <strong>Flux de données Azure Analytique</strong>
                </p>
            </td>
            <td width="246" valign="top">
                <p>
                    <strong>Vague d’Apache sur HDInsight</strong>
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Fonctionnalités : SQL DSL</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Oui, une prise en charge des langues SQL facile à utiliser est disponible.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
Non, les utilisateurs doivent écrire du code dans Java c# ou utilisez Trident API.
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Capacités : Opérateurs temporelles</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Dans une fenêtre agrégats et temporelles jointures sont prises en charge prêts à l’emploi.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
Opérateurs temporelles doivent être activée par l’utilisateur.
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Expérience de développement</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Création et le débogage interactif expérience via Azure Portal sur exemples de données.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
Développement, débogage et de surveillance expérience sont fourni par le biais de Visual Studio destinée aux utilisateurs de .NET, tandis que pour Java et autres développeurs langues peut-être utiliser l’IDE de votre choix.
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Prise en charge débogage</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Flux Analytique offre l’état des travaux de base et des journaux des opérations comme un moyen de débogage, mais ne propose actuellement pas flexibilité dans que se passe-t-il/comment très est inclus dans les journaux d’Internet Explorer : mode détaillé.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
Journaux détaillés sont disponibles pour le débogage. Il existe deux manières pour les fichiers journaux en surface à utilisateur, via visual Studio ou l’utilisateur peut RDP dans le cluster pour accéder aux journaux.
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Prise en charge des UDF (fonctions définies par l’utilisateur)</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Il n’existe actuellement aucune prise en charge des UDF.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
UDF peuvent être écrits en c#, Java ou la langue de votre choix.
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Extensible - code personnalisé</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Il n’existe aucune prise en charge pour les codes extensible dans flux Analytique.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
Oui, il est la disponibilité d’écrire du code personnalisé dans c#, Java ou d’autres langues prises en charge sur vague de.
                </p>
            </td>
        </tr>
    </tbody>
</table>
##Sources de données et des sorties##
<table border="1" cellspacing="0" cellpadding="0">
    <tbody>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong> </strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
                    <strong>Flux de données Azure Analytique</strong>
                </p>
            </td>
            <td width="246" valign="top">
                <p>
                    <strong>Vague d’Apache sur HDInsight</strong>
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                 <strong>Sources de données d’entrée</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>Sources d’entrée pris en charge sont Azure événement Hubs et des objets BLOB Azure.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
Connecteurs sont disponibles pour les événements Hubs, Bus des services, Kafka, etc.. Connecteurs non prises en charge peuvent être implémentées par code personnalisé.
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Formats de données d’entrée</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Formats d’entrée pris en charge sont Avro, JSON, CSV.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
N’importe quel format peut-être être implémenté par code personnalisé.
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Sorties</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Une tâche de diffusion en continu peut-être avoir plusieurs sorties. Pris en charge des sorties : Événement Azure Hubs, stockage d’objets Blob Azure, Azure des tableaux, base de données SQL Azure et PowerBI.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
Prise en charge pour beaucoup de sorties dans une topologie, chaque sortie peut avoir une logique personnalisée pour le traitement en aval. Prêts à l’emploi vague d’inclut les connecteurs pour PowerBI, Azure événement Hubs, banque d’objets Blob Azure, DocumentDB Azure, SQL et HBase. Connecteurs non prises en charge peuvent être implémentées par code personnalisé.
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Formats de codage de données</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Flux Analytique nécessite format des données UTF-8 à être utilisée.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
N’importe quel format de codage de données peut-être être implémentée par code personnalisé.
                </p>
            </td>
        </tr>
    </tbody>
</table>
##Gestion et opérations##
<table border="1" cellspacing="0" cellpadding="0">
    <tbody>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong> </strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
                    <strong>Flux de données Azure Analytique</strong>
                </p>
            </td>
            <td width="246" valign="top">
                <p>
                    <strong>Vague d’Apache sur HDInsight</strong>
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Modèle de déploiement de travail</strong>
                </p>
                <p>
                    - <strong>Portail Azure</strong>
                </p>
                <p>
                    - <strong>Visual Studio</strong>
                </p>
                <p>
                    - <strong>PowerShell</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Déploiement est activée via le portail Azure, PowerShell et API REST.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
Depolyment est activée via le portail Azure, PowerShell, Visual Studio et API REST.
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Surveillance (statistiques, compteurs, etc.).</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Surveillance est activée via le portail Azure et API REST.
                </p>
                <p>
L’utilisateur peut également configurer des alertes Azure.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
Surveillance est activée via vague d’interface utilisateur et des API REST.
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Extensibilité élevées</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Nombre d’unités de diffusion en continu pour chaque tâche. Chaque unité de diffusion en continu traite jusqu'à 1 Mo/s. Max de 50 unités par défaut. Appel pour augmenter la limite.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
Nombre de nœuds dans le cluster HDI vague. Aucune limite sur le nombre de nœuds (limite supérieure définie par votre quota Azure). Appel pour augmenter la limite.
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Limites de traitement des données</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Les utilisateurs peuvent adapter vers le haut ou vers le bas du nombre d’unités de diffusion en continu à augmenter le traitement des données ou optimiser les coûts.
                </p>
                <p>
Mettre à l’échelle jusqu'à 1 Go/s </p>
            </td>
            <td width="246" valign="top">
                <p>
Utilisateur peut augmenter ou réduire la taille de cluster pour répondre aux besoins.
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Arrêter/Reprendre</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Arrêter et reprendre l’emplacement du dernier arrêté.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
Arrêter et reprendre le dernier placer arrêté basé sur le filigrane.
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Mise à jour de service et framework</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Correction automatique sans interruption de service.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
Correction automatique sans interruption de service.
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Continuité des activités via un Service hautement disponible avec garantie SLA</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
SLA de 99,9 % </p>
                <p>
Récupération automatique des défaillances </p>
                <p>
Récupération d’opérateurs temporelles avec état est intégrée.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
SLA de 99,9 % du cluster vague. Vague d’Apache est une plateforme de diffusion en continu à tolérance de panne mais il est s’engage le client à garantir que leur travail en continu s’exécuter sans interruption.
                </p>
            </td>
        </tr>
    </tbody>
</table>
##Fonctionnalités avancées##
<table border="1" cellspacing="0" cellpadding="0">
    <tbody>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong> </strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
                    <strong>Flux de données Azure Analytique</strong>
                </p>
            </td>
            <td width="246" valign="top">
                <p>
                    <strong>Vague d’Apache sur HDInsight</strong>
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Arrivée en retard et gestion des événements de manière désordonnée</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Stratégies configurables intégrées à réorganiser, déplacer ou régler l’heure de l’événement.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
Utilisateur doit implémenter logique pour gérer ce scénario.
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Données de référence</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
Données de référence disponibles à partir des objets BLOB Azure avec une taille maximale de 100 Mo de mémoire cache de recherche en mémoire. L’actualisation des données de référence est géré par le service.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
Aucune limite de taille des données. Connecteurs disponibles pour HBase, DocumentDB, SQL Server et Azure. Connecteurs non prises en charge peuvent être implémentées par code personnalisé.
                </p>
                <p>
L’actualisation des données de référence doit être gérée par code personnalisé.
                </p>
            </td>
        </tr>
        <tr>
            <td width="174" valign="top">
                <p>
                    <strong>Intégration avec apprentissage automatique</strong>
                </p>
            </td>
            <td width="204" valign="top">
                <p>
En configurant publiés modèles d’apprentissage automatique Azure en tant que fonctions lors de création la tâche ASA <a href="http://blogs.msdn.com/b/streamanalytics/archive/2015/05/24/real-time-scoring-of-streaming-data-using-machine-learning-models.aspx">(preview privé)</a>.
                </p>
            </td>
            <td width="246" valign="top">
                <p>
Disponible via vague détaillée.
                </p>
            </td>
        </tr>
    </tbody>
</table>

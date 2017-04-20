<properties
    pageTitle="Surveiller, diagnostiquer et résoudre les problèmes de stockage | Microsoft Azure"
    description="Utiliser des fonctionnalités telles qu’analytique de stockage, journalisation côté client et d’autres outils tiers à identifier, diagnostiquer et résoudre les problèmes liés au stockage Azure."
    services="storage"
    documentationCenter=""
    authors="jasonnewyork"
    manager="tadb"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/22/2016"
    ms.author="jahogg"/>

# <a name="monitor-diagnose-and-troubleshoot-microsoft-azure-storage"></a>Surveiller, diagnostiquer et résoudre les problèmes de stockage de Microsoft Azure

[AZURE.INCLUDE [storage-selector-portal-monitoring-diagnosing-troubleshooting](../../includes/storage-selector-portal-monitoring-diagnosing-troubleshooting.md)]

## <a name="overview"></a>Vue d’ensemble

Diagnostic et résolution des problèmes dans une application distribuée hébergé dans un environnement cloud peuvent être plus complexes que dans les environnements traditionnels. Applications peuvent être déployées dans une infrastructure PaaS ou IaaS, en local, sur un appareil mobile, ou dans une combinaison de ces. En règle générale, le trafic réseau de votre application peut parcourir réseaux publics et privés et votre application peut utiliser plusieurs technologies de stockage telles que Microsoft Azure stockage de Tables, des objets BLOB et files d’attente, ou par exemple stocke les fichiers ainsi que les autres données relationnelles et de documenter des bases de données.

Pour gérer ces applications avec succès, vous devez surveiller les fait et comprendre comment diagnostiquer et résoudre les problèmes de tous les aspects de leur et leurs technologies dépendantes. En tant qu’utilisateur de services de stockage Azure, vous devez en permanence surveiller les services de stockage que votre application utilise pour tous les changements inattendus dans comportement (par exemple, plus lente que réactivité habituel) et utiliser la fonctionnalité journalisation pour collecter les données plus détaillées et analyser un problème en profondeur. Les informations de diagnostic, vous obtenez à partir de surveillance et de journalisation vous aidera à déterminer la cause du problème rencontrée votre application. Ensuite, vous pouvez résoudre le problème et déterminer les étapes appropriées que vous pouvez prendre plus. Stockage Azure est un service Azure central et formulaires essentiel de la plupart des solutions déploient des clients à l’infrastructure Azure. Stockage Azure inclut des fonctions pour simplifier la surveillance, diagnostic et résolution des problèmes de stockage dans vos applications basées sur le cloud.

> [AZURE.NOTE] Comptes de stockage à un type de réplication de stockage Zone redondantes (ZRS) n’ont pas la métriques ou la fonction de journalisation pour le moment. En outre, le Service de fichiers Azure ne reconnaît pas de journalisation pour le moment.

Pour un guide pratique de bout en bout dépannage dans les applications de stockage Azure, reportez-vous [À bout en bout dépannage à l’aide de métriques de stockage Azure et journalisation, AzCopy et l’Analyseur de Message](storage-e2e-troubleshooting.md).

+ [Introduction]
    + [Comment ce guide est organisé]
+ [Votre service de stockage de surveillance]
    + [État du service de surveillance]
    + [Analyse de la capacité]
    + [Analyse de la disponibilité]
    + [Analyse des performances]
+ [Diagnostiquer les problèmes de stockage]
    + [Problèmes d’intégrité du service]
    + [Problèmes de performances]
    + [Diagnostiquer les erreurs]
    + [Problèmes d’émulateur de stockage]
    + [Outils de journalisation de stockage]
    + [À l’aide des outils de journalisation de réseau]
+ [Suivi de bout en bout]
    + [Corrélation des données du journal]
    + [ID de demande de client]
    + [ID de demande de serveur]
    + [Horodatages]
+ [Conseils de dépannage]
    + [Métrique affiche AverageE2ELatency haute et faible AverageServerLatency]
    + [Métrique affiche AverageE2ELatency basse et AverageServerLatency faible, mais le client rencontre une latence élevée]
    + [Afficher les mesures AverageServerLatency haute]
    + [Vous rencontrez des retards inattendus dans une file d’attente de remise des messages]
    + [Indicateurs montrent une augmentation dans PercentThrottlingError]
    + [Indicateurs montrent une augmentation dans PercentTimeoutError]
    + [Indicateurs montrent une augmentation dans PercentNetworkError]
    + [Le client reçoit des messages HTTP 403 (refusé)]
    + [Le client reçoit des messages HTTP 404 (non trouvée)]
    + [Le client reçoit des messages HTTP 409 (conflit)]
    + [Métrique affiche PercentSuccess faible ou d’entrées de journal analytique avoir opérations statut transaction de ClientOtherErrors]
    + [Mesures de capacité affichent une augmentation inattendue de l’utilisation de la capacité de stockage]
    + [Vous rencontrez inattendus redémarrage des Machines virtuelles qui ont un grand nombre de disques durs virtuels joints]
    + [Votre problème se pose d’utiliser l’émulateur de stockage de développement ou de test]
    + [Vous rencontrez des problèmes d’installation du Kit de développement Azure pour .NET]
    + [Vous avez un autre problème avec un service de stockage]
    + [Résolution des problèmes de fichiers Azure avec Windows et Linux](storage-troubleshoot-file-connection-problems.md)
+ [Appendices]
    + [Appendice 1 : À l’aide de Fiddler pour capturer le trafic HTTP et HTTPS]
    + [Appendice 2 : Utilisation de Wireshark pour capturer le trafic réseau]
    + [Appendice 3 : À l’aide de l’Analyseur de Message de Microsoft pour capturer le trafic réseau]
    + [Appendice 4 : À l’aide d’Excel pour afficher les mesures et les données du journal]
    + [Appendice 5 : Surveillance avec des aperçus d’Application pour Visual Studio Team Services]

## <a name="introduction"></a>Introduction

Ce guide vous montre comment utiliser des fonctionnalités telles que Azure stockage Analytique, la journalisation dans la bibliothèque de Client de stockage Azure et d’autres outils tiers pour identifier, diagnostiquer et résoudre les problèmes de stockage Azure côté client les problèmes.

![][1]

*Figure 1 surveillance, Diagnostics et résolution des problèmes*

Ce guide est destiné à être lus principalement par les développeurs des services en ligne qui utilisent des Services de stockage Azure et informatique responsable de la gestion de ces services en ligne. Les objectifs de ce guide sont :

- Pour vous aider à gérer la santé et les performances de vos comptes de stockage Azure.
- Pour vous fournir les outils pour vous aider à décider si un problème dans une application ou un problème est lié au stockage Azure et de processus nécessaires.
- Pour vous fournir des instructions pour la résolution des problèmes liés au stockage Azure.

### <a name="how-this-guide-is-organized"></a>Comment ce guide est organisé

La section «[votre service de stockage de surveillance]» décrit comment faire pour surveiller l’intégrité et les performances de vos services Azure Storage à l’aide d’indicateurs d’Analytique du stockage Azure (métriques de stockage).

La section «[diagnostic des problèmes de stockage]» décrit comment diagnostiquer les problèmes à l’aide de Azure stockage Analytique journalisation (stockage journalisation). Il décrit également comment activer la journalisation côté client en utilisant les fonctionnalités d’une des bibliothèques du client telle que la bibliothèque de Client de stockage pour .NET ou Azure SDK pour Java.

La section «[suivi de bout en bout]» décrit comment vous pouvez faire correspondre les informations contenues dans les différents fichiers journaux et données de métrique.

La section «[conseils de dépannage]» fournit des conseils de dépannage pour les problèmes liés au stockage courants que vous pouvez rencontrer.

«[Appendices]» inclure des informations sur l’utilisation d’autres outils tels que Wireshark et Netmon pour analyser les données de paquets réseau, Fiddler pour analyser les messages HTTP/HTTPS et Microsoft Message Analyseur de corrélation des données du journal.


## <a name="monitoring-your-storage-service"></a>Votre service de stockage de surveillance

Si vous êtes habitué à l’analyse des performances Windows, vous pouvez considérer comme étant une équivalent au stockage Azure compteurs Windows métriques de stockage. Dans métriques de stockage, vous trouverez un ensemble complet de mesures (compteurs dans la terminologie Analyseur de performances Windows), telles que la disponibilité du service, le nombre total de demandes de service ou pourcentage de demandes réussies au service. Pour une liste complète des mesures disponibles, voir [Schéma de la Table Analytique métriques de stockage](http://msdn.microsoft.com/library/azure/hh343264.aspx). Vous pouvez spécifier si vous souhaitez que le service de stockage pour collecter et regrouper les indicateurs toutes les heures ou toutes les minutes. Pour plus d’informations sur l’activation des mesures et surveiller vos comptes de stockage, voir [activation métriques de stockage et l’affichage des données de métrique](http://go.microsoft.com/fwlink/?LinkId=510865).

Vous pouvez choisir les indicateurs horaires vous souhaitez afficher dans le [Portail Azure](https://portal.azure.com) et configurer des règles qui informer les administrateurs par courrier électronique chaque fois qu’un horaire métrique dépasse un seuil particulier. Pour plus d’informations, voir [Recevoir des Notifications d’alerte](../monitoring-and-diagnostics/insights-receive-alert-notifications.md). 

Le service de stockage collecte des indicateurs à l’aide de son mieux, mais ne peut pas enregistrer chaque opération de stockage.

Dans le portail Azure, vous pouvez afficher des indicateurs tels que la disponibilité et nombre total de demandes de latence moyenne pour un compte de stockage. Une règle de notification a également été définie pour alerter un administrateur si disponibilité est inférieur à un certain niveau. D’afficher ces données, une zone possible pour enquête est le pourcentage de succès de service de tableau est inférieure à 100 % (pour plus d’informations, consultez la section «[métriques afficher PercentSuccess faible ou d’entrées de journal analytique avoir opérations statut de ClientOtherErrors transaction]»).

Vous devez analyser en continu vos applications Azure pour vous assurer qu’ils sont corrects et performantes comme prévu par :

- Établissement de certaines métriques de base pour une application qui vous permet de comparer les données actuelles et d’identifier les modifications importantes dans le comportement du stockage Azure et de votre application. Les valeurs de votre métriques de base sera, dans de nombreux cas, spécifique à l’application et vous devez les établir lorsque vous testez les performances de votre application.
- L’enregistrement minute indicateurs et les utiliser pour surveiller activement les erreurs inattendues et anomalies tels que pointes erreur compte ou demander taux.
- L’enregistrement métriques horaires et les utiliser pour surveiller les valeurs moyennes tel que moyenne décomptes d’erreurs et demander taux.
- Rechercher les problèmes potentiels à l’aide des outils de diagnostic comme expliqué plus loin dans la section «[problèmes de stockage de diagnostic]. »

Les graphiques dans la Figure 3 ci-dessous illustrent comment la moyenne qui se produit pour toutes les heures indicateurs pouvez masquer les pointes d’activité. Les mesures horaires apparaissent pour indiquer une vitesse constante de requêtes, lors de la minute métriques révèlent les variations qui sont réellement en cours.

![][3]

Le reste de cette section décrit les mesures que vous devez analyser et pourquoi.

### <a name="monitoring-service-health"></a>État du service de surveillance

Vous pouvez utiliser le [Portail Azure](https://portal.azure.com) pour afficher l’état du service stockage (et d’autres services Azure) dans toutes les régions Azure dans le monde entier. Cela vous permet de voir immédiatement si un problème en dehors de votre contrôle affecte le service de stockage dans la région que vous utilisez pour votre application.

Le [Portail Azure](https://portal.azure.com) peut également fournir les notifications des incidents qui affectent les différents services Azure.
Remarque : Ces informations étaient auparavant disponibles, ainsi que les données historiques, sur le [Tableau de bord de Service Azure](http://status.azure.com).

Que le [Portail Azure](https://portal.azure.com) collecte des informations d’intégrité à l’intérieur les centres de données Azure (inside out surveillance), vous pouvez également envisager adoptant une approche d’extérieur pour générer des transactions synthétiques régulièrement accèdent à votre application web Azure hébergé à partir de plusieurs emplacements. Les services proposés par [Dynatrace](http://www.dynatrace.com/en/synthetic-monitoring) et les idées de l’Application pour Visual Studio Team Services sont des exemples de cette approche dans à l’extérieur. Pour plus d’informations sur l’Application perspectives pour Visual Studio Team Services, consultez l’annexe «[appendice 5 : surveillance avec des aperçus d’Application pour Visual Studio Team Services](#appendix-5). »

### <a name="monitoring-capacity"></a>Analyse de la capacité

Métriques de stockage stocke uniquement les mesures de capacité pour le service blob parce que des objets BLOB généralement la plus grande proportion des données stockées (au moment de l’écriture, il n’est pas possible d’utiliser des indicateurs de stockage pour surveiller la capacité de vos tables et les files d’attente). Vous pouvez trouver ces données dans la table **$MetricsCapacityBlob** si vous avez activé le contrôle pour le service d’objets Blob. Métriques de stockage enregistre ces données une fois par jour, et vous pouvez utiliser la valeur de la **RowKey** pour déterminer si la ligne contient une entité qui est lié à des données utilisateur ( **données**de la valeur) ou analytique (valeur **analytique**). Chaque entité stockée contient des informations sur la quantité d’espace de stockage utilisé (**capacité** mesurée en octets) et le nombre actuel de conteneurs (**ContainerCount**) et des objets BLOB (**ObjectCount**) en cours d’utilisation dans le compte de stockage. Pour plus d’informations sur les mesures de capacité stockées dans la table **$MetricsCapacityBlob** , voir [Schéma de la Table Analytique métriques de stockage](http://msdn.microsoft.com/library/azure/hh343264.aspx).

> [AZURE.NOTE] Vous devez analyser ces valeurs pour un avertissement au plus tôt que vous allez bientôt atteindre les limites de capacité de votre compte de stockage. Dans le portail Azure, vous pouvez ajouter des règles d’alerte pour être averti si utilisation du stockage agrégation est supérieure ou inférieure seuils que vous spécifiez.

Pour obtenir de l’estimation de la taille des différents objets de stockage tels que des objets BLOB, voir le billet de blog [Compréhension Azure stockage facturation – bande passante, Transactions et à la capacité](http://blogs.msdn.com/b/windowsazurestorage/archive/2010/07/09/understanding-windows-azure-storage-billing-bandwidth-transactions-and-capacity.aspx).

### <a name="monitoring-availability"></a>Analyse de la disponibilité

Vous devez analyser la disponibilité des services de stockage dans votre compte de stockage en analysant la valeur dans la colonne **disponibilité** dans les tables de toutes les heures ou minutes indicateurs : **$MetricsHourPrimaryTransactionsBlob**, **$MetricsHourPrimaryTransactionsTable**, **$MetricsHourPrimaryTransactionsQueue**, **$MetricsMinutePrimaryTransactionsBlob**, **$MetricsMinutePrimaryTransactionsTable**, **$MetricsMinutePrimaryTransactionsQueue**, **$MetricsCapacityBlob**. La colonne **disponibilité** contient une valeur en pourcentage qui indique la disponibilité du service ou l’opération API représentée par la ligne (l’illustre **RowKey** si la ligne contient des indicateurs pour le service entier ou pour une opération de l’API spécifique).

Toute valeur inférieure à 100 % indique que certaines demandes de stockage échoue. Vous pouvez voir pourquoi échouent en examinant les autres colonnes dans les données de mesures qui affichent les numéros de requêtes avec différents types d’erreurs telles que **ServerTimeoutError**. Vous devriez voir **disponibilité** inférieurs temporairement 100 % pour des raisons de tels que des délais du serveur transitoires si le service déplace partitions à mieux demande d’équilibrage de charge ; la logique de nouvelle tentative dans votre application cliente doit gérer ces conditions intermittentes. L’article [stockage Analytique connecté opérations et les Messages d’état](http://msdn.microsoft.com/library/azure/hh343260.aspx) répertorie les types de transactions métriques de stockage inclut dans son calcul de **disponibilité** .

Dans le [Portail Azure](https://portal.azure.com), vous pouvez ajouter des règles d’alerte pour être averti si **la disponibilité** d’un service est inférieur à un seuil que vous spécifiez.

La section «[conseils de dépannage]» de ce guide décrit certains problèmes de service de stockage courantes relatives à la disponibilité.

### <a name="monitoring-performance"></a>Analyse des performances

Pour analyser les performances des services de stockage, vous pouvez utiliser les mesures suivantes à partir des tables des mesures de toutes les heures et minutes.

- Les valeurs dans les colonnes **AverageE2ELatency** et **AverageServerLatency** indiquent la durée moyenne le service de stockage ou type d’opération API est-elle traiter les demandes. **AverageE2ELatency** est une mesure de latence de bout en bout qui inclut le temps nécessaire pour lire la demande et envoyer la réponse ainsi que le temps nécessaire pour traiter la requête (c’est pourquoi inclut la latence du réseau lorsque la requête atteint le service de stockage) ; **AverageServerLatency** est une mesure de la durée du traitement et par conséquent exclut latence réseau liée à la communication avec le client. Consultez la section «[métriques afficher AverageE2ELatency haute et faible AverageServerLatency]» plus loin dans ce guide pour une description des raisons pour lesquelles il peut y avoir une différence significative entre ces deux valeurs.
- Les valeurs dans les colonnes **TotalIngress** et **TotalEgress** indiquent le volume total de données, en octets, entrant et va se déconnecter de votre service de stockage ou via un type d’opération API spécifique.
- Les valeurs dans la colonne **TotalRequests** indiquent le nombre total de requêtes qui reçoit le service de stockage de fonctionnement de l’API. **TotalRequests** est le nombre total de requêtes qui reçoit le service de stockage.

En règle générale, vous allez surveiller les modifications inattendus dans chacune de ces valeurs en tant qu’indicateur que vous avez un problème nécessitant une enquête.

Dans le [Portail Azure](https://portal.azure.com), vous pouvez ajouter des règles d’alerte pour être averti si un des mesures de performances pour cet automne service en dessous ou dépassent un seuil que vous spécifiez.

La section «[conseils de dépannage]» de ce guide décrit certains problèmes de service de stockage courants liés aux performances.


## <a name="diagnosing-storage-issues"></a>Diagnostiquer les problèmes de stockage

Il existe plusieurs méthodes que vous rendre compte d’un problème ou d’un problème dans votre application, notamment :

- Une défaillance majeure entraînant l’application dysfonctionnement ou le blocage.
- Des modifications substantielles à partir des valeurs de référence dans les mesures vous analysez comme décrit dans la section précédente «[votre service de stockage de surveillance]. »
- Les rapports sur les utilisateurs de votre application qu’une opération particulière n’est pas terminé comme prévu ou que certaines fonctionnalités ne fonctionne pas.
- Erreurs générées au sein de votre application qui s’affichent dans les fichiers journaux ou via une autre méthode de notification.

En règle générale, les problèmes liés aux services de stockage Azure appartiennent à une des quatre catégories :

- Votre application rencontre un problème de performance, signalés par vos utilisateurs, soit révélé par les modifications apportées dans les mesures de performances.
- Il existe un problème avec l’infrastructure de stockage Azure dans une ou plusieurs zones.
- Votre application rencontre une erreur, signalés par vos utilisateurs, soit révélé par une augmentation d’une des mesures de nombre d’erreur que vous surveillez.
- Au cours de développement et de test, vous pouvez utiliser l’émulateur de stockage local ; Vous pouvez rencontrer des problèmes ayant trait spécifiquement à l’utilisation de l’émulateur de stockage.

Les sections suivantes décrivent les étapes à suivre pour diagnostiquer et résoudre les problèmes dans chacune de ces quatre catégories. La section «[conseils de dépannage]» plus loin dans ce guide fournit des détails supplémentaires pour certains problèmes courants que vous pouvez rencontrer.

### <a name="service-health-issues"></a>Problèmes d’intégrité du service

Problèmes d’intégrité du service sont généralement pas le contrôle. Le [Portail Azure](https://portal.azure.com) fournit des informations sur les problèmes en cours avec les services Azure, y compris les services de stockage. Si vous opté pour l’accès en lecture Geo redondants stockage lorsque vous avez créé votre compte de stockage, puis en cas de vos données n’est pas disponible dans l’emplacement principal, votre application peut réduire provisoirement à la copie en lecture seule à l’emplacement secondaire. Pour ce faire, votre application doit être en mesure de basculer entre l’utilisation des emplacements de stockage principaux et secondaires et puissent travailler en mode fonctionnalités réduites avec les données en lecture seule. Les bibliothèques Azure stockage Client permettent de définir une stratégie de nouvelles tentatives qui peut lire à partir du stockage secondaire en cas d’échec de lecture sur le stockage principal. Votre application doit également n’oubliez pas que les données à l’emplacement secondaire soient finalement cohérentes. Pour plus d’informations, consultez le blog [Azure stockage redondance Options et accès en lecture Geo redondants stockage](https://blogs.msdn.microsoft.com/windowsazurestorage/2013/12/11/windows-azure-storage-redundancy-options-and-read-access-geo-redundant-storage/).

### <a name="performance-issues"></a>Problèmes de performances

Les performances d’une application peuvent être subjective, notamment à partir d’un point de vue utilisateur. Par conséquent, il est important de disposer d’indicateurs de référence disponibles pour vous aider à identifier l’emplacement dans lequel il peut y avoir un problème de performance. De nombreux facteurs peuvent affecter les performances d’un service de stockage Azure à partir de la perspective d’application client. Ces facteurs peuvent fonctionner dans le service de stockage, dans le client ou dans l’infrastructure réseau ; Par conséquent, il est important de disposer d’une stratégie d’identification de l’origine du problème de performances.

Après avoir identifié l’emplacement de la cause du problème de performance à partir de la métrique probable, vous pouvez utiliser ensuite les fichiers journaux pour trouver des informations détaillées pour diagnostiquer et résoudre le problème persiste.

La section «[conseils de dépannage]» plus loin dans ce guide fournit plus d’informations sur certains des performances courants liés problèmes que vous pouvez rencontrer.

### <a name="diagnosing-errors"></a>Diagnostiquer les erreurs

Les utilisateurs de votre application peuvent vous avertir des erreurs signalées par l’application cliente. Métriques de stockage enregistre également comptages des différents types d’erreurs à partir de vos services de stockage tels que **NetworkError**, **ClientTimeoutError**ou **AuthorizationError**. Tandis que métriques de stockage enregistre uniquement le nombre de différents types d’erreurs, vous pouvez obtenir plus d’informations sur les requêtes individuelles en examinant journaux réseau, côté client et côté serveur. En règle générale, le code d’état HTTP retourné par le service de stockage donnera une indication de la raison pour laquelle la demande a échoué.

> [AZURE.NOTE] N’oubliez pas que vous devriez voir des erreurs intermittents : par exemple, les erreurs en raison de problèmes réseau temporaires, ou les erreurs d’application.

Les ressources suivantes sont utiles pour comprendre liés au stockage état et codes d’erreur :

- [Codes d’erreur de l’API REST courants](http://msdn.microsoft.com/library/azure/dd179357.aspx)
- [Codes d’erreur Service BLOB](http://msdn.microsoft.com/library/azure/dd179439.aspx)
- [Codes d’erreur Service file d’attente](http://msdn.microsoft.com/library/azure/dd179446.aspx)
- [Codes d’erreur de Service de table](http://msdn.microsoft.com/library/azure/dd179438.aspx)
- [Codes d’erreur de Service de fichier](https://msdn.microsoft.com/library/azure/dn690119.aspx)

### <a name="storage-emulator-issues"></a>Problèmes d’émulateur de stockage

Le Kit de développement Azure inclut un émulateur de stockage que vous pouvez exécuter sur un poste de développement. Cet émulateur simule la plupart du comportement des services de stockage Azure et est utile pendant le développement et de test, ce qui vous permet d’exécuter des applications qui utilisent des services de stockage Azure sans qu’il soit nécessaire pour un abonnement Azure et un compte de stockage Azure.

La section «[conseils de dépannage]» de ce guide décrit certains problèmes courants rencontrés à l’aide de l’émulateur de stockage.

### <a name="storage-logging-tools"></a>Outils de journalisation de stockage

Stockage journalisation permet la journalisation côté serveur des demandes de stockage dans votre compte de stockage Azure. Pour plus d’informations sur la façon d’activer la journalisation côté serveur et accéder aux données du journal, voir [activation de la journalisation de stockage et accéder aux données du journal](http://go.microsoft.com/fwlink/?LinkId=510867).

La bibliothèque de Client de stockage pour .NET vous permet de collecter les données de journal côté client est lié à des opérations de stockage effectuées par votre application. Pour plus d’informations, voir [journalisation côté Client avec la bibliothèque .NET stockage Client](http://go.microsoft.com/fwlink/?LinkId=510868).

> [AZURE.NOTE] Dans certaines circonstances (par exemple, les échecs d’autorisation associations de sécurité), un utilisateur peut signaler une erreur pour laquelle vous ne pouvez trouver aucune donnée demande dans les journaux de stockage côté serveur. Vous pouvez utiliser les fonctions d’enregistrement de la bibliothèque de Client de stockage pour déterminer si la cause du problème se trouve sur le client ou utiliser les outils d’analyse réseau pour déterminer l’origine du réseau.

### <a name="using-network-logging-tools"></a>À l’aide des outils de journalisation de réseau

Vous pouvez capturer le trafic entre le client et serveur pour fournir des informations détaillées sur les données que client et serveur échangez ainsi que les conditions de réseau sous-jacente. Outils de journalisation utiles du réseau :

- [Fiddler](http://www.telerik.com/fiddler) est un site web gratuit débogage proxy qui vous permet d’examiner les en-têtes et les données de charge utile des messages de demande et réponse HTTP et HTTPS. Pour plus d’informations, voir [appendice 1 : à l’aide de Fiddler pour capturer le trafic HTTP et HTTPS](#appendix-1).
- [Moniteur réseau Microsoft (Netmon)](http://www.microsoft.com/download/details.aspx?id=4865) et [Wireshark](http://www.wireshark.org/) sont analyseurs de protocole réseau libre qui vous permettent d’afficher des informations détaillées de paquets pour un large éventail de protocoles réseau. Pour plus d’informations sur Wireshark, voir «[appendice 2 : à l’aide de Wireshark pour capturer le trafic réseau](#appendix-2)».
- Analyseur de Message de Microsoft est un outil de Microsoft qui remplace Netmon et qui en plus de capturer des données de paquets réseau, vous aide à afficher et analyser les données du journal capturées à partir d’autres outils. Pour plus d’informations, voir «[appendice 3 : à l’aide de l’Analyseur de Message de Microsoft pour capturer le trafic réseau](#appendix-3)».
- Si vous souhaitez effectuer un test de connectivité de base pour vérifier que votre ordinateur client peut se connecter au service de stockage Azure via le réseau, vous ne pouvez pas faire ceci à l’aide de l’outil standard **ping** sur le client. Toutefois, vous pouvez utiliser l' [outil **tcping** ](http://www.elifulkerson.com/projects/tcping.php) pour vérifier la connectivité.

Dans de nombreux cas, les données du journal de journalisation de stockage et de la bibliothèque de stockage Client sera suffisantes pour diagnostiquer un problème, mais dans certains cas, vous devrez peut-être des informations plus détaillées qui fournissent ces outils de journalisation de réseau. Par exemple, à l’aide de Fiddler pour afficher les messages HTTP et HTTPS permet de vous permettent de visualiser des données d’en-tête et charge utile envoyées vers et depuis les services de stockage, qui vous permet d’examiner la manière dont une application cliente relance l’opération de stockage. Analyseurs de protocole tel que Wireshark fonctionnent au niveau des paquets vous permettant d’afficher des données TCP, ce qui vous permet de résoudre les problèmes de connectivité et de perte de paquets. Analyseur de message peut fonctionner à couches HTTP et TCP.

## <a name="end-to-end-tracing"></a>Suivi de bout en bout

Traçage de bout en bout à l’aide d’une variété de fichiers journaux est très utile pour rechercher les problèmes potentiels. Vous pouvez utiliser les informations de date/heure à partir de vos données métriques comme une indication d’où commencer vos recherches dans les fichiers journaux pour des informations détaillées qui vous aidera à résoudre le problème.

### <a name="correlating-log-data"></a>Corrélation des données du journal

Lorsque vous affichez les journaux des applications clientes, network trace, et journalisation côté serveur stockage qu'il est essentiel de pouvoir faire correspondre les demandes entre les différents fichiers journaux. Les fichiers journaux incluent un certain nombre de plusieurs champs pouvant être utilisés comme identificateurs de corrélation. L’id de demande de client est le champ à utiliser pour faire correspondre les entrées dans les différents journaux plus utile. Toutefois parfois, il peut être utile d’utiliser l’id de demande de serveur ou les horodatages. Les sections suivantes fournissent plus d’informations sur ces options.

### <a name="client-request-id"></a>ID de demande de client

La bibliothèque de stockage Client génère automatiquement un id de demande de client unique pour chaque demande.

- Dans le journal côté client qui crée la bibliothèque de stockage Client, l’id de demande de client apparaît dans le champ **ID de demande de Client** de chaque entrée de journal relatif à la demande.
- Dans une trace réseau comme un capturé par Fiddler, l’id de demande de client est visible dans les messages de requête en tant que la valeur de l’en-tête **x-ms-client-demande-id** HTTP.
- Dans le journal de stockage journalisation côté serveur, l’id de demande de client apparaît dans la colonne ID de demande Client.

> [AZURE.NOTE]Il est possible pour plusieurs requêtes partager le même id de demande de client, car le client peut affecter cette valeur (même si la bibliothèque de stockage Client affecte automatiquement une nouvelle valeur). Dans le cas de tentatives à partir du client, toutes les tentatives partagent le même id de demande de client. Dans le cas d’un lot envoyé à partir du client, le lot possède un id de demande de client unique.


### <a name="server-request-id"></a>ID de demande de serveur

Le service de stockage génère automatiquement un ID de demande de serveur.

- Dans le journal de stockage journalisation côté serveur, l’id de demande de serveur apparaît la colonne **d’en-tête de l’ID de la requête** .
- Dans une trace réseau comme un capturé par Fiddler, l’id de demande de serveur apparaît dans les messages de réponse en tant que la valeur de l’en-tête **x-ms-demande-id** HTTP.
- Dans le journal côté client qui crée la bibliothèque de stockage Client, l’id de demande de serveur apparaît dans la colonne de **Texte de l’opération** pour l’entrée de journal avec les détails de la réponse du serveur.

> [AZURE.NOTE] Le service de stockage toujours affecte un serveur unique id de la requête à chaque demande qu'elle reçoit, afin que chaque nouvelle tentative à partir du client et chaque opération incluses dans un lot possède un id de demande de serveur unique.

Si la bibliothèque de stockage Client lève un **StorageException** dans le client, la propriété **RequestInformation** contient un objet **RequestResult** qui inclut une propriété **ServiceRequestID%%** . Vous pouvez également accéder à un objet **RequestResult** à partir d’une instance **OperationContext** .

L’exemple ci-dessous montre comment définir une valeur personnalisée **ClientRequestId** en associant un objet **OperationContext** la demande au service de stockage. Il indique également comment récupérer la valeur **ServerRequestId** dans le message de réponse.

    //Parse the connection string for the storage account.
    const string ConnectionString = "DefaultEndpointsProtocol=https;AccountName=account-name;AccountKey=account-key";
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(ConnectionString);
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // Create an Operation Context that includes custom ClientRequestId string based on constants defined within the application along with a Guid.
    OperationContext oc = new OperationContext();
    oc.ClientRequestID = String.Format("{0} {1} {2} {3}", HOSTNAME, APPNAME, USERID, Guid.NewGuid().ToString());

    try
    {
        CloudBlobContainer container = blobClient.GetContainerReference("democontainer");
        ICloudBlob blob = container.GetBlobReferenceFromServer("testImage.jpg", null, null, oc);  
        var downloadToPath = string.Format("./{0}", blob.Name);
        using (var fs = File.OpenWrite(downloadToPath))
        {
            blob.DownloadToStream(fs, null, null, oc);
            Console.WriteLine("\t Blob downloaded to file: {0}", downloadToPath);
        }
    }
    catch (StorageException storageException)
    {
        Console.WriteLine("Storage exception {0} occurred", storageException.Message);
        // Multiple results may exist due to client side retry logic - each retried operation will have a unique ServiceRequestId
        foreach (var result in oc.RequestResults)
        {
                Console.WriteLine("HttpStatus: {0}, ServiceRequestId {1}", result.HttpStatusCode, result.ServiceRequestID);
        }
    }


### <a name="timestamps"></a>Horodatages

Vous pouvez également utiliser horodatages pour localiser des entrées de journal associées, mais en veillant à ne de n’importe quel décalage horaire entre le client et serveur qui peut-être exister. Vous devez rechercher plus ou moins 15 minutes pour la correspondance des entrées côté serveur en fonction de l’horodatage sur le client. N’oubliez pas que les métadonnées d’objets blob pour les objets BLOB qui contiennent les métriques indiquent la plage horaire pour les indicateurs stockés dans le blob ; Ceci est utile si vous avez plusieurs objets BLOB métriques pour la même minute ou heure.

## <a name="troubleshooting-guidance"></a>Conseils de dépannage

Cette section vous aidera au diagnostic et résolution des problèmes de certains des problèmes courants votre application peuvent rencontrer lorsque vous utilisez les services de stockage Azure. Utilisez la liste ci-dessous pour localiser les informations relatives à votre problème spécifique.

**Résolution des problèmes d’arbre de décision**

----------

Votre problème est-il relié aux performances d’un des services de stockage ?

- [Métrique affiche AverageE2ELatency haute et faible AverageServerLatency]
- [Métrique affiche AverageE2ELatency basse et AverageServerLatency faible, mais le client rencontre une latence élevée]
- [Afficher les mesures AverageServerLatency haute]
- [Vous rencontrez des retards inattendus dans une file d’attente de remise des messages]

----------

Votre problème est-il relié à la disponibilité d’un des services de stockage ?

- [Indicateurs montrent une augmentation dans PercentThrottlingError]
- [Indicateurs montrent une augmentation dans PercentTimeoutError]
- [Indicateurs montrent une augmentation dans PercentNetworkError]

----------

Votre application cliente reçoit une réponse HTTP en 4XX (par exemple, 404) à partir d’un service de stockage ?

- [Le client reçoit des messages HTTP 403 (refusé)]
- [Le client reçoit des messages HTTP 404 (non trouvée)]
- [Le client reçoit des messages HTTP 409 (conflit)]

----------

[Métrique affiche PercentSuccess faible ou d’entrées de journal analytique avoir opérations statut transaction de ClientOtherErrors]

----------

[Mesures de capacité affichent une augmentation inattendue de l’utilisation de la capacité de stockage]

----------

[Vous rencontrez inattendus redémarrage des Machines virtuelles qui ont un grand nombre de disques durs virtuels joints]

----------

[Votre problème se pose d’utiliser l’émulateur de stockage de développement ou de test]

----------

[Vous rencontrez des problèmes d’installation du Kit de développement Azure pour .NET]

----------

[Vous avez un autre problème avec un service de stockage]

----------

### <a name="metrics-show-high-AverageE2ELatency-and-low-AverageServerLatency"></a>Métrique affiche AverageE2ELatency haute et faible AverageServerLatency

L’illustration ci-dessous à partir du [Portail Azure](https://portal.azure.com) outil d’analyse montre un exemple où **AverageE2ELatency** sont beaucoup plus **AverageServerLatency**.

![][4]

Notez que le service de stockage uniquement calcule la mesure **AverageE2ELatency** de demandes réussies et, contrairement à **AverageServerLatency**, inclut la durée du client pour envoyer les données et recevoir un accusé de réception à partir du service de stockage. Par conséquent, une différence entre **AverageE2ELatency** et **AverageServerLatency** pourrait être en raison de l’application cliente est lent à répondre ou en raison de conditions sur le réseau.

> [AZURE.NOTE] Vous pouvez également afficher **E2ELatency** et **ServerLatency** pour les opérations de stockage individuels dans les données du journal journalisation de stockage.

#### <a name="investigating-client-performance-issues"></a>Rechercher les problèmes de performances client

Le client répond lentement causes possibles ayant un nombre limité de connexions disponibles ou threads, ou étant trop peu de ressources telles que la bande passante processeur, la quantité de mémoire ou le réseau. Vous serez peut-être en mesure de résoudre le problème en modifiant le code du client pour être plus efficace (par exemple en utilisant des appels asynchrones au service de stockage) ou en utilisant une plus grande Machine virtuelle (avec plus de cœurs et plus de mémoire).

Pour les services de tableau et file d’attente, l’algorithme Nagle peut également entraîner haute **AverageE2ELatency** par rapport à **AverageServerLatency**: pour plus d’informations, voir le billet [algorithme de Nagle n’est pas convivial vers demandes petite](http://blogs.msdn.com/b/windowsazurestorage/archive/2010/06/25/nagle-s-algorithm-is-not-friendly-towards-small-requests.aspx). Vous pouvez désactiver l’algorithme de Nagle dans le code à l’aide de la classe **ServicePointManager** dans **l’espace de noms** . Vous devez faire cela avant de procéder à des appels à la table ou services file d’attente dans votre application dans la mesure où cela n’affecte pas les connexions qui se trouvent déjà ouverts. L’exemple suivant provient de la méthode **Application_Start** dans un rôle de collaborateur.

    var storageAccount = CloudStorageAccount.Parse(connStr);
    ServicePoint tableServicePoint = ServicePointManager.FindServicePoint(storageAccount.TableEndpoint);
    tableServicePoint.UseNagleAlgorithm = false;
    ServicePoint queueServicePoint = ServicePointManager.FindServicePoint(storageAccount.QueueEndpoint);
    queueServicePoint.UseNagleAlgorithm = false;

Vous devez vérifier les journaux côté client pour afficher le nombre de requêtes qu'est l’envoi de votre application cliente et vérifier la présence de .NET général liés défauts de performances dans votre client tel que nettoyage processeur, .NET, l’utilisation du réseau ou la mémoire. Comme point de départ pour la résolution des applications clientes .NET, consultez [débogage, suivi et profil](http://msdn.microsoft.com/library/7fe0dd2y).

#### <a name="investigating-network-latency-issues"></a>Rechercher les problèmes de latence réseau

En règle générale, une latence élevée bout en bout provoquée par le réseau est en raison de conditions temporaires. Vous pouvez examiner les deux problèmes réseau transitoires et permanentes telles que les paquets à l’aide d’outils tels que Wireshark ou de l’Analyseur de Message de Microsoft.

Pour plus d’informations sur l’utilisation de Wireshark pour résoudre les problèmes de réseau, voir «[appendice 2 : utilisation de Wireshark pour capturer le trafic réseau]. »

Pour plus d’informations sur l’Analyseur de Message Microsoft pour résoudre les problèmes de réseau, voir «[appendice 3 : à l’aide de l’Analyseur de Message Microsoft pour capturer le trafic réseau]. »

### <a name="metrics-show-low-AverageE2ELatency-and-low-AverageServerLatency"></a>Métrique affiche AverageE2ELatency basse et AverageServerLatency faible, mais le client rencontre une latence élevée

Dans ce scénario, la cause probable est un délai dans les demandes de stockage atteindre le service de stockage. Vous devez examiner pourquoi demandes à partir du client sont n’apportez à l’aide du service d’objets blob.

Une des raisons possibles pour le client retarder l’envoi de demandes sont qu’il existe un nombre limité de connexions disponibles ou threads.

Vous devez également vérifier si le client effectue plusieurs tentatives et examinez la raison pour laquelle si c’est le cas. Pour déterminer si le client effectue plusieurs tentatives, vous pouvez :

- Examinez les journaux de stockage Analytique. Si plusieurs tentatives sont produisent, vous verrez plusieurs opérations avec le même ID de demande de client, mais avec demande de serveur différent ID.
- Examinez les journaux de client. La journalisation détaillée indiquera qu’une nouvelle tentative s’est produite.
- Déboguer votre code, puis vérifiez les propriétés de l’objet **OperationContext** associé à la demande. Si l’opération a retentée, la propriété **RequestResults** inclut plusieurs demande unique server ID. Vous pouvez également vérifier les heures de début et de fin pour chaque demande. Pour plus d’informations, voir l’exemple de code dans la section [ID de demande de serveur].

S’il n’y a aucun problème dans le client, vous devez examiner les problèmes réseau potentiels tels que de perte de paquets. Vous pouvez utiliser les outils tels que Wireshark ou de l’Analyseur de Message de Microsoft pour étudier des problèmes réseau.

Pour plus d’informations sur l’utilisation de Wireshark pour résoudre les problèmes de réseau, voir «[appendice 2 : utilisation de Wireshark pour capturer le trafic réseau]. »

Pour plus d’informations sur l’Analyseur de Message Microsoft pour résoudre les problèmes de réseau, voir «[appendice 3 : à l’aide de l’Analyseur de Message Microsoft pour capturer le trafic réseau]. »

### <a name="metrics-show-high-AverageServerLatency"></a>Afficher les mesures AverageServerLatency haute

Dans le cas **AverageServerLatency** élevé pour les demandes de téléchargement blob, vous devez utiliser les journaux de journalisation de stockage pour déterminer s’il existe des demandes répétées pour la même blob (ou l’ensemble des objets BLOB). Demandes de téléchargement pour blob, vous devez examiner quels bloc utilise la taille du client (par exemple, se bloque inférieure à 64 Ko peut entraîner transparents, à moins que les lectures sont également dans inférieure à 64 Ko segments), et si plusieurs clients Téléchargez blocs dans le même objet blob en parallèle. Vous devez également vérifier les mesures par minute les pointes dans le nombre de requêtes qui génèrent dépassant la par cibles extensibilité du deuxième : également voir «[Afficher les mesures augmente PercentTimeoutError]. »

Si vous voyez haute **AverageServerLatency** pour blob télécharger demandes lorsqu’il ont a des demandes répétées la même blob ou l’ensemble des objets BLOB, vous devez envisager la mise en cache ces objets BLOB à l’aide de Cache Azure ou le réseau de remise de contenu (CDN) d’Azure. Pour les demandes de téléchargement, vous pouvez améliorer le débit en utilisant une plus grande taille de bloc. Pour les requêtes à des tables, il est également possible d’implémenter un cache côté client sur les clients qui effectue les mêmes opérations de requête et l’endroit où les données ne changent fréquemment.

Des valeurs élevées **AverageServerLatency** peuvent également être une manifestation de mal conçues tables ou requêtes que résultat dans les opérations d’analyse ou qui suivent le modèle anti-courrier ajout/ajouter. Pour plus d’informations, voir «[Afficher les mesures augmente PercentThrottlingError]».

> [AZURE.NOTE] Vous pouvez trouver une liste de vérification complète performances liste de vérification ici : [Microsoft Azure stockage performances et extensibilité élevées aide-mémoire](storage-performance-checklist.md).

### <a name="you-are-experiencing-unexpected-delays-in-message-delivery"></a>Vous rencontrez des retards inattendus dans une file d’attente de remise des messages

Si vous rencontrez un délai entre le moment qu'une application ajoute un message à une file d’attente et le temps qu’elle est disponible pour lire à partir de la file d’attente, vous devez prendre les étapes suivantes pour diagnostiquer le problème :

- Vérifiez que l’application ajoute correctement les messages dans la file d’attente. Vérifiez que l’application est ne pas réessayer la méthode **AddMessage** plusieurs fois tentatives. Les journaux de stockage Client bibliothèque indiquent les tentatives répétées des opérations de stockage.
- Vérifiez qu’il n’existe aucune horloge maximal autorisé entre le rôle de collaborateur qui ajoute le message dans la file d’attente et le rôle de collaborateur qui lit le message dans la file d’attente rend s’affichent comme s’il y a un délai de traitement.
- Vérifiez si le rôle de collaborateur qui lit les messages à partir de la file d’attente ignore. Si un client file d’attente appelle la méthode **GetMessage** mais ne répond pas avec un accusé de réception, le message restera invisible dans la file d’attente avant l’expiration de la période **invisibilityTimeout** . À ce stade, le message devient disponible pour le traitement à nouveau.
- Vérifiez si la file d’attente se développe au fil du temps. Cela peut se produire si vous ne disposez pas de suffisamment travailleurs disponibles pour traiter tous les messages que d’autres travailleurs sont placer dans la file d’attente. Vous devez également vérifier les mesures pour voir si les demandes de supprimer sont répétées défectueux et le nombre de dequeue sur les messages, susceptibles d’indiquer les échecs de supprimer le message.
- Examinez les journaux de journalisation de stockage pour toutes les opérations de file d’attente qui ont supérieur à celui attendus valeurs **E2ELatency** et **ServerLatency** sur une longue période de temps que d’habitude.


### <a name="metrics-show-an-increase-in-PercentThrottlingError"></a>Indicateurs montrent une augmentation dans PercentThrottlingError

Limitation des erreurs se produisent lorsque vous dépassez les cibles extensibilité élevées d’un service de stockage. Le service de stockage pour cela, pour vous assurer qu’aucun client unique ou le client ne peut utiliser le service au détriment des autres. Pour plus d’informations, voir [extensibilité du stockage Azure et des objectifs de Performance](storage-scalability-targets.md) pour plus d’informations sur les cibles extensibilité élevées pour les comptes de stockage et les objectifs de performances des partitions au sein des comptes de stockage.

Si la métrique **PercentThrottlingError** affiche une augmentation du pourcentage de demandes ignore présentant un problème de limitation, vous devez examiner un des deux scénarios :

- [Augmentation transitoire de PercentThrottlingError]
- [Augmentation permanente PercentThrottlingError erreur]

Augmente **PercentThrottlingError** se produit souvent en même temps qu’augmente le nombre de demandes de stockage, ou lorsque vous êtes initialement charger le test de votre application. Cela peut également manifeste dans le client en tant que « 503 serveur occupé (e) » ou « délai d’expiration de 500 opération » HTTP état à partir d’opérations de stockage.

#### <a name="transient-increase-in-PercentThrottlingError"></a>Augmentation transitoire de PercentThrottlingError

Si vous voyez pointes de la valeur de **PercentThrottlingError** qui correspondent aux périodes de forte activité pour l’application, vous devez implémenter un précédent (non linéaire) exponentielle désactiver la stratégie de nouvelles tentatives dans votre client : vous réduisez la charge immédiatement sur la partition et à votre application pour atténuer les pointes de trafic. Pour plus d’informations sur la mise en œuvre les stratégies de nouvelle tentative à l’aide de la bibliothèque de stockage Client, voir [Microsoft.WindowsAzure.Storage.RetryPolicies Namespace](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.retrypolicies.aspx).

> [AZURE.NOTE] Vous pouvez également voir pointes de la valeur de **PercentThrottlingError** qui ne coïncident pas avec des périodes de forte activité pour l’application : la cause probable ici est le service de stockage déplacement des partitions pour améliorer l’équilibrage de charge.

#### <a name="permanent-increase-in-PercentThrottlingError"></a>Augmentation permanente PercentThrottlingError erreur

Si vous voyez une valeur élevée de **PercentThrottlingError** après une augmentation permanente dans vos volumes transactions, ou lorsque vous effectuez votre charge initiale des tests sur votre application, puis vous devez évaluer la manière dont votre application utilise les partitions de stockage et qu’il arrive les cibles extensibilité élevées pour un compte de stockage. Par exemple, si vous voyez la limitation des erreurs dans une file d’attente (qui est considérée comme une partition unique), puis vous envisagez d’utiliser files d’attente supplémentaires pour répartir les transactions sur plusieurs partitions. Si vous voyez la limitation des erreurs dans une table, vous devez prendre en considération avec un jeu de partition différent pour répartir vos transactions sur plusieurs partitions à l’aide d’un large éventail de valeurs clés partition. L’une des causes courantes de ce problème sont le modèle anti-courrier prepend/ajouter où vous sélectionnez la date comme clé partition et puis toutes les données dans un jour donné est écrit avec une partition : en charge, cela peut entraîner une critique écriture. Vous devez prendre en compte d’une autre création partition ou choisir entre l’utilisation du stockage blob peut être une meilleure solution. Vous devez également Vérifiez si la limitation se produit à la suite de pointes de votre trafic et recherchez façons de lissage votre modèle de requêtes.

Si vous distribuez vos transactions sur plusieurs partitions, vous devez toujours être prenant en charge des limites extensibilité élevées définies pour le compte de stockage. Par exemple, si vous avez utilisé files d’attente de dix chaque traitement de la valeur maximale de 2 000 messages de 1 Ko par seconde, vous serez à la limite de 20 000 messages par seconde pour le compte de stockage globale. Si vous avez besoin de traiter plus de 20 000 entités par seconde, envisagez d’utiliser plusieurs comptes de stockage. Vous devez également garder à l’esprit que la taille de vos demandes et entités a un impact sur lorsque le service de stockage limite vos clients : Si vous avez plus grande demandes et entités, vous pouvez limité plus tôt.

Création de requête inefficace peut également entraîner que vous deviez les limites extensibilité élevées des partitions de table. Par exemple, une requête avec un filtre qui sélectionne uniquement un pour cent des entités dans une partition mais qui analysent toutes les entités dans une partition devrez accèdent à chaque entité. Chaque entité lue comptera dans le nombre total de transactions dans cette partition ; Par conséquent, vous pouvez rapidement atteindre les cibles extensibilité élevées.

> [AZURE.NOTE] Vos tests de performances doit faire apparaître les structures de requêtes inefficace dans votre application.

### <a name="metrics-show-an-increase-in-PercentTimeoutError"></a>Indicateurs montrent une augmentation dans PercentTimeoutError

Vos mesures indiquer une augmentation **PercentTimeoutError** pour un de vos services de stockage. En même temps, le client reçoit une grande quantité de messages d’état HTTP « délai d’expiration de 500 opération » à partir d’opérations de stockage.

> [AZURE.NOTE] Erreurs de délai d’attente temporairement en tant que le service de stockage peuvent s’afficher la charge des requêtes de soldes en déplaçant une partition vers un nouveau serveur.

La mesure **PercentTimeoutError** est une agrégation des mesures suivantes : **ClientTimeoutError**, **AnonymousClientTimeoutError**, **SASClientTimeoutError**, **ServerTimeoutError**, **AnonymousServerTimeoutError**et **SASServerTimeoutError**.

Les délais du serveur sont provoquées par une erreur sur le serveur. Les délais d’expiration du client se produisent, car une opération sur le serveur a dépassé le délai d’attente spécifié par le client ; par exemple, un client à l’aide de la bibliothèque de stockage Client pouvez définir un délai d’expiration pour une opération à l’aide de la propriété **ServerTimeout** de la classe **QueueRequestOptions** .

Délais du serveur indiquent un problème avec le service de stockage qui requiert des investigations. Vous pouvez utiliser les mesures pour voir si vous en appuyant sur les limites extensibilité du service et d’identifier les pointes de trafic qui peuvent être la cause du problème. Si le problème est intermittent, cela peut être dû à l’équilibrage de charge activité dans le service. Si le problème persiste et n’est pas dû à votre application en appuyant sur les limites extensibilité élevées du service, vous devez déclencher un problème de support. Pour les délais de client, vous devez décider si le délai d’expiration est défini sur une valeur appropriée dans le client et modifier la valeur du délai d’attente définie dans le client ou étudier comment vous pouvez améliorer les performances des opérations dans le service de stockage, par exemple par optimisation de vos requêtes de table ou réduire la taille de vos messages.

### <a name="metrics-show-an-increase-in-PercentNetworkError"></a>Indicateurs montrent une augmentation dans PercentNetworkError

Vos mesures indiquer une augmentation **PercentNetworkError** pour un de vos services de stockage. La mesure **PercentNetworkError** est une agrégation des mesures suivantes : **NetworkError**, **AnonymousNetworkError**et **SASNetworkError**. Cela se produit lorsque le service de stockage détecte une erreur réseau lorsque le client effectue une demande de stockage.

La cause la plus courante de cette erreur est un client déconnexion avant un délai d’expiration dans le service de stockage. Vous devez examiner le code dans votre client à comprendre pourquoi et quand le client est déconnecté à partir du service de stockage. Vous pouvez également utiliser Wireshark, Microsoft Message Analyzer ou Tcping pour déterminer l’origine des problèmes de connectivité réseau à partir du client. Ces outils sont décrites dans les [Appendices].

### <a name="the-client-is-receiving-403-messages"></a>Le client reçoit des messages HTTP 403 (refusé)

Si votre application cliente est levée d’erreurs HTTP 403 (refusé), une cause probable est que le client utilise une Signature de Access partagé (sa) qui a expiré lorsqu’il envoie une demande de stockage (bien que les autres causes possibles : horloge non valides, inclinaison clés et des en-têtes vides). Si une clé de sa expirée est le cas, vous verrez pas toutes les entrées dans les données du journal stockage journalisation côté serveur. Le tableau suivant montre un exemple du journal côté client générée par la bibliothèque de Client de stockage qui illustre ce problème :

Source|Niveau de détail|Niveau de détail|Id de demande de client|Texte de l’opération
---|---|---|---|---
Microsoft.WindowsAzure.Storage|Informations|3|85d077ab-...|Démarrage opération avec l’emplacement principal mode emplacement PrimaryOnly par.
Microsoft.WindowsAzure.Storage|Informations|3|85d077ab-...|Démarrage demande synchrone pour https://domemaildist.blob.core.windows.netazureimblobcontainer/blobCreatedViaSAS.txt?sv=2014-02-14&amp;sr = c&amp;si = mypolicy&amp;sig = OFnd4Rd7z01fIvh % 2BmcR6zbudIH2F5Ikm % 2FyhNYZEmJNQ % 3D&amp;version de l’api = 2014-02-14.
Microsoft.WindowsAzure.Storage|Informations|3|85d077ab-...|En attente de réponse.
Microsoft.WindowsAzure.Storage|Avertissement|2|85d077ab-...|Une exception en attendant réponse : le serveur distant a renvoyé une erreur : (403) interdit...
Microsoft.WindowsAzure.Storage|Informations|3|85d077ab-...|Réponse reçue. Code d’état = 403, ID de demande = 9d67c64a-64ed-4b0d-9515-3b14bbcdc63d, Content-MD5 = ETag =.
Microsoft.WindowsAzure.Storage|Avertissement|2|85d077ab-...|Une exception lors de l’opération : le serveur distant a renvoyé une erreur : (403) interdit...
Microsoft.WindowsAzure.Storage|Informations|3 |85d077ab-...|Vérifier si l’opération doit être retentée. Nombre de tentatives = 0, code d’état HTTP = 403, Exception = au serveur distant a renvoyé une erreur : (403) interdit...
Microsoft.WindowsAzure.Storage|Informations|3|85d077ab-...|L’emplacement du prochain a été défini pour principal, en fonction du mode d’emplacement.
Microsoft.WindowsAzure.Storage|Erreur|1|85d077ab-...|Stratégie de nouvelles tentatives n’a pas permis pour une nouvelle tentative. Échec avec le serveur distant a renvoyé une erreur : (403) interdit.

Dans ce scénario, vous devez examiner pourquoi le jeton sa arrive à expiration avant que le client envoie le jeton sur le serveur :

- En règle générale, vous ne devez pas définir une heure de début lorsque vous créez une associations de sécurité pour un client à utiliser immédiatement. S’il existe des différences de petite horloge entre l’hôte générant les associations de sécurité à l’aide de l’heure actuelle et le service de stockage, il est possible que le service de stockage recevoir une associations de sécurité qui ne sont pas valide.
- Vous ne devez pas définir un délai d’expiration très courte sur une associations de sécurité. Là encore, différences petite horloge entre l’hôte générant les associations de sécurité et le service de stockage peuvent entraîner une sa apparemment expirant au plus tôt que prévu.
- Quoi le paramètre de version sous la clé associations de sécurité (par exemple **ED = 04/2015 / 05**) correspond à la version de la bibliothèque de Client de stockage que vous utilisez ? Nous vous recommandons d’utiliser toujours la dernière version de la [Bibliothèque de stockage Client](https://www.nuget.org/packages/WindowsAzure.Storage/).
- Si vous régénérez votre touches d’accès de stockage, cela peut invalider des jetons associations de sécurité existants. Cela peut être un problème si vous générez jetons associations de sécurité avec un délai d’expiration de temps pour les applications clientes à mettre en cache.

Si vous utilisez la bibliothèque de stockage Client pour générer des jetons associations de sécurité, il est facile générer un jeton valide. Toutefois, si vous êtes à l’aide de l’API REST de stockage et construire manuellement les jetons sa doivent lire attentivement la rubrique [Délégation de l’accès avec une Signature accès partagé](http://msdn.microsoft.com/library/azure/ee395415.aspx).

### <a name="the-client-is-receiving-404-messages"></a>Le client reçoit des messages HTTP 404 (non trouvée)
Si l’application cliente reçoit un message HTTP 404 (non trouvée) à partir du serveur, cela signifie que l’objet que le client a tenté d’utiliser (par exemple, une entité, un tableau, blob, conteneur ou file d’attente) n’existe pas dans le service de stockage. Il existe un certain nombre de raisons possibles pour ce faire, telles que :

- [Le client ou un autre processus précédemment supprimé l’objet]
- [Un problème d’autorisation partagés accès Signature (sa)]
- [Code JavaScript côté client n’est pas autorisé à accéder à l’objet]
- [Échec du réseau]

#### <a name="client-previously-deleted-the-object"></a>Le client ou un autre processus précédemment supprimé l’objet
Dans les scénarios où le client tente de lire, mettre à jour ou supprimer des données dans un service de stockage, il est facile d’identifier dans les journaux côté serveur une opération précédente qui supprimées de l’objet en question à partir du service de stockage. Très souvent, les données du journal indiquent qu’un autre utilisateur ou un processus supprimé l’objet. Dans le journal de stockage journalisation côté serveur, le type d’opération et des colonnes de clé de l’objet demandé affichent quand un client supprimé un objet.

Dans le scénario où un client est vous essayez d’insérer un objet, il peut ne pas être évidente pourquoi cela se traduit par une réponse 404 HTTP (non trouvé) étant donné que le client consiste à créer un nouvel objet. Toutefois, si le client consiste à créer un blob doit être en mesure de trouver le conteneur blob, si le client de création d’un message qu’il doit être en mesure de trouver une file d’attente et que le client consiste à ajouter une ligne il doit être en mesure de la table.

Vous pouvez utiliser le journaux côté client à partir de la bibliothèque de stockage Client pour plus de comprendre lorsque le client envoie des requêtes spécifiques au service de stockage.

Le journal côté client suivant généré par la bibliothèque de stockage Client illustre le problème lorsque le client ne trouve pas le conteneur pour le blob de que création. Ce journal inclut des détails des opérations de stockage suivantes :

ID de la requête|Opération
---|---
07b26a5d-...|Méthode de **DeleteIfExists** pour supprimer le conteneur blob. Notez que cette opération inclut une demande de **tête** pour vérifier l’existence du conteneur.
e2d06d78...|Méthode de **CreateIfNotExists** pour créer le conteneur blob. Notez que cette opération inclut une demande de **tête** vérifie l’existence du conteneur. La **tête** renvoie un message 404 mais continue.
de8b1c3c-...|Méthode de **UploadFromStream** pour créer le blob. Échec de la demande de **placer** un message 404

Entrées du journal :

ID de la requête |  Texte de l’opération
---|---
07b26a5d-...|Démarrage de la requête synchrone à https://domemaildist.blob.core.windows.net/azuremmblobcontainer.
07b26a5d-...|StringToSign = HEAD...x-ms-client-request-id:07b26a5d-...x-ms-date:Tue, 03 jui 2014 10:33:11 GMT.x-ms-version:2014-02-14./domemaildist/azuremmblobcontainer.restype:container.
07b26a5d-...|En attente de réponse.
07b26a5d-... | Réponse reçue. Code d’état = 200, ID de demande = eeead849... Contenu-MD5 = ETag = &quot;0x8D14D2DC63D059B&quot;.
07b26a5d-... | En-têtes de réponse ont été traitées correctement, continuer le reste de l’opération.
07b26a5d-... | Téléchargement de corps de la réponse.
07b26a5d-... | Opération terminée avec succès.
07b26a5d-... | Démarrage de la requête synchrone à https://domemaildist.blob.core.windows.net/azuremmblobcontainer.
07b26a5d-... | StringToSign = DELETE...x-ms-client-request-id:07b26a5d-...x-ms-date:Tue, 03 juin 2014 10:33:12 GMT.x-ms-version:2014-02-14./domemaildist/azuremmblobcontainer.restype:container.
07b26a5d-... | En attente de réponse.
07b26a5d-... | Réponse reçue. Code d’état = 202, ID de demande = 6ab2a4cf-..., Content-MD5 = ETag =.
07b26a5d-... | En-têtes de réponse ont été traitées correctement, continuer le reste de l’opération.
07b26a5d-... | Téléchargement de corps de la réponse.
07b26a5d-... | Opération terminée avec succès.
e2d06d78-... | Démarrage de la requête asynchrone pour https://domemaildist.blob.core.windows.net/azuremmblobcontainer.</td>
e2d06d78-... | StringToSign = HEAD...x-ms-client-request-id:e2d06d78-...x-ms-date:Tue, 03 jui 2014 10:33:12 GMT.x-ms-version:2014-02-14./domemaildist/azuremmblobcontainer.restype:container.
e2d06d78-...| En attente de réponse.
de8b1c3c-... | Démarrage de la requête synchrone à https://domemaildist.blob.core.windows.net/azuremmblobcontainer/blobCreated.txt.
de8b1c3c-... |  StringToSign = place... 64.qCmF+TQLPhq/YYK50mP9ZQ==...x-MS-BLOB-type:BlockBlob.x-MS-client-Request-ID:de8b1c3c-...x-MS-date:TUE, 03 juin 2014 10:33:12 GMT.x-ms-version:2014-02-14./domemaildist/azuremmblobcontainer/blobCreated.txt.
de8b1c3c-... | Préparation pour écrire des données de la requête.
e2d06d78-... | Une exception en attendant réponse : le serveur distant a renvoyé une erreur : (404) introuvable...
e2d06d78-... | Réponse reçue. Code d’état = 404, ID de demande = 353ae3bc-..., Content-MD5 = ETag =.
e2d06d78-... | En-têtes de réponse ont été traitées correctement, continuer le reste de l’opération.
e2d06d78-... | Téléchargement de corps de la réponse.
e2d06d78-... | Opération terminée avec succès.
e2d06d78-... | Démarrage de la requête asynchrone pour https://domemaildist.blob.core.windows.net/azuremmblobcontainer.
e2d06d78-...|StringToSign = place... 0...x-MS-client-Request-ID:e2d06d78-...x-MS-date:TUE, 03 juin 2014 10:33:12 GMT.x-ms-version:2014-02-14./domemaildist/azuremmblobcontainer.restype:container.
e2d06d78-... | En attente de réponse.
de8b1c3c-... | Écrire des données de demande.
de8b1c3c-... | En attente de réponse.
e2d06d78-... | Une exception en attendant réponse : le serveur distant a renvoyé une erreur : (409) conflit...
e2d06d78-... | Réponse reçue. Code d’état = 409, ID de demande = c27da20e-..., Content-MD5 = ETag =.
e2d06d78-... | Téléchargement de corps de la réponse d’erreur.
de8b1c3c-... | Une exception en attendant réponse : le serveur distant a renvoyé une erreur : (404) introuvable...
de8b1c3c-... | Réponse reçue. Code d’état = 404, ID de demande = 0eaeab3e-..., Content-MD5 = ETag =.
de8b1c3c-...| Une exception lors de l’opération : le serveur distant a renvoyé une erreur : (404) introuvable...
de8b1c3c-... | Stratégie de nouvelles tentatives n’a pas permis pour une nouvelle tentative. Échec avec le serveur distant a renvoyé une erreur : (404) introuvable...
e2d06d78-... | Stratégie de nouvelles tentatives n’a pas permis pour une nouvelle tentative. Échec avec le serveur distant a renvoyé une erreur : (409) conflit...

Dans cet exemple, le journal indique que le client est entrelacement demandes à partir de la méthode **CreateIfNotExists** (demande id e2d06d78...) avec les requêtes à partir de la méthode **UploadFromStream** (de8b1c3c-...) ; Ce problème se produit car l’application cliente appelle ces méthodes asynchrone. Vous devez modifier le code asynchrone dans le client pour vous assurer qu’il crée le conteneur avant d’essayer de télécharger des données dans un blob dans ce conteneur. Dans l’idéal, vous devez créer toutes vos conteneurs à l’avance.

#### <a name="SAS-authorization-issue"></a>Un problème d’autorisation partagés accès Signature (sa)

Si l’application cliente tente d’utiliser une clé de sa qui n’inclut pas les autorisations nécessaires pour l’opération, le service de stockage renvoie un message HTTP 404 (non trouvée) au client. En même temps, vous verrez également une valeur non nulle pour **SASAuthorizationError** dans les mesures.

Le tableau suivant montre un exemple de message journal côté serveur dans le fichier journal de journalisation de stockage :

<table>
  <tr>
    <td>Demander l’heure de début</td>
    <td>2014-05-30T06:17:48.4473697Z</td>
  </tr>
  <tr>
    <td>Type d’opération</td>
    <td>GetBlobProperties</td>
  </tr>
  <tr>
    <td>État de la demande</td>
    <td>SASAuthorizationError</td>
  </tr>
  <tr>
    <td>Code d’état HTTP</td>
    <td>404</td>
  </tr>
  <tr>
    <td>Type d’authentification</td>
    <td>Associations de sécurité</td>
  </tr>
  <tr>
    <td>Type de service</td>
    <td>Objets BLOB</td>
  </tr>
  <tr>
    <td>URL de la requête</td>
    <td>
    https://domemaildist.BLOB.Core.Windows.NET/azureimblobcontainer/blobCreatedViaSAS.txt?sv=2014-02-14&amp;a ; sr = c&amp;a ; si = mypolicy&amp;a ; sig = XXXXX&amp;a ; la version de l’api = 2014-02-14&amp;trajectoires</td>
  </tr>
  <tr>
    <td>En-tête de l’id de requête</td>
    <td>a1f348d5-8032-4912-93ef-b393e5252a3b</td>
  </tr>
  <tr>
    <td>ID de demande de client</td>
    <td>2d064953-8436-4ee0-aa0c-65cb874f7929</td>
  </tr>
</table>

Vous devez examiner pourquoi votre application cliente tente d’effectuer une opération qu’il n’a pas reçu d’autorisations pour.

#### <a name="JavaScript-code-does-not-have-permission"></a>Code JavaScript côté client n’est pas autorisé à accéder à l’objet

Si vous utilisez un client JavaScript et le service de stockage renvoie des messages HTTP 404, recherchez les erreurs JavaScript suivantes dans le navigateur :

    SEC7120: Origin http://localhost:56309 not found in Access-Control-Allow-Origin header.
    SCRIPT7002: XMLHttpRequest: Network Error 0x80070005, Access is denied.

> [AZURE.NOTE] Vous pouvez utiliser les outils de développement F12 dans Internet Explorer pour suivre les messages échangés entre le navigateur et le service de stockage lorsque vous résolvez des problèmes de JavaScript côté client.

Ces erreurs se produisent parce que le navigateur web mettent en œuvre, la restriction de sécurité [même stratégie d’origine](http://www.w3.org/Security/wiki/Same_Origin_Policy) qui empêche l’appel d’une API dans un autre domaine à partir du domaine à d' que la page provient une page web.

Pour contourner le problème JavaScript, vous pouvez configurer croisées Origin ressource partage (CORS) pour le service de stockage sur que le client accède. Pour plus d’informations, voir [prise en charge le partage de ressources Cross-Origin (CORS) des Services de stockage Azure](http://msdn.microsoft.com/library/azure/dn535601.aspx).

L’exemple de code suivant montre comment configurer votre service d’objets blob pour autoriser un code JavaScript en cours d’exécution dans le domaine Contoso pour accéder à un blob dans votre service de stockage blob :

    CloudBlobClient client = new CloudBlobClient(blobEndpoint, new StorageCredentials(accountName, accountKey));
    // Set the service properties.
    ServiceProperties sp = client.GetServiceProperties();
    sp.DefaultServiceVersion = "2013-08-15";
    CorsRule cr = new CorsRule();
    cr.AllowedHeaders.Add("*");
    cr.AllowedMethods = CorsHttpMethods.Get | CorsHttpMethods.Put;
    cr.AllowedOrigins.Add("http://www.contoso.com");
    cr.ExposedHeaders.Add("x-ms-*");
    cr.MaxAgeInSeconds = 5;
    sp.Cors.CorsRules.Clear();
    sp.Cors.CorsRules.Add(cr);
    client.SetServiceProperties(sp);

#### <a name="network-failure"></a>Échec du réseau

Dans certains cas, les paquets réseau perdues peuvent entraîner le service de stockage renvoyer des messages HTTP 404 au client. Par exemple, lorsque votre application cliente est suppression d’une entité à partir du service de tableau que vous consultez le client lever une exception de stockage reporting un « HTTP 404 (non trouvée) » le message d’état à partir du service de la table. Lorsque vous examinez la table dans le service de stockage de table, vous constatez que le service supprimez l’entité comme demandé.

Détails de l’exception dans le client incluent l’id de demande (7e84f12d...) affecté par le service de table pour la demande : vous pouvez utiliser ces informations pour repérer les détails de demande dans les journaux côté serveur stockage en effectuant une recherche dans la colonne **id-en-tête de demande** dans le fichier journal. Vous pouvez également utiliser les mesures pour identifier lorsque telles que des échecs de se produisent, puis rechercher les fichiers journaux en fonction du temps que les mesures enregistrement cette erreur. Cette entrée de journal indique que la suppression a échoué avec un message d’état « HTTP (404) Client autre erreur ». L’entrée de journal même comprend également l’id de demande généré par le client dans la colonne **id client-demande** (813ea74f...).

Le journal côté serveur comprend également une autre entrée avec la même valeur **id de demande de client** (813ea74f...) pour une opération de suppression réussie pour la même entité et à partir du même client. Cette opération de suppression réussie a eu lieu très peu de temps avant de supprimer l’échec de la demande.

La cause probable de ce scénario est que le client a envoyé une demande de suppression de l’entité dans le service de tableau, ce qui a réussi, mais n’a pas reçu un accusé de réception à partir du serveur (par exemple en raison d’un problème temporaire réseau). Le client puis automatiquement retentée l’opération (en utilisant le même **id de demande de client**), et cette nouvelle tentative a échoué car l’entité a déjà été supprimée.

Si ce problème survient fréquemment, vous devez examiner pourquoi le client ne parvient pas à recevoir des accusés de réception du service de table. Si le problème est intermittent, intercepter l’erreur « HTTP (404) non trouvé » et se connecter dans le client, mais autoriser le client continuer.

### <a name="the-client-is-receiving-409-messages"></a>Le client reçoit des messages HTTP 409 (conflit)

Le tableau suivant montre un extrait du journal côté serveur pour deux opérations du client : **DeleteIfExists** suivie immédiatement **CreateIfNotExists** en utilisant le même nom de conteneur blob. Notez que chaque résultats de l’opération client dans deux requêtes envoyées au serveur, tout d’abord une demande de **GetContainerProperties** pour vérifier si le conteneur existe, suivi de la requête **DeleteContainer** ou **CreateContainer** .

Horodatage|Opération|Résultat|Nom du conteneur|Id de demande de client
---|---|---|---|---
05:10:13.7167225|GetContainerProperties|200|mmcont|c9f52c89-...
05:10:13.8167325|DeleteContainer|202|mmcont|c9f52c89-...
05:10:13.8987407|GetContainerProperties|404|mmcont|bc881924-...
05:10:14.2147723|CreateContainer|409|mmcont|bc881924-...

Le code de l’application cliente supprime et recrée ensuite immédiatement un conteneur blob en utilisant le même nom : la méthode **CreateIfNotExists** (Client demander ID bc881924-...) finit par échouer avec l’erreur HTTP 409 (conflit). Quand un client supprime des conteneurs blob, des tableaux ou des files d’attente il existe une brève période avant le nom du nouveau disponible.

L’application cliente doit utiliser des noms de conteneur unique chaque fois qu’il crée de nouveaux conteneurs si le modèle de supprimer/recréer est courantes.

### <a name="metrics-show-low-percent-success"></a>Métrique affiche PercentSuccess faible ou d’entrées de journal analytique avoir opérations statut transaction de ClientOtherErrors

La métrique **PercentSuccess** capture le pourcentage d’opérations réussies en fonction de leur Code d’état HTTP. Opérations avec des codes de statut de 2XX comptent comme étant réussies, alors que les opérations avec des codes d’état dans les plages 3XX, 4XX et 5XX sont considérées comme des échecs et réduire la valeur métrique **PercentSucess** . Ces opérations sont enregistrées dans les fichiers journaux de stockage côté serveur, avec un statut de **ClientOtherErrors**transaction.

Il est important de noter que ces opérations ont été apportées et par conséquent ne concernent pas les autres mesures de disponibilité. Voici quelques exemples d’opérations qui s’exécuter avec succès, mais qui peut entraîner des codes d’état HTTP échouées :
- **ResourceNotFound** (Non trouvé 404), par exemple à partir d’une requête GET à un blob qui n’existe pas.
- **ResouceAlreadyExists** (Conflit 409), par exemple à partir d’une opération de **CreateIfNotExist** où la ressource existe déjà.
- **ConditionNotMet** (Non modifié 304), par exemple à partir d’une opération conditionnelle tels que lorsqu’un client envoie une valeur **ETag** et un en-tête HTTP **If-None-Match** pour demander une image uniquement si elle a été mis à jour depuis la dernière opération.

Vous pouvez rechercher une liste des codes d’erreur courants API REST renvoyant les services de stockage sur la page de [Codes d’erreur courants reste API](http://msdn.microsoft.com/library/azure/dd179357.aspx).

### <a name="capacity-metrics-show-an-unexpected-increase"></a>Mesures de capacité affichent une augmentation inattendue de l’utilisation de la capacité de stockage


Si vous voyez soudain, des modifications inattendues dans Utilisation de la capacité dans votre compte de stockage, que vous pouvez examiner les raisons en premier consultant votre métriques de disponibilité ; par exemple, augmente le nombre de suppression en échec demandes susceptibles d’entraîner une augmentation de stockage d’objets blob que vous utilisez comme les opérations de nettoyage spécifiques de l’application que vous pouvez vous attendre à être libérer de l’espace ne fonctionne pas comme prévu (par exemple, car les jetons sa utilisés pour libérer de l’espace ont expiré).

### <a name="you-are-experiencing-unexpected-reboots"></a>Vous rencontrez inattendus redémarrage de Machines virtuelles Azure qui ont un grand nombre de disques durs virtuels joints

Si une Machine virtuelle Azure (machine virtuelle) comporte un grand nombre de disques durs virtuels joints qui se trouvent dans le même compte de stockage, vous pourriez dépasser les cibles extensibilité élevées pour un compte de stockage individuels à l’origine de la machine virtuelle échec. Vous devez vérifier les mesures minutes pour le compte de stockage (**TotalRequests**/**TotalIngress**/**TotalEgress**) les pointes dépassant les cibles extensibilité élevées pour un compte de stockage. Consultez la section «[qu'afficher les mesures augmente PercentThrottlingError]» pour vous aider à déterminer si la limitation s’est produite sur votre compte de stockage.

En règle générale, chaque entrée individuelle ou une opération de sortie sur un disque dur virtuel à partir d’une Machine virtuelle se traduit par **Page obtenir** ou **Placer** opérations sur l’objets blob page sous-jacente. Par conséquent, vous pouvez utiliser les sorties par estimée pour votre environnement pour optimiser les disques durs virtuels combien vous pouvez avoir dans un compte de stockage unique basé sur le comportement spécifique de votre application. Nous ne recommandons pas avoir plus de 40 disques dans un compte de stockage unique. Voir [extensibilité du stockage Azure et des objectifs de Performance](storage-scalability-targets.md) pour plus d’informations des cibles extensibilité élevées en cours pour les comptes de stockage, notamment le taux de demande total et la bande passante totale pour le type de compte de stockage que vous utilisez.
Si vous dépassez les cibles extensibilité élevées pour votre compte de stockage, vous devez placer vos disques durs virtuels dans plusieurs comptes de stockage différents afin de réduire l’activité dans chaque compte.

### <a name="your-issue-arises-from-using-the-storage-emulator"></a>Votre problème se pose d’utiliser l’émulateur de stockage de développement ou de test

En règle générale, vous utilisez l’émulateur de stockage pendant le développement et effectuez un test pour éviter la configuration minimale requise pour un compte de stockage Azure. Les problèmes courants qui peuvent survenir lorsque vous utilisez l’émulateur de stockage sont les suivantes :

- [Fonctionnalité « X » ne fonctionne pas dans l’émulateur de stockage]
- [Erreur « la valeur pour un des en-têtes HTTP n’est pas dans le format approprié » lors de l’utilisation de l’émulateur de stockage]
- [Exécution de l’émulateur de stockage nécessite des privilèges d’administrateur]

#### <a name="feature-X-is-not-working"></a>Fonctionnalité « X » ne fonctionne pas dans l’émulateur de stockage

L’émulateur de stockage ne prend en charge toutes les fonctionnalités des services de stockage Azure tels que le service de fichiers. Pour plus d’informations, voir [utiliser l’émulateur de stockage Azure de développement et de test](storage-use-emulator.md).

Pour les fonctionnalités qui l’émulateur de stockage ne prend pas en charge, utilisez le service de stockage Azure dans le cloud.

#### <a name="error-HTTP-header-not-correct-format"></a>Erreur « la valeur pour un des en-têtes HTTP n’est pas dans le format approprié » lors de l’utilisation de l’émulateur de stockage

Vous testez votre application qui utilisent la bibliothèque de Client de stockage par rapport à l’émulateur de stockage local et méthode appelle tels que **CreateIfNotExists** échouer avec le message d’erreur « la valeur pour un des en-têtes HTTP n’est pas dans le format approprié. » Cela indique que la version de l’émulateur de stockage que vous utilisez ne prend pas en charge la version de la bibliothèque de client de stockage que vous utilisez. La bibliothèque de stockage Client ajoute l' en-tête **x-ms-version** à toutes les demandes que rend. Si l’émulateur de stockage ne reconnaît pas la valeur de l’en-tête **x-ms-version** , il refuse la demande.

Vous pouvez utiliser les journaux de stockage bibliothèque Client pour afficher la valeur de l' **en-tête x-ms-version** qu’il envoie. Vous pouvez également voir la valeur de l' **en-tête x-ms-version** si vous utilisez Fiddler pour repérer les requêtes à partir de votre application cliente.

Ce scénario se produit généralement si vous installez et utilisez la dernière version de la bibliothèque de Client de stockage sans mettre à jour l’émulateur de stockage. Vous devez installer la dernière version de l’émulateur de stockage, ou utilisez le stockage cloud au lieu de l’émulateur de développement et de test.

#### <a name="storage-emulator-requires-administrative-privileges"></a>Exécution de l’émulateur de stockage nécessite des privilèges d’administrateur

Vous êtes invité vos informations d’identification d’administrateur lorsque vous exécutez l’émulateur de stockage. Cela se produit uniquement lorsque l’initialisation de l’émulateur de stockage pour la première fois. Une fois que vous avez initialisé l’émulateur de stockage, vous n’avez pas besoin de privilèges d’administrateur pour exécuter à nouveau.

Pour plus d’informations, voir [utiliser l’émulateur de stockage Azure de développement et de test](storage-use-emulator.md). Notez que vous pouvez également initialisation l’émulateur de stockage dans Visual Studio, qui nécessitent également des privilèges d’administrateur.

### <a name="you-are-encountering-problems-installing-the-Windows-Azure-SDK"></a>Vous rencontrez des problèmes d’installation du Kit de développement Azure pour .NET

Lorsque vous essayez d’installer le Kit de développement, il échoue essayez d’installer l’émulateur de stockage sur votre ordinateur local. Le journal d’installation contient l’un des messages suivants :

- CAQuietExec : Erreur : Impossible d’accéder aux instance SQL
- CAQuietExec : Erreur : Impossible de créer la base de données

La raison est un problème d’installation LocalDB existante. Par défaut, l’émulateur de stockage utilise LocalDB pour conserver les données lors de la simulation les services de stockage Azure. Vous pouvez réinitialiser votre instance LocalDB en exécutant les commandes suivantes dans une fenêtre d’invite de commandes avant d’essayer d’installer le Kit de développement.

    sqllocaldb stop v11.0
    sqllocaldb delete v11.0
    delete %USERPROFILE%\WAStorageEmulatorDb3*.*
    sqllocaldb create v11.0

La commande **Supprimer** supprime tous les anciens fichiers de base de données dans les installations précédentes de l’émulateur de stockage.

### <a name="you-have-a-different-issue-with-a-storage-service"></a>Vous avez un autre problème avec un service de stockage

Si les sections de dépannage précédentes n’incluent pas le problème que vous rencontrez avec un service de stockage, adoptez l’approche suivante pour diagnostiquer et de résoudre votre problème.

- Vérifier vos mesures pour savoir s’il y a aucune modification dans votre comportement attendu de la ligne de base. À partir d’indicateurs, vous pourrez déterminer si le problème est temporaire ou permanent, et les opérations de stockage affecte le problème.
- Vous pouvez utiliser les informations des indicateurs pour vous aider à rechercher vos données de journal côté serveur pour plus d’informations sur les erreurs éventuelles qui se produisent. Ces informations peuvent vous aider à résoudre le problème.
- Si les informations contenues dans les journaux côté serveur ne sont pas suffisants pour résoudre le problème avec succès, vous pouvez utiliser les journaux côté client une bibliothèque de stockage Client d’étudier le comportement de votre application cliente et d’outils tels que Fiddler Wireshark et Microsoft Message Analyzer à votre réseau.

Pour plus d’informations sur l’utilisation de Fiddler, voir «[appendice 1 : à l’aide de Fiddler pour capturer le trafic HTTP et HTTPS]. »

Pour plus d’informations sur l’utilisation de Wireshark, voir «[appendice 2 : utilisation de Wireshark pour capturer le trafic réseau]. »

Pour plus d’informations sur l’Analyseur de Message de Microsoft, voir «[appendice 3 : à l’aide de l’Analyseur de Message Microsoft pour capturer le trafic réseau]. »

## <a name="appendices"></a>Appendices

Appendices décrivent plusieurs outils que vous pouvez trouver utile lorsque vous êtes diagnostic et résolution des problèmes avec le stockage Azure (et d’autres services). Ces outils ne font pas partie de stockage Azure et certains sont les produits tiers. Par conséquent, les outils mentionnés dans ces annexes ne sont pas couverts par un contrat de support que vous devrez peut-être avec Microsoft Azure ou stockage Azure, et par conséquent, dans le cadre de votre processus d’évaluation, vous devez examiner les options de prise en charge et de gestion de licences disponibles à partir des fournisseurs de ces outils.

### <a name="appendix-1"></a>Appendice 1 : À l’aide de Fiddler pour capturer le trafic HTTP et HTTPS

[Fiddler](http://www.telerik.com/fiddler) est un outil utile pour analyser le trafic HTTP et HTTPS entre votre application cliente et le service de stockage Azure que vous utilisez.

> [AZURE.NOTE] Fiddler peut décoder le trafic HTTPS ; Vous devez lire la documentation Fiddler avec soin à comprendre le fonctionnement de ce, ainsi que de comprendre les implications de sécurité.

Cette annexe fournit une brève présentation détaillée des comment configurer Fiddler pour capturer le trafic entre l’ordinateur local dans lequel vous avez installé Fiddler et les services de stockage Azure.

Une fois que vous avez lancé Fiddler, il se lance capture du trafic HTTP et HTTPS sur votre ordinateur local. Certaines commandes utiles permettant de contrôler Fiddler sont les suivantes :

- Arrêtez et démarrez la capture du trafic. Dans le menu principal, cliquez sur **fichier** , puis sur **Le trafic capturer** pour activer ou désactiver la capture et désactiver.
- Enregistrer les données le trafic capturé. Dans le menu principal, accédez au **fichier**, cliquez sur **Enregistrer**, puis cliquez sur **Toutes les Sessions**: cela vous permet d’enregistrer le trafic dans un fichier d’Archive de Session. Vous pouvez recharger une Archive Session ultérieurement pour l’analyse, ou l’envoyer si demande de support technique Microsoft.

Pour limiter la quantité de trafic Fiddler capture, vous pouvez utiliser des filtres que vous configurez dans l’onglet **filtres** . La capture d’écran suivante montre un filtre qui capture uniquement le trafic envoyé au point de terminaison de stockage **contosoemaildist.table.core.windows.net** :

![][5]

### <a name="appendix-2"></a>Appendice 2 : Utilisation de Wireshark pour capturer le trafic réseau

[Wireshark](http://www.wireshark.org/) est un analyseur de protocole réseau qui vous permet d’afficher des informations détaillées paquets pour un large éventail de protocoles réseau.

La procédure suivante vous montre comment faire pour capturer les informations détaillées de paquets pour le trafic de l’ordinateur local où vous avez installé Wireshark au service de tableau dans votre compte de stockage Azure.

1.  Lancez Wireshark sur votre ordinateur local.
2.  Dans la section **Démarrer** , sélectionnez l’interface de réseau local ou les interfaces qui sont connectés à internet.
3.  Cliquez sur **Options de saisie**.
4.  Ajouter un filtre à la zone de texte **Filtre de Capture** . Par exemple, **contosoemaildist.table.core.windows.net hôte** configurera Wireshark pour capturer uniquement les paquets envoyés vers ou à partir du point de terminaison du service de table dans le compte de stockage **contosoemaildist** . Consultez la [liste complète des filtres de capturer](http://wiki.wireshark.org/CaptureFilters).

    ![][6]

5.  Cliquez sur **Démarrer**. Wireshark est prêt à capturer tous les paquets d’envoi vers ou à partir du point de terminaison du service tableau que vous utilisez votre application cliente sur votre ordinateur local.
6.  Lorsque vous avez terminé, dans le menu principal, cliquez sur **Capture** , puis sur **Arrêter**.
7.  Pour enregistrer les données capturées dans un fichier de capturer Wireshark, dans le menu principal, cliquez sur **fichier** , puis sur **Enregistrer**.

WireShark surligne toutes les erreurs qui existent dans la fenêtre **packetlist** . Vous pouvez également utiliser la fenêtre **Informations Expert** (cliquez sur **analyse**, puis **Informations Expert**) pour afficher une synthèse des erreurs et avertissements.

![][7]

Vous pouvez également choisir d’afficher les données TCP comme la couche d’application le voit en cliquant sur les données TCP et en sélectionnant **Suivez flux TCP**. Ceci est particulièrement utile si vous avez capturé le vidage sans un filtre de capture. Pour plus d’informations, voir [suite de flux de données TCP] (http://www.wireshark.org/docs/wsug_html_chunked/ChAdvFollowTCPSection.html).

![][8]

> [AZURE.NOTE] Pour plus d’informations sur l’utilisation de Wireshark, consultez le [Guide de l’utilisateur Wireshark](http://www.wireshark.org/docs/wsug_html_chunked).

### <a name="appendix-3"></a>Appendice 3 : À l’aide de l’Analyseur de Message de Microsoft pour capturer le trafic réseau

Vous pouvez utiliser l’Analyseur de Message de Microsoft pour capturer le trafic HTTP et HTTPS de façon similaire à Fiddler et capturer le trafic réseau de la même façon pour Wireshark.

#### <a name="configure-a-web-tracing-session-using-microsoft-message-analyzer"></a>Configurer une session de suivi web à l’aide de l’Analyseur de Message de Microsoft

Pour configurer une session de suivi web pour le trafic HTTP et HTTPS à l’aide de l’Analyseur de Message de Microsoft, exécutez l’application Microsoft Message Analyzer et puis, dans le menu **fichier** , cliquez sur **Capture/Trace**. Dans la liste des scénarios trace disponibles, sélectionnez **Proxy Web**. Puis, dans le panneau de **Configuration du scénario de suivi** , dans la zone de texte **HostnameFilter** , ajoutez les noms de vos points de terminaison de stockage (vous pouvez rechercher ces noms dans le [Portail Azure](https://portal.azure.com)). Par exemple, si le nom de votre compte de stockage Azure est **contosodata**, vous devez ajouter ce qui suit à la zone de texte **HostnameFilter** :

    contosodata.blob.core.windows.net contosodata.table.core.windows.net contosodata.queue.core.windows.net

> [AZURE.NOTE] Un caractère d’espace sépare les noms d’hôte.

Lorsque vous êtes prêt à commencer à collecter des données de suivi, cliquez sur le bouton **Démarrer avec** .

Pour plus d’informations sur le suivi de l’Analyseur de Message Microsoft **Web Proxy** , voir [Microsoft-PEF-WebProxy fournisseur](http://technet.microsoft.com/library/jj674814.aspx).

Le suivi de **Proxy Web** intégré dans l’Analyseur de Message de Microsoft est basé sur Fiddler ; Il peut capturer le trafic HTTPS côté client et afficher des messages HTTPS non chiffrés. Le suivi des **Proxy Web** fonctionnement en configurant un proxy local pour l’ensemble du trafic HTTP et HTTPS qui permet d’accéder aux messages non chiffrés.

#### <a name="diagnosing-network-issues-using-microsoft-message-analyzer"></a>Diagnostiquer les problèmes de réseau à l’aide de l’Analyseur de Message de Microsoft

Outre le suivi de l’Analyseur de Message Microsoft **Web Proxy** pour capturer les détails du trafic HTTP/HTTPs entre l’application cliente et le service de stockage, vous pouvez également utiliser le suivi de **Couche de liaison locale** intégré pour capturer les informations de paquets réseau. Ainsi, vous permettent de capturer des données similaires à celles que vous pouvez capturer avec Wireshark et diagnostiquer les problèmes de réseau tels que les paquets ignorés.

La capture d’écran suivante montre un exemple trace **Couche de liaison locale** avec certains messages **d’information** dans la colonne **DiagnosisTypes** . Cliquez sur une icône dans la colonne **DiagnosisTypes** affiche les détails du message. Dans cet exemple, le serveur retransmission message #305, car il n’a pas reçu un accusé de réception à partir du client :

![][9]

Lorsque vous créez la session de suivi dans l’Analyseur de Message de Microsoft, vous pouvez spécifier des filtres pour réduire la quantité de bruit dans la trace. Dans la page **capturer / repérer** l’endroit où vous définissez la trace, cliquez sur le lien **configurer** en regard de **Microsoft-Windows-NDIS-PacketCapture**. La capture d’écran suivante montre une configuration qui permet de filtrer le trafic TCP pour les adresses IP de trois services de stockage :

![][10]

Pour plus d’informations sur la trace Microsoft Message analyseur Local lien calque, voir [Microsoft-PEF-NDIS-PacketCapture fournisseur](http://technet.microsoft.com/library/jj659264.aspx).

### <a name="appendix-4"></a>Appendice 4 : À l’aide d’Excel pour afficher les mesures et les données du journal

De nombreux outils permettent de télécharger les données métriques de stockage du stockage de table Azure dans un format délimité qui facilite le charger les données dans Excel pour l’affichage et l’analyse. Données de journalisation de stockage depuis le stockage blob Azure se trouvent déjà dans un format délimité par des tabulations que vous pouvez charger dans Excel. Toutefois, vous devrez ajouter des titres de colonne appropriée en fonction des informations au [Format de stockage du journal Analytique](http://msdn.microsoft.com/library/azure/hh343259.aspx) et [Schéma de la Table Analytique métriques de stockage](http://msdn.microsoft.com/library/azure/hh343264.aspx).

Pour importer vos données de journalisation de stockage dans Excel après que l’avoir téléchargé depuis le stockage blob :

- Dans le menu **données** , cliquez sur **à partir du texte**.
- Recherchez le fichier journal que vous souhaitez afficher et cliquez sur **Importer**.
- Étape 1 de l' **Assistant Importation de texte**, sélectionnez **délimité**.

Lors de l’étape 1 de l' **Assistant Importation de texte**, sélectionnez **point virgule** comme séparateur uniquement et choisissez guillemet double comme **identificateur de texte**. Cliquez sur **Terminer** , puis choisissez où vous souhaitez placer les données dans votre classeur.

### <a name="appendix-5"></a>Appendice 5 : Surveillance avec des aperçus d’Application pour Visual Studio Team Services

Vous pouvez également utiliser la fonctionnalité d’analyse de l’Application pour Visual Studio Team Services dans le cadre de vos performances et analyse de la disponibilité. Cet outil peut :

- Vérifiez que votre service web est disponible et injoignable. Si votre application est un site web ou une application d’appareil qui utilise un service web, il peut tester votre URL toutes les n minutes à partir d’emplacements dans le monde entier et vous permettent de savoir si un problème est.
- Rapidement diagnostiquer les problèmes de performances ou des exceptions dans votre service web. Découvrez si processeur ou autres ressources sont étirées, obtenez traces de la pile d’exceptions et parcourir facilement traces journal. Si les performances de l’application sont inférieur à limites acceptables, nous pouvons vous envoyer un message électronique. Vous pouvez contrôler les services web .NET et Java.

Vous trouverez davantage d’informations sur [Nouveautés Application Insights ?](../application-insights/app-insights-overview.md).

<!--Anchors-->
[Introduction]: #introduction
[Comment ce guide est organisé]: #how-this-guide-is-organized

[Votre service de stockage de surveillance]: #monitoring-your-storage-service
[État du service de surveillance]: #monitoring-service-health
[Analyse de la capacité]: #monitoring-capacity
[Analyse de la disponibilité]: #monitoring-availability
[Analyse des performances]: #monitoring-performance

[Diagnostiquer les problèmes de stockage]: #diagnosing-storage-issues
[Problèmes d’intégrité du service]: #service-health-issues
[Problèmes de performances]: #performance-issues
[Diagnostiquer les erreurs]: #diagnosing-errors
[Problèmes d’émulateur de stockage]: #storage-emulator-issues
[Outils de journalisation de stockage]: #storage-logging-tools
[À l’aide des outils de journalisation de réseau]: #using-network-logging-tools

[Suivi de bout en bout]: #end-to-end-tracing
[Corrélation des données du journal]: #correlating-log-data
[ID de demande de client]: #client-request-id
[ID de demande de serveur]: #server-request-id
[Horodatages]: #timestamps

[Conseils de dépannage]: #troubleshooting-guidance
[Métrique affiche AverageE2ELatency haute et faible AverageServerLatency]: #metrics-show-high-AverageE2ELatency-and-low-AverageServerLatency
[Métrique affiche AverageE2ELatency basse et AverageServerLatency faible, mais le client rencontre une latence élevée]: #metrics-show-low-AverageE2ELatency-and-low-AverageServerLatency
[Afficher les mesures AverageServerLatency haute]: #metrics-show-high-AverageServerLatency
[Vous rencontrez des retards inattendus dans une file d’attente de remise des messages]: #you-are-experiencing-unexpected-delays-in-message-delivery

[Indicateurs montrent une augmentation dans PercentThrottlingError]: #metrics-show-an-increase-in-PercentThrottlingError
[Augmentation transitoire de PercentThrottlingError]: #transient-increase-in-PercentThrottlingError
[Augmentation permanente PercentThrottlingError erreur]: #permanent-increase-in-PercentThrottlingError
[Indicateurs montrent une augmentation dans PercentTimeoutError]: #metrics-show-an-increase-in-PercentTimeoutError
[Indicateurs montrent une augmentation dans PercentNetworkError]: #metrics-show-an-increase-in-PercentNetworkError

[Le client reçoit des messages HTTP 403 (refusé)]: #the-client-is-receiving-403-messages
[Le client reçoit des messages HTTP 404 (non trouvée)]: #the-client-is-receiving-404-messages
[Le client ou un autre processus précédemment supprimé l’objet]: #client-previously-deleted-the-object
[Un problème d’autorisation partagés accès Signature (sa)]: #SAS-authorization-issue
[Code JavaScript côté client n’est pas autorisé à accéder à l’objet]: #JavaScript-code-does-not-have-permission
[Échec du réseau]: #network-failure
[Le client reçoit des messages HTTP 409 (conflit)]: #the-client-is-receiving-409-messages

[Métrique affiche PercentSuccess faible ou d’entrées de journal analytique avoir opérations statut transaction de ClientOtherErrors]: #metrics-show-low-percent-success
[Mesures de capacité affichent une augmentation inattendue de l’utilisation de la capacité de stockage]: #capacity-metrics-show-an-unexpected-increase
[Vous rencontrez inattendus redémarrage des Machines virtuelles qui ont un grand nombre de disques durs virtuels joints]: #you-are-experiencing-unexpected-reboots
[Votre problème se pose d’utiliser l’émulateur de stockage de développement ou de test]: #your-issue-arises-from-using-the-storage-emulator
[Fonctionnalité « X » ne fonctionne pas dans l’émulateur de stockage]: #feature-X-is-not-working
[Erreur « la valeur pour un des en-têtes HTTP n’est pas dans le format approprié » lors de l’utilisation de l’émulateur de stockage]: #error-HTTP-header-not-correct-format
[Exécution de l’émulateur de stockage nécessite des privilèges d’administrateur]: #storage-emulator-requires-administrative-privileges
[Vous rencontrez des problèmes d’installation du Kit de développement Azure pour .NET]: #you-are-encountering-problems-installing-the-Windows-Azure-SDK
[Vous avez un autre problème avec un service de stockage]: #you-have-a-different-issue-with-a-storage-service

[Appendices]: #appendices
[Appendice 1 : À l’aide de Fiddler pour capturer le trafic HTTP et HTTPS]: #appendix-1
[Appendice 2 : Utilisation de Wireshark pour capturer le trafic réseau]: #appendix-2
[Appendice 3 : À l’aide de l’Analyseur de Message de Microsoft pour capturer le trafic réseau]: #appendix-3
[Appendice 4 : À l’aide d’Excel pour afficher les mesures et les données du journal]: #appendix-4
[Appendice 5 : Surveillance avec des aperçus d’Application pour Visual Studio Team Services]: #appendix-5

<!--Image references-->
[1]: ./media/storage-monitoring-diagnosing-troubleshooting/overview.png
[3]: ./media/storage-monitoring-diagnosing-troubleshooting/hour-minute-metrics.png
[4]: ./media/storage-monitoring-diagnosing-troubleshooting/high-e2e-latency.png
[5]: ./media/storage-monitoring-diagnosing-troubleshooting/fiddler-screenshot.png
[6]: ./media/storage-monitoring-diagnosing-troubleshooting/wireshark-screenshot-1.png
[7]: ./media/storage-monitoring-diagnosing-troubleshooting/wireshark-screenshot-2.png
[8]: ./media/storage-monitoring-diagnosing-troubleshooting/wireshark-screenshot-3.png
[9]: ./media/storage-monitoring-diagnosing-troubleshooting/mma-screenshot-1.png
[10]: ./media/storage-monitoring-diagnosing-troubleshooting/mma-screenshot-2.png

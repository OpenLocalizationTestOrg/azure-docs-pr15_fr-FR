<properties
 pageTitle="Comprendre et résoudre les erreurs WebHCat sur HDInsight"
 description="Découvrez comment à propos des erreurs courantes renvoyées par WebHCat sur HDInsight et comment les résoudre."
 services="hdinsight"
 documentationCenter=""
 authors="Blackmist"
 manager="jhubbard"
 editor="cgronlun"
 tags="azure-portal"/>

<tags
 ms.service="hdinsight"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="big-data"
 ms.date="09/27/2016"
 ms.author="larryfr"/>

#<a name="understand-and-resolve-errors-received-from-webhcat-templeton-on-hdinsight"></a>Comprendre et résoudre les erreurs provenant de WebHCat (Templeton), sur HDInsight

Lorsque vous utilisez WebHCat (auparavant appelé Templeton,) pour l’utiliser avec HDInsight, vous pouvez recevoir des erreurs. Ce document fournit des conseils sur les erreurs courantes – pourquoi ils se produisent et que vous pouvez faire pour les résoudre.

##<a name="what-is-webhcat"></a>Quelles sont les WebHCat ?

[WebHCat](https://cwiki.apache.org/confluence/display/Hive/WebHCat) est une API REST pour couche de gestion des [HCatalog](https://cwiki.apache.org/confluence/display/Hive/HCatalog), un tableau et de stockage pour Hadoop. WebHCat est activée par défaut sur les clusters HDInsight et est utilisée par différents outils pour soumettre les tâches, obtenir le statut de la tâche, etc. sans se connecter au cluster.

##<a name="modifying-configuration"></a>Modification de la configuration

> [AZURE.IMPORTANT] Plusieurs des messages d’erreur répertoriés dans ce document dû au maximum configuré a été dépassé. Lorsque l’étape de résolution mentionne que vous pouvez modifier une valeur, vous devez utiliser une des opérations suivantes pour effectuer la modification :

* Pour les clusters **Windows** : utiliser une action de script pour configurer la valeur lors de la création de cluster. Pour plus d’informations, voir [développer les actions de script](hdinsight-hadoop-script-actions.md).

* Pour les clusters **Linux** : utilisation Ambari (web ou API REST) pour modifier la valeur. Pour plus d’informations, voir [Gérer les HDInsight à l’aide de Ambari](hdinsight-hadoop-manage-ambari.md)

###<a name="default-configuration"></a>Configuration par défaut

Les valeurs de configuration par défaut qui peuvent avoir un impact sur les performances de WebHCat ou provoquer des erreurs en cas de dépassement de ces valeurs sont les suivantes :

| Paramètre | Fonction | Valeur par défaut |
| ------- | ------------ | ------------- |
| [yarn.Scheduler.Capacity.maximum applications][maximum-applications] | Le nombre maximal de tâches pouvant être actives simultanément (en attente ou en cours d’exécution) | 10 000 |
| [procédures de templeton.Exec.max][max-procs] | Le nombre maximal de demandes pouvant être traités simultanément | 20 |
| [MapReduce.jobhistory.max-âge-ms][max-age-ms] | Le nombre de jours pendant lesquels l’historique des travaux sont conservées | 7 jours |

##<a name="too-many-requests"></a>Trop de demandes

**Code d’état HTTP**: 429

| Cause | Résolution |
| ----- | ---------- |
| Vous avez dépassé le nombre maximal de requêtes simultané pris en charge par WebHCat par minute (20 par défaut) | Réduire votre charge de travail pour vous assurer que vous n’envoyez pas plus que le nombre maximal de demandes simultanées ou augmenter la limite des demandes simultanées en modifiant `templeton.exec.max-procs`. Pour plus d’informations, consultez [configuration de modification](#modifying-configuration) |

##<a name="server-unavailable"></a>Serveur non disponible

**Code d’état HTTP**: 503

| Cause | Résolution |
| ---------------- | ------------------- |
| Cela se produit généralement pendant le basculement entre le HeadNode principal et secondaire pour le cluster | Attendez deux minutes, puis recommencez l’opération |

##<a name="bad-request-content-could-not-find-job"></a>Demande incorrecte du contenu : Impossible de trouver travail

**Code d’état HTTP**: 400

| Cause | Résolution |
| ---------------- | ------------------- |
| Détails de la tâche ont été supprimés par l’historique des travaux nettoyage | La période de rétention par défaut pour l’historique des travaux est 7 jours. Cela peut être modifié en modifiant `mapreduce.jobhistory.max-age-ms`. Pour plus d’informations, consultez [configuration de modification](#modifying-configuration) |
| Tâche a été supprimé en raison d’un basculement | Réessayer de soumission de la tâche pour atteindre deux minutes |
| Un id de travail non valide a été utilisé | Vérifier si l’id de travail est correct |

##<a name="bad-gateway"></a>Passerelle incorrecte

**Code d’état HTTP**: 502

| Cause | Résolution |
| ---------------- | ------------------- |
| Nettoyage interne se produit au sein du processus WebHCat | Attendez que nettoyage de terminer ou redémarrez le service WebHCat |
| Délai d’attente sur une réponse à partir du service ResourceManager. Cela peut se produire lorsque le nombre d’applications actifs est la valeur maximale configurée (par défaut 10 000) | Attendre en cours d’exécution des tâches pour exécuter ou augmenter la limite de tâches simultanées en modifiant `yarn.scheduler.capacity.maximum-applications`. Pour plus d’informations, consultez [configuration de modification](#modifying-configuration)  |
| Lorsque vous tentez d’extraire tous les travaux via l’appel [obtenir /jobs](https://cwiki.apache.org/confluence/display/Hive/WebHCat+Reference+Jobs) lors de la `Fields` est défini sur`*` | Ne pas extraire *tous les* détails d’une tâche, utilisez plutôt `jobid` pour récupérer les informations pour les travaux supérieure uniquement à certain id de la tâche. Ou, n’utilisez pas`Fields` |
| Le service WebHCat est arrêté pendant le basculement HeadNode | Patientez pendant deux minutes, puis recommencez l’opération |
| Il existe plus de 500 travaux en attente soumises via WebHCat | Attendez que travaux actuellement en attente aient terminé avant l’envoi de plusieurs tâches |

[maximum-applications]: http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.1.3/bk_system-admin-guide/content/setting_application_limits.html
[max-procs]: https://hive.apache.org/javadocs/hcat-r0.5.0/configuration.html
[max-age-ms]: http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.0.6.0/ds_Hadoop/hadoop-mapreduce-client/hadoop-mapreduce-client-core/mapred-default.xml
 

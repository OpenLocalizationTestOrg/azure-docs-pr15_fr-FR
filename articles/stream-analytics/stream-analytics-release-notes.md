<properties 
    pageTitle="Notes de publication de flux Analytique | Microsoft Azure" 
    description="Notes de mise à jour de flux Analytique" 
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
    ms.workload="data-services" 
    ms.date="09/26/2016" 
    ms.author="jeffstok"/>

#<a name="stream-analytics-release-notes"></a>Notes de publication de flux Analytique

## <a name="notes-for-04152016-release-of-stream-analytics"></a>Notes de version 15/04/2016 de flux Analytique ##

Cette version contient la mise à jour suivante.

Titre | Description
---|---
Disponibilité générale pour des sorties de Power BI  | [Power BI sorties](stream-analytics-power-bi-dashboard.md) sont désormais généralement disponibles. La date d’expiration de l’autorisation de 90 jours pour Power BI a été supprimée. Pour plus d’informations sur des scénarios où l’autorisation doit être renouvelé voir la section [renouveler l’autorisation](stream-analytics-power-bi-dashboard.md#Renew-authorization) de création d’un tableau de bord Power BI.

## <a name="notes-for-03032016-release-of-stream-analytics"></a>Notes pour 03/03/2016 mise à jour de flux Analytique ##

Cette version contient la mise à jour suivante.

Titre | Description
---|---
Nouveaux éléments de langage de requête Analytique de flux de données  | SAQL comporte maintenant [GetType](https://msdn.microsoft.com/library/azure/mt643890.aspx "GetType MSDN Page"), [TRY_CAST](https://msdn.microsoft.com/library/azure/mt643735.aspx "TRY_CAST MSDN Page") et [REGEXMATCH](https://msdn.microsoft.com/library/azure/mt643891.aspx "REGEXMATCH MSDN Page").

## <a name="notes-for-12102015-release-of-stream-analytics"></a>Notes pour 10/12/2015 mise à jour de flux Analytique ##

Cette version contient la mise à jour suivante.

Titre | Description
---|---
Mise à jour de la version API REST | La version de l’API REST a été mis à jour à 2015-10-01. Vous trouverez plus d’informations sur MSDN à la [Référence de l’API REST gestion Analytique flux](https://msdn.microsoft.com/library/azure/dn835031.aspx) et [apprentissage automatique intégration aux flux Analytique](stream-analytics-how-to-configure-azure-machine-learning-endpoints-in-stream-analytics.md).
Intégration d’apprentissage Machine Azure | Dans cette version est prise en charge pour Azure d’apprentissage automatique fonctions définies par l’utilisateur. Consultez le [didacticiel](stream-analytics-machine-learning-integration-tutorial.md) pour plus d’informations, ainsi que l' [annonce blog général](http://blogs.technet.com/b/machinelearning/archive/2015/12/10/apply-azure-ml-as-a-function-in-azure-stream-analytics.aspx).

## <a name="notes-for-11122015-release-of-stream-analytics"></a>Notes de version 11/12/2015 de flux Analytique ##

Cette version contient la mise à jour suivante.

Titre | Description
---|---
Nouveau comportement de l’instruction SELECT | SELECT dans le flux de données Analytique a été améliorée pour permettre * comme un accesseur de propriété d’un enregistrement imbriqué. Pour plus d’informations, consultez [http://msdn.microsoft.com/library/mt622759.aspx](http://msdn.microsoft.com/library/mt622759.aspx "Types de données complexes").

## <a name="notes-for-10222015-release-of-stream-analytics"></a>Notes pour mise à jour de flux Analytique 22/10/2015 ##

Cette version contient les mises à jour suivants.

Titre | Description
---|---
Fonctionnalités de langage de requête supplémentaires | Flux Analytique a développé le langage de requête en incluant les fonctionnalités suivantes : [ABS](https://msdn.microsoft.com/library/azure/mt574054.aspx), [plafond](https://msdn.microsoft.com/library/azure/mt605286.aspx), [EXP](https://msdn.microsoft.com/library/azure/mt605289.aspx), [FLOOR](https://msdn.microsoft.com/library/azure/mt605240.aspx), [POWER](https://msdn.microsoft.com/library/azure/mt605287.aspx), [signe](https://msdn.microsoft.com/library/azure/mt605290.aspx), [carré](https://msdn.microsoft.com/library/azure/mt605288.aspx)et [racine](https://msdn.microsoft.com/library/azure/mt605238.aspx).
Limitations agrégation supprimées  | Cette version supprime la limitation de 15 agrégats dans une requête. Il n’existe désormais aucune limite au nombre d’agrégats par la requête.
Fonction de groupe par System.Timestamp ajoutée | La fonction [GROUP BY](https://msdn.microsoft.com/library/azure/dn835023.aspx) permet maintenant window_type ou [System.Timestamp](https://msdn.microsoft.com/library/azure/mt598501.aspx).
DÉCALAGE ajouté de distribution automatique et saut de windows | Par défaut, windows [distribution automatique](https://msdn.microsoft.com/library/azure/dn835055.aspx) et [saut](https://msdn.microsoft.com/library/azure/dn835041.aspx) sont alignés par rapport à zéro heure (1/1/0001 12:00:00 AM UTC). Le nouveau paramètre (facultatif) 'offsetsize' permet de spécifier un décalage personnalisée (ou alignement).


## <a name="notes-for-09292015-release-of-stream-analytics"></a>Notes pour mise à jour de flux Analytique 29/09/2015 ##

Cette version contient les mises à jour suivants.

Titre | Description
---|---
Présentation publique IoT Azure Suite | Flux Analytique est inclus dans l’aperçu de la Suite IoT Azure Public.
Intégration de portail Azure | En plus de présence continue dans le portail de gestion Azure, flux Analytique est désormais intégré dans le [Portail Azure](https://azure.microsoft.com/overview/preview-portal/). Remarque qu’Analytique flux fonctionnalité dans le portail Preview est actuellement un sous-ensemble des fonctionnalités proposées dans le portail de gestion Azure, sans prise en charge pour le test de la requête dans le navigateur, Power BI de sortie configuration et navigation à ou en créant une nouvelle entrée et sortie ressources dans les abonnements que vous avez accès à.
Prise en charge de sortie DocumentDB | Tâches d’Analytique flux peuvent à présent sortir à [DocumentDB](https://azure.microsoft.com/services/documentdb/).
Prise en charge pour l’entrée IoT concentrateur | Travaux Analytique de flux de données peut maintenant traiter des données à partir de IoT Hubs.
HORODATAGE par pour les événements hétérogènes | Lorsqu’un flux de données contient plusieurs types d’événements ayant horodatages dans plusieurs champs, vous pouvez désormais utiliser [Horodatage par](http://msdn.microsoft.com/library/mt573293.aspx) avec les expressions pour spécifier les champs d’horodatage différentes pour chaque cas.

## <a name="notes-for-09102015-release-of-stream-analytics"></a>Notes pour 10/09/2015 mise à jour de flux Analytique ##

Cette version contient les mises à jour suivants.

Titre|Description
---|---
Prise en charge pour les groupes PowerBI|Pour activer le partage de données avec d’autres utilisateurs de Power BI, tâches de flux de données Analytique peuvent désormais écrire sur [PowerBI groupes](stream-analytics-define-outputs.md#power-bi) à l’intérieur de votre compte de Power BI.

## <a name="notes-for-08202015-release-of-stream-analytics"></a>Notes pour 20/08/2015 mise à jour de flux Analytique ##

Cette version contient les mises à jour suivants.

Titre|Description
---|---
Fonction dernière ajoutée |La fonction [dernière](http://msdn.microsoft.com/library/mt421186.aspx) est désormais disponible dans les tâches de flux Analytique, ce qui vous permet de récupérer l’événement le plus récent dans un flux d’événements au cours d’une période donnée.
Nouvelles fonctions de tableau|Fonctions de tableau [GetArrayElement](http://msdn.microsoft.com/library/mt270218.aspx), [GetArrayElements](http://msdn.microsoft.com/library/mt298451.aspx) et [GetArrayLength](http://msdn.microsoft.com/library/mt270226.aspx) sont désormais disponibles.
Nouvelles fonctions d’enregistrement|Fonctions d’enregistrement [GetRecordProperties](http://msdn.microsoft.com/library/mt270221.aspx) et [GetRecordPropertyValue](http://msdn.microsoft.com/library/mt270220.aspx) sont désormais disponibles.

## <a name="notes-for-07302015-release-of-stream-analytics"></a>Notes pour mise à jour de flux Analytique 30/07/2015 ##

Cette version contient les mises à jour suivants.

Titre|Description
---|---
Id d’organisation Power BI découplé à partir d’Azure Id|Cette fonctionnalité permet de [sortie de Power BI](stream-analytics-power-bi-dashboard.md) pour les travaux ASA sous n’importe quel type de compte Azure (identifiant Live Id ou Id d’organisation). En outre, vous pouvez avoir un Id d’organisation pour votre compte Azure et utiliser un nom différent pour autoriser la sortie de Power BI.
Prise en charge des files d’attente de Service Bus sortie|[Files d’attente de service Bus](stream-analytics-define-outputs.md#service-bus-queues) sorties est désormais disponibles dans les tâches de flux de données Analytique.
Prise en charge de sortie de Service Bus rubriques|[Service Bus rubriques](stream-analytics-define-outputs.md#service-bus-topics) sorties sont désormais disponibles dans les tâches de flux de données Analytique.

## <a name="notes-for-07092015-release-of-stream-analytics"></a>Notes d’Analytique de flux de virtualisation 07/09/2015 ##

Cette version contient les mises à jour suivants.


Titre|Description
---|---
Personnalisé Blob partition de sortie|Des sorties de stockage BLOB autorisent maintenant une option pour spécifier la fréquence cette sortie des objets BLOB sont écrits et la structure et le format de l’arborescence de sortie données chemin d’accès. 

## <a name="notes-for-05032015-release-of-stream-analytics"></a>Notes pour mise à jour de flux Analytique 05/03/2015 ##

Cette version contient les mises à jour suivants.


Titre|Description
---|---
Valeur maximale accrue ordre des commandes tolérance de fenêtre|La taille maximale de la fenêtre de tolérance hors de la commande est maintenant 59:59 (ss)
Format de sortie JSON : Ligne séparée ou une matrice.|Il existe désormais une option lors de la sortie à stockage Blob ou un événement concentrateur sous forme d’un tableau d’objets JSON ou en séparant les objets JSON avec une nouvelle ligne. 

## <a name="notes-for-04162015-release-of-stream-analytics"></a>Notes pour mise à jour de flux Analytique 16/04/2015 ##


Titre|Description
---|---
Retarder dans la configuration de compte de stockage Azure|Lorsque vous créez une tâche de flux de données Analytique dans une zone pour la première fois, vous devrez créer un nouveau compte de stockage ou spécifiez un compte existant pour le suivi des tâches de flux de données Analytique dans cette zone. En raison de la latence de la configuration de surveillance, création d’une autre tâche de flux de données Analytique dans la même région dans les 30 minutes vous invite à la spécification d’un deuxième compte de stockage au lieu de montrant l’option récemment configuré dans le menu déroulant compte de stockage de surveillance. Pour éviter de créer un compte de stockage inutile, attendez 30 minutes après la création d’une tâche dans une zone pour la première fois avant la mise en service des travaux supplémentaires dans cette zone.
Mise à niveau de la tâche|Pour l’instant, flux Analytique ne reconnaît pas les modifications live jusqu'à la définition ou la configuration d’une tâche en cours d’exécution. Pour modifier l’entrée, sortie, requête, échelle ou la configuration d’une tâche en cours d’exécution, vous devez tout d’abord arrêter la tâche.
Types de données déduits à partir de la source d’entrée|Si une instruction CREATE TABLE n’est pas utilisée, le type d’entrée est dérivé de format d’entrée, par exemple tous les champs de CSV sont de type string. Les champs doivent être converties de manière explicite pour le type correct à l’aide de la fonction CAST afin d’éviter les erreurs d’incompatibilité de type.
Champs manquants sont envoyées sous forme de valeurs null|Référence à un champ qui ne figure pas dans la source d’entrée génèrera de valeurs null dans l’événement de sortie.
AVEC les instructions doivent précéder les instructions SELECT|Dans votre requête, les instructions SELECT doivent suivre sous-requêtes définis à l’aide d’instructions.
Problème de mémoire insuffisante|Redémarrez de diffusion en continu travaux Analytique avec une grande tolérance pour les événements de sortie de commande et/ou des requêtes complexes maintenir qu'une grande quantité d’état peut entraîner la tâche à manquer de mémoire, ce qui entraîne une tâche. Les opérations de début et d’arrêt sont visibles dans les journaux d’opération de la tâche. Pour éviter ce problème, la requête à l’échelle sur plusieurs partitions. Dans une version ultérieure, cette limitation sera résolue par la perte de performances sur les tâches concernés au lieu de redémarrer.
Entrées blob large sans horodatage charge utile peuvent entraîner le problème de mémoire insuffisante|Consomme des fichiers volumineux depuis le stockage Blob peut provoquer travaux flux Analytique bloquer si un champ d’horodatage n’est pas spécifié par le biais d’horodatage par. Pour éviter ce problème, conservez chaque blob inférieure à 10 Mo en taille.
Limitation de volume événement de base de données SQL|Lorsque vous utilisez la base de données SQL en tant que sortie cible, très haute volumes de données de sortie peuvent entraîner la tâche de flux Analytique délai d’expiration. Pour résoudre ce problème, réduisez le volume de sortie à l’aide des agrégats ou des opérateurs de filtre, ou sélectionnez stockage d’objets Blob Azure ou Hubs événement en tant que sortie cible.
Jeux de données PowerBI peut contenir une table|PowerBI ne prend pas en charge plusieurs tables dans un jeu de données donné.

## <a name="get-help"></a>Obtenir de l’aide
Pour obtenir une aide supplémentaire, essayez de notre [forum Azure flux Analytique](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>Étapes suivantes

- [Présentation des flux de données Azure Analytique](stream-analytics-introduction.md)
- [Commencer à utiliser Azure flux Analytique](stream-analytics-get-started.md)
- [Échelle Azure flux Analytique travaux](stream-analytics-scale-jobs.md)
- [Référence du langage flux Azure Analytique requête](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Référence de l’API REST gestion Analytique flux Azure](https://msdn.microsoft.com/library/azure/dn835031.aspx)
 

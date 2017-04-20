<properties
    pageTitle="Base de données sur le serveur n’est pas disponible actuellement, se connecter à la base de données SQL | Microsoft Azure"
    description="Résoudre les problèmes de la base de données sur serveur n’est pas disponible actuellement erreur lorsqu’une application se connecte à la base de données SQL."
    services="sql-database"
    documentationCenter=""
    authors="dalechen"
    manager="felixwu"
    editor=""
    keywords="base de données sur le serveur n’est pas disponible actuellement, se connecter à la base de données sql"/>

<tags
    ms.service="sql-database"
    ms.workload="data-management"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/21/2016"
    ms.author="daleche"/>

# <a name="error-database-on-server-is-not-currently-available-when-connecting-to-sql-database"></a>Erreur « Base de données sur le serveur n’est pas disponible pour le moment » lors de la connexion à la base de données sql
[AZURE.INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

Lorsqu’une application se connecte à une base de données SQL Azure, le message d’erreur suivant s’affiche :

```
Error code 40613: "Database <x> on server <y> is not currently available. Please retry the connection later. If the problem persists, contact customer support, and provide them the session tracing ID of <z>"
```

> [AZURE.NOTE] Ce message d’erreur est généralement transitoire (courte durée de vie).

Cette erreur se produit lors de la base de données Azure déplacé (ou reconfigurés) et votre application perd sa connexion à la base de données SQL. Événements de reconfiguration de base de données SQL se produit en raison d’un événement planifié (par exemple, une mise à niveau logicielle) ou un événement planifié (par exemple, un blocage du processus ou l’équilibrage de charge). La plupart des événements reconfiguration sont généralement éphémères et doivent être effectuées en moins de 60 secondes au maximum. Toutefois, ces événements peuvent parfois prendre plus de temps, par exemple lorsqu’une longue transaction provoque une récupération longue.

## <a name="steps-to-resolve-transient-connectivity-issues"></a>Étapes à suivre pour résoudre les problèmes de connectivité transitoires
1.  Vérifier le [Tableau de bord de Service Microsoft Azure](https://azure.microsoft.com/status) pour n’importe quel défaillances connus qui se sont produites pendant la période pendant laquelle les erreurs ont été signalés par l’application.
2. Applications qui se connectent à un service cloud, telles que la base de données SQL Azure doivent attendre événements reconfiguration périodiques et mise en œuvre réessayer logique pour gérer ces erreurs au lieu de ces erreurs d’application exposition aux utilisateurs. Passez en revue la section [erreurs transitoires](sql-database-connectivity-issues.md) et les meilleures pratiques et instructions en [Vue d’ensemble de développement de base de données SQL](sql-database-develop-overview.md) pour plus d’informations générales et réessayer de stratégies de conception. Consultez des exemples de code en [Bibliothèques de connexions de base de données SQL et SQL Server](sql-database-libraries.md) pour les caractéristiques de l’objet.
3.  Une base de données approche de ses limites de ressources, il peut sembler un problème de connectivité transitoires. Voir [résolution des problèmes de performances](sql-database-troubleshoot-performance.md).
4.  Si des problèmes de connectivité continuent, ou si la durée pour laquelle votre application rencontre l’erreur est supérieur à 60 secondes ou si vous voyez plusieurs occurrences de l’erreur dans un jour donné, créer une demande de support Azure en sélectionnant la **Prise en charge** sur le site [Azure prend en charge](https://azure.microsoft.com/support/options) .

## <a name="next-steps"></a>Étapes suivantes
- Si vous recevez une erreur différente, évaluer le [message d’erreur](sql-database-develop-error-messages.md) pour obtenir des indices sur la cause.
- Si le problème est permanent, consultez les instructions de [résoudre des problèmes de connexion courants à base de données SQL Azure](sql-database-troubleshoot-common-connection-issues.md).

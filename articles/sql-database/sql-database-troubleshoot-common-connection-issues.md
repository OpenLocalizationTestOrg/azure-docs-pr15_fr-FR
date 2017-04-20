<properties
    pageTitle="Résoudre les problèmes de connexion courants à base de données SQL Azure"
    description="Étapes pour identifier et résoudre les erreurs de connexion de base de données SQL Azure."
    services="sql-database"
    documentationCenter=""
    authors="dalechen"
    manager="felixwu"
    editor=""/>

<tags
    ms.service="sql-database"
    ms.workload="data-management"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/31/2016"
    ms.author="daleche"/>

# <a name="troubleshoot-connection-issues-to-azure-sql-database"></a>Résoudre les problèmes de connexion à la base de données SQL Azure

Lorsque la connexion à la base de données SQL Azure échoue, vous recevez des [messages d’erreur](sql-database-develop-error-messages.md). Cet article est un sujet centralisé qui vous permet de résoudre les problèmes de connectivité de base de données SQL Azure. Il présente [courantes provoque](#cause) des problèmes de connexion, recommande [un outil de dépannage](#try-the-troubleshooter-for-azure-sql-database-connectivity-issues) qui vous permet d’identité le problème et fournit les étapes de dépannage pour résoudre les [erreurs transitoires](#troubleshoot-transient-errors) et [permanente ou non transitoires](#troubleshoot-the-persistent-errors). Enfin, il répertorie [tous les articles pertinents pour les problèmes de connectivité de base de données SQL Azure](#all-topics-for-azure-sql-database-connection-problems).

Si vous rencontrez les problèmes de connexion, essayez les étapes de résolution des problèmes qui sont décrites dans cet article.
[AZURE.INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="cause"></a>Cause

Les problèmes de connexion peuvent être due à une des opérations suivantes :

- Échec d’appliquer les meilleures pratiques et les règles de conception au cours du processus de conception d’application.  Voir [Vue d’ensemble de développement de base de données SQL](sql-database-develop-overview.md) pour commencer.
- Reconfiguration de base de données SQL Azure
- Paramètres du pare-feu
- Délai de connexion
- Informations de connexion incorrect
- Limite maximale atteinte sur certaines ressources de base de données SQL Azure

En règle générale, les problèmes de connexion à la base de données SQL Azure peuvent être classés comme suit :

- [Erreurs transitoires (courtes ou intermittents)](#troubleshoot-transient-errors)
- [Permanentes ou non transitoires erreurs (erreurs régulièrement périodique)](#troubleshoot-the-persistent-errors)

## <a name="try-the-troubleshooter-for-azure-sql-database-connectivity-issues"></a>Essayez l’outil de dépannage pour les problèmes de connectivité de base de données SQL Azure

Si vous rencontrez une erreur de connexion spécifiques, essayez de [cet outil](https://support.microsoft.com/help/10085/troubleshooting-connectivity-issues-with-microsoft-azure-sql-database), qui vous permettra de rapidement identité et résoudre votre problème.

## <a name="troubleshoot-transient-errors"></a>Résoudre les erreurs transitoires
Si votre application connaît des erreurs transitoires, consultez les rubriques suivantes pour obtenir des conseils sur la façon de réduire la fréquence de ces erreurs résoudre les problèmes :

- [Résolution des problèmes de base de données &lt;x&gt; sur serveur &lt;y&gt; n’est pas disponible (erreur : 40613)](sql-database-troubleshoot-connection.md)
- [Résoudre les problèmes, diagnostiquer et d’éviter les erreurs de connexion SQL et erreurs transitoires pour la base de données SQL](sql-database-connectivity-issues.md)

<a id="troubleshoot-the-persistent-errors" name="troubleshoot-the-persistent-errors"></a>

## <a name="troubleshoot-persistent-errors-non-transient-errors"></a>Résoudre les erreurs permanentes (non transitoires erreurs)

Si l’application de façon permanente ne parvient pas à se connecter à la base de données SQL Azure, elle indique généralement un problème avec une des opérations suivantes :

- Configuration du pare-feu. Le pare-feu de base de données ou côté client SQL Azure bloque les connexions à la base de données SQL Azure.
- Réseau reconfiguration côté client : par exemple, une nouvelle adresse IP ou un serveur proxy.
- Erreur de l’utilisateur : par exemple, incorrectement entrée des paramètres de connexion, telles que le nom du serveur dans la chaîne de connexion.

### <a name="steps-to-resolve-persistent-connectivity-issues"></a>Étapes à suivre pour résoudre les problèmes de connectivité permanente

1.  Configurer [les règles de pare-feu](sql-database-configure-firewall-settings.md) pour autoriser le client adresse IP.
2.  Dans tous les pare-feu entre le client et Internet, assurez-vous que le port 1433 est ouvert pour les connexions sortantes. Reportez-vous à [configurer le pare-feu Windows pour autoriser l’accès SQL Server](https://msdn.microsoft.com/library/cc646023.aspx) pour des pointeurs supplémentaires.
3.  Vérifiez votre chaîne de connexion et d’autres paramètres de connexion. Consultez la section de chaîne de connexion dans la [rubrique de problèmes de connectivité](sql-database-connectivity-issues.md#connections-to-azure-sql-database).
4.  Vérifier l’intégrité du service dans le tableau de bord. Si vous pensez que coupure régionaux, voir [récupérer en cas de panne](sql-database-disaster-recovery.md) pour obtenir la procédure pour récupérer vers une nouvelle zone.

## <a name="all-topics-for-azure-sql-database-connection-problems"></a>Toutes les rubriques pour des problèmes de connexion de base de données SQL Azure

Le tableau suivant répertorie chaque rubrique de problème de connexion qui s’applique directement au service de base de données SQL Azure.


| &nbsp; | Titre | Description |
| --: | :-- | :-- |
| 1 | [Résoudre les problèmes de connexion à la base de données SQL Azure](sql-database-troubleshoot-common-connection-issues.md) | Il s’agit de la page d’accueil pour la résolution des problèmes de connectivité dans la base de données SQL Azure. Il explique comment identifier et résoudre les erreurs transitoires et les erreurs permanentes ou non transitoires dans la base de données SQL Azure. |
| 2 | [Résoudre les problèmes, diagnostiquer et d’éviter les erreurs de connexion SQL et erreurs transitoires pour la base de données SQL](sql-database-connectivity-issues.md) | Découvrez comment résoudre les problèmes, diagnostiquer et d’éviter une erreur de connexion SQL ou transitoires dans la base de données SQL Azure. |
| 3 | [Conseils de gestion des erreurs transitoires généraux](best-practices-retry-general.md) | Fournit des instructions générales pour la gestion de pannes transitoires lors de la connexion à la base de données SQL Azure. |
| 4 | [Résoudre les problèmes de connectivité avec la base de données SQL Microsoft Azure](https://support.microsoft.com/help/10085/troubleshooting-connectivity-issues-with-microsoft-azure-sql-database) | Cet outil permet d’identité votre problème de résoudre les erreurs de connexion. |
| 5 | [Résoudre les problèmes « base de données &lt;x&gt; sur serveur &lt;y&gt; n’est pas disponible actuellement. Erreur réessayez plus tard la connexion »](sql-database-troubleshoot-connection.md) | Décrit comment identifier et résoudre une erreur 40613 : « base de données &lt;x&gt; sur serveur &lt;y&gt; n’est pas disponible actuellement. Réessayez plus tard la connexion. » |
| 6 | [Codes d’erreur SQL pour les applications clientes de base de données SQL : erreur de connexion et d’autres problèmes de base de données](sql-database-develop-error-messages.md) | Fournit des informations sur les codes d’erreur SQL pour les applications clientes de base de données SQL, tels que les erreurs de connexion de base de données, des problèmes de copie de base de données et des erreurs générales. |
| 7 | [Conseils pour les performances de base de données SQL Azure des bases de données unique](sql-database-performance-guidance.md) | Fournit des conseils pour vous aider à déterminer le niveau de service est adapté à votre application. Fournit également des recommandations pour optimiser votre application pour tirer le meilleur parti de votre base de données SQL Azure. |
| 8 | [Vue d’ensemble de développement de base de données SQL](sql-database-develop-overview.md) | Fournit des liens vers des exemples de code pour différentes technologies que vous pouvez utiliser pour se connecter à et d’interagir avec la base de données SQL Azure. |
| 9 | Mettre à niveau vers la page de v12 de base de données SQL Azure ([portail Azure](sql-database-upgrade-server-portal.md), [PowerShell](sql-database-upgrade-server-powershell.md)) | Fournit des instructions relatives à la mise à niveau des bases de données et des serveurs 11 de base de données SQL Azure existant V12 de base de données SQL Azure à l’aide de portail Azure ou PowerShell. |


## <a name="next-steps"></a>Étapes suivantes

- [Résoudre les problèmes de performances de base de données SQL Azure](sql-database-troubleshoot-performance.md)
- [Résoudre les problèmes liés aux autorisations de base de données SQL Azure](sql-database-troubleshoot-permissions.md)
- [Voir toutes les rubriques pour le service de base de données SQL Azure](sql-database-index-all-articles.md)
- [Effectuer une recherche dans la documentation sur Microsoft Azure](http://azure.microsoft.com/search/documentation/)
- [Afficher les dernières mises à jour du service de base de données SQL Azure](http://azure.microsoft.com/updates/?service=sql-database)


## <a name="additional-resources"></a>Ressources supplémentaires

- [Vue d’ensemble de développement de base de données SQL](sql-database-develop-overview.md)
- [Conseils de gestion des erreurs transitoires généraux](../best-practices-retry-general.md)
- [Bibliothèques de connexions de base de données SQL et SQL Server](sql-database-libraries.md)
- [Rubriques d’apprentissage pour l’utilisation de base de données SQL Azure](https://azure.microsoft.com/documentation/learning-paths/sql-database-training-learn-sql-database)
- [Rubriques d’apprentissage pour utiliser les outils et les fonctionnalités de base de données élastique](https://azure.microsoft.com/documentation/learning-paths/sql-database-elastic-scale) 

<properties
   pageTitle="Service Centre de sécurité Azure et base de données SQL Azure | Microsoft Azure"
   description="Cet article vous explique comment le centre de sécurité peuvent vous aider à protéger vos bases de données dans la base de données SQL Azure."
   services="sql-database"
   documentationCenter="na"
   authors="TerryLanfear"
   manager="MBaldwin"
   editor=""/>

<tags
   ms.service="security-center"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/18/2016"
   ms.author="terrylan"/>

# <a name="azure-security-center-and-azure-sql-database-service"></a>Service Centre de sécurité Azure et base de données SQL Azure

[Centre de sécurité Azure](https://azure.microsoft.com/documentation/services/security-center/) vous permet d’empêcher, détecter et répondre aux menaces. Il assure la gestion de surveillance et la stratégie de sécurité intégrée sur vos abonnements Azure, permettant de détecter les menaces qui pourraient passer inaperçus et fonctionne avec une large réseau de solutions de sécurité.

Cet article vous explique comment le centre de sécurité peuvent vous aider à protéger vos bases de données dans la base de données SQL Azure.

## <a name="why-use-security-center"></a>Pourquoi utiliser le centre de sécurité ?

Centre de sécurité vous aident à protéger des données dans une base de données SQL en fournissant visibilité sur la sécurité de tous vos serveurs et les bases de données. Avec le centre de sécurité, vous pouvez :

- Définir les stratégies d’audit et de chiffrement de base de données SQL.
- Contrôler la sécurité des ressources de base de données SQL sur tous vos abonnements.
- Identifier et résoudre les problèmes de sécurité rapidement.
- Intégrer des alertes de [détection des menaces de base de données SQL Azure](../sql-database/sql-database-threat-detection-get-started.md).

En plus de protéger vos ressources de base de données SQL, centre de sécurité fournit également le contrôle de la sécurité et gestion des machines virtuelles Azure, les Services en nuage, Services d’application, réseaux virtuels et plus. En savoir plus sur le centre de sécurité [ici](security-center-intro.md).

## <a name="prerequisites"></a>Conditions préalables

Pour commencer à utiliser le centre de sécurité, vous devez posséder un abonnement à Microsoft Azure. La couche libre du centre de sécurité est compatible avec votre abonnement. Pour plus d’informations sur Free du centre de sécurité et les niveaux Standard, consultez [Tarification de centre de sécurité](https://azure.microsoft.com/pricing/details/security-center/).

Centre de sécurité prend en charge l’accès par rôle. Pour en savoir plus sur le contrôle d’accès basé sur un rôle (RBAC) dans Azure, consultez [Contrôle d’accès basé sur Azure Active Directory rôle](../active-directory/role-based-access-control-configure.md). Forum aux questions du centre de sécurité fournit des informations sur [la gestion des autorisations dans le centre de sécurité](security-center-faq.md#how-are-permissions-handled-in-azure-security-center).

## <a name="access-security-center"></a>Centre de sécurité d’Access

Vous accédez centre de sécurité à partir du [portail Azure](https://azure.microsoft.com/features/azure-portal/). [Connectez-vous au portail](https://portal.azure.com/) et sélectionnez l' **option Centre de sécurité**.

![Option Centre de sécurité][1]

La carte du **Centre de sécurité** s’ouvre.
![Carte du centre de sécurité][2]

## <a name="set-security-policy"></a>Définir la stratégie de sécurité

Une stratégie de sécurité définit le jeu de contrôles qui sont recommandés pour les ressources au sein de l’abonnement spécifié ou le groupe de ressources. Dans le centre de sécurité, vous définissez des stratégies pour vos abonnements ou des groupes de ressources en fonction des besoins de sécurité de votre société et le type d’applications ou critère de diffusion des données dans chaque abonnement.

Vous pouvez définir une stratégie pour afficher les recommandations pour l’audit SQL et chiffrement transparent des données SQL (TDE).

- Lorsque vous activez **l’audit SQL et détection des menaces**, centre de sécurité recommande que l’audit d’accès à la base de données Azure être activé pour la conformité, avancées de détection et à des fins de l’enquête.
- Lorsque vous activez le **chiffrement transparent des données SQL**, centre de sécurité recommande que le chiffrement inactives être activée pour votre base de données SQL Azure, les sauvegardes associés et les fichiers journaux des transactions.

Pour définir une stratégie de sécurité, sélectionnez la vignette de **stratégie** dans la carte du centre de sécurité. Sur la carte de **stratégie de sécurité** , sélectionnez l’abonnement sur lequel vous voulez activer la stratégie de sécurité. Sélectionnez la **stratégie de prévention** , **activez les recommandations de sécurité que vous souhaitez utiliser pour cet abonnement** .
![Stratégie de sécurité][3]

Pour plus d’informations, voir [définir les stratégies de sécurité](security-center-policies.md).

## <a name="manage-security-recommendation"></a>Gérer les recommandations de sécurité

Centre de sécurité analyse régulièrement l’état de sécurité de vos ressources Azure. Lorsque le centre de sécurité identifie les problèmes de sécurité potentielles, qu’il crée des recommandations. Les recommandations vous guident dans le processus de configuration des contrôles nécessaires.

Après avoir défini une stratégie de sécurité, centre de sécurité analyse l’état de sécurité de vos ressources pour identifier les faiblesses potentielles. Les recommandations sont affichées dans un format de tableau où chaque ligne représente une seule recommandation particulière. Utilisez le tableau suivant comme référence pour vous aider à comprendre les recommandations disponibles pour la base de données SQL Azure, et que signifie chaque recommandation si vous appliquez. Sélection d’une recommandation vous permet d’accéder à un article qui explique comment mettre en œuvre la recommandation dans le centre de sécurité.

| Recommandation | Description |
| ----- | ----- |
| [Activer la détection d’audit et menaces sur les serveurs SQL](security-center-enable-auditing-on-sql-servers.md) | Vous recommande d’activer l’audit et détection pour les serveurs de base de données SQL. (Service de base de données SQL uniquement. N’inclut pas Microsoft SQL Server est en cours d’exécution sur vos ordinateurs virtuels.) |
| [Activer l’audit et menace la détection sur bases de données SQL](security-center-enable-auditing-on-sql-databases.md) | Vous recommande d’activer l’audit et détection des bases de données de base de données SQL. (Service de base de données SQL uniquement. N’inclut pas Microsoft SQL Server est en cours d’exécution sur vos ordinateurs virtuels.) |
| [Activer le chiffrement de données Transparent](security-center-enable-transparent-data-encryption.md) | Recommande l’activation de chiffrement pour les bases de données SQL. (Service de base de données SQL uniquement). |

Pour afficher les recommandations relatives à vos ressources Azure, sélectionnez la vignette de **recommandations** sur la carte du centre de sécurité. Dans la carte de **recommandations** , sélectionnez une recommandation pour consulter les détails. Dans cet exemple, nous allons sélectionnez **Activer l’audit et détection des menaces sur les serveurs SQL**.

![Recommandations][4]

Comme indiqué ci-dessous, centre de sécurité vous indique les serveurs SQL Server dans laquelle l’audit et détection ne sont pas activés. Une fois que vous activez l’audit, vous pouvez configurer les paramètres de détection des menaces et des paramètres de messagerie pour recevoir des alertes de sécurité. Détection des menaces vous avertit quand il détecte des activités de base de données anormale indiquant les menaces de sécurité potentielles à la base de données. Les alertes sont affichent dans le tableau de bord centre de sécurité.
![Détection audit des menaces][5]

Suivez les étapes de [prise en main de détection de base de données SQL](../sql-database/sql-database-threat-detection-get-started.md) pour activer et configurer la détection de menace et configurer la liste des messages électroniques qui recevra les alertes de sécurité lors de la détection des activités anormales.

Pour en savoir plus sur les recommandations, voir [Gestion des sécurité recommandations](security-center-recommendations.md).

## <a name="monitor-security-health"></a>Surveiller l’intégrité du sécurité

Après avoir activé [les stratégies de sécurité](security-center-policies.md) pour les ressources d’un abonnement, le centre de sécurité analysera la sécurité de vos ressources pour identifier les faiblesses potentielles.  Vous pouvez afficher l’état de sécurité de vos ressources dans la vignette **santé sécurité des ressources** . Lorsque vous cliquez sur **données** dans la vignette **au niveau de sécurité de ressource** , la carte de **Ressources de données** s’ouvre avec recommandations SQL pour les questions telles que le chiffrement des données d’audit et transparente non activée. Il est également recommandations pour l’état d’intégrité général de la base de données.
![Santé de sécurité des ressources][6]

Pour plus d’informations, voir [analyse du fonctionnement sécurité](security-center-monitoring.md).

## <a name="manage-and-respond-to-security-alerts"></a>Gérer et répondre aux alertes de sécurité

Centre de sécurité automatiquement collecte, analyse et intègre les données du journal de [Détection des menaces SQL Azure](../sql-database/sql-database-threat-detection-get-started.md), ainsi que d’autres ressources Azure, à détecter des menaces réels et réduire les fausses. Liste des alertes de sécurité par ordre de priorité est présentée dans le centre de sécurité ainsi que les informations que nécessaires pour effectuer des recherches rapides le problème et recommandations pour savoir comment mettre à jour une attaque.

Pour afficher les alertes, sélectionnez la vignette **d’alertes de sécurité** dans la carte du centre de sécurité. Sur la carte **d’alertes de sécurité** , sélectionnez une alerte pour en savoir plus sur les événements qui a déclenché l’alerte et quoi, si un, étapes que vous devez suivre pour convertir une attaque. Dans cet exemple, nous allons sélectionnez **injection SQL potentiels**.
![Alertes de sécurité][7]

Comme indiqué ci-dessous, le centre de sécurité fournit des détails supplémentaires qui proposent des claires sur ce qui a déclenché l’alerte, la ressource cible, le cas échéant l’adresse IP source et des recommandations sur la façon de résoudre.
![Injection SQL potentielle][8]

Pour plus d’informations, voir [gérer et répondre aux alertes de sécurité](security-center-managing-and-responding-alerts.md).

## <a name="next-steps"></a>Étapes suivantes

- [Forum aux questions du centre de sécurité](security-center-faq.md) : Forum aux questions sur l’utilisation du service de recherche.
- [Guide de planification et opérations du centre de sécurité](security-center-planning-and-operations-guide.md) : suivre un ensemble de tâches et les étapes pour optimiser l’utilisation du centre de sécurité en fonction de votre organisation en matière de sécurité et modèle de gestion de cloud.
- [Données du centre de sécurité](security-center-data-security.md) – Découvrez la façon dont le centre de sécurité collecte et traite les données relatives à vos ressources Azure, notamment les informations de configuration, métadonnées, les journaux d’événements, blocage des fichiers de vidage et plus encore.
- [Gestion des incidents de sécurité](security-center-incident.md) - apprendre à utiliser la fonctionnalité alerte de sécurité dans le centre de sécurité pour vous aider à des incidents de sécurité.

<!--Image references-->
[1]: ./media/security-center-sql-database/security-center.png
[2]: ./media/security-center-sql-database/security-center-blade.png
[3]: ./media/security-center-sql-database/security-policy.png
[4]: ./media/security-center-sql-database/recommendation.png
[5]: ./media/security-center-sql-database/turn-on-auditing.png
[6]: ./media/security-center-sql-database/monitor-health.png
[7]: ./media/security-center-sql-database/alert.png
[8]: ./media/security-center-sql-database/sql-injection.png

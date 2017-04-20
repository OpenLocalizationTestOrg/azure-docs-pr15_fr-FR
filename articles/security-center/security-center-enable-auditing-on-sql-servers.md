<properties
   pageTitle="Activer l’audit sur les serveurs SQL dans le centre de sécurité Azure | Microsoft Azure"
   description="Ce document vous montre comment mettre en œuvre la recommandation centre de sécurité Azure **Activer l’audit sur les serveurs SQL**."
   services="security-center"
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
   ms.date="07/29/2016"
   ms.author="terrylan"/>

# <a name="enable-auditing-on-sql-servers-in-azure-security-center"></a>Activer l’audit sur les serveurs SQL Azure centre de sécurité

Centre de sécurité Azure recommande que vous activez l’audit pour toutes les bases de données sur vos serveurs SQL Azure si l’audit n’est pas déjà activée. L’audit peut vous aider à assurer la conformité réglementaire, comprendre l’activité de base de données et découvrez les différences et les anomalies qui peuvent indiquer des problèmes d’entreprise ou suspects violations de sécurité.

Une fois que vous avez activé l’audit, vous pouvez configurer les paramètres de détection des menaces et messages électroniques pour recevoir des alertes de sécurité. Détection des menaces détecte les activités de base de données anormale indiquant menaces de sécurité potentielles à la base de données. Cela vous permet de détecter et de répondre aux menaces potentielles qu’ils se produisent.

Cette recommandation s’applique au service SQL Azure uniquement ; Il n’inclut pas SQL Server s’exécutant sur vos ordinateurs virtuels dans Azure Infrastructure Services (IaaS Azure).

> [AZURE.NOTE] Ce document présente le service à l’aide d’un exemple de déploiement.  Il s’agit pas d’un guide étape par étape.

## <a name="implement-the-recommendation"></a>Mettre en œuvre la recommandation

1. Dans la carte de **recommandations** , sélectionnez **Activer l’audit sur les serveurs SQL**.  Cette action ouvre la carte **Activer l’audit sur les serveurs SQL** .
![Activer l’audit sur les serveurs SQL][1]

2. Sélectionnez un serveur SQL server pour activer l’audit sur. Cette action ouvre la carte de **Paramètres d’audit** .
![Paramètres d’audit][2]
3. Sur la carte de **Paramètres d’audit** , sélectionnez **activé** sous **audit**.
![Activer les paramètres d’audit][3]

4. Suivez les étapes de [prise en main d’audit de base de données SQL](../sql-database/sql-database-auditing-get-started.md) pour configurer le stockage de l’emplacement de stockage des vos journaux d’audit. Compte de stockage de l’abonnement de collecte de données est le compte de stockage par défaut.

5. Suivez les étapes de [prise en main de détection de base de données SQL](../sql-database/sql-database-threat-detection-get-started.md) pour activer et configurer la détection de menace et configurer la liste des messages électroniques qui recevra les alertes de sécurité lors de la détection des activités anormales.

## <a name="see-also"></a>Voir aussi

Cet article vous montrer comment mettre en œuvre la recommandation centre de sécurité « Activer l’audit sur les serveurs SQL ». Pour plus d’informations sur la sécurisation de votre base de données SQL, voir les rubriques suivantes :

- [Protéger votre base de données SQL](../sql-database/sql-database-security.md)

Pour en savoir plus sur le centre de sécurité, voir les rubriques suivantes :

- [Définition de stratégies de sécurité dans le centre de sécurité Azure](security-center-policies.md) --Apprenez à configurer des stratégies de sécurité pour vos abonnements Azure et les groupes de ressources.
- [Gestion des recommandations de sécurité dans le centre de sécurité Azure](security-center-recommendations.md) , découvrez comment les recommandations vous aident à protéger vos ressources Azure.
- [Analyse du fonctionnement de la sécurité dans le centre de sécurité Azure](security-center-monitoring.md) , découvrez comment contrôler l’état de vos ressources Azure.
- [Gérer et répondre aux alertes de sécurité dans le centre de sécurité Azure](security-center-managing-and-responding-alerts.md) --apprendre à gérer et répondre aux alertes de sécurité.
- [Solutions des partenaires surveillance avec le centre de sécurité Azure](security-center-partner-solutions.md) , découvrez comment contrôler l’état d’intégrité de votre solutions des partenaires.
- [Forum aux questions sur Centre de sécurité azure](security-center-faq.md) --rechercher Forum aux questions sur l’utilisation du service.
- [Blog sur la sécurité azure](http://blogs.msdn.com/b/azuresecurity/) --obtenir les dernières informations de sécurité Azure et des informations.

<!--Image references-->
[1]: ./media/security-center-enable-auditing-on-sql-server/enable-auditing-on-sql-servers.png
[2]:./media/security-center-enable-auditing-on-sql-server/enable-auditing.png
[3]: ./media/security-center-enable-auditing-on-sql-server/auditing-settings-blade.png

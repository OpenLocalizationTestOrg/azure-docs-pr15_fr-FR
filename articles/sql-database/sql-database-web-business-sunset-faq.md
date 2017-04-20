<properties
   pageTitle="FAQ coucher de soleil Azure SQL de base de données Web et Business Edition | Microsoft Azure"
   description="Savoir quand les bases de données Web SQL Azure et entreprise validité prend fin et en savoir plus sur les fonctionnalités des nouvelle niveaux de service."
   services="sql-database"
   documentationCenter="na"
   authors="stevestein"
   manager="jhubbard"
   editor="monicar" />
<tags
   ms.service="sql-database"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="data-management"
   ms.date="08/08/2016"
   ms.author="sstein" />

# <a name="web-and-business-edition-sunset-faq"></a>Forum aux questions sur coucher de soleil Web et Business Edition

Les bases de données Web SQL et entreprise Azure sont annulées maintenant. Les niveaux basique, Standard, Premium et élastique remplacent les bases de données Web et entreprise retraite.

Pour vous aider avec la mise à niveau des bases de données Web et entreprise, le service de base de données SQL recommande un niveau et les performances niveau de service approprié (tarification couche) pour chaque base de données en fonction de sa charge historique.

**Pour obtenir des tarifs recommandations niveau :**

- [Mise à niveau vers V12 de base de données SQL à l’aide du portail Azure](sql-database-upgrade-server-portal.md)
- [Mise à niveau vers V12 de base de données SQL à l’aide de PowerShell](sql-database-upgrade-server-powershell.md)
- [Modifier le niveau de tarification d’une base de données Web ou entreprise](sql-database-service-tier-advisor.md)



## <a name="why-does-the-azure-portal-show-my-web-and-business-edition-databases-as-retired"></a>Pourquoi le portail Azure affiche-t-il mes Web et entreprise edition bases de données comme déclassé ?

Étant donné que les bases de données Web et entreprise edition ne seront plus disponibles après septembre 2015, le portail des étiquettes de bases de données Web et entreprise que déclassé. L’étiquette déclassé fournit également un rappel que des bases de données Web et entreprise doivent être mis à niveau vers Standard, Basic ou Premium. Pour plus d’informations sur la mise à niveau des bases de données Web ou entreprise existantes vers les nouveaux niveaux de service, voir [mettre à niveau vers V12 de base de données SQL Azure](sql-database-upgrade-server-portal.md).

## <a name="which-new-service-tier-is-the-best-choice-to-upgrade-my-existing-web-or-business-database-to"></a>Le nouveau niveau de service est le meilleur choix pour mettre à niveau ma base de données Web ou entreprise existant à ?

Sélection d’un nouveau service niveau et les performances niveau approprié pour votre base de données Web ou entreprise existante dépend de la fonctionnalité et les performances de configuration spécifiques pour votre application.

Utilisez les recommandations couche tarification décrites ci-dessus, ou pour plus d’informations pour vous aider lors de la sélection d’un nouveau niveau de service approprié, voir [mise à niveau vers V12 de base de données SQL Azure](sql-database-upgrade-server-portal.md).

## <a name="why-is-microsoft-introducing-new-service-tiers"></a>Pourquoi Microsoft introduit de nouveaux niveaux de service ?

Selon les commentaires des clients, base de données SQL Azure introduit de nouveaux niveaux de service pour aider les clients plus facilement prend en charge les charges de base de données relationnelle. Les nouveaux niveaux sont conçus pour offrir des performances prévisibles sur un large éventail de sept niveaux pour légère aux demandes des applications de transactions de charge élevée. Par ailleurs, les nouveaux niveaux offrent une plage de fonctionnalités de continuité d’entreprise, un SLA disponibilité renforcée, tailles de base de données plus grandes pour moins d’argent et une meilleure expérience de facturation.

## <a name="where-can-i-learn-more-about-the-new-service-tiers"></a>Où puis-je obtenir plus d’informations sur les niveaux de service nouveau ?

Pour plus d’informations sur les nouveaux niveaux de service et le modèle de performances, voir [niveaux de Service](sql-database-service-tiers.md). Pour d’informations pour les nouveaux niveaux de service de tarification, voir [tarifs de base de données SQL](https://azure.microsoft.com/pricing/details/sql-database/).

## <a name="what-features-or-functionality-will-not-be-available-in-basic-standard-and-premium"></a>Quelles fonctions ou fonctionnalités ne sera pas disponible dans basique, Standard et Premium ?

La fonctionnalité fédérations sera supprimée dans les éditions Web et entreprise. Les clients qui ont besoin de horizontale leurs bases de données sont invités à la place utiliser ou migrer vers [une base de données élastique tools](sql-database-elastic-scale-get-started.md) pour [Base de données SQL Azure](sql-database-elastic-scale-get-started.md), qui simplifie la création et la gestion d’une application qui utilise ont. Une bibliothèque client .NET permet aux applications de définir comment les données sont mappées au milieu des fragments et des itinéraires demandes OLTP aux bases de données appropriées. Pour prendre en charge les opérations de gestion que reconfigurer la façon dont les données sont réparties sur milieu des fragments, un modèle de service cloud Azure est inclus que vous pouvez héberger votre propre abonnement Azure. Outre les [Outils de base de données élastique](sql-database-elastic-scale-get-started.md), Microsoft continuent à créer et publier des [instructions de modèles et pratiques ont personnalisé](https://msdn.microsoft.com/library/azure/dn764977.aspx) en fonction d’appris à partir des engagements client complète. Nouveaux clients qui ont besoin de fonctionnalités de mise à l’échelle doivent extraire les [Outils de base de données élastique](sql-database-elastic-scale-get-started.md) et/ou contacter le Support Microsoft pour évaluer leurs options.

Microsoft change également l’expérience de copie de base de données avec les bases de données Premium. CRÉER une base de données précédemment comme quota de base de données premium a été limitée... Une copie d’en T-SQL créé une base de données de Premium suspendue sans ressources réservées, qui a été perçu à la même vitesse comme une base de données professionnelle. Quota premium est maintenant disponible plus gratuitement, suspendue Premium est n’est plus pris en charge. Copies de base de données va être créés avec la même édition et le niveau de performance comme source et facturés en conséquence. Clients peuvent choisir de mettre à niveau des bases de données copiées à un niveau de niveau ou les performances de services différente pour réduire le coût leur si vous le souhaitez. Bases de données existantes Premium suspendue seront converties en Professionnel dans le cadre de cette version. Il est prévu que l’introduction de [Point-à-temps restaurer](sql-database-recovery-using-backups.md#point-in-time-restore) permet de réduire la nécessité d’effectuer des copies de sauvegarde des bases de données.

## <a name="how-does-basic-standard-and-premium-improve-my-billing-experience"></a>Comment est-elle basique, Standard et Premium meilleure mon expérience facturation ?

Base, Standard et les bases de données SQL Azure Premium sont facturés par l’heure, et que vous avez la possibilité de mettre à l’échelle chaque base de données vers le haut ou vers le bas. Vous êtes facturé à taux fixe en fonction de niveau le plus élevé service couche et de performances que vous choisissez pour chaque heure. En outre, les niveaux de performances (exemple : Basic, S1 et P2) sont répartis dans la facturation afficher le nombre de base de données jours/heures vous prévu en un mois unique pour chaque niveau de performance plus facilement. Bases de données Web et entreprise continuent à être facturé à l’aide des unités de base de données en fonction de la taille de la base de données. Visitez la [base de données SQL tarifs page](https://azure.microsoft.com/pricing/details/sql-database/) pour en savoir plus sur les tarifs et les différences entre les niveaux de service nouveau.


## <a name="see-also"></a>Voir aussi

[Base de données SQL Azure](https://azure.microsoft.com/documentation/services/sql-database/)

[Web et les prix de l’entreprise](https://azure.microsoft.com/pricing/details/sql-database/web-business/)

[Niveaux de service](sql-database-service-tiers.md)

[Mise à niveau vers V12 de base de données SQL Azure](sql-database-upgrade-server-portal.md)

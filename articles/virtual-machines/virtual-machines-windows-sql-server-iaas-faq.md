<properties
    pageTitle="SQL Server sur Machines virtuelles Azure FAQ | Microsoft Azure"
    description="Cet article fournit des réponses aux questions fréquemment posées sur l’exécution de SQL Server sur machines virtuelles Azure."
    services="virtual-machines-windows"
    documentationCenter=""
    authors="v-shysun"
    manager="felixwu"
    editor=""
    tags="azure-service-management"/>

<tags
    ms.service="virtual-machines-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="vm-windows-sql-server"
    ms.workload="infrastructure-services"
    ms.date="09/13/2016"
    ms.author="v-shysun"/>

# <a name="sql-server-on-azure-virtual-machines-faq"></a>SQL Server sur Machines virtuelles Azure Forum aux questions

Cette rubrique fournit des réponses à certaines des questions plus fréquentes sur l’exécution de [SQL Server Azure machines virtuelles en fonctionnement](https://azure.microsoft.com/services/virtual-machines/sql-server/).

[AZURE.INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="frequently-asked-questions"></a>Forum aux Questions

1. **Comment créer une machine virtuelle Azure avec SQL Server ?**

    Il existe deux façons de le faire. La solution la plus simple consiste à créer une Machine virtuelle incluant SQL Server. Pour un didacticiel sur qui s’inscrit à Azure et la création d’un VM SQL à partir du portail, voir [mise en service une machine virtuelle SQL Server dans le portail Azure](virtual-machines-windows-portal-sql-server-provision.md). Vous avez également la possibilité d’installation de SQL Server sur un ordinateur virtuel et réutilisation d’une licence en local avec [Licence mobilité via Software Assurance sur Azure](https://azure.microsoft.com/pricing/license-mobility/)manuellement.

1. **Quelle est la différence entre machines virtuelles SQL et le service de base de données SQL ?**

    Point de vue conceptuel, exécute SQL Server sur un ordinateur virtuel Azure ne diffère pas qui exécute SQL Server dans un centre de données distante. En revanche, [Base de données SQL](../sql-database/sql-database-technical-overview.md) offre de base de données sous forme de service. Base de données SQL, vous n’avez pas accès pour les ordinateurs qui héberge vos bases de données. Pour une comparaison complète, consultez [Choisissez un option de SQL Server cloud : base de données SQL Azure (PaaS) ou SQL Server sur Azure machines virtuelles (IaaS)](../sql-database/sql-database-paas-vs-sql-server-iaas.md).

1. **Comment puis-je migrer ma base de données SQL Server en local dans le Cloud ?**

    Tout d’abord créer une machine virtuelle Azure avec une instance de SQL Server. Migrer vos bases de données locale à cette instance. Pour les stratégies de migration de données, voir [migrer une base de données SQL Server à SQL Server dans une machine virtuelle Azure](virtual-machines-windows-migrate-sql.md).

2. **Puis-je modifier les fonctions installées ou installer une deuxième instance de SQL Server sur la même machine virtuelle ?**

    Oui. Le support d’installation de SQL Server se trouve dans un dossier sur le lecteur **C** . Exécutez **Setup.exe** à partir de cet emplacement pour ajouter des instances de SQL Server ou pour modifier d’autres fonctionnalités de SQL Server installées sur l’ordinateur.

3. **Comment passer vers une nouvelle version/édition de SQL Server dans une machine virtuelle Azure ?**

    Il n’existe actuellement aucune mise à niveau sur place pour SQL Server en cours d’exécution dans une machine virtuelle Azure. Créer une nouvelle machine virtuelle Azure avec la version de SQL Server souhaitée/edition, avant de migrer vos bases de données vers le nouveau serveur à l’aide des [techniques de migration des données](virtual-machines-windows-migrate-sql.md)standard.

4. **Comment puis-je installer ma copie sous licence de SQL Server sur un ordinateur virtuel Azure ?**

    Copier le support d’installation de SQL Server dans la machine virtuelle de Windows Server, puis installez SQL Server sur l’ordinateur virtuel. Pour les licences raisons, vous devez avoir [Mobilité licence via Software Assurance sur Azure](https://azure.microsoft.com/pricing/license-mobility/).

5. **Vous devez payer les coûts SQL d’une machine virtuelle s’il est utilisé uniquement pour la mise en veille/avec basculement ?**

    Si vous créez la VM SQL via la galerie, puis doit disposer d’une licence distincte de secours SQL VM et les tarifs sont la même. Si vous installez manuellement SQL Server sur une machine virtuelle à [Mobilité de licence](https://azure.microsoft.com/pricing/license-mobility/), vous avez la possibilité d’avoir une instance SQL passive gratuite pour le basculement. Veuillez consulter les restrictions et conditions.

6. **Comment sont mises à jour et des service packs appliquées à une machine virtuelle de SQL Server ?**

    Machines virtuelles vous permettent de vérifier sur l’ordinateur hôte, y compris quand et comment appliquer des mises à jour. Pour le système d’exploitation, vous pouvez appliquer manuellement des mises à jour windows, ou vous pouvez activer un service planification appelé [Correction automatique](virtual-machines-windows-classic-sql-automated-patching.md). Automatisé correctifs installe des mises à jour sont marqué comme importants, y compris les mises à jour de SQL Server dans cette catégorie. Autres mises à jour facultatives à SQL Server doivent être installés manuellement.

7. **Est-il possible de définir des configurations s’affichées ne pas dans la galerie de machine virtuelle (par exemple Windows 2008 R2 + SQL Server 2012) ?**

    Non. Pour les images de la galerie de machine virtuelle qui incluent SQL Server, vous devez sélectionner une des images fournies.

9. **Comment installer les outils de données SQL sur mon ordinateur virtuel Azure ?**

    Téléchargez et installez les outils de données SQL à partir de [Microsoft SQL Server Data Tools - Business Intelligence pour Visual Studio 2013 ](https://www.microsoft.com/en-us/download/details.aspx?id=42313).

## <a name="resources"></a>Ressources

Pour obtenir une vue d’ensemble de SQL Server Azure machines virtuelles en fonctionnement, regardez vidéo [Azure machine virtuelle est la meilleure plate-forme pour SQL Server 2016](https://channel9.msdn.com/Events/DataDriven/SQLServer2016/Azure-VM-is-the-best-platform-for-SQL-Server-2016). Vous pouvez également obtenir une bonne introduction dans la rubrique, [SQL Server dans la vue d’ensemble des Machines virtuelles Azure](virtual-machines-windows-sql-server-iaas-overview.md).

Autres ressources sont les suivantes :

- [Configurer une machine virtuelle SQL Server dans le portail Azure](virtual-machines-windows-portal-sql-server-provision.md)
- [Migration d’une base de données vers SQL Server sur un ordinateur virtuel Azure](virtual-machines-windows-migrate-sql.md)
- [Disponibilité et reprise pour SQL Server dans des Machines virtuelles Azure](virtual-machines-windows-sql-high-availability-dr.md)
- [Les procédures recommandées pour SQL Server dans le Machines virtuelles Azure](virtual-machines-windows-sql-performance.md)
- [Modèles d’application et les stratégies de développement pour SQL Server dans des Machines virtuelles Azure](virtual-machines-windows-sql-server-app-patterns-dev-strategies.md)

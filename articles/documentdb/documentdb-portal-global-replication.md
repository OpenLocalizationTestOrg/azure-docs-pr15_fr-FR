<properties
    pageTitle="Réplication de base de données globale DocumentDB | Microsoft Azure"
    description="Découvrez comment gérer la réplication globale de votre compte DocumentDB via le portail Azure."
    services="documentdb"
    keywords="base de données globale, réplication"
    documentationCenter=""
    authors="mimig1"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="documentdb"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/17/2016"
    ms.author="mimig"/>

# <a name="how-to-perform-documentdb-global-database-replication-using-the-azure-portal"></a>Comment faire pour effectuer la réplication de base de données globale DocumentDB via le portail Azure

Découvrez comment utiliser le portail Azure répliquer des données dans plusieurs régions pour la disponibilité globale de données dans Azure DocumentDB.

Pour plus d’informations sur la base de données globale comment fonctionne la réplication dans DocumentDB, voir [données répartir globalement avec DocumentDB](documentdb-distribute-data-globally.md). Pour plus d’informations sur l’exécution de la réplication de base de données globale par programme, consultez [développement avec les comptes DocumentDB plusieurs régions](documentdb-developing-with-multiple-regions.md).

> [AZURE.NOTE] Distribution globale des bases de données DocumentDB est généralement disponible et automatiquement activée pour tous les comptes DocumentDB nouvellement créés. Nous travaillons pour activer la distribution globale sur tous les comptes existants, mais en attendant, si vous souhaitez que la distribution globale activée pour votre compte, veuillez [contacter le support technique](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) et nous allons activez-le pour vous maintenant.

## <a id="addregion"></a>Ajouter des zones de base de données globale

DocumentDB est disponible dans la plupart des [régions Azure] [azureregions]. Après avoir sélectionné le niveau de cohérence par défaut pour votre compte de base de données, vous pouvez associer une ou plusieurs zones (selon votre choix des besoins de niveau et globale de la distribution de cohérence par défaut).

1. Dans le [portail Azure](https://portal.azure.com/), dans le Jumpbar, cliquez sur **Comptes DocumentDB**.
2. Dans la carte **DocumentDB compte** , sélectionnez le compte de base de données à modifier.
3. Dans la carte de compte, cliquez sur **répliquer globalement les données** dans le menu.
4. Dans la carte **répliquer globalement les données** , sélectionnez les régions pour ajouter ou supprimer, puis cliquez sur **Enregistrer**. Il existe un coût à l’ajout de régions, voir l’article [données répartir globalement avec DocumentDB](documentdb-distribute-data-globally.md) pour plus d’informations ou la [page de tarification](https://azure.microsoft.com/pricing/details/documentdb/) .

    ![Cliquez sur les zones dans la carte pour ajouter ou les supprimer][1]

### <a name="selecting-global-database-regions"></a>Sélection de régions globale de la base de données

Lorsque vous configurez deux ou plusieurs régions, il est recommandé que les régions sont sélectionnées basé sur des paires région décrites dans la [entreprise continuité et récupération d’urgence (BCDR) : régions appariés Azure]  [ bcdr] article.

Plus précisément, lorsque vous configurez plusieurs régions, veillez à sélectionner le même nombre de régions (+/-1 pour impair/pair) à partir de chacune des colonnes région appariés. Par exemple, si vous voulez déployer sur quatre zones US, sélectionnez deux régions US à partir de la colonne de gauche et deux par la droite. Par conséquent, ce qui suit est un ensemble approprié : États-Unis Ouest, orientale États-Unis, Amérique du Nord États-Unis et Sud.

Ce guide est important de suivre lorsque uniquement deux régions sont configurées pour récupération d’urgence. Pour plus de deux régions, la suite de ce guide est bonnes pratiques, mais pas critique dans la mesure où certaines des régions sélectionnés adhèrent à cette association.

<!---
## <a id="selectwriteregion"></a>Select the write region

While all regions associated with your DocumentDB database account can serve reads (both, single item as well as multi-item paginated reads) and queries, only one region can actively receive the write (insert, upsert, replace, delete) requests. To set the active write region, do the following  


1. In the **DocumentDB Account** blade, select the database account to modify.
2. In the account blade, if the **All Settings** blade is not already opened, click **All Settings**.
3. In the **All Settings** blade, click **Write Region Priority**.
    ![Change the write region under DocumentDB Account > Settings > Add/Remove Regions][2]
4. Click and drag regions to order the list of regions. The first region in the list of regions is the active write region.
    ![Change the write region by reordering the region list under DocumentDB Account > Settings > Change Write Regions][3]
-->

## <a id="next"></a>Étapes suivantes

Découvrez comment gérer la cohérence de votre compte globalement répliquée en lisant l’article [niveaux de cohérence dans DocumentDB](documentdb-consistency-levels.md).

Pour plus d’informations sur la base de données globale comment fonctionne la réplication dans DocumentDB, voir [données répartir globalement avec DocumentDB](documentdb-distribute-data-globally.md). Pour plus d’informations sur la réplication par programme des données dans plusieurs régions, consultez [développement avec les comptes DocumentDB plusieurs régions](documentdb-developing-with-multiple-regions.md).

<!--Image references-->
[1]: ./media/documentdb-portal-global-replication/documentdb-add-region.png
[2]: ./media/documentdb-portal-global-replication/documentdb_change_write_region-1.png
[3]: ./media/documentdb-portal-global-replication/documentdb_change_write_region-2.png

<!--Reference style links - using these makes the source content way more readable than using inline links-->
[bcdr]: https://azure.microsoft.com/documentation/articles/best-practices-availability-paired-regions/
[consistency]: https://azure.microsoft.com/documentation/articles/documentdb-consistency-levels/
[azureregions]: https://azure.microsoft.com/en-us/regions/#services
[offers]: https://azure.microsoft.com/en-us/pricing/details/documentdb/

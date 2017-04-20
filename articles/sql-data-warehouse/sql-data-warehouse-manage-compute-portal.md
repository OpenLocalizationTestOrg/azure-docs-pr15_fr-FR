<properties
   pageTitle="Gérer l’alimentation cluster dans SQL Azure Data Warehouse (Azure portal) | Microsoft Azure"
   description="Tâches du portail Azure pour gérer calculent power. Échelle de ressources de calcul en ajustant DWUs. Ou, interrompre et reprendre cluster ressources pour réduire les coûts."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="barbkess"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="08/22/2016"
   ms.author="barbkess;sonyama"/>

# <a name="manage-compute-power-in-azure-sql-data-warehouse-azure-portal"></a>Gérer l’alimentation cluster dans SQL Azure Data Warehouse (Azure portal)

> [AZURE.SELECTOR]
- [Vue d’ensemble](sql-data-warehouse-manage-compute-overview.md)
- [Portail](sql-data-warehouse-manage-compute-portal.md)
- [PowerShell](sql-data-warehouse-manage-compute-powershell.md)
- [RESTE](sql-data-warehouse-manage-compute-rest-api.md)
- [TSQL](sql-data-warehouse-manage-compute-tsql.md)


Adapter les performances en faisant évoluer calculent des ressources et la mémoire pour répondre à la demande de modification de votre charge de travail. Réduire les coûts par des ressources précédent mise à l’échelle au cours des heures non pointe ou cluster suspension complètement. 

Cet ensemble de tâches utilise le portail Azure pour :

- Échelle cluster
- Cluster pause
- CV cluster

Pour plus d’informations, voir [Gérer calculer vue d’ensemble][].

<a name="scale-performance-bk"></a>
<a name="scale-compute-bk"></a>

## <a name="scale-compute-power"></a>Échelle cluster power

[AZURE.INCLUDE [SQL Data Warehouse scale DWUs description](../../includes/sql-data-warehouse-scale-dwus-description.md)]

Pour modifier les ressources de calcul :

1. Ouvrez le [portail Azure][], ouvrez votre base de données, puis cliquez sur **échelle**.

    ![Cliquez sur échelle][1]

1. Dans la carte échelle, déplacez le curseur de gauche ou la droite pour modifier le paramètre DWU.

    ![Déplacer le curseur][2]

1. Cliquez sur **Enregistrer**. Un message de confirmation s’affiche. Cliquez sur **Oui** pour confirmer ou **Aucun** pour annuler.

    ![Cliquez sur Enregistrer][3]

<a name="pause-compute-bk"></a>

## <a name="pause-compute"></a>Cluster pause

[AZURE.INCLUDE [SQL Data Warehouse pause description](../../includes/sql-data-warehouse-pause-description.md)]

Pour mettre en pause une base de données :

1. Ouvrez le [portail Azure][] et ouvrez votre base de données. Notez que l’état est **en ligne**. 

    ![Statut de connexion][6]

1. Pour suspendre des ressources informatiques et la mémoire, cliquez sur **Pause**, puis un message de confirmation apparaît. Cliquez sur **Oui** pour confirmer ou **Aucun** pour annuler.

    ![Confirmer la pause][7]

1. Pendant le démarrage de la base de données SQL Data Warehouse, l’état est **interruption**.
2. Lorsque l’état est **suspendu**, l’opération de pause est terminée et que vous n’avez plus facturées pour DWUs.

    ![État de pause][4]

<a name="resume-compute-bk"></a>

## <a name="resume-compute"></a>CV cluster

[AZURE.INCLUDE [SQL Data Warehouse resume description](../../includes/sql-data-warehouse-resume-description.md)]Pour reprendre une base de données :

1. Ouvrez le [portail Azure][] et ouvrez votre base de données. Notez que l’état est **suspendu**. 

    ![Base de données pause][4]

1. Pour reprendre la base de données cliquez sur **Démarrer**et un message de confirmation apparaît. Cliquez sur **Oui** pour confirmer ou **Aucun** pour annuler.

    ![Confirmer CV][5]

1. Pendant le démarrage de la base de données SQL Data Warehouse, l’état est « Relance ».
2. Lorsque l’état est **en ligne**, la base de données est prêt.

    ![Statut de connexion][6]

<a name="next-steps-bk"></a>

## <a name="next-steps"></a>Étapes suivantes
Pour plus d’informations, voir [Présentation de la gestion][].

<!--Image references-->
[1]: ./media/sql-data-warehouse-manage-compute-portal/click-scale.png
[2]: ./media/sql-data-warehouse-manage-compute-portal/move-slider.png
[3]: ./media/sql-data-warehouse-manage-compute-portal/click-save.png
[4]: ./media/sql-data-warehouse-manage-compute-portal/resume-database.png
[5]: ./media/sql-data-warehouse-manage-compute-portal/resume-confirm.png
[6]: ./media/sql-data-warehouse-manage-compute-portal/pause-database.png
[7]: ./media/sql-data-warehouse-manage-compute-portal/pause-confirm.png

<!--Article references-->
[Présentation de la gestion]: ./sql-data-warehouse-overview-manage.md
[Gérer la vue d’ensemble de calcul]: ./sql-data-warehouse-manage-compute-overview.md

<!--MSDN references-->


<!--Other Web references-->

[Portail Azure]: http://portal.azure.com/

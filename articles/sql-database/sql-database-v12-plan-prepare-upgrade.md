<properties
    pageTitle="Planifier votre mise à niveau vers SQL de base de données V12 | Microsoft Azure"
    description="Décrit les préparations et les limites de mise à niveau vers la version V12 d’Azure SQL de base de données."
    services="sql-database"
    documentationCenter=""
    authors="MightyPen"
    manager="jhubbard"
    editor=""/>


<tags
    ms.service="sql-database"
    ms.workload="data-management"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/24/2016"
    ms.author="genemi"/>


# <a name="plan-and-prepare-to-upgrade-to-sql-database-v12"></a>Planifier et préparer la mise à niveau vers V12 de base de données SQL


Cette rubrique décrit la planification et préparation que vous devez effectuer pour mettre à niveau vos bases de données SQL Azure version 11 vers V12.


Un nouveau [Portail Azure](https://portal.azure.com/) est disponible pour prendre en charge votre mise à niveau vers V12.


Le tableau suivant répertorie les autres rubriques d’aide pour V12.


| Titre et liaison | Description du contenu |
| :--- | :--- |
| [Quelles sont les nouveautés dans V12 de base de données SQL](sql-database-v12-whats-new.md) | Décrit les détails de comment V12 apporte Azure SQL de base de données plus complète équivaut à Microsoft SQL Server. |
| [Créer une base de données dans V12 de base de données SQL](sql-database-get-started.md) | Décrit comment vous pouvez créer une nouvelle base de données SQL Azure en version V12. Il décrit les différentes options au-delà simplement une base de données vide. |


## <a name="plan-ahead"></a>Planifier à l’avance


Les sous-sections suivantes décrivent la formation et la prise de décisions que vous devez résoudre avant de vous procéder vers la mise à niveau votre base de données SQL Azure V12.

### <a name="version-clarification"></a>Clarifier version


Ce document concerne la mise à niveau de base de données Microsoft Azure SQL à partir de la version 11 V12. Plus formelle les numéros de version sont près de deux valeurs suivantes, comme indiqué par l’instruction Transact-SQL **Sélectionnez @@version; ** :


- 12.0.2000.8 *(ou un peu plus élevé, V12)*
- 11.0.9228.18 *(11)*
 - Parfois 11 a été également appelées SAWA v2.

### <a name="service-tier-planning"></a>Planification de niveau du service


En commençant par V12, base de données SQL Azure prend en charge uniquement les niveaux de service nommés basique, Standard et Premium. Le niveau de service Web et entreprise n’est pas pris en charge sur V12. Par conséquent, si vous envisagez de mettre à niveau votre base de données SQL Azure qui se trouve actuellement au niveau de service Web ou d’entreprise, vous devez décider quelle doit être son nouveau niveau de service.


Pour plus d’informations sur les niveaux de service Basique, Standard et Premium, voir :

- [Niveaux de service de base de données SQL](sql-database-service-tiers.md)
- [Mettre à niveau des bases de données SQL de base de données Web/entreprise sur de nouveaux niveaux de Service](sql-database-upgrade-server-portal.md)



### <a name="review-the-geo-replication-configuration"></a>Passez en revue la configuration de la réplication Geo


Si votre base de données SQL Azure est configuré pour la réplication de Geo, vous devez documenter la configuration en cours et arrêter Geo-réplication, avant de commencer les actions de préparation mise à niveau. Après que mise à niveau terminée, vous devez reconfigurer votre base de données pour la réplication de Geo.


La stratégie consiste à laisser la source intacte et tester sur une copie de la base de données.


## <a name="preparation-actions"></a>Actions de préparation


Une fois que vous avez terminé votre planification, vous pouvez effectuer les étapes décrites dans les sous-sections suivantes pour préparer la phase de mise à niveau finale d’action.


Description détaillée de la phase de mise à niveau finale est disponibles dans les rubriques d’aide qui sont liées en haut de cette rubrique d’aide.


### <a name="service-tier-actions"></a>Actions de niveau de service


Le service Web et entreprise tarifs niveau n’est pas pris en charge sur V12.


Si votre base de données SQL Azure 11 est une base de données Web ou entreprise, le processus de mise à niveau propose de passer votre base de données à un niveau pris en charge. La mise à niveau recommande un niveau qui correspond à l’historique de la charge de travail de votre base de données. Toutefois, vous pouvez choisir n’importe quel niveau pris en charge que vous le souhaitez.


Vous pouvez réduire les étapes nécessaires au cours de la mise à niveau en optant votre base de données 11 en dehors de la couche Web et entreprise avant de commencer la mise à niveau. Vous pouvez le faire à l’aide du nouveau [Portail Azure](https://portal.azure.com/).


Si vous ne savez pas quel niveau de service pour l’atteindre, le niveau S2 de la couche Standard peut être un choix initial sensible. N’importe quel niveau inférieur ont moins de ressources que la couche Web et entreprise avait.


### <a name="suspend-geo-replication-during-upgrade"></a>Suspendre la réplication de Geo pendant la mise à niveau


Impossible d’exécuter la mise à niveau vers V12 si Geo réplication est active sur votre base de données. Vous devez d’abord reconfigurer votre base de données pour arrêter d’utiliser la réplication Geo.


Une fois la mise à niveau terminée, vous pouvez configurer votre base de données pour réutiliser Geo réplication.


### <a name="open-ports-on-an-azure-vm-for-client-connectivity"></a>Ports ouverts sur une machine virtuelle Azure pour la connectivité des clients


Si votre programme client se connecte à V12 de base de données SQL pendant l’exécution de votre client sur une Azure machine virtuelle (), vous devez ouvrir les plages de ports suivants sur l’ordinateur virtuel :

- 11000 11999
- 14000 14999


Cliquez [ici](sql-database-develop-direct-route-ports-adonet-v12.md) pour plus d’informations sur les ports pour V12 de base de données SQL. Les ports sont requis par les améliorations des performances de V12 de base de données SQL.


##<a id="limitations"></a>Limitations pendant et après la mise à niveau vers V12


### <a name="portals-for-v12"></a>Portails pour V12


Il existe trois portails pour Azure, et chacune possède les différentes fonctions concernant V12 de base de données SQL.


- [http://Portal.Azure.com/](https://portal.azure.com/)<br/>Ce portail Azure sont les nouveautés et est toujours au statut d’aperçu. Ce portail n’est pas tout à fait à complète disponibilité (générale). Ce portail :
 - Peuvent gérer votre serveur V12 et la base de données.
 - Peut mettre à niveau votre base de données 11 vers V12.


- [http://Manage.windowsazure.com/](http://manage.windowsazure.com/)<br/>Ce portail classique Azure peuvent être progressivement. Ce portail :
 - Peuvent gérer votre serveur V12 et la base de données.
 - CAN *non* mise à niveau votre 11 base de données à V12.


- (http://*yourservername*. database.windows.net)<br/>Portail classique de base de données SQL Azure :
 - Pouvez*pas* gérer les serveurs V12.


Nous vous invitons à vous connecter à vos bases de données SQL Azure avec Visual Studio 2015 (VS2015). VS2015 peut être utilisée pour les tâches suivantes :


- Pour exécuter une instruction Transact-SQL.
- Pour créer un schéma.
- Développement d’une base de données, en ligne ou hors connexion.


Ou à la place, vous pouvez télécharger gratuitement [Visual Studio Communauté 2015](https://www.visualstudio.com/vs/community/), qui est une version complète de VS2015.


Dans l’ancienne portail classique Azure, dans la page de base de données, vous pouvez cliquer sur **ouvert dans Visual Studio** pour lancer VS2015 sur votre ordinateur pour la connexion à votre base de données SQL Azure.


Pour une autre, vous pouvez utiliser SQL Server Management Studio (SSMS) 2014 avec [CU6](http://support.microsoft.com/kb/3031047/) pour vous connecter à la base de données SQL Azure. Plus de détails sont sur ce billet de blog :<br/>[Mises à jour de base de données SQL Azure des outils clients](https://azure.microsoft.com/blog/2014/12/22/client-tooling-updates-for-azure-sql-database/).


> [AZURE.IMPORTANT] Il est recommandé d’utiliser toujours la dernière version de Management Studio doit pour rester synchronisé avec les mises à jour Microsoft Azure et base de données SQL. [Mettre à jour de SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx).


### <a name="limitation-during-upgrade-to-v12"></a>Limitation *au cours de* mise à niveau vers V12


La base de données 11 reste disponible pour l’accès aux données pendant la mise à niveau vers V12. Il existe encore quelques limitations à prendre en compte.


| Limitation | Description |
| :--- | :--- |
| Durée de mise à niveau | La durée de mise à niveau dépend de la taille, Édition et le nombre de bases de données sur le serveur. Le processus de mise à niveau peut exécuter pour les heures et jours pour les serveurs en particulier pour les serveurs contenant des bases de données :<br/><br/>*Plus de 50 Go, ou<br/> * À un niveau de service non premium<br/><br/>Création de nouvelles bases de données sur le serveur pendant la mise à niveau peut également augmenter la durée de la mise à niveau. |
| Aucune réplication Geo | Geo réplication n’est pas pris en charge sur un serveur V12 qui travaille actuellement dans une mise à niveau à partir de 11. |
| Base de données indisponible brièvement en phase finale de mise à niveau vers V12 | Les bases de données appartenant à votre serveur 11 restent disponibles pendant le processus de mise à niveau. Toutefois, la connexion au serveur et les bases de données est brièvement pas disponible dans la phase finale, lorsque le commutateur sur commence à partir de 11 à la V12 prêt.<br/><br/>Le commutateur sur période peut être comprise entre 40 secondes à 5 minutes. Pour la plupart des serveurs, passage est censée se terminer dans les 90 secondes. Commutateur au fil du temps augmente pour les serveurs qui ont un grand nombre de bases de données, ou lorsque les bases de données ont les charges de travail en écriture. |


### <a name="limitation-after-upgrade-to-v12"></a>Limitation *après* la mise à niveau vers V12


| Limitation | Description |
| :--- | :--- |
| Impossible de rétablir 11 | Après une mise à niveau sur place, le résultat ne peut pas être rétabli ou annulé. |
| Couche Web ou entreprise | Une fois que la mise à niveau commence, le serveur pour la nouvelle base de données V12 pouvez n’est plus reconnu ou acceptez le niveau de service Web ou entreprise. |



### <a name="export-and-import-after-upgrade-to-v12"></a>Exporter et importer *après* mise à niveau vers V12


Vous pouvez exporter ou importer une base de données V12 à l’aide du [Portail Azure](https://portal.azure.com/). Vous pouvez exporter ou importer en utilisant l’un des outils suivants :


- SQL Server Management Studio (SSMS)
- Visual Studio 2015
- Infrastructure d’Application de couche données (DacFx)


Toutefois, pour utiliser les outils, vous devez tout d’abord avoir ou installer les dernières mises à jour pour vous assurer qu’ils prennent en charge les nouvelles fonctionnalités V12 :


- [Mise à jour cumulative 6 pour SQL Server Management Studio 2014](http://support2.microsoft.com/kb/3031047)
- [Mise à jour de février 2015 pour la base de données SQL Server outils dans Visual Studio 2013](https://msdn.microsoft.com/data/hh297027)
- [Infrastructure de l’Application de couche données février 2015 (DacFx) pour V12 de base de données SQL Azure](http://www.microsoft.com/download/details.aspx?id=45886)


> [AZURE.NOTE] Les liens d’outil précédentes ont été mis à jour ou après le 2 mars 2015. Nous vous recommandons d’utiliser ces mises à jour la plus récentes de ces outils.


#### <a name="automated-export"></a>Exportation automatisée


Exportation automatique reste disponible en tant qu’aperçu.  Aucune mise à jour d’outil client n’est nécessaires pour utiliser automatisé exporter.  Si vous choisissez prendre le fichier qui en résulte et importer vers un serveur locales, les mises à jour des outils répertoriés ci-dessus sont nécessaires pour importer correctement.


### <a name="restore-to-v12-of-a-deleted-v11-database"></a>Restaurer à V12 d’une base de données 11 supprimé

Le scénario suivant décrit le fait qu’une base de données SQL Azure 11 supprimé peut être restauré sur un serveur de base de données SQL V12 Azure.

1. Supposons que vous disposez d’un serveur de base de données SQL 11 Azure. <br/> Sur le serveur, vous avez une base de données au niveau de service Web-Business obsolète.
2. Vous supprimez la base de données.
3. Vous mettez à niveau le serveur à V12.
4. Ensuite, vous restaurez la base de données sur le serveur. <br/> La base de données est ainsi mis à niveau vers V12, au niveau de la couche de service Standard S0.
5. Vous pouvez basculer la base de données à n’importe quel niveau de service pris en charge, si S0 n’est pas vos préférences.


### <a name="powershell-cmdlets"></a>Applets de commande PowerShell


Applets de commande PowerShell sont disponibles pour démarrer, arrêter ou contrôler une mise à niveau vers V12 de base de données SQL Azure dans 11 ou tout autre version de pré-V12.

- [Mise à niveau vers V12 de base de données SQL à l’aide de PowerShell](sql-database-upgrade-server-powershell.md)

Pour des informations de référence sur ces applets de commande PowerShell, voir :


- [Get-AzureRMSqlServerUpgrade](https://msdn.microsoft.com/library/azure/mt603582.aspx)
- [Démarrage AzureRMSqlServerUpgrade](https://msdn.microsoft.com/library/azure/mt619403.aspx)
- [Stop AzureRMSqlServerUpgrade](https://msdn.microsoft.com/library/azure/mt603589.aspx)


L’applet de commande Stop signifie Annuler, placez le curseur pas. Il n’existe aucun moyen à reprendre une mise à niveau, différent de redémarrer à partir du début. L’applet de commande Stop efface et libère toutes les ressources appropriées.


## <a name="failure-resolution"></a>Résolution de défaillance


Si la mise à niveau échoue pour une raison quelconque impaire, votre base de données 11 reste active et disponible comme d’habitude.


## <a name="related-links"></a>Liens connexes


- Microsoft Azure [fonctionnalités](https://azure.microsoft.com/services/preview/)


<!--Anchors-->
[Subheading 1]: #subheading-1

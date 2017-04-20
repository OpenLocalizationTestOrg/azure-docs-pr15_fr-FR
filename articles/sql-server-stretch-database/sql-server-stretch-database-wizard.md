<properties
    pageTitle="Prise en main en exécutant la base de données activer étirement Assistant | Microsoft Azure"
    description="Découvrez comment configurer une base de données pour étirement de base de données en exécutant la base de données activer Assistant étirement."
    services="sql-server-stretch-database"
    documentationCenter=""
    authors="douglaslMS"
    manager="jhubbard"
    editor=""/>

<tags
    ms.service="sql-server-stretch-database"
    ms.workload="data-management"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="hero-article"
    ms.date="08/05/2016"
    ms.author="douglasl"/>

# <a name="get-started-by-running-the-enable-database-for-stretch-wizard"></a>Prise en main en exécutant la base de données activer Assistant étirement

Pour configurer une base de données de base de données étirer, exécutez la base de données activer Assistant étirement.  Cette rubrique décrit les informations que vous devrez entrer et les choix que vous devez effectuer dans l’Assistant.

Pour en savoir plus sur la base de données étirer, voir [Étirement de base de données](sql-server-stretch-database-overview.md).

 >   [AZURE.NOTE] Plus tard, si vous désactivez étirement de base de données, n’oubliez pas que la désactivation de la base de données étirement pour une table ou une base de données ne supprime pas l’objet distant. Si vous voulez supprimer la table distante ou la base de données distante, vous devez glisser à l’aide du portail de gestion Azure. Les objets distants continuent d’engager des coûts Azure jusqu'à ce que vous les supprimez manuellement. 

## <a name="launch-the-wizard"></a>Lancer l’Assistant

1.  Dans SQL Server Management Studio, dans l’Explorateur d’objets, sélectionnez la base de données sur lequel vous voulez activer étirement.

2.  Droite\-cliquez sur et sélectionnez **tâches**et puis sélectionnez **Étirer**, puis **Activer** pour lancer l’Assistant.

## <a name="Intro"></a>Introduction
Passez en revue l’objectif de l’Assistant et les conditions préalables.

Les conditions préalables importantes sont les suivantes :

-   Vous devez être un administrateur pour modifier les paramètres de base de données.
-   Vous devez disposer d’un abonnement Microsoft Azure.
-   Votre serveur SQL Server doit être en mesure de communiquer avec le serveur distant Azure.

![Page d’introduction de l’Assistant de base de données étirement][StretchWizardImage1]

## <a name="Tables"></a>Sélectionner les tables
Sélectionnez les tables que vous souhaitez activer pour étirement.

Tables avec un grand nombre de lignes s’affichent en haut de la liste triée. Avant de l’Assistant affiche la liste des tables, il les analyse pour les types de données qui ne sont pas pris en charge par étirement de base de données.

![Page Sélectionner les tables de l’Assistant de base de données étirement][StretchWizardImage2]

|Colonne|Description|
|----------|---------------|
|(sans titre)|Cochez la case à cocher de cette colonne pour activer la table sélectionnée pour étirement.|
|**Nom**|Spécifie le nom de la colonne du tableau.|
|(sans titre)|Un symbole dans cette colonne peut représenter un avertissement n’apparaît-il pas\'t vous empêcher d’activation de la table sélectionnée pour étirement. Il peut également représenter un problème de blocage qui vous empêche de l’activation de la table sélectionnée pour étirement \- par exemple, étant donné que la table utilise un type de données non prises en charge. Pointez sur le symbole pour afficher plus d’informations dans une info-bulle. Pour plus d’informations, voir [Limitations de base de données étirement](sql-server-stretch-database-limitations.md).|
|**Elle est étirée**|Indique si la table est déjà activée pour étirement.|
|**Migrer**|Vous pouvez déplacer un tableau entier (**Tableau entier**), ou vous pouvez spécifier un filtre sur une colonne existante dans la table. Si vous souhaitez utiliser une fonction de filtre différent pour sélectionner les lignes à migrer, exécutez l’instruction ALTER TABLE pour spécifier la fonction filter lorsque vous quittez l’Assistant. Pour plus d’informations sur la fonction filter, consultez [Sélectionner des lignes pour migrer en utilisant une fonction de filtre](sql-server-stretch-database-predicate-function.md). Pour plus d’informations sur la façon d’appliquer la fonction, voir [Activer étirement de base de données pour une table](sql-server-stretch-database-enable-table.md) ou [ALTER TABLE (Transact-SQL)](https://msdn.microsoft.com/library/ms190273.aspx).|
|**Lignes**|Spécifie le nombre de lignes dans la table.|
|**Taille (Ko)**|Spécifie la taille de la table en Ko.|

## <a name="Filter"></a>Vous pouvez également fournir un filtre de lignes

Si vous souhaitez fournir une fonction de filtre pour sélectionner les lignes à migrer, effectuer les opérations suivantes dans la page **Sélectionner des tables** .

1.  Dans la liste **Sélectionnez les tables que vous voulez étirer** , cliquez sur **Tableau entier** dans la ligne de la table. La boîte de dialogue **Sélectionner les lignes à étirer** s’ouvre.

    ![Définir une fonction de filtre][StretchWizardImage2a]

2.  Dans la boîte de dialogue **Sélectionner les lignes à étirer** , sélectionnez **Choisir les lignes**.

3.  Dans le **champ nom**, attribuez un nom pour la fonction filter.

4.  Pour la clause **Where** , sélectionnez une colonne de la table, choisissez un opérateur, puis fournir une valeur.

5. Cliquez sur **Vérifier** pour tester la fonction. Si la fonction renvoie des résultats de la table - autrement dit, s’il existe pour migrer les lignes qui répondent à la condition - le test signale de **Réussite**.

    >   [AZURE.NOTE] La zone de texte qui affiche la requête de filtre est en lecture seule. Vous ne pouvez pas modifier la requête dans la zone de texte.

6.  Cliquez sur terminé pour revenir à la page **Sélectionner les tables** .

La fonction filter est créée dans SQL Server uniquement lorsque vous avez terminé l’Assistant. Jusque là, vous pouvez revenir à la page **Sélectionner des tables** pour modifier ou renommer la fonction filter.

![Sélectionnez page Tables après avoir défini une fonction de filtre][StretchWizardImage2b]

Si vous voulez utiliser un autre type de la fonction filter pour sélectionner des lignes à déplacer, effectuez l’une des manières suivantes.  

-   Quitter l’Assistant et exécutez l’instruction ALTER TABLE pour activer l’étirement pour le tableau et spécifier une fonction de filtre. Pour plus d’informations, voir [Activer étirement de base de données pour une table](sql-server-stretch-database-enable-table.md).  

-   Exécutez l’instruction ALTER TABLE pour spécifier une fonction de filtre lorsque vous quittez l’Assistant. Pour les étapes à suivre, voir [Ajouter une fonction de filtre après l’exécution de l’Assistant](sql-server-stretch-database-predicate-function.md#addafterwiz).

## <a name="Configure"></a>Configurer le déploiement d’Azure

1.  Se connecter à Microsoft Azure avec un compte Microsoft.

    ![Se connecter à Azure - Assistant étirement de base de données][StretchWizardImage3]

2.  Sélectionnez l’abonnement Azure existant pour étirement de base de données.

3.  Sélectionnez une région Azure.
    -   Si vous créez un nouveau serveur, le serveur est créé dans cette zone.  
    -   Si vous avez des serveurs existants dans la zone sélectionnée, l’Assistant répertorie les lorsque vous choisissez **server existante**.

    Pour réduire le temps de latence, sélectionnez la région Azure dans lequel se trouve votre serveur SQL Server. Pour plus d’informations sur les zones, voir [Régions Azure](https://azure.microsoft.com/regions/).

4.  Indiquez si vous souhaitez utiliser un serveur existant ou créer un nouveau serveur Azure.

    Si l’annuaire Active Directory sur votre serveur SQL fédérée avec Azure Active Directory, vous pouvez éventuellement utiliser un compte de service fédéré pour SQL Server pour communiquer avec le serveur distant Azure. Pour plus d’informations sur la configuration requise pour cette option, voir [Modifier base de données définir les Options (Transact-SQL)](https://msdn.microsoft.com/library/bb522682.aspx).

    -   **Créer un nouveau serveur**

        1.  Créer une connexion et un mot de passe pour l’administrateur du serveur.

        2.  Vous pouvez également utiliser un compte de service fédéré pour SQL Server pour communiquer avec le serveur distant Azure.

        ![Créer un nouveau serveur Azure - Assistant étirement de base de données][StretchWizardImage4]

    -   **Serveur existant**

        1.  Sélectionnez le serveur Azure existant.

        2.  Sélectionnez la méthode d’authentification.

            -   Si vous sélectionnez **L’authentification SQL Server**, fournissent la connexion de l’administrateur et le mot de passe.

            -   Sélectionnez **Authentification intégrée à Active Directory** pour utiliser un compte de service fédéré pour SQL Server pour communiquer avec le serveur distant Azure. Si le serveur sélectionné n’est pas intégré à Azure Active Directory, cette option ne s’affiche.

        ![Sélectionnez serveur Azure existant - Assistant étirement de base de données][StretchWizardImage5]

## <a name="Credentials"></a>Sécuriser les informations d’identification
Vous devez disposer d’une clé principale pour sécuriser les informations d’identification étirement de base de données pour se connecter à la base de données distante.  

S’il existe déjà une clé principale de base de données, entrez le mot de passe pour qu’elle.  

![Page d’informations d’identification de l’Assistant étirement de base de données de banque d’informations sécurisé][StretchWizardImage6b]

Si la base de données n’a pas d’une clé principale existante, entrez un mot de passe fort pour créer une clé principale de base de données.  

![Page d’informations d’identification de l’Assistant étirement de base de données de banque d’informations sécurisé][StretchWizardImage6]

Pour plus d’informations sur la clé principale de base de données, voir [créer une clé de masque (Transact-SQL)](https://msdn.microsoft.com/library/ms174382.aspx) et [créer une clé principale de base de données](https://msdn.microsoft.com/library/aa337551.aspx). Pour plus d’informations sur les informations d’identification que l’Assistant crée, voir [Créer de base de données inclus dans l’étendue d’informations d’identification (Transact-SQL)](https://msdn.microsoft.com/library/mt270260.aspx).

## <a name="Network"></a>Sélectionnez l’adresse IP
Utiliser la plage d’adresses IP sous-réseau (recommandé) ou l’adresse IP de votre serveur SQL Server, pour créer une règle de pare-feu sur Azure qui vous permet de SQL Server communiquer avec le serveur distant Azure.

L’adresse IP ou les adresses que vous spécifiez dans cette page indiquent au serveur Azure pour autoriser les données entrantes, les requêtes et les opérations de gestion initiées par SQL Server pour passer à travers le pare-feu Azure. L’Assistant ne modifie pas rien dans les paramètres du pare-feu sur SQL Server.

![Sélectionnez la page adresse IP de l’Assistant de base de données étirement][StretchWizardImage7]

## <a name="Summary"></a>Résumé
Passez en revue les valeurs que vous avez entrées et les options que vous avez sélectionné dans l’Assistant et les coûts estimées d’Azure. Sélectionnez **Terminer** pour activer étirement.

![Page de résumé de l’Assistant de base de données étirement][StretchWizardImage8]

## <a name="Results"></a>Résultats
Passez en revue les résultats.

Pour contrôler le statut de migration des données, voir [moniteur et résoudre les problèmes de migration de données (base de données étirement)](sql-server-stretch-database-monitor.md).

![Page de résultats de l’Assistant de base de données étirement][StretchWizardImage9]

## <a name="KnownIssues"></a>Résolution des problèmes de l’Assistant
**L’Assistant étirement de base de données a échoué.**
Si étirement de base de données n’est pas encore activée au niveau du serveur et que vous exécutez l’Assistant sans le système autorisations d’administrateur pour l’activer, l’Assistant échoue. Demandez à l’administrateur système pour activer étirement de base de données sur l’instance du serveur local, puis réexécutez l’Assistant. Pour plus d’informations, voir [prérequises : autorisé à activer étirement de base de données sur le serveur](sql-server-stretch-database-enable-database.md#EnableTSQLServer).

## <a name="next-steps"></a>Étapes suivantes
Activer des tables supplémentaires pour étirement de base de données. Contrôler la migration des données et de gérer étirement\-activé les bases de données et des tables.

-   [Activer la base de données étirement pour une table](sql-server-stretch-database-enable-table.md) activer des tables supplémentaires.

-   [Moniteur et résoudre les problèmes de migration des données](sql-server-stretch-database-monitor.md) pour afficher l’état de la migration.

-   [Interrompre et reprendre étirement de base de données](sql-server-stretch-database-pause.md)

-   [Gérer et résoudre les problèmes de base de données étirement](sql-server-stretch-database-manage.md)

-   [Bases de données compatibles avec étirement de sauvegarde](sql-server-stretch-database-backup.md)

## <a name="see-also"></a>Voir aussi

[Activer étirement de base de données pour une base de données](sql-server-stretch-database-enable-database.md)

[Activer étirement de base de données pour une table](sql-server-stretch-database-enable-table.md)

[StretchWizardImage1]: ./media/sql-server-stretch-database-wizard/stretchwiz1.png
[StretchWizardImage2]: ./media/sql-server-stretch-database-wizard/stretchwiz2.png
[StretchWizardImage2a]: ./media/sql-server-stretch-database-wizard/stretchwiz2a.png
[StretchWizardImage2b]: ./media/sql-server-stretch-database-wizard/stretchwiz2b.png
[StretchWizardImage3]: ./media/sql-server-stretch-database-wizard/stretchwiz3.png
[StretchWizardImage4]: ./media/sql-server-stretch-database-wizard/stretchwiz4.png
[StretchWizardImage5]: ./media/sql-server-stretch-database-wizard/stretchwiz5.png
[StretchWizardImage6]: ./media/sql-server-stretch-database-wizard/stretchwiz6.png
[StretchWizardImage6b]: ./media/sql-server-stretch-database-wizard/stretchwiz6b.png
[StretchWizardImage7]: ./media/sql-server-stretch-database-wizard/stretchwiz7.png
[StretchWizardImage8]: ./media/sql-server-stretch-database-wizard/stretchwiz8.png
[StretchWizardImage9]: ./media/sql-server-stretch-database-wizard/stretchwiz9.png

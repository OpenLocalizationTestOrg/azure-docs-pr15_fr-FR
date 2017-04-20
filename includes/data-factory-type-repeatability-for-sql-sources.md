## <a name="repeatability-during-copy"></a>Répétabilité au cours de copie

Lors de la copie de données à Azure SQL/SQL Server à partir d’autres données stocke il faut esprit répétabilité afin d’éviter des résultats inattendus. 

Lorsque vous copiez les données à la base de données Azure SQL/SQL Server, copie activité sera par défaut ajout le jeu de données à la table récepteur par défaut. Par exemple, lorsque vous copiez les données d’une source de fichier CSV (valeurs de données séparées par des virgules) contenant les deux enregistrements de serveur de base de données SQL/SQL Azure, il s’agit de la table ressemble à :
    
    ID  Product     Quantity    ModifiedDate
    ... ...         ...         ...
    6   Flat Washer 3           2015-05-01 00:00:00
    7   Down Tube   2           2015-05-01 00:00:00

Supposons que vous a détecté des erreurs dans le fichier source et mis à jour la quantité de tubes vers le bas à partir de 2 à 4 dans le fichier source. Si vous exécutez de nouveau la tranche de données pour cette période, vous trouverez deux enregistrements ajoutés à la base de données Azure SQL/SQL Server. La ci-dessous suppose aucun des colonnes de la table possède la contrainte de clé primaire.
    
    ID  Product     Quantity    ModifiedDate
    ... ...         ...         ...
    6   Flat Washer 3           2015-05-01 00:00:00
    7   Down Tube   2           2015-05-01 00:00:00
    6   Flat Washer 3           2015-05-01 00:00:00
    7   Down Tube   4           2015-05-01 00:00:00

Pour éviter ce problème, vous devez spécifier sémantique UPSERT grâce à un de le ci-dessous 2 mécanismes indiqués ci-après.

> [AZURE.NOTE] Un secteur peut être relancer automatiquement dans Azure Data Factory conformément à la stratégie de nouvelles tentatives spécifiée.

### <a name="mechanism-1"></a>Mécanisme 1

Vous pouvez tirer parti de propriété **sqlWriterCleanupScript** pour effectuer tout d’abord action de nettoyage lors de l’exécution d’un secteur. 

    "sink":  
    { 
      "type": "SqlSink", 
      "sqlWriterCleanupScript": "$$Text.Format('DELETE FROM table WHERE ModifiedDate >= \\'{0:yyyy-MM-dd HH:mm}\\' AND ModifiedDate < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
    }

Le script de nettoyage sera exécuté à la première pendant la copie d’un secteur donné qui supprime les données à partir de la Table SQL correspondant à ce secteur. L’activité par la suite insère les données dans la Table SQL. 

Si le secteur est maintenant exécutez à nouveau, puis vous trouverez que la quantité est mis à jour comme vous le souhaitez.
    
    ID  Product     Quantity    ModifiedDate
    ... ...         ...         ...
    6   Flat Washer 3           2015-05-01 00:00:00
    7   Down Tube   4           2015-05-01 00:00:00

Supposons que l’enregistrement rondelle plate est supprimé de la csv d’origine. Exécuter de nouveau le secteur produit le résultat suivant : 
    
    ID  Product     Quantity    ModifiedDate
    ... ...         ...         ...
    7   Down Tube   4           2015-05-01 00:00:00

Rien de nouveau devait être effectués. L’activité de copie avez exécuté le script de nettoyage de disque pour supprimer les données correspondantes pour cette section. Puis il lire des entrées de la csv (qui puis contenues uniquement 1 enregistrement) et insérées dans la Table. 

### <a name="mechanism-2"></a>Mécanisme de 2
> [AZURE.IMPORTANT] sliceIdentifierColumnName n’est pas prise en charge pour Azure SQL Data Warehouse pour le moment. 

Un autre mécanisme pour réaliser répétabilité est en demandant une colonne dédiée (**sliceIdentifierColumnName**) dans la Table cible. Cette colonne peut être utilisée par Azure Data Factory pour garantir que la source et destination restent synchronisés. Cette approche fonctionne lorsqu’il y a flexibilité lors de la modification ou de définition du schéma de Table SQL de destination. 

Cette colonne peut être utilisée par les Azure Data Factory à des fins de répétabilité et de la procédure Azure Data Factory apportera pas les modifications de schéma à la Table. Moyen d’utiliser cette approche :

1.  Définir une colonne de type binaire (32) dans la Table SQL de destination. Il doit être sans les contraintes sur cette colonne. Nous allons nommez cette colonne en tant que « ColumnForADFuseOnly » dans cet exemple.
2.  Utiliser dans l’activité de copie comme suit :

        "sink":  
        { 
          "type": "SqlSink", 
          "sliceIdentifierColumnName": "ColumnForADFuseOnly"
        }

Azure Data Factory remplit cette colonne selon sa nécessité d’assurer que la source et destination restent synchronisés. Les valeurs de cette colonne ne doivent pas être utilisées en dehors de ce contexte par l’utilisateur. 

Tout comme mécanisme de 1, activité de copie sera automatiquement nettoyer tout d’abord les données pour le secteur donné à partir de la Table SQL de destination, puis exécutez l’activité copie normalement pour insérer les données de source à destination pour cette section. 

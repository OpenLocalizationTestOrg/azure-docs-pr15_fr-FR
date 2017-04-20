## <a name="repeatability-during-copy"></a>Répétabilité au cours de copie

Lorsque vous copiez les données du et au stores relationnelles, vous devez garder répétabilité à l’esprit afin d’éviter des résultats inattendus. 

Un secteur peut être réexécuter automatiquement dans Azure Data Factory conformément à la stratégie de nouvelles tentatives spécifiée. Nous vous conseillons de définir une stratégie de réessayer de se protéger contre les incidents transitoires. Par conséquent, répétabilité est un aspect important à prendre en compte pendant le déplacement de données. 

**En tant que source :**

> [AZURE.NOTE] Les exemples suivants sont appliquent aux SQL Azure, mais sont appliquent à n’importe quel magasin de données qui prend en charge les jeux de données rectangulaire. Vous devrez peut-être ajuster le **type** de source et de la propriété de **requête** (par exemple : requête au lieu de sqlReaderQuery) pour les données stocker.   

En règle générale, lors de la lecture à partir de magasins relationnelles, vous pouvez lire uniquement les données correspondant à ce secteur. Un moyen serait en utilisant les variables WindowStart et WindowEnd disponibles dans Azure Data Factory. En savoir plus sur les variables et les fonctions dans Azure Data Factory ici dans l’article de la [planification et l’exécution](../articles/data-factory/data-factory-scheduling-and-execution.md) . Exemple : 
    
      "source": {
        "type": "SqlSource",
        "sqlReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd HH:mm\\' AND timestampcolumn < \\'{1:yyyy-MM-dd HH:mm\\'', WindowStart, WindowEnd)"
      },

Cette requête lit les données à partir de 'MyTable' qui est comprise dans la plage de durée de secteur. Réexécuter de cette section grâce au également toujours ce comportement. 

Dans les autres cas, vous souhaiterez peut-être lire l’intégralité du tableau (supposons que pour une seule fois déplacer uniquement) et peut définir la sqlReaderQuery comme suit :

    
    "source": {
                "type": "SqlSource",
                "sqlReaderQuery": "select * from MyTable"
              },
    

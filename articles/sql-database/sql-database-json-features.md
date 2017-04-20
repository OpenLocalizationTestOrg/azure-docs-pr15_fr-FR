<properties
    pageTitle="Fonctionnalités JSON de base de données SQL Azure | Microsoft Azure"
    description="Base de données SQL Azure vous permet d’analyse, requête et le format des données en notation Notation JSON (JavaScript Object)."
    services="sql-database"
    documentationCenter=""
    authors="jovanpop-msft"
    manager="jhubbard"
    editor=""/>

<tags
    ms.service="sql-database"
    ms.devlang="NA"
    ms.date="08/17/2016"
    ms.author="jovanpop"
   ms.workload="NA"
    ms.topic="article"
    ms.tgt_pltfrm="NA"/>



# <a name="getting-started-with-json-features-in-azure-sql-database"></a>Mise en route des fonctionnalités JSON dans la base de données SQL Azure

Base de données SQL Azure vous permet d’analyser et interroger les données représentées au format JavaScript Object Notation [(JSON)](http://www.json.org/) et exporter vos données relationnelles en tant que texte JSON.

JSON est un format de données les plus consultés utilisé pour échanger des données dans les applications web moderne et mobiles. JSON est également utilisée pour le stockage des données semi-structurées dans les fichiers journaux ou dans les bases de données NoSQL comme [DocumentDB Azure](https://azure.microsoft.com/services/documentdb/). Les services web REST plusieurs résultats renvoyés au format texte JSON ou acceptent des données mises en forme en tant que JSON. Plus Azure services tels que [Recherche Azure](https://azure.microsoft.com/services/search/)et [Stockage Azure](https://azure.microsoft.com/services/storage/) [Azure DocumentDB](https://azure.microsoft.com/services/documentdb/) ont des extrémités reste qui retournent ou consomment JSON.

Base de données SQL Azure vous permet de travailler facilement avec des données JSON et intégrer votre base de données services modernes.

## <a name="overview"></a>Vue d’ensemble

Base de données SQL Azure fournit les fonctions suivantes pour travailler avec des données JSON :

![Fonctions JSON](./media/sql-database-json-features/image_1.png)

Si vous avez texte JSON, vous pouvez extraire les données à partir de JSON ou vérifier que JSON est correctement mis en forme à l’aide de fonctions intégrées [JSON_VALUE](https://msdn.microsoft.com/library/dn921898.aspx), [JSON_QUERY](https://msdn.microsoft.com/library/dn921884.aspx)et [ISJSON](https://msdn.microsoft.com/library/dn921896.aspx). La fonction [JSON_MODIFY](https://msdn.microsoft.com/library/dn921892.aspx) vous permet de mettre à jour la valeur à l’intérieur du texte JSON. Pour interroger et analyse plus avancées, fonction [OPENJSON](https://msdn.microsoft.com/library/dn921885.aspx) pouvez transformer un tableau d’objets JSON en un ensemble de lignes. Une requête SQL pouvant être exécutée sur jeu de résultats. Enfin, il existe une clause [FOR JSON](https://msdn.microsoft.com/library/dn921882.aspx) qui vous permet de mettre en forme des données stockées dans vos tables relationnelles en tant que texte JSON.

## <a name="formatting-relational-data-in-json-format"></a>Mise en forme de données relationnelles au format JSON
Si vous avez un service web que prend des données à partir de la base de données de la couche et fournissant une réponse dans JSON mettre en forme, ou structures JavaScript ou des bibliothèques acceptent des données côté client mis en forme en tant que JSON, vous pouvez mettre en forme le contenu de votre base de données en tant que JSON directement dans une requête SQL. Vous n’avez plus avez écrire du code d’application qui met en forme les résultats à partir de la base de données SQL Azure en tant que JSON ou pour incluez une bibliothèque de sérialisation JSON pour convertir les résultats de la requête sous forme de tableau puis sérialiser des objets au format JSON. À la place, vous pouvez utiliser la clause FOR JSON pour mettre en forme les résultats de la requête SQL en tant que JSON dans la base de données SQL Azure et utilisez-le directement dans votre application.

Dans l’exemple suivant, les lignes de la table Sales.Customer sont mis en forme en tant que JSON à l’aide de la clause JSON pour :

```
select CustomerName, PhoneNumber, FaxNumber
from Sales.Customers
FOR JSON PATH
```

La clause FOR JSON PATH met en forme les résultats de la requête en tant que texte JSON. Noms de colonnes sont utilisés comme clés, tandis que les valeurs de cellule sont générés en tant que valeurs JSON :

```
[
{"CustomerName":"Eric Torres","PhoneNumber":"(307) 555-0100","FaxNumber":"(307) 555-0101"},
{"CustomerName":"Cosmina Vlad","PhoneNumber":"(505) 555-0100","FaxNumber":"(505) 555-0101"},
{"CustomerName":"Bala Dixit","PhoneNumber":"(209) 555-0100","FaxNumber":"(209) 555-0101"}
]
```

Le jeu de résultats est mis en forme en tant que JSON tableau où chaque ligne est mis en forme comme un objet JSON distinct.

Chemin d’accès indique que vous pouvez personnaliser le format de sortie de votre résultat JSON en utilisant la notation point dans les alias de colonne. La requête suivante modifie le nom de la clé « Client » dans le format JSON et inclut les numéros de téléphone et de télécopie dans l’objet sous-adresse « Contact » :

```
select CustomerName as Name, PhoneNumber as [Contact.Phone], FaxNumber as [Contact.Fax]
from Sales.Customers
where CustomerID = 931
FOR JSON PATH, WITHOUT_ARRAY_WRAPPER
```

Le résultat de cette requête ressemble à ceci :

```
{
    "Name":"Nada Jovanovic",
    "Contact":{
           "Phone":"(215) 555-0100",
           "Fax":"(215) 555-0101"
    }
}
```

Dans cet exemple, nous renvoyé un seul objet JSON au lieu d’un tableau en spécifiant l’option [WITHOUT_ARRAY_WRAPPER](https://msdn.microsoft.com/library/mt631354.aspx) . Vous pouvez utiliser cette option si vous savez que vous retournez un objet unique à la suite de requête.

La valeur principale de la clause FOR JSON est qu’il vous permet de renvoyer des données hiérarchiques complexes à partir de votre base de données mis en forme en tant que les objets JSON imbriqués ou les tableaux. L’exemple suivant montre comment inclure les commandes qui appartiennent au client sous forme de matrice imbriquée de commandes :

```
select CustomerName as Name, PhoneNumber as Phone, FaxNumber as Fax,
        Orders.OrderID, Orders.OrderDate, Orders.ExpectedDeliveryDate
from Sales.Customers Customer
    join Sales.Orders Orders
        on Customer.CustomerID = Orders.CustomerID
where Customer.CustomerID = 931
FOR JSON AUTO, WITHOUT_ARRAY_WRAPPER

```

Au lieu d’envoyer séparer les requêtes pour obtenir des données client et puis pour extraire une liste des commandes connexes, vous pouvez obtenir toutes les données nécessaires à l’aide d’une requête unique, comme illustré dans l’exemple suivant :

```
{
  "Name":"Nada Jovanovic",
  "Phone":"(215) 555-0100",
  "Fax":"(215) 555-0101",
  "Orders":[
    {"OrderID":382,"OrderDate":"2013-01-07","ExpectedDeliveryDate":"2013-01-08"},
    {"OrderID":395,"OrderDate":"2013-01-07","ExpectedDeliveryDate":"2013-01-08"},
    {"OrderID":1657,"OrderDate":"2013-01-31","ExpectedDeliveryDate":"2013-02-01"}
]
}
```

## <a name="working-with-json-data"></a>Utilisation des données JSON

Si vous n’avez pas des données structurées strictement, si vous avez des objets sous-adresse complexes, des matrices ou des données hiérarchiques, ou si vos structures de données évoluer au fil du temps, le format JSON peut vous aider à représenter une structure de données complexes.

JSON est un format de texte qui peut être utilisé comme tout autre type de chaîne dans la base de données SQL Azure. Vous pouvez envoyer ou stocker des données JSON comme un NVARCHAR standard :

```
CREATE TABLE Products (
  Id int identity primary key,
  Title nvarchar(200),
  Data nvarchar(max)
)
go
CREATE PROCEDURE InsertProduct(@title nvarchar(200), @json nvarchar(max))
AS BEGIN
    insert into Products(Title, Data)
    values(@title, @json)
END
```

Les données JSON utilisées dans cet exemple sont représentées en utilisant le type de nvarchar (max). JSON pouvant être inséré dans ce tableau ou en tant qu’argument de la procédure stockée à l’aide de syntaxe Transact-SQL standard comme le montre l’exemple suivant :

```
EXEC InsertProduct 'Toy car', '{"Price":50,"Color":"White","tags":["toy","children","games"]}'
```

N’importe quel côté client langue ou la bibliothèque qui fonctionne avec les données de chaîne dans la base de données SQL Azure fonctionneront également avec des données JSON. JSON pouvant être stocké dans une table qui prend en charge le type NVARCHAR, par exemple une table de mémoire optimisée ou une version système. JSON ne présente pas une contrainte dans le code côté client ou dans la couche de base de données.

## <a name="querying-json-data"></a>Interroger des données JSON

Si vous avez des données mises en forme en tant que JSON stockée dans des tables SQL Azure, fonctions JSON vous permettent d’utiliser ces données dans une requête SQL.

Fonctions JSON disponibles dans vous permettent de base de données SQL Azure données sont-elles traitées mis en forme en tant que JSON comme tout autre type de données SQL. Vous pouvez facilement extraire les valeurs à partir du texte JSON et utiliser des données JSON dans une requête :

```
select Id, Title, JSON_VALUE(Data, '$.Color'), JSON_QUERY(Data, '$.tags')
from Products
where JSON_VALUE(Data, '$.Color') = 'White'

update Products
set Data = JSON_MODIFY(Data, '$.Price', 60)
where Id = 1
```

La fonction JSON_VALUE extrait une valeur texte JSON stocké dans la colonne de données. Cette fonction utilise un chemin d’accès par exemple JavaScript pour faire référence à une valeur en texte JSON pour extraire. La valeur extraite peut être utilisée dans une partie quelconque de la requête SQL.

La fonction JSON_QUERY est similaire à JSON_VALUE. Contrairement aux JSON_VALUE, cette fonction extrait objet sous-adresse complexe tels que des matrices ou des objets qui sont insérés dans un texte JSON.

La fonction JSON_MODIFY vous permet de spécifier le chemin d’accès de la valeur le texte JSON qui doit être mis à jour, ainsi qu’une nouvelle valeur qui remplace l’ancien. Ainsi, vous pouvez mettre à jour facilement les texte JSON sans nouvelle analyse en vue de l’ensemble de la structure.

Étant donné que JSON est stocké dans un texte standard, il n’existe aucune garantie que les valeurs stockées dans des colonnes de texte sont correctement mis en forme. Vous pouvez vérifier que texte stocké dans la colonne JSON est correctement mis en forme à l’aide des contraintes de vérification de base de données SQL Azure standards et la fonction ISJSON :

```
ALTER TABLE Products
    ADD CONSTRAINT [Data should be formatted as JSON]
        CHECK (ISJSON(Data) > 0)
```

Si le texte d’entrée est correctement mis en forme en JSON, la fonction ISJSON renvoie la valeur 1. Dans chaque insertion ou mise à jour de colonne JSON, cette contrainte vérifie que nouvelle valeur de texte n’est pas JSON incorrect.

## <a name="transforming-json-into-tabular-format"></a>Transformer JSON en format tabulaire

Base de données SQL Azure vous permet également de transformer collections JSON en tabulaires données JSON format et chargement ou la requête.

OPENJSON est une fonction de la valeur de la table qui analyse JSON texte, localise un tableau d’objets JSON, parcourt les éléments du tableau et retourne une seule ligne dans le résultat de sortie pour chaque élément du tableau.

![JSON tabulaire](./media/sql-database-json-features/image_2.png)

Dans l’exemple ci-dessus, nous pouvons préciser où vous souhaitez rechercher la matrice JSON qui doit être ouverts (dans le $. Chemin de commandes), les colonnes doivent être renvoyées comme résultat et l’endroit où trouver les valeurs JSON qui seront retournées sous forme de cellules.

Nous pouvons transformer un tableau JSON dans le @orders variable dans un jeu de lignes, analyser le jeu de résultats, ou insérer des lignes dans un tableau standard :

```
CREATE PROCEDURE InsertOrders(@orders nvarchar(max))
AS BEGIN

    insert into Orders(Number, Date, Customer, Quantity)
    select Number, Date, Customer, Quantity
    OPENJSON (@orders)
     WITH (
            Number varchar(200),
            Date datetime,
            Customer varchar(200),
            Quantity int
     )

END
```
La collection de commandes mis en forme comme un tableau JSON et fourni comme un paramètre à la procédure stockée peut être analysé et inséré dans la table Orders.

## <a name="next-steps"></a>Étapes suivantes

Pour savoir comment intégrer JSON dans votre application, consultez ces ressources :

- [TechNet Blog](https://blogs.technet.microsoft.com/dataplatforminsider/2016/01/05/json-in-sql-server-2016-part-1-of-4/)
- [Documentation MSDN](https://msdn.microsoft.com/library/dn921897.aspx)
- [Canal vidéo 9](https://channel9.msdn.com/Shows/Data-Exposed/SQL-Server-2016-and-JSON-Support)

Pour en savoir plus sur les différents scénarios d’intégration JSON dans votre application, voir les démonstrations de cette [vidéo Channel 9](https://channel9.msdn.com/Events/DataDriven/SQLServer2016/JSON-as-a-bridge-betwen-NoSQL-and-relational-worlds) ou encore trouver un scénario qui correspond à votre cas d’utilisation de [billets de Blog JSON](http://blogs.msdn.com/b/sqlserverstorageengine/archive/tags/json/).

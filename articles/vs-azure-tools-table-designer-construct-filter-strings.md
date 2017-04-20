<properties
   pageTitle="Construction des chaînes de filtrage pour le Concepteur de tables | Microsoft Azure"
   description="Construction des chaînes de filtrage pour le Concepteur de tables"
   services="visual-studio-online"
   documentationCenter="na"
   authors="TomArcher"
   manager="douge"
   editor="" />
<tags
   ms.service="storage"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/15/2016"
   ms.author="tarcher" />

# <a name="constructing-filter-strings-for-the-table-designer"></a>Construction des chaînes de filtrage pour le Concepteur de tables

## <a name="overview"></a>Vue d’ensemble

Pour filtrer des données dans une table Azure qui s’affiche dans le **Concepteur de tables**de Visual Studio, vous construisez une chaîne de filtre, entrez dans le champ de filtre. La syntaxe de la chaîne filtre défini par les Services de données WCF et est semblable à une clause WHERE SQL, mais est envoyée au service de Table via une demande HTTP. Le **Concepteur de tables** gère le codage approprié pour vous, donc pour filtrer sur une valeur de propriété souhaitée, il suffit de taper le nom de la propriété, opérateur de comparaison, valeur de critère et vous pouvez également booléen opérateur dans le champ de filtre. Vous n’avez pas besoin d’inclure l’option de requête $filter comme vous le feriez si vous ont été construire une URL pour interroger la table via la [Référence de l’API REST stockage Services](http://go.microsoft.com/fwlink/p/?LinkId=400447).

WCF Data Services sont basés sur le [Open Data Protocol](http://go.microsoft.com/fwlink/p/?LinkId=214805) (OData). Pour plus d’informations sur l’option de requête de filtre système (**$filter**), consultez la [spécification Conventions d’URI OData](http://go.microsoft.com/fwlink/p/?LinkId=214806).

## <a name="comparison-operators"></a>Opérateurs de comparaison

Les opérateurs logiques suivants sont pris en charge pour tous les types de propriété :

|Opérateurs logiques|Description|Exemple de chaîne de filtre|
|---|---|---|
|EQ|Égal|Ville eq « Redmond »|
|gt|Supérieur à|Prix gt 20|
|page|Supérieur ou égal à|Page de prix 10|
|lt|Inférieur à|Prix lt 20|
|le|Inférieur ou égal|Le prix 100|
|garanties|N’est pas égale|Ville garanties « Londres »|
|et|Et|Le prix 200 et prix gt 3.5|
|ou|Ou|Le prix 3.5 ou prix gt 200|
|pas|Pas|isAvailable pas|

Lors de la création d’une chaîne de filtrage, les règles suivantes sont importants :

- Utiliser les opérateurs logiques pour comparer une propriété à une valeur. Notez qu’il n’est pas possible de comparer une propriété avec une valeur dynamique ; une partie de l’expression doit être une constante.

- Toutes les parties de la chaîne de filtre respectent la casse.

- La valeur de la constante doit être du même type de données en tant que la propriété afin que le filtre pour renvoyer les résultats valides. Pour plus d’informations sur les types de propriété pris en charge, voir [Présentation du modèle de données de Service de Table](http://go.microsoft.com/fwlink/p/?LinkId=400448).

## <a name="filtering-on-string-properties"></a>Filtrage sur les propriétés de chaîne

Lorsque vous filtrez des propriétés de chaîne, placez la constante de chaîne entre des guillemets simples.

L’exemple ci-dessous filtre sur les propriétés **PartitionKey** et **RowKey** ; propriétés de clé non supplémentaires peuvent également être ajoutées à la chaîne de filtre :

    PartitionKey eq 'Partition1' and RowKey eq '00001'

Vous pouvez mettre chaque expression de filtre entre parenthèses, même s’il n’est pas nécessaire :

    (PartitionKey eq 'Partition1') and (RowKey eq '00001')

Notez que le service de tableau ne prend pas en charge génériques requêtes, et ils ne sont pas pris en charge dans le Concepteur de tables soit. Toutefois, vous pouvez effectuer préfixe correspondant à l’aide des opérateurs de comparaison sur le préfixe de votre choix. L’exemple suivant retourne entités avec un début de propriété nom avec la lettre « A » :

    LastName ge 'A' and LastName lt 'B'

## <a name="filtering-on-numeric-properties"></a>Filtrage sur les propriétés numériques

Pour filtrer sur un nombre entier ou un nombre à virgule flottante, indiquez le nombre sans guillemets.

Cet exemple renvoie toutes les entités avec une propriété Age dont la valeur est supérieure à 30 :

    Age gt 30

Cet exemple renvoie toutes les entités avec une propriété MontantDû dont la valeur est inférieure ou égale à 100.25 :

    AmountDue le 100.25

## <a name="filtering-on-boolean-properties"></a>Filtrage sur propriétés booléennes

Pour filtrer une valeur booléenne, spécifiez **true** ou **false** sans guillemets.

L’exemple suivant retourne toutes les entités dont la propriété IsActive est définie sur **true**:

    IsActive eq true

Vous pouvez également écrire cette expression de filtre sans l’opérateur logique. Dans l’exemple suivant, le service de tableau retournera également toutes les entités où IsActive est **vraie**:

    IsActive

Pour renvoyer toutes les entités où IsActive est faux, vous pouvez utiliser pas opérateur :

    not IsActive

## <a name="filtering-on-datetime-properties"></a>Filtrage sur les propriétés de date/heure

Pour filtrer une valeur DateTime, spécifiez le mot clé **datetime** , suivi par la constante de date/heure dans des guillemets simples. La constante de date/heure doit être au format UTC combiné, comme décrit dans [La mise en forme des valeurs de propriété DateTime](http://go.microsoft.com/fwlink/p/?LinkId=400449).

L’exemple suivant retourne entités où la propriété CustomerSince est égale à 10 juillet 2008 :

    CustomerSince eq datetime'2008-07-10T00:00:00Z'

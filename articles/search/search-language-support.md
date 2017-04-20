<properties
   pageTitle="Créer un index pour les documents dans plusieurs langues dans Azure recherche | Microsoft Azure | Service de recherche cloud hébergé"
   description=" Recherche Azure prend en charge 56 langues, exploiter les analyseurs langue à partir de la technologie Lucene et en langage naturel de traitement de Microsoft."
   services="search"
   documentationCenter=""
   authors="yahnoosh"
   manager="pablocas"
   editor=""/>

<tags
   ms.service="search"
   ms.devlang="na"
   ms.workload="search"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.date="07/14/2016"
   ms.author="jlembicz"/>

# <a name="create-an-index-for-documents-in-multiple-languages-in-azure-search"></a>Créer un index pour les documents dans plusieurs langues dans recherche Azure
> [AZURE.SELECTOR]
- [Portail](search-language-support.md)
- [RESTE](https://msdn.microsoft.com/library/azure/dn879793.aspx)
- [.NET](https://msdn.microsoft.com/library/azure/microsoft.azure.search.models.analyzername.aspx)

Exploiter toute la puissance d’analyseurs langue est aussi simple que la propriété paramètre sur un champ de recherche dans la définition d’index. Désormais, vous pouvez effectuer cette étape dans le portail.

Voici les captures d’écran des cartes Azure portail pour la recherche Azure qui permettent aux utilisateurs de définir un schéma d’index. À partir de cette carte, les utilisateurs peuvent créer tous les champs et définissez la propriété analyzer pour chacun d’eux.

> [AZURE.IMPORTANT] Vous ne pouvez définir un analyseur de langue lors de la définition de champ, comme dans lorsque vous créez un nouvel index notions fondamentales sur vers le haut, ou lorsque vous ajoutez un nouveau champ à un index existant. Vérifiez que vous spécifiez entièrement tous les attributs, y compris l’analyseur lors de la création du champ. Vous ne pourrez pas modifier les attributs ou modifier le type de l’analyseur une fois que vous enregistrez vos modifications.

## <a name="define-a-new-field-definition"></a>Définir une nouvelle définition de champ

1. Connectez-vous au [Portail Azure](https://portal.azure.com) et ouvrez la carte de service de votre service de recherche.
2. Cliquez sur **Ajouter l’index** dans la barre de commandes en haut du tableau de bord service pour démarrer un nouvel index, ou ouvrez un index existant pour définir un analyseur de nouveaux champs que vous ajoutez à un index existant.
3. La carte de champs s’affiche, vous donnant des options permettant de définir le schéma de l’index, y compris l’onglet Analyseur utilisés pour le choix d’un analyseur de langue.
4. Dans les champs, démarrez une définition de champ en fournissant un nom, en choisissant le type de données et définition des attributs pour marquer le champ en tant que texte intégral consultables, récupérables dans les résultats de recherche peuvent être utilisées par les structures de navigation facette, triable et ainsi de suite. 
5. Avant de passer au champ suivant, ouvrez l’onglet **Analyzer** . 

   
![][1]
*Pour sélectionner un analyseur, cliquez sur l’onglet Analyseur sur la carte de champs*

## <a name="choose-an-analyzer"></a>Choisissez un analyseur

6. Faites défiler pour rechercher le champ que vous définissez. 
7. Si vous n’avez pas coché le champ la recherche, cliquez sur la case à cocher maintenant pour marquer en tant que la **possibilité de recherche**.
8. Cliquez sur la zone Analyzer pour afficher la liste des analyseurs disponibles.
9. Cliquez sur l’analyseur que vous voulez utiliser.

![][2]
*Sélectionnez une des analyseurs pris en charge pour chaque champ*

Par défaut, tous les champs de recherche utilisent l' [Analyseur Lucene Standard](http://lucene.apache.org/core/4_10_0/analyzers-common/org/apache/lucene/analysis/standard/StandardAnalyzer.html) qui est indépendante du langage. Pour afficher la liste complète des analyseurs pris en charge, voir [Prise en charge des langues dans Azure rechercher](https://msdn.microsoft.com/library/azure/dn879793.aspx).

Une fois que l’Analyseur de langue est activée pour un champ, il est utilisée avec chaque demande d’indexation et de recherche de ce champ. Lorsqu’une requête est émise par rapport à plusieurs champs à l’aide de différents analyseurs, la requête sera traitée séparément par les analyseurs appropriés pour chaque champ.

De nombreux web et les applications mobiles partagés par les utilisateurs dans le monde à l’aide de différentes langues. Il est possible de définir un index pour un scénario à ceci en créant un champ pour chaque langue prise en charge.

![][3]
*Définition de l’index avec un champ de description pour chaque langue prise en charge*

Si la langue de l’agent émission d’une requête est connue, une demande de recherche peut être configurée pour un champ spécifique à l’aide du paramètre de requête **searchFields** . La requête suivante sera émise par rapport à la description en polonais :

`https://[service name].search.windows.net/indexes/[index name]/docs?search=darmowy&searchFields=description_pl&api-version=2015-02-28`

Vous pouvez interroger votre index à partir du portail, à l’aide de **l’Explorateur de recherche** pour le coller dans une requête similaire à celui illustré ci-dessus. Explorateur de recherche est disponible à partir de la barre de commandes dans la carte de service. Pour plus d’informations, voir [requête votre index de recherche Azure dans le portail](search-explorer.md) .

Parfois la langue de l’agent émission d’une requête n’est pas connue, auquel cas la requête peut être exécutée simultanément sur tous les champs. Si nécessaire, préférence pour les résultats dans une langue donnée peut être défini à l’aide de la [notation profils](https://msdn.microsoft.com/library/azure/dn798928.aspx). Dans l’exemple ci-dessous, correspondances trouvées dans la description en anglais auront un score supérieur par rapport à des correspondances en polonais et en Français :

    "scoringProfiles": [
      {
        "name": "englishFirst",
        "text": {
          "weights": { "description_en": 2 }
        }
      }
    ]

`https://[service name].search.windows.net/indexes/[index name]/docs?search=Microsoft&scoringProfile=englishFirst&api-version=2015-02-28`

Si vous êtes un développeur .NET, notez que vous pouvez configurer analyseurs langue à l’aide du [Kit de développement .NET Azure recherche](http://www.nuget.org/packages/Microsoft.Azure.Search). La dernière version prend en charge Microsoft langue chaque type d’analyseur également.

<!-- Image References -->
[1]: ./media/search-language-support/AnalyzerTab.png
[2]: ./media/search-language-support/SelectAnalyzer.png
[3]: ./media/search-language-support/IndexDefinition.png

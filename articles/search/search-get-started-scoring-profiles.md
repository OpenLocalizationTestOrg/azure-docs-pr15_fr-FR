<properties 
    pageTitle="Comment utiliser la notation des profils dans Azure recherche | Microsoft Azure | Service de recherche cloud hébergé" 
    description="Optimiser les classement par le biais de notation profils dans Azure rechercher, un service de recherche cloud hébergés sur Microsoft Azure de recherche." 
    services="search" 
    documentationCenter="" 
    authors="HeidiSteen" 
    manager="mblythe" 
    editor=""/>

<tags 
    ms.service="search" 
    ms.devlang="rest-api" 
    ms.workload="search" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.date="10/17/2016" 
    ms.author="heidist"/>

# <a name="how-to-use-scoring-profiles-in-azure-search"></a>Comment utiliser les profils score dans recherche Azure

Profils score sont une fonctionnalité de Microsoft Azure recherche que personnaliser le calcul de résultats de recherche, qui influencent le mode de classement des éléments dans une liste de résultats de recherche. Vous pouvez considérer score profils comme un moyen de pertinence de modèle, en augmentation des éléments répondant aux critères prédéfinis. Par exemple, supposons que votre application est un site de réservation hôtel en ligne. En augmentation de la `location` de champ, recherches qui incluent un terme comme Seattle créent des scores élevés pour les éléments ayant Seattle dans le `location` champ. Notez que vous pouvez avoir plusieurs profils score ou aucun tout, si le système d’évaluation par défaut est suffisant pour votre application.

Pour vous aider à tester les profils d’évaluation, vous pouvez télécharger un exemple d’application qui utilise des profils score pour modifier l’ordre de classement des résultats de recherche. L’exemple est une application console – ne sont peut-être pas très plausible pour le développement d’applications réelles – mais utile néanmoins comme outil d’apprentissage. 

L’exemple d’application illustre les comportements score à l’aide de données fictives, appelées le `musicstoreindex`. La simplicité de l’application exemple facilite la modifier score profils et requêtes, puis affichez les effets immédiates sur classement lors de l’exécution du programme.

<a id="sub-1"></a>
## <a name="prerequisites"></a>Conditions préalables

L’exemple d’application écrit en c# à l’aide de Visual Studio 2013. Essayez l' [édition Visual Studio 2013 Express](http://www.visualstudio.com/products/visual-studio-express-vs.aspx) gratuite si vous n’avez pas une copie de Visual Studio.

Vous avez besoin d’un abonnement Azure et un service de recherche Azure pour terminer le didacticiel. Pour obtenir de l’aide de la configuration du service, voir [créer un service de recherche dans le portail](search-create-service-portal.md) .

[AZURE. INCLURE [vous avez besoin d’un compte Azure pour effectuer ce didacticiel :](../../includes/free-trial-note.md)]

<a id="sub-2"></a>
## <a name="download-the-sample-application"></a>Télécharger l’exemple d’application

Accédez à [Azure recherche score profils démo](https://azuresearchscoringprofiles.codeplex.com/) sur codeplex pour télécharger l’application exemple décrite dans ce didacticiel.

Sous l’onglet de Code Source, cliquez sur **Télécharger** pour accéder à un fichier zip de la solution. 

 ![][12]

<a id="sub-3"></a>
## <a name="edit-appconfig"></a>Modifier app.config

1. Une fois que vous avez extrait les fichiers, ouvrez la solution dans Visual Studio afin de modifier le fichier de configuration.
1. Dans l’Explorateur de solutions, double-cliquez sur **app.config**. Ce fichier Spécifie le point de terminaison de service et un `api-key` utilisée pour authentifier la demande. Vous pouvez obtenir ces valeurs à partir du portail classique.
1. Connectez-vous au [portail Azure](https://portal.azure.com).
1. Accédez au tableau de bord de service de recherche Azure.
1. Cliquez sur la vignette de **Propriétés** pour copier l’URL du service
1. Cliquez sur la vignette de **touches** pour copier la `api-key`.

Lorsque vous avez terminé d’ajouter l’URL et `api-key` à app.config, les paramètres de l’application doivent ressembler à ceci :

   ![][11]


<a id="sub-4"></a>
## <a name="explore-the-application"></a>Explorer l’application

Vous êtes presque prêt à créer et exécuter l’application, mais avant de procéder, consultez les fichiers JSON utilisés pour créer et remplir l’index.

**Schema.JSON** définit l’index, y compris les profils de score sont mis en évidence au cours de cette démonstration. Notez que le schéma définit tous les champs utilisés dans l’index, y compris les champs non recherche, tel que `margin`, que vous pouvez utiliser dans un profil score. Notation de syntaxe profil présentée dans [Ajouter un profil score à un index de recherche Azure](http://msdn.microsoft.com/library/azure/dn798928.aspx).

**Données 1 3.json** fournit les données, 246 albums sur un petit nombre de styles. Les données sont une combinaison d’informations album et artistes réelles, avec des champs fictifs, par exemple `price` et `margin` utilisé pour illustrer les opérations de recherche. Les fichiers de données sont conformes à l’index et sont téléchargés vers votre service de recherche Azure. Une fois que les données sont téléchargées et indexées, vous pouvez émettre des requêtes.

**Program.cs** effectue les opérations suivantes :

- Ouvre une fenêtre de console.

- Se connecte à recherche Azure à l’aide de l’URL du service et `api-key`.

- Supprime la `musicstoreindex` le cas échéant.

- Crée un nouveau `musicstoreindex` en utilisant le fichier schema.json.

- Remplit l’index en utilisant les fichiers de données.

- L’index à l’aide de quatre requêtes des requêtes. Notez que les profils score sont spécifiées comme un paramètre de requête. Toutes les requêtes rechercher le même terme « mieux ». La première requête montre score par défaut. Les requêtes de trois restants utilisent un profil score.

<a id="sub-5"></a>
## <a name="build-and-run-the-application"></a>Créer et exécuter l’application

Pour éliminer de connectivité ou de problèmes de référence assembly, créer et exécuter l’application pour vous assurer qu’aucun problème pour travailler en premier. Vous devriez voir une application console ouvrir en arrière-plan. Toutes les quatre requêtes s’exécutent dans séquence sans interruption. Dans de nombreux systèmes, la totalité du programme s’exécute en sous 15 secondes. Si l’application console inclut un message indiquant « achevé. Appuyez sur ENTRÉE pour continuer », le programme terminée avec succès. 

Pour comparer requête s’exécute, vous pouvez marquer-copier-coller les résultats d’une requête à partir de la console et collez dans un fichier Excel. 

L’illustration suivante montre les résultats de la première trois requêtes côte à côte. Tous les requêtes utilisent le même terme de recherche, mieux, qui apparaît dans plusieurs titres album.

   ![][10]

La première requête utilise la notation par défaut. Dans la mesure où le terme de recherche s’affiche uniquement dans les titres de l’album, et aucun autre critère n’est spécifié, les éléments ayant « mieux » dans le titre de l’album sont retournés dans l’ordre dans lequel le service de recherche trouve les. 

La deuxième requête utilise un profil d’évaluation, mais vous constatez que le profil n’a aucun effet. Les résultats sont identiques à ceux de la première requête. C’est parce que le profil score renforce un champ ('genre') qui n’est pas pertinentes pour la requête. Le terme de recherche « meilleures » n’existent pas dans n’importe quel champ 'genre' de n’importe quel document. Lorsqu’un profil score n’a aucun effet, les résultats sont les mêmes comme score par défaut.  

La troisième requête est la première preuve de notation impact de profil. Le terme de recherche est toujours meilleur afin que nous travaillons avec le même jeu d’albums, mais parce que le profil score fournit des critères supplémentaires qui augmente « indice » et « dernière mise à jour », certains éléments sont faits supérieur dans la liste.

L’illustration suivante montre la requête quatrième et dernière augmentée par « marge ». Le champ « marge » est non interrogeables et ne peut pas être retourné dans les résultats de recherche. La valeur « marge » a été ajoutée manuellement à la feuille de calcul afin d’illustrer le point éléments par rapport aux marges supérieures apparaissant plus hauts dans la liste des résultats. 

   ![][9]

À présent que vous avez terminé les exercices score profils, essayez de modifier le programme à utiliser la syntaxe de requête différente, notation des profils ou données plus riches. Liens dans la section suivante fournissent des informations supplémentaires.

<a id="next-steps"></a>
## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur les profils de score. Pour plus d’informations, voir [Ajouter un profil score à un index de recherche Azure](http://msdn.microsoft.com/library/azure/dn798928.aspx) .

En savoir plus sur les paramètres de requête et de syntaxe de la recherche. Pour plus d’informations, voir [Rechercher des Documents (API REST de recherche Azure)](http://msdn.microsoft.com/library/azure/dn798927.aspx) .

Si vous souhaitez revenir en arrière et en savoir plus sur la création d’index ? [Regardez cette vidéo](http://channel9.msdn.com/Shows/Cloud+Cover/Cloud-Cover-152-Azure-Search-with-Liam-Cavanagh) pour comprendre les concepts de base.

<!--Anchors-->
[Prerequisites]: #sub-1
[Download the sample application]: #sub-2
[Edit app.config]: #sub-3
[Explore the application]: #sub-4
[Build and run the application]: #sub-5
[Next steps]: #next-steps

<!--Image references-->
[12]: ./media/search-get-started-scoring-profiles/AzureSearch_CodeplexDownload.PNG
[11]: ./media/search-get-started-scoring-profiles/AzureSearch_Scoring_AppConfig.PNG
[10]: ./media/search-get-started-scoring-profiles/AzureSearch_XLSX1.PNG
[9]: ./media/search-get-started-scoring-profiles/AzureSearch_XLSX2.PNG 
<properties
    pageTitle="Importer des données à la recherche Azure à l’aide d’indexeurs dans le portail Azure | Microsoft Azure | Service de recherche cloud hébergé"
    description="Utiliser Azure recherche Assistant Importer les données dans le portail Azure à des données d’analyse à partir de stockage, stockage de table, base de données SQL et SQL Server sur machines virtuelles Azure Blob Azure."
    services="search"
    documentationCenter=""
    authors="HeidiSteen"
    manager="jhubbard"
    editor=""
    tags="Azure Portal"/>

<tags
    ms.service="search"
    ms.devlang="na"
    ms.workload="search"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="na"
    ms.date="08/29/2016"
    ms.author="heidist"/>

# <a name="import-data-to-azure-search-using-the-portal"></a>Importer des données à la recherche Azure à l’aide du portail

Le portail Azure fournit un Assistant **Importer des données** dans le tableau de bord Azure recherche pour charger des données dans un index. 

  ![Importer des données dans la barre de commandes][1]

En interne, l’Assistant configure et appelle un *indexeur*, automatisation de plusieurs étapes du processus d’indexation : 

- Se connecter à une source de données externes dans l’abonnement Azure actif
- Génère automatiquement un schéma d’index en fonction de la structure des données source
- Créer des documents basés sur un jeu de lignes récupérée à partir de la source de données
- Télécharger des documents dans l’index dans votre service de recherche

Vous pouvez essayer ce flux de travail à l’aide des exemples de données dans DocumentDB. Pour plus d’informations, consultez [prise en main avec la recherche dans le portail Azure Azure](search-get-started-portal.md) .

## <a name="data-sources-supported-by-the-import-data-wizard"></a>Sources de données prises en charge par l’Assistant Importation de données

L’Assistant Importation de données prend en charge les sources de données suivantes : 

- Base de données SQL Azure
- Données relationnelles SQL Server sur un ordinateur virtuel Azure
- DocumentDB Azure
- Stockage d’objets Blob Azure (en preview)
- Azure Table stockage (preview)

Un dataset plat est une entrée requise. Vous pouvez uniquement importer à partir d’une seule table, une vue de base de données ou une structure de données équivalent. Vous devez créer cette structure de données avant d’exécuter l’Assistant.

Notez que certains des indexeurs sont toujours dans l’aperçu, ce qui signifie que la définition indexeur est sauvegardée par la version de l’API. Pour plus d’informations et des liens, voir [vue d’ensemble indexeur](search-indexer-overview.md) .

## <a name="connect-to-your-data"></a>Se connecter à vos données

1. Connectez-vous au [Portail Azure](https://portal.azure.com) et tableau de bord de service en cours. Vous pouvez cliquer sur **services de recherche** dans la barre de raccourcis pour afficher les services existants dans l’abonnement actif. 

2. Cliquez sur **Importer des données** dans la barre de commandes à la diapositive ouvrir la carte importer des données.  

3. Cliquez sur **se connecter à vos données** pour spécifier une définition de source de données utilisée par un indexeur. Pour les sources de données interne abonnement, l’Assistant puisse généralement détecter et lire les informations de connexion en réduisant générale configuration requise.

| | |
|--------|------------|
|**Source de données existante** | Si vous avez déjà indexeurs définis dans votre service de recherche, vous pouvez sélectionner une définition de source de données existante pour importer un autre.|
|**Base de données SQL Azure** | Nom du service, informations d’identification d’un utilisateur de base de données avec l’autorisation de lecture et un nom de base de données peuvent être indiquée dans la page ou via une chaîne de connexion ADO.NET. Choisissez l’option de chaîne de connexion pour afficher ou personnaliser des propriétés. <br/><br/>La table ou la vue qui fournit l’ensemble de lignes doit être indiquée dans la page. Cette option n’apparaît une fois la connexion établie, donnant une liste déroulante de sorte que vous pouvez effectuer une sélection.|
|**SQL Server sur machine virtuelle Azure** | Spécifiez un nom complet de service, ID d’utilisateur et mot de passe et base de données sous forme d’une chaîne de connexion. Pour utiliser cette source de données, vous devez avoir précédemment installé un certificat dans le magasin local qui chiffre la connexion. <br/><br/>La table ou la vue qui fournit l’ensemble de lignes doit être indiquée dans la page. Cette option n’apparaît une fois la connexion établie, donnant une liste déroulante de sorte que vous pouvez effectuer une sélection.
|**DocumentDB** |Configuration requise pour inclure le compte, la base de données et la collection de sites. Tous les documents dans la collection de sites sont inclus dans l’index. Vous pouvez définir une requête pour uniformiser ou filtrer le jeu de lignes, ou pour détecter les documents modifiés pour les opérations d’actualisation des données suivantes.|
|**Stockage d’objets Blob Azure** | Configuration requise pour inclure le compte de stockage et un conteneur. Vous pouvez également si les noms des objets blob respectent une convention d’appellation virtuelle à des fins de regroupement, vous pouvez spécifier la partie répertoire virtuel du nom en tant que dossier sous conteneur. Pour plus d’informations, consultez [L’indexation de stockage d’objets Blob (preview)](search-howto-indexing-azure-blob-storage.md) . |
|**Stockage de Table Azure** | Configuration requise pour inclure le compte de stockage et un nom de table. Si vous le souhaitez, vous pouvez spécifier une requête pour récupérer un sous-ensemble des tables. Pour plus d’informations, consultez [L’indexation de stockage de Table (preview)](search-howto-indexing-azure-tables.md) . |

## <a name="customize-target-index"></a>Personnaliser les index cible

Un index préliminaire est généralement déduit du jeu de données. Ajouter, modifier ou supprimer des champs pour terminer le schéma. En outre, définir les attributs au niveau des champs pour déterminer le comportement de recherche ultérieure.

1. **Index cible personnaliser**, spécifiez le nom et une **clé** utilisée pour identifier de façon unique chaque document. La clé doit être une chaîne. Si les valeurs de champ incluent des espaces ou des tirets veillez à définir des options avancées dans **importer vos données** pour supprimer le contrôle de validation pour ces caractères.

2. Passez en revue et corrigez les champs restants. Type et le nom de champ sont généralement renseignées pour vous. Vous pouvez modifier le type de données.

3. Définir les attributs d’index pour chaque champ :

 - Récupérables retourne le champ dans les résultats de recherche.
 - Filtrables permet le champ à être référencées dans les expressions de filtre.
 - Triable permet le champ à utiliser dans un tri.
 - Facetable Active le champ pour la navigation par facettes.
 - Possibilité de recherche permet de recherche en texte intégral.
  
4. Cliquez sur **l’analyseur** si vous souhaitez spécifier un analyseur de langue au niveau des champs. Uniquement les analyseurs langue peuvent être spécifiés pour le moment. À l’aide d’un analyseur personnalisé ou un analyseur de langue autre que comme mot clé, motif et ainsi de suite, nécessitent code.

 - Cliquez sur la **possibilité de recherche** pour désigner recherche en texte intégral sur le champ et activer la liste déroulante Analyzer.
 - Cliquez sur l’analyseur souhaité. Pour plus d’informations, voir [créer un index pour les documents dans plusieurs langues](search-language-support.md) .

5. Cliquez sur **Suggester** pour activer les suggestions de requête semi-automatique sur les champs sélectionnés.


## <a name="import-your-data"></a>Importer vos données

1. Dans **importer vos données**, indiquez un nom pour l’indexeur. Rappelez-vous que le produit de l’Assistant Importation de données est un indexeur. Plus tard, si vous souhaitez afficher ou le modifier, vous pouvez le sélectionner à partir du portail plutôt qu’en relançant l’Assistant. 

2. Spécifiez la planification, qui est basée sur le fuseau horaire régionaux dans lequel le service est configuré.

3. Définir des options avancées pour spécifier les seuils sur l’indexation si puisse continuer si un document est abandonné. En outre, vous pouvez spécifier si les champs de **clé** sont autorisés à contenir des espaces et barres obliques.  

## <a name="edit-an-existing-indexer"></a>Modifier un indexeur existant

Dans le tableau de bord de service, double-cliquez sur la vignette indexeur à la diapositive une liste de tous les indexeurs créé pour votre abonnement. Double-cliquez sur une des indexeurs pour exécuter, modifier ou supprimer. Vous pouvez remplacer l’index existant par un autre, modifier la source de données et définir les options de seuils d’erreur lors de l’indexation.

## <a name="edit-an-existing-index"></a>Modifier un index existant

Dans Rechercher Azure, structurelles mises à jour un index nécessite une reconstruction de cet index, qui se compose de suppression de l’index, recréer l’index et recharger les données. Mises à jour structurels incluent la modification d’un type de données et renommer ou supprimer un champ.

Modifications qui ne nécessitent une reconstruction comprennent l’ajout d’un nouveau champ, modification d’un profil, de modification suggesters, ou la modification analyseurs langage score. Pour plus d’informations, consultez [Index de mise à jour](https://msdn.microsoft.com/library/azure/dn800964.aspx) .

## <a name="next-step"></a>Étape suivante

Passez en revue ces liens pour en savoir plus sur les indexeurs :

- [L’indexation de la base de données SQL Azure](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers-2015-02-28.md)
- [L’indexation DocumentDB](../documentdb/documentdb-search-indexer.md)
- [Stockage d’objets Blob indexation (preview)](search-howto-indexing-azure-blob-storage.md)
- [Stockage de tables d’indexation (preview)](search-howto-indexing-azure-tables.md)



<!--Image references-->
[1]: ./media/search-import-data-portal/search-import-data-command.png


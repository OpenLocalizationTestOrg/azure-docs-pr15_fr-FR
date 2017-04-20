<properties
   pageTitle="Catalogue de données Azure Forum aux questions | Microsoft Azure"
   description="Questions fréquemment posées sur catalogue de données Azure, y compris les fonctionnalités de découverte de source de données, les annotations et gestion."
   services="data-catalog"
   documentationCenter=""
   authors="steelanddata"
   manager="NA"
   editor=""
   tags=""/>
<tags
   ms.service="data-catalog"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-catalog"
   ms.date="10/04/2016"
   ms.author="maroche"/>

# <a name="azure-data-catalog-frequently-asked-questions"></a>Catalogue de données Azure Forum aux questions

Cet article fournit des réponses pour les questions fréquemment posées relatives au service Microsoft **Catalogue de données Azure** .

## <a name="q-what-is-azure-data-catalog"></a>Q : quelle est le catalogue de données Azure ?

R : catalogue de données Microsoft Azure est un service entièrement géré hébergé dans le cloud Microsoft Azure qui sert à un système d’inscription et système de recherche pour les sources de données d’entreprise. Catalogue de données Azure fournit des fonctionnalités qui permettent à tout utilisateur – analystes aux scientifiques des données pour les développeurs – pour enregistrer, découvrir, comprennent et utiliser les sources de données.

## <a name="q-what-customer-challenges-does-azure-data-catalog-solve"></a>Q : quelle client défis signifie catalogue de données Azure résoudre ?

Catalogue de données Azure corrige le défi de découverte de source de données et des « données foncées » en permettant aux utilisateurs de découvrir et de comprendre les sources de données d’entreprise.

## <a name="q-who-are-the-target-audiences-for-azure-data-catalog"></a>Q : quels sont les audiences ciblées pour le catalogue de données Azure ?

Catalogue de données Azure fournit les fonctionnalités pour les utilisateurs techniques et non techniques, y compris :

- Les développeurs de données, BI et professionnels Analytique : qui sont responsables de production de données et analytique du contenu aux autres pour consommer
-   Gestionnaires de données : Les utilisateurs disposant de la base de connaissances concernant les données, signification et comment il est destiné à être utilisé et leur usage
- Consommateurs de données : Les personnes qui doivent pouvoir facilement découvrir, comprendre et se connecter aux données nécessaires pour accomplir leur travail à l’aide de l’outil de votre choix
- Central informatique : ceux qui ont besoin de passer des centaines de sources de données détectable pour les utilisateurs professionnels et qui ont besoin de mettre à jour une vue d’ensemble sur l’utilisation de données et l’auteur

## <a name="q-what-is-the-azure-data-catalog-region-availability"></a>Q : quelle est la disponibilité de la région du catalogue de données Azure ?

Services de catalogue de données Azure sont actuellement disponibles dans les centres de données suivants :

- États-Unis Ouest
- États-Unis Extrême-Orient
- Europe occidentale
- Europe du Nord
- Australie Extrême-Orient
- Pays d’Asie du Sud-est

## <a name="q-what-are-the-limits-on-the-number-of-data-assets-in-azure-data-catalog"></a>Q : quelles sont les limites du nombre d’éléments de données dans le catalogue de données Azure ?

Le gratuit édition d’Azure catalogue de données est limité aux éléments de données enregistré 5 000.

Le Standard Edition de Azure catalogue de données prend en charge jusqu'à 100 000 éléments de données enregistré.

## <a name="q-what-are-the-supported-data-source-and-asset-types"></a>Q : quels sont les types de biens et de la source de données pris en charge ?

Reportez-vous au [DSR de catalogue de données](data-catalog-dsr.md) pour la liste des sources de données prises en charge actuellement.

## <a name="q-how-do-i-request-support-for-another-data-source"></a>Q : comment demander la prise en charge pour une autre source de données ?

Demandes et les commentaires d’autres peuvent être soumises dans le [catalogue de données Azure forum](http://go.microsoft.com/fwlink/?LinkID=616424&clcid=0x409).

## <a name="q-how-do-i-get-started-with-azure-data-catalog"></a>Q : Comment puis-je commencer avec le catalogue de données Azure ?

Il est l’endroit idéal pour commencer en suivant les instructions de [Prise en main catalogue de données](data-catalog-get-started.md). Cet article est une vue d’ensemble de bout en bout des fonctionnalités dans le service.

## <a name="q-how-do-i-register-my-data"></a>Q : Comment puis-je m’inscrire mes données ?

Pour enregistrer vos données dans le catalogue de données Azure, lancez l’outil d’inscription catalogue de données Azure à partir de la zone « Publier » du portail du catalogue de données Azure. Dans l’application de publication du catalogue de données Azure, connectez-vous en utilisant les mêmes informations d’identification que vous utilisez pour accéder au portail de catalogue de données Azure, puis la source de données et les biens spécifiques que vous souhaitez enregistrer.

## <a name="q-what-properties-are-extracted-for-data-assets-that-are-registered"></a>Q : quelles propriétés sont extraites pour les ressources de données sont enregistrées ?

Les propriétés spécifiques seront différent de source de données à la source de données, mais en règle générale le service de publication du catalogue de données Azure extrait les informations suivantes :

- Nom de la ressource
- Type d’élément
- Description de la ressource
- Noms de colonne/attribut
- Types de données attribut ou de colonne
- Description de l’attribut ou de colonne

> [AZURE.IMPORTANT] Enregistrement des données actifs avec le catalogue de données Azure ne soit déplacé ou copier les données dans le cloud. Enregistrement des actifs à partir d’une source de données copie les métadonnées des biens sur Azure, mais les données sont conservées dans l’emplacement de source de données existante. La seule exception à cette règle est si un utilisateur choisit de télécharger des enregistrements preview ou un profil de données lors de l’inscription des biens. Lorsque y compris un aperçu, jusqu'à 20 enregistrements sont copiées dans chaque ressource et sont stockés en tant qu’instantané dans le catalogue de données Azure. Lorsque vous incluez un profil de données, agrégation informations (par exemple, la taille des tables, les valeurs null en pourcentage par colonne et les valeurs minimum, maximum et moyennes pour les colonnes) sont calculées et incluses dans les métadonnées stockées dans le catalogue.

<br/>

> [AZURE.NOTE] Pour les sources de données telles que SQL Server Analysis Services qui ont une propriété **Description** première classe, le catalogue de données Azure publication d’application extrait cette valeur de propriété. Pour SQL Server bases de données relationnelles, qui ne disposent pas d’une propriété **Description** première classe, l’application de publication du catalogue de données Azure extrait la valeur à partir de la ms_description propriété des objets et des colonnes étendue. Pour plus d’informations, voir TechNet [à l’aide des propriétés étendues sur les objets de base de données](https://technet.microsoft.com/library/ms190243%28v=sql.105%29.aspx).

## <a name="q-how-long-should-it-take-for-newly-registered-assets-to-appear-in-azure-data-catalog"></a>Q : combien de temps doit prendre pour immobilisations récemment enregistrées doivent apparaître dans le catalogue de données Azure ?

Une fois enregistré actifs avec le catalogue de données Azure il peut y avoir une période de 5 à 10 secondes avant qu’ils apparaissent dans le portail du catalogue de données Azure.

## <a name="q-how-do-i-annotate-and-enrich-the-metadata-for-my-registered-data-assets"></a>Q : comment annoter et enrichir les métadonnées pour mes éléments de données enregistré ?

Le moyen le plus simple pour fournir des métadonnées pour les actifs inscrits consiste à sélectionner l’actif dans le portail du catalogue de données Azure, puis entrez les valeurs de métadonnées dans le volet de propriétés ou schéma pour l’objet sélectionné.

Vous pouvez également fournir des métadonnées, telles que des experts et indicateurs, pendant le processus d’inscription. Les valeurs fournies dans le service de publication du catalogue de données Azure seront appliquent à tous les éléments en cours d’inscription à ce moment. Pour afficher les objets récemment enregistrée dans le portail d’annotation supplémentaire, sélectionnez le bouton **Mode Portal** sur l’écran final de l’application de publication du catalogue de données Azure.

## <a name="q-how-do-i-delete-my-registered-data-objects"></a>Q : comment supprimer mes objets de données enregistré ?

Vous pouvez supprimer un objet de catalogue de données Azure en sélectionnant l’objet dans le portail, puis en cliquant sur le bouton **Supprimer** . Cela supprime les métadonnées de l’objet à partir du catalogue de données Azure mais n’affecte pas la source de données sous-jacente réel.

## <a name="q-what-is-an-expert"></a>Q : quelle est un expert ?

Un expert est une personne qui a une perspective informée sur un objet de données. Un objet peut avoir plusieurs experts. Un expert n’a pas besoin être le « propriétaire » pour un objet ; l’utilisateur expérimenté est simplement une personne qui sait comment les données et doit être utilisé.

## <a name="q-how-do-i-share-information-with-the-azure-data-catalog-team-if-i-encounter-problems"></a>Q : comment partager des informations avec l’équipe du catalogue de données Azure si je rencontre des problèmes ?

Utiliser le catalogue de données Azure le forum pour signaler des problèmes, partager des informations et poser des questions. Vous pouvez trouver le forum en http://go.microsoft.com/fwlink/?LinkID=616424&clcid=0x409

##<a name="q-does-azure-data-catalog-work-with-this-other-data-source-im-interested-in"></a>Q : catalogue de données Azure fonctionne-t-il avec cet autre source de données que je suis intéressé ?
Nous travaillons activement sur l’ajout de plusieurs sources de données au catalogue de données Azure. S’il existe une source de données que vous voulez voir prise en charge, veuillez suggère (ou votre prise en charge de la voix si elle a déjà été suggéré) dans le [catalogue de données Azure forum](http://go.microsoft.com/fwlink/?LinkID=616424&clcid=0x409).

## <a name="q-how-is-azure-data-catalog-related-to-the-data-catalog-in-power-bi-for-office-365"></a>Q : comment catalogue de données Azure est lié au catalogue de données dans Power BI pour Office 365 ?

Vous pouvez considérer catalogue de données Azure comme une évolution du catalogue de données. Catalogue de données Azure offre des fonctionnalités similaires pour la publication de source de données et découverte, mais il est activé sur des scénarios plus larges et ne dépendent pas Office 365. Peu de temps après que le catalogue de données Azure devient disponible généralement les deux catalogues fusionne dans un seul service.

## <a name="q-what-permissions-does-a-user-need-to-register-assets-with-azure-data-catalog"></a>Q : quelles sont les autorisations utilisateur a-t-il besoin pour inscrire actifs avec le catalogue de données Azure ?

L’utilisateur qui exécute l’outil d’inscription catalogue de données Azure a besoin d’autorisations sur la source de données qui permet de lui permettre de lire les métadonnées de la source. Si l’utilisateur sélectionne également inclure un aperçu, l’utilisateur doit également disposer des autorisations qui lui permettent de lire dans les données à partir des objets en cours d’inscription.

## <a name="q-will-azure-data-catalog-be-made-available-for-on-premises-deployment-as-well"></a>Q : catalogue de données Azure sera-t-il disponible pour le déploiement local également ?

Catalogue de données Azure est un service cloud qui peut utiliser des sources de données cloud et en local, exécution d’une solution de découverte de source de données hybride. Il n’existe actuellement aucune offre pour une version du service de catalogue de données Azure qui s’exécute en local.

##<a name="q-can-we-extract-more--richer-metadata-from-the-data-sources-we-register"></a>Q : pouvons-nous extraire les métadonnées plus / plus riche des sources de données que nous inscrire ?

Nous travaillons activement pour développer les fonctionnalités de catalogue de données Azure. S’il existe des métadonnées supplémentaires que vous voulez voir extraite à partir de la source de données lors de l’inscription, veuillez suggérer (ou que vote pour qu’elle si elle a déjà été suggéré) dans le [catalogue de données Azure forum](http://go.microsoft.com/fwlink/?LinkID=616424&clcid=0x409). Nous permettra à l’avenir tiers pour ajouter de nouveaux types de source de données via une API d’extensibilité.

## <a name="q-how-do-i-restrict-the-visibility-of-registered-data-assets-so-that-only-certain-people-can-discover-them"></a>Q : comment limiter la visibilité des éléments de données enregistré, afin que seules certaines personnes puissent découvrir les ?

R : sélectionnez les éléments de données dans le catalogue de données Azure, puis cliquez sur le bouton « Prendre possession ». Propriétaires des ressources de données dans le catalogue de données Azure peuvent modifier les paramètres de visibilité pour autoriser tous les utilisateurs de catalogue pour découvrir les biens propriétaire, ou pour limiter la visibilité à des utilisateurs spécifiques.

## <a name="q-how-do-i-update-the-registration-for-a-data-asset-to-that-changes-in-the-data-source-are-reflected-in-the-catalog"></a>Q : comment mettre à jour l’enregistrement pour un élément de données pour les modifications dans la source de données sont reflétées dans le catalogue ?

R : pour mettre à jour les métadonnées pour les ressources de données qui sont déjà enregistrés dans le catalogue, il vous suffit réenregistrer la source de données qui contient les biens. Les modifications apportées à la source de données, telles que les colonnes soient ajoutés ou supprimés à partir de tables ou vues, seront mise à jour dans le catalogue, mais les annotations fournies par les utilisateurs seront conservées.

## <a name="q-my-question-isnt-answered-here--what-should-i-do"></a>Q : ma question n’est pas une réponse ici : que dois-je faire ?

Accédez au [catalogue de données Azure forum](http://go.microsoft.com/fwlink/?LinkID=616424&clcid=0x409). Toutes les questions posées il recherche dirigés ici.

<properties
   pageTitle="Conditions préalables techniques pour la création d’un Service de données pour le marché | Microsoft Azure"
   description="Comprendre la configuration requise pour la création d’un Service de données pour déployer et vendre sur Azure Marketplace"
   services="marketplace-publishing"
   documentationCenter=""
   authors="HannibalSII"
   manager="hascipio"
   editor=""/>

<tags
   ms.service="marketplace"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/26/2016"
   ms.author="hascipio; avikova" />

# <a name="technical-pre-requisites-for-creating-a-data-service-offer-for-the-azure-marketplace"></a>Conditions préalables techniques pour la création d’un Service de données offrent de Azure Marketplace

>[AZURE.IMPORTANT] **Pour le moment nous ne sont plus arrivant les nouvelles publications de Service de données. Nouvelle dataservices ne seront pas obtenir approuvées pour la liste.** Si vous avez une application métier SaaS que vous voulez publier sur AppSource vous trouverez plus d’informations [ici](https://appsource.microsoft.com/partners). Si vous avez un applications IaaS ou service développeur que vous voulez publier sur Azure Marketplace, vous pouvez en savoir plus [ici](https://azure.microsoft.com/marketplace/programs/certified/).

Lire le processus de manière approfondie avant de commencer et à comprendre où et pourquoi chaque étape est effectuée. Autant que possible, vous devez préparer les informations de votre société et d’autres données, téléchargez les outils nécessaires, et/ou créer composants techniques avant de commencer le processus de création d’une offre.

Vous devez disposer des éléments suivants prêts avant de commencer le processus :

## <a name="make-a-decision-on-what-technology-will-be-used-to-publish-your-data-service-offer"></a>Prendre une décision sur quelle technologie sera utilisée pour publier votre offre de Service de données

Un éditeur permet de choisir entre plusieurs technologies lors de la publication d’un Service de données dans Azure Marketplace. Les principales technologies prises en charge décrites ci-dessous. Quel que soit l’utilisateur final quelle technologie est utilisée pour publier le Service de données, consomme des données par le biais du **flux OData** exposés par Azure Marketplace Service. Informations complètes sur OData service que vous pouvez trouver sur [http://www.odata.org/](http://www.odata.org/)

## <a name="sql-azure-database"></a>SQL Azure base de données

Jeu de données prêt dans SQL Azure est responsabilité de Publisher. Vous devez vous abonner aux Azure, mise en service de taille appropriée de base de données et télécharger vos données dans une base de données SQL Azure. Publisher est également chargé pour maintenir ses données toujours à jour. Plus d’informations sur l’abonnement aux Services Azure que vous pouvez trouver sur [https://azure.microsoft.com/services/sql-database/](https://azure.microsoft.com/services/sql-database/)


Lorsque vous déplacez les données dans SQL Azure, le Azure Marketplace peut exposer des tables et des vues. L’éditeur peut spécifier les tables/vues et les colonnes sont présentés à l’utilisateur final. Le fournisseur de contenu peut également affiner les colonnes qui peuvent être interrogées par l’utilisateur final et quels sont ceux qui est uniquement renvoyées dans la charge utile. Cela vous donne un niveau élevé de flexibilité sur les données dans la base de données doivent être exposées. Colonnes qui peuvent être interrogées doivent être sauvegardées par un ou plusieurs indices de base de données.

## <a name="rest-based-web-service"></a>Service web REST en fonction

Protocole pris en charge : **HTTPS uniquement**

Services reste en fonction existants peuvent être exposés par le biais du Azure Marketplace. Étant donné que le jeu de données est toujours exposé à l’utilisateur final comme un flux OData, le Azure Marketplace service doit être en mesure d’établir une correspondance entre le service un OData en fonction de service. Pour ce faire le reste en fonction de points de terminaison devez exposer tous les paramètres en tant que paramètres HTTP.

La charge utile doit se trouver dans un formulaire qui peut être mappé dans une réponse ATOM. Par conséquent, la réponse de le services doit être en format XML et ne peut contenir un élément extensible qui contient les valeurs de charge utile (par exemple, le jeu d’enregistrements). Le service Azure Marketplace est mappé le nœud extensible avec le nœud d’entrée dans ATOM et les valeurs de la charge utile dans les nœuds des propriétés dans le nœud d’entrée.

Informations d’autorisation (par exemple, l’API clés, l’authentification jeton, etc.) doivent être fourni sous forme d’un paramètre HTTP ou dans l’en-tête HTTP (paire de valeur de clé) – authentification de base est également prise en charge. Une clé valide doit être fourni et toutes les requêtes via Azure Marketplace effectuées par le biais cette touche. Utilisateur de facturation et de contrôle se produit au niveau de la couche Azure Marketplace.

Erreurs renvoyées par le service doivent être mappées dans les codes d’état HTTP. Dans le cas où le service renvoie un fichier XML qui contient l’erreur ces vont être mappée par le service Azure Marketplace aux codes d’état HTTP.

## <a name="soap-based-web-services"></a>Services web SOAP en fonction

Protocole : **HTTPS uniquement**

La configuration requise est qu'identique à celle du reste en fonction de section service. La seule différence est que paramètres peuvent également être fournis dans le corps du XML publié au service de l’éditeur avec chaque demande faite via Azure Marketplace. Cela signifie que les paramètres HTTP que fournit de l’utilisateur au serveur frontal sont être convertis dans des éléments XML d’un document XML publié avec la demande de service web du fournisseur de contenu.

## <a name="odata-based-web-services"></a>OData en fonction des services web

Protocole : **HTTPS uniquement**

Données peuvent être exposées comme un service OData à Azure Marketplace. Le système va passer le service via et remplace la racine du service par la racine de service Azure Marketplace – afin de tous les appels ultérieurs traitée Azure Marketplace.

Les services OData ne suffit accéder à une base de données dans le système principal. OData prend en charge tous les types de stockage ou entreprise logique conduire le service.


## <a name="next-steps"></a>Étapes suivantes
À présent que vous examiné les conditions préalables et accompli les tâches nécessaires, vous pouvez avancer avec la création de votre offre de Service de données comme indiqué dans le [Guide de publication de Service de données](marketplace-publishing-data-service-creation.md).

Ou, si vous souhaitez examiner l’ensemble du processus et les articles respectifs pour chacune des phases publication, consultez l’article [mise en route : comment publier une offre à la place de marché Azure](marketplace-publishing-getting-started.md).

[link-acct]:marketplace-publishing-accounts-creation-registration.md

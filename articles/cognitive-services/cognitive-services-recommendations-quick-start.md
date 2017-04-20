<properties
    pageTitle="Guide de démarrage rapide : Machine apprentissage recommandations API | Microsoft Azure"
    description="Azure apprentissage automatique recommandations - Guide de démarrage rapide"
    services="cognitive-services"
    documentationCenter=""
    authors="luiscabrer"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="cognitive-services"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/22/2016"
    ms.author="luisca"/>

# <a name="quick-start-guide-for-the-cognitive-services-recommendations-api"></a>Guide de démarrage rapide de l’API de recommandations Services cognitifs

Ce document décrit comment intégré à votre service ou l’application à utiliser l' [API de recommandations](http://go.microsoft.com/fwlink/?LinkId=759710).
Vous pouvez trouver plus d’informations sur l’API de recommandations et d’autres Services cognitifs [ici](http://go.microsoft.com/fwlink/?LinkId=759709). Dans ce guide, vous pouvez également trouver la [Référence de l’API recommandations](http://go.microsoft.com/fwlink/?LinkId=759348) pratique.


<a name="Overview"></a>
## <a name="general-overview"></a>Vue d’ensemble

Ce document est un guide étape par étape. Notre objectif est de vous guident dans les étapes nécessaires pour former un modèle et vous oriente vers des ressources qui vous permettra d’utiliser le modèle à partir de votre environnement de production.

Cet exercice prendra environ 30 minutes.

Pour utiliser [L’API de recommandations](http://go.microsoft.com/fwlink/?LinkId=759710), vous devez procéder comme suit :

1. Créer un modèle : un modèle est un conteneur de vos données d’utilisation, les données du catalogue et le modèle de recommandations.
1. Importer des données catalogue - catalogue contient des informations de métadonnées sur les éléments.
1. Importer des données d’utilisation - données d’utilisation peuvent être téléchargés dans une des méthodes de 2 (ou les deux) :
  -  En téléchargeant un fichier qui contient les données d’utilisation.
  -  Envoi d’événements d’acquisition de données.
  En règle générale, vous téléchargez un fichier de l’utilisation afin de pouvoir créer un modèle de recommandation initiale (démarrage) et utilisez-le jusqu'à ce que le système recueille suffisamment de données en utilisant le format d’acquisition de données.
1. Créez un modèle de recommandation : il s’agit d’une opération asynchrone dans lequel le système de recommandation prend toutes les données de l’utilisation et crée un modèle de recommandations. Cette opération peut prendre plusieurs minutes ou plusieurs heures selon la taille des données et les paramètres de configuration de génération. Lors du déclenchement de la version, vous obtiendrez un ID de génération. Utilisez-le pour vérifier lorsque le processus de génération est terminé avant de commencer à utiliser les recommandations.
1. Utilisation de recommandations - recommandations obtenir un élément spécifique ou une liste d’éléments.

<a name="GetStarted"></a>
### <a name="lets-get-started"></a>Prise en main !

Vous allez commencer la création d’un modèle de recommandations. Nous allons puis vous guide sur la façon d’utiliser les résultats générés par le modèle pour consulter des recommandations power sur un site de commerce.

<a name="Ex1Task1"></a>
#### <a name="task-1---signing-up-for-the-recommendations-api"></a>Tâche 1 - signature pour l’API de recommandations ####

Dans cette tâche, vous allez inscrire pour le service de recommandations API et créer un modèle de recommandations.

1. Accédez au **Portail Azure** à [http://portal.azure.com/](http://portal.azure.com/) et vous connecter avec votre compte Azure.

1.  Cliquez sur **+ Nouveau**.

1. Sélectionnez l’option **Intelligence** .

1. Sélectionnez le produit **Cognitifs API des Services** .
Ce produit vous permettra de démarrer un abonnement pour un des services cognitifs API (nominale, texte Analytique, ordinateur Vision, etc.). Aujourd'hui nous allons nous concentrer sur l’API de recommandations.

1. Dans la page d’accueil cognitifs API des Services, entrez le **nom du compte** pour votre abonnement recommandations. (Pour l’instance : « MyRecommendations »). Ce nom ne doit pas avoir de n’importe quel espace.

1. Sur le **type de l’API**, sélectionnez **les recommandations**.

1. Sur **couche de tarification**, vous pouvez sélectionner un plan. Vous pouvez sélectionner la couche **Free** pour 10 000 transactions par mois. Il s’agit d’une offre gratuite, aussi est-il un bon moyen de commencer à essayer le système. Une fois que vous accédez à de production, nous vous recommandons de vous envisagez de volume de votre demande, puis modifiez le type de plan en conséquence. (Remarque : vous pouvez disposent d’un abonnement gratuit à niveaux à la fois)

1. Sélectionnez un **Groupe de ressources**, ou créer un nouveau si vous n’en avez pas déjà.

1. Vous pouvez modifier les autres éléments dans la boîte de dialogue Créer. Nous devons vous signaler que le fournisseur de ressources aujourd'hui est uniquement prise en charge des centres de données des États-Unis.

1. Une fois que vous avez fini avec toutes les sélections, cliquez sur **créer**.

1. Patientez quelques minutes pour la ressource à déployer.
Une fois qu’elle est déployée, vous pouvez accéder à la section **clés** dans la carte de **paramètres** où vous disposez d’une clé principale et secondaire à utiliser l’API.  Copiez la clé primaire, que vous en aurez besoin lorsque vous créez votre premier modèle.

<a name="Ex1Task2"></a>
#### <a name="task-2---did-you-bring-your-data"></a>Tâche 2 : avez vous récupérez vos données ? ####

L’API recommandations apprendrez à partir de votre catalogue et vos transactions afin de fournir des recommandations bon produit. Cela signifie que vous avez besoin de flux des données sur vos produits (nous appelons cela un fichier de **catalogue** ) et un ensemble de transactions assez grandes pour qu’elle rechercher des modèles intéressantes de consommation (nous appelons cette **utilisation**).

1. En règle générale, vous le feriez interroger votre base de données de transactions pour ces éléments d’information.
Au cas où vous n’avez pas les pratique, nous vous fournissons des exemples de données en fonction des données de transaction Microsoft Store.

 Vous pouvez télécharger les données à partir [d’ici](http://aka.ms/RecoSampleData). Copier et décompresser le MsStoreData.Zip dans un dossier sur votre ordinateur local.

 > **Remarque :** L’exemple de code que vous allez télécharger et exécuter dans la tâche 3 comporte des exemples de données incorporés déjà qu’elle contient--afin que cette tâche est facultative.  Ceci étant dit, cette tâche vous permettra de télécharger des jeux de données plus plausible et vous permettent de comprendre les entrées dans l’API de recommandations mieux.

1.  Maintenant examinons le fichier de catalogue. Accédez à l’emplacement où vous avez copié les données.
 Ouvrez le fichier de catalogue dans **le bloc-notes**.

 Vous remarquerez que le fichier de catalogue est assez simple. Il présente comme suit`<itemid>,<item name>,<product category>`

 >  AAA-04294, OfficeLangPack 2013 32/64 E34 en ligne DwnLd, Office <br>
 > AAA-04303, OfficeLangPack 2013 32/64 ET en ligne DwnLd, Office  <br>
 > C9F 00168, KRUSELL Kiruna retourner garde pour Nokia Lumia 635 - mixte, Accessoires

 Nous devons vous signaler qu’un fichier de catalogue peut être beaucoup plus riche, par exemple vous pouvez ajouter des métadonnées relatives aux produits (nous appelons ces *fonctionnalités de l’élément*). Vous devez voir la section [format de catalogue](http://go.microsoft.com/fwlink/?LinkID=760716) dans la référence de l’API pour plus d’informations sur le format de catalogue.

1. Nous allons faire de même avec les données d’utilisation. Vous remarquerez que la date de l’utilisation de la mise en forme est `<User Id>,<Item Id>,<Time Stamp>,<Event>`.

  > 00037FFEA61FCA16, 288186200, 2015/08/04T11:02:52, 0003BFFDD4C2148C achat, 297833400, 2015/08/04T11:02:50, 0003BFFDD4C2118D achat, 297833300, 08/2015/04T11:02:40, 00030000D16C4237 achat, 297833300, 08/2015/04T11:02:37, 0003BFFDD4C20B63 achat, 297833400, 08/2015/04T11:02:12, 00037FFEC8567FB8 achat, 297833400, 2015/08/04T11:02:04, achat

Notez que les trois premiers éléments sont obligatoires. Le type d’événement est facultatif. Vous pouvez consulter le [format de l’utilisation](http://go.microsoft.com/fwlink/?LinkID=760712) pour plus d’informations sur cette rubrique.

 > **La quantité de données avez-vous besoin ?**
 <p>
De même, il dépend vraiment les données d’utilisation. Le système apprend lorsque les utilisateurs qui achètent différents éléments. Pour certaines versions comme FBT, il est important de savoir quels éléments sont achetées dans ces mêmes transactions. (Nous appelons cette *occurrences co-création*). Règle générale est d’utiliser la plupart des éléments de toutes les 20 transactions plus d’informations, afin que si vous avez 10 000 éléments dans votre catalogue, nous recommande que vous disposez au moins 20 heures ce nombre de transactions ou d’environ 200 000.
Là encore, il s’agit d’une règle de base. Vous avez besoin tester avec vos données.
</p>

<a name="Ex1Task3"></a>
####Tâche 3 : création d’un modèle de recommandations####

À présent que vous avez un compte et contiennent des données, nous allons créer votre premier modèle.

Dans cette tâche, vous allez utiliser l’exemple d’application pour créer votre premier modèle.

1. Vous devez tout d’abord être prenant en charge de la [Référence de l’API recommandations](http://go.microsoft.com/fwlink/?LinkId=759348).

1. Télécharger l' [Exemple d’Application](http://go.microsoft.com/fwlink/?LinkID=759344) dans un dossier local.

1. Dans Visual Studio, ouvrez la solution **RecommendationsSample.sln** située dans le dossier **c#** .

1. Ouvrez le fichier **SampleApp.cs** . Notez que les étapes suivantes dans le fichier :
 + Créer un modèle
 + Ajouter un fichier de catalogue
 + Ajouter un fichier de l’utilisation
 + Déclencher la lecture d’une version du modèle
 + Obtenir une recommandation basée sur une paire d’éléments
<p></p>

1. Remplacez la valeur du champ **AccountKey** par la clé à partir de la tâche 1.

1. Étape via la solution, et vous verrez comment un modèle est créé.

1. Essayez de remplacer les fichiers de catalogue et l’utilisation que vous venez de télécharger pour créer un nouveau modèle pour Microsoft Store, ou pour obtenir des recommandations livre. Vous devez modifier le nom du modèle, ainsi que les éléments pour lesquels vous demandez recommandations.

1. Lorsque le modèle est créé, prenez note de l' **ID du modèle** que vous en aurez besoin lors de la demande de recommandations dans votre environnement.

>  En savoir plus sur Générer des types et comment évaluer la qualité de versions préliminaires [ici](cognitive-services-recommendations-buildtypes.md).

<a name="Ex1Task4"></a>
### <a name="putting-your-model-in-production"></a>Placer votre modèle en production ! ###

Maintenant que vous comprenez comment créer un modèle et utiliser les recommandations, l’étape suivante consiste à mettre en production sur votre site Web, application mobile ou intégrer dans votre système CRM ou planification.
Bien évidemment, chacun de ces mises en œuvre est différent. Dans la mesure où l’API recommandations sont demandés comme un service web, vous devriez pouvoir appeler n’importe lequel de ces différents environnements facilement.

**Importantes :**  Si vous souhaitez afficher les recommandations à partir d’un client public (par exemple, votre site de commerce électronique), vous devez créer un serveur proxy pour fournir les recommandations. Ceci est important pour que votre clé API n’est pas exposée à des entités externes (potentiellement non fiables).

Voici quelques suggestions des emplacements où vous pouvez utiliser des recommandations :

### <a name="product-page"></a>Page du produit

**Recommandations d’élément**
<p>Si le modèle a été une formation sur les données d’achat, il permet à votre client pour *découvrir les produits qui sont susceptibles d’être utiles aux personnes qui ont acheté l’objet source*.</p>
<p>Si le modèle a été formé sur cliquez sur données, il permet à votre client pour *découvrir les produits qui sont susceptibles d’être visités par les personnes qui ont visités l’élément de source*. Ce type de modèle peut retourner des objets similaires.</p>

**Forum acheté ensemble recommandations**
<p>A fréquemment acheté ensemble build pourrait être formé, pour le rendre accessible ensembles d’éléments sont susceptibles d’être achetés avec cet élément.</p>

### <a name="check-out-page"></a>Consultez la Page

**Recommandations d’élément**
<p>Recommandations modèle peut prendre comme entrée une liste d’éléments. Vous pouvez donc passer tous les éléments dans le panier comme entrée pour obtenir des recommandations.
Dans ce cas, le modèle fournira recommandations qui intéressent donné tous les éléments dans le panier.
</p>

### <a name="landing-page"></a>Page d’accueil

**Recommandations d’utilisateur**
<p>
Recommandations modèle peut prendre comme entrée l’id d’utilisateur.  Utiliser l’historique des transactions par cet utilisateur pour fournir des recommandations personnalisées à l’utilisateur spécifié.
</p>

Consultez la [Documentation de recommandations élément obtenir](http://go.microsoft.com/fwlink/?LinkID=760719).

<a name="Ex1Task6"></a>
### <a name="whats-next"></a>Quoi d’autre ?
Félicitations si vous avez apporté cette extrémité ! Pour en savoir que plus, vous pouvez visiter la [Référence de l’API recommandations](http://go.microsoft.com/fwlink/?LinkId=759348) complète si vous avez des questions, n’hésitez pas à nous contacter aumlapi@microsoft.com

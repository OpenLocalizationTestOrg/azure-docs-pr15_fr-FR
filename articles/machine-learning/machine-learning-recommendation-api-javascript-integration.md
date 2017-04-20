<properties 
    pageTitle="Recommandations d’apprentissage machine : JavaScript intégration | Microsoft Azure" 
    description="Documentation présentée sous d’Azure Machine apprentissage recommandations - intégration JavaScript :" 
    services="machine-learning" 
    documentationCenter="" 
    authors="LuisCabrer" 
    manager="jhubbard" 
    editor="cgronlun"/>

<tags 
    ms.service="machine-learning" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="javascript" 
    ms.topic="article" 
    ms.date="09/08/2016" 
    ms.author="luisca"/>

# <a name="azure-machine-learning-recommendations---javascript-integration"></a>Machine Azure recommandations - intégration JavaScript d’apprentissage

>[AZURE.NOTE]Vous devez commencer au moyen du Service de troubles de l’API de recommandations au lieu de cette version. Le Service de recommandations cognitifs remplacera ce service, et toutes les nouvelles fonctionnalités seront développées il. Elle comporte des nouvelles fonctionnalités telles que le traitement par lots de prise en charge, une meilleure API Explorer, une expérience de l’inscription/facturation surface, plus cohérentes nettoyage API, etc..
> Pour plus d’informations sur la [migration vers le nouveau Service cognitifs](http://aka.ms/recomigrate)


Ce document décrivent comment intégrer votre site à l’aide de JavaScript. Le code JavaScript vous permet d’envoyer des événements d’Acquisition de données et comment utiliser les recommandations une fois que vous créez un modèle de recommandations. Toutes les opérations effectuées via JS peuvent également être effectuées côté serveur.

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

##<a name="1-general-overview"></a>1. présentation générale
Intégration de votre site avec Azure ML recommandations se composent sur 2 Phases :

1.  Envoyer des événements dans Azure ML recommandations. Ceci permet de créer un modèle de recommandations.
2.  Utiliser les recommandations. Une fois que le modèle est créé, vous pouvez utiliser les recommandations. (Ce document n’explique pas comment créer un modèle, lisez le guide de démarrage rapide pour obtenir plus d’informations sur la façon de).


<ins>J’ai phase</ins>

Dans la première phase vous insérez dans vos pages html une petite bibliothèque JavaScript qui permet à la page envoyer des événements quand ils apparaissent sur la page html dans des serveurs Azure ML recommandations (par le biais Data Market) :

![Dessin1][1]

<ins>Phase II</ins>

Dans la deuxième phase lorsque vous souhaitez afficher les recommandations sur la page que vous sélectionnez une des options suivantes :

1.Cliquez votre serveur (sur la phase de rendu de page) appelle Azure ML recommandations Server (par le biais Data Market) pour obtenir des recommandations. Les résultats incluent obtenir la liste des éléments. Votre serveur a besoin enrichir les résultats avec les éléments de métadonnées (par exemple, images, description) et envoyer la page créée dans le navigateur.

![Drawing2][2]

2.cliquez l’autre option consiste à utiliser le fichier JavaScript petit à partir de la phase 1 pour obtenir une simple liste d’éléments recommandés. Les données reçues ici sont très optimisées celui qui figure dans la première option.

![Drawing3][3]

##<a name="2-prerequisites"></a>2. conditions préalables

1. Créer un nouveau modèle à l’aide de l’API. Consultez le guide de démarrage rapide sur la façon d’y parvenir.
2. Coder votre &lt;dataMarketUser&gt;:&lt;dataMarketKey&gt; avec en base 64. (Cela servira pour l’authentification de base pour activer le code JS d’appeler l’API).


##<a name="3-send-data-acquisition-events-using-javascript"></a>3. envoyer des événements d’Acquisition de données à l’aide de JavaScript
Les étapes suivantes facilitent l’envoi d’événements :

1.  Inclure bibliothèque JQuery dans votre code. Vous pouvez le télécharger à partir de nuget dans l’URL suivante.

        http://www.nuget.org/packages/jQuery/1.8.2
2.  Inclure la bibliothèque recommandations Java Script à partir de l’URL suivante : http://aka.ms/RecoJSLib1

3.  Initialisation de la bibliothèque Azure ML recommandations avec les paramètres appropriés.

        <script>
            AzureMLRecommendationsStart("<base64encoding of username:key>",
            "<model_id>");
        </script>

4.  Envoyer l’événement approprié. Voir la section détaillée ci-dessous sur tous les types d’événements (exemple de cliquez sur l’événement)

        <script>
            if (typeof AzureMLRecommendationsEvent=="undefined") {      
                        AzureMLRecommendationsEvent = [];
                    }
            AzureMLRecommendationsEvent.push({ event: "click", item: "18321116" });
        </script>


###<a name="31-limitations-and-browser-support"></a>3.1. Limitations et navigateurs pris en charge
Il s’agit d’une implémentation de référence et lui est attribué en l’état. Il doit prendre en charge tous les principaux navigateurs.

###<a name="32-type-of-events"></a>3.2. Type d’événements
Il existe 5 types d’événement qui prend en charge de la bibliothèque : cliquez sur, recommandations, ajouter au panier un magasin, supprimer à partir d’un magasin panier et d’achat. Il existe un événement supplémentaire qui est utilisé pour définir le contexte utilisateur appelé connexion.

####<a name="321-click-event"></a>3.2.1. Cliquez sur l’événement
Cet événement doit être utilisé chaque fois qu’un utilisateur a cliqué sur un élément. En règle générale, lorsque l’utilisateur clique sur un élément une nouvelle page s’ouvre avec les détails d’un élément ; dans cette page cet événement doit être déclenché.

Paramètres :
- « Cliquez sur l’événement (chaîne, obligatoire) »
- élément (chaîne, obligatoire) - identificateur Unique de l’élément
- nom de l’élément (chaîne, facultatif) - le nom de l’élément
- DescriptionArticle (chaîne, facultatif) - la description de l’élément
- itemCategory (chaîne, facultatif) - la catégorie de l’élément
        
        <script>
            if (typeof AzureMLRecommendationsEvent == "undefined") { AzureMLRecommendationsEvent = []; }
            AzureMLRecommendationsEvent.push({ event: "click", item: "3111718" });
        </script>

Ou avec données facultatives :

        <script>
            if (typeof AzureMLRecommendationsEvent === "undefined") { AzureMLRecommendationsEvent = []; }
            AzureMLRecommendationsEvent.push({event: "click", item: "3111718", itemName: "Plane", itemDescription: "It is a big plane", itemCategory: "Aviation"});
        </script>


####<a name="322-recommendation-click-event"></a>3.2.2. Recommandation cliquez sur l’événement
Cet événement doit être utilisé chaque fois qu’un utilisateur a cliqué sur un élément qui a été reçu à partir d’Azure ML recommandations comme un élément recommandé. En règle générale, lorsque l’utilisateur clique sur un élément une nouvelle page s’ouvre avec les détails d’un élément ; dans cette page cet événement doit être déclenché.

Paramètres :
- événement (chaîne, obligatoire) - « recommendationclick »
- élément (chaîne, obligatoire) - identificateur Unique de l’élément
- nom de l’élément (chaîne, facultatif) - le nom de l’élément
- DescriptionArticle (chaîne, facultatif) - la description de l’élément
- itemCategory (chaîne, facultatif) - la catégorie de l’élément
- (tableau de chaînes, facultatif) - est basée sur les grains à l’origine de la requête recommandation.
- recoList (chaîne matrice, facultatif) - le résultat de la demande de recommandation à l’origine de l’élément que vous avez cliqué.
        
        <script>
            if (typeof AzureMLRecommendationsEvent=="undefined") { AzureMLRecommendationsEvent = []; }
            AzureMLRecommendationsEvent.push({event: "recommendationclick", item: "18899918" });
        </script>

Ou avec données facultatives :

        <script>
            if (typeof AzureMLRecommendationsEvent == "undefined") { AzureMLRecommendationsEvent = []; }
            AzureMLRecommendationsEvent.push({ event: eventName, item: "198", itemName: "Plane2", itemDescription: "It is a big plane2", itemCategory: "Default2", seeds: ["Seed1", "Seed2"], recoList: ["199", "198", "197"]               });
        </script>


####<a name="323-add-shopping-cart-event"></a>3.2.3. Ajouter l’événement de panier d’achat
Cet événement doit être utilisé lorsque l’utilisateur Ajouter un élément au panier.
Paramètres :
* événement (chaîne, obligatoire) - « addshopcart »
* élément (chaîne, obligatoire) - identificateur Unique de l’élément
* nom de l’élément (chaîne, facultatif) - le nom de l’élément
* DescriptionArticle (chaîne, facultatif) - la description de l’élément
* itemCategory (chaîne, facultatif) - la catégorie de l’élément
        
        <script>
            if (typeof AzureMLRecommendationsEvent == "undefined") { AzureMLRecommendationsEvent = []; }
            AzureMLRecommendationsEvent.push({event: "addshopcart", item: "13221118" });
        </script>

####<a name="324-remove-shopping-cart-event"></a>3.2.4. Supprimer l’événement de panier d’achat
Cet événement doit être utilisé lorsque l’utilisateur supprime un élément au panier.

Paramètres :
* événement (chaîne, obligatoire) - « removeshopcart »
* élément (chaîne, obligatoire) - identificateur Unique de l’élément
* nom de l’élément (chaîne, facultatif) - le nom de l’élément
* DescriptionArticle (chaîne, facultatif) - la description de l’élément
* itemCategory (chaîne, facultatif) - la catégorie de l’élément
        
        <script>
            if (typeof AzureMLRecommendationsEvent=="undefined") { AzureMLRecommendationsEvent = []; }
            AzureMLRecommendationsEvent.push({ event: "removeshopcart", item: "111118" });
        </script>

####<a name="325-purchase-event"></a>3.2.5. Événement d’achat
Cet événement doit être utilisé lorsque l’utilisateur acheté son panier.

Paramètres :
* événement (chaîne) - « acheter »
* objets (achetés []) - tableau contenant une entrée pour chaque élément acheté.<br><br>
Mise en forme que vous avez achetée :
    * élément (chaîne) - identificateur Unique de l’élément.
    * compter le nombre (int ou string) - nombre d’éléments qui ont été achetés.
    * prix (le flottement ou chaîne) - champ facultatif - le prix de l’élément.

L’exemple ci-après affiche achat de 3 éléments (33, 34, 35), deux avec tous les champs remplis (élément, nombre, prix) et l’autre (élément 34) sans un prix.

        <script>
            if ( typeof AzureMLRecommendationsEvent == "undefined"){ AzureMLRecommendationsEvent = []; }
            AzureMLRecommendationsEvent.push({ event: "purchase", items: [{ item: "33", count: "1", price: "10" }, { item: "34", count: "2" }, { item: "35", count: "1", price: "210" }] });
        </script>

####<a name="326-user-login-event"></a>3.2.6. Événement de connexion utilisateur
Bibliothèque ML recommandations événement Azure crée et utiliser des cookies afin d’identifier les événements fourni du même navigateur. Afin d’améliorer les résultats du modèle Azure ML recommandations permet de définir une identification unique utilisateur qui remplace l’utilisation des cookies.

Cet événement doit être utilisé après la connexion de l’utilisateur à votre site.

Paramètres :
* événement (chaîne) - « userlogin »
* utilisateur (chaîne) - identification unique de l’utilisateur.
        <script>
  Si (typeof AzureMLRecommendationsEvent == « non défini ») {AzureMLRecommendationsEvent = [ ;]} AzureMLRecommendationsEvent.push ({événement : « userlogin » utilisateur : « ABCD10AA »}) ;      </script>

##<a name="4-consume-recommendations-via-javascript"></a>4. Utilisation de recommandations via JavaScript
Le code qui utilise la recommandation est déclenché par un événement JavaScript par page Web du client. La réponse recommandation inclut les ID éléments recommandés, leurs noms et leurs évaluations. Il est préférable d’utiliser cette option uniquement pour un affichage de la liste des éléments recommandés - gestion plus complexe (par exemple en ajoutant des métadonnées de l’élément) doit être effectuée sur l’intégration de côté serveur.

###<a name="41-consume-recommendations"></a>4.1 consommer recommandations
Pour utiliser les recommandations que vous deviez incluent les bibliothèques JavaScript obligatoires dans votre page et d’appeler AzureMLRecommendationsStart. Voir la section 2.

Pour utiliser les recommandations pour un ou plusieurs éléments que vous avez besoin d’appeler une méthode appelée : AzureMLRecommendationsGetI2IRecommendation.

Paramètres :
* Un ou plusieurs éléments pour obtenir des recommandations pour des éléments (tableau de chaînes -). Si vous utilisez une version Fbt puis vous pouvez définir ici qu’un seul élément.
* numberOfResults (int) - nombre de résultats requis.
* includeMetadata (booléen, facultatif) - si la valeur « true » indique que le champ de métadonnées doit être rempli dans le résultat.
* Fonction - une fonction qui gérera les recommandations de traitement renvoyé. Les données sont renvoyées sous forme de tableau de :
    * Id d’article - élément unique
    * Name : nom de l’option (si existent dans le catalogue)
    * évaluation - évaluation de recommandations
    * métadonnées - une chaîne qui représente les métadonnées de l’élément

Exemple : Le code suivant demande 8 recommandations pour 64f6eb0d-947a-4c18-a16c-888da9e228ba « élément » (et en spécifiant ne pas includeMetadata - implicitement indiqué qu’aucune métadonnées n’exigeant requise), il puis effectuera les résultats dans un tampon.

        <script>
            var reco = AzureMLRecommendationsGetI2IRecommendation(["64f6eb0d-947a-4c18-a16c-888da9e228ba"], 8, false, function (reco) {
                var buff = "";
                for (var ii = 0; ii < reco.length; ii++) {
                    buff += reco[ii].item + "," + reco[ii].name + "," + reco[ii].rating + "\n";
                }
                alert(buff);
            });
        </script>


[1]: ./media/machine-learning-recommendation-api-javascript-integration/Drawing1.png
[2]: ./media/machine-learning-recommendation-api-javascript-integration/Drawing2.png
[3]: ./media/machine-learning-recommendation-api-javascript-integration/Drawing3.png
 

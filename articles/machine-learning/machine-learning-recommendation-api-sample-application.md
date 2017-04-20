<properties 
    pageTitle="Opérations courantes dans l’API de recommandations apprentissage Machine | Microsoft Azure" 
    description="Exemple de recommandation ML Azure Application" 
    services="machine-learning" 
    documentationCenter="" 
    authors="LuisCabrer" 
    manager="jhubbard" 
    editor="cgronlun"/>

<tags 
    ms.service="machine-learning" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/08/2016" 
    ms.author="luisca"/> 


# <a name="recommendations-api-sample-application-walkthrough"></a>Procédure d’Application de l’API exemple de recommandations

>[AZURE.NOTE]Vous devez commencer au moyen du Service de troubles de l’API de recommandations au lieu de cette version. Le Service de recommandations cognitifs remplacera ce service, et toutes les nouvelles fonctionnalités seront développées il. Elle comporte des nouvelles fonctionnalités telles que le traitement par lots de prise en charge, une meilleure API Explorer, une expérience de l’inscription/facturation surface, plus cohérentes nettoyage API, etc..
> Pour plus d’informations sur la [migration vers le nouveau Service cognitifs](http://aka.ms/recomigrate)

##<a name="purpose"></a>Objectif

Ce document illustre l’utilisation de l’API Azure Machine apprentissage recommandations via un [exemple d’application](https://code.msdn.microsoft.com/Recommendations-144df403).

Cette application n’est pas destinée à inclure toutes les fonctionnalités et n’utilise pas toutes les API. Il montre quelques opérations courantes à effectuer lorsque vous souhaitez tout d’abord jouer avec le service de recommandations apprentissage automatique. 

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

##<a name="introduction-to-machine-learning-recommendation-service"></a>Présentation du service de recommandations d’apprentissage automatique

Recommandations via le service de recommandations apprentissage automatique sont activées lorsque vous créez un modèle de recommandation basé sur les données suivantes :

* Un référentiel des éléments que vous souhaitez vous recommandons, également connu sous le nom d’un catalogue
* Données représentant l’utilisation d’éléments par l’utilisateur ou la session (Cela peut être posé au fil du temps via l’acquisition de données, non dans le cadre de l’application exemple)

Une fois créé un modèle de recommandations, vous pouvez l’utiliser pour prévoir les éléments un utilisateur peut être intéressent par, en fonction d’un ensemble d’éléments (ou un seul élément) l’utilisateur sélectionne.

Pour activer le scénario précédent, procédez comme suit dans le service de recommandations apprentissage automatique :

* Créer un modèle : il s’agit d’un conteneur logique qui contient les données (catalogue et l’utilisation) et les modèles de prévision. Chaque conteneur de modèle est identifié via un identificateur unique, qui est attribué lors de sa création. Cet identifiant est appelé l’ID de modèle, et qu’il est utilisé par la plupart des API. 
* Téléchargement au catalogue : lorsqu’un conteneur de modèle est créé, vous pouvez associer au catalogue.

**Remarque**: création d’un modèle et le téléchargement à un catalogue sont généralement effectuées une fois pour le cycle de vie du modèle.

* Télécharger l’utilisation : cela ajoute des données d’utilisation dans le conteneur de modèle.
* Créez un modèle de recommandation : une fois que vous avez suffisamment de données, vous pouvez créer le modèle de recommandations. Cette opération utilise les algorithmes d’apprentissage automatique supérieure pour créer un modèle de recommandations. Chaque build est associée à un ID unique. Vous devez garder une trace de ce code, car il n’est nécessaire pour la fonctionnalité de certaines API.
* Surveiller le processus de construction : construction d’un modèle de recommandation est une opération asynchrone et peut prendre plusieurs minutes à plusieurs heures, selon la quantité de données (catalogue et l’utilisation) et les paramètres de génération. Par conséquent, vous devez surveiller la génération. Un modèle de recommandation est créé uniquement si sa génération associée est terminée avec succès.
* (Facultatif) Choisissez un modèle intégré active recommandation : cette étape est nécessaire uniquement si vous avez plusieurs modèles recommandation intégré dans le conteneur de votre modèle. Toute demande pour obtenir des recommandations sans indiquant le modèle de recommandation active est redirigé automatiquement par le système vers la version active par défaut. 

**Remarque**: un modèle de recommandation active est prête production et il est conçu pour la charge de production. Cela est différent d’un modèle de recommandations non actif, reste dans un environnement de test de type (parfois appelé mise en attente).

* Afficher des recommandations : une fois que vous avez un modèle de recommandation, vous pouvez déclencher recommandations relatives à un seul élément ou une liste d’éléments que vous sélectionnez. 

Vous appelle généralement obtenir recommandation pour une période donnée. Pendant ce laps de temps, vous pouvez rediriger les données d’utilisation pour le système de recommandation apprentissage automatique, qui permet d’ajouter ces données au conteneur de modèle spécifié. Lorsque vous avez suffisamment les données d’utilisation, vous pouvez créer un nouveau modèle de recommandations qui intègre les données d’utilisation supplémentaires. 

##<a name="prerequisites"></a>Conditions préalables

* Visual Studio 2013
* Accès à Internet 
* Abonnement aux recommandations API (https://datamarket.azure.com/dataset/amla/recommendations).

##<a name="azure-machine-learning-sample-app-solution"></a>Solution d’application Azure apprentissage automatique exemple

Cette solution contient le code source, exemple d’utilisation, fichier catalogue et directives pour télécharger les packages requis pour la compilation.

##<a name="the-apis-used"></a>Les API utilisées

L’application utilise la fonctionnalité de recommandation d’apprentissage automatique via un sous-ensemble des API disponibles. API suivantes sont présentés dans l’application :

* Créer le modèle : créer un conteneur logique pour mettre en attente de modèles de données et de recommandations. Un modèle est identifié par un nom, et vous ne pouvez pas créer plusieurs modèles portant le même nom.
* Télécharger un fichier catalogue : permet de télécharger les données du catalogue.
* Télécharger le fichier d’utilisation : permet de télécharger des données d’utilisation.
* Déclencher la lecture build : permet de créer un modèle de recommandations.
* Contrôler l’exécution de la génération : permet de contrôler le statut de construction d’un modèle de recommandations.
* Choisir un modèle intégré pour recommandation : permet d’indiquer le modèle de recommandation à utiliser par défaut pour une certaine conteneur de modèle. Cette étape est nécessaire uniquement si vous avez plusieurs modèles de recommandation et que vous voulez activer une version non active comme modèle recommandation active.
* Obtenir des recommandations : permet de récupérer des éléments recommandés en fonction d’un seul élément donné ou un ensemble d’éléments. 

Pour une description complète des API, voir la documentation de Microsoft Azure Marketplace. 

**Remarque**: un modèle peut avoir plusieurs versions préliminaires au fil du temps (et non pas simultanément). Chaque build est créé avec la même ou un catalogue mis à jour et les données d’utilisation supplémentaires.

## <a name="common-pitfalls"></a>Problèmes courants

* Vous devez fournir votre nom d’utilisateur et votre clé de compte principal Microsoft Azure Marketplace pour exécuter l’application d’exemple.
* Exécutez l’application exemple consécutivement échouera. Le flux de l’application inclut la création, télécharger, créer le moniteur et obtention des recommandations à partir d’un modèle prédéfini ; Par conséquent, il est impossible sur consécutives d’exécution si vous ne modifiez pas le nom du modèle entre les appels.
* Recommandations peuvent renvoyer sans données. L’exemple d’application utilise un très petit fichier catalogue et l’utilisation. Par conséquent, certains éléments à partir du catalogue n’ont aucun élément recommandés.

## <a name="disclaimer"></a>Exclusion de responsabilité
L’exemple d’application n’est pas destiné à être exécuté dans un environnement de production. Les données fournies dans le catalogue sont très petites, et elle ne fournit pas un modèle de recommandation significatif. Les données sont fournies comme une démonstration. 
 

<properties
   pageTitle="À l’aide de fonctions Azure avec applications logique | Microsoft Azure"
   description="Apprendre à utiliser les fonctions Azure avec les applications de logique"
   services="logic-apps,functions"
   documentationCenter=".net,nodejs,java"
   authors="jeffhollan"
   manager="dwrede"
   editor=""/>

<tags
   ms.service="logic-apps"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration"
   ms.date="10/18/2016"
   ms.author="jehollan"/>

# <a name="using-azure-functions-with-logic-apps"></a>À l’aide de fonctions Azure avec les applications de logique

Vous pouvez exécuter personnalisés extraits de c# ou node.js à l’aide de fonctions Azure à partir d’au sein d’une application logique.  [Fonctions Azure](../azure-functions/functions-overview.md) offre sans serveur computing dans Microsoft Azure. Cela est utile pour effectuer les tâches suivantes :

* Mise en forme avancée ou cluster des champs dans une application de logique
* Réalisation de calculs dans un flux de travail
* Étendre les fonctionnalités des applications logique avec les fonctions qui sont prises en charge dans c# ou node.js

## <a name="create-a-function-for-logic-apps"></a>Créer une fonction pour les applications de logique

Nous vous recommandons de créer une nouvelle fonction dans le portail Azure fonctions en utilisant les modèles **Webhook générique - nœud** ou **Webhook - générique c#** . Cela automatique-remplit un modèle qui accepte `application/json` à partir d’une application logique.  Si vous sélectionnez l’onglet **intégrer** dans Azure fonctions il doit avoir **le Mode** est défini sur **Webhook** et le **type de Webhook** de **JSON générique**.  Fonctions qui utilisent ces modèles sont automatiquement découvertes et répertoriées dans le Concepteur de logique applications sous **fonctions Azure dans ma région.**

Fonctions Webhook accepter une demande et passez à la méthode via un `data` variable. Vous pouvez accéder aux propriétés de votre charge utile en utilisant la notation point comme `data.foo`.  Par exemple, une fonction JavaScript simple qui convertit une valeur de date/heure dans une chaîne de date ressemble à l’exemple suivant :

```
function start(req, res){
    var data = req.body;
    res = {
        body: data.date.ToDateString();
    }
}
```

## <a name="call-azure-functions-from-a-logic-app"></a>Fonctions d’Azure appel à partir d’une application de logique

Dans le concepteur, si vous cliquez sur le menu **Actions** , vous pouvez sélectionner **Fonctions Azure dans ma région**.  Cette répertorie les conteneurs de votre abonnement et vous permet de choisir la fonction que vous souhaitez appeler.  

Après avoir sélectionné la fonction, vous êtes invité à spécifier un objet de la charge utile d’entrée. Il s’agit le message que l’application logique envoie à la fonction, et il doit être un objet JSON. Par exemple, si vous souhaitez passer la date **De dernière modification** à partir d’un déclencheur Salesforce, la charge utile de fonction peut ressembler à ceci :

![Dernière date modifiée][1]

## <a name="trigger-logic-apps-from-a-function"></a>Déclencheur logique applications à partir d’une fonction

Il est également possible de déclencher la lecture d’une application logique à partir d’une fonction.  Pour ce faire, créez simplement une application logique avec un déclencheur manuel. Pour plus d’informations, voir [applications logique comme points de terminaison pouvant être appelé par](app-service-logic-http-endpoint.md).  Ensuite, dans votre fonction, générer un HTTP POST vers l’URL de déclenchement manuel avec la charge utile que vous voulez envoyer à l’application logique.

### <a name="create-a-function-from-the-designer"></a>Créer une fonction à partir du Concepteur

Vous pouvez également créer une node.js webhook fonction dans le concepteur. Tout d’abord, sélectionnez **Fonctions Azure dans ma région,** puis un conteneur pour votre fonction.  Si vous n’avez encore un conteneur, vous devez créer un à partir du [portail de fonctions Azure](https://functions.azure.com/signin). Puis cliquez sur **Créer**.  

Pour générer un modèle basé sur les données que vous souhaitez calculer, spécifiez l’objet de contexte que vous prévoyez de passer dans une fonction. Ce doit être un objet JSON. Par exemple, si vous passez le contenu du fichier à partir d’une action FTP, la charge utile de contexte ressemble à ceci :

![Charge utile de contexte][2]

>[AZURE.NOTE] Étant donné que cet objet n’a pas été casté sous forme de chaîne, le contenu sera ajouté directement à la charge utile JSON. Toutefois, il provoque une erreur, si elle n’est pas un jeton JSON (autrement dit, une chaîne ou un tableau d’objets JSON). Pour effectuer un cast sous forme de chaîne, simplement ajouter devis comme indiqué dans la première illustration dans cet article.

Le concepteur génère ensuite un modèle de fonction que vous pouvez créer en ligne. Variables préalable sont créées en fonction du contexte que vous prévoyez de passer à la fonction.




<!--Image references-->
[1]: ./media/app-service-logic-azure-functions/callFunction.png
[2]: ./media/app-service-logic-azure-functions/createFunction.png

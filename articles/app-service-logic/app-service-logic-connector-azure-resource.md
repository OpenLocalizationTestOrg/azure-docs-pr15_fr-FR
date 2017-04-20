<properties
   pageTitle="L’utilisation du connecteur ressource Azure dans les applications logique | Service d’application Microsoft Azure"
   description="Comment créer et configurer l’application Azure ressource connecteur ou API et l’utiliser dans une application logique dans le Service d’application Azure"
   services="logic-apps"
   documentationCenter=".net,nodejs,java"
   authors="stepsic-microsoft-com"
   manager="erikre"
   editor=""/>

<tags
   ms.service="logic-apps"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration"
   ms.date="09/01/2016"
   ms.author="stepsic"/>

# <a name="get-started-with-the-azure-resource-connector-and-add-it-to-your-logic-app"></a>Prise en main du connecteur ressource Azure et l’ajouter à votre application logique
>[AZURE.NOTE] Cette version de l’article s’applique à la version du schéma logique applications 2014-12-01-aperçu.

Utiliser le connecteur ressource Azure pour gérer facilement les ressources Azure à l’intérieur de votre application logique.

## <a name="create-the-azure-resource-connector"></a>Créer le lien ressources Azure
Pour utiliser l’application Azure ressource connecteur API, vous devez d’abord créer une instance de celle-ci. Vous pouvez faire inline lors de la création d’une application logique ou en sélectionnant l’application Azure ressource Gestionnaire connecteur API dans la Azure Marketplace.

Pour configurer, vous avez besoin définir l’un Service Principal avec les autorisations pour faire tout ce qu’il vous voulez faire dans Azure. Tous les appels sont effectués sur à la place de ce Service Principal que vous avez configurées. Cela permet de vous pour définir l’étendue du connecteur pour utiliser uniquement exactement ce que vous voulez qu’il fasse et rien de plus.

David Ebbo a écrit [un billet de blog correctement](http://blog.davidebbo.com/2014/12/azure-service-principal.html) sur la façon de configurer ce paramètre. Suivez toutes les instructions fournies et obtenir votre **ID client**, la **Réf Client** et le **code Secret**. Ces trois champs, ainsi que l' **ID de l’abonnement**, sont que sont requis pour configurer le connecteur.

## <a name="using-the-azure-resource-connector-in-logic-apps-designer"></a>L’utilisation du connecteur de ressources Azure dans le Concepteur d’applications logique
### <a name="trigger"></a>Déclencheur
Il existe deux déclencheurs prises en charge dans le connecteur :

Nom | Description
---- | -----------
Événement se produit | Déclencheur lorsqu’un événement se produit à une ressource dans votre abonnement.
Métrique dépasse seuil |  Déclencheur lorsqu’elle est une métrique répond à un certain seuil.

### <a name="action"></a>Action

De même, vous pouvez fournir un grand nombre d’actions à l’intérieur de votre abonnement Azure :

Pour les **groupes de ressources** que vous pouvez :

Nom | Description
---- | -----------
Répertorier les groupes de ressources | Répertorier tous les groupes de ressources de l’abonnement.
Obtenir le groupe de ressources | Obtenir un groupe de ressources par son id.
Créer le groupe de ressources | Créer ou mettre à jour un groupe de ressources.
Supprimer le groupe de ressources | Supprimer un groupe de ressources.

Pour les **ressources** , vous pouvez :

Nom | Description
---- | -----------
Liste des ressources | Liste des ressources dans votre abonnement par différents types de filtres.
Obtenir une ressource | Obtenir une seule ressource par sa ressource de code.
Créer ou mettre à jour des ressources | Créer une ressource ou mettre à jour une ressource existante. Vous devez fournir toutes les propriétés pour cette ressource.
Action de ressources |  Effectuer d’autres actions sur une ressource. Vous devez connaître le nom de l’action et la charge utile que cette action vous permet d’accéder (le cas échéant).
Supprimer la ressource | Supprimer une ressource.

Pour **Les fournisseurs de ressources** , vous pouvez :

Nom | Description
---- | -----------
Fournisseurs de ressources de liste | La liste tous les fournisseurs de ressources disponibles dans l’abonnement.

Pour les **Déploiements de groupe de ressources** , vous pouvez :

Nom | Description
---- | -----------
Déploiements de liste | La liste tous les déploiements dans un groupe de ressources.
Obtenir le déploiement | Obtenir un déploiement modèle par son id.
Créer le déploiement | Créer un nouveau déploiement de groupe de ressources en fournissant un modèle.

Pour les **événements** sur les ressources, vous pouvez :

Nom | Description
---- | -----------
Obtenir des événements | Obtenir des événements dans un abonnement ou d’une ressource.

Pour **les mesures** sur les ressources, vous pouvez :

Nom | Description
---- | -----------
Obtenir des indicateurs | Obtenir une métrique pour une ressource de code.

## <a name="do-more-with-your-connector"></a>Faire plus avec le lien
Maintenant que le lien est créé, vous pouvez l’ajouter à un flux d’entreprise à l’aide d’une application logique. Voir [que sont les applications logique ?](app-service-logic-what-are-logic-apps.md).

>[AZURE.NOTE] Si vous voulez commencer à utiliser les applications Azure logique avant de vous inscrire pour un compte Azure, accédez à [l’application logique essayez](https://tryappservice.azure.com/?appservice=logic), où vous pouvez créer une application de logique starter courte immédiatement dans le Service d’application. Aucune carte de crédit obligatoire ; Aucune engagements.

Afficher la référence Swagger des API REST en [liens et des API applications référence](http://go.microsoft.com/fwlink/p/?LinkId=529766).

<!--References -->

<!--Links -->
[Creating a Logic app]: app-service-logic-create-a-logic-app.md

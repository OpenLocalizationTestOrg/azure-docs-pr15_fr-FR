<properties
    pageTitle="Vue d’ensemble des connecteurs d’applications logique | Microsoft Azure"
    description="Vue d’ensemble des connecteurs pouvant être utilisées dans une application de logique"
    services=""
    documentationCenter="" 
    authors="jeffhollan"
    manager="erikre"
    editor=""
    tags="connectors"/>

<tags
   ms.service="logic-apps"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na" 
   ms.date="07/15/2016"
   ms.author="jehollan"/>

# <a name="using-connectors-in-a-logic-app"></a>Utiliser des liens dans une application de logique

Connecteurs permettent d’accéder rapidement aux événements, les données et les actions sur plates-formes, protocoles et services.  La liste complète des connecteurs qui prend en charge les applications logique peut [être ici](apis-list.md).  Connecteurs peut être utilisés comme un déclencheur ou une action dans une application logique et peuvent nécessiter une *connexion* configurée pour utiliser (par exemple : autoriser un compte Twitter puissent accéder ou publier en votre nom).

## <a name="basics"></a>Concepts de base

Les connecteurs sont services hébergés, vous pouvez accéder dans le cadre d’une application logique à intégrer à d’autres services, tels que Dynamics, Azure, force de vente, [et plus encore](apis-list.md).  Ils sont déployés et gérés par Microsoft, afin de pouvoir créer votre flux de travail de l’intégration avec échelle, de débit et de sécurité pris en charge.  Vous pouvez ajouter un lien à une application logique en recherchant et en sélectionnant une action de connecteur ou un déclencheur sous **afficher Microsoft managed API**.

![Menu d’action pour la sélection de déclencheur][1]

Chaque connecteur action ou un déclencheur possède son jeu de propriétés à configurer.  Vous pouvez cliquez sur les boutons d’informations pour en savoir plus sur l’action, ou faire référence à sa documentation [pour en savoir plus](apis-list.md).

Si vous voulez intégrer dans un service ou d’API qui n’est pas encore un connecteur, vous pouvez également étendre des applications logique via un [lien personnalisé](../app-service-logic/app-service-logic-create-api-app.md) ou simplement appeler directement au service via un protocole HTTP, par exemple.

## <a name="triggers"></a>Déclencheurs

Certains liens n’ont un déclencheur, ce qui signifie qu’un événement à partir de ce lien déclenche une application logique, passer dans toutes les données dans le cadre du déclencheur.  Un déclencheur est toujours la première étape dans une application logique.  Déclencheurs populaires incluent les opérations, telles que :
 
 * Périodicité - s’exécute chaque heure
 * Lors de la réception d’une requête HTTP
 * Lorsqu’un élément est ajouté à une file d’attente
 * Lors de la réception d’un message électronique
 
Certains déclencheurs seront déclenche le moment qu'un événement se produit au cours d’une notification à l’application logique, et d’autres nécessitent un intervalle de périodicité configuré sur la fréquence à laquelle l’application logique doit vérifier le service pour un événement (jusqu'à toutes les 15 secondes).  

Une fois la réception d’un événement, l’application logique exécuter se déclenche et les actions contenues dans le flux de travail démarre.  Vous pourrez également accéder à des données à partir du déclencheur dans l’ensemble du flux de travail (par exemple le déclencheur « sur un tweet nouveau' passera la tweet dans l’exécution).

## <a name="actions"></a>Actions

La plupart des connecteurs ont un ou plusieurs des actions pouvant être exécutées dans le cadre du flux de travail.  Les actions sont toutes les étapes qui se produisent après le déclenchement de l’exécution d’un déclencheur.  Pour ajouter un action, cliquez sur le bouton **Nouvelle étape** et la recherche pour le connecteur que vous souhaitez utiliser.  Une fois sélectionné (et configurez une fois toutes les [connexions](#connections) qui peuvent être nécessaires) que vous voyez la carte de l’action que vous pouvez configurer.  Vous pouvez sélectionner des données des étapes précédentes en cliquant sur un des jetons pour sorties ou entrez dans n’importe quelle autre configuration selon vos besoins.

![Configuration d’une action de connecteur][2]

## <a name="connections"></a>Connexions

La plupart des connecteurs nécessitent vous permettent de configurer une *connexion* avant de pouvoir utiliser le connecteur.  Une *connexion* est n’importe quelle configuration de connexion ou connexion nécessaire pour accéder au connecteur.  Pour les connecteurs qui utilisent OAuth, créer une connexion signifie que vous vous connectez au service (par exemple, Office 365, Salesforce ou GitHub) où votre jeton d’accès peut être chiffré et stockée en toute sécurité dans un magasin secrète Azure.  Autres connecteurs (par exemple, FTP et SQL) nécessitent une connexion qui contient la configuration, tels que l’adresse du serveur, nom d’utilisateur et mot de passe.  Ces informations de configuration de connexion sont également chiffrées et stockées en toute sécurité.  Connexions seront en mesure d’accéder au service pour tant que le service permet.  Pour les connexions Azure Active Directory OAuth (par exemple, Office 365 et Dynamics), nous pouvons continuer à actualiser le jeton d’accès indéfiniment.  D’autres services peuvent mettre limites combien nous pouvons utiliser un jeton sans qu’il en cours d’actualisation.  En règle générale certaines actions telles que la modification d’un mot de passe invalide tous les jetons d’accès.  

Connexions peuvent être affichées et gérées dans Azure en cliquant sur **Parcourir** et en sélectionnant **L’API connexions**.  À partir de la ressource de connexions de l’API que vous pouvez afficher, modifier, mettre à jour ou autoriser de nouveau toutes les connexions que vous avez créé.

## <a name="next-steps"></a>Étapes suivantes

- [Créer votre première application logique](../app-service-logic/app-service-logic-create-a-logic-app.md)
- [Découvrez utilise et exemples de logique applications courants](../app-service-logic/app-service-logic-examples-and-scenarios.md)
- [Prise en main actions et déclencheurs de l’intégration d’entreprise](../app-service-logic/app-service-logic-enterprise-integration-overview.md)

<!--Image References -->
[1]: ./media/connectors-overview/addAction.png
[2]: ./media/connectors-overview/configureAction.png
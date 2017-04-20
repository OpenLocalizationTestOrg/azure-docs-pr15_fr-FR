<properties 
    pageTitle="Créer une API pour les applications de logique" 
    description="Création d’une API personnalisée à utiliser avec les applications de logique" 
    authors="jeffhollan" 
    manager="dwrede" 
    editor="" 
    services="logic-apps" 
    documentationCenter=""/>

<tags
    ms.service="logic-apps"
    ms.workload="integration"
    ms.tgt_pltfrm="na"
    ms.devlang="na" 
    ms.topic="article"
    ms.date="10/18/2016"
    ms.author="jehollan"/>
    
# <a name="creating-a-custom-api-to-use-with-logic-apps"></a>Création d’une API personnalisée à utiliser avec les applications de logique

Si vous souhaitez étendre la plateforme logique applications, il existe de nombreuses façons, que vous pouvez appeler API ou des systèmes qui ne sont pas disponibles dans l’un de nos nombreux liens l’emploi.  Une de ces méthodes pour créer une application de l’API que vous pouvez appeler à partir d’un flux de travail application logique.

## <a name="helpful-tools"></a>Outils utiles

API fonctionnent mieux avec les applications logique, il est recommandé de générer un document [swagger](http://swagger.io) détaillant les opérations prises en charge et les paramètres pour votre API.  Il existe de nombreuses bibliothèques (par exemple, [Swashbuckle](https://github.com/domaindrivendev/Swashbuckle)) génèrent automatiquement le swagger pour vous.  Vous pouvez également utiliser [TRex](https://github.com/nihaue/TRex) pour aider à annoter le swagger pour fonctionner avec les applications logique (afficher les noms, types de propriétés, etc.)..  Pour certains exemples d’applications API conçues pour les applications logique, veillez à consultez notre [référentiel GitHub](http://github.com/logicappsio) ou un [blog](http://aka.ms/logicappsblog).

## <a name="actions"></a>Actions

L’action de base pour une application logique est un contrôleur qui sera accepter une demande HTTP et renvoyer une réponse (généralement 200).  Cependant, il existe différents modèles que vous pouvez suivre pour étendre actions selon vos besoins.

Par défaut le moteur application logique n’expire une demande de bout d’une minute.  Toutefois, vous pouvez avoir votre API exécuter des actions qui durent et disposez le moteur d’attendre la fin, en suivant un asynchrone ou webhook motif détaillé ci-dessous.

Pour les actions standards, écrivez simplement une méthode de demande HTTP dans votre API qui est exposé via swagger.  Vous pouvez consulter des applications API qui fonctionnent avec les applications logique dans notre [référentiel GitHub](https://github.com/logicappsio)exemples.  Voici les méthodes permettant d’accomplir certaines des modèles communs avec un lien personnalisé.

### <a name="long-running-actions---async-pattern"></a>Actions longues - modèle asynchrone

Lorsque vous exécutez une étape long ou une tâche, la première chose à faire est Vérifiez que le moteur sait que vous n’avez pas dépassé. Vous devez également communiquer avec le moteur comment il sauront lorsque vous avez terminé avec la tâche, et enfin, vous devez retourner les données pertinentes au moteur afin de pouvoir continuer avec le flux de travail. Vous pouvez effectuer que via une API en suivant le sens ci-dessous. Ces étapes sont à partir du point de vue de l’API personnalisé :

1. Lorsqu’une demande est reçue, renvoyer immédiatement une réponse (avant que le travail est effectué). Cette réponse sera un `202 ACCEPTED` réponse, avertir le moteur de vous avez obtenu les données, accepté la charge utile et traitez maintenant. La réponse 202 doit contenir les en-têtes suivants : 
 * `location`en-tête (obligatoire) : il s’agit d’une absolue chemin d’accès aux URL logique applications peut utiliser pour vérifier l’état de la tâche.
 * `retry-after`(facultatif, sera par défaut à 20 pour les actions). Il s’agit le nombre de secondes que le moteur doit attendre avant l’interrogation de l’URL d’en-tête emplacement pour vérifier l’état.

2. Quand un statut de la tâche est coché, effectuez les vérifications suivantes : 
 * Si la tâche est terminée : retourner une `200 OK` réponse, avec la charge utile de réponse.
 * Si la tâche est en cours de traitement : retourner une autre `202 ACCEPTED` réponse, avec les mêmes en-têtes en tant que la réponse initiale

Ce modèle vous permet extrêmement durée des tâches dans un thread de votre API personnalisé, mais maintenir une connexion active avec le moteur applications logique afin qu’il n’expire pas ou passez avant la date d’achèvement. Lorsque vous ajoutez cette dans votre application logique, il est important de noter que vous n’avez pas besoin de faire tout ce que dans la définition de votre pour l’application d’une logique de continuer à interroger et vérifier l’état. Dès que le moteur voit une réponse accepté 202 avec un en-tête d’emplacement valide, il sera respectent le modèle asynchrone et continuez à interroger l’en-tête emplacement jusqu'à ce qu’un 202-non est renvoyée.

Vous pouvez afficher un exemple de ce modèle dans GitHub [ici](https://github.com/jeffhollan/LogicAppsAsyncResponseSample)

### <a name="webhook-actions"></a>Actions Webhook

Au cours de votre flux de travail, vous pouvez installer l’application logique suspendre et attendre un « rappel » continuer.  Ce rappel est fourni sous la forme d’une publication HTTP.  Pour mettre en œuvre ce modèle, vous devez fournir les deux points de terminaison votre manette : s’abonner et annuler l’abonnement.

Sous « s’inscrire », l’application logique sera créer et enregistrer une URL de rappel votre API peut stocker et le rappel dans prêt comme une publication HTTP.  N’importe quel contenu/en-têtes sont passés dans l’application logique et peuvent être utilisés dans le reste du flux de travail.  Le moteur application logique appelle le point de s’abonner à exécution dès qu’il accède à cette étape.

Si l’exécution a été annulée, le moteur de l’application logique fera un appel au point de terminaison « annuler l’abonnement ».  Votre API pouvez unregister puis l’URL de rappel selon vos besoins.

Actuellement le Concepteur d’application logique non prises en charge expériences un point de terminaison webhook via swagger, afin de pour utiliser ce type d’action Ajouter l’action « Webhook » et spécifiez l’URL, les en-têtes et les corps de votre demande.  Vous pouvez utiliser la `@listCallbackUrl()` fonction de flux de travail dans un de ces champs selon vos besoins pour passer dans l’URL de rappel.

Vous pouvez afficher un exemple d’un modèle webhook dans GitHub [ici](https://github.com/jeffhollan/LogicAppTriggersExample/blob/master/LogicAppTriggers/Controllers/WebhookTriggerController.cs)

## <a name="triggers"></a>Déclencheurs

En plus des actions, vous pouvez avoir votre act API personnalisé comme un déclencheur pour une application logique.  Il existe deux modèles, que vous pouvez suivre en dessous pour déclencher la lecture d’une application logique :

### <a name="polling-triggers"></a>Déclencheurs d’interrogation

Déclencheurs d’interrogation agissent comme les actions asynchrone en cours d’exécution longue ci-dessus.  Le moteur de l’application logique appelle le point de terminaison déclencheur après qu’un certain temps écoulé (dépendant de référence (SKU), 15 secondes pour Premium, 1 minute pour Standard et 1 heure gratuite).

Si aucune donnée n’est disponible, le déclencheur renvoie une `202 ACCEPTED` réponse, avec une `location` et `retry-after` en-tête.  Toutefois, des déclencheurs, il est recommandé la `location` en-tête contient un paramètre de requête de `triggerState`.  Il s’agit d’un identificateur pour votre API de savoir quand la dernière fois déclenché l’application logique.  Si donnée n’est disponible, le déclencheur renvoie une `200 OK` réponse avec la charge utile de contenu.  Cette action va lancer l’application logique.

Par exemple, si je me suis d’interrogation pour voir si un fichier n’est pas disponible, vous pouvez créer un déclencheur d’interrogation qui serait procédez comme suit :

* Si une demande a été reçue avec aucune triggerState l’API retournerait un `202 ACCEPTED` avec un `location` en-tête qui comporte un triggerState de l’heure actuelle et un `retry-after` de 15.
* Si une demande a été reçue avec une triggerState :
 * Vérifiez si les fichiers ont été ajoutés après la date/heure triggerState. 
  * Si 1 fichier, retourner un `200 OK` réponse avec la charge utile de contenu, incrémenter la triggerState à la date/heure du fichier j’ai renvoyé et définir le `retry-after` 15.
  * S’il existe plusieurs fichiers, je peux retourner 1 à la fois avec un `200 OK`, incrémenter mon triggerState dans les `location` en-tête et ensemble `retry-after` 0.  Cela indiquera le moteur davantage de données est disponible et il demandera immédiatement sur la `location` en-tête spécifié.
  * Si aucun fichier ne sont disponibles, retournez une `202 ACCEPTED` réponse et quitter le `location` triggerState le même.  Définir `retry-after` 15.

Vous pouvez afficher un exemple d’un déclencheur d’interrogation dans GitHub [ici](https://github.com/jeffhollan/LogicAppTriggersExample/tree/master/LogicAppTriggers)

### <a name="webhook-triggers"></a>Déclencheurs Webhook

Déclencheurs Webhook agissent comme Actions Webhook ci-dessus.  Le moteur de l’application logique appelle le point de terminaison « s’inscrire » chaque fois qu’un déclencheur webhook est ajouté et enregistré.  Votre API peut enregistrer l’URL webhook et appelez-le via HTTP POST chaque fois que les données sont disponibles.  La charge utile de contenu et les en-têtes sont passés dans l’application logique exécuter.

Si un déclencheur webhook est supprimé jamais (soit l’application logique entièrement ou simplement le déclencheur webhook), le moteur de passer un appel à l’URL « annuler l’abonnement » où votre API pouvez unregister l’URL de rappel et arrêter tout processus selon vos besoins.

Actuellement le Concepteur d’application logique non prises en charge expériences d’un déclencheur webhook via swagger, afin de pour utiliser ce type d’action d’ajouter le déclencheur « Webhook » et spécifiez l’URL, les en-têtes et les corps de votre demande.  Vous pouvez utiliser la `@listCallbackUrl()` fonction de flux de travail dans un de ces champs selon vos besoins pour passer dans l’URL de rappel.

Vous pouvez afficher un exemple d’un déclencheur webhook dans GitHub [ici](https://github.com/jeffhollan/LogicAppTriggersExample/tree/master/LogicAppTriggers)
<properties
    pageTitle="Azure Engagement Mobile Web SDK API | Microsoft Azure"
    description="Les dernières mises à jour et procédures pour le Kit de développement Web pour Azure Mobile Engagement"
    services="mobile-engagement"
    documentationCenter="mobile"
    authors="piyushjo"
    manager="erikre"
    editor="" />

<tags
    ms.service="mobile-engagement"
    ms.workload="mobile"
    ms.tgt_pltfrm="web"
    ms.devlang="js"
    ms.topic="article"
    ms.date="06/07/2016"
    ms.author="piyushjo" />

# <a name="use-the-azure-mobile-engagement-api-in-a-web-application"></a>Utiliser l’API Azure Mobile Engagement dans une application web

Ce document a été ajouté au document qui vous indique comment [intégrer Engagement Mobile dans une application web](mobile-engagement-web-integrate-engagement.md). Il fournit des informations détaillées sur l’utilisation de l’API Azure Mobile Engagement pour signaler vos statistiques d’applications.

L’API Engagement Mobile est fourni par le `engagement.agent` objet. La valeur par défaut Azure Mobile Engagement Web SDK alias est `engagement`. Vous pouvez redéfinir cette alias à partir de la configuration du Kit de développement logiciel.

## <a name="mobile-engagement-concepts"></a>Concepts Engagement mobile

Les composants suivants affiner [Mobile Engagement concepts](mobile-engagement-concepts.md) courants pour la plate-forme web.

### <a name="session-and-activity"></a>`Session`et`Activity`

Si l’utilisateur reste inactif pendant plus de quelques secondes entre deux activités, séquence de l’utilisateur d’activités est fractionné en deux sessions distinctes. Ces quelques secondes sont appelés le délai d’expiration de la session.

Si votre application web ne déclare la fin des activités de l’utilisateur en lui-même (en appelant le `engagement.agent.endActivity` fonction), le serveur Mobile Engagement expire automatiquement la session d’utilisateur dans les trois minutes après la fermeture de la page d’application. Cette option est appelée le délai d’expiration de la session serveur.

### `Crash`

Rapports automatisés des exceptions JavaScript non gérées ne sont pas créés par défaut. Toutefois, vous pouvez le signaler se bloque manuellement à l’aide de la `sendCrash` fonction (voir la section sur la création de rapports se bloque).

## <a name="reporting-activities"></a>Activités de création de rapports

Création de rapports sur l’activité des utilisateurs comprend lorsqu’un utilisateur démarre une nouvelle activité et l’expiration de l’activité actuelle.

### <a name="user-starts-a-new-activity"></a>Utilisateur démarre une nouvelle activité

    engagement.agent.startActivity("MyUserActivity");

Vous devez appeler `startActivity()` chaque activité utilisateur change. Le premier appel à cette fonction démarre une nouvelle session utilisateur.

### <a name="user-ends-the-current-activity"></a>Utilisateur met fin à l’activité en cours

    engagement.agent.endActivity();

Vous devez appeler `endActivity()` au moins une fois lorsque l’utilisateur termine à leur dernière activité. Cela vous informe le Kit de développement Mobile Engagement Web que l’utilisateur est actuellement inactif et que la session de l’utilisateur doit être fermé après l’expiration de session. Si vous appelez `startActivity()` avant l’expiration de session, la session est simplement relancée.

Étant donné qu’aucun appel fiable pour lorsque la fenêtre du navigateur est fermée, il est souvent difficile, voire impossible intercepter la fin des activités de l’utilisateur à l’intérieur d’un environnement web. C’est pourquoi le serveur Mobile Engagement expire automatiquement la session d’utilisateur dans les trois minutes après la fermeture de la page d’application.

## <a name="reporting-events"></a>Création de rapports d’événements

Création de rapports sur les événements traite des événements de session et événements autonome.

### <a name="session-events"></a>Événements de session

Événements de session sont généralement utilisés pour signaler les actions exécutées par un utilisateur pendant la session de l’utilisateur.

**Exemple sans données supplémentaires :**

    loginButton.onclick = function() {
      engagement.agent.sendSessionEvent('login');
      // [...]
    }

**Exemple avec des données supplémentaires :**

    loginButton.onclick = function() {
      engagement.agent.sendSessionEvent('login', {user: 'alice'});
      // [...]
    }

### <a name="standalone-events"></a>Événements autonome

Contrairement aux événements de session, événements autonome peuvent se produire en dehors du contexte d’une session.

Pour ce faire, utilisez ``engagement.agent.sendEvent`` au lieu de ``engagement.agent.sendSessionEvent``.

## <a name="reporting-errors"></a>Signalement d’erreurs

Création de rapports sur les erreurs traite des erreurs de session et autonome.

### <a name="session-errors"></a>Erreurs de session

Erreurs de session sont généralement utilisés pour signaler les erreurs qui ont une incidence sur l’utilisateur pendant la session de l’utilisateur.

**Exemple sans données supplémentaires :**

    var validateForm = function() {
      // [...]
      if (password.length < 6) {
        engagement.agent.sendSessionError('password_too_short');
      }
      // [...]
    }

**Exemple avec des données supplémentaires :**

    var validateForm = function() {
      // [...]
      if (password.length < 6) {
        engagement.agent.sendSessionError('password_too_short', {length: 4});
      }
      // [...]
    }

### <a name="standalone-errors"></a>Erreurs autonome

Contrairement aux erreurs de session, erreurs autonome peuvent se produire en dehors du contexte d’une session.

Pour ce faire, utilisez `engagement.agent.sendError` au lieu de `engagement.agent.sendSessionError`.

## <a name="reporting-jobs"></a>Tâches de création de rapports

Création de rapports sur les travaux aborde signaler les erreurs et les événements qui se produisent pendant une tâche et la création de rapports se bloque.

**Exemple :**

Si vous voulez surveiller une requête AJAX, vous utilisez les éléments suivants :

    // [...]
    xhr.onreadystatechange = function() {
      if (xhr.readyState == 4) {
      // [...]
        engagement.agent.endJob('publish');
      }
    }
    engagement.agent.startJob('publish');
    xhr.send();
    // [...]

### <a name="reporting-errors-during-a-job"></a>Signalement d’erreurs lors d’un travail

Erreurs peuvent être associés à une tâche en cours d’exécution plutôt qu’à la session utilisateur en cours.

**Exemple :**

Si vous voulez signaler une erreur en cas d’échec une requête AJAX :

    // [...]
    xhr.onreadystatechange = function() {
      if (xhr.readyState == 4) {
        // [...]
        if (xhr.status == 0 || xhr.status >= 400) {
          engagement.agent.sendJobError('publish_xhr', 'publish', {status: xhr.status, statusText: xhr.statusText});
        }
        engagement.agent.endJob('publish');
      }
    }
    engagement.agent.startJob('publish');
    xhr.send();
    // [...]

### <a name="reporting-events-during-a-job"></a>Création de rapports d’événements au cours d’une tâche

Événements peuvent être liés à une tâche en cours d’exécution au lieu de la session active de l’utilisateur, grâce à la `engagement.agent.sendJobEvent` fonction.

Cette fonction fonctionne exactement comme `engagement.agent.sendJobError`.

### <a name="reporting-crashes"></a>Création de rapports se bloque

Utiliser la `sendCrash` fonction au rapport se bloque manuellement.

La `crashid` argument est une chaîne qui identifie le type de blocage.
La `crash` argument est généralement la trace de la pile de l’incident sous forme de chaîne.

    engagement.agent.sendCrash(crashid, crash);

## <a name="extra-parameters"></a>Paramètres supplémentaires

Vous pouvez joindre des données arbitraires à un événement, une erreur, une activité ou une tâche.

Les données peuvent être n’importe quel objet JSON (mais pas un tableau ou type primitif).

**Exemple :**

    var extras = {"video_id": 123, "ref_click": "http://foobar.com/blog"};
    engagement.agent.sendEvent("video_clicked", extras);

### <a name="limits"></a>Limites

Limites qui s’appliquent aux paramètres supplémentaires sont dans les zones d’expressions régulières pour clés, types valeur et la taille.

#### <a name="keys"></a>Touches

Chaque clé dans l’objet doit correspondre à l’expression suivante :

    ^[a-zA-Z][a-zA-Z_0-9]*

Cela signifie que les touches doivent commencer par au moins une lettre, suivi des lettres, des chiffres ou des traits de soulignement (\_).

#### <a name="values"></a>Valeurs

Valeurs sont limitées à chaîne, de nombre et types booléens.

#### <a name="size"></a>Taille

Suppléments sont limités à 1 024 caractères par appel (une fois que le Kit de développement Mobile Engagement Web chiffre dans JSON).

## <a name="reporting-application-information"></a>Informations sur l’application de création de rapports

Vous pouvez signaler manuellement le suivi d’informations (ou toute autre information spécifique à l’application) à l’aide de la `sendAppInfo()` fonction.

Notez que ces informations peuvent être envoyées par incréments. Uniquement la dernière valeur pour une clé spécifique est conservée pour un périphérique spécifique.

Comme événement bonus, vous pouvez utiliser n’importe quel objet JSON pour extraire des informations sur l’application. Notez que les tableaux ou des objets sous-adresse sont traitées comme des chaînes plats (à l’aide de sérialisation JSON).

**Exemple :**

Voici un exemple de code pour l’envoi de sexe de l’utilisateur et la date de naissance :

    var appInfos = {"birthdate":"1983-12-07","gender":"female"};
    engagement.agent.sendAppInfo(appInfos);

### <a name="limits"></a>Limites

Limites qui s’appliquent aux informations sur l’application sont dans les zones d’expressions régulières pour les clés et taille.

#### <a name="keys"></a>Touches

Chaque clé dans l’objet doit correspondre à l’expression suivante :

    ^[a-zA-Z][a-zA-Z_0-9]*

Cela signifie que les touches doivent commencer par au moins une lettre, suivi des lettres, des chiffres ou des traits de soulignement (\_).

#### <a name="size"></a>Taille

Informations sur l’application sont limitées à 1 024 caractères par appel (une fois que le Kit de développement Mobile Engagement Web chiffre dans JSON).

Dans l’exemple précédent, le JSON envoyé au serveur est 44 caractères :

    {"birthdate":"1983-12-07","gender":"female"}

<properties
    pageTitle="Synchronisation de données hors connexion dans les applications mobiles Azure | Microsoft Azure"
    description="Référence conceptuelle et présentation de la fonctionnalité de synchronisation des données en mode hors connexion pour les applications Mobile Azure"
    documentationCenter="windows"
    authors="adrianhall"
    manager="dwrede"
    editor=""
    services="app-service\mobile"/>

<tags
    ms.service="app-service-mobile"
    ms.workload="mobile"
    ms.tgt_pltfrm="na"
    ms.devlang="multiple"
    ms.topic="article"
    ms.date="10/01/2016"
    ms.author="adrianha"/>

# <a name="offline-data-sync-in-azure-mobile-apps"></a>Synchronisation de données hors connexion dans les applications mobiles Azure

## <a name="what-is-offline-data-sync"></a>Quelle est la synchronisation de données hors connexion ?

Synchronisation des données en mode hors connexion est un client et serveur fonctionnalité Kit de développement des applications mobiles Azure qui facilite aux développeurs de créer des applications qui fonctionne sans une connexion réseau.

Lorsque votre application est en mode hors connexion, les utilisateurs peuvent toujours créer et modifier des données, qui seront enregistrées dans un magasin local. Lorsque l’application est en ligne, il peut synchroniser les modifications locales avec votre application Mobile Azure le serveur principal. La fonctionnalité prend également en charge pour détecter les conflits lors de la même enregistrement est modifié sur le client et le serveur principal. Les conflits peuvent ensuite être traitées sur le serveur ou le client.

Synchronisation hors connexion compte de nombreux avantages :

* Améliorer la réactivité de l’application en mettant en cache les données server localement sur l’appareil
* Créer des applications fiables restent utiles lorsqu’il existe des problèmes réseau
* Permettre aux utilisateurs finaux de créer et modifier des données même lorsqu’il n’y a aucun accès réseau, prenant en charge les scénarios avec peu ou pas de connectivité
* Synchroniser des données entre plusieurs appareils et détecter les conflits lors de l’enregistrement même modifié par deux appareils
* Limiter l’utilisation du réseau sur les réseaux de latence élevée ou limitées

Les didacticiels suivants montrent comment ajouter la synchronisation hors connexion à vos clients mobiles à l’aide d’applications Mobile Azure :

* [Android : Activer la synchronisation hors connexion]
* [iOS : activer la synchronisation hors connexion]
* [IOS Xamarin : activer la synchronisation hors connexion]
* [Xamarin Android : Activer la synchronisation hors connexion]
* [Xamarin.Forms : Activer la synchronisation hors connexion](app-service-mobile-xamarin-forms-get-started-offline-data.md)
* [Plateforme Windows universel : Activer la synchronisation hors connexion]

## <a name="what-is-a-sync-table"></a>Qu’est une table de synchronisation ?

Pour accéder au point de terminaison « / tables », le client Mobile Azure SDK fournissent des interfaces telles que `IMobileServiceTable` (client .NET SDK) ou `MSTable` (iOS client). Ces API se connecter directement à la version serveur Azure Mobile application et échouera si le périphérique client ne dispose pas d’une connexion réseau.

Pour prendre en charge utilisation hors connexion, votre application doit à la place utiliser la *table de synchronisation* API, telles que `IMobileServiceSyncTable` (client .NET SDK) ou `MSSyncTable` (iOS client). Les mêmes opérations (créer, lire, mettre à jour, supprimer) fonctionnent sur table de synchronisation API, sauf maintenant qu’ils seront lues ou écrire dans un *magasin local*. Avant de pouvoir effectuer les opérations de table de synchronisation, le magasin local doit être initialisé.

## <a name="what-is-a-local-store"></a>Qu’est un magasin local ?

Un magasin local est la couche de persistance de données sur le périphérique client. Kits de développement logiciel client Azure Mobile applications fournissent une implémentation de magasin local par défaut. Sous Windows, Xamarin et Android, il est basé sur SQLite ; sous iOS, il est basé sur les données de base.

Pour utiliser l’implémentation SQLite sur Windows Phone ou Windows Store 8.1, vous devez installer une extension SQLite. Pour plus d’informations, voir [universel plateforme Windows : activer la synchronisation hors connexion]. Android et iOS sont fournis avec une version de SQLite dans le système d’exploitation périphérique lui-même, afin qu’il n’est pas nécessaire de faire référence à votre propre version de SQLite.

Les développeurs peuvent également implémenter leur propre magasin local. Par exemple, si vous souhaitez stocker les données dans un format chiffré sur le client mobile, vous pouvez définir un magasin local qui utilise SQLCipher pour le chiffrement.

## <a name="what-is-a-sync-context"></a>Qu’est un contexte de synchronisation ?

Un *contexte de synchronisation* est associé à un objet de client mobile (tel que `IMobileServiceClient` ou `MSClient`) et effectue le suivi des modifications effectuées avec les tables de synchronisation. Le contexte de synchronisation conserve une *file d’attente opération* qui le conserve une liste d’opérations CUD (créer, mettre à jour, supprimer) qui sera envoyé ultérieurement sur le serveur.

Un magasin local est associé au contexte de synchronisation à l’aide d’une méthode d’initialisation tel que `IMobileServicesSyncContext.InitializeAsync(localstore)` dans le [client .NET SDK].

## <a name="how-sync-works"></a>Comment en mode hors connexion fonctionnement de la synchronisation

Lorsque vous utilisez les tables de synchronisation, votre code client contrôles lorsque modifications locales sont synchronisées avec le serveur principal une application Mobile Azure. Rien n’est envoyé sur le serveur principal jusqu'à ce qu’un appel pour *Publier* des modifications local. De même, le magasin local est rempli avec les nouvelles données uniquement lorsqu’il y a un appel pour *Extraire* des données.

* **Push**: Push est une opération dans le contexte de synchronisation et envoie toutes les modifications CUD depuis le dernier push. Notez qu’il n’est pas possible d’envoyer uniquement les modifications d’une table individuelle, car sinon opérations a pu être envoyées de manière désordonnées. Push exécute une série d’appels reste de votre serveur principal application Mobile Azure, qui à son tour modifiera votre base de données de serveur.

* **Extraire**: extraire est effectué sur une base par table et peut être personnalisée à l’aide d’une requête pour récupérer uniquement un sous-ensemble des données du serveur. Kits de développement logiciel client Mobile Azure puis insérez les données résultantes dans le magasin local.

* **Pousse implicite**: si une extraction est exécutée sur une table qui comporte en attente de mises à jour locales, l’extraction exécutera tout d’abord un push sur le contexte de synchronisation. Cela permet de minimiser les conflits entre les modifications sont déjà en file d’attente et de nouvelles données à partir du serveur.

* **Synchronisation incrémentielles**: le premier paramètre de l’opération d’extraction est un *nom de la requête* qui est utilisé uniquement dans le client. Si vous utilisez un nom de requête non null, le Kit de développement Mobile Azure effectue une *synchronisation incrémentielle*.
  Chaque fois qu’une opération d’extraction renvoie un jeu de résultats, la dernière `updatedAt` horodatage à partir de ce jeu de résultats est stocké dans les tables système local SDK. Opérations d’extraction ultérieures récupère uniquement les enregistrements après cet horodatage.

  Pour utiliser la synchronisation incrémentielle, votre serveur doit renvoyer significatif `updatedAt` les valeurs et doit également prendre en charge le tri par ce champ. Toutefois, étant donné que le Kit de développement ajoute sa propre trier sur le champ updatedAt, vous ne pouvez pas utiliser une requête d’extraction qui possède son propre `$orderBy$` clause.

  Le nom de la requête peut être toute chaîne choisie, mais il doit être unique pour chaque requête logique dans votre application.
  Dans le cas contraire, opérations d’extraction différents risquez d’écraser le même horodatage synchronisation incrémentielles et vos requêtes peuvent retourner des résultats incorrects.

  Si la requête comporte un paramètre, permettent de créer un nom de requête unique consiste à incorporer la valeur de paramètre.
  Par exemple, si vous filtrez sur le nom d’utilisateur, votre nom de la requête peut être comme suit (en c#) :

        await todoTable.PullAsync("todoItems" + userid,
            syncTable.Where(u => u.UserId == userid));

  Si vous souhaitez cesser d’utiliser la synchronisation incrémentielle, passez `null` en tant que l’ID de requête. Dans ce cas, tous les enregistrements seront récupérées sur chaque appel à `PullAsync`, qui est potentiellement inefficace.

* **Purge**: vous pouvez effacer le contenu du magasin local en utilisant `IMobileServiceSyncTable.PurgeAsync`.
  Cela peut être nécessaire si vous avez des données obsolètes dans la base de données client, ou si vous souhaitez ignorer toutes les modifications en attente.

  Une purge efface un tableau à partir du magasin local. S’il existe des opérations en attente de la synchronisation avec la base de données, la purge lève une exception, à moins que le paramètre *Forcer purge* est défini.

  Exemple de données obsolètes sur le client, supposons que dans l’exemple « liste todo », appareil1 extrait uniquement les éléments qui ne sont pas terminés. Ensuite, un todoitem « Acheter laitiers » est marquée terminée sur le serveur par un autre périphérique. Toutefois, appareil1 disposeront toujours la todoitem « Acheter laitiers » dans le magasin local, car il est tirant uniquement les éléments qui ne sont pas marquées comme étant achevées. Une purge efface cet élément obsolète.

## <a name="next-steps"></a>Étapes suivantes

* [iOS : activer la synchronisation hors connexion]
* [IOS Xamarin : activer la synchronisation hors connexion]
* [Xamarin Android : Activer la synchronisation hors connexion]
* [Plateforme Windows universel : Activer la synchronisation hors connexion]

<!-- Links -->
[Client .NET SDK]: app-service-mobile-dotnet-how-to-use-client-library.md
[Android : Activer la synchronisation hors connexion]: app-service-mobile-android-get-started-offline-data.md
[iOS : activer la synchronisation hors connexion]: app-service-mobile-ios-get-started-offline-data.md
[IOS Xamarin : activer la synchronisation hors connexion]: app-service-mobile-xamarin-ios-get-started-offline-data.md
[Xamarin Android : Activer la synchronisation hors connexion]: app-service-mobile-xamarin-ios-get-started-offline-data.md
[Plateforme Windows universel : Activer la synchronisation hors connexion]: app-service-mobile-windows-store-dotnet-get-started-offline-data.md

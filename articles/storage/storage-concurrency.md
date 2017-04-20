<properties
    pageTitle="Gestion des accès concurrentiels au stockage de Microsoft Azure"
    description="Comment gérer les accès concurrentiels pour les services Blob, file d’attente, Table et fichier"
    services="storage"
    documentationCenter=""
    authors="jasonnewyork"
    manager="tadb"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="10/18/2016"
    ms.author="jahogg"/>

# <a name="managing-concurrency-in-microsoft-azure-storage"></a>Gestion des accès concurrentiels au stockage de Microsoft Azure

## <a name="overview"></a>Vue d’ensemble

Les applications modernes Internet en fonction disposent généralement plusieurs utilisateurs l’affichage et mise à jour des données simultanément. Cette fonctionnalité nécessite les développeurs d’applications de réfléchir avec soin fournir une expérience prévisible aux utilisateurs finaux, en particulier pour les scénarios où plusieurs utilisateurs peuvent mettre à jour les mêmes données. Il existe trois stratégies d’accès concurrentiels de données principale développeurs généralement tient compte :  


1.  Simultané – une application effectuer de qu'une mise à jour dans le cadre de sa mise à jour vérifie si les données ont changé depuis la demande dernière lecture ces données. Par exemple, si deux utilisateurs affichant une page wiki effectuent une mise à jour à la même page puis la plateforme wiki devez vous assurer que la seconde mise à jour ne remplace pas la première mise à jour – et que les deux utilisateurs comprennent si leur mise à jour a réussi ou non. Cette stratégie est souvent utilisée dans les applications web.
2.  Concurrence pessimiste – une application vous recherchez pour effectuer une mise à jour prendra un verrou sur un objet empêcher les autres utilisateurs de mettre à jour les données jusqu'à ce que le verrou est libéré. Par exemple, dans un scénario de réplication de données maître/esclave où seul le masque effectue les mises à jour le masque des généralement contiendra un accès exclusif pour une longue période de temps sur les données afin de que personne d’autre ne peut mettre à jour.
3.  Dernière gagne – une approche qui permet les opérations de mise à jour continuer sans vérifier si une autre application a mis à jour les données depuis la demande de tout d’abord lire les données. Cette stratégie (ou manque d’une stratégie formel) est généralement utilisé dans lequel les données sont réparties de façon à ce qu’il n’existe aucune probabilité que plusieurs utilisateurs n’accèdent les mêmes données. Il peut également être utile dans lequel des flux de données courtes sont traités.  

Cet article fournit une vue d’ensemble de la façon dont la plateforme de stockage Azure simplifie le développement grâce à la première classe prise en charge pour les trois de ces stratégies d’accès concurrentiels au.  

## <a name="azure-storage--simplifies-cloud-development"></a>Stockage Azure – simplifie le développement en nuage
Le service de stockage Azure prend en charge tous les trois stratégies, bien qu’il soit distinctive dans sa capacité à fournir la prise en charge complète pour concurrence optimiste et pessimiste parce qu’il a été conçu pour utiliser un modèle de cohérence fort qui garantit que lorsque le service de stockage validée une insertion de données ou mise à jour tous les autres accède à pour que les données verront la dernière mise à jour. Plateformes de stockage qui utilisent un modèle de cohérence éventuelle ont un retard entre lorsqu’une écriture est effectuée par un seul utilisateur lorsque les données mises à jour peuvent être vu par d’autres utilisateurs ainsi, ce qui complique développement d’applications clientes afin d’éviter les incohérences d’affecter les utilisateurs finaux.  

Outre la sélection d’une stratégie d’accès concurrentiels au approprié les développeurs doivent également tenir compte des comment une plateforme de stockage isole modifications – en particulier les modifications au même objet sur transactions. Le service de stockage Azure utilise isolement de capture instantanée pour permettre les opérations de lecture pour se produire en même temps que les opérations d’écriture dans une même partition. Contrairement à d’autres niveaux d’isolement, isolement de capture instantanée garantit que toutes les lectures afficher un instantané cohérent des données pendant que mises à jour – essentiellement en renvoyant les dernières valeurs validées lors de la mise à jour transaction est en cours de traitement.  

## <a name="managing-concurrency-in-blob-storage"></a>Gestion des accès concurrentiels au stockage d’objets Blob
Vous pouvez choisir d’utiliser des modèles de concurrence optimiste ou pessimiste pour gérer l’accès à des conteneurs dans le service d’objets blob et des objets BLOB. Si vous ne spécifiez pas explicitement une stratégie le dernier écrit gagne est la valeur par défaut.  

### <a name="optimistic-concurrency-for-blobs-and-containers"></a>Simultané pour des conteneurs et des objets BLOB  

Le service de stockage assigne un identificateur à chaque objet stocké. Cet identifiant est mis à jour chaque fois qu’une opération de mise à jour est exécutée sur un objet. L’identificateur est renvoyée au client dans le cadre d’une réponse HTTP GET à l’aide de l’en-tête ETag (balise entité) qui est défini dans le protocole HTTP. Un utilisateur qui effectue une mise à jour sur ce type d’objet peut envoyer dans l’ETag d’origine avec un en-tête conditionnel pour vous assurer qu’une mise à jour se produira uniquement si une condition est remplie : dans ce cas la condition est un en-tête « If-Match » qui nécessite le Service de stockage garantir la valeur de l’ETag spécifié dans la demande de mise à jour est identique à celui stocké dans le Service de stockage.  

Le plan de ce processus se présente comme suit :  

1.  Récupérer un blob à partir du service de stockage, la réponse inclut une valeur HTTP ETag en-tête qui identifie la version actuelle de l’objet dans le service de stockage.
2.  Lorsque vous mettez à jour le blob, incluez la valeur ETag que vous avez reçu à l’étape 1 dans l’en-tête conditionnelle **If-Match** de la requête que vous envoyez au service.
3.  Le service compare la valeur ETag dans la demande avec la valeur ETag actuelle du blob.
4.  Si la valeur ETag actuelle du blob est différente de celle l’ETag dans l’en-tête conditionnelle **If-Match** dans la demande, le service renvoie une erreur 412 au client. Cela indique au client qu’un autre processus a mis à jour le blob étant donné que le client a récupéré.
5.  Si la valeur ETag actuelle du blob est la même version que l’ETag dans l’en-tête conditionnelle **If-Match** dans la demande, le service effectue l’opération demandée et met à jour la valeur ETag actuelle du blob pour afficher qu’il a créé une nouvelle version.  

L’extrait c# suivant (à l’aide de la bibliothèque de stockage Client 4.2.0) montre un simple exemple comment créer un **If-Match AccessCondition** basé sur la valeur ETag qui est accessible à partir des propriétés d’un objet blob qui a été précédemment extraites ou insérées. Il utilise ensuite l’objet **AccessCondition** lorsqu’il le blob de mise à jour : l’objet **AccessCondition** ajoute l’en-tête **If-Match** à la demande. Si un autre processus a mis à jour le blob, le service blob renvoie un message d’état HTTP 412 (préalable a échoué). Vous pouvez télécharger l’exemple complet ici : [Gestion des accès concurrentiels à l’aide de stockage Azure](http://code.msdn.microsoft.com/Managing-Concurrency-using-56018114).  

    // Retrieve the ETag from the newly created blob
    // Etag is already populated as UploadText should cause a PUT Blob call
    // to storage blob service which returns the etag in response.
    string orignalETag = blockBlob.Properties.ETag;

    // This code simulates an update by a third party.
    string helloText = "Blob updated by a third party.";

    // No etag, provided so orignal blob is overwritten (thus generating a new etag)
    blockBlob.UploadText(helloText);
    Console.WriteLine("Blob updated. Updated ETag = {0}",
    blockBlob.Properties.ETag);

    // Now try to update the blob using the orignal ETag provided when the blob was created
    try
    {
        Console.WriteLine("Trying to update blob using orignal etag to generate if-match access condition");
        blockBlob.UploadText(helloText,accessCondition:
        AccessCondition.GenerateIfMatchCondition(orignalETag));
    }
    catch (StorageException ex)
    {
        if (ex.RequestInformation.HttpStatusCode == (int)HttpStatusCode.PreconditionFailed)
        {
            Console.WriteLine("Precondition failure as expected. Blob's orignal etag no longer matches");
            // TODO: client can decide on how it wants to handle the 3rd party updated content.
        }
        else
            throw;
    }  

Le Service de stockage inclut également prise en charge des en-têtes conditionnelle supplémentaires telles que **Si modifiée depuis**, **Si non modifiés depuis** et **If-None-Match** ainsi que leurs combinaisons. Pour plus d’informations consultez [Spécifiant conditionnelle en-têtes pour les opérations de Service Blob](http://msdn.microsoft.com/library/azure/dd179371.aspx) sur MSDN.  

Le tableau suivant récapitule les opérations de conteneur qui acceptent les en-têtes conditionnelle comme **If-Match** dans la demande et qui retourne une valeur ETag dans la réponse.  

| Opération                | Renvoie la valeur ETag conteneur | Accepte les en-têtes conditionnelle |
|:-------------------------|:-----------------------------|:----------------------------|
| Créer le conteneur         | Oui                          | N°                          |
| Obtenir les propriétés du conteneur | Oui                          | N°                          |
| Obtenir des métadonnées conteneur   | Oui                          | N°                          |
| Définir les métadonnées de conteneur   | Oui                          | Oui                         |
| Obtenir le conteneur et        | Oui                          | N°                          |
| Définir et conteneur        | Oui                          | Oui (*)                     |
| Supprimer le conteneur         | N°                           | Oui                         |
| Conteneur location          | Oui                          | Oui                         |
| Liste des objets BLOB               | N°                           | N°                          |

(*) Les autorisations définies par SetContainerACL sont mis en cache et mises à jour de ces autorisations prennent 30 secondes de se propager délai pendant lequel mises à jour ne sont pas nécessairement cohérentes.  

Le tableau suivant récapitule les opérations d’objets blob qui acceptent les en-têtes conditionnelle comme **If-Match** dans la demande et qui retourne une valeur ETag dans la réponse.

| Opération           | Renvoie la valeur ETag | Accepte les en-têtes conditionnelle           |
|:--------------------|:-------------------|:--------------------------------------|
| Placer des objets Blob            | Oui                | Oui                                   |
| Obtenir des objets Blob            | Oui                | Oui                                   |
| Obtenir les propriétés d’objets Blob | Oui                | Oui                                   |
| Définir les propriétés d’objets Blob | Oui                | Oui                                   |
| Obtenir des métadonnées d’objets Blob   | Oui                | Oui                                   |
| Définir les métadonnées d’objets Blob   | Oui                | Oui                                   |
| Objets Blob Location (*)      | Oui                | Oui                                   |
| Instantané Blob       | Oui                | Oui                                   |
| Copier des objets Blob           | Oui                | Oui (pour les objets blob source et de destination) |
| Abandonner Blob de copie     | N°                 | N°                                    |
| Supprimer des objets Blob         | N°                 | Oui                                   |
| Placer le bloc           | N°                 | N°                                    |
| Placer la liste de blocs      | Oui                | Oui                                   |
| Obtenir la liste de blocs      | Oui                | N°                                    |
| Placer la Page            | Oui                | Oui                                   |
| Obtenir les plages de pages     | Oui                | Oui                                   |


(*) Location Blob ne modifie pas l’ETag sur un blob.  

### <a name="pessimistic-concurrency-for-blobs"></a>Concurrence pessimiste pour les objets BLOB
Pour verrouiller un blob en mode exclusif, vous pouvez obtenir un [location](http://msdn.microsoft.com/library/azure/ee691972.aspx) dessus. Lorsque vous achetez une location, que vous spécifiez pendant combien de temps, vous devez la location : il peut s’agir d’entre 15 à 60 secondes ou infini qui s’élève à un accès exclusif. Vous pouvez renouveler une location déterminée étendre, et vous pouvez libérer tout location lorsque vous avez terminé avec lui. Le service blob automatiquement mises à jour de location déterminée lorsqu’ils expirent.  

Location activer les stratégies de synchronisation différent à prendre en charge, y compris écriture exclusives / partagé écriture exclusive, lecture / exclusif lire et partagés écriture / exclusive en lecture. Lorsqu’il existe une location le service de stockage applique exclusif écrit (placer, définir et supprimer des opérations) toutefois garantissant exclusivité pour les opérations de lecture nécessite le développeur de s’assurer que toutes les applications clientes utilisent un code location et ce qu’un seul client à la fois comporte un ID de location valide. Opérations de lecture qui ne contiennent pas un résultat de l’ID location dans lit partagé.  

L’extrait de code c# suivant montre un exemple d’acquisition une location exclusif pendant 30 secondes dans un blob, mise à jour le contenu de l’objet blob et puis relâché la location. S’il existe déjà une location valide sur l’objets blob lorsque vous essayez d’acquérir de nouvelles perspectives, le service blob renvoie un résultat de l’état « Conflit HTTP (409) ». L’extrait de code ci-dessous utilise un objet **AccessCondition** pour encapsuler les informations location lorsqu’elle effectue une requête pour mettre à jour le blob dans le service de stockage.  Vous pouvez télécharger l’exemple complet ici : [Gestion des accès concurrentiels à l’aide de stockage Azure](http://code.msdn.microsoft.com/Managing-Concurrency-using-56018114).

    // Acquire lease for 15 seconds
    string lease = blockBlob.AcquireLease(TimeSpan.FromSeconds(15), null);
    Console.WriteLine("Blob lease acquired. Lease = {0}", lease);

    // Update blob using lease. This operation will succeed
    const string helloText = "Blob updated";
    var accessCondition = AccessCondition.GenerateLeaseCondition(lease);
    blockBlob.UploadText(helloText, accessCondition: accessCondition);
    Console.WriteLine("Blob updated using an exclusive lease");

    //Simulate third party update to blob without lease
    try
    {
        // Below operation will fail as no valid lease provided
        Console.WriteLine("Trying to update blob without valid lease");
        blockBlob.UploadText("Update without lease, will fail");
    }
    catch (StorageException ex)
    {
        if (ex.RequestInformation.HttpStatusCode == (int)HttpStatusCode.PreconditionFailed)
            Console.WriteLine("Precondition failure as expected. Blob's lease does not match");
        else
            throw;
    }  

Si vous essayez d’une opération d’écriture dans un blob spécialisée sans passer l’ID de location, la requête échoue avec une erreur 412. Notez que si la location expire avant d’appeler la méthode **UploadText** , mais vous passez toujours l’ID de location, la requête échoue également avec une erreur **412** . Pour plus d’informations sur la gestion des délais d’expiration location et les ID de location, consultez la documentation de reste [Location Blob](http://msdn.microsoft.com/library/azure/ee691972.aspx) .  

Les opérations suivantes blob peuvent permet de gérer l’accès simultané pessimiste location :  


-   Placer des objets Blob
-   Obtenir des objets Blob
-   Obtenir les propriétés d’objets Blob
-   Définir les propriétés d’objets Blob
-   Obtenir des métadonnées d’objets Blob
-   Définir les métadonnées d’objets Blob
-   Supprimer des objets Blob
-   Placer le bloc
-   Placer la liste de blocs
-   Obtenir la liste de blocs
-   Placer la Page
-   Obtenir les plages de pages
-   Instantané Blob - code location facultatif si une location existe
-   Copier des objets Blob - location ID obligatoire si une location existe sur le blob de destination
-   Abandon copie Blob - code location obligatoire si une location infinie existe sur le blob de destination
-   Location Blob  

### <a name="pessimistic-concurrency-for-containers"></a>Concurrence pessimiste des conteneurs
Location sur conteneurs activer les stratégies de synchronisation même être pris en charge que sur des objets BLOB (exclusif écrire / partagé écriture exclusive, lecture / exclusif lire et partagés écriture / exclusive en lecture) toutefois à la différence des objets BLOB le service de stockage applique uniquement exclusivité sur les opérations de suppression. Pour supprimer un conteneur avec un location active, un client doit inclure l’ID de location active avec la demande de suppression. Toutes les autres opérations conteneur réussi sur un conteneur spécialisée sans y compris l’ID de location auquel cas ils sont partagés opérations. Si exclusivité de mise à jour (place ou ensemble) ou les opérations de lecture est requise puis les développeurs doivent s’assurer que tous les clients utilisent un code location et que seul un client à la fois possède un ID de location valide.  

Les opérations suivantes conteneur peuvent permet de gérer l’accès simultané pessimiste location :  

-   Supprimer le conteneur
-   Obtenir les propriétés du conteneur
-   Obtenir des métadonnées conteneur
-   Définir les métadonnées de conteneur
-   Obtenir le conteneur et
-   Définir et conteneur
-   Conteneur location  

Pour plus d’informations, voir :  

- [Si vous spécifiez en-têtes conditionnelle pour les opérations de Service d’objets Blob](http://msdn.microsoft.com/library/azure/dd179371.aspx)
- [Conteneur location](http://msdn.microsoft.com/library/azure/jj159103.aspx)
- [Location Blob](http://msdn.microsoft.com/library/azure/ee691972.aspx)

## <a name="managing-concurrency-in-the-table-service"></a>Gestion des accès concurrentiels dans le Service de tableau
Le service de tableau utilise optimiste concurrence vérifie que le comportement par défaut lorsque vous travaillez avec des entités, contrairement au service blob dans laquelle vous devez choisir explicitement pour effectuer des vérifications simultané. Autres la différence entre le tableau et blob les services est que vous pouvez uniquement gérer le comportement de la concurrence d’entités alors que le service blob vous pouvez de gérer la concurrence des conteneurs et des objets BLOB.  

Pour utiliser simultané et vérifiez si un autre processus modifié une entité dans la mesure où vous récupérées à partir du service de stockage de table, vous pouvez utiliser la valeur ETag que vous recevez lorsque le service de tableau retourne une entité. Le plan de ce processus se présente comme suit :  

1.  Récupérer une entité à partir du service de stockage de table, la réponse inclut une valeur ETag qui identifie l’identificateur actuel associée à cette entité dans le service de stockage.
2.  Lorsque vous mettez à jour l’entité, incluez la valeur ETag que vous avez reçu à l’étape 1 dans l’en-tête **If-Match** obligatoire de la requête que vous envoyez au service.
3.  Le service compare la valeur ETag dans la demande avec la valeur ETag actuelle de l’entité.
4.  Si la valeur ETag actuelle de l’entité est différente de celle l’ETag dans l’en-tête **If-Match** obligatoire dans la demande, le service renvoie une erreur 412 au client. Cela indique au client qu’un autre processus a mis à jour l’entité dans la mesure où le client a récupéré.
5.  Si la valeur ETag actuelle de l’entité est identique à l’ETag dans l’en-tête **If-Match** obligatoire dans la demande ou l’en-tête **If-Match** contient le caractère générique (*), le service effectue l’opération demandée et met à jour la valeur ETag actuelle de l’entité à afficher qu’il a été mis à jour.  

Notez que contrairement au service blob, le service de tableau nécessite le client inclure un en-tête **If-Match** de demandes de mise à jour. Toutefois, il est possible de forcer un non conditionnel mettre à jour (dernière writer wins stratégie) et ignorer les contrôles d’accès concurrentiels si le client définit l’en-tête **If-Match** sur le caractère générique (*) dans la demande.  

L’extrait de code c# suivant montre une entité client qui a été précédemment créée ou récupérée ayant leur adresse de messagerie mis à jour. Initial insérer ou récupérer opération stocke la valeur ETag dans l’objet client et étant donné que l’exemple utilise la même instance d’objet lorsqu’il est exécuté l’opération de remplacement, il envoie automatiquement la valeur ETag dans le service de tableau, l’activation du service vérifier les violations simultanées. Si un autre processus a mis à jour l’entité de stockage de table, le service renvoie un message d’état HTTP 412 (préalable a échoué).  Vous pouvez télécharger l’exemple complet ici : [Gestion des accès concurrentiels à l’aide de stockage Azure](http://code.msdn.microsoft.com/Managing-Concurrency-using-56018114).

    try
    {
        customer.Email = "updatedEmail@contoso.org";
        TableOperation replaceCustomer = TableOperation.Replace(customer);
        customerTable.Execute(replaceCustomer);
        Console.WriteLine("Replace operation succeeded.");
    }
    catch (StorageException ex)
    {
        if (ex.RequestInformation.HttpStatusCode == 412)
            Console.WriteLine("Optimistic concurrency violation – entity has changed since it was retrieved.");
        else
            throw;
    }  

Pour désactiver explicitement le contrôle de concurrence, vous devez définir la propriété **ETag** de l’objet **employé** à « * » avant d’exécuter l’opération de remplacement.  

    customer.ETag = "*";  

Le tableau suivant résume la façon dont les opérations entité tableau utilisent ETag :

| Opération                | Renvoie la valeur ETag | Nécessite l’en-tête de requête If-Match |
|:-------------------------|:-------------------|:---------------------------------|
| Interroger des entités           | Oui                | N°                               |
| Insérer entité            | Oui                | N°                               |
| Mettre à jour entité            | Oui                | Oui                              |
| Fusionner entité             | Oui                | Oui                              |
| Supprimer une entité            | N°                 | Oui                              |
| Insérer ou remplacer entité | Oui                | N°                               |
| Insertion ou l’entité de fusion   | Oui                | N°                               |

Notez que les opérations **Insertion ou remplacer une entité** et **Insertion ou entité fusionner** *pas* effectuer les contrôles d’accès concurrentiels, car ils ne pas envoient une valeur ETag au service de tableau.  

En règle générale les développeurs à l’aide de tables doivent s’appuient sur simultané lorsque vous développez des applications scalable. Si le verrouillage pessimiste est nécessaire, les développeurs une approche peuvent prendre lorsque l’accès à des Tables est attribuer un blob désigné pour chaque table et tenter d’effectuer une location sur le blob avant d’utiliser dans le tableau. Cette approche nécessite-t-il l’application pour assurer tous les chemins d’accès aux données obtiennent la location avant d’exploitation sur la table. Notez également que la durée minimale est de 15 secondes qui requiert une attention particulière pour extensibilité élevées.  

Pour plus d’informations, voir :  

- [Opérations sur les entités](http://msdn.microsoft.com/library/azure/dd179375.aspx)  

## <a name="managing-concurrency-in-the-queue-service"></a>Gestion des accès concurrentiels dans le Service de file d’attente
Un scénario que dans quel concurrence est un problème dans le service de file d’attente est l’endroit où plusieurs clients récupérez des messages à partir d’une file d’attente. Lorsqu’un message est extrait dans la file d’attente, la réponse inclut le message et une valeur de la réception pop, qui est requis pour supprimer le message. Le message n’est pas automatiquement supprimé de la file d’attente, mais après que qu’elle a été récupérée, il n’est pas visible à d’autres clients pour l’intervalle de temps spécifié par le paramètre visibilitytimeout. Le client qui Récupère le message est censé supprimer le message une fois qu’elle a été traitée, avant le délai spécifié par le TimeNextVisible élément de la réponse, qui est calculée selon la valeur du paramètre visibilitytimeout. La valeur de visibilitytimeout est ajoutée à l’heure à laquelle le message est extrait pour déterminer la valeur de TimeNextVisible.  

Le service de file d’attente n’est pas prise en charge de la concurrence optimiste ou pessimiste et pendant ce clients raison traitement des messages récupérées à partir d’une file d’attente, nous recommandons d’une manière idempotent les messages sont traités. Une stratégie de wins writer dernière est utilisée pour les opérations de mise à jour tels que SetQueueServiceProperties, SetQueueMetaData, SetQueueACL et UpdateMessage.  

Pour plus d’informations, voir :  

- [API REST de Service file d’attente](http://msdn.microsoft.com/library/azure/dd179363.aspx)
- [Recevoir les Messages](http://msdn.microsoft.com/library/azure/dd179474.aspx)  

## <a name="managing-concurrency-in-the-file-service"></a>Gestion des accès concurrentiels dans le Service de fichiers
Le service de fichiers sont accessibles à l’aide des points de terminaison autre protocole deux – PME et reste. Le service REST n’est pas prise en charge pour le verrouillage optimiste ou verrouillage pessimiste et toutes les mises à jour suivent une stratégie de wins writer dernière. Clients PME qui monter partages de fichiers peuvent exploiter les mécanismes de verrouillage du système de fichiers pour gérer l’accès aux fichiers partagés, y compris la possibilité d’effectuer le verrouillage pessimiste. Lorsqu’un client PME ouvre un fichier, elle indique l’accès au fichier et le partager en mode. Définition d’une option d’accès aux fichiers de « Rédiger » ou « En lecture/écriture » ainsi qu’un mode de partage de fichiers de « Aucun » entraînera le fichier est verrouillé par un client PME jusqu'à ce que le fichier est fermé. Si la tentative d’opération reste sur un fichier dont un client PME comporte le fichier est verrouillé le service reste renverra code d’état 409 (conflit) avec le code d’erreur SharingViolation.  

Lorsqu’un client PME ouvre un fichier à supprimer, il marque le fichier comme étant en attente de suppression jusqu'à ce que tous les autres clients PME sont fermées poignées ouvertes sur ce fichier. Pendant un fichier est marqué comme suppression en attente, n’importe quelle opération reste sur ce fichier retourne code d’état 409 (conflit) avec le code d’erreur SMBDeletePending. Code d’état 404 (introuvable) n’est renvoyée car il est possible que le client supprimer l’indicateur de suppression en attente avant de fermer le fichier. En d’autres termes, code d’état 404 (introuvable) est prévu uniquement lorsque le fichier a été supprimé. Notez que lorsque le fichier est dans une PME en attente de supprimer l’état, il n’être pas inclus dans les résultats de la liste des fichiers. Notez également que les opérations reste supprimer le fichier et supprimer le répertoire reste engageons atomique et n’entraînent pas d’en attente de supprimer l’état.  

Pour plus d’informations, voir :  

- [Gestion de fichier verrouille](http://msdn.microsoft.com/library/azure/dn194265.aspx)  

## <a name="summary-and-next-steps"></a>Résumé et étapes suivantes
Le service de stockage de Microsoft Azure a été conçu pour répondre aux besoins des applications en ligne plus complexes sans obliger les développeurs à compromettre ou concevoir de nouveau les hypothèses de conception clés tels que la cohérence concurrence et les données qui proviennent de prendre pour accorder.  

Pour l’application exemple complet référencée dans ce billet de blog :  

- [Gestion des accès concurrentiels à l’aide de stockage Azure - exemple d’Application](http://code.msdn.microsoft.com/Managing-Concurrency-using-56018114)  

Pour plus d’informations sur le stockage Azure consultez :  

- [Page d’accueil de stockage de Microsoft Azure](https://azure.microsoft.com/services/storage/)
- [Introduction au stockage Azure](storage-introduction.md)
- Prise en main pour [Blob](storage-dotnet-how-to-use-blobs.md), [Table](storage-dotnet-how-to-use-tables.md), [files d’attente](storage-dotnet-how-to-use-queues.md)et [fichiers](storage-dotnet-how-to-use-files.md) de stockage
- Architecture de stockage – [stockage Azure : un Service de stockage Cloud hautement disponible grâce à la cohérence fort](http://blogs.msdn.com/b/windowsazurestorage/archive/2011/11/20/windows-azure-storage-a-highly-available-cloud-storage-service-with-strong-consistency.aspx)

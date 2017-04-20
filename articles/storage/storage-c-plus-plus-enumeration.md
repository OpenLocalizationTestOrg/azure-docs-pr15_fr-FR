<properties
    pageTitle="Liste des ressources de stockage Azure avec la bibliothèque Microsoft Azure stockage Client pour C++ | Microsoft Azure"
    description="Découvrez comment utiliser des API dans la liste dans une bibliothèque Microsoft Azure stockage Client pour C++ énumérer les conteneurs, des objets BLOB, des files d’attente, des tableaux et des entités."
    documentationCenter=".net"
    services="storage"
    authors="dineshmurthy"
    manager="jahogg"
    editor="tysonn"/>
<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/18/2016"
    ms.author="dineshm"/>

# <a name="list-azure-storage-resources-in-c"></a>Liste des ressources de stockage Azure dans C++

Opérations de liste sont essentiels à de nombreux scénarios de développement avec stockage Azure. Cet article décrit comment plus efficacement énumérer les objets dans le stockage Azure à l’aide de la liste des API fournies dans la bibliothèque Microsoft Azure stockage Client pour C++.

>[AZURE.NOTE] Ce guide cible de la bibliothèque de Client de stockage Azure pour version C++ 2.x, qui est disponible via [NuGet](http://www.nuget.org/packages/wastorage) ou [GitHub](https://github.com/Azure/azure-storage-cpp).

La bibliothèque de stockage Client fournit plusieurs méthodes aux objets de liste ou d’une requête dans le stockage Azure. Cet article aborde les scénarios suivants :

-   Conteneurs de liste dans un compte
-   Liste des objets BLOB dans un conteneur ou d’un répertoire virtuel blob
-   Files d’attente de liste dans un compte
-   Liste des tables dans un compte
-   Interroger des entités dans un tableau

Chacune de ces méthodes s’affiche à l’aide de surcharges différentes pour différents scénarios.

## <a name="asynchronous-versus-synchronous"></a>Asynchrone ou synchrone

La bibliothèque de Client de stockage pour C++ est créée en haut de la [bibliothèque C++ reste](https://github.com/Microsoft/cpprestsdk), nous fondamentalement prend en charge les opérations asynchrones à l’aide de [pplx::task](http://microsoft.github.io/cpprestsdk/classpplx_1_1task.html). Par exemple :

    pplx::task<list_blob_item_segment> list_blobs_segmented_async(continuation_token& token) const;

Opérations synchrones renvoyer à la ligne les opérations asynchrones correspondantes :

    list_blob_item_segment list_blobs_segmented(const continuation_token& token) const
    {
        return list_blobs_segmented_async(token).get();
    }

Si vous travaillez avec plusieurs applications ou services thread, nous vous recommandons d’utiliser l’API asynchrone directement au lieu de créer un thread pour appeler la synchronisation API, ce qui affecte considérablement les performances.

## <a name="segmented-listing"></a>Annonce segmenté

L’échelle de stockage cloud nécessite annonce segmenté. Par exemple, vous pouvez avoir via un million BLOB dans un conteneur blob Azure ou entités un milliards dans une Table Azure. Il s’agit pas nombres théorique, mais des exemples d’utilisation client réel.

Il est donc pas pratique pour répertorier tous les objets dans une seule réponse. À la place, vous pouvez afficher des objets à l’aide de pagination. Chacun des API dans la liste a un *segmenté* surcharger.

La réponse à une opération de liste segmenté inclut :

-   <i>_segment</i>, qui contient le jeu de résultats renvoyés pour un seul appel à l’API de la liste.
-   *continuation_token*, qui est passé à l’appel suivante afin d’obtenir la page suivante de résultats. Lorsqu’il n’y a aucuns plus de résultats à retourner, le jeton de continuation est null.

Par exemple, un appel typique à répertorier tous les objets BLOB dans un conteneur peut ressembler à l’extrait de code suivantes. Le code est disponible dans nos [exemples](https://github.com/Azure/azure-storage-cpp/blob/master/Microsoft.WindowsAzure.Storage/samples/BlobsGettingStarted/Application.cpp):

    // List blobs in the blob container
    azure::storage::continuation_token token;
    do
    {
        azure::storage::list_blob_item_segment segment = container.list_blobs_segmented(token);
        for (auto it = segment.results().cbegin(); it != segment.results().cend(); ++it)
    {
        if (it->is_blob())
        {
            process_blob(it->as_blob());
        }
        else
        {
            process_diretory(it->as_directory());
        }
    }

        token = segment.continuation_token();
    }
    while (!token.empty());

Notez que le nombre de résultats renvoyés dans une page peut être contrôlé par le paramètre *max_results* dans la surcharge de chaque API, par exemple :

    list_blob_item_segment list_blobs_segmented(const utility::string_t& prefix, bool use_flat_blob_listing,
        blob_listing_details::values includes, int max_results, const continuation_token& token,
        const blob_request_options& options, operation_context context)

Si vous ne spécifiez pas le paramètre *max_results* , la valeur maximale par défaut des résultats jusqu'à 5 000 est renvoyée dans une seule page.

Notez également qu’une requête sur le stockage de Table Azure peut retourner aucun enregistrement ou moins d’enregistrements que la valeur du paramètre *max_results* que vous avez spécifié, même si le jeton de continuation n’est pas vide. L’une des raisons peuvent être que la requête n’a pas pu terminer de cinq secondes. Dans la mesure où le jeton de continuation n’est pas vide, la requête doit toujours, et votre code ne doit pas supposer la taille des résultats de segment.

Le modèle de codage recommandé pour la plupart des scénarios est segmenté répertoriant, qui indique la progression explicite de liste ou l’interrogation et comment le service répond à chaque demande. En particulier pour les applications C++ ou les services, contrôle de niveau inférieur de la progression de la liste peut-être vous aider contrôle mémoire et performances.

## <a name="greedy-listing"></a>Calcul des ressources nécessaires annonce

Les versions antérieures de la bibliothèque de Client de stockage pour C++ (versions 0.5.0 afficher un aperçu et les versions antérieures) inclus non segmenté annonce API pour les tables et les files d’attente, comme dans l’exemple suivant :

    std::vector<cloud_table> list_tables(const utility::string_t& prefix) const;
    std::vector<table_entity> execute_query(const table_query& query) const;
    std::vector<cloud_queue> list_queues() const;

Ces méthodes ont été implémentées comme wrappers d’API segmenté. Pour chaque réponse de vente segmenté, le code ajouté les résultats à un vecteur et renvoyée tous les résultats une fois que les conteneurs complètes ont été analysés.

Cette approche peut-être fonctionner lorsque le compte de stockage ou le tableau contient un petit nombre d’objets. Toutefois, avec une augmentation du nombre d’objets, la mémoire requise peut augmenter sans limitation, car tous les résultats est resté en mémoire. Une seule opération liste peut prendre beaucoup de temps, pendant laquelle l’appelant ne dispose pas d’informations sur sa progression.

Ces API dans le Kit de développement de liste calcul des ressources nécessaires n’existent pas dans c#, Java ou l’environnement JavaScript Node.js. Pour éviter les problèmes potentiels de l’utilisation de ces API calcul des ressources nécessaires, nous avons supprimé dans la version 0.6.0 (version préliminaire).

Si votre code appelle ces API calcul des ressources nécessaires :

    std::vector<azure::storage::table_entity> entities = table.execute_query(query);
    for (auto it = entities.cbegin(); it != entities.cend(); ++it)
    {
        process_entity(*it);
    }

Ensuite, vous devez modifier votre code pour utiliser la liste segmentée API :

    azure::storage::continuation_token token;
    do
    {
        azure::storage::table_query_segment segment = table.execute_query_segmented(query, token);
        for (auto it = segment.results().cbegin(); it != segment.results().cend(); ++it)
        {
            process_entity(*it);
        }

        token = segment.continuation_token();
    } while (!token.empty());

En spécifiant le paramètre *max_results* du segment, vous pouvez équilibrer entre les numéros de requêtes et utilisation de la mémoire pour répondre aux considérations sur les performances de votre application.

En outre, si vous utilisez la liste segmentée API, mais stockez les données dans une collection locale dans un style « calcul des ressources nécessaires », également fortement recommandé que vous refactorisez votre code pour gérer le stockage de données dans une collection locale avec soin à l’échelle.

## <a name="lazy-listing"></a>Annonce différée

Calcul des ressources nécessaires annonce élevé de problèmes potentiels, mais il est pratique s’il n’y a pas trop d’objets dans le conteneur.

Si vous utilisez également c# ou Oracle Java SDK, vous devez être familiarisé avec le modèle de programmation énumérable, qui offre un style jattes répertoriant, où les données à un certain offset sont extraite uniquement si nécessaire. Dans C++, le modèle d’itérateur fournit également une approche similaire.

Significative différée typique API, par exemple, à l’aide de **list_blobs** ressemble à ceci :

    list_blob_item_iterator list_blobs() const;

Un extrait de code standard qui utilise le modèle de liste différée peut ressembler à ceci :

    // List blobs in the blob container
    azure::storage::list_blob_item_iterator end_of_results;
    for (auto it = container.list_blobs(); it != end_of_results; ++it)
    {
        if (it->is_blob())
        {
            process_blob(it->as_blob());
        }
        else
        {
            process_directory(it->as_directory());
        }
    }

Notez qu’annonce différée est uniquement disponible en mode synchrone.

Annonce différée par rapport à la liste calcul des ressources nécessaires, extrait les données uniquement lorsque cela est nécessaire. En coulisses, il extrait les données à partir du stockage Azure uniquement lorsque l’itérateur suivante se déplace dans le segment suivant. Par conséquent, utilisation de la mémoire est contrôlée avec une taille limite, et l’opération est rapide.

Annonce jattes API sont incluses dans la bibliothèque de stockage Client pour C++ version2.2.0.

## <a name="conclusion"></a>Conclusion

Dans cet article, nous avons abordé différentes surcharges permet de répertorier des API pour différents objets dans la bibliothèque de stockage Client pour C++. En résumé

-   Asynchrone APIs sont vivement recommandé sous plusieurs scénarios threads.
-   Annonce segmenté est recommandée pour la plupart des scénarios.
-   Annonce différée est fourni dans la bibliothèque comme une enveloppe dans les scénarios synchrones pratique.
-   Calcul des ressources nécessaires annonce n’est pas recommandé et a été supprimé de la bibliothèque.

##<a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur le stockage Azure et bibliothèque Client pour C++, consultez les ressources suivantes.

-   [L’utilisation de stockage d’objets Blob à partir de C++](storage-c-plus-plus-how-to-use-blobs.md)
-   [Comment utiliser le stockage de Table à partir de C++](storage-c-plus-plus-how-to-use-tables.md)
-   [Comment utiliser le stockage de file d’attente à partir de C++](storage-c-plus-plus-how-to-use-queues.md)
-   [Bibliothèque de Client de stockage Azure pour obtenir une documentation Fonctionapic.](http://azure.github.io/azure-storage-cpp/)
-   [Blog de l’équipe stockage Azure](http://blogs.msdn.com/b/windowsazurestorage/)
-   [Documentation du stockage Azure](https://azure.microsoft.com/documentation/services/storage/)

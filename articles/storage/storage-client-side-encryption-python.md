<properties
    pageTitle="Chiffrement côté client avec Python pour le stockage de Microsoft Azure | Microsoft Azure"
    description="La bibliothèque de Client de stockage Azure pour Python prend en charge le chiffrement côté client pour une sécurité maximale pour vos applications de stockage Azure."
    services="storage"
    documentationCenter="python"
    authors="dineshmurthy"
    manager="jahogg"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="python"
    ms.topic="article"
    ms.date="10/18/2016"
    ms.author="dineshm"/>


# <a name="client-side-encryption-with-python-for-microsoft-azure-storage"></a>Chiffrement côté client avec Python pour le stockage de Microsoft Azure

[AZURE.INCLUDE [storage-selector-client-side-encryption-include](../../includes/storage-selector-client-side-encryption-include.md)]

## <a name="overview"></a>Vue d’ensemble

La [Bibliothèque de Client de stockage Azure pour Python](https://pypi.python.org/pypi/azure-storage) prend en charge le chiffrement des données dans les applications clientes avant de télécharger vers le stockage Azure et déchiffrer des données pendant leur téléchargement vers le client.

>[AZURE.NOTE] La bibliothèque Azure stockage Python est dans l’aperçu.

## <a name="encryption-and-decryption-via-the-envelope-technique"></a>Chiffrement et déchiffrement via la technique enveloppe
Les processus de chiffrement et déchiffrement suivent la technique d’enveloppe.

### <a name="encryption-via-the-envelope-technique"></a>Chiffrement via la technique enveloppe
Chiffrement via la technique enveloppe fonctionne de la manière suivante :

1.  La bibliothèque de client de stockage Azure génère une clé de chiffrement de contenu (CEK), ce qui correspond à une clé symétrique usage unique.

2.  Données de l’utilisateur sont chiffrées à l’aide de cette CEK.

3.  Le CEK est ensuite encapsulé (chiffrées) à l’aide de la clé de chiffrement clés (KEK). Le KEK est identifié par un identificateur de clé et peut être une paire de clés asymétriques ou une clé symétrique, qui est gérée localement.
La bibliothèque de client de stockage elle-même a jamais accès aux KEK. La bibliothèque appelle l’algorithme de clé habillage qui est fourni par le KEK. Les utilisateurs peuvent choisir d’utiliser des fournisseurs personnalisés pour habillage/désencapsuler une clé si vous le souhaitez.

4.  Les données chiffrées sont ensuite téléchargées au service de stockage Azure. La touche encapsulée ainsi que des métadonnées de chiffrement supplémentaire est stockée sous forme de métadonnées (sur un blob) ou interpolée avec les données chiffrées (messages file d’attente et entités de table).

### <a name="decryption-via-the-envelope-technique"></a>Déchiffrement via la technique enveloppe
Déchiffrement via la technique enveloppe fonctionne de la manière suivante :

1.  La bibliothèque cliente suppose que l’utilisateur est géré par la clé de chiffrement clés (KEK) localement. L’utilisateur n’a pas besoin de savoir la clé spécifique qui a été utilisée pour le chiffrement. À la place, un programme de résolution clé, qui est résolue différents identificateurs de clé aux clés, peut être configuré et utilisé.

2.  La bibliothèque cliente télécharge les données chiffrées ainsi que tout matériel de chiffrement qui se trouve sur le service.

3.  La clé de chiffrement de contenu encapsulé (CEK) est non (déchiffré) à l’aide du chiffrement clé principales (KEK). Ici, la bibliothèque cliente n’a pas accès à KEK. Il appelle tout simplement algorithme désencapsuler du fournisseur personnalisé.

4.  La clé de chiffrement de contenu (CEK), est utilisée pour déchiffrer les données utilisateur chiffré.

## <a name="encryption-mechanism"></a>Mécanisme de chiffrement  
La bibliothèque de stockage client utilise [AES](http://en.wikipedia.org/wiki/Advanced_Encryption_Standard) pour chiffrer les données de l’utilisateur. [Chaîne de bloc de chiffrement (CBC)](http://en.wikipedia.org/wiki/Block_cipher_mode_of_operation#Cipher-block_chaining_.28CBC.29) en particulier, le mode avec AES. Chaque service fonctionne différemment, afin que nous allons étudier chacune d’elles ici.

### <a name="blobs"></a>Objets BLOB
La bibliothèque cliente prend actuellement en charge le chiffrement des objets BLOB entiers uniquement. Plus précisément, le chiffrement est pris en charge lorsque les utilisateurs utilisent le **créer*** méthodes. Pour les téléchargements, toutes deux terminées et plage sont prises en charge de parallélisation de chargement et le téléchargement est disponible.

Pendant le chiffrement, la bibliothèque cliente génère un vecteur d’initialisation aléatoire (IV) de 16 octets, avec une clé de chiffrement de contenu aléatoire (CEK) de 32 octets et effectuer le chiffrement enveloppe des données blob à l’aide de ces informations. La CEK renvoyé à la ligne et des métadonnées de chiffrement supplémentaires sont ensuite stockés comme blob métadonnées ainsi que le blob chiffré sur le service.

>[AZURE.WARNING] Si vous modifiez ou téléchargement de vos propres métadonnées pour l’objets blob, vous devez vous assurer que ces métadonnées sont conservée. Si vous téléchargez des nouvelles métadonnées sans ces métadonnées, la CEK encapsulé, IV et autres métadonnées seront perdues et le contenu d’objets blob ne sera jamais récupérable à nouveau.

Le téléchargement d’un objet blob chiffré, il faut récupérer le contenu du blob entière à l’aide de la **prise*** méthodes pratiques. La CEK encapsulé est dévoilé et utilisé avec la IV (stocké en tant que blob métadonnées dans ce cas) pour renvoyer les données déchiffrées aux utilisateurs.

Le téléchargement d’une plage arbitraire (**obtenir*** méthodes avec les paramètres de plage passé) dans le blob chiffré implique l’ajustement de la plage fournie par les utilisateurs afin d’obtenir une petite quantité de données supplémentaires qui peuvent servir à déchiffrer correctement la plage demandée.

Bloc des objets BLOB et des objets BLOB page uniquement peuvent être à l’aide de ce modèle chiffrées/déchiffré. Il n’existe actuellement aucune prise en charge pour le chiffrement ajouter des objets BLOB.

### <a name="queues"></a>Files d’attente
Dans la mesure où les messages file d’attente peuvent être de n’importe quel format, la bibliothèque client définit un format personnalisé qui contient le vecteur d’initialisation (IV) et la clé de chiffrement de contenu chiffré (CEK) dans le texte du message.

Pendant le chiffrement, la bibliothèque cliente génère une IV aléatoires de 16 octets ainsi qu’un CEK aléatoires de 32 octets et effectue le chiffrement enveloppe du texte du message file d’attente à l’aide de ces informations. La CEK renvoyé à la ligne et des métadonnées de chiffrement supplémentaires sont ensuite ajoutés au message chiffré file d’attente. Ce message modifié (comme illustré ci-après) est stocké sur le service.

    <MessageText>{"EncryptedMessageContents":"6kOu8Rq1C3+M1QO4alKLmWthWXSmHV3mEfxBAgP9QGTU++MKn2uPq3t2UjF1DO6w","EncryptionData":{…}}</MessageText>

Pendant le déchiffrement, la touche encapsulée est extraite à partir du message file d’attente et dévoilée. Le VI est également extrait à partir du message file d’attente et utilisé ainsi que la clé non pour déchiffrer les données du message file d’attente. Notez que les métadonnées de chiffrement sont petite (sous 500 octets), même si elle compte pour la limite de 64 Ko pour un message file d’attente, l’impact doit être gérable.

### <a name="tables"></a>Tables
La bibliothèque cliente prend en charge le chiffrement des propriétés de l’entité pour l’insertion et les opérations de remplacement.

>[AZURE.NOTE] Fusion n’est pas actuellement pris en charge. Dans la mesure où un sous-ensemble de propriétés peut-être ont été chiffré précédemment à l’aide d’une clé différente, simplement fusionner les nouvelles propriétés et mise à jour les métadonnées générera de perte de données. La fusion soit nécessite passer des appels de service supplémentaire à lire l’entité existante à partir du service, ou à l’aide d’une nouvelle clé par propriété, qui ne conviennent pas pour des raisons de performances.

Chiffrement de données de tableau fonctionne comme suit :

1.  Les utilisateurs spécifier les propriétés d’être chiffrées.

2.  La bibliothèque cliente génère un vecteur d’initialisation aléatoire (IV) de 16 octets ainsi qu’une clé de chiffrement de contenu aléatoire (CEK) de 32 octets pour chaque entité et effectue le chiffrement enveloppe sur les propriétés individuelles à chiffrer par dérivation une nouvelle IV par propriété. La propriété cryptée est stockée en tant que données binaires.

3.  La CEK renvoyé à la ligne et des métadonnées de chiffrement supplémentaires sont ensuite stockés que les deux propriétés réservées supplémentaires. La première réservé propriété (\_ClientEncryptionMetadata1) est une propriété de chaîne qui conserve des informations sur IV, version et clé renvoyé à la ligne. Le second réservé propriété (\_ClientEncryptionMetadata2) est une propriété binaire qui conserve des informations sur les propriétés qui sont chiffrés. Les informations contenues dans cette propriété deuxième (\_ClientEncryptionMetadata2) elle-même chiffrée.

4.  En raison de ces propriétés réservées supplémentaires requises pour le chiffrement, les utilisateurs peuvent désormais avoir uniquement 250 propriétés personnalisées au lieu de 252. La taille totale de l’entité doit être inférieure à 1 Mo.

    Notez que seules les propriétés de chaîne peuvent être chiffrées. Si d’autres types de propriétés doivent être chiffrés, elles doivent être converties en chaînes. Les chaînes chiffrées sont stockées sur le service en tant que propriétés binaires, et ils sont converties en chaînes (brutes chaînes, pas EntityProperties avec type EdmType.STRING) après le déchiffrement.

    Pour les tables, en plus de la stratégie de chiffrement, les utilisateurs doivent spécifier les propriétés d’être chiffrées. Cela peut être effectuée à stocker ces propriétés dans les objets TableEntity dont la valeur type EdmType.STRING et chiffrer défini sur true ou définir l’encryption_resolver_function sur l’objet tableservice. Une résolution de chiffrement est une fonction qui prend une clé de partition, la clé de ligne et le nom de la propriété et retourne une valeur booléenne qui indique si cette propriété doit être chiffrée. Pendant le chiffrement, la bibliothèque client utilisera ces informations pour décider si une propriété doit être chiffrée pendant l’écriture sur le réseau. Le délégué fournit également la possibilité de logique autour de la façon dont les propriétés sont chiffrées. (Par exemple, si les arguments X, puis chiffrer propriété A ; autrement chiffrer les propriétés de A et B.) Notez qu’il n’est pas nécessaire de fournir ces informations lors de la lecture ou l’interrogation d’entités.

### <a name="batch-operations"></a>Opérations de commandes
Une stratégie de chiffrement s’applique à toutes les lignes du lot. La bibliothèque cliente en interne génère une nouvelle IV aléatoires et aléatoire CEK par ligne dans le lot. Les utilisateurs peuvent également choisir chiffrer des propriétés différentes pour chaque opération dans le lot en définissant ce comportement dans l’utilitaire de résolution de chiffrement.
Si un lot est créé comme un gestionnaire de contexte via la méthode batch() tableservice, stratégie de chiffrement de tableservice automatiquement est appliquée au lot. Si un lot est créé explicitement en appelant le constructeur, la stratégie de chiffrement doit être passée comme paramètre et non modifiée gauche pour la durée de vie du lot.
Notez qu’entités sont chiffrées qu’elles sont insérées dans le lot à l’aide de la stratégie de chiffrement du lot (entités sont chiffrées pas au moment de la validation du lot à l’aide de la stratégie de chiffrement de tableservice).

### <a name="queries"></a>Requêtes
Pour effectuer les opérations de requête, vous devez spécifier une résolution clée est en mesure de résoudre toutes les clés dans le jeu de résultats. Si une entité contenue dans les résultats de la requête ne peut pas être résolue à un fournisseur, la bibliothèque cliente génère une erreur. Pour une requête qui effectue des projections côté serveur, la bibliothèque cliente ajoute les propriétés de métadonnées chiffrement spéciaux (\_ClientEncryptionMetadata1 et \_ClientEncryptionMetadata2) par défaut dans les colonnes sélectionnées.

>[AZURE.IMPORTANT] N’oubliez pas de ces points importants lors de l’utilisation du chiffrement côté client :
>
>- Lors de la lecture ou de l’écriture dans un objet blob chiffré, utilisez blob tout téléchargement commandes et plage/entier blob téléchargement. Éviter l’écriture dans un objet blob chiffré à l’aide d’opérations de protocole tels que bloc placer, placer la liste de blocs, Pages écrire ou effacer des Pages ; dans le cas contraire, vous pouvez endommagée le blob chiffré et rendez-le illisible.
>
>- Pour les tableaux, il existe une contrainte similaire. En veillant à ne pas mettre à jour des propriétés chiffrées sans mettre à jour les métadonnées de chiffrement.
>
>- Si vous définissez des métadonnées sur le blob chiffré, vous risquez d’écraser les métadonnées de chiffrement requises pour le déchiffrement, étant donné que la définition des métadonnées n’est pas cumulable. Ceci est également vrai pour instantanés ; Évitez de spécifier les métadonnées lors de la création d’un instantané d’un objet blob chiffré. Si métadonnées doivent être définie, n’oubliez pas de la méthode **get_blob_metadata** tout d’abord pour obtenir les métadonnées de chiffrement en cours d’appel et éviter écritures simultanées pendant le paramétrage de métadonnées.
>
>- Activer l’indicateur **require_encryption** sur l’objet de service pour les utilisateurs doivent fonctionnent uniquement avec les données chiffrées. Pour plus d’informations, voir ci-dessous.

La bibliothèque de stockage client attend la KEK fournie et vider clé pour implémenter l’interface suivant. Prise en charge de [L’archivage sécurisé de clé Azure](https://azure.microsoft.com/services/key-vault/) pour la gestion de Python KEK est en attente et est intégré dans cette bibliothèque lorsque terminée.


## <a name="client-api--interface"></a>API cliente / Interface
Après avoir créé un objet de service de stockage (c'est-à-dire blockblobservice), l’utilisateur peut affecter des valeurs aux champs qui constituent une stratégie de chiffrement : key_encryption_key, key_resolver_function et require_encryption. Les utilisateurs peuvent fournir uniquement un KEK, uniquement un programme de résolution ou les deux. key_encryption_key est le type de clé simple qui est identifié à l’aide d’un identificateur de clé et qui fournit la logique pour dévoilement/retour à la ligne. key_resolver_function est utilisé pour résoudre une clé au cours du processus de déchiffrement. Elle retourne un KEK valide donné un identificateur de clé. Cela permet aux utilisateurs de choisir entre plusieurs touches sont gèrent à plusieurs endroits.

La KEK doit implémenter les méthodes suivantes pour le chiffrement des données :
- wrap_key(cek) : le CEK spécifié (octets) en utilisant un algorithme de choix de l’utilisateur à la ligne. Retourne la clé renvoyé à la ligne.
- get_key_wrap_algorithm() : renvoie l’algorithme utilisé pour renvoyer à la ligne clés.
- get_kid() : renvoie l’identificateur de clé de chaîne pour cette KEK.
La KEK doit implémenter les méthodes suivantes pour déchiffrer des données :
- unwrap_key (cek, algorithme) : retourne la forme non de la CEK spécifiée à l’aide de l’algorithme de chaîne spécifiée.
- get_kid() : renvoie un id de clés de chaîne pour cette KEK.

L’utilitaire de résolution clé doit implémenter au moins une méthode que, étant donné un id de clé, renvoie le correspondante KEK implémentation de l’interface ci-dessus. Uniquement cette méthode doit être affectée à la propriété key_resolver_function sur l’objet de service.

- Pour le chiffrement, la clé est toujours utilisée et l’absence d’une clé provoquera une erreur.
- Pour le déchiffrement :
    - L’utilitaire de résolution clé est appelée si spécifié pour obtenir la clé. Si l’utilitaire de résolution est spécifié mais n’a pas une correspondance pour l’identificateur de clé, une erreur est générée.
    - Si vider n’est pas spécifié, mais une clé est spécifiée, elle est utilisée si son identificateur correspond à l’identificateur de clé requis. Si l’identificateur ne correspond pas, une erreur est générée.

      Les exemples de chiffrement de azure.storage.samples <fix URL>illustrent un scénario de bout en bout plus détaillé pour des objets BLOB, les files d’attente et les tables.
        Exemples d’implémentation des KEK et vider clé sont fournies dans les fichiers d’exemples comme KeyWrapper et KeyResolver respectivement.

### <a name="requireencryption-mode"></a>Mode RequireEncryption
Les utilisateurs peuvent éventuellement activer un mode de fonctionnement où tous les chargements et téléchargements doivent être chiffrés. Dans ce mode, tentatives de télécharger des données qui ne sont pas chiffrées sur le service des données sans une stratégie de chiffrement échoue sur le client. L’indicateur **require_encryption** sur l’objet de service de contrôler ce comportement.

### <a name="blob-service-encryption"></a>Chiffrement de service d’objets BLOB
Ajouter le chiffrement des champs de stratégie dans l’objet blockblobservice. Tout le reste sera géré par la bibliothèque cliente en interne.

    # Create the KEK used for encryption.
    # KeyWrapper is the provided sample implementation, but the user may use their own object as long as it implements the interface above.
    kek = KeyWrapper('local:key1') # Key identifier

    # Create the key resolver used for decryption.
    # KeyResolver is the provided sample implementation, but the user may use whatever implementation they choose so long as the function set on the service object behaves appropriately.
    key_resolver = KeyResolver()
    key_resolver.put_key(kek)

    # Set the KEK and key resolver on the service object.
    my_block_blob_service.key_encryption_key = kek
    my_block_blob_service.key_resolver_funcion = key_resolver.resolve_key

    # Upload the encrypted contents to the blob.
    my_block_blob_service.create_blob_from_stream(container_name, blob_name, stream)

    # Download and decrypt the encrypted contents from the blob.
    blob = my_block_blob_service.get_blob_to_bytes(container_name, blob_name)

### <a name="queue-service-encryption"></a>Chiffrement de service file d’attente
Ajouter le chiffrement des champs de stratégie dans l’objet queueservice. Tout le reste sera géré par la bibliothèque cliente en interne.

    # Create the KEK used for encryption.
    # KeyWrapper is the provided sample implementation, but the user may use their own object as long as it implements the interface above.
    kek = KeyWrapper('local:key1') # Key identifier

    # Create the key resolver used for decryption.
    # KeyResolver is the provided sample implementation, but the user may use whatever implementation they choose so long as the function set on the service object behaves appropriately.
    key_resolver = KeyResolver()
    key_resolver.put_key(kek)

    # Set the KEK and key resolver on the service object.
    my_queue_service.key_encryption_key = kek
    my_queue_service.key_resolver_funcion = key_resolver.resolve_key

    # Add message
    my_queue_service.put_message(queue_name, content)

    # Retrieve message
    retrieved_message_list = my_queue_service.get_messages(queue_name)

### <a name="table-service-encryption"></a>Chiffrement de service de table
Outre la création d’une stratégie de chiffrement et en définissant les options de requête, vous devez spécifier un **encryption_resolver_function** sur la **tableservice**ou définir l’attribut chiffrer sur la EntityProperty.

### <a name="using-the-resolver"></a>À l’aide de l’utilitaire de résolution

    # Create the KEK used for encryption.
    # KeyWrapper is the provided sample implementation, but the user may use their own object as long as it implements the interface above.
    kek = KeyWrapper('local:key1') # Key identifier

    # Create the key resolver used for decryption.
    # KeyResolver is the provided sample implementation, but the user may use whatever implementation they choose so long as the function set on the service object behaves appropriately.
    key_resolver = KeyResolver()
    key_resolver.put_key(kek)

    # Define the encryption resolver_function.
    def my_encryption_resolver(pk, rk, property_name):
            if property_name == 'foo':
                    return True
            return False

    # Set the KEK and key resolver on the service object.
    my_table_service.key_encryption_key = kek
    my_table_service.key_resolver_funcion = key_resolver.resolve_key
    my_table_service.encryption_resolver_function = my_encryption_resolver

    # Insert Entity
    my_table_service.insert_entity(table_name, entity)

    # Retrieve Entity
    # Note: No need to specify an encryption resolver for retrieve, but it is harmless to leave the property set.
    my_table_service.get_entity(table_name, entity['PartitionKey'], entity['RowKey'])

### <a name="using-attributes"></a>À l’aide d’attributs
Comme indiqué ci-dessus, une propriété peut être marquée pour le chiffrement en enregistrant dans un objet EntityProperty et en définissant le champ chiffrer.

    encrypted_property_1 = EntityProperty(EdmType.STRING, value, encrypt=True)

## <a name="encryption-and-performance"></a>Chiffrement et les performances
Notez que le chiffrement votre stockage de données aboutit à surcharge supplémentaire des performances. La clé de contenu et IV doivent être généré, le contenu proprement dit doit être chiffré et métadonnées supplémentaires doivent être mis en forme et téléchargées. Cette surcharge varient selon la quantité de données en cours de chiffrement. Nous vous recommandons de toujours tester leurs performances lors du développement des applications.

## <a name="next-steps"></a>Étapes suivantes

- Télécharger la [Bibliothèque de Client de stockage Azure package PyPi Java](https://pypi.python.org/pypi/azure-storage)
- Télécharger le [stockage Azure bibliothèque du Client pour Python Code à partir de GitHub Source](https://github.com/Azure/azure-storage-python)

<properties
    pageTitle="Chiffrement côté client avec Java pour le stockage de Microsoft Azure | Microsoft Azure"
    description="La bibliothèque de Client de stockage Azure pour Java prend en charge le chiffrement côté client et l’intégration avec l’archivage sécurisé de clé Azure pour une sécurité maximale pour vos applications de stockage Azure."
    services="storage"
    documentationCenter="java"
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


# <a name="client-side-encryption-with-java-for-microsoft-azure-storage"></a>Chiffrement côté client avec Java pour le stockage de Microsoft Azure   

[AZURE.INCLUDE [storage-selector-client-side-encryption-include](../../includes/storage-selector-client-side-encryption-include.md)]

## <a name="overview"></a>Vue d’ensemble  

La [Bibliothèque de Client de stockage Azure pour Java](http://mvnrepository.com/artifact/com.microsoft.azure/azure-storage) prend en charge le chiffrement des données dans les applications clientes avant de télécharger vers le stockage Azure et déchiffrer des données pendant leur téléchargement vers le client. La bibliothèque prend également en charge l’intégration avec [L’archivage sécurisé de clé Azure](https://azure.microsoft.com/services/key-vault/) pour la gestion de clé de compte de stockage.

## <a name="encryption-and-decryption-via-the-envelope-technique"></a>Chiffrement et déchiffrement via la technique enveloppe    
Les processus de chiffrement et déchiffrement suivent la technique d’enveloppe.  

### <a name="encryption-via-the-envelope-technique"></a>Chiffrement via la technique enveloppe  
Chiffrement via la technique enveloppe fonctionne de la manière suivante :  

1.  La bibliothèque de client de stockage Azure génère une clé de chiffrement de contenu (CEK), ce qui correspond à une clé symétrique usage unique.  

2.  Données de l’utilisateur sont chiffrées à l’aide de cette CEK.   

3.  Le CEK est ensuite encapsulé (chiffrées) à l’aide de la clé de chiffrement clés (KEK). Le KEK est identifié par un identificateur de clé et peut être une paire de clés asymétriques ou une clé symétrique et peuvent être gérée localement ou stockée dans Azure clé chambres fortes.  
La bibliothèque de client de stockage elle-même a jamais accès aux KEK. La bibliothèque appelle l’algorithme de clé habillage qui est fourni par l’archivage sécurisé clé. Les utilisateurs peuvent choisir d’utiliser des fournisseurs personnalisés pour habillage/désencapsuler une clé si vous le souhaitez.  

4.  Les données chiffrées sont ensuite téléchargées au service de stockage Azure. La touche encapsulée ainsi que des métadonnées de chiffrement supplémentaire est stockée sous forme de métadonnées (sur un blob) ou interpolée avec les données chiffrées (messages file d’attente et entités de table).

### <a name="decryption-via-the-envelope-technique"></a>Déchiffrement via la technique enveloppe  
Déchiffrement via la technique enveloppe fonctionne de la manière suivante :  

1.  La bibliothèque cliente suppose que l’utilisateur est géré par la clé de chiffrement clés (KEK) localement ou en chambres fortes Azure clé. L’utilisateur n’a pas besoin de savoir la clé spécifique qui a été utilisée pour le chiffrement. À la place, un programme de résolution clé qui résout différents identificateurs de clé aux clés peut être configuré et utilisé.  

2.  La bibliothèque cliente télécharge les données chiffrées ainsi que tout matériel de chiffrement qui se trouve sur le service.  

3.  La clé de chiffrement de contenu encapsulé (CEK) est non (déchiffré) à l’aide du chiffrement clé principales (KEK). Ici, la bibliothèque cliente n’a pas accès à KEK. Il appelle tout simplement personnalisé ou algorithme désencapsuler du fournisseur clé l’archivage sécurisé.  

4.  La clé de chiffrement de contenu (CEK), est utilisée pour déchiffrer les données utilisateur chiffré.

## <a name="encryption-mechanism"></a>Mécanisme de chiffrement  
La bibliothèque de stockage client utilise [AES](http://en.wikipedia.org/wiki/Advanced_Encryption_Standard) pour chiffrer les données de l’utilisateur. [Chaîne de bloc de chiffrement (CBC)](http://en.wikipedia.org/wiki/Block_cipher_mode_of_operation#Cipher-block_chaining_.28CBC.29) en particulier, le mode avec AES. Chaque service fonctionne différemment, afin que nous allons étudier chacune d’elles ici.

### <a name="blobs"></a>Objets BLOB  
La bibliothèque cliente prend actuellement en charge le chiffrement des objets BLOB entiers uniquement. Plus précisément, le chiffrement est pris en charge lorsque les utilisateurs utilisent le **Télécharger** * méthodes ou la * *openOutputStream** méthode. Téléchargements, toutes deux terminées et plage sont prises en charge.  

Pendant le chiffrement, la bibliothèque cliente génère un vecteur d’initialisation aléatoire (IV) de 16 octets, avec une clé de chiffrement de contenu aléatoire (CEK) de 32 octets et effectuer le chiffrement enveloppe des données blob à l’aide de ces informations. La CEK renvoyé à la ligne et des métadonnées de chiffrement supplémentaires sont ensuite stockés comme blob métadonnées ainsi que le blob chiffré sur le service.

>[AZURE.WARNING] Si vous modifiez ou téléchargement de vos propres métadonnées pour l’objets blob, vous devez vous assurer que ces métadonnées sont conservée. Si vous téléchargez des nouvelles métadonnées sans ces métadonnées, la CEK encapsulé, IV et autres métadonnées seront perdues et le contenu d’objets blob ne sera jamais récupérable à nouveau.

Le téléchargement d’un objet blob chiffré, il faut récupérer le contenu du blob entière à l’aide de la * *Télécharger*/openInputStream** méthodes pratiques. La CEK encapsulé est dévoilé et utilisé avec la IV (stocké en tant que blob métadonnées dans ce cas) pour renvoyer les données déchiffrées aux utilisateurs.

Le téléchargement d’une plage arbitraire (**downloadRange*** méthodes) dans le blob chiffré implique l’ajustement de la plage fournie par les utilisateurs afin d’obtenir une petite quantité de données supplémentaires qui peuvent servir à déchiffrer correctement la plage demandée.  

Tous les blob types (bloquer des objets BLOB, des objets BLOB de page et ajouter des objets BLOB) peut être chiffré/déchiffré à l’aide de ce modèle.

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

3.  La CEK renvoyé à la ligne et des métadonnées de chiffrement supplémentaires sont ensuite stockés que les deux propriétés réservées supplémentaires. La première propriété réservée (_ClientEncryptionMetadata1) est une propriété de chaîne qui conserve des informations sur IV, version et clé renvoyé à la ligne. La propriété deuxième réservée (_ClientEncryptionMetadata2) est une propriété binaire qui conserve des informations sur les propriétés qui sont chiffrés. Les informations contenues dans cette propriété deuxième (_ClientEncryptionMetadata2) sont elle-même cryptée.  

4.  En raison de ces propriétés réservées supplémentaires requises pour le chiffrement, les utilisateurs peuvent désormais avoir uniquement 250 propriétés personnalisées au lieu de 252. La taille totale de l’entité doit être inférieure à 1 Mo.  

    Notez que seules les propriétés de chaîne peuvent être chiffrées. Si d’autres types de propriétés doivent être chiffrés, elles doivent être converties en chaînes. Les chaînes chiffrées sont stockées sur le service en tant que propriétés binaires, et ils sont converties en chaînes après déchiffrement.

    Pour les tables, en plus de la stratégie de chiffrement, les utilisateurs doivent spécifier les propriétés d’être chiffrées. Pour ce faire, vous pouvez spécifier un attribut [chiffrer] (pour les entités POCO dérivent TableEntity) ou un programme de résolution de chiffrement dans les options de requête. Une résolution de chiffrement est un délégué qui prend une clé de partition, la clé de ligne et le nom de la propriété et retourne une valeur booléenne qui indique si cette propriété doit être chiffrée. Pendant le chiffrement, la bibliothèque client utilisera ces informations pour décider si une propriété doit être chiffrée pendant l’écriture sur le réseau. Le délégué fournit également la possibilité de logique autour de la façon dont les propriétés sont chiffrées. (Par exemple, si les arguments X, puis chiffrer propriété A ; autrement chiffrer les propriétés de A et B.) Notez qu’il n’est pas nécessaire de fournir ces informations lors de la lecture ou l’interrogation d’entités.

### <a name="batch-operations"></a>Opérations de commandes  
Dans les opérations de lot, le même KEK sera utilisé sur toutes les lignes de cette opération lot, car la bibliothèque cliente autorise un objet options (et donc une stratégie/KEK) par lots. Toutefois, la bibliothèque cliente générera en interne une nouvelle IV aléatoires et aléatoire CEK par ligne dans le lot. Les utilisateurs peuvent également choisir chiffrer des propriétés différentes pour chaque opération dans le lot en définissant ce comportement dans l’utilitaire de résolution de chiffrement.

### <a name="queries"></a>Requêtes  
Pour effectuer les opérations de requête, vous devez spécifier une résolution clée est en mesure de résoudre toutes les clés dans le jeu de résultats. Si une entité contenue dans les résultats de la requête ne peut pas être résolue à un fournisseur, la bibliothèque cliente génère une erreur. Pour une requête qui effectue des projections côté serveur, la bibliothèque cliente ajoute les propriétés de métadonnées chiffrement spéciaux (_ClientEncryptionMetadata1 et _ClientEncryptionMetadata2) par défaut dans les colonnes sélectionnées.

## <a name="azure-key-vault"></a>Archivage sécurisé clé Azure  
L’archivage sécurisé clé Azure permet aux clés de chiffrement divulgation d’informations et secrets utilisés par les services et d’applications cloud. À l’aide de l’archivage sécurisé de clé Azure, les utilisateurs peuvent chiffrer clés et secrets (par exemple, l’authentification clés clés de compte de stockage, les clés de chiffrement de données. Fichiers PFX et les mots de passe) en utilisant les touches qui sont protégés par les modules de sécurité matérielle (HSM). Pour plus d’informations, voir [Quel est l’archivage sécurisé de clé Azure ?](../key-vault/key-vault-whatis.md).

La bibliothèque de stockage client utilise la bibliothèque principale de l’archivage sécurisé clé afin de fournir un cadre commun entre Azure de gestion de clés. Les utilisateurs obtenir également l’avantage de l’utilisation de la bibliothèque d’extensions de l’archivage sécurisé clé. La bibliothèque d’extensions fournit des fonctionnalités utiles autour simple et transparente symétrique/RSA local et fournisseurs de clé cloud, ainsi qu’avec l’agrégation et la mise en cache.

### <a name="interface-and-dependencies"></a>Interface et dépendances  
Il existe trois packages l’archivage sécurisé clé :  

- Azure cœur keyvault contient la IKey et IKeyResolver. Il s’agit d’un package petit sans dépendances. La bibliothèque de client de stockage pour Java la définit comme une dépendance.
- Azure keyvault contient le client reste de l’archivage sécurisé clé.  
- extensions de keyvault Azure contient le code d’extension qui inclut des mises en œuvre des algorithmes de chiffrement et un RSAKey et un SymmetricKey. Il varie selon les espaces de noms de base et KeyVault et fournit les fonctionnalités pour définir une résolution agrégation (lorsque les utilisateurs souhaitent utiliser plusieurs fournisseurs de clé) et une résolution clée du cache. Bien que la bibliothèque de client de stockage ne pas dépend directement ce package, si vous souhaitez utiliser l’archivage sécurisé de clé Azure pour stocker leurs clés ou utiliser les extensions de l’archivage sécurisé clé pour consommer local et cloud fournisseurs de chiffrement, elles devront ce package.  

  Clé de l’archivage sécurisé est conçu pour des clés principales haute valeur, et limites limitation par l’archivage sécurisé clé sont conçues avec ce à l’esprit. Lorsque vous effectuez le chiffrement côté client avec l’archivage sécurisé clé, le modèle par défaut consiste à utiliser des clés principales symétriques stockés en tant que secrets dans l’archivage sécurisé clé et mis en cache localement. Les utilisateurs doivent effectuer les éléments suivants :  

1.  Créer un code secret en mode hors connexion et téléchargez-le sur l’archivage sécurisé clé.  

2.  Utiliser identificateur de base de secret en tant que paramètre pour résoudre la version actuelle du code secret pour le chiffrement et mettre en cache ces informations localement. Utiliser CachingKeyResolver pour la mise en cache ; les utilisateurs ne devront pas implémenter leurs propre logique de mise en cache.  

3.  Utiliser la résolution du cache comme une entrée lors de la création de la stratégie de chiffrement.
Vous trouverez plus d’informations sur l’utilisation de l’archivage sécurisé clé dans les exemples de code de chiffrement. <fix URL>  

## <a name="best-practices"></a>Meilleures pratiques  
Prise en charge le chiffrement est disponible uniquement dans la bibliothèque de client de stockage pour Java.

>[AZURE.IMPORTANT] N’oubliez pas de ces points importants lors de l’utilisation du chiffrement côté client :
>  
>- Lors de la lecture ou écriture dans un objet blob chiffré, utilisez blob tout téléchargement commandes et plage/entier blob téléchargement. Éviter l’écriture dans un objet blob chiffré à l’aide de protocole opérations placer le bloc, placer la liste de blocs, écrire des Pages, Pages effacer ou ajouter un bloc ; dans le cas contraire, vous pouvez endommagée le blob chiffré et rendez-le illisible.
>
>- Pour les tableaux, il existe une contrainte similaire. En veillant à ne pas mettre à jour des propriétés chiffrées sans mettre à jour les métadonnées de chiffrement.  
>
>- Si vous définissez des métadonnées sur le blob chiffré, vous risquez d’écraser les métadonnées de chiffrement requises pour le déchiffrement, étant donné que la définition des métadonnées n’est pas cumulable. Ceci est également vrai pour instantanés ; Évitez de spécifier les métadonnées lors de la création d’un instantané d’un objet blob chiffré. Si métadonnées doivent être définie, n’oubliez pas de la méthode **downloadAttributes** tout d’abord pour obtenir les métadonnées de chiffrement en cours d’appel et éviter écritures simultanées pendant le paramétrage de métadonnées.  
>
>- Activer l’indicateur **requireEncryption** dans les options de requête par défaut pour les utilisateurs doivent fonctionnent uniquement avec les données chiffrées. Pour plus d’informations, voir ci-dessous.  

## <a name="client-api--interface"></a>API cliente / Interface  
Lors de la création d’un objet EncryptionPolicy, les utilisateurs peuvent fournir une clé (implémentation IKey), uniquement une résolution (implémentation IKeyResolver), ou les deux. IKey est le type de clé simple qui est identifié à l’aide d’un identificateur de clé et qui fournit la logique pour dévoilement/retour à la ligne. IKeyResolver est utilisé pour résoudre une clé au cours du processus de déchiffrement. Il définit une méthode ResolveKey qui retourne une IKey donné un identificateur de clé. Cela permet aux utilisateurs de choisir entre plusieurs touches sont gèrent à plusieurs endroits.

- Pour le chiffrement, la clé est toujours utilisée et l’absence d’une clé provoquera une erreur.  
- Pour le déchiffrement :  
    - L’utilitaire de résolution clé est appelée si spécifié pour obtenir la clé. Si l’utilitaire de résolution est spécifié mais n’a pas une correspondance pour l’identificateur de clé, une erreur est générée.  
    - Si vider n’est pas spécifié, mais une clé est spécifiée, elle est utilisée si son identificateur correspond à l’identificateur de clé requis. Si l’identificateur ne correspond pas, une erreur est générée.  

      Les [exemples de chiffrement](https://github.com/Azure/azure-storage-net/tree/master/Samples/GettingStarted/EncryptionSamples) <fix URL>montrer à un scénario de bout en bout plus détaillé pour des objets BLOB, les files d’attente et les tables, avec l’intégration de l’archivage sécurisé clé.

### <a name="requireencryption-mode"></a>Mode RequireEncryption  
Les utilisateurs peuvent éventuellement activer un mode de fonctionnement où tous les chargements et téléchargements doivent être chiffrés. Dans ce mode, tentatives de télécharger des données qui ne sont pas chiffrées sur le service des données sans une stratégie de chiffrement échoue sur le client. L’indicateur **requireEncryption** de l’objet d’options de demande de contrôler ce comportement. Si votre application sera chiffrer tous les objets stockés dans le stockage Azure, vous pouvez définir la propriété **requireEncryption** les options de requête par défaut pour l’objet du service client.   

Par exemple, utilisez **CloudBlobClient.getDefaultRequestOptions().setRequireEncryption(true)** à chiffrement pour tous les objets blob diverses opérations effectuées par le biais de cet objet client.

### <a name="blob-service-encryption"></a>Chiffrement de service d’objets BLOB  
Créer un objet **BlobEncryptionPolicy** et définir les options de demande (par API ou à un niveau client à l’aide de **DefaultRequestOptions**). Tout le reste sera géré par la bibliothèque cliente en interne.

    // Create the IKey used for encryption.
    RsaKey key = new RsaKey("private:key1" /* key identifier */);

    // Create the encryption policy to be used for upload and download.
    BlobEncryptionPolicy policy = new BlobEncryptionPolicy(key, null);

    // Set the encryption policy on the request options.
    BlobRequestOptions options = new BlobRequestOptions();
    options.setEncryptionPolicy(policy);

    // Upload the encrypted contents to the blob.
    blob.upload(stream, size, null, options, null);

    // Download and decrypt the encrypted contents from the blob.
    ByteArrayOutputStream outputStream = new ByteArrayOutputStream();
    blob.download(outputStream, null, options, null);

### <a name="queue-service-encryption"></a>Chiffrement de service file d’attente  
Créer un objet **QueueEncryptionPolicy** et définir les options de demande (par API ou à un niveau client à l’aide de **DefaultRequestOptions**). Tout le reste sera géré par la bibliothèque cliente en interne.

    // Create the IKey used for encryption.
    RsaKey key = new RsaKey("private:key1" /* key identifier */);

    // Create the encryption policy to be used for upload and download.
    QueueEncryptionPolicy policy = new QueueEncryptionPolicy(key, null);

    // Add message
    QueueRequestOptions options = new QueueRequestOptions();
    options.setEncryptionPolicy(policy);

    queue.addMessage(message, 0, 0, options, null);

    // Retrieve message
    CloudQueueMessage retrMessage = queue.retrieveMessage(30, options, null);

### <a name="table-service-encryption"></a>Chiffrement de service de table  
Outre la création d’une stratégie de chiffrement et en définissant les options de requête, vous devez spécifier un **EncryptionResolver** dans **TableRequestOptions**ou définir l’attribut [chiffrer] sur obtention et de réglage de l’entité.

### <a name="using-the-resolver"></a>À l’aide de l’utilitaire de résolution  

    // Create the IKey used for encryption.
    RsaKey key = new RsaKey("private:key1" /* key identifier */);

    // Create the encryption policy to be used for upload and download.
    TableEncryptionPolicy policy = new TableEncryptionPolicy(key, null);

    TableRequestOptions options = new TableRequestOptions()
    options.setEncryptionPolicy(policy);
    options.setEncryptionResolver(new EncryptionResolver() {
        public boolean encryptionResolver(String pk, String rk, String key) {
            if (key == "foo")
            {
                return true;
            }
            return false;
        }
    });

    // Insert Entity
    currentTable.execute(TableOperation.insert(ent), options, null);

    // Retrieve Entity
    // No need to specify an encryption resolver for retrieve
    TableRequestOptions retrieveOptions = new TableRequestOptions()
    retrieveOptions.setEncryptionPolicy(policy);

    TableOperation operation = TableOperation.retrieve(ent.PartitionKey, ent.RowKey, DynamicTableEntity.class);
    TableResult result = currentTable.execute(operation, retrieveOptions, null);

### <a name="using-attributes"></a>À l’aide d’attributs  
Comme indiqué ci-dessus, si l’entité mettent en œuvre, TableEntity, puis la lecture et propriétés peuvent être décorées avec l’attribut [chiffrer] au lieu de spécifier **EncryptionResolver**.

    private string encryptedProperty1;

    @Encrypt
    public String getEncryptedProperty1 () {
        return this.encryptedProperty1;
    }

    @Encrypt
    public void setEncryptedProperty1(final String encryptedProperty1) {
        this.encryptedProperty1 = encryptedProperty1;
    }

## <a name="encryption-and-performance"></a>Chiffrement et les performances  
Notez que le chiffrement votre stockage de données aboutit à surcharge supplémentaire des performances. La clé de contenu et IV doivent être généré, le contenu proprement dit doit être chiffré et métadonnées supplémentaires doivent être mis en forme et téléchargées. Cette surcharge varient selon la quantité de données en cours de chiffrement. Nous vous recommandons de toujours tester leurs performances lors du développement des applications.

## <a name="next-steps"></a>Étapes suivantes  

- Télécharger la [Bibliothèque de Client de stockage Azure package Maven Java](http://mvnrepository.com/artifact/com.microsoft.azure/azure-storage)  
- Télécharger la [bibliothèque de Client de stockage Azure pour GitHub du Code Source Java](https://github.com/Azure/azure-storage-java)   
- Télécharger la bibliothèque Maven Azure clé l’archivage sécurisé pour les packages Java Maven :
    - Package de [base](http://mvnrepository.com/artifact/com.microsoft.azure/azure-keyvault-core)
    - Package [client](http://mvnrepository.com/artifact/com.microsoft.azure/azure-keyvault)
- Visitez la [clé Azure Vault Documentation](../key-vault/key-vault-whatis.md)  

<properties
    pageTitle="Prise en charge de ressources d’origine croisée partage (CORS) | Microsoft Azure"
    description="Découvrez comment activer CORS prise en charge pour les Services de stockage de Microsoft Azure."
    services="storage"
    documentationCenter=".net"
    authors="cbrooks"
    manager="carmonm"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="09/07/2016"
    ms.author="cbrooks"/>

# <a name="cross-origin-resource-sharing-cors-support-for-the-azure-storage-services"></a>Prise en charge (CORS) pour les Services de stockage Azure le partage des ressources origine croisée

Depuis la version 2013-08-15, les services de stockage Azure prend en charge le partage de ressources origine croisée (CORS) pour les services Blob, Table, file d’attente et de fichier. CORS est une fonctionnalité HTTP qui permet à une application web s’exécutant sur un domaine accéder aux ressources dans un autre domaine. Navigateurs Web implémentent une restriction de sécurité connue sous le [même origine stratégie](http://www.w3.org/Security/wiki/Same_Origin_Policy) qui empêche une page web à partir d’appeler des API dans un autre domaine. CORS fournit un moyen sécurisé pour permettre à un domaine (le domaine d’origine) appeler des API dans un autre domaine. Consultez la [spécification CORS](http://www.w3.org/TR/cors/) pour plus d’informations sur CORS.

Vous pouvez définir des règles CORS individuellement pour chacun des services de stockage, en appelant [Définir les propriétés d’objets Blob Service](https://msdn.microsoft.com/library/hh452235.aspx), [Définir les propriétés du Service file d’attente](https://msdn.microsoft.com/library/hh452232.aspx)et [Définir les propriétés de Table Service](https://msdn.microsoft.com/library/hh452240.aspx). Une fois que vous définissez les règles CORS du service, une demande authentifiée correctement effectuée par rapport au service à partir d’un autre domaine sera évaluée pour déterminer si elle est conforme aux règles que vous avez spécifié.

>[AZURE.NOTE] Notez que CORS n’est pas un mécanisme d’authentification. Toute demande effectuée par rapport à une ressource de stockage lorsque CORS est activée doit avoir une signature authentifications ou doit être effectuée par rapport à une ressource publique.

## <a name="understanding-cors-requests"></a>Présentation des requêtes CORS

Une demande de CORS à partir d’un domaine d’origine peut comprendre deux requêtes séparées :

- Une demande de contrôle en amont, qui effectue des requêtes les restrictions CORS imposées par le service. La demande de contrôle en amont est requise, à moins que la méthode de requête est une [méthode simple](http://www.w3.org/TR/cors/), ce qui signifie que GET, tête ou POST.

- La demande réelle, effectuée par rapport à la ressource souhaitée.

### <a name="preflight-request"></a>Demande de contrôle en amont

Les requêtes de demande de contrôle en amont les restrictions CORS qui ont été établies pour le service de stockage par le propriétaire du compte. Le navigateur web (ou autre agent utilisateur) envoie une demande d’OPTIONS qui inclut les en-têtes de demande, domaine méthode et d’origine. Le service de stockage évalue l’opération prévue selon un ensemble de règles CORS qui spécifient les domaines d’origine, des méthodes de demande et les en-têtes de demande peuvent être spécifiés sur une demande réelle par rapport à une ressource de stockage préconfiguré.

Si CORS est activée pour le service et il existe une règle CORS qui correspond à la demande de contrôle en amont, le service répond avec un code d’état 200 (OK) et inclut les en-têtes de contrôle d’accès requis dans la réponse.

Si CORS n’est pas activée pour le service ou aucune règle CORS correspond à la demande de contrôle en amont, le service répondra avec code d’état 403 (refusé).

Si la demande OPTIONS ne contient pas les en-têtes CORS requis (en-têtes Origin et accès contrôle demande méthode), le service répondra avec code d’état 400 (demande incorrecte).

Notez qu’une demande de contrôle en amont est évaluée par rapport au service (Blob, file d’attente et Table) et non pour la ressource demandée. Le propriétaire du compte doit avoir activé CORS dans le cadre des propriétés du service de compte dans l’ordre de la demande réussir.

### <a name="actual-request"></a>Demande réelle

Une fois la demande de contrôle en amont est acceptée et la réponse est renvoyée, le navigateur envoie la demande réelle par rapport à la ressource de stockage. Le navigateur refuse la demande immédiatement si la demande de contrôle en amont est rejetée.

La demande est considérée comme normale requête auprès du service de stockage. La présence de l’en-tête d’origine indique que la requête est une requête CORS et le service vérifie les règles CORS correspondantes. Si une correspondance, les en-têtes de contrôle d’accès sont ajoutés à la réponse et envoyées au client. Si une correspondance n’est pas trouvée, les en-têtes de contrôle d’accès CORS ne sont pas renvoyés.

## <a name="enabling-cors-for-the-azure-storage-services"></a>Activation CORS pour les services de stockage Azure

Règles CORS sont définis au niveau du service, vous devez activer ou désactiver les CORS pour chaque service (Blob, file d’attente et Table) séparément. Par défaut, CORS est désactivée pour chaque service. Pour activer le CORS, vous devez définir les propriétés du service approprié à l’aide de la version 2013-08-15 ou version ultérieure et ajouter des règles CORS pour les propriétés du service. Pour savoir comment activer ou désactiver les CORS d’un service et à définir des règles CORS, reportez-vous à [Définition des propriétés de Service Blob](https://msdn.microsoft.com/library/hh452235.aspx), [Définir les propriétés du Service file d’attente](https://msdn.microsoft.com/library/hh452232.aspx)et [Définir les propriétés de Table Service](https://msdn.microsoft.com/library/hh452240.aspx).

Voici un exemple d’une règle CORS unique, spécifiée via une opération de définir les propriétés de Service :

    <Cors>    
        <CorsRule>
            <AllowedOrigins>http://www.contoso.com, http://www.fabrikam.com</AllowedOrigins>
            <AllowedMethods>PUT,GET</AllowedMethods>
            <AllowedHeaders>x-ms-meta-data*,x-ms-meta-target*,x-ms-meta-abc</AllowedHeaders>
            <ExposedHeaders>x-ms-meta-*</ExposedHeaders>
            <MaxAgeInSeconds>200</MaxAgeInSeconds>
        </CorsRule>
    <Cors>

Chaque élément inclus dans la règle CORS est décrite ci-dessous :

- **AllowedOrigins**: le domaine d’origine qui est autorisés à effectuer une demande auprès du service de stockage via CORS. Le domaine d’origine est le domaine d'où provient la requête. Notez que l’origine doit être une correspondance exacte respectant la casse à l’origine de l’âge utilisateur envoie au service. Vous pouvez également utiliser le caractère générique ' *' pour permettre à tous les domaines d’origine effectuer des demandes via CORS. Dans l’exemple ci-dessus, les domaines [http://www.contoso.com](http://www.contoso.com) et [http://www.fabrikam.com](http://www.fabrikam.com) peuvent envoyer des demandes auprès du service à l’aide de CORS.

- **AllowedMethods**: les méthodes (verbes de requête HTTP) que le domaine d’origine peut-être utiliser pour une demande de CORS. Dans l’exemple ci-dessus, seules les demandes de placer et obtenir sont autorisés.

- **AllowedHeaders**: les en-têtes de demande que le domaine d’origine peut spécifier dans la demande CORS. Dans l’exemple ci-dessus, tous les en-têtes de métadonnées commençant par x-ms-métadonnées, cible x-ms-meta et x-ms-meta-abc sont autorisés. Notez que le caractère générique ' *' indique que tout début de l’en-tête par le préfixe spécifié est autorisée.

- **ExposedHeaders**: les en-têtes de réponse qui peuvent être envoyés dans la réponse à la demande CORS et exposés par le navigateur à l’émetteur de la demande. Dans l’exemple ci-dessus, le navigateur est invité à exposer les commençant en-tête x-ms-meta.

- **MaxAgeInSeconds**: le délai maximal qu’un navigateur doit mettre en cache les OPTIONS de contrôle en amont demander.

Les services de stockage Azure prend en charge la spécification avec préfixe en-têtes pour les éléments de la **AllowedHeaders** et le **ExposedHeaders** . Pour permettre à une catégorie d’en-têtes, vous pouvez spécifier un préfixe commun à cette catégorie. Par exemple, si vous spécifiez *x-ms-meta** comme un en-tête avec préfixe qui établit une règle adaptés à tous les en-têtes qui commencent par x-ms-meta.

Les limitations suivantes s’appliquent aux règles CORS :

- Vous pouvez spécifier un maximum de cinq règles CORS par le service de stockage (Blob, Table et file d’attente).
- La taille maximale de tous les CORS règles paramètres lors de la demande, à l’exclusion des balises XML, ne doit pas dépasser 2 Ko.
- La longueur d’un en-tête autorisé, en-tête exposée ou d’origine autorisé ne doit pas dépasser 256 caractères.
- En-têtes autorisés et exposée peuvent être :
  - En-têtes littérales, où le nom d’en-tête exacte est fourni, telles que **traitées de x-ms-meta**. Un maximum de 64 en-têtes littérales peut être indiqué dans la demande.
  - En-têtes, où un préfixe de l’en-tête est fourni, par exemple **x-ms-métadonnées**le préfixe *. Spécifier un préfixe de cette manière permet ou expose tout en-tête commence par le préfixe donné. Un maximum de deux en-têtes avec préfixe peut être indiqué dans la demande.
- Les méthodes (ou verbes HTTP) spécifiée dans l’élément **AllowedMethods** doivent respecter les méthodes pris en charge par le service de stockage Azure API. Méthodes prises en charge sont supprimer, GET, tête, fusionner, publier, OPTIONS et place.

## <a name="understanding-cors-rule-evaluation-logic"></a>Logique de présentation CORS règle d’évaluation

Lorsqu’un service de stockage reçoit une demande de contrôle en amont ou réelle, il prend la valeur cette requête basée sur les règles CORS que vous avez établi pour le service via l’opération de définir les propriétés de Service appropriée. Règles CORS sont évaluées dans l’ordre dans lequel ils ont été définis dans le corps de la demande de l’opération de définir les propriétés de Service.

Règles CORS sont évaluées comme suit :

1. Tout d’abord, le domaine d’origine de la demande est recherchée dans les domaines répertoriés pour l’élément **AllowedOrigins** . Si le domaine d’origine est inclus dans la liste ou tous les domaines autorisés avec le caractère générique ' *', puis les règles d’évaluation produit. Si le domaine d’origine n’est pas inclus, la requête échoue.

2. Ensuite, la méthode (ou verbe HTTP) de la demande est recherchée dans les méthodes répertoriées dans l’élément **AllowedMethods** . Si la méthode est incluse dans la liste, évaluation règles s’exécute ; Si ce n’est pas le cas, la requête échoue.

3. Si la requête correspond à une règle dans son domaine d’origine et sa méthode, cette règle est activée au processus de la demande et pas plus de règles sont évaluées. Avant de la demande peut réussir, cependant, tous les en-têtes spécifiées dans la demande sont comparées les en-têtes répertoriés dans l’élément **AllowedHeaders** . Si les en-têtes envoyés ne correspondent pas les en-têtes autorisés, la requête échoue.

Dans la mesure où les règles sont traitées dans l’ordre qu'elles ne figurent pas dans le corps de la requête, recommandations recommandent que vous spécifiez les règles plus restrictifs en ce qui concerne les origines d’abord dans la liste, afin que celles-ci sont évaluées en premier. Spécifier des règles qui sont moins restrictifs – par exemple, une règle pour permettre à toutes les origines – à la fin de la liste.

### <a name="example--cors-rules-evaluation"></a>Exemple – CORS règles d’évaluation

L’exemple suivant montre un corps de demande partielle d’une opération de définir des règles CORS pour les services de stockage. Pour plus d’informations sur la construction de la demande, voir [Définir les propriétés d’objets Blob Service](https://msdn.microsoft.com/library/hh452235.aspx), [Définir les propriétés du Service file d’attente](https://msdn.microsoft.com/library/hh452232.aspx)et [Définir les propriétés de Table Service](https://msdn.microsoft.com/library/hh452240.aspx) .

    <Cors>
        <CorsRule>
            <AllowedOrigins>http://www.contoso.com</AllowedOrigins>
            <AllowedMethods>PUT,HEAD</AllowedMethods>
            <MaxAgeInSeconds>5</MaxAgeInSeconds>
            <ExposedHeaders>x-ms-*</ExposedHeaders>
            <AllowedHeaders>x-ms-blob-content-type, x-ms-blob-content-disposition</AllowedHeaders>
        </CorsRule>
        <CorsRule>
            <AllowedOrigins>*</AllowedOrigins>
            <AllowedMethods>PUT,GET</AllowedMethods>
            <MaxAgeInSeconds>5</MaxAgeInSeconds>
            <ExposedHeaders>x-ms-*</ExposedHeaders>
            <AllowedHeaders>x-ms-blob-content-type, x-ms-blob-content-disposition</AllowedHeaders>
        </CorsRule>
        <CorsRule>
            <AllowedOrigins>http://www.contoso.com</AllowedOrigins>
            <AllowedMethods>GET</AllowedMethods>
            <MaxAgeInSeconds>5</MaxAgeInSeconds>
            <ExposedHeaders>x-ms-*</ExposedHeaders>
            <AllowedHeaders>x-ms-client-request-id</AllowedHeaders>
        </CorsRule>
    </Cors>

Ensuite, envisagez les demandes CORS suivantes :

Demande||| Réponse||
---|---|---|---|---
**Méthode** |**Origine** |**En-têtes de requête** |**Correspondance de règle** |**Résultat**
**PLACER** | http://www.contoso.com |type du contenu blob x ms | Première règle |Opération réussie
**Télécharger** | http://www.contoso.com| x-ms-blob-type de contenu | Deuxième règle |Opération réussie
**Télécharger** | http://www.contoso.com| x-ms-blob-type de contenu | Deuxième règle | Échec

La première requête correspond à la première règle : le domaine d’origine correspond à l’origine autorisés, la méthode correspond aux méthodes autorisées, et l’en-tête correspond aux en-têtes autorisés – et donc a réussi.

La deuxième requête ne correspond pas à la première règle, car la méthode ne correspond pas aux méthodes autorisées. Il est le cas, cependant, correspondant à la deuxième règle, il a réussi.

La troisième requête correspond la deuxième règle dans son domaine d’origine et la méthode, donc aucuns plus de règles ne sont évaluées. Toutefois, l' *en-tête x-ms-client-demande-id* n’est pas autorisée par la deuxième règle, afin que la requête échoue, bien que la sémantique de la troisième règle aurait autorisés à réussir.

>[AZURE.NOTE] Bien que cet exemple montre une règle moins restrictive avant un plus restrictif, en général la meilleure solution consiste à répertorier les règles plus restrictifs en premier.

## <a name="understanding-how-the-vary-header-is-set"></a>Comprendre comment l’en-tête de variation est défini

L’en-tête de *variation* est un en-tête HTTP/1.1 standard composée d’un ensemble de champs d’en-tête demande que conseiller l’agent utilisateur ou du navigateur sur les critères qui ont été sélectionnés par le serveur pour traiter la requête. L’en-tête de *variation* est utilisé principalement pour la mise en cache par des proxys, les navigateurs et les CDN, qui permet de déterminer comment la réponse doit être mis en cache. Pour plus d’informations, consultez la spécification de l' [en-tête de variation](http://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html).

Lorsque le navigateur ou un autre agent utilisateur met en cache la réponse à une demande CORS, le domaine d’origine est mis en cache comme origine autorisé. Lorsqu’un domaine de second envoie la même demande pour une ressource de stockage alors que le cache est actif, l’agent utilisateur récupère le domaine d’origine mis en cache. Le deuxième domaine ne correspond pas au domaine mis en cache, la requête échoue lorsqu’il devraient normalement réussir. Dans certains cas, le stockage Azure définit l’en-tête de variation à l' **origine** pour indiquer à l’agent utilisateur d’envoyer la demande suivante CORS du service lorsque le domaine demandeur diffère de l’origine de la mise en cache.

Stockage Azure définit l’en-tête de *variation* à **l’origine** pour les requêtes GET/tête réels dans les cas suivants :

- Lorsque l’origine de la requête correspond exactement à l’origine autorisé définie par une règle CORS. Pour être une correspondance exacte, la règle CORS ne peut pas inclure un caractère générique ' * ' caractère.

- Il n’existe aucune règle correspondant à l’origine de la demande, mais CORS est activée pour le service de stockage.

Dans le cas où une demande GET/tête correspond à une règle CORS qui permet de toutes les origines, la réponse indique que toutes les origines sont autorisés, et le cache de l’agent utilisateur autorise les demandes suivantes à partir de n’importe quel domaine d’origine alors que le cache est actif.

Notez que pour les requêtes utilisant des méthodes différentes GET/tête, les services de stockage pas définissent l’en-tête variation, étant donné que les réponses à ces méthodes ne sont pas mis en cache par les agents utilisateurs.

Le tableau suivant indique comment Azure stockage répondra aux requêtes GET/tête basées sur les cas mentionnés précédemment :

Demande|Paramètre de compte et résultat de l’évaluation de la règle|||Réponse|||
---|---|---|---|---|---|---|---|---
**En-tête d’origine présent sur demande** | **Ou plusieurs règles CORS spécifiés pour ce service** | **Règle de mise en correspondance existe qui permet de toutes les origines (*)** | **Règle de correspondance existe pour la correspondance exacte d’origine** | **Réponse inclut en-tête variation défini à l’origine** | **Réponse inclut accès contrôle-autorisé-origine : «*»** | **Réponse inclut Access--exposé-en-têtes de contrôle**
N°|N°|N°|N°|N°|N°|N°
N°|Oui|N°|N°|Oui|N°|N°
N°|Oui|Oui|N°|N°|Oui|Oui
Oui|N°|N°|N°|N°|N°|N°
Oui|Oui|N°|Oui|Oui|N°|Oui
Oui|Oui|N°|N°|Oui|N°|N°
Oui|Oui|Oui|N°|N°|Oui|Oui


## <a name="billing-for-cors-requests"></a>Facturation pour les demandes CORS

Les demandes de contrôle en amont réussies sont facturés si vous avez activé CORS pour aucun des services de stockage pour votre compte (en appelant [Définir les propriétés d’objets Blob Service](https://msdn.microsoft.com/library/hh452235.aspx), [Définir les propriétés du Service file d’attente](https://msdn.microsoft.com/library/hh452232.aspx)ou [Définir les propriétés du Service Table](https://msdn.microsoft.com/library/hh452240.aspx)). Pour réduire les frais, pensez à définir l’élément **MaxAgeInSeconds** dans vos règles CORS une valeur plus élevée afin que l’agent utilisateur met en cache la demande.

Demandes en amont échec ne seront pas facturées.

## <a name="next-steps"></a>Étapes suivantes

[Définir les propriétés de Service d’objets Blob](https://msdn.microsoft.com/library/hh452235.aspx)

[Définir les propriétés du Service file d’attente](https://msdn.microsoft.com/library/hh452232.aspx)

[Définir les propriétés de Service de Table](https://msdn.microsoft.com/library/hh452240.aspx)

[Spécification le partage des ressources origine croisée W3C](http://www.w3.org/TR/cors/)

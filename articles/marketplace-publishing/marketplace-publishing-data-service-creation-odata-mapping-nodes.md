<properties
   pageTitle="Guide de création d’un Service de données pour le marché | Microsoft Azure"
   description="Comment créer, certifier et déployer un Service de données pour obtenir des instructions détaillées acheter sur Azure Marketplace."
   services="marketplace-publishing"
   documentationCenter=""
   authors="HannibalSII"
   manager="hascipio"
   editor=""/>

   <tags
      ms.service="marketplace"
      ms.devlang="na"
      ms.topic="article"
      ms.tgt_pltfrm="na"
      ms.workload="na"
      ms.date="08/26/2016"
      ms.author="hascipio; avikova" />

# <a name="understanding-the-nodes-schema-for-mapping-an-existing-web-service-to-odata-through-csdl"></a>Présentation du schéma de nœuds pour le mappage d’un service web existant à OData via CSDL

>[AZURE.IMPORTANT] **Pour le moment nous ne sont plus arrivant les nouvelles publications de Service de données. Nouvelle dataservices ne seront pas obtenir approuvées pour la liste.** Si vous avez une application métier SaaS que vous voulez publier sur AppSource vous trouverez plus d’informations [ici](https://appsource.microsoft.com/partners). Si vous avez un applications IaaS ou service développeur que vous voulez publier sur Azure Marketplace, vous pouvez en savoir plus [ici](https://azure.microsoft.com/marketplace/programs/certified/).

Ce document vous aide à clarifier la structure de nœud de mappage d’un protocole OData à CSDL. Il est important de noter que la structure de nœud est bien du code XML. Schéma racine, parents et enfants est donc applicable lors de la conception de votre mappage OData.

## <a name="ignored-elements"></a>Éléments ignorés
Voici les éléments CSDL haut niveau (nœuds XML) ne passent pas devant être utilisé par le serveur principal Azure Marketplace pendant l’importation de métadonnées du service web. Ils peuvent être présents mais sont ignorées.

| Élément | Étendue |
|----|----|
| À l’aide d’élément | Le nœud, sub nœuds et tous les attributs |
| Élément documentation | Le nœud, sub nœuds et tous les attributs |
| ComplexType | Le nœud, sub nœuds et tous les attributs |
| Élément association | Le nœud, sub nœuds et tous les attributs |
| Propriété étendue | Le nœud, sub nœuds et tous les attributs |
| EntityContainer | Uniquement les attributs suivants sont ignorés : *étend* et *AssociationSet* |
| Schéma | Uniquement les attributs suivants sont ignorés : *Namespace* |
| FunctionImport | Uniquement les attributs suivants sont ignorés : *Mode* (valeur par défaut de ln est supposé égal) |
| EntityType | Uniquement les nœuds sub suivants sont ignorés : *clé* et *PropertyRef* |

Suit décrit les modifications (ajouté et ignoré des éléments) à différents nœuds CSDL XML en détail.

## <a name="functionimport-node"></a>Nœud FunctionImport
Nœud FunctionImport représente une URL (point d’entrée) qui expose un service à l’utilisateur final. Le nœud permet décrivant comment l’URL est adressée, quels sont les paramètres sont disponibles à l’utilisateur final et comment ces paramètres sont fournis.

Plus d’informations sur ce nœud sont trouvées [ici] [MSDNFunctionImportLink]

[MSDNFunctionImportLink] : (https://msdn.microsoft.com/library/cc716710 (v=vs.100).aspx)

Voici les attributs supplémentaires (ou compléments à attributs) qui sont exposés par le nœud FunctionImport :

**d:baseUri** -
modèle URI de la ressource reste qui est exposée à Marketplace. Marketplace utilise le modèle pour créer des requêtes auprès du service web REST. Le modèle URI contient des espaces réservés pour les paramètres dans le formulaire de {nom_paramètre}, où nom_paramètre est le nom du paramètre. Exemple : apiVersion = {apiVersion}.
Les paramètres sont autorisés à s’affichent en tant que paramètres URI ou dans le cadre du chemin URI. Dans le cas de l’apparence du chemin d’accès, ils sont toujours obligatoires (ne peut pas être marqué comme nullable). *Exemple :*`d:BaseUri="http://api.MyWeb.com/Site/{url}/v1/visits?start={start}&amp;end={end}&amp;ApiKey=3fadcaa&amp;Format=XML"`

**Nom** : le nom de la fonction importée.  Ne peut pas être identique à d’autres noms définis dans le fichier CSDL.  Exemple : Nom = « GetModelUsageFile »

**EntitySet** *(facultatif)* - si la fonction retourne une collection de types d’entités, la valeur de l' **EntitySet** doit être l’entité jeu auquel appartient la collection de sites. Dans le cas contraire, l’attribut **EntitySet** ne doit pas être utilisé. *Exemple :*`EntitySet="GetUsageStatisticsEntitySet"`

**ReturnType** *(Facultatif)* - Spécifie le type d’éléments renvoyés par l’URI.  N’utilisez pas cet attribut si la fonction ne renvoie pas une valeur. Les types pris en charge sont les suivants :

 - **Collection de sites (<Entity type name>)**: Spécifie une collection de types d’entités définis. Le nom ne figure pas dans l’attribut Name du nœud EntityType. Par exemple, la collection de sites (WXC. HourlyResult).
 - **Premières (<mime type>)**: Spécifie un document/blob brut qui est renvoyé à l’utilisateur. Un exemple est Raw(image/jpeg) d’autres exemples :

  - ReturnType="Raw(text/plain) »
  - ReturnType = « collection de sites (sage. DeleteAllUsageFilesEntity) « *

**d:Paging** - spécifie comment pagination est gérée par la ressource reste. Les valeurs de paramètres sont utilisés dans des branches, par exemple, page = {$page} & itemsperpage = {$size} les options disponibles sont :

- **Aucun :** aucune pagination n’est disponible
- **Ignorer :** pagination est exprimée via une logique « sauter » et « suivre » (en haut). Ignorer les déviations sur les éléments M et prendre renvoie les éléments N. Valeur de paramètre : $skip
- **Prendre :** Prendre renvoie les éléments N. Valeur de paramètre : $take
- **PageSize :** pagination est exprimée via une page logique et une taille (éléments par page). Page représente la page active est renvoyée. Valeur de paramètre : $page
- **Taille :** taille représente le nombre d’éléments renvoyés pour chaque page. Valeur de paramètre : $size

**d:AllowedHttpMethods** *(Facultatif)* - Spécifie le verbe est géré par la ressource reste. En outre, limite verbe acceptée à la valeur spécifiée.  Par défaut = POST.  *Exemple :* `d:AllowedHttpMethods="GET"` Les options disponibles sont :

- **Obtenir :** généralement utilisé pour renvoyer des données
- **POST :** généralement utilisée pour insérer de nouvelles données
- **Placer :** généralement utilisée pour mettre à jour des données
- **Supprimer :** permet de supprimer des données

Nœud enfant supplémentaire (non couverts par la documentation CSDL) au sein du nœud FunctionImport est :

**d:RequestBody** *(Facultatif)* - le corps de la requête est utilisée pour indiquer que la demande attend un corps soient envoyées. Paramètres peuvent être données dans le corps de la demande. Ils sont exprimés au sein des accolades, par exemple {parameterName}. Ces paramètres sont mappés à partir de l’entrée d’utilisateur dans le corps est automatiquement transférée vers service du fournisseur de contenu. L’élément requestBody possède un attribut avec nom httpMethod. L’attribut permet de deux valeurs :

- **POST :** Utilisé si la demande est une publication HTTP
- **Obtenir :** Utilisé si la demande est un HTTP GET

    Exemple :

        `<d:RequestBody d:httpMethod="POST">
        <![CDATA[
        <req1:Request xmlns:r1="http://schemas.mysite.com//generic/requests/1" Version="1.0">
        <req1:Query>{Query}</req1:Query><req1:AppId>D453474</req1:AppId>
        <req:DestinationSchemas><req1:Schema>Generic.RequestResponse[1.0]</req1:Schema>
        </req1:DestinationSchemas>
        </req1: Request>
        ]]>
        </d:RequestBody>`

**d:Namespaces** et **d:Namespace** - ce nœud décrit les espaces de noms définis dans le fichier XML qui est retourné par l’importation de fonction (point de terminaison URI). Le code XML qui est renvoyé par le service principal peut contenir n’importe quel nombre d’espaces de noms de différencier le contenu est renvoyé. **Tous ces espaces de noms, si utilisé dans des requêtes XPath d:Map ou d:Match doivent être répertoriés.** Le nœud d:Namespaces contient une liste de jeux/des nœuds d:Namespace. Chacun d’eux répertorie un espace de noms utilisé dans la réponse du service principal. L’attribut du nœud d:Namespace sont les suivantes :

-   **d:Prefix :** Préfixe de l’espace de noms, comme illustré dans les résultats XML renvoyés par le service, par exemple, f:FirstName, f:LastName, où f est le préfixe.
- **d:Uri :** URI complet de l’espace de noms utilisé dans le document de résultat. Il représente la valeur qui le préfixe est résolu en cours d’exécution.

**d:ErrorHandling** *(Facultatif)* : ce nœud contient des modalités de gestion des erreurs. Chacune des conditions est comparée le résultat retourné par service du fournisseur. Si une condition représente le code d’erreur HTTP proposé qu'un message d’erreur est renvoyé à l’utilisateur final.

**d:ErrorHandling** *(Facultatif)* et **d:Condition** *(facultatif)* - le nœud de condition contient une condition est cochée dans le résultat retourné par le service du fournisseur de contenu. Les attributs **obligatoires** sont les suivantes :

- **d:Match :** Expression XPath qui vérifie si une valeur de nœud/donnée est présente produit une sortie du fournisseur de contenu XML. L’expression XPath est exécuté sur la sortie et doit retourner la valeur true si la condition est une correspondance ou false dans le cas contraire.
- **d:HttpStatusCode :** Le code d’état HTTP qui doit être retourné par Marketplace dans le cas la condition correspond à. Marketplace signalizes erreurs à l’utilisateur par le biais codes d’état HTTP. Liste des codes d’état HTTP sont disponibles dans la page http://en.wikipedia.org/wiki/HTTP_status_code
- **d:ErrorMessage :** Le message d’erreur est retourné – avec le code d’état HTTP – à l’utilisateur final. Il s’agit d’un message d’erreur convivial qui ne contient aucun secret.

**d:Title** *(Facultatif)* : permet de décrire le titre de la fonction. Provenant de la valeur pour le titre

- L’attribut facultatif carte (xpath) qui indique où trouver le titre dans la réponse retournée par la demande de service.
- - Ou - le titre spécifié en tant que valeur du nœud.

**d:Rights** *(Facultatif)* : les droits (par exemple, copyright) associées à la fonction. La valeur pour les droits proviennent :

- L’attribut facultatif carte (xpath) qui indique où se trouvent les droits dans la réponse renvoyée à partir de la demande de service.
-   - Ou - les droits spécifiés en tant que valeur du nœud.

**d:description** *(Facultatif)* : une brève description de la fonction. Provenant de la valeur de la description

- L’attribut facultatif carte (xpath) qui indique où trouver la description de la réponse retournée à partir de la demande de service.
- - Ou – la description spécifié en tant que valeur du nœud.

**d:EmitSelfLink** - *Voir exemple ci-dessus « FunctionImport 'Pagination' à travers les renvoyée données »*

**d:EncodeParameterValue** - extension facultative à OData

**d:QueryResourceCost** - extension facultative à OData

**d:Map** - extension facultative à OData

**d:Headers** - extension facultative à OData

**d:Headers** - extension facultative à OData

**d:Value** - extension facultative à OData

**d:HttpStatusCode** - extension facultative à OData

**d:ErrorMessage** - Extension facultative à OData

## <a name="parameter-node"></a>Nœud du paramètre

Ce nœud représente un paramètre, qui est exposé dans le cadre du modèle URI / corps qui a été spécifié dans le nœud FunctionImport de requête.

Page d’un document très utile pour plus d’informations sur le nœud « Élément de paramètre » se trouve à [ici](http://msdn.microsoft.com/library/ee473431.aspx) (utilisez la liste déroulante **Version autres** pour sélectionner une autre version si nécessaire pour afficher la documentation). *Exemple :*`<Parameter Name="Query" Nullable="false" Mode="In" Type="String" d:Description="Query" d:SampleValues="Rudy Duck" d:EncodeParameterValue="true" MaxLength="255" FixedLength="false" Unicode="false" annotation:StoreGeneratedPattern="Identity"/>`

| Attribut de paramètre | Est requis | Valeur |
|----|----|----|
| Nom | Oui | Le nom du paramètre. Respecter la casse !  Respecter la casse BaseUri. **Exemple :**`<Property Name="IsDormant" Type="Byte" />` |
| Type | Oui | Le type de paramètre. La valeur doit être un **EDMSimpleType** ou un type complexe qui se trouve dans le cadre du modèle. Pour plus d’informations, voir « 6 types de propriété/paramètre pris en charge ».  (Respecter la casse ! Premier caractère est en majuscule, reste sont en minuscules).  Voir aussi, [conceptuelle modèle Types (CSDL)] [MSDNParameterLink]. **Exemple :**`<Property Name="LimitedPartnershipID " Type="Int32" />` |
| Mode | N° | **In**, Out ou entrée-sortie selon si le paramètre est une entrée, la sortie ou le paramètre d’entrée/sortie. (Uniquement « IN » est disponible dans Azure Marketplace.) **Exemple :**`<Parameter Name="StudentID" Mode="In" Type="Int32" />` |
| MaxLength | N° | Longueur du paramètre maximale autorisée. **Exemple :**`<Property Name="URI" Type="String" MaxLength="100" FixedLength="false" Unicode="false" />` |
| Précision | N° | La précision du paramètre. **Exemple :**`<Property Name="PreviousDate" Type="DateTime" Precision="0" />` |
| Échelle | N° | L’échelle du paramètre. **Exemple :**`<Property Name="SICCode" Type="Decimal" Precision="10" Scale="0" />` |

[MSDNParameterLink] : (http://msdn.microsoft.com/library/bb399548 (v=VS.100).aspx)

Les attributs qui ont été ajoutés à la spécification CSDL sont les suivantes :

| Attribut de paramètre | Description |
|----|----|
| **d:Regex** *(Facultatif)* | Une instruction regex utilisée pour valider la valeur d’entrée pour le paramètre. Si la valeur d’entrée ne correspond pas à la déclaration de la valeur est rejetée. Cela permet de spécifier également un ensemble de valeurs possibles, par exemple, ^ [0-9] + ? $ pour autoriser uniquement les nombres. **Exemple :** ' < nom du paramètre = « nom » en Mode = « In » Type = « Chaîne » d : Nullable = d:Regex « false » = « ^ [a-zA-Z] * $« d:Description = « un nom qui ne peut pas contenir d’espaces ou des caractères non alphanumériques non anglais » d:SampleValues = « Georges|John|Thomas|James « / > » |
| **d:Enum** *(Facultatif)* | Liste de valeurs valides pour le paramètre séparés par un canal. Le type des valeurs doit correspondre au type du paramètre défini. Exemple : « anglais|métrique|brutes`. Enum will display as a selectable dropdown list of parameters in the UI (service explorer). **Example:** `< nom du paramètre = « Durée » Type = « Chaîne » Mode = « In » Nullable = « true » d:Enum = « 1 an|5years|10years « / > » |
| **d : Nullable** *(Facultatif)* | Permet de définir si un paramètre peut être null. La valeur par défaut : true. Toutefois, les paramètres sont présentés dans le cadre du chemin d’accès dans le modèle URI ne peut pas être null. Lorsqu’elle est l’attribut est définie sur false pour ces paramètres – l’entrée d’utilisateur est ignorée. **Exemple :**`<Parameter Name="BikeType" Type="String" Mode="In" Nullable="false"/>` |
| **d:SampleValue** *(Facultatif)* | Un exemple de valeur pour l’afficher sous forme de Remarque au Client dans l’interface utilisateur.  Il est possible d’ajouter plusieurs valeurs à l’aide d’une liste séparée par des canaux, c'est-à-dire « une|b|c` **Example:** `< nom du paramètre = « BikeOwner » Type = « Chaîne » Mode = « In » d:SampleValues = « Georges|John|Thomas|James « / > » |

## <a name="entitytype-node"></a>Nœud EntityType

Ce nœud représente un des types de renvoyés par Marketplace à l’utilisateur final. Il contienne également le mappage à partir du résultat retourné par le service du fournisseur de contenu pour les valeurs qui sont retournés à l’utilisateur final.

Plus d’informations sur ce nœud sont trouvées en [ici](http://msdn.microsoft.com/library/bb399206.aspx) (utilisez la liste déroulante **Version autres** pour sélectionner une autre version si nécessaire pour afficher la documentation).

| Nom de l’attribut | Est requis | Valeur |
|----|----|----|
| Nom | Oui | Le nom du type d’entité. **Exemple :**`<EntityType Name="ListOfAllEntities" d:Map="//EntityModel">` |
| BaseType | N° | Le nom d’un autre type d’entité qui est le type de base du type d’entité qui est défini. **Exemple :**`<EntityType Name="PhoneRecord" BaseType="dqs:RequestRecord">` |

Les attributs qui ont été ajoutés à la spécification CSDL sont les suivantes :

**d:Map** - expression XPath exécutée sur la sortie du service. L’hypothèse est que la sortie du service contienne un ensemble d’éléments qui se répètent, tels qu’un ATOM flux où il est un ensemble de nœuds des entrées qui se répètent. Chacun de ces extensible nœuds contient un enregistrement. L’expression XPath est alors spécifié pour qu’il pointe sur le nœud extensible individuel dans les résultats de service du fournisseur de contenu qui contient les valeurs d’un enregistrement distinct. Exemple de sortie à partir du service :

        `<foo>
          <bar> … content … </bar>
          <bar> … content … </bar>
          <bar> … content … </bar>
        </foo>`

L’expression XPath serait être /foo/barre, car chaque de la barre de nœud est le nœud extensible dans la sortie, qui contient le contenu effectif est renvoyé à l’utilisateur final.

**Clé** - cet attribut est ignoré par Marketplace. RESTE en fonction des services web, en général n’exposent pas une clé primaire.


## <a name="property-node"></a>Nœud de propriété

Ce nœud contient une propriété de l’enregistrement.

Plus d’informations sur ce nœud sont trouvées en [http://msdn.microsoft.com/library/bb399546.aspx](http://msdn.microsoft.com/library/bb399546.aspx) (utilisez la liste déroulante **Version autres** pour sélectionner une autre version si nécessaire pour afficher la documentation). *Exemple :*
        `<EntityType Name="MetaDataEntityType" d:Map="/MyXMLPath">
        <Property Name="Name"   Type="String" Nullable="true" d:Map="./Service/Name" d:IsPrimaryKey="true" DefaultValue=”Joe Doh” MaxLength="25" FixedLength="true" />
        ...
        </EntityType>`

| AttributeName | Obligatoire | Valeur |
|----|----|----|
| Nom | Oui | Le nom de la propriété. |
| Type | Oui | Le type de la valeur de propriété. Le type de valeur de propriété doit être un **EDMSimpleType** ou un type complexe (indiqué par un nom complet) qui se trouve dans l’étendue du modèle. Pour plus d’informations, voir Types de modèle conceptuel (CSDL). |
| Nullable | N° | **Vrai** (la valeur par défaut) ou **False** selon que la propriété peut avoir une valeur null. Remarque : Dans la version de CSDL indiqué par l’espace de noms [http://schemas.microsoft.com/ado/2006/04/edm](http://schemas.microsoft.com/ado/2006/04/edm) , une propriété de type complexe doit comporter Nullable = « False ». |
| Valeur par défaut | N° | La valeur par défaut de la propriété. |
|MaxLength | N° | La longueur maximale de la valeur de propriété. |
| FixedLength | N° | **True** ou **False** selon si la valeur de propriété sera stockée comme une chaîne de longueur fiexed. |
| Précision | N° | Fait référence au nombre maximal de chiffres à conserver dans la valeur numérique. |
| Échelle | N° | Nombre maximal de décimales à conserver dans la valeur numérique. |
| Unicode | N° | **True** ou **False** selon si la valeur de propriété être stockées sous forme de chaîne Unicode. |
| Classement | N° | Une chaîne qui spécifie la séquence de classement à utiliser dans la source de données. |
| ConcurrencyMode | N° | **Aucun** (la valeur par défaut) ou **fixe**. Si la valeur est définie à **fixe**, la valeur de propriété servira de contrôles d’accès concurrentiels optimiste. |

Les attributs supplémentaires qui ont été ajoutés à la spécification CSDL sont les suivantes :

**d:Map** - expression XPath exécutée sur le service de sortie et extrait une propriété de sortie. L’expression XPath spécifié est relatif le nœud extensible qui a été sélectionné dans XPath du nœud EntityType. Il est également possible de spécifier un XPath absolu pour autoriser, y compris une ressource statique dans chacun des nœuds de sortie, comme par exemple une instruction de copyright qui se trouve uniquement une fois dans le service d’origine de sortie mais doivent être présents dans chacune des lignes dans la sortie OData. Exemple du service :

        `<foo>
          <bar>
           <baz0>… value …</baz0>
           <baz1>… value …</baz1>
           <baz2>… value …</baz2>
          </bar>
        </foo>`

L’expression XPath ici serait ./bar/baz0 pour obtenir le nœud baz0 de service du fournisseur de contenu.

**d:CharMaxLength** - pour le type de chaîne, vous pouvez spécifier la longueur maximale. Voir exemple DataService CSDL

**d:IsPrimaryKey** - indique si la colonne est la clé primaire dans la table/vue. Voir exemple DataService CSDL.

**d:isExposed** - détermine si le schéma de table est exposé (généralement true). Voir exemple DataService CSDL

**d:IsView** *(Facultatif)* - true si elle est basée sur une vue plutôt qu’une table.  Voir exemple DataService CSDL

**d:Tableschema** - voir l’exemple DataService CSDL

**d:ColumnName** - est le nom de la colonne dans la table/vue.  Voir exemple DataService CSDL

**d:IsReturned** - est la valeur booléenne qui détermine si le Service expose cette valeur pour le client.  Voir exemple DataService CSDL

**d:IsQueryable** - est la valeur booléenne qui détermine si la colonne peut être utilisée dans une requête de base de données.   Voir exemple DataService CSDL

**d:OrdinalPosition** - est la position numérique de la colonne de l’apparence, x, dans la table ou la vue, où x est compris entre 1 au nombre de colonnes de la table.  Voir exemple DataService CSDL

**d:DatabaseDataType** - est le type de données de la colonne dans la base de données, c'est-à-dire les types de données SQL. Voir exemple DataService CSDL

## <a name="supported-parametersproperty-types"></a>Types de propriété/paramètres pris en charge
Voici les types pris en charge pour les paramètres et les propriétés. (Respecter la casse)

| Types primitifs | Description |
|----|----|
| Null | Représente l’absence d’une valeur |
| Valeur booléenne | Représente le concept mathématique de logique à valeurs en nombre binaire.|
| Sur un octet | Valeur d’entier 8 bits non signé|
|Date/heure| Représente la date et l’heure avec des valeurs comprises entre 12:00:00 minuit, le 1er janvier 1753 A.D. par le biais 11:59:59 (du soir), le mois de décembre 9999 et|
|Decimal | Représente des valeurs numériques avec précision et échelle fixes. Ce type peut décrire une valeur numérique comprise entre 10 négatif ^ 255 + 1 et + 10 ^ 255 -1|
| Double | Représente un nombre avec une précision de 15 chiffres qui peut représenter des valeurs dans une plage approximative de ± 2,23E-308 + à 1, 79E à virgule flottante +308. **Utiliser des décimales en raison de problème d’exportation Exel**|
| Unique | Représente un nombre avec une précision de 7 chiffres qui peut représenter des valeurs dans une plage approximative de ± 1,18E-38 + à 3,40E à virgule flottante +38|
|GUID |Représente une valeur de l’identificateur unique de 16 octets (128 bits) |
|Int16|Représente une valeur d’entier 16 bits signé |
|Int32|Représente une valeur d’entier signé 32 bits |
|Int64|Représente une valeur d’entier 64 bits signé |
|Chaîne | Représente les données de caractère-longueur fixe ou variable |


## <a name="see-also"></a>Voir aussi
- Si vous êtes intéressé à comprendre le processus de mappage OData et l’objectif globale, lisez cet article [Mappage du Service de données OData](marketplace-publishing-data-service-creation-odata-mapping.md) passer en revue les définitions, les structures et les instructions.
- Si vous êtes intéressé révision exemples, lisez cet article [Exemples de mappage des données Service OData](marketplace-publishing-data-service-creation-odata-mapping-examples.md) pour voir des exemples de code et comprendre le contexte et la syntaxe de code.
- Pour renvoyer le chemin d’accès prescrit pour la publication d’un Service de données à la place de marché Azure, lisez cet article [Guide de publication de Service de données](marketplace-publishing-data-service-creation.md).

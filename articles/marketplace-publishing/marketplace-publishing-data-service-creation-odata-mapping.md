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

# <a name="mapping-an-existing-web-service-to-odata-through-csdl"></a>Mappage d’un service web existant vers OData via CSDL

>[AZURE.IMPORTANT] **Pour le moment nous ne sont plus arrivant les nouvelles publications de Service de données. Nouvelle dataservices ne seront pas obtenir approuvées pour la liste.** Si vous avez une application métier SaaS que vous voulez publier sur AppSource vous trouverez plus d’informations [ici](https://appsource.microsoft.com/partners). Si vous avez un applications IaaS ou service développeur que vous voulez publier sur Azure Marketplace, vous pouvez en savoir plus [ici](https://azure.microsoft.com/marketplace/programs/certified/).

Cet article offre une vue d’ensemble sur l’utilisation d’un CSDL pour mapper un service existant vers un service compatible OData. Il explique comment créer le document de mappage (CSDL) qui transforme la demande de saisie à partir du client via un appel de service et la sortie (données) au client via un OData compatible flux. Azure Marketplace de Microsoft propose des services pour les utilisateurs finaux en utilisant le protocole OData. Les services exposés par les fournisseurs de contenu (données propriétaires) sont présentés dans une variété de formes, telles que le reste, SOAP, etc..

## <a name="what-is-a-csdl-and-its-structure"></a>Qu’est un CSDL et sa structure ?
Un CSDL (schéma conceptuel Definition Language) est une spécification définissant comment décrire service web ou base de données dans courantes formulation XML vers Azure Marketplace.

Vue d’ensemble simple de la **demander flux :**

  `Client -> Azure Marketplace -> Content Provider’s Web Service (Get, Post, Delete, Put)`

Le **flux de données** se trouve dans le sens inverse :

  `Client <- Azure Marketplace <- Content Provider’s WebService`

**Figure 1** diagrammes comment un client serait obtenir des données à partir d’un fournisseur de contenu (votre service) par le biais de la Azure Marketplace.  Le langage CSDL est utilisé par le composant de mappage/Transformation pour gérer la demande et transférer des données entre ou les services du fournisseur de contenu et le client demandeur.

*Figure 1 : Détaillée du flux de client qui demande au fournisseur de contenu via Azure Marketplace*

  ![dessin](media/marketplace-publishing-data-service-creation-odata-mapping/figure-1.png)

Pour des informations générales sur Atom, Atom publication et le protocole OData sur laquelle les extensions Azure Marketplace générer, veuillez révision : [http://msdn.microsoft.com/library/ff478141.aspx](http://msdn.microsoft.com/library/ff478141.aspx)

Extrait point situé au-dessus du lien :     *« l’objectif du protocole Open Data (désigné comme OData) consiste à fournir un protocole basé sur le reste des opérations de style CRUD (créer, lire, mettre à jour et supprimer) par rapport aux ressources exposés en tant que les services de données. Un « service de données » est un point de terminaison lorsqu’il existe des données exposées à partir d’une ou plusieurs « collections » chaque avec zéro ou plusieurs « entrées », qui se composent de tapé paires de valeurs nommées. OData est publié par Microsoft sous normes OASIS (organisation pour l’avancement of Structured Information Standards) afin que tout le monde qui souhaite pouvez créer des serveurs, clients ou outils sans les droits d’auteur ou restrictions. »*

### <a name="three-critical-pieces-that-have-to-be-defined-by-the-csdl-are"></a>Trois éléments critiques qui doivent être définies par le langage CSDL sont :

- **point de terminaison** de la Service fournisseur Web adresse (URI) du Service
- Les **paramètres de données** passé comme entrée au fournisseur de services les définitions des paramètres envoyés au service du fournisseur de contenu vers le bas jusqu'à le type de données.
- **Schéma** de données renvoyées au Service demandant le schéma de données remis en service du fournisseur de contenu, y compris conteneur, des collections de sites/tableaux, des variables/colonnes et des types de données.

Le diagramme suivant montre une vue d’ensemble du flux à partir de l’endroit où le client entre l’instruction OData (appel de service web du fournisseur de contenu) pour obtenir les données des résultats/précédent.

  ![dessin](media/marketplace-publishing-data-service-creation-odata-mapping/figure-2.png)

### <a name="steps"></a>Étapes suivantes :

1. Le client envoie demande via un appel de Service avec des paramètres d’entrée définis dans XML vers Azure Marketplace
2. CSDL est utilisé pour valider l’appel du Service.
    - L’appel d’assistance mis en forme est ensuite envoyé au service de fournisseurs de contenu par le Azure Marketplace
3. Webservice exécute et valide l’action du verbe Http (c'est-à-dire GET) les données sont renvoyées à Azure Marketplace où les données demandées (le cas échéant) sont expose au Format XML dans le Client à l’aide du mappage défini dans le fichier CSDL.
4. Le Client est envoyé les données (le cas échéant) au format XML ou JSON

## <a name="definitions"></a>Définitions

### <a name="odata-atom-pub"></a>Publication ATOM OData

Une extension de la publication ATOM où chaque entrée représente une seule ligne d’un résultat défini. Le composant de contenu de l’entrée est amélioré pour contiennent les valeurs de la ligne, sous forme de paires de valeur de clé. Plus d’informations est disponible à l’adresse : [https://www.odata.org/documentation/odata-version-3-0/atom-format/](https://www.odata.org/documentation/odata-version-3-0/atom-format/)

### <a name="csdl---conceptual-schema-definition-language"></a>CSDL - langage de définition de schéma conceptuel

Permet de définir des fonctions (procédures stockées) et entités qui sont exposées via une base de données. Plus d’informations icis : [http://msdn.microsoft.com/library/bb399292.aspx](http://msdn.microsoft.com/library/bb399292.aspx)  

> [AZURE.TIP] Cliquez sur la liste déroulante des **autres versions** et sélectionnez une version si vous ne voyez pas l’article.

### <a name="edm---entry-data-model"></a>EDM - modèle de données d’entrée
- Vue d’ensemble : [http://msdn.microsoft.com/library/vstudio/ee382825 (v=vs.100).aspx][OverviewLink] [OverviewLink] : http://msdn.microsoft.com/library/vstudio/ee382825 (v=vs.100).aspx
- Aperçu : [http://msdn.microsoft.com/library/aa697428 (v=vs.80).aspx][PreviewLink] [PreviewLink] : http://msdn.microsoft.com/library/aa697428 (v=vs.80).aspx
- Types de données : [http://msdn.microsoft.com/library/bb399548 (v=VS.100).aspx][DataTypesLink] [DataTypesLink] : http://msdn.microsoft.com/library/bb399548 (v=VS.100).aspx

Le suivant la détaillées de gauche à droite de flux à partir de l’endroit où le client entre l’instruction OData (appel de service web du fournisseur de contenu) pour obtenir les données des résultats/sauvegarde :

  ![dessin](media/marketplace-publishing-data-service-creation-odata-mapping/figure-3.png)


## <a name="csdl-basics"></a>Concepts de base CSDL

Un CSDL (schéma conceptuel Definition Language) est une spécification définissant comment décrire service web ou base de données dans courantes formulation XML vers Azure Marketplace. CSDL décrit le critique pièces qui **permet la transmission des données à partir de la Source de données à l’Azure Marketplace.** Les parties principales sont décrites ici :

- Informations sur l’interface décrire toutes les fonctions accessibles au public (FunctionImport nœud)
- Type de données pour tous les messages requests(input) et message responses(outputs) (EntityContainer/EntitySet/EntityType nœuds)
- Liaison d’informations sur le protocole de transport pour être utilisé (en-tête nœud)
- Informations d’adresse pour localiser le service spécifié (attribut BaseURI)

En bref, le langage CSDL représente un contrat plate-forme et langue indépendante entre le demandeur de service et le fournisseur de services. À l’aide du CSDL, un client de localiser un service/base de données de service web et appeler ses fonctions accessible au public.

### <a name="relating-a-csdl-to-a-database-or-a-collection"></a>Concernant un CSDL une base de données ou une collection de sites
**La spécification CSDL**

CSDL est grammaire XML pour la description d’un service web. La spécification est divisée en 4 principaux éléments : EntitySet, FunctionImport ; Espace de noms et EntityType.

Pour simplifier cette abstraction à comprendre vous permet de mettre en relation une CSDL à une table.

N’oubliez pas ;

  CSDL représente un contrat plate-forme et langue indépendante entre le **demandeur de service** et le **fournisseur de services**. À l’aide de CSDL, un **client** de localiser un **service/base de données de service web** et appeler une de ses accessibles **fonctions.**

Pour un Service de données les quatre parties d’un CSDL peuvent être considérés en termes d’une base de données, une Table, une colonne et une procédure stockée.

Concernant ces comme suit pour un Service de données :

- EntityContainer ~ = base de données
- EntitySet ~ = Table
- EntityType ~ = colonnes
- FunctionImport ~ = procédure stockée

**Verbes HTTP autorisés**
- GET-renvoie les valeurs de la base de données (renvoie une collection de sites)
- BILLET – utilisé pour transmettre des données à et facultatifs renvoie les valeurs de la base de données (créer une nouvelle entrée dans la collection de sites, retour id/URI)
- Supprimer – supprime des données à partir de la base de données (supprime une collection de sites)
- PLACER – mise à jour des données dans une base de données (remplacer une collection de sites ou créez-en un)

## <a name="metadatamapping-document"></a>Document de métadonnées/mappage

Le document de métadonnées/mappage est utilisé pour mapper des services web existants d’un fournisseur de contenu afin qu’il peut être exposé comme un service web OData par le système Azure Marketplace. Il est basé sur CSDL et met en œuvre quelques extensions CSDL selon les besoins de repos en fonction des services web exposés par le biais Azure Marketplace. Les extensions sont trouvées dans l’espace de noms [http://schemas.microsoft.com/dallas/2010/04](http://schemas.microsoft.com/dallas/2010/04) .

Voici un exemple de fichier CSDL : (copier et coller l’exemple CSDL dans un éditeur XML et les modifier pour correspondre à votre Service ci-dessous.  Collez dans CSDL mappage sous l’onglet DataService lorsque vous créez votre service dans le [Portail de publication Azure Marketplace](https://publish.windowsazure.com)).

**Termes :** Concernant les termes CSDL les termes de l’interface utilisateur (PPUI) du [Portail de publication](https://publish.windowsazure.com) .
- Proposer « Titre » dans la PPUI se rapporte à MyWebOffer
- Société dans la PPUI se rapporte au **Nom d’affichage de Publisher** dans le [Centre de développement Microsoft](http://dev.windows.com/registration?accountprogram=azure) de l’interface utilisateur
- Votre API est lié à un site ou d’un Service de données (un Plan dans le PPUI)

**Hiérarchie :** 
 une société (fournisseur de contenu) possède des offres ayant ou les plans, à savoir service(s), la ligne vers le haut avec une API.

### <a name="webservice-csdl-example"></a>Exemple CSDL WebService

Se connecte à un service qui expose un point de terminaison application web (par exemple, une application c#)

        <?xml version="1.0" encoding="utf-8"?>
        <!-- The namespace attribute below is used by our system to generate C#. You can change “MyCompany.MyOffer” to something that makes sense for you, but change “MyOffer” consistently throughout the document. -->
        <Schema Namespace="MyCompany.MyWebOffer" Alias="MyOffer" xmlns="http://schemas.microsoft.com/ado/2009/08/edm" xmlns:d="http://schemas.microsoft.com/dallas/2010/04" >
        <!-- EntityContainer groups all the web service calls together into a single offering. Every web service call has a FunctionImport definition. -->
          <EntityContainer Name="MyOffer">
        <!-- EntitySet is defined for CSDL compatibility reasons, not required for ReturnType=”Raw”
        @Name is used as reference by FunctionImport @EntitySet. And is used in the customer facing UI as name of the Service.
        @EntityType is used to point at the type definition near the bottom of this file. -->
            <EntitySet Name="MyEntities" EntityType="MyOffer.MyEntityType" />
        <!-- Add a FunctionImport for every service method. Multiple FunctionImports can share a single return type (EntityType). -->
        <!-- ReturnType is either Raw() for a stream or Collection() for an Atom feed. Ex. of Raw: ReturnType=”Raw(text/plain)” -->
        <!—EntitySet is the entityset defined above, and is needed if ReturnType is not Raw -->
        <!-- BaseURI attribute defines the service call, replace & with the encode value (&amp;).
        In the input name value pairs {param} represents passed in value.
        Or the value can be hard coded as with AccountKey. -->
        <!-- AllowedHttpMethods optional (default = “GET”), allows the CSDL to specifically specify the verb of the service, “Get”, “Post”, “Put”, or “Delete”. -->
        <!-- EncodeParameterValues, True encodes the parameter values, false does not. -->
        <!-- BaseURI is translated into an URITemplate which defines how the web service call is exposed to marketplace customers.
        Ex. https://api.datamarket.azure.com/mycompany/MyOfferPlan?name={name}
        BaseURI is XML encoded, the {...} point to the parameters defined below.
        Marketplace will read the parameters from this URITemplate and fill the values into the corresponding parameters of the BaseUri or RequestBody (below) during calls to your service.  
        It is okay for @d:BaseUri to include information only for Marketplace consumption, it will not be exposed to end users. i.e. the hardcoded AccountKey in the above BaseURI does not show up in the client facing URITemplate. -->
            <FunctionImport Name="MyWebServiceMethod"
                            EntitySet="MyEntities"
                            ReturnType="Collection(MyOffer.MyEntityType)"
        d:AllowedHttpMethods="GET"
        d:EncodeParameterValues="true"
        d:BaseUri="http://services.organization.net/MyServicePath?name={name}&amp;AccountKey=22AC643">
        <!-- Definition of the RequestBody is only required for HTTP POST requests and is optional for HTTP GET requests. -->
        <d:RequestBody d:httpMethod="POST">
                <!-- Use {} for placeholders to insert parameters. -->
                <!-- This example uses SOAP formatting, but any POST body can be used. -->
            <!-- This example shows how to pass userid and password via the header -->
                <![CDATA[<soapenv:Envelope xmlns:soapenv="http://schemas.xmlsoap.org/soap/envelope/" xmlns:MyOffer="http://services.organization.net/MyServicePath">
                  <soapenv:Header/>
                  <soapenv:Body>
                    <MyOffer:ws_MyWebServiceMethod>
                      <myWebServiceMethodRequest>
                        <!--This information is not exposed to end users. -->
                        <UserId>userid</UserId>
                        <Password>password</Password>
                        <!-- {name} is replaced with the value read from @d:UriTemplate above -->
                        <Name>{name}</Name>
                        <!-- Parameters can be used more than once and are not limited to appearing as the value of an element -->
                        <CustomField Name="{name}" />
                        <MyField>Static content</MyField>
                      </myWebServiceMethodRequest>
                    </MyOffer:ws_MyWebServiceMethod>
                  </soapenv:Body>
                </soapenv:Envelope>      
              ]]>
        </d:RequestBody>
        <!-- Title, Rights and Description are optional and used to specify values to insert into the ATOM feed returned to the end user.  You can specify the element to contain a fixed message by providing a value for the element (this is the default value).  @d:Map is an XPath expression that points into the response returned by your service and is optional.  -->
        <d:Title d:Map="/MyResponse/Title">Default title.</d:Title>
        <d:Rights>© My copyright. This is a fixed response. It is okay to also add a d:Map attribute to override this text.</d:Rights>
        <d:Description d:Map="/MyResponse/Description"></d:Description>
        <d:Namespaces>
        <d:Namespace d:Prefix="p"  d:Uri="http://schemas.organization.net/2010/04/myNamespace" />
        <d:Namespace d:Prefix="p2" d:Uri="http://schemas.organization.net/2010/04/MyNamespace2" />
        </d:Namespaces>
        <!-- Parameters of the web service call:
        @Name should match exactly (case sensitive) the {…} placeholders in the @d:BaseUri, @d:UriTemplate, and d:RequestBody, i.e. “name” parameter in above BaseURI.
        @Mode is always "In", compatibility with CSDL
        @Type is the EDM.SimpleType of the parameter, see http://msdn.microsoft.com/library/bb399548(v=VS.100).aspx
        @d:Nullable indicates whether the parameter is required.
        @d:Regex - optional, attribute to describe the string, limiting unwanted input at the entry of the system
        @d:Description - optional, is used by Service Explorer as help information
        @d:SampleValues - optional, is used by Service Explorer as help information. Multiple Sample values are separated by '|', e.g. "804735132|234534224|23409823234"
        @d:Enum - optional for string type. Contains an enumeration of possible values separated by a '|', e.g. d:enum="english|metric|raw". Will be converted in a dropdown list in the Service Explorer.
        -->
        <Parameter name="name" Mode="In" Type="String" d:Nullable="false" d:Regex="^[a-zA-Z]*$" d:Description="A name that cannot contain any spaces or non-alpha non-English characters"
        d:Enum="George|John|Thomas|James"
        d:SampleValues="George"/>
        <Parameter Name=" AccountKey" Mode="In" Type="String" d:Nullable="false" />

        <!-- d:ErrorHandling is an optional element. Use it define standardized errors by evaluating the service response. -->
        <d:ErrorHandling>
        <!-- Any number of d:Condition elements are allowed, they are evaluated in the order listed.
        @d:Match is an Xpath query on the service response, it should return true or false where true indicates an error.
        @d:httpStatusCode is the error code to return if an response matches the error.
        @d:errorMessage is the user friendly message to return when an error occurs.
        -->
        <d:Condition d:Match="/Result/ErrorMessage[text()='Invalid token']" d:HttpStatusCode="403" d:ErrorMessage="User cannot connect to the service." />
        </d:ErrorHandling>
           </FunctionImport>

            <!-- The EntityContainer defines the output data schema -->
        </EntityContainer>
        <!-- The EntityType @d:Map defines the repeating node (an XPath query) in the response (output data schema). -->
        <!-- If these nodes are outside a namespace, add the prefix in the xpath. -->
        <!--
        @Name - define your user readable name, will become an XML element in the ATOM feed, so comply with the XML element naming restrictions (no spaces or other illegal characters).
        @Type is the EDM.SimpleType of the parameter, see http://msdn.microsoft.com/library/bb399548(v=VS.100).aspx.
        @d:Map uses an Xpath query to point at the location to extract the content from your services response.
        The "." is relative to the repeating node in the EntityType @d:Map Xpath expression.
        -->
            <EntityType Name="MyEntityType" d:Map="/MyResponse/MyEntities">
        <Property Name="ID" d:IsPrimaryKey="True" Type="Int32"  Nullable="false" d:Map="./Remaining[@Amount]"/>
        <Property Name="Amount" Type="Double"   Nullable="false" d:Map="./Remaining[@Amount]"/>
        <Property Name="City"   Type="String"   Nullable="false" d:Map="./City"/>
        <Property Name="State"  Type="String"   Nullable="false" d:Map="./State"/>
        <Property Name="Zip"    Type="Int32"    Nullable="false" d:Map="./Zip"/>
        <Property Name="Updated"    Type="DateTime" Nullable="false" d:Map="./Updated"/>
        <Property Name="AdditionalInfo" Type="String" Nullable="true"
        d:Map="./Info/More[1]"/>
            </EntityType>
        </Schema>

> [AZURE.TIP] Voir d’autres exemples de Service Web CSDL dans l’article [exemples de mappage d’un service web existant à OData via CSDLs](marketplace-publishing-data-service-creation-odata-mapping-examples.md)

###<a name="dataservice-csdl-example"></a>Exemple DataService CSDL

Se connecte à un service qu’exposez une table de base de données ou une vue comme un point de terminaison exemple ci-dessous montre que deux API pour la base de données en fonction de l’API CSDL (peut utiliser affichages plutôt que des tables).

        <?xml version="1.0"?>
        <!-- The namespace attribute below is used by our system to generate C#. You can change “MyCompany.MyOffer” to something that makes sense for you, but change “MyOffer” consistently throughout the document. -->
        <Schema Namespace="MyCompany.MyDataOffer" Alias="MyOffer" xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/ado/2009/08/edm">
        <!-- EntityContainer groups all the data service calls together into a single offering. Every web service call has a FunctionImport definition. -->
        <EntityContainer Name="MyOfferContainer">
        <!-- EntitySet is defined for CSDL compatibility reasons, not required for ReturnType=”Raw”
            Think of the EntitySet as a Service
        @Name is used in the customer facing UI as name of the Service.
        @EntityType is used to point at the type definition (returned set of table columns). -->
        <EntitySet Name="CompanyInfoEntitySet" EntityType="MyOffer.CompanyInfo" />
        <EntitySet Name="ProductInfoEntitySet" EntityType="MyOffer.ProductInfo" />
        </EntityContainer>
        <!-- EntityType defines result (output); the table (or view) and columns to be returned by the data service.)
            Map is the schema.tabel or schema.view
            dals.TableName is the table Name
            Name is the name identifier for the EntityType and the Name of the service exposed to the client via the UI.
            dals:IsExposed determines if the table schema is exposed (generally true).
            dals:IsView (optional) true if this is based on a view rather than a table
            dals:TableSchema is the schema name of the table/view
        -->
        <EntityType
        Map="[dbo].[CompanyInfo]"
        dals:TableName="CompanyInfo"
        Name="CompanyInfo"
        dals:IsExposed="true"
        dals:IsView="false"
        dals:TableSchema="dbo"
        xmlns:dals="http://schemas.microsoft.com/dallas/2010/04">
        <!-- Property defines the column properties and the output of the service.
            dals:ColumnName is the name of the column in the table /view.
            Type is the emd.SimpleType
            Nullable determines if NULL is a valid output value
            dals.CharMaxLenght is the maximum length of the output value
            Name is the name of the Property and is exposed to the client facing UI
            dals:IsReturned is the Boolean that determines if the Service exposes this value to the client.
            IsQueryable is the Boolean that determines if the column can be used in a database query
            (For data Services: To improve Performance make sure that columns marked ISQueryable=”true” are in an index.)
            dals:OrdinalPosition is the numerical position x in the table or the View, where x is from 1 to the number of columns in the table.
            dals:DatabaseDataType is the data type of the column in the database, i.e. SQL data type dals:IsPrimaryKey indicates if the column is the Primary key in the table/view.  (The columns marked ISPrimaryKey are used in the Order by clause when returning data.)
        -->
        <Property dals:ColumnName="data" Type="String" Nullable="true" dals:CharMaxLength="-1" Name="data" dals:IsReturned="true" dals:IsQueryable="false" dals:IsPrimaryKey="false" dals:OrdinalPosition="3" dals:DatabaseDataType="nvarchar" />
        <Property dals:ColumnName="id" Type="Int32" Nullable="false" Name="id" dals:IsReturned="true" dals:IsQueryable="true" dals:IsPrimaryKey="true" dals:OrdinalPosition="1" dals:NumericPrecision="10" dals:DatabaseDataType="int" />
        <Property dals:ColumnName="ticker" Type="String" Nullable="true" dals:CharMaxLength="10" Name="ticker" dals:IsReturned="true" dals:IsQueryable="true" dals:IsPrimaryKey="false" dals:OrdinalPosition="2" dals:DatabaseDataType="nvarchar" />
        </EntityType>
        <EntityType Map="[dbo].[ProductInfo]" dals:TableName="ProductInfo" Name="ProductInfo" dals:IsExposed="true" dals:IsView="false" dals:TableSchema="dbo" xmlns:dals="http://schemas.microsoft.com/dallas/2010/04">
        <Property dals:ColumnName="companyid" Type="Int32" Nullable="true" Name="companyid" dals:IsReturned="true" dals:IsQueryable="true" dals:IsPrimaryKey="false" dals:OrdinalPosition="2" dals:NumericPrecision="10" dals:DatabaseDataType="int" />
        <Property dals:ColumnName="id" Type="Int32" Nullable="false" Name="id" dals:IsReturned="true" dals:IsQueryable="true" dals:IsPrimaryKey="true" dals:OrdinalPosition="1" dals:NumericPrecision="10" dals:DatabaseDataType="int" />
        <Property dals:ColumnName="product" Type="String" Nullable="true" dals:CharMaxLength="50" Name="product" dals:IsReturned="true" dals:IsQueryable="true" dals:IsPrimaryKey="false" dals:OrdinalPosition="3" dals:DatabaseDataType="nvarchar" />
        </EntityType>
        </Schema>

## <a name="see-also"></a>Voir aussi
- Si vous êtes intéressé par apprentissage et la compréhension des nœuds spécifiques et leurs paramètres, lisez cet article [Données Service OData mappage des nœuds](marketplace-publishing-data-service-creation-odata-mapping-nodes.md) de définitions et explications, des exemples et utiliser casse contexte.
- Si vous êtes intéressé révision exemples, lisez cet article [Exemples de mappage des données Service OData](marketplace-publishing-data-service-creation-odata-mapping-examples.md) pour voir des exemples de code et comprendre le contexte et la syntaxe de code.
- Pour renvoyer le chemin d’accès prescrit pour la publication d’un Service de données à la place de marché Azure, lisez cet article [Guide de publication de Service de données](marketplace-publishing-data-service-creation.md).

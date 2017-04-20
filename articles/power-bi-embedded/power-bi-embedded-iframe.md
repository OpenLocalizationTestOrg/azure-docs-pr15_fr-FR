<properties
   pageTitle="Comment utiliser Power BI incorporé avec reste | Microsoft Azure"
   description="Découvrez comment utiliser Power BI incorporé avec reste "
   services="power-bi-embedded"
   documentationCenter=""
   authors="guyinacube"
   manager="erikre"
   editor=""
   tags=""/>
<tags
   ms.service="power-bi-embedded"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="powerbi"
   ms.date="10/04/2016"
   ms.author="asaxton"/>

# <a name="how-to-use-power-bi-embedded-with-rest"></a>Comment utiliser Power BI incorporé avec reste


## <a name="power-bi-embedded-what-it-is-and-what-its-for"></a>Power BI incorporés : Signification et qu’il est pour
Une vue d’ensemble de Power BI incorporé est décrite dans le [site Power BI incorporé](https://azure.microsoft.com/services/power-bi-embedded/)officiel, mais jetons un coup rapide avant d’entrer dans les détails sur l’utilisation avec reste.

Il est très simple, vraiment. Un logiciel indépendant souvent à utiliser les visualisations échange dynamique de données de [Power BI](https://powerbi.microsoft.com) dans leur propre application comme blocs de construction de l’interface utilisateur.

Mais, vous savez, l’incorporation de rapports Power BI ou vignettes dans votre page web est déjà possible sans que le service Power BI incorporé Azure, à l’aide de l' **API de BI Power**. Lorsque vous souhaitez partager vos rapports de votre organisation même, vous pouvez incorporer les rapports avec l’authentification Azure Active Directory. L’utilisateur qui affiche les rapports doit se connecter à l’aide de leur propre compte Azure AD. Lorsque vous souhaitez partager vos rapports pour tous les utilisateurs (y compris les utilisateurs externes), vous pouvez simplement incorporer avec l’accès anonyme.

Mais que vous voyez, cette simple d’incorporer solution ne répond pas aux besoins d’une application fil.
La plupart des applications fil besoin pour fournir les données à leurs clients, pas nécessairement les utilisateurs de leur organisation. Par exemple, si vous donnez un service pour les sociétés A et B, les utilisateurs dans la société A doivent uniquement voir données pour leur propre entreprise A. En d’autres termes, la location multiples est nécessaire pour la remise.

L’application fil peut-être également être offrant ses propres méthodes d’authentification tel que formulaires auth, authentification de base, etc.... Puis, la solution incorporation doit collaborer avec ces méthodes d’authentification existant en toute sécurité. Il est également nécessaire pour les utilisateurs à être en mesure d’utiliser ces applications fil sans l’achat supplémentaire ou la licence d’un abonnement Power BI.

 **Power BI incorporé** est conçu pour précisément ces types de scénarios fil. Donc maintenant que nous avons cette brève introduction gêne, nous allons apprendre à certains détails

Vous pouvez utiliser .NET \(c#) ou SDK Node.js, permettent de créer facilement votre application avec Power BI incorporé. Mais, dans cet article, nous allons expliquer à propos des flux HTTP \(incluse seul) de Power BI sans SDK. Présentation de ce flux, vous pouvez créer votre application **avec un langage de programmation**et vous aider à comprendre profondeur l’essence même de Power BI incorporé.

## <a name="create-power-bi-workspace-collection-and-get-access-key-provisioning"></a>Collection de sites espace de travail créer Power BI et touche d’accès rapide get \(Provisioning)
Power BI incorporé est un des services Azure. Uniquement l’éditeur de logiciels qui utilise portail Azure est facturée pour les frais de l’utilisation de \(par toutes les heures des session utilisateur), et l’utilisateur qui affiche le rapport n’est pas facturée ou même nécessite un abonnement Azure.
Avant de commencer notre développement d’applications, nous devons créer la **collection de sites Power BI espace de travail** à l’aide du portail Azure.

Chaque espace de travail de Power BI incorporé est l’espace de travail pour chaque client (client) et nous pouvons ajouter plusieurs espaces de travail dans chaque collection de sites espace de travail. La même touche d’accès est utilisée dans chaque collection de sites espace de travail. Effet, la collection de sites espace de travail est la limite de sécurité pour Power BI incorporé.

![](media\power-bi-embedded-iframe\create-workspace.png)

Quand nous terminer la création de la collection de sites espace de travail, copiez la touche d’accès rapide à partir d’Azure Portal.

![](media\power-bi-embedded-iframe\copy-access-key.png)

> [AZURE.NOTE] Nous pouvons également mise en service de la collection de sites espace de travail et obtenez touche d’accès rapide via l’API REST. Pour plus d’informations, voir [Power BI ressource fournisseur API](https://msdn.microsoft.com/library/azure/mt712306.aspx).

## <a name="create-pbix-file-with-power-bi-desktop"></a>Créer un fichier .pbix avec Power BI Desktop
Ensuite, nous devons créer la connexion de données et les rapports d’être incorporé.
Pour cette tâche, il n’existe pas de programmation ou code. Nous suffit d’utiliser Power BI Desktop.
Dans cet article, nous ne passent par les détails sur l’utilisation de Power BI Desktop. Si vous avez besoin d’aide ici, voir [prise en main de Power BI Desktop](https://powerbi.microsoft.com/documentation/powerbi-desktop-getting-started/). Dans notre exemple, nous utiliserons simplement l' [Échantillon d’analyse de vente au détail](https://powerbi.microsoft.com/documentation/powerbi-sample-datasets/).

![](media\power-bi-embedded-iframe\power-bi-desktop-1.png)

## <a name="create-a-power-bi-workspace"></a>Créer un espace de travail Power BI

À présent que la mise en service se produit, prise en main création d’espace de travail d’un client dans la collection de sites espace de travail via des API REST. Suivant HTTP POST demander (reste) consiste à créer le nouvel espace de travail dans notre collection de sites espace de travail existant. Dans notre exemple, le nom de la collection de sites espace de travail est **mypbiapp**.
Nous simplement définir la touche d’accès rapide, qui nous copiées précédemment, comme **AppKey**. Il est très simple authentification !

**Demande HTTP**

```
POST https://api.powerbi.com/v1.0/collections/mypbiapp/workspaces
Authorization: AppKey MpaUgrTv5e...
```

**Réponse HTTP**

```
HTTP/1.1 201 Created
Content-Type: application/json; odata.metadata=minimal; odata.streaming=true
Location: https://wabi-us-east2-redirect.analysis.windows.net/v1.0/collections/mypbiapp/workspaces
RequestId: 4220d385-2fb3-406b-8901-4ebe11a5f6da

{
  "@odata.context": "http://wabi-us-east2-redirect.analysis.windows.net/v1.0/collections/mypbiapp/$metadata#workspaces/$entity",
  "workspaceId": "32960a09-6366-4208-a8bb-9e0678cdbb9d",
  "workspaceCollectionName": "mypbiapp"
}
```

Le **workspaceId** retourné est utilisé pour les appels API ultérieures suivants. Notre application doit conserver cette valeur.

## <a name="import-pbix-file-into-the-workspace"></a>Importer le fichier .pbix dans l’espace de travail
Chaque espace de travail peut héberger un seul fichier Power BI Desktop avec un jeu de données \(y compris les paramètres de la source de données) et les rapports. Nous pouvons importer notre fichier .pbix à l’espace de travail comme indiqué dans le code ci-dessous. Comme vous pouvez le voir, nous pouvons télécharger le fichier binaire du fichier .pbix à l’aide de MIME multipart dans http.

Le fragment d’uri **32960a09-6366-4208-a8bb-9e0678cdbb9d** est le workspaceId et requête paramètre **datasetDisplayName** est le nom de jeu de données à créer. Le jeu de données créé conserve toutes les données liées objets .pbix fichier tels que les données importées, le pointeur à la source de données, etc....

```
POST https://api.powerbi.com/v1.0/collections/mypbiapp/workspaces/32960a09-6366-4208-a8bb-9e0678cdbb9d/imports?datasetDisplayName=mydataset01
Authorization: AppKey MpaUgrTv5e...
Content-Type: multipart/form-data; boundary="A300testx"

--A300testx
Content-Disposition: form-data

{the content (binary) of .pbix file}
--A300testx--
```

Cette tâche d’importation peut s’exécuter pendant un certain temps. Lorsque vous avez terminé, notre application peut demander à l’état des tâches à l’aide des id de l’importation. Dans notre exemple, l’id de l’importation est **4eec64dd-533b-47c3-a72c-6508ad854659**.

```
HTTP/1.1 202 Accepted
Content-Type: application/json; charset=utf-8
Location: https://wabi-us-east2-redirect.analysis.windows.net/v1.0/collections/mypbiapp/workspaces/32960a09-6366-4208-a8bb-9e0678cdbb9d/imports/4eec64dd-533b-47c3-a72c-6508ad854659?tenantId=myorg
RequestId: 658bd6b4-b68d-4ec3-8818-2a94266dc220

{"id":"4eec64dd-533b-47c3-a72c-6508ad854659"}
```

Demandant état à l’aide de ce code importer les éléments suivants :

```
GET https://api.powerbi.com/v1.0/collections/mypbiapp/workspaces/32960a09-6366-4208-a8bb-9e0678cdbb9d/imports/4eec64dd-533b-47c3-a72c-6508ad854659
Authorization: AppKey MpaUgrTv5e...
```

Si la tâche n’est pas terminée, la réponse HTTP peut être comme suit :

```
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8
RequestId: 614a13a5-4de7-43e8-83c9-9cd225535136

{
  "id": "4eec64dd-533b-47c3-a72c-6508ad854659",
  "importState": "Publishing",
  "createdDateTime": "2016-07-19T07:36:06.227",
  "updatedDateTime": "2016-07-19T07:36:06.227",
  "name": "mydataset01"
}
```

Si la tâche est terminée, la réponse HTTP peut être plus comme suit :

```
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8
RequestId: eb2c5a85-4d7d-4cc2-b0aa-0bafee4b1606

{
  "id": "4eec64dd-533b-47c3-a72c-6508ad854659",
  "importState": "Succeeded",
  "createdDateTime": "2016-07-19T07:36:06.227",
  "updatedDateTime": "2016-07-19T07:36:06.227",
  "reports": [
    {
      "id": "2027efc6-a308-4632-a775-b9a9186f087c",
      "name": "mydataset01",
      "webUrl": "https://app.powerbi.com/reports/2027efc6-a308-4632-a775-b9a9186f087c",
      "embedUrl": "https://app.powerbi.com/appTokenReportEmbed?reportId=2027efc6-a308-4632-a775-b9a9186f087c"
    }
  ],
  "datasets": [
    {
      "id": "458e0451-7215-4029-80b3-9627bf3417b0",
      "name": "mydataset01",
      "tables": [
      ],
      "webUrl": "https://app.powerbi.com/datasets/458e0451-7215-4029-80b3-9627bf3417b0"
    }
  ],
  "name": "mydataset01"
}
```

## <a name="data-source-connectivity-and-multi-tenancy-of-data"></a>Connexion de source de données \(et location multiples de données)
Tandis que quasiment tous les objets dans le fichier .pbix importés dans notre espace de travail, les informations d’identification pour les sources de données ne sont pas. Par conséquent, lorsque vous utilisez le **mode DirectQuery**, le rapport incorporé ne peut pas s’afficher correctement. Toutefois, lorsque vous utilisez le **mode d’importation**, nous pouvons afficher le rapport en utilisant les données importées existantes. Dans ce cas, nous devons définir les informations d’identification en suivant ces étapes via des appels reste.

Tout d’abord, nous devons obtenir la source de données passerelle. Nous connaître que le dataset **id** est l’id renvoyée précédemment.

**Demande HTTP**

```
GET https://api.powerbi.com/v1.0/collections/mypbiapp/workspaces/32960a09-6366-4208-a8bb-9e0678cdbb9d/datasets/458e0451-7215-4029-80b3-9627bf3417b0/Default.GetBoundGatewayDatasources
Authorization: AppKey MpaUgrTv5e...
```

**Réponse HTTP**

```
GET HTTP/1.1 200 OK
Content-Type: application/json; odata.metadata=minimal; odata.streaming=true
RequestId: 574b0b18-a6fa-46a6-826c-e65840cf6e15

{
  "@odata.context": "http://wabi-us-east2-redirect.analysis.windows.net/v1.0/collections/mypbiapp/workspaces/32960a09-6366-4208-a8bb-9e0678cdbb9d/$metadata#gatewayDatasources",
  "value": [
    {
      "id": "5f7ee2e7-4851-44a1-8b75-3eb01309d0ea",
      "gatewayId": "ca17e77f-1b51-429b-b059-6b3e3e9685d1",
      "datasourceType": "Sql",
      "connectionDetails": "{\"server\":\"testserver.database.windows.net\",\"database\":\"testdb01\"}"
    }
  ]
}
```

À l’aide de l’id de passerelle renvoyée et l’id de source de données \(voir le précédent **gatewayId** et l' **id** dans le résultat retourné), nous pouvons modifier les informations d’identification de cette source de données comme suit :

**Demande HTTP**

```
PATCH https://api.powerbi.com/v1.0/collections/mypbiapp/workspaces/32960a09-6366-4208-a8bb-9e0678cdbb9d/gateways/ca17e77f-1b51-429b-b059-6b3e3e9685d1/datasources/5f7ee2e7-4851-44a1-8b75-3eb01309d0ea
Authorization: AppKey MpaUgrTv5e...
Content-Type: application/json; charset=utf-8

{
  "credentialType": "Basic",
  "basicCredentials": {
    "username": "demouser",
    "password": "P@ssw0rd"
  }
}
```

**Réponse HTTP**

```
HTTP/1.1 200 OK
Content-Type: application/octet-stream
RequestId: 0e533c13-266a-4a9d-8718-fdad90391099
```

En production, nous pouvons également définir la chaîne de connexion différente pour chaque espace de travail à l’aide de l’API REST. \(Autrement dit, nous pouvons séparez la base de données pour chaque client.)

Les éléments suivants change la chaîne de connexion de source de données via le reste.

```
POST https://api.powerbi.com/v1.0/collections/mypbiapp/workspaces/32960a09-6366-4208-a8bb-9e0678cdbb9d/datasets/458e0451-7215-4029-80b3-9627bf3417b0/Default.SetAllConnections
Authorization: AppKey MpaUgrTv5e...
Content-Type: application/json; charset=utf-8

{
  "connectionString": "data source=testserver02.database.windows.net;initial catalog=testdb02;persist security info=True;encrypt=True;trustservercertificate=False"
}
```

Ou, nous pouvons utiliser la sécurité au niveau de ligne dans Power BI incorporé et nous pouvons séparer les données tous les utilisateurs dans un rapport. As a result, nous pouvons mise en service de chaque état client avec la même .pbix \(l’interface utilisateur, etc..) et de différentes sources de données.

> [AZURE.NOTE] Si vous utilisez le **mode d’importation** au lieu de **mode DirectQuery**, il n’existe aucun moyen d’actualiser les modèles via l’API. Et, en local de sources de données via la passerelle Power BI n’est pas encore pris en charge dans Power BI incorporé. Toutefois, vous souhaiterez vraiment garder un œil sur le [blog Power BI](https://powerbi.microsoft.com/blog/) pour quelles sont les nouveautés et mises à jour de ce qui se passe à l’avenir.

## <a name="authentication-and-hosting-embedding-reports-in-our-web-page"></a>L’authentification et qui héberge les rapports (incorporation) dans notre page web

Dans l’API REST précédente, nous pouvons utiliser la touche d’accès rapide **AppKey** lui-même sous forme de l’en-tête d’autorisation. Étant donné que ces appels peuvent être gérés sur le côté serveur principal, il est recommandé.

Mais lorsque nous incorporez le rapport dans notre page web, ce type d’informations sur la sécurité serait géré à l’aide d’un code JavaScript \(frontend). Puis la valeur de l’en-tête d’autorisation doit être sécurisée. Si notre touche d’accès rapide est découvert par un utilisateur malveillant ou du code malveillant, elles peuvent appeler n’importe quel opérations à l’aide de cette touche.

Quand nous incorporer le rapport dans notre page web, nous devons utiliser le jeton calculé au lieu de la touche d’accès rapide **AppKey**. Notre application doit créer le jeton OAuth Json Web \(JWT) qui se compose de créances et la signature numérique calculée. Comme illustré ci-dessous, cette OAuth JWT est jetons de chaîne encodée point par des virgules.

![](media\power-bi-embedded-iframe\oauth-jwt.png)

Tout d’abord, nous devons préparer la valeur d’entrée, qui est signée ultérieurement. Cette valeur est la chaîne d’url codée (rfc4648) en base 64 de la json suivant, et ces délimités par le point \(.) caractère. Plus tard, nous vous expliquerons comment obtenir l’id du rapport.

> [AZURE.NOTE] Si nous voulons utiliser la sécurité de niveau ligne (RLS) avec Power BI incorporé, nous devons également indiquer **nom d’utilisateur** et les **rôles** dans les revendications.

```
{
  "typ":"JWT",
  "alg":"HS256"
}
```

```
{
  "wid":"{workspace id}",
  "rid":"{report id}",
  "wcn":"{workspace collection name}",
  "iss":"PowerBISDK",
  "ver":"0.2.0",
  "aud":"https://analysis.windows.net/powerbi/api",
  "nbf":{start time of token expiration},
  "exp":{end time of token expiration}
}
```

Ensuite, nous devons créer la chaîne en base 64 codé de HMAC \(la signature) avec algorithme SHA256. Cette valeur d’entrée signée est la chaîne précédente.

Pour finir, nous devons combiner la chaîne de valeur et une signature d’entrée à l’aide de la période \(.) caractère. La chaîne finale est le jeton d’application pour l’incorporation de rapport. Même si le jeton de l’application est détecté par un utilisateur malveillant, ils ne peuvent pas accéder la touche d’accès d’origine. Ce jeton application va expirer rapidement.

Voici un exemple PHP pour comme suit :

```
<?php
// 1. power bi access key
$accesskey = "MpaUgrTv5e...";

// 2. construct input value
$token1 = "{" .
  "\"typ\":\"JWT\"," .
  "\"alg\":\"HS256\"" .
  "}";
$token2 = "{" .
  "\"wid\":\"32960a09-6366-4208-a8bb-9e0678cdbb9d\"," . // workspace id
  "\"rid\":\"2027efc6-a308-4632-a775-b9a9186f087c\"," . // report id
  "\"wcn\":\"mypbiapp\"," . // workspace collection name
  "\"iss\":\"PowerBISDK\"," .
  "\"ver\":\"0.2.0\"," .
  "\"aud\":\"https://analysis.windows.net/powerbi/api\"," .
  "\"nbf\":" . date("U") . "," .
  "\"exp\":" . date("U" , strtotime("+1 hour")) .
  "}";
$inputval = rfc4648_base64_encode($token1) .
  "." .
  rfc4648_base64_encode($token2);

// 3. get encoded signature
$hash = hash_hmac("sha256",
    $inputval,
    $accesskey,
    true);
$sig = rfc4648_base64_encode($hash);

// 4. show result (which is the apptoken)
$apptoken = $inputval . "." . $sig;
echo($apptoken);

// helper functions
function rfc4648_base64_encode($arg) {
  $res = $arg;
  $res = base64_encode($res);
  $res = str_replace("/", "_", $res);
  $res = str_replace("+", "-", $res);
  $res = rtrim($res, "=");
  return $res;
}
?>
```

## <a name="finally-embed-the-report-into-the-web-page"></a>Enfin, intégrez le rapport dans la page web

Pour l’incorporation de notre rapport, nous devons obtenir l’url incorporer et **id** à l’aide de l’API REST suivant de l’état.

**Demande HTTP**

```
GET https://api.powerbi.com/v1.0/collections/mypbiapp/workspaces/32960a09-6366-4208-a8bb-9e0678cdbb9d/reports
Authorization: AppKey MpaUgrTv5e...
```

**Réponse HTTP**

```
HTTP/1.1 200 OK
Content-Type: application/json; odata.metadata=minimal; odata.streaming=true
RequestId: d4099022-405b-49d3-b3b7-3c60cf675958

{
  "@odata.context": "http://wabi-us-east2-redirect.analysis.windows.net/v1.0/collections/mypbiapp/workspaces/32960a09-6366-4208-a8bb-9e0678cdbb9d/$metadata#reports",
  "value": [
    {
      "id": "2027efc6-a308-4632-a775-b9a9186f087c",
      "name": "mydataset01",
      "webUrl": "https://app.powerbi.com/reports/2027efc6-a308-4632-a775-b9a9186f087c",
      "embedUrl": "https://embedded.powerbi.com/appTokenReportEmbed?reportId=2027efc6-a308-4632-a775-b9a9186f087c",
      "isFromPbix": false
    }
  ]
}
```

Nous pouvons incorporer le rapport dans notre application web en utilisant le jeton application précédente.
Si nous examinons l’exemple de code suivant, la partie ancienne est identique à l’exemple précédent. Dans la dernière partie, cet exemple montre **embedUrl** \(voir le résultat précédent) dans l’iframe et comptabilise le jeton d’application dans l’iframe.

> [AZURE.NOTE] Vous devez modifier la valeur d’id de rapport à celle de votre choix. En outre, en raison d’un bogue dans notre système de gestion de contenu, la balise iframe dans l’exemple de code est lu littéralement. Supprimer le texte limité à partir de la balise si vous copiez et collez le code suivant.

```
    <?php
    // 1. power bi access key
    $accesskey = "MpaUgrTv5e...";

    // 2. construct input value
    $token1 = "{" .
      "\"typ\":\"JWT\"," .
      "\"alg\":\"HS256\"" .
      "}";
    $token2 = "{" .
      "\"wid\":\"32960a09-6366-4208-a8bb-9e0678cdbb9d\"," . // workspace id
      "\"rid\":\"2027efc6-a308-4632-a775-b9a9186f087c\"," . // report id
      "\"wcn\":\"mypbiapp\"," . // workspace collection name
      "\"iss\":\"PowerBISDK\"," .
      "\"ver\":\"0.2.0\"," .
      "\"aud\":\"https://analysis.windows.net/powerbi/api\"," .
      "\"nbf\":" . date("U") . "," .
      "\"exp\":" . date("U" , strtotime("+1 hour")) .
      "}";
    $inputval = rfc4648_base64_encode($token1) .
      "." .
      rfc4648_base64_encode($token2);

    // 3. get encoded signature value
    $hash = hash_hmac("sha256",
        $inputval,
        $accesskey,
        true);
    $sig = rfc4648_base64_encode($hash);

    // 4. get apptoken
    $apptoken = $inputval . "." . $sig;

    // helper functions
    function rfc4648_base64_encode($arg) {
      $res = $arg;
      $res = base64_encode($res);
      $res = str_replace("/", "_", $res);
      $res = str_replace("+", "-", $res);
      $res = rtrim($res, "=");
      return $res;
    }
    ?>
    <!DOCTYPE html>
    <html>
    <head>
      <meta charset="utf-8" />
      <meta http-equiv="X-UA-Compatible" content="IE=edge">
      <title>Test page</title>
      <meta name="viewport" content="width=device-width, initial-scale=1">
    </head>
    <body>
      <button id="btnView">View Report !</button>
      <div id="divView">
        <**REMOVE THIS CAPPED TEXT IF COPIED** iframe id="ifrTile" width="100%" height="400"></iframe>
      </div>
      <script>
        (function () {
          document.getElementById('btnView').onclick = function() {
            var iframe = document.getElementById('ifrTile');
            iframe.src = 'https://embedded.powerbi.com/appTokenReportEmbed?reportId=2027efc6-a308-4632-a775-b9a9186f087c';
            iframe.onload = function() {
              var msgJson = {
                action: "loadReport",
                accessToken: "<?=$apptoken?>",
                height: 500,
                width: 722
              };
              var msgTxt = JSON.stringify(msgJson);
              iframe.contentWindow.postMessage(msgTxt, "*");
            };
          };
        }());
      </script>
    </body>
```

Et Voici notre résultat :

![](media\power-bi-embedded-iframe\view-report.png)

Pour l’instant, Power BI incorporé affiche uniquement le rapport dans l’iframe. Mais, gardez un œil sur le [Blog Power BI](). Les améliorations peuvent utiliser côté client nouvelle API qui sera nous envoyer des informations dans l’iframe, ainsi que fournir des informations. Contenus original !


## <a name="see-also"></a>Voir aussi
- [Authentification et autorisation dans Power BI incorporé](power-bi-embedded-app-token-flow.md)

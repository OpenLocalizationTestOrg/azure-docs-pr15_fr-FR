<properties
    pageTitle="La mise en cache personnalisés dans la gestion des API Azure"
    description="Découvrez comment mettre en cache les éléments par clé de gestion des API Azure"
    services="api-management"
    documentationCenter=""
    authors="darrelmiller"
    manager="erikre"
    editor=""/>

<tags
    ms.service="api-management"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="na"
    ms.date="10/25/2016"
    ms.author="darrmi"/>

# <a name="custom-caching-in-azure-api-management"></a>La mise en cache personnalisés dans la gestion des API Azure
Service de gestion de l’API d’Azure prend en charge pour [la mise en cache de réponse HTTP](api-management-howto-cache.md) à l’aide de l’URL de la ressource comme clé. La clé peut être modifiée par les en-têtes de demande à l’aide de la `vary-by` propriétés. Ceci est utile pour la mise en cache des réponses HTTP entières (aka représentations), mais il est parfois utile pour mettre en cache uniquement une partie d’une représentation. Les nouvelles stratégies de [valeur de recherche de cache](https://msdn.microsoft.com/library/azure/dn894086.aspx#GetFromCacheByKey) et la [valeur de base de cache](https://msdn.microsoft.com/library/azure/dn894086.aspx#StoreToCacheByKey) permettent de stocker et récupérer les éléments arbitraires de données à partir des définitions de stratégie. Cette fonctionnalité ajoute également valeur à la stratégie précédemment introduits [demande d’envoi](https://msdn.microsoft.com/library/azure/dn894085.aspx#SendRequest) , car vous pouvez désormais mettre en cache les réponses à partir des services externes.

## <a name="architecture"></a>Architecture  
Service de gestion des API utilise un cache de données client partagé afin que, étant donné que vous évoluent jusqu'à divisions vous obtenez toujours accès à la même des données mises en cache. Toutefois, lorsque vous travaillez avec un déploiement de plusieurs région, il existe cache indépendante dans chacune des régions. Par conséquent, il est important de ne pas considérer le cache comme un magasin de données, où il est la seule source de toute information. Si vous avez et ultérieurement décidez de tirer parti du déploiement plusieurs région, puis clients avec les utilisateurs de voyage risquent de perdre l’accès à ces données mises en cache.

## <a name="fragment-caching"></a>Fragment de mise en cache
Il existe certains cas où les réponses renvoyées contiennent une partie des données sont coûteux déterminer et encore restent à jour pour un laps de temps acceptable. Par exemple, envisagez d’un service créé par une compagnie aérienne qui fournit des informations concernant les réservations de vol, état aérienne, etc.. Si l’utilisateur est membre du programme points compagnies aériennes, ils aurait également les informations relatives à leur état actuel et le kilométrage cumulé. Ces informations relatives à l’utilisateur pouvant être stockées dans un autre système, mais il est préférable de les inclure dans les réponses renvoyées concernant les réservations et l’état de vol. Cela peut être effectuée à l’aide d’un processus appelé mise en cache de fragment. Représentation primaire peut être renvoyée à partir du serveur d’origine à l’aide de quelconque du jeton pour indiquer l’endroit où les informations relatives à l’utilisateur désignant être inséré. 

Vous pouvez la réponse JSON suivante à partir d’un serveur principal API.


    {
      "airline" : "Air Canada",
      "flightno" : "871",
      "status" : "ontime",
      "gate" : "B40",
      "terminal" : "2A",
      "userprofile" : "$userprofile$"
    }  

Et des ressources secondaire en `/userprofile/{userid}` qui ressemble à,

     { "username" : "Bob Smith", "Status" : "Gold" }

Afin de déterminer les informations utilisateur appropriés à inclure, nous devons identifier qui est l’utilisateur final. Ce mécanisme est dépend de l’implémentation. Par exemple, j’utilise le `Subject` réclamer d’un `JWT` jeton. 

    <set-variable
      name="enduserid"
      value="@(context.Request.Headers.GetValueOrDefault("Authorization","").Split(' ')[1].AsJwt()?.Subject)" />

Nous stocker ce `enduserid` valeur dans une variable de contexte pour une utilisation ultérieure. L’étape suivante consiste à déterminer si une demande précédente est déjà extrait les informations utilisateur et stockées dans le cache. Pour cela, nous utilisons la `cache-lookup-value` stratégie.

      <cache-lookup-value
      key="@("userprofile-" + context.Variables["enduserid"])"
      variable-name="userprofile" />

S’il n’existe aucune entrée dans le cache qui correspond à la valeur de clé, puis aucune `userprofile` variable de contexte est créée. Nous vérifier la réussite de la recherche à l’aide du `choose` contrôler la stratégie de flux.

    <choose>
        <when condition="@(!context.Variables.ContainsKey("userprofile"))">
            <!— If the userprofile context variable doesn’t exist, make an HTTP request to retrieve it.  -->
        </when>
    </choose>


Si la `userprofile` variable de contexte n’existe pas, puis nous allons devoir effectuer une demande HTTP pour la récupérer.

    <send-request
      mode="new"
      response-variable-name="userprofileresponse"
      timeout="10"
      ignore-error="true">

      <!-- Build a URL that points to the profile for the current end-user -->
      <set-url>@(new Uri(new Uri("https://apimairlineapi.azurewebsites.net/UserProfile/"),
          (string)context.Variables["enduserid"]).AbsoluteUri)
      </set-url>
      <set-method>GET</set-method>
    </send-request>

Nous utilisons la `enduserid` pour créer l’URL de la ressource de profil utilisateur. Une fois que nous avons la réponse, nous pouvons extraire le corps du texte en dehors de la réponse et stockez-le dans une variable de contexte.

    <set-variable
        name="userprofile"
        value="@(((IResponse)context.Variables["userprofileresponse"]).Body.As<string>())" />

Pour éviter de nous avoir à rendre à nouveau, cette requête HTTP lorsque l’utilisateur même effectue une autre requête, nous pouvons stocker le profil utilisateur dans le cache.

    <cache-store-value
        key="@("userprofile-" + context.Variables["enduserid"])"
        value="@((string)context.Variables["userprofile"])" duration="100000" />

Nous stocker la valeur dans le cache à l’aide de la même clé que nous a tenté de récupérer avec. La durée que nous avons choisi pour stocker la valeur doit être selon la façon dont les modifications des informations et les utilisateurs comment tolérance sont souvent des informations à jour. 

Il est important de savoir que récupération à partir du cache est toujours une hors du processus, une demande de réseau et potentiellement toujours ajoutent dizaines de millisecondes à la demande. Les avantages fournis pour déterminer que les informations de profil utilisateur dure plus longtemps que celle en raison de devoir de la base de données de requêtes ou les informations agrégées provenant de plusieurs principaux.

La dernière étape du processus consiste à mettre à jour la réponse renvoyée avec ses informations de profil utilisateur.

    <!—Update response body with user profile-->
    <find-and-replace
        from='"$userprofile$"'
        to="@((string)context.Variables["userprofile"])" />

J’ai choisi d’inclure les guillemets dans le cadre du jeton de sorte que même lorsque la remplacer n’a pas lieu, la réponse était JSON toujours valide. Il s’agissait principalement pour faciliter le débogage.

Une fois que vous combinez toutes ces étapes, le résultat final est une stratégie qui ressemble à celle qui suit.

     <policies>
        <inbound>
            <!-- How you determine user identity is application dependent -->
            <set-variable
              name="enduserid"
              value="@(context.Request.Headers.GetValueOrDefault("Authorization","").Split(' ')[1].AsJwt()?.Subject)" />

            <!--Look for userprofile for this user in the cache -->
            <cache-lookup-value
              key="@("userprofile-" + context.Variables["enduserid"])"
              variable-name="userprofile" />

            <!-- If we don’t find it in the cache, make a request for it and store it -->
            <choose>
                <when condition="@(!context.Variables.ContainsKey("userprofile"))">
                    <!—Make HTTP request to get user profile -->
                    <send-request
                      mode="new"
                      response-variable-name="userprofileresponse"
                      timeout="10"
                      ignore-error="true">

                       <!-- Build a URL that points to the profile for the current end-user -->
                        <set-url>@(new Uri(new Uri("https://apimairlineapi.azurewebsites.net/UserProfile/"),(string)context.Variables["enduserid"]).AbsoluteUri)</set-url>
                        <set-method>GET</set-method>
                    </send-request>

                    <!—Store response body in context variable -->
                    <set-variable
                      name="userprofile"
                      value="@(((IResponse)context.Variables["userprofileresponse"]).Body.As<string>())" />

                    <!—Store result in cache -->
                    <cache-store-value
                      key="@("userprofile-" + context.Variables["enduserid"])"
                      value="@((string)context.Variables["userprofile"])"
                      duration="100000" />
                </when>
            </choose>
            <base />
        </inbound>
        <outbound>
            <!—Update response body with user profile-->
            <find-and-replace
                  from='"$userprofile$"'
                  to="@((string)context.Variables["userprofile"])" />
            <base />
        </outbound>
     </policies>

Cette approche mise en cache est utilisée principalement dans les sites web où HTML est composée sur le côté serveur afin qu’il peut être affiché comme une seule page. Toutefois, il peut également être utile dans API dans lequel les clients ne peut pas faire client côté de mise en cache HTTP ou il convient ne pas placer cette responsabilité sur le client.

Ce même type de mise en cache de fragment peut également être effectué sur les serveurs web principal à l’aide d’un Redis mise en cache de serveur, cependant, à l’aide du service de gestion de l’API pour effectuer cette opération est utile lorsque les fragments de mises en cache provenant de différents principaux que les réponses principales.

## <a name="transparent-versioning"></a>Contrôle de version transparente
Il est courant pour plusieurs versions d’implémentation différente d’une API à prendre en charge à un moment donné. Il s’agit peut-être pour prendre en charge des environnements différents, tels que le développement, test, production, etc., ou il peut être pour prendre en charge les anciennes versions de l’API pour donner le temps pour les consommateurs API migrer vers une version plus récente. 

Régler ce au lieu de développeurs de client modifier l’URL à partir d’une approche `/v1/customers` à `/v2/customers` consiste à stocker dans les données de profil du consommateur quelle version de l’API qu’ils souhaitent actuellement utilisez et appelez l’URL principales appropriées. Pour déterminer l’URL du serveur principal correcte pour appeler pour un client particulier, il est nécessaire interroger des données de configuration. Mise en cache ces données de configuration, nous pouvons réduire la baisse des performances d’effectuer cette liste de choix.

La première étape consiste à déterminer l’identificateur utilisé pour configurer la version souhaitée. Dans cet exemple, j’ai choisi d’associer la version à la clé du produit abonnement. 

        <set-variable name="clientid" value="@(context.Subscription.Key)" />

Nous puis effectuez une recherche de cache pour voir si nous avons déjà extrait la version du client de votre choix.

        <cache-lookup-value
        key="@("clientversion-" + context.Variables["clientid"])"
        variable-name="clientversion" />

Puis nous Vérifiez si nous n’avons trouvé dessus dans le cache.

    <choose>
        <when condition="@(!context.Variables.ContainsKey("clientversion"))">

Si nous n’a pas été puis nous accédez et le récupérer.

    <send-request
        mode="new"
        response-variable-name="clientconfiguresponse"
        timeout="10"
        ignore-error="true">
                <set-url>@(new Uri(new Uri(context.Api.ServiceUrl.ToString() + "api/ClientConfig/"),(string)context.Variables["clientid"]).AbsoluteUri)</set-url>
                <set-method>GET</set-method>
    </send-request>

Extraire le corps du texte de réponse de la réponse.

    <set-variable
          name="clientversion"
          value="@(((IResponse)context.Variables["clientconfiguresponse"]).Body.As<string>())" />

Stockez-le dans le cache pour une utilisation ultérieure.

    <cache-store-value
          key="@("clientversion-" + context.Variables["clientid"])"
          value="@((string)context.Variables["clientversion"])"
          duration="100000" />

Et enfin mettre à jour l’URL principale pour sélectionner la version du service souhaitée par le client.

    <set-backend-service
          base-url="@(context.Api.ServiceUrl.ToString() + "api/" + (string)context.Variables["clientversion"] + "/")" />

La stratégie complètement est la suivante.

     <inbound>
        <base />
        <set-variable name="clientid" value="@(context.Subscription.Key)" />
        <cache-lookup-value key="@("clientversion-" + context.Variables["clientid"])" variable-name="clientversion" />

        <!-- If we don’t find it in the cache, make a request for it and store it -->
        <choose>
            <when condition="@(!context.Variables.ContainsKey("clientversion"))">
                <send-request mode="new" response-variable-name="clientconfiguresponse" timeout="10" ignore-error="true">
                    <set-url>@(new Uri(new Uri(context.Api.ServiceUrl.ToString() + "api/ClientConfig/"),(string)context.Variables["clientid"]).AbsoluteUri)</set-url>
                    <set-method>GET</set-method>
                </send-request>
                <!-- Store response body in context variable -->
                <set-variable name="clientversion" value="@(((IResponse)context.Variables["clientconfiguresponse"]).Body.As<string>())" />
                <!-- Store result in cache -->
                <cache-store-value key="@("clientversion-" + context.Variables["clientid"])" value="@((string)context.Variables["clientversion"])" duration="100000" />
            </when>
        </choose>
        <set-backend-service base-url="@(context.Api.ServiceUrl.ToString() + "api/" + (string)context.Variables["clientversion"] + "/")" />
    </inbound>


L’activation consommateurs API en toute transparence contrôler la version serveur principal est utilisée par les clients sans avoir à mettre à jour et redéployez clients est une solution élégante qui répond aux besoins de nombreux API le contrôle de version.

## <a name="tenant-isolation"></a>Isolement client

Dans les déploiements plus grandes et clients multiples certaines sociétés créer des groupes distinctes de clients sur des déploiements distincts du matériel principal. Cela permet de réduire le nombre de clients qui sont affectés par un problème matériel sur le serveur principal. Il permet également de nouvelles versions du logiciel à être transférées en plusieurs étapes. Dans l’idéal cette architecture serveur principal doit être transparente pour les consommateurs API. Ceci peut être réalisé dans la même façon que le contrôle de version transparente, car il est basé sur la même technique de manipulation de l’URL du serveur principal à l’aide de l’état de configuration par clé d’API.  

Au lieu de renvoyer une version par défaut de l’API pour chaque clé d’abonnement, vous retournerait un identificateur qui se rapporte un client au groupe matériel affectées. Cet identificateur peut être utilisé pour créer l’URL principales appropriées.

## <a name="summary"></a>Résumé
La liberté d’utiliser le cache de gestion des API Azure pour le stockage de n’importe quel type de données permet d’en faciliter l’accès aux données de configuration qui peuvent affecter le mode de traitée d’une requête entrante. Il peut également être utilisé pour stocker les fragments de données qui peuvent enrichir réponses, retournées à partir d’un serveur principal API.

## <a name="next-steps"></a>Étapes suivantes
Veuillez nous envoyer vos commentaires dans le thread Disqus pour cette rubrique si il existe d’autres scénarios ces politiques ayant activées pour vous, ou s’il existe des scénarios vous souhaite obtenir mais ne sont actuellement possibles n’est ne pas achevée.

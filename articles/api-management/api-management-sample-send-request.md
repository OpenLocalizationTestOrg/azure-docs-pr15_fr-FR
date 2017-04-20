<properties
    pageTitle="À l’aide du service de gestion des API pour générer des requêtes HTTP"
    description="Découvrez comment utiliser les stratégies de demande et réponse gestion des API pour appeler des services externes à partir de votre API"
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


# <a name="using-external-services-from-the-azure-api-management-service"></a>À l’aide des services externes à partir du service de gestion de l’API Azure

Les stratégies disponibles dans le service de gestion des API Azure peuvent faire un large éventail de travail utile en fonction des arguments purement la demande entrante, réponse sortante et informations de configuration de base. Toutefois, la possibilité d’interagir avec des services externes à partir de gestion de l’API stratégies s’ouvre plus de nombreuses opportunités.

Nous l’avons vu précédemment comment nous pouvons interagir avec le [service Azure événement concentrateur pour la journalisation, la surveillance et analytique](api-management-log-to-eventhub-sample.md). Dans cet article, nous présentera service basé sur des stratégies qui vous permettent d’interagir avec n’importe quel HTTP externe. Ces stratégies peuvent être utilisées pour déclenchement d’événements à distance ou pour extraire des informations permettant de manipuler la demande d’origine et la réponse d’une certaine manière.

## <a name="send-one-way-request"></a>Envoyer une façon de requête
Éventuellement l’interaction externe la plus simple est le style fire et oubliez de demande qui permet à un service externe être averti d’un certain type d’événement important. Nous pouvons utiliser la stratégie de flux de contrôle `choose` pour détecter tout type de condition que nous intéresse et puis, si la condition est remplie, nous pouvons effectuer une demande HTTP externe à l’aide de la stratégie [d’Envoyer une façon de requête](https://msdn.microsoft.com/library/azure/dn894085.aspx#SendOneWayRequest) . Cela peut être une demande d’un système de messagerie telle que Hipchat ou marge ou un message API comme SendGrid ou MailChimp, ou pour incidents de support critiques quelque chose comme PagerDuty. Tous ces systèmes de messagerie ont APIs HTTP simples que nous pouvons appeler facilement.

### <a name="alerting-with-slack"></a>Les alertes avec la marge
L’exemple suivant montre comment envoyer un message à une salle de conversation marge si le code d’état de réponse HTTP est supérieur ou égal à 500. Une erreur de 500 plage indique un problème avec notre serveur principal API que le client de notre API ne peut pas résoudre eux-mêmes. Elle requiert généralement un certain type de l’intervention de notre part.  

    <choose>
        <when condition="@(context.Response.StatusCode >= 500)">
          <send-one-way-request mode="new">
            <set-url>https://hooks.slack.com/services/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</set-url>
            <set-method>POST</set-method>
            <set-body>@{
                    return new JObject(
                            new JProperty("username","APIM Alert"),
                            new JProperty("icon_emoji", ":ghost:"),
                            new JProperty("text", String.Format("{0} {1}\nHost: {2}\n{3} {4}\n User: {5}",
                                                    context.Request.Method,
                                                    context.Request.Url.Path + context.Request.Url.QueryString,
                                                    context.Request.Url.Host,
                                                    context.Response.StatusCode,
                                                    context.Response.StatusReason,
                                                    context.User.Email
                                                    ))
                            ).ToString();
                }</set-body>
          </send-one-way-request>
        </when>
    </choose>

Marge possède la notion de crochets web entrants. Lorsque vous configurez un crochet web entrants, marge génère une URL spéciale qui vous permet d’effectuer une demande POST simple et pour passer d’un message dans le canal marge. Le corps JSON que nous créons est basé sur un format défini par la marge.

![Crochet Web marge](./media/api-management-sample-send-request/api-management-slack-webhook.png)

### <a name="is-fire-and-forget-good-enough"></a>Est fire et oublier suffisamment intéressante ?
Il existe certains compromis lors de l’utilisation d’un style fire et oubliez de demande. Si, pour une raison quelconque, la requête échoue, puis l’échec n’est pas signalé. Dans cette situation particulière, la complexité de disposer d’une défaillance secondaire reporting système et le coût des performances supplémentaires d’en attente de la réponse n’est pas justifiée. Pour les scénarios où il est essentiel de vérification de la réponse, puis la stratégie de [demande d’envoi](https://msdn.microsoft.com/library/azure/dn894085.aspx#SendRequest) est une meilleure option.

## <a name="send-request"></a>Demande d’envoi
La `send-request` stratégie permet à l’aide d’un service externe pour effectuer des fonctions de traitement complexe et retourner des données à la gestion de l’API de service qui peut être utilisée pour le traitement des stratégies supplémentaire.

### <a name="authorizing-reference-tokens"></a>Autorisation des jetons de référence
Une fonction majeure de gestion de l’API est protection des ressources de serveur principal. Si le serveur d’autorisations utilisé par votre API crée des [jetons de JWT](http://jwt.io/) dans le cadre de son flux oauth2 ne, comme le fait [d’Azure Active Directory](../active-directory/active-directory-aadconnect.md) , vous pouvez ensuite utiliser la `validate-jwt` stratégie de vérifier la validité du jeton. Toutefois, certains serveurs d’autorisation créent ce que sont appelé [jetons de référence](http://leastprivilege.com/2015/11/25/reference-tokens-and-introspection/) qui ne peut pas être vérifiée sans effectuer un appel vers le serveur d’autorisation.

### <a name="standardized-introspection"></a>Introspection standardisée
Dans le passé n’a été aucun moyen standardisé un jeton de référence avec un serveur d’autorisation à vérifier. Toutefois, un standard récemment proposées [RFC 7662](https://tools.ietf.org/html/rfc7662) a été publié par l’IETF qui définit la façon dont un serveur de ressources peut vérifier la validité d’un jeton.

### <a name="extracting-the-token"></a>Extraire le jeton
La première étape consiste à extraire le jeton de l’en-tête d’autorisation. La valeur d’en-tête doit être mis en forme avec la `Bearer` schéma d’autorisation, un seul espace, puis le jeton d’autorisation conformément à [RFC 6750](http://tools.ietf.org/html/rfc6750#section-2.1). Il existe malheureusement cas dans lequel la stratégie d’autorisation est omis. Pour en tenir compte lors de l’analyse, nous fractionner la valeur d’en-tête dans un espace et de sélectionner la dernière chaîne à partir de la matrice renvoyée de chaînes. Cela fournit une solution de contournement pour les en-têtes d’autorisation mal formaté.

    <set-variable name="token" value="@(context.Request.Headers.GetValueOrDefault("Authorization","scheme param").Split(' ').Last())" />

### <a name="making-the-validation-request"></a>Qui effectue la demande de validation
Une fois que nous avons le jeton d’autorisation, nous pouvons faire la demande pour valider le jeton. RFC 7662 appelle cette introspection processus et requiert que vous `POST` un formulaire HTML pour la ressource introspection. Le formulaire HTML doit contenir au moins une paire clé/valeur avec la touche `token`. Cette requête sur le serveur d’autorisation également doit être authentifiée pour vous assurer que clients malveillants ne peuvent pas accéder chalutage pour les jetons valides.

    <send-request mode="new" response-variable-name="tokenstate" timeout="20" ignore-error="true">
      <set-url>https://microsoft-apiappec990ad4c76641c6aea22f566efc5a4e.azurewebsites.net/introspection</set-url>
      <set-method>POST</set-method>
      <set-header name="Authorization" exists-action="override">
        <value>basic dXNlcm5hbWU6cGFzc3dvcmQ=</value>
      </set-header>
      <set-header name="Content-Type" exists-action="override">
        <value>application/x-www-form-urlencoded</value>
      </set-header>
      <set-body>@($"token={(string)context.Variables["token"]}")</set-body>
    </send-request>

### <a name="checking-the-response"></a>Vérification de la réponse
La `response-variable-name` attribut est utilisé pour donner accès à la réponse renvoyée. Le nom défini dans cette propriété peut être utilisé comme clé dans la `context.Variables` dictionnaire pour accéder à la `IResponse` objet.

À partir de l’objet response nous pouvons extraire le corps et RFC 7622 nous indique que la réponse doit être un objet JSON et doit contenir au moins une propriété appelée `active` qui est une valeur booléenne. Lorsque `active` est vraie, le jeton est considérée comme valide.

### <a name="reporting-failure"></a>Échec de création de rapports
Nous utilisons une `<choose>` stratégie pour détecter si le jeton n’est pas valide et le cas échéant, renvoyer une réponse 401.

    <choose>
      <when condition="@((bool)((IResponse)context.Variables["tokenstate"]).Body.As<JObject>()["active"] == false)">
        <return-response response-variable-name="existing response variable">
          <set-status code="401" reason="Unauthorized" />
          <set-header name="WWW-Authenticate" exists-action="override">
            <value>Bearer error="invalid_token"</value>
          </set-header>
        </return-response>
      </when>
    </choose>

Conformément à [RFC 6750](https://tools.ietf.org/html/rfc6750#section-3) qui décrit comment `bearer` jetons doivent être utilisés, nous avons également renvoyer un `WWW-Authenticate` avec la réponse 401. L’authentification WWW est conçu pour demander à un client sur la création d’une demande de conseillers correctement. En raison de la diversité des approches offertes par l’infrastructure d’oauth2 ne, il est difficile communiquer toutes les informations nécessaires. Il existe peut être effectué en efforts en cours d’exécution pour aider les [clients de découvrir comment autoriser correctement les requêtes vers un serveur de ressources](http://tools.ietf.org/html/draft-jones-oauth-discovery-00).

### <a name="final-solution"></a>Solution finale
Placer tous les éléments ensemble, nous obtenir la stratégie suivante :

    <inbound>
      <!-- Extract Token from Authorization header parameter -->
      <set-variable name="token" value="@(context.Request.Headers.GetValueOrDefault("Authorization","scheme param").Split(' ').Last())" />

      <!-- Send request to Token Server to validate token (see RFC 7662) -->
      <send-request mode="new" response-variable-name="tokenstate" timeout="20" ignore-error="true">
        <set-url>https://microsoft-apiappec990ad4c76641c6aea22f566efc5a4e.azurewebsites.net/introspection</set-url>
        <set-method>POST</set-method>
        <set-header name="Authorization" exists-action="override">
          <value>basic dXNlcm5hbWU6cGFzc3dvcmQ=</value>
        </set-header>
        <set-header name="Content-Type" exists-action="override">
          <value>application/x-www-form-urlencoded</value>
        </set-header>
        <set-body>@($"token={(string)context.Variables["token"]}")</set-body>
      </send-request>

      <choose>
            <!-- Check active property in response -->
            <when condition="@((bool)((IResponse)context.Variables["tokenstate"]).Body.As<JObject>()["active"] == false)">
                <!-- Return 401 Unauthorized with http-problem payload -->
                <return-response response-variable-name="existing response variable">
                    <set-status code="401" reason="Unauthorized" />
                    <set-header name="WWW-Authenticate" exists-action="override">
                        <value>Bearer error="invalid_token"</value>
                    </set-header>
                </return-response>
            </when>
        </choose>
      <base />
    </inbound>

Il s’agit uniquement un des nombreux exemples de la façon dont `send-request` stratégie peut être utilisée pour intégrer des services externes utiles dans le processus de demandes et les réponses s’étalant via le service de gestion de l’API.

## <a name="response-composition"></a>Composition de réponse
La `send-request` stratégie peut être utilisée pour améliorer une demande principale à un système d’arrière-plan, comme nous l’avons vu dans l’exemple précédent, ou il peut être utilisé comme un remplacement pour complète de l’appel et. À l’aide de cette technique nous pouvons créer facilement composites ressources regroupées à partir de plusieurs systèmes différents.

### <a name="building-a-dashboard"></a>Création d’un tableau de bord   
Parfois, vous souhaitez pouvoir pour exposer les informations présentes dans plusieurs systèmes principaux, par exemple, pour un tableau de bord du lecteur. Indicateurs de performance clés proviennent de tous les principaux différents, mais vous préférez ne pas fournir un accès direct et il peut être intéressant si toutes les informations peuvent être récupérées dans une seule demande. Certaines des informations de version serveur doit peut-être certains découpage et de découpage et de nettoyage un peu tout d’abord ! La possibilité de mettre en cache cette ressource composite serait un utile réduire la charge principale comme vous le savez utilisateurs ont l’habitude de marteler la touche F5 pour voir si leurs indicateurs peu performantes peut changer.    

### <a name="faking-the-resource"></a>Émulant la ressource
La première étape à la création de notre ressource du tableau de bord consiste à configurer une nouvelle opération dans le portail de gestion de l’API de publisher. Il s’agit d’une opération d’espace réservé permettant de configurer notre politique de composition pour créer notre ressource dynamique.

![Opération de tableau de bord](./media/api-management-sample-send-request/api-management-dashboard-operation.png)

### <a name="making-the-requests"></a>Rendre les requêtes
Une fois la `dashboard` opération a été créée, nous pouvons configurer une stratégie spécifiquement pour cette opération. 

![Opération de tableau de bord](./media/api-management-sample-send-request/api-management-dashboard-policy.png)

La première étape consiste à extraire les paramètres de la requête de la demande entrante, afin que nous puissions les transférer à notre principal. Dans cet exemple notre tableau de bord est présentent des informations basées sur une période donnée une par conséquent a une `fromDate` et `toDate` paramètre. Nous pouvons utiliser la `set-variable` stratégie pour extraire les informations à partir de l’URL de requête.

    <set-variable name="fromDate" value="@(context.Request.Url.Query["fromDate"].Last())">
    <set-variable name="toDate" value="@(context.Request.Url.Query["toDate"].Last())">

Une fois que nous avons ces informations nous pouvons demander à tous les systèmes de serveur principal. Chaque demande construit une nouvelle URL avec les informations sur les paramètres et appelle son propre serveur et stocke la réponse dans une variable de contexte.

    <send-request mode="new" response-variable-name="revenuedata" timeout="20" ignore-error="true">
      <set-url>@($"https://accounting.acme.com/salesdata?from={(string)context.Variables["fromDate"]}&to={(string)context.Variables["fromDate"]}")"</set-url>
      <set-method>GET</set-method>
    </send-request>

    <send-request mode="new" response-variable-name="materialdata" timeout="20" ignore-error="true">
      <set-url>@($"https://inventory.acme.com/materiallevels?from={(string)context.Variables["fromDate"]}&to={(string)context.Variables["fromDate"]}")"</set-url>
      <set-method>GET</set-method>
    </send-request>

    <send-request mode="new" response-variable-name="throughputdata" timeout="20" ignore-error="true">
    <set-url>@($"https://production.acme.com/throughput?from={(string)context.Variables["fromDate"]}&to={(string)context.Variables["fromDate"]}")"</set-url>
      <set-method>GET</set-method>
    </send-request>

    <send-request mode="new" response-variable-name="accidentdata" timeout="20" ignore-error="true">
    <set-url>@($"https://production.acme.com/throughput?from={(string)context.Variables["fromDate"]}&to={(string)context.Variables["fromDate"]}")"</set-url>
      <set-method>GET</set-method>
    </send-request>

Ces demandes seront exécutera en séquence, qui n’est pas idéale. Dans une version à venir nous introduisant une nouvelle stratégie nommée `wait` qui permettra toutes ces requêtes à exécuter en parallèle.

### <a name="responding"></a>Répondre

Pour créer la réponse composite, nous pouvons utiliser la stratégie de [réponse de retour](https://msdn.microsoft.com/library/azure/dn894085.aspx#ReturnResponse) . La `set-body` élément peut utiliser une expression pour générer une nouvelle `JObject` avec toutes les représentations composant incorporées en tant que propriétés.

    <return-response response-variable-name="existing response variable">
      <set-status code="200" reason="OK" />
      <set-header name="Content-Type" exists-action="override">
        <value>application/json</value>
      </set-header>
      <set-body>
        @(new JObject(new JProperty("revenuedata",((IResponse)context.Variables["revenuedata"]).Body.As<JObject>()),
                      new JProperty("materialdata",((IResponse)context.Variables["materialdata"]).Body.As<JObject>()),
                      new JProperty("throughputdata",((IResponse)context.Variables["throughputdata"]).Body.As<JObject>()),
                      new JProperty("accidentdata",((IResponse)context.Variables["accidentdata"]).Body.As<JObject>())
                      ).ToString())
      </set-body>
    </return-response>

La stratégie complète se présente comme suit :

    <policies>
        <inbound>

      <set-variable name="fromDate" value="@(context.Request.Url.Query["fromDate"].Last())">
      <set-variable name="toDate" value="@(context.Request.Url.Query["toDate"].Last())">

        <send-request mode="new" response-variable-name="revenuedata" timeout="20" ignore-error="true">
          <set-url>@($"https://accounting.acme.com/salesdata?from={(string)context.Variables["fromDate"]}&to={(string)context.Variables["fromDate"]}")"</set-url>
          <set-method>GET</set-method>
        </send-request>

        <send-request mode="new" response-variable-name="materialdata" timeout="20" ignore-error="true">
          <set-url>@($"https://inventory.acme.com/materiallevels?from={(string)context.Variables["fromDate"]}&to={(string)context.Variables["fromDate"]}")"</set-url>
          <set-method>GET</set-method>
        </send-request>

        <send-request mode="new" response-variable-name="throughputdata" timeout="20" ignore-error="true">
        <set-url>@($"https://production.acme.com/throughput?from={(string)context.Variables["fromDate"]}&to={(string)context.Variables["fromDate"]}")"</set-url>
          <set-method>GET</set-method>
        </send-request>

        <send-request mode="new" response-variable-name="accidentdata" timeout="20" ignore-error="true">
        <set-url>@($"https://production.acme.com/throughput?from={(string)context.Variables["fromDate"]}&to={(string)context.Variables["fromDate"]}")"</set-url>
          <set-method>GET</set-method>
        </send-request>

        <return-response response-variable-name="existing response variable">
          <set-status code="200" reason="OK" />
          <set-header name="Content-Type" exists-action="override">
            <value>application/json</value>
          </set-header>
          <set-body>
            @(new JObject(new JProperty("revenuedata",((IResponse)context.Variables["revenuedata"]).Body.As<JObject>()),
                          new JProperty("materialdata",((IResponse)context.Variables["materialdata"]).Body.As<JObject>()),
                          new JProperty("throughputdata",((IResponse)context.Variables["throughputdata"]).Body.As<JObject>()),
                          new JProperty("accidentdata",((IResponse)context.Variables["accidentdata"]).Body.As<JObject>())
                          ).ToString())
          </set-body>
        </return-response>
        </inbound>
        <backend>
            <base />
        </backend>
        <outbound>
            <base />
        </outbound>
    </policies>

Dans la configuration de l’espace réservé opération que nous pouvons configurer la ressource de tableau de bord doit être mis en cache au moins une heure, car nous comprendre la nature des données signifie que même s’il s’agit d’une heure obsolète, que vous pourrez toujours y suffisamment efficace transmettre les informations importantes aux utilisateurs.

## <a name="summary"></a>Résumé
Service de gestion de l’API Azure fournit des stratégies flexibles qui peuvent être appliqués de manière sélective le trafic HTTP et permet de composition de services principaux. Si vous souhaitez améliorer votre passerelle API avec l’alerte de fonctions, la vérification des fonctionnalités de validation ou créer de nouvelles ressources composites basées sur plusieurs services de serveur principal, le `send-request` et les stratégies associées ouvrir un monde de possibilités.

## <a name="watch-a-video-overview-of-these-policies"></a>Regarder une vidéo de présentation de ces politiques
Pour plus d’informations sur la [demande envoyer à celui-moyen](https://msdn.microsoft.com/library/azure/dn894085.aspx#SendOneWayRequest) [demande d’envoi](https://msdn.microsoft.com/library/azure/dn894085.aspx#SendRequest)et les stratégies de [retour-réponse](https://msdn.microsoft.com/library/azure/dn894085.aspx#ReturnResponse) traitées dans cet article, veuillez regardez la vidéo suivante.

> [AZURE.VIDEO send-request-and-return-response-policies]

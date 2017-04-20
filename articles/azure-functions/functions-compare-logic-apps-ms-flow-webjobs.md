<properties
    pageTitle="Choisir entre flux, applications logique, fonctions et WebJobs | Microsoft Azure"
    description="Comparez la pour cloud integration services depuis Microsoft et décider quels services vous devez utiliser."
    services="functions,app-service\logic"
    documentationCenter="na"
    authors="cephalin"
    manager="wpickett"
    tags=""
    keywords="Microsoft flux, flux, applications logique, fonctions azure, fonctions, azure webjobs, webjobs, traitement, cluster dynamique, sans serveur architecture des événements"/>

<tags
    ms.service="functions"
    ms.devlang="multiple"
    ms.topic="article"
    ms.tgt_pltfrm="multiple"
    ms.workload="na"
    ms.date="09/08/2016"
    ms.author="chrande; glenga"/>

# <a name="choose-between-flow-logic-apps-functions-and-webjobs"></a>Choisir entre flux, applications logique, fonctions et WebJobs

Cet article compare et distingue les services suivants dans le nuage Microsoft, qui peut résoudre des problèmes d’intégration et automatisation des processus métiers :

- [Flux de Microsoft](https://flow.microsoft.com/)
- [Applications Azure logique](https://azure.microsoft.com/services/logic-apps/)
- [Fonctions Azure](https://azure.microsoft.com/services/functions/)
- [Azure Application Service WebJobs](../app-service-web/web-sites-create-web-jobs.md)

Tous ces services sont utiles lorsque « collant « différents systèmes. Ils toutes définissent input, actions, conditions et sortie. Vous pouvez exécuter chacun d’eux sur une grille ou un déclencheur. Toutefois, chaque service ajoute un ensemble unique de valeur et comparant ne sont pas une question de « quel service est le meilleur ? » mais un des « quel service le mieux cette situation ? » Une combinaison de ces services est souvent, la meilleure façon de créer rapidement une solution complète, scalable intégration proposée.

<a name="flow"></a>
## <a name="flow-vs-logic-apps"></a>Logique de flux et applications

Nous pouvons voir Flow Microsoft et les applications de logique Azure ensemble, car ils sont les deux services d’intégration *d’abord configuration* , ce qui facilite son générer des processus et des flux de travail et intégrer différentes applications SaaS et d’entreprise. 

- Flux intégré en haut de la logique d’applications
- Ils ont le même concepteur de flux de travail
- Les [connecteurs](../connectors/apis-list.md) qui fonctionnent dans une fonctionne également dans l’autre

Flux donne un employé de bureau pour procéder à des intégrations simples (par exemple : get SMS pour les messages électroniques importants) sans passer par les développeurs ou informatique. En revanche, applications logique pouvez activer avancées ou critiques intégrations (par exemple, processus B2B) où sont requises pratiques DevOps et la sécurité au niveau de l’entreprise. Il est généralement utilisé pour un flux de travail business Agrandir dans les heures supplémentaires la complexité. Par conséquent, vous pouvez commencer avec un flux dans un premier temps, puis convertir en une application logique selon vos besoins.

Le tableau suivant vous permet de déterminer si le flux ou logique applications est recommandées pour une intégration donnée.

|               | Flux                                                                             | Applications de logique                                                                                          |
|---------------|----------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------|
| Audience      | employés de bureau, les utilisateurs professionnels                                                   | Professionnels de l’informatique, les développeurs                                                                                 |
| Scénarios     | Inscription en libre service                                                                     | Critiques                                                                                    |
| Outil de conception   | Dans un navigateur, uniquement l’interface utilisateur                                                              | Dans le navigateur et [Visual Studio](../app-service/logic/app-service-logic-deploy-from-vs.md), [le mode Code](../app-service-logic/app-service-logic-author-definitions.md) disponible |
| DevOps        | Ad hoc, développer de production                                                    | contrôle de code source, test, prise en charge et automation et facilité de gestion de [La gestion des ressources Azure](../app-service-logic/app-service-logic-arm-provision.md)|
| Expérience d’administration| [https://Flow.Microsoft.com](https://flow.microsoft.com)                       | [https://Portal.Azure.com](https://portal.azure.com)                                                |
| Sécurité      | Pratiques standards : [chiffrement inactives](https://wikipedia.org/wiki/Data_at_rest#Encryption) pour des données sensibles, etc., [souveraineté de données](https://wikipedia.org/wiki/Technological_Sovereignty). | Garantie de sécurité d’Azure : [Sécurité Azure](https://www.microsoft.com/trustcenter/Security/AzureSecurity), [Centre de sécurité](https://azure.microsoft.com/services/security-center/), [des journaux d’audit](https://azure.microsoft.com/blog/azure-audit-logs-ux-refresh/)et plus encore. |

<a name="function"></a>
## <a name="functions-vs-webjobs"></a>Fonctions et WebJobs

Nous pouvez discuter des fonctions Azure et Azure Application Service WebJobs ensemble, car ils sont les deux services d’intégration *d’abord code* et conçu pour les développeurs. Ils permettent de vous permet d’exécuter un script ou une partie du code en réponse aux événements différents, tels que [Nouveau stockage BLOB](functions-bindings-storage.md) ou [d’un WebHook demander](functions-bindings-http-webhook.md). Voici les similitudes : 

- Les deux reposent sur [Azure Application Service](../app-service/app-service-value-prop-what-is.md) et bénéficient de fonctionnalités telles que le [contrôle de code source](../app-service-web/app-service-continuous-deployment.md), [l’authentification](../app-service/app-service-authentication-overview.md)et [de surveillance](../app-service-web/web-sites-monitor.md).
- Sont tous deux des services destinés aux développeurs.
- Les deux prennent en charge l’écriture de script standard et langages de programmation.
- Les deux ont NuGet et NPM prend en charge.

Fonctions est l’évolution naturelle de WebJobs dans la mesure où elle prend meilleures choses WebJobs et améliore les. Les améliorations apportées sont les suivantes : 

- Développement simplifiée, tester et exécuter du code, directement dans le navigateur.
- Intégration à des services plus Azure et 3ème tiers comme [GitHub WebHooks](https://developer.github.com/webhooks/creating/).
- Paie-utilisation, sans avoir à payer pour une [Application Service planifier](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md).
- Automatique, [la mise à l’échelle dynamique](functions-scale.md).
- Pour les clients existants de Service d’application, s’exécutant sur plan de services d’application toujours possible (pour tirer parti des ressources utilisées sous).
- Intégration avec les applications de logique.

Le tableau suivant récapitule les différences entre les fonctions et WebJobs :

|                        | Fonctions                                                                                                                                                                | WebJobs                            |
|------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------|------------------------------------|
| Mise à l’échelle                | Mise à l’échelle configurationless                                                                                                                                                | mettre à l’échelle avec plan de services d’application        |
| Tarifs                | Payer par utilisation ou une partie d’un plan de services d’application                                                                                                                                  | Partie du plan de services d’application           |
| Exécuter-type               | déclenchée, planifiées (par déclencheur minuteur)                                                                                                                                  | déclenchées, continue, planifiée   |
| Déclencher des événements         | [timer](functions-bindings-timer.md), [DocumentDB Azure](functions-bindings-documentdb.md), [Azure événement Hubs](functions-bindings-event-hubs), [HTTP/WebHook (GitHub, marge)](functions-bindings-http-webhook.md), [Azure Application Service Mobile applications](functions-bindings-mobile-apps.md), [Azure Notification Hubs](functions-bindings-notification-hubs.md), [Bus des services Azure](functions-bindings-service-bus.md), [Le stockage Azure](articles/functions-bindings-storage.md) | [Stockage azure](websites-dotnet-webjobs-sdk-storage-blobs-how-to.md), [Bus des services Azure](websites-dotnet-webjobs-sdk-service-bus.md)         |
| Développement dans le navigateur | x                                                                                                                                                                        |                                    |
| Fenêtre de script       | expérience                                                                                                                                                             | x                                  |
| PowerShell             | expérience                                                                                                                                                             | x                                  |
| C#                     | x                                                                                                                                                                        | x                                  |
| F#                     | x                                                                                                                                                                        |                                    |
| Bash                   | expérience                                                                                                                                                             | x                                  |
| PHP                    | expérience                                                                                                                                                             | x                                  |
| Python                 | expérience                                                                                                                                                             | x                                  |
| JavaScript             | x                                                                                                                                                                        | x                                  |
| Java                   | expérience                                                                                                                                                             | x                                  |

Si vous souhaitez utiliser les fonctions ou WebJobs dépend de ce que vous faites déjà avec le Service d’application. Si vous avez une application de Service d’application pour laquelle vous souhaitez exécuter extraits de code et que vous voulez gérer les ensemble dans le même environnement DevOps, vous devez utiliser WebJobs. Si vous souhaitez exécuter extraits de code pour d’autres services Azure ou les applications de même 3ème tiers, ou si vous voulez gérer vos extraits de code d’intégration séparément à partir de vos applications de Service d’application, ou si vous voulez appeler vos extraits de code à partir d’une application logique, vous devez tirer parti de toutes les améliorations dans les fonctions.  

<a name="together"></a>
## <a name="flow-logic-apps-and-functions-together"></a>Ne forment, des fonctions et des applications de logique

Comme indiqué précédemment, le service qui est la mieux adapté à votre varie selon votre situation. 

- Pour optimiser les métier simple, puis utilisez le flux.
- Si votre scénario d’intégration est trop avancé pour le flux, ou vous avez besoin des fonctionnalités de DevOps et cas de sécurité, puis utiliser les applications logique.
- Si une étape dans votre scénario d’intégration nécessite transformation hautement personnalisée ou un code spécialisé, puis rédigez une application de la fonction et puis déclencher la lecture d’une fonction en tant qu’une action dans votre application logique.

Vous pouvez appeler une application logique dans un flux. Vous pouvez également appeler une fonction dans une application logique et une application logique dans une fonction. L’intégration entre les fonctions, applications logique et de flux de continuer à améliorer des heures supplémentaires. Vous pouvez créer un élément dans un service et l’utiliser dans les autres services. Par conséquent, tout investissement que vous apportez dans ces trois technologies est utile.

## <a name="next-steps"></a>Étapes suivantes

Prise en main chacun des services en créant votre flux, application logique, fonction application ou WebJob première. Cliquez sur un des liens suivants :

- [Prise en main Flow Microsoft](https://flow.microsoft.com/en-us/documentation/getting-started/)
- [Créer une application de logique](../app-service-logic/app-service-logic-create-a-logic-app.md)
- [Créer votre première fonction Azure](../azure-functions/functions-create-first-azure-function.md)
- [Déployer WebJobs à l’aide de Visual Studio](../app-service-web/websites-dotnet-deploy-webjobs.md)

Ou, obtenez plus d’informations sur ces services d’intégration avec les liens suivants :

- [Exploiter les fonctions Azure et Azure Application Service pour des scénarios d’intégration en Christopher Anderson](http://www.biztalk360.com/integrate-2016-resources/leveraging-azure-functions-azure-app-service-integration-scenarios/)
- [Intégrations apportées Simple par Charles Lamanna](http://www.biztalk360.com/integrate-2016-resources/integrations-made-simple/)
- [Présentation technique des applications de logique](http://aka.ms/logicappslive)
- [Microsoft flux Forum aux questions](https://flow.microsoft.com/documentation/frequently-asked-questions/)
- [Ressources de documentation WebJobs Azure](../app-service-web/websites-webjobs-resources.md)

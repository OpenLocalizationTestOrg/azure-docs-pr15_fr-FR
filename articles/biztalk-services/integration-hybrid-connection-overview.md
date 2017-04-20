<properties
    pageTitle="Vue d’ensemble des connexions hybride | Microsoft Azure"
    description="En savoir plus sur les connexions hybride, la sécurité, les ports TCP et configurations prises en charge. MABS, WABS."
    services="biztalk-services"
    documentationCenter=""
    authors="MandiOhlinger"
    manager="erikre"
    editor=""/>

<tags
    ms.service="biztalk-services"
    ms.workload="integration"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="10/18/2016"
    ms.author="ccompy"/>


# <a name="hybrid-connections-overview"></a>Vue d’ensemble des connexions hybride
Présentation des connexions hybride, répertorie les configurations pris en charge et les ports TCP requis.


## <a name="what-is-a-hybrid-connection"></a>Qu’est une connexion hybride

Connexions hybride sont une fonctionnalité d’Azure BizTalk Services. Connexions hybride offrent un moyen facile et pratique pour vous connecter la fonctionnalité d’applications Web dans le Service d’application Azure (anciennement sites Web) et la fonctionnalité d’applications Mobile dans le Service d’application Azure (anciennement Mobile Services) vers des ressources locales derrière le pare-feu.

![Connexions hybride][HCImage]

Hybride connexions les avantages suivants :

- Applications Web et les applications mobiles peuvent accéder en toute sécurité les données locales et les services existants.
- Plusieurs applications Web et applications Mobile peuvent partager une connexion hybride pour accéder à une ressource locale.
- Ports TCP minimales sont requises pour accéder à votre réseau.
- Applications à l’aide de connexions hybride accéder uniquement la ressource spécifique en local est publiée via la connexion hybride.
- Peuvent se connecter à toutes les ressources locales qui utilise un port TCP statique, tels que SQL Server, MySQL, API Web HTTP et la plupart des Services Web personnalisée.

    > [AZURE.NOTE] Les services TCP qui utilisent des ports dynamiques (par exemple, Mode FTP passif ou en Mode passif étendu) ne sont actuellement pas pris en charge. LDAP n’est pas prise en charge également. LDAP utilise un port TCP statique, mais elle peut également utiliser UDP. Par conséquent, il n'est pas pris en charge.

- Peut être utilisé avec tous les structures pris en charge par les applications Web (.NET, PHP, Java, Python, Node.js) et les applications mobiles (Node.js, .NET).
- Applications Web et applications mobiles peuvent accéder ressources locales de la même façon que si le Web ou une application Mobile se trouve sur votre réseau local. Par exemple, la même connexion chaîne utilisée en local peut également être utilisé sur Azure.


Connexions hybride également fournissent les administrateurs d’entreprise avec le contrôle et la visibilité dans les ressources d’entreprise accédé par applications hybrides, notamment :

- Paramètres de stratégie de groupe, les administrateurs peuvent autoriser les connexions hybride sur le réseau et également désigner ressources qui sont accessibles par les applications hybride.
- Journaux d’événements et d’audit du réseau d’entreprise offrent une visibilité les ressources accédé via les connexions hybride.


## <a name="example-scenarios"></a>Exemples de scénarios

Connexions hybride prennent en charge les combinaisons d’infrastructure et application suivantes :

- Accès .NET framework pour SQL Server
- Accès .NET framework aux services HTTP/HTTPS avec WebClient
- Accès PHP à SQL Server, MySQL
- Accès Java à SQL Server, MySQL et Oracle
- Accès aux services HTTP/HTTPS Java

Lorsque vous utilisez connexions hybride pour accéder à locale SQL Server, procédez comme suit :

- Instances de nommé SQL Express doit être configuré pour utiliser les ports statiques. Par défaut, SQL Express instances nommées utiliser des ports dynamiques.
- SQL Express par défaut Instances utilisent un port statique, mais TCP doit être activé. Par défaut, TCP n’est pas activée.
- Lorsque vous utilisez cluster ou groupes de disponibilité, la `MultiSubnetFailover=true` mode n’est actuellement pas pris en charge.
- La `ApplicationIntent=ReadOnly` n’est actuellement pas pris en charge.
- Authentification SQL peuvent être nécessaire en tant que la méthode de l’autorisation de bout en bout pris en charge par l’application Azure et SQL server locale.


## <a name="security-and-ports"></a>Ports et sécurité

Connexions hybride utilisent l’autorisation de Signature accès partagé (sa) pour sécuriser les connexions entre les applications Azure et le Gestionnaire de connexions hybrides en local et la connexion hybride. Clés de connexion distincte sont créées pour l’application et le Gestionnaire de connexions hybride local. Ces touches connexion peuvent être annulés et révoqués séparément.

Fournissent des connexions hybride pour la distribution transparente et sécurisée des clés pour les applications et le Gestionnaire de connexions hybride local.

Voir [créer et gérer les connexions hybride](integration-hybrid-connection-create-manage.md).

*Autorisation de l’application est différente de la connexion hybride*. N’importe quelle méthode d’autorisation appropriée peut être utilisée. La méthode d’autorisation dépend des méthodes d’autorisation de bout en bout pris en charge par le cloud Azure et les composants locaux. Par exemple, votre application Azure accède à un serveur SQL local. Dans ce scénario, autorisation de SQL peut être la méthode d’autorisation qui est pris en charge de bout en bout.

#### <a name="tcp-ports"></a>Ports TCP
Les connexions hybride nécessitent une connectivité TCP ou HTTP sortante uniquement à partir de votre réseau privé. Vous n’avez pas besoin ouvrir les ports de pare-feu ou modifier votre configuration du périmètre réseau pour autoriser les connexions entrantes à votre réseau.

Les ports TCP suivants sont utilisées par les connexions hybride :

Port | Pourquoi vous en avez besoin
--- | ---
9350 - 9354 | Ces ports sont utilisés pour la transmission des données. Le Gestionnaire de relais Bus des services détecte port 9350 pour déterminer si la connectivité TCP est disponible. Si elle est disponible, il suppose que port 9352 est également disponible. Le trafic de données dépasse port 9352. <br/><br/>Autoriser les connexions sortantes à ces ports.
5671 | Lorsque le port 9352 est utilisé pour le trafic de données, port 5671 est utilisé comme le canal de contrôle. <br/><br/>Autoriser les connexions sortantes à ce port.
80, 443 | Ces ports sont utilisés pour des demandes de données à Azure. En outre, si les ports 9352 et 5671 ne sont pas utilisables, *puis* les ports 80 et 443 sont les ports secours utilisés pour la transmission des données et le canal de contrôle.<br/><br/>Autoriser les connexions sortantes à ces ports. <br/><br/>**Remarque** Il n’est pas recommandé d’utiliser ces que les ports de secours à la place les autres ports TCP. Le HTTP/WebSocket est utilisé comme protocole au lieu de TCP native pour les canaux de données. Il peut entraîner des performances inférieur.



## <a name="next-steps"></a>Étapes suivantes

[Créer et gérer les connexions hybride](integration-hybrid-connection-create-manage.md)<br/>
[Connecter des applications Web Azure à une ressource locale](../app-service-web/web-sites-hybrid-connection-get-started.md)<br/>
[Connectez-vous à locale SQL Server à partir d’une application web Azure](../app-service-web/web-sites-hybrid-connection-connect-on-premises-sql-server.md)<br/>


## <a name="see-also"></a>Voir aussi

[API REST de gestion des Services BizTalk sur Microsoft Azure](http://msdn.microsoft.com/library/azure/dn232347.aspx)
[Services BizTalk : graphique des éditions](biztalk-editions-feature-chart.md)<br/>
[Créer un BizTalk Service à l’aide du portail Azure](biztalk-provision-services.md)<br/>
[BizTalk Services : Onglets de tableau de bord, surveiller et échelle](biztalk-dashboard-monitor-scale-tabs.md)<br/>

[HCImage]: ./media/integration-hybrid-connection-overview/WABS_HybridConnectionImage.png
[HybridConnectionTab]: ./media/integration-hybrid-connection-overview/WABS_HybridConnectionTab.png
[HCOnPremSetup]: ./media/integration-hybrid-connection-overview/WABS_HybridConnectionOnPremSetup.png
[HCManageConnection]: ./media/integration-hybrid-connection-overview/WABS_HybridConnectionManageConn.png

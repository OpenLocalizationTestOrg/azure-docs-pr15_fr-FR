<properties 
    pageTitle="Configuration d’un pare-feu d’Application Web (WAF) pour un environnement de Service d’application" 
    description="Découvrez comment configurer un pare-feu d’application web devant votre environnement de Service d’application." 
    services="app-service\web" 
    documentationCenter="" 
    authors="naziml" 
    manager="wpickett" 
    editor="jimbe"/>

<tags 
    ms.service="app-service" 
    ms.workload="web" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="08/17/2016" 
    ms.author="naziml"/>    

# <a name="configuring-a-web-application-firewall-waf-for-app-service-environment"></a>Configuration d’un pare-feu d’Application Web (WAF) pour un environnement de Service d’application

## <a name="overview"></a>Vue d’ensemble ##
Web pare-feu application comme la [WAF Barracuda pour Azure](https://www.barracuda.com/programs/azure) disponible dans l’aide [d’Azure Marketplace](https://azure.microsoft.com/marketplace/partners/barracudanetworks/waf-byol/) sécurisé que vos applications web en inspectant le trafic web pour bloquer injections SQL, l’écriture de script intersites, les logiciels malveillants chargements et application DDoS et autres attaques entrant. Elle examine également les réponses des serveurs web principale pour prévention de perte de données (DLP). Combiné avec l’isolement et la même échelle supplémentaires fournies par les environnements de Service d’application, cela fournit un environnement idéal pour héberger entreprise critique des applications web devant résistance demandes malveillants et volume de trafic élevé.

+[AZURE.INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)] 

## <a name="setup"></a>Programme d’installation ##
Pour ce document, nous allons configurer notre environnement de Service d’application derrière plusieurs charge équilibrée instances de Barracuda WAF afin que seul le trafic à partir de la WAF communiquer avec l’environnement de Service d’application et ne sera pas accessible à partir de la zone DMZ. Nous devons également Azure le trafic Gestionnaire devant notre instances Barracuda WAF à équilibrer les charges sur centres de données Azure et régions. Un diagramme de haut niveau de l’installation ressemblera à l’exemple ci-dessous.

![Architecture][Architecture] 

> Remarque : Avec l’introduction de [ILB prise en charge de l’environnement de Service d’application](app-service-environment-with-internal-load-balancer.md), vous pouvez configurer le ASE pour être inaccessibles à partir de la zone DMZ et n’est disponible que pour le réseau privé. 

## <a name="configuring-your-app-service-environment"></a>Configuration de votre environnement de Service d’application ##
Pour configurer un environnement de Service d’application consultez [notre documentation](app-service-web-how-to-create-an-app-service-environment.md) sur le sujet. Une fois que vous avez un environnement de Service d’application créée, vous pouvez créer des [Applications Web](app-service-web-overview.md), [API applications](../app-service-api/app-service-api-apps-why-best-platform.md) et [Applications Mobile](../app-service-mobile/app-service-mobile-value-prop.md) dans cet environnement toutes protégé derrière la WAF nous configurer dans la section suivante.

## <a name="configuring-your-barracuda-waf-cloud-service"></a>Configuration de votre Service de nuage WAF Barracuda ##
Barracuda possède un [article détaillé](https://campus.barracuda.com/product/webapplicationfirewall/article/WAF/DeployWAFInAzure) sur le déploiement de ses WAF sur une machine virtuelle dans Azure. Mais étant donné que redondance et nous pas introduire un point de défaillance unique, que vous voulez déployer au moins 2 machines virtuelles instance de WAF dans le Service Cloud même lorsqu’ils suivent ces instructions.

### <a name="adding-endpoints-to-cloud-service"></a>Ajout d’une extrémité vers le Cloud Service ###
Une fois que les instances de machine virtuelle WAF 2 ou plus dans votre Service Cloud, vous pouvez utiliser le [portail Azure](https://portal.azure.com/) pour ajouter des points de terminaison HTTP et HTTPS utilisés par votre application comme le montre l’image ci-dessous.

![Configurer le point de terminaison][ConfigureEndpoint]

Si vos applications utilisent des autres points de terminaison, veillez à rajoutez-les dans cette liste. 

### <a name="configuring-barracuda-waf-through-its-management-portal"></a>Configuration Barracuda WAF via le portail de gestion ###
Barracuda WAF utilise TCP Port 8000 pour une configuration via le portail de gestion. Étant donné que nous avons plusieurs instances des ordinateurs WAF virtuels vous devrez répéter les étapes décrites ici pour chaque instance de machine virtuelle. 


> Remarque : Une fois que vous avez fini avec une configuration WAF, supprimez le point de terminaison TCP/8000 de tous vos ordinateurs WAF virtuels pour protéger votre WAF.

Ajouter le point de terminaison gestion comme le montre l’image ci-dessous pour configurer votre WAF Barracuda.

![Ajouter le point de terminaison de gestion][AddManagementEndpoint]
 
Utilisez un navigateur pour accéder au point de terminaison de gestion de votre Service Cloud. Si votre Service Cloud est appelée test.cloudapp.net, vous accédez à ce point de terminaison en accédant à http://test.cloudapp.net:8000. Vous devriez voir une page de connexion comme ci-dessous que vous pouvez vous connecter à l’aide des informations d’identification que vous avez spécifié lors de la phase de configuration de machine virtuelle WAF.

![Page de connexion de gestion][ManagementLoginPage]

Une fois votre connexion vous devriez voir un tableau de bord que celui de l’image ci-dessous qui présentera les statistiques de base sur la protection WAF.

![Tableau de bord de gestion][ManagementDashboard]

Cliquez sur l’onglet Services vous permettent de configurer votre WAF pour les services qu’il protège. Pour plus d’informations sur la configuration de votre WAF Barracuda, vous pouvez consulter [leur documentation](https://techlib.barracuda.com/waf/getstarted1). Dans l’exemple ci-dessous, une application Web Azure servant le trafic sur HTTP et HTTPS a été configurée.

![Ajouter des Services de gestion][ManagementAddServices]

> Remarque : En fonction de la configuration de vos applications et quelles fonctionnalités sont utilisées dans votre environnement de Service d’application, vous devrez transférer le trafic TCP ports différent de 80 et 443, par exemple, si vous avez configuré IP SSL pour une application Web. Pour une liste des ports réseau utilisés dans les environnements de Service d’application, reportez-vous à [la documentation du trafic entrant contrôle](app-service-app-service-environment-control-inbound-traffic.md) Ports réseau section.

## <a name="configuring-microsoft-azure-traffic-manager-optional"></a>Configuration de Microsoft Azure le trafic Manager (facultatif) ##
Si votre application est disponible dans plusieurs régions, vous ne souhaitez pas charger les équilibrer derrière le [Gestionnaire de trafic Azure](../traffic-manager/traffic-manager-overview.md). Pour ce faire, vous pouvez ajouter un point de terminaison dans le [portail classique Azure](https://manage.azure.com) utilisant le nom de Service Cloud pour votre WAF dans le profil le trafic Manager comme le montre l’image ci-dessous. 

![Point de terminaison le trafic Manager][TrafficManagerEndpoint]

Si votre application requiert une authentification, assurez-vous de que disposer d’une ressource qui ne nécessite pas d’authentification pour le Gestionnaire de trafic avec la commande ping pour la disponibilité de votre application. Vous pouvez configurer l’URL dans la section Configurer dans le [portail classique Azure](https://manage.azure.com) comme illustré ci-dessous.

![Configurer le Gestionnaire de trafic][ConfigureTrafficManager]

Pour transférer les ping Manager le trafic de votre WAF à votre application, vous devez le programme d’installation les traductions du site Web sur votre WAF Barracuda pour transférer le trafic vers votre application, comme indiqué dans l’exemple ci-dessous.

![Site Web traductions][WebsiteTranslations]

## <a name="securing-traffic-to-app-service-environment-using-network-security-groups-nsg"></a>Sécurisation du trafic vers un environnement de Service d’application à l’aide de groupes de sécurité réseau (NSG)##
Suivez la [documentation de trafic entrant contrôle](app-service-app-service-environment-control-inbound-traffic.md) pour plus d’informations sur la limitation de trafic à votre environnement de Service d’application à partir de la WAF uniquement à l’aide de l’adresse de votre Service Cloud. Voici un exemple de commande Powershell pour effectuer cette tâche pour le port TCP 80.


    Get-AzureNetworkSecurityGroup -Name "RestrictWestUSAppAccess" | Set-AzureNetworkSecurityRule -Name "ALLOW HTTP Barracuda" -Type Inbound -Priority 201 -Action Allow -SourceAddressPrefix '191.0.0.1'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '80' -Protocol TCP

Remplacez le SourceAddressPrefix avec l’adresse IP virtuelle (VIP) du Service de nuage de votre WAF.

> Remarque : L’adresse VIP de votre Service Cloud changeront lorsque vous supprimez et recréez le Service Cloud. Veillez à mettre à jour l’adresse IP du groupe de ressources réseau une fois que vous le faire. 
 
<!-- IMAGES -->
[Architecture]: ./media/app-service-app-service-environment-web-application-firewall/Architecture.png
[ConfigureEndpoint]: ./media/app-service-app-service-environment-web-application-firewall/ConfigureEndpoint.png
[AddManagementEndpoint]: ./media/app-service-app-service-environment-web-application-firewall/AddManagementEndpoint.png
[ManagementAddServices]: ./media/app-service-app-service-environment-web-application-firewall/ManagementAddServices.png
[ManagementDashboard]: ./media/app-service-app-service-environment-web-application-firewall/ManagementDashboard.png
[ManagementLoginPage]: ./media/app-service-app-service-environment-web-application-firewall/ManagementLoginPage.png
[TrafficManagerEndpoint]: ./media/app-service-app-service-environment-web-application-firewall/TrafficManagerEndpoint.png
[ConfigureTrafficManager]: ./media/app-service-app-service-environment-web-application-firewall/ConfigureTrafficManager.png
[WebsiteTranslations]: ./media/app-service-app-service-environment-web-application-firewall/WebsiteTranslations.png

<properties 
    pageTitle="Détails de la Configuration réseau permettant de travailler avec itinéraire Express" 
    description="Détails de la configuration réseau pour les environnements de Service d’application en cours d’exécution dans un réseau virtuel connecté à un ExpressRoute Circuit." 
    services="app-service" 
    documentationCenter="" 
    authors="stefsch" 
    manager="nirma" 
    editor=""/>

<tags 
    ms.service="app-service" 
    ms.workload="na" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/14/2016" 
    ms.author="stefsch"/>   

# <a name="network-configuration-details-for-app-service-environments-with-expressroute"></a>Détails de la Configuration réseau pour les environnements de Service d’application avec ExpressRoute 

## <a name="overview"></a>Vue d’ensemble ##
Les clients peuvent connecter un [Azure ExpressRoute] [ ExpressRoute] circuit à leur infrastructure réseau virtuel, étendant ainsi leur réseau local vers Azure.  Un environnement de Service d’application peuvent être créé dans un sous réseau de ce [réseau virtuel] [ virtualnetwork] infrastructure.  Applications en cours d’exécution sur l’environnement de Service d’application peuvent puis établir une connexion sécurisée aux ressources principale accessibles uniquement sur la connexion ExpressRoute.  

Un environnement de Service d’application peut être créé dans **soit** un réseau virtuel Azure le Gestionnaire de ressources, **ou** un déploiement classique de modèle de réseau virtuel.  Avec une modification récente apportée dans juin 2016, ASEs peuvent également désormais être déployées dans les réseaux virtuels qui utilisent les plages d’adresses public, ou les espaces d’adressage RFC1918 (c'est-à-dire adresses privées). 

[AZURE.INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)] 

## <a name="required-network-connectivity"></a>Connectivité réseau requis ##
Il existe des exigences de connectivité réseau pour les environnements de Service d’application qui ne peuvent pas être satisfaites initiale dans un réseau virtuel connecté à un ExpressRoute.  Environnements de Service d’application nécessitent toutes les conditions suivantes pour fonctionner correctement :


-  Connectivité réseau sortant pour le stockage Azure points de terminaison dans le monde entier sur les deux ports 80 et 443.  Cela inclut les points de terminaison situés dans la même région, comme l’environnement de Service d’application, ainsi que les points de terminaison de stockage situé dans **d’autres** régions Azure.  Résoudre les points de terminaison de stockage Azure sous les domaines DNS suivants : *table.core.windows.net*, *blob.core.windows.net*, *queue.core.windows.net* et *file.core.windows.net*.  
-  Connectivité réseau sortant pour le service de fichiers Azure sur le port 445.
-  Connectivité réseau sortant aux points de terminaison de base de données Sql situé dans la même région en tant que l’environnement de Service d’application.  Résoudre les points de terminaison de base de données SQL sous le domaine suivant : *database.windows.net*.  Cette fonctionnalité nécessite d’ouverture d’un accès aux ports 1433, 11000 11999 et 14000 14999.  Pour plus d’informations, consultez [cet article sur l’utilisation du port V12 de base de données Sql](../sql-database/sql-database-develop-direct-route-ports-adonet-v12.md).
-  Connectivité réseau sortant pour les extrémités de plan de gestion des Azure (points de terminaison ASM et processeur).  Cela inclut connectivité sortante *management.core.windows.net* et *management.azure.com*. 
-  Connectivité réseau sortant *ocsp.msocsp.com*, *mscrl.microsoft.com* et *crl.microsoft.com*.  Cela est nécessaire pour prendre en charge la fonctionnalité SSL.
-  La configuration de DNS pour le réseau virtuel doit être capable de résoudre tous les points de terminaison et domaines mentionnées dans les points précédents.  Si ces points de terminaison ne peut pas être résolus, les tentatives de création d’une application Service environnement échoueront et environnements de Service d’application existant seront marqués comme incorrects.
-  Accès sortant sur le port 53 est requis pour la communication avec les serveurs DNS.
-  S’il existe un serveur DNS personnalisé à l’autre extrémité d’une passerelle VPN, le serveur DNS doit être accessible à partir du sous-réseau contenant l’environnement de Service d’application. 
-  Le chemin d’accès réseau sortant ne peut pas traversent les proxys d’entreprise internes, ni qu’il peut être force en tunnel vers local.  Cette opération modifie l’adresse NAT efficace du trafic réseau sortant à partir de l’environnement de Service d’application.  Modification de l’adresse NAT du trafic réseau sortant d’un environnement de Service application va entraîner des problèmes de connectivité à la plupart des points de terminaison répertorié ci-dessus.  Le résultat échecs de création d’une application Service environnement, ainsi que précédemment exact environnements de Service d’application soient marqués comme incorrect.  
-  Accès réseau entrant sur ports requis pour les environnements de Service d’application doivent être autorisés comme décrit dans cet [article][requiredports].

Les exigences DNS peuvent être remplies en garantissant une infrastructure DNS valide est configurée et gérée pour le réseau virtuel.  Si pour une raison quelconque, que la configuration de DNS est modifiée après la création d’un environnement de Service d’application, les développeurs peuvent forcer un environnement de Service d’application pour reprendre la nouvelle configuration de DNS.  Déclencher un redémarrage environnement propagée à l’aide de l’icône « Redémarrer » situé en haut de la carte de gestion d’environnement de Service d’application dans le [portail Azure] [ NewPortal] va entraîner l’environnement reprendre la nouvelle configuration de DNS.

Respecter les exigences d’accès réseau entrant en configurant un [groupe de sécurité réseau] [ NetworkSecurityGroups] sur sous-réseau de l’environnement Service application pour autoriser l’accès requis comme décrit dans cet [article][requiredports].

## <a name="enabling-outbound-network-connectivity-for-an-app-service-environment"></a>L’activation de la connectivité réseau sortant pour un environnement de Service d’application##
Par défaut, un circuit ExpressRoute nouvellement créé publie un itinéraire par défaut qui permet la connectivité Internet sortante.  Avec cette configuration un environnement de Service d’application seront en mesure de vous connecter à d’autres points de terminaison Azure.

Toutefois, une configuration client commune consiste à définir leur propre itinéraire par défaut (0.0.0.0/0) qui force le trafic Internet sortant de flux à la place en local.  Ce flux de trafic sauts toujours environnements de Service d’application, car le trafic sortant est bloqué localement ou NAT le feriez avec un ensemble d’adresses qui ne fonctionnent plus avec différents points de terminaison Azure reconnu.

La solution consiste à définir un (ou plusieurs) itinéraires définis par l’utilisateur (UDRs) sur le sous-réseau qui contient l’environnement de Service d’application.  Un UDR définit itinéraires spécifiques à un sous-réseau qui seront respectées au lieu de l’itinéraire par défaut.

Si possible, il est recommandé d’utiliser la configuration suivante :

- La configuration ExpressRoute annonce 0.0.0.0/0 et par défaut force tunnel tout le trafic sortant en local.
- La UDR appliqué au sous-réseau contenant l’environnement de Service d’application définit 0.0.0.0/0 avec un type de saut suivant d’Internet (un exemple est plu bas dans cet article).

L’effet combiné de ces étapes est que le niveau de sous-réseau UDR sera prioritaire sur ExpressRoute forcé tunnel, afin de garantir l’accès Internet sortant à partir de l’environnement de Service d’application.

> [AZURE.IMPORTANT] Les itinéraires définis dans une UDR **doit** être suffisamment prioritaire sur tous les itinéraires publiés par la configuration ExpressRoute.  L’exemple ci-dessous utilise la plage d’adresses 0.0.0.0/0 large et en tant que tel peut potentiellement être accidentellement remplacée par les annonces de routage à l’aide de plages d’adresses plus spécifiques.
>
>Environnements de Service d’application ne sont pas pris en charge avec des configurations ExpressRoute ce **entre-annoncer itinéraires à partir du chemin homologation public le chemin d’accès homologation privé**.  Configurations ExpressRoute ayant public peering configuré, recevront les annonces de routage de Microsoft pour un large éventail de Microsoft Azure plages d’adresses.  Si ces plages d’adresses sont publiés entre sur le chemin d’accès homologation privé, le résultat final est que tous les paquets réseau sortants à partir de sous-réseau de l’application Service environnement sera mis en tunnel forcer à l’infrastructure réseau local du client.  Ce flux de réseau n’est actuellement pas pris en charge aux environnements de Service d’application.  Une solution à ce problème consiste à arrêter itinéraires entre publicitaires du chemin d’accès homologation public vers le chemin d’accès homologation privé.

Informations générales sur itinéraires définis par l’utilisateur ne sont disponibles dans cette [vue d’ensemble]de[UDROverview].  

Plus d’informations sur la création et en configurant des itinéraires définis par l’utilisateur n’est disponible dans ce [Le Guide][UDRHowTo].

## <a name="example-udr-configuration-for-an-app-service-environment"></a>Exemple de Configuration UDR pour un environnement de Service d’application ##

**Conditions préalables**

1. Installer Powershell Azure à partir de la [page de téléchargements Azure] [ AzureDownloads] (daté juin 2015 ou version ultérieure).  Sous « Outils de ligne de commande » il existe un lien « Installer » sous « Windows Powershell » qui va installer les dernières applets de commande Powershell.

2. Il est conseillé de créer un sous-réseau unique pour une utilisation exclusive par un environnement de Service d’application.  Ainsi que les UDRs appliqués au sous-réseau ne seront ouvre le trafic sortant pour l’environnement de Service d’application.
3. **Important**: ne pas déployer l’environnement de Service d’application jusqu'à **après avoir** suivi les étapes de configuration suivantes.  Ainsi que la connectivité réseau sortant est disponible avant d’essayer de déploiement d’un environnement de Service d’application.

**Étape 1 : Créer une table nommée itinéraire**

L’extrait de code suivant crée une table itinéraire appelée « DirectInternetRouteTable » dans la région Ouest nous Azure :

    New-AzureRouteTable -Name 'DirectInternetRouteTable' -Location uswest

**Étape 2 : Créer un ou plusieurs itinéraires dans la table de routage**

Vous devrez ajouter un ou plusieurs itinéraires à la table de routage pour activer l’accès Internet sortant.  

L’approche recommandée pour configurer l’accès sortant à Internet consiste à définir un itinéraire pour 0.0.0.0/0 comme illustré ci-dessous.
  
    Get-AzureRouteTable -Name 'DirectInternetRouteTable' | Set-AzureRoute -RouteName 'Direct Internet Range 0' -AddressPrefix 0.0.0.0/0 -NextHopType Internet

N’oubliez pas que 0.0.0.0/0 est une plage d’adresses large et en tant que tel sera remplacée par plages d’adresses plus spécifiques publiés par le ExpressRoute.  Pour effectuer une ré-itération la recommandation antérieure, un UDR avec un itinéraire 0.0.0.0/0 doit être utilisé conjointement avec une configuration ExressRoute qui publie uniquement également 0.0.0.0/0. 

Comme alternative, vous pouvez télécharger une liste complète et mise à jour des plages CIDR utilisé par Azure.  Le fichier Xml contenant toutes les plages d’adresses IP Azure est disponible à partir du [Centre de téléchargement Microsoft][DownloadCenterAddressRanges].  

Remarque Bien que ces modification des plages au fil du temps, d'où la nécessité des mises à jour manuelles périodiques aux itinéraires définis par l’utilisateur pour assurer la synchronisation.  En outre, dans la mesure où il existe une limite supérieure par défaut de 100 itinéraires dans une seule UDR, vous devrez « résumé » les plages d’adresses IP Azure pour l’ajuster dans la limite de 100 itinéraire, en gardant à l’esprit que UDR défini itinéraires doivent être plus spécifique que les itinéraires publiés par votre ExpressRoute.  


**Étape 3 : Associer la table de routage au sous-réseau contenant l’environnement de Service d’application**

La dernière étape de configuration consiste à associer la table de routage au sous-réseau où l’environnement de Service d’application sera déployée.  La commande suivante associe le « DirectInternetRouteTable » à « ASESubnet » qui contiendra un environnement de Service d’application.

    Set-AzureSubnetRouteTable -VirtualNetworkName 'YourVirtualNetworkNameHere' -SubnetName 'ASESubnet' -RouteTableName 'DirectInternetRouteTable'


**Étape 4 : Dernières étapes**

Une fois que la table de routage est liée au sous-réseau, il est recommandé pour tout d’abord tester et confirmer l’effet souhaité.  Par exemple, déployer une machine virtuelle dans le sous-réseau et confirmez que :


- Le trafic sortant pour les points de terminaison Azure et non Azure mentionné précédemment dans cet article se **pas** dirige vers le bas le circuit ExpressRoute.  Il est très important de vérifier ce problème, étant donné que si le trafic sortant à partir du sous-réseau est toujours mise en tunnel local, environnement de Service d’application création échouera toujours. 
- Recherches DNS pour les points de terminaison mentionné précédemment sont toutes résout correctement. 

Une fois les étapes ci-dessus sont confirmées, vous devrez supprimer la machine virtuelle, car le sous-réseau doit être « vide » au moment de que la création de l’environnement de Service d’application.
 
Puis commencer la création d’un environnement de Service d’application !

## <a name="getting-started"></a>Prise en main
Tous les articles et comment-du pour les environnements de Service d’application sont disponibles dans le [fichier Lisez-moi pour les environnements de Service d’Application](../app-service/app-service-app-service-environments-readme.md).

Pour commencer avec les environnements de Service d’application, consultez [Introduction à l’environnement de Service d’application][IntroToAppServiceEnvironment]

Pour plus d’informations sur la plateforme Azure Application Service, voir [Service d’application Azure][AzureAppService].

<!-- LINKS -->
[virtualnetwork]: http://azure.microsoft.com/services/virtual-network/
[ExpressRoute]: http://azure.microsoft.com/services/expressroute/
[requiredports]: http://azure.microsoft.com/documentation/articles/app-service-app-service-environment-control-inbound-traffic/
[NetworkSecurityGroups]: http://azure.microsoft.com/documentation/articles/virtual-networks-nsg/
[UDROverview]: http://azure.microsoft.com/documentation/articles/virtual-networks-udr-overview/
[UDRHowTo]: http://azure.microsoft.com/documentation/articles/virtual-networks-udr-how-to/
[HowToCreateAnAppServiceEnvironment]: http://azure.microsoft.com/documentation/articles/app-service-web-how-to-create-an-app-service-environment/
[AzureDownloads]: http://azure.microsoft.com/en-us/downloads/ 
[DownloadCenterAddressRanges]: http://www.microsoft.com/download/details.aspx?id=41653  
[NetworkSecurityGroups]: https://azure.microsoft.com/documentation/articles/virtual-networks-nsg/
[AzureAppService]: http://azure.microsoft.com/documentation/articles/app-service-value-prop-what-is/
[IntroToAppServiceEnvironment]:  http://azure.microsoft.com/documentation/articles/app-service-app-service-environment-intro/
[NewPortal]:  https://portal.azure.com
 

<!-- IMAGES -->

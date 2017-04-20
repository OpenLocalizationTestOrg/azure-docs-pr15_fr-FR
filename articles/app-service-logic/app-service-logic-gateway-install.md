<properties
   pageTitle="Applications logique installer passerelle de données locale | Microsoft Azure"
   description="Informations sur l’installation de la passerelle de données locale à utiliser dans une application logique."
   services="logic-apps"
   documentationCenter=".net,nodejs,java"
   authors="jeffhollan"
   manager="erikre"
   editor=""/>

<tags
   ms.service="logic-apps"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration"
   ms.date="07/05/2016"
   ms.author="jehollan"/>

# <a name="install-the-on-premises-data-gateway-for-logic-apps"></a>Installer la passerelle de données locale pour les applications de logique

## <a name="installation-and-configuration"></a>Installation et Configuration

### <a name="prerequisites"></a>Conditions préalables

Minimum :

* 4.5 .NET framework
* version 64 bits de Windows 7 ou Windows Server 2008 R2 (ou version ultérieure)

Recommandé :

* Processeur core 8
* 8 Go de mémoire
* version 64 bits de Windows 2012 R2 (ou version ultérieure)

Considérations liées :

* Vous ne pouvez pas installer une passerelle sur un contrôleur de domaine.
* Vous ne doivent pas installer une passerelle sur un ordinateur, ce un portable, qui peut être éteint, en veille, ou non connecté à Internet, car la passerelle ne peut pas s’exécutent dans une de ces situations. En outre, les performances de la passerelle peuvent en souffrir via un réseau sans fil.

### <a name="install-a-gateway"></a>Installer une passerelle

Vous pouvez obtenir [programme d’installation de la passerelle de données locale ici](http://go.microsoft.com/fwlink/?LinkID=820931&clcid=0x409).

Spécifier **la passerelle de données locale** en tant que le mode, connectez-vous à l’aide de votre travail ou scolaire compte, puis soit configurer une nouvelle passerelle ou migrer, restaurer ou prendre le contrôle d’une passerelle existante.

* Pour configurer une passerelle, tapez un **nom** et une **clé de récupération**, puis cliquez ou appuyez sur **configurer**.

    Spécifier une clé de récupération qui contient au moins huit caractères et le laisser dans un emplacement sécurisé. Vous devez cette clé si vous souhaitez migrer, restaurer ou prendre le contrôle sa passerelle.

* Pour migrer, restaurer ou prendre le contrôle d’une passerelle existante, fournissent la clé de récupération qui a été spécifiée lors de la création de la passerelle.

### <a name="restart-the-gateway"></a>Redémarrez la passerelle

La passerelle fonctionne comme un service Windows et, à l’instar de tout autre service Windows, vous pouvez démarrer et arrêter de différentes manières. Vous pouvez par exemple, ouvrez une invite de commandes avec des autorisations élevées sur l’ordinateur où la passerelle est en cours d’exécution et puis exécutez une des commandes suivantes :

* Pour arrêter le service, exécutez la commande suivante :

    `net stop PBIEgwService`

* Pour démarrer le service, exécutez la commande suivante :

    `net start PBIEgwService`

### <a name="configure-a-firewall-or-proxy"></a>Configurer un pare-feu ou proxy

Pour plus d’informations sur la façon de fournir des informations de proxy pour votre passerelle, voir [configurer les paramètres proxy](https://powerbi.microsoft.com/en-us/documentation/powerbi-gateway-proxy/).

Vous pouvez vérifier si votre pare-feu ou proxy, peut bloquer les connexions en exécutant la commande suivante à partir d’une invite PowerShell. Cela va tester la connectivité au Service Bus Azure. Ce que teste la connectivité réseau et n’a rien à faire avec le service de serveur nuage ou sur la passerelle. Il est utile pour déterminer si votre ordinateur peut y accéder à internet.

`Test-NetConnection -ComputerName watchdog.servicebus.windows.net -Port 9350`

Les résultats doivent ressembler à cet exemple. Si **TcpTestSucceeded** n’est pas remplie, vous pouvez être bloqué par un pare-feu.

```
ComputerName           : watchdog.servicebus.windows.net
RemoteAddress          : 70.37.104.240
RemotePort             : 5672
InterfaceAlias         : vEthernet (Broadcom NetXtreme Gigabit Ethernet - Virtual Switch)
SourceAddress          : 10.120.60.105
PingSucceeded          : False
PingReplyDetails (RTT) : 0 ms
TcpTestSucceeded       : True
```

Si vous voulez être exhaustive, remplacez les valeurs **nom_ordinateur** et le **Port** à ceux répertoriés sous [configurer les ports](#configure-ports) plus loin dans cette rubrique.

Le pare-feu peut également bloquer les connexions le Bus des services Azure permet aux centres de données Azure. Si c’est le cas, vous souhaiterez d’autorisation (débloquer) toutes les adresses IP pour votre région pour ces centres de données. Vous pouvez obtenir une liste [d’adresses IP Azure ici](https://www.microsoft.com/download/details.aspx?id=41653).

### <a name="configure-ports"></a>Configurer les ports

La passerelle crée une connexion sortante à Bus des services Azure. Il communique sur les ports sortants : TCP 443 (par défaut), 5671, 5672, 9350 via 9354. La passerelle ne nécessite pas de ports entrants.

Découvrez les [solutions hybride](../service-bus-messaging/service-bus-fundamentals-hybrid-solutions.md).

| NOMS DE DOMAINE | PORTS DE SORTIE | DESCRIPTION |
| ----- | ------ | ------ |
| *. analysis.windows.net | 443 | HTTPS |
| *. login.windows.net | 443 | HTTPS |
| *. servicebus.windows.net |5671 5672 | Message avancés Queuing Protocol (AMQP) |
| *. servicebus.windows.net | 443, 9350 9354 | Récepteurs sur Service Bus relais sur TCP (nécessite 443 pour l’acquisition de jeton de contrôle d’accès) |
| *. frontend.clouddatahub.net | 443 | HTTPS |
| *. core.windows.net | 443 | HTTPS |
| Login.microsoftonline.com | 443 | HTTPS |
| *. msftncsi.com | 443 | Permet de tester la connectivité internet si la passerelle n’est pas accessible par le service Power BI. |

Si vous devez livre liste des adresses IP au lieu des domaines, vous pouvez télécharger et utiliser la [liste des plages IP de centre de données Microsoft Azure](https://www.microsoft.com/download/details.aspx?id=41653). Dans certains cas, les connexions de Bus des services Azure sont effectuées avec adresse IP au lieu des noms de domaine complet.

### <a name="sign-in-account"></a>Compte de connexion

Les utilisateurs seront connectez-vous à l’aide de des options un travail compte professionnel ou scolaire. Il s’agit de votre compte d’organisation. Si vous souscrit une offre Office 365 et vous n’avez pas fournir votre adresse de messagerie professionnelle réelle, elle peut ressembler à jeff@contoso.onmicrosoft.com. Votre compte, au sein d’un service cloud, est stocké dans un client dans Azure Active Directory (DAS). Dans la plupart des cas, UPN de votre compte DAS correspondra à l’adresse de messagerie.

### <a name="windows-service-account"></a>Compte de Service Windows

La passerelle de données locale est configurée pour utiliser NT SERVICE\PBIEgwService pour les informations d’identification de connexion de service Windows. Par défaut, il a le droit de journal en tant que service. Il s’agit dans le contexte de l’ordinateur sur lequel vous installez la passerelle.

Ce n’est pas le compte utilisé pour vous connecter à des sources de données locales ou le compte professionnel ou scolaire avec laquelle vous vous connectez aux services cloud.

##<a name="frequently-asked-questions"></a>Forum aux questions

### <a name="general"></a>Général

**Question**: quelles sources de données pris en charge par la passerelle ?<br/>
**Answer**: aujourd'hui, SQL Server.

**Question**: utiliser une passerelle pour les sources de données dans le cloud, tel que SQL Azure ? <br/>
**Answer**: non. Une passerelle se connecte à des sources de données locales uniquement.

**Question**: qu’est appelé le service Windows proprement dit ?<br/>
**Answer**: dans les Services, la passerelle est appelée Service de passerelle entreprise Power BI.

**Question**: existe-t-il toutes les connexions entrantes à la passerelle à partir du cloud ? <br/>
**Answer**: non. La passerelle utilise les connexions sortantes à Bus des services Azure.

**Question**: que se passe-t-il si je bloque les connexions sortantes ? Que dois-je ouvrir ? <br/>
**Answer**: voir les ports et les hôtes qui utilise la passerelle.


**Question**: la passerelle doit être installée sur le même ordinateur que la source de données ? <br/>
**Answer**: non. La passerelle qui se connecte à la source de données en utilisant les informations de connexion qui ont été fournies. Considérez la passerelle comme une application cliente dans ce sens. Elle devra simplement s’y connecter pour le nom du serveur qui ont été fourni.


**Question**: quelle est la latence d’exécution des requêtes à une source de données à partir de la passerelle ? Quelle est la meilleure architecture ? <br/>
**Answer**: pour réduire le délai réseau, installez la passerelle à proximité la source de données que possible. Si vous pouvez installer la passerelle sur la source de données, il permet de minimiser le temps de latence. Vous pouvez également les centres de données. Par exemple, si votre service est à l’aide du centre de données États-Unis Ouest et que vous avez hébergé dans un ordinateur virtuel Azure SQL Server, vous souhaiterez ont ainsi que la machine virtuelle Azure aux États-Unis Ouest. Cela minimiser la latence et éviter des frais de sortie sur l’ordinateur virtuel Azure.


**Question**: existe-t-il des conditions de bande passante réseau ? <br/>
**Answer**: il est recommandé d’avoir bon débit de votre connexion réseau. Chaque environnement est différent, et la quantité de données envoyées affecte les résultats. À l’aide de ExpressRoute peut vous aider à garantir un niveau de débit entre en local et les centres de données Azure.

Vous pouvez utiliser l’application de Test de vitesse Azure un outil tiers pour aider à évaluer quelle est votre débit.


**Question**: la passerelle service Windows peut exécuter avec un compte Azure Active Directory ? <br/>
**Answer**: non. Le service Windows doit avoir un compte Windows valide. Par défaut, il est exécuté avec l’identificateur de sécurité du Service, NT SERVICE\PBIEgwService.


**Question**: comment les résultats sont envoyées sur le cloud ? <br/>
**Answer**: pour ce faire par le biais du Bus des services Azure. Pour plus d’informations, voir comment cela fonctionne.


**Question**: où sont mes informations d’identification stockées ? <br/>
**Answer**: les informations d’identification que vous entrez pour une source de données sont stockées chiffrées dans le service de nuage passerelle. Les informations d’identification sont déchiffrées à la passerelle locale.

### <a name="high-availabilitydisaster-recovery"></a>Disponibilité haute/sinistre

**Question**: existe-t-il des plans d’activation de scénarios de disponibilité avec la passerelle ? <br/>
**Answer**: il s’agit de la feuille de route, mais nous ne disposez pas encore d’une barre de planning.


**Question**: quelles options sont disponibles pour la reprise ? <br/>
**Answer**: vous pouvez utiliser la clé de récupération pour restaurer ou déplacer une passerelle. Lorsque vous installez la passerelle, spécifiez la clé de récupération.


**Question**: quels sont les avantages de la clé de récupération ? <br/>
**Answer**: il permet de migrer ou récupérer vos paramètres de la passerelle après un incident.

### <a name="troubleshooting"></a>Résolution des problèmes

**Question**: où se trouvent les journaux de passerelle ? <br/>
**Answer**: consultez outils plus loin dans cette rubrique.


**Question**: Comment puis-je voir requêtes est envoyée à la source de données en local ? <br/>
**Answer**: vous pouvez activer le suivi de requête, qui inclut les requêtes envoyées. N’oubliez pas de modifier la valeur d’origine terminé lorsque la résolution des problèmes. Quitter requête traçage activé entraînera les journaux supérieure.

Vous pouvez également consulter des outils votre source de données pour les requêtes de suivi. Par exemple, vous pouvez utiliser des événements étendus ou générateur pour SQL Server et Analysis Services.

## <a name="how-the-gateway-works"></a>Fonctionne de la passerelle

Lorsqu’un utilisateur interagit avec un élément qui est connecté à une source de données locale :

1. Le service cloud crée une requête, ainsi que les informations d’identification chiffrées pour la source de données et envoie la requête à la file d’attente de la passerelle à traiter.
1. Le service analyse la requête et envoie la demande au Bus des services Azure.
1. La passerelle de données locale consulte le Bus des services Azure pour les demandes en attente.
1. La passerelle qui obtient la requête, déchiffre les informations d’identification et se connecte aux sources de données avec les informations d’identification.
1. La passerelle envoie la requête à la source de données pour l’exécution.
1. Les résultats sont envoyés à partir de la source de données à la passerelle, puis sur service cloud. Le service utilise ensuite les résultats.

## <a name="troubleshooting"></a>Résolution des problèmes

### <a name="update-to-the-latest-version"></a>Mettre à jour vers la version la plus récente

Un grand nombre de problèmes peut faire apparaître lorsque la version de passerelle est mis à jour.  Il est recommandé pour vous assurer que vous êtes sur la dernière version.  Si vous n’avez pas mis à jour la passerelle pour un mois, ou plus, vous souhaiterez envisagez d’installer la dernière version de la passerelle et voyez si vous pouvez reproduire le problème.

### <a name="error-failed-to-add-user-to-group--2147463168-pbiegwservice-performance-log-users-"></a>Erreur : Impossible d’ajouter un utilisateur au groupe. (Utilisateurs du journal de performances-2147463168 PBIEgwService)

Vous pouvez recevoir cette erreur si vous essayez d’installer la passerelle sur un contrôleur de domaine qui n’est pas pris en charge. Vous devez déployer la passerelle sur un ordinateur qui n’est pas un contrôleur de domaine.

## <a name="tools"></a>Outils

### <a name="collecting-logs-from-the-gateway-configurator"></a>Collecter les journaux à partir de l’outil de configuration de passerelle

Vous pouvez collecter les journaux de plusieurs pour la passerelle. Toujours commencer avec les journaux.

#### <a name="installer-logs"></a>Installer les journaux

`%localappdata%\Temp\Power_BI_Gateway_–Enterprise.log`

#### <a name="configuration-logs"></a>Journaux de configuration

`%localappdata%\Microsoft\Power BI Enterprise Gateway\GatewayConfigurator.log`

#### <a name="enterprise-gateway-service-logs"></a>Journaux de service passerelle Enterprise

`C:\Users\PBIEgwService\AppData\Local\Microsoft\Power BI Enterprise Gateway\EnterpriseGateway.log`

#### <a name="event-logs"></a>Journaux d’événements

Les journaux de passerelle de gestion des données et PowerBIGateway figurent sous **Application et des journaux des Services**.

### <a name="fiddler-trace"></a>Trace Fiddler

[Fiddler](http://www.telerik.com/fiddler) est un outil gratuit de Telerik contrôlant le trafic HTTP.  Vous pouvez voir l’arrière et quatrième Power BI service à partir de l’ordinateur client. Cela peut afficher les erreurs et autres informations liées.

## <a name="next-steps"></a>Étapes suivantes
- [Créer une connexion locale aux applications de logique](app-service-logic-gateway-connection.md)
- [Fonctionnalités d’intégration entreprise](app-service-logic-enterprise-integration-overview.md)
- [Connecteurs d’applications logique](../connectors/apis-list.md)
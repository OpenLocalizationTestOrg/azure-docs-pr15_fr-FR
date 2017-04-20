<properties
   pageTitle="Exemple de DMZ – créer une DMZ pour protéger les applications avec un pare-feu et NSGs | Microsoft Azure"
   description="Créer une DMZ avec un pare-feu et des groupes de sécurité réseau (NSG)"
   services="virtual-network"
   documentationCenter="na"
   authors="tracsman"
   manager="rossort"
   editor=""/>

<tags
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="02/01/2016"
   ms.author="jonor;sivae"/>

# <a name="example-2--build-a-dmz-to-protect-applications-with-a-firewall-and-nsgs"></a>Exemple 2 : créer une DMZ pour protéger les applications avec un pare-feu et NSGs

[Revenir à la Page de pratiques meilleure sécurité limite][HOME]

Cet exemple crée une DMZ avec un pare-feu, les serveurs de quatre fenêtres et les groupes de sécurité réseau. Il va également guider dans chacune des commandes pertinentes pour fournir une bonne compréhension de chaque étape. Il existe également une section scénario le trafic pour fournir une profondeur étape par étape comment le trafic traversent les couches de défense dans la zone DMZ. Enfin, dans les références de section est le code complet et les instructions permettant de créer cet environnement pour tester et expérimenter différents scénarios. 

![Entrant DMZ avec à couloirs et NSG][1]

## <a name="environment-description"></a>Description de l’environnement
Dans cet exemple, il est un abonnement qui inclut les éléments suivants :

- Deux services en nuage : « FrontEnd001 » et « BackEnd001 »
- Un réseau virtuel « CorpNetwork », avec deux sous-réseaux : « FrontEnd » et « Et »
- Un seul groupe de sécurité réseau qui est appliqué à deux sous-réseaux
- Un matériel virtuel réseau, dans cet exemple Barracuda NextGen pare-feu, connecté au sous-réseau Frontend
- Windows Server qui représente un serveur web d’application (« IIS01 »)
- Serveurs deux fenêtres qui représentent application revenir terminer serveurs (« AppVM01 », « AppVM02 »)
- Windows server qui représente un serveur DNS (« DNS01 »)

>[AZURE.NOTE] Bien que cet exemple utilise un pare-feu NextGen Barracuda, bon nombre de l’authentification multifacteur réseau différents peuvent être utilisées dans cet exemple.

Dans la section références ci-dessous présente un script PowerShell qui générera la plupart de l’environnement ci-dessus. Construction du machines virtuelles et des réseaux virtuels, bien que sont effectuées par l’exemple de script, ne sont pas décrites en détail dans ce document.
 
Pour créer l’environnement :

  1.    Enregistrer le fichier xml de configuration réseau inclus dans la section Références (mis à jour avec les noms, l’emplacement et IP adresses pour faire correspondre le scénario donné)
  2.    Mettre à jour les variables d’utilisateur dans le script pour correspondre à l’environnement que le script doit être exécuté sur (abonnements, les noms de service, etc.)
  3.    Exécuter le script dans PowerShell

**Remarque**: la région indiquée dans le script PowerShell doit correspondre à la région indiquée dans le fichier xml de configuration réseau.

Une fois que le script s’exécute correctement les étapes PostScript suivantes peuvent être prises :

1.  Configurer les règles de pare-feu, il est traité dans la section intitulée : les règles de pare-feu.
2.  Vous pouvez également dans la section références sont des deux scripts pour configurer le serveur web et le serveur de l’application avec une application web simple pour permettre le test avec cette configuration DMZ.

La section suivante explique la plupart des instructions scripts par rapport aux groupes de sécurité réseau.

## <a name="network-security-groups-nsg"></a>Groupes de sécurité réseau (NSG)
Dans cet exemple, un groupe NSG est créé et puis chargé avec six règles. 

>[AZURE.TIP] En règle générale, vous devez d’abord créer vos règles « Autoriser » spécifiques et enfin les règles « Refuser » plus génériques. Exigences imposées par la priorité affectée lequel les règles sont évaluées première. Une fois que le trafic a été trouvé s’appliquent à une règle spécifique, aucune règle supplémentaire n’est évaluées. Règles NSG peuvent s’appliquer dans des options dans la direction entrante ou sortante (du point de vue du sous-réseau).

Manière déclarative, les règles suivantes sont générés pour le trafic entrant :

1.  Le trafic DNS interne (port 53) est autorisé
2.  Le trafic RDP (Port3389) à partir d’Internet à n’importe quel ordinateur virtuel est autorisé
3.  Le trafic HTTP (port 80) à partir d’Internet vers l’à couloirs (pare-feu) est autorisé
4.  Tout le trafic (tous les ports) à partir de IIS01 à AppVM1 est autorisé
5.  Tout le trafic (tous les ports) à partir d’Internet à l’ensemble du VNet (les deux sous-réseaux) est refusée
6.  Tout le trafic (tous les ports) à partir du sous-réseau Frontend au sous-réseau principal est refusé

Avec ces règles lié à chaque sous-réseau, si une requête HTTP a été entrante à partir d’Internet vers le serveur web, les deux règles 3 (autoriser) et 5 (refuser) s’appliquent, mais comme règle 3 ne comportant une priorité plus élevée qu’elle s’appliquera et règle 5 vient pas à lire. Par conséquent la requête HTTP serait autorisée au pare-feu. Si ce même trafic a essayé d’accéder au serveur DNS01, règle 5 (refuser) serait le premier à appliquer et le trafic n'aurait pas autorisé à passer au serveur. Règle 6 (refuser) bloque le sous-réseau Frontend de communiquer au sous-réseau principal (à l’exception du trafic autorisé dans les règles 1 et 4), cela protège le réseau principal en cas de modification un intrus découvre l’application web sur le site Web frontal, l’intrus aurait un accès limité au réseau principal « protégé » (uniquement pour les ressources exposées sur le serveur AppVM01).

Il existe une règle sortante par défaut qui autorise l’absence de trafic à internet. Dans cet exemple, nous allons autoriser le trafic sortant et ne pas modifier les règles de trafic sortant. Pour verrouiller le trafic dans les deux directions, le routage définies par l’utilisateur est requise, cela est exploré dans un autre exemple qui peut trouve dans le [document de limite de sécurité principale][HOME].

Les règles NSG présentées ci-dessus sont très semblables aux règles NSG dans [l’exemple 1 : créer une DMZ Simple avec NSGs][Example1]. Consultez la Description NSG dans ce document pour une description détaillée à chaque règle NSG et ses attributs.

## <a name="firewall-rules"></a>Règles de pare-feu
Une gestion des devra être installée sur un PC pour gérer le pare-feu et créer les configurations requises. Consultez le fournisseur de documentation de votre pare-feu (ou à d’autres couloirs) sur la gestion de l’appareil. Le reste de cette section décrit la configuration du pare-feu lui-même, via le client de gestion des fournisseurs (et non le portail Azure ou PowerShell).

Instructions pour le téléchargement du client et la connexion à la Barracuda utilisés dans cet exemple sont accessibles ici : [Barracuda naturel d’administration](https://techlib.barracuda.com/NG61/NGAdmin)

Sur le pare-feu, règles de transfert devra être créé. Cet exemple achemine uniquement le trafic internet vers le pare-feu, puis vers le serveur web entrant, le transfert une de règle NAT est nécessaire. Sur le pare-feu NextGen Barracuda utilisés dans cet exemple, la règle serait une règle NAT de Destination (« l’heure d’été NAT ») pour passer ce trafic.

Pour créer la règle suivante (ou vérifier les règles existantes par défaut), en commençant par le tableau de bord d’administration Barracuda naturel client, accédez à l’onglet configuration, dans la Configuration opérationnelle de section, cliquez sur ensemble de règles. Une grille appelée, « Règles principaux » affiche les règles actifs et désactivés existants sur le pare-feu. Dans le coin supérieur droit de cette grille est un petit, vert « + » bouton, cliquez sur ce bouton pour créer une règle (Remarque : votre pare-feu peut être « verrouillée » pour les modifications, si vous voyez un bouton marqué « Verrouiller » et vous ne pouvez pas créer ou modifier des règles, cliquez sur ce bouton pour « déverrouiller » l’ensemble de règles et autoriser la modification). Si vous souhaitez modifier une règle existante, sélectionnez cette règle, avec le bouton droit et sélectionnez Modifier la règle.

Créer une règle et fournissez un nom, tel que « WebTraffic ». 

L’icône règle NAT de Destination ressemble à ceci : ![Icône de NAT de Destination][2]

La règle se présente comme suit :

![Règle de pare-feu][3]

Ici les entrant adresse qui accède le pare-feu essayez d’atteindre HTTP (ports 80 et 443 pour HTTPS) sera envoyée à l’interface du pare-feu « IP locale DHCP1 » et redirigé vers le serveur Web avec l’adresse IP de 10.0.1.5. Dans la mesure où le trafic est entrant sur le port 80 et accéder au serveur web sur le port 80 ne change port a été nécessaires. Toutefois, la liste cible aurait pu être 10.0.1.5:8080 si notre serveur Web attendu sur le port 8080 traduction et donc le port entrant 80 sur le pare-feu au port entrant 8080 sur le serveur web.

Une méthode de connexion doivent également être signalée, pour la règle de Destination à partir d’Internet, « SNAT dynamique » est la plus appropriée. 

Bien que qu’une seule règle a été créée, il est important que sa priorité est correctement définie. Si dans la grille de toutes les règles sur le pare-feu cette nouvelle règle se trouve dans la partie inférieure (en dessous de la règle « BLOCKALL ») il arrive jamais dans lecture. Vérifiez que la règle nouvellement créée pour le trafic web se trouve au-dessus de la règle BLOCKALL.

Une fois que la règle est créée, doivent être envoyée vers le pare-feu et puis activé, si ce n’est pas terminé la modification de règle ne prend pas effet. Le processus d’activation et de push est décrite dans la section suivante.

## <a name="rule-activation"></a>Activation de la règle
Avec l’ensemble de règles modifié pour ajouter cette règle, l’ensemble de règles doit être téléchargé dans le pare-feu et activé.

![Activation de règles de pare-feu][4]

Dans le coin supérieur droit du client management sont un cluster de boutons. Cliquez sur le bouton « Envoyer des modifications » pour envoyer les règles modifiées pour le pare-feu, puis cliquez sur le bouton « Activer ».

La procédure d’activation de l’ensemble de règles de pare-feu de création de l’environnement cet exemple est terminée. Vous pouvez également les scripts de compilation billet dans la section références peuvent être exécutés pour ajouter une application à cet environnement pour tester l’en dessous de scénarios de trafic.

>[AZURE.IMPORTANT] Il est essentiel de réaliser que vous ne sera pas atteint le serveur web directement. Lorsqu’un navigateur demande une page HTTP FrontEnd001.CloudApp.Net, le point de terminaison HTTP (port 80) passe ce trafic vers le pare-feu pas le serveur web. Le pare-feu puis, en fonction de la règle créé ci-dessus, NAT qui vous demandent au serveur Web.

## <a name="traffic-scenarios"></a>Scénarios de trafic

#### <a name="allowed-web-to-web-server-through-firewall"></a>(Autorisé) Serveur Web sur le Web via le pare-feu
1.  Page Internet utilisateur demandes HTTP à partir de FrontEnd001.CloudApp.Net (Facing Cloud Service Internet)
2.  Service de nuage passe le trafic via le point de terminaison ouvert sur le port 80 à interface locale pare-feu sur 10.0.1.4:80
3.  Sous-réseau Frontend commence le traitement de règle de trafic entrant :
  1.    NSG règle 1 (DNS) ne s’appliquent, atteindre la règle suivante
  2.    NSG règle 2 (RDP) ne s’appliquent, atteindre la règle suivante
  3.    S’applique-t-elle NSG règle 3 (Internet pour le pare-feu), le trafic est traitement des règles autorisés, arrêter
4.  Le trafic accède à une adresse IP interne du pare-feu (10.0.1.4)
5.  Règle de transfert de pare-feu voir il s’agit le trafic de port 80, redirige vers le serveur web IIS01
6.  IIS01 est à l’écoute pour le trafic web, reçoit cette requête et démarre le traitement de la demande
7.  IIS01 vous invite à indiquer le serveur SQL Server sur AppVM01 pour plus d’informations
8.  Aucune règle sortante sur sous-réseau Frontend, le trafic est autorisé
9.  Le sous-réseau principal commence le traitement de règle de trafic entrant :
  1.    NSG règle 1 (DNS) ne s’appliquent, atteindre la règle suivante
  2.    NSG règle 2 (RDP) ne s’appliquent, atteindre la règle suivante
  3.    NSG règle 3 (Internet pare-feu) ne s’appliquent, atteindre la règle suivante
  4.    NSG règle 4 (IIS01 à AppVM01) s’applique, le trafic est autorisé, arrêter le traitement des règles
10. AppVM01 reçoit la requête SQL et répond
11. Étant donné qu’il n’existe aucune règle sortante sur le serveur principal sous-réseau la réponse est autorisée
12. Sous-réseau Frontend commence le traitement de règle de trafic entrant :
  1.    Il n’existe aucune règle NSG qui s’applique à entrant le trafic du sous-réseau principal au sous-réseau Frontend, afin qu’aucune de la NSG appliquer des règles
  2.    La règle de système par défaut autorisant le trafic entre sous-réseaux permet ce trafic afin que le trafic est autorisé.
13. Le serveur IIS reçoit la réponse SQL et termine la réponse HTTP et envoie au demandeur
14. Dans la mesure où il s’agit d’une session NAT du pare-feu, la destination de réponse est (initiale) pour le pare-feu
15. Le pare-feu reçoit la réponse à partir du serveur Web et la transfère à l’utilisateur Internet
16. Étant donné qu’aucune règle sortante sur le sous-réseau Frontend la réponse n’est autorisée, et l’utilisateur Internet reçoit la page web demandée.

#### <a name="allowed-rdp-to-backend"></a>(Autorisé) RDP au serveur principal
1.  Administrateur de serveur sur internet demande session RDP AppVM01 sur BackEnd001.CloudApp.Net:xxxxx où xxxxx est le numéro de port assigné de manière aléatoire pour RDP à AppVM01 (le port assigné se trouve sur le portail Azure ou via PowerShell)
2.  Dans la mesure où le pare-feu est uniquement à l’écoute sur l’adresse FrontEnd001.CloudApp.Net, il n’est pas impliqué avec ce flux de trafic
3.  Serveur principal sous-réseau commence le traitement de règle de trafic entrant :
  1.    NSG règle 1 (DNS) ne s’appliquent, atteindre la règle suivante
  2.    NSG règle 2 (RDP) s’applique, le trafic est traitement des règles autorisés, arrêter
4.  Qu’aucune règle sortante, appliquent des règles par défaut et le trafic de retour est autorisé
5.  Session RDP est activée
6.  AppVM01 vous invite à mot de passe utilisateur

#### <a name="allowed-web-server-dns-lookup-on-dns-server"></a>(Autorisé) Recherche du serveur DNS Web sur serveur DNS
1.  Web serveur, IIS01, besoins un données flux à www.data.gov, mais qu’il faut résoudre l’adresse.
2.  La configuration réseau pour les listes VNet DNS01 (10.0.2.4 sur le serveur principal sous-réseau) en tant que le serveur DNS principal, IIS01 envoie la demande DNS DNS01
3.  Aucune règle sortante sur sous-réseau Frontend, le trafic est autorisé
4.  Serveur principal sous-réseau commence le traitement de règle de trafic entrant :
  1.     S’applique-t-elle NSG règle 1 (DNS), le trafic est traitement des règles autorisés, arrêter
5.  Serveur DNS reçoit la demande
6.  Serveur DNS n’a pas l’adresse mis en cache et vous invite à fournir un serveur DNS racine sur internet
7.  Aucune règle sortante sur sous-réseau principal, le trafic est autorisé
8.  Serveur DNS Internet répond, étant donné que cette session a été démarrée en interne, la réponse est autorisée
9.  Met en cache la réponse et votre serveur DNS répond à la requête initiale en IIS01
10. Aucune règle sortante sur sous-réseau principal, le trafic est autorisé
11. Sous-réseau Frontend commence le traitement de règle de trafic entrant :
  1.    Il n’existe aucune règle NSG qui s’applique à entrant le trafic du sous-réseau principal au sous-réseau Frontend, afin qu’aucune de la NSG appliquer des règles
  2.    La règle de système par défaut autorisant le trafic entre sous-réseaux serait autoriser ce trafic afin que le trafic est autorisé
12. IIS01 reçoit la réponse DNS01

#### <a name="allowed-web-server-access-file-on-appvm01"></a>(Autorisés) Fichier d’access de serveur Web sur AppVM01
1.  IIS01 vous invite à fournir un fichier sur AppVM01
2.  Aucune règle sortante sur sous-réseau Frontend, le trafic est autorisé
3.  Le sous-réseau principal commence le traitement de règle de trafic entrant :
  1.    NSG règle 1 (DNS) ne s’appliquent, atteindre la règle suivante
  2.    NSG règle 2 (RDP) ne s’appliquent, atteindre la règle suivante
  3.    NSG règle 3 (Internet pare-feu) ne s’appliquent, atteindre la règle suivante
  4.    NSG règle 4 (IIS01 à AppVM01) s’applique, le trafic est autorisé, arrêter le traitement des règles
4.  AppVM01 reçoit la demande et y répond par fichier (en supposant que l’accès est autorisé)
5.  Étant donné qu’il n’existe aucune règle sortante sur le serveur principal sous-réseau la réponse est autorisée
6.  Sous-réseau Frontend commence le traitement de règle de trafic entrant :
  1.    Il n’existe aucune règle NSG qui s’applique à entrant le trafic du sous-réseau principal au sous-réseau Frontend, afin qu’aucune de la NSG appliquer des règles
  2.    La règle de système par défaut autorisant le trafic entre sous-réseaux permet ce trafic afin que le trafic est autorisé.
7.  Le serveur IIS reçoit le fichier

#### <a name="denied-web-direct-to-web-server"></a>(Refusé) Web directement vers le serveur Web
Étant donné que le serveur Web, IIS01 et le pare-feu sont dans le même Service Cloud qu’ils partagent la même adresse IP qui fait face à public. Ainsi, tout le trafic HTTP serait dirigé vers le pare-feu. Tandis que la demande serait servie avec succès, il ne peut pas accéder directement au serveur Web, il passé, comme prévu, à travers le pare-feu tout d’abord. Consultez le premier scénario dans cette section pour le flux de trafic.

#### <a name="denied-web-to-backend-server"></a>(Refusé) Site Web sur un serveur principal
1.  Utilisateur Internet tente d’accéder à un fichier sur AppVM01 via le service BackEnd001.CloudApp.Net
2.  Étant donné qu’aucun point de terminaison ouvert pour le partage de fichiers, cela n’aurait pas passer le Service Cloud et n’est pas accéder au serveur
3.  Si les points de terminaison étaient ouverts pour une raison quelconque, règle NSG 5 (Internet pour VNet) serait bloquer ce trafic

#### <a name="denied-web-dns-lookup-on-dns-server"></a>(Refusé) Recherche DNS Web sur serveur DNS
1.  Utilisateur Internet tente de rechercher un enregistrement DNS interne sur DNS01 via le service BackEnd001.CloudApp.Net
2.  Étant donné qu’aucun point de terminaison ouvert pour DNS, cela n’aurait pas passer le Service Cloud et n’est pas accéder au serveur
3.  Si les points de terminaison étaient ouverts pour une raison quelconque, règle NSG 5 (Internet pour VNet) serait bloquer ce trafic (Remarque : cette règle 1 (DNS) ne s’appliquent pas pour deux raisons, tout d’abord l’adresse source est internet, cette règle s’applique uniquement à la VNet local comme source, aussi il s’agit une règle d’autorisation, afin qu’il serait jamais refuser le trafic)

#### <a name="denied-web-to-sql-access-through-firewall"></a>(Refusé) Web dans access SQL via le pare-feu
1.  Utilisateur Internet demande des données SQL à partir de FrontEnd001.CloudApp.Net (Facing Cloud Service Internet)
2.  Étant donné qu’aucun point de terminaison ouvert pour SQL, cela ne sera pas considérée le Service Cloud et n’est pas atteindre le pare-feu
3.  Si les points de terminaison étaient ouverts pour une raison quelconque, le sous-réseau Frontend commence le traitement de règle de trafic entrant :
  1.    NSG règle 1 (DNS) ne s’appliquent, atteindre la règle suivante
  2.    NSG règle 2 (RDP) ne s’appliquent, atteindre la règle suivante
  3.    S’applique-t-elle NSG règle 2 (Internet pour le pare-feu), le trafic est traitement des règles autorisés, arrêter
4.  Le trafic accède à une adresse IP interne du pare-feu (10.0.1.4)
5.  Pare-feu a sans les règles de transfert pour SQL et supprime le trafic

## <a name="conclusion"></a>Conclusion
Il s’agit d’une pratique relativement directe de protection de votre application avec un pare-feu et isoler le sous-réseau principal de trafic entrant.

Autres exemples et une vue d’ensemble des limites de sécurité réseau sont accessibles [ici][HOME].

## <a name="references"></a>Références
### <a name="main-script-and-network-config"></a>Script principal et configuration réseau
Enregistrer le Script complet dans un fichier de script PowerShell. Enregistrer la configuration réseau dans un fichier nommé « NetworkConf2.xml ».
Modifiez les variables définies par l’utilisateur selon vos besoins. Exécuter le script, puis suivez les instructions de configuration de règle pare-feu ci-dessus.

#### <a name="full-script"></a>Script complet
Ce script est, selon les variables définies par l’utilisateur :

1.  Se connecter à un abonnement Azure
2.  Créer un nouveau compte de stockage
3.  Créer un nouveau VNet et des deux sous de réseaux, telle que définie dans le fichier de configuration de réseau
4.  Créer des machines virtuelles server 4 windows
5.  Configuration de NSG tels que :
  - Création d’un NSG
  - Remplissage avec des règles
  - Liaison du NSG à sous-réseaux appropriés

Ce script PowerShell doit être exécuté localement sur qu'internet connecté PC ou serveur.

>[AZURE.IMPORTANT] Lorsque ce script est exécuté, il peut être avertissements ou autres messages d’information pop dans PowerShell. Seuls les messages d’erreur en rouge sont raison de vous inquiéter.


    <# 
     .SYNOPSIS
      Example of DMZ and Network Security Groups in an isolated network (Azure only, no hybrid connections)
    
     .DESCRIPTION
      This script will build out a sample DMZ setup containing:
       - A default storage account for VM disks
       - Two new cloud services
       - Two Subnets (FrontEnd and BackEnd subnets)
       - A Network Virtual Appliance (NVA), in this case a Barracuda NextGen Firewall
       - One server on the FrontEnd Subnet (plus the NVA on the FrontEnd subnet)
       - Three Servers on the BackEnd Subnet
       - Network Security Groups to allow/deny traffic patterns as declared
      
      Before running script, ensure the network configuration file is created in
      the directory referenced by $NetworkConfigFile variable (or update the
      variable to reflect the path and file name of the config file being used).
    
     .Notes
      Security requirements are different for each use case and can be addressed in a
      myriad of ways. Please be sure that any sensitive data or applications are behind
      the appropriate layer(s) of protection. This script serves as an example of some
      of the techniques that can be used, but should not be used for all scenarios. You
      are responsible to assess your security needs and the appropriate protections
      needed, and then effectively implement those protections.
    
      FrontEnd Service (FrontEnd subnet 10.0.1.0/24)
       myFirewall - 10.0.1.4
       IIS01      - 10.0.1.5
     
      BackEnd Service (BackEnd subnet 10.0.2.0/24)
       DNS01      - 10.0.2.4
       AppVM01    - 10.0.2.5
       AppVM02    - 10.0.2.6
    
    #>
    
    # Fixed Variables
        $LocalAdminPwd = Read-Host -Prompt "Enter Local Admin Password to be used for all VMs"
        $VMName = @()
        $ServiceName = @()
        $VMFamily = @()
        $img = @()
        $size = @()
        $SubnetName = @()
        $VMIP = @()
    
    # User Defined Global Variables
      # These should be changes to reflect your subscription and services
      # Invalid options will fail in the validation section
    
      # Subscription Access Details
        $subID = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    
      # VM Account, Location, and Storage Details
        $LocalAdmin = "theAdmin"
        $DeploymentLocation = "Central US"
        $StorageAccountName = "vmstore02"
    
      # Service Details
        $FrontEndService = "FrontEnd001"
        $BackEndService = "BackEnd001"
    
      # Network Details
        $VNetName = "CorpNetwork"
        $FESubnet = "FrontEnd"
        $FEPrefix = "10.0.1.0/24"
        $BESubnet = "BackEnd"
        $BEPrefix = "10.0.2.0/24"
        $NetworkConfigFile = "C:\Scripts\NetworkConf2.xml"
    
      # VM Base Disk Image Details
        $SrvImg = Get-AzureVMImage | Where {$_.ImageFamily -match 'Windows Server 2012 R2 Datacenter'} | sort PublishedDate -Descending | Select ImageName -First 1 | ForEach {$_.ImageName}
        $FWImg = Get-AzureVMImage | Where {$_.ImageFamily -match 'Barracuda NextGen Firewall'} | sort PublishedDate -Descending | Select ImageName -First 1 | ForEach {$_.ImageName}
    
      # NSG Details
        $NSGName = "MyVNetSG"
    
    # User Defined VM Specific Config
        # Note: To ensure proper NSG Rule creation later in this script:
        #       - The Web Server must be VM 1
        #       - The AppVM1 Server must be VM 2
        #       - The DNS server must be VM 4
        #
        #       Otherwise the NSG rules in the last section of this
        #       script will need to be changed to match the modified
        #       VM array numbers ($i) so the NSG Rule IP addresses
        #       are aligned to the associated VM IP addresses.
    
        # VM 0 - The Network Virtual Appliance (NVA)
          $VMName += "myFirewall"
          $ServiceName += $FrontEndService
          $VMFamily += "Firewall"
          $img += $FWImg
          $size += "Small"
          $SubnetName += $FESubnet
          $VMIP += "10.0.1.4"
    
        # VM 1 - The Web Server
          $VMName += "IIS01"
          $ServiceName += $FrontEndService
          $VMFamily += "Windows"
          $img += $SrvImg
          $size += "Standard_D3"
          $SubnetName += $FESubnet
          $VMIP += "10.0.1.5"
    
        # VM 2 - The First Appliaction Server
          $VMName += "AppVM01"
          $ServiceName += $BackEndService
          $VMFamily += "Windows"
          $img += $SrvImg
          $size += "Standard_D3"
          $SubnetName += $BESubnet
          $VMIP += "10.0.2.5"
    
        # VM 3 - The Second Appliaction Server
          $VMName += "AppVM02"
          $ServiceName += $BackEndService
          $VMFamily += "Windows"
          $img += $SrvImg
          $size += "Standard_D3"
          $SubnetName += $BESubnet
          $VMIP += "10.0.2.6"
    
        # VM 4 - The DNS Server
          $VMName += "DNS01"
          $ServiceName += $BackEndService
          $VMFamily += "Windows"
          $img += $SrvImg
          $size += "Standard_D3"
          $SubnetName += $BESubnet
          $VMIP += "10.0.2.4"
    
    # ----------------------------- #
    # No User Defined Varibles or   #
    # Configuration past this point #
    # ----------------------------- #
    
      # Get your Azure accounts
        Add-AzureAccount
        Set-AzureSubscription –SubscriptionId $subID -ErrorAction Stop
        Select-AzureSubscription -SubscriptionId $subID -Current -ErrorAction Stop
    
      # Create Storage Account
        If (Test-AzureName -Storage -Name $StorageAccountName) { 
            Write-Host "Fatal Error: This storage account name is already in use, please pick a diffrent name." -ForegroundColor Red
            Return}
        Else {Write-Host "Creating Storage Account" -ForegroundColor Cyan 
              New-AzureStorageAccount -Location $DeploymentLocation -StorageAccountName $StorageAccountName}
    
      # Update Subscription Pointer to New Storage Account
        Write-Host "Updating Subscription Pointer to New Storage Account" -ForegroundColor Cyan 
        Set-AzureSubscription –SubscriptionId $subID -CurrentStorageAccountName $StorageAccountName -ErrorAction Stop
    
    # Validation
    $FatalError = $false
    
    If (-Not (Get-AzureLocation | Where {$_.DisplayName -eq $DeploymentLocation})) {
         Write-Host "This Azure Location was not found or available for use" -ForegroundColor Yellow
         $FatalError = $true}
    
    If (Test-AzureName -Service -Name $FrontEndService) { 
        Write-Host "The FrontEndService service name is already in use, please pick a different service name." -ForegroundColor Yellow
        $FatalError = $true}
    Else { Write-Host "The FrontEndService service name is valid for use." -ForegroundColor Green}
    
    If (Test-AzureName -Service -Name $BackEndService) { 
        Write-Host "The BackEndService service name is already in use, please pick a different service name." -ForegroundColor Yellow
        $FatalError = $true}
    Else { Write-Host "The BackEndService service name is valid for use." -ForegroundColor Green}
    
    If (-Not (Test-Path $NetworkConfigFile)) { 
        Write-Host 'The network config file was not found, please update the $NetworkConfigFile variable to point to the network config xml file.' -ForegroundColor Yellow
        $FatalError = $true}
    Else { Write-Host "The network config file was found" -ForegroundColor Green
            If (-Not (Select-String -Pattern $DeploymentLocation -Path $NetworkConfigFile)) {
                Write-Host 'The deployment location was not found in the network config file, please check the network config file to ensure the $DeploymentLocation varible is correct and the netowrk config file matches.' -ForegroundColor Yellow
                $FatalError = $true}
            Else { Write-Host "The deployment location was found in the network config file." -ForegroundColor Green}}
    
    If ($FatalError) {
        Write-Host "A fatal error has occured, please see the above messages for more information." -ForegroundColor Red
        Return}
    Else { Write-Host "Validation passed, now building the environment." -ForegroundColor Green}
    
    # Create VNET
        Write-Host "Creating VNET" -ForegroundColor Cyan 
        Set-AzureVNetConfig -ConfigurationPath $NetworkConfigFile -ErrorAction Stop
    
    # Create Services
        Write-Host "Creating Services" -ForegroundColor Cyan
        New-AzureService -Location $DeploymentLocation -ServiceName $FrontEndService -ErrorAction Stop
        New-AzureService -Location $DeploymentLocation -ServiceName $BackEndService -ErrorAction Stop
    
    # Build VMs
        $i=0
        $VMName | Foreach {
            Write-Host "Building $($VMName[$i])" -ForegroundColor Cyan
            If ($VMFamily[$i] -eq "Firewall") 
                { 
                New-AzureVMConfig -Name $VMName[$i] -ImageName $img[$i] –InstanceSize $size[$i] | `
                    Add-AzureProvisioningConfig -Linux -LinuxUser $LocalAdmin -Password $LocalAdminPwd  | `
                    Set-AzureSubnet  –SubnetNames $SubnetName[$i] | `
                    Set-AzureStaticVNetIP -IPAddress $VMIP[$i] | `
                    New-AzureVM –ServiceName $ServiceName[$i] -VNetName $VNetName -Location $DeploymentLocation
                # Set up all the EndPoints we'll need once we're up and running
                # Note: Web traffic goes through the firewall, so we'll need to set up a HTTP endpoint.
                #       Also, the firewall will be redirecting web traffic to a new IP and Port in a
                #       forwarding rule, so the HTTP endpoint here will have the same public and local
                #       port and the firewall will do the NATing and redirection as declared in the
                #       firewall rule.
                Add-AzureEndpoint -Name "MgmtPort1" -Protocol tcp -PublicPort 801  -LocalPort 801  -VM (Get-AzureVM -ServiceName $ServiceName[$i] -Name $VMName[$i]) | Update-AzureVM
                Add-AzureEndpoint -Name "MgmtPort2" -Protocol tcp -PublicPort 807  -LocalPort 807  -VM (Get-AzureVM -ServiceName $ServiceName[$i] -Name $VMName[$i]) | Update-AzureVM
                Add-AzureEndpoint -Name "HTTP"      -Protocol tcp -PublicPort 80   -LocalPort 80   -VM (Get-AzureVM -ServiceName $ServiceName[$i] -Name $VMName[$i]) | Update-AzureVM
                # Note: A SSH endpoint is automatically created on port 22 when the appliance is created.
                }
            Else
                {
                New-AzureVMConfig -Name $VMName[$i] -ImageName $img[$i] –InstanceSize $size[$i] | `
                    Add-AzureProvisioningConfig -Windows -AdminUsername $LocalAdmin -Password $LocalAdminPwd  | `
                    Set-AzureSubnet  –SubnetNames $SubnetName[$i] | `
                    Set-AzureStaticVNetIP -IPAddress $VMIP[$i] | `
                    Set-AzureVMMicrosoftAntimalwareExtension -AntimalwareConfiguration '{"AntimalwareEnabled" : true}' | `
                    Remove-AzureEndpoint -Name "PowerShell" | `
                    New-AzureVM –ServiceName $ServiceName[$i] -VNetName $VNetName -Location $DeploymentLocation
                    # Note: A Remote Desktop endpoint is automatically created when each VM is created.
                }
            $i++
        }
    
    # Configure NSG
        Write-Host "Configuring the Network Security Group (NSG)" -ForegroundColor Cyan
        
      # Build the NSG
        Write-Host "Building the NSG" -ForegroundColor Cyan
        New-AzureNetworkSecurityGroup -Name $NSGName -Location $DeploymentLocation -Label "Security group for $VNetName subnets in $DeploymentLocation"
    
      # Add NSG Rules
        Write-Host "Writing rules into the NSG" -ForegroundColor Cyan
        Get-AzureNetworkSecurityGroup -Name $NSGName | Set-AzureNetworkSecurityRule -Name "Enable Internal DNS" -Type Inbound -Priority 100 -Action Allow `
            -SourceAddressPrefix VIRTUAL_NETWORK -SourcePortRange '*' `
            -DestinationAddressPrefix $VMIP[4] -DestinationPortRange '53' `
            -Protocol *
    
        Get-AzureNetworkSecurityGroup -Name $NSGName | Set-AzureNetworkSecurityRule -Name "Enable RDP to $VNetName VNet" -Type Inbound -Priority 110 -Action Allow `
            -SourceAddressPrefix INTERNET -SourcePortRange '*' `
            -DestinationAddressPrefix VIRTUAL_NETWORK -DestinationPortRange '3389' `
            -Protocol *
    
        Get-AzureNetworkSecurityGroup -Name $NSGName | Set-AzureNetworkSecurityRule -Name "Enable Internet to $($VMName[0])" -Type Inbound -Priority 120 -Action Allow `
            -SourceAddressPrefix Internet -SourcePortRange '*' `
            -DestinationAddressPrefix $VMIP[0] -DestinationPortRange '*' `
            -Protocol *
    
        Get-AzureNetworkSecurityGroup -Name $NSGName | Set-AzureNetworkSecurityRule -Name "Enable $($VMName[1]) to $($VMName[2])" -Type Inbound -Priority 130 -Action Allow `
            -SourceAddressPrefix $VMIP[1] -SourcePortRange '*' `
            -DestinationAddressPrefix $VMIP[2] -DestinationPortRange '*' `
            -Protocol *
        
        Get-AzureNetworkSecurityGroup -Name $NSGName | Set-AzureNetworkSecurityRule -Name "Isolate the $VNetName VNet from the Internet" -Type Inbound -Priority 140 -Action Deny `
            -SourceAddressPrefix INTERNET -SourcePortRange '*' `
            -DestinationAddressPrefix VIRTUAL_NETWORK -DestinationPortRange '*' `
            -Protocol *
    
        Get-AzureNetworkSecurityGroup -Name $NSGName | Set-AzureNetworkSecurityRule -Name "Isolate the $FESubnet subnet from the $BESubnet subnet" -Type Inbound -Priority 150 -Action Deny `
            -SourceAddressPrefix $FEPrefix -SourcePortRange '*' `
            -DestinationAddressPrefix $BEPrefix -DestinationPortRange '*' `
            -Protocol *
    
        # Assign the NSG to the Subnets
            Write-Host "Binding the NSG to both subnets" -ForegroundColor Cyan
            Set-AzureNetworkSecurityGroupToSubnet -Name $NSGName -SubnetName $FESubnet -VirtualNetworkName $VNetName
            Set-AzureNetworkSecurityGroupToSubnet -Name $NSGName -SubnetName $BESubnet -VirtualNetworkName $VNetName
    
    # Optional Post-script Manual Configuration
      # Configure Firewall
      # Install Test Web App (Run Post-Build Script on the IIS Server)
      # Install Backend resource (Run Post-Build Script on the AppVM01)
      Write-Host
      Write-Host "Build Complete!" -ForegroundColor Green
      Write-Host
      Write-Host "Optional Post-script Manual Configuration Steps" -ForegroundColor Gray
      Write-Host " - Configure Firewall" -ForegroundColor Gray
      Write-Host " - Install Test Web App (Run Post-Build Script on the IIS Server)" -ForegroundColor Gray
      Write-Host " - Install Backend resource (Run Post-Build Script on the AppVM01)" -ForegroundColor Gray
      Write-Host


#### <a name="network-config-file"></a>Fichier de configuration du réseau
Enregistrez ce fichier xml avec l’emplacement mis à jour et ajouter le lien vers ce fichier à la variable $NetworkConfigFile dans le script ci-dessus.
    
    <NetworkConfiguration xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/ServiceHosting/2011/07/NetworkConfiguration">
      <VirtualNetworkConfiguration>
        <Dns>
          <DnsServers>
            <DnsServer name="DNS01" IPAddress="10.0.2.4" />
            <DnsServer name="Level3" IPAddress="209.244.0.3" />
          </DnsServers>
        </Dns>
        <VirtualNetworkSites>
          <VirtualNetworkSite name="CorpNetwork" Location="Central US">
            <AddressSpace>
              <AddressPrefix>10.0.0.0/16</AddressPrefix>
            </AddressSpace>
            <Subnets>
              <Subnet name="FrontEnd">
                <AddressPrefix>10.0.1.0/24</AddressPrefix>
              </Subnet>
              <Subnet name="BackEnd">
                <AddressPrefix>10.0.2.0/24</AddressPrefix>
              </Subnet>
            </Subnets>
            <DnsServersRef>
              <DnsServerRef name="DNS01" />
              <DnsServerRef name="Level3" />
            </DnsServersRef>
          </VirtualNetworkSite>
        </VirtualNetworkSites>
      </VirtualNetworkConfiguration>
    </NetworkConfiguration>

#### <a name="sample-application-scripts"></a>Exemples de Scripts Application
Si vous voulez installer un exemple d’application pour cela et d’autres exemples DMZ, un a été fourni à l’adresse suivante : [Exemple de Script Application][SampleApp]

<!--Image References-->
[1]: ./media/virtual-networks-dmz-nsg-fw-asm/example2design.png "Entrant DMZ avec NSG"
[2]: ./media/virtual-networks-dmz-nsg-fw-asm/dstnaticon.png "Icône NAT de destination"
[3]: ./media/virtual-networks-dmz-nsg-fw-asm/firewallrule.png "Règle de pare-feu"
[4]: ./media/virtual-networks-dmz-nsg-fw-asm/firewallruleactivate.png "Activation de règles de pare-feu"

<!--Link References-->
[HOME]: ../best-practices-network-security.md
[SampleApp]: ./virtual-networks-sample-app.md
[Example1]: ./virtual-networks-dmz-nsg-asm.md

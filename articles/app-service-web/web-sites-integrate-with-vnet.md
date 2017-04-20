<properties 
    pageTitle="Intégrer une application à un réseau virtuel Azure" 
    description="Vous montre comment établir une connexion à un réseau virtuel Azure existant ou nouvel une application dans le Service d’application Azure" 
    services="app-service" 
    documentationCenter="" 
    authors="ccompy" 
    manager="wpickett" 
    editor="cephalin"/>

<tags 
    ms.service="app-service" 
    ms.workload="na" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="08/11/2016" 
    ms.author="ccompy"/>

# <a name="integrate-your-app-with-an-azure-virtual-network"></a>Intégrer votre application à un réseau virtuel Azure #

Ce document décrit la fonctionnalité d’intégration réseau virtuel Azure Application Service et explique comment configurer les applications dans le [Service d’application Azure](http://go.microsoft.com/fwlink/?LinkId=529714).  Si vous connaissez mal les réseaux virtuels Azure (VNETs), il s’agit d’une fonctionnalité qui vous permet de placer la plupart de vos ressources Azure dans un réseau acheminables non internet qui vous contrôlez l’accès à.  Ces réseaux peut être liés à vos réseaux site activé à l’aide d’une variété de technologies VPN.  Pour en savoir plus sur les réseaux virtuels Azure commencent avec les informations ici : [Vue d’ensemble du réseau virtuel Azure][VNETOverview].  

Le Service d’application Azure possède deux formes.  

1. Les systèmes clients multiples qui prennent en charge la gamme complète de tarification
1. La fonctionnalité de premium application Service environnement (ASE) qui déploie dans votre VNET.  

Ce document parcourt VNET intégration et pas environnement de Service d’application.  Si vous voulez en savoir plus sur la fonctionnalité ASE puis commencez avec les informations ici : [Présentation de l’environnement de Service d’application][ASEintro].

Intégration VNET permet d’accéder application web aux ressources dans votre réseau virtuel mais pas accès privé pour votre application web à partir du réseau virtuel.  Accès aux sites privés est disponible uniquement avec une ASE configuré avec un équilibrage de charge interne (ILB).  Pour plus d’informations sur l’utilisation de ASE ILB, commencez par l’article ici : [Création et utilisation ASE ILB][ILBASE]. 

Un scénario courant où vous pouvez utiliser l’intégration VNET est permettant d’accéder à partir de votre application web à une base de données ou un services web s’exécutant sur une machine virtuelle dans votre réseau virtuel Azure.  Avec l’intégration VNET vous n’avez pas besoin exposer un point de terminaison public pour les applications sur votre ordinateur virtuel mais peut utilisent les adresses pouvant être routés internet non privées à la place.  

La fonctionnalité d’intégration de VNET :

- nécessite un Standard ou Premium plan de tarification 
- fonctionnera avec Classic(V1) ou ressource Manager(V2) VNET 
- prend en charge TCP et UDP
- fonctionne avec les applications Web, Mobile et API
- permet à une application pour se connecter à (1) seul VNET à la fois
- permet de 5 VNETs s’intègre dans un Plan de Service d’application 
- permet de la même VNET devant être utilisé par plusieurs applications dans un Plan de Service d’application
- prend en charge un SLA 99,9 % en raison d’une dépendance sur la passerelle VNET

Il existe quelques points VNET intégration ne prenant pas en charge, y compris :

- montage d’un lecteur
- Intégration avec Active Directory 
- NetBios
- accès au site privé

### <a name="getting-started"></a>Prise en main ###
Voici quelques éléments à prendre en compte avant de vous connecter votre application web à un réseau virtuel :

- Intégration de VNET fonctionne uniquement avec les applications dans un **Standard** ou **Premium** plan de tarification.  Si vous activez la fonctionnalité et puis adapter votre Plan de services d’application à un plan de tarification non prises en charge vos applications perdrez leurs connexions aux VNETs qu’ils utilisent.  
- Si votre réseau virtuel cible existe déjà, il doit avoir VPN point-à-site activé avec une passerelle routage dynamique avant qu’il peut être connecté à une application.  Vous ne pouvez pas activer réseau privé virtuel (VPN) point-à-site si votre passerelle est configurée avec un routage statique.
- La VNET doit être placé dans le même abonnement en tant que votre Plan(ASP) de Service d’application.  
- Les applications qui s’intègrent à un VNET utilisera le DNS n’est spécifié pour ce VNET.
- Par défaut vos applications intégrées achemine le trafic dans votre VNET selon les itinéraires définis dans votre VNET.  


## <a name="enabling-vnet-integration"></a>Activer l’intégration VNET ##

Ce document se concentre principalement sur l’utilisation du portail Azure pour l’intégration VNET.  Pour activer l’intégration VNET avec votre application à l’aide de PowerShell, suivez les instructions fournies ici : [se connecter votre application à votre réseau à l’aide de PowerShell virtuel][IntPowershell].

Vous avez la possibilité pour vous connecter votre application à un réseau virtuel nouveau ou existant.  Si vous créez un nouveau réseau dans le cadre de l’intégration puis outre créer simplement le VNET, une passerelle routage dynamique sera préconfigurée pour vous et pointez sur Site VPN est activé.  

>[AZURE.NOTE] Configuration d’une nouvelle intégration réseau virtuel peut prendre plusieurs minutes.  

Pour activer l’intégration VNET ouvrir votre application de paramètres, puis sélectionnez mise en réseau.  L’interface utilisateur qui ouvre propose trois options de mise en réseau.  Ce guide est uniquement être placés dans l’intégration VNET cependant connexions hybride et environnements de Service d’application sont décrites plus loin dans ce document.  

Si votre application n'est pas correctement le prix de plan de l’interface utilisateur lorsque vous permettra à l’échelle de votre plan à un plan de tarification supérieur de votre choix.


![][1]
 
###<a name="enabling-vnet-integration-with-a-pre-existing-vnet"></a>Activer l’intégration VNET avec un VNET existant###
L’interface utilisateur de l’intégration VNET permet de sélectionner dans une liste de vos VNETs.  La VNETs classique indiquera qu’elles sont par exemple avec le mot « Classique » entre parenthèses en regard du nom VNET.  La liste est triée tel que le Gestionnaire de ressources VNETs apparaissent en premier.  Dans l’image ci-dessous, vous pouvez voir que seul VNET pouvant être sélectionné.  Il existe plusieurs raisons qu’un VNET grisé, y compris :

- la VNET se trouve dans un autre abonnement votre compte a accès à
- la VNET n’a pas de Point à Site activé
- le VNET ne dispose pas d’une passerelle routage dynamique


![][2]

Pour activer l’intégration cliquez simplement sur le VNET que vous souhaitez intégrer.  Après avoir sélectionné la VNET, votre application sera automatiquement redémarrée pour que les modifications prennent effet.  

##### <a name="enable-point-to-site-in-a-classic-vnet"></a>Activer le Point de Site dans un VNET classique #####
Si votre VNET ne dispose pas d’une passerelle ni a Point au Site vous devez d’abord définir qui.  Pour effectuer cette opération pour un VNET classique, accédez au [Portail Azure] [ AzurePortal] et accédez à la liste des Networks(classic) virtuel.  Vous voulez intégrer et cliquez sur la zone volumineux sous Essentials appelé connexions VPN dans ici, cliquez sur le réseau.  À partir de là, vous pouvez créer votre point pour le site VPN et même lui demander de créer une passerelle.  Une fois que vous accédez à travers le point au site expérience de création d’une passerelle qu’il sera environ 30 minutes avant qu’il soit prêt.  

![][8]

##### <a name="enabling-point-to-site-in-a-resource-manager-vnet"></a>L’activation de Point de Site dans un VNET le Gestionnaire de ressources #####

Pour configurer un gestionnaire de ressources VNET avec une passerelle, puis pointez sur Site, vous devez utiliser PowerShell comme indiqué ici, [configurer une connexion Point-à-Site à un réseau virtuel à l’aide de PowerShell][V2VNETP2S].  L’interface utilisateur pour effectuer cette fonctionnalité n’est pas encore disponible. 

### <a name="creating-a-pre-configured-vnet"></a>Création d’un VNET préconfiguré ###
Si vous voulez créer un nouveau VNET qui est configuré avec une passerelle et Point-à-Site, le Service d’application réseau à l’interface utilisateur a la possibilité d’effectuer ceci, mais uniquement pour un gestionnaire de ressources VNET.  Si vous souhaitez créer un VNET classique avec une passerelle et Point-à-Site vous devez effectuer cette opération manuellement via l’interface utilisateur de mise en réseau. 

Pour créer un VNET Gestionnaire de ressources via l’interface utilisateur intégration VNET, sélectionnez **Créer nouveau réseau virtuel** et fournissez la :

- Nom du réseau virtuel
- Bloc d’adresse réseau virtuel
- Nom du sous-réseau
- Bloc d’adresse sous-réseau
- Bloc d’adresse passerelle
- Bloc d’adresse de point-à-Site

Si vous souhaitez que cette VNET pour vous connecter à un des autres votre réseau vous devez éviter en sélectionnant espace d’adressage IP qui chevauche ces réseaux.  

>[AZURE.NOTE] Création de VNET Gestionnaire de ressources avec une passerelle prend environ 30 minutes et actuellement n'est pas intégrer la VNET avec votre application.  Une fois que votre VNET est créé avec la passerelle, que vous devez y revenir à votre application de l’interface utilisateur de l’intégration VNET et sélectionnez votre nouveau VNET.

![][3]

En règle générale, VNETs Azure sont créés dans les adresses de réseau privé.  Par défaut l’intégration VNET fonctionnalité achemine le trafic destiné à ces plages d’adresses IP dans votre VNET.  Les plages d’adresses IP privées sont :

- 10.0.0.0/8 - cela est différente de celle 10.0.0.0 - 10.255.255.255
- 172.16.0.0/12 - cela est différente de celle 172.16.0.0 - 172.31.255.255 
- 192.168.0.0/16 - cela est différente de celle 192.168.0.0 - 192.168.255.255
 
L’espace d’adressage VNET doit être indiquée dans la notation CIDR.  Si vous n’êtes pas familiarisé avec la notation CIDR, il est une méthode permettant de spécifier des blocs d’adresses à l’aide d’une adresse IP et un nombre entier qui représente le masque de réseau. Un aide-mémoire, prenons que 10.1.0.0/24 serait les 256 premières adresses et 10.1.0.0/25 serait 128 adresses.  Une adresse IPv4 avec un /32 serait simplement 1 adresse.  

Si vous définissez les informations du serveur DNS ici qui sera défini pour votre VNET.  Après la création de VNET, vous pouvez modifier ces informations à partir de l’expérience utilisateur VNET.

Lorsque vous créez un VNET classique à l’aide de l’interface utilisateur de l’intégration VNET, il crée un VNET dans le même groupe de ressources en tant que votre application. 

## <a name="how-the-system-works"></a>Comment fonctionne le système ##
Coulisses cette fonctionnalité crée en haut de la technologie Point-à-Site VPN pour vous connecter votre application à votre VNET.   Applications dans le Service d’application Azure ont une architecture de système client à plusieurs ce qui empêche la mise en service d’une application directement dans un VNET comme traité avec des machines virtuelles.  En s’appuyant sur la technologie point-à-site nous limiter l’accès réseau à simplement la machine virtuelle qui héberge l’application.  Accès au réseau est limité davantage sur ces hôtes application afin que vos applications puissent accéder uniquement les réseaux que vous configurez les pour accéder à.  

![][4]
 
Si vous n’avez pas configuré un serveur DNS avec votre réseau virtuel, que vous devrez utiliser des adresses IP.  Lors de l’utilisation des adresses IP, n’oubliez pas que le principal avantage de cette fonctionnalité est qu’elle vous permet d’utiliser les adresses privés au sein de votre réseau privé.  Si vous définissez votre application de manière à utiliser des adresses IP publiques pour un de vos ordinateurs virtuels vous n’utilisez pas la fonctionnalité d’intégration VNET et communiquez via internet.


##<a name="managing-the-vnet-integrations"></a>Gérer les intégrations VNET##

La possibilité de se connecter et se déconnecter pour une VNET est au niveau de l’application.  Opérations qui peuvent affecter l’intégration VNET entre plusieurs applications sont au niveau de l’ASP.  À partir de l’interface utilisateur qui s’affiche au niveau de l’application que vous pouvez obtenir des détails sur votre VNET.  La plupart des mêmes informations s’affiche au niveau du ASP.  

![][5]

Dans la page d’état de la fonctionnalité réseau, vous pouvez voir si votre application est connectée à votre VNET.  Si votre passerelle VNET est vers le bas pour raison quelconque puis cela affichait comme n’est pas connecté.  

Les informations que vous avez à présent à votre disposition dans l’application de que l’interface utilisateur de l’intégration VNET niveau est le même que les informations détaillées que vous accéder à partir de la page ASP.  Voici les éléments :

- VNET Name : ce lien ouvre l’interface utilisateur réseau
- Emplacement - Ceci correspond à l’emplacement de votre VNET.  Il est possible d’intégrer un VNET dans un autre emplacement.
- État du certificat - sont les certificats utilisés pour sécuriser la connexion VPN entre l’application et la VNET.  Cela reflète un test pour vérifier qu’ils sont synchronisés.
- État de la passerelle - vos passerelles convient vers le bas pour une raison quelconque votre application ne peut pas accéder aux ressources dans la VNET.  
- Espace d’adressage VNET - il s’agit de l’espace d’adressage IP pour votre VNET.  
- Pointez sur l’espace d’adressage Site - c’est le point à l’espace d’adressage IP site pour votre VNET.  Votre application vous montrent communication comme provenant d’une des adresses IP dans cet espace d’adressage.  
- Site espace d’adressage site - VPN Site à Site permet de connecter votre VNET à vos ressources site activé ou à d’autres VNETs.  Si vous avez configurées puis les plages d’adresses IP défini à l’aide que connexion VPN s’affichent ici.
- Serveurs DNS - si vous avez des serveurs DNS configurés avec votre VNET puis ils sont répertoriés ici.
- Adresses IP routé vers la VNET - cet emplacement sont une liste d’adresses IP que votre VNET a défini pour le routage.  Ces adresses seront affichent ici.  

La seule opération que vous pouvez prendre dans la vue de l’application de l’intégration VNET consiste à déconnecter le VNET il est connecté à votre application.  Pour faire cela cliquez simplement sur se déconnecter en haut.  Cette action ne modifie pas votre VNET.  La VNET et sa configuration, y compris les passerelles ne change pas.  Si vous souhaitez supprimer votre VNET vous devez tout d’abord supprimer les ressources qu’il contient, y compris les passerelles.  

Le mode Plan de Service d’application comporte un nombre d’opérations supplémentaires.  Il est également accessible différemment qu’à partir de l’application.  Pour accéder à l’interface utilisateur de mise en réseau ASP suffit d’ouvrir votre interface utilisateur ASP et faites défiler vers le bas.  Il existe un élément de l’interface utilisateur appelé état de la fonctionnalité réseau.  Pour plus d’informations secondaires autour de l’intégration VNET donne.  Cliquez sur cette interface utilisateur pour ouvrir l’interface utilisateur de réseau fonctionnalité état.  Si vous cliquez ensuite sur « Cliquez ici pour gérer » vous s’ouvre l’interface utilisateur qui répertorie les intégrations VNET dans cette ASP.

![][6]

L’emplacement de la page ASP est préférable à l’esprit lorsque vous examinez les emplacements des VNETs vous intégrez à.  Lorsque la VNET se trouve dans un autre emplacement vous aurez plus de chances afficher les problèmes de latence.  

Les VNETs intégrées à est un rappel sur combien VNETs vos applications sont intégrées avec cette ASP et combien vous pouvez avoir.  

Pour afficher des détails ajoutés sur chaque VNET, cliquez sur le VNET qui vous intéresse.  Outre les détails qui ont été indiqué précédemment que vous verrez également une liste des applications dans cette ASP qui utilisent ce VNET.  

En ce qui concerne les actions, il existe deux actions principales.  La première est la possibilité d’ajouter des itinéraires qui Attirez le quitter votre application dans votre VNET.  L’action deuxième est la possibilité de synchroniser les informations du réseau et certificats.

![][7]

**Routage** Comme indiqué précédemment les itinéraires définis dans votre VNET sont à quoi sert permettant de diriger le trafic dans votre VNET à partir de votre application.  Il existe certaines utilisations où clients le trafic sortant supplémentaire à partir duquel envoyer une application dans la VNET et leur cette fonctionnalité est fourni ;  Que se passe-t-il pour le trafic une fois que jusqu'à la façon dont le client configure leur VNET.  

**Certificats** L’état du certificat reflète une vérification effectuée par le Service d’application pour valider que les certificats que nous utilisons pour la connexion VPN sont toujours correctes.  Lorsque l’intégration VNET activée, puis s’il s’agit la première intégration à cette VNET à partir de toutes les applications dans cette ASP, il est un requis échange de certificats pour assurer la sécurité de la connexion.  Ainsi que les certificats nous obtenir la configuration de DNS, itinéraires et autres éléments similaires qui décrivent le réseau.
Si les certificats ou les informations du réseau sont modifié vous devrez cliquer sur « Réseau de synchronisation ».  **Remarque**: lorsque vous cliquez sur « Synchronisation réseau » puis vous provoquera une brève interruption de connectivité entre votre application et votre VNET.  Alors que votre application ne sera pas redémarrée la perte de connectivité peut provoquer votre site de fonctionner correctement.  

##<a name="accessing-on-premise-resources"></a>Accéder à des ressources de site##

Un des avantages de la fonctionnalité d’intégration VNET est que si votre VNET est connecté à votre réseau de site sur un réseau VPN pour un Site puis vos applications peuvent avoir accès aux ressources de votre site sur à partir de votre application.  Pour cela fonctionne bien que vous devrez peut-être mettre à jour votre passerelle VPN site activé avec les itinéraires pour votre Point de Site IP comprise.  Lorsque le réseau privé virtuel à un Site est configurez d’abord les scripts utilisés pour configurer doivent configurer la route, y compris votre Point de VPN de Site.  Si vous ajoutez le Point à VPN de Site après votre créer votre réseau privé virtuel à un Site puis vous devez mettre à jour manuellement les itinéraires.  Plus d’informations sur la procédure à suivre varient selon le passerelle et ne sont pas décrites ici.  

>[AZURE.NOTE] Bien que la fonctionnalité d’intégration VNET fonctionnent avec un réseau privé virtuel à un Site pour accéder aux ressources site il actuellement ne fonctionne avec un VPN ExpressRoute pour faire la même chose.  C’est vrai lors de l’intégration avec un classique ou le Gestionnaire de ressources VNET.  Si vous avez besoin accéder aux ressources via un VPN ExpressRoute vous pouvez utiliser un ASE pouvant s’exécuter dans votre VNET. 

##<a name="pricing-details"></a>Pour plus d’informations##
Il existe quelques tarifs nuances que vous devez tenir compte des lors de l’utilisation de la fonctionnalité d’intégration VNET.  Il existe 3 frais associés à l’utilisation de cette fonctionnalité :

- ASP tarifs configuration de la couche
- Frais de transfert de données
- Coûts de passerelle VPN.

Pour les applications pouvoir utiliser cette fonctionnalité, ils doivent être dans un Standard ou le Plan de Service d’application Premium.  Vous trouverez plus d’informations sur ces coûts ici : [Application Service tarifs][ASPricing]. 

En raison de la façon de Point VPN Site sont gérées, vous avez toujours un frais pour les données sortantes via une connexion VNET intégration même si le VNET se trouve dans le même centre de données.  Pour voir quels sont les frais sont un coup de œil ici : [Données transférer tarifs détails][DataPricing].  

Le dernier élément est le coût des passerelles VNET.  Si vous n’avez pas besoin les passerelles pour autre chose tel qu’un Site à VPN vous payez pour les passerelles prendre en charge la fonctionnalité d’intégration VNET.  Il existe plus d’informations sur ces coûts ici : [VPN passerelle tarifs][VNETPricing].  

##<a name="troubleshooting"></a>Résolution des problèmes##

Bien que la fonctionnalité soit facile à configurer qui ne signifie que votre expérience sera problème gratuit.  Si vous rencontrez des problèmes d’accès votre point de terminaison souhaité il sont certains utilitaires que vous pouvez utiliser pour tester la connectivité à partir de la console d’application.  Il existe deux expériences console que vous pouvez utiliser.  Une est à partir de la console Kudu et l’autre est la console que vous pouvez atteindre dans le portail Azure.  Pour accéder à la console Kudu à partir de votre application, accédez à outils -> Kudu.  Il s’agit de la même que si vous passez à [nom du site]. scm.azurewebsites.net.  Une fois qu’ouvert simplement atteindre l’onglet console débogage.  Pour accéder à la console hébergée portail Azure puis à partir de votre application, accédez à outils -> Console.  


####<a name="tools"></a>Outils####

Ping d’Outils, nslookup et tracert ne fonctionnent pas via la console en raison des contraintes de sécurité.  Pour remplir la là void ont été ajoutés deux outils.  Pour tester les fonctionnalités DNS, nous avons ajouté un outil nommé nameresolver.exe.  La syntaxe est la suivante :

    nameresolver.exe hostname [optional: DNS Server]

Vous pouvez utiliser nameresolver pour vérifier les noms d’hôtes qui dépend de votre application.  Ainsi, vous pouvez tester si vous avez rien incorrectement configuré avec votre serveur DNS ou peut-être n’avez pas accès à votre serveur DNS.

L’outil suivante permet de tester la connectivité TCP à une combinaison de port et l’hôte.  Cet outil s’appelle tcpping.exe et la syntaxe est :

    tcpping.exe hostname [optional: port]

Cet outil signale si vous communiquer avec un hôte spécifique et un port mais n’effectue pas la même tâche que vous offre ICMP en tenant compte des commande ping.  La commande ping ICMP vous indique si votre hôte est vers le haut.  Avec tcpping vous découvrez si vous pouvez accéder à un port sur un hôte spécifique.  


####<a name="debugging-access-to-vnet-hosted-resources"></a>Accès aux VNET le débogage hébergé ressources####

Il existe un certain nombre de choses que vous pouvez empêcher votre application de joindre un hôte spécifique et un port.  La plupart du temps il est une des trois actions suivantes :

- **Il existe un pare-feu de la façon**  Si vous avez un pare-feu dans la manière dont vous atteignez le délai d’expiration TCP.  Qui est 21 secondes dans ce cas.  Utilisez l’outil tcpping pour tester la connectivité.  TCP des délais d’expiration peut être dû à des choses derrière un pare-feu mais commencer.  
- **DNS n’est pas accessible**  Le délai d’attente DNS est de 3 secondes par serveur DNS.  Si vous avez 2 serveurs DNS qui est de 6 secondes.  Utiliser nameresolver pour déterminer si votre système DNS fonctionne.  N’oubliez pas que vous ne pouvez pas utiliser nslookup comme qui n’utilise pas le DNS de votre VNET est configuré avec.
- **Plage d’adresses IP P2S non valide** Le point de la plage d’adresses IP site doit se trouver dans les plages d’adresses IP privées RFC 1918 (10.0.0.0-10.255.255.255 / 172.16.0.0-172.31.255.255 / 192.168.0.0-192.168.255.255) si la plage utilise des adresses IP en dehors qui puis éléments ne fonctionnent pas.  

Si ces éléments ne répondent pas votre problème, recherchez d’abord pour les opérations simples, comme :  

- La passerelle s’affiche-t-il comme étant la dans le portail ?
- Certificats affichent comme étant synchronisée ?
- Quelqu'un modifier la configuration du réseau sans faire d’un réseau « synchroniser » dans les ASP concernés ? 

Si votre passerelle est arrêté mettez-la sauvegarder.  Si vos certificats ne sont pas synchronisés accéder au mode ASP de l’intégration VNET, puis appuyez sur « Réseau de synchronisation ».  Si vous soupçonnez qu’une modification apportée à votre configuration VNET a été et il n’était pas synchroniser le feriez avec votre ASP puis accédez à la vue ASP de l’intégration VNET et appuyez sur « Synchronisation réseau » juste en tant que rappel, cela entraîne une brève indisponibilité avec votre connexion VNET et vos applications.  

Si tout cela est prêt vous devez aller un peu plus loin :

- Quels sont les autres applications à l’aide de l’intégration VNET pour atteindre les ressources dans la même VNET ? 
- Accédez à la console d’application et l’utiliser tcpping pour accéder à d’autres ressources dans votre VNET ?  

Si des options ci-dessus sont remplies, l’intégration VNET est correctement et le problème est ailleurs.  Il s’agit de l’endroit où il arrive à être plus complexe, car il n’existe aucun moyen simple pour voir pourquoi vous ne pouvez atteindre un hôte : port.  Voici quelques-unes des causes :

- vous avez un pare-feu vers le haut sur votre hôte empêchent l’accès au port de l’application à partir de votre point de la plage d’adresses IP de site.  Passage sous-réseaux souvent nécessite un accès Public.
- votre hôte cible ne fonctionne plus
- votre application est vers le bas
- vous avez l’adresse IP ou le nom d’hôte incorrect
- votre application est à l’écoute sur un port autre que celui que vous attendiez.  Vous pouvez vérifier ceci en accédant sur cet hôte et en utilisant « netstat - aon » à partir de l’invite cmd.  Quel processus ID est à l’écoute sur le port s’affichent.  
- les groupes de sécurité de votre réseau sont configurés de telle sorte qu’ils empêchent l’accès à votre application hôte et le port à partir de votre point de la plage d’adresses IP de site

N’oubliez pas que vous ne savez pas quel IP dans votre Point à la plage Site IP que votre application utilisera afin que vous avez besoin autoriser l’accès à partir de l’intégralité de la plage.  

Étapes supplémentaires de débogage :

- Connectez-vous à un autre ordinateur virtuel dans votre VNET et tenter d’atteindre votre hôte : port ressource à partir de là.  Il existe certains utilitaires de test ping TCP que vous pouvez utiliser à cet effet ou que vous pouvez même utiliser telnet si besoin d’être.  L’objectif ici est afin de déterminer si la connectivité est il à partir de cet ordinateur virtuel autres. 
- faire apparaître une application sur une autre machine virtuelle et tester l’accès à cet hôte et le port à partir de la console à partir de votre application  
####<a name="on-premise-resources"></a>Sur les ressources de site####
Si votre ne peut pas atteindre ressources local est alors la première chose que vous devez vérifier si vous pouvez joindre une ressource dans votre VNET.  Si qui fonctionne étapes suivantes sont faciles.  À partir d’un ordinateur virtuel dans votre VNET vous devez tenter d’atteindre l’application site activé.  Vous pouvez utiliser telnet ou une commande ping TCP.  Si votre ordinateur virtuel ne peut pas atteigne la ressource de site sur puis assurez-vous tout d’abord votre connexion VPN de Site à fonctionne.  S’il fonctionne, puis cochez les mêmes choses indiqués précédemment ainsi que la configuration de la passerelle site et l’état.  

Maintenant si votre VNET hébergée machine virtuelle peut accéder à votre système de site activé mais votre application ne peut pas la raison pour laquelle est probablement de l’une des opérations suivantes :
- votre parcours ne sont pas configurés avec votre point de plages IP de site dans votre passerelle site activé
- les groupes de sécurité de votre réseau bloquent access pour votre Point de la plage d’adresses IP de Site
- votre pare-feu site activé bloquent le trafic de votre Point à la plage IP du Site
- vous avez un Route(UDR) définies par l’utilisateur dans votre VNET qui empêche votre Point pour le trafic du Site en fonction d’atteindre votre réseau site activé

## <a name="hybrid-connections-and-app-service-environments"></a>Connexions hybride et environnements de Service d’application##
Il existe 3 fonctionnalités qui permettent d’accéder aux ressources VNET hébergé.  Ils sont :

- Intégration VNET
- Connexions hybride
- Environnements de Service d’application

Connexions hybride, vous devez installer un agent de relais appelé la Manager(HCM) connexion hybride dans votre réseau.  La HCM doit être en mesure de vous connecter à Azure ainsi qu’à votre application.  Cette solution est particulièrement correctement à partir d’un réseau à distance tel que votre réseau site activé ou cloud même un autre hébergé réseau car il ne nécessite pas un point de terminaison accessible internet.  La HCM fonctionne uniquement sur Windows et vous pouvez avoir jusqu'à 5 instances en cours d’exécution pour augmenter la disponibilité.  Connexions hybride prend uniquement en charge TCP via et chaque point de terminaison HC doit correspondre à une combinaison de port : hôte spécifique.  

La fonctionnalité de l’application Service environnement vous permet d’exécuter une instance du Service application Azure dans votre VNET.  Cela vous permet de vos applications accéder aux ressources dans votre VNET sans étapes supplémentaires.  Certains des autres avantages d’un environnement de Service d’application est que vous pouvez utiliser 8 travailleurs core dédié avec 14 Go de RAM.  Un autre avantage est que vous pouvez mettre à l’échelle du système pour répondre à vos besoins.  Contrairement aux environnements de multi-clients taille où votre ASP est limitée, dans ASE vous contrôlez combien de ressources que vous voulez accorder au système.  En ce qui concerne le focus réseau de ce document, un des ASE que vous n’avez pas avec l’intégration de VNET vous offre notamment les avantages est qu’il peut travailler avec un ExpressRoute VPN.  

S’il existe que certaines utilisent chevauchement casse, aucune de ces fonctions peuvent remplacer tous les autres.  Savoir quelle fonction à utiliser est liée à vos besoins et comment vous souhaitez l’utiliser.  Par exemple :

- Si vous êtes développeur et que vous souhaitez simplement exécuter un site dans Azure et accéder à la base de données sur le poste de travail sous votre bureau, le plus simple à utiliser est connexions hybride.  
- Si vous êtes une grande entreprise qui souhaite placer un grand nombre de propriétés web dans le public cloud et les gérer dans votre propre réseau, alors que vous voulez accéder à l’environnement de Service d’application.  
- Si vous disposez d’un certain nombre de Service d’application hébergé applications et que vous voulez simplement accéder à des ressources dans votre VNET VNET intégration est la solution idéale.  

Au-delà de cas d’utilisation il existe certaines simplicité liés aspects.  Si votre VNET est déjà connecté à votre réseau site activé, puis en utilisant l’intégration VNET ou un environnement de Service d’application est un moyen facile à utiliser sur les ressources de site.  En revanche, si votre VNET n’est pas connecté à votre réseau site activé, il est beaucoup plus de charge pour configurer un réseau privé virtuel à un site avec votre VNET par rapport à l’installation de la HCM.  

Outre les différences fonctionnelles il existe également tarifs différences.  La fonctionnalité de l’application Service environnement est une prime offre de service offre, mais le meilleur réseau possibilités de configuration en plus des autres fonctionnalités.  Intégration VNET peut être utilisée avec Standard ou Premium ASP et est idéale pour l’utilisation des ressources dans votre VNET à partir du multi-client application Service en toute sécurité.  Connexions hybride dépend actuellement un BizTalk compte qui dispose de niveaux qui commencent gratuit et se progressivement plus cher de prix basée sur le montant que vous avez besoin.  Lorsqu’il s’agit à travailler sur les réseaux de nombreux Cependant, il n’existe aucune autre fonctionnalité comme connexions hybride qui peut vous permettre d’accéder à des ressources de plus de 100 réseaux distincts.    


<!--Image references-->
[1]: ./media/web-sites-integrate-with-vnet/vnetint-upgradeplan.png
[2]: ./media/web-sites-integrate-with-vnet/vnetint-existingvnet.png
[3]: ./media/web-sites-integrate-with-vnet/vnetint-createvnet.png
[4]: ./media/web-sites-integrate-with-vnet/vnetint-howitworks.png
[5]: ./media/web-sites-integrate-with-vnet/vnetint-appmanage.png
[6]: ./media/web-sites-integrate-with-vnet/vnetint-aspmanage.png
[7]: ./media/web-sites-integrate-with-vnet/vnetint-aspmanagedetail.png
[8]: ./media/web-sites-integrate-with-vnet/vnetint-vnetp2s.png

<!--Links-->
[VNETOverview]: http://azure.microsoft.com/documentation/articles/virtual-networks-overview/ 
[AzurePortal]: http://portal.azure.com/
[ASPricing]: http://azure.microsoft.com/pricing/details/app-service/
[VNETPricing]: http://azure.microsoft.com/pricing/details/vpn-gateway/
[DataPricing]: http://azure.microsoft.com/pricing/details/data-transfers/
[V2VNETP2S]: http://azure.microsoft.com/documentation/articles/vpn-gateway-howto-point-to-site-rm-ps/
[IntPowershell]: http://azure.microsoft.com/documentation/articles/app-service-vnet-integration-powershell/
[ASEintro]: http://azure.microsoft.com/documentation/articles/app-service-app-service-environment-intro/
[ILBASE]: http://azure.microsoft.com/documentation/articles/app-service-environment-with-internal-load-balancer/

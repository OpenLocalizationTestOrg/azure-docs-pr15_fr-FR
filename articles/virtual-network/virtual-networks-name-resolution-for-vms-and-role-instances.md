<properties 
   pageTitle="Résolution de machines virtuelles et des Instances de rôles"
   description="Nommez des scénarios de résolution de Azure IaaS, solutions hybride, entre les services en nuage différent, Active Directory et à l’aide de votre serveur DNS "
   services="virtual-network"
   documentationCenter="na"
   authors="GarethBradshawMSFT"
   manager="carmonm"
   editor="tysonn" />
<tags 
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="08/31/2016"
   ms.author="telmos" />

# <a name="name-resolution-for-vms-and-role-instances"></a>Résolution de noms pour machines virtuelles et des Instances de rôles

Selon l’utilisation Azure pour héberger IaaS, PaaS et solutions hybride, vous devrez peut-être autoriser les machines virtuelles et les instances de rôle que vous créez pour communiquer avec eux. Bien que cette communication peut être effectuée à l’aide d’adresses IP, il est plus simple d’utiliser des noms qui peuvent être faciles à retenir et ne changent pas. 

Lorsque les instances de rôle et machines virtuelles hébergés dans Azure ont besoin résoudre les noms de domaine vers les adresses IP internes, ils peuvent utiliser une des deux méthodes suivantes :

- [Résolution de noms fournis par Azure](#azure-provided-name-resolution)

- [Résolution du nom à l’aide de votre serveur DNS](#name-resolution-using-your-own-dns-server) (qui peut rediriger les requêtes vers les serveurs DNS fournies par Azure) 

Le type de résolution de noms que vous utilisez dépend de comment votre machines virtuelles et des instances de rôle doivent communiquer avec eux.

**Le tableau suivant montre les scénarios et des solutions de résolution de nom correspondantes :**

| **Scénario** | **Solution** | **Suffixe** |
|--------------|--------------|----------|
| Résolution de noms entre les instances de rôles ou machines virtuelles situées dans la même service cloud ou réseau virtuel | [Résolution de noms fournis par Azure](#azure-provided-name-resolution)| nom d’hôte ou nom de domaine complet |
| Résolution de noms entre les instances de rôles ou machines virtuelles situés dans différents réseaux virtuels | Gérés par le client serveurs DNS redirection des requêtes entre vnets pour la résolution par Azure (proxy DNS).  consultez [résolution de noms à l’aide de votre serveur DNS](#name-resolution-using-your-own-dns-server)| Nom de domaine complet uniquement |
| Résolution de noms de service et ordinateur local à partir d’instances de rôles ou machines virtuelles dans Azure | Gérés par le client de serveurs DNS (contrôleur de domaine sur site, par exemple, contrôleur de domaine local en lecture seule ou un DNS secondaire synchronisé à l’aide des transferts de zone).  Consultez [résolution de noms à l’aide de votre serveur DNS](#name-resolution-using-your-own-dns-server)|Nom de domaine complet uniquement |
| Résolution de noms d’hôtes Azure à partir d’ordinateurs sur site | Transférer des requêtes à un serveur de proxy DNS gérés par le client dans le vnet correspondante, le serveur proxy transfère des requêtes à Azure pour la résolution. Consultez [résolution de noms à l’aide de votre serveur DNS](#name-resolution-using-your-own-dns-server)| Nom de domaine complet uniquement |
| Inverser le DNS pour les adresses IP internes | [Résolution du nom à l’aide de votre serveur DNS](#name-resolution-using-your-own-dns-server) | n/a |
| Résolution des noms entre machines virtuelles ou des instances de rôle situés dans les services en nuage différent, pas dans un réseau virtuel| Non applicable. Connectivité entre machines virtuelles et des instances de rôles dans les services en nuage différents n’est pas pris en charge en dehors d’un réseau virtuel.| n/a |



## <a name="azure-provided-name-resolution"></a>Résolution de noms fournis par Azure

Ainsi que de résolution de noms DNS publics, Azure fournit la résolution de noms interne pour machines virtuelles et des instances de rôle qui résident dans le même réseau virtuel ou d’un service cloud.  Machines virtuelles/instances dans un service cloud partagent le même suffixe DNS (de sorte que le nom d’hôte seul est suffisant) mais cloud différents réseaux virtuels classique services ayant autre suffixe DNS afin que le nom de domaine complet est nécessaire pour résoudre les noms entre les services en nuage différent.  Dans des réseaux virtuels dans le modèle de déploiement du Gestionnaire de ressources, le suffixe DNS est cohérent sur le réseau virtuel (de sorte que le nom de domaine complet n’est pas nécessaire) et les noms DNS peuvent être affectées aux cartes réseau et machines virtuelles. Bien que la résolution de noms Azure condition ne nécessite pas de configuration, il n’est pas le choix approprié pour tous les scénarios de déploiement, comme indiqué dans le tableau ci-dessus.

> [AZURE.NOTE] Dans le cas des rôles web et de travail, vous pouvez également accéder les adresses IP internes d’instances de rôle en fonction du nombre de nom et l’instance rôle à l’aide de l’API REST de Azure Service gestion. Pour plus d’informations, voir [Référence de l’API REST Service gestion](https://msdn.microsoft.com/library/azure/ee460799.aspx).

### <a name="features-and-considerations"></a>Fonctionnalités et les considérations

**Fonctionnalités :**

- Facilité d’utilisation : aucune configuration est requise pour pouvoir utiliser la résolution de noms Azure condition.

- Le service de résolution de noms Azure condition est hautement disponible, ce qui vous évite la nécessité de créer et gérer des groupes de vos propres serveurs DNS.

- Peut être utilisé conjointement avec vos propres serveurs DNS pour résoudre les noms d’hôtes sur site et Azure.

- Résolution de noms est fournie entre rôle instances/machines virtuelles au sein du même service cloud sans besoin d’un nom de domaine complet.

- Résolution de noms est fournie entre machines virtuelles dans des réseaux virtuels qui utilisent le modèle de déploiement Gestionnaire de ressources, sans avoir besoin du nom de domaine complet. Réseaux virtuels dans le modèle de déploiement classique nécessitent le nom de domaine complet lors de la résolution de noms dans les services en nuage différent. 

- Vous pouvez utiliser des noms d’hôtes qui décrivent le mieux vos déploiements, plutôt que d’utilisation des noms générées automatiquement.

**Considérations relatives à :**

- Le suffixe DNS créés Azure ne peut pas être modifié.

- Vous ne pouvez pas enregistrer manuellement vos propres enregistrements.

- WINS et NetBIOS ne sont pas pris en charge. (Vous ne voyez pas vos ordinateurs virtuels dans l’Explorateur Windows.)

- Noms d’hôtes doit être compatible avec le DNS (ils doivent utiliser uniquement 0 à 9, d’a à z et '-' et ne peut pas commencer ou se terminer par un «- ». Voir la section 3696 RFC 2).

- Le trafic des requêtes DNS est limité pour chaque machine virtuelle. Cela ne doit pas avoir un impact sur la plupart des applications.  Si la limitation de requêtes est observée, vérifiez que la mise en cache côté client est activé.  Pour plus d’informations, voir [prise le meilleur parti de résolution de noms Azure condition](#Getting-the-most-from-Azure-provided-name-resolution).

- Uniquement les ordinateurs virtuels dans les services de 180 cloud premières sont enregistrés pour chaque réseau virtuel dans un modèle de déploiement classique. Cela ne s’applique pas aux réseaux virtuels dans les modèles de déploiement Gestionnaire de ressources.


### <a name="getting-the-most-from-azure-provided-name-resolution"></a>Obtenir le meilleur parti de résolution de noms fournis par Azure
**La mise en cache côté client :**

Pas toutes les requêtes DNS doivent être envoyée sur le réseau.  La mise en cache côté client permet de réduire la latence et améliorer la résilience aux blips réseau en résolvant les requêtes DNS périodiques d’un cache local.  Enregistrements DNS contiennent un Time To Live (TTL) qui permet de stocker l’enregistrement pour autant que possible sans ayant un impact sur l’actualisation enregistrement, afin que la mise en cache côté client est adaptée à la plupart des cas le cache.

La valeur par défaut Client DNS Windows possède un cache DNS intégré.  Certains Linux distros n’incluez pas la mise en cache par défaut, il est recommandé qu’une être ajoutée à chaque Linux VM (après avoir vérifié qu’il n’est pas un cache local déjà).

Il existe un nombre de différents DNS mise en cache packages disponibles, par exemple, dnsmasq, voici la procédure pour installer dnsmasq sur les plus courants distros :

- **Ubuntu (utilise resolvconf)**:
    - Installez le package dnsmasq (« sudo susceptibles get installer dnsmasq »).
- **SUSE (utilise netconf)**:
    - installer le package dnsmasq (« sudo zypper installation dnsmasq ») 
    - Activer le service dnsmasq (« systemctl activer dnsmasq.service ») 
    - Démarrer le service dnsmasq (« systemctl démarrer dnsmasq.service ») 
    - modifier « / etc/sysconfig/réseau/config » et modifier NETCONFIG_DNS_FORWARDER = « » à « dnsmasq »
    - mettre à jour resolv.conf (« netconfig mise à jour ») pour définir le cache en tant que la résolution DNS locale
- **OpenLogic (utilise NetworkManager)**:
    - installer le package dnsmasq (« sudo yum installer dnsmasq »)
    - Activer le service dnsmasq (« systemctl activer dnsmasq.service »)
    - Démarrer le service dnsmasq (« systemctl démarrer dnsmasq.service »)
    - Ajouter « faire précéder les serveurs de noms de domaine 127.0.0.1 ; » à « /etc/dhclient-eth0.conf »
    - Redémarrez le service de réseau (« service réseau redémarrage ») pour définir le cache en tant que la résolution DNS locale

> [AZURE.NOTE] Le package 'dnsmasq' est seul le cache DNS nombreux disponibles pour Linux.  Avant de l’utiliser, veuillez vérifier sa pertinence de vos besoins et que le cache n’est pas installé.

**Nouvelles tentatives côté client :**

DNS est principalement un protocole UDP.  Comme le protocole UDP ne garantit pas la remise des messages, logique des nouvelles tentatives est gérée dans le protocole DNS proprement dit.  Chaque client DNS (système d’exploitation) peut présenter logique des nouvelles tentatives différentes selon la préférence créateurs :

 - Systèmes d’exploitation Windows puis réessayez après 1 deuxième, puis à nouveau un autre 2, 4 et l’autre 4 secondes. 
 - Les tentatives de configuration par défaut Linux après 5 secondes.  Il est recommandé de modifier cette option pour recommencer 5 fois à intervalles deuxième 1.  

Pour vérifier les paramètres actuels sur un Linux VM, « Chat /etc/resolv.conf » et examinez la ligne « options », par exemple :

    options timeout:1 attempts:5

Le fichier resolv.conf est généralement généré automatiquement et ne doit pas être modifié.  Les étapes spécifiques pour l’ajout de la ligne « options » varient selon distro :

- **Ubuntu** (utilise resolvconf) :
    - ajouter la courbe options ' / etc/resolveconf/resolv.conf.d/head' 
    - Exécutez « resolvconf -u » pour mettre à jour
- **SUSE** (utilise netconf) :
    - Ajouter « timeout:1 tentatives : 5 » à la NETCONFIG_DNS_RESOLVER_OPTIONS = » « paramètre en '/ etc/sysconfig/réseau/config' 
    - Exécutez « netconfig mise à jour » pour mettre à jour
- **OpenLogic** (utilise NetworkManager) :
    - Ajouter « écho « options timeout:1 tentatives : 5 » » à ' / etc/NetworkManager/dispatcher.d/11-dhclient' 
    - exécuter « réseau redémarrer le service » pour mettre à jour

## <a name="name-resolution-using-your-own-dns-server"></a>Résolution du nom à l’aide de votre serveur DNS
Il existe un certain nombre de situations dans lesquelles vos besoins de résolution du nom peuvent aller au-delà des fonctionnalités fournies par Azure, par exemple quand à l’aide de domaines Active Directory ou lorsque vous avez besoin de résolution DNS entre les réseaux virtuels (vnets).  Pour couvrir ces scénarios, Azure offre la possibilité d’utiliser vos propres serveurs DNS.  

Serveurs DNS au sein d’un réseau virtuel transférer des requêtes DNS pour les programmes de résolution d’Azure récursive pour résoudre les noms d’hôtes au sein de ce réseau virtuel.  Par exemple, un contrôleur de domaine (DC) en cours d’exécution dans Azure peut répondre aux requêtes DNS de ses domaines et les transférer toutes les autres requêtes vers Azure.  Cela permet de machines virtuelles afficher vos ressources locales (par le biais du contrôleur de domaine) et les noms d’hôtes Azure condition (par le biais du redirecteurs).  Programmes de résolution d’Azure récursive est accessible via l’adresse IP virtuelle 168.63.129.16.

Redirection DNS Active la résolution DNS vnet notamment également et permet de vos ordinateurs locales résoudre les noms d’hôtes Azure condition.  Afin de résoudre le nom d’hôte d’une machine virtuelle, le serveur DNS machine virtuelle doit se trouver dans le même réseau virtuel et être configuré pour les requêtes hostname transférer vers Azure.  Comme le suffixe DNS est différent dans chaque vnet, vous pouvez utiliser les règles de transfert conditionnelle pour envoyer des requêtes DNS pour le vnet correct pour la résolution.  L’image suivante montre deux vnets et un réseau locales effectuant résolution DNS vnet notamment à l’aide de cette méthode.  Un redirecteurs DNS exemple est disponible dans la [Galerie de modèles de démarrage rapide d’Azure](https://azure.microsoft.com/documentation/templates/301-dns-forwarder/) et [GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/301-dns-forwarder).

![Vnet notamment DNS](./media/virtual-networks-name-resolution-for-vms-and-role-instances/inter-vnet-dns.png)

Lors de l’utilisation de résolution de noms Azure fourni, un suffixe DNS interne (*. internal.cloudapp.net) n’est fourni pour chaque machine virtuelle à l’aide de DHCP.  Cela permet de résolution du nom d’hôte comme les enregistrements de nom d’hôte figurent dans la zone internal.cloudapp.net.  Lorsque vous utilisez votre propre solution de résolution de nom, le suffixe les IDN n’est pas fourni aux machines virtuelles parce qu’il interfère avec d’autres architectures de DNS (par exemple, des scénarios à un domaine).  Nous fournissons à la place d’un espace réservé non fonctionnement (reddog.microsoft.com).  

Si nécessaire, le suffixe DNS interne peut être déterminé à l’aide de PowerShell ou l’API :

-  Pour les réseaux virtuels dans les modèles de déploiement Gestionnaire de ressources, le suffixe est disponible via la ressource de [carte réseau](https://msdn.microsoft.com/library/azure/mt163668.aspx) ou via l’applet de commande [Get-AzureRmNetworkInterface](https://msdn.microsoft.com/library/mt619434.aspx) .    
-  Dans les modèles de déploiement classique, le suffixe est disponible via un appel à [l’API de déploiement obtenir](https://msdn.microsoft.com/library/azure/ee460804.aspx) ou via la [Get-AzureVM-déboguer](https://msdn.microsoft.com/library/azure/dn495236.aspx) applet de commande.


Si la redirection des requêtes vers Azure ne selon vos besoins, vous devrez fournir votre propre solution DNS.  Votre solution DNS devez :

-  Fournir la résolution de nom d’hôte approprié, par exemple via [DDNS](virtual-networks-name-resolution-ddns.md).  Note, si vous utilisez DDNS vous devrez peut-être désactiver le nettoyage des enregistrements DNS comme location DHCP d’Azure est très longues et nettoyage risque de supprimer les enregistrements DNS prématurément. 
-  Fournir la résolution récursive appropriée pour autoriser la résolution de noms de domaines externes.
-  Être accessible (TCP et UDP sur le port 53) à partir des clients qu'il sert et pouvoir accéder à internet.
-  Sécurisé contre l’accès à partir d’internet, à limiter les risques exemptés externes.

> [AZURE.NOTE] Pour de meilleures performances, lorsque vous utilisez des machines virtuelles Azure comme serveurs DNS, le protocole IPv6 doit être désactivé et une [Adresse IP publique au niveau de l’Instance](virtual-networks-instance-level-public-ip.md) doivent être affectées à chaque machine virtuelle du serveur DNS.  Si vous choisissez d’utiliser Windows Server que votre serveur DNS, [cet article](http://blogs.technet.com/b/networking/archive/2015/08/19/name-resolution-performance-of-a-recursive-windows-dns-server-2012-r2.aspx) fournit des optimisations et analyse des performances supplémentaires.


### <a name="specifying-dns-servers"></a>Spécification de serveurs DNS

Lorsque vous utilisez vos propres serveurs DNS, Azure fournit la possibilité de spécifier plusieurs serveurs DNS par réseau virtuel ou par interface réseau (Gestionnaire de ressources) / cloud service (standard).  Serveurs DNS spécifiés pour une interface de service/réseau cloud obtiennent priorité sur celles qui sont spécifiées pour le réseau virtuel.

> [AZURE.NOTE] Propriétés de connexion réseau, telles que le serveur DNS adresses IP, ne doit pas être modifié directement à partir de Windows machines virtuelles qu’ils peuvent obtenir supprimées au cours du service de correction lorsque la carte réseau virtuelle obtient remplacée. 


Lorsque vous utilisez le modèle de déploiement du Gestionnaire de ressources, serveurs DNS peuvent être indiquées dans le portail, API/modèles ([vnet](https://msdn.microsoft.com/library/azure/mt163661.aspx), [carte réseau](https://msdn.microsoft.com/library/azure/mt163668.aspx)) ou PowerShell ([vnet](https://msdn.microsoft.com/library/mt603657.aspx), [carte réseau](https://msdn.microsoft.com/library/mt619370.aspx)).

Lorsque vous utilisez le modèle de déploiement classique, serveurs DNS pour le réseau virtuel peuvent être indiquées dans le portail ou [le fichier de *Configuration du réseau* ](https://msdn.microsoft.com/library/azure/jj157100).  Pour les services en nuage, les serveurs DNS sont spécifiés via [le fichier de *Configuration de Service* ](https://msdn.microsoft.com/library/azure/ee758710) ou dans PowerShell ([New AzureVM](https://msdn.microsoft.com/library/azure/dn495254.aspx)).

> [AZURE.NOTE] Si vous modifiez les paramètres DNS pour une machine réseau/virtuel qui est déjà déployé, vous devez redémarrer chaque machine virtuelle concerné pour que les modifications soient prises en compte.


## <a name="next-steps"></a>Étapes suivantes

Modèle de déploiement du Gestionnaire de ressources :

- [Créer ou mettre à jour un réseau virtuel](https://msdn.microsoft.com/library/azure/mt163661.aspx)
- [Créer ou mettre à jour d’une carte réseau](https://msdn.microsoft.com/library/azure/mt163668.aspx)
- [Nouvelle AzureRmVirtualNetwork](https://msdn.microsoft.com/library/mt603657.aspx)
- [Nouvelle AzureRmNetworkInterface](https://msdn.microsoft.com/library/mt619370.aspx)

 
Modèle de déploiement classique :

- [Schéma de Configuration de Service Azure](https://msdn.microsoft.com/library/azure/ee758710)
- [Schéma de Configuration du réseau virtuel](https://msdn.microsoft.com/library/azure/jj157100)
- [Configurer un réseau virtuel à l’aide d’un fichier de Configuration du réseau](virtual-networks-using-network-configuration-file.md) 


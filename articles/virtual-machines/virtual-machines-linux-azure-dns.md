<properties 
   pageTitle="Options de résolution de nom DNS pour les machines virtuelles Linux dans Azure"
   description="Les services DNS de scénarios pour les machines virtuelles Linux dans Azure IaaS, y compris les noms résolution, serveur hybride DNS et de mettre votre propre externes."
   services="virtual-machines"
   documentationCenter="na"
   authors="RicksterCDN"
   manager="timlt"
   editor="tysonn" />
<tags 
   ms.service="virtual-machines-linux"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/19/2016"
   ms.author="rclaus" />

# <a name="dns-name-resolution-options-for-linux-vms-in-azure"></a>Options de résolution de nom DNS pour les machines virtuelles Linux dans Azure

Azure fournit la résolution de noms DNS par défaut pour tous les ordinateurs virtuels contenus dans un seul réseau virtuel. Vous êtes en mesure d’implémenter votre propre solution de résolution du nom DNS en configurant votre propre services DNS sur vos ordinateurs virtuels hébergés Azure. Les scénarios suivants devraient vous aider à choisir celui qui vous convient le mieux selon vos besoins.

- [Résolution de noms fournis par Azure](#azure-provided-name-resolution)

- [Résolution du nom à l’aide de votre serveur DNS](#name-resolution-using-your-own-dns-server) 

Le type de résolution de noms que vous utilisez dépend de comment votre machines virtuelles et des instances de rôle doivent communiquer avec eux.

**Le tableau suivant montre les scénarios et des solutions de résolution de nom correspondantes :**

| **Scénario** | **Solution** | **Suffixe** |
|--------------|--------------|----------|
| Résolution de noms entre les instances de rôles ou machines virtuelles situés dans le même réseau virtuel | [Résolution de noms fournis par Azure](#azure-provided-name-resolution)| nom d’hôte ou nom de domaine complet |
| Résolution de noms entre les instances de rôles ou machines virtuelles situés dans différents réseaux virtuels | Gérés par le client serveurs DNS redirection des requêtes entre vnets pour la résolution par Azure (proxy DNS).  consultez [résolution de noms à l’aide de votre serveur DNS](#name-resolution-using-your-own-dns-server)| Nom de domaine complet uniquement |
| Résolution de noms de service et ordinateur local à partir d’instances de rôles ou machines virtuelles dans Azure | Gérés par le client DNS servers (par exemple, contrôleur de domaine sur site, contrôleur de domaine local en lecture seule ou un DNS secondaire synchronisé à l’aide des transferts de zone).  Consultez [résolution de noms à l’aide de votre serveur DNS](#name-resolution-using-your-own-dns-server)|Nom de domaine complet uniquement |
| Résolution de noms d’hôtes Azure à partir d’ordinateurs sur site | Transférer des requêtes à un serveur de proxy DNS gérés par le client dans le vnet correspondante, le serveur proxy transfère des requêtes à Azure pour la résolution. Consultez [résolution de noms à l’aide de votre serveur DNS](#name-resolution-using-your-own-dns-server)| Nom de domaine complet uniquement |
| Inverser le DNS pour les adresses IP internes | [Résolution du nom à l’aide de votre serveur DNS](#name-resolution-using-your-own-dns-server) | n/a |

## <a name="azure-provided-name-resolution"></a>Résolution de noms fournis par Azure

Ainsi que de résolution de noms DNS publics, Azure fournit la résolution de noms interne de machines virtuelles et des instances de rôle qui résident dans le même réseau virtuel.  Dans processeur ARM réseaux virtuels, le suffixe DNS est cohérent sur le réseau virtuel (de sorte que le nom de domaine complet n’est pas nécessaire) et les noms DNS peuvent être affectées aux cartes réseau et machines virtuelles. Bien que la résolution de noms Azure condition ne nécessite pas de n’importe quelle configuration, il n’est pas le choix approprié pour tous les scénarios de déploiement, comme indiqué dans le tableau ci-dessus.

### <a name="features-and-considerations"></a>Fonctionnalités et les considérations

**Fonctionnalités :**

- Facilité d’utilisation : aucune configuration est requise pour utiliser la résolution du nom Azure fourni.

- Le service de résolution de noms Azure condition est hautement disponible, ce qui vous évite la nécessité de créer et gérer des groupes de vos propres serveurs DNS.

- Peut servir en même temps que vos propres serveurs DNS pour résoudre les noms d’hôtes sur site et Azure.

- Résolution de noms est fournie entre machines virtuelles dans des réseaux virtuels sans avoir besoin du nom de domaine complet. 

- Vous pouvez utiliser des noms d’hôtes qui décrivent le mieux vos déploiements, plutôt que d’utilisation des noms générées automatiquement.

**Considérations relatives à :**

- Le suffixe DNS créés Azure ne peut pas être modifié.

- Vous ne pouvez pas enregistrer manuellement vos propres enregistrements.

- WINS et NetBIOS ne sont pas pris en charge.

- Noms d’hôtes doit être compatible avec le DNS (ils doivent utiliser uniquement 0 à 9, d’a à z et '-' et ne peut pas commencer ou se terminer par un «- ». Voir la section 3696 RFC 2).

- Le trafic des requêtes DNS est limité pour chaque machine virtuelle. Cela ne doit pas avoir un impact sur la plupart des applications.  Si la limitation de requêtes est observée, vérifiez que la mise en cache côté client est activé.  Pour plus d’informations, voir [prise le meilleur parti de résolution de noms Azure condition](#Getting-the-most-from-Azure-provided-name-resolution).


### <a name="getting-the-most-from-azure-provided-name-resolution"></a>Obtenir le meilleur parti de résolution de noms fournis par Azure
**La mise en cache côté client :**

Pas toutes les requêtes DNS sont envoyées sur le réseau.  La mise en cache côté client permet de réduire la latence et améliorer la résilience aux blips réseau en résolvant les requêtes DNS périodiques d’un cache local.  Enregistrements DNS contiennent un Time To Live (TTL) qui permet du cache stocker l’enregistrement pour autant que possible sans ayant un impact sur l’actualisation enregistrement.  Pour cette raison, la mise en cache côté client est approprié pour la plupart des cas.

Certaines distros Linux n’incluez pas la mise en cache par défaut.  Il est recommandé de que vous ajoutez un à chaque Linux VM (après avoir vérifié qu’il n’est pas un cache local déjà).

Il existe plusieurs DNS mise en cache packages différents disponibles, par exemple dnsmasq, voici la procédure pour installer dnsmasq sur les plus courants distros :

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

> [AZURE.NOTE]: Le package 'dnsmasq' est seul le cache DNS nombreux disponibles pour Linux.  Avant de l’utiliser, vérifiez sa pertinence de vos besoins et que cache n’est pas installé.

**Nouvelles tentatives côté client :**

DNS est principalement un protocole UDP.  Comme le protocole UDP ne garantit pas la remise des messages, logique des nouvelles tentatives est gérée dans le protocole DNS proprement dit.  Chaque client DNS (système d’exploitation) peut présenter logique des nouvelles tentatives différentes selon la préférence créateurs :

 - Systèmes d’exploitation Windows Recommencer après une deuxième, puis à nouveau après un autre deux, quatre et l’autre quatre secondes. 
 - Les tentatives de configuration par défaut Linux après cinq secondes.  Vous devez modifier cette option pour recommencer cinq fois à intervalles d’une seconde.  

Pour vérifier les paramètres actuels sur un Linux VM, « Chat /etc/resolv.conf » et examinez la ligne « options », par exemple :

    options timeout:1 attempts:5

Le fichier resolv.conf est généré automatiquement et ne doit pas être modifié.  Les étapes spécifiques pour l’ajout de la ligne « options » varient selon distro :

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
Il existe plusieurs situations où vos besoins de résolution du nom peuvent ne vous limitez les fonctionnalités fournies par Azure, par exemple quand vous avez besoin résolution DNS entre les réseaux virtuels (vnets).  Pour couvrir ce scénario, Azure offre la possibilité d’utiliser vos propres serveurs DNS.  

Serveurs DNS au sein d’un réseau virtuel transférer des requêtes DNS pour les programmes de résolution d’Azure récursive pour résoudre les noms d’hôtes au sein de ce réseau virtuel.  Par exemple, un serveur DNS exécutant dans Azure peut répondre aux requêtes DNS pour ses fichiers de zone DNS et les transférer toutes les autres requêtes vers Azure.  Cela permet de machines virtuelles à voir les deux vos entrées dans vos fichiers de zone et les noms d’hôtes Azure condition (par le biais du redirecteurs).  Programmes de résolution d’Azure récursive est accessible via l’adresse IP virtuelle 168.63.129.16.

Redirection DNS Active la résolution DNS vnet notamment également et permet de vos ordinateurs locales résoudre les noms d’hôtes Azure condition.  Pour résoudre le nom d’hôte d’une machine virtuelle, le serveur DNS machine virtuelle doit se trouver dans le même réseau virtuel et être configuré pour les requêtes hostname transférer vers Azure.  Comme le suffixe DNS est différent dans chaque vnet, vous pouvez utiliser les règles de transfert conditionnelle pour envoyer des requêtes DNS pour le vnet correct pour la résolution.  L’image suivante montre deux vnets et un réseau locales effectuant résolution DNS vnet notamment à l’aide de cette méthode :

![Vnet notamment DNS](./media/virtual-machines-linux-azure-dns/inter-vnet-dns.png)

Lorsque vous utilisez la résolution de noms Azure condition, le suffixe DNS interne est fourni pour chaque machine virtuelle à l’aide de DHCP.  Lorsque vous utilisez votre propre solution de résolution du nom, ce suffixe n’est fourni aux machines virtuelles parce qu’il interfère avec d’autres architectures de DNS.  Pour faire référence à des ordinateurs par nom de domaine complet, ou pour configurer le suffixe sur vos ordinateurs virtuels, le suffixe peut être déterminé à l’aide de PowerShell ou l’API :

-  Pour la gestion des ressources Azure gérées vnets, le suffixe est disponible via la ressource de [carte réseau](https://msdn.microsoft.com/library/azure/mt163668.aspx) ou vous pouvez exécuter la commande `azure network public-ip show <resource group> <pip name>` pour afficher les détails de votre adresse IP publique, y compris le nom de domaine complet de la carte réseau.    


Si la redirection des requêtes vers Azure ne selon vos besoins, vous devez fournir votre propre solution DNS.  Votre solution DNS doit :

-  Fournir la résolution de nom d’hôte approprié, par exemple via [DDNS](../virtual-network/virtual-networks-name-resolution-ddns.md).  Note, si vous utilisez DDNS vous devrez peut-être désactiver le nettoyage des enregistrements DNS comme location DHCP d’Azure est très longues et nettoyage risque de supprimer les enregistrements DNS prématurément. 
-  Fournir la résolution récursive appropriée pour autoriser la résolution de noms de domaines externes.
-  Être accessible (TCP et UDP sur le port 53) à partir des clients qu'il sert et pouvoir accéder à internet.
-  Sécurisé contre l’accès à partir d’internet, à limiter les risques exemptés externes.

> [AZURE.NOTE] Pour de meilleures performances, lorsque vous utilisez des machines virtuelles Azure comme serveurs DNS, le protocole IPv6 doit être désactivé et une [Adresse IP publique au niveau de l’Instance](../virtual-network/virtual-networks-instance-level-public-ip.md) doivent être affectées à chaque machine virtuelle du serveur DNS.  


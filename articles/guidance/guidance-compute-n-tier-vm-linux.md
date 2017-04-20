<properties
   pageTitle="Exécution d’ordinateurs virtuels Linux pour une architecture multicouches sur Azure | Microsoft Azure"
   description="Comment exécuter Linux machines virtuelles pour une architecture multicouches dans Microsoft Azure."
   services=""
   documentationCenter="na"
   authors="MikeWasson"
   manager="roshar"
   editor=""
   tags=""/>

<tags
   ms.service="guidance"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/20/2016"
   ms.author="mwasson"/>

# <a name="running-linux-vms-for-an-n-tier-architecture-on-azure"></a>Exécution d’ordinateurs virtuels Linux pour une architecture multicouches sur Azure

[AZURE.INCLUDE [pnp-header](../../includes/guidance-pnp-header-include.md)]


> [AZURE.SELECTOR]
- [Exécution d’ordinateurs virtuels Linux pour une architecture multicouches sur Azure](guidance-compute-n-tier-vm-linux.md)
- [Exécution des machines virtuelles Windows pour une architecture multicouches sur Azure](guidance-compute-n-tier-vm.md)

Cet article présente un ensemble de pratiques pour l’exécution des machines virtuelles Linux (machines virtuelles) pour une application avec une architecture multicouches. Il s’appuie sur l’article [en cours d’exécution plusieurs ordinateurs virtuels sur Azure][multi-vm].

> [AZURE.NOTE] Azure comporte deux modèles de déploiement différents : [Le Gestionnaire de ressources] [ resource-manager-overview] et classique. Cet article utilise le Gestionnaire de ressources, Microsoft recommande pour les nouveaux déploiements.

## <a name="architecture-diagram"></a>Diagramme d’architecture

Il existe des variantes des architectures multicouches. Pour l’essentiel, les différences ne doivent pas a pas d’importance dans le cadre de ces recommandations. Cet article suppose une application web niveau 3 courantes :

- **Niveau Web.** Gère les demandes HTTP entrantes. Les réponses sont retournés à ce niveau.

- **Niveau d’entreprise.** Processus d’entreprise met en œuvre et d’autres logiques fonctionnel pour le système.

- **Niveau de la base de données.** Fournit un stockage de données permanente, à l’aide de Apache Cassandra de disponibilité.

> Un document Visio incluant ce diagramme architecture est disponible au téléchargement sur le [Centre de téléchargement Microsoft][visio-download]. Ce diagramme est sur « cluster - page de couche (Linux) multiples.

![[0]][0]

- **Jeux de disponibilité.** Créer une [Disponibilité définir] [ azure-availability-sets] pour chaque niveau de service des machines virtuelles au moins deux dans chaque niveau. Cette approche est nécessaire pour atteindre la disponibilité [SLA] [ vm-sla] pour les machines virtuelles.

- **Sous-réseaux.** Créer un sous-réseau distinct pour chaque niveau. Spécifier l’adresse plage et masque de sous-réseau à l’aide de la notation [CIDR] . 

- **Programmes d’équilibrage de charger.** Utiliser un [programme d’équilibrage de charge via Internet] [ load-balancer-external] pour distribuer le trafic Internet entrant vers le niveau web et un [équilibrage de charge interne] [ load-balancer-internal] pour distribuer le trafic réseau à partir de la couche web vers le niveau d’entreprise.

- **Jumpbox**. Un _jumpbox_, également appelé [hôte bastion], est une machine virtuelle sur le réseau par les administrateurs pour se connecter aux autres ordinateurs virtuels. Le jumpbox a un NSG qui autorise le trafic à distance uniquement à partir de la liste d’autorisation des adresses IP publiques. La NSG doit autoriser le trafic SSH (secure shell).

- **Surveillance des mots clés**. Surveillance des logiciels tels que [Nagios], [Zabbix]ou [Icinga] peut vous fournir claires temps de réponse, disponibilité machine virtuelle et la santé générale de votre système. Installer le logiciel de surveillance sur un ordinateur virtuel qui se trouve dans un sous-réseau gestion distincte.

- **NSGs**. Utiliser des [groupes de sécurité réseau] [ nsg] (NSGs) pour limiter le trafic réseau au sein de la VNet. Par exemple, dans l’architecture de niveau 3 illustré ici, le niveau de base de données n’accepte pas le trafic de frontal web, uniquement à partir de la couche d’entreprise et le sous-réseau gestion.

- **Base de données Apache Cassandra**. Fournit disponibilité du niveau données en activant la réplication et basculement.

## <a name="recommendations"></a>Recommandations

Azure offre de nombreuses ressources différentes et des types de ressources, de sorte que cette architecture de référence peut être mis en service de différentes manières. Nous vous fournissons un modèle Azure le Gestionnaire de ressources pour installer l’architecture de référence qui suit ces recommandations. Si vous choisissez de créer votre propre architecture de référence suivez ces recommandations, à moins d’avoir un besoin spécifique une recommandation ne tient pas.

### <a name="vnet--subnets"></a>VNet / sous-réseaux

Lorsque vous créez la VNet, allouer suffisamment d’espace adresse pour les sous-réseaux que vous avez besoin. Spécifier le masque de sous-réseau et de plage d’adresse VNet à l’aide de la notation [CIDR] . Utiliser un espace d’adressage qui se situe dans le standard [privés blocs d’adresses IP][private-ip-space], qui sont 10.0.0.0/8, 172.16.0.0/12 et 192.168.0.0/16.

Sélectionnez une plage d’adresses qui ne se chevauche pas avec votre réseau sur site, au cas où vous devez configurer une passerelle entre le VNet et votre réseau locales plus tard. Une fois que vous créez la VNet, vous ne pouvez pas modifier la plage d’adresses.

Créer des sous-réseaux avec exigences de fonctionnalités et de sécurité à l’esprit. Tous les ordinateurs virtuels au sein du même niveau ou rôle doit être placé dans le même sous-réseau, qui peut être une limite de sécurité. Pour plus d’informations sur la conception VNets et des sous réseaux, voir [planifier et concevoir des réseaux virtuels Azure][plan-network].

De chaque sous réseau, spécifiez l’espace d’adressage pour le sous-réseau en notation CIDR. Par exemple, « 10.0.0.0/24 » crée une plage de 256 adresses IP. (Machines virtuelles peuvent utiliser 251 de ces ; cinq sont réservés. Consultez le [Forum aux questions de réseau virtuel][vnet faq].) Vérifiez que les plages d’adresses ne se chevauchent sur sous-réseaux.

### <a name="load-balancers"></a>Programmes d’équilibrage de charge

L’équilibrage de charge externe répartit le trafic Internet vers le niveau web. Créer une adresse IP publique pour cette équilibrage de charge. Voir [Création d’un équilibrage de charge via Internet][lb-external-create].

L’équilibrage de charge interne distribue le trafic réseau à partir de la couche web vers le niveau d’entreprise. Pour donner à cet équilibrage de charge une adresse IP privée, créez une configuration IP frontend et associer avec le sous-réseau pour le niveau d’entreprise. Voir [prise en main de création d’un programme d’équilibrage de charge interne][lb-internal-create].

### <a name="cassandra"></a>Cassandra

Nous vous recommandons [d’Entreprise DataStax] [ datastax] pour production utilisation, mais ces recommandations s’appliquent à n’importe quelle édition Cassandra. Pour plus d’informations sur l’exécution de DataStax dans Azure, voir le [Guide de déploiement d’entreprise DataStax pour Azure][cassandra-in-azure]. 

Placez les ordinateurs virtuels pour un cluster Cassandra dans un jeu de disponibilité, pour vous assurer que les réplicas Cassandra sont réparties sur plusieurs domaines d’erreur et domaines mis à niveau. Pour plus d’informations sur les domaines de défaillance et mise à niveau, voir [Gérer la disponibilité des machines virtuelles][availability-sets-manage]. 

Configurer des domaines de défaillance 3 (au maximum) par jeu de disponibilité. 

Configurer des domaines de mise à niveau 18 par jeu de disponibilité. Cela vous donne le nombre maximal de domaines mise à niveau que vous pouvez toujours être répartis de manière égale sur les domaines de défaillance.   

Configurer des nœuds en mode prenant en charge en rack. Établir une correspondance entre domaines d’erreur rayons dans le `cassandra-rackdc.properties` fichier.

Vous n’avez pas besoin un équilibrage de charge devant le cluster. Le client se connecte directement à un nœud dans le cluster.

### <a name="jumpbox"></a>Jumpbox

Placez la jumpbox dans la même VNet en tant que les autres ordinateurs virtuels, mais dans un sous-réseau gestion distincte.

Créer une [adresse IP publique] pour la jumpbox.

Utiliser une petite taille de la mémoire virtuelle pour le jumpbox, par exemple A1 Standard.

Configurer les NSGs pour la couche web, couche d’entreprise et sous-réseaux de niveau de base de données pour autoriser le trafic (SSH) administration passer à partir du sous-réseau gestion.

Pour sécuriser la jumpbox, créez un NSG et appliquez-le au sous-réseau jumpbox. Ajouter une règle NSG qui autorise les connexions SSH uniquement à partir d’un ensemble de l’autorisation d’adresses IP publiques.

La NSG peut être attaché au sous-réseau ou à la jumpbox carte réseau. Dans ce cas, nous vous recommandons de pièce jointe dans la carte, afin que le trafic SSH est autorisé uniquement à la jumpbox, même si vous ajoutez d’autres ordinateurs virtuels au même sous-réseau.

N’autorisez pas accèsSSH à partir d’Internet public pour les ordinateurs virtuels qui s’exécutent de la charge de travail d’application. En revanche, tous les accèsSSH à ces machines virtuelles doit provenir via la jumpbox. Un administrateur se connecte à la jumpbox et puis se connecte à l’autre ordinateur virtuel à partir de la jumpbox. Le jumpbox permet le trafic SSH à partir d’Internet, mais uniquement à partir des adresses IP autorisés connues.

## <a name="availability-considerations"></a>Considérations relatives à la disponibilité

Placez chaque niveau ou rôle machine virtuelle dans un jeu de disponibilité distincte. Ne placez machines virtuelles à partir de différents niveaux dans le même jeu de disponibilité. 

Au niveau de la base de données, avoir plusieurs ordinateurs virtuels ne traduit pas automatiquement dans une base de données hautement disponible. Pour une base de données relationnel, vous devrez généralement utiliser la réplication et basculement disponibilité élevée.  

Si vous avez besoin d’une plus grande disponibilité du [Azure SLA pour les machines virtuelles] [ vm-sla] fournit, répliquer l’application sur les deux régions et utiliser le Gestionnaire de trafic Azure pour le basculement. Pour plus d’informations, voir [En cours d’exécution des machines virtuelles Linux dans plusieurs zones de disponibilité][multi-dc].  

## <a name="security-considerations"></a>Considérations sur la sécurité

Utiliser les règles NSG pour restreindre le trafic entre les niveaux. Par exemple, dans l’architecture de niveau 3 ci-dessus, la couche web ne communique pas directement avec la couche de base de données. Pour appliquer cette, le niveau de base de données doit bloquer le trafic entrant à partir du sous-réseau couche web.  

  1. Créer un NSG et associer au sous-réseau couche de base de données.

  2. Ajouter une règle qui refuse l’ensemble du trafic entrant à partir de la VNet. (Utiliser la `VIRTUAL_NETWORK` balise dans la règle.) 

  3. Ajouter une règle avec une priorité élevée qui autorise le trafic entrant à partir du sous-réseau de niveau entreprise. Cette règle remplace la règle précédente et permet le niveau d’entreprise de communiquer avec le niveau de base de données.

  4. Ajouter une règle qui autorise le trafic entrant au sein du sous-réseau de niveau de base de données elle-même. Cette règle permet la communication entre machines virtuelles dans le niveau de base de données, qui est nécessaire pour la réplication de base de données et basculement.

  5. Ajouter une règle qui autorise le trafic SSH à partir du sous-réseau jumpbox. Cette règle permet aux administrateurs de se connecter à la couche de base de données à partir de la jumpbox.

  > [AZURE.NOTE] Un NSG a [règles par défaut] [ nsg-rules] qui autoriser le trafic entrant à partir de la VNet. Les règles suivantes ne peuvent pas être supprimées, mais vous pouvez les remplacer en créant des règles de priorité plus élevée.

Vous pouvez ajouter un appareil virtuel réseau (à couloirs) pour créer une DMZ entre l’Internet public et le réseau virtuel Azure. À couloirs est un terme générique pour un appareil virtuel qui peut effectuer les tâches liées à un réseau tels que pare-feu, inspection des paquets, l’audit, routage personnalisé ou une série d’autres actions. Pour plus d’informations, consultez [implémentation d’une DMZ entre Azure et Internet][dmz].

## <a name="scalability-considerations"></a>Considérations relatives à l’extensibilité élevées

Les programmes d’équilibrage de charge distribuer le trafic réseau vers les niveaux web et de l’entreprise. Mettre à l’échelle horizontalement en ajoutant de nouvelles instances de machine virtuelle. Notez que vous pouvez mettre à l’échelle les web et les niveaux métier séparément, en fonction de charge. Pour réduire les problèmes éventuels causés par la nécessité de maintenir affinité du client, les ordinateurs virtuels dans la couche web doivent être sans état. Les ordinateurs virtuels qui héberge la logique métier doivent également être sans état.

## <a name="manageability-considerations"></a>Considérations relatives à la facilité de gestion

Simplifier la gestion de l’ensemble du système à l’aide des outils d’administration centralisés tels que [Azure Automation][azure-administration], [Microsoft Operations Management Suite][operations-management-suite], [Chef][chef], ou [Marionnette][puppet]. Ces outils peuvent consolider les informations de diagnostic et la santé capturées à partir de plusieurs ordinateurs virtuels pour fournir une vue d’ensemble du système.

## <a name="solution-deployment"></a>Déploiement de solution

Un déploiement pour une architecture de référence qui mettent en œuvre, ces recommandations n’est disponible sur [Github][github-folder]. Cette architecture de référence inclut un niveau web, couche d’entreprise et un niveau de données.

1. Cliquez sur le bouton ci-dessous.  
[! [« Déployer sur Azure »] [1]][2]

2. Une fois que le lien a ouvert dans le portail Azure, entrez les valeurs suivantes : 
  - Le nom du **groupe de ressources** est déjà défini dans le fichier de paramètres : sélectionnez **Créer un nouveau** et entrez `ra-ntier-sql-network-rg` dans la zone de texte.
  - Sélectionnez la région dans la zone de liste déroulante **emplacement** .
  - Ne modifiez pas **Modèle racine Uri** ou les zones de texte **Paramètre racine Uri** .
  - Passez en revue les termes et conditions, puis cliquez sur la case à cocher **que j’accepte les termes et conditions indiquées ci-dessus** .
  - Cliquez sur le bouton **achat** .

3. Vérifier Azure notification portail pour un message du déploiement est terminée.

4. Les fichiers de paramètres incluent un codé en dur noms d’utilisateur administrateur et les mots de passe et il est fortement recommandé que vous modifiez immédiatement les deux sur tous les ordinateurs virtuels. Cliquez sur chaque ordinateur virtuel dans le portail Azure, puis cliquez sur **Réinitialiser votre mot de passe** dans la carte de **Support + résolution des problèmes** . Sélectionnez **Réinitialiser votre mot de passe** dans la zone de liste déroulante **Mode** , puis sélectionnez un nouveau **nom d’utilisateur** et **mot de passe**. Cliquez sur le bouton de **mise à jour** pour conserver le nouveau nom d’utilisateur et mot de passe.

## <a name="next-steps"></a>Étapes suivantes

Pour obtenir une haute disponibilité pour cette architecture de référence, nous vous recommandons de [déploiement sur plusieurs régions][multi-dc].

<!-- links -->

[azure-administration]: ../automation/automation-intro.md
[azure-availability-sets]: ../virtual-machines/virtual-machines-windows-manage-availability.md#configure-each-application-tier-into-separate-availability-sets
[availability-sets-manage]: ../virtual-machines/virtual-machines-windows-manage-availability.md
[hôte bastion]: https://en.wikipedia.org/wiki/Bastion_host
[cassandra-consistency]: http://docs.datastax.com/en/cassandra/2.0/cassandra/dml/dml_config_consistency_c.html
[cassandra-consistency-usage]: http://medium.com/@foundev/cassandra-how-many-nodes-are-talked-to-with-quorum-also-should-i-use-it-98074e75d7d5#.b4pb4alb2
[cassandra-in-azure]: https://docs.datastax.com/en/datastax_enterprise/4.5/datastax_enterprise/install/installAzure.html
[cassandra-replication]: http://www.planetcassandra.org/data-replication-in-nosql-databases-explained/
[CIDR]: https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing
[chef]: https://www.chef.io/solutions/azure/
[datastax]: http://www.datastax.com/products/datastax-enterprise
[dmz]: guidance-iaas-ra-secure-vnet-dmz.md
[github-folder]: https://github.com/mspnp/reference-architectures/tree/master/guidance-compute-n-tier
[lb-external-create]: ../load-balancer/load-balancer-get-started-internet-portal.md
[lb-internal-create]: ../load-balancer/load-balancer-get-started-ilb-arm-portal.md
[load-balancer-external]: ../load-balancer/load-balancer-internet-overview.md
[load-balancer-internal]: ../load-balancer/load-balancer-internal-overview.md
[multi-dc]: guidance-compute-multiple-datacenters-linux.md
[multi-vm]: guidance-compute-multi-vm.md
[naming conventions]: guidance-naming-conventions.md
[nsg]: ../virtual-network/virtual-networks-nsg.md
[nsg-rules]: ../best-practices-resource-manager-security.md#network-security-groups
[operations-management-suite]: https://www.microsoft.com/en-us/server-cloud/operations-management-suite/overview.aspx
[plan-network]: ../virtual-network/virtual-network-vnet-plan-design-arm.md
[private-ip-space]: https://en.wikipedia.org/wiki/Private_network#Private_IPv4_address_spaces
[adresse IP publique]: ../virtual-network/virtual-network-ip-addresses-overview-arm.md
[puppet]: https://puppetlabs.com/blog/managing-azure-virtual-machines-puppet
[resource-manager-overview]: ../azure-resource-manager/resource-group-overview.md
[vm-sla]: https://azure.microsoft.com/en-us/support/legal/sla/virtual-machines
[vnet faq]: ../virtual-network/virtual-networks-faq.md
[visio-download]: http://download.microsoft.com/download/1/5/6/1569703C-0A82-4A9C-8334-F13D0DF2F472/RAs.vsdx
[Nagios]: https://www.nagios.org/
[Zabbix]: http://www.zabbix.com/
[Icinga]: http://www.icinga.org/
[0]: ./media/blueprints/compute-n-tier-linux.png "Architecture multicouches à l’aide de Microsoft Azure"
[1]: ./media/blueprints/deploybutton.png 
[2]: https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fmspnp%2Freference-architectures%2Fmaster%2Fguidance-compute-n-tier%2Fazuredeploy.json



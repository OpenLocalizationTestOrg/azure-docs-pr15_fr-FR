<properties
   pageTitle="Plusieurs ordinateurs virtuels en cours d’exécution | Architecture de référence | Microsoft Azure"
   description="Découvrez comment exécuter plusieurs instances de machine virtuelle sur Azure pour extensibilité élevées, résilience, facilité de gestion et la sécurité."
   services=""
   documentationCenter="na"
   authors="MikeWasson"
   manager="christb"
   editor=""
   tags=""/>

<tags
   ms.service="guidance"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/19/2016"
   ms.author="mwasson"/>

# <a name="running-multiple-vms-on-azure-for-scalability-and-availability"></a>Exécution de plusieurs ordinateurs virtuels sur Azure pour disponibilité et extensibilité élevées 

[AZURE.INCLUDE [pnp-header](../../includes/guidance-pnp-header-include.md)]

Cet article présente un ensemble de pratiques pour l’exécution de plusieurs instances de machines virtuelles (machine virtuelle) pour améliorer la sécurité, disponibilité, facilité de gestion et extensibilité élevées.   

Dans cette architecture, la charge de travail est réparti entre les instances de machine virtuelle. Il existe une seule adresse IP publique, et le trafic Internet est distribué sur les ordinateurs virtuels à l’aide d’un programme d’équilibrage de charge. Cette architecture peut être utilisée pour une application à un niveau, par exemple un cluster de stockage ou application web sans état. Il est également un bloc de construction pour les applications multicouches. 

Cet article est basé sur [l’exécution d’une seule machine virtuelle sur Azure][single vm]. Les recommandations dans cet article s’appliquent également à l’architecture.

## <a name="architecture-diagram"></a>Diagramme d’architecture

Machines virtuelles dans Azure nécessitent prenant en charge des ressources de stockage et de mise en réseau.

> Un document Visio incluant ce diagramme architecture est disponible au téléchargement sur le [Centre de téléchargement Microsoft][visio-download]. Ce diagramme est sur le « cluster - onglet de machine virtuelle multi. » 

![[0]][0]

L’architecture comprend les éléments suivants : 

- **Disponibilité ensemble.** La [disponibilité définie] [ availability set] contient les ordinateurs virtuels et est nécessaire pour prendre en charge la [disponibilité SLA pour les machines virtuelles Azure][vm-sla].

- **VNet**. Chaque machine virtuelle dans Azure est déployé dans un réseau virtuel (VNet) qui est divisé en **sous-réseaux**.

- **Équilibrage de charge Azure.** [Équilibrage de charge] répartit les demandes Internet entrantes vers les instances de machine virtuelle dans un jeu de disponibilité. L’équilibrage de charge inclut certaines ressources connexes :

  - **Adresse IP publique.** Une adresse IP publique est nécessaire pour l’équilibrage de charge recevoir le trafic Internet.

  - **Configuration frontale.** Associe l’adresse IP à l’équilibrage de charge.

  - **Adresses principale à affecter.** Contient les interfaces réseau (cartes réseau) pour les ordinateurs virtuels qui recevront le trafic entrant.

- **Charger les règles d’équilibrage.** Permet de distribuer le trafic réseau parmi tous les ordinateurs virtuels du pool d’adresses principale. 

- **Règles NAT.** Utilisé pour acheminer le trafic vers une machine virtuelle spécifique. Par exemple, pour activer le protocole Bureau à distance (RDP) pour les ordinateurs virtuels, créer une règle de la traduction des adresses réseau distincte pour chaque machine virtuelle. 

- **Interfaces réseau (cartes réseau)**. Chaque machine virtuelle a une carte réseau pour vous connecter au réseau.

- **Espace de stockage.** Comptes de stockage contiennent les images de machine virtuelle et d’autres ressources relatifs aux fichiers, tels que les données de diagnostic machine virtuelle capturées par Azure.

## <a name="recommendations"></a>Recommandations

Azure offre de nombreuses ressources différentes et des types de ressources, de sorte que cette architecture de référence peut être mis en service de différentes manières. Nous vous fournissons un modèle Azure le Gestionnaire de ressources pour installer l’architecture de référence qui suit les recommandations décrites ci-dessous. Si vous choisissez de créer votre propre architecture de référence, vous devez suivre ces recommandations, à moins que vous ayez un besoin spécifique une recommandation ne prenant pas en charge. 

### <a name="availability-set-recommendations"></a>Disponibilité définie recommandations

Vous devez créer au moins deux machines virtuelles dans la disponibilité définie pour prendre en charge la [disponibilité SLA pour les machines virtuelles Azure][vm-sla]. Notez que l’équilibrage de charge Azure requiert également que machines virtuelles équilibrage appartiennent au même jeu de disponibilité.

### <a name="network-recommendations"></a>Recommandations de réseau

Tous les ordinateurs virtuels derrière l’équilibrage de charge doivent être placés dans le même sous-réseau. N’exposent pas les ordinateurs virtuels directement à Internet, mais plutôt attribuer à chaque machine virtuelle une adresse IP privée. Les clients se connecter à l’aide de l’adresse IP de l’équilibrage de charge.

### <a name="load-balancer-recommendations"></a>Recommandations d’équilibrage de charge

Ajouter tous les ordinateurs virtuels dans la disponibilité définie au pool d’adresses principale de l’équilibrage de charge.

Définir des règles d’équilibrage de charge pour le trafic réseau directement aux ordinateurs virtuels. Par exemple, pour activer le trafic HTTP, créez une règle qui mappe le port 80 à partir de la configuration frontale au port 80 sur le pool d’adresses principale. Lorsque l’équilibrage de charge reçoit une requête sur le port 80 de l’adresse IP, il achemine la demande vers le port 80 sur une des cartes réseau du pool d’adresses principale.

Définir des règles NAT pour acheminer le trafic vers une machine virtuelle spécifique. Par exemple, pour activer RDP pour les ordinateurs virtuels créer une règle NAT distincte pour chaque machine virtuelle. Chaque règle doit mapper un numéro de port distinct vers le port 3389, le port par défaut pour RDP. (Par exemple, utilisez le port 50001 pour « VM1, « port 50002 pour « Ordinateur virtuel 2 » et ainsi de suite). Affecter les règles NAT aux cartes réseau sur les ordinateurs virtuels. 

### <a name="storage-account-recommendations"></a>Recommandations de compte de stockage

Créer des comptes de stockage Azure distincte pour chaque machine virtuelle stocker les disques durs virtuels (disques durs virtuels), afin d’éviter les réussir entrées/sorties par seconde [(sorties par) limites] [ vm-disk-limits] pour les comptes de stockage. 

Créer un compte de stockage pour les journaux de diagnostic. Ce compte de stockage peut être partagé par tous les ordinateurs virtuels.

## <a name="scalability-considerations"></a>Considérations relatives à l’extensibilité élevées

Il existe deux possibilités pour suivre l’évolution des machines virtuelles dans Azure : 

- Utiliser un programme d’équilibrage de charge pour distribuer le trafic réseau dans un ensemble de machines virtuelles. Pour augmenter les capacités, mise en service des machines virtuelles supplémentaires et les placer derrière l’équilibrage de charge. 

- Utiliser des [ensembles d’échelle Machine virtuelle][vmss]. Un ensemble d’échelle contient un nombre spécifié de machines virtuelles identiques derrière un équilibrage de charge. Machine virtuelle échelle définit autoscaling prise en charge basée sur les indicateurs de performance. Augmentation de la charge sur les ordinateurs virtuels, machines virtuelles supplémentaires sont automatiquement ajoutés à l’équilibrage de charge. 

Les sections suivantes comparent ces deux options.

### <a name="load-balancer-without-vm-scale-sets"></a>Équilibrage de charge sans jeux d’échelle machine virtuelle

Un équilibrage de charge prend demandes entrantes de réseau et les distribue vers les cartes réseau du pool d’adresses principale. Pour mettre à l’échelle horizontalement, ajouter plusieurs instances machine virtuelle à l’ensemble de disponibilité (ou libérer des machines virtuelles à l’échelle vers le bas). 

Par exemple, supposons que vous exécutez un serveur web. Vous devez ajouter une règle d’équilibrage de charge pour le port 80 et/ou le port 443 (pour SSL). Lorsqu’un client envoie une demande HTTP, l’équilibrage de charge sélectionne une adresse IP principale à l’aide d’un [algorithme de hachage] [ load balancer hashing] qui inclut l’adresse IP source. De cette façon, les demandes des clients sont réparties sur tous les ordinateurs virtuels. 

> [AZURE.TIP] Lorsque vous ajoutez une nouvelle machine virtuelle à une disponibilité définir, veillez à créer une carte réseau pour la machine virtuelle et ajouter la carte réseau à la liste principale adresse sur l’équilibrage de charge. Dans le cas contraire, le trafic Internet ne sont pas être routé vers la nouvelle machine virtuelle.

Chaque abonnement Azure a des limites par défaut en place, y compris un nombre maximal de machines virtuelles par région. Vous pouvez augmenter la limite en déposant une demande de support. Pour plus d’informations, voir [abonnement Azure et les limites de service, les quotas et les contraintes][subscription-limits].  

### <a name="vm-scale-sets"></a>Machine virtuelle échelle jeux 

Machine virtuelle échelle jeux vous aide à déployer et gérer un ensemble de machines virtuelles identiques. Avec toutes les machines virtuelles configuré de la même, jeux d’échelle machine virtuelle prise en charge true échelle, sans la mise en service de machines virtuelles, ce qui facilite générer des services à grande échelle ciblage cluster volumineux, big data et charges de travail en conteneur. 

Pour plus d’informations sur les ensembles d’échelle machine virtuelle, voir [Vue d’ensemble de Machine virtuelle échelle définit][vmss].

Considérations relatives à l’aide de jeux d’échelle machine virtuelle à :

- Prendre en compte les groupes d’échelle si vous devez rapidement évoluer machines virtuelles, ou à l’échelle. 

- Pour l’instant, jeux d’échelle ne prennent pas en charge disques de données. Les options de stockage des données sont le stockage de fichiers Azure, le lecteur système d’exploitation, le lecteur Temp ou un magasin externe, comme le stockage Azure. 

- Toutes les instances de machine virtuelle au sein d’une échelle définie automatiquement appartiennent au même jeu de disponibilité, avec les domaines défaillance 5 et 5 mise à jour.

- Par défaut, jeux d’échelle utilise « overprovisioning », qui signifie que le jeu d’échelle initialement davantage d’ordinateurs virtuels que vous leur demandez les dispositions, puis supprime les ordinateurs virtuels supplémentaires. Cela permet d’améliorer le taux de réussite globale lors de la configuration des ordinateurs virtuels. 

- Nous vous recommandons de ne plus puis à 20 machines virtuelles par stockage compte avec overprovisioning machines virtuelles activés ou non plus de 40 avec overprovisioning désactivé.  

- Vous pouvez trouver des modèles de gestionnaire de ressources de déploiement échelle définit dans les [Modèles de démarrage rapide d’Azure][vmss-quickstart].

- Il existe deux manières de base pour configurer des ordinateurs virtuels déployés dans un jeu d’échelle : créer une image personnalisée, ou utiliser des extensions pour configurer la machine virtuelle après sa configuration.

    - Un ensemble d’échelle basé sur une image personnalisée doit créer tous les disques virtuels de système d’exploitation au sein d’un compte de stockage. 

    - Avec des images personnalisées, vous devez maintenir l’image à jour.

    - Avec les extensions, cela peut prendre plus de temps pour une machine virtuelle nouvellement générée pour commencer à tourner.

Pour les considérations supplémentaires, voir [Conception virtuelle échelle jeux pour échelle][vmss-design].

> [AZURE.TIP]  Lorsque vous utilisez n’importe quelle solution échelle automatique, testez celle-ci avec les charges de travail au niveau de production à l’avance. 

## <a name="availability-considerations"></a>Considérations relatives à la disponibilité

La disponibilité ensemble constitue votre application plus résistant aux planifiés et événements de maintenance.

- _Maintenance planifiée_ se produit lorsque Microsoft met à jour la plateforme sous-jacente, ce qui provoque parfois machines virtuelles de redémarrer. Azure fait que les ordinateurs virtuels dans un jeu de disponibilité ne sont pas tous redémarré en même temps, au moins une est conservée en cours d’exécution tandis que d’autres personnes sont le redémarrage.

- _Maintenance planifiée_ se passe-t-il s’il existe une défaillance matérielle. Azure permet de s’assurer que les machines virtuelles dans un jeu de disponibilité sont mis en service entre plusieurs rack de serveurs. Cela permet de réduire l’impact des défaillances matérielles, panne réseau, power interruptions et ainsi de suite.

Pour plus d’informations, voir [Gérer la disponibilité des machines virtuelles][availability set]. Aperçu des ensembles de disponibilité est également équipé de la vidéo suivante : [Comment puis-je configurer un ensemble de disponibilité aux machines virtuelles échelle][availability set ch9]. 

> [AZURE.WARNING]  Veillez à configurer la disponibilité définie lorsque vous configurez la machine virtuelle. Actuellement, il n’existe aucun moyen d’ajouter un gestionnaire de ressources VM à disponibilité définie une fois la machine virtuelle est mis en service.

L’équilibrage de charge utilise [santé teste] pour analyser la disponibilité des instances de machine virtuelle. Si une sonde ne peut pas atteindre une instance dans un délai d’expiration, l’équilibrage de charge cesse d’envoyer le trafic vers cette dernière. Toutefois, l’équilibrage de charge continuent à sonde, et si la machine virtuelle redevient disponible, l’équilibrage de charge reprend envoie le trafic à cette dernière.

Voici quelques recommandations sur sondes de santé équilibrage de charge :

- Sondes peuvent tester HTTP ou TCP. Si votre machines virtuelles exécuter un serveur HTTP (IIS, nginx, application Node.js et ainsi de suite), créez une sonde HTTP. Sinon, créez une sonde TCP.

- Pour une sonde HTTP, spécifiez le chemin d’accès au point de terminaison HTTP. La sonde vérifie pour une réponse HTTP 200 à partir de ce chemin d’accès. Cela peut être le chemin d’accès racine (« / »), ou un point de terminaison de surveillance qui mettent en œuvre, certaines logique personnalisée pour vérifier l’état de l’application. Le point de terminaison doivent avoir autorisé les demandes HTTP anonymes.

- La sonde est envoyée à partir d’un [connus] [ health-probe-ip] adresse IP, 168.63.129.16. Veillez à que ne pas bloquer le trafic vers ou à partir de cette adresse IP dans les stratégies de pare-feu ou les règles de groupe (NSG) de sécurité de réseau.

- Utiliser [les journaux d’intégrité sonde] [ health probe log] pour afficher l’état d’intégrité sondes. Activer la journalisation dans le portail Azure pour chaque programme d’équilibrage de charge. Les journaux sont écrits sur le stockage d’objets Blob Azure. Les journaux indiquent combien machines virtuelles sur le serveur principal ne reçoivent pas le trafic réseau en raison de réponses sonde a échoué.

## <a name="manageability-considerations"></a>Considérations relatives à la facilité de gestion

Avec plusieurs ordinateurs virtuels, il est important automatiser des processus, afin qu’elles soient fiable et renouvelable. Vous pouvez utiliser [Automation Azure] [ azure-automation] pour automatiser déploiement, correction du système d’exploitation et les autres tâches. Automatisation Azure est un service d’automatisation qui s’exécute sur Azure et est basé sur Windows PowerShell. Exemples de scripts automation sont disponibles dans la [Galerie Runbook] sur TechNet.

## <a name="security-considerations"></a>Considérations sur la sécurité

Réseaux virtuels sont une limite d’isolement le trafic dans Azure. Machines virtuelles dans une que vnet ne peut pas communiquer directement aux machines virtuelles dans une autre VNet. Machines virtuelles au sein du même VNet peuvent communiquer, sauf si vous créez des [groupes de sécurité réseau] [ nsg] (NSGs) pour limiter le trafic. Pour plus d’informations, consultez [services cloud Microsoft et la sécurité du réseau][network-security].

Pour le trafic Internet entrant, les règles d’équilibrage de charge définissent le trafic qui communiquer avec le serveur principal. Toutefois, règles d’équilibrage de charge ne prend pas en charge Création de listes autorisées IP, donc si vous souhaitez liste d’autorisation des adresses certaine IP publiques, ajouter un NSG au sous-réseau.

## <a name="solution-deployment"></a>Déploiement de solution

Un déploiement pour une architecture de référence qui mettent en œuvre, ces recommandations est disponible sur GitHub. Cette architecture de référence inclut un réseau virtuel (VNet), groupe de sécurité réseau (NSG), équilibrage de charge et deux machines virtuelles (machines virtuelles).

L’architecture de référence peut être déployé avec Windows ou Linux machines virtuelles en suivant les instructions ci-dessous : 

1. Cliquez sur le bouton ci-dessous, puis sélectionnez soit « ouvrir le lien dans un nouvel onglet » ou « Ouvrir le lien dans une nouvelle fenêtre » :  
[![Déploiement d’Azure](./media/blueprints/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fmspnp%2Freference-architectures%2Fmaster%2Fguidance-compute-multi-vm%2Fazuredeploy.json)

2. Une fois que le lien a ouvert dans le portail Azure, vous devez entrer des valeurs pour certains paramètres : 
    - Le nom du **groupe de ressources** est déjà défini dans le fichier de paramètres : sélectionnez **Utiliser existant** et saisir `ra-multi-vm-rg` dans la zone de texte.
    - Sélectionnez la région dans la zone de liste déroulante **emplacement** .
    - Ne modifiez pas **Modèle racine Uri** ou les zones de texte **Paramètre racine Uri** .
    - Sélectionnez le **Type de système d’exploitation** dans la zone déroulante zone, **windows** ou **linux**. 
    - Passez en revue les termes et conditions, puis cliquez sur la case à cocher **que j’accepte les termes et conditions indiquées ci-dessus** .
    - Cliquez sur le bouton **achat** .

3. Attendez que le déploiement terminer.

4. Les fichiers de paramètres incluent un nom d’utilisateur administrateur codé en dur et le mot de passe, et il est fortement recommandé que vous modifiez immédiatement les deux. Cliquez sur la machine virtuelle nommée `ra-multi-vm1` dans le portail Azure. Ensuite, cliquez sur **Réinitialiser votre mot de passe** dans la carte de **Support + résolution des problèmes** . Sélectionnez **Réinitialiser votre mot de passe** dans la zone de liste déroulante **Mode** , puis sélectionnez un nouveau **nom d’utilisateur** et **mot de passe**. Cliquez sur le bouton de **mise à jour** pour enregistrer le nouveau nom d’utilisateur et mot de passe. Répétez cette étape pour la machine virtuelle nommée `ra-multi-vm2`.

Pour plus d’informations sur les autres façons de déployer cette architecture de référence, voir le fichier Lisez-moi dans le [Guide-unique-machine virtuelle] [ github-folder] GitHub dossier. 

## <a name="next-steps"></a>Étapes suivantes

En plaçant plusieurs machines virtuelles derrière un équilibrage de charge est un bloc de construction pour créer des architectures multicouches. Pour plus d’informations, voir [En cours d’exécution des machines virtuelles Windows pour une architecture multicouches sur Azure] [ n-tier-windows] et [En cours d’exécution des machines virtuelles Linux pour une architecture multicouches sur Azure][n-tier-linux]

<!-- Links -->
[availability set]: ../virtual-machines/virtual-machines-windows-manage-availability.md
[availability set ch9]: https://channel9.msdn.com/Series/Microsoft-Azure-Fundamentals-Virtual-Machines/08
[azure-automation]: https://azure.microsoft.com/en-us/documentation/services/automation/
[azure-cli]: ../virtual-machines-command-line-tools.md
[bastion host]: https://en.wikipedia.org/wiki/Bastion_host
[github-folder]: https://github.com/mspnp/reference-architectures/tree/master/guidance-compute-multi-vm
[health probe log]: ../load-balancer/load-balancer-monitor-log.md
[sondes d’intégrité]: ../load-balancer/load-balancer-overview.md#service-monitoring
[health-probe-ip]: ../virtual-network/virtual-networks-nsg.md#special-rules
[équilibrage de charge]: ../load-balancer/load-balancer-get-started-internet-arm-cli.md
[load balancer hashing]: ../load-balancer/load-balancer-overview.md#hash-based-distribution
[n-tier-linux]: guidance-compute-n-tier-vm-linux.md
[n-tier-windows]: guidance-compute-n-tier-vm.md
[naming conventions]: guidance-naming-conventions.md
[network-security]: ../best-practices-network-security.md
[nsg]: ../virtual-network/virtual-networks-nsg.md
[resource-manager-overview]: ../azure-resource-manager/resource-group-overview.md 
[Galerie Runbook]: ../automation/automation-runbook-gallery.md#runbooks-in-runbook-gallery
[single vm]: guidance-compute-single-vm.md
[subscription-limits]: ../azure-subscription-service-limits.md
[visio-download]: http://download.microsoft.com/download/1/5/6/1569703C-0A82-4A9C-8334-F13D0DF2F472/RAs.vsdx
[vm-disk-limits]: ../azure-subscription-service-limits.md#virtual-machine-disk-limits
[vm-sla]: https://azure.microsoft.com/en-us/support/legal/sla/virtual-machines/v1_2/
[vmss]: ../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md
[vmss-design]: ../virtual-machine-scale-sets/virtual-machine-scale-sets-design-overview.md
[vmss-quickstart]: https://azure.microsoft.com/documentation/templates/?term=scale+set
[VM-sizes]: https://azure.microsoft.com/documentation/articles/virtual-machines-windows-sizes/
[0]: ./media/blueprints/compute-multi-vm.png "Architecture d’une solution multi-machine virtuelle sur Azure comprenant une ensemble avec deux machines virtuelles et un équilibrage de charge de disponibilité"

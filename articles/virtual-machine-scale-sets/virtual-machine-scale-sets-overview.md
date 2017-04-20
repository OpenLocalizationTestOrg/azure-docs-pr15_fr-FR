<properties
    pageTitle="Présentation des jeux de Machine virtuelle échelle | Microsoft Azure"
    description="Pour plus d’informations sur les ensembles d’échelle Machine virtuelle"
    services="virtual-machine-scale-sets"
    documentationCenter=""
    authors="gbowerman"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machine-scale-sets"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="09/13/2016"
    ms.author="guybo"/>

# <a name="virtual-machine-scale-sets-overview"></a>Vue d’ensemble des jeux d’échelle Machine virtuelle

Machine virtuelle échelle jeux sont une ressource Azure calculer, que vous pouvez utiliser pour déployer et gérer un ensemble de machines virtuelles identiques. Avec tous les ordinateurs virtuels configurés la même échelle machine virtuelle jeux sont conçus pour gérer échelle true – sans la mise en service de machines virtuelles est requise – et simplifie en tant que tel générer des services à grande échelle ciblage cluster volumineux, big data et charges de travail en conteneur.

Pour les applications que vous avez besoin à l’échelle de ressources de calcul, puis dans échelle opérations sont implicitement équilibrées sur plusieurs domaines défaillance et mise à jour. Pour obtenir une introduction à l’échelle machine virtuelle jeux de faire référence à l' [annonce blog Azure](https://azure.microsoft.com/blog/azure-virtual-machine-scale-sets-ga/).

Examinons ces vidéos pour plus d’informations sur les ensembles d’échelle machine virtuelle :

 - [Mark Russinovich parle jeux d’échelle Azure](https://channel9.msdn.com/Blogs/Regular-IT-Guy/Mark-Russinovich-Talks-Azure-Scale-Sets/)  

 - [Machine virtuelle échelle définit avec bonhomme Bowerman](https://channel9.msdn.com/Shows/Cloud+Cover/Episode-191-Virtual-Machine-Scale-Sets-with-Guy-Bowerman)

## <a name="creating-and-managing-vm-scale-sets"></a>Création et gestion d’échelle machine virtuelle définit

Vous pouvez créer un ensemble d’échelle de machine virtuelle dans le [portail Azure](https://portal.azure.com) en sélectionnant _Nouveau_ et en tapant « échelle » dans la barre de recherche. Vous verrez « machine virtuelle échelle » définir dans les résultats. À partir de là, vous pouvez renseigner les champs requis pour personnaliser et déployer votre jeu d’échelle. 

Échelle de machine virtuelle jeux peuvent également être définis et déploiement à l’aide de modèles JSON et [API REST](https://msdn.microsoft.com/library/mt589023.aspx) simplement comme machines virtuelles de gestionnaire de ressources Azure individuels. Par conséquent, les méthodes de déploiement Gestionnaire de ressources Azure standard peuvent être utilisées. Pour plus d’informations sur les modèles, voir [Gestionnaire de ressources Azure de création de modèles](../resource-group-authoring-templates.md).

Un ensemble d’exemples de modèles pour les jeux d’échelle machine virtuelle, vous pouvez trouver dans le référentiel de GitHub de modèles de démarrage rapide Azure [ici.](https://github.com/Azure/azure-quickstart-templates) (Rechercher les modèles avec _vmss_ dans le titre)

Dans les pages de détails pour ces modèles vous voyez un bouton qui est lié à la fonctionnalité de déploiement de portail. Pour déployer le jeu d’échelle machine virtuelle, cliquez sur le bouton, puis renseignez les paramètres qui sont requis dans le portail. Si vous ne savez pas si une ressource prend en charge supérieur ou minuscules, il est recommandé d’utiliser toujours les valeurs de paramètre de minuscules. Il existe également une pratique dissection vidéo d’un modèle machine virtuelle échelle définir ici :

[Machine virtuelle échelle définie Dissection de modèle](https://channel9.msdn.com/Blogs/Windows-Azure/VM-Scale-Set-Template-Dissection/player)

## <a name="scaling-a-vm-scale-set-out-and-in"></a>Mise à l’échelle une échelle machine virtuelle définie, puis dans

Pour augmenter ou diminuer le nombre de machines virtuelles dans un jeu d’échelle machine virtuelle, simplement modifier la propriété _capacité_ et redéployez le modèle. Cette simplicité facilite l’écrivez votre propre calque mise à l’échelle personnalisée si vous voulez définir des événements échelle personnalisée qui ne sont pas pris en charge par échelle Azure.

Si vous redéployez un modèle pour modifier la capacité, vous pouvez définir un quantité modèle plus petit qui inclut uniquement la référence (SKU) et la capacité de mise à jour. Voici un exemple de ce [ici.](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-scale-existing)

Pour connaître les étapes pour créer un jeu d’échelle qui est automatiquement mise à l’échelle, voir [Automatiquement Machines à l’échelle dans un jeu d’échelle de Machine virtuelle](virtual-machine-scale-sets-windows-autoscale.md)

## <a name="monitoring-your-vm-scale-set"></a>Définie votre échelle machine virtuelle surveillance des mots clés

Le [portail Azure](https://portal.azure.com) listes échelle jeux et affiche les propriétés de base, ainsi annonce machines virtuelles dans l’ensemble. Pour plus d’informations, vous pouvez utiliser l' [Explorateur de ressources Azure](https://resources.azure.com) pour afficher les jeux d’échelle machine virtuelle. Machine virtuelle échelle jeux sont une ressource sous Microsoft.Compute, afin qu’à partir du site que vous puissiez les voir, développez les liens suivants :

    subscriptions -> your subscription -> resourceGroups -> providers -> Microsoft.Compute -> virtualMachineScaleSets -> your VM scale set -> etc.

## <a name="vm-scale-set-scenarios"></a>Machine virtuelle échelle définie scénarios

Cette section répertorie quelques scénarios de jeu échelle machine virtuelle classiques. Certains services Azure de niveau supérieur (par exemple, le lot, Service tissu, Azure conteneur Service) utilisera ces scénarios.

 - **RDP / SSH sur une échelle de machine virtuelle définie instances** - machine virtuelle A échelle ensemble est créé à l’intérieur d’une VNET et machines virtuelles individuelles dans l’ensemble d’échelle ne sont pas affectés adresses IP publiques. Il s’agit d’une bonne chose parce que vous ne voulez généralement pas la charge de note de frais et gestion d’attribution des adresses IP publics distinctes à toutes les ressources sans état dans la grille de calcul et que vous pouvez facilement vous connecter à ces ordinateurs virtuels à partir d’autres ressources dans votre VNET, y compris ceux qui ont des adresses IP publiques telles que les programmes d’équilibrage de charge ou machines virtuelles autonome.

 - **Se connecter aux machines virtuelles à l’aide de règles NAT** - vous pouvez créer une adresse IP publique, affectez-le à un équilibrage de charge et définir les règles de trafic entrant NAT qui mapper un port sur l’adresse IP à un port sur un ordinateur virtuel dans l’ensemble d’échelle machine virtuelle. Par exemple :
 
    Source | Port source | Destination | Port de destination
    --- | --- | --- | ---
    Adresse IP publique | Port 50000 | vmss\_0 | Port 22
    Adresse IP publique | Port 50001 | vmss\_1 | Port 22
    Adresse IP publique | Port 50002 | vmss\_2 | Port 22

    Voici un exemple de création d’un jeu d’échelle machine virtuelle qui utilise les règles NAT pour activer des connexions SSH à chaque machine virtuelle dans une échelle à l’aide d’une seule adresse IP publique : [https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-linux-nat](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-linux-nat)

    Voici un exemple d’obtenant le même avec RDP et Windows : [https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-windows-nat](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-windows-nat)

 - **Se connecter aux machines virtuelles à l’aide d’un « jumpbox »** - si vous créez une échelle machine virtuelle définissez et une machine virtuelle dans la même VNET, la version autonome de machine virtuelle et le jeu d’échelle machine virtuelle que machines virtuelles peuvent se connecter à l’autre à l’aide de son adresse IP interne autonome adresses telle que définie par le VNET/sous-réseau. Si vous créez une adresse IP publique et affectez à la version autonome de machine virtuelle vous pouvez RDP ou SSH vers la version autonome de machine virtuelle, puis connectez-vous à partir de cet ordinateur à vos instances machine virtuelle échelle ensemble. À ce stade, vous pouvez remarquer qu’un ensemble d’échelle machine virtuelle simple est plus sécurisé à une machine virtuelle autonome simple avec une adresse IP publique dans sa configuration par défaut.

    [Pour obtenir un exemple de cette approche, ce modèle crée un cluster Mesos simple composée d’une instance autonome VM maître qui gère un cluster machine virtuelle échelle jeu en fonction de machines virtuelles.](https://github.com/gbowerman/azure-myriad/blob/master/mesos-vmss-simple-cluster.json)

 - **L’équilibrage de charge sur une échelle de machine virtuelle définie instances** - si vous voulez effectuer le travail devant un cluster de calcul de machines virtuelles à l’aide d’une approche « alternées », vous pouvez configurer un programme d’équilibrage de charge Azure avec l’équilibrage de charge des règles en conséquence. Vous pouvez définir des sondes pour vérifier votre application est en cours d’exécution en exécutant la commande ping ports avec un chemin d’accès de protocole, intervalle et demande spécifié. La [Passerelle d’Application](https://azure.microsoft.com/services/application-gateway/) Azure prend également en charge les jeux d’échelle, ainsi que des scénarios d’équilibrage de charge plus sophistiquée.

    [Voici un exemple qui crée un ensemble d’échelle machine virtuelle de machines virtuelles serveur web IIS en cours d’exécution et utilise un équilibrage de charge pour équilibrer la charge reçoit chaque machine virtuelle. Il utilise également le protocole HTTP pour commande ping sur une URL spécifique sur chaque ordinateur virtuel.](https://github.com/gbowerman/azure-myriad/blob/master/vmss-win-iis-vnet-storage-lb.json) (Regardez le type de ressource Microsoft.Network/loadBalancers et networkProfile et extensionProfile dans la virtualMachineScaleSet)

 - **Déploiement d’une échelle de machine virtuelle définie comme un cluster de calcul dans un gestionnaire de cluster PaaS** - échelle machine virtuelle jeux sont parfois décrits comme un rôle de collaborateur nouvelle génération. Il est une description valide, mais elle s’exécute également le risque d’échelle confusion ensemble de fonctionnalités avec des fonctionnalités de rôle PaaS v1 collaborateur. Dans un sens échelle machine virtuelle jeux de fournissent un véritable « rôle de travail » ou une ressource de travail, car ils fournissent une ressource cluster GRG qui est indépendante, plateforme/runtime personnalisables et s’intègre à Azure Gestionnaire de ressources IaaS.

    Un rôle de collaborateur v1 PaaS, tandis que limité en termes de prise en charge de la plateforme/runtime (images de plateforme Windows) inclut également des services tels que permutation VIP, les paramètres de mise à niveau configurables, runtime/déploiement des paramètres de l’application qui ne sont pas _encore_ disponibles dans la machine virtuelle échelle jeux, ou seront remises par d’autres services de niveau supérieur PaaS comme tissu de Service. Ceci vous pouvez examiner machine virtuelle échelle des ensembles comme une infrastructure qui prend en charge PaaS. Par exemple Solutions PaaS tels que tissu de Service ou responsables cluster comme Mesos pouvez créer en haut de la machine virtuelle échelle jeux en tant que format SVG cluster calque.

    [Pour obtenir un exemple de cette approche, ce modèle crée un cluster Mesos simple composée d’une instance autonome VM maître qui gère un cluster machine virtuelle échelle jeu en fonction de machines virtuelles.](https://github.com/gbowerman/azure-myriad/blob/master/mesos-vmss-simple-cluster.json) Les versions ultérieures du [Service de conteneur Azure](https://azure.microsoft.com/blog/azure-container-service-now-and-the-future/) déployez plus complexe/renforcée les versions de ce scénario basée sur les ensembles d’échelle machine virtuelle.

## <a name="vm-scale-set-performance-and-scale-guidance"></a>Machine virtuelle échelle jeu de performances et les proportions de recommandations

- Ne créez pas plus de 500 machines virtuelles dans plusieurs jeux d’échelle machine virtuelle à la fois.
- Planifier pour les pas plus de 20 machines virtuelles par compte de stockage (sauf si vous définissez la propriété _overprovision_ « false », auquel cas vous pouvez accéder jusqu'à 40).
- Aérer les premières lettres du nom de compte de stockage autant que possible.  Les exemples de modèles VMSS dans les [modèles de démarrage rapide Azure](https://github.com/Azure/azure-quickstart-templates/) donnez des exemples de cette procédure.
- Si vous utilisez machines virtuelles personnalisés, planifiez pas plus de 40 machines virtuelles par jeu d’échelle machine virtuelle, dans un compte de stockage unique.  Vous devez l’image préalable copié dans le compte de stockage avant de vous lancer machine virtuelle échelle jeu de déploiement. Consultez le Forum aux questions pour plus d’informations.
- Planifier pour les pas plus de 4096 machines virtuelles par VNET.
- Le nombre d’ordinateurs virtuels vous pouvez créer est limité par le quota de base dans la région dans lequel vous effectuez le déploiement. Vous devrez contacter le support technique pour accroître votre limite de quota cluster augmentée même si vous avez une limite haute de cœurs pour une utilisation avec les services en nuage ou IaaS v1 aujourd'hui. Pour rechercher votre quota, vous pouvez exécuter la commande suivante Azure infrastructure du langage commun : `azure vm list-usage`et la commande PowerShell suivante : `Get-AzureRmVMUsage` (si vous utilisez une version de PowerShell ci-dessous 1.0 utilisent `Get-AzureVMUsage`).

## <a name="vm-scale-set-frequently-asked-questions"></a>Échelle de machine virtuelle définie Forum aux questions

**QUESTIONS/RÉPONSES.** Machines virtuelles combien vous avez dans un jeu d’échelle machine virtuelle ?

**A.** 100 si vous utilisez des images de plateforme qui peuvent être réparties entre plusieurs comptes de stockage. Si vous utilisez des images personnalisées, jusqu'à 40 (si la propriété _overprovision_ est définie sur « false », 20 par défaut), depuis des images personnalisées sont actuellement limités à un compte de stockage unique.

**Q** quels autres limites de ressources existent pour les jeux d’échelle machine virtuelle ?

**A.** Vous êtes limité à la création de pas plus de 500 machines virtuelles dans plusieurs jeux d’échelle par région pendant une période de 10 minutes. Existant [limites de Service d’abonnement Azure /](../azure-subscription-service-limits.md) appliquer.

**QUESTIONS/RÉPONSES.** Sont les données disques pris en charge dans les ensembles d’échelle machine virtuelle ?

**A.** Pas dans la version initiale. Les options disponibles pour le stockage de données sont :

- Fichiers Azure (lecteurs PME partagé)

- Lecteur du système d’exploitation

- Lecteur TEMP (local, ne pas sauvegardé par stockage Azure)

- Service de données Azure (par exemple, tables Azure, des objets BLOB Azure)

- Service de données externes (par exemple, à distance DB)

**QUESTIONS/RÉPONSES.** Les régions Azure prennent en charge les jeux d’échelle machine virtuelle ?

**A.** N’importe quelle région qui prend en charge le Gestionnaire de ressources Azure prend en charge les jeux d’échelle machine virtuelle.

**QUESTIONS/RÉPONSES.** Comment créer une échelle de machine virtuelle définie à l’aide d’une image personnalisée ?

**A.** Laissez la propriété vhdContainers vide, par exemple :

    "storageProfile": {
        "osDisk": {
            "name": "vmssosdisk",
            "caching": "ReadOnly",
            "createOption": "FromImage",
            "image": {
                "uri": "https://mycustomimage.blob.core.windows.net/system/Microsoft.Compute/Images/mytemplates/template-osDisk.vhd"
            },
            "osType": "Windows"
        }
    },


**QUESTIONS/RÉPONSES.** Si j’ai réduisez mon échelle machine virtuelle définie capacité de 20 15, machines virtuelles sont supprimées ?

**A.** Machines virtuelles sont supprimés à partir de l’échelle de la valeur uniformément sur les domaines mise à niveau et défaillance optimiser la disponibilité. Machines virtuelles avec l’id le plus élevé sont supprimées en premier.

**QUESTIONS/RÉPONSES.** Qu’en est-il il si je puis augmentez la capacité de 15 à 18 ?

**A.** Si vous augmentez la capacité à 18, puis 3 nouvelles machines virtuelles est créées. Chaque fois que l’id de l’instance machine virtuelle incrémentée de la valeur la plus élevée précédente (par exemple 20, 21, 22). Machines virtuelles sont réparties sur FDs et UDs.

**QUESTIONS/RÉPONSES.** Lorsque vous utilisez plusieurs extensions dans un jeu d’échelle machine virtuelle, puis-je appliquer une séquence d’exécution ?

**A.** Pas directement, mais pour l’extension customScript, votre script peut attendre une autre extension Terminer ([par exemple en contrôlant le journal de l’extension](https://github.com/Azure/azure-quickstart-templates/blob/master/201-vmss-lapstack-autoscale/install_lap.sh)). Vous trouverez des conseils supplémentaires sur la mise en séquence extension dans ce billet de blog : [Extension de classement de jeux d’échelle Azure machine virtuelle](https://msftstack.wordpress.com/2016/05/12/extension-sequencing-in-azure-vm-scale-sets/).

**QUESTIONS/RÉPONSES.** Machine virtuelle échelle jeux fonctionnent avec des jeux de disponibilité Azure ?

**A.** Oui. Un ensemble d’échelle machine virtuelle est une disponibilité implicite définie avec 5 FDs et 5 UDs. Vous n’avez pas besoin de configurer rien sous virtualMachineProfile. Dans les futures versions, jeux d’échelle machine virtuelle est susceptibles de s’étalent sur plusieurs clients, mais pour maintenant définie une échelle est un ensemble de disponibilité unique.



Pour les applications que vous avez besoin à l’échelle de ressources de calcul, puis dans échelle opérations sont implicitement équilibrées sur plusieurs domaines défaillance et mise à jour. Pour obtenir une introduction à l’échelle machine virtuelle jeux de faire référence à l' [annonce Azure blog](https://azure.microsoft.com/blog/azure-vm-scale-sets-public-preview/)récents.

Examinons ces vidéos pour plus d’informations sur les ensembles d’échelle machine virtuelle :

 - [Mark Russinovich parle jeux d’échelle Azure](https://channel9.msdn.com/Blogs/Regular-IT-Guy/Mark-Russinovich-Talks-Azure-Scale-Sets/)  

 - [Machine virtuelle échelle définit avec bonhomme Bowerman](https://channel9.msdn.com/Shows/Cloud+Cover/Episode-191-Virtual-Machine-Scale-Sets-with-Guy-Bowerman)

## <a name="creating-and-managing-vm-scale-sets"></a>Création et gestion d’échelle machine virtuelle définit

Échelle de machine virtuelle jeux peuvent être définis et déploiement à l’aide de modèles JSON et [API REST](https://msdn.microsoft.com/library/mt589023.aspx) simplement comme machines virtuelles de gestionnaire de ressources Azure individuels. Par conséquent, les méthodes de déploiement Gestionnaire de ressources Azure standard peuvent être utilisées. Pour plus d’informations sur les modèles, voir [Gestionnaire de ressources Azure de création de modèles](../articles/resource-group-authoring-templates.md).

Un ensemble d’exemples de modèles pour les jeux d’échelle machine virtuelle vous pouvez trouver dans le référentiel de GitHub de modèles de démarrage rapide Azure ici :

[https://github.com/Azure/azure-quickstart-templates](https://github.com/Azure/azure-quickstart-templates) - recherchez des modèles avec _vmss_ dans le titre.

Dans les pages de détails pour ces modèles vous voyez un bouton qui est lié à la fonctionnalité de déploiement de portail. Pour déployer le jeu d’échelle machine virtuelle, cliquez sur le bouton, puis renseignez les paramètres qui sont requis dans le portail. Si vous ne savez pas si une ressource prend en charge supérieur ou minuscules, il est recommandé d’utiliser toujours les valeurs de paramètre de minuscules. Il existe également une pratique dissection vidéo d’un modèle machine virtuelle échelle définir ici :

[Machine virtuelle échelle définie Dissection de modèle](https://channel9.msdn.com/Blogs/Windows-Azure/VM-Scale-Set-Template-Dissection/player)

## <a name="scaling-a-vm-scale-set-out-and-in"></a>Mise à l’échelle une échelle machine virtuelle définie, puis dans

Pour augmenter ou diminuer le nombre de machines virtuelles dans un jeu d’échelle machine virtuelle, simplement modifier la propriété _capacité_ et redéployez le modèle. Cette simplicité facilite l’écrivez votre propre calque mise à l’échelle personnalisée si vous voulez définir des événements échelle personnalisée qui ne sont pas pris en charge par échelle Azure.

Si vous redéployez un modèle pour modifier la capacité, vous pouvez définir un quantité modèle plus petit qui inclut uniquement la référence (SKU) et la capacité de mise à jour. Voici un exemple de ce : [https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vmss-scale-in-or-out/azuredeploy.json](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vmss-linux-nat/azuredeploy.json).

Pour connaître les étapes pour créer un jeu d’échelle qui est automatiquement mise à l’échelle, voir [Automatiquement Machines à l’échelle dans un jeu d’échelle de Machine virtuelle](../articles/virtual-machines/virtual-machines-windows-vmss-powershell-creating.md)

## <a name="monitoring-your-vm-scale-set"></a>Définie votre échelle machine virtuelle surveillance des mots clés

Il est actuellement recommandé de qu'utiliser l' [Explorateur de ressources Azure](https://resources.azure.com) pour afficher les jeux d’échelle machine virtuelle. Machine virtuelle échelle jeux sont une ressource sous Microsoft.Compute, afin qu’à partir du site que vous puissiez les voir, développez les liens suivants :

    subscriptions -> your subscription -> resourceGroups -> providers -> Microsoft.Compute -> virtualMachineScaleSets -> your VM scale set -> etc.

## <a name="vm-scale-set-scenarios"></a>Machine virtuelle échelle définie scénarios

Cette section répertorie quelques scénarios de jeu échelle machine virtuelle classiques. Certains services Azure de niveau supérieur (par exemple, le lot, Service tissu, Azure conteneur Service) utilisera ces scénarios.

 - **RDP / SSH sur une échelle de machine virtuelle définie instances** - machine virtuelle A échelle ensemble est créé à l’intérieur d’une VNET et machines virtuelles individuelles dans ne sont pas affectés adresses IP publiques. Il s’agit d’une bonne chose, car vous ne voulez généralement pas la charge de note de frais et gestion d’attribution des adresses IP distinctes à toutes les ressources dans votre grille cluster sans état, et vous pouvez facilement vous connecter à ces ordinateurs virtuels à partir d’autres ressources dans votre VNET, y compris ceux qui ont des adresses IP publiques telles que les programmes d’équilibrage de charge ou machines virtuelles autonome.

 - **Se connecter aux machines virtuelles à l’aide de règles NAT** - vous pouvez créer une adresse IP publique, affectez-le à un équilibrage de charge et définir les règles de trafic entrant NAT qui mapper un port sur l’adresse IP à un port sur un ordinateur virtuel dans l’ensemble d’échelle machine virtuelle. Par exemple

    Adresse IP publique -> Port 50000 -> vmss\_0 -> Port 22 Public IP - > Port 50001 -> vmss\_1 -> Port 22 Public IP - > Port 50002 -> vmss\_2 -> Port 22

    Voici un exemple de création d’un jeu d’échelle machine virtuelle qui utilise les règles NAT pour activer des connexions SSH à chaque machine virtuelle dans une échelle à l’aide d’une seule adresse IP publique : [https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-linux-nat](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-linux-nat)

    Voici un exemple d’obtenant le même avec RDP et Windows : [https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-windows-nat](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-windows-nat)

 - **Se connecter aux machines virtuelles à l’aide d’un « jumpbox »** - si vous créez une échelle machine virtuelle définissez et une machine virtuelle dans la même VNET, la version autonome de machine virtuelle et le jeu d’échelle machine virtuelle que machines virtuelles peuvent se connecter à l’autre à l’aide de son adresse IP interne autonome adresses telle que définie par le VNET/sous-réseau. Si vous créez une adresse IP publique et affectez à la version autonome de machine virtuelle vous pouvez RDP ou SSH vers la version autonome de machine virtuelle, puis connectez-vous à partir de cet ordinateur à vos instances machine virtuelle échelle ensemble. À ce stade, vous pouvez remarquer qu’un ensemble d’échelle machine virtuelle simple est plus sécurisé à une machine virtuelle autonome simple avec une adresse IP publique dans sa configuration par défaut.

    Pour obtenir un exemple de cette approche, ce modèle crée un cluster Mesos simple composée d’une instance autonome VM maître qui gère un cluster machine virtuelle échelle jeu en fonction de machines virtuelles : [https://github.com/gbowerman/azure-myriad/blob/master/mesos-vmss-simple-cluster.json](https://github.com/gbowerman/azure-myriad/blob/master/mesos-vmss-simple-cluster.json)

 - **L’équilibrage de charge alternées sur une échelle de machine virtuelle définie instances** - si vous voulez effectuer le travail devant un cluster de calcul de machines virtuelles à l’aide d’une approche « alternées », vous pouvez configurer un programme d’équilibrage de charge Azure avec l’équilibrage de charge des règles en conséquence. Vous pouvez également définir des sondes pour vérifier votre application est en cours d’exécution en exécutant la commande ping ports avec un chemin d’accès de protocole, intervalle et demande spécifié.

    Voici un exemple qui crée un ensemble d’échelle machine virtuelle de machines virtuelles serveur web IIS en cours d’exécution et utilise un équilibrage de charge pour équilibrer la charge reçoit chaque machine virtuelle. Elle utilise également le protocole HTTP pour commande ping sur une URL spécifique sur chaque ordinateur virtuel : [https://github.com/gbowerman/azure-myriad/blob/master/vmss-win-iis-vnet-storage-lb.json](https://github.com/gbowerman/azure-myriad/blob/master/vmss-win-iis-vnet-storage-lb.json) - examiner le type de ressource Microsoft.Network/loadBalancers et les networkProfile et extensionProfile dans la virtualMachineScaleSet.

 - **Déploiement d’une échelle de machine virtuelle définie comme un cluster de calcul dans un gestionnaire de cluster PaaS** - échelle machine virtuelle jeux sont parfois décrits comme un rôle de collaborateur nouvelle génération. Il est une description valide mais il s’exécute également le risque d’échelle confusion ensemble de fonctionnalités avec les fonctionnalités de rôle de collaborateur PaaS v1. Dans un sens échelle machine virtuelle jeux de fournissent un véritable « rôle de travail » ou une ressource de travail, car ils fournissent une ressource cluster GRG qui est indépendante, plateforme/runtime personnalisables et s’intègre à Azure Gestionnaire de ressources IaaS.

    Un rôle de collaborateur v1 PaaS, tandis que limité en termes de prise en charge de la plateforme/runtime (images de plateforme Windows) inclut également des services tels que permutation VIP, les paramètres de mise à niveau configurables, runtime/déploiement des paramètres de l’application qui ne sont pas _encore_ disponibles dans la machine virtuelle échelle jeux, ou seront remises par d’autres services de niveau supérieur PaaS comme tissu de Service. Ceci vous pouvez examiner machine virtuelle échelle des ensembles comme une infrastructure qui prend en charge PaaS. Par exemple Solutions PaaS tels que tissu de Service ou responsables cluster comme Mesos pouvez créer en haut de la machine virtuelle échelle jeux en tant que format SVG cluster calque.

    Voici un exemple d’un cluster Mesos qui déploie une échelle machine virtuelle définie dans le même VNET une instance autonome machine virtuelle. La version autonome de machine virtuelle est un masque de Mesos, et le jeu d’échelle de machine virtuelle représente un ensemble de nœuds esclave : [https://github.com/gbowerman/azure-myriad/blob/master/mesos-vmss-simple-cluster.json](https://github.com/gbowerman/azure-myriad/blob/master/mesos-vmss-simple-cluster.json). Les versions ultérieures du [Service de conteneur Azure](https://azure.microsoft.com/blog/azure-container-service-now-and-the-future/) déployez plus complexe/renforcée les versions de ce scénario basée sur les ensembles d’échelle machine virtuelle.

## <a name="vm-scale-set-performance-and-scale-guidance"></a>Machine virtuelle échelle jeu de performances et les proportions de recommandations

- Au cours de la version d’évaluation, ne créez pas plus de 500 machines virtuelles dans plusieurs jeux d’échelle machine virtuelle à la fois.
- Planifier pour les pas plus de 40 machines virtuelles par compte de stockage.
- Aérer les premières lettres du nom de compte de stockage autant que possible.  Les exemples de modèles VMSS dans les [modèles de démarrage rapide Azure](https://github.com/Azure/azure-quickstart-templates/) donnez des exemples de cette procédure.
- Si vous utilisez machines virtuelles personnalisés, planifiez pas plus de 40 machines virtuelles par jeu d’échelle machine virtuelle, dans un compte de stockage unique.  Vous devez l’image préalable copié dans le compte de stockage avant de vous lancer machine virtuelle échelle jeu de déploiement. Consultez le Forum aux questions pour plus d’informations.
- Planifier pour les pas plus de 2 048 machines virtuelles par VNET.  Cette limite est augmentée à l’avenir.
- Le nombre d’ordinateurs virtuels vous pouvez créer est limité par votre quota core cluster dans n’importe quelle région. Vous devrez contacter le support technique pour accroître votre limite de quota cluster augmentée même si vous avez une limite haute de cœurs pour une utilisation avec les services en nuage ou IaaS v1 aujourd'hui. Pour rechercher votre quota, vous pouvez exécuter la commande suivante Azure infrastructure du langage commun : _machine virtuelle azure - utilisation de la liste_et la commande PowerShell suivante : _Get-AzureRmVMUsage_ (si à l’aide d’une version de PowerShell ci-dessous 1.0 utilisez _Get-AzureVMUsage_).

## <a name="vm-scale-set-frequently-asked-questions"></a>Échelle de machine virtuelle définie Forum aux questions

**QUESTIONS/RÉPONSES.** Machines virtuelles combien vous avez dans un jeu d’échelle machine virtuelle ?

**A.** 100 si vous utilisez des images de plateforme qui peuvent être réparties entre plusieurs comptes de stockage. Si vous utilisez des images personnalisées, jusqu'à 40, étant donné que des images personnalisées sont limités à un compte de stockage unique lors de l’aperçu.

**Q** quels autres limites de ressources existent pour les jeux d’échelle machine virtuelle ?

**A.** Vous êtes limité à la création de pas plus de 500 machines virtuelles dans plusieurs jeux d’échelle par région pendant la période d’aperçu. Existant [limites de Service d’abonnement Azure /](../articles/azure-subscription-service-limits.md) appliquer.

**QUESTIONS/RÉPONSES.** Sont les données disques pris en charge dans les ensembles d’échelle machine virtuelle ?

**A.** Pas dans la version initiale. Les options disponibles pour le stockage de données sont :

- Fichiers Azure (lecteurs PME partagé)

- Lecteur du système d’exploitation

- Lecteur TEMP (local, ne pas sauvegardé par stockage Azure)

- Service de données externes (par exemple, base de données distante, tables Azure, des objets BLOB Azure)

**QUESTIONS/RÉPONSES.** Les régions Azure prend en charge pour les jeux d’échelle machine virtuelle ?

**A.** N’importe quelle région qui prend en charge le Gestionnaire de ressources Azure prend en charge les jeux d’échelle machine virtuelle.

**QUESTIONS/RÉPONSES.** Comment créer une échelle de machine virtuelle jeux à l’aide d’une image personnalisée ?

**A.** Laissez la propriété vhdContainers vide, par exemple :

    "storageProfile": {
        "osDisk": {
            "name": "vmssosdisk",
            "caching": "ReadOnly",
            "createOption": "FromImage",
            "image": {
                "uri": [https://mycustomimage.blob.core.windows.net/system/Microsoft.Compute/Images/mytemplates/template-osDisk.vhd](https://mycustomimage.blob.core.windows.net/system/Microsoft.Compute/Images/mytemplates/template-osDisk.vhd)
            },
            "osType": "Windows"
        }
    },


**QUESTIONS/RÉPONSES.** Si j’ai réduisez mon échelle machine virtuelle définie capacité de 20 15, machines virtuelles sont supprimées ?

**A.** Machines virtuelles sont supprimés à partir de l’échelle de la valeur uniformément sur les domaines mise à niveau et défaillance optimiser la disponibilité.

**QUESTIONS/RÉPONSES.** Qu’en est-il il si je puis augmentez la capacité de 15 à 18 ?

**A.** Si vous augmentez 18, machines virtuelles index 15, 16, 17 est créée. Dans les deux cas, les ordinateurs virtuels sont réparties sur FDs et UDs.

**QUESTIONS/RÉPONSES.** Lorsque vous utilisez plusieurs extensions dans un jeu d’échelle machine virtuelle, puis-je appliquer une séquence d’exécution ?

**A.** Pas directement, mais pour l’extension customScript, votre script peut attendre une autre extension Terminer (par exemple en contrôlant l’extension journal - voir [https://github.com/Azure/azure-quickstart-templates/blob/master/201-vmss-lapstack-autoscale/install\_lap.sh](https://github.com/Azure/azure-quickstart-templates/blob/master/201-vmss-lapstack-autoscale/install_lap.sh)).

**QUESTIONS/RÉPONSES.** Machine virtuelle échelle jeux fonctionnent avec des jeux de disponibilité Azure ?

**A.** Oui. Un ensemble d’échelle machine virtuelle est une disponibilité implicite définir avec 3 FDs et 5 UDs. Vous n’avez pas besoin de configurer rien sous virtualMachineProfile. Dans les futures versions, jeux d’échelle machine virtuelle est susceptibles de s’étalent sur plusieurs clients, mais pour maintenant définie une échelle est un ensemble de disponibilité unique.

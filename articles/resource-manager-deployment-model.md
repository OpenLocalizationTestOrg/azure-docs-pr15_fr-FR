<properties
   pageTitle="Gestionnaire de ressources et classique déploiement | Microsoft Azure"
   description="Décrit les différences entre le modèle de déploiement du Gestionnaire de ressources et de l’interface classique (ou gestion des services) modèle de déploiement."
   services="azure-resource-manager"
   documentationCenter="na"
   authors="tfitzmac"
   manager="timlt"
   editor="tysonn"/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/27/2016"
   ms.author="tomfitz"/>

# <a name="azure-resource-manager-vs-classic-deployment-understand-deployment-models-and-the-state-of-your-resources"></a>Le Gestionnaire de ressources Azure et déploiement classique : comprendre les modèles de déploiement et l’état de vos ressources

Dans cette rubrique, vous en savoir plus sur le Gestionnaire de ressources Azure et les modèles de déploiement classique, l’état de vos ressources, et pourquoi vos ressources ont été déployés avec un ou l’autre. Le Gestionnaire de ressources et les modèles de déploiement classique représentent deux manières de déploiement et la gestion de vos solutions Azure. Vous travaillez avec eux via deux ensembles d’API différentes, et les ressources déployées peuvent contenir des différences importantes. Les deux modèles ne sont pas entièrement compatibles entre eux. Cette rubrique décrit ces différences.

Pour simplifier le déploiement et la gestion des ressources, Microsoft recommande d’utiliser le Gestionnaire de ressources pour toutes les nouvelles ressources. Si possible, Microsoft recommande que vous redéployez existant par le biais du Gestionnaire de ressources.

Si vous êtes novice au Gestionnaire de ressources, vous souhaiterez peut-être passez en revue la terminologie définie dans [vue d’ensemble du Gestionnaire de ressources Azure](azure-resource-manager/resource-group-overview.md).

## <a name="history-of-the-deployment-models"></a>Historique des modèles de déploiement

Azure initialement fourni uniquement le modèle de déploiement classique. Dans ce modèle, chaque ressource existe indépendamment ; aucun moyen de regrouper les ressources connexes s’est produite. À la place, vous deviez manuellement les ressources constitués de votre solution ou votre application de suivre et n’oubliez pas de les gérer dans une approche coordonnée. Pour déployer une solution, que vous deviez créer chaque ressource individuellement via le portail classique ou créer un script déployées toutes les ressources dans l’ordre correct. Pour supprimer une solution, que vous deviez supprimer chaque ressource individuellement. Vous ne pouvez pas facilement appliquer et mettre à jour les stratégies de contrôle d’accès pour les ressources connexes. Enfin, vous ne pouvez pas appliquer des balises à des ressources pour les étiqueter avec des termes qui vous aideront à contrôler vos ressources et gérer la facturation.

En 2014, Azure introduite Gestionnaire de ressources qui a ajouté le concept d’un groupe de ressources. Un groupe de ressources est un conteneur pour les ressources qui partagent un cycle de vie courant. Le modèle de déploiement du Gestionnaire de ressources présente plusieurs avantages :

- Vous pouvez déployer, gérer et surveiller tous les services de votre solution en tant que groupe, plutôt que la gestion de ces services individuellement.
- Vous pouvez à plusieurs reprises déployer votre solution tout au long de son cycle de vie et confiance que vos ressources sont déployés dans un état cohérent.
- Vous pouvez appliquer un contrôle d’accès à toutes les ressources dans votre groupe de ressources, et ces stratégies sont appliquées automatiquement quand de nouvelles ressources sont ajoutés au groupe de ressources.
- Vous pouvez appliquer des balises à des ressources pour organiser logiquement toutes les ressources dans votre abonnement.
- Vous pouvez utiliser la Notation JSON (JavaScript Object) pour définir l’infrastructure de votre solution. Le fichier JSON est appelé un modèle de gestionnaire de ressources.
- Vous pouvez définir les dépendances entre les ressources afin qu’ils sont déployés dans l’ordre correct.

Lorsque le Gestionnaire de ressources a été ajouté, toutes les ressources ont été ajoutés rétroactivement aux groupes de ressources par défaut. Si vous créez une ressource au cours du déploiement classique maintenant, la ressource est automatiquement créée au sein d’un groupe de ressources par défaut pour ce service, même si vous n’avez pas spécifié ce groupe de ressources au moment du déploiement. Toutefois, simplement existant au sein d’un groupe de ressources pas signifie que la ressource a été convertie au modèle de gestionnaire de ressources. Nous allons aborder la manière dont chaque service gère les modèles de deux déploiement dans la section suivante. 

## <a name="understand-support-for-the-models"></a>Comprendre la prise en charge pour les modèles 

Lorsque vous choisissez le modèle de déploiement à utiliser pour vos ressources, il existe trois scénarios importants :

1. Le service prend en charge le Gestionnaire de ressources et fournit un seul type.
2. Le service prend en charge le Gestionnaire de ressources mais propose deux types de - une pour le Gestionnaire de ressources et l’autre pour classique. Ce scénario s’applique uniquement aux machines virtuelles, les comptes de stockage et les réseaux virtuels.
3. Le service ne prend pas en charge le Gestionnaire de ressources.

Pour découvrir si un service prend en charge le Gestionnaire de ressources, consultez [le Gestionnaire de ressources pris en charge les fournisseurs](resource-manager-supported-services.md).

Si le service que vous souhaitez utiliser ne prend pas en charge Gestionnaire de ressources, vous devez continuer à l’aide de déploiement classique.

Si le service prend en charge le Gestionnaire de ressources et qui **n’est pas** une machine virtuelle, compte de stockage ou réseau virtuel, vous pouvez utiliser le Gestionnaire de ressources sans difficulté.

Pour machines virtuelles, comptes de stockage et réseaux virtuels, si la ressource a été créée par le biais déploiement classique, vous devez continuer à employer dessus à travers des opérations classiques. Si la machine virtuelle, un compte de stockage ou un réseau virtuel a été créé au cours du Gestionnaire de ressources du déploiement, vous devez continuer à l’aide du Gestionnaire de ressources opérations. Cette distinction accessible confusion lorsque votre abonnement contient un mélange de ressources créé via le Gestionnaire de ressources et de déploiement classique. Cette combinaison de ressources peut créer des résultats inattendus, car les ressources ne prennent pas en charge les mêmes opérations.

Dans certains cas, une commande Gestionnaire de ressources pouvez récupérer les informations relatives à une ressource créée au cours du déploiement classique ou peut effectuer une tâche administrative, comme le déplacement d’une ressource classique à un autre groupe de ressources. Toutefois, ces cas ne doivent pas donner l’impression que le type prend en charge les opérations de gestionnaire de ressources. Par exemple, supposons que vous avez un groupe de ressources qui contient une machine virtuelle qui a été créée avec le déploiement classique. Si vous exécutez la commande Gestionnaire de ressources PowerShell suivante :

    Get-AzureRmResource -ResourceGroupName ExampleGroup -ResourceType Microsoft.ClassicCompute/virtualMachines

Elle renvoie la machine virtuelle :
    
    Name              : ExampleClassicVM
    ResourceId        : /subscriptions/{guid}/resourceGroups/ExampleGroup/providers/Microsoft.ClassicCompute/virtualMachines/ExampleClassicVM
    ResourceName      : ExampleClassicVM
    ResourceType      : Microsoft.ClassicCompute/virtualMachines
    ResourceGroupName : ExampleGroup
    Location          : westus
    SubscriptionId    : {guid}

Toutefois, Gestionnaire de ressources d’applet de commande **Get-AzureRmVM** retourne uniquement les ordinateurs virtuels déployés par le biais du Gestionnaire de ressources. La commande suivante ne renvoie pas la machine virtuelle créée par le biais déploiement classique.

    Get-AzureRmVM -ResourceGroupName ExampleGroup

Uniquement les ressources créées via des balises de prise en charge le Gestionnaire de ressources. Vous ne pouvez pas appliquer des balises aux ressources classiques.

## <a name="resource-manager-characteristics"></a>Caractéristiques du Gestionnaire de ressources

Pour vous aider à comprendre les deux modèles, passons en revue les caractéristiques de types de gestionnaire de ressources :

- Créé à partir du [portail Azure](https://portal.azure.com/).

     ![Portail Azure](./media/resource-manager-deployment-model/portal.png)

     Pour le calcul, le stockage et ressources réseau, vous avez la possibilité de l’aide du Gestionnaire de ressources ou classique déploiement. Sélectionnez **Le Gestionnaire de ressources**.

     ![Gestionnaire de ressources du déploiement](./media/resource-manager-deployment-model/select-resource-manager.png)

- Créé avec la version du Gestionnaire de ressources des applets de commande PowerShell Azure. Ces commandes ont le format *Verbe AzureRmNoun*.

        New-AzureRmResourceGroupDeployment

- Créé via l' [REST API de gestionnaire de ressources Azure](https://msdn.microsoft.com/library/azure/dn790568.aspx) pour les opérations de reste.

- Créé aide Azure s’exécutent dans le mode **processeur** .

        azure config mode arm
        azure group deployment create 

- Le type de ressource n’inclut pas **(classique)** dans le nom. L’image suivante montre le type en tant que **compte de stockage**.

    ![dans le navigateur](./media/resource-manager-deployment-model/resource-manager-type.png)

## <a name="classic-deployment-characteristics"></a>Caractéristiques de déploiement classique

Vous pouvez également connaître le modèle de déploiement classique que le modèle de gestion des services.

Ressources créées dans le modèle de déploiement classique partagent les caractéristiques suivantes :

- Créé à partir du [portail classique](https://manage.windowsazure.com)

     ![Portail classique](./media/resource-manager-deployment-model/classic-portal.png)

     Ou, le portail Azure et que vous spécifiez un déploiement **classique** (pour le cluster, le stockage et mise en réseau).

     ![Déploiement classique](./media/resource-manager-deployment-model/select-classic.png)

- Créé via la version de gestion des services des applets de commande PowerShell Azure. Ces noms de commandes ont le format *Verbe AzureNoun*.

        New-AzureVM 

- Créé via l' [API REST de gestion des services](https://msdn.microsoft.com/library/azure/ee460799.aspx) pour les opérations de reste.
- Créé aide Azure s’exécuter en mode **asm** .

        azure config mode asm
        azure vm create 

- Le type de ressource inclut **(classique)** dans le nom. L’image suivante montre le type de compte de **stockage (classique)**.

    ![type classique](./media/resource-manager-deployment-model/classic-type.png)

Vous pouvez utiliser le portail Azure pour gérer les ressources qui ont été créées au cours du déploiement classique.

## <a name="changes-for-compute-network-and-storage"></a>Modifications des cluster, de réseau et de stockage

Le diagramme suivant affiche les ressources informatiques, de réseau et de stockage déployés par le biais du Gestionnaire de ressources.

![Architecture du Gestionnaire de ressources](./media/virtual-machines-azure-resource-manager-architecture/arm_arch3.png)

Notez les relations entre les ressources suivantes :

- Toutes les ressources existent au sein d’un groupe de ressources.
- La machine virtuelle varie selon un compte de stockage spécifiques défini dans le fournisseur de ressources de stockage pour stocker ses disques dans le stockage blob (obligatoire).
- La machine virtuelle fait référence à une carte réseau spécifique définie dans le fournisseur de ressources réseau (obligatoire) et une disponibilité définies dans le fournisseur de ressources cluster (facultatif).
- La carte réseau fait référence à adresse IP la machine virtuelle (obligatoire), le sous réseau du réseau virtuel pour la machine virtuelle (obligatoire) et d’un groupe de sécurité réseau (facultatif).
- Le sous-réseau au sein d’un réseau virtuel fait référence à un groupe de sécurité réseau (facultatif).
- L’instance d’équilibrage de charge fait référence à la liste principale d’adresses IP qui incluent la carte réseau d’une machine virtuelle (facultative) et fait référence à une charge équilibrage publique ou privée adresse IP (facultative).

Voici les éléments et leurs relations pour le déploiement classique :

![architecture classique](./media/virtual-machines-azure-resource-manager-architecture/arm_arch1.png)

La solution classique pour héberger une machine virtuelle inclut :

- Un service cloud requis qui sert de conteneur pour l’hébergement des machines virtuelles (cluster). Machines virtuelles sont fournis automatiquement avec une carte réseau (carte réseau) et une adresse IP affectée par Azure. En outre, le service cloud contient une instance d’équilibrage de charge externe, une adresse IP publique et points de terminaison par défaut pour permettre à distance Bureau et remote PowerShell le trafic de machines virtuelles fonctionnant sous Windows et SSH (Secure Shell) le trafic de machines virtuelles basées sur Linux.
- Un compte de stockage requis qui stocke les disques durs virtuels pour une machine virtuelle, y compris le système d’exploitation, les données temporaires et d’autres disques (stockage).
- Un réseau virtuel facultatif qui se comporte comme un conteneur supplémentaire, dans laquelle vous pouvez créer une structure de sous-réseaux et désigner le sous-réseau sur lequel se trouve la machine virtuelle (réseau).

Le tableau suivant décrit les modifications apportées à l’interagissent des fournisseurs de ressources informatiques, de réseau et de stockage :

 Élément | Classique | Gestionnaire de ressources
 ---|---|---
| Service cloud pour les Machines virtuelles |  Service de nuage a été un conteneur d’ordinateurs virtuels qui obligatoire disponibilité de la plate-forme et l’équilibrage de charge. | Service de nuage n’est plus un objet est requis pour la création d’une Machine virtuelle à l’aide du nouveau modèle. |
| Réseaux virtuels | Un réseau virtuel est facultatif pour la machine virtuelle. Si inclus, le réseau virtuel ne peut pas être déployé avec le Gestionnaire de ressources. | Machine virtuelle nécessite une connexion réseau virtuel qui a été déployé avec le Gestionnaire de ressources. |
| Comptes de stockage | La machine virtuelle nécessite un compte de stockage qui stocke les disques durs virtuels pour le système d’exploitation, disques temporaires et d’autres données. | La machine virtuelle nécessite un compte de stockage pour stocker ses disques dans le stockage blob. |
| Jeux de disponibilité | Disponibilité de la plateforme a été indiquée en configurant le même « AvailabilitySetName » sur les ordinateurs virtuels. Le nombre maximal de domaines d’erreur est 2. | Jeu de disponibilité est une ressource exposée par Microsoft.Compute fournisseur. Machines virtuelles qui nécessitent une disponibilité élevée doit être inclus dans l’ensemble de disponibilité. Le nombre maximal de domaines d’erreur devient 3. |
| Affinités | Affinités ont été nécessaires pour créer des réseaux virtuels. Toutefois, avec l’introduction de réseaux virtuels régionaux, cela n’était pas requise plus. |Pour simplifier, le concept affinités n’existe pas dans les API exposées par le biais du Gestionnaire de ressources Azure. |
| Équilibrage de charge    | Création d’un Service Cloud fournit un équilibrage de charge implicite pour les ordinateurs virtuels déployés. | L’équilibrage de charge est une ressource exposée par le fournisseur Microsoft.Network. L’interface réseau principale des Machines virtuelles doit être équilibrage de charge doit faire référence à l’équilibrage de charge. Programmes d’équilibrage de charge peuvent être internes ou externes. Une instance d’équilibrage de charge fait référence à la liste principale d’adresses IP qui incluent la carte réseau d’une machine virtuelle (facultative) et fait référence à une charge équilibrage publique ou privée adresse IP (facultative). [En savoir plus.](../articles/resource-groups-networking.md) |
|Adresse IP virtuelle | Les Services en nuage obtenir un VIP par défaut (adresse IP virtuelle) lorsqu’une machine virtuelle est ajoutée à un service cloud. L’adresse IP virtuelle est l’adresse associée à l’équilibrage de charge implicite.    | Adresse IP publique est une ressource exposée par le fournisseur Microsoft.Network. Adresse IP publique peut être (réservé) statique ou dynamique. Adresses IP Public dynamique peuvent être affectée à un équilibrage de charge. Adresses IP public peut être sécurisé à l’aide de groupes de sécurité. |
|Adresse IP réservée|   Vous pouvez réserver une adresse IP dans Azure et l’associer à un Service Cloud pour vous assurer que l’adresse IP est pense-bête.   | Adresse IP publique peuvent être créée en mode « Statique » et il offre la même fonctionnalité en tant que « Adresse IP réservée ». Adresses IP Public statiques peuvent uniquement être affectée à un équilibrage de charge immédiatement. |
|Adresse IP publique (PIP) par machine virtuelle | Adresses IP publiques peuvent également être associés à une machine virtuelle directement. | Adresse IP publique est une ressource exposée par le fournisseur Microsoft.Network. Adresse IP publique peut être (réservé) statique ou dynamique. Toutefois, les adresses IP Public dynamique uniquement peuvent être affectée à une Interface réseau pour obtenir une adresse IP publique par machine virtuelle immédiatement. |
|Points de terminaison| Points de terminaison d’entrée nécessaires doit être configuré sur une Machine virtuelle être ouverts la connectivité pour certains ports. Un des modes courantes de connexion à des machines virtuelles terminés en configurant des points de terminaison d’entrée. | Règles de trafic entrant NAT peut être configurés dans les programmes d’équilibrage de charge pour obtenir la même fonctionnalité de l’activation de points de terminaison sur des ports spécifiques pour vous connecter aux ordinateurs virtuels. |
|Nom de DNS| Un service cloud obtenez un nom DNS globalement unique implicite. Par exemple : `mycoffeeshop.cloudapp.net`. | Les noms de DNS sont des paramètres facultatifs qui peuvent être spécifiés sur une ressource adresse IP publique. Le nom de domaine complet est au format suivant : `<domainlabel>.<region>.cloudapp.azure.com`. |
|Interfaces réseau | Principal et secondaire Interface réseau et à ses propriétés ont été définies comme configuration du réseau d’un ordinateur virtuel. | Interface réseau est une ressource exposée par Microsoft.Network fournisseur. Le cycle de vie de l’Interface réseau n’est pas liée à une Machine virtuelle. Il fait référence à adresse IP la machine virtuelle (obligatoire), le sous réseau du réseau virtuel pour la machine virtuelle (obligatoire) et d’un groupe de sécurité réseau (facultatif). |

Pour en savoir plus sur la connexion des réseaux virtuels à partir de modèles de déploiement différentes, voir [se connecter des réseaux virtuels à partir de modèles de déploiement différentes dans le portail](./vpn-gateway/vpn-gateway-connect-different-deployment-models-portal.md).

## <a name="migrate-from-classic-to-resource-manager"></a>Migrez classique au Gestionnaire de ressources

Si vous êtes prêt à migrer vos ressources de déploiement classique au Gestionnaire de ressources du déploiement, voir :

1. [Technique approfondie sur la plateforme prise en charge la migration de classique au Gestionnaire de ressources Azure](./virtual-machines/virtual-machines-windows-migration-classic-resource-manager-deep-dive.md)
2. [Migration de plates-formes prises en charge des ressources IaaS de classique au Gestionnaire de ressources Azure](./virtual-machines/virtual-machines-windows-migration-classic-resource-manager.md)
3. [Migrez ressources IaaS classique au Gestionnaire de ressources Azure à l’aide de PowerShell Azure](./virtual-machines/virtual-machines-windows-ps-migration-classic-resource-manager.md)
4. [Migrez ressources IaaS classique au Gestionnaire de ressources Azure à l’aide de Azure infrastructure du langage commun](./virtual-machines/virtual-machines-linux-cli-migration-classic-resource-manager.md)

## <a name="frequently-asked-questions"></a>Forum aux Questions

**Puis-je créer une machine virtuelle à l’aide de gestionnaire de ressources Azure déployer dans un compte de stockage créés à l’aide de déploiement classique ou le réseau virtuel ?**

Ce n’est pas pris en charge. Vous ne pouvez pas utiliser le Gestionnaire de ressources Azure pour déployer une machine virtuelle dans un réseau virtuel qui a été créé à l’aide de déploiement classique.

**Puis-je créer un ordinateur virtuel en utilisant le Gestionnaire des ressources à partir d’une image de l’utilisateur qui a été créée à l’aide de l’API de gestion de Service Azure Azure ?**

Ce n’est pas pris en charge. Toutefois, vous pouvez copier ces fichiers à partir d’un compte de stockage qui a été créé à l’aide de l’API de gestion de Service et les ajouter à un nouveau compte créé par le biais du Gestionnaire de ressources Azure.

**Quel est l’impact sur le quota pour mon abonnement ?**

Les quotas pour machines virtuelles, des réseaux virtuels et des comptes de stockage créés par le biais du Gestionnaire de ressources Azure sont distinguent des autres quotas. Chaque abonnement Obtient les quotas pour créer les ressources à l’aide des nouvelle API. Vous pouvez en savoir plus sur les quotas supplémentaires [ici](../articles/azure-subscription-service-limits.md).

**Puis-je continuer à utiliser mes scripts automatisés de mise en service des machines virtuelles réseaux virtuels et des comptes de stockage via les API du Gestionnaire de ressources ?**

Tous les automation et les scripts que vous avez créé continuent à fonctionner pour les ordinateurs virtuels existants, réseaux virtuels créés sous le mode de gestion des services Azure. Toutefois, les scripts doivent être mis à jour pour utiliser le nouveau schéma pour créer les mêmes ressources par le biais du mode directeur des ressources.

**Où puis-je trouver des exemples de modèles de gestionnaire de ressources Azure ?**

Un ensemble complet de modèles starter vous pouvez trouver sur les [Modèles de démarrage rapide pour le Gestionnaire de ressources Azure](https://azure.microsoft.com/documentation/templates/).

## <a name="next-steps"></a>Étapes suivantes

- Pour connaître les étapes de la création de modèle qui définit une machine virtuelle, le compte de stockage et le réseau virtuel, voir [Présentation du modèle Gestionnaire de ressources](resource-manager-template-walkthrough.md).
- Pour afficher les commandes pour le déploiement d’un modèle, voir [déployer une application avec le Gestionnaire de ressources Azure modèle](resource-group-template-deploy.md).

<properties
    pageTitle="Vue d’ensemble des Machines virtuelles Windows | Microsoft Azure"
    description="Découvrez comment créer et gérer des machines virtuelles de Windows dans Azure."
    services="virtual-machines-windows"
    documentationCenter=""
    authors="davidmu1"
    manager="timlt"
    editor="tysonn"
    tags="azure-resource-manager,azure-service-management"/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="10/20/2016"
    ms.author="davidmu"/>

# <a name="overview-of-windows-virtual-machines-in-azure"></a>Vue d’ensemble des machines virtuelles de Windows dans Azure

Machines virtuelles Azure (machine virtuelle) est un des différents types d' [à la demande, scalable ressources informatiques](../app-service-web/choose-web-site-cloud-service-vm.md) qui Azure offre. En règle générale, vous choisissez une machine virtuelle lorsque vous avez besoin de davantage de contrôle sur l’environnement informatique de proposer d’autres choix. Cet article donne des informations sur ce que vous devez prendre en compte avant de créer une machine virtuelle, comment créer et la gestion.

Une machine virtuelle Azure vous offre la possibilité de virtualisation sans avoir à acheter et mettre à jour le matériel qui s’exécute. Toutefois, vous devez mettre à jour de la machine virtuelle en effectuant des tâches, telles que la configuration, correctifs et l’installation du logiciel qui s’exécute sur celui-ci.

Machines virtuelles Azure peut être utilisés de différentes manières. Quelques exemples sont :

- **Développement et test** – machines virtuelles Azure offre un jeu d’enfant créer un ordinateur avec des configurations spécifiques requis pour le code et tester une application.
- **Applications dans le cloud** –, car la demande pour votre application peut varier, il peut être utile économique à exécuter sur un ordinateur virtuel dans Azure. Vous payez pour les machines virtuelles supplémentaires lorsque vous avez besoin et les arrêtez lorsque vous n’avez pas.
- **Centre de données étendues** – machines virtuelles dans un réseau virtuel Azure facilement pouvant être connectés au réseau de votre organisation.

Le nombre de machines virtuelles que votre application utilise peut évoluer vers le haut et déconnecter tout ce qui est nécessaire pour répondre à vos besoins.

## <a name="what-do-i-need-to-think-about-before-creating-a-vm"></a>Que dois-je envisager avant de créer une machine virtuelle ?

Il existe toujours une multitude de [conception](virtual-machines-windows-infrastructure-virtual-machine-guidelines.md) lorsque vous créez une infrastructure d’application dans Azure. Ces aspects d’un ordinateur virtuel sont importantes à envisager avant de commencer :

- Les noms des ressources de votre application
- L’emplacement où se trouvent les ressources
- La taille de la machine virtuelle
- Le nombre maximal de machines virtuelles pouvant être créées
- Le système d’exploitation qui s’exécute à la machine virtuelle
- La configuration de la machine virtuelle après son démarrage
- Les ressources associées nécessitant la machine virtuelle

### <a name="naming"></a>Affectation des noms

Une machine virtuelle a un [nom](virtual-machines-windows-infrastructure-naming-guidelines.md) qui lui est assignée et il a un nom d’ordinateur configuré dans le cadre du système d’exploitation. Le nom d’un ordinateur virtuel peut être jusqu'à 15 caractères.

Si vous utilisez Azure pour créer le disque système d’exploitation, le nom d’ordinateur et le nom de la machine virtuelle sont identiques. Si vous [Téléchargez et utiliser votre propre image](virtual-machines-windows-upload-image.md) qui contient un système d’exploitation précédemment configuré et utilisez-le pour créer une machine virtuelle, les noms peuvent être différents. Nous vous conseillons de lorsque vous téléchargez votre propre fichier image, réaliser le nom d’ordinateur dans le système d’exploitation et le même nom de la machine virtuelle.

### <a name="locations"></a>Emplacements

Toutes les ressources créées dans Azure sont réparties sur plusieurs [régions géographiques](https://azure.microsoft.com/regions/) du monde. En règle générale, la région est appelée **emplacement** lorsque vous créez une machine virtuelle. Pour une machine virtuelle, l’emplacement indique où les disques durs virtuels sont stockés.

Ce tableau présente certaines des façons, que vous pouvez obtenir une liste des emplacements disponibles.

| Méthode | Description |
| ------ | ----------- |
| Portail Azure | Sélectionnez un emplacement dans la liste lorsque vous créez une machine virtuelle. |
| PowerShell Azure | Utilisez la commande [Get-AzureRmLocation](https://msdn.microsoft.com/library/mt619449.aspx) . |
| API REST | Utiliser l’opération [d’emplacements de la liste](https://msdn.microsoft.com/library/dn790540.aspx) . |

### <a name="vm-size"></a>Taille de mémoire virtuelle

La [taille](virtual-machines-windows-sizes.md) de la machine virtuelle que vous utilisez est déterminé par la charge de travail que vous souhaitez exécuter. La taille que vous choisissez puis détermine certains facteurs tels que la capacité d’alimentation, la mémoire et stockage de traitement. Azure offre une grande variété de tailles pour prendre en charge de nombreux types d’utilisation.

Azure frais un [prix horaire](https://azure.microsoft.com/pricing/details/virtual-machines/windows/) basé sur le système d’exploitation et la taille de la machine virtuelle. Pour les heures partielles, frais Azure uniquement pour le nombre de minutes utilisée. Stockage est le prix et facturé séparément.

### <a name="vm-limits"></a>Limites de mémoire virtuelle

Votre abonnement a des [limites de quota](../azure-subscription-service-limits.md) par défaut en place pourrait avoir un impact sur le déploiement de nombreuses machines virtuelles pour votre projet. La limite actuelle sur une base par abonnement est 20 machines virtuelles par région. Limites peuvent être portées en déposant une demande d’assistance demandant une augmentation.

### <a name="operating-system-disks-and-images"></a>Images et des disques du système d’exploitation

Machines virtuelles permet de stocker leur système d’exploitation (OS) et les données [des disques durs virtuels (disques durs virtuels)](virtual-machines-windows-about-disks-vhds.md) . Disques durs virtuels sont également utilisés pour les images que vous pouvez choisir d’installer un système d’exploitation. 

Azure fournit de nombreuses [images marketplace](https://azure.microsoft.com/marketplace/virtual-machines/) à utiliser avec différentes versions et types de systèmes d’exploitation Windows Server. Images Marketplace sont identifiés par publisher image, offre, la référence (SKU) et version (généralement version spécifiée plus tard). 

Ce tableau présente quelques méthodes que vous pouvez trouver les informations relatives à une image.

| Méthode | Description |
| ------ | ----------- |
| Portail Azure | Les valeurs sont spécifiés automatiquement pour vous lorsque vous sélectionnez une image à utiliser. |
| PowerShell Azure | [Get-AzureRMVMImagePublisher](https://msdn.microsoft.com/library/mt603484.aspx) -emplacement « emplacement »<BR>[Get-AzureRMVMImageOffer](https://msdn.microsoft.com/library/mt603824.aspx) -emplacement « emplacement »-Publisher « éditeur »<BR>[Get-AzureRMVMImageSku](https://msdn.microsoft.com/library/mt619458.aspx) -emplacement « emplacement »-Publisher « éditeur »-offrent « offerName » |
| API REST | [Éditeurs d’image de la liste](https://msdn.microsoft.com/library/mt743702.aspx)<BR>[Liste des offres d’image](https://msdn.microsoft.com/library/mt743700.aspx)<BR>[Références SKU image de la liste](https://msdn.microsoft.com/library/mt743701.aspx) |

Vous pouvez choisir de [télécharger et utiliser votre propre image](virtual-machines-windows-upload-image.md) et lorsque vous effectuez, le nom de l’éditeur, offre et référence (SKU) ne sont pas utilisés.

### <a name="extensions"></a>Extensions

Machine virtuelle [extensions](virtual-machines-windows-extensions-features.md) accroître les fonctionnalités votre machine virtuelle via la configuration de déploiement de billet et tâches automatisées.

Ces tâches courantes peuvent être effectuées à l’aide des extensions :

- **Exécuter des scripts personnalisés** – l' [Extension de Script personnalisé](virtual-machines-windows-extensions-customscript.md) vous permet de configurer les charges de travail sur l’ordinateur virtuel en exécutant le script lors de la machine virtuelle est mis en service.
- **Déployer et gérer les configurations** – l' [Extension de Configuration d’état souhaité (PowerShell DSC)](virtual-machines-windows-extensions-dsc-overview.md) vous aide à définir DSC sur un ordinateur virtuel pour gérer les configurations et environnements.
- **Collecter les données de diagnostic** – l' [Extension de Diagnostics Azure](https://azure.microsoft.com/blog/windows-azure-virtual-machine-monitoring-with-wad-extension/) vous permet de configurer la machine virtuelle pour collecter les données de diagnostic peuvent être utilisées pour surveiller l’intégrité de votre application.

### <a name="related-resources"></a>Ressources connexes

Les ressources dans ce tableau sont utilisées par la machine virtuelle et doivent exister ou être créée en même temps que la machine virtuelle.

| Ressource | Obligatoire | Description |
| -------- | -------- | ----------- |
| [Groupe de ressources](../azure-resource-manager/resource-group-overview.md) | Oui | La machine virtuelle doit figurer dans un groupe de ressources. |
| [Compte de stockage](../storage/storage-create-storage-account.md) | Oui | La machine virtuelle doit du compte de stockage pour stocker ses disques durs virtuels. |
| [Réseau virtuel](../virtual-network/virtual-networks-overview.md) | Oui | La machine virtuelle doit être membre d’un réseau virtuel. |
| [Adresse IP publique](../virtual-network/virtual-network-ip-addresses-overview-arm.md) | N° | La machine virtuelle peut avoir une adresse IP publique est affectée à y accéder à distance. |
| [Interface réseau](../virtual-network/virtual-network-network-interface-overview.md) | Oui | La machine virtuelle doit l’interface réseau pour communiquer sur le réseau. |
| [Disques de données](virtual-machines-windows-attach-disk-portal.md) | N° | La machine virtuelle peut inclure des disques de données pour développer les capacités de stockage. |

## <a name="how-do-i-create-my-first-vm"></a>Comment créer mon première machine virtuelle ?

Vous devez sélectionner plusieurs options pour la création de votre ordinateur virtuel. Le choix que vous effectuez dépend de l’environnement. 

Le tableau suivant fournit des informations pour vous aider à commencer à créer votre machine virtuelle.

| Méthode | Article |
| ------ | ------- |
| Portail Azure | [Créer une machine virtuelle exécutant Windows à l’aide du portail](virtual-machines-windows-hero-tutorial.md) |
| Modèles | [Créer une machine virtuelle Windows avec un modèle de gestionnaire de ressources](virtual-machines-windows-ps-template.md) |
| PowerShell Azure | [Créer une machine virtuelle Windows à l’aide de PowerShell](virtual-machines-windows-ps-create.md) |
| Kits de développement logiciel client | [Déployer les ressources Azure à l’aide de c#](virtual-machines-windows-csharp.md) |
| API REST | [Créer ou mettre à jour une machine virtuelle](https://msdn.microsoft.com/library/mt163591.aspx) |

Vous espérons que ce problème survient jamais, mais il peut arriver que quelque chose en cas de problème. Si cette situation est le cas, examinez les informations dans le [Gestionnaire de ressources de résolution des problèmes de déploiement aide pour créer une machine virtuelle Windows dans Azure](virtual-machines-windows-troubleshoot-deployment-new-vm.md).

## <a name="how-do-i-manage-the-vm-that-i-created"></a>Comment gérer la machine virtuelle que j’ai créé ?

Machines virtuelles peuvent être gérés à l’aide d’un portail via un navigateur, les outils de ligne de commande avec prise en charge pour l’écriture de script, ou directement via des API. Certaines tâches de gestion classiques que vous pouvez effectuer sont obtention d’informations sur un ordinateur virtuel, se connecter à une machine virtuelle, gestion de la disponibilité et les sauvegardes.

### <a name="get-information-about-a-vm"></a>Obtenir des informations sur une machine virtuelle

Ce tableau vous présente certaines des façons dont vous pouvez obtenir des informations sur un ordinateur virtuel.

| Méthode | Description |
| ------ | ----------- |
| Portail Azure | Dans le menu concentrateur, cliquez sur **Machines virtuelles** , puis sélectionnez la machine virtuelle dans la liste. Sur la carte pour la machine virtuelle, vous avez accès à des informations générales, définissant les valeurs et les mesures de surveillance des mots clés. |
| PowerShell Azure | Pour plus d’informations sur l’utilisation de PowerShell pour gérer des ordinateurs virtuels, consultez [Gérer Azure virtuelles Machines en utilisant le Gestionnaire de ressources et PowerShell](virtual-machines-windows-ps-manage.md). |
| API REST | Utiliser l’opération [machine virtuelle obtenir des informations](https://msdn.microsoft.com/library/mt163682.aspx) pour obtenir des informations sur un ordinateur virtuel. |
| Kits de développement logiciel client | Pour plus d’informations sur l’utilisation de c# pour gérer des ordinateurs virtuels, consultez [Gérer Azure virtuelles Machines à l’aide de gestionnaire de ressources Azure et c#](virtual-machines-windows-csharp-manage.md). |

### <a name="log-on-to-the-vm"></a>Ouvrez une session sur l’ordinateur virtuel

Vous utilisez le bouton de connexion dans le portail Azure pour [Démarrer une session de bureau à distance (RDP)](virtual-machines-windows-connect-logon.md). Pouvant parfois rencontrer lorsque vous essayez d’utiliser une connexion à distance. Si cette situation est le cas, consultez les informations d’aide dans les [connexions de résoudre les problèmes de bureau à distance à une machine virtuelle Azure exécutant Windows](virtual-machines-windows-troubleshoot-rdp-connection.md).

### <a name="manage-availability"></a>Gérer la disponibilité

Il est important de comprendre comment [optimiser la disponibilité](virtual-machines-windows-manage-availability.md) de votre application. Cette configuration consiste à créer plusieurs ordinateurs virtuels pour vous assurer qu’au moins une est en cours d’exécution.

Afin que votre déploiement bénéficier de notre contrat de niveau de Service machine virtuelle 99.95, vous devez déployer deux ou plusieurs ordinateurs virtuels exécutant votre charge de travail à l’intérieur d’une [disponibilité définie](virtual-machines-windows-infrastructure-availability-sets-guidelines.md). Cette configuration garantit vos ordinateurs virtuels sont réparties sur plusieurs domaines d’erreur et sont déployés sur hosts avec windows de maintenance. Le total [Azure SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_0/) explique la disponibilité garantie d’Azure dans son ensemble.
 
### <a name="back-up-the-vm"></a>Sauvegarder la machine virtuelle
 
Un [Services de récupération de l’archivage sécurisé](../backup/backup-introduction-to-azure-backup.md) est utilisé pour protéger des données et des ressources dans les services Azure sauvegarde et restauration de Site Azure. Vous pouvez utiliser un archivage sécurisé de Services de récupération à [déployer et gérer des sauvegardes pour les machines virtuelles déployé le Gestionnaire de ressources à l’aide de PowerShell](../backup/backup-azure-vms-automation.md). 

## <a name="next-steps"></a>Étapes suivantes

- Si vous avez l’intention d’utiliser avec machines virtuelles Linux, recherchez-le sur [Azure et Linux](virtual-machines-linux-azure-overview.md).
- En savoir plus sur les instructions autour de la configuration de votre infrastructure de la [procédure pas à pas exemple Azure infrastructure](virtual-machines-windows-infrastructure-example.md).
- Veillez à que respecter les [Meilleures pratiques pour une machine virtuelle Windows sur Azure en cours d’exécution](virtual-machines-windows-guidance-compute-single-vm.md).



<properties
    pageTitle="Outils de la Communauté pour la gestion des services Azure à la migration Azure le Gestionnaire de ressources"
    description="Cet article répertorie les outils qui ont été fournis par la Communauté pour aider avec migration des ressources IaaS à partir de la gestion des services Azure dans la pile Azure le Gestionnaire de ressources."
    services="virtual-machines-windows"
    documentationCenter=""
    authors="singhkays"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/29/2016"
    ms.author="singhkay"/>

# <a name="community-tools-for-azure-service-management-to-azure-resource-manager-migration"></a>Outils de la Communauté pour la gestion des services Azure à la migration Azure le Gestionnaire de ressources

Cet article répertorie les outils qui ont été fournis par la Communauté pour aider avec migration des ressources IaaS à partir de la gestion des services Azure dans la pile Azure le Gestionnaire de ressources.

>[AZURE.NOTE]Ces outils ne sont pas prises en charge embauche par le Support Microsoft. Par conséquent, ils sont ouverts défini pour Github et nous sommes satisfaits accepter PRs pour solutions ou des scénarios supplémentaires. Pour signaler un problème, utilisez la fonctionnalité de problèmes Github.
>
> Migration avec ces outils va entraîner l’arrêt de votre Machine virtuelle classique. Si vous recherchez la migration des plates-formes prises en charge, visitez 
>
>- [Migration de plates-formes prises en charge des ressources IaaS de classique pile Azure le Gestionnaire de ressources](./virtual-machines-windows-migration-classic-resource-manager.md)
>- [Technique approfondie plateformes pris en charge la migration de classique au Gestionnaire de ressources Azure](./virtual-machines-windows-migration-classic-resource-manager-deep-dive.md)
>- [Migrez ressources IaaS classique au Gestionnaire de ressources Azure à l’aide de PowerShell Azure](./virtual-machines-windows-ps-migration-classic-resource-manager.md)

## <a name="asm2arm"></a>ASM2ARM

Il s’agit d’un module de script PowerShell pour la migration de pile de gestion des services Azure votre **seule** Machine virtuelle (machine virtuelle) dans le Gestionnaire de ressources Azure pile. 

[Créer un lien vers la documentation de l’outil](https://github.com/Azure/classic-iaas-resourcemanager-migration/tree/master/asm2arm)

## <a name="migaz"></a>migAz

migAz est une autre option pour migrer un ensemble complet de ressources Azure Service gestion IaaS aux ressources Azure Gestionnaire de ressources IaaS. La migration peut se produire dans le même abonnement ou entre différents abonnements et les types d’abonnements (ex : abonnements fournisseur).

[Créer un lien vers la documentation de l’outil](https://github.com/Azure/classic-iaas-resourcemanager-migration/tree/master/migaz)
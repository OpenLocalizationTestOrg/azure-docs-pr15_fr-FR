<properties
    pageTitle="Quelle est la récupération de Site ? | Microsoft Azure"
    description="Fournit une vue d’ensemble du service de récupération de Site Azure et résume les scénarios de déploiement."
    services="site-recovery"
    documentationCenter=""
    authors="rayne-wiselman"
    manager="cfreeman"
    editor=""/>

<tags
    ms.service="site-recovery"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="na"
    ms.workload="storage-backup-recovery"
    ms.date="10/13/2016"
    ms.author="raynew"/>

#  <a name="what-is-site-recovery"></a>Quelle est la récupération de Site ?

Bienvenue dans la récupération de Site Azure ! Cet article fournit un aperçu rapide du service de récupération de Site, et comment il contribue à votre entreprise.

Votre organisation a besoin d’une continuité des activités et la stratégie de rétablissement (BCDR) qui conserver des applications, les charges de travail et les données approuvés et disponible au cours des interruptions liées aux tâches planifiées et et récupère à des conditions de travail normal dès que possible. Récupération de site est un service Azure qui participe à cette stratégie.

Récupération de site orchestre réplication de charges de travail en cours d’exécution serveurs physiques en local et machines virtuelles. Vous pouvez répliquer serveurs et machines virtuelles à partir d’un centre de données principal dans le cloud (Azure), ou à un centre de données secondaire. Lorsque des défaillances se trouvent dans le site principal, vous basculer vers le site secondaire pour conserver les charges de travail et les applications accessibles et disponibles. Vous ne parvenez pas à votre emplacement principal lorsqu’elle retourne à la normale.

## <a name="site-recovery-in-the-azure-portal"></a>Récupération de site dans le portail Azure

Azure comporte deux différents [modèles de déploiement](../resource-manager-deployment-model.md) pour la création et utilisation des ressources. Le modèle Azure le Gestionnaire de ressources et le modèle de gestion des services classique. Azure également comporte deux portails – le [portail classique Azure](https://manage.windowsazure.com/) qui prend en charge le modèle de déploiement classique, le [portail Azure](https://portal.azure.com) avec prise en charge pour le standard et les modèles de gestionnaire de ressources.

- Récupération de site est disponible dans le portail classique et le portail Azure.
- Dans le portail Azure classique, vous pouvez prend en charge récupération de Site avec le modèle de gestion des services classique.
- Dans le portail Azure, vous pouvez prend en charge le modèle classique ou les déploiements du Gestionnaire de ressources. 

Les informations contenues dans cet article s’applique aux déploiements portails classiques et Azure. Différences sont indiquées le cas échéant.


## <a name="why-deploy-site-recovery"></a>Pourquoi déployer récupération de Site ?

Voici ce que récupération de Site peut faire pour votre entreprise :

- **Simplifier BCDR**— vous pouvez gérer la réplication, le basculement et récupération de plusieurs charges de travail dans un seul emplacement dans le portail Azure. Récupération de site orchestre réplication et basculement mais n’intercepter vos données d’application ou demandez à des informations.
- **Fournir la réplication flexible**— à l’aide de la récupération de Site, vous pouvez répliquer les charges de travail en cours d’exécution sur les ordinateurs virtuels Hyper-V pris en charge, machines virtuelles VMware et serveurs physiques Windows/Linux.
- **Faciliter la réplication des tests**— récupération de Site fournit basculement test pour prendre en charge des exercices de récupération d’urgence, sans toucher aux environnements de production.
- **Basculer et récupérer**— vous pouvez exécuter basculement prévisionnels pour les interruptions attendues avec une perte de données zéro ou non planifiée basculement avec perte de données minimale (selon la fréquence de réplication) pour incidents inattendus. Après le basculement, vous pouvez retour arrière à vos sites principales. Récupération de site fournit des plans de récupération comprenant des scripts et des classeurs automation Azure, afin que vous pouvez personnaliser le basculement et la restauration des applications à plusieurs niveaux.
- **Éliminer un centre de données secondaire**, vous pouvez répliquer les charges de travail à Azure, plutôt qu’à un site secondaire. Cela supprime le coût et la complexité de la gestion d’un centre de données secondaire. Données répliquées sont stockées dans le stockage Azure, avec tous les la résilience fournit. Machines virtuelles sont créées avec les données répliquées lors du basculement.
- **Intégrer avec les technologies BCDR existantes**— récupération de Site s’intègre à d’autres fonctionnalités BCDR. Par exemple, vous pouvez utiliser la récupération de Site pour protéger le système principal de SQL Server de charges de travail d’entreprise, notamment la prise en charge native pour SQL Server AlwaysOn pour gérer le basculement des groupes de disponibilité.

## <a name="what-can-i-replicate"></a>Que puis-je répliquer ?

Voici un résumé de ce que vous pouvez répliquer à l’aide de la récupération de Site.

![Local vers local](./media/site-recovery-overview/asr-overview-graphic.png)

**RÉPLIQUER** | **RÉPLIQUER VERS** 
---|---
Charges de travail en cours d’exécution sur machines virtuelles VMware en local | [Azure](site-recovery-vmware-to-azure-classic.md)<br/><br/> [Site secondaire](site-recovery-vmware-to-vmware.md)
Charges de travail en cours d’exécution sur les ordinateurs virtuels Hyper-V local géré dans nuages VMM  | [Azure](site-recovery-vmm-to-azure.md)<br/><br/> [Site secondaire](site-recovery-vmm-to-vmm.md) 
La charge de travail en cours d’exécution sur les ordinateurs virtuels Hyper-V local géré dans nuages VMM, avec le stockage SAN|  [Site secondaire](site-recovery-vmm-san.md)
Charges de travail en cours d’exécution sur les ordinateurs virtuels Hyper-V en local, sans VMM | [Azure](site-recovery-hyper-v-site-to-azure.md)
Charges de travail en cours d’exécution sur des serveurs Windows/Linux physiques en local | [Azure](site-recovery-vmware-to-azure-classic.md)<br/><br/> [Site secondaire](site-recovery-vmware-to-vmware.md)


## <a name="what-workloads-can-i-protect"></a>Les charges de travail puis-je protéger ?

Récupération de site permet d’application prenant en charge BCDR, afin que les charges de travail et les applications continuent à exécuter de façon cohérente lorsque défaillances se produisent. Récupération de site fournit :

- **Instantanés cohérents avec les applications**— Machines répliquent à l’aide des instantanés cohérents avec les applications, pour les applications simples ou à plusieurs niveaux. En plus de capturer des données de disque, instantanés cohérents avec les applications capture capture toutes les données en mémoire et toutes les transactions de processus.
- **Réplication près synchrone**— récupération de Site fournit la fréquence de réplication est aussi faible que 30 secondes pour Hyper-V et la réplication continue pour VMware.
- **Plans de récupération flexibles**, vous pouvez créer et personnaliser des plans de récupération avec des scripts externes et les actions à manuelles. Intégration avec les procédures opérationnelles Azure Automation permettent de récupérer une pile d’ensemble de l’application d’un simple clic.
- **Intégration à SQL Server AlwaysOn**: vous pouvez gérer le basculement des groupes de disponibilité dans les plans de récupération de récupération de Site.
- **Bibliothèque d’automatisation**, une bibliothèque d’automatisation Azure riche propose des scripts opérationnelle, spécifiques à l’application qui peuvent être téléchargés et intégrées à récupération de Site.
- **Gestion de réseau simple**: gestion de réseau avancée dans récupération de Site et Azure simplifie conditions d’application réseau, y compris réserver des adresses IP, la configuration des programmes d’équilibrage de charge et l’intégration Azure le trafic gestionnaire pour basculements efficace du réseau.


## <a name="next-steps"></a>Étapes suivantes

- En savoir plus dans [pouvez récupération de Site protéger les charges de travail ?](site-recovery-workload.md)
- En savoir plus sur l’architecture de récupération de Site dans [comment fonctionne la récupération de Site ?](site-recovery-components.md)
 

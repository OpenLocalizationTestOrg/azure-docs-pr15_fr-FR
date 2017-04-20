<properties
    pageTitle="Migrer machines virtuelles IaaS Azure à partir d’une région Azure vers un autre Site récupération | Microsoft Azure"
    description="Utiliser la récupération de Site Azure migrer des machines virtuelles IaaS Azure à partir d’une région Azure vers un autre."
    services="site-recovery"
    documentationCenter=""
    authors="rayne-wiselman"
    manager="jwhit"
    editor="tysonn"/>

<tags
    ms.service="site-recovery"
    ms.workload="backup-recovery"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/21/2016"
    ms.author="raynew"/>

#  <a name="migrate-azure-iaas-virtual-machines-between-azure-regions-with-azure-site-recovery"></a>Migrer les machines virtuelles de Azure IaaS entre Azure régions dont la récupération de Site Azure

## <a name="overview"></a>Vue d’ensemble

Bienvenue dans récupération de Site Azure ! Utilisez cet article si vous voulez migrer Azure machines virtuelles entre les régions Azure. Avant de commencer, notez que :

- Azure comporte deux modèles de déploiement différents pour la création et utilisation des ressources : Gestionnaire de ressources Azure et classique. Azure est également équipé de deux portails – le portail classique Azure qui prend en charge le modèle de déploiement classique et le portail Azure avec prise en charge pour les deux modèles de déploiement. Les étapes de base pour la migration sont les mêmes si vous êtes configuration de la récupération de Site dans le Gestionnaire de ressources ou dans classique. Toutefois, les instructions de l’interface utilisateur et les captures d’écran dans cet article concernent pour le portail Azure.
- **Vous pouvez actuellement migrer uniquement à partir d’une région à l’autre. Vous pouvez basculer machines virtuelles à partir d’une région Azure à l’autre, mais vous ne pouvez pas restauré les à nouveau.**
- Les instructions de migration dans cet article sont basées sur les instructions pour la réplication une machine physique vers Azure. Il inclut des liens vers les étapes dans [répliquer des machines virtuelles VMware ou serveurs physiques vers Azure](site-recovery-vmware-to-azure.md), qui explique comment répliquer un serveur physique dans le portail Azure.
- Si vous configurez récupération de Site dans le portail classique, suivez les instructions détaillées dans [cet article](site-recovery-vmware-to-azure-classic.md). **Vous devez utiliser n’est plus** les instructions fournies dans cet [article héritée](site-recovery-vmware-to-azure-classic-legacy.md).

Publier des commentaires ou des questions en bas de cet article, ou sur le [Forum de Services de récupération Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).


## <a name="prerequisites"></a>Conditions préalables

Voici ce dont vous avez besoin pour ce déploiement :

- **Serveur de configuration**: un ordinateur local virtuel exécutant Windows Server 2012 R2 qui sert de serveur de configuration. Vous installez trop les autres composants de récupération de Site (y compris le serveur de traitement et le serveur de base cible) sur cet ordinateur virtuel. En savoir plus dans [l’architecture de scénario](site-recovery-vmware-to-azure.md#scenario-architecture) et [conditions préalables de serveur de configuration](site-recovery-vmware-to-azure.md#configuration-server-prerequisites).
- **Machines virtuelles IaaS**: The machines virtuelles que vous voulez migrer. Vous migrez ces ordinateurs virtuels en les traitant comme des machines physiques.

## <a name="deployment-steps"></a>Étapes de déploiement

Cette section décrit les étapes de déploiement dans le nouveau portail Azure. Si vous avez besoin de ces étapes de déploiement récupération de Site dans le portail classique, reportez-vous à [cet article](site-recovery-vmware-to-azure-classic.md).

1. [Créer un archivage sécurisé](site-recovery-vmware-to-azure.md#create-a-recovery-services-vault).
2. [Déployer un serveur de configuration](site-recovery-vmware-to-azure.md#step-2-set-up-the-source-environment).
3. Une fois que vous avez déployé le serveur de configuration, vérifier qu’il peut communiquer avec les ordinateurs virtuels que vous voulez migrer.
4. [Configurer les paramètres de réplication](site-recovery-vmware-to-azure.md#step-4-set-up-replication-settings). Créer une stratégie de réplication et affectez au serveur de configuration.
5. [Installer le service mobilité](site-recovery-vmware-to-azure.md#step-6-replication-application). Chaque machine virtuelle à protéger doit le service mobilité est installé. Ce service envoie des données au serveur de processus. Le service mobilité peut installé manuellement ou poussé et installé automatiquement par le serveur de processus lorsque la protection de la machine virtuelle est activée. Règles de pare-feu sur les ordinateurs virtuels que vous voulez migrer doivent être configurés pour autoriser l’installation push de ce service.
6. [Activer la réplication](site-recovery-vmware-to-azure.md#enable-replication). Activer la réplication pour les ordinateurs virtuels que vous voulez migrer. Vous pouvez découvrir les machines virtuelles IaaS que vous voulez migrer vers Azure à l’aide de l’adresse IP privée des machines virtuelles. Trouver cette adresse dans le tableau de bord machine virtuelle dans Azure. Lorsque vous activez la réplication, vous définissez le type d’ordinateur pour les ordinateurs virtuels comme des machines physiques.
7. [Exécuter un basculement non planifié](site-recovery-failover.md#run-an-unplanned-failover). Une fois la réplication initiale terminée, vous pouvez exécuter un basculement non planifié à partir d’une région Azure à l’autre. Si vous le souhaitez, vous pouvez créer un plan de récupération et exécuter un basculement non planifié, pour migrer plusieurs machines virtuelles entre des régions. [En savoir plus](site-recovery-create-recovery-plans.md) sur les offres de récupération.

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur les autres scénarios réplication dans [Nouveautés récupération de Site Azure ?](site-recovery-overview.md)

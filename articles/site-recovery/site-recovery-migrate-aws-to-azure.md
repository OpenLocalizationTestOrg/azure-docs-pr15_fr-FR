<properties
    pageTitle="Migrer des machines virtuelles Windows à partir d’Amazon Web Services vers Azure avec récupération de Site | Microsoft Azure"
    description="Cet article décrit comment migrer des machines virtuelles Windows Azure à l’aide de la récupération de Site Azure en cours d’exécution dans Amazon Web Services (AWA)."
    services="site-recovery"
    documentationCenter=""
    authors="rayne-wiselman"
    manager="jwhit"
    editor=""/>

<tags
    ms.service="site-recovery"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="backup-recovery"
    ms.date="08/22/2016"
    ms.author="raynew"/>

#  <a name="migrate-windows-virtual-machines-in-amazon-web-services-aws-to-azure-with-azure-site-recovery"></a>Migrer des machines virtuelles Windows dans Amazon Web Services (AWS) vers Azure avec récupération de Site Azure

## <a name="overview"></a>Vue d’ensemble

Bienvenue dans récupération de Site Azure. Utiliser cet article pour déplacer des instances de Windows qui s’exécutent dans AWS vers Azure avec récupération de Site. Avant de commencer, notez que :

- Azure comporte deux modèles de déploiement différents pour la création et utilisation des ressources : Gestionnaire de ressources Azure et classique. Azure est également équipé de deux portails – le portail classique Azure qui prend en charge le modèle de déploiement classique et le portail Azure avec prise en charge pour les deux modèles de déploiement. Les étapes de base pour la migration sont les mêmes si vous êtes configuration de la récupération de Site dans le Gestionnaire de ressources ou dans classique. Toutefois, les instructions de l’interface utilisateur et les captures d’écran dans cet article concernent pour le portail Azure.
- **Vous pouvez actuellement migrer uniquement à partir de AWS vers Azure. Vous pouvez basculer machines virtuelles à partir de AWS vers Azure, mais vous ne pouvez pas restauré les à nouveau. Il n’existe aucune réplication en cours.**
- Les instructions de migration dans cet article sont basées sur les instructions pour la réplication une machine physique vers Azure. Il inclut des liens vers les étapes dans [répliquer des machines virtuelles VMware ou serveurs physiques vers Azure](site-recovery-vmware-to-azure.md), qui explique comment répliquer un serveur physique dans le portail Azure.
- Si vous configurez récupération de Site dans le portail classique, suivez les instructions détaillées dans [cet article](site-recovery-vmware-to-azure-classic.md). **Vous devez utiliser n’est plus** les instructions fournies dans cet [article héritée](site-recovery-vmware-to-azure-classic-legacy.md).

Publier des commentaires ou des questions en bas de cet article, ou sur le [Forum de Services de récupération Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)


## <a name="prerequisites"></a>Conditions préalables

Voici ce dont vous avez besoin pour ce déploiement

- **Serveur de configuration**: un ordinateur local virtuel exécutant Windows Server 2012 R2 qui sert de serveur de configuration. Vous installez trop les autres composants de récupération de Site (y compris le serveur de traitement et le serveur de base cible) sur cet ordinateur virtuel. En savoir plus dans [l’architecture de scénario](site-recovery-vmware-to-azure.md#scenario-architecture) et [conditions préalables de serveur de configuration](site-recovery-vmware-to-azure.md#configuration-server-prerequisites).
- **Machine virtuelle EC2 instances**: les instances exécutant Windows que vous voulez migrer.

## <a name="deployment-steps"></a>Étapes de déploiement

Cette section décrit les étapes de déploiement dans le nouveau portail Azure. Si vous avez besoin de ces étapes de déploiement récupération de Site dans le portail classique, reportez-vous à [cet article](site-recovery-vmware-to-azure-classic.md).

1. [Créer un archivage sécurisé](site-recovery-vmware-to-azure.md#create-a-recovery-services-vault).
2. [Déployer un serveur de configuration](site-recovery-vmware-to-azure.md#step-2-set-up-the-source-environment).
3. Une fois que vous avez déployé le serveur de configuration, valider qu’il peut communiquer avec les ordinateurs virtuels que vous voulez migrer.
4. [Configurer les paramètres de réplication](site-recovery-vmware-to-azure.md#step-4-set-up-replication-settings). Créer une stratégie de réplication et affectez au serveur de configuration.
5. [Installer le service mobilité](site-recovery-vmware-to-azure.md#step-6-replication-application). Chaque machine virtuelle à protéger doit le service mobilité est installé. Ce service envoie des données au serveur de processus. Le service mobilité peut installé manuellement ou poussé et installé automatiquement par le serveur de processus lorsque la protection de la machine virtuelle est activée. Règles de pare-feu sur des instances EC2 que vous voulez migrer doivent être configurés pour autoriser l’installation push de ce service. Le groupe de sécurité EC2 instances doit avoir les règles suivantes :

    ![règles de pare-feu](./media/site-recovery-migrate-aws-to-azure/migrate-firewall.png)

6. [Activer la réplication](site-recovery-vmware-to-azure.md#enable-replication). Activer la réplication pour les ordinateurs virtuels que vous voulez migrer. Vous pouvez découvrir les instances EC2 à l’aide d’adresses IP privées, vous pouvez accéder à partir de la console EC2.
7. [Exécuter un basculement non planifié](site-recovery-failover.md#run-an-unplanned-failover). Une fois la réplication initiale terminée, vous pouvez exécuter un basculement non planifié à partir de AWS vers Azure pour chaque machine virtuelle. Si vous le souhaitez, vous pouvez créer un plan de récupération et exécuter un basculement non planifié, pour migrer plusieurs machines virtuelles de AWS dans Azure. [En savoir plus](site-recovery-create-recovery-plans.md) sur les offres de récupération.

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur les autres scénarios réplication dans [Nouveautés récupération de Site Azure ?](site-recovery-overview.md)

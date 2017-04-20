
<properties
    pageTitle="Azure récupération de Site : Machines virtuelles réplication Hyper-V un seul serveur VMM | Microsoft Azure"
    description="Cet article décrit comment répliquer machines virtuelles Hyper-V lorsque vous disposez d’un seul serveur VMM."
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
    ms.date="08/24/2016"
    ms.author="raynew"/>

#  <a name="replicate-hyper-v-virtual-machines-on-a-single-vmm-server"></a>Répliquer machines virtuelles de Hyper-V sur un seul serveur VMM

Lisez cet article pour découvrir comment répliquer des ordinateurs virtuels Hyper-V situés sur un serveur hôte Hyper-V dans un nuage VMM lorsque vous disposez d’un serveur VMM unique dans votre déploiement.

Azure comporte deux différents [modèles de déploiement](../resource-manager-deployment-model.md) pour la création et utilisation des ressources : Gestionnaire de ressources Azure et classique. Azure est également équipé de deux portails – le portail classique Azure qui prend en charge le modèle de déploiement classique et le portail Azure avec prise en charge pour les deux modèles de déploiement. Cet article contient des instructions de configuration de la réplication dans le portail Azure.


Si vous avez des questions après la lecture de cet article, placez-les dans les commentaires Disqus en bas de cet article ou sur le [forum de Services de récupération Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

## <a name="overview"></a>Vue d’ensemble

Si vous souhaitez répliquer machines virtuelles Hyper-V situé sur hôtes Hyper-V dans VMM et vous n’avez qu’un seul serveur VMM, vous pouvez [répliquer vers Azure](site-recovery-vmm-to-azure.md), ou entre nuages sur le serveur VMM unique.

Nous vous recommandons de répliquer vers Azure car basculement et récupération ne sont pas transparentes lors de la réplication entre nuages et un nombre d’étapes manuelles est nécessaires. Si vous ne souhaitez pas répliquer à l’aide du serveur VMM uniquement, procédez comme suit :

- Répliquer avec un serveur VMM autonome unique
- Répliquer avec un seul serveur VMM déployé dans un cluster Windows étiré


## <a name="replicate-across-sites-with-a-single-standalone-vmm-server"></a>Répliquer sur des sites avec un serveur VMM autonome unique

![Serveur VMM virtuel autonome](./media/site-recovery-single-vmm/single-vmm-standalone.png)

Dans ce scénario, vous déployez le serveur VMM unique comme une machine virtuelle dans le site principal et répliquez cet ordinateur virtuel sur un site secondaire à l’aide de la récupération de Site et réplica Hyper-V.

1. Configurer VMM sur un ordinateur virtuel Hyper-V. Nous vous recommandons de que vous installez l’instance SQL Server utilisée par VMM sur le même ordinateur virtuel pour gagner du temps. Si vous souhaitez utiliser une instance de SQL Server et d’arrêt à distance se produit, vous devez récupérer cette instance avant de pouvoir récupérer VMM.
2. Vérifiez qu’au moins deux nuages configurés sur le serveur VMM. Un nuage contient les ordinateurs virtuels vous souhaitez répliquer et l’autres cloud sert à l’emplacement secondaire. Le nuage qui contient les ordinateurs virtuels à protéger doit avoir :

    - Un ou plusieurs groupes hôtes VMM contenant un ou plusieurs des serveurs hôte Hyper-V dans chaque groupe hôte.
    - Au moins une machine virtuelle Hyper-V sur chaque serveur hôte Hyper-V.

3. Créer un archivage sécurisé aux Services de récupération, générer et télécharger une clé d’inscription de l’archivage sécurisé et inscrire le serveur VMM dans l’archivage sécurisé. Lors de l’enregistrement que vous installez le fournisseur de récupération de Site Azure sur le serveur VMM.
4. Configurer un ou plusieurs nuages sur la VM VMM, puis ajoutez les hôtes Hyper-V à ces nuages.
3. Configurer les paramètres de protection pour les nuages. Vous indiquez le nom du serveur VMM unique en tant que les emplacements source et cible. Pour configurer le mappage réseau, vous mappez le réseau machine virtuelle pour le nuage avec les ordinateurs virtuels à protéger, sur le réseau machine virtuelle par le cloud réplication.
4. Activer la réplication initiale pour les machines virtuelles à protéger sur le réseau, car les deux nuages se trouvent sur le même serveur.
4. Dans la console Gestionnaire Hyper-V, activer réplica Hyper-V sur l’hôte Hyper-V qui contient le VMM VM et activer la réplication sur l’ordinateur virtuel. Vérifiez que vous n’ajoutez pas le VMM VM à n’importe quel nuages qui sont protégés par récupération de Site. Cela garantit que les paramètres de Hyper-V réplica ne sont pas remplacés par la récupération de Site.
5. Si vous souhaitez créer des plans de récupération, vous spécifiez le serveur VMM même pour les sources et cibles.

Suivez les instructions [cet article](site-recovery-vmm-to-vmm.md) pour créer un archivage sécurisé, enregistrez le serveur et configurer la protection.

### <a name="what-to-do-in-an-outage"></a>Procédure à suivre dans une panne

Si une panne complète se produit et que vous voulez utiliser à partir du site secondaire, procédez comme suit :

1.  Dans la console Gestionnaire Hyper-V dans le site secondaire, exécutez un basculement non planifié pour basculer le VMM VM du site principal vers le site secondaire.
2.  Vérifiez que le VMM VM est en cours d’exécution dans le site secondaire.
3.  Dans l’archivage sécurisé aux Services de récupération, exécutez un basculement non planifié basculer sur la charge de travail machines virtuelles du site principal à nuages secondaires. Pour exécuter le basculement non planifié des ordinateurs virtuels, valider le basculement ou sélectionnez un point de récupération différents selon les besoins.
4.  Une fois le basculement non planifié terminée, les utilisateurs peuvent accéder aux ressources la charge de travail dans le site secondaire.

Quand le site principal fonctionne normalement à nouveau, procédez comme suit :

1.  Dans la console Gestionnaire Hyper-V, activez la réplication inverse de VMM VM, pour lancer la réplication de secondaire principal.
2.  Dans la console Gestionnaire Hyper-V, exécutez un basculement planifié pour basculer le VMM VM en vue du site principal. Valider le basculement pour terminer. Puis activez la réplication inverse lancer la réplication le moniteur principal de sur le site secondaire.
3.  Dans l’archivage sécurisé aux Services de récupération, activer la réplication inverse pour la charge de travail machines virtuelles, pour lancer la réplication les de secondaire principal.
4.  Dans l’archivage sécurisé aux Services de récupération, exécutez un basculement planifié pour être restauré la charge machines virtuelles sur le site principal. Valider le basculement pour terminer. Puis activez la réplication inverse lancer la réplication de la charge de travail machines virtuelles à partir de principal vers le site secondaire.



## <a name="replicate-across-sites-with-a-single-vmm-server-in-a-stretched-cluster"></a>Répliquer sur des sites avec un seul serveur VMM dans un cluster étiré

![Serveur VMM virtuel groupé](./media/site-recovery-single-vmm/single-vmm-cluster.png)

Au lieu de déploiement d’un serveur VMM autonome comme un ordinateur virtuel qui réplique sur un site secondaire, vous pouvez rendre VMM hautement disponible en déployant comme une machine virtuelle dans un cluster de basculement Windows. Cette procédure résilience la charge de travail et la protection contre les défaillances matérielles. Pour déployer avec récupération de Site le VMM VM doit être déployée dans un cluster étirement entre sites géographiquement. Pour cela :

1. Installer VMM sur un ordinateur virtuel dans un cluster de basculement Windows, puis sélectionnez l’option pour exécuter le serveur comme étant hautement disponible lors de l’installation.
2. L’instance SQL Server qui est utilisé par VMM doit être répliquée des groupes de disponibilité de SQL Server AlwaysOn afin qu’il existe un réplica de la base de données dans le site secondaire.
3. Suivez les instructions [cet article](site-recovery-vmm-to-vmm.md) pour créer un archivage sécurisé, enregistrez le serveur et configurer la protection. Vous devez enregistrer chaque serveur VMM du cluster dans l’archivage sécurisé aux Services de récupération. Pour ce faire, vous installez le fournisseur sur un nœud actif et enregistrez le serveur VMM. Puis que vous installez le fournisseur sur d’autres nœuds.

### <a name="what-to-do-in-an-outage"></a>Procédure à suivre dans une panne

En cas de panne, le serveur VMM sa base de données SQL Server correspondante basculé et sont accessibles à partir du site secondaire.


## <a name="next-steps"></a>Étapes suivantes

[En savoir plus](site-recovery-vmm-to-vmm.md) sur le déploiement de récupération de Site détaillée pour VMM réplication VMM.

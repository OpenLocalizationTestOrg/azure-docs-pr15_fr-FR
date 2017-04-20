<properties
    pageTitle="Récupération de Site Azure : Forum aux questions | Microsoft Azure"
    description="Cet article traite des questions fréquentes sur la récupération de Site Azure."
    services="site-recovery"
    documentationCenter=""
    authors="rayne-wiselman"
    manager="cfreeman"
    editor=""/>

<tags
    ms.service="get-started-article"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="storage-backup-recovery"
    ms.date="10/10/2016"
    ms.author="raynew"/>


# <a name="azure-site-recovery-frequently-asked-questions-faq"></a>Récupération de Site Azure : Forum aux questions (FAQ)

Cet article contient des questions fréquemment posées sur la récupération de Site Azure. Si vous avez des questions après la lecture de cet article, publiez-les sur le [Forum de Services de récupération Azure](https://social.msdn.microsoft.com/Forums/azure/home?forum=hypervrecovmgr).


## <a name="general"></a>Général

### <a name="what-does-site-recovery-do"></a>Que faire récupération de Site ?

Récupération de site contribue à votre stratégie de récupération (BCDR) entreprise continuité et de reprise, en organiser et automatisation de la réplication de machines virtuelles en local et des serveurs physiques à Azure ou à un centre de données secondaire. [En savoir plus](site-recovery-overview.md).


### <a name="what-can-site-recovery-protect"></a>Que peuvent être protégés récupération de Site ?

- **Machines virtuelles Hyper-V**: récupération de Site permet de protéger les charges de travail en cours d’exécution sur un ordinateur virtuel Hyper-V.
- **Serveurs physiques**: récupération de Site peut protéger les serveurs physiques exécutant Windows ou Linux.
- **Machines virtuelles VMware**: récupération de Site permet de protéger les charges de travail en cours d’exécution dans un VMware VM.

### <a name="does-site-recovery-support-the-azure-resource-manager-model"></a>Récupération de Site ne prend en charge le modèle de gestionnaire de ressources Azure ?

En plus de récupération de Site dans le portail classique Azure, récupération de Site est disponible dans le portail Azure avec prise en charge pour le Gestionnaire de ressources. Pour la plupart des scénarios de déploiement récupération de Site dans le Azure portal offre une expérience de rationaliser le déploiement et vous pouvez répliquer machines virtuelles et des serveurs physiques dans stockage classique ou de stockage du Gestionnaire de ressources. Voici les déploiements pris en charge :

- [Dupliquer des machines virtuelles VMware ou serveurs physiques vers Azure dans le portail Azure](site-recovery-vmware-to-azure.md)
- [Répliquer les machines virtuelles Hyper-V dans nuages VMM à Azure dans le portail Azure](site-recovery-vmm-to-azure.md)
- [Répliquer les machines virtuelles Hyper-V (sans VMM) vers Azure dans le portail Azure](site-recovery-hyper-v-site-to-azure.md)
- [Répliquer machines virtuelles Hyper-V dans nuages VMM sur un site secondaire dans le portail Azure](site-recovery-vmm-to-vmm.md)


### <a name="what-do-i-need-in-hyper-v-to-orchestrate-replication-with-site-recovery"></a>Que dois-je dans Hyper-V pour harmoniser réplication avec récupération de Site ?

Pour le serveur hôte Hyper-V ce dont vous avez besoin dépend du scénario de déploiement. Consultez les conditions préalables Hyper-V dans :

- [Réplication des ordinateurs virtuels Hyper-V (sans VMM) vers Azure](site-recovery-hyper-v-site-to-azure.md#before-you-start)
- [Réplication des ordinateurs virtuels Hyper-V (avec VMM) vers Azure](site-recovery-vmm-to-azure.md#before-you-start)
- [Réplication des ordinateurs virtuels Hyper-V vers un centre de données secondaire](site-recovery-vmm-to-vmm.md#before-you-start)

- Si vous êtes réplication vers un centre de données secondaire en savoir plus sur [les systèmes d’exploitation pris en charge invités pour les machines virtuelles Hyper-V](https://technet.microsoft.com/library/mt126277.aspx).
- Si vous êtes réplication vers Azure, récupération de Site prend en charge tous les systèmes d’exploitation invité qui sont [pris en charge par Azure](https://technet.microsoft.com/library/cc794868%28v=ws.10%29.aspx).

### <a name="can-i-protect-vms-when-hyper-v-is-running-on-a-client-operating-system"></a>Puis-je protéger machines virtuelles lorsque Hyper-V s’exécute sur un système d’exploitation client ?

Non, machines virtuelles doivent se trouver sur un serveur hôte Hyper-V qui s’exécute sur un serveur Windows pris en charge. Si vous avez besoin protéger un ordinateur client vous a le répliquer comme un ordinateur physique pour [Azure](site-recovery-vmware-to-azure.md) ou un [Centre de données secondaire](site-recovery-vmware-to-vmware.md).


### <a name="what-workloads-can-i-protect-with-site-recovery"></a>Les charges de travail puis-je protéger avec récupération de Site ?

Vous pouvez utiliser la récupération de Site pour protéger la plupart des charges de travail en cours d’exécution sur un serveur physique ou machine virtuelle pris en charge. Récupération de site prend en charge réplication d’application, afin que les applications peuvent être restaurées à un état intelligent. Il s’intègre avec les applications Microsoft tels que SharePoint, Exchange, Dynamics, SQL Server et Active Directory et fonctionne en étroite collaboration avec les principaux fournisseurs, y compris Oracle, SAP, IBM et chapeau rouge. [En savoir plus](site-recovery-workload.md) sur la protection de la charge de travail.


### <a name="do-hyper-v-hosts-need-to-be-in-vmm-clouds"></a>Hôtes Hyper-V ont-ils besoin d’être présent à nuages VMM ?

Si vous souhaitez répliquer vers un centre de données secondaire, puis ordinateurs virtuels Hyper-V doivent se trouver sur Hyper-V héberge serveurs situés dans un nuage VMM. Si vous voulez dupliquer vers Azure, vous pouvez répliquer machines virtuelles sur les serveurs hôtes Hyper-V avec ou sans nuages VMM. [Pour en savoir plus](site-recovery-hyper-v-site-to-azure.md).

### <a name="can-i-deploy-site-recovery-with-vmm-if-i-only-have-one-vmm-server"></a>Puis-je déployer récupération de Site avec VMM si j’utilise uniquement un serveur VMM ?

Oui. Vous pouvez répliquer soit machines virtuelles dans serveurs Hyper-V dans le nuage VMM Azure, ou vous pouvez répliquer entre nuages VMM sur le même serveur. Pour en local pour la réplication en local, nous vous recommandons de que vous avez un serveur VMM dans les sites principaux et secondaires.  [En savoir plus](site-recovery-single-vmm.md)

### <a name="what-physical-servers-can-i-protect"></a>Quels serveurs physiques puis-je protéger ?

Vous pouvez répliquer serveurs physiques exécutant Windows et Linux vers Azure ou sur un site secondaire. [En savoir plus sur les](site-recovery-vmware-to-azure.md#protected-machine-prerequisites) systèmes d’exploitation requis.  Les mêmes conditions s’appliquent si vous êtes la réplication des serveurs physiques vers Azure, ou vers un site secondaire.

Notez que les serveurs physiques seront exécutera en tant que machines virtuelles dans Azure si votre serveur local s’arrête. Retour arrière vers un serveur physique local n’est pas pris en charge actuellement, mais vous pouvez restauré vers une machine virtuelle s’exécutant sur Hyper-V ou VMware.


### <a name="what-vmware-vms-can-i-protect"></a>Quels ordinateurs virtuels VMware puis-je protéger ?

Pour protéger des machines virtuelles VMware, vous devez un hyperviseur vSphere et machines virtuelles VMware outils en cours d’exécution. Nous vous recommandons également que vous avez un serveur de vCenter VMware pour gérer les hyperviseurs. [En savoir plus](site-recovery-vmware-to-azure.md#protected-machine-prerequisites) sur la configuration requise exacte pour la réplication des serveurs VMware et machines virtuelles à Azure ou sur un site secondaire.

### <a name="can-i-manage-disaster-recovery-for-my-branch-offices-with-site-recovery"></a>Puis-je gérer sinistre pour mon succursales avec récupération de Site ?

Oui. Lorsque vous utilisez récupération de Site pour organiser réplication et basculement dans vos succursales, vous recevrez une orchestration unifiée et l’affichage de toutes les charges de travail votre office branche dans un emplacement central. Vous pouvez facilement exécuter basculement et administrer sinistre de toutes les branches à partir de votre siège social, sans visiter les branches.

## <a name="security"></a>Sécurité

### <a name="is-replication-data-sent-to-the-site-recovery-service"></a>Données de la réplication sont envoyées au service de récupération de Site ?

Non, récupération de Site n’intercepter des données dupliquées et n’a pas d’informations sur ce qui fonctionne sur des machines virtuelles ou physique.
Données de la réplication sont échangées entre hôtes Hyper-V en local, hyperviseurs VMware, ou des serveurs physiques et stockage Azure ou votre site secondaire. Récupération de site n’a aucun moyen d’intercepter ces données. Seules les métadonnées nécessaires pour organiser basculement et réplication sont envoyée au service de récupération de Site.

Récupération de site est ISO 27001:2013, 27018, HIPAA, DPA certifié et est en train d’évaluations SOC2 et FedRAMP JAB.


### <a name="for-compliance-reasons-even-our-on-premises-metadata-must-remain-within-the-same-geographic-region-can-site-recovery-help-us"></a>Pour des raisons de conformité, même nos métadonnées local doivent rester dans la même région géographique. Récupération de Site peut-il nous ?

Oui. Lorsque vous créez un archivage sécurisé récupération de Site dans une zone, nous vous assurer que toutes les métadonnées que nous avons besoin activer et organiser réplication et basculement reste dans cette zone sont géographique limite.

### <a name="does-site-recovery-encrypt-replication"></a>Récupération de Site chiffre réplication ?

Machines virtuelles et des serveurs physiques, réplication entre sites en local de chiffrement dans voies est pris en charge. Machines virtuelles et des serveurs physiques réplication vers Azure, chiffrement dans voies et chiffrement reste (dans Azure) sont pris en charge.


## <a name="replication"></a>Réplication


### <a name="are-there-any-prerequisites-for-replicating-virtual-machines-to-azure"></a>Existe-t-il des conditions requises pour la réplication des machines virtuelles vers Azure ?

Machines virtuelles que vous souhaitez répliquer vers Azure doivent être conformes aux [exigences Azure](site-recovery-best-practices.md#virtual-machines).

### <a name="can-i-replicate-hyper-v-generation-2-virtual-machines-to-azure"></a>Puis-je répliquer machines virtuelles de génération 2 Hyper-V vers Azure ?

Oui. Récupération de site convertit de génération 2 à la génération 1 pendant le basculement. Au retour arrière l’ordinateur est converti en génération 2. [Pour en savoir plus](http://azure.microsoft.com/blog/2015/04/28/disaster-recovery-to-azure-enhanced-and-were-listening/).

### <a name="if-i-replicate-to-azure-how-do-i-pay-for-azure-vms"></a>Si j’ai répliquer vers Azure comment dois-je payer pour les machines virtuelles Azure ?

Lors de la réplication régulière, les données sont répliquées vers le stockage Azure geo redondants et vous n’avez pas besoin de payer les frais de machine virtuelle Azure IaaS, fournir un avantage significatif. Lorsque vous exécutez un basculement vers Azure, récupération de Site crée automatiquement des machines virtuelles IaaS Azure et après qui vous seront facturés pour les ressources de calcul que vous consommer dans Azure.


### <a name="is-there-an-sdk-i-can-use-to-automate-the-asr-workflow"></a>Existe-t-il un kit de développement que je peux utiliser pour automatiser le flux de travail de récupération automatique du système ?

Oui. Automatiser les flux de travail de récupération de Site à l’aide de l’API Rest, PowerShell ou le Kit de développement Azure. Scénarios actuellement pris en charge pour le déploiement de récupération de Site à l’aide de PowerShell :

- [Répliquer les machines virtuelles Hyper-V dans le moniteur nuages PowerShell Azure classique](site-recovery-deploy-with-powershell.md)
- [Répliquer les machines virtuelles Hyper-V dans le moniteur nuages au Gestionnaire de ressources PowerShell Azure](site-recovery-vmm-to-azure-powershell-resource-manager.md)
- [Répliquer les machines virtuelles Hyper-V sans VMM PowerShell Azure classique](site-recovery-hyper-v-site-to-azure-classic.md)
- [Répliquer les machines virtuelles Hyper-V sans VMM au Gestionnaire de ressources PowerShell Azure](site-recovery-deploy-with-powershell-resource-manager.md)


### <a name="if-i-replicate-to-azure-what-kind-of-storage-account-do-i-need"></a>Si j’ai répliquer vers Azure quel type de compte de stockage ai-je besoin ?

- **Portail classique Azure**: Si vous déployez récupération de Site dans le portail classique Azure, vous devez vous un [compte de stockage geo redondants standard](../storage/storage-redundancy.md#geo-redundant-storage). Stockage Premium n’est pas actuellement pris en charge. Le compte doit être placé dans la même région en tant que l’archivage sécurisé récupération de Site.

- **Portail Azure**: Si vous déployez récupération de Site dans le portail Azure, vous devez posséder un compte de stockage LRS ou GRS. Nous vous recommandons de GRS afin que les données sont résistants si une panne régionale se produit ou si la région primaire ne peuvent pas être récupérée. Le compte doit être placé dans la même région en tant que l’archivage sécurisé aux Services de récupération. Stockage Premium est pris en charge uniquement si vous êtes la réplication machines virtuelles VMware ou serveurs physiques.

### <a name="how-often-can-i-replicate-data"></a>À quelle fréquence puis-je répliquer des données ?

- **Hyper-v :** Machines virtuelles Hyper-V peuvent être répliquées toutes les 30 secondes, 5 minutes ou 15 minutes. Si vous avez configuré la réplication SAN réplication est synchrone.
- **VMware et des serveurs physiques :** Une fréquence de réplication n’est pas pertinente ici. La réplication est continue.

### <a name="can-i-extend-replication-from-existing-recovery-site-to-another-tertiary-site"></a>Puis-je étendre réplication de site récupération existant vers un autre site troisième ?

Réplication étendue ou de chaîne n’est pas prise en charge. Demander à cette fonctionnalité [d’évaluation](http://feedback.azure.com/forums/256299-site-recovery/suggestions/6097959-support-for-exisiting-extended-replication).


### <a name="can-i-do-an-offline-replication-the-first-time-i-replicate-to-azure"></a>Puis-je faire une réplication en mode hors connexion la première fois que j’ai répliquer vers Azure ?

Ceci n’est pas pris en charge. Demander à cette fonctionnalité dans le [forum de commentaires](http://feedback.azure.com/forums/256299-site-recovery/suggestions/6227386-support-for-offline-replication-data-transfer-from).


### <a name="can-i-exclude-specific-disks-from-replication"></a>Puis-je exclure des disques spécifiques de la réplication ?

Ceci est pris en charge lorsque vous êtes [réplication des machines virtuelles VMware et des serveurs physiques](site-recovery-vmware-to-azure.md#exclude-disks-from-replication) sur Azure, à l’aide du portail Azure.


### <a name="can-i-replicate-virtual-machines-with-dynamic-disks"></a>Puis-je répliquer machines virtuelles avec des disques dynamiques ?

Disques dynamiques sont prises en charge lors de la réplication des ordinateurs virtuels Hyper-V. Ils sont également pris en charge lors de la réplication des machines virtuelles VMware et machines physiques vers Azure. Le disque système d’exploitation doit être un disque de base.

### <a name="can-i-add-a-new-machine-to-an-existing-replication-group"></a>Puis-je ajouter un nouvel ordinateur à un groupe de réplication existant ?

Ajout de nouveaux ordinateurs à des groupes de réplication existants est prise en charge. Pour ce faire, sélectionnez le groupe de réplication (à partir de carte « Éléments répliqué ») et le menu contextuel/sélectionner cliquez avec le bouton droit sur le groupe de réplication et sélectionnez l’option appropriée.

![Ajouter au groupe de réplication](./media/site-recovery-faq/add-server-replication-group.png)

### <a name="can-i-throttle-bandwidth-allotted-for-hyper-v-replication-traffic"></a>Puis-je limiter la bande passante allouée pour le trafic de réplication Hyper-V ?

Oui. Vous pouvez en savoir plus sur la limitation de bande passante dans les articles de déploiement :

- [Planifier la capacité de réplication des machines virtuelles VMware et des serveurs physiques](site-recovery-vmware-to-azure.md#step-5-capacity-planning)
- [Planifier la capacité de réplication des machines virtuelles Hyper-V dans nuages VMM](site-recovery-vmm-to-azure.md#step-5-capacity-planning)
- [Planifier la capacité de réplication des ordinateurs virtuels Hyper-V sans VMM](site-recovery-hyper-v-site-to-azure.md#step-5-capacity-planning)

## <a name="failover"></a>Basculement


### <a name="if-im-failing-over-to-azure-how-do-i-access-the-azure-virtual-machines-after-failover"></a>Si je suis basculement vers Azure, comment accéder aux machines virtuelles Azure après le basculement ?

Vous pouvez accéder les ordinateurs virtuels Azure via une connexion Internet sécurisée, sur un réseau privé virtuel de site à ou sur Azure ExpressRoute. Vous devez préparer un certain nombre de choses afin de vous connecter. En savoir plus dans :

- [Se connecter aux machines virtuelles Azure après le basculement d’ordinateurs virtuels VMware ou serveurs physiques](hsite-recovery-vmware-to-azure.md#step-7-test-the-deployment)
- [Se connecter aux machines virtuelles Azure après le basculement machines virtuelles Hyper-V en nuages VMM](site-recovery-vmm-to-azure.md#step-7-test-your-deployment)
- [Se connecter aux machines virtuelles Azure après le basculement d’ordinateurs virtuels Hyper-V sans VMM](site-recovery-hyper-v-site-to-azure.md#step-7-test-the-deployment)


### <a name="if-i-fail-over-to-azure-how-does-azure-make-sure-my-data-is-resilient"></a>Si basculer vers Azure comment Azure assurer que mes données sont résistants ?

Azure est conçu pour la résilience. Récupération de site est déjà conçue pour basculer vers un secondaire centre de données Azure, selon le SLA Azure si besoin. Dans ce cas, il s’assurer que les métadonnées de votre et chambres fortes resteront au sein de la même région géographique que vous avez choisi pour votre l’archivage sécurisé.  

### <a name="if-im-replicating-between-two-datacenters-what-happens-if-my-primary-datacenter-experiences-an-unexpected-outage"></a>Si je suis réplication entre deux centres de données que se passe-t-il si mon centre de données principal connaît une panne inattendue ?

Vous pouvez déclencher un basculement non planifié à partir du site secondaire. Récupération de site n’a pas besoin de connectivité à partir du site principal pour effectuer le basculement.

### <a name="is-failover-automatic"></a>Basculement est automatique ?

Basculement n’est pas automatique. Vous passez basculement simple clic dans le portail, ou vous pouvez utiliser [PowerShell de récupération de Site](https://msdn.microsoft.com/library/dn850420.aspx) pour déclencher la lecture d’un basculement. Ignore précédent est une action simple dans le portail de récupération de Site.

Pour automatiser vous faire permet de détecter une défaillance machine virtuelle Orchestrator locaux ou Operations Manager et puis déclencher le basculement à l’aide du Kit de développement.

- [Pour en savoir plus](site-recovery-create-recovery-plans.md) sur les offres de récupération.
- [Pour en savoir plus](site-recovery-failover.md) sur le basculement.
- [Pour en savoir plus](site-recovery-failback-azure-to-vmware.md) sur défectueux sauvegarder VMware machines virtuelles et des serveurs physiques


## <a name="service-providers"></a>Fournisseurs de services


### <a name="im-a-service-provider-does-site-recovery-work-for-dedicated-and-shared-infrastructure-models"></a>Je suis un fournisseur de services. Récupération de Site fonctionne pour les modèles d’infrastructure dédié et partagé ?

Oui, récupération de Site prend en charge les deux modèles d’infrastructure dédié et partagé.

### <a name="for-a-service-provider-is-the-identity-of-my-tenant-shared-with-the-site-recovery-service"></a>Pour un fournisseur de services, est l’identité de mon client partagé avec le service de récupération de Site ?

Non. Identité client reste anonyme. Vos clients ne doivent pas accéder au portail récupération de Site. Seul l’administrateur du fournisseur de service interagit avec le portail.


### <a name="will-tenant-application-data-ever-go-to-azure"></a>Données d’application client passera jamais à Azure ?

Lors de la réplication entre sites appartenant à un fournisseur de service, les données d’application accède jamais au Azure. Données sont chiffrées dans voies et répliquée directement entre les sites de fournisseur de service.

Si vous êtes réplication vers Azure, données de l’application sont envoyées au stockage Azure, mais pas pour le service de récupération de Site. Données sont chiffré dans voies et restent chiffrées dans Azure.


### <a name="will-my-tenants-receive-a-bill-for-any-azure-services"></a>Mes clients recevront une facture pour tous les services Azure ?

Non. Relation de facturation de Azure est directement avec le fournisseur de services. Fournisseurs de services sont responsables pour générer des factures spécifiques pour leurs clients.

### <a name="if-im-replicating-to-azure-do-we-need-to-run-virtual-machines-in-azure-at-all-times"></a>Si je suis réplication vers Azure, avons-nous besoin exécuter des machines virtuelles à tout moment dans Azure ?

Non, les données sont répliquées vers un compte de stockage Azure dans votre abonnement. Lorsque vous effectuez un test basculement (exploration DR) ou un basculement réel, récupération de Site crée automatiquement des machines virtuelles dans votre abonnement.

### <a name="do-you-ensure-tenant-level-isolation-when-i-replicate-to-azure"></a>Garantissez-vous isolement au niveau du client lorsque je réplique vers Azure ?

Oui.

### <a name="what-platforms-do-you-currently-support"></a>Quelles sont les plates-formes vous prennent en charge ?

Nous prenons en charge module Azure, système de plateforme Cloud, et System Center en fonction des déploiements (2012 et versions ultérieures). [En savoir plus](https://technet.microsoft.com/library/dn850370.aspx) sur l’intégration de module Azure et de récupération de Site.

### <a name="do-you-support-single-azure-pack-and-single-vmm-server-deployments"></a>Vous prennent en charge module Azure unique et les déploiements de serveur VMM unique ?

Oui, vous pouvez répliquer machines virtuelles Hyper-V sur Azure ou répliquer entre les sites de fournisseur de service.  Notez que si vous répliquez entre les sites de fournisseur de service, l’intégration runbook Azure n’est pas disponible.


## <a name="next-steps"></a>Étapes suivantes

- Lire la [Présentation de la récupération de Site](site-recovery-overview.md)
- En savoir plus sur [l’architecture de Site](site-recovery-components.md)  

<properties
    pageTitle="Surveiller et résoudre les problèmes de protection des machines virtuelles et des serveurs physiques | Microsoft Auzre" 
    description="Récupération de Site Azure coordonnées de la réplication, le basculement et la restauration des machines virtuelles sur des serveurs locaux Azure ou un centre de données secondaire. Utilisez cet article pour surveiller et résoudre les problèmes de protection VMM ou Site Hyper-V." 
    services="site-recovery" 
    documentationCenter="" 
    authors="anbacker" 
    manager="mkjain" 
    editor=""/>

<tags 
    ms.service="site-recovery" 
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="storage-backup-recovery" 
    ms.date="10/13/2016"    
    ms.author="rajanaki"/>
    
# <a name="monitor-and-troubleshoot-protection-for-virtual-machines-and-physical-servers"></a>Surveiller et résoudre les problèmes de protection des machines virtuelles et des serveurs physiques

Ce Guide de dépannage de surveillance et de vous permet d’apprendre à l’état de la réplication de suivi et résolution des problèmes techniques Azure récupération de Site.

## <a name="understanding-the-components"></a>Comprendre les composants

### <a name="vmwarephysical-site-deployment-for-replication-between-on-premises-and-azure"></a>Déploiement de Site VMware/physique pour la réplication entre en local et Azure.
Configuration de DR entre machine de VMware/physique local ; Configuration, Masque cible et processus Server doit configuré. Lors de l’activation de protection pour le serveur source Azure récupération de Site installe mobilité service. Publier une panne local une fois que le serveur source bascule sur Azure, besoins de clients pour installer un serveur de traitement dans Azure et une cible masque server en local pour protéger le serveur source en reconstruit en local. 

![Déploiement d’un Site VMware/physique pour la réplication entre en local et Azure](media/site-recovery-monitoring-and-troubleshooting/image18.png)

### <a name="vmm-site-deployment-for-replication-between-on-premises-site"></a>Déploiement de Site VMM pour la réplication entre le site local.

Dans le cadre de la configuration DR entre deux emplacement local ; Fournisseur de récupération de Site Azure doit être téléchargé et installé sur le serveur VMM. Fournisseur doit connectivité internet pour vous assurer que toutes les opérations déclenchées à partir du portail Azure est traduite en local telles que les opérations activer la protection, virtualisation arrêt côté primaire dans le cadre de basculement etc..

![Déploiement d’un Site VMM pour la réplication entre le site local](media/site-recovery-monitoring-and-troubleshooting/image1.png)

### <a name="vmm-site-deployment-for-replication-between-on-premises--azure"></a>Déploiement de Site VMM pour la réplication entre en local et Azure.

Dans le cadre de la configuration DR entre en local et Azure ; Fournisseur de récupération de Site Azure doit être téléchargé et installé sur le serveur VMM en même temps que l’Agent de Services de récupération Azure qui doit être installé sur chaque hôte Hyper-V. Pour plus d’informations, voir [Comprendre Site Azure protection par](./site-recovery-understanding-site-to-azure-protection.md) .

![Déploiement d’un Site VMM pour la réplication entre en local et Azure](media/site-recovery-monitoring-and-troubleshooting/image2.png)

### <a name="hyper-v-site-deployment-for-replication-between-on-premises--azure"></a>Déploiement d’un Site Hyper-V pour la réplication entre en local et Azure

Il s’agit de même que celle de déploiement VMM : uniquement les différence étant fournisseur et Agent sont installé sur l’hôte Hyper-V lui-même. Pour plus d’informations, voir [Comprendre Site Azure protection par](./site-recovery-understanding-site-to-azure-protection.md) .

## <a name="monitor-configuration-protection-and-recovery-operations"></a>Surveiller les opérations de configuration, de protection et de restauration

Chaque opération de récupération automatique du système obtient audit et est suivie sous l’onglet « Tâches ». Dans le cas d’une protection, une erreur de récupération ou toute configuration accédez à l’onglet tâches et déterminer s’il existe les échecs.

![Surveiller les opérations de configuration, de protection et de restauration](media/site-recovery-monitoring-and-troubleshooting/image3.png)

Une fois que vous avez trouvé échecs sous la vue tâches, sélectionnez la tâche, puis cliquez sur Détails de l’erreur pour cette tâche.

![Surveiller les opérations de configuration, de protection et de restauration](media/site-recovery-monitoring-and-troubleshooting/image4.png)

Les détails des erreurs vous aidera à identifier la cause possible et recommandation pour le problème.

![Surveiller les opérations de configuration, de protection et de restauration](media/site-recovery-monitoring-and-troubleshooting/image5.png)

Dans le cas ci-dessus il pourrait être une autre opération qui est en cours en raison de laquelle ignore configuration de Protection. Veiller à résoudre le problème conformément à la recommandation – après cet emplacement cliquez sur redémarrez votre pour relancer l’opération.

![Surveiller les opérations de configuration, de protection et de restauration](media/site-recovery-monitoring-and-troubleshooting/image6.png)

Option à redémarrer l’ordinateur n’est pas disponible pour toutes les opérations – pour ceux qui ne possèdent l’option de redémarrage naviguer vers l’objet et rétablir une nouvelle fois l’opération. Chaque tâche peut être annulé à tout moment lors de l’aide du bouton Annuler en cours.

![Surveiller les opérations de configuration, de protection et de restauration](media/site-recovery-monitoring-and-troubleshooting/image7.png)

## <a name="monitor-replication-health-for-virtual-machine"></a>Surveiller l’intégrité du réplication pour machine virtuelle

Fournisseur de récupération automatique du système central et surveillance via le portail Azure pour chacune des entités protégées à distance. Accéder aux éléments protégés après cet emplacement select nuages VMM ou groupes de PROTECTION. Onglet nuages VMM est uniquement pour les déploiements VMM basé et tous les autres scénarios ont les entités protégées sous l’onglet de groupes de PROTECTION.

![Surveiller l’intégrité du réplication pour machine virtuelle](media/site-recovery-monitoring-and-troubleshooting/image8.png)

Après cet emplacement sélectionnez l’entité protégée sous le cloud respectif ou le groupe de protection. Une fois que vous sélectionnez l’entité de tous les protégée autorisée opérations sont affichées dans le volet inférieur.

![Surveiller l’intégrité du réplication pour machine virtuelle](media/site-recovery-monitoring-and-troubleshooting/image9.png)

Comme indiqué ci-dessus au cas où la machine virtuelle que santé est critique – vous pouvez cliquer sur le bouton Détails de l’erreur dans le bas pour afficher l’erreur. Basé sur les « causes possibles » et « Recommandation « mentionnée résoudre le problème.

![Surveiller l’intégrité du réplication pour machine virtuelle](media/site-recovery-monitoring-and-troubleshooting/image10.png)

![Surveiller l’intégrité du réplication pour machine virtuelle](media/site-recovery-monitoring-and-troubleshooting/image11.png)

Remarque : Il existe des opérations actives qui sont en cours ou échec puis accéder à la vue tâches comme mentionné précédemment pour afficher l’erreur spécifique de travail.

## <a name="troubleshoot-on-premises-hyper-v-issues"></a>Résoudre les problèmes de Hyper-V en local

Se connecter à la console du Gestionnaire Hyper-V en local, sélectionnez la machine virtuelle et afficher l’état de la réplication.

![Résoudre les problèmes de Hyper-V en local](media/site-recovery-monitoring-and-troubleshooting/image12.png)

Dans ce cas *Fonctionnement de la réplication* est en cours indiquée comme critique : *Fonctionnement de la réplication affichage* pour afficher les détails.

![Résoudre les problèmes de Hyper-V en local](media/site-recovery-monitoring-and-troubleshooting/image13.png)

Dans les cas où la réplication est suspendue pour la machine virtuelle, sélectionnez *réplication*avec le bouton droit->*reprise de la réplication*
![résoudre les problèmes en local problèmes Hyper-V](media/site-recovery-monitoring-and-troubleshooting/image19.png)

En cas de machine virtuelle migre un nouvel hôte Hyper-V (dans le cluster ou un ordinateur autonome), ce qui a été configuré par le biais récupération automatique du système, réplication de la machine virtuelle n’est pas affectée. Assurez-vous que le nouvel hôte Hyper-V répond à tous les par-requis et est configuré à l’aide de la récupération automatique du système.

### <a name="event-log"></a>Journal des événements

| Sources d’événements                | Plus d’informations                                                                                                                                                                                           |
|-------------------------  |:------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------    |
| **Les journaux/Microsoft/VirtualMachineManager/serveur/administrateur de Service et d’applications** (Serveur VMM)   |  Cela permet d’utile journalisation pour le dépannage de nombreux autres facteurs VMM. |
| **Les journaux/MicrosoftAzureRecoveryServices/réplication des services et applications** (Hôte hyper-V)   | Cela permet d’utile journalisation pour résoudre les problèmes de l’Agent de Microsoft Azure récupération Services nombreux. <br/> ![Source d’événement pour hôte Hyper-V](media/site-recovery-monitoring-and-troubleshooting/eventviewer03.png) |
| **Service journaux/Microsoft/Azure Site récupération/fournisseur/opérationnelles et applications** (Hôte hyper-V)   | Cela permet d’utile journalisation pour résoudre les problèmes de Service de récupération de Site Microsoft Azure nombreux. <br/> ![Source d’événement pour hôte Hyper-V](media/site-recovery-monitoring-and-troubleshooting/eventviewer02.png) |
| **Les journaux/Microsoft/Windows/Hyper-V-le moniteur/administrateur de Service et d’applications** (Hôte hyper-V) | Cela permet d’utile journalisation pour résoudre les problèmes de gestion de nombreux Hyper-V machine virtuelle. <br/> ![Source d’événement pour hôte Hyper-V](media/site-recovery-monitoring-and-troubleshooting/eventviewer01.png) |


### <a name="hyper-v-replication-logging-options"></a>Options de journalisation de réplication Hyper-V

Tous les événements liés au réplica Hyper-V sont enregistrés dans Hyper-V-moniteur\\journal administrateur situé sous **journaux des Applications et Services\\Microsoft\\Windows**. En outre, un journal d’analyse peut être activé pour Hyper-V-moniteur. Pour activer ce journal, tout d’abord Vérifiez les journaux d’analyse et de débogage ne s’affichent dans l’Observateur d’événements. Ouvrez l’Observateur d’événements, puis dans le **menu Affichage**, cliquez sur **Afficher les analyse et les journaux de débogage**.

![Résoudre les problèmes de Hyper-V en local](media/site-recovery-monitoring-and-troubleshooting/image14.png)

Un journal d’analyse est visible sous Hyper-V-moniteur

![Résoudre les problèmes de Hyper-V en local](media/site-recovery-monitoring-and-troubleshooting/image15.png)

Dans le volet **Actions** , cliquez sur **Activer le journal**. Une fois activé, il apparaît dans **L’Analyseur de performances** comme une Session de suivi des événements situé sous **jeux de données de collecteur.**

![Résoudre les problèmes de Hyper-V en local](media/site-recovery-monitoring-and-troubleshooting/image16.png)

Pour afficher les informations collectées, tout d’abord arrêter la session de suivi en désactivant le journal, puis enregistrez le journal et rouvrez-le dans Observateur d’événements ou utiliser d’autres outils pour le convertir comme vous le souhaitez.



## <a name="reaching-out-for-microsoft-support"></a>Un public plus large du Support Microsoft

### <a name="log-collection"></a>Journal de collection de sites

Pour la protection de Site VMM, reportez-vous [à l’aide de Collection de journaux de récupération automatique du système outil plate-forme SDP (Support Diagnostics)](http://social.technet.microsoft.com/wiki/contents/articles/28198.asr-data-collection-and-analysis-using-the-vmm-support-diagnostics-platform-sdp-tool.aspx) pour collecter les journaux requis.

Pour la protection de Site Hyper-V, téléchargez l' [outil](https://dcupload.microsoft.com/tools/win7files/DIAG_ASRHyperV_global.DiagCab) et exécuter sur l’hôte Hyper-V pour collecter les journaux.

Pour des scénarios VMware/physique, consultez [Azure récupération journal de Collection de sites pour la protection de site VMware et physique](http://social.technet.microsoft.com/wiki/contents/articles/30677.azure-site-recovery-log-collection-for-vmware-and-physical-site-protection.aspx) pour collecter les journaux requis.

Outil collecte les fichiers journaux localement dans un sous-dossier nommé de manière aléatoire sous **%LocalAppData%\ElevatedDiagnostics**

![Exemples de procédures affichées à partir de la protection du site Hyper-V.](media/site-recovery-monitoring-and-troubleshooting/animate01.gif)

### <a name="opening-a-support-ticket"></a>Ouverture d’un tickets de support

Pour déclencher des tickets de support pour récupération automatique du système, atteindre Azure prend en charge à l’aide de l’URL à <http://aka.ms/getazuresupport>

## <a name="kb-articles"></a>Articles de la base de connaissances

-   [Comment faire pour conserver la lettre protégées machines virtuelles sont basculé ou migrée vers Azure](http://support.microsoft.com/kb/3031135)
-   [Comment gérer les locaux pour l’utilisation de la bande passante réseau protection Azure](https://support.microsoft.com/kb/3056159)
-   [Récupération automatique du système : erreur « la ressource de cluster n’a pas pu être trouvée » lorsque vous essayez d’activer la protection pour une machine virtuelle](http://support.microsoft.com/kb/3010979)
-   [Comprendre et résoudre les problèmes de Guide réplica Hyper-V](http://www.microsoft.com/en-in/download/details.aspx?id=29016) 

## <a name="common-asr-errors-and-their-resolutions"></a>Les erreurs de récupération automatique du système et leurs solutions

Voici les erreurs courantes que vous avez peut-être atteint et leurs solutions. Chacun de l’erreur présentée dans une autre page WIKI.

### <a name="general"></a>Général
-   <span style="color:green;">Nouveau</span> Tâches [échoue avec l’erreur « une opération est en cours ». Erreur 505, 514, 532](http://social.technet.microsoft.com/wiki/contents/articles/32190.azure-site-recovery-jobs-failing-with-error-an-operation-is-in-progress-error-505-514-532.aspx)
-   <span style="color:green;">Nouveau</span> Tâches [échoue avec l’erreur « Le serveur n’est pas connecté à Internet ». Erreur 25018](http://social.technet.microsoft.com/wiki/contents/articles/32192.azure-site-recovery-jobs-failing-with-error-server-isn-t-connected-to-the-internet-error-25018.aspx)

### <a name="setup"></a>Programme d’installation
-   [Le serveur VMM ne peut pas être enregistré en raison d’une erreur interne. Reportez-vous à la vue tâches dans le portail de récupération de Site pour plus d’informations sur l’erreur. Exécutez le programme d’installation à nouveau pour enregistrer le serveur.](http://social.technet.microsoft.com/wiki/contents/articles/25570.the-vmm-server-cannot-be-registered-due-to-an-internal-error-please-refer-to-the-jobs-view-in-the-site-recovery-portal-for-more-details-on-the-error-run-setup-again-to-register-the-server.aspx)
-   [Impossible d’établir une connexion à l’archivage sécurisé Gestionnaire de récupération Hyper-V. Vérifiez les paramètres de proxy ou réessayez plus tard.](http://social.technet.microsoft.com/wiki/contents/articles/25571.a-connection-cant-be-established-to-the-hyper-v-recovery-manager-vault-verify-the-proxy-settings-or-try-again-later.aspx)

### <a name="configuration"></a>Configuration
-   [Impossible de créer le groupe de Protection : une erreur s’est produite lors de la récupération de la liste des serveurs.](http://blogs.technet.com/b/somaning/archive/2015/08/12/unable-to-create-the-protection-group-in-azure-site-recovery-portal.aspx)
-   [Hôte Hyper-V cluster contient au moins une carte réseau statique, ou aucune cartes connectés ne sont configurées pour utiliser DHCP.](http://social.technet.microsoft.com/wiki/contents/articles/25498.hyper-v-host-cluster-contains-at-least-one-static-network-adapter-or-no-connected-adapters-are-configured-to-use-dhcp.aspx)
-   [VMM n’a pas les autorisations pour effectuer une action](http://social.technet.microsoft.com/wiki/contents/articles/31110.vmm-does-not-have-permissions-to-complete-an-action.aspx)
-   [Impossible de sélectionner le compte de stockage au sein de l’abonnement lors de la configuration de la protection](http://social.technet.microsoft.com/wiki/contents/articles/32027.can-t-select-the-storage-account-within-the-subscription-while-configuring-protection.aspx)

### <a name="protection"></a>Protection
- <span style="color:green;">Nouveau</span> Activer la Protection des [défectueux avec l’erreur « la Protection n’a pas pu être configurée pour la machine virtuelle ». Erreur 60007, 40003](http://social.technet.microsoft.com/wiki/contents/articles/32194.azure-site-recovery-enable-protection-failing-with-error-protection-couldn-t-be-configured-for-the-virtual-machine-error-60007-40003.aspx)
- <span style="color:green;">Nouveau</span> Activer la Protection des [défectueux avec l’erreur « la Protection n’a pas pu être activée pour la machine virtuelle. » Erreur 70094](http://social.technet.microsoft.com/wiki/contents/articles/32195.azure-site-recovery-enable-protection-failing-with-error-protection-couldn-t-be-enabled-for-the-virtual-machine-error-70094.aspx)
- <span style="color:green;">Nouveau</span> Erreur de migration live [23848 - la machine virtuelle est vont être déplacés à l’aide de type Live. Ceci peut interrompre l’état de protection de la récupération de la machine virtuelle.](http://social.technet.microsoft.com/wiki/contents/articles/32021.live-migration-error-23848-the-virtual-machine-is-going-to-be-moved-using-type-live-this-could-break-the-recovery-protection-status-of-the-virtual-machine.aspx) 
- [Activer la protection a échoué depuis Agent pas installé sur l’ordinateur hôte](http://social.technet.microsoft.com/wiki/contents/articles/31105.enable-protection-failed-since-agent-not-installed-on-host-machine.aspx)
- [Impossible de trouver un hôte approprié pour la machine virtuelle réplica - en raison de faible ressources de calcul](http://social.technet.microsoft.com/wiki/contents/articles/25501.a-suitable-host-for-the-replica-virtual-machine-can-t-be-found-due-to-low-compute-resources.aspx)
- [Un hôte approprié pour la machine virtuelle réplica est introuvable - en raison d’aucun réseau logique joint](http://social.technet.microsoft.com/wiki/contents/articles/25502.a-suitable-host-for-the-replica-virtual-machine-can-t-be-found-due-to-no-logical-network-attached.aspx)
- [Impossible de se connecter à l’ordinateur hôte réplica - connexion pas pu être établie](http://social.technet.microsoft.com/wiki/contents/articles/31106.cannot-connect-to-the-replica-host-machine-connection-could-not-be-established.aspx)


### <a name="recovery"></a>Récupération
- VMM ne pourrez pas terminer l’opération d’hôte :
    -   [Basculer vers le point de récupération sélectionné pour machine virtuelle : erreur accès général refusé.](http://social.technet.microsoft.com/wiki/contents/articles/25504.fail-over-to-the-selected-recovery-point-for-virtual-machine-general-access-denied-error.aspx)
    -   [Hyper-V n’a pas pu basculer vers le point de récupération sélectionné pour machine virtuelle : opération abandonnée essayez un point de récupération plus récent. (0x80004004)](http://social.technet.microsoft.com/wiki/contents/articles/25503.hyper-v-failed-to-fail-over-to-the-selected-recovery-point-for-virtual-machine-operation-aborted-try-a-more-recent-recovery-point-0x80004004.aspx)
    -   Une connexion avec le serveur n’a pas pu être établie (0x00002EFD)
        -   [Échec de l’activation de la réplication inverse pour machine virtuelle Hyper-V](http://social.technet.microsoft.com/wiki/contents/articles/25505.a-connection-with-the-server-could-not-be-established-0x00002efd-hyper-v-failed-to-enable-reverse-replication-for-virtual-machine.aspx)
        -   [Échec de l’activation de la réplication pour machine virtuelle machine virtuelle Hyper-V](http://social.technet.microsoft.com/wiki/contents/articles/25506.a-connection-with-the-server-could-not-be-established-0x00002efd-hyper-v-failed-to-enable-replication-for-virtual-machine-virtual-machine.aspx)
    -   [Basculement de la machine virtuelle n’a pas pu être valider](http://social.technet.microsoft.com/wiki/contents/articles/25508.could-not-commit-failover-for-virtual-machine.aspx)
-   [Le plan de récupération contient des machines virtuelles qui ne sont pas prêts à basculement planifié](http://social.technet.microsoft.com/wiki/contents/articles/25509.the-recovery-plan-contains-virtual-machines-which-are-not-ready-for-planned-failover.aspx)
-   [La machine virtuelle n’est pas prête pour basculement planifié](http://social.technet.microsoft.com/wiki/contents/articles/25507.the-virtual-machine-isn-t-ready-for-planned-failover.aspx)
-   [Machine virtuelle ne fonctionne pas et n’est pas éteint](http://social.technet.microsoft.com/wiki/contents/articles/25510.virtual-machine-is-not-running-and-is-not-powered-off.aspx)
-   [Absence d’opération de bande s’est produite sur une machine virtuelle et basculement valider a échoué](http://social.technet.microsoft.com/wiki/contents/articles/25507.the-virtual-machine-isn-t-ready-for-planned-failover.aspx)
-   Tester le basculement
    -   [Basculement n’a pas pu démarrer car le basculement test est en cours](http://social.technet.microsoft.com/wiki/contents/articles/31111.failover-could-not-be-initiated-since-test-failover-is-in-progress.aspx)
-   <span style="color:green;">Nouveau</span>  Basculement arrive à expiration avec 'PreFailoverWorkflow task WaitForScriptExecutionTaskTimeout' en raison des paramètres de configuration dans le groupe de sécurité réseau associé à l’ordinateur virtuel ou le sous réseau auquel l’ordinateur appartient à. Pour plus d’informations, consultez [« PreFailoverWorkflow tâche WaitForScriptExecutionTaskTimeout »](https://aka.ms/troubleshoot-nsg-issue-azure-site-recovery) .


### <a name="configuration-server-process-server-master-target"></a>Configuration serveur, processus, Masque cible
Configuration serveur (SC), processus (PS), Masque cible (MT)
-   [L’hôte ESXi sur lequel la PS/SC est hébergée comme une machine virtuelle échoue avec un écran violet du décès.](http://social.technet.microsoft.com/wiki/contents/articles/31107.vmware-esxi-host-experiences-a-purple-screen-of-death.aspx)

### <a name="remote-desktop-troubleshooting-after-failover"></a>Résolution des problèmes après le basculement de bureau à distance
-   De nombreux clients ont des problèmes pour vous connecter à l’échec sur machine virtuelle dans Azure rencontrés. [Utiliser le document de dépannage pour RDP la machine virtuelle](http://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx)

#### <a name="adding-a-public-ip-on-a-resource-manager-virtual-machine"></a>Ajout d’une adresse IP publique sur une machine virtuelle de Gestionnaire des ressources
Si le bouton de **connexion** dans le portail est grisé et vous n’êtes pas connecté à Azure via une Express itinéraire ou une connexion VPN de Site à Site, vous devez créer et affecter votre machine virtuelle une adresse IP publique avant de pouvoir utiliser RDP/SSH. Suivez les étapes ci-dessous pour ajouter une adresse IP publique sur l’interface réseau de la machine virtuelle.  

![Échec de l’ajout d’une adresse IP publique dans l’interface de réseau de sur machine virtuelle](media/site-recovery-monitoring-and-troubleshooting/createpublicip.gif)
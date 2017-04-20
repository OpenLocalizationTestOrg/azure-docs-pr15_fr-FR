<properties
   pageTitle="Services de récupération vault FAQ | Microsoft Azure"
   description="Cette version du Forum aux questions prend en charge la version préliminaire publique du service sauvegarde Azure. Réponses aux questions fréquemment posées sur l’agent de sauvegarde, sauvegarde et rétention, restauration, sécurité et d’autres questions courantes sur la solution de sauvegarde Azure."
   services="backup"
   documentationCenter=""
   authors="markgalioto"
   manager="jwhit"
   editor=""
   keywords="solution de sauvegarde ; service de sauvegarde"/>

<tags
   ms.service="backup"
   ms.workload="storage-backup-recovery"
     ms.tgt_pltfrm="na"
     ms.devlang="na"
     ms.topic="get-started-article"
     ms.date="10/21/2016"
     ms.author="trinadhk; markgal; jimpark;"/>

# <a name="recovery-services-vault---faq"></a>Archivage sécurisé des Services de récupération - Forum aux questions


Cet article fournit des informations spécifiques aux Services de récupération l’archivage sécurisé et il complète le [Forum aux questions de sauvegarde Azure](backup-azure-backup-faq.md). Le Forum aux questions de sauvegarde Azure fournit l’ensemble des questions et réponses concernant le service de sauvegarde Azure.  

Vous pouvez poser des questions sur sauvegarde Azure dans la section Disqus de cet article ou un article connexe. Vous pouvez également publier des questions sur le service de sauvegarde Azure sur le [forum de discussion](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazureonlinebackup).

## <a name="recovery-services-vaults-are-resource-manager-based-are-backup-vaults-classic-mode-still-supported-br"></a>Chambres fortes de Services de récupération sont en fonction de gestionnaire de ressources. Chambres fortes sauvegarde (mode classique) sont toujours gérées ? <br/>
Oui, chambres fortes sauvegarde sont toujours pris en charge. Créer des chambres fortes sauvegarde dans le [portail classique](https://manage.windowsazure.com). Créer les Services de récupération chambres fortes dans le [portail Azure](https://portal.azure.com). Cependant nous vous recommandons vivement de vous permet de créer l’archivage sécurisé des services de récupération en tant que toutes les futures améliorations est disponible que dans les Services de récupération l’archivage sécurisé.

## <a name="can-i-migrate-a-backup-vault-to-a-recovery-services-vault-br"></a>Puis-je migrer un archivage sécurisé sauvegarde vers un archivage sécurisé aux Services de récupération ? <br/>
Malheureusement non, pour le moment que vous ne peut pas migrer le contenu d’un archivage sécurisé sauvegarde vers un archivage sécurisé aux Services de récupération. Nous travaillons sur l’ajout de cette fonctionnalité, mais elle n’est pas disponible dans le cadre de la version d’évaluation.

## <a name="do-recovery-services-vaults-support-classic-vms-or-resource-manager-based-vms-br"></a>Les Services de récupération chambres fortes prennent en charge machines virtuelles classiques ou le Gestionnaire de ressources en fonction des machines virtuelles ? <br/>
Chambres fortes de Services de récupération prend en charge les deux modèles.  Vous pouvez sauvegarder une machine virtuelle créée dans le portail classique (qui sont des machines virtuelles mode classique), ou une machine virtuelle créée dans le portail Azure (qui sont en fonction de gestionnaire de ressources) à un archivage sécurisé aux Services de récupération.

## <a name="i-have-backed-up-my-classic-vms-in-backup-vault-now-i-want-to-migrate-my-vms-from-classic-mode-to-resource-manager-mode--how-can-i-backup-them-in-recovery-services-vault"></a>J’ai sauvegardé mon machines virtuelles classiques dans l’archivage sécurisé sauvegarde. Je souhaite migrer mes machines virtuelles à partir du mode classique au mode directeur des ressources.  Comment puis-je sauvegarder les dans l’archivage sécurisé des services de récupération ?
Machines virtuelles classique en l’archivage sécurisé sauvegarde des sauvegardes ne migrer automatiquement vers l’archivage sécurisé des services de récupération lorsque vous migrez les ordinateurs virtuels classique en mode directeur des ressources. Suivez ces étapes pour la migration de sauvegardes de machines virtuelles :

1. Dans l’archivage sécurisé sauvegarde, accédez à l’onglet **Éléments protégés** et sélectionnez la machine virtuelle. Cliquez sur [Arrêter la Protection](backup-azure-manage-vms-classic.md#stop-protecting-virtual-machines). N’activez pas *Supprimer les données de sauvegarde associées* option **désactivée**.
2. Migrer la machine virtuelle à partir du mode classique vers le mode Gestionnaire de ressources. Vérifiez que le stockage et réseau correspondant à la machine virtuelle est également migré vers le mode Gestionnaire de ressources.
3. Créer un archivage sécurisé des services de récupération et configurer la sauvegarde sur l’ordinateur virtuel migré à l’aide de **sauvegarde** d’action en haut du tableau de bord l’archivage sécurisé. Pour plus d’informations sur [l’activation de sauvegarde dans l’archivage sécurisé des services de récupération](backup-azure-vms-first-look-arm.md)

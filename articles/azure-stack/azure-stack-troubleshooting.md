<properties
    pageTitle="Résolution des problèmes de Microsoft Azure pile | Microsoft Azure"
    description="Résolution des problèmes Azure pile."
    services="azure-stack"
    documentationCenter=""
    authors="heathl17"
    manager="byronr"
    editor=""/>

<tags
    ms.service="azure-stack"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/13/2016"
    ms.author="helaw"/>

# <a name="microsoft-azure-stack-troubleshooting"></a>Résolution des problèmes de Microsoft Azure pile

Ce document fournit des informations de dépannage courantes pour pile Azure.

Pour optimiser les performances, assurez-vous que votre environnement de déploiement répond à toutes les [conditions requises](azure-stack-deploy.md) et [préparations](azure-stack-run-powershell-script.md) avant d’installer. 

Les recommandations pour la résolution des problèmes qui sont décrits dans cette section proviennent de plusieurs sources et peuvent ou ne peuvent pas résoudre le problème particulier. Si vous rencontrez un problème ne présentée pas, veillez à vérifier la [Pile Azure Forum MSDN sur](https://social.msdn.microsoft.com/Forums/azure/home?forum=azurestack) pour obtenir un support technique et des informations supplémentaires.

Exemples de code sont fournis en l’état et les résultats attendus ne peut pas être garanties. Cette section est soumis à des modifications fréquentes et mises à jour comme améliorations au produit sont implémentées.

  

## <a name="known-issues"></a>Problèmes connus

 - Vous pouvez voir les erreurs suivantes, ne se terminant pas pendant le déploiement, qui affectant la réussite du déploiement :
     - « Le terme « C:\WinRM\Start-Logging.ps1 » n’est pas reconnu »
     - « EceAction appeler : Impossible d’indexer dans un tableau null » 
     - « InvokeEceAction : Impossible de lier argument au paramètre « Message » car il s’agit d’une chaîne vide. »
 - Vous pouvez voir déploiement échouent à l’étape 60.61.93 avec l’erreur « Application avec identificateur 'URI' non trouvé ». Ce comportement est en raison de la façon dont les applications sont enregistrées dans Azure Active Directory.  Si vous recevez cette erreur, passez à [réexécuter le script d’installation](azure-stack-rerun-deploy.md) de l’étape 60.61.93 jusqu'à ce que le déploiement est terminé.
 - Vous verrez que la ressource **Jeu de disponibilité** sur le marché s’affiche sous la catégorie **VirtualMachine processeur** – cet aspect est uniquement un problème d’aspect.
 - Lorsque vous créez une nouvelle machine virtuelle dans le portail, dans l’étape **Concepts de base** , l’option de stockage par défaut SSD.  Ce paramètre doit être modifié sur le disque dur ou la **taille** à l’étape du déploiement d’ordinateur virtuel, vous ne verrez pas les tailles de mémoire virtuelle disponibles pour sélectionner et de continuer à déployer. 
 - Vous verrez AzureRM PowerShell modules ne sont plus installés par défaut sur l’ordinateur virtuel MAS CON01 (dans TP1 Ceci était nommé ClientVM). Ce comportement est voulu par la conception, car il existe une autre méthode pour [installer ces modules et vous connecter](azure-stack-connect-powershell.md).  
 - Vous verrez que le fournisseur de ressources **Microsoft.Insights** n’est pas enregistré automatiquement pour les abonnements du client. Si vous voulez voir les données d’analyse d’un ordinateur virtuel déployé comme un client, exécutez la commande suivante à partir de PowerShell (après vous [installer et se connecter](azure-stack-connect-powershell.md) en tant qu’un client) : 
       
        Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Insights 

 - Vous verrez fonctionnalité dans le portail d’exportation pour le groupe de ressources, même si aucun texte n’est affiché et disponible pour exporter.      
 - Vous pouvez démarrer un déploiement des ressources de stockage plus de quota disponible.  Ce déploiement échoue et les ressources de compte seront suspendus.  Il existe deux options de mise à jour disponibles :
     - Administrateur de service peut augmenter le quota, bien que les modifications ne sont pas immédiatement appliquées et fréquemment prendre jusqu'à une heure de se propager.
     - Service administrateur peut créer un plan de module complémentaire avec quota supplémentaire que le client peut ensuite ajouter à l’abonnement.
 - Lorsque vous utilisez le portail pour créer des machines virtuelles sur les environnements Azure pile avec identité dans « Azure - Chine », vous ne verrez pas les tailles de mémoire virtuelle disponibles pour sélectionner dans l’étape de **taille** de déploiement d’ordinateur virtuel et ne pourrez pas continuer à déployer.
 - Vous pouvez voir un échec du déploiement dans le portail, lorsque la machine virtuelle a réellement déployé avec succès.
 - Lorsque vous supprimez un plan, offre ou abonnement, machines virtuelles ne peuvent pas être supprimés.
 - Vous verrez les extensions de machine virtuelle sur le marché.
 - Vous ne pouvez pas déployer une machine virtuelle à partir d’une image de machine virtuelle enregistré.
 - Clients peuvent voir les services qui ne figurent pas dans son abonnement.  Essaient de clients déployer ces ressources, ils reçoivent une erreur.  Exemple : Abonnement client inclut uniquement les ressources de stockage.  Option pour créer d’autres ressources comme machines virtuelles apparaît dans le client.  Dans ce scénario, lorsqu’un client essaie de déployer une machine virtuelle, ils reçoivent un message indiquant que la machine virtuelle ne peut pas être créée. 
 - Lorsque vous installez TP2, vous ne devez pas activer l’hôte de système d’exploitation du disque dur virtuel fourni où vous exécutez le script de mise en pile Azure, ou vous pouvez recevoir un message d’erreur indiquant que Windows de messagerie se terminant bientôt.


## <a name="deployment"></a>Déploiement

### <a name="deployment-failure"></a>Échec du déploiement
Si vous rencontrez un problème lors de l’installation, le programme d’installation pile Azure permet de vous permettre de continuer un échec de l’installation en suivant la [réexécuter les étapes de déploiement](azure-stack-rerun-deploy.md).

### <a name="at-the-end-of-the-deployment-the-powershell-session-is-still-open-and-doesnt-show-any-output"></a>À la fin du déploiement, la session PowerShell est toujours ouverte et ne s’affiche pas de sortie

Ce comportement est probablement uniquement le résultat du comportement par défaut d’une fenêtre de commande PowerShell, lorsqu’elle a été sélectionnée. Le déploiement du contact a réussi, mais le script a été interrompu lors de la sélection de la fenêtre. Vous pouvez vérifier que c’est le cas en recherchant le mot « select » dans la barre de titre de la fenêtre de commande.  Appuyez sur la touche ÉCHAP pour annuler la sélection de celle-ci, puis le message de saisie semi-automatique doit figurer après celui-ci.

## <a name="templates"></a>Modèles

### <a name="azure-template-wont-deploy-to-azure-stack"></a>Modèle Azure ne déployer à pile d’Azure

Vérifiez que :

- Le modèle doit utiliser un service Microsoft Azure qui est déjà disponible ou dans un aperçu de pile Azure.
- Les API utilisées pour une ressource spécifique sont pris en charge par l’instance Azure pile locale et que vous ciblez un emplacement valide (« local » dans Azure pile Technical Preview (pt) 2, vs « États-Unis Extrême-Orient » ou « Inde Sud » dans Azure).
- Vous consultez [cet article](https://github.com/Azure/AzureStack-QuickStart-Templates/blob/master/README.md) sur les applets de commande Test-AzureRmResourceGroupDeployment petites différences dans le Gestionnaire de ressources Azure syntaxe d’intercepter.

Vous pouvez également utiliser les modèles de pile Azure déjà fournis dans le [référentiel GitHub](http://aka.ms/AzureStackGitHub/) pour vous aider à commencer.

## <a name="virtual-machines"></a>Machines virtuelles

### <a name="after-starting-my-microsoft-azure-stack-poc-host-all-my-tenants-vms-are-gone-from-hyper-v-manager-and-come-back-automatically-after-waiting-a-bit"></a>Après avoir démarré hôte de sites Mon Microsoft Azure pile du contact, tous mes clients machines virtuelles sont disparaît Gestionnaire Hyper-V et revenez automatiquement après en attente un peu ?

Comme le système revient le sous-système de stockage et vous avez besoin de RPs pour déterminer la cohérence. Le temps nécessaire dépend du matériel et aux spécifications en cours d’utilisation, mais il est peu de temps après un redémarrage de l’hôte de client machines virtuelles de revenir et reconnue.

### <a name="i-have-deleted-some-virtual-machines-but-still-see-the-vhd-files-on-disk-is-this-behavior-expected"></a>J’ai ont supprimés certains ordinateurs virtuels, mais toujours voir ces fichiers sur le disque. Ce comportement est prévu ?

Oui, il s’agit d’un comportement attendu. Il a été conçu de cette façon, car :

- Lorsque vous supprimez une machine virtuelle, disques durs virtuels ne sont pas supprimés. Disques sont des ressources distinctes dans le groupe de ressources.
- Lorsqu’un compte de stockage est supprimé, la suppression est visible immédiatement via Azure Gestionnaire de ressources (portail, PowerShell), mais les disques qu’il contient sont toujours conservées dans le stockage avant l’exécution de nettoyage.

Si vous voyez « orphelines » virtuels, il est important de savoir si elles font partie du dossier pour un compte de stockage qui a été supprimé. Si le compte de stockage n’a pas été supprimé, il est normal qu’ils sont toujours disponibles.

Vous pouvez en savoir plus sur la configuration du seuil de rétention dans [Gérer les comptes de stockage](azure-stack-manage-storage-accounts.md).

## <a name="installation-steps"></a>Étapes d’installation
Les informations suivantes sur les étapes d’installation pile Azure peuvent vous être utiles pour résoudre ce problème :  

| Index | Nom | Description|
| ----- | ----- | -----|
|0.11 | (PED) Valider des Machines physiques | Valider la configuration matérielle et du système d’exploitation sur les nœuds physiques. |
| 0,12 | (PED) Configurer la mise en réseau Machines physiques pour contact | Configuration des commutateurs de réseau virtuel et interfaces. |
| 0,14 | (PED) Déploiement de domaine | Déploiement d’Active Directory sur Machine virtuelle. |
| 0,15. | (PED) Configurer le serveur de domaine | Configurer les serveurs de domaine avec les groupes de sécurité etc.. |
| 0,16 | (PED) Configurer Machine physique | Configurer la mise en réseau, l’accès au domaine et les administrateurs local le programme d’installation. |
| 0,18 | (ARRÊTER) Configurer Cluster de stockage | Créez cluster de stockage, créez un serveur de pool et un fichier de stockage. |
| 0.19 | (IPC) Infrastructure de tissu d’installation | Configurer les conditions préalables pour le déploiement de tissu. |
| 0,21 | (NET) Configuration BGP et NAT | Installe BGP et NAT - nécessaire uniquement pour un nœud. |
| 0.22 | (NET) Configurer NAT et serveur de temps | Synchronise le serveur de temps et configure les entrées de NAT. |
| 40.41 | (IPC) Créer invitées | Créer la gestion des machines virtuelles. |
| 40.42 | (FBI) Configurer la PowerShell JEA | Configurer la PowerShell JEA pour tous les rôles. |
| 40.43 | (FBI) Configurer la pile Azure autorité de certification | Installe autorité de Certification pile Azure. |
| 40.44 | (FBI) Configurer autorité de Certification pile Azure | Configure autorité de Certification pile Azure. |
| 40.45 | (NET) Configurer la NC sur machines virtuelles | Installe NC sur les invitées |
| 40.46 | (NET) Configurer NC sur machines virtuelles | Configurer NC sur les invitées |
| 40.47 | (NET) Configurer invitées | Configurer la gestion des machines virtuelles avec des utilisateurs NC. |
| 60.61.81 | (FBI) Déployer pile Azure TISSU simultanée Services - préalable FabricRing | Crée VIP pour FabricRing |
| 60.61.82 | (FBI) Déployer pile Azure TISSU simultanée Services - déployer TISSU simultanée Cluster | Installe et configure Azure pile TISSU simultanée Cluster. |
| 60.61.83 | (FBI) Déployer des Extensions d’administration pour les fournisseurs de ressources | Installation des Extensions d’administration pour les fournisseurs de ressources |
| 60.61.84 | (ACS) Définir l’espace de stockage Azure cohérentes dans niveau nœud. | Installe et configure stockage Azure cohérentes dans niveau nœud. |
| 60.61.85 | (ACS) Définir l’espace de stockage Azure cohérentes dans niveau cluster. | Installe et configure stockage Azure cohérentes dans niveau cluster. |
| 60.61.86 | (FBI) Déployer les Services de contrôleur de pile Azure TISSU simultanée - préalable | Conditions préalables pour InfraServiceController |
| 60.61.87 | (FBI) Déployer les Services de contrôleur de pile Azure TISSU simultanée - préalable | Conditions préalables pour IPC |
| 60.61.88 | (FBI) Déployer les Services de contrôleur de pile Azure TISSU simultanée - préalable | Conditions préalables pour ASAppGateway |
| 60.61.89 | (FBI) Déployer les Services de contrôleur de pile Azure TISSU simultanée - préalable | Conditions préalables pour contrôleur de stockage |
| 60.61.90 | (FBI) Déployer les Services de contrôleur de pile Azure TISSU simultanée - préalable | Conditions préalables pour HealthMonitoring |
| 60.61.91 | (FBI) Déployer les Services de contrôleur de pile Azure TISSU simultanée - préalable | Conditions préalables pour pertinents |
| 60.61.92 | (FBI) Déployer les Services de contrôleur de pile Azure TISSU simultanée - préalable | Conditions préalables pour PMM |
| 60.61.93 | (Katal) Créer des identités de Service AzureStack | Créer des Applications Azure Graph et entités de Service dans AAD. |
| 60.61.94 | (NET) Machines virtuelles configuration GW | Installe GW sur les ordinateurs virtuels invités. |
| 60.61.95 | (NET) Configurer des ordinateurs virtuels GW | Configure GW sur les ordinateurs virtuels invités. |
| 60.61.96 | (NET) Déployer les IDN sur hôtes | Déployer les IDN sur hôtes de l’infrastructure |
| 60.61.97 | (NET) Configurer les IDN | Configurer les IDN rôle |
| 60.61.98 | (FBI) Machines virtuelles WSUS le programme d’installation | Installe déconnecté sur les ordinateurs virtuels invités. |
| 60.61.99 | (FBI) Configurer des ordinateurs virtuels WSUS | Configure déconnecté sur les ordinateurs virtuels invités. |
| 60.61.100 | (FBI) Configuration des machines virtuelles SQL Azure | Installations Azure SQL server sur les invitées |
| 60.61.101 | (Katal) Paramétrage des conditions préalables pour les machines virtuelles a été. | Configure les conditions préalables pour Microsoft Azure pile sur les ordinateurs virtuels invités. |
| 60.61.102 | (Katal) Le programme d’installation a été machines virtuelles | Installe Microsoft Azure pile sur les ordinateurs virtuels invités. |
| 60.120.121 | (FBI) Déployer des fournisseurs de ressources et contrôleurs | Installe des fournisseurs de ressources et contrôleurs |
| 60.120.121 | (FBI) Déployer des fournisseurs de ressources et contrôleurs | Installe des fournisseurs de ressources et contrôleurs |
| 60.120.121 | (FBI) Déployer des fournisseurs de ressources et contrôleurs | Installe des fournisseurs de ressources et contrôleurs |
| 60.120.121 | (FBI) Déployer des fournisseurs de ressources et contrôleurs | Installe des fournisseurs de ressources et contrôleurs |
| 60.120.121 | (FBI) Déployer des fournisseurs de ressources et contrôleurs | Installe des fournisseurs de ressources et contrôleurs |
| 60.120.121 | (FBI) Déployer des fournisseurs de ressources et contrôleurs | Installe des fournisseurs de ressources et contrôleurs |
| 60.120.121 | (FBI) Déployer des fournisseurs de ressources et contrôleurs | Installe des fournisseurs de ressources et contrôleurs |
| 60.120.121 | (FBI) Déployer des fournisseurs de ressources et contrôleurs | Installe des fournisseurs de ressources et contrôleurs |
| 60.120.122 | (FBI) Configuration du contrôleur | Configure les contrôleurs |
| 60.120.123 | (Katal) Configurer avons machines virtuelles | Configure Microsoft Azure pile d’ordinateurs virtuels invités. |
| 60.120.124 | (Katal) Configuration de AAD la pile Azure. | Configure pile Azure avec Azure AD. |
| 60.120.125 | (Katal) Installer ADFS | Installe des Services de fédération Active Directory (AD FS) |
| 60.120.126 | (Katal) Installer ADFS/Graph | Installe Graph pile Azure |
| 60.120.127 | (Katal) Configurer les services ADFS | Configure les Services de fédération Active Directory (AD FS) |
| 60.140.141 | (FBI) Configurer SRP | Configure le fournisseur de ressources de stockage |
| 60.140.142 | (ACS) Configurer le stockage Azure cohérentes. | Configure le stockage Azure cohérentes. |
| 60.140.143 | (FBI) Créer des comptes de stockage | Créer tous les comptes de stockage devant être utilisé par des fournisseurs différents. |
| 60.140.144 | (FBI) Utilisation de Registre pour SRP | S’inscrire à l’utilisation de fournisseur de stockage. |
| 60.140.145 | (IPC) Migrer des machines virtuelles créés, Hosts et Cluster à IPC | Migre les objets des machines virtuelles, Hosts et Cluster créé à IPC |
| 60.140.146 | (FBI) Configurer Windows Defender | Configure Windows Defender |
| 60.160.161 | (LUN) Configurer l’Agent de surveillance | Configure l’Agent de surveillance |
| 60.160.162 | (FBI) Conditions préalables NRP | Conditions préalables pour les installations NRP |
| 60.160.163 | (FBI) Déploiement NRP | Installations NRP  |
| 60.160.164 | (FBI) Configuration NRP | Configure NRP |
| 60.160.165 | (FBI) Conditions préalables PRC | Conditions préalables pour les installations PRC |
| 60.160.166 | (FBI) Déploiement PRC | Installe PRC |
| 60.160.167 | (FBI) Configuration PRC | Configure PRC |
| 60.160.168 | (FBI) Conditions préalables FRP | Conditions préalables pour les installations FRP |
| 60.160.169 | (FBI) Déploiement FRP | Installations FRP  |
| 60.160.170 | (FBI) Configuration FRP | Configure FRP |
| 60.160.174 | (FBI) URP prérequises | Conditions préalables pour les installations URP |
| 60.160.175 | (FBI) Déploiement URP | Installations URP  |
| 60.160.176 | (FBI) Configuration URP | Configure URP |
| 60.160.171 | (FBI) Ensemble de correctifs requis | Conditions préalables pour les installations ensemble de correctifs |
| 60.160.172 | (FBI) Déploiement de l’ensemble de correctifs | Installations ensemble de correctifs  |
| 60.160.173 | (FBI) Configuration de l’ensemble de correctifs | Configure l’ensemble de correctifs |
| 60.160.177 | (KV) Conditions préalables KeyVault | Conditions préalables pour les installations KeyVault |
| 60.160.178 | (KV) Déploiement KeyVault | Installe KeyVault  |
| 60.160.179 | (KV) Configuration de KeyVault | Configure KeyVault | 
| 60.190.191 | (FBI) Configurer la galerie | Configurer la galerie |
| 60.190.192 | (FBI) Configurer les Services de sonneries TISSU | Configurer les Services de sonneries TISSU |
| 60.221 | (FBI) Machines virtuelles Console le programme d’installation | Installe Console server sur les ordinateurs virtuels invités. |
| 60.222 | (FBI) Machines virtuelles Console le programme d’installation | Déplacer DVM contenu à la Console machine virtuelle. |
| 251 | Préparer pour le redémarrage de l’hôte futur | Définir la stratégie de redémarrage |


## <a name="next-steps"></a>Étapes suivantes

[Forum aux questions](azure-stack-FAQ.md)

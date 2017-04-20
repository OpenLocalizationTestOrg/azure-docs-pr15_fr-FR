<properties
    pageTitle="Répliquer machines virtuelles de Hyper-V dans nuages VMM sur un site VMM secondaire | Microsoft Azure"
    description="Cet article décrit comment répliquer machines virtuelles Hyper-V dans nuages VMM sur un site VMM secondaire avec récupération de Site Azure."
    services="site-recovery"
    documentationCenter=""
    authors="rayne-wiselman"
    manager="jwhit"
    editor=""/>

<tags
    ms.service="site-recovery"
    ms.workload="backup-recovery"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/23/2016"
    ms.author="raynew"/>

# <a name="replicate-hyper-v-virtual-machines-in-vmm-clouds-to-a-secondary-vmm-site"></a>Répliquer machines virtuelles de Hyper-V dans nuages VMM sur un site VMM secondaire

> [AZURE.SELECTOR]
- [Portail Azure](site-recovery-vmm-to-vmm.md)
- [Portail classique](site-recovery-vmm-to-vmm-classic.md)
- [PowerShell - Gestionnaire de ressources](site-recovery-vmm-to-vmm-powershell-resource-manager.md)

Le service de récupération de Site Azure contribue à votre stratégie d’entreprise continuité et de reprise récupération (BCDR) en organiser réplication, basculement et la restauration des machines virtuelles et des serveurs physiques. Machines peuvent être répliquées vers Azure, ou à un centre de données secondaire en local. Pour un bref aperçu lisez [Nouveautés récupération de Site Azure ?](site-recovery-overview.md)

## <a name="overview"></a>Vue d’ensemble

Cet article décrit comment répliquer machines virtuelles de Hyper-V sur les serveurs hôtes Hyper-V qui sont gérées dans nuages VMM sur site VMM secondaire à l’aide de la récupération de Site Azure.

L’article inclut les conditions préalables, vous montre comment configurer un archivage sécurisé récupération de Site, installer le fournisseur de récupération de Site Azure sur source VMM serveurs cibles Inscrivez les serveurs dans l’archivage sécurisé, configurer les paramètres de protection pour les nuages VMM et, puis activer la protection des ordinateurs virtuels Hyper-V. Terminer la configuration en testant le basculement pour vous assurer que tout fonctionne comme prévu.

Publier des commentaires ou des questions en bas de cet article, ou sur le [Forum de Services de récupération Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

## <a name="architecture"></a>Architecture

L’image ci-dessous montre les canaux de communication différent et ports utilisés par récupération de Site Azure pour l’orchestration et réplication

![Topologie E2E](./media/site-recovery-vmm-to-vmm-classic/e2e-topology.png)

## <a name="before-you-start"></a>Avant de commencer

Vérifiez que vous respectez ces conditions préalables en place :

**Conditions préalables** | **Plus d’informations**
--- | ---
**Azure**| Vous avez besoin d’un compte [Microsoft Azure](https://azure.microsoft.com/) . Vous pouvez commencer avec une [version d’évaluation gratuite](https://azure.microsoft.com/pricing/free-trial/). [En savoir plus](https://azure.microsoft.com/pricing/details/site-recovery/) sur les tarifs de récupération de Site.
**VMM** | Vous devez au moins un serveur VMM.<br/><br/>Le serveur VMM doit être en cours d’exécution au moins System Center 2012 SP1 avec les dernières mises à jour cumulatives.<br/><br/>Si vous souhaitez définir la protection avec un seul serveur VMM, vous devez au moins deux nuages configurés sur le serveur.<br/><br/>Si vous voulez déployer la protection avec deux serveurs VMM, chaque serveur doit avoir au moins un nuage configuré sur le serveur VMM principal à protéger et un nuage configuré sur le serveur VMM secondaire que vous souhaitez utiliser pour la protection et de restauration<br/><br/>Tous les nuages VMM doivent avoir le profil de fonctionnalité Hyper-V.<br/><br/>Le cloud source que vous voulez protéger doit contenir un ou plusieurs groupes hôtes VMM.<br/><br/>En savoir plus sur la configuration nuages VMM dans [procédure pas à pas : création de nuages privés avec System Center 2012 SP1 VMM](http://blogs.technet.com/b/keithmayer/archive/2013/04/18/walkthrough-creating-private-clouds-with-system-center-2012-sp1-virtual-machine-manager-build-your-private-cloud-in-a-month.aspx) sur le blog de Keith Mayer.
**Hyper-V** | Vous avez besoin d’un ou plusieurs des serveurs hôte Hyper-V dans les groupes d’hôtes VMM principaux et secondaires et une ou plusieurs machines virtuelles sur chaque serveur hôte Hyper-V.<br/><br/>Les serveurs Hyper-V hôte et cible doivent exécuter au minimum Windows Server 2012 avec le rôle Hyper-V et ont installé les dernières mises à jour.<br/><br/>N’importe quel serveur Hyper-V contenant des machines virtuelles à protéger doit se trouver dans un nuage VMM.<br/><br/>Si vous exécutez Hyper-V dans un cluster, notez qu’intermédiaire cluster n’est pas créée automatiquement si vous avez un cluster de basée sur l’adresse IP statique. Vous devez configurer manuellement l’intermédiaire cluster. [En savoir plus](https://www.petri.com/use-hyper-v-replica-broker-prepare-host-clusters) dans une entrée de blog de Marie Finn.
**Mappage réseau** | Vous pouvez configurer le mappage de réseau pour vous assurer que les machines virtuelles répliquées sont placés optimale sur vos serveurs d’hôtes Hyper-V secondaires après le basculement, et qu’ils peuvent se connecter à des réseaux machine virtuelle appropriés. Si vous ne configurez mappage réseau, réplica machines virtuelles n’est pas connecté à un réseau après le basculement.<br/><br/>Pour configurer le mappage réseau lors du déploiement, assurez-vous que les ordinateurs virtuels sur le serveur hôte source Hyper-V sont connectés à un réseau VMM VM. Ce réseau doit être lié à un réseau logique est associé avec le cloud. < br /<br/>Le cloud cible sur le serveur VMM secondaire que vous utilisez pour récupération doit avoir un réseau machine virtuelle correspondant configuré, et il doit à son tour être lié à un réseau logique correspondant qui est associé avec le cloud cible.<br/><br/>[En savoir plus](site-recovery-network-mapping.md) sur le mappage réseau.
**Mappage d’espace de stockage** | Par défaut lors de la réplication une machine virtuelle sur un serveur source Hyper-V hôte sur un serveur de hôte cible Hyper-V, données répliquées sont stockées dans l’emplacement par défaut qui est indiqué pour l’hôte Hyper-V cible dans le Gestionnaire Hyper-V. Pour contrôler davantage l’emplacement de stockage des données dupliquées, vous pouvez configurer le mappage de stockage<br/><br/> Pour configurer le mappage de stockage, vous devez configurer les classifications de stockage sur la source et cible serveurs VMM avant de commencer le déploiement. [En savoir plus](site-recovery-storage-mapping.md).


## <a name="step-1-create-a-site-recovery-vault"></a>Étape 1 : Créer un archivage sécurisé récupération de Site

1. Connectez-vous au [Portail de gestion](https://portal.azure.com) du serveur VMM que vous souhaitez enregistrer.

2. Développez **Data Services** > **Services de récupération** et cliquez sur **L’archivage sécurisé récupération de Site**.

3. Cliquez sur **créer une nouvelle** > **Création rapide**.

4. Dans la zone **nom**, entrez un nom convivial pour identifier l’archivage sécurisé.

5. Dans la **zone** sélectionner la zone géographique pour l’archivage sécurisé. Pour rester informé des régions pris en charge Voir disponibilité géographique dans [Les détails du prix Azure Site récupération](http://go.microsoft.com/fwlink/?LinkId=389880).

6. Cliquez sur **créer l’archivage sécurisé**.

    ![Créer l’archivage sécurisé](./media/site-recovery-vmm-to-vmm-classic/create-vault.png)

Dans la barre d’état, vérifiez que l’archivage sécurisé a été créée. L’archivage sécurisé est indiquée comme étant **actif** sur la page principale dans les Services de récupération.

## <a name="step-2-generate-a-vault-registration-key"></a>Étape 2 : Générer une clé d’inscription de l’archivage sécurisé

Générer une clé d’enregistrement dans l’archivage sécurisé. Après avoir téléchargé le fournisseur de récupération de Site Azure et installez-le sur le serveur VMM, vous allez utiliser cette clé pour inscrire le serveur VMM dans l’archivage sécurisé.

1. Dans la page **Services de récupération** , cliquez sur l’archivage sécurisé pour ouvrir la page de démarrage rapide. Démarrage rapide peut également être ouvert à tout moment à l’aide de l’icône.

    ![Icône de démarrage rapide](./media/site-recovery-vmm-to-vmm-classic/quick-start-icon.png)

2. Dans la liste déroulante, sélectionnez **entre deux sites VMM en local**.
3. Dans les **Serveurs de VMM préparer**, cliquez sur **Générer un fichier clé d’inscription**. Le fichier de clé est généré automatiquement et est valable 5 jours après sa création. Si vous n’êtes pas accéder au portail Azure à partir du serveur VMM vous devez copier ce fichier sur le serveur.

    ![Clé d’enregistrement](./media/site-recovery-vmm-to-vmm-classic/register-key.png)

## <a name="step-3-install-the-azure-site-recovery-provider"></a>Étape 3 : Installer le fournisseur de récupération de Site Azure

4. Dans la page de **Démarrage rapide** , dans les **serveurs VMM préparer**, cliquez sur **Télécharger Microsoft Azure récupération fournisseur de Site pour l’installation sur des serveurs VMM** pour obtenir la dernière version du fichier d’installation fournisseur.

2. Exécuter ce fichier sur le serveur VMM source.

    >[AZURE.NOTE] Si VMM est déployé dans un cluster et que vous installez le fournisseur pour la première fois l’installer sur un nœud actif et terminer l’installation pour enregistrer le serveur VMM dans l’archivage sécurisé. Installez ensuite le fournisseur sur les autres nœuds. Notez que vous devez mettre à niveau sur tous les nœuds car ils doivent tous exécuter la même version du fournisseur si vous mettez à niveau le fournisseur.

3. Le programme d’installation effectue quelques de **Vérifier conditions requises** et les demandes d’autorisation pour arrêter le service VMM pour commencer la configuration du fournisseur. Le Service VMM sera redémarré automatiquement lors de la fin de l’installation. Si vous installez sur un cluster VMM que vous serez invité à arrêter le rôle Cluster.

4. Dans **Microsoft Update,** vous pouvez choisir dans les mises à jour. Lorsque ce paramètre est activé fournisseur mises à jour seront installés en fonction de votre stratégie de Microsoft Update.

    ![Mises à jour Microsoft](./media/site-recovery-vmm-to-vmm-classic/ms-update.png)

5. L’emplacement d’installation est défini sur ** <SystemDrive>\Program Files\Microsoft System Center 2012 R2\Virtual Machine Manager\bin**. Cliquez sur le bouton Installer pour démarrer l’installation du fournisseur.

    ![InstallLocation](./media/site-recovery-vmm-to-vmm-classic/install-location.png)

6. Après avoir installé le fournisseur de cliquer sur **Enregistrer** pour enregistrer le serveur dans l’archivage sécurisé.

    ![InstallComplete](./media/site-recovery-vmm-to-vmm-classic/install-complete.png)
9. Dans la zone **nom de l’archivage sécurisé**, vérifiez le nom de l’archivage sécurisé dans lequel le serveur sera enregistré. Cliquez sur *suivant*.

    ![Enregistrement du serveur](./media/site-recovery-vmm-to-vmm-classic/vaultcred.PNG)

7. Dans **Connexion Internet** spécifiez comment le fournisseur en cours d’exécution sur le serveur VMM se connecte à Internet. Sélectionnez **se connecter avec les paramètres de proxy existants** d’utiliser les paramètres de connexion Internet par défaut configurés sur le serveur.

    ![Paramètres d’Internet](./media/site-recovery-vmm-to-vmm-classic/proxydetails.PNG)

    - Si vous souhaitez utiliser un proxy personnalisé, que vous devez le configurer avant d’installer le fournisseur. Lorsque vous configurez des paramètres de proxy personnalisés un test s’exécutera pour vérifier la connexion de proxy.
    - Si vous n’utilisez pas un proxy personnalisé ou votre proxy par défaut requiert une authentification, que vous devez entrer les détails de proxy, y compris l’adresse proxy et le port.
    - URL suivantes doivent être accessibles à partir du serveur VMM et les hôtes Hyper-v
        - *. hypervrecoverymanager.windowsazure.com
        - *. accesscontrol.windows.net
        - *. backup.windowsazure.com
        - *. blob.core.windows.net
        - *. store.core.windows.net
    - Autoriser les adresses IP décrits dans les [Plages d’adresses IP Azure centre de données](https://www.microsoft.com/download/confirmation.aspx?id=41653) et HTTPS protocol (443). Vous devez plages d’adresses IP liste blanche de la région Azure que vous envisagez d’utiliser et celle des États-Unis Ouest.
    - Si vous utilisez un proxy personnalisé qu'un compte RunAs VMM (DRAProxyAccount) est créé automatiquement en utilisant les informations d’identification du proxy spécifié. Configurer le serveur proxy de sorte que ce compte peut s’authentifier avec succès. Les paramètres de compte RunAs VMM peuvent être modifiés dans la console. Pour ce faire, ouvrir l’espace de travail **paramètres** développez **sécurité**et cliquez sur **Exécuter en tant que comptes**, puis modifiez le mot de passe pour DRAProxyAccount. Vous devez redémarrer le service VMM pour que ce paramètre prenne effet.


8. Sous **Clé d’inscription**, sélectionnez la clé que vous avez téléchargé à partir de récupération de Site Azure copiés sur le serveur VMM.


10.  Le paramètre de chiffrement est utilisé uniquement lorsque vous êtes réplication machines virtuelles Hyper-V dans nuages VMM vers Azure. Si vous êtes réplication vers un site secondaire n’est pas utilisé.

11.  Dans la zone **nom du serveur**, spécifiez un nom convivial pour identifier le serveur VMM dans l’archivage sécurisé. Dans une configuration cluster spécifiez le nom du rôle cluster VMM.
12.  Dans Sélectionner **synchroniser cloud métadonnées** si vous souhaitez synchroniser les métadonnées pour tous les nuages sur le serveur VMM avec l’archivage sécurisé. Cette action ne doit se produire une fois sur chaque serveur. Si vous ne voulez pas synchroniser tous les nuages, vous pouvez laisser ce paramètre désactivé et synchroniser chaque cloud individuellement dans les propriétés du cloud dans la console.

13.  Cliquez sur **suivant** pour terminer le processus. Après l’enregistrement, métadonnées à partir du serveur VMM récupérées lors de la récupération de Site Azure. Le serveur est affiché dans **Les serveurs VMM** > **serveurs** dans l’archivage sécurisé.

    ![Serveurs](./media/site-recovery-vmm-to-vmm-classic/provider13.PNG)

### <a name="command-line-installation"></a>Installation de ligne de commande

Le fournisseur de récupération de Site Azure peuvent également être installé à partir de la ligne de commande. Cette méthode peut être utilisée pour installer le fournisseur sur un serveur CORE pour Windows Server 2012 R2.

1. Téléchargez la clé de fichier et d’enregistrement de l’installation fournisseur dans un dossier. Par exemple C:\ASR.
2. Arrêter le Service de gestionnaire de Machine virtuelle System Center
3. Extraire le programme d’installation de fournisseur en exécutant ces commandes à partir d’une invite de commandes en tant **qu’administrateur** :

        C:\Windows\System32> CD C:\ASR
        C:\ASR> AzureSiteRecoveryProvider.exe /x:. /q

4. Installer le fournisseur en cours d’exécution :

        C:\ASR> setupdr.exe /i

5. Enregistrer le fournisseur en exécutant :

        CD C:\Program Files\Microsoft System Center 2012 R2\Virtual Machine Manager\bin
        C:\Program Files\Microsoft System Center 2012 R2\Virtual Machine Manager\bin\> DRConfigurator.exe /r  /Friendlyname <friendly name of the server> /Credentials <path of the credentials file> /EncryptionEnabled <full file name to save the encryption certificate>     

Où les paramètres sont :

 - **/Credentials**: paramètre obligatoire qui spécifie l’emplacement dans lequel se trouve le fichier de clé d’enregistrement  
 - **/FriendlyName**: paramètre obligatoire pour le nom du serveur hôte Hyper-V qui s’affiche dans le portail de récupération de Site Azure.
 - **/EncryptionEnabled**: paramètre facultatif dont vous avez besoin pour utiliser uniquement dans le Gestionnaire de mémoire virtuelle Azure scénario si vous avez besoin de chiffrement de vos machines virtuelles en inactives dans Azure. Vérifiez que le nom du fichier que vous fournissez a une extension **.pfx** .
 - **/ProxyAddress**: paramètre facultatif qui spécifie l’adresse du serveur proxy.
 - **/ProxyPort**: paramètre facultatif qui spécifie le port du serveur proxy.
 - **/proxyUsername**: paramètre facultatif qui indique le nom d’utilisateur Proxy (si proxy requiert une authentification).
 - **/proxyPassword**: paramètre facultatif qui spécifie le mot de passe pour l’authentification avec le serveur proxy (si proxy requiert une authentification).  

## <a name="step-4-configure-cloud-protection-settings"></a>Étape 4 : Configurez cloud paramètres de protection

Après l’enregistrement de serveurs VMM, vous pouvez configurer les paramètres de protection cloud. Si vous avez activé l’option **synchroniser les données de cloud avec l’archivage sécurisé** lorsque vous avez installé le fournisseur ainsi, tous les nuages sur le serveur VMM seront affichent dans l’onglet **Éléments protégés** de l’archivage sécurisé. Si vous n’avez pas vous pouvez synchroniser un nuage spécifique avec récupération de Site Azure dans l’onglet **Général** de la page de propriétés du cloud dans la console.

![Cloud publié](./media/site-recovery-vmm-to-vmm-classic/clouds-list.png)

1. Dans la page de démarrage rapide, cliquez sur **définir la protection pour les nuages VMM**.
2. Sous l’onglet **Nuages VMM** , sélectionnez le cloud que vous souhaitez configurer et accédez à l’onglet **Configuration** .
3. Dans **cible**, sélectionnez **VMM**.
4. Dans un **emplacement cible**, sélectionnez le serveur VMM sur site qui gère le cloud à utiliser pour la récupération.
4. Dans le **cloud cible**, sélectionnez le cloud cible que vous voulez utiliser pour le basculement des machines virtuelles dans le cloud source. Notez que :

    - Nous vous conseillons de sélectionner un nuage cible qui répond aux besoins de récupération pour les ordinateurs virtuels que vous permettent de protéger.
    - Un nuage peut appartenir uniquement une paire cloud unique — en tant que principal ou un nuage cible.

5. Dans la zone **fréquence de copie**, spécifiez la fréquence à laquelle données doivent être synchronisées entre les sites sources et cibles 5he. Notez que ce paramètre est pertinent uniquement lorsque l’hôte Hyper-V exécutant Windows Server 2012 R2. Pour d’autres serveurs un paramètre par défaut de cinq minutes est utilisé.
6. Dans **points de récupération supplémentaires**, indiquez si vous souhaitez créer des points de récupération supplémentaires. La valeur par défaut zéro indique que seul le dernier point de récupération pour une machine virtuelle principal est stocké sur un serveur hôte réplica. Notez que l’activation de plusieurs points de récupération requiert espace de stockage supplémentaire pour les instantanés sont stockés sur chaque point de récupération. Par défaut, les points de récupération sont créés toutes les heures, afin que chaque point de récupération contient la valeur d’une heure de données. La valeur de point de récupération que vous affectez pour la machine virtuelle dans la console ne doit pas être inférieur à la valeur que vous affectez dans la console de récupération de Site Azure.
7. Dans la **fréquence des instantanés cohérents avec les applications**, spécifier la fréquence de création d’instantanés cohérents avec les applications. Hyper-V utilise deux types d’instantanés — une capture instantanée standard qui fournit un instantané incrémentiel de la machine virtuelle complète et un instantané cohérentes avec les applications qui prend un instantané de données de l’application à l’intérieur de la machine virtuelle. Instantanés cohérents avec les applications permet de s’assurer que les applications sont dans un état cohérent lorsque l’instantané Volume Shadow Copy Service (VSS). Notez que si vous activez les instantanés cohérents avec les applications, il affectent les performances des applications qui s’exécutent sur des machines virtuelles source. Vérifiez que la valeur est inférieure à celle du nombre de points de récupération supplémentaires que vous configurez.

    ![Configurer les paramètres de protection](./media/site-recovery-vmm-to-vmm-classic/cloud-settings.png)

8. Dans la **compression de transfert de données**, spécifiez si les données répliquées transférées doivent être compressées.
9. Dans **l’authentification**, spécifiez comment le trafic est authentifié entre les serveurs de l’hôte Hyper-V principaux et de restauration. Sélectionnez HTTPS à moins d’avoir un environnement Kerberos configuré qui fonctionne. Azure récupération de Site configure automatiquement les certificats pour l’authentification HTTPS. Aucune configuration manuelle n’est requise. Si vous sélectionnez Kerberos, un tickets Kerberos seront utilisées pour l’authentification commun des serveurs hôte. Par défaut, port 8083 et 8084 (pour les certificats) s’ouvre dans le pare-feu Windows sur les serveurs hôtes Hyper-V. Notez que ce paramètre n’est pertinent pour les serveurs hôtes Hyper-V en cours d’exécution sur Windows Server 2012 R2.
10. Dans la zone **Port**, modifiez le numéro de port que les ordinateurs hôtes sources et cibles écoutent le trafic de réplication. Par exemple, vous pouvez modifier le paramètre si vous voulez appliquer la limitation pour le trafic de réplication de bande passante du réseau de qualité de Service (qualité de service). Vérifiez que le port n’est pas utilisé par une autre application et qu’il est ouvert dans les paramètres du pare-feu.
11. Dans **la méthode de réplication**, spécifiez comment la réplication initiale de données à partir de la source à l’emplacement cible est traitée, avant le démarrage de réplication régulières :

    - **Réseau**: copier des données sur le réseau peut prendre du temps et beaucoup de ressources. Nous vous recommandons d’utiliser cette option si le cloud contient machines virtuelles avec des disques durs virtuels relativement petites, et si le site principal est connecté au site secondaire via une connexion avec la bande passante large. Vous pouvez spécifier que la copie doit commencer immédiatement, ou sélectionnez une heure. Si vous utilisez la réplication de réseau, nous vous recommandons de planifier en dehors des heures.
    - **En mode hors connexion**, cette méthode indique que la réplication initiale va être effectuée à l’aide du support externe. Il est utile si vous voulez éviter une dégradation des performances du réseau, ou pour les sites géographiquement distants. Pour utiliser cette méthode, vous spécifiez l’emplacement d’exportation sur le cloud source et l’emplacement d’importation sur le nuage cible. Lorsque vous activez la protection pour une machine virtuelle, le disque dur virtuel est copié à l’emplacement d’exportation spécifié. Vous envoyez vers le site cible et copiez dans l’emplacement d’importation. Le système copie les informations importées aux machines virtuelles réplica.

12. Sélectionnez **Supprimer réplica Virtual Machine** pour indiquer que la machine virtuelle réplica doit être supprimée si vous arrêtez la protection de la machine virtuelle en sélectionnant l’option **Supprimer la protection de la machine virtuelle** sous l’onglet Machines virtuelles des propriétés cloud. Avec ce paramètre est activé, lorsque vous désactivez la protection contre la machine virtuelle est supprimée de récupération de Site Azure, les paramètres de récupération de Site pour la machine virtuelle sont supprimés dans la console et la réplique est supprimée.

    ![Configurer les paramètres de protection](./media/site-recovery-vmm-to-vmm-classic/cloud-settings-replica.png)

Une fois que vous enregistrez les paramètres d’une tâche est créée et peut être contrôlée sous l’onglet **tâches** . Tous les serveurs hôtes Hyper-V dans le cloud source VMM seront configurés pour la réplication. Paramètres de cloud peuvent être modifiés dans l’onglet **configurer** . Si vous souhaitez modifier le cloud cible emplacement ou cible vous devez supprimer la configuration cloud et puis reconfigurer le cloud.

### <a name="prepare-for-offline-initial-replication"></a>Préparer pour la réplication initiale en mode hors connexion

Vous devez effectuer les opérations suivantes pour préparer pour la réplication initiale en mode hors connexion :

- Sur le serveur source, vous devez spécifier un chemin d’accès à partir de laquelle l’exportation de données exécutée. Affecter un contrôle total des autorisations NTFS et de partage au service VMM sur le chemin d’exportation. Sur le serveur cible, vous devez spécifier un chemin d’accès à partir duquel l’importation de données a lieu. Affecter les mêmes autorisations sur ce chemin d’accès de l’importation.
- Si le chemin d’accès de l’importation ou exportation est partagée, attribuer administrateur, utilisateur Power, opérateur d’impression ou un opérateur de serveur l’appartenance aux groupes pour le compte de service VMM sur l’ordinateur distant sur lequel le partagé se trouve.
- Si vous utilisez tous les comptes d’exécuter en tant que pour ajouter des hôtes, sur les chemins d’accès d’importation et d’exportation, attribuer en lecture et en écriture aux comptes d’exécuter en tant que dans VMM.
- Les actions d’importation et d’exportation ne doivent pas être trouver sur n’importe quel ordinateur utilisé comme serveur hôte Hyper-V, car configuration en boucle n’est pas pris en charge par Hyper-V.
- Dans Active Directory, sur chaque Hyper-V serveur hôte qui contient des machines virtuelles que vous voulez protéger, activer et configurer la délégation contrainte d’approuver les ordinateurs distants sur lesquels les chemins d’accès d’importation et d’exportation sont installées, comme suit :
    1. Sur le contrôleur de domaine, ouvrez **utilisateurs Active Directory et ordinateurs**.
    2. Dans l’arborescence de la console cliquez sur **nom_domaine** > **ordinateurs**.
    3. Cliquez sur le nom du serveur hôte Hyper-V > **Propriétés**.
    4. Sous l’onglet **délégation** cliquez sur T**rouille cet ordinateur pour la délégation aux services spécifiés**.
    5. Cliquez sur **utiliser n’importe quel protocole d’authentification**.
    6. Cliquez sur **Add** > **utilisateurs et ordinateurs**.
    7. Tapez le nom de l’ordinateur qui héberge le chemin d’exportation > **OK**. Dans la liste des services disponibles, maintenez la touche CTRL enfoncée et cliquez sur **cifs** > **OK**. Répétez ces étapes pour le nom de l’ordinateur qui héberge le chemin d’accès de l’importation. Répétez cette procédure pour les autres serveurs d’hôtes Hyper-V.

## <a name="step-5-configure-network-mapping"></a>Étape 5 : Configurer le mappage de réseau
1. Dans la page de démarrage rapide, cliquez sur **mapper réseaux**.
2. Sélectionnez le serveur VMM source à partir de laquelle vous voulez mapper réseaux, puis sur le serveur VMM cible auquel les réseaux seront mappés. La liste des réseaux source et leurs cibles associés sont affichés. Une valeur vide est affichée pour les réseaux qui ne sont pas mappés actuellement.
3. Sélectionnez un réseau dans un **réseau sur source** > **carte**. Le service détecte les réseaux machine virtuelle sur le serveur cible et les affiche. Cliquez sur l’icône d’information en regard des noms réseau sources et cibles pour afficher les sous-réseaux pour chaque réseau.

    ![Configurer le mappage de réseau](./media/site-recovery-vmm-to-vmm-classic/network-mapping1.png)

4. Dans la boîte de dialogue Sélectionnez un des réseaux machine virtuelle à partir du serveur VMM cible.

    ![Sélectionnez un réseau cible](./media/site-recovery-vmm-to-vmm-classic/network-mapping2.png)

5. Lorsque vous sélectionnez un réseau cible, les nuages protégées qui utilisent le réseau source sont affichent. Réseaux cible disponibles qui sont associés à nuages utilisés pour la protection sont également affichées. Nous vous conseillons de sélectionner un réseau cible qui n’est disponible pour tous les nuages que vous utilisez pour la protection. Ou vous pouvez également accéder au serveur VMM et modifier les propriétés de cloud pour ajouter le réseau logique correspondant au réseau machine virtuelle que vous voulez sélectionner.
6. Activez la case à cocher pour terminer le processus de mappage. Une tâche commence à suivre la progression de mappage. Vous pouvez l’afficher dans l’onglet **tâches** .


## <a name="step-6-configure-storage-mapping"></a>Étape 6 : Configurer le mappage de stockage
Par défaut lors de la réplication une machine virtuelle sur un serveur source Hyper-V hôte sur un serveur de hôte cible Hyper-V, données répliquées sont stockées dans l’emplacement par défaut qui est indiqué pour l’hôte Hyper-V cible dans le Gestionnaire Hyper-V. Pour plus de contrôle sur l’emplacement de stockage des données dupliquées, vous pouvez configurer des mappages de stockage comme suit :



1. Définir des classifications de stockage sur des serveurs VMM source et cible. [En savoir plus](https://technet.microsoft.com/library/gg610685.aspx). Classifications doivent être disponibles pour les serveurs hôtes Hyper-V dans nuages sources et cibles. Classifications n’êtes pas obligé d’avoir le même type de stockage. Par exemple, vous pouvez mapper une classification de source qui contient des partages de PME une classification cible qui contient CSVs.
2. Une fois que les classifications sont en place vous pouvez créer des mappages. Pour ce faire, dans la page de **Démarrage rapide** > **carte de stockage**.
3. Cliquez sur l’onglet **stockage** > **mapper classifications de stockage**.
4. Sous l’onglet **mapper classifications de stockage** , sélectionnez classifications sur la source et VMM serveurs cibles. Enregistrez vos paramètres.

    ![Sélectionnez un réseau cible](./media/site-recovery-vmm-to-vmm-classic/storage-mapping.png)


## <a name="step-7-enable-virtual-machine-protection"></a>Étape 7 : Activer la protection de la machine virtuelle
Une fois que les serveurs, les nuages et les réseaux sont correctement configurés, vous pouvez activer la protection des machines virtuelles dans le cloud.

1. Sous l’onglet **Machines virtuelles** dans le cloud dans lequel se trouve la machine virtuelle, cliquez sur **Activer la protection** > **machines virtuelles ajouter**.
2. Dans la liste des machines virtuelles dans le cloud, sélectionnez celle que vous voulez protéger.

    ![Activer la protection de la machine virtuelle](./media/site-recovery-vmm-to-vmm-classic/enable-protection.png)

3. Suivre la progression de l’action Activer la Protection dans l’onglet **tâches** , y compris la réplication initiale. Après l’exécution de la tâche de Protection finaliser la machine virtuelle est prête pour le basculement. Une fois la protection est activée et machines virtuelles sont répliquées, vous pourrez les afficher dans Azure.

    ![Machine virtuelle une tâche de protection](./media/site-recovery-vmm-to-vmm-classic/vm-jobs.png)

>[AZURE.NOTE] Vous pouvez également activer la protection des machines virtuelles dans la console. Cliquez sur **Activer la Protection** dans la barre d’outils sous l’onglet **Récupération de Site Azure** dans les propriétés de la machine virtuelle.

### <a name="on-board-existing-virtual-machines"></a>Machines virtuelles existantes intégrés

Si vous possédez des machines virtuelles dans VMM qui réplication avec Hyper-V réplica vous devez intégré les pour la protection de la récupération de Site Azure comme suit :

1. Assurez-vous de qu'avoir nuages principales et secondaires. Assurez-vous que le serveur Hyper-V qui héberge la machine virtuelle existante se trouve dans le cloud principal et que le serveur Hyper-V qui héberge la machine virtuelle réplica se trouve dans le cloud secondaire. Vérifiez que vous avez configuré les paramètres de protection pour les nuages. Les paramètres doivent correspondre à celles déjà configurées pour réplica Hyper-V. Dans le cas contraire réplication machine virtuelle peut ne pas fonctionne comme prévu.
2. Puis activer la protection de la machine virtuelle principale. Azure récupération de Site et VMM garantit que la machine virtuelle et même hôte réplica est détecté, et Azure Site reprise réutiliser et rétablir la réplication en utilisant les paramètres configurés lors de la configuration de cloud.


## <a name="test-your-deployment"></a>Tester votre déploiement

Pour tester votre déploiement, vous pouvez exécuter un basculement de test pour une seule machine virtuelle, ou créer un plan de récupération constituée de plusieurs machines virtuelles et exécuter un basculement de test pour le plan.  Basculement de test simule votre mécanisme de basculement et de récupération dans un réseau isolé.

### <a name="create-a-recovery-plan"></a>Créer un plan de récupération

1. Sous l’onglet **Plans de restauration** , cliquez sur **Créer un Plan de récupération**.
2. Spécifiez un nom pour le plan de récupération et les serveurs VMM sources et cibles. Le serveur source doit avoir des machines virtuelles qui sont activés pour le basculement et récupération. Sélectionnez **Hyper-V** pour afficher uniquement les nuages qui sont configurés pour la réplication de Hyper-V.

    ![Créer le plan de récupération](./media/site-recovery-vmm-to-vmm-classic/recovery-plan1.png)

3. Dans **Sélectionner l’ordinateur virtuel**, sélectionnez groupes de réplication. Tous les ordinateurs virtuels associés au groupe de réplication est sélectionnés et ajoutées au plan de récupération. Ces machines virtuelles sont ajoutés au groupe par défaut de plan de récupération, groupe 1. Vous pouvez ajouter plusieurs groupes si nécessaire. Notez qu’après que machines virtuelles réplication démarre selon l’ordre des groupes de plan de récupération.

    ![Ajouter des machines virtuelles](./media/site-recovery-vmm-to-vmm-classic/recovery-plan2.png)

Après une récupération plan a été créé, il apparaît dans la liste sous l’onglet **Plans de récupération** .

###<a name="run-a-test-failover"></a>Exécuter un basculement de test

1. Sous l’onglet **Plans de** , sélectionnez le plan, puis cliquez sur **Test basculement**.
2. Dans la page **Confirmer le basculement Test** , sélectionnez **Aucun**. Notez qu’activé l’échec de machines virtuelles réplica avec cette option n’est pas connecté à un réseau. Cela va tester que la machine virtuelle bascule comme prévu, mais ne vérifie pas votre environnement de réseau de réplication. Observez comment [exécuter un basculement test](site-recovery-failover.md#run-a-test-failover) pour plus d’informations sur l’utilisation des différentes options de mise en réseau.
3. La machine virtuelle test sont créée sur le même hôte que l’hôte sur lequel se trouve la machine virtuelle réplica. Il est ajouté dans le cloud même dans lequel se trouve la machine virtuelle réplica.

### <a name="run-a-recovery-plan"></a>Exécuter un plan de récupération
Après la réplication la machine virtuelle réplica n’ait pas une adresse IP qui est identique à l’adresse IP de l’ordinateur virtuel principal. Machines virtuelles met à jour le serveur DNS qu’ils utilisent mise en route. Vous pouvez également ajouter un script pour mettre à jour le serveur DNS pour vous assurer une mise à jour en temps voulu.

#### <a name="script-to-retrieve-the-ip-address"></a>Script pour récupérer l’adresse IP
Exécuter cet exemple de script pour récupérer l’adresse IP.

        $vm = Get-SCVirtualMachine -Name <VM_NAME>
        $na = $vm[0].VirtualNetworkAdapters>
        $ip = Get-SCIPAddress -GrantToObjectID $na[0].id
        $ip.address  

#### <a name="script-to-update-dns"></a>Script pour mettre à jour de DNS
Exécuter cet exemple de script pour mettre à jour DNS, spécifiez l’adresse IP récupérées à l’aide de l’exemple de script précédent.

        string]$Zone,
        [string]$name,
        [string]$IP
        )
        $Record = Get-DnsServerResourceRecord -ZoneName $zone -Name $name
        $newrecord = $record.clone()
        $newrecord.RecordData[0].IPv4Address  =  $IP
        Set-DnsServerResourceRecord -zonename $zone -OldInputObject $record -NewInputObject $Newrecord



## <a name="privacy-information-for-site-recovery"></a>Déclaration de confidentialité pour la récupération de Site

Cette section fournit des informations de confidentialité supplémentaires pour le service de récupération de Site Microsoft Azure (« Service »). Pour afficher la déclaration de confidentialité pour les services Microsoft Azure, consultez la [Déclaration de confidentialité de Microsoft Azure](http://go.microsoft.com/fwlink/?LinkId=324899)

**Fonctionnalité : l’enregistrement**

- **Fonction**: enregistre server avec service afin que les machines virtuelles peuvent être protégées
- **Informations collectées**: après l’enregistrement du Service collecte, traite et transmet des informations sur le certificat gestion à partir du serveur VMM qui a désigné pour fournir sinistre utilisant le nom du Service du serveur VMM et le nom de nuages rognée machine virtuelle sur votre serveur VMM.
- **Utilisation des informations**:
    - Certificat de gestion, il est utilisé pour aider à identifier et authentifier le serveur VMM enregistré pour l’accès au Service. Le Service utilise la clé publique du certificat pour sécuriser un jeton uniquement le serveur VMM enregistré peut y accéder. Le serveur doit utiliser ce jeton pour accéder aux fonctionnalités de Service.
    - Nom du serveur VMM — VMM le nom du serveur est requise pour identifier et communiquer avec le serveur VMM approprié sur lequel se trouvent les nuages.
    - Noms à partir du serveur VMM en nuage, le nom de nuage est nécessaire lors de l’utilisation du cloud Service le jumelage/unpairing fonctionnalité décrite ci-dessous. Lorsque vous décidez d’associer votre cloud à partir d’un centre de données principal avec un autre cloud dans le centre de récupération des données, les noms de tous les nuages à partir du centre de données de récupération sont présentés.

- **Choix**: ces informations sont une partie essentielle de l’inscription Service car elle permet de vous et le Service pour identifier le serveur VMM pour lequel vous souhaitez fournir protection récupération de Site Azure, ainsi que pour identifier le serveur VMM enregistré correct. Si vous ne voulez pas envoyer ces informations au Service, n’utilisez pas ce Service. Si vous avez enregistré votre serveur et que vous souhaitez ensuite annuler son inscription, vous pouvez le faire en supprimant les informations du serveur VMM à partir du portail de Service (qui est le portail Azure).

**Fonctionnalité : Activer la protection de la récupération de Site Azure**

- **Comportement**: le fournisseur de récupération de Site Azure installés sur le serveur VMM est le canal de communication avec le Service. Le fournisseur est une bibliothèque de liens dynamiques (DLL) hébergée dans le processus VMM. Une fois que le fournisseur est installé, la fonctionnalité « Récupération de centre de données » est activée dans la console administrateur. Les machines virtuelles nouvelles ou existantes dans un nuage peuvent activer une propriété appelée « Récupération de centre de données » pour mieux vous protéger la machine virtuelle. Une fois que cette propriété est définie, le fournisseur envoie le nom et l’ID de la machine virtuelle au Service. La protection virtuelle est activée par la technologie de réplication Windows Server 2012 ou Windows Server 2012 R2 Hyper-V. Les données de machine virtuelle répliquées à partir d’un hôte Hyper-V à l’autre (généralement situé dans un centre de données différents « récupération »).

- **Informations collectées**: le Service de collecte, traite et transmet les métadonnées de la machine virtuelle, qui inclut le nom, ID, réseau virtuel et le nom du cloud à laquelle elle appartient.

- **Utilisation des informations**: le Service utilise les informations ci-dessus pour remplir les machine virtuelle d’informations sur le portail de Service.

- **Choix**: Ceci est un élément essentiel du service et ne peut pas être désactivée. Si vous ne voulez pas ces informations envoyées au Service, n’activez pas la protection par récupération de Site Azure pour toutes les machines virtuelles. Notez que toutes les données envoyées par le fournisseur au Service est envoyé sur HTTPS.

**Fonctionnalité : Plan de récupération**

- **Fonction**: cette fonctionnalité vous aide à créer un plan d’orchestration pour le centre de données « récupération ». Vous pouvez définir l’ordre dans lequel les ordinateurs virtuels ou un groupe de machines virtuelles doit être démarré sur le site de récupération. Vous pouvez également spécifier les scripts automatisés pour être s’exécute ou tout manuelle action à exécuter, au moment de la récupération pour chaque machine virtuelle. Basculement (décrites dans la section suivante) est généralement déclenché au niveau du Plan de récupération pour récupération coordonné.

- **Informations collectées**: le Service de collecte, traite et transmet les métadonnées pour le plan de récupération, y compris la machine virtuelle et les métadonnées des scripts d’automatisation et notes action manuelle.

- **Utilisation des informations**: les métadonnées ci-dessus sont utilisée pour générer le plan de récupération dans votre portail de Service.

- **Choix**: Ceci est un élément essentiel du service et ne peut pas être désactivée. Si vous ne voulez pas ces informations envoyées au Service, ne créer des Plans de récupération dans ce Service.

**Fonctionnalité : Mappage réseau**

- **Fonction**: cette fonctionnalité, vous pouvez associer des informations du réseau à partir du centre de données principal vers le centre de récupération des données. Lorsque les machines virtuelles sont restaurées sur le site de récupération, ce mappage vous permet de l’établissement de la connectivité réseau pour les.

- **Informations collectées**: dans le cadre de la fonctionnalité de mappage réseau, le Service de collecte, traite et transmet les métadonnées des réseaux logiques pour chaque site (primaire et centre de données).

- **Utilisation des informations**: le Service utilise les métadonnées pour remplir votre portail de Service dans lequel vous pouvez mapper les informations du réseau.

- **Choix**: Ceci est un élément essentiel du Service et ne peut pas être désactivée. Si vous ne voulez pas ces informations envoyées au Service, n’utilisez pas la fonctionnalité de mappage réseau.

**Fonctionnalité : Basculement - planifié, non, test**

- **Fonction**: cette fonctionnalité permet de basculement d’une machine virtuelle à partir d’un centre de données gérées VMM un autre VMM gérées du centre de données. L’action de basculement est déclenchée par l’utilisateur sur le portail de Service. Les raisons possibles pour un basculement incluent un événement planifié (par exemple dans le cas d’un disaster0 naturel ; un événement planifié (par exemple centre de données équilibrage de charge) ; un basculement de test (par exemple une répétition de plan de récupération).

Le fournisseur sur le serveur VMM reçoit des notifications de l’événement à partir du Service et exécute une action basculement sur l’hôte Hyper-V via les interfaces VMM. Basculement réel de la machine virtuelle d’un hôte Hyper-V à l’autre (en général en cours d’exécution dans un centre de données différents « récupération ») est gérée par la technologie de réplication Windows Server 2012 ou Windows Server 2012 R2 Hyper-V. Une fois le basculement terminé, le fournisseur est installé sur le serveur VMM du centre de données « récupération » envoie les informations de réussite au Service.

- **Informations collectées**: le Service utilise les informations ci-dessus pour renseigner l’état des informations sur le portail de Service action basculement.

- **Utilisation des informations**: le Service utilise les informations ci-dessus comme suit :

    - Certificat de gestion, il est utilisé pour aider à identifier et authentifier le serveur VMM enregistré pour l’accès au Service. Le Service utilise la clé publique du certificat pour sécuriser un jeton uniquement le serveur VMM enregistré peut y accéder. Le serveur doit utiliser ce jeton pour accéder aux fonctionnalités de Service.
    - Nom du serveur VMM — VMM le nom du serveur est requise pour identifier et communiquer avec le serveur VMM approprié sur lequel se trouvent les nuages.
    - Noms à partir du serveur VMM en nuage, le nom de nuage est nécessaire lors de l’utilisation du cloud Service le jumelage/unpairing fonctionnalité décrite ci-dessous. Lorsque vous décidez d’associer votre cloud à partir d’un centre de données principal avec un autre cloud dans le centre de récupération des données, les noms de tous les nuages à partir du centre de données de récupération sont présentés.

- **Choix**: Ceci est un élément essentiel du service et ne peut pas être désactivée. Si vous ne voulez pas ces informations envoyées au Service, n’utilisez pas ce Service.

## <a name="next-steps"></a>Étapes suivantes

Une fois que vous avez exécuté un basculement test pour vérifier que votre environnement fonctionne comme prévu, [Découvrez les](site-recovery-failover.md) différents types de basculement.

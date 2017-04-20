<properties
    pageTitle="Réplication locale machines virtuelles VMware ou serveurs physiques vers un site secondaire | Microsoft Azure"
    description="Utilisez cet article pour répliquer machines virtuelles VMware ou Windows/Linux serveurs physiques sur un site secondaire avec récupération de Site Azure."
    services="site-recovery"
    documentationCenter=""
    authors="nsoneji"
    manager="jwhit"
    editor=""/>

<tags
    ms.service="site-recovery"
    ms.workload="backup-recovery"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/20/2016"
    ms.author="nisoneji"/>


# <a name="replicate-on-premises-vmware-virtual-machines-or-physical-servers-to-a-secondary-site"></a>Réplique des machines virtuelles VMware locaux ou serveurs physiques sur un site secondaire


## <a name="overview"></a>Vue d’ensemble

InMage Scout dans Azure Site récupération assure la réplication en temps réel entre les sites de VMware en local. InMage Scout est inclus dans les abonnements aux services de récupération de Site Azure.


## <a name="prerequisites"></a>Conditions préalables

**Compte Azure**: vous devez disposer d’un compte [Microsoft Azure](https://azure.microsoft.com/) . Vous pouvez commencer avec une [version d’évaluation gratuite](https://azure.microsoft.com/pricing/free-trial/). [En savoir plus](https://azure.microsoft.com/pricing/details/site-recovery/) sur les tarifs de récupération de Site.


## <a name="step-1-create-a-vault"></a>Étape 1 : Créer un archivage sécurisé
1. Connectez-vous au [portail Azure](https://portal.azure.com).
2. Cliquez sur Nouveau > Gestion > sauvegarde et restauration de Site (OMS). Vous pouvez également cliquer sur Parcourir > l’archivage sécurisé des Services de récupération > Ajouter.
3. Dans la zone **nom** , spécifiez un nom convivial pour identifier l’archivage sécurisé. Si vous avez plusieurs abonnements, sélectionnez un d’eux.
4. Dans le **groupe de ressources** , créez un nouveau groupe de ressources ou sélectionnez-en un. Spécifier une région Azure pour remplissez les champs requis. 
5.  Dans un **emplacement**, sélectionnez la région géographique pour l’archivage sécurisé. Pour rester informé des régions pris en charge, consultez [Azure Site récupération tarification](https://azure.microsoft.com/pricing/details/site-recovery/).
5. Si vous voulez accéder rapidement l’archivage sécurisé du tableau de bord cliquez sur Ajouter au tableau de bord, puis sur Créer.
6. L’archivage sécurisé nouveau s’affichent dans le tableau de bord > toutes les ressources et sur les Services de récupération principale coffres-forts carte.

## <a name="step-2-configure-the-vault-and-download-inmage-scout-components"></a>Étape 2 : Configurer l’archivage sécurisé et télécharger les composants InMage Scout
7. Dans les Services de récupération carte chambres fortes Sélectionnez votre l’archivage sécurisé et cliquez sur paramètres.
8. Dans **paramètres** > **Prise en main** cliquez sur **Récupération de Site** > étape 1 : **Préparer une Infrastructure** > **objectif de Protection**.
9. Dans **l’objectif de Protection** sélectionnez sur le site de, puis sélectionnez Oui, avec hyperviseur VMware vSphere. Cliquez ensuite sur OK.
10. Dans **le programme d’installation Scout**, cliquez sur la clé pour télécharger InMage Scout 8.0.1 disponibilité générale de logiciels et de l’enregistrement. Les fichiers d’installation pour tous les composants requis se trouvent dans le fichier .zip téléchargé.


## <a name="step-3-install-component-updates"></a>Étape 3 : Installer des mises à jour de composant

En savoir plus sur les plus récentes [mises à jour](#updates). Vous devez installer les mise à jour des fichiers sur des serveurs dans l’ordre suivant :

1. Serveur de réception, le cas échéant
2. Serveurs de configuration
3. Serveurs de traitement
3. Serveurs maître cibles
4. serveurs vContinuum
5. Serveur source (serveur Linux et Windows)

Installez les mises à jour comme suit :

1. Téléchargez le fichier .zip [mettre à jour](https://aka.ms/asr-scout-update4) . Ce fichier .zip contient les fichiers suivants :

    - RX_8.0.4.0_GA_Update_4_8725872_16Sep16.tar.gz
    - CX_Windows_8.0.4.0_GA_Update_4_8725865_14Sep16.exe
    - UA_Windows_8.0.4.0_GA_Update_4_9035261_27Sep16.exe
    - UA_RHEL6 64_8.0.4.0_GA_Update_4_9035261_26Sep16.tar.gz
    - vCon_Windows_8.0.4.0_GA_Update_4_8921562_16Sep16.exe
    - Bits pour update4 CU RHEL5, OL5, OL6, SUSE 10, 11 SUSE : UA_<Linux OS>_8.0.4.0_GA_Update_4_9035261_26Sep16.tar.gz 
    
2. Extraire les fichiers .zip.<br>
3. **Serveur pour la réception**: copie **RX_8.0.4.0_GA_Update_4_8725872_16Sep16.tar.gz** sur le serveur de réception et extraire. Dans le dossier d’extraction, **exécutez/installer**.<br>
4. **Pour le serveur de configuration de serveur/processus**: copie **CX_Windows_8.0.4.0_GA_Update_4_8725865_14Sep16.exe** pour le serveur de configuration et le processus serveur. Double-cliquez sur pour l’exécuter.<br>
5. **Pour Windows maîtres serveur cible**: pour mettre à jour l’agent unifiée, copiez **UA_Windows_8.0.4.0_GA_Update_4_9035261_27Sep16.exe** sur le serveur maître cible. Double-cliquez dessus pour l’exécuter. Notez que l’agent unifiée est également applicable au serveur source. Vous devez l’installer sur le serveur source ainsi, comme mentionné plus loin dans cette liste.<br>
7. **Pour le serveur vContinuum**: copier **vCon_Windows_8.0.4.0_GA_Update_4_8921562_16Sep16.exe** sur le serveur vContinuum.  Vérifiez que vous avez fermé l’Assistant vContinuum. Double-cliquez sur le fichier pour l’exécuter.<br>
8. **Serveur de cible maître pour Linux**: pour mettre à jour l’agent unifiée, copiez **UA_RHEL6 64_8.0.4.0_GA_Update_4_9035261_26Sep16.tar.gz** sur le serveur cible maître et extraire. Dans le dossier d’extraction, **exécutez/installer**.<br>
9. **Serveur source pour Windows**: pour mettre à jour l’agent unifiée, copiez **UA_Windows_8.0.4.0_GA_Update_4_9035261_27Sep16.exe** sur le serveur source. Double-cliquez dessus pour l’exécuter.<br>
10. **Serveur source pour Linux**: pour mettre à jour l’agent unifiée, copiez version de fichier CU correspondante sur le serveur Linux et extraire. Dans le dossier d’extraction, **exécutez/installer**.  Exemple : Pour RHEL 6,7 64 bits serveur, copiez **UA_RHEL6 64_8.0.4.0_GA_Update_4_9035261_26Sep16.tar.gz** sur le serveur et extraire. Dans le dossier d’extraction, **exécutez/installer**.

## <a name="step-4-set-up-replication"></a>Étape 4 : Configurer la réplication
1. Configurer la réplication entre la source et VMware sites cibles.
2. Pour des instructions, utilisez la documentation InMage Scout est téléchargée avec le produit. Par ailleurs, vous pouvez accéder à la documentation comme suit :

    - [Notes de publication](https://aka.ms/asr-scout-release-notes)
    - [Matrice de compatibilité](https://aka.ms/asr-scout-cm)
    - [Guide de l’utilisateur](https://aka.ms/asr-scout-user-guide)
    - [Guide de l’utilisateur réception](https://aka.ms/asr-scout-rx-user-guide)
    - [Guide d’installation rapide](https://aka.ms/asr-scout-quick-install-guide)


## <a name="updates"></a>Mises à jour

### <a name="azure-site-recovery-scout-801-update-4"></a>Mise à jour de Scout 8.0.1 Site Azure récupération 4
Mise à jour de Scout 4 est une mise à jour cumulative. Elle comporte tous les correctifs de clic1 jusqu'à ce que des améliorations et correctifs nouveau suivants et update3.

**Nouvelle prise en charge de la plateforme** 

- Prise en charge a été ajouté pour vCenter/vSphere 6.0, 6.1 et 6.2
- Prise en charge a été ajouté pour les systèmes d’exploitation Linux suivants
    - Chapeau rouge Enterprise Linux (RHEL) 7.0, 7.1 et 7.2 
    - CentOS 7.0, 7.1 et 7.2
    - Chapeau rouge Enterprise Linux (RHEL) 6.8
    - CentOS 6.8

>[AZURE.NOTE]
>
> RHEL/CentOS 7 64 bits **InMage_UA_8.0.1.0_RHEL7-64_GA_06Oct2016_release.tar.gz** est fourni avec le package de base Scout disponibilité générale **InMage_Scout_Standard_8.0.1 GA.zip**. Téléchargez le package Scout disponible à partir du portail comme indiqué dans [l’étape 1](site-recovery-vmware-to-vmware.md#Step 1: Create a vault).

**Correctifs et améliorations** 

- Arrêt amélioré gestion suivant Linux OSes et clones à éviter des problèmes de resynchroniser indésirable.
    - Chapeau rouge Enterprise Linux (RHEL) 6.x
    - Oracle Linux (OL) 6.x
- Pour Linux, effectuez l’accès à un dossier des autorisations dans le répertoire d’installation de l’agent unifiée sont désormais limitées uniquement à l’utilisateur local.
- Sur Windows arriver à expiration problème lors de l’envoi courantes ajouter aux Favoris cohérence distribué sur intensément chargée applications distribuées comme clusters SQL et Point de partage.
- Correctif connexes d’Ajout du programme d’installation base CX.
- Lien de téléchargement VMware vCLI 6.0 est ajouté à installer base Windows masque cible.
- Ajouté plusieurs contrôles et les journaux de modifications de configurations réseau lors du basculement et DR exercices.
- Informations de rétention parfois ne sont pas signalées pour le CX.  
- Avec cluster physique, volume redimensionner opération via vContinuum Assistant échoue lors de la réduction du volume source est devenu.
- Protection de cluster a échoué avec l’erreur « Impossible de trouver la signature de disque » lorsque disque cluster est PRDM.
- cxps transport panne du serveur en raison d’une exception non valides. 
- Nom du serveur et les colonnes IP peuvent désormais être redimensionnés dans la page d’installation push de l’Assistant vContinuum.
- Améliorations de l’API réception
    - Fournit des cinq dernières disponibles la cohérence des points communs (balises garantie uniquement).
    - Fournit capacité et les détails de l’espace disponible pour tous les appareils protégées.
    - Fournit Scout pilote état sur le serveur source. 
    
>[AZURE.NOTE] 
>
>- Maintenant le package de base **InMage_Scout_Standard_8.0.1_GA.zip** a mis à jour CX installer base **InMage_CX_8.0.1.0_Windows_GA_26Feb2015_release.exe** et Windows masque cible base programme d’installation **InMage_Scout_vContinuum_MT_8.0.1.0_Windows_GA_26Feb2015_release.exe**. Pour toutes les nouvelles installations utilisez nouveaux éléments de disponibilité générale CX et Windows masque cible.
>- Mise à jour 4 peut être appliqué directement sur 8.0.1 GA.
>- Le serveur de configuration et les mises à jour de réception ne peut pas être annulées après que elles sont appliquées sur le système.

### <a name="azure-site-recovery-scout-801-update-3"></a>Mise à jour de Scout 8.0.1 Site Azure récupération 3
Mise à jour 3 inclut les correctifs et améliorations suivantes :

- Le serveur de configuration et la réception échouent enregistrer sur l’archivage sécurisé récupération de Site lorsqu’ils sont derrière le serveur proxy.
- Le nombre d’heures la récupération point l’objectif n’est pas respecté n’est pas prise mis à jour dans le rapport d’intégrité.
- Le serveur de configuration ne se synchronise pas avec réception lorsque les détails du matériel ESX ou les détails de réseau contient des caractères UTF-8.
- Windows Server 2008 R2 superflus échouent démarrer après récupération.
- Synchronisation hors connexion ne fonctionne pas comme prévu.
- Après le basculement de la machine virtuelle (), suppression de réplication paire coincée dans le CX UI pendant une longue période, et les utilisateurs ne peuvent pas exécuter le retour arrière ou reprendre l’opération.
- Instantané opérations qui sont effectuées par la tâche de cohérence ont été optimisées pour réduire l’application déconnecte global comme clients SQL.
- Les performances de l’outil de cohérence (VACP.exe) a été améliorée en réduisant l’utilisation de la mémoire qui est requise pour créer des instantanés de Windows.
- La diffusion installer service se bloque lorsque le mot de passe est supérieure à 16 caractères.
- vContinuum n’est pas la vérification et demander de nouvelles informations d’identification vCenter lorsque les informations d’identification sont modifiées.
- Sous Linux, le Gestionnaire de cache cible maître (cachemgr) ne télécharge pas les fichiers à partir du serveur de processus, qui se traduit par la limitation de paire réplication.
- Lorsque l’ordre des disques basculement physique cluster (MSCS) n’est pas le même sur tous les nœuds, réplication n’est pas définie pour certains des volumes cluster.
<br/>Notez que le cluster doit être reprotected pour tirer parti de ce correctif.  
- Fonctionnalité SMTP ne fonctionne pas correctement après que réception est mis à niveau à partir de 7.1 Scout Scout 8.0.1.
- Statistiques plus ont été ajoutées dans le journal pour l’opération de restauration suivre le temps que nécessaire à exécuter.
- Prise en charge a été ajouté pour les systèmes d’exploitation Linux sur le serveur source :
    - Mise à jour de rouge chapeau Enterprise Linux (RHEL) 6 7
    - Mise à jour centOS 6 7
- L’interface de réception et CX affiche désormais la notification pour la paire, passe en mode bitmap.
- Les solutions de sécurité suivantes ont été ajoutées dans réception :

**Description du problème**|**Procédures d’implémentation**
---|---
Autorisation ignorer via le paramètre falsification|Accès limité aux utilisateurs non applicable.
Falsification intersites demande|Mise en œuvre le concept de jeton de page, génère au hasard pour chaque page. <br/>Cela, vous verrez : <li> Il existe une seule connexion instance pour le même utilisateur.</li><li>Actualisation de la page ne fonctionne pas, il vous redirige au tableau de bord.</li>
Téléchargement de fichier malveillants|Fichiers restreints à certaines extensions. Autorisé les extensions sont : 7z, aiff, asf, avi, bmp, csv, un document, docx, fla, flv, gif, gz, gzip, jpeg, jpg, journal, STXT, mov, mp3, mp4, mpc, mpeg, mpg, ods, outil de détection Office, pdf, png, ppt, pptx, pxd, qt, ram, rar, rm, rmi, rmvb, au format rtf, sdc, sitd, swf, sxc, sxw, tar, tgz, tif, tiff, txt, vsd, wav, wma, wmv, xls, xlsx, xml et zip.
Permanente l’écriture de script intersites | Ajouter des validations d’entrée.


>[AZURE.NOTE]
>
>-  Toutes les mises à jour de récupération de Site sont cumulés. Mise à jour 3 comporte tous les correctifs de mise à jour 1 et 2 de la mise à jour. Mise à jour 3 peut être appliqué directement sur 8.0.1 GA.
>-  Le serveur de configuration et les mises à jour de réception ne peut pas être annulées après que elles sont appliquées sur le système.

### <a name="azure-site-recovery-scout-801-update-2-update-03dec15"></a>Mise à jour de Scout 8.0.1 Site Azure récupération 2 (mise à jour 03 déc 15)

Correctifs de mise à jour 2 sont les suivantes :

- **Serveur de configuration**: résoudre un problème qui ont empêché la fonctionnalité de mesure gratuite 31 jours de travail comme prévu lorsque le serveur de configuration a été enregistré dans la récupération de Site.
- **Agent d’unifiée**: résoudre un problème de mise à jour 1 qui a donné lieu à la mise à jour ne pas installé sur le serveur cible maître lorsqu’il a été mis à niveau à partir de 8.0 à version8.0.1.


### <a name="azure-site-recovery-scout-801-update-1"></a>Mise à jour de Scout 8.0.1 Site Azure récupération 1

Mise à jour 1 comprend les correctifs suivants et les nouvelles fonctionnalités :

- 31 derniers jours de protection gratuite par instance de serveur. Cela vous permet de tester les fonctionnalités ou configurer une preuve de concept.
    - Toutes les opérations sur le serveur, y compris le basculement et restauration, sont gratuites pour les premiers jours 31, à partir de l’heure à laquelle un serveur est protégé d’abord avec Scout de récupération de Site.
    - À partir du jour 32nd compter, chaque serveur protégé est facturée au taux standard instance pour la protection de récupération de Site Azure à un site appartenant à un client.
    - À tout moment, le nombre de serveurs protégés qui sont actuellement en cours facturée est disponible dans la page tableau de bord de l’archivage sécurisé récupération de Site Azure.
- Prise en charge ajoutée pour vSphere Interface de ligne de commande (vCLI) 5,5 mise à jour 2.
- Prise en charge pour les systèmes d’exploitation Linux sur le serveur source :
    - Mettre à jour RHEL 6 6
    - Mettre à jour RHEL 5 11
    - Mise à jour centOS 6 6
    - Mise à jour centOS 5 11
- Correctifs des bogues pour corriger les problèmes suivants :
    - Inscription de l’archivage sécurisé échoue pour le serveur de configuration ou réception.
    - Volumes cluster n’apparaissent pas comme prévu lorsque groupés machines virtuelles sont reprotected lors de leur reprise.
    - Retour arrière échoue quand le serveur maître cible est hébergé sur un autre serveur ESXi des machines virtuelles production locale.
    - Autorisations de fichier de configuration sont modifiées lorsque vous passez à la version 8.0.1, ce qui affecte la protection et opérations.
    - Le seuil de resynchronisation n’est pas appliqué comme prévu, qui permet d’accéder à comportement de la réplication n’est pas cohérente.
    - Les paramètres de RPO ne s’affichent pas correctement dans l’interface de serveur de configuration. La valeur de données non compressées affiche incorrectement la valeur compressée.
    -  L’opération de suppression ne supprime pas comme prévu dans l’Assistant vContinuum et réplication n’est pas supprimée à partir de l’interface de serveur de configuration.
    -  Dans l’Assistant vContinuum, le disque est automatiquement désactivée lorsque vous cliquez sur **Détails** dans l’affichage de disque pendant la protection des machines virtuelles MSCS.
    - Pendant le scénario (P2V) physique vers virtuel, les services HP requis, tels que CIMnotify et CqMgHost, ne sont pas déplacés manuel de récupération machine virtuelle. Le résultat démarrage supplémentaires.
    - Protection de la machine virtuelle Linux échoue lorsqu’il existe plus de 26 disques sur le serveur maître cible.

## <a name="next-steps"></a>Étapes suivantes

Publiez des questions que vous êtes sur le [forum de Services de récupération Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

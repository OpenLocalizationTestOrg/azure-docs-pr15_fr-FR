<properties
    pageTitle="Introduction à la sauvegarde DPM Azure | Microsoft Azure"
    description="Présentation de la sauvegarde des serveurs DPM au moyen du service de sauvegarde d’Azure"
    services="backup"
    documentationCenter=""
    authors="Nkolli1"
    manager="shreeshd"
    editor=""
    keywords="System Center Data Protection Manager, Gestionnaire de protection des données, sauvegarde dpm"/>

<tags
    ms.service="backup"
    ms.workload="storage-backup-recovery"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/21/2016"
    ms.author="trinadhk;giridham;jimpark;markgal"/>

# <a name="preparing-to-back-up-workloads-to-azure-with-dpm"></a>Préparation sauvegarder les charges de travail sur Azure avec DPM

> [AZURE.SELECTOR]
- [Serveur de sauvegarde Azure](backup-azure-microsoft-azure-backup.md)
- [SCDPM](backup-azure-dpm-introduction.md)
- [Serveur de sauvegarde Azure (classique)](backup-azure-microsoft-azure-backup-classic.md)
- [SCDPM (classique)](backup-azure-dpm-introduction-classic.md)


Cet article fournit une introduction à l’aide de Microsoft Azure sauvegarde pour protéger vos serveurs de System Center Data Protection Manager (DPM) et les charges de travail. En le lisant, vous devez comprendre :

- Fonctionne de sauvegarde du serveur DPM Azure
- Les conditions préalables pour obtenir une meilleure expérience de sauvegarde
- Les erreurs courantes rencontrées et comment traiter les
- Scénarios pris en charge

System Center DPM sauvegarde les données de fichiers et d’applications. Données sauvegardées sur DPM pouvant être stockées sur bande, sur le disque, ou sauvegardées sur Azure avec Microsoft Azure Backup. DPM interagit avec Azure sauvegarde comme suit :

- **DPM déployé comme une machine virtuelle serveur ou local physique** — si DPM est déployé comme un serveur physique ou comme une machine virtuelle de Hyper-V local vous pouvez sauvegarder des données sur un archivage sécurisé Azure sauvegarde en plus de disque et la bande sauvegarde.
- **DPM déployé comme une machine virtuelle Azure** — à partir de System Center 2012 R2 avec mise à jour 3, DPM peut être déployé comme une machine virtuelle Azure. Si DPM est déployé comme une machine virtuelle Azure, que vous pouvez sauvegarder des données sur disques Azure liés à la machine virtuelle DPM Azure, ou vous pouvez décharger le stockage des données en sauvegardant jusqu'à un archivage sécurisé sauvegarde Azure.

## <a name="why-backup-your-dpm-servers"></a>Pourquoi sauvegarder vos serveurs DPM ?

Les avantages de l’utilisation de sauvegardes Azure pour sauvegarder les serveurs DPM sont les suivantes :

- Pour le déploiement de DPM en local, vous pouvez utiliser sauvegarde Azure place un déploiement à long terme à bandes.
- Pour les déploiements DPM dans Azure, Azure sauvegarde permet de déléguer le stockage à partir du disque Azure, vous permettant d’évoluer par le stockage de données plus anciennes dans Azure sauvegarde et de nouvelles données sur le disque.

## <a name="how-does-dpm-server-backup-work"></a>Comment fonctionne la sauvegarde de serveur DPM ?
Pour sauvegarder une machine virtuelle, tout d’abord un instantané des données est nécessaire. Le service de sauvegarde Azure lance le travail de sauvegarde à l’heure planifiée et déclenche l’extension pour prendre un instantané de sauvegarde. L’extension de sauvegarde coordonnées avec le service VSS dans invité pour garantir la cohérence et appelle l’API instantané blob du service stockage Azure une fois que la cohérence a été atteinte. Pour ce faire pour obtenir un instantané cohérent des disques de la machine virtuelle, sans avoir pour l’arrêter.

Une fois l’instantané a été effectuée, les données sont transférées par le service de sauvegarde Azure à l’archivage sécurisé sauvegarde. Le service s’occupe d’identification et de transfert uniquement les blocs qui ont changé depuis la dernière sauvegarde optimiser le stockage des sauvegardes et le réseau. Lorsque le transfert de données est terminé, l’instantané est supprimée et un point de récupération est créé. Ce point de récupération peut être vues dans le portail classique Azure.

>[AZURE.NOTE] Pour les machines virtuelles Linux, sauvegarde uniquement fichier cohérente est possible.

## <a name="prerequisites"></a>Conditions préalables
Préparer la sauvegarde Azure pour sauvegarder des données DPM comme suit :

1. **Créer un archivage sécurisé sauvegarde** — créer un archivage sécurisé dans la console de sauvegarde Azure.
2. **Informations d’identification de l’archivage sécurisé téléchargement** — dans Azure sauvegarde, télécharger le certificat de gestion que vous avez créé dans l’archivage sécurisé.
3. **Installer l’Agent de sauvegarde Azure et enregistrer le serveur** — à partir d’Azure sauvegarde, installez l’agent sur chaque serveur DPM et inscrire le serveur DPM dans l’archivage sécurisé sauvegarde.

[AZURE.INCLUDE [backup-create-vault](../../includes/backup-create-vault.md)]

[AZURE.INCLUDE [backup-download-credentials](../../includes/backup-download-credentials.md)]

[AZURE.INCLUDE [backup-install-agent](../../includes/backup-install-agent.md)]


## <a name="requirements-and-limitations"></a>Configuration requise (et limitations)

- DPM peut fonctionner comme un serveur physique ou une machine virtuelle Hyper-V installé sur System Center 2012 SP1 ou System Center 2012 R2. Il peut également s’exécuter en tant qu’une machine virtuelle Azure s’exécutant sur System Center 2012 R2 au moins DPM 2012 R2 mise à jour cumulative 3 ou une machine virtuelle Windows dans VMWare exécuté sur System Center 2012 R2 au moins 5 report de mise à jour.
- Si vous exécutez DPM avec System Center 2012 SP1, vous devez installer la mise à jour cumulative 2 pour System Center Data Protection Manager SP1. Cela est nécessaire avant d’installer l’Agent de sauvegarde Azure.
- Le serveur DPM doit comporter Windows PowerShell et .net Framework 4.5 installé.
- DPM peut en sauvegarder la plupart des charges de travail de sauvegarde Azure. Pour une liste complète des a pris en charge de voir la sauvegarde Azure prend en charge les éléments ci-dessous.
- Données stockées dans Azure sauvegarde ne peuvent pas être récupérées avec l’option « Copier sur bande ».
- Vous devez posséder un compte Azure avec la fonction de sauvegarde Azure activée. Si vous n’avez pas un compte, vous pouvez créer un compte d’évaluation gratuit en quelques minutes. En savoir plus sur [sauvegarde Azure tarifs](https://azure.microsoft.com/pricing/details/backup/).
- L’utilisation de sauvegardes Azure nécessite l’Agent de sauvegarde Azure soit installé sur les serveurs que vous souhaitez sauvegarder. Chaque serveur doit avoir au moins 10 % de la taille des données qui sont sauvegardées, disponible en tant qu’espace de stockage gratuit local. Par exemple, la sauvegarde de 100 Go de données requiert un minimum de 10 Go d’espace libre dans l’emplacement de travail. Bien que la valeur minimale est de 10 %, 15 % de l’espace de stockage local à utiliser pour l’emplacement du cache est recommandé.
- Données seront stockées dans le stockage de l’archivage sécurisé Azure. Il n’existe aucune limite à la quantité de données, que vous pouvez revenir à une sauvegarde Azure vault mais la taille d’une source de données (par exemple une machine virtuelle ou une base de données) ne doivent pas dépasser 54,400 Go.

Types de fichiers suivants sont pris en charge pour sauvegarder vos fichiers Azure :

- Chiffré (sauvegardes complètes uniquement)
- Compressé (incrémentielles pris en charge)
- Diminuer (incrémentielles pris en charge)
- Comprimée et incomplets (considérées comme diminuer)

Et Voici non prises en charge :

- Serveurs sur les systèmes de fichiers respectant la casse ne sont pas pris en charge.
- Liens en dur (ignoré)
- Points (ignoré) d’analyse
- Chiffrés et compressé (ignoré)
- Chiffré et incomplet (ignorée)
- Flux compressé
- Flux incomplet

>[AZURE.NOTE] À partir de System Center 2012 DPM avec SP1, vous pouvez sauvegarder les charges de travail protégés par DPM vers Azure à l’aide de Microsoft Azure sauvegarde.

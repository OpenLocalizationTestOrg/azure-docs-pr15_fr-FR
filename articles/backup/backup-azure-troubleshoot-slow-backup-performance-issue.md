<properties
   pageTitle="Résoudre les problèmes de sauvegarde lente des fichiers et dossiers dans Azure sauvegarde | Microsoft Azure"
   description="Fournit des conseils de dépannage pour vous aider à diagnostiquer l’origine des problèmes de performances sauvegarde Azure"
   services="backup"
   documentationCenter=""
   authors="genlin"
   manager="jimpark"
   editor=""/>

<tags
    ms.service="backup"
    ms.workload="storage-backup-recovery"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/13/2016"
    ms.author="genli"/>

# <a name="troubleshoot-slow-backup-of-files-and-folders-in-azure-backup"></a>Résoudre les problèmes de sauvegarde lente des fichiers et dossiers dans Azure sauvegarde

Cet article fournit des conseils de dépannage pour vous aider à diagnostiquer la cause de baisse des performances de sauvegarde des fichiers et dossiers lorsque vous utilisez Azure sauvegarde. Lorsque vous utilisez l’agent de sauvegarde Azure pour sauvegarder des fichiers, le processus de sauvegarde peut prendre plus longtemps que prévu. Ce retard peut être dû d’une ou plusieurs des opérations suivantes :

-   [Il existe dégradation des performances sur l’ordinateur qui est sauvegardé.](#cause1)
-   [Un autre processus ou un logiciel antivirus interfère avec le processus de sauvegarde Azure.](#cause2)
-   [L’agent de sauvegarde s’exécute sur une Azure machine virtuelle ().](#cause3)  
-   [Vous sauvegardez un grand nombre (millions) de fichiers.](#cause4)

Avant de commencer la résolution des problèmes, nous vous recommandons de télécharger et d’installer [dernière agent de sauvegarde Azure](http://aka.ms/azurebackup_agent). Nous apportez fréquentes mises à jour à l’agent de sauvegarde à corriger les problèmes différents, ajouter des fonctionnalités et améliorer les performances.

Nous vous recommandons vivement également que vous passez en revue le [Forum aux questions de sauvegarde Azure service](backup-azure-backup-faq.md) pour vous assurer que vous rencontrez pas les problèmes de configuration courants.

[AZURE.INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

<a id="cause1"></a>
## <a name="cause-performance-bottlenecks-on-the-computer"></a>Cause : Dégradation des performances sur l’ordinateur

Engorgements sur l’ordinateur qui est sauvegardé peuvent entraîner des retards. Par exemple, possibilité de l’ordinateur pour lire ou écrire sur disque ou la bande passante disponible pour envoyer des données via le réseau, peut provoquer des engorgements.

Windows fournit un outil intégré qui a appelé [Analyseur de performances](https://technet.microsoft.com/magazine/2008.08.pulse.aspx) (Analyseur de performances) pour détecter ces engorgements.

Voici quelques compteurs de performance et les plages qui peuvent être utiles pour diagnostiquer engorgements des sauvegardes optimales.

| Compteur  | État  |
|---|---|
|Disque logique (disque physique)--% inactive   | • 100 % idle à 50 % inactif = sain</br>• 49 % idle à 20 % inactif = avertissement ou au moniteur</br>• 19 % idle sur 0 % inactif = critique ou absence du contexte|
|  Disque logique (disque physique)--% moy. Disque Sec lecture ou écriture |  • 0,001 ms à 0,015 ms = sain</br>• 0,015 ms pour ms 0,025 = avertissement ou au moniteur</br>• ms 0.026 ou plus = critique ou absence du contexte|
|  Disque logique (disque physique)--long. (pour toutes les instances) | 80 demandes pendant plus de 6 minutes |
| Mémoire--Pool Non pagination octets|• Inférieure à 60 % du pool consommée = sain<br>• 61 à 80 % du pool consommée = avertissement ou au moniteur</br>• Supérieure à 80 % pool consommée = critique ou absence du contexte|
| Mémoire--Octets paginés du Pool |• Inférieure à 60 % du pool consommée = sain</br>• 61 à 80 % du pool consommée = avertissement ou au moniteur</br>• Supérieure à 80 % pool consommée = critique ou absence du contexte|
| Mémoire : mégaoctets disponibles| • 50 % de mémoire disponible ou plus = sain</br>• 25 % de mémoire disponible = Moniteur</br>• 10 % de mémoire disponible = avertissement</br>• Inférieure à 100 Mo ou égal à 5 % de mémoire disponible = critique ou absence du contexte|
|Processeur--\%temps processeur (toutes les instances)|• Inférieure à 60 % consommées = sain</br>• 61 à 90 % consommées = moniteur ou faire preuve de prudence</br>• 91 à 100 % consommées = critique|


> [AZURE.NOTE] Si vous estimez que l’infrastructure est la cause du problème, nous vous recommandons de défragmenter les disques régulièrement pour améliorer les performances.

<a id="cause2"></a>
## <a name="cause-another-process-or-antivirus-software-interfering-with-azure-backup"></a>Cause : Un autre processus ou un logiciel antivirus interférer avec sauvegarde Azure

Nous l’avons vu plusieurs instances où autres processus dans le système Windows ont affectés par les performances du processus de l’agent de sauvegarde Azure. Par exemple, si vous utilisez l’agent de sauvegarde Azure et un autre programme pour sauvegarder des données, ou si un logiciel antivirus est en cours d’exécution et possède un verrou sur des fichiers à sauvegarder, le multiple verrouille sur fichiers risque de conflit. Dans ce cas, la sauvegarde peut échouer ou la tâche peut prendre plus longtemps que prévu.

La meilleure recommandation dans ce scénario consiste à désactiver l’autre programme sauvegarde pour voir si la durée de sauvegarde de l’agent de sauvegarde Azure change. En règle générale, il est suffisant pour empêcher d’affecter les uns des autres de s’assurer que plusieurs opérations de sauvegarde ne s’exécutent pas en même temps.

Pour les programmes antivirus, nous vous recommandons d’exclure les fichiers et dossiers suivants :

- C:\Program Files\Microsoft Azure récupération Services Agent\bin\cbengine.exe en tant que processus
- C:\Program Files\Microsoft Azure récupération Services l’Agent\ dossiers
- Emplacement des rayures (si vous n’utilisez pas l’emplacement standard)

<a id="cause3"></a>
## <a name="cause-backup-agent-running-on-an-azure-virtual-machine"></a>Cause : Agent de sauvegarde s’exécutant sur une machine virtuelle Azure

Si vous exécutez l’agent de sauvegarde sur un ordinateur virtuel, performances seront plus lente que lorsque vous exécutez sur un ordinateur physique. Il s’agit normalement en raison des limitations sorties par.  Toutefois, vous pouvez optimiser les performances en changeant les lecteurs de données sont sauvegardées sur le stockage Azure Premium. Nous travaillons sur résoudre ce problème, et le correctif sera disponible dans une version ultérieure.

<a id="cause4"></a>
## <a name="cause-backing-up-a-large-number-millions-of-files"></a>Cause : Sauvegarder un grand nombre (millions) de fichiers

Déplacement d’un grand volume de données doit prendre plu de déplacement du plus petit volume de données. Dans certains cas, les temps de sauvegarde est lié à non seulement la taille des données, mais également le nombre de fichiers ou dossiers. Cela est particulièrement vrai lorsque des millions de petits fichiers (quelques octets à quelques kilo-octets) sont sauvegardées.

Ce problème se produit car lorsque vous êtes sauvegardant les données et déplaçant vers Azure, Azure est catalogues simultanément vos fichiers. Dans certains scénarios rares, l’opération de catalogue peut prendre plus longtemps que prévu.

Les indicateurs suivants peuvent vous aider à comprendre la critique et en conséquence travailler sur les étapes suivantes :

- **Interface utilisateur s’affiche pour le transfert de données de l’avancement**. Les données sont toujours en cours de transfert. La bande passante réseau ou la taille des données peut être décalé en raison.

- **Interface utilisateur ne s’affiche pas l’avancement pour le transfert de données**. Ouvrir les journaux situé à C:\Microsoft Azure récupération Services Agent\Temp, puis recherchent de l’entrée FileProvider::EndData dans les journaux. Cette entrée indique que le transfert de données terminé et que l’opération de catalogue neuf. Ne pas annuler les travaux de sauvegarde. À la place, attendez un peu plus de l’opération de catalogue terminer. Si le problème persiste, contactez [Azure prend en charge](https://portal.azure.com/#create/Microsoft.Support).

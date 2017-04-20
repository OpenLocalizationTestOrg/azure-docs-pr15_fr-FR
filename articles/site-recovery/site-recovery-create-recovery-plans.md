<properties
    pageTitle="Créer des plans de récupération | Microsoft Azure" 
    description="Créer des plans de récupération avec récupération du Site Azure pour basculer et récupérer des groupes de machines virtuelles et des serveurs physiques." 
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
    ms.workload="storage-backup-recovery" 
    ms.date="10/05/2016"
    ms.author="raynew"/>

# <a name="create-recovery-plans"></a>Créer des plans de restauration

Le service de récupération de Site Azure contribue à votre stratégie d’entreprise continuité et de reprise récupération (BCDR) en organiser réplication, basculement et la restauration des machines virtuelles et des serveurs physiques. Machines peuvent être répliquées vers Azure, ou à un centre de données secondaire en local. Pour un bref aperçu lisez [Nouveautés récupération de Site Azure ?](site-recovery-overview.md).


## <a name="overview"></a>Vue d’ensemble

Cet article fournit des informations sur la création et personnalisation des plans de récupération. 

Plans de récupération consistant par un ou plusieurs groupes ordonnées qui contiennent des machines virtuelles serveurs physiques que vous souhaitez basculer entre eux. Plans de récupération procédez comme suit :

- Définir des groupes d’ordinateurs qui basculent, puis démarrent ensemble.
- Dépendances de modèle entre machines en les groupant ensemble dans un groupe de plan de récupération. Pour exemple si vous voulez basculer et faire apparaître une application spécifique que vous souhaitez regrouper les ordinateurs virtuels pour cette application dans le même groupe de plan de récupération.
- Automatiser et étendre basculement. Vous pouvez exécuter un test, planifiée, ou le basculement non planifié sur un plan de récupération. Vous pouvez personnaliser les plans de récupération des scripts, automatisation Azure et actions manuelles.

Plans de récupération sont affichés dans les **Plans de récupération** dans le portail de récupération de Site.


Publier des commentaires ou des questions en bas de cet article, ou sur le [Forum de Services de récupération Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

## <a name="before-you-start"></a>Avant de commencer

Notez les points suivants :

- Un plan de récupération ne doivent pas mélanger machines virtuelles avec cartes réseau une ou plusieurs. C’est parce que mélange de ces ordinateurs virtuels n’est pas autorisée dans un service cloud Azure.
- Vous pouvez étendre les plans de récupération avec des scripts et actions manuelles. Notez les points suivants :
    - Écriture de scripts avec Windows PowerShell.
    - Vérifiez que les scripts utilisent blocs try captures afin que les exceptions sont traitées normalement. S’il existe une exception dans le script l’arrêt en cours d’exécution et la tâche affiche qu’a échoué.  Si une erreur se produit, une partie quelconque restante du script ne fonctionne pas. Si cela se produit lorsque vous exécutez un basculement non planifié, le plan de récupération continue. Si cela se produit lorsque vous exécutez un basculement planifié, le plan de récupération s’arrête. Dans ce cas, corrigez le script, assurez-vous qu’il fonctionne comme prévu et puis exécutez à nouveau le plan de récupération.
    - La commande écriture hôte ne fonctionne pas dans un script de plan de récupération, et le script échouera. Si vous voulez créer la sortie, créez un script de proxy qui à son tour s’exécute votre script principal et vous assurer que tous les résultats sont transmis à l’aide de la >> commande.
    - Le script arrive à expiration si elle ne renvoie pas de 600 secondes.
    - Si rien est écrites dans STDERR, le script est considéré comme ayant échoué. Ces informations seront affichera dans les détails de l’exécution de script.
    - Si vous utilisez VMM dans votre déploiement Notez que :

        - Scripts dans un plan de récupération s’exécutent dans le contexte du compte de Service VMM. Vérifiez que ce compte dispose des autorisations de lecture sur le partage distant sur lequel se trouve le script et tester le script à exécuter au niveau de privilèges du compte service VMM.
        - Applets de commande VMM sont remis dans un module Windows PowerShell. Le module VMM Windows PowerShell est installé lorsque vous installez la console. Le module VMM peut être chargé dans votre script en utilisant la commande suivante dans le script : Module d’importation-virtualmachinemanager nom. [Obtenir plus de détails](hhttps://technet.microsoft.com/library/hh875013.aspx).
        - Vérifiez que vous avez au moins un serveur de bibliothèque dans votre déploiement VMM. Par défaut le chemin d’accès du partage de bibliothèque pour un serveur VMM se trouve localement sur le serveur VMM avec le nom du dossier MSCVMMLibrary.
        - Si votre chemin d’accès du partage de bibliothèque est distant (ou locale mais pas partagé avec MSCVMMLibrary, configurez le partage comme suit (à l’aide de \\libserver2.contoso.com\share\ par exemple) :
            - Ouvrez l’Éditeur du Registre et accédez à HKEY_LOCAL_MACHINE\SOFTWARE\MICROSOFT\Azure Site Recovery\Registration.
            -  Modifiez la valeur ScriptLibraryPath et placez la valeur en tant que \\libserver2.contoso.com\share\. spécifier le nom de domaine complet complète. Accorder des autorisations à l’emplacement partagé.
            -  Veillez à tester le script avec un compte d’utilisateur possédant les mêmes autorisations que le compte de service VMM, pour vous assurer que les scripts testés autonomes s’exécutent de la même façon qu’ils ne le sont dans les plans de récupération. Sous le moniteur serveur, définissez la stratégie d’exécution pour ignorer comme suit :
                -  Ouvrez la console Windows PowerShell 64 bits avec des privilèges élevés.
                -  Type : **Set-executionpolicy ignorer**. [Obtenir plus de détails](https://technet.microsoft.com/library/ee176961.aspx).

## <a name="create-a-recovery-plan"></a>Créer un plan de récupération

Le mode dans lequel vous créez un plan de récupération dépend de votre déploiement de récupération de Site.

- **Réplication de machines virtuelles VMware et des serveurs physiques**, vous créez un plan et ajouter des groupes de réplication qui contiennent des machines virtuelles et des serveurs physiques à un plan de récupération.
- **Réplication de machines virtuelles Hyper-V (en nuages VMM)**, vous créez un plan et ajouter protégées machines virtuelles de Hyper-V à partir d’un nuage VMM à un plan de récupération.
- **Réplication de machines virtuelles Hyper-V (et non dans nuages VMM)**: créez un plan et ajouter protégées machines virtuelles de Hyper-V à partir d’un groupe de protection à un plan de récupération.
- **La réplication SAN**: créer un plan et ajouter un groupe de réplication contenant des machines virtuelles pour le plan de récupération. Vous sélectionnez un groupe de réplication au lieu de machines virtuelles spécifiques, car toutes les machines virtuelles dans un groupe de réplication doit basculer entre eux (basculement se produit à la couche de stockage tout d’abord).


Créer un plan de récupération comme suit :

1. Cliquez sur onglet **Récupération Plans** > **Créer un Plan de récupération**.
Spécifiez un nom pour le plan de récupération et une source et cible. Le serveur source doit avoir des machines virtuelles qui sont activés pour le basculement et récupération.

    - Si vous êtes la réplication à partir de VMM à VMM sélectionner le **Type de Source** > **VMM**et les serveurs VMM sources et cibles. Cliquez sur **Hyper-V** pour voir nuages qui sont configurés pour utiliser réplica Hyper-V. 
    - Si vous êtes la réplication à partir de VMM à VMM à l’aide de SAN sélectionner un **Type de Source** > **VMM**et les serveurs VMM sources et cibles. Cliquez sur **SAN** pour voir nuages qui sont configurés pour la réplication SAN.
    - Si vous êtes réplication à partir de VMM vers Azure sélectionner le **Type de Source** > **VMM**.  Sélectionnez le serveur VMM source et **Azure** comme cible.
    - Si vous êtes la réplication à partir d’un site Hyper-V sélectionner le **Type de Source** > **site Hyper-V**. Sélectionnez le site en tant que source et **Azure **comme cible.
    - Si vous êtes réplication auprès de VMware ou un serveur physique local vers Azure, sélectionnez un serveur de configuration comme source et **Azure** comme cible

2. Dans la zone **Sélectionner machines virtuelles** sélectionnez machines virtuelles (ou groupe de réplication) que vous voulez ajouter au groupe par défaut (groupe 1) dans le plan de récupération.

## <a name="customize-recovery-plans"></a>Personnaliser les plans de récupération

Une fois que vous avez ajouté protégée machines virtuelles ou des groupes de réplication dans le groupe plan de récupération par défaut et créé le plan que vous pouvez le personnaliser :

- **Ajouter de nouveaux groupes**, vous pouvez ajouter des groupes de plan de récupération supplémentaires. Groupes que vous ajoutez sont numérotées dans l’ordre dans lequel vous les ajoutez. Vous pouvez ajouter jusqu'à sept groupes. Vous pouvez ajouter plusieurs ordinateurs ou groupes de réplication à ces nouveaux groupes. Notez qu’une machine virtuelle ou un groupe de réplication peut uniquement être inclus dans le groupe plan d’une récupération.
- **Ajout d’un script **, vous pouvez ajouter des scripts que, avant ou après une récupération, envisagez de groupe. Lorsque vous ajoutez un script qu’elle ajoute un nouvel ensemble d’actions pour le groupe. Par exemple un ensemble d’étapes préalables pour 1 groupe sera créé avec le nom : 1 groupe : étapes préalables. Toutes les étapes préalables seront affichent à l’intérieur de cet ensemble. Notez que vous pouvez uniquement ajouter un script sur le site principal si vous avez un serveur VMM déployé.
- **Ajouter une action manuelle**, vous pouvez ajouter des actions manuelles qui s’exécutent avant ou après un groupe de plan de récupération. Lors de l’exécution du plan de récupération, il s’arrête à l’endroit où vous avez inséré l’action manuelle et une boîte de dialogue vous invite à indiquer que l’action manuelle ont été achevée.

## <a name="extend-recovery-plans-with-scripts"></a>Étendre les plans de récupération avec des scripts

Vous pouvez ajouter un script à votre plan de récupération :

- Si vous avez un site source VMM vous pouvez créer un script sur le serveur VMM et inclure dans votre plan de récupération.
- Si vous êtes réplication vers Azure vous pouvez intégrer des procédures opérationnelles automation Azure dans votre plan de récupération

### <a name="create-a-vmm-script"></a>Créer un script VMM


Créez le script comme suit :

1. Créer un dossier dans le partage de bibliothèque, par exemple \<VMMServerName > \MSSCVMMLibrary\RPScripts. Placer sur la source et cible serveurs VMM.
2. Créez le script (par exemple RPScript) et vérifier qu’il fonctionne comme prévu.
3. Placez le script dans l’emplacement \<VMMServerName > \MSSCVMMLibrary sur les serveurs VMM sources et cibles.

### <a name="create-an-azure-automation-runbook"></a>Créer un runbook automation Azure

Vous pouvez étendre votre plan de récupération en exécutant un runbook Azure automation dans le cadre du plan. [Pour en savoir plus](site-recovery-runbook-automation.md).


### <a name="add-custom-settings-to-a-recovery-plan"></a>Ajouter des paramètres personnalisés à un plan de récupération

1. Ouvrez le plan de récupération que vous voulez personnaliser.
2. Cliquez ici pour ajouter un machines virtuelles ou sur Nouveau groupe.
3. Pour ajouter un script ou manuel action n’importe quel élément dans la liste **étape** , puis cliquez sur **Script** ou **Action manuelle**. Indiquez si vous souhaitez ajouter le script ou l’action avant ou après l’élément sélectionné. Utilisez les boutons de commande **Déplacer vers le haut** et **Déplacer vers le bas** pour déplacer la position du script vers le haut ou vers le bas.
4. Si vous ajoutez un script VMM, sélectionnez **basculement script VMM**et dans **Chemin d’accès de Script** , tapez le chemin d’accès relatif au partage. C’est le cas, dans notre exemple où se trouve le partage en \\ <VMMServerName>\MSSCVMMLibrary\RPScripts, spécifiez le chemin d’accès : \RPScripts\RPScript.PS1.
5. Si vous ajoutez un objet automation Azure exécuter carnet de, spécifiez le **Compte d’automatisation Azure** dans lequel se trouve le runbook, puis sélectionnez le **Azure Runbook Script**approprié.
5. Effectuez un basculement du plan de récupération pour vous assurer que le script fonctionne comme prévu.


## <a name="next-steps"></a>Étapes suivantes

Vous pouvez exécuter différents types de basculement sur le plan de récupération, y compris un basculement test pour vérifier votre environnement et basculement planifié ou non. [En savoir plus](site-recovery-failover.md).


 

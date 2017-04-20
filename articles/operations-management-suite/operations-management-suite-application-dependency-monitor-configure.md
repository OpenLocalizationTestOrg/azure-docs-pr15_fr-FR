<properties
   pageTitle="Configuration de la Suite de gestion des opérations (OMS) Application dépendance moniteur (ADM) | Microsoft Azure"
   description="Application dépendance moniteur (autorisé) est une solution Suite de gestion des opérations (OMS) qui automatiquement découvre des composants d’application sur les systèmes Windows et Linux et mappe la communication entre les services.  Cet article fournit des détails pour le déploiement ADM dans votre environnement et à l’utiliser dans différents scénarios."
   services="operations-management-suite"
   documentationCenter=""
   authors="daseidma"
   manager="jwhit"
   editor="tysonn" />
<tags
   ms.service="operations-management-suite"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="09/28/2016"
   ms.author="daseidma;bwren" />

# <a name="configuring-application-dependency-monitor-solution-in-operations-management-suite-oms"></a>Configuration de solution d’Application dépendance moniteur dans Suite de gestion des opérations (OMS)
![Icône d’alerte de gestion](media/operations-management-suite-application-dependency-monitor/icon.png) Application dépendance moniteur automatiquement découvre des composants d’application sur les systèmes Windows et Linux et mappe la communication entre les services. Il permet de vous permettent de visualiser vos serveurs qu’il vous semble d'entre eux – systèmes interconnectés de fournir des services critiques.  Application dépendance moniteur affiche les connexions entre les serveurs, processus, et ports sur n’importe quelle architecture connecté TCP sans configuration requise autre que l’installation d’un agent.

Cet article décrit les détails de la configuration des agents moniteur de dépendance d’Application et d’intégration.  Pour plus d’informations sur l’utilisation de ADM, voir [Moniteur de dépendance d’Application à l’aide de solution dans Suite de gestion des opérations (OMS)](operations-management-suite-application-dependency-monitor.md)

>[AZURE.NOTE]Application dépendance moniteur est en cours d’aperçu privé.  Vous pouvez demander l’accès à l’aperçu privé ADM en [https://aka.ms/getadm](https://aka.ms/getadm).
>
>Lors de l’aperçu privé, tous les comptes OMS ont un accès illimité à ADM.  Nœuds ADM sont gratuites, mais les données de journal Analytique pour les types AdmComputer_CL et AdmProcess_CL sont limitées comme n’importe quelle autre solution.
>
>Une fois que ADM entré présentation publique, il sera disponible uniquement aux clients gratuites et payantes d’un aperçu et Analytique dans le Plan de tarification OMS.  Comptes couche libre sont limités à 5 nœuds ADM.  Si vous participez à la version bêta privée et que vous n’êtes pas inscrit dans le Plan de tarification OMS lorsqu’ADM entre la version d’évaluation, ADM est désactivée à cet horaire. 



## <a name="connected-sources"></a>Sources connectées
Le tableau suivant décrit les sources connectés sont pris en charge par la solution ADM.

| Source connecté | Prise en charge | Description |
|:--|:--|:--|
| [Agents Windows](../log-analytics/log-analytics-windows-agents.md) | Oui | ADM analyse et collecte des données à partir d’ordinateurs de l’agent Windows.  <br><br>Notez que, en plus de l’agent OMS, agents Windows nécessitent l’Agent de dépendance de Microsoft.  Voir les [systèmes d’exploitation pris en charge](#supported-operating-systems) pour une liste complète des versions des systèmes d’exploitation. |
| [Agents Linux](../log-analytics/log-analytics-linux-agents.md) | Oui | ADM analyse et collecte des données à partir d’ordinateurs agents Linux.  <br><br>Notez qu’en plus de l’agent OMS, agents Linux nécessitent l’Agent de dépendance de Microsoft.  Voir les [systèmes d’exploitation pris en charge](#supported-operating-systems) pour une liste complète des versions des systèmes d’exploitation. |
| [Groupe d’administration SCOM](../log-analytics/log-analytics-om-agents.md) | Oui | ADM analyse et collecte des données à partir de Windows et Linux agents dans un groupe d’administration SCOM connecté. <br><br>Une connexion directe à partir de l’ordinateur de l’agent SCOM à OMS est requise. Données sont envoyées directement à partir de transférés vers le référentiel OMS du groupe d’administration.|
| [Compte de stockage Azure](../log-analytics/log-analytics-azure-storage.md) | N° | ADM collecte des données à partir d’ordinateurs agent, donc il n’existe pas de données à partir de celui-ci pour recueillir à partir du stockage Azure. |

Notez que ADM prend en charge les plateformes 64 bits.

Sous Windows, l’Agent de surveillance de Microsoft (MMA) est utilisé par SCOM et OMS pour collecter et envoyer des données d’analyse.  (Cet agent est appelé Agent SCOM, Agent OMS, MMA ou Agent directe, selon le contexte.)  SCOM et OMS fournissent différentes déconnecter les versions de zone de MMA, mais ces versions peuvent chaque état SCOM, OMS ou les deux.  Sous Linux, l’Agent OMS pour Linux collecte et envoie analyse des données à OMS.  Vous pouvez utiliser ADM sur des serveurs avec Agents Direct OMS ou sur des serveurs qui sont joints à OMS via SCOM des groupes d’administration.  Aux fins de cette documentation, nous avons fait référence à tous ces agents – sur Linux ou Windows, si connecté à un MG SCOM ou directement à OMS – comme « Agent OMS », à moins que le nom du déploiement spécifique de l’agent est nécessaire pour le contexte.

L’agent ADM ne transmet pas toutes les données lui-même, et elle ne nécessite pas les modifications apportées aux pare-feux ou ports.  Données de ADM sont toujours transmises par l’Agent OMS OMS, directement ou via la passerelle OMS.

![ADM Agents](media/operations-management-suite-application-dependency-monitor/agents.png)

Si vous êtes un client SCOM avec un groupe d’administration connecté à OMS :

- Si vos agents SCOM puissent accéder à internet pour vous connecter à OMS, aucune configuration supplémentaire est requise.  
- Si vos agents SCOM n’est pas accessible OMS via internet, vous devrez configurer la passerelle OMS pour l’utiliser avec SCOM.
  
Si vous utilisez l’Agent Direct OMS, vous devez configurer l’Agent OMS lui-même se connecter à OMS ou à votre passerelle OMS.  La passerelle OMS peuvent être téléchargée à partir de [https://www.microsoft.com/en-us/download/details.aspx?id=52666](https://www.microsoft.com/en-us/download/details.aspx?id=52666)


### <a name="avoiding-duplicate-data"></a>Pour éviter d’obtenir des données en double

Si vous êtes un client SCOM, vous ne devez pas configurer vos agents SCOM communiquer directement à OMS, ou les données sont signalées à deux reprises.  Cela provoquera autorisé sur les ordinateurs qui apparaissent deux fois dans la liste des ordinateurs.

Configuration de OMS doit se produire que dans l’un des emplacements suivants :

- Le panneau de configuration SCOM Console opérations Management Suite pour les ordinateurs gérés
- Configuration de perspectives opérationnelles Azure dans les propriétés MMA

L’utilisation de ces deux configurations avec le même espace de travail dans chaque provoquera dupliquer des données. À l’aide à la fois avec différents espaces de travail peut entraîner une configuration en conflit (celui entouré par une solution ADM activé, l’autre sans) qui peut empêcher le dirige vers ADM complètement de données.  

Notez que, même si la machine elle-même n’est pas spécifiée dans OMS configuration la Console SCOM, si un groupe d’Instance telles que « Windows Server Instances groupe » est actif, il peut entraîner toujours dans la configuration OMS réception machine via SCOM.



## <a name="management-packs"></a>Packs d’administration
Lorsqu’ADM est activé dans un espace de travail OMS, un 300 Ko Management Pack est envoyée à tous les Microsoft surveillance Agents dans cet espace de travail.  Si vous utilisez agents SCOM dans un [groupe gestion connecté](../log-analytics/log-analytics-om-agents.md), le Pack d’administration ADM seront déployé à partir de SCOM.  Si les agents sont connectés directement, le panneau de gestion est remise par OMS.

Le panneau de gestion est nommé Microsoft.IntelligencePacks.ApplicationDependencyMonitor*.  Il est conçu pour *%Programfiles%\Microsoft surveillance Agent\Agent\Health Service State\Management Packs\*.  La source de données utilisée par le pack d’administration est *% programme files%\Microsoft surveillance Agent\Agent\Health Service State\Resources\<AutoGeneratedID > \Microsoft.EnterpriseManagement.Advisor.ApplicationDependencyMonitorDataSource.dll*.



## <a name="configuration"></a>Configuration
En plus de Windows et Linux ordinateurs un agent sont installé et connecté à OMS, le programme d’installation de l’Agent de dépendance doit être téléchargé à partir de la solution ADM et puis installé comme racine ou administrateur sur chaque serveur géré.  Une fois que l’agent ADM est installé sur un serveur de création de rapports d’OMS, cartes de dépendance ADM seront affichent dans les 10 minutes.  Si vous rencontrez des problèmes, veuillez envoyer un e-mail [oms-adm-support@microsoft.com](mailto:oms-adm-support@microsoft.com).


### <a name="migrating-from-bluestripe-factfinder"></a>Migration de BlueStripe FactFinder
Moniteur d’application dépendance apportera BlueStripe technologie dans OMS en phases. FactFinder est toujours pris en charge pour les clients existants, mais n’est plus disponible à l’achat individuel.  Cette version d’évaluation de l’Agent de dépendance ne peut communiquer avec OMS.  Si vous êtes un client FactFinder actuel, Veuillez identifier un ensemble de serveurs de test pour ADM qui ne sont pas gérés par FactFinder. 

### <a name="download-the-dependency-agent"></a>Téléchargez l’Agent de dépendance
Outre l’Agent de gestion de Microsoft (MMA) et OMS Linux Agent qui fournit la connexion entre l’ordinateur et OMS, tous les ordinateurs analysés par l’Analyseur de dépendance Application doivent la dépendance Agent est installé.  Sous Linux, l’Agent OMS pour Linux doit être installé avant l’Agent de dépendance. 

![Vignette de l’application Moniteur de dépendance](media/operations-management-suite-application-dependency-monitor/tile.png)

Pour télécharger l’Agent de dépendance, cliquez sur **Configurer la Solution** dans la vignette **Application dépendance moniteur** pour ouvrir la carte de **L’Agent de dépendance** .  La carte de dépendance Agent dispose des liens pour les fenêtres et les agents Linux. Cliquez sur le lien pour télécharger chaque agent approprié. Consultez les sections suivantes pour plus d’informations sur l’installation de l’agent sur des systèmes différents.

### <a name="install-the-dependency-agent"></a>Installer l’Agent de dépendance

#### <a name="microsoft-windows"></a>Microsoft Windows
Droits d’administrateur sont requises pour installer et désinstaller l’agent.

L’Agent de dépendance est installé sur les ordinateurs Windows avec ADM-Agent-Windows.exe. Si vous exécutez ce fichier exécutable sans aucune option, il démarre un Assistant que vous pouvez suivre pour effectuer l’installation de façon interactive.  

Procédez comme suit pour installer l’Agent de dépendance sur chaque ordinateur Windows.

1.  Assurez-vous que l’agent OMS est installé en suivant les instructions sur les ordinateurs de se connecter directement au OMS.
2.  Téléchargez l’agent Windows et exécutez la commande suivante.<br>*ADM-Agent-Windows.exe*
3.  Suivez l’Assistant pour installer l’agent.
4.  Si l’Agent de dépendance ne démarre pas, vérifiez les journaux pour des informations détaillées. Sous agents Windows, le répertoire du journal est *C:\Program Files\BlueStripe\Collector\logs*. 

L’Agent pour Windows de dépendance peut être désinstallé par un administrateur via le panneau de configuration.


#### <a name="linux"></a>Linux
Accès à la racine est nécessaire pour installer et configurer l’agent.

L’Agent de dépendance est installé sur les ordinateurs Linux avec ADM-Agent-Linux64.bin, un script shell avec un fichier binaire extraction automatique. Vous pouvez exécuter le fichier avec ch ou ajouter des autorisations d’exécution pour le fichier proprement dit.
 
Procédez comme suit pour installer l’Agent de dépendance sur chaque ordinateur Linux.

1.  Assurez-vous que l’agent OMS est installé en suivant les instructions à [collecter et gérer des données à partir d’ordinateurs Linux.  Il doit être installé avant l’Agent de dépendance Linux](https://technet.microsoft.com/library/mt622052.aspx).
2.  Installez l’agent de dépendance Linux en tant que racine à l’aide de la commande suivante.<br>*CH ADM-Agent-Linux64.bin*.
3.  Si l’Agent de dépendance ne démarre pas, vérifiez les journaux pour des informations détaillées. Sous agents Linux, le répertoire du journal est */var/opt/microsoft/dependency-agent/log*.

### <a name="uninstalling-the-dependency-agent-on-linux"></a>Désinstallation de l’Agent de dépendance sous Linux
Pour désinstaller complètement l’Agent de dépendance de Linux, vous devez supprimer l’agent lui-même et le serveur proxy qui est installé automatiquement avec l’agent.  Vous pouvez désinstaller les deux avec la commande ci-dessous.

    rpm -e dependency-agent dependency-agent-connector


### <a name="installing-from-a-command-line"></a>L’installation à partir d’une ligne de commande
La section précédente fournit des conseils sur l’installation de l’agent de dépendance moniteur en utilisant les options par défaut.  Les sections suivantes fournissent des instructions pour l’installation de l’agent à partir d’une ligne de commande à l’aide des options personnalisées.

#### <a name="windows"></a>Windows
Utilisez les options du tableau ci-dessous pour effectuer l’installation à partir d’une ligne de commande. Pour afficher la liste de l’installation d’indicateurs exécuter le programme d’installation avec le / ? Marquer comme suit.

    ADM-Agent-Windows.exe /?

| Indicateur | Description |
|:--|:--|
| /S | Effectuer une installation en mode silencieux sans invite utilisateur. |

Fichiers de l’Agent de dépendance Windows sont placées dans *C:\Program Files\BlueStripe\Collector* par défaut.


#### <a name="linux"></a>Linux
Utilisez les options du tableau ci-dessous pour effectuer l’installation. Pour afficher la liste de l’installation d’indicateurs exécuter l’installation du programme grâce à la - indicateur comme suit.

    ADM-Agent-Linux64.bin -help

| Description de l’indicateur
|:--|:--|
| -d | Effectuer une installation en mode silencieux sans invite utilisateur. |
| --vérifier | Vérifie les autorisations et système d’exploitation, mais n’installe pas l’agent. |

Fichiers de l’Agent de dépendance sont placés dans les répertoires suivants.

| Fichiers | Emplacement |
|:--|:--|
| Fichiers de base | /usr/lib/bluestripe-collector |
| Fichiers journaux | /var/opt/Microsoft/Dependency-agent/log |
| Fichiers de configuration | /etc/opt/Microsoft/Dependency-agent/config |
| Service exécutables | /sbin/bluestripe-collector<br>/sbin/bluestripe-collector-Manager |
| Fichiers de stockage binaire | /var/opt/Microsoft/Dependency-agent/Storage |



## <a name="troubleshooting"></a>Résolution des problèmes
Si vous rencontrez des problèmes avec l’Analyseur de dépendance Application, vous pouvez collecter les informations de dépannage sur plusieurs composants en utilisant les informations suivantes.

### <a name="windows-agents"></a>Agents Windows

#### <a name="microsoft-dependency-agent"></a>Dépendance de Microsoft Agent
Pour générer des données de dépannage de l’Agent de dépendance, ouvrez une invite de commandes en tant qu’administrateur et exécuter le script CollectBluestripeData.vbs à l’aide de la commande suivante.  Vous pouvez ajouter la--indicateur d’aide pour afficher d’autres options.

    cd C:\Program Files\Bluestripe\Collector\scripts
    cscript CollectDependencyData.vbs

Le lot de données prise en charge est enregistré dans le répertoire % %userprofile% pour l’utilisateur actuel.  Vous pouvez utiliser le--fichier <filename> option permettant d’enregistrer à un autre emplacement.

#### <a name="microsoft-dependency-agent-management-pack-for-mma"></a>Dépendance de Microsoft Agent Management Pack pour MMA
Le Pack d’administration dépendance Agent s’exécute à l’intérieur de l’Agent de gestion de Microsoft.  Il reçoit les données de l’Agent de dépendance et la transfère au service cloud ADM.
  
Vérifiez que le pack d’administration est téléchargé en effectuant les étapes suivantes.

1.  Recherchez un fichier nommé Microsoft.IntelligencePacks.ApplicationDependencyMonitor.mp dans C:\Program Files\Microsoft surveillance Agent\Agent\Health Service State\Management modules.  
2.  Si le fichier n’est pas présent et que l’agent est connecté à un groupe d’administration SCOM, puis vérifiez qu’il a été importé dans SCOM en vérifiant Packs d’administration dans l’espace de travail de l’Administration de la Console d’opérations.

Le panneau de gestion ADM écrit des événements dans le journal des événements Operations Manager Windows.  Le journal peut être [recherché dans OMS](../log-analytics/log-analytics-log-searches.md) via la solution de journal système, dans lequel vous pouvez configurer les fichiers journaux à télécharger.  Si les événements de débogage sont activés, ils sont écrits dans le journal des événements, avec la source d’événements *AdmProxy*.

#### <a name="microsoft-monitoring-agent"></a>Agent de surveillance Microsoft
Pour recueillir des suivis de diagnostic, ouvrez une invite de commandes en tant qu’administrateur et exécutez les commandes suivantes : 

    cd \Program Files\Microsoft Monitoring Agent\Agent\Tools
    net stop healthservice 
    StartTracing.cmd ERR
    net start healthservice

Traces sont écrits sur c:\Windows\Logs\OpsMgrTrace.  Vous pouvez arrêter le suivi avec StopTracing.cmd.


### <a name="linux-agents"></a>Agents Linux

#### <a name="microsoft-dependency-agent"></a>Dépendance de Microsoft Agent
Pour générer des données de dépannage de l’Agent de dépendance, connectez-vous avec un compte doté de privilèges sudo ou racine et exécutez la commande suivante.  Vous pouvez ajouter la--indicateur d’aide pour afficher d’autres options.

    /usr/lib/bluestripe-collector/scripts/collect-dependency-agent-data.sh

Le lot de données prise en charge est enregistré dans /var/opt/microsoft/dependency-agent/log (si racine) sous répertoire d’installation de l’Agent ou au répertoire de base de l’utilisateur exécutant le script (si non racine).  Vous pouvez utiliser le--fichier <filename> option permettant d’enregistrer à un autre emplacement.

#### <a name="microsoft-dependency-agent-fluentd-plug-in-for-linux"></a>Dépendance de Microsoft Agent Fluentd plug-in pour Linux
Le plug-in de dépendance Agent Fluentd s’exécute à l’intérieur de l’Agent de Linux OMS.  Il reçoit les données de l’Agent de dépendance et la transfère au service cloud ADM.  

Les journaux sont écrits dans les deux fichiers suivants.

- /var/opt/Microsoft/omsagent/log/omsagent.log
- /var/log/messages

#### <a name="oms-agent-for-linux"></a>Agent OMS pour Linux
Une ressource de résolution des problèmes de connexion des serveurs Linux à OMS trouverez ici : [https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/docs/Troubleshooting.md](https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/docs/Troubleshooting.md) 

Les journaux de l’Agent OMS pour Linux se trouvent dans */var/opter/microsoft/omsagent/journal/*.  

Les journaux pour omsconfig (configuration de l’agent) se trouvent dans */var/opter/microsoft/omsconfig/journal/*.
 
Le journal des composants OMI et SCX qui fournissent des données des indicateurs de performance se trouvent dans */var/opter/omi/journal/* et */var/opt/microsoft/scx/log*.


## <a name="data-collection"></a>Collecte de données
Vous pouvez vous attendre chaque agent à transmettre à peu près 25 Mo par jour, selon la complexité des dépendances de votre système.  Données de dépendance ADM sont envoyées par chaque agent toutes les 15 secondes.  

L’Agent ADM consomme généralement 0,1 % de la mémoire système et 0,1 % du système du processeur.


## <a name="supported-operating-systems"></a>Systèmes d’exploitation pris en charge
Les sections suivantes répertorient les systèmes d’exploitation pris en charge pour l’Agent de dépendance.   architectures 32 bits ne sont pas prises en charge pour les systèmes d’exploitation.

### <a name="windows-server"></a>Windows Server
- Windows Server 2012 R2
- Windows Server 2012
- Windows Server 2008 R2 SP1

### <a name="windows-desktop"></a>Windows Desktop
- Remarque : Windows 10 n'est pas encore pris en charge
- Windows 8.1
- Windows 8
- Windows 7

### <a name="red-hat-enterprise-linux-centos-linux-and-oracle-linux-with-rhel-kernel"></a>Chapeau rouge Enterprise Linux, Linux CentOS et Linux Oracle (avec noyau RHEL)
- Uniquement par défaut et les versions de noyau Linux SMP sont prises en charge.
- Versions de noyau non standard, telles que l’extension d’adresse physique et Xen, ne sont pas pris en charge pour une distribution Linux. Par exemple, un système avec la chaîne de version de « 2.6.16.21-0.8-xen » n’est pas pris en charge.
- Noyaux personnalisés, y compris les recompilations de grains standards, ne sont pas prises en charge
- Noyau CentOS Plus n’est pas pris en charge.
- Oracle Unbreakable noyau (UEK) est traité dans une autre section ci-dessous.


#### <a name="red-hat-linux-7"></a>Chapeau rouge Linux 7
| Version du système d’exploitation | Version du noyau |
|:--|:--|
| 7.0 | 3.10.0-123 |
| 7.1 | 3.10.0-229 |
| 7.2 | 3.10.0-327 |

#### <a name="red-hat-linux-6"></a>Chapeau rouge Linux 6
| Version du système d’exploitation | Version du noyau |
|:--|:--|
| 6.0 | 2.6.32-71 |
| 6.1 | 2.6.32-131 |
| 6.2 | 2.6.32-220 |
| 6.3 | 2.6.32-279 |
| 6.4 | 2.6.32-358 |
| 6.5 | 2.6.32-431 |
| 6.6 | 2.6.32-504 |
| 6,7 | 2.6.32-573 |
| 6.8 | 2.6.32-642 |

#### <a name="red-hat-linux-5"></a>Chapeau rouge Linux 5
| Version du système d’exploitation | Version du noyau |
|:--|:--|
| 5.8 | 2.6.18-308 |
| 5,9 | 2.6.18-348 |
| 5.10 | 2.6.18-371 |
| 5.11 | 2.6.18-398<br>2.6.18-400<br>2.6.18-402<br>2.6.18-404<br>2.6.18-406<br>2.6.18-407<br>2.6.18-408<br>2.6.18-409<br>2.6.18-410<br>2.6.18-411 |

#### <a name="oracle-enterprise-linux-w-unbreakable-kernel-uek"></a>Oracle Enterprise Linux avec noyau Unbreakable (UEK)

#### <a name="oracle-linux-6"></a>Oracle Linux 6
| Version du système d’exploitation | Version du noyau
|:--|:--|
| 6.2 | 2.6.32-300 Oracle (UEK R1) |
| 6.3 | 2.6.39-200 Oracle (UEK R2) |
| 6.4 | 2.6.39-400 Oracle (UEK R2) |
| 6.5 | 2.6.39-400 Oracle (UEK i386 R2) |
| 6.6 | 2.6.39-400 Oracle (UEK i386 R2) |

#### <a name="oracle-linux-5"></a>Oracle Linux 5

| Version du système d’exploitation | Version du noyau
|:--|:--|
| 5.8 | 2.6.32-300 Oracle (UEK R1) |
| 5,9 | 2.6.39-300 Oracle (UEK R2) |
| 5.10 | 2.6.39-400 Oracle (UEK R2) |
| 5.11 | 2.6.39-400 Oracle (UEK R2) |

#### <a name="suse-linux-enterprise-server"></a>SUSE Linux Enterprise Server

#### <a name="suse-linux-11"></a>SUSE Linux 11
| Version du système d’exploitation | Version du noyau
|:--|:--|
| 11 | 2.6.27 |
| 11 SP1 | 2.6.32 |
| 11 SP2 | 3.0.13 |
| 11 SP3 | 3.0.76 |
| SERVICE PACK 4 11 | 3.0.101 |

#### <a name="suse-linux-10"></a>SUSE Linux 10
| Version du système d’exploitation | Version du noyau
|:--|:--|
| SERVICE PACK 4 10 | 2.6.16.60 |

## <a name="diagnostic-and-usage-data"></a>Diagnostics et l’utilisation des données
Microsoft recueille automatiquement les données d’utilisation et de performances grâce à votre utilisation du service Moniteur de dépendance d’Application. Microsoft utilise ces données pour fournir et améliorer la qualité, la sécurité et l’intégrité du service Moniteur de dépendance d’Application. Données inclut des informations sur la configuration de votre logiciel de système d’exploitation et version et inclut également adresse IP, nom DNS et nom poste de travail afin de fournir des fonctionnalités de dépannage précises et efficaces. Nous ne recueillons pas les noms, adresses ou autres informations de contact.

Pour plus d’informations sur la collecte de données et l’utilisation, consultez la [Déclaration de confidentialité Microsoft Online Services](https://go.microsoft.com/fwlink/?LinkId=512132).



## <a name="next-steps"></a>Étapes suivantes
- Découvrez comment [utiliser l’Analyseur de dépendance Application](operations-management-suite-application-dependency-monitor.md) une fois qu’il a été déployé et configuré.

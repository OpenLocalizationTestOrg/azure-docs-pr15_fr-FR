<properties
   pageTitle="Configuration requise StorSimple | Microsoft Azure"
   description="Décrit les meilleures pratiques pour une solution Microsoft Azure StorSimple logiciels, mise en réseau et exigences de haute disponibilité."
   services="storsimple"
   documentationCenter="NA"
   authors="alkohli"
   manager="carmonm"
   editor=""/>

<tags
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD"
   ms.date="08/31/2016"
   ms.author="alkohli"/>

# <a name="storsimple-software-high-availability-and-networking-requirements"></a>Logiciel StorSimple, disponibilité et configuration requise de mise en réseau

## <a name="overview"></a>Vue d’ensemble

Bienvenue dans Microsoft Azure StorSimple. Cet article décrit la configuration requise importantes et des pratiques recommandées pour votre appareil StorSimple et pour les clients de stockage l’accès au périphérique. Nous vous recommandons de consulter les informations avec soin avant de déployer votre système de StorSimple, puis référer comme bon vous semble pendant le déploiement et les opérations suivantes.

La configuration système requise est les suivantes :

- **Configuration logicielle requise pour les clients de stockage** : décrit les systèmes d’exploitation pris en charge et les autres conditions requises pour les systèmes d’exploitation.
- **Configuration requise pour l’appareil StorSimple de mise en réseau** - fournit des informations sur les ports qui doivent être ouverts dans votre pare-feu afin de permettre iSCSI, cloud ou le trafic de gestion.
- **Configuration requise de disponibilité pour StorSimple** - décrit les exigences de haute disponibilité et des pratiques recommandées pour votre ordinateur hôte et l’appareil StorSimple. 


## <a name="software-requirements-for-storage-clients"></a>Configuration logicielle requise pour les clients de stockage

La configuration logicielle requise suivants est pour les clients de stockage qui accèdent à votre appareil StorSimple.

| Systèmes d’exploitation pris en charge | Version requise | Configuration requise supplémentaires/notes |
| --------------------------- | ---------------- | ------------- |
| Windows Server              | 2008 R2 SP1, 2012, 2012R2 |Volumes iSCSI StorSimple sont pris en charge pour une utilisation uniquement les types de disque de Windows suivants :<ul><li>Volume simple sur le disque de base</li><li>Volume simple et en miroir sur disque dynamique</li></ul>Windows Server 2012 contrôle de l’approvisionnement et fonctionnalités ODX sont pris en charge si vous utilisez un volume iSCSI StorSimple.<br><br>StorSimple pouvez créer exactement généré et entièrement généré volumes. Il ne peut pas créer des volumes partiellement mis en service.<br><br>Remise en forme un volume exactement généré peut prendre beaucoup de temps. Nous vous recommandons de supprimer le volume, puis en créant une nouvelle visualisation au lieu de remise en forme. Si vous avez toujours préfèrent reformater un volume :<ul><li>Exécuter la commande suivante avant la reformater pour éviter des délais de récupération espace : <br>`fsutil behavior set disabledeletenotify 1`</br></li><li>Après que la mise en forme est terminée, utilisez la commande suivante pour réactiver la fonctionnalité Récupération de l’espace :<br>`fsutil behavior set disabledeletenotify 0`</br></li><li>Appliquer le correctif Windows Server 2012 comme décrit dans la [base de connaissances 2878635](https://support.microsoft.com/kb/2870270) sur votre ordinateur Windows Server.</li></ul></li></ul></ul> Si vous configurez le Gestionnaire d’instantanés StorSimple ou StorSimple carte pour SharePoint, accédez à la [configuration logicielle requise pour les composants facultatifs](#software-requirements-for-optional-components).|
| GRAPPE de serveurs | 5.5 et 6.0 | Prise en charge avec VMWare vSphere comme iSCSI client. Fonctionnalité de bloc de VAAI est pris en charge avec VMware vSphere sur les appareils StorSimple.
| Linux RHEL/CentOS | 5, 6 et 7 | Prise en charge pour les clients iSCSI Linux avec les versions initiateur ouvrir iSCSI 5, 6 et 7. |
| Linux | SUSE Linux 11 | |
 > [AZURE.NOTE] IBM AIX n’est actuellement pas pris en charge avec StorSimple.

## <a name="software-requirements-for-optional-components"></a>Configuration logicielle requise pour les composants facultatifs

La configuration logicielle requise suivantes s’appliquent aux composants StorSimple facultatifs (Gestionnaire d’instantanés StorSimple et carte StorSimple pour SharePoint).

| Composant | Plateforme hôte | Configuration requise supplémentaires/notes |
| --------------------------- | ---------------- | ------------- |
| Gestionnaire d’instantanés StorSimple | Windows Server 2008 R2 SP1, 2012, 2012R2 | Utilisation du Gestionnaire d’instantané StorSimple sur Windows Server est requise pour la sauvegarde/restauration des disques dynamiques en miroir et pour toutes les sauvegardes cohérentes avec les applications.<br> Gestionnaire d’instantanés StorSimple est pris en charge uniquement sur Windows Server 2008 R2 SP1 (64 bits), Windows 2012 R2 et Windows Server 2012.<ul><li>Si vous utilisez la fenêtre Server 2012, vous devez installer .NET 3.5 – 4.5 avant d’installer le Gestionnaire d’instantanés StorSimple.</li><li>Si vous utilisez Windows Server 2008 R2 SP1, vous devez installer Windows Management Framework 3.0 avant d’installer le Gestionnaire d’instantanés StorSimple.</li></ul> |
| Carte StorSimple pour SharePoint | Windows Server 2008 R2 SP1, 2012, 2012R2 |<ul><li>StorSimple carte pour SharePoint est uniquement prise en charge dans SharePoint 2010 et SharePoint 2013.</li><li>RBS nécessite SQL Server Enterprise Edition, version 2008 R2 ou 2012.</li></ul>|

## <a name="networking-requirements-for-your-storsimple-device"></a>Configuration requise pour votre appareil StorSimple de mise en réseau

Votre appareil StorSimple est un appareil verrouillé. Toutefois, ports doivent être ouverts dans votre pare-feu afin de permettre iSCSI, cloud et le trafic de gestion. Le tableau suivant répertorie les ports qui doivent être ouverts dans votre pare-feu. Dans ce tableau *en* *entrant* ou fait référence à la direction à partir de laquelle demandes des clients accèdent à votre périphérique. *Absence* ou *sortant* fait référence à la direction dans laquelle votre appareil StorSimple envoie des données à l’extérieur, au-delà du déploiement : par exemple, sortant à Internet.

| Port n°<sup>1,2</sup> | Ou l’arrière | Étendue de port | Obligatoire | Notes |
|------------------------|-----------|------------|----------|-------|
|TCP 80 (HTTP)<sup>3</sup>|  Déconnecter |  RÉSEAU ÉTENDU | N° |<ul><li>Port sortant est utilisé pour l’accès Internet pour récupérer des mises à jour.</li><li>Le proxy web sortante est configurable par l’utilisateur.</li><li>Pour autoriser les mises à jour système, ce port doit également être ouvert pour le contrôleur fixé adresses IP.</li></ul> |
|TCP 443 (HTTPS)<sup>3</sup>| Déconnecter | RÉSEAU ÉTENDU | Oui |<ul><li>Port de sortie est utilisé pour l’accès aux données dans le cloud.</li><li>Le proxy web sortante est configurable par l’utilisateur.</li><li>Pour autoriser les mises à jour système, ce port doit également être ouvert pour le contrôleur fixé adresses IP.</li><li>Ce port est également utilisé sur les deux contrôleurs pour le nettoyage.</li></ul>|
|UDP 53 (DNS) | Déconnecter | RÉSEAU ÉTENDU | Dans certains cas ; Voir notes. |Ce port est nécessaire uniquement si vous utilisez un serveur DNS basées sur Internet. |
| UDP 123 (NTP) | Déconnecter | RÉSEAU ÉTENDU | Dans certains cas ; Voir notes. |Ce port est nécessaire uniquement si vous utilisez un serveur NTP basées sur Internet. |
| TCP 9354 | Déconnecter | RÉSEAU ÉTENDU | Oui |Le port sortant est utilisé par le périphérique StorSimple pour communiquer avec le service Manager StorSimple. |
| 3260 (iSCSI) | Dans | RÉSEAU LOCAL | N° | Ce port est utilisé pour accéder aux données via iSCSI.|
| 5985 | Dans | RÉSEAU LOCAL | N° | Port d’entrée est utilisé par le Gestionnaire d’instantané StorSimple pour communiquer avec le périphérique StorSimple.<br>Ce port est également utilisé lorsque vous à distance à se connectez à Windows PowerShell pour StorSimple sur HTTP. |
| 5986 | Dans | RÉSEAU LOCAL | N° | Ce port est utilisé lorsque vous à distance se connectez à Windows PowerShell pour StorSimple sur HTTPS. |

<sup>1</sup> aucun ports entrants ne doivent être ouverts sur l’Internet public.

<sup>2</sup> si plusieurs ports comporte une configuration de passerelle, l’ordre de trafic routé sortant sera déterminé selon l’ordre de routage port décrit dans le [Port routage](#routing-metric), ci-dessous.

<sup>3</sup> le contrôleur fixé adresses IP sur votre appareil StorSimple doit être en mesure de vous connecter à Internet et prenant. Les adresses IP fixes sont utilisées pour traiter les mises à jour à l’appareil. Si les contrôleurs appareil ne peut pas se connecter à Internet via les adresses IP fixe, il se peut que vous ne pourrez pas mettre à jour votre appareil StorSimple.

> [AZURE.IMPORTANT] Assurez-vous que le pare-feu ne pas modifier ou déchiffrer tout le trafic SSL entre le périphérique de StorSimple et Azure.

### <a name="url-patterns-for-firewall-rules"></a>Modèles d’URL pour les règles de pare-feu

Administrateurs réseau peuvent souvent configurer des règles de pare-feu avancées basées sur les types d’URL pour filtrer entrant et le trafic sortant. Votre appareil StorSimple et le service Manager StorSimple dépendent d’autres applications Microsoft tels que Bus des services Azure, un contrôle d’accès Azure Active Directory, comptes de stockage et serveurs Microsoft Update. Les modèles d’URL associés à ces applications peuvent servir à configurer des règles de pare-feu. Il est important de comprendre que les modèles d’URL associés à ces applications peuvent changer. Cette opération à son tour nécessite l’administrateur de réseau surveiller et mettre à jour des règles de pare-feu pour votre StorSimple en tant qu’et lorsque cela est nécessaire.

Nous vous conseillons de définir vos règles de pare-feu pour le trafic sortant, en fonction de StorSimple fixé recourir largement adresses IP, dans la plupart des cas. Toutefois, vous pouvez utiliser les informations ci-dessous pour définir des règles de pare-feu avancé qui sont nécessaires pour créer des environnements sécurisés.

> [AZURE.NOTE] Le périphérique adresses IP (source) doit toujours être défini à toutes les interfaces réseau activée. La destination adresses IP doit être défini sur les [plages d’adresses IP Azure centre de données](https://www.microsoft.com/en-us/download/confirmation.aspx?id=41653).

#### <a name="url-patterns-for-azure-portal"></a>Modèles d’URL de portail Azure
| Modèle d’URL                                                      | Fonctionnalités des composants                                           | Adresses IP appareil                           |
|------------------------------------------------------------------|---------------------------------------------------------------|-----------------------------------------|
| `https://*.storsimple.windowsazure.com/*`<br>`https://*.accesscontrol.windows.net/*`<br>`https://*.servicebus.windows.net/*`   | Service Manager StorSimple<br>Service de contrôle d’accès<br>Bus des services Azure| Interfaces réseau activé pour le cloud        |
|`https://*.backup.windowsazure.com`|Inscription de l’appareil| DONNÉES 0|
|`http://crl.microsoft.com/pki/*`<br>`http://www.microsoft.com/pki/*`|Révocation des certificats |Interfaces réseau activé pour le cloud |
| `https://*.core.windows.net/*` <br>`https://*.data.microsoft.com`<br>`http://*.msftncsi.com` | Comptes de stockage Azure et de surveillance | Interfaces réseau activé pour le cloud        |
| `http://*.windowsupdate.microsoft.com`<br>`https://*.windowsupdate.microsoft.com`<br>`http://*.update.microsoft.com`<br> `https://*.update.microsoft.com`<br>`http://*.windowsupdate.com`<br>`http://download.microsoft.com`<br>`http://wustat.windows.com`<br>`http://ntservicepack.microsoft.com`| Serveurs Microsoft Update<br>                             | Contrôleur fixe adresses IP uniquement               |
| `http://*.deploy.akamaitechnologies.com`                         |CDN Akamai |Contrôleur fixe adresses IP uniquement   |
| `https://*.partners.extranet.microsoft.com/*`                    | Prise en charge                                                  | Interfaces réseau activé pour le cloud        |

#### <a name="url-patterns-for-azure-government-portal"></a>Modèles d’URL de portail pour le gouvernement Azure
| Modèle d’URL                                                      | Fonctionnalités des composants                                           | Adresses IP appareil                           |
|------------------------------------------------------------------|---------------------------------------------------------------|-----------------------------------------|
| `https://*.storsimple.windowsazure.us/*`<br>`https://*.accesscontrol.usgovcloudapi.net/*`<br>`https://*.servicebus.usgovcloudapi.net/*`   | Service Manager StorSimple<br>Service de contrôle d’accès<br>Bus des services Azure| Interfaces réseau activé pour le cloud        |
|`https://*.backup.windowsazure.us`|Inscription de l’appareil| DONNÉES 0|
|`http://crl.microsoft.com/pki/*`<br>`http://www.microsoft.com/pki/*`|Révocation des certificats |Interfaces réseau activé pour le cloud |
| `https://*.core.usgovcloudapi.net/*` <br>`https://*.data.microsoft.com`<br>`http://*.msftncsi.com` | Comptes de stockage Azure et de surveillance | Interfaces réseau activé pour le cloud        |
| `http://*.windowsupdate.microsoft.com`<br>`https://*.windowsupdate.microsoft.com`<br>`http://*.update.microsoft.com`<br> `https://*.update.microsoft.com`<br>`http://*.windowsupdate.com`<br>`http://download.microsoft.com`<br>`http://wustat.windows.com`<br>`http://ntservicepack.microsoft.com`| Serveurs Microsoft Update<br>                             | Contrôleur fixe adresses IP uniquement               |
| `http://*.deploy.akamaitechnologies.com`                         |CDN Akamai |Contrôleur fixe adresses IP uniquement   |
| `https://*.partners.extranet.microsoft.com/*`                    | Prise en charge                                                  | Interfaces réseau activé pour le cloud        |

### <a name="routing-metric"></a>Métrique de routage

Une métrique routage est associée avec les interfaces et la passerelle qui acheminer les données vers les réseaux spécifiés. Métrique de routage est utilisé par le protocole de routage pour calculer la meilleure option consiste à une destination donnée, si elle apprend que les chemins multiples existent pour la même cible. La plus basse le métrique de routage, plus la préférence.

Dans le contexte de StorSimple, si plusieurs passerelles et interfaces réseau sont configurés pour le trafic du canal, les métriques de l’itinéraire soient transférés en jeu pour déterminer l’ordre relatif dans lequel les interfaces va obtenir utilisés. Les mesures de routage ne peut pas être modifiés par l’utilisateur. Vous pouvez toutefois utiliser les `Get-HcsRoutingTable` applet de commande pour imprimer le tableau routage (et indicateurs) sur votre appareil StorSimple. Plus d’informations sur l’applet de commande Get-HcsRoutingTable dans [StorSimple de résolution des problèmes de déploiement](storsimple-troubleshoot-deployment.md).

Les algorithmes métriques routage sont différentes selon la version du logiciel s’exécutant sur votre appareil StorSimple.

**Versions antérieures à la mise à jour 1**

Cela inclut les versions de logiciel avant la mise à jour 1 tels que la disponibilité générale, 0,1, 0,2 ou 0,3 version. La commande basée sur métriques de l’itinéraire est la suivante :

   *Dernière configuré interface de réseau solution 10 > interface réseau autre solution 10 > configuré en dernier 1 interface de réseau solution > interface réseau autre solution 1*


**Versions en commençant à partir de la mise à jour 1 et avant la mise à jour 2**

Cela inclut les versions des logiciels tels que 1, 1.1 ou 1.2. La commande basée sur métriques de l’itinéraire est déterminée comme suit :

   *DONNÉES 0 > configuré en dernier interface de réseau solution 10 > interface réseau autre solution 10 > configuré en dernier 1 interface de réseau solution > interface réseau autre solution 1*

   Mise à jour 1, la métrique de routage de données 0 est rendue la plus basse ; Par conséquent, tout le trafic cloud est routé via données 0. Prenez note de ce s’il existe plus d’une interface réseau activé pour le cloud sur votre appareil StorSimple.


**Versions à partir de la mise à jour 2**

Mise à jour 2 comporte plusieurs améliorations en matière de réseau et les mesures de routage a changé. Le comportement peut s’expliquer comme suit.

- Une série de valeurs prédéterminés a été octroyée aux interfaces réseau.   

- Prenons un exemple d’une table ci-dessous avec valeurs affectées pour les diverses interfaces réseau lorsqu’ils sont cloud-activés ou cloud est désactivée, mais avec une passerelle configurée. Notez les valeurs affectés ici sont uniquement les valeurs d’exemple.


  	| Interface réseau | Activé pour le cloud | Cloud avec la passerelle est désactivée |
  	|-----|---------------|---------------------------|
  	| Données 0  | 1            | -                        |
  	| Données 1  | 2            | 20                       |
  	| Données 2  | 3            | 30                       |
  	| Données 3  | 4            | 40                       |
  	| Données 4  | 5            | 50                       |
  	| Données 5  | 6            | 60                       |


- L’ordre dans lequel le trafic cloud sera routé via les interfaces réseau est la suivante :

    *Données 0 > données 1 > Date 2 > données 3 > données 4 > données 5*

    Cela peut s’expliquer par l’exemple suivant.

    Envisagez d’un appareil StorSimple avec deux interfaces réseau activé pour le cloud, données 0 et 5 de données. Données 1 à 4 de données sont est désactivée cloud mais ont une passerelle configurée. L’ordre dans lequel le trafic doivent être routé pour cet appareil sera :

    *0 (1) de données > données 5 (6) > 1 (20) de données > données 2 (30) > données 3 (40) > données 4 (50)*

    *Lorsque les nombres entre parenthèses indiquent les métriques de routage respectifs.*

    En cas d’échec données 0, le trafic cloud est routé à 5 de données. Étant donné qu’une passerelle est configurée sur tous les autres réseau, si les données 0 et 5 données étaient échec, le trafic cloud doit être placé à 1 de données.


- Si une interface réseau activé pour le cloud échoue, puis sont 3 tentatives avec un délai de 30 secondes pour vous connecter à l’interface. Si toutes les tentatives échouent, le trafic est routé suivante disponible activé pour le cloud interface tel que déterminé par la table de routage. Si fail interfaces tout le réseau activé pour le cloud, puis le périphérique bascule vers à l’autre contrôleur (sans redémarrage dans le cas présent).

- En cas de panne VIP pour une interface réseau iSCSI activé, il sera 3 tentatives avec un retard de 2 secondes. Ce comportement a pas changé par rapport aux versions précédentes. Si toutes les interfaces réseau iSCSI échouent, un basculement contrôleur produira (accompagnés par un redémarrage).


- Une alerte est également déclenchée sur votre appareil StorSimple lorsqu’il y a une défaillance VIP. Pour plus d’informations, accédez à [aide-mémoire alerte](storsimple-manage-alerts.md).

- En termes de tentatives, iSCSI ont priorité sur le cloud.

    Prenons l’exemple suivant : StorSimple A appareil comporte deux interfaces réseau activé, données 0 et 1 de données. Données 0 sont activé pour le cloud alors que les données 1 est les deux cloud et activé iSCSI. Aucune autre interface réseau sur cet appareil n’est activés pour le cloud ou iSCSI.

    Si 1 de données échoue, étant donné qu’il est la dernière interface réseau iSCSI, ainsi, un basculement contrôleur aux données 1 sur l’autre contrôleur.


### <a name="networking-best-practices"></a>Meilleures pratiques de mise en réseau

Outre les exigences de réseau ci-dessus, pour optimiser les performances de votre solution StorSimple, veuillez respecter les meilleures pratiques suivantes :

- Assurez-vous que votre appareil StorSimple possède une bande passante Mbps 40 dédiée (ou plus) disponibles à tout moment. La bande passante ne doit pas être partagée (ou allocation doit être garantie au moyen de stratégies de qualité de service) avec d’autres applications.

- Vérifiez la connectivité réseau à Internet est disponible à tout moment. Connexions Internet interruptions ou non fiables pour les appareils, y compris sans connexion à Internet que ce soit, seront traduit par une configuration non prises en charge.

- Isoler le trafic iSCSI et cloud par dédiés interfaces réseau sur votre appareil pour l’accès iSCSI et cloud. Pour plus d’informations, voir comment [Modifier les interfaces réseau](storsimple-modify-device-config.md#modify-network-interfaces) sur votre appareil StorSimple.

- N’utilisez pas une configuration lien agrégation contrôle Protocol (LACP) pour les interfaces de votre réseau. Il s’agit d’une configuration non prises en charge.


## <a name="high-availability-requirements-for-storsimple"></a>Configuration requise de disponibilité pour StorSimple

La plate-forme matérielle qui est incluse avec la solution StorSimple inclut des fonctionnalités de disponibilité et la fiabilité qui fournissent une base pour une infrastructure de stockage hautement disponible, tolérance dans votre centre de données. Toutefois, il existe des besoins et des pratiques recommandées qui doivent être conformes pour garantir la disponibilité de votre solution StorSimple. Avant de déployer StorSimple, attentivement les conditions suivantes et des pratiques recommandées pour les appareils StorSimple et des ordinateurs hôte connecté.

Pour plus d’informations sur la surveillance et la mise à jour les composants matériels de votre appareil StorSimple, allez à [utiliser le service Manager StorSimple pour surveiller l’état et des composants matériels](storsimple-monitor-hardware-status.md) et [remplacement des composants matériels StorSimple](storsimple-hardware-component-replacement.md).

### <a name="high-availability-requirements-and-procedures-for-your-storsimple-device"></a>Exigences de haute disponibilité et les procédures de votre périphérique StorSimple

Passez en revue les informations suivantes avec soin pour vous assurer que la disponibilité de votre périphérique StorSimple.

#### <a name="pcms"></a>PCMs

StorSimple périphériques incluent power redondante, chaud et refroidissement (PCM pour) les modules. Chaque PCM a une capacité suffisante pour fournir le service pour le châssis entière. Pour optimiser la disponibilité, les deux PCMs doivent être installés.

- Se connecter à votre PCMs aux sources d’alimentation différentes pour offrir une disponibilité si une source d’alimentation échoue.
- Si un PCM échoue, demander un remplacement immédiatement.
- Supprimer un échec PCM uniquement lorsque vous avez le remplacement et que vous êtes prêt à l’installer.
- Ne supprimez pas simultanément les deux PCMs. Le module PCM inclut le module de batterie de secours. Suppression des PCMs entraînera un arrêt sans protection de batterie et l’état du périphérique n’est pas enregistrée. Pour plus d’informations sur la batterie, accédez à [tenir à jour le module de batterie de secours](storsimple-battery-replacement.md#maintain-the-backup-battery-module).

#### <a name="controller-modules"></a>Modules contrôleur

Appareils StorSimple incluent contrôleur redondantes, chaud modules. Les modules contrôleur fonctionnent en mode actif/passif. À un moment donné, un module contrôleur est actif et fournit des services, tandis que l’autre module contrôleur est passif. Le module contrôleur passif est sous tension et devient opérationnel si le module contrôleur active échoue ou est supprimé. Chaque module contrôleur a une capacité suffisante pour fournir le service pour le châssis entière. Les deux modules contrôleur doivent être installés pour assurer la disponibilité élevée.

- Assurez-vous que les deux modules contrôleur sont installés à tout moment.

- Si un module contrôleur ne fonctionne pas, demander un remplacement immédiatement.

- Supprimer un module contrôleur échec uniquement lorsque vous avez le remplacement et que vous êtes prêt à l’installer. Suppression d’un module pour une période prolongée détermineront l’air et donc le refroidissement du système.

- Assurez-vous que les connexions réseau pour les deux modules contrôleur sont identiques, et les interfaces réseau connecté ont une configuration réseau identiques.

- Si un module contrôleur échoue ou doit être remplacé, assurez-vous que l’autre module contrôleur se trouve dans un état actif avant de remplacer le module contrôleur a échoué. Pour vérifier qu’un contrôleur est actif, accédez à [identifier le contrôleur actif sur votre appareil](storsimple-controller-replacement.md#identify-the-active-controller-on-your-device).

- Ne supprimez pas les deux modules contrôleur en même temps. Si un basculement contrôleur est en cours, ne pas arrêter le module contrôleur de secours ou supprimer du châssis.

- Après un basculement contrôleur, patientez au moins 5 minutes avant de supprimer des modules contrôleur.

#### <a name="network-interfaces"></a>Interfaces réseau

StorSimple contrôleur modules aux périphériques chaque ont quatre de 1 et deux 10 interfaces réseau Gigabit Ethernet.

- Assurez-vous que les connexions réseau pour les deux modules contrôleur sont identiques, et le réseau interfaces que les interfaces de module contrôleur sont connectés pour que la configuration d’un réseau identiques.

- Dès que possible, déployer des connexions réseau dans différents commutateurs pour assurer la disponibilité du service en cas de panne de périphérique réseau.

- Lorsque vous déconnectez uniquement ou l’interface compatibles avec iSCSI restante dernière (avec les adresses IP affectée), désactivez l’interface tout d’abord, puis débranchez que les câbles. Si l’interface est débranchée tout d’abord, elle provoquera le contrôleur actif basculer vers le contrôleur passif. Si le contrôleur passif a également ses interfaces correspondantes débranchés, à la fois les contrôleurs redémarre plusieurs fois avant de s’installer sur un seul contrôleur.

- Se connecter au moins deux interfaces de données au réseau de chaque module contrôleur.

- Si vous avez activé les deux interfaces solution 10, déployer ceux entre différents commutateurs.

- Si possible, utilisez o sur des serveurs pour vous assurer que les serveurs tolèrent un lien, de réseau ou Échec de l’interface.

Pour plus d’informations sur les réseaux votre appareil de disponibilité et les performances, accédez à [installer votre appareil StorSimple 8100](storsimple-8100-hardware-installation.md#cable-your-storsimple-8100-device) ou [votre appareil StorSimple 8600](storsimple-8600-hardware-installation.md#cable-your-storsimple-8600-device).

#### <a name="ssds-and-hdds"></a>SSDs et des disques durs

Appareils StorSimple incluent état unie disques (SSDs) et disques durs (disques durs) qui sont protégés à l’aide en miroir espaces. Utilisation d’espaces en miroir garantit que l’appareil est en mesure de tolérer l’échec d’une ou plusieurs SSDs ou disques durs.

- Assurez-vous que tous les modules SSD et du disque dur sont installés.

- Si un SSD ou le disque dur échoue, demander un remplacement immédiatement.

- Si un SSD ou un disque dur échoue ou doit être remplacé, assurez-vous que vous supprimez uniquement les SSD ou le disque dur qui doit être remplacé.

- Ne supprimez pas plusieurs SSD ou disque dur à partir du système à tout moment dans le temps.
Perte de données potentielle et une défaillance système peut entraîner une défaillance de disques 2 ou plus de certain type (disque dur, SSD) ou consécutives dans un court délai. Dans ce cas, [contactez le support technique Microsoft](storsimple-contact-microsoft-support.md) pour obtenir une assistance.

- En cas de remplacement, surveiller l' **État du matériel** dans la page de **Maintenance** pour les lecteurs de disques durs les SSDs. Un état avec une coche verte indique que les disques sont exact ou OK, alors que pointez sur un point d’exclamation rouge indique qu’un échec SSD ou disque dur.

- Nous vous recommandons de configurer des instantanés cloud pour tous les volumes dont vous avez besoin pour protéger en cas d’échec système.

#### <a name="ebod-enclosure"></a>Encadrement EBOD

Modèle d’appareil StorSimple 8600 inclut un encadrement étendu Bunch de disques (EBOD) en plus du boîtier principal. Un EBOD contient EBOD contrôleurs et disques durs (disques durs) qui sont protégés à l’aide en miroir espaces. Utilisation d’espaces en miroir garantit que l’appareil est en mesure de tolérer l’échec d’un ou plusieurs disques durs. Le boîtier EBOD est connecté à l’encadrement primaire via câbles sa redondants.

- Assurez-vous que les deux modules contrôleur du boîtier EBOD, à la fois les associations de sécurité câbles et tous les lecteurs de disques durs soient installés à tout moment.

- Si un module de contrôleur d’encadrement EBOD ne fonctionne pas, demander un remplacement immédiatement.

- Si un module de contrôleur d’encadrement EBOD ne fonctionne pas, vérifiez que l’autre module contrôleur est actif avant de remplacer le module défectueux. Pour vérifier qu’un contrôleur est actif, accédez à [identifier le contrôleur actif sur votre appareil](storsimple-controller-replacement.md#identify-the-active-controller-on-your-device).

- Pendant un remplacement des modules EBOD contrôleur, en permanence surveiller l’état du composant dans le service Manager StorSimple en accédant à la **Maintenance** > **état du matériel**.

- Si un câble sa échoue ou doit être remplacé (Support Microsoft doivent être impliqué pour rendre une telle détermination), assurez-vous que vous supprimez uniquement le câble associations de sécurité qui doit être remplacé.

- Ne pas simultanément supprimez les deux câbles associations de sécurité du système à tout moment dans le temps.

### <a name="high-availability-recommendations-for-your-host-computers"></a>Recommandations de disponibilité pour vos ordinateurs hôte

Lire attentivement les meilleures pratiques suivantes pour vous assurer que la disponibilité d’hôtes connectés à votre appareil StorSimple.

- Configurer le StorSimple avec les [configurations de cluster deux nœuds fichier server][1]. En supprimant des points de panne et la création de redondance sur le côté hôte, toute la solution devient hautement disponible.

- Utilisez Actions (CA) disponibles en permanence disponibles avec Windows Server 2012 (PME 3.0) de disponibilité pendant le basculement du contrôleur de stockage de. Pour plus d’informations sur la configuration des clusters de serveur de fichier et actions disponibles en permanence avec Windows Server 2012, consultez cette [vidéo de démonstration](http://channel9.msdn.com/Events/IT-Camps/IT-Camps-On-Demand-Windows-Server-2012/DEMO-Continuously-Available-File-Shares).

## <a name="next-steps"></a>Étapes suivantes

- [En savoir plus sur les limites de système StorSimple](storsimple-limits.md).
- [Découvrez comment déployer votre solution StorSimple](storsimple-deployment-walkthrough-u2.md).

<!--Reference links-->
[1]: https://technet.microsoft.com/library/cc731844(v=WS.10).aspx

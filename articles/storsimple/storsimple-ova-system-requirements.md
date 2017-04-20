<properties
   pageTitle="Configuration requise tableau virtuel StorSimple"
   description="En savoir plus sur les logiciels et la mise en réseau configuration requise pour votre tableau virtuel StorSimple"
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
   ms.workload="NA"
   ms.date="10/17/2016"
   ms.author="alkohli"/>

# <a name="storsimple-virtual-array-system-requirements"></a>Configuration requise tableau virtuel StorSimple

## <a name="overview"></a>Vue d’ensemble

Cet article décrit la configuration requise importantes pour votre tableau virtuel Microsoft Azure StorSimple (également appelé StorSimple locaux périphérique virtuel ou StorSimple virtuel) et pour les clients de stockage accès au tableau. Nous vous recommandons de consulter les informations avec soin avant de déployer votre système de StorSimple, puis référer comme bon vous semble pendant le déploiement et les opérations suivantes.

La configuration système requise est les suivantes :

-   **Configuration logicielle requise pour les clients de stockage** : décrit les plateformes virtualisation pris en charge, les navigateurs web, initiateurs, clients PME, exigences minimale périphérique virtuel et les autres conditions requises pour les systèmes d’exploitation.

-   **Configuration requise pour l’appareil StorSimple de mise en réseau** - fournit des informations sur les ports qui doivent être ouverts dans votre pare-feu afin de permettre iSCSI, cloud ou le trafic de gestion.

Les informations de configuration requise du système StorSimple publiées dans cet article s’applique uniquement aux tableaux virtuel StorSimple.

- Pour les périphériques 8000 série, accédez à la [configuration système requise pour votre appareil série 8000 StorSimple](storsimple-system-requirements.md).
 
- Pour les appareils série 7000, accédez à [configuration système requise pour votre appareil StorSimple 5000-7000 série](http://onlinehelp.storsimple.com/1_StorSimple_System_Requirements).


## <a name="software-requirements"></a>Configuration logicielle requise

La configuration logicielle requise pour inclure les informations sur les navigateurs web pris en charge, versions des PME, plates-formes de virtualisation et la configuration requise minimale périphérique virtuel.

### <a name="supported-virtualization-platforms"></a>Plateformes virtualisation pris en charge


| **Hyperviseur** | **Version**                          |
|----------------|--------------------------------------|
| Hyper-V        | Windows Server 2008 R2 SP1 ou version ultérieure |
| VMware ESXi    | 5,5 et versions ultérieures                        |

### <a name="virtual-device-requirements"></a>Configuration requise de périphérique virtuel

| **Composant**                                | **Configuration requise**            |
|----------------------------------------------|----------------------------|
| Nombre minimal de processeurs virtuels (cœurs) | 4                          |
| Mémoire minimale (RAM)                         | 8 GO                       |
| Espace disque<sup>1</sup>                       | Disque du système d’exploitation - 80 Go <br></br>Disque de données - 500 Go à 8 To|
| Nombre minimal d’interfaces réseau       | 1                          |
| De bande passante Internet minimale<sup>2</sup>       | 5 Mbps                     |

<sup>1</sup> - mince sa mise en service

<sup>2</sup> - exigences réseau peuvent varier selon le taux de modification des données quotidiennes. Par exemple, si un appareil doit sauvegarder les 10 Go ou d’autres modifications au cours d’une journée, puis la sauvegarde quotidienne via une connexion Mbps 5 peut prendre jusqu'à 4,25 heures (si les données impossible compressées ou dédupliquées).

### <a name="supported-web-browsers"></a>Navigateurs web pris en charge

| **Composant**     | **Version** | **Configuration requise supplémentaires/notes** |
|-------------------|-----------------|-----------------------------------|
| Microsoft Edge    | Version la plus récente  |                                   |
| Internet Explorer | Version la plus récente  | Testé avec Internet Explorer 11  |
| Google Chrome     | Version la plus récente  | Testé avec Chrome 46             |

### <a name="supported-storage-clients"></a>Clients de stockage pris en charge 

La configuration logicielle requise suivantes s’appliquent aux initiateurs qui accèdent à votre tableau virtuel StorSimple (configuré comme un serveur iSCSI).

| **Systèmes d’exploitation pris en charge** | **Version requise** | **Configuration requise supplémentaires/notes** |
| --------------------------- | ---------------- | ------------- |
| Windows Server              | 2008 R2 SP1, 2012, 2012R2 |StorSimple pouvez créer exactement généré et entièrement généré volumes. Il ne peut pas créer des volumes partiellement mis en service. Volumes iSCSI StorSimple sont uniquement pris en charge pour : <ul><li>Volumes simples sur disques de base de Windows.</li><li>Windows NTFS pour mettre en forme un volume.</li>|


La configuration logicielle requise suivantes s’appliquent aux clients PME qui accèdent à votre tableau virtuel StorSimple (configuré comme un serveur de fichiers).

| **Version PME** |
|-------------|
| PME 2.x     |
| PME 3.0     |
| PME 3.02    |

> [AZURE.IMPORTANT] Ne pas copier ou stocker des fichiers protégés par Windows système (ENCRYPTING) au serveur de fichiers tableau virtuel StorSimple ; Cela provoquera dans une configuration non prises en charge. 

## <a name="networking-requirements"></a>Configuration requise de mise en réseau 

Le tableau suivant répertorie les ports qui doivent être ouverts dans votre pare-feu afin de permettre iSCSI, PME, cloud ou le trafic de gestion. Dans ce tableau *en* *entrant* ou fait référence à la direction à partir de laquelle demandes des clients accèdent à votre périphérique. *Absence* ou *sortant* fait référence à la direction dans laquelle votre appareil StorSimple envoie des données à l’extérieur, au-delà du déploiement : par exemple, sortant à Internet.

| **Port n °<sup>1</sup>** | **Ou l’arrière** | **Étendue de port** | **Obligatoire**              | **Notes**                                                                                                            |
|--------------------------|---------------|----------------|---------------------------|----------------------------------------------------------------------------------------------------------------------|
| TCP 80 (HTTP)            | Déconnecter           | RÉSEAU ÉTENDU            | N°                        | Port sortant est utilisé pour l’accès Internet pour récupérer des mises à jour. <br></br>Le proxy web sortante est configurable par l’utilisateur. |
| TCP 443 (HTTPS)          | Déconnecter           | RÉSEAU ÉTENDU            | Oui                       | Port de sortie est utilisé pour l’accès aux données dans le cloud. <br></br>Le proxy web sortante est configurable par l’utilisateur. |
| UDP 53 (DNS)             | Déconnecter           | RÉSEAU ÉTENDU            | Dans certains cas ; Voir notes. | Ce port est nécessaire uniquement si vous utilisez un serveur DNS basées sur Internet. <br></br> **Remarque**: Si vous déployez un serveur de fichiers, nous vous recommandons d’à l’aide du serveur DNS local.|
| UDP 123 (NTP)            | Déconnecter           | RÉSEAU ÉTENDU            | Dans certains cas ; voir les notes. | Ce port est nécessaire uniquement si vous utilisez un serveur NTP basées sur Internet.<br></br> **Remarque :** Si vous déployez un serveur de fichiers, nous vous recommandons de synchroniser l’heure votre superflus Active Directory.  |
| TCP 80 (HTTP)           | Dans            | RÉSEAU LOCAL            | Oui                       | Il s’agit le port d’entrée pour l’interface utilisateur local sur le périphérique StorSimple pour la gestion locale. <br></br> **Remarque**: l’accès à l’interface utilisateur local sur HTTP redirigera automatiquement en HTTPS.|
| TCP 443 (HTTPS)          | Dans            | RÉSEAU LOCAL            | Oui                       | Il s’agit le port d’entrée pour l’interface utilisateur local sur le périphérique StorSimple pour la gestion locale.|
| TCP 3260 (iSCSI)         | Dans            | RÉSEAU LOCAL            | N°                        | Ce port est utilisé pour accéder aux données via iSCSI.|

<sup>1</sup> aucun ports entrants ne doivent être ouverts sur l’Internet public.

> [AZURE.IMPORTANT] Assurez-vous que le pare-feu ne pas modifier ou déchiffrer tout le trafic SSL entre le périphérique de StorSimple et Azure.


### <a name="url-patterns-for-firewall-rules"></a>Modèles d’URL pour les règles de pare-feu 

Administrateurs réseau peuvent souvent configurer des règles de pare-feu avancées basées sur les types d’URL pour filtrer entrant et le trafic sortant. Votre tableau virtuel et le service Manager StorSimple dépendent d’autres applications Microsoft tels que Bus des services Azure, un contrôle d’accès Azure Active Directory, comptes de stockage et serveurs Microsoft Update. Les modèles d’URL associés à ces applications peuvent servir à configurer des règles de pare-feu. Il est important de comprendre que les modèles d’URL associés à ces applications peuvent changer. Cette opération à son tour nécessite l’administrateur de réseau surveiller et mettre à jour des règles de pare-feu pour votre StorSimple en tant qu’et lorsque cela est nécessaire. 

Nous vous conseillons de définir vos règles de pare-feu pour le trafic sortant, en fonction de StorSimple fixé recourir largement adresses IP, dans la plupart des cas. Toutefois, vous pouvez utiliser les informations ci-dessous pour définir des règles de pare-feu avancé qui sont nécessaires pour créer des environnements sécurisés.

> [AZURE.NOTE] 
> 
> - Le périphérique adresses IP (source) doit toujours être défini à toutes les interfaces réseau activé pour le cloud. 
> - La destination adresses IP doit être défini sur les [plages d’adresses IP Azure centre de données](https://www.microsoft.com/en-us/download/confirmation.aspx?id=41653).


| Modèle d’URL                                                      | Fonctionnalités des composants                                           |
|------------------------------------------------------------------|---------------------------------------------------------------|
| `https://*.storsimple.windowsazure.com/*`<br>`https://*.accesscontrol.windows.net/*`<br>`https://*.servicebus.windows.net/*`   | Service Manager StorSimple<br>Service de contrôle d’accès<br>Bus des services Azure|
|`http://*.backup.windowsazure.com`|Inscription de l’appareil|
|`http://crl.microsoft.com/pki/*`<br>`http://www.microsoft.com/pki/*`|Révocation des certificats |
| `https://*.core.windows.net/*`<br>`https://*.data.microsoft.com`<br>`http://*.msftncsi.com` | Comptes de stockage Azure et de surveillance |
| `http://*.windowsupdate.microsoft.com`<br>`https://*.windowsupdate.microsoft.com`<br>`http://*.update.microsoft.com`<br> `https://*.update.microsoft.com`<br>`http://*.windowsupdate.com`<br>`http://download.microsoft.com`<br>`http://wustat.windows.com`<br>`http://ntservicepack.microsoft.com`| Serveurs Microsoft Update<br>                        |
| `http://*.deploy.akamaitechnologies.com`                         |CDN Akamai |
| `https://*.partners.extranet.microsoft.com/*`                    | Prise en charge                                                  |
| `http://*.data.microsoft.com `                   | Service de télémétrie dans Windows, consultez [mise à jour d’amélioration du produit et de diagnostic télémétrie](https://support.microsoft.com/en-us/kb/3068708)                                                  |

## <a name="next-step"></a>Étape suivante

-   [Préparer le portail pour déployer votre tableau virtuel StorSimple](storsimple-ova-deploy1-portal-prep.md)

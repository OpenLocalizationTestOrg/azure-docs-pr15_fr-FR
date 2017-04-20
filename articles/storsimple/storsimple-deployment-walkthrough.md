<properties
   pageTitle="Déployer un périphérique StorSimple local | Microsoft Azure"
   description="Décrit les étapes et les meilleures pratiques pour le déploiement de l’appareil StorSimple et le service. (S’applique à Microsoft Azure StorSimple version.3 et les versions antérieures.)"
   services="storsimple"
   documentationCenter="NA"
   authors="alkohli"
   manager="carmonm"
   editor="" />
<tags
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="hero-article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="08/17/2016"
   ms.author="alkohli" />

# <a name="deploy-your-on-premises-storsimple-device"></a>Déployez votre appareil StorSimple en local

> [AZURE.SELECTOR]
- [Mise à jour 2](../articles/storsimple/storsimple-deployment-walkthrough-u2.md)
- [Mise à jour 1](../articles/storsimple/storsimple-deployment-walkthrough-u1.md)
- [Version disponible](../articles/storsimple/storsimple-deployment-walkthrough.md)

## <a name="overview"></a>Vue d’ensemble

Bienvenue dans Microsoft Azure StorSimple déploiement du périphérique. Ces didacticiels de déploiement s’appliquent à la Version d’origine StorSimple 8000 série, mise à jour série StorSimple 8000 0,1, mise à jour de la série StorSimple 8000 0,2 et mise à jour série StorSimple 8000 0,3. Cette série de didacticiels explique comment configurer votre appareil StorSimple et inclut une liste de vérification de configuration, configuration préalable et configuration détaillée étapes.


Les informations contenues dans ces didacticiels supposent que vous avez examiné les mesures de sécurité et décompressé, montés en rack et relié votre appareil StorSimple. Si vous avez besoin effectuer ces tâches, commencez par examiner les [précautions à prendre](storsimple-safety.md). Selon votre modèle d’appareil, vous pouvez ensuite décompresser, montage en rack et câble en suivant les instructions dans :

- [Décompresser, montage en rack et câble votre 8100](storsimple-8100-hardware-installation.md)
- [Décompresser, montage en rack et câble votre 8600](storsimple-8600-hardware-installation.md)

Vous avez besoin des privilèges d’administrateur pour terminer le processus d’installation et de configuration. Nous vous recommandons de consulter la liste de vérification de configuration avant de commencer. Le processus de configuration et le déploiement peut prendre un certain temps pour terminer.

> [AZURE.NOTE] Les informations de déploiement StorSimple publiées sur le site Web Microsoft Azure s’applique aux périphériques série 8000 StorSimple uniquement. Pour obtenir des informations complètes sur les appareils série 5 000 et 7000, accédez à : [http://onlinehelp.storsimple.com/](http://onlinehelp.storsimple.com). Pour plus d’informations de déploiement série 5 000 et 7000, consultez le [Guide de démarrage rapide StorSimple système](http://onlinehelp.storsimple.com/111_Appliance/).

## <a name="deployment-steps"></a>Étapes de déploiement

Effectuer ces étapes requises pour configurer votre appareil StorSimple et se connecter à votre service Manager StorSimple. Outre les étapes nécessaires, il existe des étapes facultatives et les procédures que nécessaires au cours du déploiement. Les instructions de déploiement étape par étape indiquent lorsque vous devez effectuer chacune de ces étapes facultatives.


| Étape                                                                                   | Description                                                                                                                                                   |
|----------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **CONDITIONS PRÉALABLES**                                                                      | Ils doivent être effectuées en vue du déploiement à venir.                                                                                        |
| Aide-mémoire de configuration de déploiement.                                                     | Utilisez cette liste de vérification pour collecter et enregistrer les informations avant et pendant le déploiement.                                                                       |
| Conditions préalables au déploiement.                                                               | Ces valider l’environnement est prêt pour le déploiement.                                                                                                     |
|                                                                                        |                                                                                                                                                               |
| **DÉPLOIEMENT ÉTAPE PAR ÉTAPE**                                                                   | Ces étapes sont nécessaires pour déployer votre appareil StorSimple en production.                                                                                      |
| Étape 1 : Créer un nouveau service.                                                         | Configurer la gestion de cloud et de stockage pour votre appareil StorSimple. Ignorer cette étape si vous avez un service existant pour les autres appareils StorSimple.                |
| Étape 2 : Obtenir la clé d’enregistrement de service.                                               | Utilisez cette touche pour enregistrer et vous connecter votre périphérique StorSimple avec le service de gestion.                                                                         |
| Étape 3 : Configurer et inscrire le périphérique à utiliser Windows PowerShell pour StorSimple.    | Connectez le périphérique à votre réseau et l’enregistrer avec Azure pour terminer l’installation à l’aide du service de gestion.                                            |
| Étape 4 : Finaliser la configuration minimale appareil</br>Facultatif : Mettre à jour votre périphérique StorSimple.      | Utiliser le service de gestion pour terminer l’installation de l’appareil et activez-le fournir le stockage.                                                                      |
| Étape 5 : Créer un conteneur de volume.                                                      | Créer un conteneur vers des volumes de mise en service. Un conteneur de volume a compte de stockage, la bande passante et les paramètres de chiffrement pour tous les volumes qu’il contient.    |
| Étape 6 : Créer un volume.                                                                | Mise en service des volumes de stockage sur le périphérique StorSimple pour vos serveurs.                                                                                        |
| Étape 7 : Monter, initialisation et mettre en forme un volume.</br>Facultatif : Configurez o.            | Se connecter à vos serveurs pour le stockage iSCSI fourni par l’appareil. Vous pouvez également configurer o pour vous assurer que vos serveurs tolèrent un lien, le réseau et Échec de l’interface.                                                                                                                                                              |
| Étape 8 : Effectuer une sauvegarde.                                                                  | Configurer votre stratégie de sauvegarde pour protéger vos données                                                                                                                 |
|                                                                                        |                                                                                                                                                               |
| **AUTRES PROCÉDURES**                                                                   | Vous devrez peut-être faire référence à ces procédures lorsque vous déployez votre solution.                                                                                        |
| Configurer un nouveau compte de stockage pour le service.                                      |                                                                                                                                                               |
| Utilisez PuTTY pour vous connecter à la console série appareil.                                    |                                                                                                                                                               |
| Obtenir le nom qualifié d’un hôte Windows Server.                                                   |                                                                                                                                                               |
| Créer une sauvegarde manuelle.                                                                 |


## <a name="deployment-configuration-checklist"></a>Aide-mémoire de configuration de déploiement

La liste de configuration de déploiement suivante décrit les informations dont vous avez besoin pour recueillir avant et que vous configurez le logiciel sur votre appareil StorSimple. Préparation de certaines de ces informations à l’avance aideront à simplifier le processus de déploiement de l’appareil StorSimple dans votre environnement. Utilisez cette liste de vérification pour Notez également les modalités de configuration lorsque vous déployez votre appareil.

| Étape                                  | Paramètre                                         | Plus d’informations                                                                                                                                                                | Valeurs |
|----------------------------------------|---------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------|
| **Câble votre appareil**                      | Accès série                                     | Configuration initiale d’un appareil                                                                  | Oui/non |
|   |   |  |  |
| **Configurer et inscrire appareil**          | Paramètres du réseau de données 0                           | Adresse IP de données 0 :</br>Masque :</br>Passerelle :</br>Serveur DNS principal :</br>Serveur NTP principal :</br>Serveur de proxy Web IP/nom de domaine complet (facultatif) :</br>Port du proxy Web :|        |
|                                        | Mot de passe administrateur                     | Mot de passe doit être comprise entre 8 et 15 caractères contenant des caractères minuscules, majuscules, numériques et spéciaux. |        |
|                                        | Gestionnaire d’instantanés StorSimple le mot de passe              | Mot de passe doit être 14 ou 15 caractères contenant des caractères minuscules, majuscules, numériques et spéciaux.|        |
|                                        | Clé d’enregistrement de service                          | Cette clé est générée à partir du portail classique Azure.    |        |
|                                        | Clé de chiffrement de données de service                       | Cette clé est créée lorsque l’appareil est inscrit auprès du service de gestion via le Windows PowerShell pour StorSimple. Copiez cette touche et enregistrez-le dans un emplacement sécurisé.|  |
|   |   |  |  |
| **Configuration minimale complète des périphériques**          | Nom convivial pour votre appareil                     | Il s’agit d’un nom descriptif pour l’appareil. |        |
|                                        | Fuseau horaire                                          | Votre périphérique utilisera ce fuseau horaire pour toutes les opérations planifiées.  |        |
|                                        | Serveur DNS secondaire                              | Il s’agit d’une configuration requise.                                  |        |
|                                        | Interface réseau : contrôleur de données 0 fixe adresses IP                                     | Ces IP doit être prenant à Internet.</br>Contrôleur 0 fixe IP adresse :</br>Contrôleur 1 fixe IP adresse :|
|   |   |  |  |
| **Paramètres de l’interface réseau supplémentaires**  | Interface réseau : données 1</br>Si iSCSI activé, ne configurez pas la passerelle.      | Objectif : Cloud/iSCSI / n’est ne pas utilisé</br>Adresse IP :</br>Masque :</br>Passerelle :|
|                                        | Interface réseau : données 2</br>Si iSCSI activé, ne configurez pas la passerelle.      | Objectif : Cloud/iSCSI / n’est ne pas utilisé</br>Adresse IP :</br>Masque :</br>Passerelle :|
|                                        | Interface réseau : données 3</br>Si iSCSI activé, ne configurez pas la passerelle.      | Objectif : Cloud/iSCSI / n’est ne pas utilisé</br>Adresse IP :</br>Masque :</br>Passerelle :|
|                                        | Interface réseau : données 4</br>Si iSCSI activé, ne configurez pas la passerelle.      | Objectif : Cloud/iSCSI / n’est ne pas utilisé</br>Adresse IP :</br>Masque :</br>Passerelle :|
|                                        | Interface réseau : données 5</br>Si iSCSI activé, ne configurez pas la passerelle.      | Objectif : Cloud/iSCSI / n’est ne pas utilisé</br>Adresse IP :</br>Masque :</br>Passerelle :|
|   |   |  |  |
| **Créer un conteneur de volume**                      | Nom du conteneur en volume :                            | Nom pour le conteneur                                                                                                                                                 |        |
|                                        | Compte de stockage Azure :                            | Clé d’accès aux et nom de compte à associer à ce conteneur de volume stockage                                                                                              |        |
|                                        | Clé de chiffrement de stockage cloud :                     | Clé de chiffrement pour le stockage dans chaque conteneur                                                                                                                           |        |
|   |   |  |  |
| **Créer un volume**                        | Détails de chaque volume                           | Nom du volume :                                                                                                                                                           |        |
|                                        |                                                   | Taille :                                                                                                                                                                  |        |
|                                        |                                                   | Type d’utilisation :                                                                                                                                                            |        |
|                                        |                                                   | Nom du blocage :                                                                                                                                                              |        |
|                                        |                                                   | Stratégie de sauvegarde par défaut :                                                                                                                                                 |        |
|   |   |  |  |
| **Monter, initialisation et mettre en forme un volume** | Détails de chaque serveur hôte de la connexion à l’espace de stockage | Nom du serveur de Windows :                                                                                                                                                   |        |
|                                        |                                                   | Windows Server nom qualifié :                                                                                                                                                    |        |
|                                        |                                                   | Nom de volume de Windows Server :                                                                                                                                                   |        |
|                                        |                                                   | NTFS monter point/lettre :                                                                                                                                      |        |

## <a name="deployment-prerequisites"></a>Conditions préalables au déploiement

Les sections suivantes décrivent les conditions préalables configuration pour votre service Manager StorSimple, votre appareil StorSimple et le réseau de votre centre de données.

### <a name="for-the-storsimple-manager-service"></a>Pour le service StorSimple Manager

Avant de commencer, vérifiez que :

- Vous avez votre compte Microsoft avec les informations d’accès.

- Vous avez votre compte de stockage de Microsoft Azure avec les informations d’accès.

- Votre abonnement Microsoft Azure est activée pour le service Manager StorSimple. Votre abonnement doit être acheté via l' [Accord entreprise](https://azure.microsoft.com/pricing/enterprise-agreement/).

- Vous avez accès au logiciel émulation tel que PuTTY.

### <a name="for-the-device-in-the-datacenter"></a>Pour le périphérique dans le centre de données

Avant de configurer le périphérique, vérifiez que :

- Votre périphérique est entièrement décompressé, monter dans un rayon et entièrement relié d’alimentation, réseau et accès série comme décrit dans :

    -  [Décompresser, montage en rack et câble votre appareil 8100](storsimple-8100-hardware-installation.md)
    -  [Décompresser, montage en rack et câble votre appareil 8600](storsimple-8600-hardware-installation.md)


### <a name="for-the-network-in-the-datacenter"></a>Pour le réseau dans le centre de données

Avant de commencer, vérifiez que :

- Les ports de pare-feu de votre centre de données sont ouverts pour autoriser pour iSCSI et le trafic comme décrit dans [mise en réseau configuration requise pour votre appareil StorSimple](storsimple-system-requirements.md#networking-requirements-for-your-storsimple-device)le cloud.
- Le périphérique dans votre centre de données peut se connecter au réseau externe. Exécuter les applets de commande [Windows PowerShell 4.0](http://www.microsoft.com/download/details.aspx?id=40855) suivant (sous forme de tableau ci-dessous) pour valider la connectivité au réseau externe. Exécuter cette validation sur un ordinateur (dans le centre de données réseau) qui comporte une connectivité à Azure et l’endroit où vous allez déployer votre appareil StorSimple.  

| Pour ce paramètre...       | Pour vérifier la validité...                                                                                                                                                                                | Exécuter ces commandes/applets de commande.                                                                                                                                                             |
|---------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **IP**</br>**Sous-réseau**</br>**Passerelle** | S’agit-il d’une adresse IPv4 ou IPv6 valide ?</br>S’agit-il d’un sous-réseau valide ?</br>S’agit-il d’une passerelle valide ?</br>S’agit-il d’une adresse IP en double sur réseau ?                                                                          | `ping ip`</br>`arp -a`</br>La `ping` et `arp` commandes doivent échouer indiquant qu’il n’existe aucun périphérique dans le réseau de centre de données qui utilise cette adresse IP.
|                           |                                                                                                                                                                    |                                                                                                                                                                                         |
| **DNS**                       | S’agit-il d’un serveur DNS valide et peut résoudre des URL Azure ?                                                                                                                    | `Resolve-DnsName -Name www.bing.com -Server <DNS server IP address>` </br>Une commande de remplacement qui peut être utilisée est la suivante :</br>`nslookup --dns-ip=<DNS server IP address> www.bing.com`      |
|                           | Vérifier si le port 53 est ouvert. Cela s’applique uniquement si vous utilisez un serveur DNS externe pour votre appareil. DNS interne doit résoudre automatiquement les URL externes.  | `Test-Port -comp dc1 -port 53 -udp -UDPtimeout 10000`  </br>[Plus d’informations sur cette applet de commande](http://learn-powershell.net/2011/02/21/querying-udp-ports-with-powershell/)|
|                           |                                                                                                                                                                    |                                                                                                                                                                                         |
| **NTP**                       | Nous déclencher une synchronisation heure au plus tôt serveurNTP est entrée. Le port UDP vérifier 123 est ouvert lorsque vous entrez `time.windows.com` ou les serveurs de temps public). | [Télécharger et utiliser ce script](https://gallery.technet.microsoft.com/scriptcenter/Get-Network-NTP-Time-with-07b216ca).                                                                                                                                                           |
|                           |                                                                                                                                                                    |                                                                                                                                                                                         |
| **Proxy (facultatif)**          | S’agit-il d’un proxy valide URI et le port ? </br> Le mode d’authentification est correcte ?                                                                                                                                | <code>wget http://bing.com &#124; % {$_.StatusCode}</code></br>Cette commande doit être exécutée immédiatement après la configuration proxy web. Si un code d’état de 200 est renvoyé, cela signifie que la connexion a réussi.                                                                                                                                 |
|                           | Est le trafic pouvant être routés via proxy ?                                                                                                                                 | Exécuter la validation de DNS, NTP ou HTTP une fois après la configuration de proxy sur votre appareil. Cela donne une idée précise si le trafic est bloqué au proxy ou un autre emplacement.                                                                                                                      |
|                           |                                                                                                                                                                    |                                                                                                                                                                                         |
| **Enregistrement**              | Vérifier si les ports TCP sortants 443, 80, 9354 sont ouverts.                                                                                                                |  `Test-NetConnection -Port   443 -InformationLevel Detailed`</br>[Plus d’informations pour l’applet de commande Test NetConnection](https://technet.microsoft.com/library/dn372891.aspx)                                                                           |

## <a name="step-by-step-deployment"></a>Déploiement étape par étape

Utilisez la procédure à suivre pour déployer votre appareil StorSimple dans le centre de données.

## <a name="step-1-create-a-new-service"></a>Étape 1 : Créer un nouveau service

Un service Manager StorSimple peut gérer plusieurs appareils StorSimple. Pour le déploiement de votre première StorSimple, vous devrez créer un nouveau service Manager StorSimple.

> [AZURE.IMPORTANT] Ignorer cette étape si vous avez un service Manager StorSimple existant et que vous voulez déployer votre appareil StorSimple avec ce service.

Procédez comme suit pour créer une nouvelle instance du service Manager StorSimple.

[AZURE.INCLUDE [storsimple-create-new-service](../../includes/storsimple-create-new-service.md)]

> [AZURE.IMPORTANT] Si vous n’avez pas activé la création automatique d’un compte de stockage auprès de votre service, vous devrez créer au moins un compte de stockage une fois que vous avez créé un service. Ce compte de stockage sera utilisé lorsque vous créez un conteneur en volume.
>
> Si vous n’avez pas créé un compte de stockage automatiquement, accédez à [configurer un nouveau compte de stockage pour le service](#configure-a-new-storage-account-for-the-service) pour obtenir des instructions détaillées.
> Si vous avez activé la création automatique d’un compte de stockage, accédez à [étape 2 : obtenir la clé d’inscription du service](#step-2:-get-the-service-registration-key).

## <a name="step-2-get-the-service-registration-key"></a>Étape 2 : Obtenir la clé d’inscription de service

Une fois que le service Manager StorSimple est en cours d’exécution, vous devrez obtenir la clé d’enregistrement de service. Cette clé est utilisée pour enregistrer et connecter votre appareil StorSimple avec le service.

Effectuez les opérations suivantes dans le portail classique Azure.

[AZURE.INCLUDE [storsimple-get-service-registration-key](../../includes/storsimple-get-service-registration-key.md)]


## <a name="step-3-configure-and-register-the-device-through-windows-powershell-for-storsimple"></a>Étape 3 : Configurer et inscrire le périphérique à utiliser Windows PowerShell pour StorSimple

> [AZURE.IMPORTANT] Avant d’effectuer cette configuration, débranchez toutes les interfaces de réseau autres que celles de 0 sur à la fois (actif et passif) les contrôleurs.

Utiliser Windows PowerShell pour StorSimple pour terminer la configuration initiale de votre appareil StorSimple comme indiqué dans la procédure suivante. Vous devrez utiliser le logiciel d’émulation terminal pour exécuter cette étape. Pour plus d’informations, voir [Utiliser PuTTY pour vous connecter à la console série appareil](#use-putty-to-connect-to-the-device-serial-console).

[AZURE.INCLUDE [storsimple-configure-and-register-device](../../includes/storsimple-configure-and-register-device.md)]

## <a name="step-4-complete-minimum-device-setup"></a>Étape 4 : Finaliser la configuration minimale appareil

Pour la configuration du périphérique minimale de votre appareil StorSimple, vous êtes obligé de :

- Configurer le serveur DNS secondaire.
- Activer iSCSI sur au moins une interface réseau.
- Affecter des adresses IP fixes aux deux contrôleurs.

Effectuez les opérations suivantes dans le portail classique Azure pour terminer l’installation de périphériques minimum.

[AZURE.INCLUDE [storsimple-complete-minimum-device-setup](../../includes/storsimple-complete-minimum-device-setup.md)]

Une fois la configuration du périphérique terminée, vous devez rechercher les mises à jour et s’il est disponible, installez les mises à jour. Les mises à jour peuvent prendre plusieurs heures. Suivez les instructions de [Rechercher et appliquer des mises à jour](#scan-for-and-apply-updates).


## <a name="step-5-create-a-volume-container"></a>Étape 5 : Créer un conteneur de volume

Un conteneur de volume a compte de stockage, la bande passante et les paramètres de chiffrement pour tous les volumes qu’il contient. Vous devrez créer un conteneur de volume avant de commencer à mise en service des volumes sur votre appareil StorSimple.

Effectuez les opérations suivantes dans le portail classique Azure pour créer un conteneur de volume.

[AZURE.INCLUDE [storsimple-create-volume-container](../../includes/storsimple-create-volume-container.md)]

## <a name="step-6-create-a-volume"></a>Étape 6 : Créer un volume

Après avoir créé un conteneur de volume, vous pouvez configurer un volume de stockage sur le périphérique StorSimple pour vos serveurs. Effectuez les opérations suivantes dans le portail classique Azure pour créer un volume.

> [AZURE.IMPORTANT] Gestionnaire de StorSimple pouvez créer des volumes de légèrement générés uniquement.  Vous ne pouvez pas créer entièrement ou partiellement générés volumes.

[AZURE.INCLUDE [storsimple-create-volume](../../includes/storsimple-create-volume.md)]

## <a name="step-7-mount-initialize-and-format-a-volume"></a>Étape 7 : Monter, initialisation et mettre en forme un volume

> [AZURE.IMPORTANT]

> - Pour la disponibilité de votre solution StorSimple, nous vous recommandons de configurer o sur votre hôte Windows Server (facultatif) avant de configurer iSCSI sur votre hôte Windows Server. Configuration o sur vos serveurs hôtes permet de garantir que les serveurs tolèrent un lien, de réseau ou Échec de l’interface.

> - Pour obtenir des instructions d’installation et configuration o et iSCSI, accédez à [Configurer o pour votre appareil StorSimple](storsimple-configure-mpio-windows-server.md). Il inclut également les étapes pour monter, initialisation et mettre en forme des volumes StorSimple.

Si vous décidez de ne pas configurer o, procédez comme suit pour monter, initialisation et mettre en forme vos volumes StorSimple.

[AZURE.INCLUDE [storsimple-mount-initialize-format-volume](../../includes/storsimple-mount-initialize-format-volume.md)]

## <a name="step-8-take-a-backup"></a>Étape 8 : Effectuer une sauvegarde

Des sauvegardes point-à-temps protègent des volumes et d’améliorent la récupération et en limitant les délais de restauration. Vous pouvez effectuer les deux types de sauvegarde sur votre appareil StorSimple : instantanés locaux et des instantanés cloud. Chacun de ces types de sauvegarde peut être **planifiée** ou **manuel**.

Effectuez les opérations suivantes dans le portail classique Azure pour créer une sauvegarde planifiée.

[AZURE.INCLUDE [storsimple-take-backup](../../includes/storsimple-take-backup.md)]

Vous pouvez prendre un manuel de sauvegarde à tout moment. Pour plus d’informations, accédez à [créer une sauvegarde manuelle](#Create-a-manual-backup).

## <a name="configure-a-new-storage-account-for-the-service"></a>Configurer un nouveau compte de stockage pour le service

Il s’agit d’une étape facultative dont vous avez besoin pour effectuer uniquement si vous n’avez pas activé la création automatique d’un compte de stockage auprès de votre service. Un compte de stockage de Microsoft Azure est requis pour créer un conteneur de volume StorSimple.

Si vous avez besoin créer un compte de stockage Azure dans une zone différente, voir [à propos des comptes d’espace de stockage Azure](../storage/storage-create-storage-account.md) pour obtenir des instructions étape par étape.

Effectuez les opérations suivantes dans le portail classique Azure, dans la page **service Manager StorSimple** .

[AZURE.INCLUDE [storsimple-configure-new-storage-account](../../includes/storsimple-configure-new-storage-account.md)]


## <a name="use-putty-to-connect-to-the-device-serial-console"></a>Permet de vous connecter à la console série appareil PuTTY

Pour vous connecter à Windows PowerShell pour StorSimple, vous devez utiliser le logiciel d’émulation terminal tel que PuTTY. Vous pouvez utiliser PuTTY lorsque vous accédez à l’appareil directement par le biais de la console en série ou en ouvrant un présents depuis un ordinateur distant.

[AZURE.INCLUDE [Use PuTTY to connect to the device serial console](../../includes/storsimple-use-putty.md)]

## <a name="scan-for-and-apply-updates"></a>Rechercher et appliquer des mises à jour

Mise à jour de votre appareil peut prendre de 1 à 4 heures. Procédez comme suit pour rechercher et appliquer des mises à jour sur votre appareil.

> [AZURE.NOTE] Si vous avez une passerelle est configurée sur une interface réseau différente de données 0, vous devez désactiver les interfaces de réseau de données 2 et 3 de données avant d’installer la mise à jour. Accédez à **appareils > configurer** et désactiver les interfaces de données 2 et 3 de données. Vous devez réactiver ces interfaces après la mise à jour de l’appareil.

#### <a name="to-update-your-device"></a>Mettre à jour votre appareil
1.  Dans la page de **Démarrage rapide** de périphérique, cliquez sur **périphériques**. Sélectionnez le périphérique physique, cliquez sur **Maintenance** , puis sur **Rechercher les mises à jour**.  
2.  Une tâche pour rechercher les mises à jour disponibles est créée. Si mises à jour sont disponibles, les **Mises à jour analyse** devient **Installer les mises à jour**. Cliquez sur **installer les mises à jour**. Vous y êtes invité à désactiver Data 2 et 3 de données avant d’installer les mises à jour. Vous devez désactiver ces interfaces réseau ou les mises à jour peuvent échouer.
3.  Un projet de mise à jour est créée. Surveiller l’état de votre mise à jour en accédant à des **projets**.

    > [AZURE.NOTE] Lorsque l’opération de mise à jour démarre, il affiche immédiatement le statut de 50 %. Le statut devient puis à 100 pour cent qu’après que la mise à jour de tâche est terminée. Il n’existe aucun état en temps réel pour le processus de mises à jour.

4.  Une fois que l’appareil est correctement mis à jour, activer les interfaces de réseau de données 2 et 3 de données si celles-ci ont été désactivés.



## <a name="get-the-iqn-of-a-windows-server-host"></a>Obtenir le nom qualifié d’un hôte Windows Server

Effectuez les opérations suivantes pour obtenir l’iSCSI complet nom (nom qualifié) d’un hôte Windows exécutant Windows Server 2012.

[AZURE.INCLUDE [Create a manual backup](../../includes/storsimple-get-iqn.md)]


## <a name="create-a-manual-backup"></a>Créer une sauvegarde manuelle

Effectuez les opérations suivantes dans le portail classique Azure pour créer une sauvegarde manuelle à la demande d’un seul volume sur votre appareil StorSimple.

[AZURE.INCLUDE [Create a manual backup](../../includes/storsimple-create-manual-backup.md)]


## <a name="next-steps"></a>Étapes suivantes

- Configurer un [périphérique virtuel](storsimple-virtual-device-u2.md).

- Utiliser le [service StorSimple Manager](https://msdn.microsoft.com/library/azure/dn772396.aspx) pour gérer votre appareil StorSimple.

<properties 
   pageTitle="Modifier la configuration du dispositif StorSimple | Microsoft Azure" 
   description="Décrit comment utiliser le service Manager StorSimple de reconfigurer un périphérique StorSimple qui a déjà été déployé." 
   services="storsimple" 
   documentationCenter="NA" 
   authors="SharS" 
   manager="carmonm" 
   editor=""/>

<tags
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD" 
   ms.date="09/29/2016"
   ms.author="v-sharos"/>

# <a name="use-the-storsimple-manager-service-to-modify-your-storsimple-device-configuration"></a>Utiliser le service Manager StorSimple pour modifier la configuration de votre appareil StorSimple

## <a name="overview"></a>Vue d’ensemble 

La page de **configuration** du portail classique Azure contient tous les paramètres de périphérique que vous pouvez reconfigurer sur un appareil StorSimple qui est géré par un service Manager StorSimple. Ce didacticiel décrit comment vous pouvez utiliser la page **configurer** pour effectuer les tâches suivantes au niveau du périphérique :

- Modifier les paramètres de l’appareil 
- Modifier les paramètres de temps 
- Modifier les paramètres DNS 
- Modifier les interfaces réseau
- Remplacez ou réattribuer des adresses IP

## <a name="modify-device-settings"></a>Modifier les paramètres de l’appareil

Les paramètres du périphérique incluent le nom convivial de l’appareil et la description de périphérique.

Un périphérique StorSimple qui est connecté au service Manager StorSimple est affecté un nom par défaut. Le nom par défaut reflète généralement le numéro de série de l’appareil. Par exemple, nom de périphérique par défaut est 15 caractères, par exemple 8600-SHX0991003G44HT indique les informations suivantes :

- **8600** – indique le modèle d’appareil.
- **SHX** – indique le site de fabrication.
- **0991003** - indique un produit spécifique.
- **G44HT**- les 5 derniers chiffres sont incrémentés pour créer des numéros de série uniques. Il peut s’agir pas une série séquentielle.

Vous pouvez utiliser le portail classique Azure pour modifier le nom de l’appareil et lui attribuer un nom convivial unique de votre choix. Le nom convivial peut contenir tout caractère et peut contenir un maximum de 64 caractères.

Vous pouvez également spécifier une description de périphérique. Une description de périphérique permet généralement d’identifier le propriétaire et l’emplacement physique du périphérique. Le champ description doit contenir moins de 256 caractères.
 
## <a name="modify-time-settings"></a>Modifier les paramètres de temps

Votre appareil doit synchroniser heure afin de vous authentifier avec votre fournisseur de service de stockage cloud. Sélectionnez votre fuseau horaire dans la liste déroulante, puis spécifiez jusqu'à deux serveurs de temps NTP (Network Protocol). Le serveur NTP principal est requis et est spécifié lorsque vous utilisez Windows PowerShell pour StorSimple pour configurer votre appareil. Vous pouvez spécifier le serveur de Windows par défaut **time.windows.com** en tant que votre serveur NTP. Vous pouvez afficher la configuration du serveur NTP principale via le portail classique Azure, mais vous devez utiliser l’interface Windows PowerShell pour le modifier.

La configuration du serveur NTP secondaire est facultative. Vous pouvez utiliser le portail classique pour configurer un serveur NTP secondaire. 

Lorsque vous configurez le serveur NTP, assurez-vous que votre réseau autorise le trafic NTP à partir de votre centre de données à Internet. Lorsque vous spécifiez un serveur NTP public, assurez-vous que votre pare-feu réseau et autres périphériques de sécurité sont configurés pour autoriser le trafic NTP et en provenance du réseau externe. Si le trafic bidirectionnel NTP n’est pas autorisé, vous devez utiliser un serveur NTP interne (un contrôleur de domaine Windows fournit cette fonction). Si votre appareil ne peut pas synchroniser time, il peut ne pas être en mesure de communiquer avec votre fournisseur de stockage cloud.

Pour afficher la liste des serveurs NTP publics, accédez à la [NTP serveurs Web](http://support.ntp.org/bin/view/Servers/WebHome). 

### <a name="what-happens-if-the-device-is-deployed-in-a-different-time-zone"></a>Que se passe-t-il si le périphérique est déployé dans un autre fuseau horaire ?

Si l’appareil est déployé dans un autre fuseau horaire, le fuseau horaire appareil changera. Étant donné que toutes les stratégies de sauvegarde utilisent le fuseau horaire appareil, les stratégies de sauvegarde ajuste automatiquement selon le nouveau fuseau horaire. Aucune intervention utilisateur est nécessaire.

## <a name="modify-dns-settings"></a>Modifier les paramètres DNS

Un serveur DNS est utilisé lors de votre appareil tente de communiquer avec votre fournisseur de service de stockage cloud. De disponibilité, vous devez configurer le serveur principal et les serveurs DNS secondaires au cours du déploiement initiale d’un appareil. Pour reconfigurer le serveur DNS principal, vous devrez utiliser l’interface Windows PowerShell sur votre appareil StorSimple.

Pour modifier le serveur DNS secondaire, vous pouvez utiliser le portail classique Azure.



## <a name="modify-network-interfaces"></a>Modifier les interfaces réseau

Votre appareil dispose d’interfaces de réseau périphérique six, dont quatre 1 solution et deux dont vous solution 10. Ces interfaces sont étiquetés en tant que données 0 – données 5. DONNÉES 0, données 1, données 4 et 5 de données sont 1 solution, tandis que les données 2 et 3 de données sont 10 interfaces de réseau de solution.

Configurer les **Paramètres de l’Interface réseau** pour chacune des interfaces à utiliser. Pour optimiser la disponibilité, nous vous recommandons d’avoir au moins deux interfaces iSCSI et deux interfaces activé pour le cloud sur votre appareil. Nous vous recommandons, mais ne nécessitent pas que les interfaces inutilisées désactivées.

Lorsque vous configurez une des interfaces réseau, vous devez configurer un IP virtuelle.

DONNÉES 0 sont activé pour le cloud par défaut. Lorsque vous configurez données 0, vous devez également configurer deux adresses IP fixes, une pour chaque contrôleur. Ces adresses IP fixes peuvent être utilisés pour accéder directement aux contrôleurs appareil et sont utiles lorsque vous installez les mises à jour sur l’appareil ou lorsque vous accédez aux contrôleurs aux fins de dépannage.

StorSimple 8000 série mise à jour 1, la métrique de routage de données 0 est définie à la plus basse ; Par conséquent, si votre appareil est en cours d’exécution StorSimple 8000 série Update 1, tout le trafic de cloud sera routé via données 0. Prenez note de ce si vous avez plusieurs interface réseau activé pour le cloud sur votre appareil StorSimple.

>[AZURE.NOTE] Les adresses IP fixes pour le contrôleur sont utilisées pour traiter les mises à jour à l’appareil. Par conséquent, les adresses IP fixe doivent être prenant et vous connecter à Internet.

Pour chaque interface réseau, les paramètres suivants sont affichés :

- **Vitesse** – pas un paramètre configurable par l’utilisateur. DONNÉES 0, données 1, données 4 et 5 de données sont toujours 1 solution, tandis que DATA 2 et 3 de données sont 10 interfaces solution.

     >[AZURE.NOTE] Vitesse et recto verso sont toujours automatique-négociation. Trames ne sont pas pris en charge.
 
- **État de l’interface** – une interface peut être activée ou désactivée. Si activé, l’appareil tente d’utiliser l’interface. Nous vous recommandons qu’uniquement les interfaces qui sont connectés au réseau et utilisés soit activé. Désactiver les interfaces que vous n’utilisez pas.

- **Type d’interface** – ce paramètre vous permet d’isoler le trafic iSCSI du trafic de stockage cloud. Ce paramètre peut être une des opérations suivantes :

    - **Cloud activé** – lorsque activé, l’appareil utilisera cette interface pour communiquer avec le cloud.
    - **iSCSI activé** – lorsque activé, l’appareil utiliser cette interface pour communiquer avec l’hôte iSCSI.

    Nous vous recommandons d’isoler le trafic iSCSI du trafic de stockage cloud. Notez également que si votre hôte se trouve dans le même sous-réseau que votre appareil, vous n’avez pas besoin affecter une passerelle ; Toutefois, si votre hôte est dans un autre sous-réseau que votre appareil, vous devez affecter une passerelle.

- **Adresse IP** – il peut s’agir IPv4 ou IPv6 ou les deux. Familles d’adresses IPv4 et IPv6 sont prises en charge pour les interfaces de réseau périphérique. Lorsque vous utilisez IPv4, spécifiez une adresse IP de 32 bits (*xxx.xxx.xxx.xxx*) en notation décimale à points. Lors de l’utilisation du protocole IPv6, il vous suffit de fournir un préfixe à 4 chiffres et une adresse 128 bits est générée automatiquement pour l’interface de réseau périphérique basé sur ce préfixe.

- **Sous-réseau** – cette formule fait référence à la masque et est configurée via l’interface Windows PowerShell.

- **Passerelle** : il s’agit de la passerelle par défaut qui doit être utilisée par cette interface lorsqu’il tente de communiquer avec des nœuds qui ne sont pas dans le même espace d’adressage IP (sous-réseau). La passerelle par défaut doit être placé dans le même espace d’adressage (sous-réseau) en tant que l’interface adresse IP, tel que déterminé par le masque.

- **Adresse IP fixe** – ce champ n’est disponible que lorsque vous configurez les données 0 interface. Pour les opérations, telles que les mises à jour ou résolution des problèmes de l’appareil, vous devrez peut-être vous connecter directement au contrôleur de périphérique. L’adresse IP statique peut être utilisé pour accéder à l’actif et le contrôleur passif sur votre appareil.

Vous pouvez reconfigurer contrôleur de 0 et 1 contrôleur via le portail classique Azure.

>[AZURE.NOTE] 
>
>- Pour garantir le bon fonctionnement, vérifiez la vitesse de l’interface et recto verso sur le commutateur chaque interface périphérique est connecté à. Interfaces commutateur soit négociez avec ou être configurées pour Gigabit Ethernet (1000 Mbps) et être bidirectionnelle. Interfaces d’exploitation vitesses plus lente ou en recto verso moitié seront traduit par des problèmes de performances.
>
>- Pour réduire les interruptions et temps d’arrêt, nous vous recommandons de que vous activez portfast sur chacun des ports de commutateur qui se connecteront à l’interface de réseau iSCSI de votre appareil. Cela garantit que la connectivité réseau peut être établie rapidement en cas de basculement.
 
## <a name="swap-or-reassign-ips"></a>Remplacez ou réattribuer des adresses IP

Pour l’instant, si une interface réseau sur le contrôleur est affectée un VIP est en cours d’utilisation (par le même appareil ou un autre appareil dans le réseau), puis le contrôleur bascule vers. Par conséquent, vous devez respecter la procédure appropriée si vous êtes inversion VIP pour l’interface de réseau périphérique, parce que vous allez créer une situation IP en double.

Effectuez les opérations suivantes pour échanger ou réaffecter la VIP pour une des interfaces réseau :

#### <a name="to-reassign-ips"></a>Pour réaffecter les adresses IP

1. Désactivez l’adresse IP pour les deux interfaces.

2. Une fois que les adresses IP sont désactivées, attribuer les nouvelles adresses IP pour les interfaces respectives.

## <a name="next-steps"></a>Étapes suivantes

- Découvrez comment [configurer o pour votre appareil StorSimple](storsimple-configure-mpio-windows-server.md).

- Découvrez comment [utiliser le service du Gestionnaire de StorSimple pour administrer votre périphérique StorSimple](storsimple-manager-service-administration.md).
     

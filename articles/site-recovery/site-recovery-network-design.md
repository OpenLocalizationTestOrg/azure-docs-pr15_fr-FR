<properties
    pageTitle="Conception de votre infrastructure réseau pour la reprise | Microsoft Azure"
    description="Cet article traite des éléments de conception de réseau Azure récupération de Site"
    services="site-recovery"
    documentationCenter=""
    authors="prateek9us"
    manager="jwhit"
    editor=""/>

<tags
    ms.service="site-recovery"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="storage-backup-recovery"
    ms.date="09/19/2016"
    ms.author="pratshar"/>

#  <a name="designing-your-network-infrastructure-for-disaster-recovery"></a>Conception de votre infrastructure réseau de sinistre

Cet article est dirigé vers informaticiens qui sont chargé de l’architecture, mise en œuvre et prenant en charge continuité des activités et infrastructure de reprise (BCDR) et qui souhaitent tirer parti de Microsoft Azure Site de récupération automatique pour prendre en charge et améliorer leurs services BCDR. Ce document traite des considérations pratiques pour le déploiement de serveur de System Center Machine virtuelle Manager, les avantages et inconvénients de sous-réseaux étirées par rapport à basculement sous-réseau et comment structurer sinistre aux sites virtuels dans Microsoft Azure.

## <a name="overview"></a>Vue d’ensemble

[Azure Site de récupération automatique](https://azure.microsoft.com/services/site-recovery/) est un service Microsoft Azure qui orchestre la protection et la restauration de vos applications virtualisées à des fins commerciales continuité urgence récupération (BCDR). Ce document est destiné à guider le lecteur au processus de création de réseaux, en mettant l’accent sur la conception des plages d’adresses IP et sous-réseaux sur le site de reprise, lors de la réplication des machines virtuelles (machines virtuelles) à l’aide de la récupération de Site.

En outre, cet article explique comment récupération de Site permet de conception et implémentation d’un centre de données virtuel multisite pour prendre en charge les services BCDR au moment de test ou de sinistre.

Dans un environnement où tout le monde attend 24/7 connectivity, il est plus important que jamais de maintenir votre infrastructure et les applications vers le haut et en cours d’exécution. L’objectif de continuité des activités et récupération d’urgence (BCDR) consiste à restaurer les composants défaillants afin que l’organisation peut reprendre rapidement le fonctionnement normal. Stratégies de récupération de traiter les événements peu, dévastateurs le développement sont très difficile. Il s’agit en raison de la difficulté intrinsèque à de prédire l’avenir, notamment par rapport aux événements improbable et le coût élevé pour fournir des mesures adéquates de protection contre des catastrophes. 

Crucial pour BCDR planification, les temps de récupération (RTO) et objectif de Point de récupération (RPO) doivent être définis dans le cadre d’un plan de récupération. Lorsqu’un sinistre centre de données du client, à l’aide de récupération de Site Azure, clients can rapidement (faible RTO) mettre en ligne leurs machines virtuelles répliquées situées dans le centre de données secondaire ou Microsoft Azure avec perte de données minimale (RPO faible). 

Basculement est rendu possible par la récupération automatique du système qui initialement copie désignés machines virtuelles à partir du centre de données principal dans le centre de données secondaire ou dans Azure (selon le scénario) et actualise régulièrement les réplicas. Lors de la planification de l’infrastructure, conception du réseau doit être considérée comme critique potentiel qui peut empêcher vous de l’entreprise réunion RTO et RPO objectifs.  

Lors de la planification des administrateurs à déployer une solution de récupération d’urgence, l’une des questions clées dans leur esprit est comment la machine virtuelle serait accessible après que le basculement est terminé. Récupération automatique du système permet à l’administrateur choisir le réseau auquel une machine virtuelle est connectée à après le basculement. Si le site principal est géré par un serveur VMM cela est accompli à l’aide de mappage du réseau. Pour plus d’informations, consultez la section [préparer mappage réseau](site-recovery-network-mapping.md) .

Lors de la conception du réseau pour le site de récupération, l’administrateur a deux possibilités :

- Utiliser une autre plage d’adresses IP pour le réseau lors du site de récupération. Dans ce scénario, la machine virtuelle après le basculement recevrez une nouvelle adresse IP et l’administrateur doit effectuer une mise à jour DNS. En savoir plus sur la façon d’y parvenir le DNS mettre à jour [ici](site-recovery-vmm-to-vmm.md#test-your-deployment) 
- Utiliser la même plage d’adresses IP pour le réseau sur le site de. Dans certains scénarios administrateurs préfèrent conserver les adresses IP qu’ils ont sur le site principal même après le basculement. Dans un scénario normal un administrateur a mettre à jour les itinéraires pour indiquer le nouvel emplacement des adresses IP. Mais dans le scénario où un réseau local virtuel étiré est déployé entre le principal et les sites de récupération, en conservant les adresses IP pour les ordinateurs virtuels devient une option intéressante. Tout en conservant la même adresse IP adresses simplifie le processus de récupération en tirant absent (e) n’importe quel réseau post-basculement étapes.


Lors de la planification des administrateurs à déployer une solution de récupération d’urgence, l’une des questions clées leur esprit est comment les applications seront inaccessibles après que le basculement est terminé. Les applications modernes sont presque toujours dépendantes de mise en réseau dans une certaine mesure, donc physiquement déplacement de qu'un service à partir d’un site à l’autre représente un défi mise en réseau. Il existe deux manières principales ce problème est traité en solutions de reprise. La première approche consiste à gérer des adresses IP fixes. Malgré les services déplacement et les serveurs d’hébergement en cours dans différents emplacements physiques, les applications tirer la configuration des adresses IP avec eux vers le nouvel emplacement. La deuxième approche implique de complètement modifier l’adresse IP lors de la transition vers le site récupéré. Chaque approche a plusieurs variations de mise en œuvre qui sont résumées ci-dessous.

Lors de la conception du réseau pour le site de récupération, l’administrateur a deux possibilités :

## <a name="option-1-retain-ip-addresses"></a>Option 1 : Conserver les adresses IP 

À partir d’un point de vue processus de récupération d’urgence, à l’aide des adresse IP fixe adresses semble être la méthode la plus simple pour mettre en œuvre, mais il comporte un certain nombre de défis potentiels qui rendent l’approche populaire dans la pratique. Azure récupération de Site permet de conserver les adresses IP dans tous les scénarios. Avant un décide de conserver les adresses IP, il convient réflexion appropriée aux contraintes qu'il impose sur les fonctionnalités de basculement. Examinons les facteurs qui peuvent vous aider à prendre une décision pour conserver les adresses IP, ou non. Il est possible de deux façons, en utilisant un sous-réseau étiré ou en effectuant un basculement sous-réseau complète.

### <a name="stretched-subnet"></a>Sous-réseau étirée

Ici le sous-réseau est rendu disponible en principal et DR emplacements. Pour simplifier, cela signifie que vous pouvez déplacer un serveur et sa configuration IP (couche 3) sur le deuxième point et le réseau achemine le trafic vers le nouvel emplacement automatiquement. Cela est très facile à traiter du point de vue server mais présente un certain nombre de défis :

- Du point de vue Layer 2 (couche de liaison de données), il faudra équipement réseau pouvant gérer un réseau local virtuel étiré, mais cela est devenu inférieure d’un problème qu’elle est maintenant largement disponible. Le problème de deuxième et plus difficile qui est en étirant le réseau local virtuel le domaine d’erreur potentielle est étendu aux deux sites, pour l’essentiel devenir un point de défaillance unique. Alors que c’est un peu probable, c’est arrivé qu’une diffusion vague de démarré, mais n’a pas pu isolé. Nous ont trouvé mixtes avis sur ce dernier problème et avez vu nombreuses mises en œuvre réussies ainsi que de « nous allez jamais implémenter cette technologie ici ».
- Étirée ne constitue pas possible si vous utilisez Microsoft Azure que le site DR.


### <a name="subnet-failover"></a>Basculement sous-réseau

Il est possible d’implémenter le basculement sous-réseau pour obtenir les avantages de la solution sous-réseau étirée ci-dessus sans en étirant le sous-réseau sur plusieurs sites. Ici n’importe quel sous-réseau donné devrait être présent sur un Site 1 ou 2 du Site, mais jamais sur les deux sites simultanément. Pour maintenir l’espace d’adressage IP en cas de basculement, il est possible organiser les par programme pour l’infrastructure routeur pour déplacer les sous-réseaux d’un site à l’autre. Dans un scénario de basculement que déplacent les sous-réseaux avec associé protégée machines virtuelles. Le principal inconvénient de cette approche est en cas de panne, que vous devez déplacer le sous-réseau entier, qui peut être OK mais peut affecter les considérations de précision basculement. 

Examinons à présent comment une entreprise fictive nommée Contoso est en mesure de répliquer ses machines virtuelles vers un emplacement de récupération tout en échec sur le sous-réseau entière. Nous allons tout d’abord étudier comment Contoso est chargée de gérer leurs sous-réseaux tandis que la réplication des machines virtuelles entre deux locaux emplacements, puis nous aborderons sous-réseau basculement fonctionne quand [Qu'azure est utilisé comme le site de reprise](#failover-to-azure).

#### <a name="failover-to-a-secondary-on-premises-site"></a>Basculer vers un site secondaire en local

Examinons un scénario où nous voulons conserver l’adresse IP de chacun des ordinateurs virtuels et basculement le sous-réseau complète ensemble. Le site principal dispose d’applications en cours d’exécution dans sous-réseau 192.168.1.0/24. Lorsque le basculement se produit, tous les ordinateurs virtuels qui font partie de ce sous-réseau est alors basculées vers le site de récupération, mais de conserver leurs adresses IP. Itinéraires doivent être modifiées en conséquence pour refléter le fait que tous les ordinateurs virtuels appartenant à sous-réseau 192.168.1.0/24 ont été déplacés vers le site de récupération. 

Dans l’illustration suivante itinéraires entre site principal et récupération site, troisième site et site principal et troisième site et récupération doivent être modifiées en conséquence. 

Les images suivantes indique les sous-réseaux avant le basculement. Sous-réseau 192.168.0.1/24 est actif sur le Site principal avant le basculement et devient actif du Site récupération après le basculement 

![Avant le basculement](./media/site-recovery-network-design/network-design2.png)

Avant le basculement


L’image ci-dessous montre les réseaux et sous-réseaux après le basculement.
    
![Après le basculement](./media/site-recovery-network-design/network-design3.png)

Après le basculement

Dans votre site secondaire est en local et vous utilisez un serveur VMM pour gérer, puis lorsque vous activez la protection par pour une machine virtuelle spécifique, récupération automatique du système affecte des ressources réseau selon le flux de travail suivant :

- Récupération automatique du système attribue une adresse IP pour chaque interface réseau sur l’ordinateur virtuel à partir du pool d’adresses IP défini dans le réseau approprié pour chaque instance de System Center VMM.
- Si l’administrateur définit le même pool d’adresses IP pour le réseau sur le site de récupération que celle du pool d’adresses IP du réseau sur le site principal, tandis que l’allocation de l’adresse IP de la machine virtuelle de réplica récupération automatique du système affecte la même adresse IP que celle de la machine virtuelle principale.  L’adresse IP est réservé dans VMM mais pas défini comme basculement IP. Failover IP définie juste avant le basculement.

![Conserver l’adresse IP](./media/site-recovery-network-design/network-design4.png)
    
Figure 5

Figure 5 affiche les paramètres TCP/IP basculement de la machine virtuelle réplica (sur la console Hyper-V). Ces paramètres seraient remplies juste avant le démarrage de la machine virtuelle après un basculement

Si la même adresse IP n’est pas disponible, récupération automatique du système affecte certaines autres adresses IP disponibles à partir du pool d’adresses IP défini. 

Une fois que la machine virtuelle est activée pour la protection que ci-après exemple de script vous permettent de vérifier l’adresse IP qui a été affecté à la machine virtuelle. La même adresse IP serait définir en tant que Failover IP et affectée à la machine virtuelle au moment de basculement :

        $vm = Get-SCVirtualMachine -Name <VM_NAME>
        $na = $vm[0].VirtualNetworkAdapters>
        $ip = Get-SCIPAddress -GrantToObjectID $na[0].id
        $ip.address  

>[AZURE.NOTE] Dans le scénario où machines virtuelles utiliser DHCP, la gestion des adresses IP est entièrement en dehors du contrôle de récupération automatique du système. Un administrateur doit s’assurer que celui-ci servant les adresses IP sur le site de récupération peut servir de la même plage qui du site principal.

#### <a name="failover-to-azure"></a>Basculer vers Azure

Azure Site récupération automatique permet à Microsoft Azure à utiliser comme un site de reprise pour vos machines virtuelles. Dans ce cas, vous devrez traiter une contrainte plus. 

Examinons un scénario où une société fictive nommée bosquet a infrastructure locale d’hébergement leur gamme d’applications d’entreprise, et ils hébergent leurs applications mobiles sur Azure. Connectivité entre machines virtuelles banque de Woodgrove dans les serveurs Azure et en local est fournie par un site à site (S2S) privé réseau virtuel (VPN). S2S VPN permet de réseau virtuel du bosquet dans Azure considéré comme une extension du réseau local de Woodgrove Bank. Cette communication est activée par S2S VPN entre bosquet et Azure réseau virtuel. Woodgrove souhaite maintenant de récupération automatique du système permet de répliquer ses charges de travail en cours d’exécution dans son centre de données vers Azure. Cette option répond aux besoins de Woodgrove, qui souhaite une option DR économique et est en mesure de stocker les données dans les environnements de cloud public. Woodgrove doit prendre en compte avec les applications et configurations qui dépendent codé en dur des adresses IP, par conséquent, ils ont une obligation pour conserver les adresses IP pour leurs applications après le basculement vers Azure.

Woodgrove a décidé d’affecter des adresses IP à partir de la plage d’adresses IP (172.16.1.0/24, 172.16.2.0/24) à ses ressources en cours d’exécution dans Azure.

Pour Woodgrove puissent répliquer ses machines virtuelles vers Azure tout en conservant les adresses IP, un réseau virtuel Azure doit être créé. Il doit être une extension du réseau local pour que les applications puissent basculement à partir du site local vers Azure en toute transparence. Azure vous permet d’ajouter à un site ainsi que point-à-site connectivité VPN aux réseaux virtuels créés dans Azure. Lorsque vous configurez votre connexion à un site, Azure réseau vous permet à acheminer le trafic vers l’emplacement local (Azure appelle réseau local) uniquement si la plage d’adresses IP est la différence entre la plage d’adresses IP en local, car Azure ne prend pas en charge les sous-réseaux étirement.  Cela signifie que si vous avez un 192.168.1.0/24 sous-réseau local, vous ne pouvez pas ajouter un 192.168.1.0/24 réseau local dans le réseau Azure. Cela est dû au fait que Azure ne sait pas qu’il n’y a aucune machines virtuelles actives dans le sous-réseau et que le sous-réseau est créé uniquement à des fins DR. Pour pouvoir correctement router le trafic réseau déconnecter un réseau Azure que les sous-réseaux dans le réseau et le réseau local ne doivent pas entrer en conflit. 

![Avant le basculement sous-réseau](./media/site-recovery-network-design/network-design7.png)

Avant le basculement

Pour mieux vous Woodgrove répondre à leurs besoins commerciaux, nous avons besoin de mettre en œuvre le flux de travail suivants :

- Création d’un réseau supplémentaire, appelez-le récupération réseau, où les machines virtuelles basculé serait créés.
- Pour vous assurer que l’adresse IP pour une machine virtuelle est conservée après un basculement, accédez à l’onglet configurer sous propriétés de la machine virtuelle, spécifiez la même adresse IP que la machine virtuelle a en local, puis cliquez sur Enregistrer. Lorsque la machine virtuelle est basculée, récupération de Site Azure affecte la période d’enquête fournie à la machine virtuelle. 

![Propriétés du réseau](./media/site-recovery-network-design/network-design8.png)

Une fois que le basculement se déclenche et les machines virtuelles sont créées dans le réseau de récupération avec l’adresse IP de votre choix, connectivité à ce réseau peut être établie à l’aide d’un [Vnet à Vnet connexion](../vpn-gateway/virtual-networks-configure-vnet-to-vnet-connection.md). Si nécessaire cette action peut être l’objet d’un script.  Comme indiqué dans la section précédente sur basculement sous-réseau, même en cas de basculement vers Azure, itinéraires devra être modifiées en conséquence afin de refléter que ce 192.168.1.0/24 a maintenant déplacé vers Azure. 

![Après le basculement sous-réseau](./media/site-recovery-network-design/network-design9.png)

Après le basculement

Si vous n’avez pas un « réseau Azure » comme indiqué dans l’image ci-dessus. Vous pouvez créer une connexion à un site vpn entre votre « Site principal » et « Récupération réseau » après le basculement.  


## <a name="option-2-changing-ip-addresses"></a>Option 2 : Modifier les adresses IP

Cette approche pourrait être plus fréquentes basé sur ce que nous avons vu. Il prend la forme de modification de l’adresse IP de chaque machine virtuelle impliqué dans le basculement. Inconvénient de cette approche nécessite le réseau entrant pour « plus » que l’application qui a été en IPx est maintenant en IPy. Même si IPx et IPy sont les noms logiques, entrées DNS doivent généralement être modifiées ou vidé dans l’ensemble du réseau et mis en cache les entrées dans les tables de réseau doivent être mis à jour ou vidé, donc un temps d’arrêt peut se produire selon la façon dont l’infrastructure DNS a été configurée. Être atténuer en utilisant les valeurs TTL faibles dans le cas d’applications intranet et [Azure le trafic gestionnaire avec récupération automatique du système](http://azure.microsoft.com/blog/2015/03/03/reduce-rto-by-using-azure-traffic-manager-with-azure-site-recovery/) pour les applications internet en fonction de ces problèmes

### <a name="changing-the-ip-addresses---illustration"></a>Modification des adresses IP - Illustration

Examinons le scénario où vous prévoyez d’utiliser différente adresses IP sur le serveur principal et les sites de récupération. Dans l’exemple suivant, nous également ont permis une troisième du site à partir de l’endroit où les applications hébergeant sur moniteur principal ou récupération site sont accessibles.

![Autre IP - avant le basculement](./media/site-recovery-network-design/network-design10.png)

Figure 11

Dans la Figure 11, il existe certaines applications hébergées dans sous-réseau 192.168.1.0/24 de sous-réseau sur le site principal, et qu’ils ont été configurés pour poser sur le site de récupération de sous-réseau 172.16.1.0/24 après un basculement. Itinéraires de connexions/réseau VPN ont été correctement configurés pour que les trois sites puissent accéder à l’autre.
 
Comme figure 12 s’affiche, après le basculement d’une ou plusieurs applications, ils seront restaurés dans le sous-réseau récupération. Dans ce cas nous ne sommes pas limités à basculement le sous-réseau entier en même temps. Aucune modification n’est nécessaires pour reconfigurer itinéraires VPN ou au réseau. Basculement et certaines mises à jour DNS va s’assurer que les applications restent accessibles. Si le DNS est configuré pour autoriser les mises à jour dynamiques machines virtuelles serait inscrire eux-mêmes à l’aide de la nouvelle période d’enquête une fois qu’elle commence après un basculement. 

![Autre IP - après le basculement](./media/site-recovery-network-design/network-design11.png)

Figure 12

Après avoir basculer la machine virtuelle réplica peut-être une adresse IP qui n’est pas identique à l’adresse IP de l’ordinateur virtuel principal. Machines virtuelles met à jour le serveur DNS qu’ils utilisent mise en route. Entrées DNS doivent généralement être modifiées ou vidé dans l’ensemble du réseau et mis en cache les entrées dans les tables de réseau doivent être mis à jour ou vidé, afin qu’elle ne soit pas dialogue à être confronté à temps d’arrêt pendant que ces modifications ont lieu. Ce problème peut être réduit par :

- À l’aide des valeurs TTL faibles pour les applications intranet.
- À l’aide du Gestionnaire de trafic Azure avec récupération automatique du système pour internet, applications basées sur.
- En utilisant le script suivant au sein de votre plan de récupération pour mettre à jour le serveur DNS pour vous assurer une mise à jour opportune (le script n’est pas nécessaire si l’enregistrement DNS dynamique est configuré)

        string]$Zone,
        [string]$name,
        [string]$IP
        )
        $Record = Get-DnsServerResourceRecord -ZoneName $zone -Name $name
        $newrecord = $record.clone()
        $newrecord.RecordData[0].IPv4Address  =  $IP
        Set-DnsServerResourceRecord -zonename $zone -OldInputObject $record -NewInputObject $Newrecord


### <a name="changing-the-ip-addresses--dr-to-azure"></a>Modification des adresses IP – DR vers Azure

Le billet de blog [Installation de l’Infrastructure réseau pour Microsoft Azure comme un Site de reprise](http://azure.microsoft.com/blog/2014/09/04/networking-infrastructure-setup-for-microsoft-azure-as-a-disaster-recovery-site/) explique comment configurer l’infrastructure de réseau social Azure requise lors de la conservation des adresses IP n’est pas obligatoire. Il commence par qui décrit l’application et consultez ensuite sur Azure et puis se terminent par la procédure à suivre un basculement de test et un planifiée et configuration de mise en réseau local.



## <a name="next-steps"></a>Étapes suivantes

[Découvrez](site-recovery-network-mapping.md) comment récupération de Site cartes réseaux source et cible lorsqu’un serveur VMM est utilisé pour gérer le site principal. 

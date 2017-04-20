Cet article présente un ensemble de pratiques pour l’exécution d’une Windows machine virtuelle () sur Azure, attention à extensibilité élevées, disponibilité, facilité de gestion et de sécurité. 

> [AZURE.NOTE] Azure comporte deux modèles de déploiement différents : [Azure le Gestionnaire de ressources] [ resource-manager-overview] et classique. Cet article utilise le Gestionnaire de ressources, Microsoft recommande pour les nouveaux déploiements.

Nous ne recommandons d’utiliser une machine virtuelle unique pour les charges de travail, car il n’est pas le temps de niveau accord de service () pour les machines virtuelles unique sur Azure. Pour obtenir l’accord SLA, vous devez déployer plusieurs ordinateurs virtuels dans une [disponibilité définie][availability-set]. Pour plus d’informations, consultez [exécution de plusieurs ordinateurs Windows virtuels sur Azure][multi-vm]. 

## <a name="architecture-diagram"></a>Diagramme d’architecture

Mise en service un ordinateur virtuel dans Azure implique pièces mobiles plus que simplement la machine virtuelle elle-même. Il existe des éléments de calcul, mise en réseau et le stockage.

> Un document Visio incluant ce diagramme architecture est disponible au téléchargement sur le [Centre de téléchargement Microsoft][visio-download]. Ce diagramme est sur « cluster - page machine virtuelle unique.

![[0]][0]


- **Groupe de ressources.** Un [_groupe de ressources_] [ resource-manager-overview] est un conteneur qui conserve les ressources connexes. Créer un groupe de ressources pour mettre en attente les ressources de cet ordinateur virtuel.

- **VM**. Vous pouvez configurer une machine virtuelle à partir d’une liste d’images publiés ou à partir d’un fichier virtuel dur disque dur que vous téléchargez à Azure blob storage.

- **Disque du système d’exploitation.** Le disque du système d’exploitation est un disque dur virtuel stocké dans [le stockage Azure][azure-storage]. Cela signifie qu’il persiste même si l’ordinateur hôte s’arrête.

- **Disque temporaire.** La machine virtuelle est créée avec un disque temporaire (la `D:` lecteur de Windows). Ce disque est stocké sur un lecteur physique sur l’ordinateur hôte. Il n’est _pas_ enregistré dans le stockage Azure et peuvent disparaître au cours de redémarrage et d’autres événements de cycle de vie machine virtuelle. Utilisez ce disque uniquement pour les données temporaires, tels que des fichiers de page ou de remplacement.

- **Disques de données.** Un [disque de données] [ data-disk] est un disque dur virtuel permanent utilisé pour les données d’application. Disques de données sont stockées dans le stockage Azure, tels que le disque du système d’exploitation.

- **Réseau virtuel (VNet) et sous-réseau.** Chaque machine virtuelle dans Azure est déployé dans un VNet, qui est divisée en sous-réseaux.

- **Adresse IP publique.** Une adresse IP publique est nécessaire pour communiquer avec la machine virtuelle&mdash;par exemple, via le Bureau à distance (RDP).

- **Interface réseau (carte réseau)**. La carte réseau permet la machine virtuelle communiquer avec le réseau virtuel.

- **Groupe de sécurité réseau (NSG)**. La [NSG] [ nsg] est utilisé pour autoriser ou refuser le trafic réseau vers le sous-réseau. Vous pouvez associer un NSG avec une carte réseau individuelle ou avec un sous-réseau. Si vous l’associer à un sous-réseau, les règles NSG s’appliquent à tous les ordinateurs virtuels dans ce sous-réseau.
 
- **Diagnostics.** Journalisation des diagnostics est cruciale pour la gestion et résolution des problèmes de la machine virtuelle.

## <a name="recommendations"></a>Recommandations

Azure offre de nombreuses ressources différentes et des types de ressources, de sorte que cette architecture de référence peut être mis en service de différentes manières. Nous vous fournissons un modèle Azure le Gestionnaire de ressources pour installer l’architecture de référence qui suit ces recommandations. Si vous choisissez de créer votre propre architecture de référence, vous devez suivre ces recommandations à moins d’avoir un besoin spécifique une recommandation ne tient pas.

### <a name="vm-recommendations"></a>Recommandations machine virtuelle

Nous vous recommandons de la série DS et GS car ces tailles ordinateur prend en charge le [Stockage Premium][premium-storage]. Sélectionnez une de ces tailles machine à moins d’avoir une charge de travail spécialisée telles que High performance computing. Pour plus d’informations, voir [tailles machine virtuelle][virtual-machine-sizes]. Lorsque vous déplacez une charge de travail existant vers Azure, précédez la taille de la mémoire virtuelle qui correspond à vos serveurs sur site le plus proche. Puis mesure les performances de votre charge de travail réel en ce qui concerne l’UC, la mémoire et disque sorties opérations par seconde (sorties par), puis ajustez la taille si nécessaire. En outre, si vous avez besoin de plusieurs cartes réseau, sachez de la limite de carte réseau pour chaque taille.  

Lorsque vous configurez la machine virtuelle et autres ressources, vous devez spécifier un emplacement. En règle générale, choisissez un emplacement le plus proche de vos utilisateurs internes ou des clients. Toutefois, pas toutes tailles machine virtuelle peuvent être disponibles dans tous les emplacements. Pour plus d’informations, consultez [Services par région][services-by-region]. Pour répertorier les tailles de mémoire virtuelle disponibles dans un emplacement donné, exécutez la commande Azure interface de ligne (commande) suivante :

```
    azure vm sizes --location <location>
```

Pour plus d’informations sur le choix d’une image de machine virtuelle publiée, voir [Naviguer et sélectionnez machine virtuelle Azure images][select-vm-image].

### <a name="disk-and-storage-recommendations"></a>Recommandations disque et le stockage

Pour de meilleures performances e/s disque, nous vous recommandons de [Stockage Premium][premium-storage], qui stocke les données sur disques (SSDs). Coût basé sur la taille du disque mis en service. E/s et débit également dépendent de la taille de disque, donc lorsque vous configurez un disque, tenez compte des trois facteurs tous les (capacité, sorties par et débit). 

Un seul compte de stockage peut prendre en charge des machines virtuelles 1 à 20.

Ajouter un ou plusieurs disques de données. Lorsque vous créez un nouveau disque dur virtuel, il n’est pas formatée. Connectez-vous à la machine virtuelle pour mettre en forme le disque.

Si vous avez un grand nombre de disques de données, n’oubliez pas des limites e/s total du compte de stockage. Pour plus d’informations, voir [Limites de disque Machine virtuelle][vm-disk-limits].

Pour de meilleures performances, créez un compte de stockage distincte pour mettre en attente les journaux de diagnostic. Un compte de stockage localement redondantes (LRS) standard est suffisant pour les journaux de diagnostic.

Si possible, installer des applications sur un disque de données, n’est pas le disque du système d’exploitation. Toutefois, certaines applications héritées devrez peut-être installer des composants sur le lecteur C:. Dans ce cas, vous pouvez [redimensionner le disque OS] [ resize-os-disk] à l’aide de PowerShell.

### <a name="network-recommendations"></a>Recommandations de réseau

L’adresse IP peut être statique ou dynamique. La valeur par défaut est dynamique.

- Réserver une [adresse IP statique] [ static-ip] si vous avez besoin d’une adresse IP fixe qui ne change pas &mdash; par exemple, si vous avez besoin créer un enregistrement A dans le système DNS ou besoin l’adresse IP pour être autorisés.

- Vous pouvez également créer un nom de domaine complet (FQDN) pour l’adresse IP. Vous pouvez alors inscrire un [enregistrement CNAME] [ cname-record] dans le système DNS qui pointe vers le nom de domaine complet. Pour plus d’informations, voir [créer un nom de domaine complet dans le portail Azure][fqdn].

Tous les NSGs contiennent un ensemble de [règles par défaut][nsg-default-rules], y compris une règle qui bloque tout le trafic Internet entrant. Les règles par défaut ne peut pas être supprimés, mais d’autres règles peuvent les remplacer. Pour activer le trafic Internet, créer des règles qui autorisent le trafic entrant vers des ports spécifiques &mdash; par exemple, le port 80 pour HTTP.  

Pour activer RDP, ajoutez une règle NSG qui autorise le trafic entrant vers le port TCP 3389.

## <a name="scalability-considerations"></a>Considérations relatives à l’extensibilité élevées

Vous pouvez mettre à l’échelle une machine virtuelle vers le haut ou vers le bas en [modifiant la taille de la mémoire virtuelle][vm-resize]. 

Pour évoluer horizontalement, placez deux ou plusieurs machines virtuelles dans une disponibilité définir derrière un équilibrage de charge. Pour plus d’informations, consultez [exécution de plusieurs ordinateurs Windows virtuels sur Azure][multi-vm].

## <a name="availability-considerations"></a>Considérations relatives à la disponibilité

Comme indiqué ci-dessus, il n’existe pas de SLA pour une machine virtuelle unique. Pour obtenir l’accord SLA, vous devez déployer plusieurs ordinateurs virtuels dans un jeu de disponibilité.

Votre ordinateur virtuel peut-être être affecté par [l’opération de maintenance planifiée] [ planned-maintenance] ou [maintenance planifiée][manage-vm-availability]. Vous pouvez utiliser [machine virtuelle redémarrer journaux] [ reboot-logs] pour déterminer si un redémarrage machine virtuelle a été causé par la maintenance planifiée.

Disques durs virtuels par reposent sur [Le stockage Azure][azure-storage], qui est répliqué de durabilité et de disponibilité.

Pour protéger contre la perte de données accidentelle en fonctionnement normal (par exemple, en raison d’une erreur de l’utilisateur), vous devez également implémenter des sauvegardes point-à-temps, l’utilisation [d’objets blob instantanés] [ blob-snapshot] ou un autre outil.

## <a name="manageability-considerations"></a>Considérations relatives à la facilité de gestion

**Groupes de ressources.** Placer les ressources étroitement qui partagent le même cycle de vie dans un même [groupe de ressources][resource-manager-overview]. Groupes de ressources permettent à déployer et surveiller les ressources en tant que groupe et opérationnelle des coûts par groupe de ressources de facturation. Vous pouvez également supprimer des ressources comme un ensemble, ce qui est très utile pour les déploiements de test. Donnez des noms parlants à ressources. Qui simplifie localiser une ressource spécifique et comprendre son rôle. Voir [Conventions d’appellation recommandées pour les ressources Azure][naming conventions].

**Diagnostics machine virtuelle.** Activer surveillance et les diagnostics, y compris les mesures de fonctionnement de base, les journaux d’infrastructure de diagnostics et les [diagnostics de démarrage][boot-diagnostics]. Démarrage diagnostics peuvent vous aider à identifier un échec de démarrage si votre ordinateur virtuel Obtient dans un état non démarrage. Pour plus d’informations, voir [Activer la surveillance et les diagnostics de][enable-monitoring]. Utilisation de la [Collection de sites Azure journal] [ log-collector] extension pour collecter les journaux de plateforme Azure et les télécharger vers le stockage Azure.   

La commande suivante infrastructure du langage commun permet de diagnostics :

```
    azure vm enable-diag <resource-group> <vm-name>
```

**Arrêt d’un ordinateur virtuel.** Azure établit une distinction entre les États « Arrêt » et « Deallocated ». Vous êtes chargé lorsque l’état de la machine virtuelle est « arrêté ». Vous ne seront pas imputés lorsque la machine virtuelle libéré.

Pour libérer une machine virtuelle, utilisez la commande CLI suivante :

```
    azure vm deallocate <resource-group> <vm-name>
```

Le bouton **Arrêter** dans le portail Azure libère également la machine virtuelle. Toutefois, si vous arrêtez par le biais du système d’exploitation alors que connecté, la machine virtuelle est arrêtée mais _pas_ libéré, donc vous devrez toujours payer.

**Suppression d’un ordinateur virtuel.** Si vous supprimez une machine virtuelle, les disques durs virtuels ne sont pas supprimés. Cela signifie que vous pouvez supprimer en toute sécurité la machine virtuelle sans perte de données. Toutefois, vous devrez toujours payer pour le stockage. Pour supprimer le disque dur virtuel, supprimez le fichier de [stockage d’objets blob][blob-storage].

Pour éviter toute suppression accidentelle, utilisez un [verrouillage de la ressource] [ resource-lock] pour verrouiller les ressource entière groupe ou verrouiller ressources individuelles, telles que la machine virtuelle. 

## <a name="security-considerations"></a>Considérations sur la sécurité

Utiliser le [Centre de sécurité Azure] [ security-center] pour obtenir une vue centrale de l’état de sécurité de vos ressources Azure. Centre de sécurité surveille problèmes potentiels de sécurité comme système met à jour, contre les logiciels malveillants et fournit une image complète de l’état de sécurité de votre déploiement. 

- Centre de sécurité est configuré pour chaque abonnement Azure. Activer la collecte de données de sécurité comme décrit dans [Utilisation du centre de sécurité].

- Lors de la collecte de données est activée, le centre de sécurité analyse automatiquement les machines virtuelles sous cet abonnement.

**Gestion des correctifs.** Si activé, centre de sécurité vérifie si la sécurité et les mises à jour critiques sont manquantes. Utiliser les [paramètres de stratégie de groupe] [ group-policy] sur l’ordinateur virtuel pour activer les mises à jour automatique du système.

**Contre les logiciels malveillants.** Si activé, centre de sécurité vérifie si contre les logiciels malveillants logiciel est installé. Vous pouvez également utiliser le centre de sécurité d’installer des logiciels contre les logiciels malveillants à l’intérieur du portail Azure.

Utiliser [le contrôle d’accès basé sur un rôle] [ rbac] (RBAC) pour contrôler l’accès aux ressources Azure que vous déployez. RBAC vous permet d’affecter des rôles d’autorisations aux membres de votre équipe DevOps. Par exemple, le rôle de lecteur peut afficher les ressources Azure mais pas créer, gérer ou supprimez-les. Certains rôles sont spécifiques aux types de ressources Azure particulier. Par exemple, le rôle de collaborateur Machine virtuelle peut redémarrer ou libérer une machine virtuelle, réinitialiser le mot de passe administrateur, créer une nouvelle machine virtuelle et ainsi de suite. Autres [rôles RBAC intégrés] [ rbac-roles] qui peuvent vous être utiles pour cette architecture de référence comprennent [DevTest ateliers utilisateur] [ rbac-devtest] et [Collaboration réseau][rbac-network]. Un utilisateur peut être affecté aux rôles plusieurs, et vous pouvez créer des rôles personnalisés pour encore plus permissions affinées.

> [AZURE.NOTE] RBAC ne limite pas les actions qu’un utilisateur connecté à un ordinateur virtuel peut effectuer. Ces autorisations sont déterminées par le type de compte sur le système d’exploitation invité.   

Pour réinitialiser le mot de passe d’administrateur local, exécutez la `vm reset-access` commande Azure infrastructure du langage commun.

```
    azure vm reset-access -u <user> -p <new-password> <resource-group> <vm-name>
```

Utiliser [des journaux d’audit] [ audit-logs] pour afficher la mise en service des actions et autres événements machine virtuelle.

Prendre en compte [Azure disque chiffrement] [ disk-encryption] si vous avez besoin chiffrer les disques du système d’exploitation et les données. 

## <a name="solution-deployment"></a>Déploiement de solution

Un [déploiement] [ github-folder] architecture qui montre les meilleures pratiques suivantes est disponible pour une référence. Cette architecture de référence inclut un réseau virtuel (VNet), groupe de sécurité réseau (NSG) et une seule machine virtuelle ().

Il existe plusieurs manières de déployer cette architecture de référence. Le plus simple consiste à suivre les étapes suivantes : 

1. Cliquez avec le bouton droit sur le bouton ci-dessous, puis sélectionnez soit « ouvrir le lien dans un nouvel onglet » ou « Ouvrir le lien dans une nouvelle fenêtre ».  
[![Déploiement d’Azure](../articles/guidance/media/blueprints/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fmspnp%2Freference-architectures%2Fmaster%2Fguidance-compute-single-vm%2Fazuredeploy.json)

2. Une fois que le lien a ouvert dans le portail Azure, vous devez entrer des valeurs pour certains paramètres : 
    - Le nom du **groupe de ressources** est déjà défini dans le fichier de paramètres : sélectionnez **Créer un nouveau** et entrez `ra-single-vm-rg` dans la zone de texte.
    - Sélectionnez la région dans la zone de liste déroulante **emplacement** .
    - Ne modifiez pas **Modèle racine Uri** ou les zones de texte **Paramètre racine Uri** .
    - Sélectionnez le **Type de système d’exploitation** dans la zone de liste déroulante, **windows**.
    - Passez en revue les termes et conditions, puis cliquez sur la case à cocher **que j’accepte les termes et conditions indiquées ci-dessus** .
    - Cliquez sur le bouton **achat** .

3. Attendez que le déploiement terminer.

4. Les fichiers de paramètres incluent un nom d’utilisateur administrateur codé en dur et le mot de passe, et il est vivement recommandé que vous modifiez immédiatement les deux. Cliquez sur la machine virtuelle nommée `ra-single-vm0 `dans le portail Azure. Ensuite, cliquez sur **Réinitialiser votre mot de passe** dans la carte de **Support + résolution des problèmes** . Sélectionnez **Réinitialiser votre mot de passe** dans la zone de liste déroulante **Mode** , puis sélectionnez un nouveau **nom d’utilisateur** et **mot de passe**. Cliquez sur le bouton de **mise à jour** pour conserver le nouveau nom d’utilisateur et mot de passe.

Pour plus d’informations sur les autres façons de déployer cette architecture de référence, voir le fichier Lisez-moi dans le [Guide-unique-machine virtuelle][github-folder]] Github dossier. 

## <a name="customize-the-deployment"></a>Personnaliser le déploiement

Si vous devez modifier le déploiement conformément à vos besoins, suivez les instructions dans le [fichier Lisez-moi][github-folder]. 

## <a name="next-steps"></a>Étapes suivantes

Afin que le [SLA pour les Machines virtuelles] [ vm-sla] pour appliquer, vous devez déployer deux ou plusieurs instances d’une série de disponibilité. Pour plus d’informations, consultez [exécution de plusieurs ordinateurs virtuels sur Azure][multi-vm].

<!-- links -->

[audit-logs]: https://azure.microsoft.com/en-us/blog/analyze-azure-audit-logs-in-powerbi-more/
[availability-set]: ../articles/virtual-machines/virtual-machines-windows-create-availability-set.md
[azure-cli]: ../articles/virtual-machines-command-line-tools.md
[azure-storage]: ../articles/storage/storage-introduction.md
[blob-snapshot]: ../articles/storage/storage-blob-snapshots.md
[blob-storage]: ../articles/storage/storage-introduction.md
[boot-diagnostics]: https://azure.microsoft.com/en-us/blog/boot-diagnostics-for-virtual-machines-v2/
[cname-record]: https://en.wikipedia.org/wiki/CNAME_record
[data-disk]: ../articles/virtual-machines/virtual-machines-windows-about-disks-vhds.md
[disk-encryption]: ../articles/security/azure-security-disk-encryption.md
[enable-monitoring]: ../articles/monitoring-and-diagnostics/insights-how-to-use-diagnostics.md
[fqdn]: ../articles/virtual-machines/virtual-machines-windows-portal-create-fqdn.md
[github-folder]: http://github.com/mspnp/reference-architectures/tree/master/guidance-compute-single-vm
[group-policy]: https://technet.microsoft.com/en-us/library/dn595129.aspx
[log-collector]: https://azure.microsoft.com/en-us/blog/simplifying-virtual-machine-troubleshooting-using-azure-log-collector/
[manage-vm-availability]: ../articles/virtual-machines/virtual-machines-windows-manage-availability.md
[multi-vm]: ../articles/guidance/guidance-compute-multi-vm.md
[naming conventions]: ../articles/guidance/guidance-naming-conventions.md
[nsg]: ../articles/virtual-network/virtual-networks-nsg.md
[nsg-default-rules]: ../articles/virtual-network/virtual-networks-nsg.md#default-rules
[planned-maintenance]: ../articles/virtual-machines/virtual-machines-windows-planned-maintenance.md
[premium-storage]: ../articles/storage/storage-premium-storage.md
[rbac]: ../articles/active-directory/role-based-access-control-what-is.md
[rbac-roles]: ../articles/active-directory/role-based-access-built-in-roles.md
[rbac-devtest]: ../articles/active-directory/role-based-access-built-in-roles.md#devtest-lab-user
[rbac-network]: ../articles/active-directory/role-based-access-built-in-roles.md#network-contributor
[reboot-logs]: https://azure.microsoft.com/en-us/blog/viewing-vm-reboot-logs/
[resize-os-disk]: ../articles/virtual-machines/virtual-machines-windows-expand-os-disk.md
[Resize-VHD]: https://technet.microsoft.com/en-us/library/hh848535.aspx
[Resize virtual machines]: https://azure.microsoft.com/en-us/blog/resize-virtual-machines/
[resource-lock]: ../articles/resource-group-lock-resources.md
[resource-manager-overview]: ../articles/azure-resource-manager/resource-group-overview.md
[security-center]: https://azure.microsoft.com/en-us/services/security-center/
[select-vm-image]: ../articles/virtual-machines/virtual-machines-windows-cli-ps-findimage.md
[services-by-region]: https://azure.microsoft.com/en-us/regions/#services
[static-ip]: ../articles/virtual-network/virtual-networks-reserved-public-ip.md
[storage-price]: https://azure.microsoft.com/pricing/details/storage/
[Utilisez le centre de sécurité]: ../articles/security-center/security-center-get-started.md#use-security-center
[virtual-machine-sizes]: ../articles/virtual-machines/virtual-machines-windows-sizes.md
[visio-download]: http://download.microsoft.com/download/1/5/6/1569703C-0A82-4A9C-8334-F13D0DF2F472/RAs.vsdx
[vm-disk-limits]: ../articles/azure-subscription-service-limits.md#virtual-machine-disk-limits
[vm-resize]: ../articles/virtual-machines/virtual-machines-linux-change-vm-size.md
[vm-sla]: https://azure.microsoft.com/en-us/support/legal/sla/virtual-machines/v1_0/
[0]: ./media/guidance-blueprints/compute-single-vm.png "Architecture de machine virtuelle Windows unique dans Azure"
[readme]: https://github.com/mspnp/reference-architectures/blob/master/guidance-compute-single-vm
[blocks]: https://github.com/mspnp/template-building-blocks

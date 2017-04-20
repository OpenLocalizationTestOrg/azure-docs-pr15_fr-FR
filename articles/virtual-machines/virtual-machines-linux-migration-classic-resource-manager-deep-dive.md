<properties
    pageTitle="Technique approfondie sur la plateforme prise en charge la migration de classique au Gestionnaire de ressources Azure | Microsoft Azure"
    description="Cet article effectue une techniques détaillées sur la migration de plateforme prise en charge des ressources à partir de classique au Gestionnaire de ressources Azure"
    services="virtual-machines-linux"
    documentationCenter=""
    authors="singhkays"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machines-linux"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-linux"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/12/2016"
    ms.author="kasing"/>

# <a name="technical-deep-dive-on-platform-supported-migration-from-classic-to-azure-resource-manager"></a>Technique approfondie sur la plateforme prise en charge la migration de classique au Gestionnaire de ressources Azure

Dans cet article, nous faire une techniques détaillées sur la migration à un niveau de ressource et de fonctionnalité pour vous aider à comprendre comment la plateforme migre des ressources à partir du modèle de déploiement classique vers le modèle de déploiement d’Azure le Gestionnaire de ressources. Pour plus d’informations, consultez l’article l’annonce service : [plates-formes prises en charge la migration de ressources IaaS classique au Gestionnaire de ressources Azure](virtual-machines-windows-migration-classic-resource-manager.md).

## <a name="detailed-guidance-on-migration-at-a-resource-and-feature-level"></a>Indications détaillées sur la migration à un niveau de ressource et de fonctionnalité

Vous pouvez trouver le classique et des représentations Gestionnaire de ressources des ressources dans le tableau suivant. Fonctionnalités et ressources non répertoriées dans ce tableau ne sont pas prises en charge pour le moment.

| Représentation classique| Représentation du Gestionnaire de ressources | Notes détaillées |
|--------------------------------------------------------|-------------------------------------------------|----------------------------------------------------------|
| Nom du service cloud                                     | Nom de DNS                                        | Lors de la migration, nous créons un nouveau groupe de ressources pour chaque service cloud avec le modèle de dénomination `<cloudservicename>-migrated`. Ce groupe de ressources contient toutes les ressources. Le nom du service cloud devient un nom DNS associé à l’adresse IP.                                                                                                                                                                                                    |   
| Machine virtuelle                                        | Machine virtuelle                                 | Propriétés machine virtuelle spécifiques sont migrées ne change pas. Notez que certaines informations osProfile, comme nom de l’ordinateur ne sont pas stockées dans le modèle de déploiement classique et restent vides après la migration.                                                                                                                                                                                                                                                                |   
| Ressources de disque attachés à la machine virtuelle                          | Disques implicites associés à la machine virtuelle                   | Disques ne sont pas modélisation comme des ressources de niveau supérieur dans le modèle de déploiement du Gestionnaire de ressources. Ils sont migrés en tant que disques implicites sous la machine virtuelle. À ce stade, nous prenons en charge uniquement les disques qui sont joints à un ordinateur virtuel. Pour activer la migration, le Gestionnaire de ressources machines virtuelles pouvez désormais utiliser des comptes de stockage classique. Cela permet des disques facilement migrer vers le modèle de directeur des ressources sans les mises à jour. |   
| Extensions de machine virtuelle                                          | Extensions de machine virtuelle                                   | Toutes les extensions de ressources, à l’exception des extensions XML, sont migrées à partir du modèle de déploiement classique.                                                                                                                                                                                                                                                                                                                                                                        |   
| Machine virtuelle certificats                           | Certificats dans l’archivage sécurisé clé Azure                 | Si un service cloud contient les certificats de service, la plateforme crée un archivage sécurisé clé Azure nouveau par service cloud et déplace les certificats dans l’archivage sécurisé clé. Les ordinateurs virtuels seront mise à jour pour référencer les certificats à partir de l’archivage sécurisé clé.                                                                                                                                                                                                                               |   
| Configuration de WinRM                                    | Configuration de WinRM sous osProfile             | Windows configuration de la gestion à distance est déplacée inchangé, dans le cadre de la migration.                                                                                                                                                                                                                                                                                                                                                                                            |   
| Propriété du jeu de disponibilité                              | Jeu de disponibilité des ressources                       | Spécification du jeu de disponibilité a été une propriété sur l’ordinateur virtuel dans le modèle de déploiement classique. Jeux de disponibilité devient une ressource de niveau supérieur dans le cadre de la migration. Notez que nous ne prend pas en charge la configuration suivante : plusieurs disponibilité définit par service cloud, ou disponibilité un ou plusieurs ensembles d’ainsi que des machines virtuelles qui ne sont pas dans n’importe quel disponibilité définie dans un service cloud.                                                                                |   
| Configuration du réseau sur un ordinateur virtuel                          | Interface réseau principale                       | Configuration du réseau sur un ordinateur virtuel est représentée par la ressource d’interface réseau principale après la migration. Machines virtuelles qui ne sont pas dans un réseau virtuel, l’adresse IP interne remplace lors de la migration.                                                                                                                                                                                                                                                            |  
| Plusieurs interfaces réseau sur un ordinateur virtuel                    | Interfaces réseau                              | Si une machine virtuelle avait plusieurs interfaces réseau associés, chaque interface réseau devient une ressource de niveau supérieur dans le cadre de la migration dans le modèle de déploiement Gestionnaire de ressources, ainsi que toutes les propriétés.                                                                                                                                                                                                                                                            |   
| Définir le point de terminaison équilibrage                             | Équilibrage de charge                                   | Dans le modèle de déploiement classique, la plateforme affecté un équilibrage de charge implicites pour chaque service cloud. Lors de la migration, nous créons une nouvelle ressource d’équilibrage de charge et le jeu de point de terminaison l’équilibrage de charge devient règles d’équilibrage de charge.                                                                                                                                                                                                                                     |   
| Règles de trafic entrant NAT                                      | Règles de trafic entrant NAT                               | Points de terminaison d’entrée définis sur l’ordinateur virtuel sont converties en entrant des règles de traduction d’accès réseau sous l’équilibrage de charge lors de la migration.                                                                                                                                                                                                                                                                                                                                           |   
| Adresse VIP                              | Adresse IP publique avec nom DNS                 | L’adresse IP virtuelle devient une adresse IP publique et est associé à l’équilibrage de charge.                                                                                                                                                                                                                                                                                                                                                        |   
| Réseau virtuel                                        | Réseau virtuel                                 | Le réseau virtuel est migré, avec toutes ses propriétés, dans le modèle de déploiement du Gestionnaire de ressources. Un nouveau groupe de ressources est créé avec le nom `-migrated`. Notez les [configurations non prises en charge](virtual-machines-windows-migration-classic-resource-manager.md).                                                                                                                                                                                                     |
| Adresses IP réservé                                           | Adresse IP publique avec la méthode d’allocation statique  | Adresses IP réservé associée à l’équilibrage de charge sont migrés, ainsi que la migration du service cloud ou la machine virtuelle. Non associée migration IP réservée n’est pas prise en charge pour le moment.                                                                                                                                                                                                                                                           |   
| Adresse IP publique par machine virtuelle                               | Adresse IP publique avec la méthode d’allocation dynamique | L’adresse IP associée à la machine virtuelle est converti en une ressource d’adresse IP publique, avec la méthode d’allocation valeur statique.                                                                                                                                                                                                                                                                                                                                   |   
| NSGs                                | NSGs                         | Groupes de sécurité réseau associés à un sous-réseau sont cloner dans le cadre de la migration vers le modèle de déploiement du Gestionnaire de ressources. Notez que la NSG dans le modèle de déploiement classique n’est pas supprimé lors de la migration. Toutefois, les opérations de plan de gestion de la NSG sont bloquées lorsque la migration est en cours.                                                                                                                                                                             |   
| Serveurs DNS                                            | Serveurs DNS                                     | Serveurs DNS associés à un réseau virtuel ou la machine virtuelle sont migrés dans le cadre de la migration de ressources correspondant, ainsi que toutes les propriétés.                                                                                                                                                                                                                                                                                                                    |   
| UDRs                                    | UDRs                             | Itinéraires définis par l’utilisateur associés à un sous-réseau sont cloner dans le cadre de la migration vers le modèle de déploiement du Gestionnaire de ressources. Notez que la UDR dans le modèle de déploiement classique n’est pas supprimé lors de la migration. Toutefois, les opérations de plan de gestion de la UDR sont bloquées lorsque la migration est en cours.                                                                                                                                                                             |   
| Propriété configuration réseau d’un ordinateur virtuel de transfert IP | Transfert de propriété sur la carte réseau IP               | L’adresse IP du transfert de propriété sur un ordinateur virtuel est converti en une propriété sur l’interface réseau lors de la migration.                                                                                                                                                                                                                                                                                                                                                           |   
| Équilibrage de charge plusieurs adresses IP                        | Équilibrage de charge plusieurs ressources IP publics | Chaque adresse IP publique associée à l’équilibrage de charge est converti en une ressource IP publique et associée à l’équilibrage de charge après la migration.                                                                                                                                                                                                                                                                                                       |   
| Noms DNS internes sur l’ordinateur virtuel                           | Noms DNS internes sur la carte réseau                    | Lors de la migration, le suffixe DNS interne de la machine virtuelle est déplacée vers une propriété en lecture seule nommée « InternalDomainNameSuffix » sur la carte réseau. Le suffixe reste inchangé après la migration et résolution de machine virtuelle doit continuer à travailler en tant que précédemment.                                                                                                                                                                                                           |   

## <a name="illustration-of-a-simple-migration-walkthrough"></a>Illustration d’une procédure pas à pas migration simple

Dans les captures d’écran exemple suivant, vous pouvez visualiser la représentation d’un service cloud avec une machine virtuelle (et non dans un réseau virtuel) après la phase de préparation.

![Capture d’écran montrant la représentation classique après préparer](./media/virtual-machines-windows-migration-classic-resource-manager/classic-migration-prepare-portal.png)
![capture d’écran montrant la représentation du Gestionnaire de ressources préparer une fois](./media/virtual-machines-windows-migration-classic-resource-manager/resourcemanager-migration-prepare-portal.png)

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous connaissez la migration de ressources IaaS classiques au Gestionnaire de ressources, vous pouvez commencer à déplacer les ressources.

- [Utiliser PowerShell pour déplacer des ressources IaaS de classique au Gestionnaire de ressources Azure](virtual-machines-windows-ps-migration-classic-resource-manager.md)
- [Infrastructure du langage commun permet de migrer des ressources IaaS de classique au Gestionnaire de ressources Azure](virtual-machines-linux-cli-migration-classic-resource-manager.md)
- [Migration de plateforme prise en charge des ressources IaaS de classique au Gestionnaire de ressources Azure](virtual-machines-windows-migration-classic-resource-manager.md)
- [Dupliquer une machine virtuelle classique au Gestionnaire de ressources Azure à l’aide des scripts PowerShell Communauté](virtual-machines-windows-migration-scripts.md)
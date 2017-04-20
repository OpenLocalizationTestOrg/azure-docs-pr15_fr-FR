<properties
    pageTitle="Glossaire Azure - dictionnaire Azure | Microsoft Azure"
    description="Utilisez le glossaire Azure pour comprendre la terminologie cloud sur la plateforme Azure. Ce dictionnaire Azure courte fournit des définitions des termes de cloud courants pour Azure."
    keywords="Dictionnaire Azure, terminologie cloud, glossaire Azure, définitions, termes cloud"
    services="na"
    documentationCenter="na"
    authors="MonicaRush"
    manager="jhubbard"
    editor=""/>

<tags
    ms.service="multiple"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/03/2016"
    ms.author="monicar"/>


# <a name="microsoft-azure-glossary-a-dictionary-of-cloud-terminology-on-the-azure-platform"></a>Glossaire Microsoft Azure : un dictionnaire de la terminologie cloud sur la plateforme Azure

Le glossaire Microsoft Azure est un dictionnaire courte de la terminologie cloud pour la plateforme Azure.

## <a name="find-service-definitions-and-other-cloud-terms"></a>Rechercher des définitions de service et d’autres termes cloud

* Pour obtenir les définitions de services Azure et leurs AWS correspondants voir [Microsoft Azure et Amazon Web Services](https://azure.microsoft.com/campaigns/azure-vs-aws/mapping/).

* Pour les professionnels du secteur cloud termes voir [Cloud computing termes](https://azure.microsoft.com/overview/cloud-computing-dictionary/).

Le glossaire Azure avec ces deux références ci-dessus fournit une classification de bout en bout pour Azure et du secteur cloud.  

## <a name="azure-glossary-list"></a>Liste Glossaire Azure


### <a name="account"></a>compte  
Professionnel ou scolaire ou compte Microsoft personnel utilisé pour accéder et gérer un abonnement Azure.  
Voir aussi [comment Azure abonnements sont associés à Azure Active Directory](./active-directory/active-directory-how-subscriptions-associated-directory.md)


### <a name="availability-set"></a>jeu de disponibilité  
Collection des machines virtuelles sont gérés ensemble à la fiabilité et redondants de l’application. L’utilisation d’un jeu de disponibilité garantit que pendant soit un événement de maintenance planifiée ou au moins une machine virtuelle est disponible.  
Voir aussi [Gérer la disponibilité des machines virtuelles Windows](./virtual-machines/virtual-machines-windows-manage-availability.md) ou [Gérer la disponibilité des machines virtuelles Linux](./virtual-machines/virtual-machines-linux-manage-availability.md)


### <a name="classic-model"></a>Modèle de déploiement d’Azure classique  
Une des deux [modèles de déploiement](resource-manager-deployment-model.md) utilisée pour déployer des ressources dans Azure (le nouveau modèle est Azure le Gestionnaire de ressources). Certaines ressources Azure peuvent être déployés dans un modèle ou l’autre, tandis que d’autres personnes peuvent être déployés dans les deux modèles. Conseils pour les détails des ressources Azure qui model(s) une ressource peut être déployé avec.


### <a name="cli"></a>Interface de ligne Azure (commande)  
Une [interface de ligne de commande](xplat-cli-install.md) qui peuvent servir à gérer les services Azure à partir de Windows, OSX et PC Linux.


### <a name="powershell"></a>PowerShell Azure  
Une [interface de ligne de commande](powershell-install-configure.md) pour gérer les services Azure via une ligne de commande à partir d’ordinateurs Windows. Certains services ou les fonctionnalités du service peuvent être gérées uniquement par le biais de PowerShell ou de l’infrastructure du langage commun. Conseils pour plus d’informations chaque ressource Azure qui model(s) une ressource peut être déployé avec.   
Voir aussi [comment installer et configurer PowerShell Azure](powershell-install-configure.md)


### <a name="arm-model"></a>Modèle de déploiement Azure Gestionnaire de ressources  
Une des deux [modèles de déploiement](resource-manager-deployment-model.md) utilisés pour déployer des ressources dans Microsoft Azure (l’autre est le modèle de déploiement classique). Certaines ressources Azure peuvent être déployés dans un modèle ou l’autre, tandis que d’autres personnes peuvent être déployés dans les deux modèles. Conseils pour les détails des ressources Azure qui model(s) une ressource peut être déployé avec.


### <a name="fault-domain"></a>domaine d’erreur  
La collection de machines virtuelles dans un jeu de disponibilité peut échouer en même temps. Un exemple est un groupe d’ordinateurs en rack qui partagent un commutateur de source et de réseau power courantes. Dans Azure, les machines virtuelles dans un jeu de disponibilité sont automatiquement séparées dans plusieurs domaines défaillance.  
Voir aussi [Gérer la disponibilité des machines virtuelles Windows](./virtual-machines/virtual-machines-windows-manage-availability.md) ou [Gérer la disponibilité des machines virtuelles Linux](./virtual-machines/virtual-machines-linux-manage-availability.md)  


### <a name="geo"></a>geo  
Une limite définie pour la délégation données qui contient généralement les deux ou plusieurs zones. Les limites peuvent être au sein ou au-delà des frontières nationales et dépendent de réglementation fiscale. Chaque geo comporte au moins une région. Exemples de zones géographiques sont Asie-Pacifique et Japon. Abréviation de *géographie*.  
Voir aussi [régions Azure](best-practices-availability-paired-regions.md)


### <a name="geo-replication"></a>geo-réplication  
Le processus de réplication automatiquement du contenu tel que des objets BLOB, les tables et les files d’attente dans une paire régionale.  
Voir aussi [Active Geo-réplication de base de données SQL Azure](./sql-database/sql-database-geo-replication-overview.md)


### <a name="image"></a>image  
Un fichier qui contient le système d’exploitation et la configuration de l’application qui peut être utilisée pour créer un nombre quelconque de machines virtuelles. Dans Azure, il existe deux types d’images : machine virtuelle du système d’exploitation et image. Une image de machine virtuelle inclut un système d’exploitation, tous les disques attachés à une machine virtuelle lorsque l’image est créée. Une image du système d’exploitation contient uniquement un système d’exploitation GRG avec aucune configuration de disque de données.  
Voir aussi [Naviguer et sélectionner les images machine virtuelle Windows dans Azure avec PowerShell ou de l’infrastructure du langage commun](./virtual-machines/virtual-machines-windows-cli-ps-findimage.md)


### <a name="limits"></a>limites  
Le nombre de ressources qui peuvent être créés ou le test d’évaluation de performances que vous pouvez obtenir. Limites sont généralement associées à des abonnements, les services et les offres.  
Voir aussi [abonnement Azure et les limites de service, les quotas et les contraintes](azure-subscription-service-limits.md)


### <a name="load-balancer"></a>équilibrage de charge  
Une ressource qui répartit le trafic entrant sur les ordinateurs dans un réseau. Dans Azure, un équilibrage de charge répartit le trafic vers des machines virtuelles définies dans un jeu d’équilibrage de charge. Un [équilibrage de charge](./load-balancer/load-balancer-overview.md) peut être via internet, ou elle peut être interne.  


### <a name="offer"></a>offre  
Les tarifs, crédits et termes apparentés applicables à un abonnement Azure.  
Voir [Azure offrent la page Détails](https://azure.microsoft.com/support/legal/offer-details/)


### <a name="portal"></a>portail  
Le portail Web sécurisé utilisé pour déployer et gérer les services Azure.  Il existe deux portails : le [portail Azure](http://portal.azure.com/) et le [portail classique](http://manage.windowsazure.com/). Certains services sont disponibles dans les deux portails, tandis que d’autres sont uniquement disponibles dans une ou l’autre. Le [diagramme de disponibilité du portail Azure](https://azure.microsoft.com/features/azure-portal/availability/) répertorie les services qui sont disponibles dans le portail.  


### <a name="region"></a>région  
Une zone dans une zone géographique qui ne prend pas croix nationales bordures et contient un ou plusieurs centres de données. Tarifs, services régionaux et types offre sont présentés au niveau de la région. Une région est généralement associée à une autre région, qui peut être jusqu'à plusieurs centaines miles absent (e), pour former une paire régionale. Paires régionaux peuvent être utilisées comme mécanisme de sinistre et disponibilité scénarios. Également appelée en général en tant *qu’emplacement*.  
Voir aussi [régions Azure](best-practices-availability-paired-regions.md)


### <a name="resource"></a>ressource  
Un élément qui fait partie de votre solution Azure. Chaque service Azure vous permet de déployer différents types de ressources, tels que des bases de données ou machines virtuelles.   
Voir aussi [vue d’ensemble du Gestionnaire de ressources Azure](azure-resource-manager/resource-group-overview.md)


### <a name="resource-group"></a>groupe de ressources  
Un conteneur dans le Gestionnaire de ressources qui contient des ressources associées à une application. Le groupe de ressources peut inclure toutes les ressources pour une application, ou uniquement les ressources qui sont regroupés logiquement. Vous pouvez décider la manière dont vous souhaitez affecter des ressources aux groupes de ressource basés sur ce qui convient le mieux pour votre organisation.  
Voir aussi [vue d’ensemble du Gestionnaire de ressources Azure](azure-resource-manager/resource-group-overview.md)


### <a name="arm-template"></a>Modèle de gestionnaire de ressources  
Un fichier JSON qui définit de manière déclarative une ou plusieurs ressources Azure et qui définit les dépendances entre les ressources déployées. Le modèle peut être utilisé pour déployer les ressources de manière cohérente à travers et à plusieurs reprises.  
Voir aussi [Gestionnaire de ressources Azure de création de modèles](resource-group-authoring-templates.md)


### <a name="resource-provider"></a>fournisseur de ressources  
Service qui fournit des ressources que vous pouvez déployer et gérer par le biais du Gestionnaire de ressources. Chaque fournisseur de ressources propose opérations pour travailler avec les ressources qui sont déployés. Fournisseurs de ressources sont accessibles via le portail Azure Azure PowerShell et plusieurs SDK programmation.  
Voir aussi [vue d’ensemble du Gestionnaire de ressources Azure](azure-resource-manager/resource-group-overview.md)


### <a name="role"></a>rôle  
Un moyen de contrôle d’accès qui peuvent être affectées à des utilisateurs, groupes et services. Les rôles sont en mesure d’effectuer des actions telles que créer, gérer et lisez la suite ressources Azure.  
Voir aussi [RBAC : rôles intégrés](./active-directory/role-based-access-built-in-roles.md)


### <a name="sla"></a>niveau accord de service)  
Le contrat qui décrit les engagements de Microsoft pour la connectivité et de disponibilité. Chaque service Azure a un SLA spécifique.  
Voir aussi [contrats de niveau de Service](https://azure.microsoft.com/support/legal/sla/)


### <a name="storage-account"></a>compte de stockage  
Un compte de stockage qui vous donne accès aux services Blob Azure, file d’attente, Table et fichier dans le stockage Azure. Votre compte de stockage fournit l’espace de noms unique pour vos objets de données de stockage Azure.  
Voir aussi [comptes de stockage sur Azure](./storage/storage-create-storage-account.md)


### <a name="subscription"></a>abonnement  
Contrat d’un client Microsoft qui leur permet d’obtenir des services Azure. Les tarifs d’abonnement et les termes associés sont régis par l’offre choisie pour l’abonnement. Consultez le [contrat d’abonnement en ligne Microsoft](https://azure.microsoft.com/support/legal/subscription-agreement/).  
Voir aussi [comment Azure abonnements sont associés à Azure Active Directory](./active-directory/active-directory-how-subscriptions-associated-directory.md)


### <a name="tag"></a>balise  
Un terme d’indexation qui vous permet de classer les ressources selon vos besoins en matière de gestion ou de facturation. Vous pouvez utiliser les balises lorsque vous disposez d’une collection complexe des groupes de ressources et des ressources, et que vous voulez visualiser ces éléments de la manière qui convient le mieux. Par exemple, vous pourriez ajouter des balises ressources qui jouent un rôle similaire dans votre organisation ou appartiennent au même service.  
Voir aussi [en utilisant des balises pour organiser vos ressources Azure](resource-group-using-tags.md)


### <a name="update-domain"></a>mettre à jour domaine  
Collection des machines virtuelles dans un jeu de disponibilité qui sont mises à jour en même temps. Machines virtuelles dans le même domaine de mise à jour sont redémarrés ensemble lors de la maintenance planifiée. Azure redémarre jamais plusieurs domaines de mise à jour à la fois. Également appelé un domaine de mise à niveau.  
Voir aussi [Gérer la disponibilité des machines virtuelles Windows](./virtual-machines/virtual-machines-windows-manage-availability.md) ou [Gérer la disponibilité des machines virtuelles Linux](./virtual-machines/virtual-machines-linux-manage-availability.md)  


### <a name="vm"></a>machine virtuelle  
L’implémentation logicielle d’un ordinateur physique exécutant un système d’exploitation. Machines virtuelles peuvent s’exécuter simultanément sur le même matériel. Dans Azure, machines virtuelles sont disponibles dans différentes tailles de.  
Voir aussi [Machines virtuelles documentation](https://azure.microsoft.com/documentation/services/virtual-machines/)


### <a name="vm-extension"></a>extension de machine virtuelle  
Une ressource qui mettent en œuvre, comportements ou des fonctionnalités qui des options vous permettent d’autres programmes fonctionne ou permettent de vous pouvez interagir avec un ordinateur en cours d’exécution. Par exemple, vous pouvez utiliser l’extension machine virtuelle Access pour réinitialiser ou modifier les valeurs d’accès à distance sur un ordinateur virtuel Azure.  
Voir aussi [sur les extensions de machine virtuelle et fonctionnalités (Windows)](./virtual-machines/virtual-machines-windows-extensions-features.md) ou [sur les extensions de machine virtuelle et fonctionnalités (Linux)](./virtual-machines/virtual-machines-linux-extensions-features.md)


### <a name="vnet"></a>réseau virtuel  
Un réseau qui fournit la connectivité entre vos ressources Azure isolé de tous les autres clients Azure. Il peut être connecté à d’autres réseaux virtuel Azure via une [Passerelle VPN Azure](./vpn-gateway/vpn-gateway-about-vpngateways.md) et à votre réseau local à l’aide de [plusieurs options](./vpn-gateway/vpn-gateway-cross-premises-options.md). Vous pouvez contrôler totalement les paramètres blocs d’adresses IP, DNS, stratégies de sécurité et tables de routage au sein de ce réseau.  
Voir également [réseau virtuel vue d’ensemble](./virtual-network/virtual-networks-overview.md)  

###<a name="see-also"></a>**Voir aussi**  
- [Prise en main Azure](https://azure.microsoft.com/get-started/)
- [Centre de ressources de cloud](https://azure.microsoft.com/resources/)  
- [Azure pour votre application d’entreprise](https://azure.microsoft.com/overview/business-apps-on-azure/)
- [Azure dans votre centre de données](https://azure.microsoft.com/overview/business-apps-on-azure/) 






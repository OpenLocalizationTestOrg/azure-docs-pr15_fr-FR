<properties
   pageTitle="Instructions pour le déploiement d’Active Directory Windows Server sur des Machines virtuelles Azure | Microsoft Azure"
   description="Si vous savez comment déployer les Services de domaine Active Directory et de Services de fédération Active Directory local, découvrir leur fonctionnement sur des machines virtuelles Azure."
   services="active-directory"
   documentationCenter=""
   authors="femila"
   manager="stevenpo"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="09/27/2016"
   ms.author="femila"/>

# <a name="guidelines-for-deploying-windows-server-active-directory-on-azure-virtual-machines"></a>Instructions pour le déploiement d’Active Directory Windows Server sur des machines virtuelles Azure

Cet article explique les différences importantes entre déploiement Windows Server Active Directory Domain Services (AD DS) et Services de fédération Active Directory (AD FS) en local et les déployer sur des machines virtuelles Microsoft Azure.

## <a name="scope-and-audience"></a>Étendues et audience

L’article est destiné aux déjà connaissant le déploiement d’Active Directory local. Il traite des différences entre le déploiement Active Directory sur les réseaux virtuels Microsoft Azure Azure/machines virtuelles et les déploiements Active Directory locaux traditionnel. Machines virtuelles Azure et réseaux virtuels Azure font partie d’un Infrastructure-en tant que-a-Service (IaaS) offrant aux organisations de tirer parti des ressources informatiques dans le cloud.

Pour ceux qui ne sont pas familiers déploiement AD, consultez le [Guide de déploiement AD DS](https://technet.microsoft.com/library/cc753963) ou [planifier votre déploiement AD FS](https://technet.microsoft.com/library/dn151324.aspx) approprié.

Cet article suppose que le lecteur doit être familiarisé avec les concepts suivants :

- Windows Server AD DS déploiement et la gestion
- Déploiement et la configuration de DNS pour prendre en charge une infrastructure Windows Server AD DS
- Windows Server AD FS déploiement et la gestion
- Déploiement, la configuration et la gestion des applications tierces confiance (sites Web et les services web) qui peuvent utiliser des jetons de Windows Server AD FS
- Concepts de base générales d’une machine virtuelle, telles que la configuration d’une machine virtuelle, disques virtuels et réseaux virtuels

Cet article présente la configuration requise pour un scénario de déploiement hybride où Windows Server AD DS ou AD FS sont partiellement déployé en local et partiellement déploiement sur les machines virtuelles Azure. Le document traite tout d’abord les différences importantes entre exécutant Windows Server AD DS et AD FS sur Azure machines virtuelles par rapport en local et des décisions importantes qui affectent la conception et déploiement. Le reste du papier explique les instructions pour chacun des points de décision plus en détail et comment appliquer les instructions à divers scénarios de déploiement.

Cet article n’aborde pas la configuration de [Azure Active Directory](http://azure.microsoft.com/services/active-directory/), qui est un service basé sur le reste qui fournit des fonctionnalités de contrôle d’accès et de gestion des identités pour les applications cloud. Azure Active Directory (AD Azure) et Windows Server AD DS sont, cependant, conçus pour fonctionner ensemble afin de fournir une solution de gestion des identités et des accès pour un déploiement hybride d’aujourd'hui informatique environnements et applications modernes. Pour aider à comprendre les différences et les relations entre Windows Server AD DS et Azure AD, procédez comme suit :

1. Vous pouvez exécuter Windows Server AD DS dans le cloud sur Azure machines virtuelles lorsque vous utilisez Azure pour étendre votre centre de données locales dans le cloud.
2. Vous pouvez utiliser Azure AD pour donner à vos utilisateurs de l’authentification unique aux applications de logiciel-as-a-Service (SaaS). Microsoft Office 365 utilise cette technologie, par exemple, et applications qui s’exécutent sur Azure ou d’autres plateformes cloud peuvent également l’utiliser.
3. Vous pouvez utiliser Azure AD (son Service de contrôle d’accès) pour permettre aux utilisateurs de se d’utilisation des identités de Facebook, Google, Microsoft et d’autres fournisseurs d’identité aux applications qui sont hébergées dans le nuage ou sur site.

Pour plus d’informations sur ces différences, consultez [L’identité Azure](fundamentals-identity.md).

## <a name="related-resources"></a>Ressources connexes

Vous pouvez télécharger et exécuter l' [Évaluation de la préparation Azure Machine virtuelle](https://www.microsoft.com/download/details.aspx?id=40898). L’évaluation est automatiquement inspecter votre environnement local et générer un rapport personnalisé selon les instructions figurant dans cette rubrique pour vous aider à migrer l’environnement vers Azure.

Nous vous recommandons de consulter également tout d’abord les didacticiels, guides et des vidéos couvrant les rubriques suivantes :

- [Configurer un réseau virtuel exclusivement le nuage, dans le portail Azure](../virtual-network/virtual-networks-create-vnet-arm-pportal.md)
- [Configurer un VPN Site à Site dans le portail Azure](../vpn-gateway/vpn-gateway-site-to-site-create.md)
- [Installer une nouvelle forêt Active Directory sur un réseau virtuel Azure](active-directory-new-forest-virtual-machine.md)
- [Installer un contrôleur de domaine Active Directory réplica sur Azure](../active-directory/active-directory-install-replica-active-directory-domain-controller.md)
- [Microsoft Azure IaaS Pro informatique : notions de base (01) Machine virtuelle](https://channel9.msdn.com/Series/Windows-Azure-IT-Pro-IaaS/01)
- [Microsoft Azure IaaS Pro informatique : (05) réseaux virtuels création et la connectivité entre locaux](https://channel9.msdn.com/Series/Windows-Azure-IT-Pro-IaaS/05)

## <a name="introduction"></a>Introduction

Les exigences fondamentales pour le déploiement d’Active Directory Windows Server sur des machines virtuelles Azure diffèrent très peu de déploiement dans des machines virtuelles locale (et, dans une certaine mesure, machines physiques). Par exemple, dans le cas de Windows Server AD DS, si le contrôleur de domaine (DC) que vous déployez sur Azure machines virtuelles est réplicas dans un existant en local forêt/domaines d’entreprise, puis le déploiement d’Azure largement peut être traité de la même façon que vous pouvez considérer n’importe quel autre site Windows Server Active Directory supplémentaire. Autrement dit, sous-réseaux doivent être définis dans Windows Server AD DS, un site créé, les sous-réseaux lié à ce site et connectée à d’autres sites à l’aide des liens de site appropriés. Il existe cependant certaines différences qui sont communes à tous les déploiements Azure et certains qui varient selon le scénario de déploiement spécifique. Deux différences fondamentales sont décrites ci-dessous :

### <a name="azure-virtual-machines-may-need-connectivity-to-the-on-premises-corporate-network"></a>Machines virtuelles Azure devrez connectivité au réseau d’entreprise en local.

Connexion Azure machines virtuelles à un réseau d’entreprise en local requiert un réseau virtuel Azure, qui inclut un site à ou site-à-point virtuel privé (VPN) composant réseau peuvent se connecter en toute transparence Azure machines virtuelles et des ordinateurs locaux. Ce composant VPN peut permettre à ordinateurs membres du domaine local accéder à un domaine Active Directory Windows Server dont superflus sont hébergés en mode exclusif sur Azure machines virtuelles. Il est important de remarquer, cependant, que si le réseau privé virtuel échoue, l’authentification et autres opérations qui dépendent de Windows Server Active Directory échouera également. Tandis que les utilisateurs peuvent être en mesure de se connecter à l’aide d’existant mis en cache les informations d’identification, égal à égal tout ou de tentatives d’authentification de client-serveur pour lequel tickets qui doivent être émis ou devenus obsolètes échouera.

Voir [Réseau virtuel](http://azure.microsoft.com/documentation/services/virtual-network/) pour une démonstration vidéo et la liste des didacticiels pas à pas, y compris [configurer un réseau privé virtuel de Site à Site dans le portail Azure](../vpn-gateway/vpn-gateway-site-to-site-create.md).

> [AZURE.NOTE] Vous pouvez également déployer Active Directory Windows Server sur un réseau virtuel Azure qui n’a pas de connectivité avec un réseau local. Les instructions fournies dans cette rubrique, cependant, supposent qu’un réseau virtuel Azure est utilisé, car il offre des fonctionnalités qui sont essentielles à Windows Server d’adressage IP.

### <a name="static-ip-addresses-must-be-configured-with-azure-powershell"></a>Adresses IP statiques doivent être configurés avec Azure PowerShell.

Adresses dynamiques sont affectés par défaut, mais l’applet de commande Set-AzureStaticVNetIP permet d’attribuer une adresse IP statique à la place. Qui définit une adresse IP statique demeure valable service correcteur et machine virtuelle arrêt/redémarrage. Pour plus d’informations, voir [l’adresse IP interne statique pour les machines virtuelles](http://azure.microsoft.com/blog/static-internal-ip-address-for-virtual-machines/).

## <a name="BKMK_Glossary"></a>Termes et définitions

Voici une liste non exhaustive de termes pour différentes technologies Azure qui seront référencées dans cet article.

- **Machines virtuelles Azure**: IaaS l’offre dans Azure qui permet aux clients de déployer des machines virtuelles en cours d’exécution pratiquement n’importe quel traditionnellement local la charge serveur.

- **Réseau virtuel Azure**: le service de mise en réseau dans Azure qui permet aux clients de créer et gérer les réseaux virtuels dans Azure et en toute sécurité associez-les à leur propre locaux infrastructure réseau à l’aide d’un réseau privé virtuel (VPN).

- **Adresse IP virtuelle**: une adresse IP avec accès internet qui n’est pas liée à une carte interface ordinateur ou réseau spécifique. Les services en nuage affectés à une adresse IP virtuelle pour recevoir le trafic réseau qui est redirigé vers une machine virtuelle Azure. Une adresse IP virtuelle est une propriété d’un service cloud qui peut contenir un ou plusieurs machines virtuelles Azure. Notez également qu’un réseau virtuel Azure peut contenir un ou plusieurs services de cloud. Adresses IP virtuelles offrent des fonctions d’équilibrage de charge natives.

- **Adresse IP dynamique**: il s’agit de l’adresse IP qui est uniquement interne. Il doit être configuré comme une adresse IP statique (à l’aide de l’applet de commande Set-AzureStaticVNetIP) pour les machines virtuelles qui hébergent les rôles de serveur DNS/contrôleur de domaine.

- **Service correcteur**: le processus dans lequel Azure renvoie automatiquement un service à un état en cours d’exécution à nouveau une fois qu’il détecte que le service a échoué. Service correcteur est un des aspects d’Azure qui prend en charge de la disponibilité et la résilience. Tandis que peu, le résultat suivant un service correcteur incident pour un contrôleur de domaine sur un ordinateur virtuel est semblable à un redémarrage planifié, mais elle a quelques effets secondaires :

 - La carte réseau virtuelle de la machine virtuelle remplacera
 - L’adresse MAC de la carte réseau virtuelle remplacera
 - Le code processeur/UC de la machine virtuelle changera
 - La configuration IP de la carte réseau virtuelle changera pas tant que la machine virtuelle est connectée à un réseau virtuel et l’adresse IP de la machine virtuelle est statique.

 Aucune de ces problèmes affectent Active Directory Windows Server, car il n’a aucune dépendance par rapport à l’adresse MAC ou ID processeur/processeur, ainsi que tous les déploiements Active Directory Windows Server sur Azure sont recommandées pour être exécuté sur un réseau virtuel Azure comme indiqué ci-dessus.

## <a name="is-it-safe-to-virtualize-windows-server-active-directory-domain-controllers"></a>Il est suffisamment virtualisation de domaine Active Directory Windows Server ?

Déploiement de Windows Server Active Directory DC sur Azure machines virtuelles est soumis aux mêmes consignes que DC local en cours d’exécution sur un ordinateur virtuel. DC virtualisé en cours d’exécution est une pratique approuvée dans la mesure où les instructions pour sauvegarder et restaurer des DC sont suivies. Pour plus d’informations sur les contraintes et les instructions pour exécuter DC virtualisé, voir [Contrôleurs de domaine en cours d’exécution dans Hyper-V](https://technet.microsoft.com/library/dd363553).

Hyperviseurs fournissent ou créez un jeu de technologies d’assistance qui peuvent entraîner des problèmes pour de nombreux systèmes distribués, y compris Active Directory Windows Server. Par exemple, sur un serveur physique, vous pouvez cloner un disque ou utiliser les méthodes non prises en charge pour restaurer l’état d’un serveur, notamment à l’aide de SAN et ainsi de suite, mais cela sur un serveur physique est beaucoup plus difficile que la restauration d’un instantané de machine virtuelle dans un hyperviseur. Azure offre des fonctionnalités qui peuvent entraîner la même condition indésirable. Par exemple, vous devez copier pas les fichiers de disque dur virtuel de DC au lieu d’effectuer des sauvegardes régulières, car restauration peut entraîner une situation similaire à l’aide des fonctionnalités de restaurer instantané.

Ces annulations introduisent bulles USN qui peuvent affecter les États définitivement divergentes entre les DC. Qui peut entraîner des problèmes tels que :

- Objets en attente
- Mots de passe n’est pas cohérente
- Valeurs de l’attribut n’est pas cohérente
- Différences de schéma si le contrôleur de schéma est annulé

Pour plus d’informations sur le contrôleur de domaine est affectés, voir [USN et reprise USN](https://technet.microsoft.com/library/virtual_active_directory_domain_controller_virtualization_hyperv.aspx#usn_and_usn_rollback).

À partir de Windows Server 2012, [garanties supplémentaires sont intégrés à AD DS](https://technet.microsoft.com/library/hh831734.aspx). Ces garanties protéger superflus virtualisé contre mentionnés ci-dessus, dans la mesure où la plateforme hyperviseur sous-jacente prend en charge la machine virtuelle GenerationID. Azure prend en charge GenerationID machine virtuelle, ce qui signifie que le domaine qui exécutent Windows Server 2012 ou version ultérieure sur Azure machines virtuelles ont les garanties supplémentaires.

> [AZURE.NOTE] Vous devez arrêter et redémarrer une machine virtuelle qui s’exécute le rôle de contrôleur de domaine dans Azure au sein du système d’exploitation invité au lieu d’utiliser l’option **Arrêter** dans le portail classique Azure. Aujourd'hui, à l’aide du portail classique pour arrêter une machine virtuelle entraîne la machine virtuelle pour être libéré. Une machine virtuelle désallouée a l’avantage de ne pas entraîner de frais, mais elle réinitialise également la mémoire virtuelle-GenerationID, qui est indésirable pour un contrôleur de domaine. Lorsque la mémoire virtuelle-GenerationID est réinitialisé, l’invocationID de la base de données AD DS est également réinitialisé, le pool RID est ignoré et SYSVOL est marquée comme ne faisant pas autorité. Pour plus d’informations, voir [Présentation des Services de domaine Active Directory (AD DS) virtualisation](https://technet.microsoft.com/library/hh831734.aspx) et [En toute sécurité virtualisation DFSR](http://blogs.technet.com/b/filecab/archive/2013/04/05/safely-virtualizing-dfsr.aspx).

## <a name="why-deploy-windows-server-ad-ds-on-azure-virtual-machines"></a>Pourquoi déployer Windows Server AD DS sur le Machines virtuelles Azure ?

Nombreux scénarios de déploiement de Windows Server AD DS sont adaptées pour le déploiement en tant que machines virtuelles sur Azure. Par exemple, supposons que vous avez une société en Europe nécessitant l’authentification des utilisateurs dans un emplacement distant en Asie. La société n’a pas précédemment déployé Windows Server Active Directory DC d’Asie en raison du coût pour déployer limitées et les compétences pour gérer le déploiement après serveurs. Par conséquent, les demandes d’authentification de l’Asie sont traitées par DC en Europe avec des résultats non optimaux. Dans ce cas, vous pouvez déployer un contrôleur de domaine sur un ordinateur virtuel que vous avez spécifié doivent être exécutés dans le centre de données Azure en Asie. Connectez ce contrôleur de domaine à un réseau virtuel Azure qui est connecté directement à l’emplacement à distance améliore les performances de l’authentification.

Azure est également parfaitement comme substitut aux sites de reprise (DR) dans le cas contraire coûteuses. La relativement peu coûteux d’héberger un petit nombre de domaine et un seul réseau virtuel sur Azure représente une alternative intéressante.

Enfin, vous souhaiterez peut-être déployer une application réseau dans Azure, tel que SharePoint, qui nécessite Windows Server Active Directory mais n’a pas de dépendance sur le réseau local ou d’entreprise Windows Server Active Directory. Dans ce cas, déploiement d’une forêt isolée sur Azure rencontrer SharePoint Configuration requise du serveur est optimal. Là encore, le déploiement des applications de réseau qui nécessitent la connectivité au réseau local et à l’annuaire Active Directory d’entreprise est également prise en charge.

> [AZURE.NOTE] Dans la mesure où il fournit une connexion layer 3, le composant VPN qui fournit la connectivité entre un réseau virtuel Azure et un réseau local pouvez également activer les serveurs membres qui exécutent local pour tirer parti du contrôleur de domaine qui fonctionnent comme des machines virtuelles Azure sous Azure réseau virtuel. Mais si le réseau privé virtuel n’est pas disponible, la communication entre local ordinateurs et superflus basée sur Azure ne fonctionnera pas, qui en résulte dans l’authentification et diverses autres erreurs.  

## <a name="contrasts-between-deploying-windows-server-active-directory-domain-controllers-on-azure-virtual-machines-versus-on-premises"></a>Contraste entre le déploiement de domaine Active Directory Windows Server Azure machines virtuelles en fonctionnement et en local

- Pour les scénarios de déploiement Active Directory Windows Server incluant une machine virtuelle unique, il est nécessaire d’utiliser un réseau virtuel Azure pour la cohérence des adresses IP. Notez que ce guide suppose que DC s’exécutent sur un réseau virtuel Azure.

- Comme avec DC en local, les adresses IP statiques sont recommandés. Une adresse IP statique peut uniquement être configurée à l’aide de PowerShell Azure. Pour plus d’informations, voir [adresse IP interne statique pour les machines virtuelles](http://azure.microsoft.com/blog/static-internal-ip-address-for-virtual-machines/) . Si vous avez surveillance systèmes ou autres solutions qui recherchent les configuration d’adresse IP statique au sein du système d’exploitation invité, vous pouvez affecter la même adresse IP statique pour les propriétés de la carte réseau de la machine virtuelle. Mais n’oubliez pas que la carte réseau est ignorée si la machine virtuelle est soumis service correcteur ou est arrêtée dans le portail classique et a libéré son adresse. Dans ce cas, l’adresse IP statique au sein de l’invité devrez réinitialiser.

- Déploiement de machines virtuelles sur un réseau virtuel ne pas sous-entendent l’existence (ou nécessitent) connectivité à un réseau local. le réseau virtuel permet simplement de cette possibilité. Vous devez créer un réseau virtuel pour la communication privée entre Azure et de votre réseau local. Vous devez déployer un point de terminaison VPN sur le réseau local. La connexion VPN est ouvert à partir d’Azure au réseau local. Pour plus d’informations, voir [Vue d’ensemble du réseau virtuel](../virtual-network/virtual-networks-overview.md) et [configurer un réseau privé virtuel de Site à Site dans le portail Azure](../vpn-gateway/vpn-gateway-site-to-site-create.md).

> [AZURE.NOTE] Une option pour [créer un réseau privé virtuel point-à-site](../vpn-gateway/vpn-gateway-point-to-site-create.md) n’est disponible pour vous connecter chaque ordinateur fonctionnant sous Windows directement à un réseau virtuel Azure.

- Que vous créiez virtuel réseau ou non, frais Azure pour le trafic de sortie, mais pas pénétration. Différentes options de conception Active Directory Windows Server peuvent affecter la quantité de trafic sortie est générée par un déploiement. Par exemple, (contrôleur) déploiement d’un contrôleur de domaine en lecture seule limite le trafic de sortie car il ne réplique pas sortant. Mais la décision de déployer un contrôleur doit être comparée à la nécessité d’effectuer des opérations d’écriture sur le contrôleur de domaine et la [compatibilité](https://technet.microsoft.com/library/cc755190) ayant des applications et services dans le site avec RODC. Pour plus d’informations sur les frais de trafic, voir [Azure tarifs un coup de œil](http://azure.microsoft.com/pricing/).

- Bien que vous ayez terminé contrôler sur le serveur de ressources à utiliser pour machines virtuelles en local, tel que RAM, disque taille, et ainsi de suite, sur Azure, vous devez sélectionner dans la liste des tailles de serveur préconfiguré. Pour un contrôleur de domaine, un disque de données est nécessaire en plus du disque système d’exploitation afin de stocker la base de données Active Directory de Windows Server.

## <a name="can-you-deploy-windows-server-ad-fs-on-azure-virtual-machines"></a>Vous pouvez déployer Windows Server AD FS sur Azure machines virtuelles ?

Oui, vous pouvez déployer Windows Server AD FS sur Azure machines virtuelles et les [meilleures pratiques pour le déploiement AD FS](https://technet.microsoft.com/library/dn151324.aspx) locaux s’appliquent également à déploiement AD FS sur Azure. Mais quelques-unes des pratiques recommandées telles que l’équilibrage de charge et disponibilité nécessitent technologies au-delà qu’AD FS offre lui-même. Il doivent être fournis par l’infrastructure sous-jacente. Nous allons passer en revue quelques-unes de ces recommandations et voyez comment ils peuvent être obtenus en utilisant des machines virtuelles Azure et un réseau virtuel Azure.

1. **Ne jamais exposer les serveurs de service de (émission) jeton de sécurité directement à Internet.**

    Ceci est important, car le STS émet des jetons de sécurité. Par conséquent, les serveurs SharePoint Team Services tels que des serveurs AD FS doivent être traités avec le même niveau de protection dans un contrôleur de domaine. Si un STS est compromis, les utilisateurs malveillants ont la possibilité d’émission des jetons d’accès potentiellement contenant revendications de leur choix pour les applications tierces confiance et d’autres serveurs STS dans les approbateurs organisations.

2. **Déployer superflus Active Directory pour tous les domaines d’utilisateur dans le même réseau que les serveurs AD FS.**

    AD FS serveurs utilisent les Services de domaine Active Directory pour authentifier les utilisateurs. Il est recommandé de déployer des contrôleurs de domaine sur le même réseau que les serveurs AD FS. Ainsi, continuité des activités au cas où le lien entre le réseau Azure et de votre réseau local est rompu et permet de latence inférieure et des performances accrues pour les connexions.

3. **Déployer plusieurs nœuds AD FS haute disponibilité et l’équilibrage de la charge.**

    Dans la plupart des cas, l’échec d’une application qui permet de AD FS est inacceptable, car les applications qui nécessitent des jetons de sécurité sont souvent critiques pour l’entreprise. Par conséquent, et étant donné que AD FS réside maintenant dans le chemin critique pour accéder aux applications critiques, les services AD FS doivent être hautement disponible via plusieurs serveurs proxy AD FS et serveurs AD FS. Pour obtenir la répartition des demandes, les programmes d’équilibrage de charge sont généralement déployés devant les serveurs AD FS et proxy AD FS.

4. **Déployer un ou plusieurs des nœuds de Proxy de l’Application Web pour accéder à internet.**

    Lorsque les utilisateurs doivent accéder aux applications protégées par le service AD FS, le service AD FS doit être disponible à partir d’internet. Pour ce faire, déploiement du service Proxy de l’Application Web. Il est fortement recommandé pour déployer plusieurs nœuds pour les besoins de disponibilité et l’équilibrage de charge.

5. **Limiter l’accès à partir des nœuds Proxy de l’Application Web vers les ressources de votre réseau interne.**

    Pour autoriser les utilisateurs externes à accéder à AD FS à partir d’internet, vous devez déployer des nœuds de Proxy de l’Application Web (ou Proxy AD FS dans les versions antérieures de Windows Server). Les nœuds de proxy Application Web sont directement exposés à Internet. Ils ne sont pas nécessaires être associés à un domaine et qu’ils uniquement besoin d’y accéder aux serveurs AD FS sur les ports TCP 443 et 80. Il est fortement recommandé que communication à tous les autres ordinateurs (notamment contrôleur de domaine) est bloquée.

    Il s’agit généralement obtenue en local au moyen d’une DMZ. Les pare-feu utilisent un mode d’autorisation pour restreindre le trafic de la zone DMZ au réseau local (autrement dit, seul le trafic à partir des adresses IP spécifiées et sur les ports spécifiés est autorisé, et toutes les autres le trafic est bloqué).

Le diagramme suivant affiche un traditionnel locale déploiement AD FS.

![Diagramme d’un déploiement Active Directory Federation Services traditionnel en local](media/active-directory-deploying-ws-ad-guidelines/ADFS_onprem.png)

Toutefois, étant donné que Azure ne fournit pas native, des fonctions complètes de pare-feu, d’autres options doivent être utilisée pour restreindre le trafic. Le tableau suivant montre chaque option et des avantages et des inconvénients.

| Option | Avantage | Inconvénient |
| ------ | --------- | ------------ |
| [Utilisateurs du réseau Azure](virtual-networks-acl.md) | Moins coûteuse et simplifie considérablement la configuration initiale | Réseau supplémentaires et configuration requise si les nouvelles machines virtuelles sont ajoutés au déploiement |
| [Pare-feu Barracuda naturel](https://www.barracuda.com/products/ngfirewall) | Le mode d’autorisation de fonctionnement et il ne nécessite aucune configuration et de réseau | Une augmentation des coûts et la complexité de la configuration initiale |

Dans ce cas, les principales étapes à suivre pour déployer ADFS sont les suivantes :

1. Créer un réseau virtuel avec la connectivité entre local, à l’aide d’une [ExpressRoute](http://azure.microsoft.com/services/expressroute/)ou VPN.

2. Déployer des contrôleurs de domaine sur le réseau virtuel. Cette étape est facultative, mais recommandée.

3. Déployer des serveurs de AD FS à un domaine sur le réseau virtuel.

4. Créer un [jeu d’équilibrage de charge interne](http://azure.microsoft.com/blog/internal-load-balancing/) qui inclut les serveurs AD FS utilise une nouvelle adresse IP privée au sein du réseau virtuel (une adresse IP dynamique).

  1. Mettre à jour DNS pour créer le nom de domaine complet pour pointer vers l’adresse IP privée (dynamique) de l’ensemble d’équilibrage de charge interne.

5. Créer un service cloud (ou un réseau virtuel distinct) pour les nœuds Proxy de l’Application Web.

6. Déployer les nœuds Proxy de l’Application Web dans le cloud service ou le réseau virtuel

  1. Créer un jeu d’équilibrage de charge externe qui inclut les nœuds Proxy de l’Application Web.

  2. Mettre à jour le nom DNS externe (FQDN) pour qu’il pointe vers le cloud service adresse IP publique (l’adresse IP virtuelle).

  3. Configurer les serveurs proxy AD FS pour utiliser le nom de domaine complet qui correspond à l’ensemble d’équilibrage de charge interne pour les serveurs AD FS.

  4. Mettre à jour basée sur les revendications des sites web pour utiliser le nom de domaine complet externes pour leur fournisseur de revendications.

7. Limiter l’accès entre Proxy de l’Application Web à n’importe quel ordinateur dans le réseau virtuel AD FS.

Pour restreindre le trafic, le jeu d’équilibrage de charge pour l’équilibrage de charge interne Azure doit être configurée pour le trafic uniquement pour les ports TCP 80 et 443, et toutes les autres le trafic vers l’adresse IP dynamique interne de l’ensemble d’équilibrage de charge est supprimé.

![Diagramme d’utilisateurs de réseau ADFS avec TCP 443 + 80 autorisées.](media/active-directory-deploying-ws-ad-guidelines/ADFS_ACLs.png)

Le trafic vers les serveurs AD FS serait autorisé uniquement par les sources suivantes :

- L’équilibrage de charge interne Azure.
- L’adresse IP d’un administrateur sur le réseau local.

> [AZURE.WARNING] La conception doit empêcher les nœuds de Proxy de l’Application Web d’atteindre les autres machines virtuelles dans le réseau virtuel Azure ou tous les emplacements sur le réseau local. Qui peut être exécuté en configurant des règles de pare-feu dans l’application en local pour les connexions Express itinéraire ou l’appareil VPN pour les connexions VPN de site à.

Un inconvénient de cette option est qu’il soit nécessaire pour configurer les utilisateurs de réseau pour plusieurs appareils, y compris équilibrage de charge interne, les serveurs AD FS et d’autres serveurs sont ajoutés au réseau virtuel. Si n’importe quel appareil est ajouté au déploiement sans configurer les utilisateurs de réseau pour restreindre le trafic vers celui-ci, la totalité du déploiement peut être exposé. Si les adresses IP des nœuds du Proxy de l’Application Web changent, l’utilisateurs du réseau (ce qui signifie que les serveurs proxy doivent être configurés pour utiliser [des adresses IP dynamiques statiques](http://azure.microsoft.com/blog/static-internal-ip-address-for-virtual-machines/)) devez réinitialiser.

![ADFS sur Azure avec réseau utilisateurs.](media/active-directory-deploying-ws-ad-guidelines/ADFS_Azure.png)

Une autre option consiste à utiliser l’application [Barracuda naturel pare-feu](https://www.barracuda.com/products/ngfirewall) pour contrôler le trafic entre serveurs proxy AD FS et les serveurs AD FS. Cette option est conforme aux meilleures pratiques pour la sécurité et la disponibilité et requiert moins d’administration après l’installation d’origine, car l’application Barracuda naturel pare-feu fournit un mode d’autorisation de l’administration de pare-feu et il peut être installé directement sur un réseau virtuel Azure. Qui évite d’avoir à configurer les utilisateurs du réseau chaque fois qu’un nouveau serveur est ajouté au déploiement. Mais cette option ajoute les coûts et la complexité de déploiement initial.

Dans ce cas, deux réseaux virtuels sont déployés au lieu d’un. Nous allons appeler les VNet1 et VNet2. VNet1 contient les proxys et VNet2 les STSs et la connexion réseau revenir au réseau d’entreprise. VNet1 est donc physiquement (, alors que pratiquement) isolées VNet2 et, à son tour, à partir du réseau d’entreprise. VNet1 se connecte ensuite à VNet2 en utilisant une technologie tunnel spéciale connue en tant que Transport Architecture réseau indépendante (TINA). Le tunnel TINA est associé à chacune des réseaux virtuels à l’aide d’un pare-feu Barracuda naturel — une Barracuda sur chacun des réseaux virtuels.  Pour la haute disponibilité, il est recommandé que vous déployez deux Barracudas sur chaque réseau virtuel ; un actif, l’autres passif. Ils offrent extrêmement riches fonctionnalités il qui nous permet de simuler l’opération d’une DMZ traditionnel local dans Azure.

![ADFS sur Azure avec le pare-feu.](media/active-directory-deploying-ws-ad-guidelines/ADFS_Azure_firewall.png)

Pour plus d’informations, voir [AD FS : étendre une application frontale prenant en charge les revendications local à Internet](#BKMK_CloudOnlyFed).

### <a name="an-alternative-to-ad-fs-deployment-if-the-goal-is-office-365-sso-alone"></a>Une suggestion pour le déploiement AD FS si l’objectif est Office 365 SSO uniquement

Il existe une autre alternative pour le déploiement AD totalement FS si votre objectif est uniquement afin d’activer se connecter à Office 365. Dans ce cas, vous pouvez simplement déployer DirSync avec mot de passe synchronisation locale et obtenir le même résultat final avec la complexité de déploiement minimal car cette approche ne nécessite pas AD FS ou Azure.

Le tableau suivant compare les processus de connexion à fonctionnement avec et sans déployer ADFS.

| Office 365 unique authentification à l’aide de AD FS et synchronisation d’annuaire | Office 365 même authentification à l’aide de la synchronisation d’annuaire + synchronisation de mot de passe |
| ------------- | ------------- |
| 1. l’utilisateur se connecte à un réseau d’entreprise et est authentifié sur Windows Server Active Directory. | 1. l’utilisateur se connecte à un réseau d’entreprise et est authentifié sur Windows Server Active Directory. |
| 2. l’utilisateur essaie d’accéder à Office 365 (je suis @contoso.com). | 2. l’utilisateur essaie d’accéder à Office 365 (je suis @contoso.com). |
| 3. office 365 redirige l’utilisateur vers Azure AD. | 3. office 365 redirige l’utilisateur vers Azure AD. |
| 4. dans la mesure où Azure AD ne peut pas s’authentifier à l’utilisateur et comprenne qu'il existe une approbation avec AD FS locaux, il redirige l’utilisateur à AD FS. | 4. azure AD ne peut pas accepter des tickets Kerberos directement et aucune relation d’approbation n’existe afin qu’il demande que l’utilisateur entrer les informations d’identification. |
| 5. l’utilisateur envoie une tickets Kerberos à l’enquête de STS AD FS. | 5. l’utilisateur entre le même mot de passe en local et Azure AD les valide en fonction du nom d’utilisateur et mot de passe qui ont été synchronisée par la synchronisation d’annuaire. |
| 6. AD FS transforme les tickets Kerberos au format/revendications jetons requises et redirige l’utilisateur vers Azure AD. | 6. azure AD redirige l’utilisateur vers Office 365. |
| 7. l’utilisateur s’authentifie à Azure Active Directory (une autre transformation se produit). |  7. l’utilisateur peut se connecter à Office 365 et OWA en utilisant le jeton Azure AD. |
| 8. azure AD redirige l’utilisateur vers Office 365. |  |
| 9. l’utilisateur est en mode silencieux connecté à Office 365. |  |

Dans Office 365 avec DirSync avec le scénario de synchronisation de mot de passe (aucune Federation Services), de l’authentification unique est remplacé par « même authentification » où « même » signifie simplement que les utilisateurs doivent retaper leurs informations d’identification locales même lorsque vous accédez à Office 365. Notez que ces données peuvent se rappeler navigateur de l’utilisateur afin de réduire les invites suivantes.

### <a name="additional-food-for-thought"></a>Réflexions

- Si vous déployez un proxy AD FS sur une machine virtuelle Azure, connectivité aux serveurs AD FS est nécessaire. S’ils sont en local, il est recommandé d’utiliser la connectivité VPN site à site fournie par le réseau virtuel pour permettre les nœuds Proxy de l’Application Web communiquer avec leurs serveurs AD FS.

- Si vous déployez un serveur AD FS sur une machine virtuelle Azure, connectivité au domaine Active Directory Windows Server, attribut Stores et bases de données de Configuration est nécessaire et peut également exiger un ExpressRoute ou une connexion VPN site à site entre le réseau virtuel Azure et le réseau local.

- Frais sont appliqués à tout le trafic des machines virtuelles Azure (trafic sortant). Si le coût est le facteur déterminant, il est préférable pour déployer les nœuds Proxy de l’Application Web sur Azure, en laissant le AD FS serveurs locale. Si les serveurs ADFS sont déployés sur Azure machines virtuelles ainsi, frais supplémentaires restant prévus pour l’authentification des utilisateurs locaux. Le trafic de sortie prenant en charge un coût indépendamment ou non il parcourt le ExpressRoute ou la connexion de site à site VPN.

- Si vous décidez d’utiliser les fonctionnalités de disponibilité des serveurs AD FS d’équilibrage de charge de serveur natif d’Azure, notez que l’équilibrage de charge fournit des sondes sont utilisées pour déterminer l’état des machines virtuelles au sein du service cloud. Dans le cas d’Azure machines virtuelles (contrairement à des rôles web ou de travail), une sonde personnalisé doit être utilisée dans la mesure où l’agent qui répond aux sondes par défaut ne figure pas dans Azure machines virtuelles. Pour simplifier, vous pouvez utiliser une sonde TCP personnalisé — requis uniquement qu’une connexion TCP (un segment TCP SYN envoyés et a répondu à un segment TCP SYN ACK) être correctement établie pour déterminer la santé machine virtuelle. Vous pouvez configurer la sonde personnalisée pour utiliser n’importe quel port TCP auquel vos machines virtuelles sont à l’écoute.

> [AZURE.NOTE] Machines qui doivent exposer le même jeu de ports directement à Internet (par exemple, les ports 80 et 443) ne peuvent pas partager le même service cloud. Il est, par conséquent, recommandé de créer un nuage dédié service pour vos serveurs de Windows Server AD FS afin d’éviter le chevauchement entre configuration requise pour une application et Windows Server AD FS.

## <a name="deployment-scenarios"></a>Scénarios de déploiement

La section suivante décrit des scénarios de déploiement courants pour attirer l’attention sur les considérations importantes à prendre en compte. Chaque scénario comporte des liens vers plus d’informations sur les décisions et les facteurs à prendre en compte.

1. [Les services AD DS : Déployer une application AD DS compatibles avec aucune configuration minimale requise pour la connectivité réseau d’entreprise](#BKMK_CloudOnly)

    Par exemple, un service SharePoint via Internet est déployé sur une machine virtuelle Azure. L’application a pas de dépendances sur les ressources du réseau d’entreprise. L’application requiert Windows Server AD DS mais ne nécessite pas le service d’annuaire Active Directory Windows Server d’entreprise.

2. [AD FS : Étendre une application frontale prenant en charge les revendications local à Internet](#BKMK_CloudOnlyFed)

    Par exemple, une application prenant en charge les revendications qui a été correctement déployée en local et utilisé par les utilisateurs d’entreprise doit sont accessibles à partir d’Internet. L’application doit être accessible directement via Internet partenaires professionnels à l’aide de leurs propre identités d’entreprise et les utilisateurs d’entreprise existants.

3. [Les services AD DS : Déployer une application Windows Server AD DS prenant en charge devant se connecter au réseau d’entreprise](#BKMK_HybridExt)

    Par exemple, une application prenant en charge LDAP prenant en charge l’authentification Windows intégrée et utilise Windows Server AD DS comme un référentiel de données de configuration et de profil utilisateur est déployée sur une machine virtuelle Azure. Il est préférable de l’application tirer parti du service d’annuaire Active Directory du serveur Windows existant d’entreprise en fournissant de l’authentification unique. L’application n’est pas prenant en charge les revendications.

### <a name="BKMK_CloudOnly"></a>1. AD DS : Déployer une application AD DS compatibles avec aucune configuration minimale requise pour la connectivité réseau d’entreprise

![Déploiement AD DS exclusivement le nuage](media/active-directory-deploying-ws-ad-guidelines/ADDS_cloud.png)
**Figure 1**

#### <a name="description"></a>Description

SharePoint est déployé sur une machine virtuelle Azure et l’application a pas de dépendances sur les ressources du réseau d’entreprise. L’application requiert Windows Server AD DS mais ne *pas* exiger le service d’annuaire Active Directory du serveur Windows d’entreprise. Aucun Kerberos ou approbations fédérées sont requises, car les utilisateurs sont automatiquement générés via l’application dans le domaine de service d’annuaire Active Directory du serveur Windows qui est également hébergé dans le cloud Azure machines virtuelles.

#### <a name="scenario-considerations-and-how-technology-areas-apply-to-the-scenario"></a>Remarques sur le scénario et comment les domaines technologiques s’appliquent au scénario

- [La topologie de réseau](#BKMK_NetworkTopology): créer un réseau virtuel Azure sans connexion cross locaux (également appelé site à connectivity).

- [Configuration de déploiement DC](#BKMK_DeploymentConfig): déployer un contrôleur de domaine dans une forêt Active Directory Windows Server nouveau domaine unique,. Il doit être déployé ainsi que le serveur DNS Windows.

- [Topologie de site Active Directory Windows Server](#BKMK_ADSiteTopology): utiliser le site Windows Server Active Directory par défaut (tous les ordinateurs seront dans Default-First-Site-Name).

- [Adresses IP et DNS](#BKMK_IPAddressDNS):

 - Définir une adresse IP statique pour le contrôleur de domaine à l’aide de l’applet de commande Set-AzureStaticVNetIP Azure PowerShell.
 - Installer et configurer DNS Windows Server sur calculateurs domaine sur Azure.
 - Configurer les propriétés de réseau virtuel avec le nom et l’adresse IP de la machine virtuelle qui héberge les rôles de serveur contrôleur de domaine et DNS.

- [Catalogue global](#BKMK_GC): premier contrôleur de domaine de la forêt doit être un serveur de catalogue global. DC supplémentaires doit également être configuré en tant que catalogue global car dans une forêt de domaine unique, le catalogue global ne nécessite pas tout travail supplémentaire du contrôleur de domaine.

- [Position de la base de données Windows Server AD DS et SYSVOL](#BKMK_PlaceDB): ajout d’un disque de données à exécuter en tant que machines virtuelles Azure pour stocker la base de données Active Directory de Windows Server, les journaux et SYSVOL contrôleur de domaine.

- [Sauvegarder et restaurer](#BKMK_BUR): déterminer l’endroit où vous voulez stocker les sauvegardes d’état système. Si nécessaire, ajoutez un autre disque de données de la machine virtuelle DC pour stocker des sauvegardes.

### <a name="BKMK_CloudOnlyFed"></a>2 AD FS : étendre une application frontale prenant en charge les revendications local à Internet

![Fédération avec connectivité entre local](media/active-directory-deploying-ws-ad-guidelines/Federation_xprem.png)
**Figure 2**

#### <a name="description"></a>Description

Une application prenant en charge les revendications qui a été correctement déployée en local et utilisé par les utilisateurs d’entreprise doit devenir accessibles directement à partir d’Internet. L’application sert à un site Web frontal web à une base de données SQL dans lequel il stocke les données. Les serveurs SQL utilisées par l’application sont également disponibles sur le réseau d’entreprise. Deux Windows Server AD FS STSs et un programme d’équilibrage de charge ont été déployés en local pour fournir un accès aux utilisateurs d’entreprise. L’application maintenant doive en outre accéder directement via Internet partenaires professionnels à l’aide de leurs propre identités d’entreprise et les utilisateurs d’entreprise existants.

Dans le but de simplifier et de répondre aux besoins de déploiement et la configuration de cette nouvelle exigence, il est décidé que deux supplémentaires web frontends et deux serveurs proxy Windows Server AD FS être installé sur des machines virtuelles Azure. Tous les ordinateurs quatre virtuels seront exposés directement à Internet et seront fournies connectivité au réseau local à l’aide de la fonctionnalité VPN de site à du réseau virtuel Azure.

#### <a name="scenario-considerations-and-how-technology-areas-apply-to-the-scenario"></a>Remarques sur le scénario et comment les domaines technologiques s’appliquent au scénario

- [La topologie de réseau](#BKMK_NetworkTopology): créer un réseau virtuel Azure et [configurer la connectivité entre locaux](../vpn-gateway/vpn-gateway-site-to-site-create.md).

 > [AZURE.NOTE] Pour chacun des certificats Windows Server AD FS, assurez-vous que l’URL défini dans le modèle de certificat et les certificats qui en résulte peut être atteintes par instances Windows Server AD FS s’exécutant sur Azure. Cela peut nécessiter la connectivité entre locaux à des parties de votre infrastructure à clé publique. Pour exemple si le point de terminaison de la liste de révocation est basé sur LDAP et hébergé en mode exclusif en local, puis croisée locaux connectivité sera requise. Si ce n’est pas opportun, il peut être nécessaire d’utiliser des certificats émis par une autorité de certification dont révocation de certificats est accessible via Internet.

- [Configuration des services cloud](#BKMK_CloudSvcConfig): disposer des deux services cloud dans l’ordre fournir des deux adresses IP virtuels équilibrage de charge. Adresse IP virtuelle du service cloud première sera dirigé vers deux Windows Server AD FS proxy ordinateurs virtuels sur les ports 80 et 443. Le serveur proxy machines virtuelles de Windows Server AD FS est configuré pour faire pointer vers l’adresse IP de l’équilibrage de charge local qui est le STSs FS Windows Server AD face. Adresse IP virtuelle du service cloud deuxième sera dirigé vers les deux ordinateurs virtuels réexécuter frontend web sur les ports 80 et 443. Configurer une sonde personnalisé pour vous assurer que l’équilibrage de charge dirige uniquement le trafic vers fonctionnelle Windows Server AD FS proxy et web site Web frontal machines virtuelles.

- [Configuration du serveur de fédération](#BKMK_FedSrvConfig): configurer Windows Server AD FS comme un serveur de fédération (STS) pour générer des jetons de sécurité pour la forêt Active Directory Windows Server créée dans le cloud. Fédération revendications fournisseur des relations d’approbation avec les partenaires différentes que vous voulez accepter des identités provenant et configurez les relations d’approbation tiers confiance avec les différentes applications que vous souhaitez générer des jetons à.

    Dans la plupart des scénarios, serveurs proxy Windows Server AD FS sont déployés dans une capacité orientés Internet pour des raisons de sécurité tandis que leurs équivalents Windows Server AD FS fédération restent isolés à partir d’une connexion Internet directe. Quelle que soit votre scénario de déploiement, vous devez configurer votre service cloud avec une adresse IP virtuelle qui fournit une adresse IP et le port qui est en mesure de l’équilibrage de charge entre soit votre deux Windows Server AD FS STS instances ou proxy exposée publiquement.

- [Configuration de disponibilité Windows Server AD FS](#BKMK_ADFSHighAvail): il est conseillé de déployer une batterie de serveurs Windows Server AD FS avec au moins deux serveurs pour le basculement et l’équilibrage de charge. Vous pourriez plus simple à l’aide de la base de données interne Windows (travail) pour les données de configuration Windows Server AD FS et utilisez la fonctionnalité d’équilibrage de charge de charge interne de Azure distribuer des demandes entrantes sur les serveurs de la batterie de serveurs.

Pour plus d’informations, voir le [Guide de déploiement AD DS](https://technet.microsoft.com/library/cc753963).


### <a name="BKMK_HybridExt"></a>3. AD DS : Déployer une application Windows Server AD DS prenant en charge devant se connecter au réseau d’entreprise

![Déploiement d’AD DS croisée locaux](media/active-directory-deploying-ws-ad-guidelines/ADDS_xprem.png)
**Figure 3**

#### <a name="description"></a>Description

Une application prenant en charge LDAP est déployée sur une machine virtuelle Azure. Il prend en charge l’authentification Windows intégrée et utilise Windows Server AD DS comme un référentiel pour les données de profil utilisateur et de configuration. L’objectif est de l’application tirer parti du service d’annuaire d’entreprise Windows Server Active Directory existant et fournir une authentification unique. L’application n’est pas prenant en charge les revendications. Les utilisateurs doivent également accéder à l’application directement à partir d’Internet. Pour optimiser les performances et de coûts, il est décidé que deux un contrôleur qui font partie d’un domaine d’entreprise déployé à côté de l’application sur Azure.

#### <a name="scenario-considerations-and-how-technology-areas-apply-to-the-scenario"></a>Remarques sur le scénario et comment les domaines technologiques s’appliquent au scénario

- [La topologie de réseau](#BKMK_NetworkTopology): créer un réseau virtuel Azure avec [croisée connectivité local](../vpn-gateway/vpn-gateway-site-to-site-create.md).

- [Méthode d’installation](#BKMK_InstallMethod): déployer DC réplica de domaine Active Directory Windows Server d’entreprise. Pour un double contrôleur de domaine, vous pouvez installer Windows Server AD DS sur la machine virtuelle et vous pouvez également utiliser la fonctionnalité installer à partir du média pour réduire la quantité de données devant être répliquées vers le nouveau contrôleur de domaine lors de l’installation. Pour un didacticiel, voir [installer un contrôleur de domaine Active Directory réplica sur Azure](../active-directory/active-directory-install-replica-active-directory-domain-controller.md). Même si vous utilisez installation, il peut être plus efficace pour générer la virtuel DC en local et déplacer l’ensemble disque dur virtuel vers le cloud au lieu de la réplication de Windows Server AD DS pendant l’installation. Pour la sécurité, il est recommandé de supprimer le disque dur virtuel à partir du réseau local une fois qu’il a été copié dans Azure.

- [Topologie de site Active Directory Windows Server](#BKMK_ADSiteTopology): créer un nouveau site Azure dans les Sites Active Directory et Services. Créez un objet sous-réseau Active Directory Windows Server pour représenter le réseau virtuel Azure et d’ajouter le sous-réseau au site. Créer un nouveau lien de site qui inclut le nouveau site Azure et le site dans lequel se trouve afin de contrôler et optimiser Windows Server Active Directory le trafic vers et depuis Azure le réseau virtuel Azure point de terminaison VPN.

- [Adresses IP et DNS](#BKMK_IPAddressDNS):

 - Définir une adresse IP statique pour le contrôleur de domaine à l’aide de l’applet de commande Set-AzureStaticVNetIP Azure PowerShell.
 - Installer et configurer DNS Windows Server sur calculateurs domaine sur Azure.
 - Configurer les propriétés de réseau virtuel avec le nom et l’adresse IP de la machine virtuelle qui héberge les rôles de serveur contrôleur de domaine et DNS.

- [Distribué geo DC](#BKMK_DistributedDCs): configurez les autres réseaux virtuels selon vos besoins. Si votre site Active Directory exige DC dans les zones géographiques qui correspondent aux différentes régions Azure, que vous voulez créer des sites Active Directory en conséquence.

- [Contrôleur de domaine en lecture seule](#BKMK_RODC): vous pouvez déployer un contrôleur dans le site Azure, selon vos besoins pour effectuer des opérations d’écriture contre le contrôleur de domaine et la compatibilité des applications et services sur le site avec RODC. Pour plus d’informations sur la compatibilité des applications, voir le [guide compatibilité des applications contrôleurs domaine en lecture seule](https://technet.microsoft.com/library/cc755190).

- [Catalogue global](#BKMK_GC): catalogues globaux est nécessaires pour traiter les demandes d’ouverture de session dans forêts plusieurs domaines. Si vous ne déployez pas un catalogue global dans le site Azure, vous implique des frais du trafic de sortie dans la mesure où les demandes d’authentification requêtes catalogues dans d’autres sites. Pour réduire le trafic, vous pouvez activer la mise en cache de l’appartenance groupe universel pour le site Azure dans les Sites Active Directory et Services.

    Si vous déployez un catalogue global, configurer les liens des sites et des coûts des liens de sites afin que le catalogue global dans le site Azure n’est pas utilisé comme source DC par d’autres catalogues globaux devant répliquer les même partitions partielle du domaine.

- [Position de la base de données Windows Server AD DS et SYSVOL](#BKMK_PlaceDB): ajout d’un disque de données à contrôleur de domaine s’exécutant sur machines virtuelles Azure afin de stocker la base de données Active Directory de Windows Server, les journaux et SYSVOL.

- [Sauvegarder et restaurer](#BKMK_BUR): déterminer l’endroit où vous voulez stocker les sauvegardes d’état système. Si nécessaire, ajoutez un autre disque de données de la machine virtuelle DC pour stocker des sauvegardes.

## <a name="deployment-decisions-and-factors"></a>Facteurs et les décisions de déploiement

Le tableau suivant résume les zones de la technologie Windows Server Active Directory qui sont affectés dans les scénarios précédents et les décisions correspondantes à prendre en considération, avec des liens vers plus en détail ci-dessous. Certaines zones de technologie peuvent ne pas être applicables à tous les scénarios de déploiement et certaines zones de technologie peuvent être plus critiques à un scénario de déploiement que les autres technologies utilisées.

Par exemple, si vous déployez un contrôleur de domaine réplica sur un réseau virtuel et votre forêt comporte un seul domaine, puis en choisissant déployer un serveur de catalogue global dans ce cas seront pas critique pour le scénario de déploiement, car il ne crée pas des exigences de réplication supplémentaires. En revanche, si la forêt possède plusieurs domaines, la décision de déployer un catalogue global sur un réseau virtuel peut affecter la bande passante disponible, performances, l’authentification, recherches dans l’annuaire et ainsi de suite.

| Zone de technologie Windows Server Active Directory | Décisions | Facteurs |
| ---- | ---- | ---- |
| [Topologie de réseau](#BKMK_NetworkTopology) | Créer un réseau virtuel ? | <li>Configuration requise pour accéder aux ressources d’entreprise</li> <li>Authentification</li> <li>Gestion des comptes</li> |
| [Configuration du déploiement](#BKMK_DeploymentConfig) | <li>Déployer une forêt distincte sans les approbations ?</li> <li>Déployer une nouvelle forêt avec la fédération ?</li> <li>Déployer une nouvelle forêt avec approbation de forêt Active Directory Windows Server ou Kerberos ?</li> <li>Étendre la forêt Corp en déployant un contrôleur de domaine réplica ?</li> <li>Étendre la forêt Corp en déployant un nouveau domaine enfant ou un arbre de domaine ?</li> | <li>Sécurité</li> <li>Conformité</li> <li>Coût</li> <li>Résilience et tolérance de panne</li> <li>Compatibilité des applications</li> |
| [Topologie de site Active Directory Windows Server](#BKMK_ADSiteTopology) | Comment configurer sous-réseaux, les sites et les liens des sites avec le réseau virtuel Azure afin d’optimiser le trafic et réduire les coûts ? | <li>Définitions de site et de sous-réseau</li> <li>Propriétés du lien de site et de notification de modification</li> <li>Compression de réplication</li> |
| [Adresses IP et DNS](#BKMK_IPAddressDNS) | Comment faire pour configurer les adresses IP et résolution de noms ? | <li>Utiliser l’applet de commande utilisation du jeu-AzureStaticVNetIP pour attribuer une adresse IP statique</li> <li>Installer Windows Server DNS server et configurer les propriétés de réseau virtuel avec le nom et l’adresse IP de la machine virtuelle qui héberge les rôles de serveur contrôleur de domaine et DNS</li> |
| [Distribué geo DC](#BKMK_DistributedDCs) | Comment répliquer vers DC sur des réseaux virtuels distincts ? | Si votre site Active Directory exige DC dans les zones géographiques qui correspond à différentes régions Azure, que vous voulez créer des sites Active Directory en conséquence. [Configurer le réseau virtuel au réseau virtuel connectivité](../vpn-gateway/virtual-networks-configure-vnet-to-vnet-connection.md) répliquer entre contrôleurs de domaine sur des réseaux virtuels distincts. |
| [Contrôleur de domaine en lecture seule](#BKMK_RODC) | Utiliser DC en lecture seule ou en écriture ? | <li>Filtrer les attributs IEA/personnelle</li> <li>Filtre secrets</li> <li>Limite le trafic sortant</li> |
| [Catalogue global](#BKMK_GC) | Installer le catalogue global ? | <li>Forêt à domaine unique, vérifiez tous les catalogues globaux DC</li> <li>Forêt à plusieurs domaines, catalogues globaux est requis pour l’authentification</li> |
| [Méthode d’installation](#BKMK_InstallMethod) | Comment installer DC dans Azure ? | Soit : <li>Installez les services AD DS à l’aide de Windows PowerShell ou Dcpromo</li> <li>Déplacer le disque dur virtuel d’un contrôleur de domaine virtuel en local</li> |
| [Position de la base de données Windows Server AD DS et SYSVOL](#BKMK_PlaceDB) | Emplacement de stockage Windows Server AD DS base de données, les journaux et SYSVOL ? | Modifier les valeurs par défaut Dcpromo.exe. Ces critique Active Directory fichiers *doivent* être placés sur des disques de données Azure au lieu de disques système d’exploitation qui implémentent cache d’écriture. |
| [Sauvegarde et restauration](#BKMK_BUR) | Comment faire pour sauvegarder et restaurer les données ? | Créer des sauvegardes d’état du système |
| [Configuration du serveur de fédération](#BKMK_FedSrvConfig) | <li>Déployer une nouvelle forêt avec la fédération dans le cloud ?</li> <li>Déployer ADFS en local et exposer un proxy dans le cloud ?</li> | <li>Sécurité</li> <li>Conformité</li> <li>Coût</li> <li>Accès aux applications par les partenaires commerciaux</li> |
| [Configuration des services cloud](#BKMK_CloudSvcConfig) | Un service cloud est déployé implicitement la première fois que vous créez une machine virtuelle. Avez-vous besoin déployer les services de cloud supplémentaire ? | <li>Une machine virtuelle ou machines virtuelles nécessite-t-il exposition directe à Internet ?</li> <li> Le service nécessite-t-il l’équilibrage de charge ?</li> |
| [Configuration requise du serveur de fédération publique et privée IP adressage (dynamique IP ou adresse IP virtuelle)](#BKMK_FedReqVIPDIP) | <li>L’instance FS Windows Server AD doit-il être contacté directement à partir d’Internet ?</li> <li>L’application déployée dans le cloud nécessite-t-il sa propre adresse IP via Internet et le port ?</li> | Créer un service cloud pour chaque adresse IP virtuelle qui est requis par votre déploiement |
| [Configuration de Windows Server AD FS haute disponibilité](#BKMK_ADFSHighAvail) | <li>Le nombre de nœuds dans ma batterie de serveurs Windows Server AD FS ?</li> <li>Le nombre de nœuds déployer dans ma batterie de serveurs proxy Windows Server AD FS ?</li> | Résilience et tolérance de panne |

### <a name="BKMK_NetworkTopology"></a>Topologie de réseau

Pour répondre à la cohérence des adresses IP et les exigences DNS de Windows Server AD DS, il est nécessaire de tout d’abord créer un [réseau virtuel Azure](../virtual-network/virtual-networks-overview.md) et attacher vos machines virtuelles à celui-ci. Lors de sa création, vous devez décider s’il faut étendre éventuellement connectivité à votre réseau d’entreprise en local, qui en toute transparence se connecte Azure machines virtuelles au machines en local, cela est effectuée à l’aide de technologies VPN traditionnels et requiert qu’un point de terminaison VPN être exposé sur le bord du réseau d’entreprise. Autrement dit, le réseau privé virtuel est lancé à partir d’Azure au réseau d’entreprise, pas vice versa.

Notez que des frais supplémentaires s’appliquent lors de l’extension d’un réseau virtuel à votre réseau local au-delà les frais standards qui s’appliquent à chaque machine virtuelle. Plus précisément, il existe des frais pour le temps processeur de la passerelle réseau virtuel Azure et pour le trafic de sortie généré par chaque ordinateur virtuel qui communique avec les ordinateurs locaux au réseau privé virtuel. Pour plus d’informations sur les frais le trafic réseau, voir [Azure tarifs un coup de œil](http://azure.microsoft.com/pricing/).

### <a name="BKMK_DeploymentConfig"></a>Configuration du déploiement

La manière de configurer le contrôleur de domaine dépend de la configuration requise pour le service que vous voulez exécuter sur Azure. Par exemple, vous pouvez déployer une nouvelle forêt, isolé à partir de votre propre forêt de l’entreprise, pour tester une preuve de concept, une nouvelle application ou un autre projet à court terme nécessitant des services d’annuaire mais un accès non spécifique aux ressources d’entreprise internes.

Comme un avantage, une forêt isolé avec que DC ne réplique pas locale DC, ce qui entraîne le trafic réseau moins sortant généré par le système lui-même directement réduire les coûts. Pour plus d’informations sur les frais le trafic réseau, voir [Azure tarifs un coup de œil](http://azure.microsoft.com/pricing/).

Un autre exemple, supposons que vous avez des besoins de confidentialité d’un service, mais le service dépend de l’accès à votre interne Windows Server Active Directory. Si vous êtes autorisé pour héberger des données pour le service dans le cloud, vous pouvez déployer un nouveau domaine enfant pour votre forêt interne sur Azure. Dans ce cas, vous pouvez déployer un contrôleur de domaine pour le nouveau domaine enfant (sans le catalogue global afin d’aider à résoudre les problèmes de confidentialité). Ce scénario, ainsi que d’un déploiement DC, réplica nécessite un réseau virtuel pour la connectivité avec votre DC local.

Si vous créez une nouvelle forêt, choisissez si vous voulez utiliser [approbations Active Directory](https://technet.microsoft.com/library/cc771397) [Federation approuve](https://technet.microsoft.com/library/dd807036). Équilibrer les besoins émanant de la compatibilité, la sécurité, conformité, coût et résilience. Par exemple, pour tirer parti de [l’authentification sélective](https://technet.microsoft.com/library/cc755844) , vous pouvez choisir déployer une nouvelle forêt sur Azure et créer une approbation Active Directory Windows Server entre la forêt en local et le cloud. Toutefois, si l’application est prenant en charge les revendications, vous pouvez déployer les approbations de fédération au lieu des approbations de forêt Active Directory. Un autre facteur sera le coût répliquer davantage de données en étendant votre local Windows Server Active Directory dans le cloud ou générer le trafic sortant plus résulte d’authentification et chargement de requête.

Configuration requise pour la disponibilité et tolérance de panne répercutées dans votre choix. Par exemple, si le lien est interrompu, exploiter les une approbation Kerberos ou une fédération de confiance des applications sont toutes probablement entièrement incorrecte, sauf si vous avez déployé infrastructure suffisante sur Azure. Configurations de déploiement de remplacement comme réplica DC (accessible en écriture ou RODC) augmenter la probabilité d’être en mesure de tolérer défaillances du lien.

### <a name="BKMK_ADSiteTopology"></a>Topologie de site Active Directory Windows Server

Vous devez définir correctement les sites et les liens des sites afin d’optimiser le trafic et réduire les coûts. Sites, les liens des sites et les sous-réseaux affectent la topologie de réplication entre DC et le flux du trafic d’authentification. Prendre en compte les frais le trafic suivant et déployer et configurer DC selon les besoins de votre scénario de déploiement :

- Il existe un abonnement nominal par heure de la passerelle lui-même :

 - Il peut être démarré et arrêté selon vos besoins

 - Si vous avez arrêté, Azure machines virtuelles sont isolées du réseau d’entreprise

- Le trafic entrant est gratuit

- Le trafic sortant est facturé selon [Azure tarifs un coup de œil](http://azure.microsoft.com/pricing/). Vous pouvez optimiser les propriétés de lien de site entre sites en local et le cloud comme suit :

 - Si vous utilisez plusieurs réseaux virtuels, configurez les liaisons de sites et de leurs coûts correctement pour éviter que Windows Server AD DS de hiérarchiser le site Azure au-dessus d’une pouvant lui fournir les mêmes niveaux de service sans frais. Vous pouvez également envisager de désactivation de la passerelle de toutes les sites option lien (BASL) (qui est activée par défaut). Cela garantit que les sites uniquement directement connecté répliquent entre eux. DC de sites transitivement connectés n’est plus en mesure de répliquer directement entre eux, mais doit se répliquer via un ou plusieurs sites courantes. Si les sites intermédiaires deviennent indisponibles pour une raison quelconque, la réplication entre DC dans sites transitivement connectés n’a pas lieu même si la connectivité entre les sites est disponible. Enfin, l’emplacement dans lequel les sections du comportement de réplication transitifs restent opportun, créer site ponts qui contiennent les liens de site appropriés et sites, par exemple en local, les sites de réseau d’entreprise.

 - [Coûts de liaison de site de configurer](https://technet.microsoft.com/library/cc794882) correctement pour éviter le trafic involontaire. Par exemple, si vous **Essayez de Site le plus proche suivant** paramètre est activé, vérifiez que les sites de réseau virtuel ne sont pas la proche en augmentant le coût associé à l’objet de liaison de sites qui connecte le site Azure revenir au réseau d’entreprise.

 - Configurer le site lien [intervalles](https://technet.microsoft.com/library/cc794878) et des [planifications](https://technet.microsoft.com/library/cc816906) en fonction des exigences de cohérence et taux de modifications de l’objet. Planification de la réplication s’alignent tolérance de latence. DC réplique uniquement le dernier état d’une valeur, afin de réduire l’intervalle de réplication peut enregistrer les coûts s’il existe un objet suffisamment modifier taux.

- Réduire les coûts soit une priorité, assurez-vous réplication est planifiée et notification de modification n’est pas activée. Il s’agit de la configuration par défaut lors de la réplication entre sites. Ceci n’est pas important si vous déployez un contrôleur sur un réseau virtuel, car celui-ci ne va pas répliquer les modifications sortantes. Mais si vous déployez un contrôleur de domaine accessible en écriture, vous devez vous assurer que la liaison de sites n’est pas configurée pour dupliquer les mises à jour fréquence inutile. Si vous déployez un serveur de catalogue global (catalogue global), assurez-vous que tous les sites qui contient un catalogue global réplique partitions de domaine à partir d’une source de contrôleur de domaine dans un site qui est connecté avec un lien de sites ou les liens de sites qui ont un coût inférieur le catalogue global dans le site Azure.


- Il est possible de réduire encore le trafic réseau généré par la réplication entre sites en modifiant l’algorithme de compression de réplication. L’algorithme de compression est contrôlé par l’algorithme de compression REG_DWORD Registre entrée HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\NTDS\Parameters\Replicator. La valeur par défaut est 3, qui correspond à l’algorithme Xpress compresser. Vous pouvez modifier la valeur à 2, ce qui détermine l’algorithme de MSZip. Dans la plupart des cas, cela augmente la compression, mais cela se fait au détriment de l’UC. Pour plus d’informations, voir [comment Active Directory la topologie de réplication fonctionne](https://technet.microsoft.com/library/cc755994).

### <a name="BKMK_IPAddressDNS"></a>Adresses IP et DNS

Machines virtuelles Azure sont affectés « adresses allouées DHCP » par défaut. Étant donné que les adresses réseau virtuel Azure dynamiques persistent avec une machine virtuelle pour la durée de vie de la machine virtuelle, la configuration requise de Windows Server AD DS est remplie.

Par conséquent, lorsque vous utilisez une adresse dynamique sur Azure, vous êtes en réalité à l’aide d’une adresse IP statique, car il est prenant pour la durée du contrat et la durée du contrat est égale à la durée de vie du service cloud.

Toutefois, l’adresse dynamique est libéré si la machine virtuelle est arrêté. Pour empêcher l’adresse IP d’aurait, vous pouvez [utiliser Set-AzureStaticVNetIP pour attribuer une adresse IP statique](http://social.technet.microsoft.com/wiki/contents/articles/23447.how-to-assign-a-private-static-ip-to-an-azure-vm.aspx).

Résolution de noms, déployer vos propres (ou optimisez votre) infrastructure de serveurs DNS ; Azure communiquée DNS ne répond pas aux besoins de résolution de nom avancées de Windows Server AD DS. Par exemple, il ne pas prise en charge des enregistrements SRV dynamiques et ainsi de suite. Résolution de noms est un élément de configuration critiques pour DC et clients à un domaine. Contrôleur de domaine doit être capable d’enregistrement des enregistrements de ressource et de résolution des enregistrements de ressource d’autres DC.
Pour des raisons de performances et tolérance de panne, il est optimal pour installer le service DNS Windows Server sur les DC s’exécutant sur Azure. Puis configurer les propriétés de réseau virtuel Azure avec le nom et l’adresse IP du serveur DNS. Lors du démarrage d’autres machines virtuelles sur le réseau virtuel, leurs paramètres du solveur DNS client seront configurés avec un serveur DNS dans le cadre de l’allocation d’adresses IP dynamique.

> [AZURE.NOTE] Vous ne pouvez pas rejoindre ordinateurs locaux à un domaine Active Directory Windows Server AD DS qui est hébergé sur Azure directement via Internet. La configuration requise ports pour Active Directory et l’opération de jointure de domaine rendent difficile de directement expose les ports nécessaires et en réalité, un contrôleur de domaine entier à Internet.

Machines virtuelles enregistrent leur nom DNS automatiquement au démarrage ou lorsqu’il y a un changement de nom.

Pour plus d’informations sur cet exemple et un autre exemple qui montre comment configurer la première machine virtuelle et installer AD DS sur celui-ci, voir [installer une nouvelle forêt Active Directory sur Microsoft Azure](active-directory-new-forest-virtual-machine.md). Pour plus d’informations sur l’utilisation de Windows PowerShell, voir [Installer Azure PowerShell](../powershell-install-configure.md) et les [Applets de commande de gestion Azure](https://msdn.microsoft.com/library/azure/jj152841).

### <a name="BKMK_DistributedDCs"></a>Distribué geo DC

Azure offre les avantages suivants lors de l’hébergement plusieurs DC sur des réseaux virtuels différents :

- La tolérance de panne sur plusieurs sites

- Proximité physique succursales (latence inférieure)

Pour plus d’informations sur la configuration des communications directes entre les réseaux virtuels, consultez [configurer réseau virtuel pour la connectivité réseau virtuel](../vpn-gateway/virtual-networks-configure-vnet-to-vnet-connection.md).

### <a name="BKMK_RODC"></a>Contrôleur de domaine en lecture seule

Vous devez choisir si vous souhaitez déployer DC en lecture seule ou en écriture. Vous pourriez être incliné à déployer RODC, car vous n’aurez pas de contrôle physique dessus, mais RODC est conçus pour être déployés dans des emplacements où leur sécurité physique est exposés, tels que les succursales.

Azure ne présente pas le risque de sécurité physique d’une filiale, mais RODC peut toujours s’avérer plus rentables parce que leurs fonctionnalités sont adaptées à ces environnements, alors que pour des raisons très différente. Par exemple, RODC n’ont aucune réplication sortante et est en mesure de manière sélective compléter secrets (mots de passe). En revanche, l’absence de ces secrets peut nécessiter à la demande le trafic sortant pour les valider en tant qu’utilisateur ou ordinateur authentifie. Mais secrets pouvant être sélectivement préremplies et mis en cache.

RODC fournit un avantage supplémentaire dans et autour de questions IEA et les informations d’identification personnelle, car vous pouvez ajouter des attributs qui contiennent des données sensibles sur le contrôleur filtré jeu d’attributs (FA). FA est un ensemble personnalisable des attributs qui ne sont pas répliquées vers RODC. Vous pouvez utiliser les FA comme une sauvegarde au cas où vous ne sont pas autorisées ou que vous ne souhaitez pas stocker les informations d’identification personnelle ou IEA sur Azure. Pour plus d’informations, voir [contrôleur filtrée attribut. définir [(https://technet.microsoft.com/library/cc753459)]

Assurez-vous que les applications seront compatibles avec RODC vous prévoyez d’utiliser. De nombreuses applications compatibles avec Active Directory Windows Server fonctionnent correctement avec RODC, mais certaines applications peuvent exécuter inefficace ou échouer s’ils n’ont pas accès à un contrôleur de domaine accessible en écriture. Pour plus d’informations, voir le [guide de compatibilité des applications DC accessible en lecture seule](https://technet.microsoft.com/library/cc755190).

### <a name="BKMK_GC"></a>Catalogue global

Vous devez choisir d’installer un catalogue global (catalogue global). Dans une forêt de domaine unique, vous devez configurer tous les DC comme serveurs de catalogue global. Augmente les coûts pas, car il n’y ait aucun trafic de réplication supplémentaire.

Dans une forêt à plusieurs domaines, les catalogues globaux est nécessaires pour développer universel appartenances pendant le processus d’authentification. Si vous ne déployez pas un catalogue global, charges de travail sur le réseau virtuel qui permettent d’authentifier par rapport à un DC sur Azure générera indirectement le trafic d’authentification sortante pour interroger des catalogues globaux en local lors de chaque tentative d’authentification.

Les coûts liés aux catalogues globaux sont moins prévisibles, car ils hébergent chaque domaine (en partie). Si la charge de travail héberge un service Internet public et authentifie les utilisateurs par rapport à Windows Server AD DS, les coûts peuvent être complètement inattendus. Pour réduire les requêtes de catalogue global en dehors du site cloud lors de l’authentification, vous pouvez [Activer le cache d’appartenance groupe universel](https://technet.microsoft.com/library/cc816928).

### <a name="BKMK_InstallMethod"></a>Méthode d’installation

Vous devez choisir comment installer DC sur le réseau virtuel :

- Promouvoir DC nouveau. Pour plus d’informations, voir [installer une nouvelle forêt Active Directory sur un réseau virtuel Azure](active-directory-new-forest-virtual-machine.md).

- Déplacer le disque dur virtuel d’un contrôleur de domaine virtuel local vers le cloud. Dans ce cas, vous devez vous assurer que le contrôleur de domaine virtuel local est » déplacé, « pas « copiées » ou « cloné ».

Utiliser uniquement Azure machines virtuelles contrôleur de domaine (par opposition à « web » ou « collaborateur » rôle Azure machines virtuelles). Il s’agit résistants et durabilité d’état pour un contrôleur de domaine est requise. Machines virtuelles Azure sont conçues pour les charges de travail comme contrôleur de domaine.

N’utilisez pas SYSPREP pour déployer ou cloner DC. La possibilité de cloner DC est uniquement disponible début dans Windows Server 2012. La fonctionnalité clonage nécessite prise en charge pour VMGenerationID dans l’hyperviseur sous-jacente. Dans Windows Server 2012 et Azure réseaux virtuels Hyper-V les deux prennent en charge VMGenerationID, comme les fournisseurs de logiciels tiers virtualisation.

### <a name="BKMK_PlaceDB"></a>Position de la base de données Windows Server AD DS et SYSVOL

Sélectionner l’emplacement de localiser la base de données Windows Server AD DS, les journaux et SYSVOL. Ils doivent être déployés sur disques de données Azure.

> [AZURE.NOTE] Azure disques de données sont limités à 1 To.

Données disques n'effectuez pas cache écrit par défaut. Lecteurs de disques de données qui sont joints à un ordinateur virtuel utilisez écriture via la mise en cache. Écriture via la mise en cache rend que l’écriture s’engage à stockage Azure résistant avant la fin du point de vue du système d’exploitation de la machine virtuelle la transaction. Il assure la durabilité et au détriment des écritures légèrement plus lentes.

Ceci est important pour Windows Server AD DS, car le cache de disque écriture différée invalide hypothèses par le contrôleur de domaine. Windows Server AD DS tente de désactiver le cache d’écriture, mais il est sur le disque système IO y répondre. Échec de désactiver le cache d’écriture peut-être, dans certaines circonstances, introduire reprise USN résultant en attente des objets et autres problèmes.

Meilleurs résultats virtuel contrôleur de domaine, procédez comme suit :

- Définissez le paramètre de préférence de Cache hôte sur le disque de données Azure pour aucun. Cela empêche les problèmes de cache d’écriture pour les opérations AD DS.

- Stocker la base de données, les journaux et SYSVOL sur le même soit données ou les disques de données distinct. En règle générale, il s’agit d’un disque distinct à partir du disque utilisé pour le système d’exploitation lui-même. Clé à retenir est que la base de données Windows Server AD DS et SYSVOL ne doivent pas être stockés sur un type de disque système d’exploitation Azure. Par défaut, le processus d’installation AD DS installe ces composants dans le dossier % SystemRoot%, qui n’est pas recommandé pour Azure.

### <a name="BKMK_BUR"></a>Sauvegarde et restauration

N’oubliez pas de qu’est n'est pas pris en charge pour la sauvegarde et restauration d’un contrôleur de domaine en général et plus particulièrement ceux en cours d’exécution sur un ordinateur virtuel. Voir [sauvegarde et restauration considérations virtualisé contrôleur de domaine](https://technet.microsoft.com/library/virtual_active_directory_domain_controller_virtualization_hyperv#backup_and_restore_considerations_for_virtualized_domain_controllers).

Créer des sauvegardes d’état du système à l’aide uniquement les logiciels de sauvegarde prenant spécifiquement connaissance des exigences de sauvegarde de service d’annuaire Active Directory du serveur Windows, tels que Windows Server sauvegarde.

Ne pas copier ou dupliquer des fichiers de disque dur virtuel de DC au lieu d’effectuer des sauvegardes régulières. Une restauration jamais est requise, cela, à l’aide de disques durs virtuels clonés ou copiées sans Windows Server 2012 et un hyperviseur pris en charge présentera USN bulles.

### <a name="BKMK_FedSrvConfig"></a>Configuration du serveur de fédération

La configuration des serveurs de fédération Windows Server AD FS (STSs) dépend en partie indique si les applications que vous voulez déployer sur Azure ont besoin d’accéder aux ressources sur votre réseau local.

Si elles répondent aux critères suivants, vous pouvez déployer les applications dans isolement à partir de votre réseau local.

- Ils acceptent les jetons de sécurité SAML

- Ils sont exposable à Internet

- Ils ne peuvent pas accèdent ressources locales

Dans ce cas, configurez Windows Server AD FS STSs comme suit :

1. Configurer une forêt de domaine unique isolée sur Azure.

2. Fournir un accès fédéré à la forêt en configurant une batterie de serveurs de fédération Windows Server AD FS.

3. Configurer Windows Server AD FS (batterie de serveurs de fédération et batterie de serveurs de fédération serveur proxy) dans la forêt locale.

4. Établir une relation d’approbation de fédération entre les locaux et Azure instances de Windows Server AD FS.

En revanche, si les applications nécessitent un accès aux ressources locales, vous pouvez configurer Windows Server AD FS avec l’application sur Azure comme suit :

1. Configurer la connectivité entre réseaux locaux et Azure.

2. Configurer une batterie de serveurs de fédération Windows Server AD FS de la forêt locale.

3. Configurer une batterie de serveurs proxy de serveur fédération Windows Server AD FS sur Azure.

Cette configuration a l’avantage de réduire l’exposition des ressources locales, similaires à la configuration de Windows Server AD FS avec des applications dans un réseau de périmètre.

Notez que dans les deux cas, vous pouvez établir les relations d’approbation avec les autres fournisseurs d’identité, si vous avez besoin de collaboration pour entreprises.

### <a name="BKMK_CloudSvcConfig"></a>Configuration des services cloud

Les services en nuage sont nécessaires si vous voulez pour exposer une machine virtuelle directement à Internet ou pour exposer une application d’équilibrage de charge via Internet. Cela est possible, car chaque service cloud offre une adresse IP virtuelle configurable unique.

### <a name="BKMK_FedReqVIPDIP"></a>Configuration requise du serveur de fédération publique et privée IP adressage (dynamique IP ou adresse IP virtuelle)

Chaque machine virtuelle Azure reçoit une adresse IP dynamique. Une adresse IP dynamique est une adresse privée accessible uniquement dans Azure. Dans la plupart des cas, cependant, il sera nécessaire configurer une adresse IP virtuelle pour vos déploiements Windows Server AD FS. L’adresse IP virtuelle est nécessaire pour exposer des points de terminaison de Windows Server AD FS à Internet et sera utilisé par les clients et partenaires fédérés pour l’authentification et la gestion en continu. Une adresse IP virtuelle est une propriété d’un service cloud qui contient une ou plusieurs machines virtuelles Azure. Si l’application prenant en charge les revendications déployée sur Azure et Windows Server AD FS est orientés Internet et partager les ports courants, chacun nécessite une adresse IP virtuelle de sa propre et il sera par conséquent nécessaire créer un service cloud pour l’application et le second pour Windows Server AD FS.

Pour obtenir les définitions des termes adresse IP virtuelle et l’adresse IP dynamique, voir les [termes et définitions](#BKMK_Glossary).

### <a name="BKMK_ADFSHighAvail"></a>Configuration de Windows Server AD FS haute disponibilité

Bien qu’il soit possible de déployer les services de fédération autonome Windows Server AD FS, il est recommandé pour le déploiement d’une batterie de serveurs au moins deux nœuds pour AD FS STS et proxys pour les environnements de production.

Voir [Considérations relatives à la topologie AD FS 2.0 déploiement](https://technet.microsoft.com/library/gg982489) dans la [AD FS 2.0 création Guide](https://technet.microsoft.com/library/dd807036) choisir les options de configuration de déploiement meilleures selon vos besoins particuliers.

> [AZURE.NOTE] Afin d’obtenir des points de terminaison Windows Server AD FS sur Azure d’équilibrage de charge, configurez tous les membres de la batterie de serveurs Windows Server AD FS dans le même service cloud et utilisez les capacités équilibrage de la charge des Azure pour le protocole HTTP (80 par défaut) et les ports HTTPS (443 par défaut). Pour plus d’informations, voir [équilibrage de charge Azure sonde](https://msdn.microsoft.com/library/azure/jj151530).
Windows Server réseau charge équilibrage n’est pas pris en charge sur Azure.

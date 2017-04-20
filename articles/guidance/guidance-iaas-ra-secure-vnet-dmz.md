<properties
   pageTitle="Architecture de Windows Azure référence - IaaS : Place d’un DMZ entre Azure et Internet | Microsoft Azure"
   description="Comment mettre en œuvre une architecture de réseau sécurisée hybride avec accès à Internet dans Azure."
   services="guidance,vpn-gateway,expressroute,load-balancer,virtual-network"
   documentationCenter="na"
   authors="telmosampaio"
   manager="christb"
   editor=""
   tags="azure-resource-manager"/>

<tags
   ms.service="guidance"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/24/2016"
   ms.author="telmos"/>

# <a name="implementing-a-dmz-between-azure-and-the-internet"></a>Mise en œuvre une DMZ entre Azure et Internet

[AZURE.INCLUDE [pnp-RA-branding](../../includes/guidance-pnp-header-include.md)]

Cet article décrit les meilleures pratiques pour implémenter un réseau hybride sécurisée qui s’étend votre réseau local qui accepte le trafic du réseau internet vers Azure. Cette architecture de référence étend l’architecture décrite dans l’article de la [place d’un DMZ entre Azure et votre centre de données locale][implementing-a-secure-hybrid-network-architecture]. Il est recommandé de vous lisez ce document et comprenez qui font référence architecture avant la lecture de ce document.

> [AZURE.NOTE] Azure comporte deux modèles de déploiement différents : [Le Gestionnaire de ressources] [ resource-manager-overview] et classique. Cette architecture de référence utilise le Gestionnaire de ressources, Microsoft recommande pour les nouveaux déploiements. 

Scénarios d’utilisation classiques pour cette architecture sont les suivantes :

- Applications hybrides où les charges de travail exécuter partiellement en local et Azure partiellement en.

- Azure infrastructure qui achemine le trafic entrant depuis en local et sur internet.

## <a name="architecture-diagram"></a>Diagramme d’architecture

Le diagramme suivant met en évidence les éléments importants de cette architecture :

> Un document Visio incluant ce diagramme architecture est disponible au téléchargement sur le [Centre de téléchargement Microsoft][visio-download]. Ce diagramme est dans la page « DMZ – Public ».

[! [0]][0]

- **Adresse IP publique (PIP).** Il s’agit de l’adresse IP du point de terminaison public. Les utilisateurs externes connectés à Internet peuvent accéder au système via cette adresse.

- **Équipement réseau virtuel (à couloirs).**  À couloirs est un terme générique décrivant une machine virtuelle effectuant des tâches comme autoriser ou refuser l’accès comme un pare-feu, optimisation opérations WAN (y compris la compression réseau), routage personnalisé ou d’autres fonctionnalités réseau.

- **Équilibrage de charge Azure.** Toutes les demandes entrantes à partir d’internet passent par cet équilibrage de charge et distribués à NVAs dans la zone DMZ publique sous-réseau entrante.

- **DMZ publique entrant sous-réseau.** Ce sous-réseau accepte les demandes de l’équilibrage de charge Azure. Demandes entrantes sont passés à un de le NVAs dans la zone DMZ.

- **Sous-réseau sortant DMZ public.** Demandes approuvés par l’à couloirs traversent ce sous-réseau à l’équilibrage de charge interne pour la couche web.

## <a name="recommendations"></a>Recommandations

Azure offre de nombreuses ressources différentes et des types de ressources, de sorte que cette architecture de référence peut être mis en service de différentes manières. Nous vous fournissons un modèle Azure le Gestionnaire de ressources pour installer l’architecture de référence qui suit ces recommandations. Si vous choisissez de créer votre propre architecture de référence, vous devez suivre ces recommandations à moins d’avoir un besoin spécifique une recommandation ne tient pas.

### <a name="nva-recommendations"></a>Recommandations à couloirs

Mise en œuvre d’un ensemble de NVAs pour le trafic provenant d’internet et l’autre pour le trafic local d’origine. Il s’agit d’un problème de sécurité à n'utiliser qu’un seul ensemble de NVAs pour les deux étant donné que cette conception ne fournit aucun périmètre de sécurité entre les deux jeux de trafic réseau. Il est un avantage à servez-vous de ce, car elle réduit la complexité de la vérification des règles de sécurité et clairement, les règles qui correspondent à chaque demande réseau entrante. Par exemple, un ensemble de NVAs met en œuvre les règles pour le trafic internet uniquement lors d’un autre ensemble de règles de mise en œuvre NVAs pour le trafic local uniquement.

Inclure un calque 7 à couloirs pour arrêter les connexions d’application au niveau à couloirs et conserve les affinités avec les couches principales. Cela garantit la connectivité symétrique dans lequel le trafic de réponse dans les couches principales retourne par le biais à couloirs.  

### <a name="public-load-balancer-recommendations"></a>Recommandations d’équilibrage de charge public ###

Pour conserver la disponibilité et extensibilité élevées, déployer la DMZ publique entrant NVAs dans une [disponibilité définie] [ availability-set] et utiliser un [internet facing équilibrage de charge] [ load-balancer] pour distribuer les demandes internet dans les NVAs dans le jeu de disponibilité.  

Configurer l’équilibrage de charge pour accepter les requêtes uniquement sur les ports nécessaires pour le trafic internet. Par exemple, restreindre les requêtes HTTP entrantes vers le port 80 et les requêtes HTTPS entrantes vers le port 443.

## <a name="scalability-considerations"></a>Considérations relatives à l’extensibilité élevées

Concevoir votre infrastructure via une connexion internet facing équilibrage de charge devant le sous-réseau DMZ public entrant dès le début. Même si votre initialement architecture requiert une seule à couloirs, il sera plus facile pour s’adapter à plusieurs NVAs à l’avenir si internet facing équilibrage de charge est déjà déployé.

## <a name="availability-considerations"></a>Considérations relatives à la disponibilité

Internet facing équilibrage de charge nécessite à chaque couloirs dans la zone DMZ Public sous-réseau entrant pour mettre en œuvre une [sonde santé][lb-probe]. Une sonde santé qui répond sur ce point de terminaison est considéré comme indisponible, et l’équilibrage de charge dirigera les requêtes à d’autres NVAs dans le même jeu de disponibilité. Notez que si tous les NVAs ne répondent pas, votre application échouera aussi est-il important de surveillance configuré pour DevOps alerte lorsque le nombre d’instances à couloirs exact tombe en dessous d’un seuil.

## <a name="manageability-considerations"></a>Considérations relatives à la facilité de gestion

Restreindre les fonctionnalités de gestion et de contrôle pour de l’entrante public DMZ NVA à répondre aux demandes de la zone passer du sous-réseau gestion uniquement. Comme indiqué à [place d’un DMZ entre Azure et votre centre de données locale] [ implementing-a-secure-hybrid-network-architecture] document, définissez un itinéraire réseau unique à partir du réseau local via la passerelle vers la zone saut dans le sous-réseau management pour restreindre l’accès.

Si la connectivité de passerelle à partir de votre réseau local vers Azure est vers le bas, vous pouvez toujours accéder à la boîte de déviation en déployant un PIP, en ajoutant à la zone passer et l’accès distant dans à partir d’internet.

## <a name="security-considerations"></a>Considérations sur la sécurité

Cette architecture de référence met en œuvre plusieurs niveaux de sécurité :

- Internet facing équilibrage de charge dirige les requêtes vers les NVAs du sous-réseau DMZ entrant public uniquement et uniquement sur les ports nécessaires à l’application.

- Les règles NSG pour le sous-réseau DMZ public entrant et sortant empêchent les NVAs d’être compromis en bloquant des requêtes qui se trouvent en dehors de règles NSG.

- La configuration du routage NAT pour les NVAs dirige des demandes entrantes sur les ports 80 et 443 à l’équilibrage de charge couche web, mais ignore les requêtes sur tous les autres ports.

Notez que vous devez vous connecter toutes les demandes entrantes sur tous les ports. Régulièrement des journaux d’audit, en faisant attention aux requêtes qui se trouvent en dehors de paramètres attendus comme celles-ci peuvent signaler les tentatives d’intrusion.

### <a name="using-nsgs-to-blockpass-traffic-between-application-tiers"></a>À l’aide de NSGs pour bloquer/test le trafic entre les niveaux de l’application

Chacune des couches web, business et données de restreindre le trafic entre eux à l’aide de NSGs. En d’autres termes, la couche d’entreprise utilise un NSG pour bloquer le trafic ne provenant de la couche web et la couche données utilise un NSG pour bloquer le trafic ne provenant de la couche d’entreprise. Si vous avez besoin pour développer les règles NSG pour autoriser l’accès plus large à ces niveaux, comparez ces exigences contre les risques de sécurité. Chaque nouvelle allée entrante représente une opportunité données accidentelle ou délibérée fuites ou à l’application n’est pas endommagé.

## <a name="solution-deployment"></a>Déploiement de solution

Un déploiement pour une architecture de référence qui mettent en œuvre, ces recommandations est disponible sur Github. Cette architecture de référence inclut un réseau virtuel (VNet), groupe de sécurité réseau (NSG), équilibrage de charge et deux machines virtuelles (machines virtuelles).

L’architecture de référence peut être déployé avec Windows ou Linux machines virtuelles en suivant les instructions ci-dessous : 

1. Cliquez avec le bouton droit sur le bouton ci-dessous, puis sélectionnez soit « ouvrir le lien dans un nouvel onglet » ou « Ouvrir le lien dans une nouvelle fenêtre » :  
[![Déploiement d’Azure](./media/blueprints/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fmspnp%2Freference-architectures%2Fmaster%2Fguidance-hybrid-network-secure-vnet-dmz%2FvirtualNetwork.azuredeploy.json)

2. Une fois que le lien a ouvert dans le portail Azure, vous devez entrer des valeurs pour certains paramètres : 
    - Le nom du **groupe de ressources** est déjà défini dans le fichier de paramètres : sélectionnez **Créer un nouveau** et entrez `ra-public-dmz-network-rg` dans la zone de texte.
    - Sélectionnez la région dans la zone de liste déroulante **emplacement** .
    - Ne modifiez pas **Modèle racine Uri** ou les zones de texte **Paramètre racine Uri** .
    - Sélectionnez le **Type de système d’exploitation** dans la zone déroulante zone, **windows** ou **linux**.
    - Passez en revue les termes et conditions, puis cliquez sur la case à cocher **que j’accepte les termes et conditions indiquées ci-dessus** .
    - Cliquez sur le bouton **achat** .

3. Attendez que le déploiement terminer.

4. Cliquez avec le bouton droit sur le bouton ci-dessous, puis sélectionnez soit « ouvrir le lien dans un nouvel onglet » ou « Ouvrir le lien dans une nouvelle fenêtre » :  
[![Déploiement d’Azure](./media/blueprints/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fmspnp%2Freference-architectures%2Fmaster%2Fguidance-hybrid-network-secure-vnet-dmz%2Fworkload.azuredeploy.json)

5. Une fois que le lien a ouvert dans le portail Azure, vous devez entrer des valeurs pour certains paramètres : 
    - Le nom du **groupe de ressources** est déjà défini dans le fichier de paramètres : sélectionnez **Créer un nouveau** et entrez `ra-public-dmz-wl-rg` dans la zone de texte.
    - Sélectionnez la région dans la zone de liste déroulante **emplacement** .
    - Ne modifiez pas **Modèle racine Uri** ou les zones de texte **Paramètre racine Uri** .
    - Passez en revue les termes et conditions, puis cliquez sur la case à cocher **que j’accepte les termes et conditions indiquées ci-dessus** .
    - Cliquez sur le bouton **achat** .

6. Attendez que le déploiement terminer.

7. Cliquez avec le bouton droit sur le bouton ci-dessous, puis sélectionnez soit « ouvrir le lien dans un nouvel onglet » ou « Ouvrir le lien dans une nouvelle fenêtre » :  
[![Déploiement d’Azure](./media/blueprints/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fmspnp%2Freference-architectures%2Fmaster%2Fguidance-hybrid-network-secure-vnet-dmz%2Fsecurity.azuredeploy.json)

8. Une fois que le lien a ouvert dans le portail Azure, vous devez entrer des valeurs pour certains paramètres : 
    - Le nom du **groupe de ressources** est déjà défini dans le fichier de paramètres : sélectionnez **Utiliser existant** et saisir `ra-public-dmz-network-rg` dans la zone de texte.
    - Sélectionnez la région dans la zone de liste déroulante **emplacement** .
    - Ne modifiez pas **Modèle racine Uri** ou les zones de texte **Paramètre racine Uri** .
    - Passez en revue les termes et conditions, puis cliquez sur la case à cocher **que j’accepte les termes et conditions indiquées ci-dessus** .
    - Cliquez sur le bouton **achat** .

9. Attendez que le déploiement terminer.

10. Les fichiers de paramètres incluent nom codé en dur administrateur d’utilisateur et mot de passe pour tous les ordinateurs virtuels, et il est fortement recommandé que vous modifiez immédiatement les deux. Pour chaque machine virtuelle dans le déploiement, sélectionnez-le dans le portail Azure et puis cliquez sur **Réinitialiser votre mot de passe** dans la carte de **Support + résolution des problèmes** . Sélectionnez **Réinitialiser votre mot de passe** dans la zone de liste déroulante **Mode** , puis sélectionnez un nouveau **nom d’utilisateur** et **mot de passe**. Cliquez sur le bouton de **mise à jour** pour conserver.


<!-- links -->

[availability-set]: ../virtual-machines/virtual-machines-windows-manage-availability.md
[guidance-vpn-gateway]: ./guidance-hybrid-network-vpn.md
[implementing-a-multi-tier-architecture-on-Azure]: ./guidance-compute-3-tier-vm.md
[implementing-a-secure-hybrid-network-architecture]: ./guidance-iaas-ra-secure-vnet-hybrid.md
[iptables]: https://help.ubuntu.com/community/IptablesHowTo
[lb-probe]: ../load-balancer/load-balancer-custom-probe-overview.md
[load-balancer]: ../load-balancer/load-balancer-internet-overview.md
[network-security-group]: ../virtual-network/virtual-networks-nsg.md
[ra-vpn]: ./guidance-hybrid-network-vpn.md
[ra-expressroute]: ./guidance-hybrid-network-expressroute.md
[resource-manager-overview]: ../azure-resource-manager/resource-group-overview.md
[visio-download]: http://download.microsoft.com/download/1/5/6/1569703C-0A82-4A9C-8334-F13D0DF2F472/RAs.vsdx
[vpn-failover]: ./guidance-hybrid-network-expressroute-vpn-failover.md
[0]: ./media/blueprints/hybrid-network-secure-vnet-dmz.png "Architecture de réseau hybride sécurisée"

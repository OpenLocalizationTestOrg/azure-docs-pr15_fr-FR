<properties
   pageTitle="Azure prise en charge du Gestionnaire de ressources pour équilibrage de charge | Microsoft Azure "
   description="À l’aide de powershell pour équilibrage de charge avec le Gestionnaire de ressources Azure. Utilisation de modèles pour équilibrage de charge"
   services="load-balancer"
   documentationCenter="na"
   authors="sdwheeler"
   manager="carmonm"
   editor="tysonn" />
<tags
   ms.service="load-balancer"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/24/2016"
   ms.author="sewhee" />


# <a name="using-azure-resource-manager-support-with-azure-load-balancer"></a>À l’aide de la prise en charge le Gestionnaire de ressources Azure avec équilibrage de charge Azure

Le Gestionnaire de ressources Azure est la structure de gestion par défaut pour les services dans Azure. Équilibrage de charge Azure peuvent être géré à l’aide des outils et des API basées sur le Gestionnaire de ressources Azure.

## <a name="concepts"></a>Concepts

Avec le Gestionnaire de ressources, équilibrage de charge Azure contient les ressources enfants suivantes :

- Frontale IP configuration – un équilibrage de charge peut inclure un ou plusieurs frontal adresses IP, connus sinon un IP virtuelles (VIP). Ces adresses IP servent de pénétration pour le trafic.

- Pool d’adresse principale – il s’agit des adresses IP associés avec la machine virtuelle réseau Interface carte (carte réseau) à laquelle charger sera distribué.

- Charger les règles d’équilibrage de charge – une propriété règle mappe une IP frontal donnée et la combinaison de port à un ensemble d’adresses IP serveur principal et la combinaison de port. Un programme d’équilibrage de charge unique peut contenir des règles d’équilibrage de charge plusieurs. Chaque règle est une combinaison d’un frontale IP, port et principale IP et port associé aux machines virtuelles.

- Sondes – sondes permettent d’effectuer le suivi de l’état d’instances de machine virtuelle. Si une sonde santé échoue, l’instance machine virtuelle est automatiquement prise se déconnecter de rotation.

- Règles de trafic entrant NAT – règles NAT définissant le trafic entrant s’étalant via l’avant terminer IP et distribué à l’adresse IP serveur principal.

![](./media/load-balancer-arm/load-balancer-arm.png)

## <a name="quickstart-templates"></a>Modèles de démarrage rapide

Le Gestionnaire de ressources Azure vous permet de mise en service de vos applications en utilisant un modèle déclaratif. Dans un seul modèle, vous pouvez déployer plusieurs services ainsi que leurs dépendances. Vous utilisez le même modèle pour déployer à plusieurs reprises votre application lors de chaque étape du cycle de vie application.

Modèles peuvent inclure des définitions des Machines virtuelles, réseaux virtuels, ensembles de disponibilité, Interfaces réseau (cartes réseau), comptes de stockage, programmes d’équilibrage de charge, les groupes de sécurité réseau et adresses IP Public. Modèles vous pouvez de créer toutes les informations que nécessaires pour une application complexe. Le fichier de modèle peut être archivé dans le système de gestion de contenu pour la collaboration et de contrôle de version.

[Pour plus d’informations sur les modèles](http://go.microsoft.com/fwlink/?LinkId=544798)

[Pour plus d’informations sur les ressources réseau](../virtual-network/resource-groups-networking.md)

Modèles de démarrage rapide à l’aide d’équilibrage de charge Azure sont accessibles dans un [référentiel GitHub](https://github.com/Azure/azure-quickstart-templates) qui héberge un ensemble de modèles généré par la Communauté.

Exemples de modèles :

- [2 machines virtuelles dans un équilibrage de charge et les règles d’équilibrage de charge](http://go.microsoft.com/fwlink/?LinkId=544799)
- [2 machines virtuelles dans un VNET avec un équilibrage de charge interne et équilibrage de charge des règles](http://go.microsoft.com/fwlink/?LinkId=544800)
- [2 machines virtuelles dans un programme d’équilibrage de charge et configurer des règles NAT sur les livres](http://go.microsoft.com/fwlink/?LinkId=544801)


## <a name="setting-up-azure-load-balancer-with-a-powershell-or-cli"></a>La configuration d’équilibrage de charge Azure PowerShell ou de l’infrastructure du langage commun

Commencer à utiliser les applets de commande Gestionnaire de ressources Azure, les outils de ligne de commande et API REST

- [Applets de commande de mise en réseau Azure](https://msdn.microsoft.com/library/azure/mt163510.aspx) pouvant être utilisées pour créer un programme d’équilibrage de charge.
- [Comment créer un équilibrage de charge à l’aide du Gestionnaire de ressources Azure](load-balancer-get-started-ilb-arm-ps.md)
- [À l’aide de l’infrastructure du langage commun Azure avec la gestion des ressources Azure](../xplat-cli-azure-resource-manager.md)
- [API REST l’équilibrage de charge](https://msdn.microsoft.com/library/azure/mt163651.aspx)


## <a name="next-steps"></a>Étapes suivantes

Vous pouvez également [commencer à créer un Internet facing équilibrage de charge](load-balancer-get-started-internet-arm-ps.md) et configurer le type de [mode de distribution](load-balancer-distribution-mode.md) d’un comportement de trafic réseau charge spécifique équilibrage.

Découvrez comment gérer les [paramètres de délai d’expiration TCP inactives pour un équilibrage de charge](load-balancer-tcp-idle-timeout.md). Ceci est important lorsque votre application doit afin de maintenir des connexions pour les serveurs derrière un programme d’équilibrage de charge.

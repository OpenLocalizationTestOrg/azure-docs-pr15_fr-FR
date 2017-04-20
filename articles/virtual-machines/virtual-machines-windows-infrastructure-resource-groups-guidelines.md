<properties
    pageTitle="Instructions de groupes de ressources | Microsoft Azure"
    description="En savoir plus sur les instructions de conception et implémentation clées pour le déploiement des groupes de ressources dans les services d’infrastructure Azure."
    documentationCenter=""
    services="virtual-machines-windows"
    authors="iainfoulds"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/08/2016"
    ms.author="iainfou"/>

# <a name="azure-resource-group-guidelines"></a>Instructions de groupe de ressources Azure

[AZURE.INCLUDE [virtual-machines-windows-infrastructure-guidelines-intro](../../includes/virtual-machines-windows-infrastructure-guidelines-intro.md)] 

Cet article se concentre sur comprendre comment logiquement générer votre environnement et regrouper tous les composants de groupes de ressources.


## <a name="implementation-guidelines-for-resource-groups"></a>Instructions d’implémentation pour les groupes de ressources

Décisions :

- Souhaitez-vous créer des groupes de ressources en les composants d’infrastructure de base, ou par le déploiement de l’application complète ?
- Vous voulez limiter l’accès aux groupes de ressources à l’aide de contrôles d’accès basé sur un rôle

Tâches :

- Définir ce qu’infrastructure composants principaux et dédié des groupes de ressources que vous avez besoin.
- Passez en revue la mise en œuvre des modèles de gestionnaire de ressources pour les déploiements cohérentes et reproduire.
- Définir les rôles d’accès utilisateur que vous avez besoin pour contrôler l’accès aux groupes de ressources.
- Créer l’ensemble de groupes de ressources à l’aide de votre convention d’appellation. Vous pouvez utiliser PowerShell Azure ou le portail.


## <a name="resource-groups"></a>Groupes de ressources

Dans Azure, vous regroupez les ressources connexes tels que les comptes de stockage et réseaux virtuels machines virtuelles (machines virtuelles) à déployer et gérer les gérer comme une seule entité. Cette approche simplifie pour déployer des applications tout en conservant toutes les ressources connexes ensemble en matière de gestion, ou pour accorder l’accès à ce groupe de ressources. Pour une présentation plus générale des groupes de ressources, consultez la [vue d’ensemble du Gestionnaire de ressources Azure](../azure-resource-manager/resource-group-overview.md).

Possibilité pour générer votre environnement à l’aide de modèles est une fonctionnalité clé aux groupes de ressources. Un modèle est qu’un fichier JSON que déclare le stockage, mise en réseau et les ressources de calcul. Vous pouvez également définir des scripts personnalisés associés ou des configurations à appliquer. À l’aide de ces modèles, vous créez des déploiements cohérentes et reproduire pour vos applications. Cette approche facilite la création d’un environnement de développement et ensuite utiliser ce même modèle pour créer un déploiement de production, ou vice versa. Pour mieux comprendre l’utilisation des modèles, lisez [la procédure pas à pas de modèle](../resource-manager-template-walkthrough.md) qui vous guide tout au long de chaque étape de la création de modèle.

Il existe deux différentes approches lors de la conception de votre environnement avec les groupes de ressources :

- Groupes de ressources pour chaque déploiement d’application qui combine les comptes de stockage, les réseaux virtuels et les sous-réseaux, machines virtuelles, chargement les programmes d’équilibrage, etc..
- Groupes de ressources centralisée qui contiennent vos comptes mise en réseau et sous-réseaux ou stockage virtuels core. Vos applications sont ensuite dans leurs propres groupes de ressources qui contiennent uniquement des machines virtuelles, les programmes d’équilibrage de charge, interfaces réseau, etc..

Comme vous mise à l’échelle, création de groupes de ressources centralisé pour votre réseau et sous-réseaux virtuel facilite la générer croisée locaux connexion réseau pour des options de connectivité hybride. L’autre approche est pour chaque application pour que leur propre réseau virtuel qui requiert la configuration et la maintenance.  [Contrôles d’accès basé sur un rôle](../active-directory/role-based-access-control-what-is.md) fournissent une manière précise pour contrôler l’accès aux groupes de ressources. Pour les applications de production, vous pouvez contrôler les utilisateurs qui peuvent accéder à ces ressources, ou pour les ressources d’infrastructure de base, vous pouvez limiter uniquement ingénieurs de l’infrastructure pour travailler avec eux. Les propriétaires des applications n’ont accès pour les composants d’application au sein de leur groupe de ressources et pas cœur Azure infrastructure de votre environnement. Lorsque vous créez votre environnement, envisagez les utilisateurs qui nécessitent un accès aux ressources et concevoir vos groupes de ressources en conséquence. 


## <a name="next-steps"></a>Étapes suivantes

[AZURE.INCLUDE [virtual-machines-windows-infrastructure-guidelines-next-steps](../../includes/virtual-machines-windows-infrastructure-guidelines-next-steps.md)] 
<properties
    pageTitle="Que sont les échelle machine virtuelle définit ? | Microsoft Azure"
    description="Obtenir des informations sur les ensembles d’échelle machine virtuelle."
    keywords="machine virtuelle Linux, échelle machine virtuelle définit" 
    services="virtual-machines-linux"
    documentationCenter=""
    authors="gatneil"
    manager="madhana"
    editor="tysonn"
    tags="azure-resource-manager" />

<tags
    ms.service="virtual-machine-linux"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-linux"
    ms.devlang="na"
    ms.topic="article"
    ms.date="03/24/2016"
    ms.author="gatneil"/>

# <a name="what-are-virtual-machine-scale-sets"></a>Que sont les machine virtuelle échelle définit ?

Machine virtuelle échelle ensembles permettent de gérer plusieurs ordinateurs virtuels comme un ensemble. À un niveau élevé, des jeux d’échelle sont les avantages et inconvénients suivants :

Professionnels de le :

1. Disponibilité. Chaque jeu échelle place ses machines virtuelles dans un jeu de disponibilité avec 5 défaillance domaines (FDs) et 5 mise à jour (UDs) pour assurer la disponibilité (pour plus d’informations sur FDs et UDs, voir [disponibilité machine virtuelle](./virtual-machines-linux-manage-availability.md)). 
2. Intégration simple avec équilibrage de charge Azure et application passerelle.
3. Intégration simple avec échelle Azure.
4. Simplifié déploiement, gestion et nettoyage des machines virtuelles.
5. Prend en charge les versions Windows et Linux courantes, ainsi que des images personnalisées.

Inconvénients :

1. Impossible d’attacher des disques de données sur des instances de machine virtuelle dans un jeu d’échelle. En revanche, si vous devez utiliser stockage Blob, fichiers Azure, Tables Azure ou autre solution de stockage.

## <a name="quick-create-using-azure-cli"></a>Création rapide à l’aide d’infrastructure du langage commun Azure

[AZURE.INCLUDE [cli-vmss-quick-create](../../includes/virtual-machines-linux-cli-vmss-quick-create-include.md)]

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations, consultez la [page d’accueil pour les jeux d’échelle](https://azure.microsoft.com/services/virtual-machine-scale-sets/).

Pour plus d’informations, consultez la [page de la documentation principale pour échelle définit](../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md).

Par exemple modèles Gestionnaire de ressources à l’aide de jeux d’échelle, recherchez « vmss » dans [mis en pension github de modèles de démarrage rapide d’Azure](https://github.com/Azure/azure-quickstart-templates).


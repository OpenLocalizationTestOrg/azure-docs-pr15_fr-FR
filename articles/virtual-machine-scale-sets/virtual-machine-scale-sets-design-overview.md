<properties
    pageTitle="Conception d’échelle Machine virtuelle définit pour échelle | Microsoft Azure"
    description="Découvrez comment concevoir vos ensembles d’échelle Machine virtuelle pour échelle"
    keywords="machine virtuelle Linux, échelle machine virtuelle définit" 
    services="virtual-machine-scale-sets"
    documentationCenter=""
    authors="gatneil"
    manager="madhana"
    editor="tysonn"
    tags="azure-resource-manager" />

<tags
    ms.service="virtual-machine-scale-sets"
    ms.workload="na"
    ms.tgt_pltfrm="vm-linux"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/28/2016"
    ms.author="gatneil"/>

# <a name="designing-vm-scale-sets-for-scale"></a>Conception virtuelle échelle définit pour échelle

Cette rubrique décrit les éléments de conception pour les jeux d’échelle Machine virtuelle. Pour plus d’informations sur la définition des jeux d’échelle Machine virtuelle, voir [Présentation de jeux d’échelle Machine virtuelle](virtual-machine-scale-sets-overview.md).


## <a name="storage"></a>Espace de stockage

Un ensemble d’échelle utilise des comptes de stockage pour stocker les disques du système d’exploitation des ordinateurs virtuels dans l’ensemble. Nous vous recommandons d’un rapport de 20 machines virtuelles par compte de stockage inférieur ou égal à. Nous vous recommandons également que vous réparti sur toute l’alphabet les premiers caractères du stockage des noms des comptes. Cela vous aide à répartir la charge sur différents systèmes internes. Par exemple, dans le modèle suivant, nous permet de la fonction de modèle du Gestionnaire de ressources uniqueString pour générer hachage préfixe qui est ajoutés aux noms de compte de stockage : [https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-linux-nat](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-linux-nat).


## <a name="overprovisioning"></a>Overprovisioning

En commençant par la version de l’API « 2016-03-30 », jeux d’échelle machine virtuelle par défaut est « overprovisioning » machines virtuelles. Overprovisioning activé, l’échelle définie réellement vrille plusieurs ordinateurs virtuels que vous l’avez demandé, puis supprime les ordinateurs virtuels supplémentaires qui lancé dernier. Overprovisioning améliore les taux de réussite de mise en service. Vous n’êtes pas facturé pour ces machines virtuelles supplémentaires, et elles ne sont pas pris les limites des quotas.

Tandis qu’overprovisioning améliore les taux de réussite de mise à disponibilité, il peut provoquer un comportement confusion pour une application qui n’est pas conçue pour gérer des machines virtuelles disparition sans préavis. Pour activer overprovisioning désactiver, assurez-vous d’avoir la chaîne suivante dans votre modèle : « overprovision » : « false ». Vous trouverez plus d’informations dans la [documentation de l’API REST de définir échelle machine virtuelle](https://msdn.microsoft.com/library/azure/mt589035.aspx).

Si vous désactivez overprovisioning, vous pouvez vous contenter d’un plus grand ratio de machines virtuelles par compte de stockage, mais nous ne recommandons pas accéder au-dessus de 40.


## <a name="limits"></a>Limites
Un ensemble d’échelle basé sur une image personnalisée (un créés par vous) devez créer tous les disques virtuels de système d’exploitation au sein d’un compte de stockage. Par conséquent, le nombre maximal recommandé de machines virtuelles dans un jeu d’échelle basé sur une image personnalisée est 20. Si vous désactivez overprovisioning, vous pouvez accéder jusqu'à 40.

Un ensemble d’échelle basé sur une image de la plateforme est actuellement limité à 100 machines (nous vous recommandons de 5 comptes de stockage pour cette échelle).

Pour les autres machines virtuelles que ces limites autoriser, vous devez déployer plusieurs jeux d’échelle comme indiqué dans [ce modèle](https://github.com/Azure/azure-quickstart-templates/tree/master/301-custom-images-at-scale).
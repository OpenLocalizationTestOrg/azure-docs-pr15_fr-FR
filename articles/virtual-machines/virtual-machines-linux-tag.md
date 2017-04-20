<properties
   pageTitle="Comment ajouter des balises à une machine virtuelle Linux | Microsoft Azure"
   description="Obtenir des informations sur le balisage d’une machine virtuelle Linux créée dans Azure à l’aide du modèle de déploiement Gestionnaire de ressources."
   services="virtual-machines-linux"
   documentationCenter=""
   authors="mmccrory"
   manager="timlt"
   editor="tysonn"
   tags="azure-resource-manager"/>

<tags
   ms.service="virtual-machines-linux"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure-services"
   ms.date="07/05/2016"
   ms.author="memccror"/>

# <a name="how-to-tag-a-linux-virtual-machine-in-azure"></a>Comment ajouter des balises à une machine virtuelle Linux dans Azure

Cet article décrit les différentes façons d’ajouter des balises à une machine virtuelle Linux dans Azure via le modèle de déploiement du Gestionnaire de ressources. Les balises sont paires clé/valeur définies par l’utilisateur qui peuvent être placés directement sur une ressource ou un groupe de ressources. Azure prend actuellement en charge jusqu'à 15 mots-clés par ressource et de groupe de ressources. Balises peuvent être placés sur une ressource au moment de la création d’ou ajoutés à une ressource existante. Veuillez noter, balises sont prises en charge pour les ressources créés via le modèle de déploiement de gestionnaire de ressources uniquement.

[AZURE.INCLUDE [virtual-machines-common-tag](../../includes/virtual-machines-common-tag.md)]

## <a name="tagging-with-azure-cli"></a>Marquage avec Azure infrastructure du langage commun

Pour commencer, [installer et configurer l’infrastructure du langage commun Azure](../xplat-cli-azure-resource-manager.md) et vérifiez que vous êtes en mode Gestionnaire de ressources (`azure config mode arm`).

Vous pouvez afficher toutes les propriétés pour une donnée Machine virtuelle, y compris les balises, à l’aide de cette commande :

        azure vm show -g MyResourceGroup -n MyTestVM

Pour ajouter une nouvelle balise machine virtuelle via l’infrastructure du langage commun Azure, vous pouvez utiliser la `azure vm set` commande ainsi que le paramètre de balise **-t**:

        azure vm set -g MyResourceGroup -n MyTestVM –t myNewTagName1=myNewTagValue1;myNewTagName2=myNewTagValue2

Pour supprimer toutes les balises, vous pouvez utiliser le paramètre **– T** dans la `azure vm set` commande.

        azure vm set – g MyResourceGroup –n MyTestVM -T


À présent que nous avons appliqué balises à nos ressources Azure infrastructure du langage commun et le portail, examinons les détails de l’utilisation de consulter les balises dans le portail de facturation.

[AZURE.INCLUDE [virtual-machines-common-tag-usage](../../includes/virtual-machines-common-tag-usage.md)]

## <a name="next-steps"></a>Étapes suivantes

* Pour plus d’informations sur le balisage de vos ressources Azure, voir [Vue d’ensemble du Gestionnaire de ressources Azure][] et [à l’aide de balises pour organiser vos ressources Azure][].
* Pour voir comment les balises peuvent vous aider à gérer votre utilisation des ressources Azure, voir [comprendre votre facture Azure][] et [Familiarisez-vous dans votre consommation de ressources de Microsoft Azure][].





[Azure CLI environment]: ./xplat-cli-azure-resource-manager.md
[Vue d’ensemble du Gestionnaire de ressources Azure]: ../azure-resource-manager/resource-group-overview.md
[Utilisation de balises pour organiser vos ressources Azure]: ../resource-group-using-tags.md
[Comprendre votre facture Azure]: ../billing/billing-understand-your-bill.md
[Familiarisez-vous dans votre consommation de ressources de Microsoft Azure]: ../billing-usage-rate-card-overview.md

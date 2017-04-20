<properties
   pageTitle="Comment ajouter des balises à une machine virtuelle | Microsoft Azure"
   description="En savoir plus sur une machine virtuelle Windows créée dans Azure à l’aide du modèle de déploiement Gestionnaire de ressources de marquage"
   services="virtual-machines-windows"
   documentationCenter=""
   authors="mmccrory"
   manager="timlt"
   editor="tysonn"
   tags="azure-resource-manager"/>

<tags
   ms.service="virtual-machines-windows"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-windows"
   ms.workload="infrastructure-services"
   ms.date="07/05/2016"
   ms.author="memccror"/>

# <a name="how-to-tag-a-windows-virtual-machine-in-azure"></a>Comment ajouter des balises à une machine virtuelle Windows dans Azure


Cet article décrit les différentes façons d’ajouter des balises à une machine virtuelle Windows dans Azure via le modèle de déploiement du Gestionnaire de ressources. Les balises sont paires clé/valeur définies par l’utilisateur qui peuvent être placés directement sur une ressource ou un groupe de ressources. Azure prend actuellement en charge jusqu'à 15 mots-clés par ressource et de groupe de ressources. Balises peuvent être placés sur une ressource au moment de la création d’ou ajoutés à une ressource existante. Notez que les balises sont prises en charge pour les ressources créés via le modèle de déploiement de gestionnaire de ressources uniquement. Si vous voulez ajouter des balises à une machine virtuelle Linux, Découvrez [comment ajouter des balises à une machine virtuelle Linux dans Azure](virtual-machines-linux-tag.md).

[AZURE.INCLUDE [virtual-machines-common-tag](../../includes/virtual-machines-common-tag.md)]

## <a name="tagging-with-powershell"></a>Marquage avec PowerShell

Pour créer, ajouter et supprimer des balises via PowerShell, vous devez tout d’abord configurer votre [environnement PowerShell avec le Gestionnaire de ressources Azure][]. Une fois que vous avez terminé la configuration, vous pouvez placer des indicateurs sur les ressources informatiques, de réseau et de stockage lors de la création ou après la création de la ressource via PowerShell. Cet article se concentre sur Afficher/modifier les balises placés sur des Machines virtuelles.

Tout d’abord, accédez à une Machine virtuelle via le `Get-AzureRmVM` applet de commande.

        PS C:\> Get-AzureRmVM -ResourceGroupName "MyResourceGroup" -Name "MyTestVM"

Si votre Machine virtuelle contient déjà des balises, vous verrez ensuite toutes les balises sur la ressource :

        Tags : {
                "Application": "MyApp1",
                "Created By": "MyName",
                "Department": "MyDepartment",
                "Environment": "Production"
               }

Si vous voulez ajouter des balises via PowerShell, vous pouvez utiliser la `Set-AzureRmResource` commande. Remarque lors de la mise à jour des balises via PowerShell, balises sont mis à jour dans son ensemble. Donc si vous ajoutez une balise à une ressource qui a déjà balises, vous devez inclure tous les marqueurs que vous souhaitez être placés sur la ressource. Voici un exemple montrant comment ajouter des balises supplémentaires à une ressource par le biais PowerShell Cmdlets.

Cette applet de commande première affecte toutes les balises placés sur *MyTestVM* à la variable *$tags* , à l’aide de la `Get-AzureRmResource` et `Tags` propriété.

        PS C:\> $tags = (Get-AzureRmResource -ResourceGroupName MyResourceGroup -Name MyTestVM).Tags

La deuxième commande affiche les étiquettes pour la variable donnée.

        PS C:\> $tags

        Name        Value
        ----                           -----
        Value       MyDepartment
        Name        Department
        Value       MyApp1
        Name        Application
        Value       MyName
        Name        Created By
        Value       Production
        Name        Environment

La troisième commande ajoute une balise supplémentaire à la variable *$tags* . Notez l’utilisation de la **+=** pour ajouter la nouvelle paire clé/valeur à la liste *$tags* .

        PS C:\> $tags += @{Name="Location";Value="MyLocation"}

La commande quatrième affecte tous les indicateurs définis dans la variable *$tags* à la ressource donnée. Dans ce cas, il est MyTestVM.

        PS C:\> Set-AzureRmResource -ResourceGroupName MyResourceGroup -Name MyTestVM -ResourceType "Microsoft.Compute/VirtualMachines" -Tag $tags

La commande cinquième affiche toutes les balises de la ressource. Comme vous pouvez le voir, *emplacement* est maintenant défini comme étant une balise avec *MyLocation* comme la valeur.

        PS C:\> (Get-AzureRmResource -ResourceGroupName MyResourceGroup -Name MyTestVM).Tags

        Name        Value
        ----                           -----
        Value       MyDepartment
        Name        Department
        Value       MyApp1
        Name        Application
        Value       MyName
        Name        Created By
        Value       Production
        Name        Environment
        Value       MyLocation
        Name        Location

Pour plus d’informations sur le balisage via PowerShell, consultez les [Applets de commande Azure ressource][].

[AZURE.INCLUDE [virtual-machines-common-tag-usage](../../includes/virtual-machines-common-tag-usage.md)]

## <a name="next-steps"></a>Étapes suivantes

* Pour plus d’informations sur le balisage de vos ressources Azure, voir [Vue d’ensemble du Gestionnaire de ressources Azure][] et [à l’aide de balises pour organiser vos ressources Azure][].
* Pour voir comment les balises peuvent vous aider à gérer votre utilisation des ressources Azure, voir [comprendre votre facture Azure][] et [Familiarisez-vous dans votre consommation de ressources de Microsoft Azure][].

[Environnement de PowerShell avec le Gestionnaire de ressources Azure]: ../powershell-azure-resource-manager.md
[Applets de commande ressource Azure]: https://msdn.microsoft.com/library/azure/dn757692.aspx
[Vue d’ensemble du Gestionnaire de ressources Azure]: ../azure-resource-manager/resource-group-overview.md
[Utilisation de balises pour organiser vos ressources Azure]: ../resource-group-using-tags.md
[Comprendre votre facture Azure]: ../billing/billing-understand-your-bill.md
[Familiarisez-vous dans votre consommation de ressources de Microsoft Azure]: ../billing-usage-rate-card-overview.md

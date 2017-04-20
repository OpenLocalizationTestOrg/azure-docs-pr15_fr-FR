<properties
    pageTitle="Créer un jeu de disponibilité machine virtuelle | Microsoft Azure"
    description="Apprenez à créer une disponibilité définie pour vos machines virtuelles à l’aide du portail Azure ou PowerShell à l’aide du modèle de déploiement Gestionnaire de ressources."
    keywords="jeu de disponibilité"
    services="virtual-machines-windows"
    documentationCenter=""
    authors="cynthn"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>
<tags
    ms.service="virtual-machines-windows"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/27/2016"
    ms.author="cynthn"/>


# <a name="create-an-availability-set"></a>Créer un jeu de disponibilité 

Lorsque la valeur à l’aide du portail, si vous souhaitez que votre ordinateur virtuel à faire partie d’une disponibilité, vous devez créer la disponibilité définie au préalable.

Pour plus d’informations sur la création et l’utilisation d’ensembles de disponibilité, voir [Gérer la disponibilité des machines virtuelles](virtual-machines-windows-manage-availability.md).


## <a name="use-the-portal-to-create-an-availability-set-before-creating-your-vms"></a>Utiliser le portail pour créer une disponibilité définir avant de créer vos ordinateurs virtuels

1. Dans le menu concentrateur, cliquez sur **Parcourir** , puis sélectionnez **jeux de disponibilité**.

2. Sur la **disponibilité les ensembles de carte**, cliquez sur **Ajouter**.

    ![Capture d’écran montrant le bouton Ajouter pour la création de disponibilité d’une nouvelle valeur.](./media/virtual-machines-windows-create-availability-set/add-availability-set.png)

3. Sur la carte **créer disponibilité définie** , complétez les informations pour votre jeu.

    ![Capture d’écran montrant les informations que vous devez entrer pour créer le jeu de disponibilité.](./media/virtual-machines-windows-create-availability-set/create-availability-set.png)

    - **Nom** : le nom doit être 1-80 caractères composés de nombres, lettres, périodes, des traits de soulignement et tirets. Le premier caractère doit être une lettre ou un chiffre. Le dernier caractère doit être une lettre, un nombre ou un trait de soulignement.
    - **Domaines d’erreur** - domaines d’erreur définissent le groupe de machines virtuelles qui partagent un commutateur de source et de réseau power courantes. Par défaut, les ordinateurs virtuels sont séparées sur jusqu'à trois domaines d’erreur et peuvent être modifiés en compris entre 1 et 3.
    - **Mettre à jour les domaines** - mise à jour cinq domaines affectés par défaut et il peut être défini compris entre 1 et 20. Mettre à jour domaines indiquent les groupes de machines virtuelles et le matériel physique sous-jacent qui peut être redémarré en même temps. Par exemple, si nous spécifiez cinq domaines, mettre à jour lorsque plus de cinq machines virtuelles sont configurés dans un seul ensemble de disponibilité, la sixième machine virtuelle sera placé dans le même domaine de mise à jour que la première machine virtuelle, la septième dans la même UD que la deuxième machine virtuelle et ainsi de suite. L’ordre du redémarrage ne peut ne pas être séquentielle, mais mettre à jour qu’un seul domaine sera redémarré à la fois.
    - **Abonnement** , sélectionnez l’abonnement à utiliser si vous avez plusieurs.
    - **Groupe de ressources** : sélectionnez un groupe de ressources existant en cliquant sur la flèche et en sélectionnant un groupe de ressources dans la liste déroulante vers le bas. Vous pouvez également créer un nouveau groupe de ressources en tapant un nom. Le nom peut contenir aucun des caractères suivants : lettres, nombres, périodes, tirets, des traits de soulignement et parenthèse ou parenthèse fermante. Le nom ne peut pas se terminer par un point. Tous les ordinateurs virtuels dans le groupe de disponibilité doivent être créés dans le même groupe de ressources que le jeu de disponibilité.
    - **Emplacement** : sélectionnez un emplacement dans la liste déroulante.

4. Lorsque vous avez terminé entrer les informations, cliquez sur **créer**. Une fois que le groupe de disponibilité a été créé, vous pouvez le voir dans la liste lorsque vous actualisez le portail.

## <a name="use-the-portal-to-create-a-virtual-machine-and-an-availability-set-at-the-same-time"></a>Utiliser le portail pour créer une machine virtuelle et disponibilité définir en même temps

Si vous créez une nouvelle machine virtuelle à l’aide du portail, vous pouvez également créer une nouvelle définie pour la machine virtuelle pendant que vous créez la première machine virtuelle dans l’ensemble de la disponibilité.

![Capture d’écran montrant le processus de création de disponibilité d’une nouvelle valeur pendant que vous créez la machine virtuelle.](./media/virtual-machines-windows-create-availability-set/new-vm-avail-set.png)


## <a name="add-a-new-vm-to-an-existing-availability-set"></a>Ajouter un nouvel ordinateur virtuel à un ensemble de disponibilité existant

Pour chaque supplémentaire machine virtuelle que vous créez auquel doit appartenir dans le jeu, assurez-vous que vous créez dans le même **groupe de ressources** et puis sélectionnez la disponibilité existante définie à l’étape 3. 

![Capture d’écran montrant comment sélectionner une disponibilité existante permet à utiliser pour votre ordinateur virtuel.](./media/virtual-machines-windows-create-availability-set/add-vm-to-set.png)



## <a name="use-powershell-to-create-an-availability-set"></a>Utiliser PowerShell pour créer un jeu de disponibilité

Cet exemple crée une disponibilité définie dans le groupe de ressources **RMResGroup** à l’emplacement **Américain ouest** . Il doit être terminé avant de créer la première machine virtuelle qui sera dans l’ensemble.

    New-AzureRmAvailabilitySet -ResourceGroupName "RMResGroup" -Name "AvailabilitySet03" -Location "West US"
    
Pour plus d’informations, voir [AzureRmAvailabilitySet de nouveau](https://msdn.microsoft.com/library/mt619453.aspx).


## <a name="troubleshooting"></a>Résolution des problèmes

- Lorsque vous créez une machine virtuelle, si le jeu de disponibilité souhaité ne figure pas dans la liste déroulante dans le portail peut avoir créé dans un groupe de ressources différent. Si vous ne connaissez pas le groupe de ressources pour votre disponibilité définit, accédez au menu concentrateur et cliquez sur Parcourir > disponibilité définit pour afficher la liste de vos ensembles de disponibilité et dont elles appartiennent à des groupes de ressources.


## <a name="next-steps"></a>Étapes suivantes

Ajouter un stockage supplémentaire à votre ordinateur virtuel en ajoutant un [disque de données](virtual-machines-windows-attach-disk-portal.md)supplémentaires.

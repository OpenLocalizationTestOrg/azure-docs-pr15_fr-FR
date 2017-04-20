<properties
    pageTitle="Différentes façons de créer une machine virtuelle Windows | Microsoft Azure"
    description="Répertorie les différentes façons pour créer une machine virtuelle Windows avec le Gestionnaire de ressources."
    services="virtual-machines-windows"
    documentationCenter=""
    authors="cynthn"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machines-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="vm-windows"
    ms.workload="infrastructure-services"
    ms.date="09/27/2016"
    ms.author="cynthn"/>

# <a name="different-ways-to-create-a-windows-virtual-machine-with-resource-manager"></a>Différentes façons de créer une machine virtuelle Windows avec le Gestionnaire de ressources

Azure propose différentes façons de créer une machine virtuelle, car machines virtuelles sont adaptées à des fins et différents utilisateurs. Cela signifie que vous devez effectuer certains choix sur la machine virtuelle et comment créer votre document. Cet article vous donne un résumé de ces options et des liens vers des instructions.

## <a name="azure-portal"></a>Portail Azure

À l’aide du portail Azure est une méthode simple pour essayer une machine virtuelle, notamment si vous débutez avec Azure. 

[Créer une machine virtuelle exécutant Windows à l’aide du portail](virtual-machines-windows-hero-tutorial.md)

## <a name="template"></a>Modèle

Machines virtuelles nécessitent une combinaison de ressources (par exemple une disponibilité des jeux et des comptes de stockage). Plutôt que de déploiement et la gestion chaque ressource séparément, vous pouvez créer un modèle de gestionnaire de ressources Azure qui est déployée et les dispositions toutes les ressources dans une seule opération coordonnée.

- [Créer une machine virtuelle Windows avec un modèle de gestionnaire de ressources](virtual-machines-windows-ps-template.md)


## <a name="azure-powershell"></a>PowerShell Azure

Si vous préférez utiliser une invite de commandes, vous pouvez utiliser PowerShell Azure.

- [Créer une machine virtuelle Windows à l’aide de PowerShell](virtual-machines-windows-ps-create.md)


## <a name="visual-studio"></a>Visual Studio

Utilisez Visual Studio pour créer, gérer et déployer des machines virtuelles avec les outils Azure pour Visual Studio et le Kit de développement Azure.

[Azure Tools pour Visual Studio](https://www.visualstudio.com/features/azure-tools-vs)


<properties
   pageTitle="Naviguez et sélectionnez les images de machine virtuelle Windows | Microsoft Azure"
   description="Découvrez comment faire pour déterminer l’éditeur, offre et référence (SKU) pour les images lorsque vous créez une machine virtuelle Windows avec le modèle de déploiement du Gestionnaire de ressources."
   services="virtual-machines-windows"
   documentationCenter=""
   authors="squillace"
   manager="timlt"
   editor=""
   tags="azure-resource-manager"
   />

<tags
   ms.service="virtual-machines-windows"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-windows"
   ms.workload="infrastructure"
   ms.date="08/23/2016"
   ms.author="rasquill"/>

# <a name="navigate-and-select-windows-virtual-machine-images-in-azure-with-powershell-or-the-cli"></a>Naviguez et sélectionnez les images de machine virtuelle Windows dans Azure avec PowerShell ou de l’infrastructure du langage commun

Cette rubrique décrit comment trouver des éditeurs d’image de la machine virtuelle, offres, références SKU et versions pour chaque emplacement dans lequel vous pouvez déployer. Pour donner un exemple, des images de machine virtuelle Windows couramment utilisés sont :

## <a name="table-of-commonly-used-windows-images"></a>Table des images Windows fréquemment utilisés


| Éditeur                        | Offre                                 | Référence (SKU)                         |
|:---------------------------------|:-------------------------------------------|:---------------------------------|:--------------------|
| MicrosoftDynamicsNAV             | DynamicsNAV                                | 2015                             |
| MicrosoftSharePoint              | MicrosoftSharePointServer                  | 2013                             |
| MicrosoftSQLServer               | SQL2014 WS2012R2                           | Entreprise-optimisé-de-DW      |
| MicrosoftSQLServer               | SQL2014 WS2012R2                           | Entreprise-optimisé-de-OLTP    |
| MicrosoftWindowsServer           | Windows Server                              | 2012 R2-centre de données                  |
| MicrosoftWindowsServer           | Windows Server                              | Centre de données 2012               |
| MicrosoftWindowsServer           | Windows Server                              | 2008-R2-SP1 |
| MicrosoftWindowsServer           | Windows Server                              | Serveur Windows-Aperçu technique |
| MicrosoftWindowsServerEssentials | WindowsServerEssentials                    | WindowsServerEssentials          |
| MicrosoftWindowsServerHPCPack    | WindowsServerHPCPack                       | 2012R2                           |


[AZURE.INCLUDE [virtual-machines-common-cli-ps-findimage](../../includes/virtual-machines-common-cli-ps-findimage.md)]

<properties 
    pageTitle="Configurer un réseau virtuel à l’aide d’un fichier de configuration du réseau" 
    description="Instructions pour exporter et importer un fichier de configuration réseau dans le portail de gestion Azure afin de créer ou modifier des réseaux virtuels. " 
    services="virtual-network" 
    documentationCenter="" 
    authors="jimdial" 
    manager="carmonm" 
    editor="tysonn"/>

<tags
    ms.service="virtual-network"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="infrastructure-services" 
    ms.date="03/15/2016"
    ms.author="jdial"/>

# <a name="configure-a-virtual-network-using-a-network-configuration-file"></a>Configurer un réseau virtuel à l’aide d’un fichier de configuration du réseau

Vous pouvez configurer un réseau virtuel (VNet) à l’aide du portail de gestion Azure, ou à l’aide d’un fichier de configuration réseau.

## <a name="creating-and-modifying-a-network-configuration-file"></a>Création et modification d’un fichier de configuration du réseau 
Pour créer un fichier de configuration du réseau le plus simple consiste à exporter les paramètres réseau à partir d’une configuration réseau virtuel existante, puis modifiez le fichier pour contenir les paramètres que vous voulez configurer pour vos réseaux virtuel.

Pour modifier le fichier de configuration du réseau, vous pouvez simplement ouvrir le fichier, apportez les modifications appropriées, puis enregistrez le fichier. Vous pouvez utiliser n’importe quel éditeur *xml* pour apporter des modifications au fichier de configuration réseau. 

Vous devez suivre étroitement les instructions pour les [paramètres de schéma de fichier de configuration réseau](https://msdn.microsoft.com/library/azure/jj157100.aspx). 

Azure considère comme un sous-réseau qui a quelque chose déployée sur celui-ci comme **en cours d’utilisation**. Lorsqu’un constitue en cours d’utilisation, elle ne peut pas être modifiée. Avant de modifier, déplacer tout ce que vous avez déployé sur le sous-réseau à un autre dont n’est pas en cours de modification.   Voir [déplacer une machine virtuelle ou une Instance de rôle à en choisir un](virtual-networks-move-vm-role-to-subnet.md).

## <a name="export-and-import-virtual-network-settings-using-the-management-portal"></a>Exporter et importer les paramètres de réseau virtuel à l’aide du portail de gestion  
Vous pouvez importer et exporter les paramètres de configuration réseau figurant dans votre fichier de configuration du réseau à l’aide de PowerShell ou le portail de gestion. Les instructions ci-dessous vous aidera à exporter et importer à l’aide du portail de gestion. 

### <a name="to-export-your-network-settings"></a>Pour exporter les paramètres de votre réseau
Lorsque vous exportez, tous les paramètres pour les réseaux virtuels dans votre abonnement seront écrit vers un fichier .xml. 

1. Connectez-vous au **portail de gestion**.
2. Dans le portail de gestion, en bas de la page **réseaux** , cliquez sur **Exporter**. 
3. Dans la fenêtre **Exporter la configuration réseau** , vérifiez que vous avez sélectionné l’abonnement pour lequel vous souhaitez exporter les paramètres de votre réseau. Ensuite, cliquez sur la coche dans le coin inférieur droit. 
4. Lorsque vous y êtes invité, enregistrez le fichier *NetworkConfig.xml* à l’emplacement de votre choix.


### <a name="to-import-your-network-settings"></a>Pour importer les paramètres de votre réseau

1. Dans le **Portail de gestion**, dans le volet de navigation en bas à gauche, cliquez sur **Nouveau**.
2. Cliquez sur **Services de réseau** -> **réseau virtuel** -> **Importer la Configuration**.
3. Dans la page **Importer le fichier de configuration du réseau** , accédez à votre fichier de configuration du réseau, puis cliquez sur la flèche **suivant** .
4. Dans la page **créer votre réseau** , vous verrez d’informations sur l’écran affichant les sections de configuration de votre réseau ne seront modifiées ou créées. Si les modifications sont correctes pour vous, cliquez sur la case à cocher pour passer à mettre à jour ou créez votre réseau virtuel. 
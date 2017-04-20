<properties 
   pageTitle="Gérer les serveurs DNS utilisés par un réseau virtuel (VNet)"
   description="Découvrez comment ajouter et supprimer des serveurs DNS dans un réseau virtuel (vnet)"
   services="virtual-network"
   documentationCenter="na"
   authors="jimdial"
   manager="carmonm"
   editor="tysonn" />
<tags 
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="03/15/2016"
   ms.author="jdial" />

# <a name="manage-dns-servers-used-by-a-virtual-network-vnet"></a>Gérer les serveurs DNS utilisés par un réseau virtuel (VNet)

Vous pouvez gérer la liste des serveurs DNS utilisés dans un VNet dans le portail de gestion ou dans le fichier de configuration réseau. Vous pouvez ajouter jusqu'à 12 serveurs DNS pour chaque VNet. Lorsque vous spécifiez serveurs DNS, il est important de vérifier que vous affichez vos serveurs DNS dans l’ordre correct pour votre environnement. Listes de serveurs DNS ne fonctionnent pas cyclique. Ils sont utilisés dans l’ordre dans lequel ils sont spécifiés. Si le premier serveur DNS dans la liste ne peut être contacté, le client utilisera ce serveur DNS indépendamment si le serveur DNS fonctionne correctement ou non. Pour modifier l’ordre du serveur DNS pour votre réseau virtuel, supprimer les serveurs DNS dans la liste et les ajouter dans l’ordre de votre choix.

>[AZURE.WARNING] Une fois que la liste DNS a été mis à jour, vous devez redémarrer les machines virtuelles situés dans votre réseau virtuel afin qu’ils décrochez les nouveaux paramètres de serveur DNS. Machines virtuelles continuent à utiliser leur configuration en cours jusqu'à ce qu’ils ont redémarré.

## <a name="edit-a-dns-server-list-for-a-virtual-network-using-the-management-portal"></a>Modifier une liste de serveurs DNS pour un réseau virtuel à l’aide du portail de gestion

1. Connectez-vous au **portail de gestion**.

1. Dans le volet de navigation, cliquez sur **les réseaux**, puis cliquez sur le nom de votre réseau virtuel apparaît dans la colonne **nom** .

1. Cliquez sur **configurer**.

1. Dans les **Serveurs DNS**, vous pouvez configurer les éléments suivants :

    - **Enregistrer le serveur (Ajouter) une nouvelle DNS :** Simplement taper le nom et l’adresse IP dans les zones. Un serveur DNS est ajouté à votre réseau virtuel liste serveurs DNS et enregistre également le serveur DNS avec Azure.

    - **Pour ajouter un serveur DNS qui a été enregistré précédemment –** Si vous déjà enregistré un serveur DNS avec Azure, vous pouvez le sélectionner dans la liste prédéfinie.

    - **Pour supprimer un serveur DNS de votre réseau virtuel –** Cliquez sur le X en regard du serveur que vous voulez supprimer. Notez que cela supprime uniquement le serveur de cette liste réseau virtuel. Le serveur DNS reste inscrit dans Azure pour vos autres réseaux virtuel à utiliser. Pour supprimer un serveur DNS de votre abonnement, accédez à la page **réseaux -> DNS Servers** .

    - **Pour réorganiser les serveurs DNS :** Supprimer tous les serveurs DNS qui sont répertoriés, puis ajoutez-les consignez dans l’ordre de votre choix. N’oubliez pas qu’il ne s’agit pas d’une liste DNS cyclique.

    - **Pour renommer un serveur DNS :** Mettez en surbrillance le serveur DNS dans la liste, puis tapez le nouveau nom. Cela sera enregistrer un nouveau serveur DNS dans Azure, ainsi que l’ajouter à la liste des serveurs DNS pour votre réseau virtuel. L’ancien serveur DNS et son adresse IP resteront enregistrés auprès d’Azure. Vous pouvez le supprimer dans la page **DNS Servers** si vous ne l’utilisez pas pour les autres réseaux virtuel.

1. Cliquez sur **Enregistrer** dans la partie inférieure de la page pour enregistrer votre nouvelle configuration de serveurs DNS.

1. Redémarrez les ordinateurs virtuels situés dans le réseau virtuel pour lui permettre d’acquérir les nouveaux paramètres DNS.

## <a name="edit-a-dns-server-list-using-a-network-configuration-file"></a>Modifier une liste des serveurs DNS à l’aide d’un fichier de configuration réseau

Pour modifier une liste de serveurs DNS à l’aide d’un fichier de configuration du réseau, vous devez d’abord exporter vos paramètres de configuration à partir du portail de gestion. Vous devez ensuite modifier le fichier de configuration du réseau et importer via le portail de gestion. Voici une liste des étapes de pour effectuer cette procédure.

1. Exporter les paramètres de votre réseau virtuel vers un fichier de configuration réseau. Pour plus d’informations et des étapes à suivre pour exporter les paramètres de configuration de votre réseau, voir [Paramètres du réseau virtuel exporter vers un fichier de Configuration réseau](virtual-networks-using-network-configuration-file.md).

1. Spécifier les informations du serveur DNS pour votre réseau virtuel. Pour plus d’informations sur la spécification d’un serveur DNS, voir [spécification d’un serveur DNS dans un fichier de Configuration de réseau virtuel](virtual-networks-specifying-a-dns-settings-in-a-virtual-network-configuration-file.md). Pour plus d’informations sur les fichiers de configuration réseau, voir [Schéma de Configuration du réseau virtuel Azure](https://msdn.microsoft.com/library/azure/jj157100.aspx) et [configurer d’un réseau virtuel à l’aide d’un fichier de Configuration réseau](virtual-networks-using-network-configuration-file.md).

1. Importer le fichier de configuration réseau. Pour plus d’informations et des étapes à suivre pour importer votre fichier de configuration réseau, voir [Importer un fichier de Configuration réseau](virtual-networks-using-network-configuration-file.md).

1. Redémarrez les ordinateurs virtuels situés dans le réseau virtuel pour lui permettre d’acquérir les nouveaux paramètres DNS.

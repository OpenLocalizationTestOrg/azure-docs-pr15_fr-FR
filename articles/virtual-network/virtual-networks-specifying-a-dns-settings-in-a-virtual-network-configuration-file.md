<properties 
   pageTitle="Spécification des paramètres DNS dans un fichier de configuration réseau virtuel | Microsoft Azure"
   description="Comment modifier les paramètres du serveur DNS dans un réseau virtuel à l’aide d’un fichier de configuration de réseau virtuel dans le modèle de déploiement classique"
   services="virtual-network"
   documentationCenter="na"
   authors="jimdial"
   manager="carmonm"
   editor="tysonn" 
   tags="azure-service-management" />
<tags 
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="02/23/2016"
   ms.author="jdial" /> 


# <a name="specifying-dns-settings-in-a-virtual-network-configuration-file"></a>Spécifier les paramètres DNS dans un fichier de configuration de réseau virtuel

Un fichier de configuration réseau comporte deux éléments que vous pouvez utiliser pour spécifier les paramètres de nom de domaine (DNS) : **DnsServers** et **DnsServerRef**. Vous pouvez ajouter une liste de serveurs DNS en spécifiant leurs adresses IP et les noms de référence à l’élément **DnsServers** . Vous pouvez ensuite utiliser un élément **DnsServerRef** pour spécifier les entrées du serveur DNS à partir de l’élément DnsServers sont utilisées pour les sites de réseau différent au sein de votre réseau virtuel.

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]Cet article décrit le modèle de déploiement classique.

Le fichier de configuration du réseau peut contenir les éléments suivants. Le titre de chaque élément est lié à une page qui fournit des informations supplémentaires sur les paramètres de valeur de l’élément.

>[AZURE.IMPORTANT] Pour plus d’informations sur la configuration du fichier de configuration réseau, voir [configurer d’un réseau virtuel à l’aide d’un fichier de Configuration réseau](virtual-networks-using-network-configuration-file.md). Pour plus d’informations sur chaque élément contenu dans le fichier de configuration réseau, voir [Azure schéma de Configuration réseau virtuel](https://msdn.microsoft.com/library/azure/jj157100.aspx).

[Élément de DNS](http://go.microsoft.com/fwlink/?LinkId=248093)

    <Dns>
      <DnsServers>
        <DnsServer name="ID1" IPAddress="IPAddress1" />
        <DnsServer name="ID2" IPAddress="IPAddress2" />
        <DnsServer name="ID3" IPAddress="IPAddress3" />
      </DnsServers>
    </Dns>

>[AZURE.WARNING] L’attribut de **nom** dans l’élément **ServeurDNS** est utilisé uniquement comme référence pour l’élément **DnsServerRef** . Il ne représente pas le nom d’hôte du serveur DNS. Chaque valeur de l’attribut **ServeurDNS** doit être unique dans tout l’abonnement Microsoft Azure

[Élément de Sites réseau virtuel](http://go.microsoft.com/fwlink/?LinkId=248093)

    <DnsServersRef>
      <DnsServerRef name="ID1" />
      <DnsServerRef name="ID2" />
      <DnsServerRef name="ID3" />
    </DnsServersRef>

>[AZURE.NOTE] Afin de spécifier ce paramètre pour l’élément Sites réseau virtuels, elle doit être précédemment défini dans l’élément DNS. Le DnsServerRef *nom* dans l’élément Sites réseau virtuels doit faire référence à une valeur de nom spécifiée dans l’élément DNS serveurDNS *nom*.

## <a name="next-steps"></a>Étapes suivantes

- Comprendre le [schéma de Configuration du réseau virtuel Azure](http://go.microsoft.com/fwlink/?LinkId=248093).
- Comprendre le [schéma de Configuration de Service Azure](https://msdn.microsoft.com/library/windowsazure/ee758710).
- [Configurer un réseau virtuel à l’aide de fichiers de configuration réseau](virtual-networks-using-network-configuration-file.md).

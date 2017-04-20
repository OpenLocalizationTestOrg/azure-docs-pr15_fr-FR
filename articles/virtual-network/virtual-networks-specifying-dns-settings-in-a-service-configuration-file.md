<properties 
   pageTitle="Spécification des paramètres DNS dans un fichier de configuration de service | Microsoft Azure"
   description="spécifier les paramètres DNS personnalisés à l’aide du fichier de configuration de service de réseau virtuel"
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
   ms.date="02/24/2016"
   ms.author="jdial" />

# <a name="specifying-dns-settings-in-a-service-configuration-file"></a>Spécifier les paramètres DNS dans un fichier de Configuration de Service

## <a name="dns-elements"></a>Éléments DNS

Un fichier de configuration de service peut contenir un élément DnsServers avec une liste d’adresses IPv4 pour les serveurs de nom de domaine (DNS) utilisées par le service. Paramètres dans le fichier de configuration de service ont la priorité sur les paramètres dans le fichier de configuration réseau. Pour plus d’informations, consultez [Schéma de Configuration du Service Azure (fichier .cscfg)](https://msdn.microsoft.com/library/azure/ee758710.aspx).

**Élément NetworkConfiguration**

      <DnsServers>
        <DnsServer name="ID1" IPAddress="IPAddress1" />
        <DnsServer name="ID2" IPAddress="IPAddress2" />
        <DnsServer name="ID3" IPAddress="IPAddress3" />
      </DnsServers>

>[AZURE.WARNING] L’attribut **name** dans l’élément **ServeurDNS** sert uniquement un nom de référence. Il ne représente pas le nom d’hôte du serveur DNS. Chaque valeur de l’attribut **ServeurDNS** doit être unique dans tout l’abonnement Microsoft Azure.

## <a name="see-also"></a>Voir aussi

[Schéma de Configuration de Service Azure (.cscfg)](https://msdn.microsoft.com/library/windowsazure/ee758710)

[Schéma de Configuration du réseau virtuel Azure](http://go.microsoft.com/fwlink/?LinkId=248093)

[Configurer un réseau virtuel à l’aide de fichiers de Configuration réseau](http://go.microsoft.com/fwlink/?LinkId=248094)

[À propos des paramètres de réseau virtuel du portail de gestion](http://go.microsoft.com/fwlink/?LinkId=248092)


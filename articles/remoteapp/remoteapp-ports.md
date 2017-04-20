
<properties
    pageTitle="Liste des Ports et les URL à liste d’autorisation pour Azure RemoteApp déployé dans le réseau des clients virtuel | Microsoft Azure"
    description="Découvrez les ports et les URL, vous devez configurer pour la communication via Azure RemoteApp."
    services="remoteapp"
    documentationCenter=""
    authors="mghosh1616"
    manager="mbaldwin" />

<tags
    ms.service="remoteapp"
    ms.workload="compute"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/16/2016"
    ms.author="elizapo" />



# <a name="list-of-ports-and-urls-to-permit-access-for-azure-remoteapp-deployed-in-customer-virtual-network"></a>Liste des Ports et URL pour autoriser l’accès à Azure RemoteApp déployé dans client réseau virtuel 

> [AZURE.IMPORTANT]
> RemoteApp Azure a été interrompu. Lire l' [annonce](https://go.microsoft.com/fwlink/?linkid=821148) pour plus d’informations.

Les éléments suivants s’applique aux Azure RemoteApp une collection cloud ou hybride si vous le déployez dans un réseau virtuel (VNET). Pour plus d’informations sur les réseaux virtuels, veuillez lire [Virtuel vue d’ensemble du réseau](../virtual-network/virtual-networks-overview.md). Si vous avez créé un groupe de sécurité réseau (NSG) limiter le trafic aux ressources de votre réseau virtuel que vous avez choisi pour Azure RemoteApp, vérifiez que les éléments suivants sont accessibles et autorisé via les stratégies de sécurité sur le réseau virtuel. Pour plus d’informations sur les groupes de sécurité réseau, veuillez lire [qu’est un groupe de sécurité du réseau ? (NSG)](../virtual-network/virtual-networks-nsg.md).

##  <a name="azure-remoteapp-subnet-needs-access-to-these-endpoints-and-urls"></a>Azure RemoteApp sous-réseau a besoin d’accéder à ces points de terminaison et l’URL : 
*   *. servicebus.windows.net
*    *. servicebus.net
*    https://*.RemoteApp.windwsazure.com  
*    https://www.RemoteApp.windowsazure.com 
*    https://*RemoteApp.windowsazure.com  
*    https://*.Core.Windows.NET  
*    Sortant : TCP : 443, TCP : 10101 10175 
*    Facultatif : UDP : 10201 10275  
 
## <a name="azure-remoteapp-clients-need-access-to-these-endpoints-and-urls"></a>Azure RemoteApp clients doivent accéder à ces points de terminaison et l’URL : 

J’entends les ordinateurs de bureau, appareils etc. que les utilisateurs utiliser pour vous connecter aux applications déployées dans la collection Azure RemoteApp par les clients.

-  https://telemetry.RemoteApp.windowsazure.com  
-  https://*.RemoteApp.windowsazure.com (les ports UDP facultatifs sont pour cette adresse) 
-  https://login.Windows.NET  
-  https://login.microsoftonline.com  
-  https://www.RemoteApp.windowsazure.com 
-  https://*.Core.Windows.NET  
-  Sortant : TCP : 443  
-  Facultatif - UDP : 3391 
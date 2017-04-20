
<properties
    pageTitle="Redimensionnement des informations pour un VNET dans Azure RemoteApp | Microsoft Azure"
    description="En savoir plus sur la configuration requise adresse IP pour RemoteApp Azure en cours d’exécution avec un VNET"
    services="remoteapp"
    documentationCenter=""
    authors="lizap"
    manager="mbaldwin" />

<tags
    ms.service="remoteapp"
    ms.workload="compute"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/15/2016"
    ms.author="elizapo" />



# <a name="sizing-information-for-a-vnet-in-azure-remoteapp"></a>Informations sur la taille d’un VNET dans Azure RemoteApp

> [AZURE.IMPORTANT]
> RemoteApp Azure a été interrompu. Lire l' [annonce](https://go.microsoft.com/fwlink/?linkid=821148) pour plus d’informations.

Lorsque vous utilisez Azure RemoteApp avec un réseau virtuel (VNET), RemoteApp utilise des adresses IP au sein du sous-réseau. En fonction de l’échelle de votre service RemoteApp, vous devez vous assurer que votre sous-réseau dispose de suffisamment d’adresses IP pour les machines virtuelles RemoteApp. Tandis que ce guide de dimensionnement n’est pas idéal donné comment RemoteApp tourne et tourne vers le bas machines virtuelles au sein d’une collection de façon dynamique, il vous aider à évaluer votre plage sous-réseau. Ceci est particulièrement important que, une fois qu’un service RemoteApp est placé dans une VNET, vous ne pouvez pas augmenter la taille de sous-réseau sans supprimer RemoteApp.

Pour chaque collection RemoteApp que vous voulez exécuter à la capacité maximale, vous devez avoir 100 adresses IP disponibles. Par exemple, si vous disposez d’une collection de RemoteApp dans le plan Standard et que vous souhaitez que le nombre maximal de 500 utilisateurs, vous devez disposer 100 adresses IP pour cette collection. De même, vous devez 100 adresses IP pour une collection de sites RemoteApp dans le plan de base qui a 800 utilisateurs. Si vous envisagez d’avoir un nombre d’utilisateurs (inférieure à la valeur maximale), vous pouvez réduire les adresses IP nécessaires par collection de sites. L’exigence de taille minimale sous-réseau est 30 adresses IP (/ 27).

Vérifier les informations suivantes pour vous assurer que votre VNET est correctement configuré et opérationnel :

- [Migrer à partir d’un VNET personnel pour un VNET Azure](remoteapp-migratevnet.md)
- [Valider la VNET Azure à utiliser avec Azure RemoteApp](remoteapp-vnet.md)

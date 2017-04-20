
<properties
    pageTitle="Valider la VNET Azure à utiliser avec Azure RemoteApp | Microsoft Azure"
    description="Découvrez comment pour vous assurer que votre VNET Azure est prêt à utiliser avec Azure RemoteApp"
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



# <a name="validate-the-azure-vnet-to-use-with-azure-remoteapp"></a>Valider la VNET Azure à utiliser avec Azure RemoteApp

> [AZURE.IMPORTANT]
> RemoteApp Azure a été interrompu. Lire l' [annonce](https://go.microsoft.com/fwlink/?linkid=821148) pour plus d’informations.

Avant d’utiliser un VNET Azure avec Azure RemoteApp, vous souhaiterez peut-être valider le VNET. Cela permet d’éviter les problèmes de connectivité.

Pour valider votre VNET Azure, procédez comme suit :

1. Créer une machine virtuelle Azure l’intérieur sous le réseau de la VNET Azure que vous voulez utiliser avec Azure RemoteApp.

2. Se connecter à cette machine virtuelle à l’aide de l’option **connexion** dans le portail de gestion.
3. Participer à la machine virtuelle au même domaine que vous voulez utiliser avec Azure RemoteApp. Si vous créez une collection hybride qui se connecte à votre réseau local, participer à la machine virtuelle à votre domaine local.

Si l’opération réussit, la VNET Azure est prêt à utiliser avec RemoteApp.

Pour plus d’informations sur le flux de travail de la collection de sites hybride de bout en bout, voir les articles suivants :

- [Comment planifier votre réseau virtuel RemoteApp Azure](remoteapp-planvnet.md)
- [Créer une collection hybride](remoteapp-create-hybrid-deployment.md)
- [Déployer collection Azure RemoteApp à votre réseau virtuel Azure (avec prise en charge ExpressRoute)](http://blogs.msdn.com/b/rds/archive/2015/04/23/deploy-azure-remoteapp-collection-to-your-azure-virtual-network-with-support-for-expressroute.aspx)

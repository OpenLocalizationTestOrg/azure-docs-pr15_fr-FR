<properties
   pageTitle="À l’aide de SAP sur machines virtuelles Windows | Microsoft Azure"
   description="Effacer sur l’utilisation de SAP sur des ordinateurs Windows virtuels (machines virtuelles) dans Microsoft Azure"
   services="virtual-machines-windows,virtual-network,storage"
   documentationCenter="saponazure"
   authors="MSSedusch"
   manager="timlt"
   editor=""
   tags="azure-service-management"
   keywords=""/>
<tags
   ms.service="virtual-machines-windows"
   ms.devlang="NA"
   ms.topic="campaign-page"
   ms.tgt_pltfrm="vm-windows"
   ms.workload="na"
   ms.date="10/04/2016"
   ms.author="sedusch"/>

# <a name="using-sap-on-windows-virtual-machines-in-azure"></a>À l’aide de SAP sur machines virtuelles Windows Azure

Le Cloud Computing est un terme couramment utilisé qui se distingue plus importance au sein du secteur informatique, à partir de petites entreprises jusqu'à multinationales et grandes entreprises. Microsoft Azure est la plateforme de Services de Cloud de Microsoft qui offre un large éventail de nouvelles possibilités. Maintenant, les clients sont en mesure de mise en service et de mise en service de désactiver les applications en tant que Services en nuage, afin qu’ils ne sont pas limités à la limitation de support technique ou budgétisation rapidement. Au lieu d’avoir à investir temps et le budget en infrastructure matérielle, sociétés peuvent se concentrer sur l’application, des processus métiers et ses avantages pour les clients et les utilisateurs.

Avec machines virtuelles Microsoft Azure, Microsoft propose une Infrastructure complète en tant que Service (IaaS) plate-forme. Les applications SAP NetWeaver basé sont prises en charge sur Machines virtuelles Azure (IaaS). Les livres blancs ci-dessous décrivent comment planifier et implémenter les applications SAP NetWeaver basé sur machines virtuelles Windows Azure. Vous pouvez également implémenter les applications SAP NetWeaver basé sur des [machines virtuelles Linux](virtual-machines-linux-classic-sap-get-started.md).

[AZURE.INCLUDE [virtual-machines-common-classic-sap-get-started](../../includes/virtual-machines-common-classic-sap-get-started.md)]

## <a name="sap-netweaver-on-azure---ha"></a>SAP NetWeaver sur Azure - HA

Titre : SAP NetWeaver sur Azure - cluster SAP ASC/SCS Instances à l’aide de Cluster de basculement Windows Server sur Azure avec DataKeeper SIÔS

Résumé : « ce document explique comment utiliser SIÔS DataKeeper pour configurer une configuration SAP ASC/SCS hautement disponible sur Azure. SAP protège leur centralisation des composants échec comme SAP ASC/SCS ou les Services de réplication de file d’attente avec les configurations de Cluster de basculement Windows Server nécessitant des disques partagés. Ces composants SAP sont essentiels pour les fonctionnalités d’un système SAP. Par conséquent, et la disponibilité des fonctionnalités devant être mis en place pour vous assurer que ces composants peuvent soutenir une défaillance d’un serveur ou une machine virtuelle comme traité des configurations Cluster Windows pour vierge et environnements Hyper-V. Depuis août 2015 Azure sur lui-même ne peut pas fournir disques partagés qui seraient requis pour les fenêtres en fonction de disponibilité configurations requises pour ces composants SAP critiques. Toutefois à l’aide du produit DataKeeper par SIÔS, configurations Cluster de basculement Windows Server nécessaire pour SAP ASC/SCS peuvent être générées sur la plateforme Azure IaaS. Cet article décrit dans une approche étape par étape comment installer une configuration Cluster de basculement Windows Server avec disque partagé fourni par Datakeeper SIÔS dans Azure. Le papier montre les détails dans les configurations sur le côté Azure, Windows et SAP, ce qui facilite la configuration de disponibilité fonctionnent de manière optimale. Le papier complète la Documentation d’Installation SAP et les Notes SAP qui représentent les ressources principales pour les installations et les déploiements de logiciel SAP sur donné plateformes.

Mise à jour : Août 2015

[Téléchargez ce guide maintenant](http://go.microsoft.com/fwlink/?LinkId=613056)

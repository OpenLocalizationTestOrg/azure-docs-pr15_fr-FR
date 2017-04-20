<properties 
   pageTitle="Débogage des Services Cloud Azure | Microsoft Azure"
   description="Débogage des Services Cloud Azure"
   services="visual-studio-online"
   documentationCenter="n/a"
   authors="TomArcher"
   manager="douge"
   editor="" />
<tags 
   ms.service="visual-studio-online"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="multiple"
   ms.workload="na"
   ms.date="08/15/2016"
   ms.author="tarcher" />

# <a name="debugging-cloud-services"></a>Services de cloud débogage

Vous pouvez utiliser différentes approches pour déboguer une application Azure en utilisant les outils Azure pour Microsoft Visual Studio et le Kit de développement Azure :

- Vous pouvez déboguer une application Azure à partir de Visual Studio lorsque vous développez, comme vous le feriez pour n’importe quelle application Visual c# ou Visual Basic. Pour plus d’informations, voir [Déboguer votre service cloud sur votre ordinateur local](vs-azure-tools-debug-cloud-services-virtual-machines.md#debug-your-cloud-service-on-your-local-computer).

- Vous pouvez utiliser Azure Diagnostics pour enregistrer des informations détaillées à partir de code qui s’exécutent dans des rôles, si les rôles sont exécutent dans l’environnement de développement ou dans Azure. Pour plus d’informations, voir [collecte journalisation des données à l’aide des Diagnostics Azure](http://go.microsoft.com/fwlink/p/?LinkId=400450).

- Si vous utilisez Visual Studio Enterprise pour écrire des rôles destinées aux .NET Framework 4 ou le .NET Framework 4.5, vous pouvez activer IntelliTrace au moment où que vous déployez un service cloud Azure à partir de Visual Studio. IntelliTrace fournit un journal que vous pouvez utiliser avec Visual Studio pour déboguer votre application comme s’il était exécuté dans Azure. Pour plus d’informations, consultez [débogage d’un service cloud publiés avec IntelliTrace et Visual Studio]( http://go.microsoft.com/fwlink/p/?LinkId=623016).

- Vous pouvez activer le débogage distant sur vos services cloud en temps lorsque vous déployez le service de nuage à partir de Visual Studio. Si vous choisissez d’activer le débogage distant pour un déploiement, services de débogage distant sont installés sur les ordinateurs virtuels qui s’exécutent chaque instance de rôle. Ces services, tels que msvsmon.exe, pas d’affecter les performances ou génèrent des coûts supplémentaires. Pour plus d’informations, consultez [débogage d’un service cloud dans Azure](vs-azure-tools-debug-cloud-services-virtual-machines.md#debug-a-cloud-service-in-azure).




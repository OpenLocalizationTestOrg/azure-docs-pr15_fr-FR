<properties
   pageTitle="Créer un conteneur de service cloud avec PowerShell | Microsoft Azure"
   description="Cet article explique comment créer un conteneur de services de cloud avec PowerShell. Le conteneur héberge rôles web et de travail."
   services="cloud-services"
   documentationCenter=".net"
   authors="cawaMS"
   manager="timlt"
   editor=""/>

<tags
   ms.service="cloud-services"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="powershell"
   ms.workload="na"
   ms.date="07/29/2016"
   ms.author="cawa"/>

# <a name="use-an-azure-powershell-command-to-create-an-empty-cloud-service-container"></a>Utiliser une commande PowerShell Azure pour créer un conteneur de service cloud vide
Cet article explique comment créer rapidement un conteneur de Services de Cloud à l’aide des applets de commande PowerShell Azure. Suivez les étapes suivantes :

1. Installez l’applet de commande Microsoft Azure PowerShell à partir de la page de [téléchargements de PowerShell Azure](http://aka.ms/webpi-azps) .
2. Ouvrez l’invite de commande PowerShell.
3. Utilisez [Ajouter AzureAccount](https://msdn.microsoft.com/library/dn495128.aspx) pour vous connecter.

    > [AZURE.NOTE] Pour obtenir plus d’informations sur l’installation de l’applet de commande PowerShell Azure et la connexion à votre abonnement Azure, reportez-vous à [comment installer et configurer Azure PowerShell](../powershell-install-configure.md).

4. Utiliser l’applet de commande **New-AzureService** pour créer un conteneur de service cloud Azure vide.

    ```
    New-AzureService [-ServiceName] <String> [-AffinityGroup] <String> [[-Label] <String>] [[-Description] <String>] [[-ReverseDnsFqdn] <String>] [<CommonParameters>]
    New-AzureService [-ServiceName] <String> [-Location] <String> [[-Label] <String>] [[-Description] <String>] [[-ReverseDnsFqdn] <String>] [<CommonParameters>]
```

5. Suivez cet exemple pour appeler l’applet de commande :
```
New-AzureService -ServiceName "mytestcloudservice" -Location "Central US" -Label "mytestcloudservice"
```

Pour plus d’informations sur la création du service cloud Azure, exécutez :
```
Get-help New-AzureService
```

### <a name="next-steps"></a>Étapes suivantes

 * Pour gérer le déploiement de service cloud, consultez les commandes [Get-AzureService](https://msdn.microsoft.com/library/azure/dn495131.aspx), [Supprimer AzureService](https://msdn.microsoft.com/library/azure/dn495120.aspx)et [Jeu AzureService](https://msdn.microsoft.com/library/azure/dn495242.aspx) . Vous pouvez également reportez-vous à [la configurer les services de cloud](cloud-services-how-to-configure.md) pour plus d’informations.

 * Pour publier votre projet de service cloud sur Azure, reportez-vous à l’exemple de code **PublishCloudService.ps1** de [remise continue pour le service cloud dans Azure](cloud-services-dotnet-continuous-delivery.md).

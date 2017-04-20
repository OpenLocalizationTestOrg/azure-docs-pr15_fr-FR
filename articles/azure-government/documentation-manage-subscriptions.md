<properties
    pageTitle="Pour le gouvernement Azure Services | Microsoft Azure"
    description="Informations sur la gestion de votre abonnement dans Azure pour le gouvernement"
    services="Azure-Government"
    cloud="gov" 
    documentationCenter=""
    authors="zakramer"
    manager="liki"
    editor="" />

<tags
    ms.service="multiple"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="azure-government"
    ms.date="10/21/2016"
    ms.author="zakramer" />


#  <a name="managing-and-connecting-to-your-subscription-in-azure-government"></a>Gestion et la connexion à votre abonnement dans Azure pour le gouvernement

Pour le gouvernement Azure a uniques URL et les points de terminaison pour gérer votre environnement. Il est important d’utiliser les connexions droite pour gérer votre environnement via le portail ou PowerShell. Une fois que vous êtes connecté à l’environnement pour le gouvernement Azure, les opérations normales pour la gestion d’un service fonctionne si le composant a été déployé.

## <a name="connecting-via-the-portal"></a>Connexion via le portail
Le portail est le principal moyen de la plupart des personnes se connectent à Azure pour le gouvernement.  Pour vous connecter, accédez au portail à [https://portal.azure.us](https://portal.azure.us).  L’ancienne version du portail Azure sont accessibles via [https://manage.windowsazure.us](https://manage.windowsazure.us).

Abonnements peuvent être créés pour votre compte en vous connectant à [https://account.windowsazure.us](https://account.windowsazure.us).

## <a name="connecting-via-powershell"></a>Connexion via PowerShell

Si vous utilisez Azure PowerShell pour gérer un grand abonnement via script ou accès aux fonctionnalités qui ne sont pas disponibles dans le portail Azure, que vous devez vous connecter à pour le gouvernement Azure au lieu Public Azure.  Si vous avez utilisé PowerShell dans Azure Public, il est principalement la même.  Différences entre les gouvernement Azure sont :

+ Se connecter à votre compte
+ Noms de région

>[AZURE.NOTE] Si vous n’avez pas encore utilisé PowerShell, consultez l' [Introduction à Azure PowerShell](../powershell-install-configure.md).

Lorsque vous démarrez PowerShell, vous devez indiquer Azure PowerShell pour vous connecter à Azure pour le gouvernement en spécifiant un paramètre d’environnement.  Le paramètre garantit que PowerShell se connecte aux points de terminaison correctes.  La collection de points de terminaison est déterminée lorsque vous vous connectez connecté à votre compte.  Différentes API requièrent différentes versions du commutateur environnement :

Type de connexion | Commande
---|----
Commandes de [Gestion des services](https://msdn.microsoft.com/library/dn708504.aspx) | `Add-AzureAccount -Environment AzureUSGovernment`
Commandes de [Gestion des ressources](https://msdn.microsoft.com/library/mt125356.aspx) | `Add-AzureRmAccount -EnvironmentName AzureUSGovernment`
Commandes [Azure Active Directory](https://msdn.microsoft.com/library/azure/jj151815.aspx) | `Connect-MsolService -AzureEnvironment UsGovernment`
[Azure Active Directory commande v2](https://msdn.microsoft.com/library/azure/mt757189.aspx) | `Connect-AzureAD -AzureEnvironmentName AzureUSGovernment`

Vous pouvez également utiliser le commutateur environnement lors de la connexion à un compte de stockage à l’aide de nouveau AzureStorageContext et spécifier AzureUSGovernment.

### <a name="determining-region"></a>Détermination de région

Une fois que vous êtes connecté, il existe une différence supplémentaire – les régions utilisées pour cibler un service.  Chaque cloud Azure comporte différentes régions.  Vous pouvez les voir répertorié sur la page de disponibilité du service.  Vous utilisez normalement la région dans le paramètre d’emplacement pour une commande.

Il existe une capture.  Les régions pour le gouvernement Azure doivent être formaté différemment de leurs noms courantes :

Nom commun | Commande
---|----
États-Unis et pour le gouvernement Virginie | Virginie USGov
États-Unis et pour le gouvernement Iowa | Iowa USGov

>[AZURE.NOTE] Il n’y a aucun espace entre les États-Unis et et pour le gouvernement lorsque vous utilisez le paramètre d’emplacement.

Si vous souhaitez que jamais valider les zones disponibles dans Azure pour le gouvernement, vous pouvez exécuter les commandes suivantes et imprimer la liste actuelle :

    Get-AzureLocation

Si vous êtes introduites dans les environnements disponibles sur Azure, vous pouvez exécuter :

    Get-AzureEnvironment

## <a name="next-steps"></a>Étapes suivantes

Si vous recherchez plus d’informations, vous pouvez consulter :

+ [Documents PowerShell sur GitHub](https://github.com/Azure/azure-powershell)
+ [Instructions détaillées sur la connexion à la gestion des ressources](https://blogs.msdn.microsoft.com/azuregov/2015/10/08/configuring-arm-on-azure-gc/)
+ [Azure documents PowerShell sur MSDN](https://msdn.microsoft.com/library/mt619274.aspx)

Pour des informations supplémentaires et des mises à jour Abonnez-vous au [Microsoft Azure pour le gouvernement Blog] (https://blogs.msdn.microsoft.com/azuregov/)

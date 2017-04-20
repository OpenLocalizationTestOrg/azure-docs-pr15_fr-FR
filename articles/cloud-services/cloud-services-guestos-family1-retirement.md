<properties
   pageTitle="Famille de système d’exploitation invité 1 retraite Notez | Microsoft Azure"
   description="Fournit des informations sur quand est-il advenu de la retraite Azure invité OS famille 1 et explique comment déterminer si vous êtes affecté"
   services="cloud-services"
   documentationCenter="na"
   authors="raiye"
   manager="timlt"
   editor=""/>

<tags
   ms.service="cloud-services"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="tbd"
   ms.date="10/24/2016"
   ms.author="raiye"/>



# <a name="guest-os-family-1-retirement-notice"></a>Avis de retraite invité OS famille 1

1er juin 2013 a été présenté tout d’abord la retraite de système d’exploitation famille 1.

**2 septembre 2014** Le système d’exploitation de Azure invité (système d’exploitation invité) famille 1.x, qui est basé sur le système d’exploitation Windows Server 2008, a été déclassé embauche. Toutes les tentatives de déploiement de nouveaux services ou mettre à niveau des services existants à l’aide de la famille 1 échoue avec un message d’erreur vous informant que la 1 de famille du système d’exploitation invité a été supprimé.

**3 novembre 2014** Prise en charge étendue des invités OS famille 1 est terminée, et elle est entièrement supprimée. Tous les services toujours sur 1 famille seront affectées. Nous pouvons arrêter ces services à tout moment. Il n’existe aucune garantie que vos services continueront à s’exécuter, sauf si vous manuellement mettre à niveau les vous-même.

Si vous avez des questions supplémentaires, visitez les [Forums de Services de Cloud](http://social.msdn.microsoft.com/Forums/home?forum=windowsazuredevelopment&filter=alltypes&sort=lastpostdesc) ou [contactez le support Azure](https://azure.microsoft.com/support/options/).




## <a name="are-you-affected"></a>Sont concernés ?

Vos Services Cloud sont affectées si l’une des opérations suivantes s’appliquent :

1. Vous avez une valeur de « osFamily = « 1 » explicitement spécifié dans le fichier ServiceConfiguration.cscfg pour votre Service Cloud.
2. Vous n’avez pas une valeur pour osFamily explicitement spécifié dans le fichier ServiceConfiguration.cscfg pour votre Service Cloud. Pour l’instant, le système utilise la valeur par défaut de « 1 » dans ce cas.
3. Le portail classique Azure répertorie la valeur de votre système d’exploitation invité famille en tant que « Windows Server 2008 ».

Pour savoir qui de vos services cloud s’exécutent quels famille du système d’exploitation, vous pouvez exécuter le script ci-dessous dans Azure PowerShell, bien que vous devez [configurer Azure PowerShell](../powershell-install-configure.md) tout d’abord. Pour plus d’informations sur le script, voir [Azure invité OS famille 1 fin de vie : juin 2014](http://blogs.msdn.com/b/ryberry/archive/2014/04/02/azure-guest-os-family-1-end-of-life-june-2014.aspx). 

```Powershell
foreach($subscription in Get-AzureSubscription) {
    Select-AzureSubscription -SubscriptionName $subscription.SubscriptionName

    $deployments=get-azureService | get-azureDeployment -ErrorAction Ignore | where {$_.SdkVersion -NE ""}

    $deployments | ft @{Name="SubscriptionName";Expression={$subscription.SubscriptionName}}, ServiceName, SdkVersion, Slot, @{Name="osFamily";Expression={(select-xml -content $_.configuration -xpath "/ns:ServiceConfiguration/@osFamily" -namespace $namespace).node.value }}, osVersion, Status, URL
}
```

Vos services cloud seront affectées par déclassement OS famille 1 si la colonne osFamily dans la sortie de script est vide ou contient un « 1 ».

## <a name="recommendations-if-you-are-affected"></a>Recommandations si vous êtes concerné

Nous vous recommandons de que vous migrez vos rôles de Service Cloud à un des familles de système d’exploitation invité pris en charge :

**Système d’exploitation invité 4.x famille** -Windows Server 2012 R2 *(recommandé)*

1. Vérifiez que votre application utilise SDK 2.1 ou version ultérieur avec .NET framework 4.0, 4.5 ou 4.5.1.
2. Définissez l’attribut osFamily à « 4 » dans le fichier ServiceConfiguration.cscfg et redéployez votre service cloud.


**Système d’exploitation invité 3.x famille** -Windows Server 2012

1. Vérifiez que votre application utilise SDK 1,8 ou version ultérieur avec .NET framework 4.0 ou 4.5.
2. Définissez l’attribut osFamily à « 3 » dans le fichier ServiceConfiguration.cscfg et redéployez votre service cloud.


**Système d’exploitation invité 2.x famille** -Windows Server 2008 R2

1. Vérifiez que votre application utilise SDK 1.3 et versions ultérieures avec .NET framework 3.5 ou 4.0.
2. Définissez l’attribut osFamily à « 2 » dans le fichier ServiceConfiguration.cscfg et redéployez votre service cloud.


## <a name="extended-support-for-guest-os-family-1-ended-nov-3-2014"></a>Prise en charge étendue des invités OS famille 1 terminé 3 novembre 2014
Services cloud sur famille de système d’exploitation invité 1 ne sont plus pris en charge. Migrez désactiver famille 1 dès que possible afin d’éviter d’interruption de service.  

## <a name="next-steps"></a>Étapes suivantes
Passez en revue les dernières [mises à jour de système d’exploitation invité](cloud-services-guestos-update-matrix.md).

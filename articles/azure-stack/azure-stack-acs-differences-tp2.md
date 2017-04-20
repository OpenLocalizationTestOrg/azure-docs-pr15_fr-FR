
<properties
    pageTitle="Stockage Azure cohérentes : différences et considérations | Microsoft Azure"
    description="Comprendre les différences entre le stockage Azure et d’autres considérations de déploiement de stockage Azure cohérentes."
    services="azure-stack"
    documentationCenter=""
    authors="MChadalapaka"
    manager="siroy"
    editor=""/>

<tags
    ms.service="azure-stack"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="09/26/2016"
    ms.author="mchad"/>

# <a name="azure-consistent-storage-differences-and-considerations"></a>Stockage Azure cohérentes : différences et considérations

Stockage Azure cohérente est l’ensemble de stockage cloud services dans Microsoft Azure pile. Stockage Azure cohérentes fournit blob, table, file d’attente et de fonctionnalités de gestion de compte avec une sémantique Azure cohérente. Cet article récapitule les différences de stockage Azure cohérentes connus avec le stockage Azure. Autres considérations à garder à l’esprit lorsque vous déployez la version actuellement accessibles de Microsoft Azure pile sont également résumées.

<span id="Concepts" class="anchor"><span id="_Toc386544169" class="anchor"><span id="_Toc389466742" class="anchor"><span id="_Ref428966996" class="anchor"><span id="_Toc433223853" class="anchor"></span></span></span></span></span>
## <a name="known-differences"></a>Différences connues

Cette version de version d’évaluation technique de stockage Azure cohérentes n’a pas les disparités qui existent de fonctionnalité 100 % avec le stockage Azure pour les versions de l’API prises en charge. Connus différences de fonctionnalité incluent les éléments suivants :

-   Certains types de compte de stockage ne sont pas encore disponibles, par exemple, Standard\_RAGRS et Standard\_GRS.

-   Premium\_comptes de stockage LRS peuvent être mis en service. Toutefois, il n’existe actuellement aucune des limites de performances ou de garanties.

-   Fonctionnalité de fichiers Azure n’est pas encore disponible.

-   L’API de plages obtenir de Page n’autorise pas l’extraction de pages différentes entre des instantanés des objets BLOB de page.

-   L’API plages Page obtenir renvoie des pages qui ont 4 Ko de précision.

-   Clés de partition et ligne dans l’implémentation de Table stockage Azure cohérentes sont limités à 400 caractères (autrement dit, 800 octets) chacun.

-   Noms d’objets BLOB dans l’implémentation du service stockage Blob Azure cohérentes sont limités à 880 caractères (autrement dit, 1760 octets).

-   La mise en œuvre stockage Azure cohérente des données de l’utilisation de stockage client rapports offre mètres de l’utilisation de stockage sont identiques à ceux des Azure, avec les mêmes sémantique et unités. Pour l’instant, cependant, la jauge d’utilisation des Transactions de stockage n’inclut pas les transactions IaaS, et la jauge d’utilisation de transfert de données ne différencie pas l’utilisation de la bande passante par le trafic réseau internes ou externes à une région pile Azure.

-   Il existe certaines différences dans l’étendue de la fonctionnalité pour la gestion de stockage. Par exemple, vous ne pouvez pas modifier le type de compte ou que des domaines personnalisés. En outre, la cohérence au niveau de l’API uniquement est proposée pour la prime\_type de compte de stockage LRS.

## <a name="deployment-considerations"></a>Considérations relatives au déploiement

-   **Test uniquement.** Ne pas déployer stockage Azure cohérente dans les environnements de production qui utilisent la version actuelle de la version d’évaluation technique de Microsoft Azure pile. Cette version est destinée uniquement à des fins d’évaluation dans un environnement de test.

-   **Performances**. La version de Microsoft Azure pile Technical Preview de stockage Azure cohérentes n’est pas entièrement performances optimisées.

-   **Extensibilité élevées.** La version de Microsoft Azure pile Technical Preview de stockage Azure cohérentes n’est pas optimisée pour extensibilité élevées.

## <a name="version-support-considerations"></a>Considérations relatives à la version prise en charge

Les versions suivantes sont prises en charge dans cette version preview de stockage Azure cohérentes :

> Bibliothèque de Client de stockage Azure : [Microsoft Azure stockage 6.x .NET SDK](http://www.nuget.org/packages/WindowsAzure.Storage/6.2.0)
>
> Services de données de stockage Azure : [04/2015 / 05 API REST version](https://msdn.microsoft.com/library/azure/mt705637.aspx)
>
> Services de gestion du stockage Azure : [2015-05-01-aperçu](https://msdn.microsoft.com/library/azure/mt163683.aspx)
> [2015-06-15](https://msdn.microsoft.com/library/azure/mt163683.aspx)
## <a name="next-steps"></a>Étapes suivantes

-   [Introduction au stockage Azure cohérente](azure-stack-storage-overview.md)

<properties
    pageTitle="Présentation des fonctions de flux Analytique fenêtre | Microsoft Azure"
    description="En savoir plus sur les fonctions de fenêtre trois dans flux Analytique (distribution automatique, sauts, décalée)."
    keywords="distribution automatique de fenêtre, décalée de fenêtre, sauts de fenêtre"
    documentationCenter=""
    services="stream-analytics"
    authors="jeffstokes72"
    manager="jhubbard"
    editor="cgronlun"
/>

<tags
    ms.service="stream-analytics"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="data-services"
    ms.date="09/26/2016"
    ms.author="jeffstok"
/>


# <a name="introduction-to-stream-analytics-window-functions"></a>Présentation des fonctions de fenêtre Analytique de flux

En temps réel nombreux scénarios de diffusion en continu, il est nécessaire effectuer des opérations uniquement sur les données contenues dans windows temporelles. Prise en charge native pour les fonctions windowing est une fonctionnalité clé d’Azure flux Analytique qui déplace l’aiguille sur la productivité du développeur lors de la création des travaux de traitement de flux de données complexes. Flux Analytique permet aux développeurs d’utiliser windows [**distribution automatique**](https://msdn.microsoft.com/library/dn835055.aspx), [**saut**](https://msdn.microsoft.com/library/dn835041.aspx) et [**coulissante**](https://msdn.microsoft.com/library/dn835051.aspx) pour effectuer des opérations temporelles sur flux des données. Il est important de noter que toutes les opérations de [fenêtre](https://msdn.microsoft.com/library/dn835019.aspx) de sortie des résultats à la **fin** de la fenêtre. Le résultat de la fenêtre sera seul événement en fonction de la fonction d’agrégation utilisée. L’événement aura l’horodatage de la fin de la fenêtre et toutes les fonctions de fenêtre sont définies avec une longueur fixe. Enfin, il est important de noter que toutes les fonctions de fenêtre doivent être utilisées dans une clause [**GROUP BY**](https://msdn.microsoft.com/library/dn835023.aspx) .

![Concepts de flux de fonctions fenêtre Analytique](media/stream-analytics-window-functions/stream-analytics-window-functions-conceptual.png)

## <a name="tumbling-window"></a>Fenêtre qui s’écroulent

Distribution automatique fenêtre fonctions sont utilisées pour segmenter un flux de données en segments temporelles distinctes et effectuer une fonction contre eux, comme l’exemple ci-dessous. Forts d’une fenêtre qui s’écroulent sont qu’ils répéter, ne se chevauchent pas et un événement ne peut pas appartenir à plus d’une fenêtre qui s’écroulent.

![Fonctions de fenêtre Analytique flux distribution automatique d’introduction](media/stream-analytics-window-functions/stream-analytics-window-functions-tumbling-intro.png)

## <a name="hopping-window"></a>Fenêtre saut

Saut de saut de fonctions de fenêtre vers l’avant dans le temps par période fixe. Il est facile de considérer qu’elles windows qui s’écroulent qui peuvent se superposer, afin que les événements peuvent appartenir à plusieurs jeux de résultats de fenêtre saut. Pour créer une fenêtre de saut identique à un qui s’écroulent fenêtre un spécifiez simplement la taille du plafond pour correspondre à la taille de la fenêtre. 

![Fonctions de fenêtre Analytique flux saut introduction](media/stream-analytics-window-functions/stream-analytics-window-functions-hopping-intro.png)

## <a name="sliding-window"></a>Fenêtre coulissante

Fonctions de fenêtre coulissante, contrairement aux fenêtres qui s’écroulent ou saut, produisent un sortir **uniquement** lorsqu’un événement se produit. Toutes les fenêtres aura au moins un événement et la fenêtre en continu se déplace vers l’avant par un € (epsilon). Comme Windows sauts, événements peuvent appartenir à plusieurs fenêtres coulissante.

![Fonctions de fenêtre Analytique flux décalée d’introduction](media/stream-analytics-window-functions/stream-analytics-window-functions-sliding-intro.png)

## <a name="getting-help-with-window-functions"></a>Obtenir de l’aide avec les fonctions de fenêtre

Pour obtenir une aide supplémentaire, essayez de notre [forum Azure flux Analytique](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>Étapes suivantes

- [Présentation des flux de données Azure Analytique](stream-analytics-introduction.md)
- [Commencer à utiliser Azure flux Analytique](stream-analytics-get-started.md)
- [Échelle Azure flux Analytique travaux](stream-analytics-scale-jobs.md)
- [Référence du langage flux Azure Analytique requête](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Référence de l’API REST gestion Analytique flux Azure](https://msdn.microsoft.com/library/azure/dn835031.aspx)

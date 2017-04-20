<properties
    pageTitle="Ajouter le déclencheur de périodicité dans les applications logique | Microsoft Azure"
    description="Vue d’ensemble du déclencheur périodicité et comment l’utiliser avec une application Azure logique."
    services=""
    documentationCenter=""
    authors="jeffhollan"
    manager="erikre"
    editor=""
    tags="connectors"/>

<tags
   ms.service="logic-apps"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="07/18/2016"
   ms.author="jehollan"/>

# <a name="get-started-with-the-recurrence-trigger"></a>Prise en main du déclencheur périodicité

À l’aide du déclencheur périodicité, vous pouvez créer des flux de travail puissantes dans le cloud.

Par exemple, vous pouvez :

- Planifier un flux de travail pour exécuter une procédure stockée SQL chaque jour.
- Envoyer par courrier électronique un résumé de tous les tweets au sein de la semaine dernière sur certain un hashtag.

Pour commencer à utiliser le déclencheur périodicité dans une application logique, voir [créer une application logique](../app-service-logic/app-service-logic-create-a-logic-app.md).

## <a name="use-a-recurrence-trigger"></a>Utilisez un déclencheur périodicité

Un déclencheur est un événement qui peut être utilisé pour démarrer le flux de travail qui est défini dans une application logique. [En savoir plus sur les déclencheurs](connectors-overview.md).

Voici un exemple de séquence de la configuration d’un déclencheur périodicité dans une application logique :

1. Ajouter le déclencheur **périodicité** en tant que la première étape dans une application logique.
2. Renseignez les paramètres pour l’intervalle de périodicité.

L’application logique permet de démarrer une exécution après chaque intervalle de temps.

![Déclencheur HTTP](./media/connectors-native-recurrence/using-trigger.png)

## <a name="trigger-details"></a>Détails du déclencheur

Le déclencheur périodicité possède les propriétés suivantes que vous pouvez configurer.

Il déclenche une application logique après un intervalle de temps spécifié.
A * signifie qu’il s’agit d’un champ obligatoire.

|Nom d’affichage|Nom de la propriété|Description|
|---|---|---|
|Fréquence *|fréquence|The unit of time: `Second`, `Minute`, `Hour`, `Day`, or `Year`.|
|Intervalle *|intervalle|L’intervalle de la fréquence donnée pour la périodicité.|
|Fuseau horaire|fuseau horaire|Si une heure de début est fournie sans un décalage UTC, ce fuseau horaire seront utilisé.|
|Heure de début|heure de début|L’heure de début au [format ISO 8601](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations).|
<br>


## <a name="next-steps"></a>Étapes suivantes

À présent, essayez la plateforme et [créer une application logique](../app-service-logic/app-service-logic-create-a-logic-app.md). Vous pouvez Explorer d’autres connecteurs disponibles dans les applications logique en consultant notre [liste API](apis-list.md).

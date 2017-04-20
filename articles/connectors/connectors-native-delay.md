<properties
    pageTitle="Ajouter un délai dans les applications logique | Microsoft Azure"
    description="Vue d’ensemble du retard et retard-jusqu'à ce que les actions et comment les utiliser avec une application Azure logique."
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

# <a name="get-started-with-the-delay-and-delay-until-actions"></a>Prise en main du retard et retard-jusqu'à actions

À l’aide du retard et « délai-jusqu'à ce que « actions, vous pouvez effectuer des scénarios de flux de travail.

Par exemple, vous pouvez :

- Attendez un jour de la semaine pour envoyer une mise à jour d’état par courrier électronique.
- Retarder le flux de travail jusqu'à ce qu’un appel HTTP ait avant reprise d’extraire le résultat de temps.

Pour commencer à utiliser l’action retard dans une application logique, voir [créer une application logique](../app-service-logic/app-service-logic-create-a-logic-app.md).

## <a name="use-the-delay-actions"></a>Utiliser les actions de retard

Une action est une opération qui est effectuée par le flux de travail qui est défini dans une application logique. [En savoir plus sur les actions](connectors-overview.md).

Voici un exemple de séquence de l’utilisation d’une étape de retard dans une application logique :

1. Après avoir ajouté un déclencheur, cliquez sur **Nouvelle étape** pour ajouter une action.
2. Recherchez le **délai** faire apparaître les actions de retard. Dans cet exemple, nous allons sélectionner **retard**.

    ![Actions de retard](./media/connectors-native-delay/using-action-1.png)

3. Effectuer l’une des propriétés action pour configurer le délai.

    ![Configuration de la retard](./media/connectors-native-delay/using-action-2.png)

4. Cliquez sur **Enregistrer** pour publier et activer l’application logique.


## <a name="action-details"></a>Détails de l’action

Le déclencheur périodicité comporte les propriétés suivantes peuvent être configurées.

### <a name="delay-action"></a>Action de retard

Cette action diffère de l’exécution pour un intervalle de temps donné.
A * signifie qu’il s’agit d’un champ obligatoire.

|Nom d’affichage|Nom de la propriété|Description|
|---|---|---|
|Nombre *|nombre|Le nombre d’unités de temps à retarder|
|Unité *|unité|L’unité de temps : `Second`, `Minute`, `Hour`, ou`Day`|
<br>

### <a name="delay-until-action"></a>Retard-jusqu'à une action

Cette action diffère de l’exécution jusqu'à une date/heure spécifiée.
A * signifie qu’il s’agit d’un champ obligatoire.

|Nom d’affichage|Nom de la propriété|Description|
|---|---|---|
|Année *|horodatage|L’année délai jusqu’au (GMT)|
|Mois *|horodatage|Le mois délai jusqu’au (GMT)|
|Jour *|horodatage|Le premier jour de retard jusqu'à (GMT)|
<br>


## <a name="next-steps"></a>Étapes suivantes

À présent, essayez la plateforme et [créer une application logique](../app-service-logic/app-service-logic-create-a-logic-app.md). Vous pouvez Explorer d’autres connecteurs disponibles dans les applications logique en consultant notre [liste API](apis-list.md).

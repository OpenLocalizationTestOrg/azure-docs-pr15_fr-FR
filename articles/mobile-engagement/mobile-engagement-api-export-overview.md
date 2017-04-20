<properties
    pageTitle="Vue d’ensemble des API Engagement mobile exporter"
    description="Découvrez comment exporter des données brutes générées par les périphériques de vos utilisateurs pour mettre à profit dans vos propres outils"
    services="mobile-engagement"
    documentationCenter="mobile"
    authors="kpiteira"
    manager="erikre"
    editor=""/>

<tags
    ms.service="mobile-engagement"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="mobile-multiple"
    ms.workload="mobile"
    ms.date="04/26/2016"
    ms.author="kpiteira"/>

# <a name="mobile-engagement-export-api-overview"></a>Vue d’ensemble des API Engagement mobile exporter

## <a name="introduction"></a>Introduction

Dans ce document, vous apprendrez les notions fondamentales sur l’exportation de vos données brutes générées par les périphériques de vos utilisateurs pour mettre à profit dans vos propres outils.

## <a name="pre-requisites"></a>Conditions préalables

Exportation des données brutes à partir d’Engagement Mobile nécessite :

- Programme d’installation de l’API d’authentification pour pouvoir utiliser les API (voir [configuration manuelle d’authentification](mobile-engagement-api-authentication-manual.md)),
- Utilisez le [Kit de développement .net](mobile-engagement-dotnet-sdk-service-api.md), soit l’API REST
- Un compte de stockage Azure.

>[AZURE.NOTE] Nous conseille également l' excellente [Explorateur de stockage de Microsoft Azure](http://storageexplorer.com/), au moins pendant la phase de développement car il offre une interface utilisateur facile à utiliser pour interagir avec le stockage Azure.

## <a name="what-can-be-exported"></a>Que peuvent être exporté ?

Engagement mobile permet à ses utilisateurs collecter les nombreux types de données et par conséquent possède plusieurs types d’exportation adaptées à ces différents types de données.
Il existe 2 types essentielles d’exportation :

- Instantané : utilisé en général pour exporter des données qui représente un état et pour lesquelles Mobile Engagement n’a pas d’un historique. Cela inclut les balises (application-info), jetons ou évaluations de campagne push par exemple. Par conséquent ces types d’exportation ne sont pas liées à une date.
- historiques : ce type d’exportation est utilisé pour les données qui sont cumulées au fil du temps, notamment des événements et activités par exemple.

Le tableau ci-dessous décrit de manière exhaustive toutes les possibles exportations :

| Type d’exportation | Type de données | Description                                                                                                                                 |
|-------------|-----------|---------------------------------------------------------------------------------------------------------------------------------------------|
| Instantané    | Notifications Push      | Génère une exportation de commentaires campagnes de transmission sur une base par ID de périphérique/nom d’utilisateur                                                              |
| Instantané    | Balise       | Génère une exportation des balises (application-info) associés à chaque appareils                                                                       |
| Instantané    | APPAREIL    | Génère une exportation de la plupart des données sur les appareils mobiles tels que les technicals (modèle, paramètres régionaux, fuseau horaire,...), les indicateurs, la première fois Guide... |
| Instantané    | Jetons     | Génère une exportation de tous les jetons valides                                                                                                 |
| Historique  | Activité  | Génère une exportation de toutes les activités pour chaque appareils sur une période donnée                                                           |
| Historique  | Événement     | Génère une exportation de toutes les activités pour chaque appareils sur une période donnée                                                           |
| Historique  | Tâche       | Génère une exportation de toutes les tâches pour chaque appareils sur une période donnée                                                                 |
| Historique  | Erreur     | Génère une exportation de toutes les erreurs pour chaque appareils sur une période donnée                                                               |

## <a name="how-does-it-work"></a>Comment cela fonctionne-t-il ?

Exportations sont longues en cours d’exécution des tâches qui peuvent produire des fichiers de données volumineux. Pour cette raison, ils ne peuvent pas être appelés pour renvoyer immédiatement un fichier à télécharger.
Pour exporter des données à partir d’Engagement Mobile, vous devrez créer une **Tâche d’exportation** via l’API dans laquelle vous spécifiez généralement :

- Le type d’exportation (capture instantanée ou historique)
- Le type de données
- Le **Conteneur de stockage Azure** (y compris un as valide avec un accès en écriture) où le résultat de l’exportation sera écrit.

Veuillez noter que peut prendre quelques minutes pour votre projet pour qu’il démarre, et il peut exécuté de quelques secondes pour des applications à plusieurs heures pour les applications avec un grand nombre d’utilisateurs ou d’activité.

Une fois que le travail est créé, il est possible d’en vérifier l’état pour voir si elle est toujours en cours d’exécution ou si elle est terminée.

Une fois que le travail est a réussi, le fichier de données qui en résulte est disponible sur le conteneur de stockage fourni.

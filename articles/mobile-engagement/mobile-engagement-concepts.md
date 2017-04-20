<properties
    pageTitle="Concepts Engagement mobile | Microsoft Azure"
    description="Concepts Mobile Engagement Azure"
    services="mobile-engagement"
    documentationCenter="mobile"
    authors="piyushjo"
    manager="dwrede"
    editor="" />

<tags
    ms.service="mobile-engagement"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-android"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="08/19/2016"
    ms.author="piyushjo" />

# <a name="azure-mobile-engagement-concepts"></a>Concepts Mobile Engagement Azure

Mobile Engagement définit quelques concepts communs à toutes les plates-formes prises en charge. Cet article décrit brièvement ces concepts.

Cet article constitue un bon point de départ si vous débutez avec Engagement Mobile. Assurez-vous également de lire la documentation spécifique à la plateforme que vous utilisez, comme il affiner les concepts décrits dans cet article avec d’autres détails et exemples ainsi que les limitations possibles.

## <a name="devices-and-users"></a>Périphériques et des utilisateurs
Engagement mobile identifie les utilisateurs en générant un identificateur unique pour chaque appareil. Cet identifiant est appelé l’identificateur de périphérique (ou `deviceid`). Il est généré de façon à ce que toutes les applications exécution du même périphérique partagent le même identificateur d’appareil.

Implicitement, cela signifie que Mobile Engagement considère comme un périphérique doit appartenir à un seul utilisateur, et par conséquent, les utilisateurs et périphériques sont des concepts équivalents.

## <a name="sessions-and-activities"></a>Sessions et activités
Une session est une utilisation de l’application effectuée par un utilisateur, l’heure de l’utilisateur commence à l’utiliser, jusqu'à ce que les taquets de l’utilisateur.

Une activité est une utilisation d’un article sous-adresse de l’application effectuée par un utilisateur (il s’agit généralement d’un écran, mais il peut s’agir approprié à l’application).

Un utilisateur peut effectuer uniquement une activité à la fois.

Une activité est identifiée par un nom (limité à 64 caractères) et peut éventuellement incorporer des données supplémentaires (dans la limite de 1 024 octets).

Sessions sont automatiquement calculées à partir de la séquence d’activités effectuées par les utilisateurs. Démarrage d’une session lorsque l’utilisateur démarre son activité première et s’arrête lorsqu’il termine à sa dernière activité. Cela signifie qu’une session n’a pas besoin explicitement démarrer ou arrêter. À la place, les activités sont explicitement démarrées ou arrêtées. Si aucune activité n’est signalée, aucune session n’est signalée.

## <a name="events"></a>Événements
Événements sont utilisés pour signaler instantanées actions (par exemple, le bouton enfoncé ou articles lus par les utilisateurs).

Un événement peut être lié à la session active, à une tâche en cours d’exécution, ou il peut être un événement autonome.

Un événement est identifié par un nom (limité à 64 caractères) et peut éventuellement incorporer des données supplémentaires (dans la limite de 1 024 octets).

## <a name="error"></a>Erreur
Erreurs sont utilisées pour signaler des problèmes détectés correctement par l’application (par exemple, les actions de l’utilisateur incorrecte ou échecs des appels API).

Une erreur peut être liée à la session active, à une tâche en cours d’exécution, ou elle peut être une erreur autonome.

Une erreur est identifiée par un nom (limité à 64 caractères) et peut éventuellement incorporer des données supplémentaires (dans la limite de 1 024 octets).

## <a name="job"></a>Tâche
Les travaux sont utilisés pour signaler des actions ayant une durée (à l’instar durée d’appels d’API, afficher l’heure du annonces, la durée des tâches en arrière-plan ou la durée des actions de l’utilisateur).

Une tâche n’est pas associée à une session, car une tâche peut être effectuée à l’arrière-plan, sans interaction de l’utilisateur.

Une tâche est identifiée par un nom (limité à 64 caractères) et peut éventuellement incorporer des données supplémentaires (dans la limite de 1 024 octets).

## <a name="crash"></a>Blocage
Se bloque est automatiquement émis par le Kit de développement Mobile Engagement pour signaler les échecs d’application où problèmes ne détectés ne pas l’application rendent incident.

## <a name="application-information"></a>Informations sur l’application
Informations sur l’application (ou informations application) sont utilisée pour ajouter des balises à des utilisateurs, c'est-à-dire à associer des données aux utilisateurs d’une application (cela revient à cookies web, sauf que les informations de l’application sont stockée sur le côté serveur sur la plateforme Azure Mobile Engagement).

Informations de l’application peuvent être inscrit à l’aide de l’API Mobile Engagement SDK ou à l’aide de la plateforme Mobile Engagement API Device.

Informations de l’application sont une paire clé/valeur associée à un appareil. La clé est le nom de l’information sur l’application (limité à lettres ASCII 64 [a-zA-Z], [0 à 9] nombres et des traits de soulignement [_]). La valeur (limitée à 1024 caractères) peut être toute chaîne, le nombre entier, la date (AAAA-MM-JJ) ou la valeur booléenne (vrai ou faux).

Un nombre quelconque d’informations application peut être associé à un appareil, dans les limites définies par les termes de tarification Mobile Engagement. Pour une clé donnée, Engagement Mobile uniquement effectue le suivi de la dernière valeur définie (aucun historique). Définition ou modification de la valeur des informations d’application force la création d’Engagement Mobile à réévaluez audience critères définis sur cette information application (le cas échéant), ce qui signifie que ces informations application peut être utilisé pour déclencher push en temps réel.

## <a name="extra-data"></a>Données supplémentaires
Données supplémentaires (ou suppléments) sont certaines données arbitraires qui peuvent être liées aux événements, les erreurs, les activités et les tâches.

Suppléments sont structurés de la même façon pour objets JSON : dont ils sont constitués d’un arbre de paires clé/valeur. Clés sont limitées à 64 lettres ASCII [a-zA-Z], [0 à 9] nombres et des traits de soulignement [_]) et la taille totale de bonus est limitée à 1024 caractères (une fois codés au format JSON par le Kit de développement Mobile Engagement).

L’arborescence de paires clé/valeur entière est stocké comme un objet JSON. Toutefois, seul le premier niveau de clés/valeurs est décomposé soit directement accessible à certaines fonctions avancées comme les Segments (par exemple, vous pouvez facilement définir un segment appelé « SciFi ventilateurs » qui est constituée de tous les utilisateurs ayant envoyé au moins 10 fois l’événement nommé « content_viewed » avec la content_type clé supplémentaire de « » de la valeur « scifi « le mois dernier). Il est donc vivement recommandé pour envoyer uniquement suppléments faite des listes simples de paires clé/valeur en utilisant les valeurs scalaires (par exemple, des chaînes, dates, entiers ou booléen).

## <a name="next-steps"></a>Étapes suivantes

- [Vue d’ensemble du Kit de développement Windows universel pour Azure Mobile Engagement](mobile-engagement-windows-store-sdk-overview.md)
- [Vue d’ensemble du Kit de développement Windows Phone Silverlight pour Azure Mobile Engagement](mobile-engagement-windows-phone-sdk-overview.md)
- [Kit de développement pour Azure Mobile Engagement iOS](mobile-engagement-ios-sdk-overview.md)
- [Kit de développement Android pour Azure Engagement Mobile](mobile-engagement-android-sdk-overview.md)

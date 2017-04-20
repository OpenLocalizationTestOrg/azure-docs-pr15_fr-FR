<properties
   pageTitle="Extraction de données SQL dans Azure événement Hubs | Microsoft Azure"
   description="Vue d’ensemble des événements centraux importer à partir d’exemple SQL"
   services="event-hubs"
   documentationCenter="na"
   authors="spyrossak"
   manager="timlt"
   editor=""/>

<tags 
   ms.service="event-hubs"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/25/2016"
   ms.author="spyros;sethm" />

# <a name="pulling-data-from-sql-into-an-azure-event-hub"></a>Extraient des données à partir de SQL à un concentrateur événement Azure

Une architecture de type d’une application pour le traitement des données en temps réel implique tout d’abord le pousser à un concentrateur événement Azure. Il peut être un scénario IoT, telles que le contrôle du trafic sur différents s’étend d’un autoroutes, ou un scénario de jeu, analyser les actions d’un atouts de frenzied participant ou un scénario d’entreprise, telles que le contrôle construction initiaux à l’emménagement. Dans ce cas, le producteur de données est généralement externes objets produire des données de série chronologique dont vous avez besoin de collecter, analyser, stocker et agir et vous pouvez investi beaucoup d’efforts en construction l’infrastructure pour les processus. Que pouvez-vous faire si vous souhaitez importer des données d’un élément comme une base de données au lieu d’une source de données de diffusion en continu et utiliser conjointement avec vos autres données de diffusion en continu ? Prenons le cas où vous souhaitez utiliser Azure flux Analytique, Explorer des données à distance (RDX) ou un autre outil pour analyser et agir sur lentement modification des données à partir de Microsoft Dynamics AX ou un système de gestion des installations personnalisé ? Pour résoudre ce problème, nous avons écrit et un échantillon cloud petite que vous pouvez modifier et déployer qui doit extraire les données d’une table SQL transmission à un concentrateur événement Azure à utiliser comme une entrée dans vos applications analytiques en aval source ouverte. Réalisez qu’il s’agit d’un scénario rare, à l’inverse de ce que vous le feriez avec un concentrateur de l’événement. Toutefois, si vous trouvez vous-même dans le cas où il s’agit de ce que vous devez faire, vous pouvez trouver le code dans la galerie d’exemples de Azure, [ici](https://azure.microsoft.com/documentation/samples/event-hubs-dotnet-import-from-sql/).  

Notez que le code dans cet exemple est qu’un échantillon. Il s’agit **pas** destiné à être une application de production et aucune tentative n’ont été apportées aux adapter à utiliser dans un environnement de ce type, c’est stricly un soi-même, destinés aux développeurs, un exemple. Vous devez examiner toutes sortes de sécurité, les performances, les fonctionnalités et allocation des coûts des facteurs avant de s’installer sur une architecture de bout en bout.

## <a name="application-structure"></a>Structure de l’application

L’application est écrite en c#, et le fichier readme.md dans l’échantillon contient toutes les informations que nécessaires pour modifier, créer et publier l’application. Les sections suivantes fournissent une vue d’ensemble détaillée de rôle de l’application.

Nous allons commencer en partant du principe que vous avez accès à une table SQL Azure. Vous devez également avoir configuré un concentrateur événement Azure et connaître la connexion chaîne nécessaire pour y accéder.

Démarrage de la solution SqlToEventHub, il lit un fichier de configuration (App.config) pour obtenir un nombre d’éléments, comme indiqué dans le fichier readme.md. Il s’agit des suffisamment explicites, telles que le nom de la table de données, etc., et il est inutile de rehash les explications ici. 

Une fois que l’application a lire le fichier de configuration, il accède dans une boucle, lecture de la table SQL reçue des enregistrements pour le hub de l’événement, puis en attente pour un intervalle de mise en veille définies par l’utilisateur avant d’effectuer tout à nouveau. Quelques points sont à noter :

1. L’application est basée sur l’hypothèse que la table SQL est mis à jour par un processus externe, et que vous voulez envoyer tous les et uniquement les mises à jour à un concentrateur de l’événement.
2. La table SQL doit avoir un champ qui comporte un nombre unique et croissant, par exemple, un numéro d’enregistrement. Cela peut être aussi simple qu’un champ nommé « Id », ou une autre valeur qui est incrémenté en tant que tout ce qui met à jour cette base de données ajoute enregistrements tels que « Creation_time » ou « Sequence_number ». L’application de commentaires et stocke la valeur de ce champ dans chaque itération. Dans chaque passage suivante dans la boucle, l’application de recherche pour l’essentiel dans la table pour tous les enregistrements où la valeur de ce champ est supérieure à la valeur il unes la dernière fois la boucle. Nous allons appeler cette dernière valeur le décalage de « ».
3. L’application crée une table « TableOffsets » lors de son démarrage, pour stocker le décalage. La table est créée avec la requête « CreateOffsetTableQuery » définie dans le fichier de configuration. 
4. Il existe plusieurs requêtes utilisées pour l’utilisation de la table de décalage, définie dans le fichier de configuration en tant que « OffsetQuery », « UpdateOffsetQuery » et « InsertOffsetQuery ». Vous ne devez pas modifier celles-ci.
5. Enfin, la requête « requête » de données définies dans le fichier de configuration est la requête qui sera exécutée pour extraire les enregistrements à partir de votre table SQL. Il n’est actuellement limité aux enregistrements haut 1 000 dans chaque étape de la boucle à des fins d’optimisation - si, par exemple, 25 000 enregistrements ont été ajoutés à la base de données depuis la dernière requête, cela peut prendre un certain temps pour exécuter la requête. En limitant la requête à 1 000 enregistrements chaque fois, les requêtes sont beaucoup plus rapides. Sélection de la partie supérieure de 1 000 simple pousse successifs par lots de 1 000 enregistrements vers le hub de l’événement.    

## <a name="next-steps"></a>Étapes suivantes

Pour déployer la solution, cloner ou télécharger l’application SqlToEventHub, modifiez le fichier App.config, créez-le et enfin le publier. Une fois que vous avez publié l’application, vous pouvez voir s’exécuter dans le portail classique Azure sous Services Cloud et surveiller les événements entrants sur votre plateforme d’événement. Notez que la fréquence sera déterminée par deux éléments : la fréquence des mises à jour à la table SQL et l’intervalle de mise en veille que vous avez spécifié dans le fichier de configuration pour l’application.
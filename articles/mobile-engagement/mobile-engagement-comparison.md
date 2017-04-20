<properties
    pageTitle="Comparaison d’Azure Mobile Engagement avec d’autres services Azure similaires"
    description="Comparaison d’Azure Mobile Engagement avec d’autres services Azure similaires - HockeyApp, AppInsights, Hubs de Notification"
    services="mobile-engagement"
    documentationCenter="mobile" 
    authors="piyushjo" 
    manager="erikre" 
    editor="" />

<tags
    ms.service="mobile-engagement"
    ms.workload="mobile"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/19/2016"
    ms.author="piyushjo" />

# <a name="comparing-azure-mobile-engagement-with-other-similar-azure-services"></a>Comparaison d’Azure Mobile Engagement avec d’autres services Azure similaires

La liste des services proposés par Microsoft Azure se jamais développe et parfois vous demanderez peut-être comment est Azure Mobile Engagement différent de celui autre service de ce que vous venez de lire ou entendez parler. Cet article tente d’effacer la confusion et dirige vous permet de choisir Azure Mobile Engagement lorsqu’elle est la plus adaptée à votre activité. 
 
Azure Engagement Mobile est un service ciblé spécifiquement **pour marketing/CMOs numériques** , mais elles peut être utilisé par n’importe quel **propriétaire de l’application mobile ou publisher** qui souhaite améliorer la rétention, l’utilisation et la monétisation de leurs applications mobiles. 

*Il est une logiciel en tant que service (SaaS) utilisateur engagement plate-forme permet d’analyse des données par l’effort dans l’utilisation de l’application, segmentation des utilisateurs en temps réel et les notifications push en fonction du contexte prenant en charge et dans l’application de messagerie.* 

Décomposer cette définition, nous avons créé les caractéristiques clés suivantes qui met également en évidence sa proposition de valeur unique :

1.  **Notifications de transmission en fonction du contexte prenant en charge et dans l’application de messagerie**
        
    Il s’agit le focus core du produit - effectuer les notifications push ciblée et personnalisées. Et pour ce faire, nous collecter les données analytique comportement riche. 

2.  **Analyse des données par l’effort dans l’utilisation de l’application**

    Nous fournissons la plateforme croisée SDK pour recueillir les signatures analytique sur les utilisateurs de l’application. Remarque l’analytique comportement terme (plutôt que par analytique performances), car nous concentrer sur la façon dont les utilisateurs d’application sont à l’aide de l’application. Nous collecter des données d’analytique performances de base sur les erreurs, se bloque etc. mais c'est-à-dire pas le focus core du produit. 

3.  **Segmentation des utilisateurs en temps réel**

    Une fois que vous avez recueilli des données analytique comportement utilisateurs de l’application, nous vous permettent de segments votre audience en fonction des paramètres différents et des données recueillies à vous permettent de lancer des campagnes ciblées push. 

4.  **Logiciel-as-a-service (SaaS) :**

    Nous avons un portail séparé à partir du portail de gestion Azure qui est optimisé pour interagir et afficher enrichi analytique comportement sur les utilisateurs de l’application et exécuter push campagnes marketing. Le produit est conçu pour vous lancer dans aucun temps !   
 
Avec cet ensemble de différencier en main, voici comment nous comparer avec les autres offres Azure apparemment similaires - approche pour définir quel produit convient le mieux basée sur la note que l’article ne fait pas une comparaison des fonctionnalités de niveau mais prend un scénario plus :
 
1.  [HockeyApp](https://azure.microsoft.com/services/hockeyapp/) est solution DevOps mobile de Microsoft. Avec, vous pouvez distribuer les versions préliminaires à testeurs bêta, collecter les données de blocage et obtenir des commentaires de l’utilisateur. Il est également Visual Studio Team Services permettant l’intégration d’élément de travail et les déploiements créer facilement. 
    
    Le focus ici est DevOps et collecte de données analytique performances sur les applications mobiles. Vous risquez intégrer les deux HockeyApps et Engagement Mobile dans votre application et qui ne sera pas inhabituel, même s’il existe un certain chevauchement dans les données recueillies, principalement des produits est différent, et ils vous aident à atteindre les objectifs différents pour vous.  

2.  [Analyse de l’application](../application-insights/app-insights-overview.md) Si votre application mobile comporte un côté serveur, puis vous utiliserez Application perspectives pour contrôler le côté serveur web de votre application, mais pour les applications mobiles côté client, vous devez utiliser HockeyApp. 

3.  [Notification Hubs](https://azure.microsoft.com/services/notification-hubs/) fournit un service lightweight dans le sens que vous n’avez pas besoin d’un kit de développement intégré dans l’application mobile et vous pouvez disposent du contrôle total de votre application mobile, et il autorise l’envoi des notifications de transmission avec les fonctionnalités de liaison de base. Il s’agit de n’importe quel propriétaire de l’application qui respecte inférieure à propos du ciblage et plus à propos d’envoi/communiquer des informations instantanément à leurs utilisateurs application (diffusion à une grande population). 

    Notez ici le focus sur l’envoi d’obtenir éblouissantes notifications rapides avec la fonctionnalité de segmentation de base. 

Jetons certains scénarios :

1.  Tim fait partie de l’équipe marketing numérique sur store Adventure Works qui publie des applications mobiles pour les utilisateurs. Objectif Cooke est de s’assurer que les utilisateurs qui téléchargent leurs applications mobiles continuent à l’utiliser et fréquemment. Ce augmente pas seulement une marque joindre avec les utilisateurs de l’application, mais également monétisation augmente lorsque les utilisateurs de l’application effectuent des achats à l’aide de l’application mobile. Pendant ce Tim devrez envoyer des notifications ciblées pour les utilisateurs de l’application, lequel ils s’avérer, encouragez-les pour ouvrir l’application et y revenir souvent. Il s’agit de l’endroit où Tim utiles Mobile Engagement. 

2.  JoAnn fait partie de l’équipe de développement des applications mobiles Adventure Works et recherche d’un produit de consigner les informations sur les incidents, exceptions, et d’obtenir de télémétrie de performances d’une application. Il s’agit de l’endroit où Joann utiles HockeyApp. JoAnn pu intégrer à la fois HockeyApp pour son développeur en fonction des scénarios et Azure Mobile Engagement pour le Tim dans l’application même de tirer le meilleur des deux. 

3.  Robin fait partie de l’équipe de développement des applications mobiles au réseau de Contoso News et tout qu'elle souhaite consiste à envoyer à transmettre les alertes de news à tous les utilisateurs sans quantité segmentation dès le déclenchement de l’alerte de news. Il s’agit de l’endroit où Robin utiles Hubs de Notification. Dans ce scénario, il est possible toutefois que l’application mobile arrivera à échéance, il est nécessaire qu’à suivre quantité segmentation plus riche et obtenir des détails sur le comportement de l’utilisateur de l’application. Pour l’instant, Robin doivent évaluer Azure Mobile Engagement. 
 
En résumé, l’objectif d’Engagement Mobile n’est pas tout simplement pour collecter analytique - il n’est pas « encore un autre produit Analytique de Microsoft ». Il est sur l’envoi des notifications de transmission ciblée et pour le ciblage, nous collecter les données de comportement analytique, mais le focus reste sur l’envoi de notifications de transmission qui les plus représentatives pour les utilisateurs de l’application afin qu’il n’est pas fourni comme courrier indésirable. 

Pour plus d’informations - Examinons cette [courte vidéo](mobile-engagement-overview.md) sur Engagement Mobile en bref. 


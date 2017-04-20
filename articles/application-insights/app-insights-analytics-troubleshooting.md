<properties 
    pageTitle="Résolution des problèmes Analytique - l’outil recherche puissante de perspectives Application | Microsoft Azure" 
    description="Problèmes avec analytique Insights Application ? Commencez ici. " 
    services="application-insights" 
    documentationCenter=""
    authors="alancameronwills" 
    manager="douge"/>

<tags 
    ms.service="application-insights" 
    ms.workload="tbd" 
    ms.tgt_pltfrm="ibiza" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="07/11/2016" 
    ms.author="awills"/>


# <a name="troubleshoot-analytics-in-application-insights"></a>Résoudre les problèmes d’Analytique dans perspectives d’Application


Problèmes de synchronisation [d’Application Insights Analytique](app-insights-analytics.md)? Commencez ici. Analytique est l’outil recherche puissante de Visual Studio Application perspectives.



## <a name="limits"></a>Limites

* À l’heure actuelle, les résultats de la requête sont limités à juste plus d’une semaine de données passées.
* Nous tester sur les navigateurs : dernières éditions de Chrome, Edge et Internet Explorer.


## <a name="known-incompatible-browser-extensions"></a>Extensions de navigateur compatible connus

* Ghostery

Désactiver l’extension ou utilisez un autre navigateur.


##<a name="e-a"></a>« Erreur inattendue »

![Écran de l’erreur inattendue](./media/app-insights-analytics-troubleshooting/010.png)

Erreur interne s’est produite lors de l’exécution du portail – exception non gérée.

* Nettoyer le cache du navigateur. 

## <a name="e-b"></a>403... Veuillez réessayer recharger

![403... Veuillez réessayer recharger](./media/app-insights-analytics-troubleshooting/020.png)

Une authentification liés erreur s’est produite (lors de l’authentification ou pendant la génération de jeton d’accès). Le portail ne peut-être aucun moyen de récupérer sans modifier les paramètres du navigateur.

* Vérifiez [les cookies tiers sont activées](#cookies) dans le navigateur. 


## <a name="authentication"></a>403... vérifier la zone de sécurité

![403.. zone de sécurité .verify](./media/app-insights-analytics-troubleshooting/030.png)

Une authentification liés erreur s’est produite (lors de l’authentification ou pendant la génération de jeton d’accès). Le portail ne peut-être aucun moyen de récupérer sans modifier les paramètres du navigateur.

1. Vérifiez [les cookies tiers sont activées](#cookies) dans le navigateur. 

2. Avez-vous utilisé un favori, le signet ou le lien enregistré pour ouvrir le portail Analytique ? Si vous êtes connectés avec différentes informations d’identification que vous avez utilisé lorsque vous avez enregistré le lien ?

2. Essayez d’utiliser une fenêtre de navigateur privé/fenêtre (après avoir fermé toutes les fenêtres de ces). Vous devrez fournir vos informations d’identification. 

2. Ouvrir une autre fenêtre de navigateur (ordinaire) et accédez à [Azure](https://portal.azure.com). Se déconnecter. Ouvrez ensuite votre lien et vous connecter avec les informations d’identification correctes.

2. Utilisateurs de bord et Internet Explorer peuvent également recevoir cette erreur lorsque les paramètres de zone de confiance ne sont pas pris en charge.

    Vérifiez [portail Analytique](https://analytics.applicationinsights.io) et [Azure Active Directory portail](https://portal.azure.com) se trouvent dans la zone de sécurité même :

 * Dans Internet Explorer, ouvrez **Options Internet**, **sécurité**, **les sites de confiance**, **Sites**:

    ![Boîte de dialogue Options Internet, ajout d’un site aux Sites de confiance](./media/app-insights-analytics-troubleshooting/033.png)

    Dans la liste des sites Web, si une des URL suivantes sont incluse, vérifiez que les autres colonnes sont également inclus :

    https://Analytics.applicationinsights.IO<br/>
   https://login.microsoftonline.com<br/>
   https://login.Windows.NET


## <a name="e-d"></a>404... Ressource introuvable

![... 404 Ressource introuvable](./media/app-insights-analytics-troubleshooting/040.png)

Ressource de l’application a été supprimée à partir de l’analyse des applications et n’est plus disponible. Cela peut arriver si vous avez enregistré l’URL dans la page Analytique.


## <a name="e-e"></a>403... Aucune autorisation

![403... non autorisé](./media/app-insights-analytics-troubleshooting/050.png)

Vous n’êtes pas autorisé à ouvrir cette application dans Analytique.

* Avez-vous obtenu le lien à partir d’une autre personne ? Demandez-leur pour vous assurer que vous êtes dans les [lecteurs ou les collaborateurs pour ce groupe de ressources](app-insights-resources-roles-access-control.md).
* Vous n’avez enregistré le lien à l’aide des informations d’identification différentes ? Ouvrez le [portail Azure](https://portal.azure.com), déconnectez-vous, puis essayez ce lien là encore, fournir les informations d’identification correctes.

## <a name="html-storage"></a>403... Stockage HTML5

Notre portail utilise sessionStorage et localStorage HTML5.

* Chrome : Paramètres de confidentialité, les paramètres de content.
* Internet Explorer : Options Internet, onglet Options avancées, sécurité, activer le stockage DOM


![403... essayez d’activer le stockage HTML5](./media/app-insights-analytics-troubleshooting/060.png)

## <a name="e-g"></a>404... Abonnement introuvable


![404... Abonnement introuvable](./media/app-insights-analytics-troubleshooting/070.png)

L’URL n’est pas valide. 

* Ouvrez la ressource application portail [d’Analyse de l’Application](https://portal.azure.com). Utilisez le bouton Analytique.

## <a name="e-h"></a>404... page n’existe pas

![404... Page n’existe pas](./media/app-insights-analytics-troubleshooting/080.png)

L’URL n’est pas valide.

* Ouvrez la ressource application portail [d’Analyse de l’Application](https://portal.azure.com). Utilisez le bouton Analytique.

## <a name="cookies"></a>Activer les cookies tiers

  Découvrez [comment désactiver les cookies tiers](http://www.digitalcitizen.life/how-disable-third-party-cookies-all-major-browsers), mais notez que nous devons **Activer** les.

## <a name="e-x"></a>Si le problème persiste    

[Nous contacter](app-insights-get-dev-support.md).
 
[AZURE.INCLUDE [app-insights-analytics-footer](../../includes/app-insights-analytics-footer.md)]


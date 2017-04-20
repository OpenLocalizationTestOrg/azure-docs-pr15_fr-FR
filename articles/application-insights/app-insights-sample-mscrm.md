<properties 
    pageTitle="Procédure pas à pas : Surveiller Microsoft Dynamics CRM avec des aperçus d’Application" 
    description="Obtenez télémétrie de Microsoft Dynamics CRM Online à l’aide d’analyse de l’Application. Procédure pas à pas de l’installation, l’obtention de données, de visualisation et d’exportation." 
    services="application-insights" 
    documentationCenter=""
    authors="mazharmicrosoft" 
    manager="douge"/>

<tags 
    ms.service="application-insights" 
    ms.workload="tbd" 
    ms.tgt_pltfrm="ibiza" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="11/17/2015" 
    ms.author="awills"/>
 
# <a name="walkthrough-enabling-telemetry-for-microsoft-dynamics-crm-online-using-application-insights"></a>Procédure pas à pas : Activation de télémétrie pour Microsoft Dynamics CRM Online à l’aide de perspectives d’Application

Cet article vous explique comment obtenir des données de télémétrie depuis [Microsoft Dynamics CRM Online](https://www.dynamics.com/) à l’aide de [Visual Studio Application perspectives](https://azure.microsoft.com/services/application-insights/). Nous allons le processus complet de l’ajout de script d’analyse des applications à votre application, capturer des données et visualisation des données.

>[AZURE.NOTE] [Parcourir la solution de l’échantillon](https://dynamicsandappinsights.codeplex.com/).

## <a name="add-application-insights-to-new-or-existing-crm-online-instance"></a>Ajouter des perspectives d’Application à existante ou nouvelle instance CRM Online 

Pour analyser votre application, vous ajoutez une Application Insights SDK à votre application. Le Kit de développement envoie télémétrie au [portail d’analyse de l’Application](https://portal.azure.com), où vous pouvez utiliser notre puissantes analyses et outils de diagnostic ou exporter les données vers le stockage.

### <a name="create-an-application-insights-resource-in-azure"></a>Création d’une ressource d’Application Insights dans Azure

1. Obtenir [un compte dans Microsoft Azure](http://azure.com/pricing). 
2. Se connecter au [portail Azure](https://portal.azure.com) et ajouter une nouvelle ressource Application perspectives. Il s’agit de l’endroit où vos données seront traitées et affichées.

    ![Cliquez sur +, Services pour les développeurs, Application perspectives.](./media/app-insights-sample-mscrm/01.png)

    Sélectionnez ASP.NET en tant que le type d’application.

3. Ouvrez l’onglet Quick Start et ouvrez le script de code.

    ![](./media/app-insights-sample-mscrm/03.png)

**Laissez la page de codes ouverte** pendant que vous effectuez la prochaine étape dans une autre fenêtre de navigateur. Vous devez le code plus rapidement. 

### <a name="create-a-javascript-web-resource-in-microsoft-dynamics-crm"></a>Créer une ressource web JavaScript dans Microsoft Dynamics CRM

1. Ouvrez votre instance CRM Online et connectez-vous en tant qu’administrateur.
2. Ouvrir Microsoft Dynamics CRM paramètres, les personnalisations, personnalisez le système

    ![](./media/app-insights-sample-mscrm/04.png)
    
    ![](./media/app-insights-sample-mscrm/05.png)


    ![](./media/app-insights-sample-mscrm/06.png)

3. Créez une ressource JavaScript.

    ![](./media/app-insights-sample-mscrm/07.png)

    Attribuez un nom, sélectionnez **Script (JScript)** et ouvrez l’éditeur de texte.

    ![](./media/app-insights-sample-mscrm/08.png)
    
4. Copiez le code d’analyse de l’Application. Lors de la copie veillez à ignorer les balises de script. Désigner en dessous de capture d’écran :

    ![](./media/app-insights-sample-mscrm/09.png)

    Le code inclut la clé d’instrumentation qui identifie votre ressource perspectives d’Application.

5. Enregistrer et publier.

    ![](./media/app-insights-sample-mscrm/10.png)

### <a name="instrument-forms"></a>Formulaires instrument

1. Dans Microsoft CRM Online, ouvrez le formulaire compte

    ![](./media/app-insights-sample-mscrm/11.png)

2. Ouvrez le formulaire de propriétés

    ![](./media/app-insights-sample-mscrm/12.png)

3. Ajouter la ressource web JavaScript que vous avez créé

    ![](./media/app-insights-sample-mscrm/13.png)

    ![](./media/app-insights-sample-mscrm/14.png)

4. Enregistrez et publiez vos personnalisations de formulaire.


## <a name="metrics-captured"></a>Métrique capturé

Vous avez maintenant configuré la capture de télémétrie pour le formulaire. Chaque fois qu’elle est utilisée, données recevrez votre ressource Application perspectives.

Voici quelques exemples des données que vous voyez.

#### <a name="application-health"></a>Santé des applications

![](./media/app-insights-sample-mscrm/15.png)

![](./media/app-insights-sample-mscrm/16.png)

Exceptions de navigateur :

![](./media/app-insights-sample-mscrm/17.png)

Cliquez sur le graphique pour obtenir plus en détail :

![](./media/app-insights-sample-mscrm/18.png)

#### <a name="usage"></a>Utilisation

![](./media/app-insights-sample-mscrm/19.png)

![](./media/app-insights-sample-mscrm/20.png)

![](./media/app-insights-sample-mscrm/21.png)

#### <a name="browsers"></a>Navigateurs

![](./media/app-insights-sample-mscrm/22.png)

![](./media/app-insights-sample-mscrm/23.png)

#### <a name="geolocation"></a>GÉOLOCALISATION

![](./media/app-insights-sample-mscrm/24.png)

![](./media/app-insights-sample-mscrm/25.png)

#### <a name="inside-page-view-request"></a>Afficher la demande de page à l’intérieur

![](./media/app-insights-sample-mscrm/26.png)

![](./media/app-insights-sample-mscrm/27.png)

![](./media/app-insights-sample-mscrm/28.png)

![](./media/app-insights-sample-mscrm/29.png)

![](./media/app-insights-sample-mscrm/30.png)

## <a name="sample-code"></a>Exemples de code

[Recherchez l’exemple de code](https://dynamicsandappinsights.codeplex.com/).

## <a name="power-bi"></a>Power BI

Vous pouvez effectuer une analyse plus approfondie même si vous [exportez les données à Microsoft Power BI](app-insights-export-power-bi.md).

## <a name="sample-microsoft-dynamics-crm-solution"></a>Solution d’exemple Microsoft Dynamics CRM

[Voici l’exemple de solution implémentée dans Microsoft Dynamics CRM] (https://dynamicsandappinsights.codeplex.com/).

## <a name="learn-more"></a>Pour en savoir plus

* [Quelles sont les perspectives d’Application ?](app-insights-overview.md)
* [Application perspectives pour les pages web](app-insights-javascript.md)
* [Autres exemples et des procédures pas à pas](app-insights-code-samples.md)

 

<properties
   pageTitle="Authentification et autorisation avec Power BI incorporé"
   description="Authentification et autorisation avec Power BI incorporé"
   services="power-bi-embedded"
   documentationCenter=""
   authors="guyinacube"
   manager="erikre"
   editor=""
   tags=""/>
<tags
   ms.service="power-bi-embedded"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="powerbi"
   ms.date="10/04/2016"
   ms.author="asaxton"/>

# <a name="authenticating-and-authorizing-with-power-bi-embedded"></a>Authentification et autorisation avec Power BI incorporé

Le service Power BI incorporé utilise des **clés** et des **Jetons de l’application** pour l’authentification et d’autorisation, au lieu de l’authentification de l’utilisateur final explicites. Dans ce modèle, votre application gère l’authentification et autorisation pour vos utilisateurs finaux. Le cas échéant, votre application crée et envoie les jetons d’application qui indique à notre service pour rendre le rapport demandé. Ce modèle ne nécessite pas votre application à utiliser Azure Active Directory pour l’authentification des utilisateurs et l’autorisation, bien que vous pouvez toujours.

## <a name="two-ways-to-authenticate"></a>Deux manières de s’authentifier

**Clé** - vous pouvez utiliser les touches pour tous les appels de l’API REST incorporé Power BI. Les touches sont accessibles dans le **portail Azure** en cliquant sur **tous les paramètres** , puis sur **les touches d’accès**. Toujours traiter votre clé comme s’il s’agissait d’un mot de passe. Ces touches disposer des autorisations pour appeler n’importe quel API REST sur une collection de sites espace de travail particulier.

Pour utiliser une clé à un appel reste, ajoutez l’en-tête d’autorisation suivant :            

    Authorization: AppKey {your key}

**Jeton d’application** - application jetons sont utilisés pour toutes les demandes d’incorporation. Ils sont conçus pour être exécuté côté client, afin qu’ils vous limités à un seul rapport et il est recommandé de définir un délai d’expiration.

Application jetons sont un JWT (jeton Web JSON) qui est signé par l’une de vos clés.

Votre jeton d’application peut contenir les affirmations suivantes :

| Réclamer      | Description        |
|--------------|------------|
| **veur**      | La version du jeton de l’application. 0.2.0 est la version actuelle.       |
| **AUD**      | Le destinataire prévu du jeton. Pour utiliser Power BI incorporé : « https://analysis.windows.net/powerbi/api ».  |
| **ISS**      |  Chaîne indiquant l’application qui a émis le jeton.    |
| **type**     | Le type de jeton d’application en cours de création. Active le seul type pris en charge est **incorporer**.   |
| **Windows Connect Now**      | Nom de collection de sites espace de travail le jeton est envoyée.  |
| **travail**      | ID de l’espace de travail le jeton est envoyée.  |
| **supprimer**      | ID de l’état du jeton est envoyée.     |
| **nom d’utilisateur** (facultatif) |  Utilisée avec RLS, c’est une chaîne qui peut vous aider à identifier l’utilisateur lors de l’application de règles RLS. |
| **rôles** (facultatif)   |   Chaîne contenant les rôles pour sélectionner lors de l’application de règles de sécurité au niveau de ligne. Si vous passez plusieurs rôles, il doivent être passés sous forme d’un tableau de chaîne.    |
| **exp** (facultatif)    |   Indique l’heure dans laquelle le jeton arrive à expiration. Il doivent être passés dans comme Unix horodatages.   |
| **NBF** (facultatif)    |   Indique l’heure dans laquelle le jeton commence valide. Il doivent être passés dans comme Unix horodatages.   |

Un jeton d’application exemple ressemble à ceci :

![](media\power-bi-embedded-app-token-flow\power-bi-embedded-app-token-flow-sample-coded.png)


Lorsque décoder, il ressemble à ceci :

![](media\power-bi-embedded-app-token-flow\power-bi-embedded-app-token-flow-sample-decoded.png)


## <a name="heres-how-the-flow-works"></a>Voici comment fonctionne le flux

1. Copier les clés de l’API dans votre application. Vous pouvez ouvrir les clés de **Portail Azure**.

    ![](media\powerbi-embedded-get-started-sample\azure-portal.png)

2. Jeton indique une demande de remboursement et comporte un délai d’expiration.

    ![](media\powerbi-embedded-get-started-sample\power-bi-embedded-token-2.png)

3. Jeton obtient connecté avec un touches d’accès API.

    ![](media\powerbi-embedded-get-started-sample\power-bi-embedded-token-3.png)

4. Demandes de l’utilisateur pour afficher un rapport.

    ![](media\powerbi-embedded-get-started-sample\power-bi-embedded-token-4.png)

5.  Jeton est validée avec un touches d’accès API.

    ![](media\powerbi-embedded-get-started-sample\power-bi-embedded-token-5.png)

6.  Power BI incorporé envoie un rapport à l’utilisateur.

    ![](media\powerbi-embedded-get-started-sample\power-bi-embedded-token-6.png)

Une fois que **Power BI incorporé** envoie un rapport à l’utilisateur, l’utilisateur peut afficher le rapport dans votre application personnalisée. Par exemple, si vous avez importé l' [exemple de l’analyse des ventes données PBIX](http://download.microsoft.com/download/1/4/E/14EDED28-6C58-4055-A65C-23B4DA81C4DE/Analyzing_Sales_Data.pbix), l’application web exemple serait comme suit :

![](media\powerbi-embedded-get-started-sample\sample-web-app.png)

## <a name="see-also"></a>Voir aussi
- [Prise en main exemple Microsoft Power BI incorporé](power-bi-embedded-get-started-sample.md)
- [Scénarios courants de Microsoft Power BI incorporé](power-bi-embedded-scenarios.md)
- [Prise en main avec Microsoft Power BI incorporé](power-bi-embedded-get-started.md)

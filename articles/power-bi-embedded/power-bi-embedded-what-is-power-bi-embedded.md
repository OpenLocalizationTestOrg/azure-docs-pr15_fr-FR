<properties
   pageTitle="Qu’est Microsoft Power BI incorporé ?"
   description="Power BI incorporé vous permet d’intégrer des rapports Power BI dans vos applications web ou mobiles afin que vous n’avez pas besoin de créer des solutions personnalisées pour visualiser des données pour vos utilisateurs"
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

# <a name="what-is-microsoft-power-bi-embedded"></a>Qu’est Microsoft Power BI incorporé ?

Avec **Power BI incorporé**, vous pouvez intégrer des rapports Power BI directement dans vos applications web ou mobiles.

![](media\powerbi-embedded-whats-is\what-is.png)

Power BI incorporé est un **service Azure** qui permet aux éditeurs de logiciels et aux développeurs de l’application en surface expériences de données Power BI dans leurs applications. En tant que développeur, vous avez créé des applications, et ces applications ont leurs propres utilisateurs et l’ensemble de fonctionnalités distinct. Ces applications peuvent se produire également pour que certains éléments de données intégrés tels que des graphiques et des rapports qui peuvent désormais être optimisés par Microsoft Power BI incorporé. Les utilisateurs ne besoin d’un compte de Power BI à utiliser votre application. Ils peuvent continuer à se connecter à votre application comme avant et afficher et interagir avec Power BI expérience de création de rapports sans avoir besoin de licence supplémentaire.

## <a name="licensing-for-microsoft-power-bi-embedded"></a>Gestion des licences de Microsoft Power BI incorporé

Dans le modèle d’utilisation **Microsoft Power BI incorporée** , la gestion des licences de Power BI ne sont pas la responsabilité de l’utilisateur final.  En revanche, **rend** achetées par le développeur de l’application qui consomme les effets visuels et sont appliqués à l’abonnement qui possède ces ressources.

## <a name="microsoft-power-bi-embedded-conceptual-model"></a>Microsoft Power BI incorporé modèle conceptuel

![](media\powerbi-embedded-whats-is\model.png)

Ressources pour Power BI incorporé sont mis en service comme tout autre service dans Azure, via les [API de processeur Azure](https://msdn.microsoft.com/library/mt712306.aspx). Dans ce cas, la ressource à laquelle vous devez configurer est une **Collection de sites Power BI espace de travail**.

## <a name="workspace-collection"></a>Collection de sites espace de travail

Une **Collection de sites espace de travail** est le conteneur de Azure niveau supérieur pour les ressources qui contient 0 ou plusieurs **espaces de travail**.  Un **espace de travail** **Collection** comporte toutes les propriétés Azure standard, ainsi que les éléments suivants :

-   **Touches d’accès rapide** – clés utilisées lors de l’appel en toute sécurité API Power BI (décrites dans une section ultérieure).
-   **Utilisateurs** – utilisateurs Azure Active Directory (DAS) qui ont des droits d’administrateur pour gérer la collection de sites Power BI espace de travail via le portail Azure ou API processeur.
-   **Région** – dans le cadre de la mise en service d’une **Collection de sites espace de travail**, vous pouvez sélectionner une région à mettre en service dans. Pour plus d’informations, voir [Régions Azure](https://azure.microsoft.com/regions/).

## <a name="workspace"></a>Espace de travail

Un **espace de travail** est un conteneur de contenu Power BI, ce qui peut inclure des groupes de données, les rapports et tableaux de bord. Un **espace de travail** est vide lorsque vous créez. Lors de l’aperçu, vous devez créer tout le contenu à l’aide de Power BI Desktop et vous devez le télécharger à un de vos espaces de travail à l’aide de l' [API de reste Power BI](http://docs.powerbi.apiary.io/reference).

## <a name="using-workspace-collections-and-workspaces"></a>À l’aide des espaces de travail et des collections de sites espace de travail
**Collections d’espace de travail** et des **espaces de travail** sont des conteneurs de contenu qui sont utilisées et organisés par la façon la plus adaptée correspond à la conception de l’application que vous créez. Il y a plusieurs façons que vous pouvez organiser le contenu qu’ils contiennent. Vous pouvez choisir de mettre tout le contenu dans un espace de travail, puis utiliserez jetons application pour diviser plus encore le contenu entre vos clients. Vous pouvez également choisir mettre tous vos clients dans les espaces de travail distincts afin qu’il existe certaines séparation entre elles. Ou bien, vous pouvez choisir d’organiser les utilisateurs par région plutôt que par le client. Cette conception flexible vous permet de choisir la meilleure façon d’organiser le contenu.

## <a name="cached-datasets"></a>Jeux de données mises en cache

Jeux de données mises en cache peut être utilisé dans l’aperçu.  Toutefois, vous ne pouvez pas actualiser les données mis en cache une fois qu’elle a été chargée dans **Microsoft Power BI incorporé**.

## <a name="authentication-and-authorization-with-app-tokens"></a>Authentification et autorisation avec jetons d’application

**Microsoft Power BI incorporé** diffère à votre application pour effectuer toutes les l’authentification des utilisateurs nécessaires et l’autorisation. Il est inutile explicite que vos utilisateurs finaux être clients d’Azure Active Directory (AD Azure).  En revanche, votre application exprime **Microsoft Power BI incorporé** d’une autorisation pour le rendu d’un rapport Power BI à l’aide des **Jetons d’authentification Application (application jetons)**.  Ces **Jetons d’application** sont créés selon vos besoins lorsque votre application souhaite pour le rendu d’un rapport.  Voir [jetons d’application](power-bi-embedded-get-started-sample.md#key-flow).

![](media\powerbi-embedded-whats-is\app-tokens.png)

**Application d’authentification jetons (application jetons)** sont utilisés pour l’authentification sur **Microsoft Power BI incorporé**.  Il existe trois types de **Jetons d’application**:

1.  Mise en service des jetons - utilisés lors de la configuration d’un nouvel **espace de travail** dans une **Collection de sites espace de travail**
2.  Jetons de développement - utilisés lorsque vous effectuez des appels directement à l' **API de reste Power BI**
3.  Incorporation de jetons - utilisés lorsque vous faites appel pour le rendu d’un rapport dans l’iframe incorporé

Ces jetons sont utilisés pour les différentes phases de vos interactions avec **Microsoft Power BI incorporé**.  Les jetons sont conçus afin que vous pouvez déléguer des autorisations à partir de votre application Power BI. Pour plus d’informations, voir [Application jeton flux](power-bi-embedded-app-token-flow.md).

## <a name="see-also"></a>Voir aussi
- [Scénarios courants de Microsoft Power BI incorporé](power-bi-embedded-scenarios.md)
- [Prise en main avec Microsoft Power BI incorporé](power-bi-embedded-get-started.md)

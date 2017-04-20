<properties 
    pageTitle="Vue d’ensemble d’intégration d’entreprise | Service d’application Microsoft Azure | Microsoft Azure" 
    description="Utiliser les fonctionnalités d’intégration d’entreprise pour activer les scénarios d’intégration et processus métier à l’aide des applications de logique" 
    services="logic-apps" 
    documentationCenter=".net,nodejs,java"
    authors="msftman" 
    manager="erikre" 
    editor="cgronlun"/>

<tags 
    ms.service="logic-apps" 
    ms.workload="integration" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/08/2016" 
    ms.author="deonhe"/>

# <a name="overview-of-the-enterprise-integration-pack"></a>Vue d’ensemble de l’entreprise Integration Pack

## <a name="what-is-the-enterprise-integration-pack"></a>Quel est le module de l’intégration d’entreprise ?
Le Pack de l’intégration entreprise est sur le nuage solution Microsoft d’activation en toute transparence les communications entreprises (B2B). Le module utilise des protocoles standard notamment [AS2](./app-service-logic-enterprise-integration-as2.md), [X12](./app-service-logic-enterprise-integration-x12.md)et [EDIFACT](./app-service-logic-enterprise-integration-edifact.md) pour échanger des messages entre partenaires professionnels. Les messages peuvent être éventuellement sécurisés utilisant le chiffrement et les signatures numériques. 

Le pack permet aux organisations qui utilisent des protocoles différents et pour échanger des messages électroniques à transformer les différents formats dans un format de systèmes les deux organisations peuvent interpréter et agir sur les formats. 

Si vous êtes habitué à BizTalk Server ou Microsoft Azure BizTalk Services, vous trouverez facile à utiliser que l’intégration d’entreprise fonctionnalités parce que la plupart des concepts est similaire. Une des différences majeures sont qu’intégration d’entreprise utilise l’intégration des comptes pour simplifier le stockage et la gestion des objets utilisés dans les communications B2B. 

Architecturale, le Pack de l’intégration d’entreprise est basé sur les **comptes d’intégration** qui stockent tous les objets pouvant être utilisées pour concevoir, déployer et gérer vos applications B2B. Un compte de l’intégration est en fait un conteneur sur le nuage dans lequel vous stockez des objets tels que des schémas, des partenaires, des certificats, des cartes et des accords. Ces objets puis utilisable dans les applications logique pour créer des flux de travail B2B. Avant de pouvoir utiliser les objets dans une application logique, vous devez lier votre compte de l’intégration à votre application logique. Une fois les lié, votre application logique a accès aux objets de compte de l’intégration.  

## <a name="why-should-you-use-enterprise-integration"></a>Pourquoi devez-vous utiliser intégration d’entreprise ?
- Intégration d’entreprise, vous ne pouvez stocker tous vos objets en un seul endroit, ce qui correspond à votre compte d’intégration. 
- Vous pouvez tirer parti du moteur d’applications logique et tous ses connecteurs pour créer des flux de travail B2B et intégrer 3e partie SaaS applications, applications locales ainsi que des applications personnalisées
- Vous pouvez également tirer parti des fonctions Azure

## <a name="how-to-get-started-with-enterprise-integration"></a>Comment se familiariser avec l’intégration de l’entreprise ?
Vous pouvez créer et gérer les applications B2B à l’aide de l’entreprise Integration Pack via le concepteur, applications logique sur le **portail Azure**.  

Vous pouvez également utiliser [PowerShell](https://msdn.microsoft.com/library/azure/mt652195.aspx "logique applications rubriques PowerShell") pour gérer vos applications logique. 

Voici une vue d’ensemble des étapes à suivre avant de créer des applications dans le portail Azure : ![image de la vue d’ensemble](./media/app-service-logic-enterprise-integration-overview/overview-0.png)  

## <a name="what-are-some-common-scenarios"></a>Quels sont les scénarios courants ?

Intégration d’entreprise prend en charge ces normes industrielles :   

- EDI - Electronic Data Interchange  
- Messages - intégration  

## <a name="heres-what-you-need-to-get-started"></a>Voici ce que vous devez prise en main
- Un abonnement Azure avec un compte de l’intégration
- Visual Studio 2015 pour créer des cartes et des schémas
- [L’intégration de Microsoft Azure logique applications Enterprise Tools pour Visual Studio 2015 2.0](https://aka.ms/vsmapsandschemas)  

## <a name="try-it"></a>Essaie
[Essayez-le maintenant](https://github.com/Azure/azure-quickstart-templates/tree/master/201-logic-app-as2-send-receive) pour déployer un échantillon opérationnel AS2 envoyer / recevoir application logique qui utilise les fonctionnalités B2B des applications logique.

## <a name="learn-more-about"></a>En savoir plus sur :
- [Accords] (./app-service-logic-enterprise-integration-agreements.md "Découvrez les contrats de l’intégration d’entreprise")
- [Scénarios interentreprises (B2B)] (./app-service-logic-enterprise-integration-b2b.md "Apprenez à créer des applications logique avec des fonctionnalités B2B")  
- [Certificats] (./app-service-logic-enterprise-integration-certificates.md "Découvrez les certificats d’intégration d’entreprise")
- [Fichier plat codage/décodage] (./app-service-logic-enterprise-integration-flatfile.md "Apprendre à coder et de décoder le contenu du fichier plat")  
- [Comptes d’intégration] (./app-service-logic-enterprise-integration-accounts.md "En savoir plus sur les comptes d’intégration")
- [Cartes] (./app-service-logic-enterprise-integration-maps.md "En savoir plus sur l’intégration d’entreprise cartes")
- [Partenaires] (./app-service-logic-enterprise-integration-partners.md "Découvrez les partenaires d’intégration entreprise")
- [Schémas] (./app-service-logic-enterprise-integration-schemas.md "En savoir plus sur les schémas de l’intégration d’entreprise")
- [Validation des messages XML] (./app-service-logic-enterprise-integration-xml.md "Découvrez comment valider les messages XML avec les applications de logique")
- [Transformation XML] (./app-service-logic-enterprise-integration-transform.md "En savoir plus sur l’intégration d’entreprise cartes")
- [Connecteurs de l’intégration d’entreprise] (../connectors/apis-list.md "En savoir plus sur les connecteurs de module intégration entreprise")




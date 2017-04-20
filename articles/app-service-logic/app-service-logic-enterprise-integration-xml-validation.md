<properties 
    pageTitle="Vue d’ensemble de la validation XML dans le Pack de l’intégration d’entreprise | Service d’application Microsoft Azure | Microsoft Azure" 
    description="Apprenez comment fonctionne la validation dans les applications Enterprise Integration Pack et logique" 
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
    ms.date="07/08/2016" 
    ms.author="deonhe"/>

# <a name="enterprise-integration-with-xml-validation"></a>Intégration d’entreprise avec validation XML

## <a name="overview"></a>Vue d’ensemble
Souvent, dans les scénarios B2B, les partenaires à un accord besoin valider les messages qu’ils échangent entre eux valides avant de commencer la transformation des données. Dans le Pack de l’intégration d’entreprise, vous pouvez utiliser le connecteur XML Validation pour valider les documents par rapport à un schéma prédéfini.  

## <a name="how-to-validate-a-document-with-the-xml-validation-connector"></a>Comment faire pour valider un document avec le connecteur Validation XML
1. Créer une application logique et [liez-le à votre compte de l’intégration](./app-service-logic-enterprise-integration-accounts.md "apprendre à lier un compte de l’intégration à une application logique") qui contient le schéma que vous utiliserez pour valider les données XML.
2. Ajouter un déclencheur **- demande HTTP un lors de la demande** dans votre application de logique  
![](./media/app-service-logic-enterprise-integration-xml/xml-1.png)    
3. Ajouter l’action de **Validation XML** en première sélection **Ajouter une action**  
4. Entrer les *xml* dans la zone de recherche pour filtrer toutes les actions à celui que vous souhaitez utiliser 
5. Sélectionnez **Validation XML**     
![](./media/app-service-logic-enterprise-integration-xml/xml-2.png)   
6. Sélectionnez la zone de texte **contenu**  
![](./media/app-service-logic-enterprise-integration-xml/xml-1-5.png)
7. Sélectionnez la balise de corps en tant que le contenu qui sera validé.   
![](./media/app-service-logic-enterprise-integration-xml/xml-3.png)  
8. Sélectionnez la zone de liste **Nom de schéma** , choisissez le schéma que vous souhaitez utiliser pour valider la saisie du *contenu* ci-dessus     
![](./media/app-service-logic-enterprise-integration-xml/xml-4.png) 
9. Enregistrer votre travail  
![](./media/app-service-logic-enterprise-integration-xml/xml-5.png) 

À ce stade, vous avez terminé la configuration de votre connecteur de validation. Dans une application réels, vous souhaiterez stocker les données validées dans une application métier tels que SalesForce. Vous pouvez facilement ajouter une action pour envoyer le résultat de la validation à force de vente. 

Vous pouvez maintenant tester votre action de validation à l’aide d’une demande au point de terminaison HTTP.  

## <a name="next-steps"></a>Étapes suivantes

[En savoir plus sur le Pack de l’intégration d’entreprise] (./app-service-logic-enterprise-integration-overview.md "En savoir plus sur le Pack de l’intégration d’entreprise")   
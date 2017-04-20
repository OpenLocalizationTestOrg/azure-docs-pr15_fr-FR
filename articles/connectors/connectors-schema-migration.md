<properties
    pageTitle="Comment migrer des applications logique schéma version 2015-08-01-Preview | Service d’application Microsoft Azure"
    description="Vous pouvez facilement migrer vos applications logique vers la dernière version de schéma. Il suffit de suivre ces étapes."
    services="logic-apps"
    documentationCenter=""
    authors="MSFTMAN"
    manager="erikre"
    editor=""
    tags="connectors"/>

<tags
    ms.service="logic-apps"
    ms.workload="integration"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="08/23/2016"
    ms.author="deonhe"/>

# <a name="how-to-migrate-logic-apps-to-schema-version-2015-08-01-preview"></a>Comment migrer des applications logique schéma version 2015-08-01-Preview

Pour déplacer vos applications logique existant vers le nouveau schéma, procédez comme suit :  
1. Ouvrez votre application logique dans le portail Azure  
2. Cliquez sur schéma de mise à jour :

 ![Icône de l’API][step1]   
La page mise à jour du schéma affiche et fournit un lien vers un document qui fournissent des détails sur les améliorations apportées dans le nouveau schéma : ![API icône][step2]

>[AZURE.NOTE] Lorsque vous sélectionnez **Mise à jour de schéma**, nous automatiquement exécutez les étapes de migration et fournissez la sortie de code pour vous. Vous pouvez utiliser cette mise à jour la définition de votre, cependant, vous assurer que vous suivez les bonnes pratiques de programmation tels que décrits dans la section **recommandations** ci-dessous.

## <a name="best-practices-when-migrating-your-logic-apps-to-the-latest-schema-version"></a>Meilleures pratiques lors de la migration de vos applications logique vers la dernière version de schéma :  

- Copiez le script a été déplacée vers une nouvelle application logique - ne remplace l’ancien une jusqu'à ce que vous avez terminé votre test confirmé l’application a été déplacée fonctionne comme prévu.
- Tester votre logique application **avant de** mettre en production
- Une fois la migration terminée, démarrez mise à jour de vos applications logique pour utiliser l' [API managé](./apis-list.md) autant que possible. Par exemple, vous pouvez commencer à utiliser Dropbox v2, où vous utilisez v1 DropBox.


## <a name="whats-next"></a>Ensuite ?
-  [Découvrez comment migrer manuellement vos applications logique](../app-service-logic/app-service-logic-schema-2015-08-01.md)


<!--Icon references-->
[step1]: ./media/connectors-schema-migration/migrateschema1.png
[step2]: ./media/connectors-schema-migration/migrateschema2.png







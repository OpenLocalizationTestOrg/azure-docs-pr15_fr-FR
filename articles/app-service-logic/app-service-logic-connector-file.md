<properties
    pageTitle="L’utilisation du connecteur de fichier dans les applications logique | Service d’application Microsoft Azure"
    description="Comment créer et configurer le connecteur de fichier ou une application API et l’utiliser dans une application logique dans le Service d’application Azure"
    authors="rajeshramabathiran"
    manager="erikre"
    editor=""
    services="logic-apps"
    documentationCenter=""/>

<tags
    ms.service="logic-apps"
    ms.workload="integration"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/01/2016"
    ms.author="rajram"/>

# <a name="get-started-with-the-file-connector-and-add-it-to-your-logic-app"></a>Prise en main du connecteur de fichier et l’ajouter à votre application logique
>[AZURE.NOTE] Cette version de l’article s’applique à la version du schéma logique applications 2014-12-01-aperçu.

Se connecter à un système de fichiers à télécharger, télécharger et bien plus encore à vos fichiers sur un ordinateur hôte. Logique applications peuvent déclencher basée sur une variété de sources de données et des connecteurs offre pour récupérer et traiter des données. Vous pouvez ajouter le lien du fichier à vos données de flux de travail et processus métier dans le cadre de ce flux de travail au sein d’une application logique. 

Le connecteur de fichier utilise le Gestionnaire de connexions hybride pour la connectivité hybride au système de fichiers hôte.

## <a name="creating-a-file-connector-for-your-logic-app"></a>Création d’un connecteur de fichier pour votre application logique ##
Pour utiliser le connecteur de fichiers, vous devez d’abord créer une instance de l’application de l’API de connecteur de fichier. Cela peut être exécuté comme suit :

1.  Ouvrir la Azure Marketplace à l’aide de + nouvelle option sur le côté gauche du portail Azure.
2.  Recherchez « connecteur de fichier ».
3.  Sélectionnez **Fichier de connecteur (preview)** dans les résultats de recherche.
4.  Sélectionnez le bouton **créer**
5.  Configurer le connecteur de fichiers comme suit :  
![][1]

    - **Nom** : donner un nom pour le lien de fichier
    - **Paramètres du package**
        - **Dossier racine** - spécifier le chemin du dossier racine sur votre ordinateur hôte. Par exemple. D:\FileConnectorTest
        - **Chaîne de connexion de Service Bus** - fournir une chaîne de connexion de Service Bus. Vérifiez que l’espace de noms bus service est de type Standard et non base afin de permettre l’utilisation du Service de relais Bus.  Service Bus relais utilisé pour la connexion au Gestionnaire de connexion hybride.
    - **Plan de services d’application** - sélectionner ou créer un plan de services d’application
    - **Tarifs couche** - choisir un niveau de tarification pour le connecteur
    - **Groupe de ressources** - sélectionner ou créer un groupe de ressources où le lien doit résider
    - **Abonnement** , sélectionnez un abonnement vous voulez que ce connecteur doit être créé dans
    - **Emplacement** , choisissez l’emplacement géographique dans lequel vous voulez que le connecteur à déployer

4. Cliquez sur Créer. Un nouveau connecteur de fichier sera créé

## <a name="configure-hybrid-connection-manager"></a>Configurer le Gestionnaire de connexion hybride ##
Une fois l’instance API application est créée, accédez à son tableau de bord.  Pour ce faire, cliquez sur Parcourir > applications API > Sélectionner le lien de fichier API application.  À partir de là, le Gestionnaire de connexions hybride doit être configurée.
Pour plus d’informations sur la configuration et de dépannage du Gestionnaire de connexions hybride voir [l’aide du Gestionnaire de connexion hybride].

## <a name="using-the-file-connector-in-your-logic-app"></a>L’utilisation du connecteur de fichier dans votre application logique ##
Une fois que votre application API est créée, vous pouvez désormais utiliser le connecteur de fichiers sous forme d’action pour votre application logique. Pour ce faire, vous devez :

1.  Créer une nouvelle application logique et choisissez le même groupe de ressources qui comporte le lien de fichier. Suivez les instructions pour [créer une nouvelle application logique].

2.  Ouvrez « Déclencheurs et Actions » de l’application logique créée pour ouvrir les applications logique Designer et configurer votre flux.

3.  Le connecteur de fichier apparaît dans la section « API applications dans ce groupe de ressources » dans la galerie sur le côté droit.

4.  Vous pouvez supprimer l’application de l’API de connecteur de fichier dans l’éditeur en cliquant sur le lien « fichier ». connecteur de fichier expose un déclencheur et 4 Actions :  
![][5]

6.  Chacun de ces expose certaines propriétés. L’image ci-dessous répertorie les propriétés du déclencheur et obtenir le fichier Action :  
![][6]

7. Une fois que celles-ci sont configurées, l’Action et le déclencheur peuvent servir dans votre flux. De même, d’autres actions peuvent être configurées également.

> [AZURE.NOTE] Le fichier le déclencheur fichier sera supprimé après avoir est lue correctement à partir du dossier.

## <a name="file-connector-rest-apis"></a>Connecteur API REST de fichiers ##
Pour utiliser le lien en dehors d’une application logique, l’API REST exposées par le connecteur peuvent être exploitées. Vous pouvez afficher ce définitions API à l’aide de parcourir -> Api application -> connecteur de fichier. Cliquez sur la loupe d’API définition sous la Section Résumé pour afficher toutes les API exposés par ce connecteur :  
![][7]

Vous trouverez des détails des API au [connecteur fichier définition de l’API].

## <a name="do-more-with-your-connector"></a>Faire plus avec le lien
Maintenant que le lien est créé, vous pouvez l’ajouter à un flux de travail de l’entreprise à l’aide d’une application logique. Voir [que sont les applications logique ?](app-service-logic-what-are-logic-apps.md).

>[AZURE.NOTE] Si vous voulez commencer à utiliser les applications Azure logique avant de vous inscrire pour un compte Azure, accédez à [l’application logique essayez](https://tryappservice.azure.com/?appservice=logic), où vous pouvez créer une application de logique starter courte immédiatement dans le Service d’application. Aucune carte de crédit obligatoire ; Aucune engagements.

Afficher la référence Swagger des API REST en [liens et des API applications référence](http://go.microsoft.com/fwlink/p/?LinkId=529766).

Vous pouvez également consulter les statistiques et contrôler les performances de sécurité au connecteur. Voir [gérer et analyser vos applications API et connecteur intégrés](app-service-logic-monitor-your-connectors.md).

<!-- Image reference -->
[1]: ./media/app-service-logic-connector-file/img1.PNG
[5]: ./media/app-service-logic-connector-file/img5.PNG
[6]: ./media/app-service-logic-connector-file/img6.PNG
[7]: ./media/app-service-logic-connector-file/img7.PNG

<!-- Links -->
[Créer une nouvelle application logique]: app-service-logic-create-a-logic-app.md
[Connecteur définition API de fichier]: https://msdn.microsoft.com/library/dn936296.aspx
[L’utilisation du Gestionnaire de connexion hybride]: app-service-logic-hybrid-connection-manager.md

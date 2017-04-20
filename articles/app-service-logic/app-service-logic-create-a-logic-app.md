<properties
    pageTitle="Créer une application logique | Microsoft Azure"
    description="Apprenez à créer une application logique connexion SaaS services"
    authors="jeffhollan"
    manager="dwrede"
    editor=""
    services="logic-apps"
    documentationCenter=""/>

<tags
    ms.service="logic-apps"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="10/18/2016"
    ms.author="jehollan"/>

# <a name="create-a-new-logic-app-connecting-saas-services"></a>Créer une nouvelle application logique connexion SaaS services

Cette rubrique explique comment, en quelques minutes, vous pouvez commencer avec [Azure logique d’applications](app-service-logic-what-are-logic-apps.md). Nous allons un flux de travail simple qui vous permet d’envoyer un Tweet intéressantes à votre messagerie.

Pour utiliser ce scénario, vous devez :

- Un abonnement Azure
- Un compte Twitter
- Un Outlook.com ou hébergé boîte aux lettres Office 365

## <a name="create-a-new-logic-app-to-email-you-tweets"></a>Créer une nouvelle application logique pour envoyer par courrier électronique vous tweets

1. Dans le [tableau de bord du portail Azure](https://portal.azure.com), sélectionnez **Nouveau**. 
2. Dans la barre de recherche, recherchez « application logique », puis sur **Application logique**. Vous pouvez également sélectionner **Nouveau**, **Web + Mobile**et sélectionnez **Application logique**. 
3. Entrez un nom pour votre application logique, sélectionnez un emplacement, un groupe de ressources, puis sélectionnez **créer**.  Si vous sélectionnez **Ajouter au tableau de bord** l’application logique s’ouvrent automatiquement une fois déployée.  
4. Après avoir ouvert votre application logique pour la première fois, vous pouvez sélectionner à partir d’un modèle pour démarrer.  Pour l’instant, cliquez sur **Application logique vierge** pour cela créer à partir de zéro. 
1. Le premier élément que vous créez est le déclencheur.  Il s’agit de l’événement qui lancera votre application logique.  Recherchez **twitter** dans la zone de recherche déclencheur, puis sélectionnez-la.
7. Maintenant, vous allez taper dans un terme de recherche pour déclencher sur.  La **fréquence** et **l’intervalle** déterminent la fréquence à laquelle votre application logique vérifie les tweet nouvelle (et retour tous les tweets pendant cette période).
    ![Recherche de Twitter](./media/app-service-logic-create-a-logic-app/twittersearch.png)

5. Sélectionnez le bouton **nouvelle étape** , puis **Ajouter une action** ou **Ajouter une condition**
6. Lorsque vous sélectionnez **Ajouter une Action**, vous pouvez rechercher des [connecteurs disponibles](../connectors/apis-list.md) pour choisir une action. Par exemple, vous pouvez sélectionner **Outlook.com - envoyer un E-mail** d’envoyer des messages à partir d’une adresse outlook.com :  
    ![Actions](./media/app-service-logic-create-a-logic-app/actions.png)

7. Vous devez ensuite remplir les paramètres pour le message électronique que vous voulez :  ![paramètres](./media/app-service-logic-create-a-logic-app/parameters.png)

8. Enfin, vous pouvez sélectionner **Enregistrer** pour rendre votre application logique live.

## <a name="manage-your-logic-app-after-creation"></a>Gérer votre application logique après sa création

À présent votre application logique est en cours d’exécution. Il recherche régulièrement les tweets avec le terme de recherche entré. Lorsqu’il détecte un tweet correspondant, il vous envoie un message électronique. Enfin, vous allez apprendre à désactiver l’application, ou consultez son fonctionnement.

1. Accédez au [portail Azure](https://portal.azure.com)

1. Cliquez sur **Parcourir** sur le côté gauche de l’écran, puis sélectionnez **Applications logique**.

2. Cliquez sur la nouvelle application logique que vous venez de créer pour afficher l’état actuel et des informations générales.

3. Pour modifier votre nouvelle application logique, cliquez sur **Modifier**.

5. Pour désactiver l’application, cliquez sur **désactiver** dans la barre de commandes.

1. Afficher exécuter et le déclencheur historiques à surveiller lorsque votre application logique est en cours d’exécution.  Vous pouvez cliquer sur **Actualiser** pour afficher les données les plus récentes.

En moins de 5 minutes, vous avez pu configurer une application logique simple en cours d’exécution dans le cloud. Pour en savoir plus sur l’utilisation des fonctionnalités des applications logique, voir [utilisation des fonctionnalités d’application logique]. Pour en savoir plus sur les définitions de l’application logique eux-mêmes, voir [créer des définitions de logique d’application](app-service-logic-author-definitions.md).

<!-- Shared links -->
[Azure portal]: https://portal.azure.com
[Utiliser les fonctionnalités de l’application logique]: app-service-logic-create-a-logic-app.md

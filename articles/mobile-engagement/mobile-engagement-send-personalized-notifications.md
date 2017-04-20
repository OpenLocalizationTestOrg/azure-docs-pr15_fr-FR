<properties 
    pageTitle="Envoyer une notification personnalisée avec Azure Mobile Engagement" 
    description="Comment envoyer des notifications personnalisées en incluant des informations de profil utilisateur dans les notifications de leur nom"        
    services="mobile-engagement" 
    documentationCenter="mobile" 
    authors="piyushjo" 
    manager="dwrede" 
    editor="" />

<tags 
    ms.service="mobile-engagement" 
    ms.workload="mobile" 
    ms.tgt_pltfrm="all" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="08/19/2016" 
    ms.author="piyushjo" />

#<a name="personalize-notifications-by-including-user-name"></a>Personnaliser les notifications en incluant le nom d’utilisateur

Dans votre recherche d’adresser des notifications plus attrayante à vos utilisateurs de l’application, pensez à les personnaliser. Une approche puissante se compose des sélectivement en utilisant les noms d’utilisateurs application pour traiter les notifications afin de les rendre plus personnel. Un mot de prudence ici - vous devez approche Ajout noms d’utilisateur pour les notifications avec soin, car si vous utilisez cette stratégie de façon excessive puis il pourrait rencontrer des comme vous pour certains utilisateurs de l’application. Vous devez également vous assurer que vous laissez l’utilisateur participer à fournir ces informations personnelles à votre consentement total dans l’application mobile avant de commencer à utiliser. 

Techniquement, avec l’Engagement Azure Mobile, vous pouvez accomplir personnaliser les notifications en suivant les étapes ci-dessous dans lequel nous utiliserons le scénario de, y compris le nom d’utilisateur dans les notifications. Vous allez utiliser le concept d’application-Info ou balises dont les valeurs peuvent être transférés par le SDK intégré dans l’application Mobile ou via des API. Ces balises ou application Infos peuvent ensuite être utilisées :

1. afin de cibler les notifications à des utilisateurs spécifiques en fonction des valeurs de l’application-Info ou 
2. comme espaces réservés dans les notifications qui seront remplacées par des valeurs spécifiques à l’appareil/utilisateur lors de l’envoi des notifications pour cet appareil. 

> [AZURE.IMPORTANT] Notez que la vitesse d’envoi des notifications verront une réduction en raison de ce traitement supplémentaire de remplacer les valeurs de l’application-info avec chaque notifications. 

##<a name="register-app-info-in-the-mobile-engagement-portal"></a>Registre App-informations sur le portail d’Engagement Mobile

1) Vous commencez avec l’enregistrement d’informations de l’application ou indicateurs dans le portail Azure. Accédez à **paramètres** -> **balise (application-Info)** pour cela.  

![][1]  

2) Cliquez sur **nouvelle balise (application-info)** et indiquez le nom en tant que *nom_utilisateur* et le type de *chaîne* , cliquez sur **Envoyer**. 

![][2]

3) Vous verrez enfin cette application-info enregistré comme suit :

![][3]

##<a name="send-app-info-from-the-client-sdk"></a>Envoyer les informations de l’application à partir du Kit de développement logiciel client

Ici, nous utilisons l’exemple de l’application Windows universel mais méthodes équivalentes existent pour notre autres SDK également. 

En supposant que vous avez une méthode dans l’application mobile où vous obtenez les informations de profil de l’utilisateur comme leurs noms probablement après leur authentification, vous pouvez appeler `SendAppInfo` méthode ici et remplir la valeur de la `user_name` les informations de l’application que vous avez enregistré précédemment dans le système principal service Mobile Engagement. 

    Dictionary<object, object> appInfo = new Dictionary<object, object>();
    appInfo.Add("user_name", str);
    EngagementAgent.Instance.SendAppInfo(appInfo); 

##<a name="send-personalized-notifications"></a>Envoyer des notifications personnalisées

Vous êtes maintenant toutes définies pour envoyer des notifications à l’aide de cette **nom_utilisateur**. 

1) Accédez au portail d’Engagement Mobile sous l’onglet **atteigne** pour créer une notification et vous pouvez utiliser cet espace réservé au format suivant n’importe où dans le titre de la notification ou le corps. 

![][4]  

> [AZURE.NOTE] Tous les utilisateurs pour lesquels les informations d’application nom_utilisateur ne sont pas définie, obtenez pas toutes les notifications. Si vous exécutez la campagne notification en mode test et si vous n’avez pas défini alors nous enverrons application-info ' ?' caractère à remplacer l’espace réservé. 

2) Lorsque Mobile Engagement allez sélectionner un périphérique pour l’envoi de cette notification ensuite examiner cette application-info et remplacez la valeur dans l’espace réservé.  
Par exemple, si nous avons défini `str = "Scott"` pour un utilisateur de l’appareil inscription reçoit associée à l’information sur l’application de **nom_utilisateur = SCOTT** pour cet utilisateur et pour cet utilisateur verra s’afficher une absence de notification d’émission application au format suivant. 

![][5]  

<!-- Images. -->
[1]: ./media/mobile-engagement-send-personalized-notifications/app-info.png
[2]: ./media/mobile-engagement-send-personalized-notifications/create-app-info.png
[3]: ./media/mobile-engagement-send-personalized-notifications/app-info-user-name.png
[4]: ./media/mobile-engagement-send-personalized-notifications/personal-notification.png
[5]: ./media/mobile-engagement-send-personalized-notifications/notification.png


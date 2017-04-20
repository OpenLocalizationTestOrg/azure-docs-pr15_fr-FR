<properties 
    pageTitle="Vous authentifier avec l’API REST de Engagement Mobile - configuration manuelle"
    description="Décrit comment configurer manuellement l’authentification pour API REST de Engagement Mobile" 
    services="mobile-engagement" 
    documentationCenter="mobile" 
    authors="piyushjo"
    manager="erikre"
    editor=""/>

<tags
    ms.service="mobile-engagement"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="mobile-multiple"
    ms.workload="mobile" 
    ms.date="08/19/2016"
    ms.author="piyushjo"/>

# <a name="authenticate-with-mobile-engagement-rest-apis---manual-setup"></a>Vous authentifier avec l’API REST de Engagement Mobile - configuration manuelle

Il s’agit d’une documentation appendice authentifier [avec l’API REST de Engagement Mobile](mobile-engagement-api-authentication.md). Veillez à lire tout d’abord pour obtenir le contexte. Ceci décrit un autre moyen d’effectuer la configuration ponctuelle permettant de configurer l’authentification pour les API REST de Engagement Mobile à l’aide du portail Azure. 

>[AZURE.NOTE] Les instructions ci-dessous sont basées sur ce [guide Active Directory](../resource-group-create-service-principal-portal.md) et personnalisées pour ce qui est requis pour l’authentification des API Engagement Mobile. Si vous y référer si vous voulez comprendre les étapes ci-dessous en détail. 

1. Connectez-vous à votre compte Azure via le [portail classique](https://manage.windowsazure.com/).

2. Sélectionnez **Active Directory** dans le volet gauche.

     ![Sélectionnez Active Directory][1]

3. Choisissez le **Par défaut Active Directory** dans votre portail Azure. 

     ![choix d’un répertoire][2]

    >[AZURE.IMPORTANT] Cette approche fonctionne uniquement lorsque vous travaillez dans Active Directory de votre compte par défaut et ne fonctionnera pas si vous effectuez cette opération dans Active Directory que vous avez créés dans votre compte. 

4. Pour afficher les applications dans votre annuaire, cliquez sur **Applications**.

     ![afficher les applications][3]

5. Cliquez sur **Ajouter**. 

     ![Ajouter une application][4]

6. Cliquez sur **Ajouter une application de développe de mon organisation**

     ![nouvelle application][5]

6. Entrez le nom de l’application d’et sélectionnez le type d’application **WEB APPLICATION et/ou WEB API** et cliquez sur le bouton suivant.

     ![application de nom][6]

7. Vous pouvez fournir des URL factices pour **Session URL** et **Application ID URI**. Ils ne sont pas utilisés pour notre scénario et les URL eux-mêmes ne sont pas validées.  

     ![propriétés de l’application][7]

8. À la fin de cet, vous aurez une application AAD avec le nom que vous avez fourni précédemment comme suit. Il s’agit de votre **AD\_application\_nom** et prenez note de celle-ci.  

     ![nom de l’application][8]

9. Cliquez sur le nom de l’application, cliquez sur **configurer**.

     ![configurer l’application][9]

10. Prenez note de l’ID de CLIENT qui sera utilisé comme **CLIENT\_ID** correspondant à votre API appelle. 

     ![configurer l’application][10]

11. Faites défiler jusqu'à la section **clés** et ajoutez une clé avec une durée de deux ans (expiration), de préférence, puis cliquez sur **Enregistrer**. 

     ![configurer l’application][11]


12. Copiez immédiatement la valeur qui est affichée pour la clé tel qu’il ne s’affiche désormais et n’est pas stocké afin de s’afficheront pas jamais à nouveau. Si vous perdez il vous devez générer une nouvelle clé. Il s’agit **CLIENT_SECRET** pour les appels API. 

     ![configurer l’application][12]

    >[AZURE.IMPORTANT] Cette touche expirera à la fin de la durée que vous avez spécifié pour veillez à le renouveler au moment opportun dans le cas contraire votre authentification API ne fonctionne plus. Vous pouvez également supprimer et recréer cette clé si vous pensez qu’il a été déchiffré.
 
13. Cliquez sur bouton de **Points de terminaison de vue** maintenant qui s’ouvre la boîte de dialogue **Points de terminaison de l’application** . 

    ![][13]

14. À partir de la boîte de dialogue points de terminaison de l’application, copiez **Point de terminaison jeton OAUTH 2.0**. 

    ![][14]

15. Ce point de terminaison seront sous la forme suivante dans laquelle le GUID dans l’URL est votre **TENANT_ID** Notez donc de celle-ci : 

        https://login.microsoftonline.com/<GUID>/oauth2/token

16. Nous allons maintenant configurer les autorisations sur cette application. Pour cela, vous devrez ouvrir le [portail Azure](https://portal.azure.com). 

17. Cliquez sur **Groupes de ressources** , recherchez le groupe de ressources **Mobile Engagement** .  

    ![][15]

18. Cliquez sur le groupe de ressources **Engagement Mobile** et accédez à la carte de **paramètres** ici. 

    ![][16]

19. Cliquez sur **les utilisateurs** dans la carte de paramètres, puis cliquez sur **Ajouter** pour ajouter un utilisateur. 

    ![][17]

20. Cliquez sur **Sélectionner un rôle**

    ![][18]

21. Cliquez sur **propriétaire**

    ![][19]

22. Recherchez le nom de votre application **AD\_application\_nom** dans la zone de recherche. Vous ne sera pas voir ceci par défaut ici. Une fois que vous l’avez trouvé, sélectionnez-le, puis cliquez sur **Sélectionner** dans la partie inférieure de la carte. 

    ![][20]

23. Sur la carte **D’ajout d’accès** , elle apparaît sous la forme **1 utilisateur, 0 groupes**. Dans cette carte pour confirmer la modification, cliquez sur **OK** . 

    ![][21]

Vous avez maintenant terminé la configuration requise AAD et vous sont définis pour appeler les API. 

<!-- Images -->
[1]: ./media/mobile-engagement-api-authentication-manual/active-directory.png
[2]: ./media/mobile-engagement-api-authentication-manual/active-directory-details.png
[3]: ./media/mobile-engagement-api-authentication-manual/view-applications.png
[4]: ./media/mobile-engagement-api-authentication-manual/add-icon.png
[5]: ./media/mobile-engagement-api-authentication-manual/what-do-you-want-to-do.png
[6]: ./media/mobile-engagement-api-authentication-manual/tell-us-about-your-application.png
[7]: ./media/mobile-engagement-api-authentication-manual/app-properties.png
[8]: ./media/mobile-engagement-api-authentication-manual/aad-app.png
[9]: ./media/mobile-engagement-api-authentication-manual/configure-menu.png
[10]: ./media/mobile-engagement-api-authentication-manual/client-id.png
[11]: ./media/mobile-engagement-api-authentication-manual/client_secret.png
[12]: ./media/mobile-engagement-api-authentication-manual/keys.png
[13]: ./media/mobile-engagement-api-authentication-manual/view-endpoints.png
[14]: ./media/mobile-engagement-api-authentication-manual/app-endpoints.png
[15]: ./media/mobile-engagement-api-authentication-manual/resource-groups.png
[16]: ./media/mobile-engagement-api-authentication-manual/resource-groups-settings.png
[17]: ./media/mobile-engagement-api-authentication-manual/add-users.png
[18]: ./media/mobile-engagement-api-authentication-manual/add-role.png
[19]: ./media/mobile-engagement-api-authentication-manual/select-role.png
[20]: ./media/mobile-engagement-api-authentication-manual/add-user-select.png
[21]: ./media/mobile-engagement-api-authentication-manual/add-access-final.png




<properties
   pageTitle="Créez le service principal dans le portail | Microsoft Azure"
   description="Décrit comment créer une nouvelle application Active Directory et principal du service pouvant être utilisées avec le contrôle d’accès basé sur un rôle dans le Gestionnaire de ressources Azure pour gérer l’accès aux ressources."
   services="azure-resource-manager"
   documentationCenter="na"
   authors="tfitzmac"
   manager="timlt"
   editor="tysonn"/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="09/07/2016"
   ms.author="tomfitz"/>

# <a name="use-portal-to-create-active-directory-application-and-service-principal-that-can-access-resources"></a>Utiliser le portail pour créer une application Active Directory et principal de service qui peut accéder aux ressources

> [AZURE.SELECTOR]
- [PowerShell](resource-group-authenticate-service-principal.md)
- [Azure infrastructure du langage commun](resource-group-authenticate-service-principal-cli.md)
- [Portail](resource-group-create-service-principal-portal.md)


Lorsque vous avez une application qui doit accéder ou modifier des ressources, vous devez configurer une application Active Directory (AD) et affecter les autorisations requises à celui-ci. Cette rubrique vous montre comment effectuer ces étapes via le portail. Pour l’instant, vous devez utiliser le portail classique pour créer une nouvelle application Active Directory, puis basculez vers le portail Azure pour attribuer un rôle à l’application. 

> [AZURE.NOTE] Les étapes décrites dans cette rubrique s’appliquent uniquement lorsque vous utilisez le **portail classique** pour créer l’application AD. **Si vous utilisez le portail Azure pour créer l’application Active Directory, ces étapes échouera.** 
>
> Il est possible qu’il plus facile à configurer votre application de AD et le service principal via [PowerShell](resource-group-authenticate-service-principal.md) ou [Azure infrastructure du langage commun](resource-group-authenticate-service-principal-cli.md), surtout si vous voulez utiliser un certificat pour l’authentification. Cette rubrique n’affiche pas l’utilisation d’un certificat.

Pour une explication des concepts Active Directory, voir [objets Application et Principal du Service](./active-directory/active-directory-application-objects.md). Pour plus d’informations sur l’authentification Active Directory, voir [Scénarios d’authentification pour Azure AD](./active-directory/active-directory-authentication-scenarios.md).

Pour plus d’informations sur l’intégration d’une application dans Azure pour la gestion des ressources, consultez le [guide du développeur autorisation avec l’API du Gestionnaire de ressources Azure](resource-manager-api-authentication.md).

## <a name="create-an-active-directory-application"></a>Créer une application Active Directory

1. Connectez-vous à votre compte Azure via le [portail classique](https://manage.windowsazure.com/). 

2. Vérifiez que vous connaissez la valeur par défaut Active Directory pour votre abonnement. Vous pouvez limiter l’accès pour les applications dans le même répertoire que votre abonnement. Sélectionnez **paramètres** , recherchez le nom du répertoire associé à votre abonnement.  Pour plus d’informations, voir [comment Azure abonnements sont associés à Azure Active Directory](./active-directory/active-directory-how-subscriptions-associated-directory.md).
   
     ![trouver le répertoire par défaut](./media/resource-group-create-service-principal-portal/show-default-directory.png)

2. Sélectionnez **Active Directory** dans le volet gauche.

     ![Sélectionnez Active Directory](./media/resource-group-create-service-principal-portal/active-directory.png)
     
3. Sélectionnez l’annuaire Active Directory que vous souhaitez utiliser pour créer l’application. Si vous avez plusieurs Active Directory, de créer l’application dans le répertoire par défaut pour votre abonnement.   

     ![choix d’un répertoire](./media/resource-group-create-service-principal-portal/active-directory-details.png)
     
3. Pour afficher les applications dans votre annuaire, sélectionnez **Applications**.

     ![afficher les applications](./media/resource-group-create-service-principal-portal/view-applications.png)

4. Si vous n’avez pas créé d’application dans ce répertoire avant, vous devez savoir doit ressembler à l’image ci-dessous. Sélectionnez **Ajouter une APPLICATION**

     ![Ajouter une application](./media/resource-group-create-service-principal-portal/create-application.png)

     Ou, cliquez sur **Ajouter** dans le volet inférieur.

     ![ajouter](./media/resource-group-create-service-principal-portal/add-icon.png)

5. Sélectionnez le type d’application que vous voulez créer. Pour ce didacticiel, sélectionnez **Ajouter une application de développe de mon organisation**. 

     ![nouvelle application](./media/resource-group-create-service-principal-portal/what-do-you-want-to-do.png)

6. Indiquez un nom pour l’application, puis sélectionnez le type d’application que vous voulez créer. Pour ce didacticiel, créez un **WEB APPLICATION et/ou WEB API** et cliquez sur le bouton suivant. Si vous sélectionnez **APPLICATION cliente NATIVE**, les étapes restantes de cet article ne correspondront pas votre expérience.

     ![application de nom](./media/resource-group-create-service-principal-portal/tell-us-about-your-application.png)

7. Renseignez les propriétés pour votre application. Pour l' **URL de session**, fournissent URI à un site web qui décrit votre application. L’existence du site web n’est pas validée. Pour l' **Application ID URI**, fournissent l’URI qui identifie votre application.

     ![propriétés de l’application](./media/resource-group-create-service-principal-portal/app-properties.png)

Vous avez créé votre application.

## <a name="get-client-id-and-authentication-key"></a>Obtenir client clé id et l’authentification

Lorsque vous vous connectez par programme, vous devez l’id de votre application. Si l’application est exécutée sous ses propres informations d’identification, vous devez également une clé d’authentification.

1. Sélectionnez l’onglet **configurer** pour configurer votre mot de passe de votre application.

     ![configurer l’application](./media/resource-group-create-service-principal-portal/application-configure.png)

2. Copiez l' **ID de CLIENT**.
  
     ![id de client](./media/resource-group-create-service-principal-portal/client-id.png)

3. Si l’application est exécutée sous ses propres informations d’identification, faites défiler jusqu'à la section **clés** et sélectionnez la durée pendant laquelle vous voulez que votre mot de passe soit valide.

     ![touches](./media/resource-group-create-service-principal-portal/create-key.png)

4. Sélectionnez **Enregistrer** pour créer votre clé.

     ![Enregistrer](./media/resource-group-create-service-principal-portal/save-icon.png)

     La clé enregistrée s’affiche et vous pouvez la copier. Vous n’êtes pas en mesure de récupérer la clé ultérieurement par conséquent, le copier maintenant.

     ![enregistré clé](./media/resource-group-create-service-principal-portal/save-key.png)

## <a name="get-tenant-id"></a>Obtenir l’id de client

Lorsque vous vous connectez par programme, vous devez passer l’id de client avec votre demande d’authentification. Pour les applications Web et applications d’API Web, vous pouvez récupérer l’id de client en sélectionnant des **points de terminaison de mode** dans la partie inférieure de l’écran et extraction de l’id comme le montre l’image suivante.  

   ![id de client](./media/resource-group-create-service-principal-portal/save-tenant.png)

Vous pouvez également récupérer l’id de client par le biais PowerShell :

    Get-AzureRmSubscription

Ou, Azure infrastructure du langage commun :

    azure account show --json

## <a name="set-delegated-permissions"></a>Délégué de définir des autorisations

Si votre application accède aux ressources au nom d’un utilisateur connecté, vous devez accorder l’autorisation d’accéder à d’autres applications déléguée votre application. Vous octroyez d’accès dans la section **autorisations à d’autres applications** de l’onglet **configurer** . Par défaut, une autorisation déléguée est déjà activée pour Azure Active Directory. Laissez cette autorisation déléguée inchangée.

1. Sélectionnez **Ajouter application**.

2. Dans la liste, sélectionnez l' **API de gestion du Service Windows Azure**. Ensuite, sélectionnez l’icône complète.

      ![Sélectionnez application](./media/resource-group-create-service-principal-portal/select-app.png)

3. Dans la liste déroulante des autorisations déléguées, sélectionnez **Gestion des services Azure Access en tant qu’organisation**.

      ![Sélectionnez l’autorisation](./media/resource-group-create-service-principal-portal/select-permissions.png)

4. Enregistrer les modifications.

## <a name="assign-application-to-role"></a>Attribuer application au rôle

Si votre application s’exécute sous ses propres informations d’identification, vous devez attribuer l’application à un rôle. Déterminez quel rôle représente les autorisations appropriées pour l’application. Pour en savoir plus sur les rôles disponibles, voir [RBAC : intégré rôles](./active-directory/role-based-access-built-in-roles.md). 

Pour attribuer un rôle à l’application, vous devez disposer des autorisations appropriées. En particulier, vous devez avoir `Microsoft.Authorization/*/Write` accès est accordé via le rôle de [propriétaire](./active-directory/role-based-access-built-in-roles.md#owner) ou [Administrateur de l’accès utilisateur](./active-directory/role-based-access-built-in-roles.md#user-access-administrator) . Le rôle de collaborateur n’a pas accès correct.

Vous pouvez définir l’étendue au niveau de l’abonnement, le groupe de ressources ou la ressource. Autorisations sont héritées à des niveaux inférieurs d’étendue. Par exemple, l’ajout d’une application pour le rôle de lecteur pour un groupe de ressources signifie qu’il peut lire le groupe de ressources et les ressources qu’il contient.

1. Pour attribuer un rôle de l’application, passez à partir du portail classique au [portail Azure](https://portal.azure.com).

1. Vérifiez vos autorisations pour vous assurer que vous pouvez affecter l’entité de service à un rôle. Sélectionnez **Mes autorisations** pour votre compte.

    ![Sélectionnez mes autorisations](./media/resource-group-create-service-principal-portal/my-permissions.png)

1. Afficher les autorisations affectées pour votre compte. Comme indiqué précédemment, vous devez appartenir aux rôles propriétaire ou administrateur de l’accès utilisateur ou disposer d’un rôle personnalisé qui autorise l’accès en écriture pour Microsoft.Authorization. L’image suivante montre un compte qui est attribué le rôle de collaborateur pour l’abonnement, qui n’est pas des autorisations appropriées pour attribuer une application à un rôle.

    ![Afficher mes autorisations](./media/resource-group-create-service-principal-portal/show-permissions.png)

     Si vous ne disposez des autorisations appropriées pour accorder l’accès à l’application, que vous devez demander que votre administrateur d’abonnement ajoute le rôle d’administrateur de l’accès utilisateur, ni demander un administrateur accorde l’accès à l’application.

1. Naviguez jusqu’au niveau de l’étendue que vous souhaitez affecter l’application. Pour attribuer un rôle à la portée d’abonnement, sélectionnez **abonnements**.

     ![Sélectionnez l’abonnement](./media/resource-group-create-service-principal-portal/select-subscription.png)

     Sélectionnez l’abonnement particulier pour affecter l’application.

     ![Sélectionnez l’abonnement pour affectation](./media/resource-group-create-service-principal-portal/select-one-subscription.png)

     Sélectionnez l’icône **Access** dans le coin supérieur droit.

     ![Sélectionnez l’accès](./media/resource-group-create-service-principal-portal/select-access.png)
     
     Ou, pour attribuer un rôle à portée de groupe de ressources, accédez à un groupe de ressources. À partir de la carte de groupe de ressources, sélectionnez **le contrôle d’accès**.

     ![Sélectionnez les utilisateurs](./media/resource-group-create-service-principal-portal/select-users.png)

     Les étapes suivantes sont les mêmes pour n’importe quelle portée.

2. Sélectionnez **Ajouter**.

     ![Sélectionnez Ajouter](./media/resource-group-create-service-principal-portal/select-add.png)

3. Sélectionnez le rôle de **lecteur** (ou un autre rôle que vous souhaitez affecter l’application à).

     ![Sélectionnez rôle](./media/resource-group-create-service-principal-portal/select-role.png)

4. Lorsque vous voyez tout d’abord la liste des utilisateurs que vous pouvez ajouter au rôle, vous ne verrez pas les applications. Vous verrez uniquement de groupe et les utilisateurs.

     ![afficher les utilisateurs](./media/resource-group-create-service-principal-portal/show-users.png)

5. Pour rechercher votre application, vous devez rechercher. Commencez à taper le nom de votre application et modifiera la liste des options disponibles. Sélectionnez votre application lorsqu’elle apparaît dans la liste.

     ![attribuer au rôle](./media/resource-group-create-service-principal-portal/assign-to-role.png)

6. Sélectionnez **j’OK** pour finaliser l’attribution de rôle. Vous devez maintenant voir votre application dans la liste des utilisations affectée à un rôle du groupe de ressources.


Pour plus d’informations sur l’attribution d’applications aux rôles via le portail et des utilisateurs, voir [utiliser les affectations de rôle pour gérer l’accès à vos ressources abonnement Azure](role-based-access-control-configure.md#manage-access-using-the-azure-management-portal).

## <a name="sample-applications"></a>Exemples d’applications

Les exemples d’application suivants montrent comment se connecter en tant que l’entité de service.

**.NET**

- [Déployer un SSH activé machine virtuelle avec un modèle avec .NET](https://azure.microsoft.com/documentation/samples/resource-manager-dotnet-template-deployment/)
- [Gérer les ressources Azure et groupes de ressources avec .NET](https://azure.microsoft.com/documentation/samples/resource-manager-dotnet-resources-and-groups/)

**Java**

- [Mise en route des ressources - déployer à l’aide de modèles Gestionnaire de ressources Azure - dans Java](https://azure.microsoft.com/documentation/samples/resources-java-deploy-using-arm-template/)
- [Mise en route des ressources - gérer le groupe de ressources - dans Java](https://azure.microsoft.com/documentation/samples/resources-java-manage-resource-group//)

**Python**

- [Déployer un SSH activé machine virtuelle avec un modèle dans Python](https://azure.microsoft.com/documentation/samples/resource-manager-python-template-deployment/)
- [Gestion des ressources Azure et groupes de ressources avec Python](https://azure.microsoft.com/documentation/samples/resource-manager-python-resources-and-groups/)

**Node.js**

- [Déployer un SSH activé machine virtuelle avec un modèle dans Node.js](https://azure.microsoft.com/documentation/samples/resource-manager-node-template-deployment/)
- [Gérer les ressources et groupes avec Node.js ressources Azure](https://azure.microsoft.com/documentation/samples/resource-manager-node-resources-and-groups/)

**Ruby**

- [Déployer un SSH activé machine virtuelle avec un modèle dans Ruby](https://azure.microsoft.com/documentation/samples/resource-manager-ruby-template-deployment/)
- [Gestion des ressources Azure et groupes de ressources Ruby](https://azure.microsoft.com/documentation/samples/resource-manager-ruby-resources-and-groups/)


## <a name="next-steps"></a>Étapes suivantes

- Pour en savoir plus sur la spécification des stratégies de sécurité, consultez [Contrôle d’accès basé sur rôle Azure](./active-directory/role-based-access-control-configure.md).  
- Pour une démonstration vidéo de ces étapes, voir [Gestion par programme l’activation d’une ressource Azure avec Azure Active Directory](https://channel9.msdn.com/Series/Azure-Active-Directory-Videos-Demos/Enabling-Programmatic-Management-of-an-Azure-Resource-with-Azure-Active-Directory).


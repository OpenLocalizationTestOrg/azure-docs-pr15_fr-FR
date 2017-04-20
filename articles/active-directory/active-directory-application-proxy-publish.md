<properties
    pageTitle="Publier des applications avec Proxy de l’Application Azure AD | Microsoft Azure"
    description="Publier des applications en local dans le cloud avec Azure AD Application Proxy."
    services="active-directory"
    documentationCenter=""
    authors="kgremban"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="07/19/2016"
    ms.author="kgremban"/>


# <a name="publish-applications-using-azure-ad-application-proxy"></a>Publier des applications à l’aide de Proxy d’Application Azure AD

Azure AD Application Proxy vous aide à prendre en charge des travailleurs à distance en le publiant applications local accessible via internet. À ce stade, vous devez déjà avoir [activé le Proxy d’Application dans le portail classique Azure](active-directory-application-proxy-enable.md). Cet article vous explique comment procéder pour publier des applications qui sont exécutent sur votre réseau local et fournissent un accès sécurisé à distance à partir d’à l’extérieur de votre réseau. Après avoir terminé cet article, vous serez prêt à configurer l’application avec des informations personnalisées ou en matière de sécurité.

> [AZURE.NOTE] Proxy de l’application est une fonctionnalité qui est disponible uniquement si vous mis à niveau vers la Premium ou l’édition de base de Azure Active Directory. Pour plus d’informations, voir [éditions Azure Active Directory](active-directory-editions.md).

## <a name="publish-an-app-using-the-wizard"></a>Publier une application à l’aide de l’Assistant

1. Se connecter en tant qu’administrateur dans le [portail classique Azure](https://manage.windowsazure.com/).
2. Accédez à Active Directory et sélectionnez le répertoire où vous avez activé le Proxy d’Application.

    ![Active Directory - icône](./media/active-directory-application-proxy-publish/ad_icon.png)

3. Cliquez sur l’onglet **Applications** , puis cliquez sur le bouton **Ajouter** en bas de l’écran

    ![Ajouter une application](./media/active-directory-application-proxy-publish/aad_appproxy_selectdirectory.png)

4. Sélectionnez **publier une application qui seront accessible à partir en dehors de votre réseau**.

    ![Publier une application qui est accessible à partir d’à l’extérieur de votre réseau](./media/active-directory-application-proxy-publish/aad_appproxy_addapp.png)

5. Entrez les informations suivantes relatives à votre application :

    - **Nom**: le nom convivial pour votre application. Il doit être unique dans l’annuaire.
    - **URL interne**: l’adresse qui utilise le connecteur de Proxy d’Application pour accéder à l’application à partir de votre réseau privé. Vous pouvez fournir un chemin d’accès spécifique sur le serveur principal à publier, tandis que le reste du serveur est non publié. De cette façon, vous pouvez publier des sites différents sur le même serveur et chacune d’elles donner ses propres règles d’accès et le nom.

        > [AZURE.TIP] Si vous publiez un chemin d’accès, assurez-vous qu’il inclut toutes les images nécessaires, les scripts et les feuilles de style de votre application. Par exemple, si votre application se situe https://yourapp/app et utilise des images situés à https://yourapp/media, vous devez publier https://yourapp/ en tant que le chemin d’accès.

    - **Méthode de la pré-authentification**: comment Proxy de l’Application vérifie les utilisateurs avant qu’ils puissent accéder à votre application. Choisissez une des options dans le menu déroulant.

        - Azure Active Directory : Application Proxy redirige les utilisateurs à se connecter avec Azure AD, qui authentifie leurs autorisations pour le répertoire et l’application.
        - Relais : Les utilisateurs ne rencontrent s’authentifier pour accéder à l’application.

    ![Propriétés de l’application](./media/active-directory-application-proxy-publish/aad_appproxy_appproperties.png)  

6. Pour terminer l’Assistant, cliquez sur la coche située en bas de l’écran. L’application est maintenant définie dans Azure AD.


## <a name="assign-users-and-groups-to-the-application"></a>Affecter des utilisateurs et des groupes à l’application

Dans l’ordre de vos utilisateurs à accéder à votre application publiée, vous devez les affecter individuellement ou en groupes. (N’oubliez pas d’attribuez-vous access, trop). Cette fonctionnalité nécessite que chaque utilisateur dispose d’une licence pour Azure base ou version ultérieure. Vous pouvez attribuer des licences individuellement ou à des groupes. Pour plus d’informations, voir [attribution aux utilisateurs d’une application](active-directory-applications-guiding-developers-assigning-users.md) . 

Pour les applications qui nécessitent la pré-authentification, cette action accorde des autorisations pour utiliser l’application. Pour les applications qui ne nécessitent pas la pré-authentification, les utilisateurs peuvent toujours être affectées à l’application afin qu’il apparaisse dans la liste application, tel que mes applications.

1. Après avoir terminé l’Assistant Ajouter une application, vous voyez la page de démarrage rapide pour votre application. Pour gérer les personnes ayant accès à l’application, sélectionnez **utilisateurs et groupes**.

    ![Démarrage rapide de l’application Proxy attribuer aux utilisateurs - capture d’écran](./media/active-directory-application-proxy-publish/aad_appproxy_usersgroups.png)

2. Rechercher des groupes spécifiques dans votre annuaire, ou afficher tous vos utilisateurs. Pour afficher les résultats de recherche, cliquez sur la coche.

    ![Rechercher des groupes ou des utilisateurs - capture d’écran](./media/active-directory-application-proxy-publish/aad_appproxy_search.png)

2. Sélectionnez chaque utilisateur ou groupe que vous voulez affecter à cette application, puis cliquez sur **attribuer**. Vous êtes invité à confirmer cette action.

> [AZURE.NOTE] Pour les applications de l’authentification Windows intégrée, vous pouvez affecter uniquement les utilisateurs et groupes qui sont synchronisées à partir de votre locale d’Active Directory. Les utilisateurs qui vous connecter avec un compte Microsoft et invités ne peuvent pas être affectées pour les applications publiées avec Azure Active Directory Application Proxy. Vérifiez que vos utilisateurs connectez-vous à l’aide des informations d’identification qui font partie du même domaine que l’application que vous publiez.

## <a name="test-your-published-application"></a>Tester votre application publiée

Une fois que vous avez publié votre application, vous pouvez tester indépendante en accédant à l’URL que vous avez publié. Vérifiez que vous pouvez y accéder, qu’il affiche correctement, et que tout fonctionne comme prévu. Si vous rencontrez des problèmes ou obtenez un message d’erreur, essayez le [guide de dépannage](active-directory-application-proxy-troubleshoot.md).

## <a name="configure-your-application"></a>Configurez votre application

Vous pouvez modifier les applications publiées ou définir les options avancées dans la page Configurer. Dans cette page, vous pouvez personnaliser votre application par la modification du nom ou le chargement d’un logo. Vous pouvez également gérer les règles d’accès à la méthode pré-authentification ou de l’authentification multifacteur.

![Configuration avancée](./media/active-directory-application-proxy-publish/aad_appproxy_configure.png)


Une fois que vous publiez des applications à l’aide de Proxy d’Application Azure Active Directory, ils apparaissent dans la liste des Applications dans Azure Active Directory, et vous pouvez les gérer il.

Si vous désactivez les services Proxy de l’Application une fois que vous avez publié des applications, ils ne sont plus accessibles à partir d’à l’extérieur de votre réseau privé. Cela ne supprime pas les applications.

Pour afficher une application et vérifiez qu’il n’est accessible, double-cliquez sur le nom de l’application. Si le service Proxy de l’Application est désactivé et l’application n’est pas disponible, un message d’avertissement s’affiche dans la partie supérieure de l’écran.

Pour supprimer une application, sélectionnez une application dans la liste, puis cliquez sur **Supprimer**.

## <a name="next-steps"></a>Étapes suivantes

- [Publier des applications à l’aide de votre propre nom de domaine](active-directory-application-proxy-custom-domains.md)
- [Activer l’authentification unique](active-directory-application-proxy-sso-using-kcd.md)
- [Activer l’accès conditionnel](active-directory-application-proxy-conditional-access.md)
- [Utilisation des applications prenant en charge sur les revendications](active-directory-application-proxy-claims-aware-apps.md)

Pour les dernières nouvelles et mises à jour, consultez le [blog de Proxy de l’Application](http://blogs.technet.com/b/applicationproxyblog/)

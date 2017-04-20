<properties
    pageTitle="Conditions requises pour accéder à l’annonce Azure API de création de rapports. | Microsoft Azure"
    description="En savoir plus sur les conditions requises pour accéder à l’annonce Azure API de création de rapports"
    services="active-directory"
    documentationCenter=""
    authors="dhanyahk"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="identity"
    ms.date="09/25/2016"
    ms.author="dhanyahk;markvi"/>

# <a name="prerequisites-to-access-the-azure-ad-reporting-api"></a>Conditions préalables pour accéder à l’annonce Azure API de création de rapports 

[Azure AD API de rapport](https://msdn.microsoft.com/library/azure/ad/graph/howto/azure-ad-reports-and-events-preview) vous permettre d’accéder par programme aux données via un ensemble d’API basées sur le reste. Vous pouvez appeler ces API à partir d’une variété de langues et outils de programmation.

L’API de création de rapports utilise [OAuth](https://msdn.microsoft.com/library/azure/dn645545.aspx) pour autoriser l’accès à l’API web. 

Pour préparer votre accès à l’API de création de rapports, vous devez :

1. Créer une application dans votre client Azure AD 

2. Accorder des autorisations appropriées application pour accéder aux données Azure AD

3. Collectez les paramètres de configuration de votre annuaire

Vous avez des questions, problèmes ou des commentaires, veuillez contacter [AAD Reporting aide](mailto:aadreportinghelp@microsoft.com).


## <a name="create-an-azure-ad-application"></a>Créer une application Azure AD

Pour configurer votre annuaire pour accéder à l’annonce Azure API de création de rapports, vous devez vous connecter au portail classique Azure avec un compte d’administrateur abonnement Azure qui est également un membre du rôle Administrateur général répertoire de votre client Azure AD.

> [AZURE.IMPORTANT] Applications qui s’exécutent sous informations d’identification avec des privilèges « administrateur » à ceci peuvent être très puissantes, donc n’oubliez pas de protéger les informations d’identification de l’application ID/secret.


1. Dans le [portail classique Azure](https://manage.windowsazure.com), dans le volet de navigation gauche, cliquez sur **Active Directory**.

    ![Enregistrement de l’application](./media/active-directory-reporting-api-prerequisites/01.png) 

2. Dans la liste **active directory** , sélectionnez dans l’annuaire.

3. Dans le menu dans la partie supérieure, cliquez sur **Applications**.

    ![Enregistrement de l’application](./media/active-directory-reporting-api-prerequisites/02.png) 

4. Dans la barre inférieure, cliquez sur **Ajouter**.

    ![Enregistrement de l’application](./media/active-directory-reporting-api-prerequisites/03.png) 

5. Sur le **que voulez-vous faire ?** boîte de dialogue, cliquez sur **Ajouter une application de développe de mon organisation**. 

    ![Enregistrement de l’application](./media/active-directory-reporting-api-prerequisites/04.png) 


6. Dans la boîte de dialogue **donner à propos de votre application** , effectuez les opérations suivantes : 

    ![Enregistrement de l’application](./media/active-directory-reporting-api-prerequisites/05.png) 

    un. Dans la zone de texte **nom** , tapez un nom (par exemple : Application de l’API de rapports).

    b. Sélectionnez **une application Web et/ou API web**.

    c. Cliquez sur **suivant**.


7. Dans la boîte de dialogue **Propriétés de l’application** , effectuez les opérations suivantes : 

    ![Enregistrement de l’application](./media/active-directory-reporting-api-prerequisites/06.png) 

    un. Dans la zone de texte **URL authentification** , tapez `https://localhost`.

    b. Dans la zone de texte **URI ID de l’application** , tapez ```https://localhost/ReportingApiApp```.

    c. Cliquez sur **terminé**.



## <a name="grant-your-application-permission-to-use-the-api"></a>Autoriser l’utilisation de l’API votre application

1. Dans le [portail classique Azure](https://manage.windowsazure.com/), dans le volet de navigation gauche, cliquez sur **Active Directory**.

    ![Enregistrement de l’application](./media/active-directory-reporting-api-prerequisites/01.png) 

2. Dans la liste **active directory** , sélectionnez dans l’annuaire.

3. Dans le menu dans la partie supérieure, cliquez sur **Applications**.

    ![Enregistrement de l’application](./media/active-directory-reporting-api-prerequisites/02.png)


3. Dans la liste des applications, sélectionnez votre application nouvellement créée.

    ![Enregistrement de l’application](./media/active-directory-reporting-api-prerequisites/07.png)

4. Dans le menu dans la partie supérieure, cliquez sur **configurer**.

    ![Enregistrement de l’application](./media/active-directory-reporting-api-prerequisites/08.png)


5. Dans la section **autorisations à d’autres applications** , pour la ressource **Azure Active Directory** , cliquez sur la liste déroulante **Autorisations des applications** , puis sur **lire les données de répertoire**.

    ![Enregistrement de l’application](./media/active-directory-reporting-api-prerequisites/09.png)


5. Dans la barre inférieure, cliquez sur **Enregistrer**.

    ![Enregistrement de l’application](./media/active-directory-reporting-api-prerequisites/10.png)


## <a name="gather-configuration-settings-from-your-directory"></a>Collectez les paramètres de configuration de votre annuaire

Cette section vous montre comment obtenir les paramètres suivants à partir de votre annuaire :

- Nom de domaine
- ID de client
- Secret client

Vous avez besoin de ces valeurs lors de la configuration des appels à l’API de création de rapports. 


### <a name="get-your-domain-name"></a>Obtenir votre nom de domaine

1. Dans le [portail classique Azure](https://manage.windowsazure.com), dans le volet de navigation gauche, cliquez sur **Active Directory**.

    ![Enregistrement de l’application](./media/active-directory-reporting-api-prerequisites/01.png) 

2. Dans la liste **active directory** , sélectionnez dans l’annuaire.

3. Dans le menu dans la partie supérieure, cliquez sur **domaines**.

    ![Enregistrement de l’application](./media/active-directory-reporting-api-prerequisites/11.png) 

4. Dans la colonne **Nom de domaine** , copiez votre nom de domaine.

    ![Enregistrement de l’application](./media/active-directory-reporting-api-prerequisites/12.png) 


### <a name="get-the-applications-client-id"></a>Obtenir l’ID de client de l’application

1. Dans le [portail classique Azure](https://manage.windowsazure.com), dans le volet de navigation gauche, cliquez sur **Active Directory**.

    ![Enregistrement de l’application](./media/active-directory-reporting-api-prerequisites/01.png) 

2. Dans la liste **active directory** , sélectionnez dans l’annuaire.

3. Dans le menu dans la partie supérieure, cliquez sur **Applications**.

    ![Enregistrement de l’application](./media/active-directory-reporting-api-prerequisites/02.png) 

4. Dans la liste des applications, sélectionnez votre application nouvellement créée.

    ![Enregistrement de l’application](./media/active-directory-reporting-api-prerequisites/07.png)

4. Dans le menu dans la partie supérieure, cliquez sur **configurer**.

    ![Enregistrement de l’application](./media/active-directory-reporting-api-prerequisites/08.png)

4. Copiez votre **ID Client**.

    ![Enregistrement de l’application](./media/active-directory-reporting-api-prerequisites/13.png)


### <a name="get-the-applications-client-secret"></a>Obtenir secret du client de l’application

Pour obtenir secret du client de votre application, vous devez créer une nouvelle clé et enregistrer sa valeur lors de l’enregistrement de la nouvelle clé, car il n’est pas possible de récupérer cette valeur plus tard plus.

1. Dans le [portail classique Azure](https://manage.windowsazure.com), dans le volet de navigation gauche, cliquez sur **Active Directory**.

    ![Enregistrement de l’application](./media/active-directory-reporting-api-prerequisites/01.png) 

2. Dans la liste **active directory** , sélectionnez dans l’annuaire.

3. Dans le menu dans la partie supérieure, cliquez sur **Applications**.

    ![Enregistrement de l’application](./media/active-directory-reporting-api-prerequisites/02.png) 

4. Dans la liste des applications, sélectionnez votre application nouvellement créée.

    ![Enregistrement de l’application](./media/active-directory-reporting-api-prerequisites/07.png)

4. Dans le menu dans la partie supérieure, cliquez sur **configurer**.

    ![Enregistrement de l’application](./media/active-directory-reporting-api-prerequisites/08.png)

5. Dans la section **clés** , procédez comme suit : 

    ![Enregistrement de l’application](./media/active-directory-reporting-api-prerequisites/14.png)

    un. Dans la liste durée, sélectionnez une durée

    b. Dans la barre inférieure, cliquez sur **Enregistrer**.

    ![Enregistrement de l’application](./media/active-directory-reporting-api-prerequisites/10.png)

    c. Copiez la valeur de clé.

## <a name="next-steps"></a>Étapes suivantes

- Vous voulez accéder aux données à partir de l’annonce Azure reporting API de manière programmée ? Consultez [prise en main avec l’API de création de rapports de répertoire Azure Active](active-directory-reporting-api-getting-started.md).

- Si vous voulez en savoir plus sur les rapports d’Azure Active Directory, consultez le [Guide Azure Active Directory création de rapports](active-directory-reporting-guide.md).  

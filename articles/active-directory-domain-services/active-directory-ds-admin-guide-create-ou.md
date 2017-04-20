<properties
    pageTitle="Azure Active Directory Domain Services : Guide d’Administration | Microsoft Azure"
    description="Créer une unité d’organisation (OU) sur les Services de domaine Active Directory Azure gérées domaines"
    services="active-directory-ds"
    documentationCenter=""
    authors="mahesh-unnikrishnan"
    manager="stevenpo"
    editor="curtand"/>

<tags
    ms.service="active-directory-ds"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/21/2016"
    ms.author="maheshu"/>

# <a name="create-an-organizational-unit-ou-on-an-azure-ad-domain-services-managed-domain"></a>Créer une unité d’organisation (OU) sur un domaine géré Azure Active Directory Domain Services
Azure domaines gérés les Services de domaine Active Directory incluent deux conteneurs prédéfinis appelés « AADDC utilisateurs » et « AADDC ordinateurs » respectivement. Le conteneur 'Ordinateurs AADDC' comporte les objets ordinateur pour tous les ordinateurs qui sont jointes au domaine géré. Le conteneur 'AADDC les utilisateurs' contient des utilisateurs et groupes dans le client Azure AD. Parfois, il peut être nécessaire de créer des comptes de service sur le domaine géré pour déployer les charges de travail. Pour cela, vous pouvez créer une unité d’organisation personnalisé (OU) sur le domaine géré et créer des comptes de service dans cette unité d’organisation. Cet article vous explique comment créer une unité d’organisation dans votre domaine géré.


## <a name="install-ad-administration-tools-on-a-domain-joined-virtual-machine-for-remote-administration"></a>Installer les outils d’administration AD sur une machine virtuelle à un domaine pour l’administration à distance
Azure domaines gérés les Services de domaine Active Directory peuvent être gérés à distance à l’aide des outils d’administration Active Directory familiers tel que Active Directory Administration Centre (ADAC) ou AD PowerShell. Administrateurs client n’ont pas de privilèges pour vous connecter risquez sur le domaine géré via le Bureau à distance. Pour administrer le domaine géré, installez la fonctionnalité d’Outils d’administration AD sur une machine virtuelle rejoint le domaine géré. Consultez l’article intitulé [administrer un domaine Azure Active Directory Domain Services gérés](active-directory-ds-admin-guide-administer-domain.md) pour obtenir des instructions.

## <a name="create-an-organizational-unit-on-the-managed-domain"></a>Créer une unité d’organisation sur le domaine géré
À présent que les outils d’administration AD sont installés sur le domaine rejoint machine virtuelle, nous pouvons utiliser ces outils pour créer une unité d’organisation sur le domaine géré. Effectuez les opérations suivantes :

> [AZURE.NOTE] Seuls les membres du groupe « Administrateurs de DC AAD » ont les privilèges requis pour créer une unité d’organisation personnalisée. Assurez-vous que vous effectuez les opérations suivantes en tant qu’utilisateur appartenant à ce groupe.

1. À partir de l’écran d’accueil, cliquez sur **Outils d’administration**. Vous devriez voir les outils d’administration AD installés sur l’ordinateur virtuel.

    ![Outils d’administration installées sur serveur](./media/active-directory-domain-services-admin-guide/install-rsat-admin-tools-installed.png)

2. Cliquez sur **Centre d’administration Active Directory**.

    ![Centre d’administration d’Active Directory](./media/active-directory-domain-services-admin-guide/adac-overview.png)

3. Pour afficher le domaine, cliquez sur le nom de domaine dans le volet gauche (par exemple, « contoso100.com »).

    ![ADAC - domaine d’affichage](./media/active-directory-domain-services-admin-guide/create-ou-adac-overview.png)

4. Dans le volet de **tâches** à droite, cliquez sur **Nouveau** sous le nœud de nom de domaine. Dans cet exemple, nous cliquez sur **Nouveau** sous le nœud « contoso100(local) » dans le volet de **tâches** à droite.

    ![ADAC - nouvelle unité d’organisation](./media/active-directory-domain-services-admin-guide/create-ou-adac-new-ou.png)

5. Vous devez voir l’option pour créer une unité d’organisation. Cliquez sur l' **Unité d’organisation** pour lancer la boîte de dialogue **Créer unité d’organisation** .

6. Dans la boîte de dialogue **Créer unité d’organisation** , spécifiez un **nom** pour la nouvelle unité d’organisation. Fournir une brève description de l’unité d’organisation. Vous pouvez également définir le champ **Géré par** pour l’unité d’organisation. Pour créer l’unité d’organisation personnalisée, cliquez sur **OK**.

    ![ADAC - boîte de dialogue unité d’organisation créer](./media/active-directory-domain-services-admin-guide/create-ou-dialog.png)

7. L’unité d’organisation nouvellement créée doit maintenant apparaître dans le centre d’administration Active Directory (ADAC).

    ![ADAC - unité d’organisation créée](./media/active-directory-domain-services-admin-guide/create-ou-done.png)


## <a name="permissionssecurity-for-newly-created-ous"></a>Sécurité/des autorisations pour les unités d’organisation nouvellement créées
Par défaut, l’utilisateur qui a créé l’unité d’organisation personnalisée (membre du groupe « Administrateurs de DC AAD ») est accordé (contrôle total) des privilèges d’administrateur sur l’unité d’organisation. L’utilisateur peut ensuite alors et accordez des privilèges à d’autres utilisateurs ou au groupe « AAD DC administrateurs » comme vous le souhaitez. Comme illustré dans l’écran suivant, l’utilisateur 'bob@domainservicespreview.onmicrosoft.com' qui a créé l’unité d’organisation 'MyCustomOU' est accordé un contrôle total sur celui-ci.

 ![ADAC - nouvelle sécurité unité d’organisation](./media/active-directory-domain-services-admin-guide/create-ou-permissions.png)


## <a name="notes-on-administering-custom-ous"></a>Notes sur l’administration des unités d’organisation personnalisées
Maintenant que vous avez créé une unité d’organisation personnalisée, vous pouvez alors et créer des utilisateurs, des groupes, des ordinateurs et des comptes de service dans cette unité d’organisation. Impossible de déplacer les utilisateurs ou groupes aux « AAD DC utilisateurs' unité d’organisation aux unités d’organisation personnalisées.

> [AZURE.WARNING] Comptes d’utilisateurs, des groupes, des comptes de service et objets ordinateur que vous créez sous unités d’organisation personnalisées ne sont pas disponibles dans votre client Azure AD. En d’autres termes, ces objets n’apparaissent pas à l’aide de l’API Azure AD graphique ou dans l’interface utilisateur AD Azure. Ces objets sont uniquement disponibles dans votre domaine géré Azure Active Directory Domain Services.


## <a name="related-content"></a>Contenu associé

- [Administrer un domaine géré Azure Active Directory Domain Services](active-directory-ds-admin-guide-administer-domain.md)

- [Centre d’administration Active Directory : Prise en main](https://technet.microsoft.com/library/dd560651.aspx)

- [Guide étape par étape des comptes de service](https://technet.microsoft.com/library/dd548356.aspx)

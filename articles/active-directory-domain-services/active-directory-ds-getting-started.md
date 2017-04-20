<properties
    pageTitle="Les Services de domaine Active Directory Azure : Créer le groupe AAD DC administrateurs | Microsoft Azure"
    description="Prise en main Azure Active Directory Domain Services"
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
    ms.date="10/03/2016"
    ms.author="maheshu"/>

# <a name="get-started-with-azure-ad-domain-services"></a>Prise en main des Services de domaine Active Directory Azure

Cet article décrit les tâches de configuration nécessaires pour activer les Services de domaine Active Directory Azure pour votre client Azure AD.

## <a name="task-1-create-the-aad-dc-administrators-group"></a>Tâche 1 : Créer le groupe « AAD DC administrateurs »
La première tâche consiste à créer un groupe d’administration dans votre client Azure Active Directory. Ce groupe d’administration spécial est appelé **AAD DC administrateurs**. Les membres de ce groupe bénéficient des privilèges d’administrateur sur les ordinateurs qui sont associés à un domaine pour le domaine géré Azure Active Directory Domain Services. Sur les ordinateurs à un domaine, ce groupe est ajouté au groupe « Administrateurs ». En outre, les membres de ce groupe peuvent utiliser Bureau à distance pour se connecter à distance aux ordinateurs à un domaine.  

> [AZURE.NOTE] Vous ne disposez pas des privilèges administrateur de domaine ou d’administrateur d’entreprise sur le domaine géré créé à l’aide des Services de domaine Active Directory Azure. Sous domaines gérés, ces privilèges sont réservés par le service et ne seront pas disponibles pour les utilisateurs dans le client. Toutefois, vous pouvez utiliser le groupe Administrateurs spécial créé dans cette tâche de configuration à effectuer des opérations dotés de privilèges. Ces opérations comprennent joindre des ordinateurs au domaine, appartenant au groupe d’administrateurs sur les ordinateurs à un domaine, etc. de stratégie de groupe de configuration.

Dans cette tâche de configuration, vous créez le groupe d’administration et ajoutez un ou plusieurs utilisateurs dans votre annuaire au groupe. Procédez comme suit pour créer le groupe d’administration pour les Services de domaine Active Directory Azure :

1. Accédez au **portail classique Azure** ([https://manage.windowsazure.com](https://manage.windowsazure.com))

2. Sélectionnez le nœud **Active Directory** dans le volet gauche.

3. Sélectionnez le client Azure AD (répertoire) pour laquelle vous voulez activer les Services de domaine Active Directory Azure. Vous ne pouvez créer un domaine pour chaque répertoire Azure AD.

    ![Sélectionnez Azure AD répertoire](./media/active-directory-domain-services-getting-started/select-aad-directory.png)

4. Cliquez sur l’onglet **groupes** .

5. Pour ajouter un groupe à votre client Azure AD, cliquez sur **Ajouter un groupe** à partir du volet de tâches en bas de la page.

    ![Ajouter le bouton Grouper](./media/active-directory-domain-services-getting-started/add-group-button.png)

6. Créer un groupe intitulé **AAD DC administrateurs**. La valeur **TYPE de groupe de** **sécurité**.

    > [AZURE.WARNING] Pour activer l’accès au sein de vos Services de domaine Active Directory Azure gérées domaine, créez un groupe avec ce nom exact.

    ![Créer le groupe Administrateurs](./media/active-directory-domain-services-getting-started/create-admin-group.png)

7. Ajoutez une description de ce groupe afin que d’autres personnes comprennent que ce groupe est utilisé pour accorder des privilèges d’administrateur au sein des Services de domaine Active Directory Azure.

8. Une fois que le groupe a été créé, cliquez sur le nom du groupe pour afficher les propriétés de ce groupe. Pour ajouter des utilisateurs en tant que membres de ce groupe, cliquez sur le bouton **Ajouter des membres** dans le volet inférieur.

    ![Groupe membres bouton Ajouter](./media/active-directory-domain-services-getting-started/add-group-members-button.png)

9. Dans la boîte de dialogue **Ajouter des membres** , sélectionnez les utilisateurs qui doivent être membres de ce groupe et activez la case à cocher lorsque vous avez terminé.

    ![Ajouter des utilisateurs au groupe d’administrateurs](./media/active-directory-domain-services-getting-started/add-group-members.png)

<br>

## <a name="task-2-create-or-select-an-azure-virtual-network"></a>Tâche 2 : Créer ou sélectionner un réseau virtuel Azure
La tâche de configuration suivante consiste à [créer ou sélectionner un réseau virtuel Azure](active-directory-ds-getting-started-vnet.md).

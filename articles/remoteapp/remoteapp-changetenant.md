
<properties
    pageTitle="Modifier le client Azure Active Directory dans Azure RemoteApp | Microsoft Azure"
    description="Découvrez comment modifier le client Azure Active Directory associé RemoteApp Azure"
    services="remoteapp"
    documentationCenter=""
    authors="lizap"
    manager="mbaldwin" />

<tags
    ms.service="remoteapp"
    ms.workload="compute"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/15/2016"
    ms.author="elizapo" />



# <a name="change-the-azure-active-directory-tenant-in-azure-remoteapp"></a>Modifier le client Azure Active Directory dans Azure RemoteApp

> [AZURE.IMPORTANT]
> RemoteApp Azure a été interrompu. Lire l' [annonce](https://go.microsoft.com/fwlink/?linkid=821148) pour plus d’informations.

RemoteApp Azure utilise Azure Active Directory (AD Azure) pour permettre l’accès des utilisateurs. Le client uniquement Azure AD que vous pouvez utiliser dans Azure RemoteApp est celui associé à l’abonnement Azure. Vous pouvez afficher l’abonnement associée dans la page **paramètres** du portail. Examinez la colonne **répertoire** sous l’onglet **abonnements** .

> [AZURE.NOTE] Pour que cette modification réussir, tout d’abord supprimer tous les utilisateurs à partir du client Azure Active Directory existant à partir de toutes les collections de RemoteApp Azure. Pour ce faire, accédez au portail Azure, accédez à l’onglet **Azure RemoteApp** et ouvrez chaque collection de sites RemoteApp Azure. Accédez à l’onglet **utilisateurs** et supprimer des utilisateurs qui appartiennent à votre client Azure Active Directory en cours. Répétez pour toutes les collections d’Azure RemoteApp existantes. Sans effectuer cette opération, vous ne pouvez pas créer ou corriger collections.

Si vous souhaitez utiliser un autre client, suivez ces étapes pour modifier l’association avec votre abonnement :

1. Dans le portail, supprimer d’utilisateurs Azure AD dont vous avez accordé l’accès aux collections RemoteApp Azure. (Voir la Remarque ci-dessus pour connaître les étapes dans cette procédure).


2. Configurer un compte Microsoft (anciennement appelé un identifiant Live ID) en tant que l’administrateur de Service. (Ne savez pas si vous êtes déjà l’administrateur de service ? Vous trouverez en cliquant sur **Paramètres -> administrateurs**.) À présent, voici comment procéder qui :
    1. Cliquez sur l’utilisateur dans le coin supérieur droit, puis cliquez sur **lire votre facture**.
    2. Cliquez sur l’abonnement. Puis, dans la page Nouveau, faites défiler vers le bas, puis cliquez sur **Modifier les détails de l’abonnement** droite. (Trier du bas au milieu à droite, si cela vous aide à trouver.)
    3. Tapez le compte Microsoft pour l’utilisateur qui doit être l’administrateur de service.

3. À présent, vous déconnecter du portail, puis connectez-vous avec le compte Microsoft que vous avez indiquée à l’étape précédente.


4. Cliquez sur **Nouveau -> application Services -> Active Directory -> répertoire -> personnalisé créer**.
5. Sous **annuaire**, sélectionnez **utiliser un répertoire existant**. Nous allons devoir que vous vous déconnecter le portail maintenant, sélectionnez **que je suis prêt à être déconnecté maintenant**.
6. Vous reconnecter au portail en tant qu’un administrateur global du répertoire que vous voulez ajouter. (Si vous n’ont pas été déjà un administrateur général, vous serez après un tour de se connecter, puis sur se déconnecter.)
7. Vous serez invité lorsque vous vous connectez si vous voulez utiliser votre client Active Directory existant avec votre abonnement. Cliquez sur **Continuer**, puis cliquez sur **se déconnecter maintenant**.
5. Se reconnecter et revenir aux **Paramètres -> abonnements**. Sélectionnez votre abonnement, puis cliquez sur **Modifier le répertoire**. Sélectionnez le client Azure AD que vous voulez utiliser.



Vous pouvez client d’utiliser la nouvelle annonce Azure maintenant pour contrôler l’accès à l’abonnement Azure et configurer l’accès utilisateur dans Azure RemoteApp.

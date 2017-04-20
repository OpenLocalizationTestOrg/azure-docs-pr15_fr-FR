<properties
   pageTitle="Gérer l’annuaire pour votre abonnement Office 365 dans Azure | Microsoft Azure"
   description="Gestion d’un répertoire des abonnements Office 365 à l’aide du portail de classique Azure et Azure Active Directory"
   services="active-directory"
   documentationCenter=""
   authors="curtand"
   manager="femila"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="08/23/2016"
   ms.author="curtand"/>

# <a name="manage-the-directory-for-your-office-365-subscription-in-azure"></a>Gérer l’annuaire pour votre abonnement Office 365 dans Azure

Cet article décrit comment gérer un répertoire qui a été créé pour un abonnement Office 365 à l’aide du portail classique Azure. Vous devez être l’administrateur de Service ou un administrateur de co-création d’un abonnement Azure pour vous connecter au portail classique Azure. Si vous n’avez encore un abonnement Azure, vous pouvez vous inscrire une [version d’évaluation de 30 jours gratuite](https://azure.microsoft.com/trial/get-started-active-directory/) aujourd'hui et déployer votre première solution cloud en sous 5 minutes, à l’aide de ce lien. Veillez à utiliser le compte professionnel ou scolaire que vous utilisez pour vous connecter à Office 365.

Après avoir terminé l’abonnement Azure, vous pouvez connecter au portail classique Azure et accéder aux services Azure. Cliquez sur l’extension de Active Directory pour gérer le même répertoire qui authentifie les utilisateurs d’Office 365.

Si vous avez encore un abonnement Azure, la procédure à suivre pour gérer un répertoire supplémentaire est aussi simple. Par exemple, Michael Smith peut-être un abonnement Office 365 pour Contoso.com. Il est également un abonnement Azure qu’il inscrit à l’aide de son compte Microsoft, msmith@hotmail.com. Dans ce cas, il gère les deux répertoires.

  Abonnement |  Office 365  |  Azure
  -------------- | ------------- | -------------------------------
  Nom d’affichage |  Contoso  |     Répertoire Azure Active Directory (AD Azure) par défaut
  Nom de domaine  |  Contoso.com  | msmithhotmail.onmicrosoft.com

Il souhaite gérer l’identité utilisateur dans le répertoire Contoso pendant qu’il est connecté à Azure à l’aide de son compte Microsoft, afin qu’il peut activer des fonctionnalités de Azure AD tels que l’authentification multifacteur. Le diagramme suivant peut-être vous aider à illustrer le processus.

![Diagramme pour gérer les deux répertoires indépendants](./media/active-directory-manage-o365-subscription/AAD_O365_03.png)

Dans ce cas, les deux annuaires sont indépendants entre eux.

## <a name="to-manage-two-independent-directories"></a>Pour gérer les deux répertoires indépendants
Dans l’ordre de Michael Smith gérer les deux annuaires pendant qu’il est connecté à Azure en tant que msmith@hotmail.com, il doit effectuer les opérations suivantes :

> [AZURE.NOTE]
> Ces étapes peuvent être exécutées que si un utilisateur est connecté avec un compte Microsoft. Si l’utilisateur est connecté avec un professionnel ou scolaire, l’option **d’utilisation du répertoire existant** n’est pas disponible. Un compte professionnel ou scolaire peut être authentifié uniquement par son répertoire de base (autrement dit, le répertoire où le compte professionnel ou scolaire est stocké et propriétaire de l’entreprise ou établissement scolaire).

1.  Connectez-vous au [portail classique Azure](https://manage.windowsazure.com) en tant que msmith@hotmail.com.
2.  Cliquez sur **Nouveau** > **services application** > **Active Directory** > **répertoire** > **Créer personnalisé**.
3.  Cliquez sur utilisation du répertoire existant et activez la case à cocher **je suis prêt à être déconnecté maintenant** .
4.  Connectez-vous au portail classique Azure en tant qu’administrateur global de Contoso.onmicrosoft.com (par exemple, msmith@contoso.com).
5.  Lorsque vous êtes invité à **utiliser le répertoire Contoso avec Azure ?**, cliquez sur **Continuer**.
6.  Cliquez sur **se déconnecter maintenant**.
7.  Connectez-vous au portail classique Azure en tant que msmith@hotmail.com. Le répertoire Contoso et le répertoire par défaut s’affichent dans l’extension Active Directory.

Après avoir effectué ces étapes, msmith@hotmail.com est un administrateur global dans l’annuaire de Contoso.

## <a name="to-administer-resources-as-the-global-admin"></a>Administrer des ressources en tant que l’administrateur global
Maintenant Supposons que Jeanne Dupont doit administrer les ressources de sites Web et la base de données qui sont associés à l’abonnement Azure pour msmith@hotmail.com. Elle peut faire qui, Michael Smith doit effectuer ces étapes supplémentaires :

1.  Connectez-vous au [portail classique Azure](https://manage.windowsazure.com) à l’aide du compte d’administrateur de Service pour l’abonnement Azure (dans cet exemple, msmith@hotmail.com).
2.  Transférer l’abonnement à l’annuaire de Contoso : cliquez sur **paramètres** > **abonnements** > sélectionnez l’abonnement > **Modifier le répertoire** > sélectionnez **Contoso (Contoso.com)**. Dans le cadre de transfert, n’importe quel professionnel ou scolaire comptes qui sont coadministrateurs de l’abonnement sont supprimés.
3.  Ajouter Jeanne Dupont en tant qu’administrateur Co-création de l’abonnement : cliquez sur **paramètres** > **administrateurs** > sélectionnez l’abonnement > **Ajouter** > type **JohnDoe@Contoso.com**.

## <a name="next-steps"></a>Étapes suivantes
Pour plus d’informations sur la relation entre les abonnements et des répertoires, voir [comment un abonnement est associé à un répertoire](active-directory-how-subscriptions-associated-directory.md).

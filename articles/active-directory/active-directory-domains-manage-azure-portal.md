<properties
    pageTitle="Gestion des noms de domaine personnalisé dans votre aperçu Azure Active Directory | Microsoft Azure"
    description="Concepts de gestion et procédures pour la gestion d’un nom de domaine dans Azure Active Directory"
    services="active-directory"
    documentationCenter=""
    authors="jeffsta"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/12/2016"
    ms.author="curtand;jeffsta"/>

# <a name="managing-custom-domain-names-in-your-azure-active-directory-preview"></a>Gestion des noms de domaine personnalisé dans votre aperçu Azure Active Directory

Un nom de domaine est une partie importante de l’identificateur pour de nombreuses ressources annuaire : il fait partie d’une adresse de messagerie ou le nom d’utilisateur pour un utilisateur, partie de l’adresse d’un groupe et peut faire partie de l’application URI ID pour une application. Une ressource dans l’aperçu Azure Active Directory (AD Azure) peut inclure un nom de domaine est vérifié déjà appartenir par le répertoire qui contient la ressource. [Nouveautés dans l’aperçu](active-directory-preview-explainer.md) Seul un administrateur global peut effectuer les tâches de gestion de domaine dans Azure AD.

## <a name="set-the-primary-domain-name-for-your-azure-ad-directory"></a>Définir le nom de domaine principal pour votre annuaire Azure AD

Lorsque votre répertoire est créé, le nom de domaine initial, par exemple « contoso.onmicrosoft.com », est également le nom de domaine principal. Le domaine principal est le nom de domaine par défaut pour un nouvel utilisateur lorsque vous créez un nouvel utilisateur. Cela simplifie le processus d’un administrateur créer des utilisateurs dans le portail. Pour modifier le nom de domaine principal :

1.  Connectez-vous au [portail Azure](https://portal.azure.com) avec un compte qui est un administrateur global pour l’annuaire.

2.  Sélectionnez **plusieurs services**, entrez **Azure Active Directory** dans la zone de texte, puis **entrée**.

    ![Gestion des utilisateurs de l’ouverture](./media/active-directory-domains-add-azure-portal/user-management.png)

3. Dans la carte de ***nom du répertoire*** , sélectionnez **les noms de domaine**.

4. Dans la carte de ** *nom du répertoire* : noms de domaine** , sélectionnez le nom de domaine que vous voulez que le nom de domaine principal.

5.  Dans la carte ***nom_domaine*** (autrement dit, la carte qui s’ouvre avec le nouveau nom de domaine dans le titre), sélectionnez la commande **Rendre principal** . Confirmez votre choix lorsque vous y êtes invité.

    ![Rendre un nom de domaine principal](./media/active-directory-domains-manage-azure-portal/make-primary.png)

Vous pouvez modifier le nom de domaine principal pour votre répertoire à n’importe quel domaine vérifié personnalisé qui n’est pas fédéré. Modifier le domaine principal pour votre annuaire changera pas les noms d’utilisateur pour tous les utilisateurs existants.

## <a name="add-custom-domain-names-to-your-azure-ad"></a>Ajouter des noms de domaine personnalisé pour votre annonce Azure

Vous pouvez ajouter jusqu'à 900 noms de domaine personnalisé à chaque répertoire Azure AD. La procédure à suivre pour [Ajouter un nom de domaine personnalisé supplémentaire](active-directory-domains-add-azure-portal.md) est identique pour le premier nom de domaine personnalisé.

## <a name="add-subdomains-of-a-custom-domain"></a>Ajouter des sous-domaines d’un domaine personnalisé

Si vous voulez ajouter un nom de domaine de troisième niveau tel que « europe.contoso.com » à votre annuaire, vous devez tout d’abord ajouter et vérifier le domaine de second niveau, tel que contoso.com. Le sous-domaine est automatiquement vérifié par Azure Active Directory. Pour vérifier que le sous-domaine que vous venez d’ajouter a été confirmé, actualisez la page dans le navigateur qui répertorie les domaines.

## <a name="what-to-do-if-you-change-the-dns-registrar-for-your-custom-domain-name"></a>Que faire si vous changez le bureau d’enregistrement DNS pour votre nom de domaine personnalisé

Si vous changez le bureau d’enregistrement DNS pour votre nom de domaine personnalisé, vous pouvez continuer à utiliser votre nom de domaine personnalisé avec Azure AD elle-même sans interruption et sans autres tâches de configuration. Si vous utilisez votre nom de domaine personnalisé avec Office 365, Intune ou autres services qui s’appuient sur les noms de domaine personnalisé dans Azure Active Directory, reportez-vous à la documentation de ces services.

## <a name="delete-a-custom-domain-name"></a>Supprimer un nom de domaine personnalisé

Vous pouvez supprimer un nom de domaine personnalisé à partir de votre annonce Azure si votre organisation n’utilise plus ce nom de domaine, ou si vous devez utiliser ce nom de domaine à un autre annonce Azure.

Pour supprimer un nom de domaine personnalisé, vous devez tout d’abord vous assurer qu’aucune ressource dans votre annuaire s’appuient sur le nom de domaine. Vous ne pouvez pas supprimer un nom de domaine à partir de votre annuaire si :

-   Tout utilisateur a un nom d’utilisateur, adresse de messagerie ou les adresses de proxy qui inclut le nom de domaine.

-   N’importe quel groupe comporte une adresse de messagerie ou une adresse proxy qui inclut le nom de domaine.

-   N’importe quelle application dans votre annonce Azure comporte une application URI ID qui inclut le nom de domaine.

Vous devez modifier ou supprimer n’importe quel ces ressources dans votre annuaire Azure AD avant de pouvoir supprimer le nom de domaine personnalisé.

## <a name="use-powershell-or-graph-api-to-manage-domain-names"></a>Utiliser PowerShell ou Graph API pour gérer les noms de domaine

La plupart des tâches de gestion des noms de domaine dans Azure Active Directory peuvent également être effectuées à l’aide de PowerShell de Microsoft, ou par programme à l’aide de l’API Azure AD Graph (dans la version d’évaluation).

-   [Utilisation de PowerShell pour gérer des noms de domaine dans Azure Active Directory](https://msdn.microsoft.com/library/azure/e1ef403f-3347-4409-8f46-d72dafa116e0#BKMK_ManageDomains)

-   [À l’aide de l’API Graph pour gérer les noms de domaine dans Azure AD](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/domains-operations)

## <a name="next-steps"></a>Étapes suivantes

-   [Ajouter des noms de domaine personnalisé](active-directory-domains-add-azure-portal.md)

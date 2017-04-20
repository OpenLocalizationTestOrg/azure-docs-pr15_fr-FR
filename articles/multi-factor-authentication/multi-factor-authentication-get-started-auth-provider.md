<properties
    pageTitle="Prise en main d’Azure multifacteur Auth fournisseur | Microsoft Azure"
    description="Découvrez comment créer un fournisseur d’authentification multifacteur Azure."
    services="multi-factor-authentication"
    documentationCenter=""
    authors="kgremban"
    manager="femila"
    editor="yossib"/>

<tags
    ms.service="multi-factor-authentication"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="10/14/2016"
    ms.author="kgremban"/>



# <a name="getting-started-with-an-azure-multi-factor-auth-provider"></a>Prise en main avec un fournisseur d’authentification multifacteur Azure
La vérification est disponible par défaut pour les administrateurs globaux ayant Azure Active Directory et les utilisateurs d’Office 365. Toutefois, si vous souhaitez bénéficier de [fonctionnalités avancées](multi-factor-authentication-whats-next.md) vous devez acheter la version complète d’Azure multifacteur l’authentification Multifacteur.

> [AZURE.NOTE]  Un fournisseur d’authentification multifacteur Azure permet de tirer parti des fonctionnalités fournies par la version complète de l’authentification Multifacteur Azure. Il est destiné aux utilisateurs qui **ne possèdent pas de licences via l’authentification Multifacteur Azure, Azure AD Premium ou em**.  Azure l’authentification Multifacteur, Azure AD Premium et EMS incluent la version complète de l’authentification Multifacteur Azure par défaut.  Si vous avez des licences, vous n’avez pas besoin d’un fournisseur d’authentification multifacteur Azure.

Un fournisseur Azure multifacteur Auth est nécessaire pour télécharger le Kit de développement.

> [AZURE.IMPORTANT]  Pour télécharger le Kit de développement, créez un fournisseur d’authentification multifacteur Azure même si vous avez l’authentification Multifacteur Azure, AAD Premium ou licences EMS.  Si vous créez un fournisseur d’authentification multifacteur Azure à cet effet et que vous avez déjà des licences, veillez à créer le fournisseur avec le modèle **Par des utilisateurs activés** . Ensuite, lier le fournisseur au répertoire qui contient les licences l’authentification Multifacteur Azure, Azure AD Premium ou em.  Cela garantit que vous êtes facturé uniquement si vous avez plusieurs utilisateurs uniques à l’aide du Kit de développement que le nombre de licences que vous êtes propriétaire.


## <a name="to-create-a-multi-factor-auth-provider"></a>Pour créer un fournisseur d’authentification multifacteur

Procédez comme suit pour créer un fournisseur d’authentification multifacteur Azure.

1. Connectez-vous au [portail classique Azure](https://manage.windowsazure.com) en tant qu’administrateur.
2. Sur la gauche, sélectionnez **Active Directory**.
3. Dans la page Active Directory, dans la partie supérieure, sélectionnez **Fournisseurs d’authentification multifacteur**.
![Création d’un fournisseur de l’authentification Multifacteur](./media/multi-factor-authentication-get-started-auth-provider/authprovider1.png)
4. Dans la partie inférieure, cliquez sur **Nouveau**.
![Création d’un fournisseur de l’authentification Multifacteur](./media/multi-factor-authentication-get-started-auth-provider/authprovider2.png)
5. Sous Services de l’application, sélectionnez le **Fournisseur d’authentification multifacteur**
![création d’un fournisseur de l’authentification Multifacteur](./media/multi-factor-authentication-get-started-auth-provider/authprovider3.png)
6. Sélectionnez **Création rapide**.
![Création d’un fournisseur de l’authentification Multifacteur](./media/multi-factor-authentication-get-started-auth-provider/authprovider4.png)
5. Renseignez les champs suivants, puis sélectionnez **créer**.
    1. **Nom** – le nom du fournisseur authentification multifacteur.
    2. **Modèle d’utilisation** – si vous souhaitez autoriser les utilisateurs individuels ou payer par vérification. Choisissez une des deux options :
        - Par authentification – modèle d’achat de frais par authentification. Généralement utilisé pour les scénarios qui utilisent l’authentification multifacteur Azure dans une application pour les consommateurs.
        - Par utilisateur activé – modèle d’achat de frais par utilisateur activée. Généralement utilisé pour l’accès des employés aux applications telles que Office 365. Choisissez cette option si vous avez des utilisateurs qui sont déjà sous licence pour l’authentification Multifacteur Azure.
    2. **Répertoire** – client Azure Active Directory auquel le fournisseur d’authentification multifacteur est associé. Tenir compte des éléments suivants :
        - Vous n’avez pas besoin d’un répertoire Azure AD pour créer un fournisseur d’authentification multifacteur. Laissez la zone vide si vous envisagez d’utiliser le serveur de l’authentification multifacteur Azure ou le Kit de développement logiciel uniquement.
        - Le fournisseur d’authentification multifacteur doivent être associé à un répertoire Azure AD pour tirer parti des fonctionnalités avancées.
        - Azure AD Connect, AAD Sync ou DirSync sont nécessaires uniquement si vous synchronisez votre environnement Active Directory local avec un annuaire Azure AD.  Si vous utilisez uniquement un répertoire Azure AD qui n’est pas synchronisé, ce n’est pas obligatoire.
![Création d’un fournisseur de l’authentification Multifacteur](./media/multi-factor-authentication-get-started-auth-provider/authprovider5.png)
5. Une fois que vous cliquez sur Créer, le fournisseur de l’authentification multifacteur est créé et vous devez voir un message indiquant : **fournisseur de l’authentification multifacteur créé avec succès**. Cliquez sur **Ok**.
![Création d’un fournisseur de l’authentification Multifacteur](./media/multi-factor-authentication-get-started-auth-provider/authprovider6.png)

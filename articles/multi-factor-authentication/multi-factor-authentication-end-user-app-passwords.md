<properties
    pageTitle="Quels sont les mots de passe application dans l’authentification Multifacteur Azure ?"
    description="Cette page permet aux utilisateurs de comprendre ce que sont les mots de passe application et qu’elles sont utilisées pour avec respect l’authentification Multifacteur Azure."
    services="multi-factor-authentication"
    documentationCenter=""
    authors="kgremban"
    manager="femila"
    editor="curtland"/>

<tags
    ms.service="multi-factor-authentication"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/04/2016"
    ms.author="kgremban"/>



# <a name="what-are-app-passwords-in-azure-multi-factor-authentication"></a>Quels sont les mots de passe application dans l’authentification multifacteur Azure ?

Actuellement, certaines applications autres que des navigateurs, telles que le client de messagerie native Apple qui utilise Exchange Active Sync, ne prennent pas en charge l’authentification multifacteur. L’authentification multifacteur est activée par utilisateur. Cela signifie que si un utilisateur a été activé pour l’authentification multifacteur et ils sont tente d’utiliser des applications de navigateur non, ils ne pourront à le faire. Un mot de passe d’application permet à ce problème.

>[AZURE.NOTE] Authentification moderne pour les Clients Office 2013
>
> Les clients Office 2013 (y compris Outlook) prend en charge de nouveaux protocoles d’authentification maintenant et peuvent être activées pour prendre en charge l’authentification multifacteur.  Cela signifie qu’une fois activée, les mots de passe application ne sont pas obligatoires pour une utilisation avec les clients Office 2013.  Pour plus d’informations, voir [présentation publique de l’authentification moderne Office 2013 annoncée](https://blogs.office.com/2015/03/23/office-2013-modern-authentication-public-preview-announced/).

## <a name="how-to-use-app-passwords"></a>Comment utiliser les mots de passe application

Voici quelques éléments à retenir sur l’utilisation de mots de passe de l’application.

- Le mot de passe est généré automatiquement et n’est pas fourni par l’utilisateur. C’est parce que le mot de passe généré automatiquement, est plus difficile pour un pirate à deviner et n’est plus sécurisé.
- Il existe actuellement une limite de 40 mots de passe par utilisateur. Si vous essayez de créer une fois que vous avez atteint la limite, vous devrez supprimer un de vos mots de passe d’application existant afin de créer un nouvel identifiant.
- Il est recommandé que les mots de passe application être créé par périphérique et non par application. Par exemple, vous pouvez créer un mot de passe de l’application pour votre ordinateur portable et utiliser ce mot de passe d’application pour l’ensemble de vos applications sur cet ordinateur portable.
- Vous obtenez un mot de passe d’application la première fois que vous se connecter.  Si vous avez besoin de nouvelles, vous pouvez les créer.

![Programme d’installation](./media/multi-factor-authentication-end-user-app-passwords/app.png)

Une fois que vous avez un mot de passe d’application, vous l’utilisez à la place de votre mot de passe d’origine avec ces applications autres que des navigateurs.  Par exemple, si vous utilisez l’authentification multifacteur et le client de messagerie native Apple sur votre téléphone.  Utilisez le mot de passe d’application afin qu’elle puisse contourner l’authentification multifacteur et continuer à travailler.

## <a name="creating-and-deleting-app-passwords"></a>Création et suppression de mots de passe de l’application
Pendant votre connexion initiale, vous obtenez un mot de passe d’application que vous pouvez utiliser.  En outre, vous pouvez également créer et supprimer les mots de passe application ultérieurement.  Comment cela dépend de la façon dont vous utilisez l’authentification multifacteur.  Choisissez celui que la plupart s’applique à vous.

Utilisation de plusieurs facteur authentiation|Description
:------------- | :------------- |
[Puis-je utiliser avec Office 365](#creating-and-deleting-app-passwords-with-office-365)|  Cela signifie que vous voulez créer des mots de passe de l’application via le portail Office 365.
[Je ne sais pas](#creating-and-deleting-app-passwords-with-myapps-portal)|Cela signifie que vous pouvez créer des mots de passe application via [https://myapps.microsoft.com](https://myapps.microsoft.com)
[Puis-je utiliser avec Microsoft Azure](#create-app-passwords-in-the-azure-portal)| Cela signifie que vous souhaiterez créer les mots de passe application via le portail d’Azure.

## <a name="creating-and-deleting-app-passwords-with-office-365"></a>Création et suppression de mots de passe application avec Office 365

Si vous utilisez l’authentification multifacteur avec Office 365, vous souhaiterez créer et supprimer les mots de passe application via le portail Office 365.

### <a name="to-create-app-passwords-in-the-office-365-portal"></a>Pour créer des mots de passe de l’application dans le portail Office 365
--------------------------------------------------------------------------------

1. Connectez-vous au [portail Office 365](https://login.microsoftonline.com/).
2. Dans le coin supérieur droit sélectionnez le widget et choisissez Paramètres Office 365.
3. Cliquez sur Vérification de la sécurité supplémentaires.
4. Sur la droite, cliquez sur le lien m’indique que **mettre à jour mes numéros de téléphone utilisé pour la sécurité du compte.** 
 ![Le programme d’installation](./media/multi-factor-authentication-end-user-manage/o365a.png)
5. Vous accédez alors à la page qui vous permet de modifier vos paramètres.
![Cloud](./media/multi-factor-authentication-end-user-manage/o365b.png)
6. Dans la partie supérieure, en regard de vérification de la sécurité supplémentaires, cliquez sur **les mots de passe application.**
7. Cliquez sur **créer**.
![Cloud](./media/multi-factor-authentication-end-user-app-passwords-create-o365/apppass.png)
8. Entrez un nom pour le mot de passe d’application et cliquez sur **suivant**.
![Créer un mot de passe d’application](./media/multi-factor-authentication-end-user-app-passwords/create1.png)
9. Copier le mot de passe d’application dans le Presse-papiers et collez-le dans votre application.
![Créer un mot de passe d’application](./media/multi-factor-authentication-end-user-app-passwords/create2.png)


### <a name="to-delete-app-passwords-using-the-office-365-portal"></a>Pour supprimer les mots de passe de l’application à l’aide du portail Office 365
--------------------------------------------------------------------------------


1. Connectez-vous au [portail Office 365](https://login.microsoftonline.com/).
2. Dans le coin supérieur droit sélectionnez le widget et choisissez Paramètres Office 365.
3. Cliquez sur Vérification de la sécurité supplémentaires.
4. Sur la droite, cliquez sur le lien m’indique que **mettre à jour mes numéros de téléphone utilisé pour la sécurité du compte.** 
 ![Le programme d’installation](./media/multi-factor-authentication-end-user-manage/o365a.png)
5. Vous accédez alors à la page qui vous permet de modifier vos paramètres.
![Cloud](./media/multi-factor-authentication-end-user-manage/o365b.png)
6. Dans la partie supérieure, en regard de vérification de la sécurité supplémentaires, cliquez sur **les mots de passe application.**
7. En regard du mot de passe d’application que vous voulez supprimer, cliquez sur **Supprimer**.
![Supprimer un mot de passe d’application](./media/multi-factor-authentication-end-user-app-passwords/delete1.png)
8. Confirmer la suppression en cliquant sur **Oui**.
![Confirmer la suppression](./media/multi-factor-authentication-end-user-app-passwords/delete2.png)
9. Une fois que le mot de passe d’application est supprimée, vous pouvez cliquer sur **Fermer**.
![Fermer](./media/multi-factor-authentication-end-user-app-passwords/delete3.png)


## <a name="creating-and-deleting-app-passwords-with-myapps-portal"></a>Création et suppression des mots de passe de l’application portail de mes applications.
Si vous ne savez pas comment vous utilisez l’authentification multifacteur, vous pouvez toujours créer et supprimer les mots de passe application via le portail de mes applications.

### <a name="to-create-an-app-password-using-the-myapps-portal"></a>Pour créer un mot de passe de l’application à l’aide du portail de mes applications

1. Se connecter au [https://myapps.microsoft.com](https://myapps.microsoft.com)
2. Dans la partie supérieure, sélectionnez profil.
3. Sélectionnez la vérification de sécurité supplémentaires.
![Cloud](./media/multi-factor-authentication-end-user-manage/myapps1.png)
4. Vous accédez alors à la page qui vous permet de modifier vos paramètres.
![Programme d’installation](./media/multi-factor-authentication-end-user-manage-myapps/proofup.png)
5. Dans la partie supérieure, en regard de vérification de la sécurité supplémentaires, cliquez sur **les mots de passe application.**
6. Cliquez sur **créer**.
![Créer un mot de passe d’application](./media/multi-factor-authentication-end-user-app-passwords/create3.png)
7. Entrez un nom pour le mot de passe d’application et cliquez sur **suivant**.
![Créer un mot de passe d’application](./media/multi-factor-authentication-end-user-app-passwords/create1.png)
8. Copier le mot de passe d’application dans le Presse-papiers et collez-le dans votre application.
![Créer un mot de passe d’application](./media/multi-factor-authentication-end-user-app-passwords/create2.png)

### <a name="to-delete-an-app-password-using-the-myapps-portal"></a>Pour supprimer un mot de passe de l’application à l’aide du portail de mes applications

1. Se connecter au [https://myapps.microsoft.com](https://myapps.microsoft.com)
2. Dans la partie supérieure, sélectionnez profil.
3. Sélectionnez la vérification de sécurité supplémentaires.
![Cloud](./media/multi-factor-authentication-end-user-manage/myapps1.png)
4. Vous accédez alors à la page qui vous permet de modifier vos paramètres.
![Programme d’installation](./media/multi-factor-authentication-end-user-manage-myapps/proofup.png)
5. Dans la partie supérieure, en regard de vérification de la sécurité supplémentaires, cliquez sur **les mots de passe application.**
6. En regard du mot de passe d’application que vous voulez supprimer, cliquez sur **Supprimer**.
![Supprimer un mot de passe d’application](./media/multi-factor-authentication-end-user-app-passwords/delete1.png)
7. Confirmer la suppression en cliquant sur **Oui**.
![Confirmer la suppression](./media/multi-factor-authentication-end-user-app-passwords/delete2.png)
8. Une fois que le mot de passe d’application est supprimée, vous pouvez cliquer sur **Fermer**.
![Fermer](./media/multi-factor-authentication-end-user-app-passwords/delete3.png)


## <a name="create-app-passwords-in-the-azure-portal"></a>Créer des mots de passe de l’application dans le portail Azure

Si vous utilisez l’authentification multifacteur avec Azure, vous souhaiterez créer les mots de passe application via le portail d’Azure.

### <a name="to-create-app-passwords-in-the-azure-portal"></a>Pour créer des mots de passe de l’application dans le portail Azure

1. Connectez-vous au portail de gestion Azure.
2. Dans la partie supérieure, avec le bouton droit sur votre nom d’utilisateur, puis sélectionnez Vérification de sécurité supplémentaire.
3. Dans la page proofup, dans la partie supérieure, sélectionnez les mots de passe application
4. Cliquez sur **créer**.
5. Entrez un nom pour le mot de passe d’application et cliquez sur **suivant**
6. Copier le mot de passe d’application dans le Presse-papiers et collez-le dans votre application.
![Cloud](./media/multi-factor-authentication-end-user-app-passwords-create-azure/app2.png)

### <a name="to-delete-app-passwords-in-the-azure-portal"></a>Pour supprimer les mots de passe application dans le portail Azure

1. Connectez-vous au portail de gestion Azure.
2. Dans la partie supérieure, avec le bouton droit sur votre nom d’utilisateur, puis sélectionnez Vérification de sécurité supplémentaire.
3. Dans la partie supérieure, en regard de vérification de la sécurité supplémentaires, cliquez sur **les mots de passe application.**
4. En regard du mot de passe d’application que vous voulez supprimer, cliquez sur **Supprimer**.
5. Confirmer la suppression en cliquant sur **Oui**.
6. Une fois que le mot de passe d’application est supprimée, vous pouvez cliquer sur **Fermer**.
![Fermer](./media/multi-factor-authentication-end-user-app-passwords/delete3.png)

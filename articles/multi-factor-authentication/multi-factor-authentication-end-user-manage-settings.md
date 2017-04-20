<properties
    pageTitle="Gérer vos paramètres de vérification en deux étapes | Microsoft Azure"
    description="Gérer l’utilisation de l’authentification multifacteur Azure notamment la modification de vos informations de contact ou de configuration de vos périphériques."
    services="multi-factor-authentication"
    keywords = "client de l’authentification multifacteur, problème d’authentification, ID de corrélation"
    documentationCenter=""
    authors="kgremban"
    manager="femila"
    editor="yossib"/>

<tags
    ms.service="multi-factor-authentication"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/10/2016"
    ms.author="kgremban"/>

# <a name="manage-your-settings-for-two-step-verification"></a>Gérer vos paramètres pour la vérification

Cet article répond aux questions sur la façon de mettre à jour les paramètres pour l’authentification multifacteur ou de vérification en deux étapes. Pour obtenir de l’aide, si vous rencontrez des problèmes de connexion à votre compte, voir [vous rencontrez des problèmes avec la vérification](multi-factor-authentication-end-user-troubleshoot.md) .


## <a name="where-to-find-the-settings-page"></a>Où trouver la page Paramètres
Selon la façon dont votre société configurer l’authentification multifacteur Azure, il existe quelques emplacements où vous pouvez modifier vos paramètres tels que votre numéro de téléphone.

Si votre administrateur informatique envoyée une URL spécifique ou étapes pour gérer la vérification, suivez ces instructions. Dans le cas contraire, suivez les instructions ci-dessous doivent utiliser pour tout le monde autre. Si vous procédez ainsi mais que vous ne voyez pas les mêmes options, cela signifie que votre Professionnel ou scolaire personnalisé leur propre portail. Demandez à votre administrateur pour le lien vers votre portail l’authentification multifacteur Azure.


1. Se connecter à [https://myapps.microsoft.com](https://myapps.microsoft.com)  
2. Dans la partie supérieure, sélectionnez **profil**.  
3. Sélectionnez la **vérification de sécurité supplémentaires**.  

    ![Mes applications](./media/multi-factor-authentication-end-user-manage/myapps1.png)

4. Chargement de la page de vérification de sécurité supplémentaires avec vos paramètres.

    ![Proofup](./media/multi-factor-authentication-end-user-manage-myapps/proofup.png)


## <a name="i-want-to-change-my-phone-number-or-add-a-secondary-number"></a>Je souhaite modifier mon numéro de téléphone ou ajouter un numéro secondaire

Il est important de configurer un numéro de téléphone d’authentification secondaire.  Étant donné que votre numéro de téléphone principal et votre application mobile sont probablement sur le téléphone de même, le numéro de téléphone secondaire est la seule façon de vous pourrez plus revenir à votre compte si votre téléphone est perte ou de vol.

> [AZURE.NOTE]
> Si vous n’avez accès à votre numéro de téléphone principal et besoin d’aide votre compte, consultez notre rubriques d’aide dans [vous rencontrez des problèmes avec la vérification](multi-factor-authentication-end-user-troubleshoot.md).

**Pour modifier votre numéro de téléphone principal :**  

1. Dans la page Vérification de sécurité supplémentaires, sélectionnez la zone de texte avec votre numéro de téléphone en cours et le modifier avec votre nouveau numéro de téléphone.  
2. Cliquez sur **Enregistrer**.  
3. Si c’est le nombre que vous utilisez pour votre option de vérification par défaut, vous devez vérifier le nouveau numéro avant que vous pouvez l’enregistrer.  


**Pour ajouter un numéro de téléphone secondaire :**  

1. Dans la page Vérification de sécurité supplémentaires, cochez la case à côté **téléphone d’authentification alternatives.**  
2. Entrez votre numéro de téléphone secondaire dans la zone de texte.  
3. Sélectionnez **Enregistrer** et vos modifications sont terminées.  


## <a name="how-do-i-clean-up-microsoft-authenticator-from-my-old-device-and-move-to-a-new-one"></a>Comment nettoyer Authenticator Microsoft à partir de mon appareil ancien et déplacer vers un autre ?
Lorsque vous désinstallez l’application à partir de votre appareil ou réinitialisez le périphérique, il ne supprime pas l’activation sur le serveur principal. Vous devez utiliser les étapes décrites dans le [déplacement vers un nouvel appareil](multi-factor-authentication-microsoft-authenticator.md#how-to-move-to-the-new-microsoft-authenticator-app).

## <a name="next-steps"></a>Étapes suivantes
- Conseils de dépannage et aider à [vous rencontrez des problèmes avec la vérification](multi-factor-authentication-end-user-troubleshoot.md)
- Configurer [les mots de passe application](multi-factor-authentication-end-user-app-passwords.md) pour toutes les applications qui ne prennent pas en charge la vérification.

<properties
    pageTitle="Configurer la vérification de mon compte professionnel ou scolaire"
    description="Lorsque votre société configure l’authentification multifacteur Azure, vous devrez inscrire pour la vérification. Découvrez comment configurer votre messagerie. "
    services="multi-factor-authentication"
    keywords="comment utiliser directory azure, active directory dans le cloud, didacticiel active directory"
    documentationCenter=""
    authors="kgremban"
    manager="femila"
    editor="pblachar"/>

<tags
    ms.service="multi-factor-authentication"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/10/2016"
    ms.author="kgremban"/>

# <a name="set-up-my-account-for-two-step-verification"></a>Configurer mon compte pour la vérification

La vérification est une mesure de sécurité supplémentaire qui permet de protéger votre compte en effectuant plus difficile à d’autres personnes rompre de. Si vous êtes en train de lire cet article, vous avez probablement un message électronique à partir de votre administrateur Professionnel ou scolaire à propos de l’authentification multifacteur. Ou peut-être essayé de vous connecter et un message vous invitant à configurer la vérification de la sécurité supplémentaires. Si c’est le cas, **que vous ne pouvez pas vous connecter jusqu'à ce que vous avez terminé le processus d’inscription automatique**.

Cet article vous aide à configurer votre **travail compte professionnel ou scolaire**. Si vous voulez activer la vérification en deux étapes pour votre propre compte Microsoft personnel, voir [à propos de la vérification](https://support.microsoft.com/help/12408/microsoft-account-about-two-step-verification).

## <a name="determine-how-you-will-use-multi-factor-authentication"></a>Déterminez comment vous allez utiliser l’authentification multifacteur

La vérification fonctionne en invitant à deux éléments d’identification lorsque vous vous connectez. Tout d’abord, nous demandons pour votre nom d’utilisateur et mot de passe comme d’habitude. Puis, nous contacter un téléphone qui nous savoir appartient à vous et que vous vérifiez que la tentative de connexion a légitime.  

Pour commencer le processus d’installation, essayez de vous connecter à votre compte comme vous le faites habituellement. Si votre administrateur a configuré votre compte pour la vérification, vous devrez commencer le processus d’inscription automatique. Commencer ce processus en cliquant sur **configurer maintenant.**

![Programme d’installation](./media/multi-factor-authentication-end-user-first-time/first.png)

La première question dans le processus d’inscription est comment vous contacter. Consultez les options du tableau, puis utilisez les liens pour accéder à la configuration pour chaque méthode.

| Méthode de contact | Description |
| --- | --- |
[Application mobile](#use-a-mobile-app-as-the-contact-method) | - **Recevoir des notifications pour la vérification.** Cette option envoie une notification à l’application authentificateur sur votre smartphone ou votre tablette. Afficher la notification et, si elle est légitime, sélectionnez **authentifier** dans l’application. Votre Professionnel ou scolaire peut-être nécessiter que vous entrez un code confidentiel avant de vous authentifiez.<br>- **Utilisez le code de vérification.** Dans ce mode, l’application authentificateur génère un code de vérification qui met à jour toutes les 30 secondes. Entrez le code de vérification plus récent dans l’interface de connexion.<br>L’application Microsoft Authenticator est disponible pour [Windows Phone](http://go.microsoft.com/fwlink/?Linkid=825071), [Android](http://go.microsoft.com/fwlink/?Linkid=825072)et [IOS](http://go.microsoft.com/fwlink/?Linkid=825073). |
[Appel de téléphone mobile ou du texte](#use-your-mobile-phone-as-the-contact-method) | - **Appel téléphonique** permet de passer un appel vocal automatisé au numéro de téléphone que vous avez défini. Répondre à l’appel, puis appuyez sur # dans le pavé de téléphone s’authentifier.<br>- **Message texte** se termine à un message texte contenant un code de vérification. Suivre l’invite dans le texte, répondre au message texte ou entrez le code de vérification fourni dans l’interface de connexion. |  
[Appeler Office](#use-your-office-phone-as-the-contact-method) | Passer un appel vocal automatisé pour le numéro de téléphone que vous avez défini. Répondre à l’appel et appuie sur # dans le pavé de téléphone s’authentifier. |

## <a name="use-a-mobile-app-as-the-contact-method"></a>Utiliser une application mobile comme la méthode de contact

L’utilisation de cette méthode nécessite que vous installez une application authentificateur sur votre téléphone ou tablette. Les étapes décrites dans cet article sont basées sur l’application Microsoft Authenticator, qui est disponible pour [Windows Phone](http://go.microsoft.com/fwlink/?Linkid=825071), [Android](http://go.microsoft.com/fwlink/?Linkid=825072)et [IOS](http://go.microsoft.com/fwlink/?Linkid=825073).

1. Sélectionnez **l’application Mobile** dans la liste déroulante.
2. Sélectionnez **recevoir des notifications pour la vérification** ou **utiliser le code de vérification**, puis sélectionnez **configurer**.

    ![Écran de vérification de sécurité supplémentaires](./media/multi-factor-authentication-end-user-first-time-mobile-app/mobileapp.png)

3. Sur votre téléphone ou tablette, ouvrez l’application et sélectionnez **+** pour ajouter un compte. (Sur les appareils Android, sélectionnez les points de suspension, puis **Ajouter un compte**).
4. Spécifier que vous souhaitez ajouter un compte professionnel ou scolaire. L’Analyseur de code QR sur votre téléphone s’ouvre. Si votre appareil photo ne fonctionne pas correctement, vous pouvez sélectionner d’entrer manuellement les informations de votre société. Pour plus d’informations, voir [Ajouter un compte manuellement](#add-an-account-manually).  
5. Passez en revue l’image de code QR qui apparaissaient avec l’écran de configuration de l’application mobile.  Cliquez sur **terminé** pour fermer l’écran de code QR.  

    ![Écran de code QR](./media/multi-factor-authentication-end-user-first-time-mobile-app/scan2.png)

6. Une fois l’activation sur le téléphone, sélectionnez **Contact moi**.  Cette étape envoie une notification ou un code de vérification à votre téléphone. Sélectionnez **Vérifier**.  
7. Si votre société nécessite un code confidentiel de l’approbation de vérification, entrez cet élément.

    ![Zone pour entrer un code confidentiel](./media/multi-factor-authentication-end-user-first-time-mobile-app/scan3.png)

8. Une fois l’entrée du code confidentiel terminée, sélectionnez **Fermer**. À ce stade, la vérification de votre doit être réussie.
9. Nous vous recommandons que vous entrez votre numéro de téléphone mobile au cas où vous perdez l’accès à votre application mobile. Spécifiez votre pays dans la liste déroulante, puis entrez votre numéro de téléphone mobile dans la zone en regard du nom de pays. Cliquez sur **suivant**.
10. À ce stade, vous êtes invité à configurer les mots de passe application pour les applications autres que des navigateurs tels que Outlook 2010 ou version antérieure, ou l’application de messagerie native sur les appareils Apple. C’est parce que certaines applications ne prend pas en charge la vérification. Si vous n’utilisez pas ces applications, cliquez sur **Terminer** et ignorez le reste des étapes.
11. Si vous utilisez ces applications, copiez le mot de passe d’application fournis et collez-le dans votre application à la place de votre mot de passe normal. Vous pouvez utiliser le même mot de passe d’application pour plusieurs applications. Pour plus d’informations, [aide sur les mots de passe application].
12. Cliquez sur **terminé**.


### <a name="add-an-account-manually"></a>Ajouter manuellement un compte
Si vous voulez ajouter un compte dans l’application mobile manuellement, au lieu d’utiliser le lecteur QR, procédez comme suit.

1. Sélectionnez le bouton **Entrez compte manuellement** .  
2. Entrez le code et l’URL qui sont fournies sur la même page qui vous montre le code-barres. Ces informations se trouvent dans les zones de **Code** et **l’URL** dans l’application mobile.

    ![Programme d’installation](./media/multi-factor-authentication-end-user-first-time-mobile-app/barcode2.png)

3. Une fois l’activation terminée, sélectionnez **Contact moi**. Cette étape envoie une notification ou un code de vérification à votre téléphone. Sélectionnez **Vérifier**.

## <a name="use-your-mobile-phone-as-the-contact-method"></a>Utiliser votre téléphone mobile en tant que la méthode de contact

1. Sélectionnez **L’authentification téléphone** dans la liste déroulante.  

    ![Programme d’installation](./media/multi-factor-authentication-end-user-first-time-mobile-phone/phone.png)  

2. Sélectionnez votre pays dans la liste déroulante, puis entrez votre numéro de téléphone mobile.
3. Sélectionnez la méthode que vous préférez utiliser avec votre téléphone mobile - texte ou l’appel.
4. Sélectionnez **Contact moi** pour vérifier votre numéro de téléphone. Selon le mode que vous avez sélectionné, nous vous envoyer un texte ou vous appeler. Suivez les instructions fournies dans l’écran, puis cliquez sur **Vérifier**.
5. À ce stade, vous êtes invité à configurer les mots de passe application pour les applications autres que des navigateurs tels que Outlook 2010 ou version antérieure, ou l’application de messagerie native sur les appareils Apple. C’est parce que certaines applications ne prend pas en charge la vérification. Si vous n’utilisez pas ces applications, cliquez sur **Terminer** et ignorez le reste des étapes.
6. Si vous utilisez ces applications, copiez le mot de passe d’application fournis et collez-le dans votre application à la place de votre mot de passe normal. Vous pouvez utiliser le même mot de passe d’application pour plusieurs applications. Pour plus d’informations, [aide sur les mots de passe application].
7. Cliquez sur **terminé**.

## <a name="use-your-office-phone-as-the-contact-method"></a>Utiliser votre téléphone de bureau comme la méthode de contact

1. Sélectionnez le **Téléphone de bureau** à partir de la liste déroulante  

    ![Programme d’installation](./media/multi-factor-authentication-end-user-first-time-office-phone/office.png)  

2. La zone numéro de téléphone est automatiquement renseignée à partir des informations de contact de votre société. Si le nombre est manquant ou incorrect, demandez à votre administrateur pour apporter des modifications.
4. **Contact moi** pour vérifier votre numéro de téléphone, et nous appelons votre numéro. Suivez les instructions fournies dans l’écran, puis cliquez sur **Vérifier**.
5. À ce stade, vous êtes invité à configurer les mots de passe application pour les applications autres que des navigateurs tels que Outlook 2010 ou version antérieure, ou l’application de messagerie native sur les appareils Apple. C’est parce que certaines applications ne prend pas en charge la vérification. Si vous n’utilisez pas ces applications, cliquez sur **Terminer** et ignorez le reste des étapes.
6. Si vous utilisez ces applications, copiez le mot de passe d’application fournis et collez-le dans votre application à la place de votre mot de passe normal. Vous pouvez utiliser le même mot de passe d’application pour plusieurs applications. Pour plus d’informations, voir [Quels sont les mots de passe application](multi-factor-authentication-end-user-app-passwords.md).
7. Cliquez sur **terminé**.

## <a name="next-steps"></a>Étapes suivantes

- Modifier vos options préférées et [gérer vos paramètres pour la vérification](multi-factor-authentication-end-user-manage-settings.md)
- Configurer [les mots de passe application](multi-factor-authentication-end-user-app-passwords.md) pour les applications de périphérique natives qui ne prennent pas en charge la vérification.
- Consultez l' [application Microsoft authentificateur](multi-factor-authentication-microsoft-authenticator.md) pour l’authentification rapide et sécurisée même lorsque vous n’avez pas service de cellule.

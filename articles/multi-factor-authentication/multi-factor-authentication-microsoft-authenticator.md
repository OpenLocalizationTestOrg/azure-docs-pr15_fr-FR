<properties
    pageTitle="Application Microsoft Authenticator pour les téléphones mobiles | Microsoft Azure"
    description="Découvrez comment mettre à niveau vers la dernière version d’Azure identificateur."
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
    ms.date="08/22/2016"
    ms.author="kgremban"/>

# <a name="microsoft-authenticator"></a>Microsoft authentificateur

L’application Microsoft Authenticator fournit un niveau de sécurité dans votre compte Azure supplémentaire (par exemple, bsimon@contoso.onmicrosoft.com), compte professionnel vos localement (par exemple, bsimon@contoso.com), ou votre compte Microsoft (par exemple, bsimon@outlook.com).

L’application fonctionne dans un des deux manières suivantes :

- **Notification**. L’application permet d’empêcher tout accès non autorisé aux comptes et arrêter transactions frauduleuses en envoyant une notification à votre smartphone ou une tablette. Simplement afficher la notification et s’il s’agit légitime, sélectionnez **Vérifier**. Dans le cas contraire, vous pouvez sélectionnez **Refuser**. Pour plus d’informations sur les refusant les notifications, voir comment utiliser la fonctionnalité de fraude de rapport et refuser pour l’authentification multifacteur.

- **Mot de passe avec code de vérification**. L’application peut servir un jeton logiciel pour générer un code de vérification OAuth. Vous entrez le code fourni par l’application dans l’écran se connecter, ainsi que le nom d’utilisateur et mot de passe, lorsque vous y êtes invité. Le code de vérification fournit une deuxième forme d’authentification.

Avec la version de l’application Microsoft Authenticator, l’ancienne application Azure authentificateur est remplacée.  L’application Azure authentificateur continueront à fonctionner, mais si vous décidez de déplacer vers la nouvelle application Microsoft Authenticator, cet article peut vous aider.  

## <a name="install-the-app"></a>Installer l’application

L’application Microsoft Authenticator est disponible pour [Windows Phone](http://go.microsoft.com/fwlink/?Linkid=825071), [Android](http://go.microsoft.com/fwlink/?Linkid=825072)et [IOS](http://go.microsoft.com/fwlink/?Linkid=825073).

## <a name="add-accounts-to-the-app"></a>Ajouter des comptes à l’application

Pour chaque compte que vous souhaitez ajouter à l’application Microsoft Authenticator, utilisez une des procédures suivantes.

### <a name="add-an-account-to-the-app-by-using-the-qr-code-scanner"></a>Ajouter un compte à l’application à l’aide de l’Analyseur de code QR

1. Accédez à l’écran de paramètres de vérification de sécurité.  Pour savoir comment accéder à cet écran, voir [modifier vos paramètres de sécurité](multi-factor-authentication-end-user-manage-settings.md).

2. Sélectionnez **configurer**.

    ![Le bouton Configurer dans l’écran de paramètres de vérification de sécurité](./media/multi-factor-authentication-azure-authenticator/azureauthe.png)

    Cela affiche un écran avec un code QR dessus.

    ![Écran qui fournit le code QR](./media/multi-factor-authentication-azure-authenticator/barcode2.png)

3. Ouvrez l’application Microsoft authentificateur. Dans l’écran **comptes** , sélectionnez **+**, puis spécifiez que vous souhaitez ajouter un compte professionnel ou scolaire.

    ![L’écran comptes avec signe plus](./media/multi-factor-authentication-azure-authenticator/addaccount3.png)

    ![Écran permettant de spécifier un compte professionnel ou scolaire](./media/multi-factor-authentication-end-user-first-time-mobile-app/scan.png)

4. Utiliser la caméra pour le code QR, puis sélectionnez **terminé** pour fermer l’écran de code QR.

    ![Écran pour l’analyse d’un code QR](./media/multi-factor-authentication-end-user-first-time-mobile-app/scan2.png)

    Si votre appareil photo ne fonctionne pas correctement, vous pouvez entrer manuellement le code QR et l’URL. Pour plus d’informations, voir [Ajouter un compte à l’application manuellement](#add-an-account-to-the-app-manually).

5. Patienter pendant que le compte est activé. Une fois l’activation, sélectionnez **Contact moi**.  Envoie une notification ou un code de vérification à votre téléphone.  Sélectionnez **Vérifier**.

    ![Écran où vous sélectionnez Vérifiez pour vous connecter](./media/multi-factor-authentication-end-user-first-time-mobile-app/verify.png)

6. Si votre société nécessite un code confidentiel de l’approbation de vérification, entrez cet élément.

    ![Zone pour entrer un code confidentiel](./media/multi-factor-authentication-end-user-first-time-mobile-app/scan3.png)

7. Une fois l’entrée du code confidentiel terminée, sélectionnez **Fermer**. À ce stade, la vérification de votre doit être réussie.
8. Nous vous recommandons que vous entrez votre numéro de téléphone mobile au cas où vous perdez l’accès à votre application. Spécifiez votre pays dans la liste déroulante, puis entrez votre numéro de téléphone mobile dans la zone en regard du nom de pays. Cliquez sur **suivant**.
9. À ce stade, vous avez configuré votre méthode de contact. Maintenant, il est temps pour configurer les mots de passe application pour les applications autres que des navigateurs, tels que Outlook 2010 ou version antérieure. Si vous n’utilisez pas ces applications, cliquez sur **terminé**. Dans le cas contraire, passez à l’étape suivante.

    ![Écran pour la création d’un mot de passe d’application](./media/multi-factor-authentication-end-user-first-time-mobile-app/step4.png)

10. Si vous utilisez les applications non navigateur, copiez le mot de passe d’application fournis et collez le mot de passe dans vos applications. Pour obtenir la procédure dans des applications telles qu’Outlook et Lync, voir comment modifier le mot de passe dans votre message électronique pour le mot de passe d’application et comment modifier le mot de passe dans votre application et le mot de passe d’application.
11. Cliquez sur **terminé**.

Vous devez maintenant voir le nouveau compte dans l’écran **comptes** .

![Écran comptes](./media/multi-factor-authentication-azure-authenticator/accounts.png)

### <a name="add-an-account-to-the-app-manually"></a>Ajouter manuellement un compte à l’application

1. Accédez à l’écran de paramètres de vérification de sécurité.  Pour savoir comment accéder à cet écran, voir [modifier vos paramètres de sécurité](multi-factor-authentication-end-user-manage-settings.md).

2. Sélectionnez **configurer**.

    ![Le bouton Configurer dans l’écran de paramètres de vérification de sécurité](./media/multi-factor-authentication-azure-authenticator/azureauthe.png)

    Cela affiche un écran avec un code QR dessus.  Remarque le code et l’URL.

    ![Écran qui fournit le code QR et l’URL](./media/multi-factor-authentication-azure-authenticator/barcode2.png)

3. Ouvrez l’application Microsoft authentificateur. Dans l’écran **comptes** , sélectionnez **+**, puis spécifiez que vous souhaitez ajouter un compte professionnel ou scolaire.

    ![L’écran comptes avec signe plus](./media/multi-factor-authentication-azure-authenticator/addaccount3.png)

    ![Écran permettant de spécifier un compte professionnel ou scolaire](./media/multi-factor-authentication-end-user-first-time-mobile-app/scan.png)

4. Dans le scanneur, sélectionnez **entrer code manuellement**.

    ![Écran pour l’analyse d’un code QR](./media/multi-factor-authentication-end-user-first-time-mobile-app/scan2.png)

5. Entrez le code et l’URL dans les zones appropriées dans l’application.

    ![Écran pour la saisie de code et l’URL](./media/multi-factor-authentication-azure-authenticator/manual.png)

    ![Écran pour la saisie de code et l’URL](./media/multi-factor-authentication-end-user-first-time-mobile-app/addaccount2.png)

6. Patienter pendant que le compte est activé. Une fois l’activation, sélectionnez **Contact moi**. Envoie une notification ou un code de vérification à votre téléphone. Sélectionnez **Vérifier**.

Vous devez maintenant voir le nouveau compte dans l’écran **comptes** .

![Écran comptes](./media/multi-factor-authentication-azure-authenticator/accounts.png)

### <a name="add-an-account-to-the-app-by-using-touch-id"></a>Ajouter un compte à l’application à l’aide des ID tactile

L’application Microsoft Authenticator sur iOS prend en charge tactile identifiant.  L’authentification multifacteur Azure permet aux organisations d’un code confidentiel est nécessaire pour appareils mobiles. Avec l’ID tactile, iOS utilisateurs n’êtes pas obligé d’entrer un code confidentiel. Au lieu de cela, ils peuvent analyser son empreinte numérique et sélectionnez **Approuver**.

La configuration d’ID tactile avec Microsoft Authenticator est simple. Vous effectuez un problème de vérification normal avec un code confidentiel. Si votre appareil prend en charge tactile ID, Microsoft Authenticator sera configurez-la automatiquement pour ce compte.

![Vérification de l’installation ID tactile](./media/multi-factor-authentication-azure-authenticator/touchid1.png)

À partir de que point, lorsque vous êtes amené à vérifier votre connexion, vous activez la notification push reçu, analyser votre empreinte au lieu de taper votre code confidentiel.

![Notification d’émission](./media/multi-factor-authentication-azure-authenticator/touchid2.png)

## <a name="uninstall-the-old-azure-authentication-app"></a>Désinstaller l’ancienne application d’authentification Azure

Une fois que vous avez ajouté tous les comptes à la nouvelle application, vous pouvez désinstaller l’ancienne application à partir de votre téléphone.

## <a name="delete-an-account"></a>Supprimer un compte

Pour supprimer un compte de l’application Microsoft Authenticator, sélectionnez le compte, puis sélectionnez **Supprimer**.

![Bouton Supprimer](./media/multi-factor-authentication-azure-authenticator/remove.png)

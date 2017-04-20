<properties 
   pageTitle="Contacter le support technique Microsoft | Microsoft Azure"
   description="Apprenez à créer une demande de support et démarrer une session de prise en charge sur votre appareil StorSimple."
   services="storsimple"
   documentationCenter=""
   authors="alkohli"
   manager="carmonm"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="09/21/2016"
   ms.author="alkohli" />

# <a name="contact-microsoft-support"></a>Contacter le support technique Microsoft

Si vous rencontrez des problèmes avec votre solution Microsoft Azure StorSimple, vous pouvez créer une demande de service de support technique. Dans une session avec votre technicien du support technique en ligne, vous devrez également démarrer une session de prise en charge sur votre appareil StorSimple. Cet article vous guide dans :

- Comment créer une demande de support.
- Comment démarrer une session de prise en charge dans l’interface Windows PowerShell de votre appareil StorSimple.

Passez en revue [StorSimple 8000 série prise en charge SLA et informations](https://msdn.microsoft.com/library/mt433077.aspx) avant de créer une demande de Support.

## <a name="create-a-support-request"></a>Créer une demande de support

Procédez comme suit pour créer une demande de support :

#### <a name="to-create-a-support-request"></a>Pour créer une demande de support

1. Dans le [portail classique Azure](https://manage.windowsazure.com/), dans le coin supérieur droit, cliquez sur votre nom de compte, puis sur **Contacter le support technique Microsoft**.

    ![Prise en charge de contact MS via ManagementPortal](./media/storsimple-contact-microsoft-support/Ibiza1.png)

2. Vous êtes redirigé vers le nouveau portail Azure (portal.azure.com). Cliquez sur la vignette **Nouveau prend en charge la demande** .

    ![Contactez le Support MS via le portail nouveau](./media/storsimple-contact-microsoft-support/Ibiza2.png)

    Sur le côté droit de l’écran, le volet **demande d’assistance nouveau** s’affiche. 

    ![Nouveau volet demande de prise en charge](./media/storsimple-contact-microsoft-support/Ibiza3a.png)

3. Dans la boîte de dialogue **Concepts de base** , procédez comme suit :                                
    1. Dans la liste déroulante **type de problème** , sélectionnez **technique**.
    2. Sélectionnez un **abonnement** dans la liste déroulante.
    3. Dans la liste déroulante **Service** , sélectionnez **StorSimple**. 
    4. Sélectionnez un **plan de prise en charge** dans la liste déroulante. Vous avez besoin d’un plan de support payant pour activer le Support technique.

4. Cliquez sur **suivant**. La boîte de dialogue **problème** s’affiche.

    ![Nouveau volet demande de prise en charge](./media/storsimple-contact-microsoft-support/Ibiza5a.png) 

5. Dans la boîte de dialogue **problème** , procédez comme suit :

    1.  Sélectionnez un niveau de **gravité** dans la liste déroulante.
    2.  Sélectionnez un **type de problème** dans la liste déroulante.
    3.  Sélectionnez une **catégorie** dans la liste déroulante. 
    4.  Dans la zone **Détails** , décrivez votre problème.
    5.  Dans la zone **intervalle de temps** , indiquez la date, heure et fuseau horaire qui correspond à l’occurrence de la plus récente de votre problème.
    6.  Sous **téléchargement de fichier**, cliquez sur l’icône de dossier pour accéder à votre package de prise en charge.
    7.  Activez la case à cocher **des informations de diagnostic partager** .

6. Cliquez sur **suivant**. La boîte de dialogue **informations de Contact** s’affiche.

    ![Nouveau volet demande de prise en charge](./media/storsimple-contact-microsoft-support/Ibiza6a.png) 

7. Entrez vos informations de contact et sélectionnez une méthode de contact (téléphone ou e-mail). 

8. Activez la case à cocher **Enregistrer les modifications de contact pour les demandes de prise en charge future** .

9. Cliquez sur **créer**.

Après l’avoir envoyé votre demande, un technicien du support technique vous contactera dès que possible pour poursuivre la demande.

## <a name="start-a-support-session-in-windows-powershell-for-storsimple"></a>Démarrer une session de prise en charge dans Windows PowerShell pour StorSimple

Pour résoudre les éventuels problèmes que vous pouvez rencontrer avec le périphérique StorSimple, vous devrez contacter l’équipe de Support Microsoft. Support Microsoft devrez peut-être utiliser une session de prise en charge pour vous connecter à votre appareil. 

Effectuez les étapes suivantes pour démarrer une session de prise en charge :

#### <a name="to-start-a-support-session"></a>Pour démarrer une session de prise en charge

1. Accéder à l’appareil directement à l’aide de la console série ou via un présents depuis un ordinateur distant. Pour ce faire, suivez les étapes de [PuTTY utiliser pour vous connecter à la console série appareil](storsimple-deployment-walkthrough.md#use-putty-to-connect-to-the-device-serial-console).

2. Dans la session qui s’ouvre, appuyez sur la touche **entrée** pour obtenir une invite de commande.

3. Dans le menu de la console série, sélectionnez l’option 1, **vous connecter avec un accès complet**.

4. À l’invite, tapez le mot de passe suivante : 

    `Password1`

5. À l’invite, tapez la commande suivante :

    `Enable-HcsSupportAccess`

6. Une chaîne chiffrée s’affiche pour vous. Copiez cette chaîne dans un éditeur de texte tel que le bloc-notes.

7. Enregistrez cette chaîne et envoyer dans un message électronique au Support Microsoft. 

> [AZURE.IMPORTANT] Vous pouvez désactiver l’accès au support en exécutant `Disable-HcsSupportAccess`. Le périphérique StorSimple tentera également désactiver l’accès au support 8 heures après que la session a été lancée. Il est recommandé de modifier vos informations d’identification du périphérique StorSimple après le lancement d’une session de prise en charge.

<properties 
   pageTitle="Modifier le mot de passe d’administrateur de périphérique virtuel StorSimple | Microsoft Azure"
   description="Décrit comment utiliser le portail classique Azure ou l’interface utilisateur du web tableau virtuel StorSimple pour modifier le mot de passe administrateur appareil."
   services="storsimple"
   documentationCenter="NA"
   authors="alkohli"
   manager="carmonm"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD"
   ms.date="06/17/2016"
   ms.author="alkohli" />

# <a name="change-the-storsimple-virtual-array-device-administrator-password"></a>Modifier le mot de passe administrateur de périphérique tableau virtuel StorSimple

## <a name="overview"></a>Vue d’ensemble

Lorsque vous utilisez l’interface Windows PowerShell pour accéder au périphérique virtuel StorSimple, vous devez entrer un mot de passe administrateur. Lorsque l’appareil StorSimple est mis en service et mise en route pour la première fois, le mot de passe par défaut est *le mot de passe1*. Pour la sécurité de vos données, le mot de passe par défaut expire la première fois que vous êtes connecté et vous devez modifier ce mot de passe.

Vous pouvez également utiliser l’interface utilisateur web local ou sur le portail classique Azure pour modifier le mot de passe administrateur appareil à tout moment une fois que l’appareil est déployé dans votre environnement de production. Chacune de ces procédures est décrite dans cet article.

## <a name="use-the-azure-classic-portal-to-change-the-password"></a>Utiliser le portail classique Azure pour modifier le mot de passe

Procédez comme suit pour modifier le mot de passe administrateur appareil via le portail classique Azure.

#### <a name="to-change-the-device-administrator-password-via-the-azure-classic-portal"></a>Pour modifier le mot de passe administrateur appareil via le portail classique Azure

1. Dans le portail, cliquez sur **périphériques** > **Configuration** pour votre appareil.

2. Faites défiler jusqu'à la section **Mot de passe administrateur de l’appareil** . Fournir un mot de passe administrateur contient à partir de 8 à 15 caractères. Le mot de passe doit être une combinaison de majuscules, minuscules, numériques et caractères spéciaux.

3. Confirmez le mot de passe.

4. Cliquez sur **Enregistrer** dans la partie inférieure de la page.

Le mot de passe administrateur appareil doit maintenant être mis à jour. Vous pouvez utiliser ce mot de passe modifié pour accéder au périphérique localement.

## <a name="use-the-storsimple-virtual-array-web-ui-to-change-the-password"></a>Utiliser l’interface utilisateur du web tableau virtuel StorSimple pour modifier le mot de passe

Procédez comme suit pour modifier le mot de passe administrateur appareil via l’interface utilisateur web local.

#### <a name="to-change-the-device-administrator-password-via-the-local-web-ui"></a>Pour modifier le mot de passe administrateur appareil via l’interface utilisateur web local

1. Dans l’interface utilisateur web local, cliquez sur **gestion** > **modifier votre mot de passe** pour votre appareil.

    ![modifier le mot de passe1](./media/storsimple-ova-change-device-admin-password/image40.png)

2. Entrez le **mot de passe actuel**.

3. Fournir un **nouveau mot de passe**. Le mot de passe doit contenir au moins 8 caractères. Il doit contenir 3 sur 4 des éléments suivants : majuscules, minuscules, numériques et caractères spéciaux.

    Notez que votre mot de passe ne peut pas être identique aux dernière 24 mots de passe.

3. Retapez le mot de passe pour le confirmer.

    ![modifier password2](./media/storsimple-ova-change-device-admin-password/image41.png)

4. Dans la partie inférieure de la page, cliquez sur **Appliquer**. Le nouveau mot de passe est appliquée. Si la modification du mot de passe n’est pas réussie, vous verrez l’erreur suivante.

    ![Erreur de mot de passe](./media/storsimple-ova-change-device-admin-password/image42.png)

    Une fois que le mot de passe est correctement mis à jour, vous serez informé. Vous pouvez ensuite utiliser ce mot de passe modifié d’accéder au périphérique localement.

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur [l’administration de votre tableau virtuel StorSimple](storsimple-ova-web-ui-admin.md).

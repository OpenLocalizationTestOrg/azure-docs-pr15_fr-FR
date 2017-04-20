<properties 
   pageTitle="Modifier votre mot de passe StorSimple | Microsoft Azure" 
   description="Décrit comment utiliser le service Manager StorSimple pour modifier votre mot de passe administrateur Gestionnaire d’instantanés StorSimple et appareil." 
   services="storsimple" 
   documentationCenter="NA" 
   authors="alkohli" 
   manager="carmonm" 
   editor=""/>

<tags
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD" 
   ms.date="08/17/2016"
   ms.author="alkohli"/>

# <a name="use-the-storsimple-manager-service-to-change-your-storsimple-passwords"></a>Utiliser le service Manager StorSimple pour modifier votre mot de passe StorSimple

## <a name="overview"></a>Vue d’ensemble 

La page de **configuration** du portail classique Azure contient tous les paramètres de périphérique que vous pouvez reconfigurer sur un appareil StorSimple qui est géré par un service Manager StorSimple. Ce didacticiel explique comment vous pouvez utiliser la page **configurer** pour modifier votre administrateur de l’appareil ou le mot de passe Gestionnaire d’instantanés StorSimple.

## <a name="change-the-device-administrator-password"></a>Modifier le mot de passe administrateur

Lorsque vous utilisez interface Windows PowerShell pour accéder au périphérique StorSimple, vous devez entrer un mot de passe administrateur. Lorsque le premier appareil StorSimple est inscrit auprès d’un service, le mot de passe par défaut pour cette interface est *le mot de passe1*. Pour la sécurité de vos données, vous devez modifier ce mot de passe à la fin de la procédure d’inscription. Vous ne pouvez pas quitter le processus d’inscription sans modifier ce mot de passe. Pour plus d’informations, voir [étape 3 : configurer et inscrire le périphérique à utiliser Windows PowerShell pour StorSimple](storsimple-deployment-walkthrough-u2.md#step-3-configure-and-register-the-device-through-windows-powershell-for-storsimple).

Le mot de passe a été défini tout d’abord via l’interface Windows PowerShell lors de l’inscription puis peut être modifié via le portail classique Azure. Procédez comme suit pour modifier le mot de passe administrateur.

#### <a name="to-change-the-device-administrator-password"></a>Pour modifier le mot de passe administrateur

1. Dans le portail classique, cliquez sur **périphériques** > **configurer** pour votre appareil.

2. Faites défiler jusqu'à la section **Mot de passe administrateur de l’appareil** . Fournir un mot de passe administrateur contient à partir de 8 à 15 caractères. Le mot de passe doit être une combinaison de 3 ou plus de caractères en majuscules, minuscules, numériques et spéciaux.

3. Confirmez le mot de passe.

4. Cliquez sur **Enregistrer** dans la partie inférieure de la page.

Le mot de passe administrateur appareil doit maintenant être mis à jour. Vous pouvez utiliser ce mot de passe modifié pour accéder à l’interface Windows PowerShell.

## <a name="change-the-storsimple-snapshot-manager-password"></a>Modifier le mot de passe Gestionnaire d’instantanés StorSimple

Gestionnaire d’instantanés StorSimple logiciel se trouve sur votre hôte Windows et permet aux administrateurs de gérer les sauvegardes de votre appareil StorSimple sous la forme de local et en nuage instantanés.

Lorsque vous configurez un périphérique dans le Gestionnaire d’instantané StorSimple, vous devrez fournir l’adresse IP du périphérique et le mot de passe pour l’authentification de votre périphérique de stockage. Ce mot de passe est configuré tout d’abord via l’interface Windows PowerShell. Pour plus d’informations, voir [étape 3 : configurer et inscrire le périphérique à utiliser Windows PowerShell pour StorSimple](storsimple-deployment-walkthrough-u2.md#step-3-configure-and-register-the-device-through-windows-powershell-for-storsimple).

Le mot de passe a été défini tout d’abord via l’interface Windows PowerShell lors de l’inscription puis peut être modifié via le portail classique. Procédez comme suit pour modifier le mot de passe Gestionnaire d’instantanés StorSimple.

#### <a name="to-change-the-storsimple-snapshot-manager-password"></a>Pour modifier le mot de passe Gestionnaire d’instantanés StorSimple

1. Dans le portail classique, cliquez sur **périphériques** > **configurer** pour votre appareil.

2. Faites défiler jusqu'à la section **Gestionnaire d’instantanés StorSimple** . Entrez un mot de passe de 14 ou 15 caractères. Assurez-vous que le mot de passe contienne une combinaison de 3 ou plus de caractères en majuscules, minuscules, numériques et spéciaux.

3. Confirmez le mot de passe.

4. Cliquez sur **Enregistrer** dans la partie inférieure de la page.

Le mot de passe Gestionnaire d’instantanés StorSimple doit maintenant être mis à jour.
 

## <a name="next-steps"></a>Étapes suivantes

- En savoir plus sur la [sécurité StorSimple](storsimple-security.md).

- En savoir plus sur la [modification de la configuration de votre appareil](storsimple-modify-device-config.md).

- En savoir plus sur [l’utilisation du service Manager StorSimple pour administrer votre périphérique StorSimple](storsimple-manager-service-administration.md).

<properties
   pageTitle="Créer un package de prise en charge StorSimple | Microsoft Azure"
   description="Découvrez comment créer, déchiffrer et modifier un package de prise en charge pour votre appareil StorSimple."
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
   ms.date="08/17/2016"
   ms.author="alkohli" />


# <a name="create-and-manage-a-storsimple-support-package"></a>Créer et gérer un package de prise en charge StorSimple

## <a name="overview"></a>Vue d’ensemble

Un package de prise en charge StorSimple est un mécanisme facile à utiliser qui collecte tous les journaux pertinents pour faciliter le Microsoft Support dépannage des problèmes de périphérique StorSimple. Les fichiers journaux collectés sont chiffrés et compressés.

Ce didacticiel inclut des instructions étape par étape pour créer et gérer le package de prise en charge.

## <a name="create-and-upload-a-support-package-in-the-azure-classic-portal"></a>Créer et télécharger un package de prise en charge dans le portail classique Azure

Vous pouvez créer et télécharger un package de prise en charge sur le site Support Microsoft via la page de **Maintenance** du service dans le portail classique Azure.

> [AZURE.NOTE] Le téléchargement nécessite une clé de prise en charge. Votre technicien doit fournir ce vous dans un message électronique.

Un package de prise en charge chiffré et compressé (fichier .cab) est créé et téléchargé sur le site d’assistance. Le technicien peut ensuite extraire ce package à partir du site de prise en charge pour résoudre le problème.

Effectuez les opérations suivantes dans le portail classique pour créer un package de prise en charge.

#### <a name="to-create-a-support-package-in-the-azure-classic-portal"></a>Pour créer un package de prise en charge dans le portail classique Azure

1. Sélectionnez **périphériques** > **Maintenance**.

2. Dans la section **prend en charge package** , sélectionnez **créer et télécharger package de prise en charge**.

3. Dans la boîte de dialogue **créer et téléchargement du package d’assistance** , procédez comme suit :

    ![Créer le package de prise en charge](./media/storsimple-create-manage-support-package/IC740923.png)

    - Dans la zone de texte **Clé d’accès prise en charge** , entrez le code secret. Votre technicien du support technique Microsoft doit cette clé d’accès à vous envoyer par courrier électronique.

    - Activez la case à cocher pour fournir consentement pour charger automatiquement le package de prise en charge pour le site du Support Microsoft.

    - Cliquez sur l’icône de vérification ![Icône de vérification](./media/storsimple-create-manage-support-package/IC740895.png).


## <a name="manually-create-a-support-package"></a>Créer manuellement un package de prise en charge

Dans certains cas, vous devez créer manuellement le package de prise en charge par le biais de Windows PowerShell pour StorSimple. Par exemple :

- Si vous avez besoin de supprimer des informations sensibles de vos fichiers journaux avant le partage avec Support Microsoft.

- Si vous rencontrez des difficultés pour télécharger le package en raison de problèmes de connectivité.

Vous pouvez partager votre package de prise en charge générée manuellement avec Support Microsoft par courrier électronique. Procédez comme suit pour créer un package de prise en charge dans Windows PowerShell pour StorSimple.

#### <a name="to-create-a-support-package-in-windows-powershell-for-storsimple"></a>Pour créer un package de prise en charge dans Windows PowerShell pour StorSimple

1. Pour démarrer une session Windows PowerShell en tant qu’administrateur sur l’ordinateur distant est utilisé pour vous connecter à votre appareil StorSimple, entrez la commande suivante :

    `Start PowerShell`

2. Dans la session Windows PowerShell, connectez-vous à la SSAdmin Console de votre appareil :

    - À l’invite de commandes, tapez :

        `$MS = New-PSSession -ComputerName <IP address for DATA 0> -Credential SSAdmin -ConfigurationName "SSAdminConsole"`

    1. Dans la boîte de dialogue qui s’ouvre, entrez votre mot de passe administrateur. Le mot de passe par défaut est la suivante :

        `Password1`

        ![Boîte de dialogue informations d’identification PowerShell](./media/storsimple-create-manage-support-package/IC740962.png)

    2. Sélectionnez **OK**.
    1. À l’invite de commandes, tapez :

        `Enter-PSSession $MS`

3. Dans la session qui s’ouvre, entrez la commande appropriée.

    - Pour les partages réseau qui sont protégés par mot de passe, entrez :

        `Export-HcsSupportPackage –PackageTag "MySupportPackage" –Credential "Username" -Force`

        Vous serez invité pour un mot de passe, un chemin d’accès au dossier partagé en réseau et un mot de passe de chiffrement (parce que le package de prise en charge est chiffré). Un package de prise en charge est ensuite créé dans le dossier spécifié.

    - Pour les partages qui ne sont pas protégés par mot de passe, vous n’avez pas besoin du `-Credential` paramètre. Entrez les informations suivantes :

        `Export-HcsSupportPackage –PackageTag "MySupportPackage" -Force`

        Le package de prise en charge est créé pour les deux contrôleurs dans le dossier partagé réseau spécifié. Il s’agit d’un fichier compressé chiffré qui peut être envoyé au Support Microsoft de résoudre les problèmes. Pour plus d’informations, voir [Contacter le support technique Microsoft](storsimple-contact-microsoft-support.md).


### <a name="the-export-hcssupportpackage-cmdlet-parameters"></a>Les paramètres d’applet de commande Exporter HcsSupportPackage
Vous pouvez utiliser les paramètres suivants avec l’applet de commande Exporter HcsSupportPackage.

| Paramètre            | Obligatoire/facultatif | Description                                                                                                                                                             |
|----------------------|-------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `-Path`                 | Obligatoire          | Utilisez pour indiquer l’emplacement du dossier réseau partagé dans lequel se trouve le package de prise en charge.                                                                 |
| `-EncryptionPassphrase` | Obligatoire          | Permet de fournir un mot de passe pour aider à chiffrer le package de prise en charge.                                                                                                        |
| `-Credential`           | Facultatif          | Permet de fournir des informations d’accès pour le dossier réseau partagé.                                                                                        |
| `-Force`                | Facultatif          | Utilisez cette option pour ignorer l’étape de confirmation de mot de passe de chiffrement.                                                                                                                |
| `-PackageTag`           | Facultatif          | Permet de spécifier un répertoire sous *chemin d’accès* dans lequel se trouve le package de prise en charge. La valeur par défaut est [nom de l’appareil]-[date du jour et time:yyyy-MM-dd-HH-mm-ss].       |
| `-Scope`                | Facultatif          | Spécifier comme **Cluster** (par défaut) pour créer un package de prise en charge pour les deux contrôleurs. Si vous voulez créer un package uniquement pour le contrôleur actuel, spécifiez **contrôleur**. |


## <a name="edit-a-support-package"></a>Modifier un package de prise en charge

Une fois que vous avez généré un package de prise en charge, vous devrez peut-être modifier le package pour supprimer des informations sensibles. Cela peut inclure les noms de volume, adresses IP appareil et sauvegarde des fichiers journaux.

> [AZURE.IMPORTANT] Vous pouvez uniquement modifier un package de support qui a été généré par le biais de Windows PowerShell pour StorSimple. Vous ne pouvez pas modifier un lot créé dans le portail classique Azure avec service Manager StorSimple.

Pour modifier un package de prise en charge avant de le télécharger sur le site Support Microsoft, déchiffrer tout d’abord le package de prise en charge, modifier les fichiers et chiffrer nouveau. Procédez comme suit.

#### <a name="to-edit-a-support-package-in-windows-powershell-for-storsimple"></a>Pour modifier un lot de prise en charge dans Windows PowerShell pour StorSimple

1. Générer un package de prise en charge, comme décrit plus haut, dans [pour créer un package de prise en charge dans Windows PowerShell pour StorSimple](#to-create-a-support-package-in-windows-powershell-for-storsimple).

2. [Téléchargez le script](http://gallery.technet.microsoft.com/scriptcenter/Script-to-decrypt-a-a8d1ed65) localement sur votre client.

3. Importer le module Windows PowerShell. Spécifiez le chemin d’accès au dossier local dans lequel vous avez téléchargé le script. Pour importer le module, tapez :

    `Import-module <Path to the folder that contains the Windows PowerShell script>`

4. Tous les fichiers sont des fichiers *.aes* qui sont compressées et chiffrées. Pour décompresser et déchiffrer des fichiers, entrez :

    `Open-HcsSupportPackage <Path to the folder that contains support package files>`

    Notez que les extensions de fichier réel sont désormais affichées pour tous les fichiers.

    ![Modifier le package de prise en charge](./media/storsimple-create-manage-support-package/IC750706.png)

5. Lorsque vous êtes invité au mot de passe de chiffrement, entrez le mot de passe que vous avez utilisé lorsque le package de prise en charge a été créé.

        cmdlet Open-HcsSupportPackage at command pipeline position 1

        Supply values for the following parameters:EncryptionPassphrase: ****

6. Accédez au dossier contenant les fichiers journaux. Étant donné que les fichiers journaux sont désormais décompressés et déchiffrés, les alias comportent des extensions de fichier d’origine. Modifier ces fichiers pour supprimer les informations spécifiques au client, telles que les noms de volume et adresses IP appareil, puis enregistrez les fichiers.

7. Fermez les fichiers pour compresser avec gzip et les chiffrer avec AES-256. Il s’agit de la vitesse et la sécurité de transfert le package de prise en charge sur un réseau. Pour compresser et chiffrer des fichiers, entrez les informations suivantes :

    `Close-HcsSupportPackage <Path to the folder that contains support package files>`

    ![Modifier le package de prise en charge](./media/storsimple-create-manage-support-package/IC750707.png)

8. Lorsque vous y êtes invité, fournir un mot de passe de chiffrement pour le package de prise en charge modifiée.

        cmdlet Close-HcsSupportPackage at command pipeline position 1
        Supply values for the following parameters:EncryptionPassphrase: ****

9. Notez le nouveau mot de passe, de sorte que vous pouvez le partager avec Support Microsoft lors d’une demande.


### <a name="example-editing-files-in-a-support-package-on-a-password-protected-share"></a>Exemple : Modification de fichiers dans un package de prise en charge sur un partage protégé par mot de passe

L’exemple suivant montre comment déchiffrer, modifier et chiffrer à nouveau un package de prise en charge.

        PS C:\WINDOWS\system32> Import-module C:\Users\Default\StorSimple\SupportPackage\HCSSupportPackageTools.psm1

        PS C:\WINDOWS\system32> Open-HcsSupportPackage \\hcsfs\Logs\TD48\TD48Logs\C0-A\etw

        cmdlet Open-HcsSupportPackage at command pipeline position 1

        Supply values for the following parameters:

        EncryptionPassphrase: ****

        PS C:\WINDOWS\system32> Close-HcsSupportPackage \\hcsfs\Logs\TD48\TD48Logs\C0-A\etw

        cmdlet Close-HcsSupportPackage at command pipeline position 1

        Supply values for the following parameters:

        EncryptionPassphrase: ****

        PS C:\WINDOWS\system32>

## <a name="next-steps"></a>Étapes suivantes

- Découvrez comment [utiliser packages prise en charge et les fichiers journaux appareil à résoudre les problèmes de déploiement de votre appareil](storsimple-troubleshoot-deployment.md#support-packages-and-device-logs-available-for-troubleshooting).

- Découvrez comment [utiliser le service du Gestionnaire de StorSimple pour administrer votre périphérique StorSimple](storsimple-manager-service-administration.md).

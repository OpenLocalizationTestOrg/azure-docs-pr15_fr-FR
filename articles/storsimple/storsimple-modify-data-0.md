<properties 
   pageTitle="Modifier les données 0 paramètres sur un appareil StorSimple | Microsoft Azure"
   description="Découvrez comment utiliser Windows PowerShell pour StorSimple reconfigurer l’interface de 0 réseau de données sur votre appareil StorSimple."
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

# <a name="modify-the-data-0-network-interface-settings-on-your-storsimple-device"></a>Modifier les paramètres de l’interface réseau 0 données sur votre appareil StorSimple

## <a name="overview"></a>Vue d’ensemble

Votre périphérique Microsoft Azure StorSimple dispose de six interfaces réseau, à partir des données 0 à 5 de données. Les données 0 interface est toujours configuré via l’interface Windows PowerShell ou la console série et est automatiquement activé pour le cloud. Notez que vous ne pouvez pas configurer interface 0 réseau de données via le portail classique Azure. 

Les données 0 interface est configurée premières étapes de l’Assistant de configuration lors de déploiement de l’appareil StorSimple initial. Lorsque l’appareil est dans un mode de fonctionnement, vous devrez peut-être reconfigurer données 0 paramètres. Ce didacticiel fournit deux méthodes pour modifier les données 0 paramètres réseau, les deux à Windows PowerShell pour StorSimple.

Après la lecture de ce didacticiel, vous pourrez :

- Modifier les données 0 réglage via l’Assistant Configuration de réseau
- Modifier les paramètres de 0 réseau de données via la `Set-HcsNetInterface` applet de commande



## <a name="modify-data-0-network-settings-through-setup-wizard"></a>Modifier les paramètres de réseau 0 données l’Assistant Installation
Vous pouvez reconfigurer les paramètres du réseau 0 données en vous connectant à l’interface Windows PowerShell de votre appareil StorSimple et lancement d’une session de l’Assistant de configuration. Effectuez les opérations suivantes pour modifier les données 0 paramètres :

#### <a name="to-modify-data-0-network-settings-through-setup-wizard"></a>Pour modifier les données des paramètres de l’Assistant Installation de réseau 0

1. Dans le menu de la console série, choisissez l’option 1, **vous connecter avec un accès complet**. Lorsque vous êtes invité fournir le **mot de passe administrateur**. Le mot de passe par défaut est `Password1`.

2. À l’invite de commandes, tapez :

    `Invoke-HcsSetupWizard`

3. Un Assistant installation s’affiche pour vous aider à configurer les données 0 interface de votre appareil. Fournir de nouvelles valeurs pour l’adresse IP, la passerelle et masque de réseau.

> [AZURE.NOTE] Les adresses IP de contrôleurs fixes doit être reconfigurés via la page **configuration** de l’appareil StorSimple dans le portail classique Azure. Pour plus d’informations, accédez à [Modifier les interfaces réseau](storsimple-modify-device-config.md#modify-network-interfaces).


## <a name="modify-data-0-network-settings-through-set-hcsnetinterface-cmdlet"></a>Modifier les paramètres de 0 réseau de données via l’applet de commande Set-HcsNetInterface
Une autre façon de reconfigurer données 0 interface réseau est à l’aide de la `Set-HcsNetInterface` applet de commande. L’applet de commande est exécutée à partir de l’interface Windows PowerShell de votre appareil StorSimple. Lorsque vous utilisez cette procédure, le contrôleur fixé adresses IP peut également être configuré ici. Effectuez les opérations suivantes pour modifier les données 0 paramètres : 

#### <a name="to-modify-data-0-network-settings-through-the-set-hcsnetinterface-cmdlet"></a>Pour modifier les paramètres de 0 réseau de données via l’applet de commande Set-HcsNetInterface

1. Dans le menu de la console série, choisissez l’option 1, **vous connecter avec un accès complet**. Lorsque vous êtes invité fournir le mot de passe administrateur. Le mot de passe par défaut est `Password1`.

2. À l’invite de commandes, tapez :

    `Set-HCSNetInterface -InterfaceAlias Data0 -IPv4Address <> -IPv4Netmask <> -IPv4Gateway <> -Controller0IPv4Address <> -Controller1IPv4Address <> -IsiScsiEnabled 1 -IsCloudEnabled 1`
    
    Dans les crochets, tapez les valeurs suivantes pour les données de 0 :
                                            
    - Adresses IPv4
    
    - Passerelle IPv4
    
    - Masque IPv4
    
    - Adresses IPv4 fixe pour contrôleur de 0

    - Adresse IPv4 fixe pour contrôleur 1

    Pour plus d’informations sur l’utilisation de cette applet de commande, accédez à [Windows PowerShell pour la référence StorSimple applet de commande](https://technet.microsoft.com/library/dn688161.aspx).

## <a name="next-steps"></a>Étapes suivantes

- Pour configurer des interfaces réseau autres que celles de 0, vous pouvez utiliser la [page Configurer dans le portail classique Azure](storsimple-modify-device-config.md). 

- Si vous rencontrez des problèmes lors de la configuration des interfaces réseau, reportez-vous à [résoudre des problèmes de déploiement](storsimple-troubleshoot-deployment.md).


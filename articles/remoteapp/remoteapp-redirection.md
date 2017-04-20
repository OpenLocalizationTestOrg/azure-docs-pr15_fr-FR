<properties
    pageTitle="Utilisation de la redirection dans Azure RemoteApp | Microsoft Azure"
    description="Découvrez comment configurer et utiliser la redirection dans RemoteApp"
    services="remoteapp"
    documentationCenter=""
    authors="lizap"
    manager="mbaldwin" />

<tags
    ms.service="remoteapp"
    ms.workload="compute"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/15/2016"
    ms.author="elizapo" />

# <a name="using-redirection-in-azure-remoteapp"></a>Utilisation de la redirection dans Azure RemoteApp

> [AZURE.IMPORTANT]
> RemoteApp Azure a été interrompu. Lire l' [annonce](https://go.microsoft.com/fwlink/?linkid=821148) pour plus d’informations.

La redirection de périphérique permet à vos utilisateurs interagir avec les applications à distance en utilisant les unités attachées à son ordinateur local, un téléphone ou une tablette. Par exemple, si vous avez fourni Skype via Azure RemoteApp, l’utilisateur doit la webcam est installée sur leur ordinateur pour l’utiliser avec Skype. Cela est également vrai pour des imprimantes, des haut-parleurs, moniteurs et un périphériques connecté à la plage de USB.

RemoteApp utilise le protocole RDP (Remote Desktop) et RemoteFX pour fournir la redirection.

## <a name="what-redirection-is-enabled-by-default"></a>Quels la redirection est activée par défaut ?
Lorsque vous utilisez RemoteApp, les redirections suivantes sont activées par défaut. Des informations entre parenthèses afficher le paramètre RDP.

- Lire les sons de l’ordinateur local (**lire sur cet ordinateur**). (audiomode:i:0)
- Capturer les données audio à partir de l’ordinateur local et envoyer vers l’ordinateur distant (**enregistrement à partir de cet ordinateur**). (audiocapturemode:i:1)
- Imprimer sur des imprimantes locales (redirectprinters:i:1)
- Ports COM (redirectcomports:i:1)
- Carte à puce appareil (redirectsmartcards:i:1)
- Presse-papiers (possibilité de copier et coller) (redirectclipboard:i:1)
- Désactivez le lissage des polices type (autoriser lissage des polices : i:1)
- Rediriger les périphériques Plug and Play toutes prises en charge. (devicestoredirect:s : *)

## <a name="what-other-redirection-is-available"></a>Quelles autres la redirection n’est disponible ?
Deux options de redirection sont désactivées par défaut :

- La redirection du lecteur (mappage du lecteur) : lecteurs de votre ordinateur local deviennent des lecteurs mappés dans la session distante. Cela vous permet de vous enregistrer ou ouvrir des fichiers à partir de vos disques durs locaux pendant que vous travaillez dans la session distante.
- Redirection USB : vous pouvez utiliser les périphériques USB connectés à votre ordinateur local dans la session distante.

## <a name="change-your-redirection-settings-in-remoteapp"></a>Modifier vos paramètres de redirection dans RemoteApp
Vous pouvez modifier les paramètres de redirection appareil pour une collection de sites à l’aide de Microsoft Azure PowerShell avec SDK. Après avoir installé le nouveau PowerShell et le Kit de développement logiciel, tout d’abord configurez-le pour gérer vos abonnements comme décrit dans [comment installer et configurer Azure PowerShell](../powershell-install-configure.md).

Puis, utilisez une commande semblable à ce qui suit pour définir les propriétés RDP personnalisées :

    Set-AzureRemoteAppCollection -CollectionName <collection name>  -CustomRdpProperty "drivestoredirect:s:*`nusbdevicestoredirect:s:*"

(Notez que *n* est utilisé comme séparateur entre les propriétés individuelles.)

Pour obtenir une liste des propriétés RDP personnalisées sont configurées, exécutez l’applet de commande suivante. Notez que les propriétés personnalisées uniquement sont représentées sous forme de résultats de sortie et pas les propriétés par défaut :  

    Get-AzureRemoteAppCollection -CollectionName <collection name>

Lorsque vous définissez les propriétés personnalisées que vous devez spécifier toutes les propriétés personnalisées chaque fois ; dans le cas contraire le paramètre revient à désactivé.   

### <a name="common-examples"></a>Exemples courants
Utiliser l’applet de commande suivante pour activer la redirection de lecteur :  

    Set-AzureRemoteAppCollection -CollectionName <collection name>  -CustomRdpProperty "drivestoredirect:s:*”

Cette applet de commande permet d’activer USB et lecteur la redirection :

    Set-AzureRemoteAppCollection -CollectionName <collection name>  -CustomRdpProperty "drivestoredirect:s:*`nusbdevicestoredirect:s:*"

Utilisez cette applet de commande pour désactiver le partage du Presse-papiers :  

    Set-AzureRemoteAppCollection -CollectionName <collection name>  -CustomRdpProperty "redirectclipboard:i:0”

> [AZURE.IMPORTANT] Veillez à déconnecter complètement tous les utilisateurs de la collection de sites (et pas seulement les déconnecter) avant de tester la modification. Pour vous assurer que les utilisateurs sont déconnectés complètement, accédez à l’onglet **Sessions** dans la collection de sites dans le portail Azure et déconnectez-vous de tous les utilisateurs qui sont déconnectés ou connectés. Parfois, il peut prendre quelques secondes pour les lecteurs locaux à afficher dans l’Explorateur au sein de la session.

## <a name="change-usb-redirection-settings-on-your-windows-client"></a>Modifier les paramètres de redirection USB sur votre ordinateur client Windows

Si vous souhaitez utiliser la redirection USB sur un ordinateur qui se connecte à RemoteApp, il existe 2 actions à effectuer. 1 - votre administrateur doit activer la redirection USB au niveau de la collection de sites à l’aide de PowerShell Azure. 2 - sur chaque appareil l’endroit où vous souhaitez utiliser la redirection USB, vous devez activer une stratégie de groupe qui permet de. Cette étape doit être effectuée pour chaque utilisateur qui souhaite utiliser la redirection USB.

> [AZURE.NOTE] Redirection USB avec Azure RemoteApp est uniquement prise en charge pour les ordinateurs Windows.

### <a name="enable-usb-redirection-for-the-remoteapp-collection"></a>Activer la redirection USB pour la collection de sites RemoteApp
Utiliser l’applet de commande suivante pour activer la redirection USB au niveau de la collection de sites :

    Set-AzureRemoteAppCollection -CollectionName <collection_name> -CustomRdpProperty "nusbdevicestoredirect:s:*"

### <a name="enable-usb-redirection-for-the-client-computer"></a>Activer la redirection USB de l’ordinateur client

Pour configurer les paramètres de redirection USB sur votre ordinateur :

1. Ouvrez l’éditeur de stratégie de groupe (GPEDIT. MSC). (Exécutez gpedit.msc à partir d’une invite de commandes).
2. Ouvrez **ordinateur Configuration ordinateur\Stratégies\Modèles administration\Composants Windows\Services Bureau distance\Hôte connexion Bureau Client\RemoteFX USB la Redirection de périphérique**.
3. Double-cliquez sur **la redirection de RDP autoriser d’autres appareils RemoteFX USB pris en charge de cet ordinateur**.
4. Sélectionnez **activé**, puis **administrateurs et utilisateurs dans les droits d’accès RemoteFX USB Redirection**.
5. Ouvrez une invite de commandes avec des autorisations d’administration, puis exécutez la commande suivante :

        gpupdate /force
6. Redémarrez l’ordinateur.

Vous pouvez également utiliser l’outil de gestion de stratégie de groupe pour créer et appliquer la stratégie de redirection USB pour tous les ordinateurs de votre domaine :

1. Connectez-vous le contrôleur de domaine en tant qu’administrateur de domaine.
2. Ouvrez la Console de gestion des stratégies de groupe. (Cliquez sur **Démarrer > Outils d’administration > Gestion des stratégies de groupe**.)
3. Accédez au domaine ou unité d’organisation pour lequel vous voulez créer la stratégie.
4. Avec le bouton droit de **La stratégie de domaine par défaut**, puis cliquez sur **Modifier**.
5. Ouvrez **ordinateur Configuration ordinateur\Stratégies\Modèles administration\Composants Windows\Services Bureau distance\Hôte connexion Bureau Client\RemoteFX USB la Redirection de périphérique**.
6. Double-cliquez sur **la redirection de RDP autoriser d’autres appareils RemoteFX USB pris en charge de cet ordinateur**.
7. Sélectionnez **activé**, puis **administrateurs et utilisateurs dans les droits d’accès RemoteFX USB Redirection**.
8. Cliquez sur **OK**.  

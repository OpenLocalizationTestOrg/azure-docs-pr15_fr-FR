<properties 
    pageTitle="Comment rediriger les périphériques USB dans Azure RemoteApp ? | Microsoft Azure" 
    description="Découvrez comment utiliser la redirection pour les périphériques USB dans Azure RemoteApp." 
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



# <a name="how-do-you-redirect-usb-devices-in-azure-remoteapp"></a>Comment rediriger les périphériques USB dans Azure RemoteApp ?

> [AZURE.IMPORTANT]
> RemoteApp Azure a été interrompu. Lire l' [annonce](https://go.microsoft.com/fwlink/?linkid=821148) pour plus d’informations.

La redirection de périphérique permet aux utilisateurs d’utiliser les périphériques USB connectés à leur ordinateur ou une tablette à partir des applications dans Azure RemoteApp. Par exemple, si vous avez partagé Skype via Azure RemoteApp, vos utilisateurs doivent être en mesure d’utiliser leurs appareils appareil.

Avant de poursuivre, vérifiez que vous lisez les informations de redirection USB [en utilisant](remoteapp-redirection.md)la redirection dans Azure RemoteApp. Cependant, recommandé nusbdevicestoredirect:s : * ne convient pas webcams USB et peut ne pas fonctionner pour certaines imprimantes USB ou appareils fonctions multiples USB. Par la conception et pour des raisons de sécurité, l’administrateur d’Azure RemoteApp dispose d’activer la redirection en classe de périphérique GUID ou par ID de l’instance périphérique avant que vos utilisateurs peuvent utiliser ces appareils.

Bien que cet article traite de la redirection de caméra web, vous pouvez utiliser une approche similaire pour rediriger imprimantes USB et autres périphériques de fonctions multiples USB qui ne sont pas redirigés par la **nusbdevicestoredirect:s :*** commande.

## <a name="redirection-options-for-usb-devices"></a>Options de redirection des périphériques USB
RemoteApp Azure utilise beaucoup mécanismes afin de rediriger les périphériques USB que celles disponibles pour les Services Bureau à distance. La technologie sous-jacente vous permet de choisir la méthode correcte de redirection pour un périphérique donné, de tirer le meilleur des deux principales et un périphérique RemoteFX USB à l’aide de la redirection la **usbdevicestoredirect:s :** commande. Il existe quatre éléments à cette commande :

| Ordre de traitement | Paramètre           | Description                                                                                                                |
|------------------|---------------------|----------------------------------------------------------------------------------------------------------------------------|
| 1                | *                   | Sélectionne tous les périphériques qui ne sont pas prises en compte par la redirection de haut niveau. Remarque : par leur conception, * ne fonctionne pas pour webcams USB.  |
|                  | {GUID de classe appareil} | Sélectionne tous les périphériques qui correspondent à la classe d’installation de périphérique spécifié.                                                           |
|                  | USB\InstanceID      | Sélectionner un périphérique USB spécifié pour l’ID de l’instance donnée.                                                                  |
| 2                | -ID USB\Instance    | Supprime les paramètres de redirection pour le périphérique spécifié.                                                                 |

## <a name="redirecting-a-usb-device-by-using-the-device-class-guid"></a>Rediriger un périphérique USB à l’aide de la classe de périphérique GUID
Il existe deux façons de trouver la classe de périphérique GUID qui peut être utilisé pour la redirection. 

La première option consiste à utiliser la [Définies appareil configuration Classes disponibles pour les fournisseurs](https://msdn.microsoft.com/library/windows/hardware/ff553426.aspx). Sélectionnez la classe qui correspond le mieux à l’appareil connecté à l’ordinateur local. Pour les appareils photo numériques, cela peut être une classe de périphérique d’image ou un cours de périphérique de Capture vidéo. Vous devez effectuer des tests avec les classes de périphériques pour rechercher la classe correcte GUID qui fonctionne avec le périphérique USB connecté localement (dans notre cas la webcam).

Une meilleure ou la deuxième option consiste à procédez comme suit pour rechercher la classe périphérique spécifique GUID :

1. Ouvrez le Gestionnaire de périphériques, recherchez le périphérique qui est redirigé, puis cliquez sur celui-ci et ouvrez les propriétés.
![Ouvrez le Gestionnaire de périphériques](./media/remoteapp-usbredir/ra-devicemanager.png)
2. Sous l’onglet **Détails** , sélectionnez la propriété **Guid de classe**. La valeur qui s’affiche est la GUID de classe pour ce type d’appareil.
![Propriétés de la caméra](./media/remoteapp-usbredir/ra-classguid.png)
3. Utilisez la valeur Guid de classe rediriger les périphériques correspondant à celle-ci.

Par exemple :

        Set-AzureRemoteAppCollection -CollectionName <collection name> -CustomRdpProperty "nusbdevicestoredirect:s:<Class Guid value>"

Vous pouvez combiner plusieurs redirections périphérique dans l’applet de commande même. Par exemple : pour rediriger le stockage local et une webcam USB, applet de commande ressemble à ceci :

        Set-AzureRemoteAppCollection -CollectionName <collection name> -CustomRdpProperty "drivestoredirect:s:*`nusbdevicestoredirect:s:<Class Guid value>"

Lorsque vous définissez la redirection de périphérique en cours GUID tous les périphériques qui correspondent à ce cours GUID dans la collection spécifiée sont redirigés. Par exemple, s’il existe plusieurs ordinateurs sur le réseau local qui ont les mêmes webcams USB, vous pouvez exécuter une seule applet de commande pour rediriger toutes les caméras web.

## <a name="redirecting-a-usb-device-by-using-the-device-instance-id"></a>Rediriger un périphérique USB à l’aide de l’ID d’instance appareil

Si vous souhaitez un contrôle plus précis et que vous souhaitez contrôler la redirection par unité, vous pouvez utiliser le paramètre de redirection **USB\InstanceID** .

La partie plus difficile de cette méthode consiste à trouver l’ID instance du périphérique USB. Vous devez accéder à l’ordinateur et le périphérique USB spécifique. Puis procédez comme suit :

1. Activer la redirection de périphérique dans la Session Bureau à distance comme décrit dans [Comment puis-je utiliser mes périphériques et des ressources dans une session de bureau à distance ?](http://windows.microsoft.com/en-us/windows7/How-can-I-use-my-devices-and-resources-in-a-Remote-Desktop-session)
2. Ouvrez une connexion Bureau à distance, puis cliquez sur **Afficher les Options**.
3. Cliquez sur **Enregistrer sous** pour enregistrer les paramètres actuels de la connexion à un fichier RDP.  
    ![Enregistrer les paramètres en tant que fichier RDP](./media/remoteapp-usbredir/ra-saveasrdp.png)
4. Choisissez un nom de fichier et un emplacement, par exemple « MyConnection.rdp » et « PC\Documents ce » et enregistrez le fichier.
5. Ouvrez le fichier MyConnection.rdp à l’aide d’un éditeur de texte et recherchez l’ID d’instance de l’appareil que vous voulez rediriger.

À présent, utilisez l’ID d’instance dans l’applet de commande suivante :

    Set-AzureRemoteAppCollection -CollectionName <collection name> -CustomRdpProperty "nusbdevicestoredirect:s: USB\<Device InstanceID value>"



### <a name="help-us-help-you"></a>Aidez-nous à améliorer vous 
Saviez-vous qu’outre évaluation de cet article et présenter des observations vers le bas en dessous, vous pouvez apporter des modifications à l’article lui-même ? Un élément manquant ? Un problème ? Écrire quelque chose qui se trouve juste confusion ? Défiler vers le haut, cliquez sur **Modifier sur GitHub** pour apporter des modifications - ceux soient transférés vers nous pour révision et puis, une fois que nous se les, vous voyez vos modifications et les améliorations ici.
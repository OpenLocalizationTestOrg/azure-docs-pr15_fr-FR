<properties 
   pageTitle="PowerShell pour la gestion des périphériques StorSimple | Microsoft Azure"
   description="Découvrez comment utiliser Windows PowerShell pour StorSimple pour gérer votre appareil StorSimple."
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
   ms.date="08/18/2016"
   ms.author="alkohli@microsoft.com" />

# <a name="use-windows-powershell-for-storsimple-to-administer-your-device"></a>Utiliser Windows PowerShell pour StorSimple pour administrer votre appareil

## <a name="overview"></a>Vue d’ensemble

Windows PowerShell pour StorSimple fournit une interface de ligne de commande que vous pouvez utiliser pour gérer votre périphérique Microsoft Azure StorSimple. Comme son nom l’indique, il est une interface de ligne de commande Windows PowerShell basé, intégré dans un espace d’exploitation limitée. Du point de vue de l’utilisateur en ligne de commande, un espace d’exploitation contrainte apparaît sous la forme d’une version de Windows PowerShell à accès restreint. Tout en conservant certaines des fonctionnalités de base de Windows PowerShell, cette interface a dédié applets de commande supplémentaires qui s’adressent à la gestion de votre périphérique Microsoft Azure StorSimple. 

Cet article décrit la Windows PowerShell pour les fonctionnalités StorSimple, y compris comment vous pouvez vous connecter à cette interface et contient des liens vers des procédures étape par étape ou flux de travail que vous pouvez effectuer à l’aide de cette interface. Les flux de travail comprendre comment inscrire votre appareil, configurer l’interface réseau sur votre appareil, installez les mises à jour nécessitant le périphérique pour être en mode maintenance, modifier l’état du périphérique et résoudre les éventuels problèmes que vous pouvez rencontrer.

Après la lecture de cet article, vous pourrez :

- Se connecter à votre appareil StorSimple à l’aide de Windows PowerShell pour StorSimple.

- Administrer votre appareil StorSimple à l’aide de Windows PowerShell pour StorSimple.

- Obtenir de l’aide de Windows PowerShell pour StorSimple.

>[AZURE.NOTE]   

>- Windows PowerShell pour les applets de commande StorSimple permettent de gérer votre appareil StorSimple à partir d’une console série ou à distance via Windows PowerShell distante. Pour plus d’informations sur chacun des applets de commande individuels peuvent être utilisés dans cette interface, accédez à la [référence de l’applet de commande pour Windows PowerShell pour StorSimple](https://technet.microsoft.com/library/dn688168.aspx).

>- Les applets de commande Azure PowerShell StorSimple sont une autre collection des applets de commande qui vous permettent d’automatiser StorSimple niveaux de service et tâches de migration à partir de la ligne de commande. Pour plus d’informations sur les applets de commande PowerShell Azure pour StorSimple, accédez à la [référence d’applet de commande StorSimple Azure](https://msdn.microsoft.com/library/azure/dn920427.aspx).

Vous pouvez accéder au Windows PowerShell pour StorSimple à l’aide d’une des méthodes suivantes :

- [Se connecter à la console en série StorSimple appareil](#connect-to-windows-powershell-for-storsimple-via-the-device-serial-console)
- [Se connecter à distance à StorSimple à l’aide de Windows PowerShell](#connect-remotely-to-storsimple-using-windows-powershell-for-storsimple)
    

## <a name="connect-to-windows-powershell-for-storsimple-via-the-device-serial-console"></a>Se connecter à Windows PowerShell pour StorSimple via la console série appareil

Vous pouvez [Télécharger PuTTY](http://www.putty.org/) ou terminal similaire logiciel d’émulation pour se connecter à Windows PowerShell StorSimple. Vous devez configurer PuTTY spécifiquement pour accéder à l’appareil Microsoft Azure StorSimple. Les rubriques suivantes contiennent les étapes détaillées sur la façon de configurer PuTTy et se connecter à l’appareil. Différentes options de menu dans la console série sont également expliquées.

### <a name="putty-settings"></a>Paramètres mastic

Assurez-vous que vous utilisez les paramètres mastic suivants pour vous connecter à l’interface Windows PowerShell à partir de la console série.

#### <a name="to-configure-putty"></a>Pour configurer PuTTY

1. Dans la boîte de dialogue mastic **Reconfiguration** , dans le volet **catégories** , sélectionnez **clavier**.

2. Vérifiez que les options suivantes sont sélectionnées (il s’agit des paramètres par défaut lorsque vous démarrez une nouvelle session). 

  	|Élément de clavier|Sélectionnez|
  	|---|---|
  	|Touche Retour arrière|Contrôle- ? (127)|
  	|Touches accueil et de fin|Standard|
  	|Clavier et touches de fonction|ÉCHAP [n ~|
  	|État initial de touches du curseur|Normal|
  	|État initial du pavé numérique|Normal|
  	|Activer les fonctionnalités de clavier supplémentaires|Contrôle Alt est la différence entre GR|

    ![Paramètres mastic pris en charge](./media/storsimple-windows-powershell-administration/IC740877.png)

3. Cliquez sur **Appliquer**.

4. Dans le volet **catégories** , sélectionnez **traduction**.

5. Dans la zone de liste **jeu de caractères à distance** , sélectionnez **UTF-8**.

6. Sous **Gestion des caractères de dessin au trait**, sélectionnez **points de code Unicode utiliser dessin au trait**. L’illustration suivante montre les sélections mastic correctes.

    ![UTF Putty paramètres](./media/storsimple-windows-powershell-administration/IC740878.png)

7. Cliquez sur **Appliquer**.


Vous pouvez désormais utiliser PuTTY pour vous connecter à la console série appareil en procédant comme suit.

[AZURE.INCLUDE [storsimple-use-putty](../../includes/storsimple-use-putty.md)]


### <a name="about-the-serial-console"></a>À propos de la console en série

Lorsque vous accédez à l’interface de votre appareil StorSimple via la console série, un message de bannière est présenté Windows PowerShell, suivi des options de menu. 

Le message de bannière contient des informations de périphérique StorSimple base tels que le modèle de nom, la version du logiciel installée et état du contrôleur vous accédez à. L’image suivante montre un exemple d’un message de bannière.

![Message de bannière en série](./media/storsimple-windows-powershell-administration/IC741098.png)

>[AZURE.IMPORTANT] Vous pouvez utiliser le message de bannière pour déterminer si le contrôleur que vous êtes connecté à est Active ou Passive.

L’image suivante montre les différentes options d’espace d’exploitation sont disponibles dans le menu de la console série.

![Inscrire votre appareil 2](./media/storsimple-windows-powershell-administration/IC740906.png)

Vous pouvez choisir les paramètres suivants :

1. **Se connecter avec un accès complet** Cette option permet de vous connecter (avec les informations d’identification appropriées) à l’instance d’exécution **SSAdminConsole** sur le contrôleur local. (Le contrôleur local est le contrôleur qui accèdent actuellement via la console série de votre appareil StorSimple.) Cette option peut également être utilisée pour permettre le Support Microsoft pour accéder à espace d’exploitation illimitée (une session de prise en charge) pour résoudre les éventuels problèmes de périphériques possible. Une fois que vous utilisez l’option 1 pour vous connecter, vous pouvez autoriser le technicien de Support Microsoft accéder aux illimitée instance d’exécution en exécutant une applet de commande spécifique. Pour plus d’informations, reportez-vous à [Démarrer une session de prise en charge](storsimple-contact-microsoft-support.md#start-a-support-session-in-windows-powershell-for-storsimple).

2. **Se connecter au contrôleur de pair avec un accès complet** Cette option est identique à l’option 1, sauf que vous pouvez vous connecter (avec les informations d’identification appropriées) à l’instance d’exécution **SSAdminConsole** sur le contrôleur de pair. Le périphérique StorSimple étant un périphérique haute disponibilité avec deux contrôleurs dans une configuration active-passive, homologue fait référence à l’autre contrôleur de l’appareil que vous accédez à via la console série).
Similaire à l’option 1, cette option peut également être utilisée pour permettre le Support Microsoft pour accéder sans restriction instance d’exécution sur un contrôleur de pair.

3. **Se connecter avec un accès limité** Cette option est utilisée pour accéder à Windows PowerShell interface en mode limité. Vous n’êtes pas invité vos informations d’identification d’accès. Cette option se connecte à une instance d’exploitation plus restreint par rapport aux options 1 et 2.  Parmi les tâches qui sont disponibles via l’option 1 qui **ne peut pas* être effectuée dans cette instance d’exécution sont :

    - Réinitialiser les paramètres par défaut
    - Modifier le mot de passe
    - Activer ou désactiver l’accès au support
    - Appliquer des mises à jour
    - Installation de correctifs 
                                                

    >[AZURE.NOTE] **Il s’agit de l’option si vous avez oublié le mot de passe administrateur appareil et ne peut pas se connecter à l’option 1 ou 2.**

4. **Changer la langue** Cette option permet de modifier la langue d’affichage dans l’interface Windows PowerShell. Les langues prises en charge sont anglais, japonais, russe, Français, coréen, espagnol, italien, allemand, chinois et portugais (Brésil).


## <a name="connect-remotely-to-storsimple-using-windows-powershell-for-storsimple"></a>Se connecter à distance à StorSimple à l’aide de Windows PowerShell pour StorSimple

Vous pouvez utiliser Windows PowerShell distante pour vous connecter à votre appareil StorSimple. Lorsque vous vous connectez de cette façon, vous ne verrez pas un menu. (Vous voyez un menu uniquement si vous utilisez la console série sur l’appareil pour vous connecter. Se connecter à distance vous permet d’accéder directement à l’équivalent de « option 1 – accès complet » sur la console série.) Avec l’accès distant Windows PowerShell, vous vous connectez à une instance d’exploitation spécifique. Vous pouvez également spécifier la langue d’affichage. 

La langue d’affichage est indépendante de la langue que vous avez défini à l’aide de l’option **Modifier la langue** dans le menu de la console série. Session PowerShell distante se verront appliquer automatiquement les paramètres régionaux de l’appareil que vous vous connectez depuis si aucun n’est spécifié.

>[AZURE.NOTE] Si vous travaillez avec des hôtes virtuels Microsoft Azure et périphériques virtuels StorSimple, vous pouvez utiliser Windows PowerShell distante et l’hôte virtuel pour vous connecter à l’appareil virtuel. Si vous avez configuré un emplacement de partage sur l’hôte sur lequel vous souhaitez enregistrer les informations de la session Windows PowerShell, sachez que le principal tout le monde inclut uniquement les utilisateurs authentifiés. Par conséquent, si vous avez configuré la partager pour autoriser l’accès à tout le monde et que vous vous connectez sans spécifier des informations d’identification, l’utilisateur principal anonyme non authentifié est utilisée et vous verrez une erreur. Pour résoudre ce problème, sur le partage d’hôte vous devez activer le compte invité et l’invité donnez-lui accès complet au compte sur le partage ou vous devez spécifier des informations d’identification valides ainsi que l’applet de commande Windows PowerShell.

Vous pouvez utiliser HTTP ou HTTPS pour vous connecter via Windows PowerShell distante. Suivez les instructions dans les didacticiels suivants :

- [Se connecter à distance à l’aide de HTTP](storsimple-remote-connect.md#connect-through-http)
- [Se connecter à distance en utilisant HTTPS](storsimple-remote-connect.md#connect-through-https)

## <a name="connection-security-considerations"></a>Considérations sur la sécurité connexion

Lorsque vous choisissez comment vous connecter à Windows PowerShell pour StorSimple, procédez comme suit :

- Se connecter directement à la console série appareil est sécurisé, mais se connectant à la console série via les commutateurs réseau n’est pas. Être prudent du risque lors de la connexion au périphérique en série sur commutateurs réseau.

- Connexion via une session HTTP peut offrir davantage de sécurité que la connexion via la console série sur réseau. Il s’agit pas de la méthode la plus sûre, mais il est acceptable de réseaux approuvés.

- Connexion via une session HTTPS est la plus sûre et l’option recommandée.


## <a name="administer-your-storsimple-device-using-windows-powershell-for-storsimple"></a>Administrer votre périphérique StorSimple à l’aide de Windows PowerShell pour StorSimple
Le tableau suivant présente un résumé de toutes les tâches de gestion courantes et des flux de travail complexe qui peut être effectuée dans l’interface Windows PowerShell de votre appareil StorSimple. Pour plus d’informations sur chaque flux de travail, cliquez sur l’entrée appropriée dans le tableau.

#### <a name="windows-powershell-for-storsimple-workflows"></a>Windows PowerShell des flux de travail StorSimple

|Si vous voulez faire ceci...|Utilisez cette procédure.|
|---|---|
|Inscrire votre appareil|[Configurer et inscrire de l’appareil à l’aide de Windows PowerShell pour StorSimple](storsimple-deployment-walkthrough.md#step-3-configure-and-register-the-device-through-windows-powershell-for-storsimple) |
|Configurer le proxy web</br>Afficher les paramètres de proxy web|[Configurer le proxy web pour votre appareil StorSimple](storsimple-configure-web-proxy.md)|
|Modifier les paramètres de l’interface réseau 0 données sur votre appareil|[Modifier des données réseau 0 interface pour votre appareil StorSimple](storsimple-modify-data-0.md)|
|Arrêter un contrôleur </br> Redémarrer ou arrêter un contrôleur </br> Arrêter un périphérique</br>Réinitialisation du dispositif aux paramètres par défaut|[Gérer les périphériques contrôleurs](storsimple-manage-device-controller.md)|
|Installer les mises à jour du mode maintenance et correctifs|[Mettre à jour votre périphérique](storsimple-update-device.md)|
|Entrer en mode maintenance </br>Quitter le mode maintenance|[Modes du périphérique StorSimple](storsimple-device-modes.md)|
|Créer un package de prise en charge</br>Déprotéger et modifier un package de prise en charge|[Créer et gérer un package de prise en charge](storsimple-create-manage-support-package.md)|
|Démarrer une session de prise en charge</br>|[Démarrer une session de prise en charge dans Windows PowerShell pour StorSimple](/storsimple-contact-microsoft-support.md#start-a-support-session-in-windows-powershell-for-storsimple)
 

## <a name="get-help-in-windows-powershell-for-storsimple"></a>Obtenir de l’aide de Windows PowerShell pour StorSimple

Dans Windows PowerShell pour StorSimple, l’aide de l’applet de commande n’est disponible. Une version en ligne, à jour de cette aide est également disponible, que vous pouvez utiliser pour mettre à jour de l’aide sur votre système.

Obtenir de l’aide de cette interface est semblable à celui de Windows PowerShell et est prises en charge la plupart des applets de commande relatives à l’aide. Vous pouvez trouver l’aide de Windows PowerShell en ligne dans la bibliothèque TechNet : [écriture de scripts avec Windows PowerShell](http://go.microsoft.com/fwlink/?LinkID=108518).

Voici une brève description des types d’aide pour cette interface Windows PowerShell, y compris comment mettre à jour de l’aide.

#### <a name="to-get-help-for-a-cmdlet"></a>Pour obtenir de l’aide pour une applet de commande

- Pour obtenir de l’aide pour une applet de commande ou une fonction, utilisez la commande suivante :`Get-Help <cmdlet-name>`

- Pour obtenir de l’aide en ligne pour toute applet de commande, utilisez l’applet de commande précédente avec la `-Online` paramètre :`Get-Help <cmdlet-name> -Online`

- Pour vous aider complète, vous pouvez utiliser la `–Full` paramètre et pour obtenir des exemples, utilisez le `–Examples` paramètre.

#### <a name="to-update-help"></a>Pour mettre à jour de l’aide

Vous pouvez facilement mettre à jour l’aide de l’interface Windows PowerShell. Procédez comme suit pour mettre à jour de l’aide sur votre système.

#### <a name="to-update-cmdlet-help"></a>Pour mettre à jour de l’aide de l’applet de commande

1. Démarrez Windows PowerShell avec l’option **Exécuter en tant qu’administrateur** .

1. À l’invite de commandes, tapez : `Update-Help`

1. Les fichiers d’aide mis à jour seront installés.

1. Une fois que les fichiers d’aide sont installés, tapez : `Get-Help Get-Command`. Ceci affiche une liste des applets de commande pour lesquelles l’aide est disponible.


>[AZURE.NOTE] Pour obtenir une liste de toutes les applets de commande disponibles dans un espace d’exploitation, connectez-vous à l’option de menu correspondante et exécuter la `Get-Command` applet de commande.

## <a name="next-steps"></a>Étapes suivantes
Si vous rencontrez des problèmes avec votre appareil StorSimple lorsque vous effectuez une des flux de travail ci-dessus, reportez-vous aux [Outils de résolution des déploiements StorSimple](storsimple-troubleshoot-deployment.md#tools-for-troubleshooting-storsimple-deployments).


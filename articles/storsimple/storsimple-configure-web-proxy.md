<properties 
   pageTitle="Configurer le proxy web pour un appareil StorSimple | Microsoft Azure"
   description="Découvrez comment utiliser Windows PowerShell pour StorSimple pour configurer les paramètres de proxy web pour votre appareil StorSimple."
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

# <a name="configure-web-proxy-for-your-storsimple-device"></a>Configurer le proxy web pour votre appareil StorSimple

## <a name="overview"></a>Vue d’ensemble

Ce didacticiel décrit comment utiliser Windows PowerShell pour StorSimple pour configurer et afficher les paramètres de proxy web pour votre appareil StorSimple. Les paramètres de proxy web sont utilisées par le périphérique StorSimple lors de la communication avec le cloud. Un serveur proxy web est utilisé pour ajouter une autre couche de sécurité, filtre le contenu, cache afin de faciliter la bande passante ou même à l’aide avec analytique.

Proxy Web est une configuration facultative pour votre appareil StorSimple. Vous pouvez configurer proxy web uniquement par le biais de Windows PowerShell pour StorSimple. La configuration est un processus en deux étapes comme suit :

1. Vous configurez tout d’abord les paramètres de proxy web via l’Assistant de configuration ou de Windows PowerShell pour les applets de commande StorSimple.

2. Puis, vous activez les paramètres de proxy web configuré par le biais de Windows PowerShell pour les applets de commande StorSimple.

Une fois la configuration du proxy web terminée, vous pouvez afficher les paramètres de proxy web configurés dans le service Microsoft Azure StorSimple Manager et Windows PowerShell pour StorSimple. 

Après la lecture de ce didacticiel, vous pourrez :

- Configurer proxy web en utilisant l’Assistant Installation et les applets de commande
- Activer le proxy web à l’aide des applets de commande
- Afficher les paramètres de proxy web dans le portail classique Azure
- Résoudre les erreurs lors de la configuration du proxy web


## <a name="configure-web-proxy-via-windows-powershell-for-storsimple"></a>Configurer le proxy web par le biais de Windows PowerShell pour StorSimple

Vous appliquez une des opérations suivantes pour configurer les paramètres de proxy web :

- Assistant de configuration vous guide dans les étapes de configuration.

- Applets de commande Windows PowerShell pour StorSimple.

Chacune de ces méthodes est décrite dans les sections suivantes.

## <a name="configure-web-proxy-via-the-setup-wizard"></a>Configurer le proxy web via l’Assistant de configuration

Vous pouvez utiliser l’Assistant Configuration vous guide dans la procédure de configuration du proxy web. Effectuez les opérations suivantes pour configurer le proxy web sur votre appareil.

#### <a name="to-configure-web-proxy-via-the-setup-wizard"></a>Pour configurer le proxy web via l’Assistant de configuration

1. Dans le menu de la console série, choisissez l’option 1, **vous connecter avec un accès intégral** et fournir le **mot de passe administrateur**. Tapez la commande suivante pour démarrer une session de l’Assistant Configuration :

    `Invoke-HcsSetupWizard`

2. Si c’est la première fois que vous avez utilisé l’Assistant de configuration pour l’enregistrement du périphérique, vous devrez configurer tous les paramètres réseau requis jusqu'à ce que vous atteigniez la configuration du proxy web. Si votre appareil est déjà enregistré, vous pouvez accepter tous les paramètres réseau configuré jusqu'à ce que vous atteigniez la configuration du proxy web. Dans l’Assistant de configuration, lorsque vous êtes invité à configurer les paramètres de proxy web, tapez **Oui**.

3. Pour l' **URL du Proxy Web**, spécifiez l’adresse IP ou le nom de domaine complet (FQDN) de votre serveur proxy web et le numéro de port TCP que vous voulez que votre périphérique à utiliser lors de la communication avec le cloud. Utilisez le format suivant :

    `http://<IP address or FQDN of the web proxy server>:<TCP port number>`

    Par défaut, le numéro de port TCP 8080 est spécifié.

4. Choisissez le type d’authentification en tant que **NTLM**, **base**ou **Aucun**. Base est l’authentification moins sûre pour la configuration du serveur proxy. NT LAN Manager (NTLM) est un protocole d’authentification hautement sécurisé et complexe qui utilise un système de messagerie à trois voies (parfois quatre si intégrité supplémentaire est requise) pour authentifier un utilisateur. L’authentification par défaut est NTLM. Pour plus d’informations, voir [base](http://hc.apache.org/httpclient-3.x/authentication.html) et [l’authentification NTLM](http://hc.apache.org/httpclient-3.x/authentication.html). 

    > [AZURE.IMPORTANT] **Dans le service Gestionnaire StorSimple, les graphiques de surveillance appareil ne fonctionnent pas lors de la base ou l’authentification NTLM est activée dans la configuration du serveur proxy pour l’appareil. Pour les graphiques de surveillance pour l’utiliser, vous devez vous assurer que l’authentification est définie sur aucun.**

5. Si vous utilisez l’authentification, fournir un **Nom d’utilisateur du Proxy Web** et un **Mot de passe Proxy Web**. Vous devrez également confirmer le mot de passe.

    ![Configurer le Proxy Web sur périphérique1 StorSimple](./media/storsimple-configure-web-proxy/IC751830.png)

Si vous enregistrez votre appareil pour la première fois, passez à l’enregistrement. Si votre appareil a déjà été enregistré, l’Assistant va fermer. La configuration de ces paramètres est enregistrés.

Proxy Web est maintenant également activé. Vous pouvez ignorer cette étape [Activer proxy web](#enable-web-proxy) et accédez directement pour [Afficher les paramètres de proxy web dans le portail classique Azure](#view-web-proxy-settings-in-the-azure-classic-portal).


## <a name="configure-web-proxy-via-windows-powershell-for-storsimple-cmdlets"></a>Configurer le proxy web par le biais de Windows PowerShell pour les applets de commande StorSimple

Une autre façon de configurer les paramètres de proxy web est via le Windows PowerShell pour les applets de commande StorSimple. Effectuez les opérations suivantes pour configurer le proxy web.

#### <a name="to-configure-web-proxy-via-cmdlets"></a>Pour configurer le proxy web via les applets de commande

1. Dans le menu de la console série, choisissez l’option 1, **vous connecter avec un accès complet**. Lorsque vous y êtes invité, fournir le **mot de passe administrateur**. Le mot de passe par défaut est `Password1`.

2. À l’invite de commandes, tapez :

    `Set-HcsWebProxy -Authentication NTLM -ConnectionURI "<http://<IP address or FQDN of web proxy server>:<TCP port number>" -Username "<Username for web proxy server>"`

    Fournir et confirmer le mot de passe lorsque vous y êtes invité, comme illustré ci-dessous.

    ![Configurer le Proxy Web sur StorSimple Device3](./media/storsimple-configure-web-proxy/IC751831.png)

Le proxy web est désormais configuré et doit être activé.

## <a name="enable-web-proxy"></a>Activer le proxy web

Proxy Web est désactivée par défaut. Après avoir configuré les paramètres de proxy web sur votre appareil StorSimple, vous devez utiliser Windows PowerShell pour StorSimple pour activer les paramètres de proxy web.

> [AZURE.NOTE] **Cette étape ne sera pas obligatoire si vous avez utilisé l’Assistant de configuration pour configurer le proxy web. Proxy Web est automatiquement activée par défaut après une session de l’Assistant Configuration.**

Dans Windows PowerShell pour StorSimple à activer le proxy web sur votre appareil, effectuez les opérations suivantes :

#### <a name="to-enable-web-proxy"></a>Pour activer le proxy web

1. Dans le menu de la console série, choisissez l’option 1, **vous connecter avec un accès complet**. Lorsque vous y êtes invité, fournir le **mot de passe administrateur**. Le mot de passe par défaut est `Password1`.

2. À l’invite de commandes, tapez :

    `Enable-HcsWebProxy`

    Vous avez maintenant activé la configuration du proxy web sur votre appareil StorSimple.

    ![Configurer le Proxy Web sur StorSimple Device4](./media/storsimple-configure-web-proxy/IC751832.png)

## <a name="view-web-proxy-settings-in-the-azure-classic-portal"></a>Afficher les paramètres de proxy web dans le portail classique Azure

Les paramètres de proxy web sont configurées via l’interface Windows PowerShell et ne peuvent pas être modifiées dans le portail classique. Vous pouvez, cependant, afficher ces paramètres configurés dans le portail classique. Procédez comme suit pour afficher le proxy web.

#### <a name="to-view-web-proxy-settings"></a>Pour afficher les paramètres de proxy web
1. Accédez à **service Manager StorSimple > appareils**. Sélectionnez et cliquez sur un appareil, puis accédez à **configurer**.
1. Faites défiler vers le bas dans la page **configurer** à la section **paramètres de proxy Web** . Vous pouvez afficher les paramètres de proxy web configuré sur votre appareil StorSimple comme illustré ci-dessous.

    ![Mode Web Proxy portail de gestion](./media/storsimple-configure-web-proxy/ViewWebProxyPortal_M.png)
 
## <a name="errors-during-web-proxy-configuration"></a>Erreurs lors de la configuration du proxy web

Si les paramètres de proxy web ont été incorrectement configurés, messages d’erreur seront affichera à l’utilisateur dans Windows PowerShell pour StorSimple. Le tableau suivant décrit certaines de ces messages d’erreur, leurs causes possibles et les actions recommandées.

|Numéro de série.|Code d’erreur HRESULT|Causes possibles|Action recommandée|
|:---|:---|:---|:---|
|1.|0 x 80070001|Commande est exécutée à partir du contrôleur passif et il n’est pas en mesure de communiquer avec le contrôleur actif.|Exécutez la commande sur le contrôleur actif. Pour exécuter la commande à partir du contrôleur passif, vous devrez résoudre la connectivité entre passif et contrôleur active. Vous avez besoin de s’engager Support Microsoft si cette connectivité est interrompue.|
|2.|0x800710dd - l’identificateur d’opération n’est pas valide|Paramètres de proxy ne sont pas pris en charge sur périphérique virtuel StorSimple.|Paramètres de proxy ne sont pas pris en charge sur périphérique virtuel StorSimple. Celles-ci peuvent uniquement être configurées sur un appareil physique StorSimple.|
|3.|0 x 80070057 - paramètre non valide|L’un des paramètres fournis pour les paramètres de proxy n’est pas valide.|L’URI n’est pas fournie dans le format correct. Utilisez le format suivant :`http://<IP address or FQDN of the web proxy server>:<TCP port number>`|
|4.|0x800706BA - serveur RPC n’est pas disponible|La cause est une des opérations suivantes :</br></br>Cluster n’est pas disponible.</br></br>Service DataPath ne fonctionne pas.</br></br>La commande est exécutée à partir de contrôleur passif et il n’est pas en mesure de communiquer avec le contrôleur actif.|Veuillez engager du Support Microsoft pour vous assurer que le cluster est disponible et datapath service est en cours d’exécution.</br></br>Exécutez la commande à partir du contrôleur actif. Si vous souhaitez exécuter la commande à partir du contrôleur passif, vous avez besoin vérifier que le contrôleur passif peut communiquer avec le contrôleur actif. Vous avez besoin de s’engager Support Microsoft si cette connectivité est interrompue.|
|5.|0x800706be - appel RPC a échoué|Cluster ne fonctionne pas.|Veuillez engager du Support Microsoft pour vous assurer que le cluster est disponible.|
|6.|0x8007138f - ressource cluster introuvable|Ressource de cluster du service plateforme n’est pas trouvée. Cela peut se produire lorsque l’installation n’a pas correcte.|Vous devrez peut-être effectuer une usine réinitialisée sur votre appareil. Vous devrez peut-être créer une ressource de plateforme. Contactez le Support Microsoft pour obtenir la procédure suivante.|
|7.|0x8007138c - ressource cluster pas en ligne|Ressources de cluster plateforme ou datapath n’êtes pas connectés.|Veuillez contacter le Support Microsoft pour vous assurer que la ressource de service datapath et plateforme sont en ligne.|

> [AZURE.NOTE] 
> 
> -  La liste des messages d’erreur ci-dessus n’est pas exhaustive. 
> - Erreurs liées aux paramètres de proxy web seront affichera pas dans le portail classique Azure dans votre service Manager StorSimple. S’il existe un problème avec proxy web une fois la configuration terminée, l’état de l’appareil changeront sur **hors connexion** dans le portail classique. |

## <a name="next-steps"></a>Étapes suivantes

- Si vous rencontrez des problèmes lors du déploiement de votre appareil ou de configuration des paramètres de proxy web, reportez-vous à [résoudre les problèmes de votre déploiement du périphérique StorSimple](storsimple-troubleshoot-deployment.md).

- Pour apprendre à utiliser le service Manager StorSimple, accédez à [utiliser le service Manager StorSimple pour administrer votre périphérique StorSimple](storsimple-manager-service-administration.md).

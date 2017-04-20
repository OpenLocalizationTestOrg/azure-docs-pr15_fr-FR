<properties 
   pageTitle="Se connecter à distance sur votre appareil StorSimple | Microsoft Azure"
   description="Explique comment configurer votre appareil pour la gestion à distance et comment vous connecter à Windows PowerShell pour StorSimple via HTTP ou HTTPS."
   services="storsimple"
   documentationCenter=""
   authors="alkohli"
   manager="carmonm"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="06/21/2016"
   ms.author="alkohli" />

# <a name="connect-remotely-to-your-storsimple-device"></a>Se connecter à distance sur votre appareil StorSimple

## <a name="overview"></a>Vue d’ensemble

Vous pouvez utiliser Windows PowerShell distante pour vous connecter à votre appareil StorSimple. Lorsque vous vous connectez de cette façon, vous ne verrez pas un menu. (Vous voyez un menu uniquement si vous utilisez la console série sur le périphérique pour se connecter). Avec l’accès distant Windows PowerShell, vous vous connectez à une instance d’exploitation spécifique. Vous pouvez également spécifier la langue d’affichage. 

Pour plus d’informations sur l’utilisation de communication à distance de Windows PowerShell pour gérer votre appareil, accédez à [Utiliser Windows PowerShell pour StorSimple administrer votre appareil StorSimple](storsimple-windows-powershell-administration.md).

Ce didacticiel décrit comment configurer votre périphérique gestion à distance, puis comment se connecter à Windows PowerShell pour StorSimple. Vous pouvez utiliser HTTP ou HTTPS pour vous connecter via Windows PowerShell distante. Toutefois, lorsque vous choisissez comment vous connecter à Windows PowerShell pour StorSimple, procédez comme suit : 

- Se connecter directement à la console série appareil est sécurisé, mais se connectant à la console série via les commutateurs réseau n’est pas. Être prudent du risque lorsque vous vous connectez à la console série appareil sur commutateurs réseau. 

- Connexion via une session HTTP peut offrir davantage de sécurité qu’une connexion via la console série sur le réseau. Il s’agit pas de la méthode la plus sûre, mais il est acceptable de réseaux approuvés. 

- Connexion via une session HTTPS avec un certificat auto-signé est la plus sûre et l’option recommandée.

Vous pouvez vous connecter à distance à l’interface Windows PowerShell. Toutefois, accès à distance sur votre appareil StorSimple via l’interface Windows PowerShell n’est pas activée par défaut. Vous devez tout d’abord activer la gestion à distance sur l’appareil et, sur le client qui est utilisé pour accéder à votre appareil.

Les étapes décrites dans cet article ont été effectuées sur un système hôte exécutant Windows Server 2012 R2.

## <a name="connect-through-http"></a>Se connecter via HTTP

Connexion à Windows PowerShell pour StorSimple via une session HTTP offre davantage de sécurité qu’une connexion via la console série de votre appareil StorSimple. Il s’agit pas de la méthode la plus sûre, mais il est acceptable de réseaux approuvés.

Vous pouvez utiliser le portail classique Azure ou la console série pour configurer la gestion à distance. Sélectionner les procédures suivantes :

- [Utiliser le portail classique Azure pour activer la gestion à distance sur HTTP](#use-the-azure-classic-portal-to-enable-remote-management-over-http)

- [Utiliser la console série pour activer la gestion à distance sur HTTP](#use-the-serial-console-to-enable-remote-management-over-http)

Après avoir activé la gestion à distance, procédez comme suit pour préparer le client pour une connexion à distance.

- [Préparer le client de connexion à distance](#prepare-the-client-for-remote-connection)

### <a name="use-the-azure-classic-portal-to-enable-remote-management-over-http"></a>Utiliser le portail classique Azure pour activer la gestion à distance sur HTTP 

Effectuez les opérations suivantes dans le portail classique Azure pour activer la gestion à distance sur HTTP.

#### <a name="to-enable-remote-management-through-the-azure-classic-portal"></a>Pour activer la gestion à distance via le portail classique Azure

1. Accéder aux **périphériques** > **configurer** pour votre appareil.

2. Faites défiler jusqu'à la section **Gestion à distance** .

3. Définissez l’option **Activer la gestion à distance** sur **Oui**.

4. Vous pouvez à présent choisir pour vous connecter à l’aide de HTTP. (La valeur par défaut consiste à connecter via HTTPS). Vérifiez que HTTP est sélectionnée.

    >[AZURE.NOTE] Connexion sur HTTP est acceptable uniquement sur les réseaux approuvés.

6. Cliquez sur **Enregistrer** dans la partie inférieure de la page.

### <a name="use-the-serial-console-to-enable-remote-management-over-http"></a>Utiliser la console série pour activer la gestion à distance sur HTTP

Effectuez les opérations suivantes sur la console série appareil pour activer la gestion à distance.

#### <a name="to-enable-remote-management-through-the-device-serial-console"></a>Pour activer la gestion à distance via la console série appareil

1. Dans le menu de la console série, sélectionnez l’option 1. Pour plus d’informations sur l’utilisation de la console série sur l’appareil, accédez à [se connecter à Windows PowerShell pour StorSimple via console série appareil](storsimple-windows-powershell-administration.md#connect-to-windows-powershell-for-storsimple-via-device-serial-console).

2. À l’invite, tapez :`Enable-HcsRemoteManagement –AllowHttp`

3. Vous serez informé sur les problèmes de sécurité de l’utilisation de HTTP pour vous connecter à l’appareil. Lorsque vous y êtes invité, confirmez en le tapant **Y**.

4. Vérifiez que HTTP est activé en tapant :`Get-HcsSystem`

5. Vérifiez que le champ **RemoteManagementMode** affiche **HttpsAndHttpEnabled**. L’illustration suivante montre ces paramètres dans PuTTY.

     ![Série HTTPS et HTTP activée](./media/storsimple-remote-connect/HCS_SerialHttpsAndHttpEnabled.png)

### <a name="prepare-the-client-for-remote-connection"></a>Préparer le client de connexion à distance

Effectuez les opérations suivantes sur le client pour activer la gestion à distance.

#### <a name="to-prepare-the-client-for-remote-connection"></a>Pour préparer le client de connexion à distance

1. Démarrer une session Windows PowerShell en tant qu’administrateur.

2. Tapez la commande suivante pour ajouter l’adresse IP du périphérique StorSimple à la liste du client hôtes approuvés : 

     `Set-Item wsman:\localhost\Client\TrustedHosts <device_ip> -Concatenate -Force`

     Remplacez <*device_ip*> avec l’adresse IP de votre appareil ; par exemple : 

     `Set-Item wsman:\localhost\Client\TrustedHosts 10.126.173.90 -Concatenate -Force`

3. Tapez la commande suivante pour enregistrer les informations d’identification de l’appareil dans une variable : 

     *$cred = get-Credential*

4. Dans la boîte de dialogue qui s’affiche :

    1. Tapez le nom d’utilisateur au format suivant : *device_ip\SSAdmin*.
    2. Tapez le mot de passe administrateur appareil a été défini lorsque l’appareil a été configuré avec l’Assistant Configuration. Le mot de passe par défaut est *le mot de passe1*.

7. Démarrer une session Windows PowerShell sur l’appareil en tapant cette commande :

     `Enter-PSSession -Credential $cred -ConfigurationName SSAdminConsole -ComputerName <device_ip>`

     >[AZURE.NOTE] Pour créer une session Windows PowerShell pour une utilisation avec le périphérique virtuel StorSimple, ajoutez la `–Port` paramètre et spécifiez le port public que vous avez configuré dans l’accès distant pour dispositif virtuel StorSimple.

     À ce stade, vous devez disposer d’une session Windows PowerShell à distance active à l’appareil.

    ![Accès distant PowerShell à l’aide de HTTP](./media/storsimple-remote-connect/HCS_PSRemotingUsingHTTP.png)

## <a name="connect-through-https"></a>Se connecter via HTTPS

Connexion à Windows PowerShell pour StorSimple via une session HTTPS est la méthode la plus sûre et recommandée de la connexion à distance sur votre appareil Microsoft Azure StorSimple. Les procédures suivantes expliquent comment configurer les ordinateurs client console et en série afin que vous puissiez utiliser HTTPS pour se connecter à Windows PowerShell StorSimple.

Vous pouvez utiliser le portail classique Azure ou la console série pour configurer la gestion à distance. Sélectionner les procédures suivantes :

- [Utiliser le portail classique Azure pour activer la gestion à distance via HTTPS](#use-the-azure-classic-portal-to-enable-remote-management-over-https)

- [Utiliser la console série pour activer la gestion à distance via HTTPS](#use-the-serial-console-to-enable-remote-management-over-https)

Après avoir activé la gestion à distance, utilisez les procédures suivantes pour préparer l’hôte pour une gestion à distance et connectez-vous à l’appareil à partir de l’hôte distant.

- [Préparer l’hôte de gestion à distance](#prepare-the-host-for-remote-management)

- [Se connecter à l’appareil à partir de l’hôte distant](#connect-to-the-device-from-the-remote-host)

### <a name="use-the-azure-classic-portal-to-enable-remote-management-over-https"></a>Utiliser le portail classique Azure pour activer la gestion à distance via HTTPS

Effectuez les opérations suivantes dans le portail classique Azure pour activer la gestion à distance sur HTTPS.

#### <a name="to-enable-remote-management-over-https-from-the-azure-classic-portal"></a>Pour activer la gestion à distance sur HTTPS à partir du portail classique Azure

1. Accéder aux **périphériques** > **configurer** pour votre appareil.

2. Faites défiler jusqu'à la section **Gestion à distance** .

3. Définissez l’option **Activer la gestion à distance** sur **Oui**.

4. Vous pouvez à présent choisir pour vous connecter à l’aide de HTTPS. (La valeur par défaut consiste à connecter via HTTPS). Vérifiez que l’option HTTPS est sélectionnée. 

5. Cliquez sur **Télécharger le certificat de gestion à distance**. Spécifier un emplacement pour enregistrer ce fichier. Vous avez besoin installer ce certificat sur l’ordinateur client ou l’hôte que vous utiliserez pour vous connecter à l’appareil.

6. Cliquez sur **Enregistrer** dans la partie inférieure de la page.

### <a name="use-the-serial-console-to-enable-remote-management-over-https"></a>Utiliser la console série pour activer la gestion à distance via HTTPS

Effectuez les opérations suivantes sur la console série appareil pour activer la gestion à distance.

#### <a name="to-enable-remote-management-through-the-device-serial-console"></a>Pour activer la gestion à distance via la console série appareil

1. Dans le menu de la console série, sélectionnez l’option 1. Pour plus d’informations sur l’utilisation de la console série sur l’appareil, accédez à [se connecter à Windows PowerShell pour StorSimple via console série appareil](storsimple-windows-powershell-administration.md#connect-to-windows-powershell-for-storsimple-via-device-serial-console).

2. À l’invite, tapez : 

     `Enable-HcsRemoteManagement`

    Cela doit activer HTTPS sur votre appareil.

3. Vérifiez que HTTPS a été activé en tapant : 

     `Get-HcsSystem`

    Assurez-vous que le champ **RemoteManagementMode** affiche **HttpsEnabled**. L’illustration suivante montre ces paramètres dans PuTTY.

     ![Série HTTPS activé](./media/storsimple-remote-connect/HCS_SerialHttpsEnabled.png)

4. À partir de la `Get-HcsSystem`, copiez le numéro de série de l’appareil et l’enregistrer pour une utilisation ultérieure.

    >[AZURE.NOTE] Le numéro de série correspond au nom CN dans le certificat.

5. Obtenir un certificat de gestion à distance en tapant : 
 
     `Get-HcsRemoteManagementCert`

    Un certificat semblable au suivant s’affiche.

    ![Obtenir un certificat de gestion à distance](./media/storsimple-remote-connect/HCS_GetRemoteManagementCertificate.png)

5. Copiez les informations dans le certificat **situés à début certificat situés à** **situés à** fin certificat situés à dans un éditeur de texte tel que le bloc-notes et enregistrez-le sous forme de fichier .cer. (Vous allez copier ce fichier vers votre hôte distant lorsque vous vous préparez l’hôte).

    >[AZURE.NOTE] Pour générer un nouveau certificat, utilisez la `Set-HcsRemoteManagementCert` applet de commande.

### <a name="prepare-the-host-for-remote-management"></a>Préparer l’hôte de gestion à distance

Pour préparer l’ordinateur hôte une connexion à distance qui utilise une session HTTPS, procédez comme suit :

- [Importer le fichier .cer dans le magasin racine du client ou hôte distant](#to-import-the-certificate-on-the-remote-host).

- [Ajouter les numéros de série appareil au fichier hosts sur votre hôte distant](#to-add-device-serial-numbers-to-the-remote-host).

Chacune de ces procédures est décrite ci-dessous.

#### <a name="to-import-the-certificate-on-the-remote-host"></a>Pour importer le certificat sur l’hôte distant

1. Cliquez sur le fichier .cer, puis sélectionnez **installer certificat**. Assistant Importation de certificat démarre.

    ![Assistant Importation de certificat 1](./media/storsimple-remote-connect/HCS_CertificateImportWizard1.png)

2. Pour l' **emplacement de stockage**, sélectionnez **Ordinateur Local**, puis cliquez sur **suivant**.

3. Sélectionnez **placer tous les certificats dans le magasin suivant**, puis cliquez sur **Parcourir**. Accédez au magasin racine de votre hôte distant, puis cliquez sur **suivant**.

    ![Assistant Importation de certificat 2](./media/storsimple-remote-connect/HCS_CertificateImportWizard2.png)

4. Cliquez sur **Terminer**. Affiche un message vous indiquant que l’importation a réussi.

    ![Assistant Importation de certificat 3](./media/storsimple-remote-connect/HCS_CertificateImportWizard3.png)

#### <a name="to-add-device-serial-numbers-to-the-remote-host"></a>Pour ajouter des numéros de série d’appareil à l’hôte distant

1. Démarrez le bloc-notes en tant qu’administrateur et ouvrez le fichier hosts situé sous \Windows\System32\Drivers\etc.

2. Ajouter les trois entrées suivantes à votre fichier hosts : **adresse IP de données 0**, **adresse IP fixe contrôleur de 0**et **adresse IP fixe contrôleur 1**.

3. Entrez le numéro de série d’appareil que vous avez enregistré précédemment. Mapper vers l’adresse IP comme le montre l’image suivante. Pour contrôleur de 0 et 1 contrôleur, ajoutez **Controller0** et **: Contrôleur1** à la fin du numéro de série (nom NC).

    ![Ajout de nom CN au fichier hosts](./media/storsimple-remote-connect/HCS_AddingCNNameToHostsFile.png)

4. Enregistrer le fichier hosts.

### <a name="connect-to-the-device-from-the-remote-host"></a>Se connecter à l’appareil à partir de l’hôte distant

Utiliser Windows PowerShell et SSL pour entrer une session SSAdmin sur votre appareil à partir d’un hôte distant ou un client. La session SSAdmin correspond à l’option 1 dans le menu [console série](storsimple-windows-powershell-administration.md#connect-to-windows-powershell-for-storsimple-via-device-serial-console) de votre appareil.

Procédez comme suit sur l’ordinateur à partir de laquelle vous souhaitez établir la connexion à distance de Windows PowerShell.

#### <a name="to-enter-an-ssadmin-session-on-the-device-by-using-windows-powershell-and-ssl"></a>Pour entrer une session SSAdmin sur le périphérique à l’aide de Windows PowerShell et SSL

1. Démarrer une session Windows PowerShell en tant qu’administrateur.

2. Ajoutez l’adresse IP du périphérique à hôtes approuvés du client en tapant :

     `Set-Item wsman:\localhost\Client\TrustedHosts <device_ip> -Concatenate -Force`

    Où <*device_ip*> est l’adresse IP de votre appareil ; par exemple : 

     `Set-Item wsman:\localhost\Client\TrustedHosts 10.126.173.90 -Concatenate -Force`

3. Créer une nouvelle information d’identification en tapant : 

     `$cred = New-Object pscredential @("<IP of target device>\SSAdmin", (ConvertTo-SecureString -Force -AsPlainText "<Device Administrator Password>"))`

    Où <*IP d’équipement cible*> est l’adresse IP de données 0 pour votre appareil ; par exemple, **10.126.173.90** comme le montre l’image précédente du fichier hosts. Vous devez également fournir le mot de passe administrateur pour votre appareil.

4. Créer une session en tapant :

     `$session = New-PSSession -UseSSL -ComputerName <Serial number of target device> -Credential $cred -ConfigurationName "SSAdminConsole"`

    Pour le paramètre - ComputerName dans l’applet de commande, fournissent le <*numéro de série d’équipement cible*>. Ce numéro de série a été mappé à l’adresse IP de données 0 dans le fichier hosts sur votre hôte distant ; par exemple, **SHX0991003G44MT** comme le montre l’image suivante.

5. Type : 

     `Enter-PSSession $session`

6. Vous devrez peut-être patienter quelques minutes, et puis vous serez connecté à votre appareil via HTTPS via le protocole SSL. Vous verrez un message indiquant que vous êtes connecté à votre appareil.

    ![Accès distant PowerShell à l’aide de HTTPS et SSL](./media/storsimple-remote-connect/HCS_PSRemotingUsingHTTPSAndSSL.png)

## <a name="next-steps"></a>Étapes suivantes

- En savoir plus sur [l’utilisation de Windows PowerShell pour administrer votre périphérique StorSimple](storsimple-windows-powershell-administration.md).

- En savoir plus sur [l’utilisation du service Manager StorSimple pour administrer votre périphérique StorSimple](storsimple-manager-service-administration.md).

<properties 
   pageTitle="Créer des certificats auto-signé pour réseau virtuel Point-à-Site croisée locaux connexions à l’aide de makecert | Microsoft Azure"
   description="Cet article contient des étapes pour utiliser makecert pour créer des certificats auto-signé sur Windows 10."
   services="vpn-gateway"
   documentationCenter="na"
   authors="cherylmc"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"/>
<tags 
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="08/22/2016"
   ms.author="cherylmc" />

# <a name="working-with-self-signed-certificates-for-point-to-site-connections"></a>Utilisation des certificats auto-signé de connexions Point-à-Site

Cet article vous permet de créer un certificat auto-signé à l’aide de **makecert**et puis générer des certificats de client à partir de celui-ci. Les étapes sont écrits pour makecert sur Windows 10. Makecert a été validée pour créer des certificats sont compatibles avec les connexions de P2S. 

Pour les connexions P2S, la méthode préférée pour les certificats consiste à utiliser votre solution de certificat d’entreprise, en prenant soin de délivrer des certificats le client avec le format de valeur nom courant 'name@yourdomain.com', plutôt que la mise en forme « NetBIOS domaine\nom d’utilisateur ».

Si vous n’avez pas une solution d’entreprise, un certificat auto-signé est nécessaire pour permettre aux clients P2S pour vous connecter à un réseau virtuel. Nous ne connaissent que makecert a été déconseillée, mais il est toujours une méthode de création de certificats auto-signé compatibles avec les connexions P2S valide. Nous travaillons sur une autre solution de création de certificats auto-signé, mais pour l’instant, makecert est la méthode préférée.

## <a name="create-a-self-signed-certificate"></a>Créer un certificat auto-signé

Makecert est une manière de créer un certificat auto-signé. Les étapes suivantes vous guident dans le processus de création d’un certificat auto-signé à l’aide de makecert. Ces étapes ne sont pas spécifique au modèle de déploiement. Ils sont valides pour le Gestionnaire de ressources et classique.

### <a name="to-create-a-self-signed-certificate"></a>Pour créer un certificat auto-signé

1. À partir d’un ordinateur exécutant Windows 10, téléchargez et installez le [Kit de développement des logiciels Windows (SDK) pour Windows 10](https://dev.windows.com/en-us/downloads/windows-10-sdk).

2. Après l’installation, vous pouvez trouver l’utilitaire makecert.exe sous ce chemin d’accès : C:\Program Files (x86) \Windows Kits\10\bin\<arc >. 
        
    Exemple :`C:\Program Files (x86)\Windows Kits\10\bin\x64`

3. Ensuite, créer et installer un certificat dans le magasin de certificats personnels sur votre ordinateur. L’exemple suivant crée un fichier *.cer* correspondant que vous téléchargez vers Azure lors de la configuration P2S. Exécutez la commande suivante, en tant qu’administrateur. Remplacez *ARMP2SRootCert* et *ARMP2SRootCert.cer* par le nom que vous souhaitez utiliser pour le certificat.<br><br>Le certificat est situé dans vos certificats - volet en cours.

        makecert -sky exchange -r -n "CN=ARMP2SRootCert" -pe -a sha1 -len 2048 -ss My "ARMP2SRootCert.cer"


###  <a name="rootpublickey"></a>Pour obtenir la clé publique

Dans le cadre de la configuration de passerelle VPN pour les connexions Point-à-Site, la clé publique pour le certificat racine est téléchargée dans Azure.

1. Pour obtenir un fichier .cer à partir du certificat, ouvrez **certmgr.msc**. Cliquez sur le certificat auto-signé racine et cliquez sur **toutes les tâches**, puis cliquez sur **Exporter**. Cette action ouvre l' **Assistant Exportation de certificat**.

2. Dans l’Assistant, cliquez sur **suivant**, sélectionnez **non, ne pas exporter la clé privée**, puis sur **suivant**.

3. Dans la page **Format de fichier d’exportation** , sélectionnez **Base 64 codé X.509 (. Limitée).** Ensuite, cliquez sur **suivant**. 

4. Sur le **fichier à exporter**, **accédez** à l’emplacement auquel vous voulez exporter le certificat. Pour **nom de fichier**, nommez le fichier de certificat. Cliquez sur **suivant**.

5. Cliquez sur **Terminer** pour exporter le certificat.

 
### <a name="export-the-self-signed-certificate-optional"></a>Exportez le certificat auto-signé (facultatif)

Vous souhaiterez peut-être exporter le certificat auto-signé et stocker en toute sécurité. Si doivent être, vous pouvez ultérieurement l’installer sur un autre ordinateur et générer des certificats client plus ou exporter un autre fichier .cer. N’importe quel ordinateur avec un certificat client installé et qui est également configuré avec le client VPN NOMPROPRE paramètres peuvent se connecter à votre réseau virtuel via P2S. Pour cette raison, vous voulez vous assurer que les certificats clients sont générées et installées uniquement lorsque vos besoins et que le certificat auto-signé est stocké en toute sécurité.

Pour exporter le certificat auto-signé comme un fichier .pfx, sélectionnez le certificat racine et utilisez les mêmes étapes conformément à [Exporter un certificat client](#clientkey) pour exporter.

## <a name="create-and-install-client-certificates"></a>Créer et installer des certificats de client

Vous n’installez le certificat auto-signé directement sur l’ordinateur client. Vous avez besoin générer un certificat client à partir du certificat auto-signé. Vous exportez et installez le certificat client à l’ordinateur client. Les étapes suivantes ne sont pas spécifique au modèle de déploiement. Ils sont valides pour le Gestionnaire de ressources et classique.

### <a name="part-1---generate-a-client-certificate-from-a-self-signed-certificate"></a>Partie 1 - générer un certificat client à partir d’un certificat auto-signé

Les étapes suivantes vous guident dans un moyen de générer un certificat client à partir d’un certificat auto-signé. Vous pouvez générer plusieurs certificats clients dans le même certificat. Chaque certificat client pouvant être exporté et installé sur l’ordinateur client. 

1. Sur le même ordinateur que vous avez utilisé pour créer le certificat auto-signé, ouvrez une invite de commandes en tant qu’administrateur.

2. Dans cet exemple, « ARMP2SRootCert » fait référence au certificat auto-signé que vous avez créé. 
    - Remplacez *« ARMP2SRootCert »* le nom de la racine auto-signé vous générez le certificat client à partir de. 
    - Remplacez le nom que vous souhaitez générer un certificat client pour être *ClientCertificateName* . 


    Modifier et exécuter l’exemple pour générer un certificat client. Si vous exécutez l’exemple suivant sans le modifier, le résultat est un certificat client nommé ClientCertificateName dans votre magasin de certificats personnels qui a été généré à partir de certificat racine ARMP2SRootCert.

        makecert.exe -n "CN=ClientCertificateName" -pe -sky exchange -m 96 -ss My -in "ARMP2SRootCert" -is my -a sha1

4. Tous les certificats sont stockés dans votre « certificats - volet en cours » stocker sur votre ordinateur. Vous pouvez générer comme bien plusieurs certificats clients selon vos besoins en fonction de cette procédure.

### <a name="clientkey"></a>Partie 2 : exporter un certificat client

1. Pour exporter un certificat client, ouvrez **certmgr.msc**. Clic droit sur le certificat client que vous voulez exporter, cliquez sur **toutes les tâches**, puis cliquez sur **Exporter**. Cette action ouvre l' **Assistant Exportation de certificat**.

2. Dans l’Assistant, cliquez sur **suivant**, puis sélectionnez **Oui, exporter la clé privée**, puis sur **suivant**.

3. Dans la page **Format de fichier d’exportation** , vous pouvez laisser les valeurs par défaut sélectionnées. Cliquez sur **suivant**. 
 
4. Dans la page de **sécurité** , vous devez protéger la clé privée. Si vous choisissez d’utiliser un mot de passe, veillez à enregistrer ou de mémoriser le mot de passe que vous avez défini pour ce certificat. Cliquez sur **suivant**.

5. Sur le **fichier à exporter**, **accédez** à l’emplacement auquel vous voulez exporter le certificat. Pour **nom de fichier**, nommez le fichier de certificat. Cliquez sur **suivant**.

6. Cliquez sur **Terminer** pour exporter le certificat.  

### <a name="part-3---install-a-client-certificate"></a>3e partie - installer un certificat client

Chaque client que vous voulez vous connecter à votre réseau virtuel en utilisant une connexion Point-à-Site doit avoir un certificat client installé. Ce certificat s’ajoute le package de configuration VPN requis. Les étapes suivantes vous guident dans installer manuellement le certificat client.

1. Localisez et copiez le fichier *.pfx* sur l’ordinateur client. Sur l’ordinateur client, double-cliquez sur le fichier *.pfx* à installer. Laissez l' **Emplacement de stockage** en tant **Qu’utilisateur actuel**, puis cliquez sur **suivant**.

2. Dans le **fichier** à importer de page, n’apportez les modifications. Cliquez sur **suivant**.

3. Dans la page de **protection de clé privée** , entrez le mot de passe pour le certificat si vous utilisé, ou vérifiez que l’entité de sécurité qui installe le certificat est correcte, puis cliquez sur **suivant**.

4. Dans la page **Magasin de certificats** , laissez l’emplacement par défaut, puis cliquez sur **suivant**.

5. Cliquez sur **Terminer**. Dans l' **Avertissement de sécurité** pour l’installation du certificat, cliquez sur **Oui**. Le certificat est importé avec succès.

## <a name="next-steps"></a>Étapes suivantes

Poursuivre la configuration de votre Site-à-Point. 

- Pour les étapes du modèle de déploiement de **Gestionnaire de ressources** , voir [configurer une connexion Point-à-Site à un VNet à l’aide de PowerShell](vpn-gateway-howto-point-to-site-rm-ps.md). 
- Pour les étapes du modèle déploiement **classique** , voir [configurer un Point-à-Site la connexion VPN à un VNet à l’aide du portail classique](vpn-gateway-point-to-site-create.md).
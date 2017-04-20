<properties 
    pageTitle="Configuration de la sécurité de fractionnement publipostage | Microsoft Azure" 
    description="Configurer la x409 certificats pour le chiffrement" 
    metaKeywords="Elastic Database certificates security" 
    services="sql-database" 
    documentationCenter="" 
    manager="jhubbard" 
    authors="torsteng"/>

<tags 
    ms.service="sql-database" 
    ms.workload="sql-database" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="05/27/2016" 
    ms.author="torsteng" />


# <a name="split-merge-security-configuration"></a>Configuration de la sécurité de fractionnement publipostage  

Pour utiliser le service Fusionner/fractionner, vous devez configurer correctement la sécurité. Le service fait partie de la fonctionnalité élastique échelle de base de données SQL Microsoft Azure. Pour plus d’informations, voir [élastique échelle scinder et fusionner Service didacticiel](sql-database-elastic-scale-configure-deploy-split-and-merge.md).

## <a name="configuring-certificates"></a>Configuration de certificats

Configurer les certificats de deux façons. 

1. [Pour configurer le certificat SSL](#To-Configure-the-SSL#Certificate)
2. [Pour configurer les certificats clients](#To-Configure-Client-Certificates) 

## <a name="to-obtain-certificates"></a>Pour obtenir des certificats

Certificats peuvent être obtenus à partir de public autorités de certification (AC) ou à partir du [Service de certificats de Windows](http://msdn.microsoft.com/library/windows/desktop/aa376539.aspx). Ce sont les méthodes préférés pour obtenir des certificats.

Si ces options ne sont pas disponibles, vous pouvez générer des **certificats auto-signé**.
 
## <a name="tools-to-generate-certificates"></a>Outils pour générer des certificats

* [Makecert.exe](http://msdn.microsoft.com/library/bfsktky3.aspx)
* [Pvk2Pfx.exe](http://msdn.microsoft.com/library/windows/hardware/ff550672.aspx)

### <a name="to-run-the-tools"></a>Pour exécuter les outils

* À partir d’une invite de commande pour les développeurs pour Visual Studio, voir [invite de commandes Visual Studio](http://msdn.microsoft.com/library/ms229859.aspx) 

    Si vous avez installé, accédez à :

        %ProgramFiles(x86)%\Windows Kits\x.y\bin\x86 

* Obtenir le WDK de [Windows 8.1 : Téléchargez kits et outils](http://msdn.microsoft.com/windows/hardware/gg454513#drivers)

## <a name="to-configure-the-ssl-certificate"></a>Pour configurer le certificat SSL
Un certificat SSL est requis pour chiffrer les communications et authentifier le serveur. Choisissez le plus applicables des trois scénarios ci-dessous, puis exécuter toutes ses étapes :

### <a name="create-a-new-self-signed-certificate"></a>Créer un nouveau certificat auto-signé

1.    [Créer un certificat auto-signé](#Create-a-Self-Signed-Certificate)
2.    [Créer un fichier PFX pour certificat d’auto-signature SSL](#Create-PFX-file-for-Self-Signed-SSL-Certificate)
3.    [Télécharger le certificat SSL cloud Service](#Upload-SSL-Certificate-to-Cloud-Service)
4.    [Mettre à jour le certificat SSL dans le fichier de Configuration de Service](#Update-SSL-Certificate-in-Service-Configuration-File)
5.    [Importer des autorité de Certification SSL](#Import-SSL-Certification-Authority)

### <a name="to-use-an-existing-certificate-from-the-certificate-store"></a>Pour utiliser un certificat existant du magasin de certificats
1. [Exporter un certificat SSL à partir du magasin de certificats](#Export-SSL-Certificate-From-Certificate-Store)
2. [Télécharger le certificat SSL cloud Service](#Upload-SSL-Certificate-to-Cloud-Service)
3. [Mettre à jour le certificat SSL dans le fichier de Configuration de Service](#Update-SSL-Certificate-in-Service-Configuration-File)

### <a name="to-use-an-existing-certificate-in-a-pfx-file"></a>Utiliser un certificat existant dans un fichier PFX

1. [Télécharger le certificat SSL cloud Service](#Upload-SSL-Certificate-to-Cloud-Service)
2. [Mettre à jour le certificat SSL dans le fichier de Configuration de Service](#Update-SSL-Certificate-in-Service-Configuration-File)

## <a name="to-configure-client-certificates"></a>Pour configurer les certificats clients
Les certificats clients sont nécessaire pour authentifier les demandes du service. Choisissez le plus applicables des trois scénarios ci-dessous, puis exécuter toutes ses étapes :

### <a name="turn-off-client-certificates"></a>Désactiver les certificats clients
1.    [Désactiver l’authentification par certificat Client](#Turn-Off-Client-Certificate-Based-Authentication)

### <a name="issue-new-self-signed-client-certificates"></a>Émettre de nouveaux certificats client auto-signé
1.    [Créer une autorité de Certification auto-signé](#Create-a-Self-Signed-Certification-Authority)
2.    [Télécharger autorité de certification cloud Service](#Upload-CA-Certificate-to-Cloud-Service)
3.    [Autorité de certification mise à jour dans le fichier de Configuration de Service](#Update-CA-Certificate-in-Service-Configuration-File)
4.    [Émettre des certificats Client](#Issue-Client-Certificates)
5.    [Créer des fichiers PFX pour les certificats clients](#Create-PFX-files-for-Client-Certificates)
6.    [Importer un certificat Client](#Import-Client-Certificate)
7.    [Copier les empreintes de certificat Client](#Copy-Client-Certificate-Thumbprints)
8.    [Configurer les Clients autorisés dans le fichier de Configuration de Service](#Configure-Allowed-Clients-in-the-Service-Configuration-File)

### <a name="use-existing-client-certificates"></a>Utiliser des certificats clients existants
1.    [Rechercher la clé publique autorité de certification](#Find-CA-Public Key)
2.    [Télécharger autorité de certification cloud Service](#Upload-CA-certificate-to-cloud-service)
3.    [Autorité de certification mise à jour dans le fichier de Configuration de Service](#Update-CA-Certificate-in-Service-Configuration-File)
4.    [Copier les empreintes de certificat Client](#Copy-Client-Certificate-Thumbprints)
5.    [Configurer les Clients autorisés dans le fichier de Configuration de Service](#Configure-Allowed-Clients-in-the-Service-Configuration File)
6.    [Configurer le contrôle de révocation du certificat Client](#Configure-Client-Certificate-Revocation-Check)

## <a name="allowed-ip-addresses"></a>Adresses IP autorisées

Accès aux points de terminaison de service peut être limité à des plages spécifiques d’adresses IP.

## <a name="to-configure-encryption-for-the-store"></a>Pour configurer le chiffrement pour le magasin

Un certificat est nécessaire pour chiffrer les informations d’identification stockées dans le magasin de métadonnées. Choisissez le plus applicables des trois scénarios ci-dessous, puis exécuter toutes ses étapes :

### <a name="use-a-new-self-signed-certificate"></a>Utiliser un nouveau certificat auto-signé

1.     [Créer un certificat auto-signé](#Create-a-Self-Signed-Certificate)
2.     [Créer un fichier PFX pour le chiffrement certificat d’auto-signature](#Create-PFX-file-for-Self-Signed-Encryption-Certificate)
3.     [Télécharger le certificat de chiffrement cloud Service](#Upload-Encryption-Certificate-to-Cloud-Service)
4.     [Certificat de chiffrement de mise à jour dans le fichier de Configuration de Service](#Update-Encryption-Certificate-in-Service-Configuration-File)

### <a name="use-an-existing-certificate-from-the-certificate-store"></a>Utiliser un certificat existant du magasin de certificats

1.     [Exporter un certificat de chiffrement de magasin de certificats](#Export-Encryption-Certificate-From-Certificate-Store)
2.     [Télécharger le certificat de chiffrement cloud Service](#Upload-Encryption-Certificate-to-Cloud-Service)
3.     [Certificat de chiffrement de mise à jour dans le fichier de Configuration de Service](#Update-Encryption-Certificate-in-Service-Configuration-File)

### <a name="use-an-existing-certificate-in-a-pfx-file"></a>Utiliser un certificat existant dans un fichier PFX

1.     [Télécharger le certificat de chiffrement cloud Service](#Upload-Encryption-Certificate-to-Cloud-Service)
2.     [Certificat de chiffrement de mise à jour dans le fichier de Configuration de Service](#Update-Encryption-Certificate-in-Service-Configuration-File)

## <a name="the-default-configuration"></a>La configuration par défaut

La configuration par défaut refuse tout accès au point de terminaison HTTP. Il s’agit le paramètre recommandé, car les requêtes à ces points de terminaison peuvent effectuer des informations sensibles telles que des informations d’identification de la base de données.
La configuration par défaut permet à tous les accès au point de terminaison HTTPS. Ce paramètre peut être restreint davantage.

### <a name="changing-the-configuration"></a>Modification de la Configuration

Le groupe de règles de contrôle d’accès qui s’appliquent aux et point de terminaison sont configurés dans le **<EndpointAcls>** section dans le **fichier de configuration de service**.

    <EndpointAcls>
      <EndpointAcl role="SplitMergeWeb" endPoint="HttpIn" accessControl="DenyAll" />
      <EndpointAcl role="SplitMergeWeb" endPoint="HttpsIn" accessControl="AllowAll" />
    </EndpointAcls>

Les règles dans un groupe de contrôle d’accès sont configurés dans un <AccessControl name=""> section du fichier de configuration du service. 

Le format est décrit dans la documentation de listes de contrôle d’accès réseau.
Par exemple, pour autoriser uniquement les adresses IP dans la plage 100.100.0.0 à 100.100.255.255 pour accéder au point de terminaison HTTPS, les règles présente comme ceci :

    <AccessControl name="Retricted">
      <Rule action="permit" description="Some" order="1" remoteSubnet="100.100.0.0/16"/>
      <Rule action="deny" description="None" order="2" remoteSubnet="0.0.0.0/0" />
    </AccessControl>
    <EndpointAcls>
    <EndpointAcl role="SplitMergeWeb" endPoint="HttpsIn" accessControl="Restricted" />

## <a name="denial-of-service-prevention"></a>Refus de prévention du service

Il existe deux mécanismes différents pris en charge pour détecter et empêcher les attaques de refus de Service :

*    Limiter le nombre de demandes simultanées par hôte distant (option désactivée par défaut)
*    Limiter les taux d’accès par hôte distant (sur par défaut)

Ils sont basés sur les fonctionnalités de sécurité IP dynamique dans IIS plus approfondie dans. Lorsque la modification de cette configuration prise en compte des facteurs suivants :

* Le comportement des proxys et périphériques de traduction d’adresses réseau sur les informations d’hôte distant
* Chaque requête à toutes les ressources dans le rôle web est considérée comme (par exemple, chargement des scripts, des images, etc.)

## <a name="restricting-number-of-concurrent-accesses"></a>Limiter le nombre d’accès simultanées

Les paramètres configurer ce comportement sont :

    <Setting name="DynamicIpRestrictionDenyByConcurrentRequests" value="false" />
    <Setting name="DynamicIpRestrictionMaxConcurrentRequests" value="20" />

Modifier DynamicIpRestrictionDenyByConcurrentRequests sur true pour activer cette protection.

## <a name="restricting-rate-of-access"></a>Taux de restriction d’accès

Les paramètres configurer ce comportement sont :

    <Setting name="DynamicIpRestrictionDenyByRequestRate" value="true" />
    <Setting name="DynamicIpRestrictionMaxRequests" value="100" />
    <Setting name="DynamicIpRestrictionRequestIntervalInMilliseconds" value="2000" />

## <a name="configuring-the-response-to-a-denied-request"></a>Configuration de la réponse à une demande refusée

Le paramètre suivant configure la réponse à une demande refusée :

    <Setting name="DynamicIpRestrictionDenyAction" value="AbortRequest" />
Consultez la documentation de sécurité IP dynamique dans IIS pour d’autres valeurs prises en charge.

## <a name="operations-for-configuring-service-certificates"></a>Opérations de configuration de certificats de service
Cette rubrique concerne les référence uniquement. Suivez les étapes de configuration sous forme de plan dans :

* Configurer le certificat SSL
* Configurer les certificats clients

## <a name="create-a-self-signed-certificate"></a>Créer un certificat auto-signé
Exécutez :

    makecert ^
      -n "CN=myservice.cloudapp.net" ^
      -e MM/DD/YYYY ^
      -r -cy end -sky exchange -eku "1.3.6.1.5.5.7.3.1" ^
      -a sha1 -len 2048 ^
      -sv MySSL.pvk MySSL.cer

Pour personnaliser :

*    -n avec l’URL du service. Caractères génériques (« CN = * .cloudapp .net ») et autres noms (« CN=myservice1.cloudapp.net, CN=myservice2.cloudapp.net ») sont prises en charge.
*    e - avec la date d’expiration du certificat créer un mot de passe et définissez-le lorsque vous y êtes invité.

## <a name="create-pfx-file-for-self-signed-ssl-certificate"></a>Créer le fichier PFX pour certificat SSL auto-signé

Exécutez :

        pvk2pfx -pvk MySSL.pvk -spc MySSL.cer

Entrez votre mot de passe, puis exporter certificat avec ces options :
* Oui, exporter la clé privée
* Exporter toutes les propriétés étendues

## <a name="export-ssl-certificate-from-certificate-store"></a>Exporter un certificat SSL à partir du magasin de certificats

* Trouver un certificat
* Cliquez sur Actions -> toutes les tâches -> Exporter...
* Exportez le certificat dans un. Fichier PFX avec ces options :
    * Oui, exporter la clé privée
    * Inclure tous les certificats dans le chemin d’accès de certification si possible * exporter toutes les propriétés étendues

## <a name="upload-ssl-certificate-to-cloud-service"></a>Téléchargez le certificat SSL sur service cloud

Téléchargement de certificats avec l’existant ou générée. Fichier PFX avec la paire de clés SSL :

* Entrez le mot de passe protégeant les informations de clé privée

## <a name="update-ssl-certificate-in-service-configuration-file"></a>Mettre à jour le certificat SSL dans le fichier de configuration de service

Mettre à jour la valeur de l’empreinte numérique du paramètre suivant dans le fichier de configuration de service avec l’empreinte numérique du certificat téléchargé dans le service cloud :

    <Certificate name="SSL" thumbprint="" thumbprintAlgorithm="sha1" />

## <a name="import-ssl-certification-authority"></a>Importer des autorité de certification SSL

Procédez comme suit dans tous les comptes/machine qui doivent communiquer avec le service :

* Double-cliquez sur la. Fichier limitée dans l’Explorateur Windows
* Dans la boîte de dialogue certificat, cliquez sur Installer le certificat...
* Importer un certificat dans le magasin Autorités de Certification racine de confiance

## <a name="turn-off-client-certificate-based-authentication"></a>Désactiver l’authentification par certificat client

Pris en charge uniquement client authentification par certificat et sa désactivation permettra pour l’accès public aux points de terminaison du service, à moins que d’autres mécanismes soient en place (par exemple, Microsoft Azure Virtual Network).

Modifier ces paramètres sur false dans le fichier de configuration de service pour désactiver la fonctionnalité :

    <Setting name="SetupWebAppForClientCertificates" value="false" />
    <Setting name="SetupWebserverForClientCertificates" value="false" />

Ensuite, copiez la même empreinte que le certificat SSL dans le paramètre de certificat autorité de certification :

    <Certificate name="CA" thumbprint="" thumbprintAlgorithm="sha1" />

## <a name="create-a-self-signed-certification-authority"></a>Créer une autorité de certification auto-signé
Exécutez les étapes suivantes pour créer un certificat auto-signé pour l’utiliser comme une autorité de Certification :

    makecert ^
    -n "CN=MyCA" ^
    -e MM/DD/YYYY ^
     -r -cy authority -h 1 ^
     -a sha1 -len 2048 ^
      -sr localmachine -ss my ^
      MyCA.cer

Pour la personnaliser

*    -e avec la date d’expiration de certification


## <a name="find-ca-public-key"></a>Rechercher la clé publique autorité de certification

Tous les certificats client doivent avoir été émis par une autorité de Certification approuvée par le service. Recherchez la clé publique à l’autorité de Certification qui reçoivent des certificats qui vont être utilisées pour l’authentification afin de télécharger dans le service cloud le client.

Si le fichier avec la clé publique n’est pas disponible, exporter à partir du magasin de certificats :

* Trouver un certificat
    * Recherchez un certificat client émis par la même autorité de Certification
* Double-cliquez sur le certificat.
* Sélectionnez l’onglet chemin d’accès de Certification dans la boîte de dialogue certificat.
* Double-cliquez sur l’entrée autorité de certification dans le chemin d’accès.
* Prenez des notes des propriétés du certificat.
* Fermez la boîte de dialogue **certificat** .
* Trouver un certificat
    * Recherchez l’autorité de certification indiquée ci-dessus.
* Cliquez sur Actions -> toutes les tâches -> Exporter...
* Exportez le certificat dans un. Limitée avec ces options :
    * **Non, ne pas exporter la clé privée**
    * Inclure tous les certificats dans le chemin d’accès de certification si possible.
    * Exporter toutes les propriétés étendues.

## <a name="upload-ca-certificate-to-cloud-service"></a>Téléchargez autorité de certification sur service cloud

Téléchargement de certificats avec l’existant ou générée. Fichier limitée avec la clé publique autorité de certification.

## <a name="update-ca-certificate-in-service-configuration-file"></a>Autorité de certification mise à jour dans le fichier de configuration de service

Mettre à jour la valeur de l’empreinte numérique du paramètre suivant dans le fichier de configuration de service avec l’empreinte numérique du certificat téléchargé dans le service cloud :

    <Certificate name="CA" thumbprint="" thumbprintAlgorithm="sha1" />

Mettre à jour la valeur du paramètre suivant avec la même empreinte :

    <Setting name="AdditionalTrustedRootCertificationAuthorities" value="" />

## <a name="issue-client-certificates"></a>Émettre des certificats de client

Chaque personne autorisé à accéder au service doit avoir un certificat client délivré pour his/hers exclusif à utiliser et choisissez que HIS/hers propre mot de passe fort pour protéger sa clé privée. 

Les étapes suivantes doivent être exécutées dans le même ordinateur où le certificat auto-signé a été généré et stocké :

    makecert ^
      -n "CN=My ID" ^
      -e MM/DD/YYYY ^
      -cy end -sky exchange -eku "1.3.6.1.5.5.7.3.2" ^
      -a sha1 -len 2048 ^
      -in "MyCA" -ir localmachine -is my ^
      -sv MyID.pvk MyID.cer

Personnalisation :

* -n avec un ID pour le client qui est authentifié avec ce certificat
* -e avec la date d’expiration du certificat
* MyID.pvk et MyID.cer avec des noms uniques pour ce certificat client

Cette commande vous invite à un mot de passe être créé et puis utilisé une seule fois. Utiliser un mot de passe.

## <a name="create-pfx-files-for-client-certificates"></a>Créer des fichiers PFX client certificats

Pour chaque certificat client générée, exécutez :

    pvk2pfx -pvk MyID.pvk -spc MyID.cer

Personnalisation :

    MyID.pvk and MyID.cer with the filename for the client certificate

Entrez votre mot de passe, puis exporter certificat avec ces options :

* Oui, exporter la clé privée
* Exporter toutes les propriétés étendues
* La personne à laquelle est émis ce certificat doit choisir le mot de passe d’exportation

## <a name="import-client-certificate"></a>Importer un certificat client

Chaque personne pour lesquels un certificat client a été émis doit importer la paire de clés sur les ordinateurs qu’il utilise pour communiquer avec le service :

* Double-cliquez sur la. Fichier PFX dans l’Explorateur Windows
* Importer un certificat dans le personnel stocker au moins cette option :
    * Inclure toutes les propriétés étendues activées

## <a name="copy-client-certificate-thumbprints"></a>Copier les empreintes de certificat client
Chaque personne pour lesquels un certificat client a été émis devez suivre ces étapes afin d’obtenir l’empreinte du his/hers certificat qui est ajouté au fichier de configuration du service :
* Exécuter certmgr.exe
* Sélectionnez l’onglet personnel
* Double-cliquez sur le certificat client à utiliser pour l’authentification
* Dans la boîte de dialogue certificat qui s’ouvre, sélectionnez l’onglet Détails
* Vérifiez qu’afficher est affiche tous les
* Sélectionnez le champ nommé empreinte numérique dans la liste
* Copie la valeur de l’empreinte numérique **Supprimer les caractères Unicode non visibles devant le premier chiffre** supprimer tous les espaces

## <a name="configure-allowed-clients-in-the-service-configuration-file"></a>Configurer les clients autorisés dans le fichier de configuration de service

Mettre à jour la valeur du paramètre suivant dans le fichier de configuration de service avec une liste séparée par des virgules des empreintes des certificats de client autorisés à accéder au service :

    <Setting name="AllowedClientCertificateThumbprints" value="" />

## <a name="configure-client-certificate-revocation-check"></a>Configurer le contrôle de révocation du certificat client

Le paramètre par défaut ne vérifie pas avec l’autorité de Certification pour l’état de révocation du certificat client. Pour activer les contrôles, si l’autorité de Certification qui a délivré le client de certificats prend en charge ces contrôles, modifiez le paramètre suivant avec l’une des valeurs définies dans l’énumération X509RevocationMode :

    <Setting name="ClientCertificateRevocationCheck" value="NoCheck" />

## <a name="create-pfx-file-for-self-signed-encryption-certificates"></a>Créer le fichier PFX les certificats de chiffrement auto-signé

Pour un certificat de chiffrement, exécutez :

    pvk2pfx -pvk MyID.pvk -spc MyID.cer

Personnalisation :

    MyID.pvk and MyID.cer with the filename for the encryption certificate

Entrez votre mot de passe, puis exporter certificat avec ces options :
*    Oui, exporter la clé privée
*    Exporter toutes les propriétés étendues
*    Lorsque vous téléchargez le certificat au service cloud, vous devez le mot de passe.

## <a name="export-encryption-certificate-from-certificate-store"></a>Exporter un certificat de chiffrement de magasin de certificats

*    Trouver un certificat
*    Cliquez sur Actions -> toutes les tâches -> Exporter...
*    Exportez le certificat dans un. Fichier PFX avec ces options : 
  *    Oui, exporter la clé privée
  *    Inclure tous les certificats dans le chemin d’accès de certification si possible 
*    Exporter toutes les propriétés étendues

## <a name="upload-encryption-certificate-to-cloud-service"></a>Téléchargez le certificat de chiffrement sur service cloud

Téléchargement de certificats avec l’existant ou générée. Fichier PFX avec la paire de clés de chiffrement :

* Entrez le mot de passe protégeant les informations de clé privée

## <a name="update-encryption-certificate-in-service-configuration-file"></a>Certificat de chiffrement de mise à jour dans le fichier de configuration de service

Mettre à jour la valeur de l’empreinte numérique des paramètres suivants dans le fichier de configuration de service avec l’empreinte numérique du certificat téléchargé dans le service cloud :

    <Certificate name="DataEncryptionPrimary" thumbprint="" thumbprintAlgorithm="sha1" />

## <a name="common-certificate-operations"></a>Opérations de certificat courantes

* Configurer le certificat SSL
* Configurer les certificats clients

## <a name="find-certificate"></a>Trouver un certificat

Procédez comme suit :

1. Exécutez mmc.exe.
2. Fichier -> Ajouter/supprimer un composant logiciel enfichable...
3. Sélectionnez **certificats**.
4. Cliquez sur **Ajouter**.
5. Choisissez l’emplacement de magasin de certificats.
6. Cliquez sur **Terminer**.
7. Cliquez sur **OK**.
8. Développez **certificats**.
9. Développez le nœud de magasin de certificats.
10. Développez le nœud enfant certificat.
11. Sélectionnez un certificat dans la liste.

## <a name="export-certificate"></a>Certificat d’exportation
Dans l' **Assistant Exportation de certificat**:

1. Cliquez sur **suivant**.
2. Sélectionnez **Oui**, puis **exportez la clé privée**.
3. Cliquez sur **suivant**.
4. Sélectionnez le format de fichier de sortie souhaité.
5. Vérifiez les options souhaitées.
6. Vérifier le **mot de passe**.
7. Entrez un mot de passe et confirmez-le.
8. Cliquez sur **suivant**.
9. Tapez ou recherchez un nom de fichier où stocker le certificat (utiliser un. Extension PFX).
10. Cliquez sur **suivant**.
11. Cliquez sur **Terminer**.
12. Cliquez sur **OK**.

## <a name="import-certificate"></a>Importer un certificat

Dans l’Assistant Importation de certificat :

1. Sélectionnez l’emplacement de stockage.

    * Sélectionnez **L’utilisateur actuel** si une seule processus qui s’exécutent sous utilisateur actuel accède au service
    * Sélectionnez **Machine locale** si d’autres processus sur cet ordinateur accède au service
2. Cliquez sur **suivant**.
3. Si vous importez à partir d’un fichier, vérifiez le chemin d’accès du fichier.
4. Si vous importez un. Fichier PFX :
    1.     Entrez le mot de passe protégeant la clé privée
    2.     Sélectionnez les options d’importation
5.     Sélectionnez certificats « Emplacement » dans le magasin suivant
6.     Cliquez sur **Parcourir**.
7.     Sélectionnez le magasin de votre choix.
8.     Cliquez sur **Terminer**.
       
    * Si le magasin d’autorité de Certification racine de confiance a été choisi, cliquez sur **Oui**.
9.     Cliquez sur **OK** dans toutes les fenêtres de boîtes de dialogue.

## <a name="upload-certificate"></a>Télécharger le certificat

Dans le [portail Azure](https://portal.azure.com/)

1. Sélectionnez **Services Cloud**.
2. Sélectionnez le service cloud.
3. Dans le menu supérieur, cliquez sur **certificats**.
4. Dans la barre inférieure, cliquez sur **Télécharger**.
5. Sélectionnez le fichier de certificat.
6. Si c’est un. PFX de fichier, entrez le mot de passe pour la clé privée.
7. Une fois terminé, copiez l’empreinte numérique du certificat de la nouvelle entrée dans la liste.

## <a name="other-security-considerations"></a>Autres considérations relatives à la sécurité
 
Les paramètres SSL décrits dans ce document chiffrer les communications entre le service et ses clients lorsque le point de terminaison HTTPS est utilisée. Ceci est important depuis les informations d’identification pour l’accès de base de données et éventuellement d’autres informations sensibles sont contenues dans la communication. Notez que le service persiste état interne, y compris les informations d’identification, dans ses tables internes dans la base de données Microsoft Azure SQL que vous avez fourni pour le stockage de métadonnées dans votre abonnement Microsoft Azure. Cette base de données a été défini dans le cadre du paramètre suivant dans votre fichier de configuration de service (. Fichier CSCFG) : 

    <Setting name="ElasticScaleMetadata" value="Server=…" />

Informations d’identification stockées dans cette base de données sont chiffrées. Toutefois, pour obtenir les meilleurs résultats, assurez-vous que rôles web et le travail de déploiement de vos services sont mises à jour et sécurisé dès qu’elles ont accès à la base de données de métadonnées et le certificat utilisé pour le chiffrement et déchiffrement des informations d’identification stockées. 

[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

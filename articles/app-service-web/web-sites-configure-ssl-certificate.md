<properties
    pageTitle="Domaine personnalisé de votre application avec HTTPS sécurisé | Microsoft Azure"
    description="Découvrez comment sécuriser le nom de domaine personnalisé pour votre application dans le Service d’application Azure en configurant une liaison de certificat SSL. Vous allez également apprendre à obtenir un certificat SSL à partir de plusieurs outils."
    services="app-service"
    documentationCenter=".net"
    authors="cephalin"
    manager="wpickett"
    editor="jimbe"
    tags="top-support-issue"/>

<tags
    ms.service="app-service"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/08/2016"
    ms.author="cephalin"/>

# <a name="secure-your-apps-custom-domain-with-https"></a>Domaine personnalisé de votre application avec HTTPS sécurisé


> [AZURE.SELECTOR]
- [Acheter le certificat SSL dans Azure](web-sites-purchase-ssl-web-site.md)
- [Utiliser un certificat SSL à partir d’un autre emplacement](web-sites-configure-ssl-certificate.md)


Cet article vous explique comment activer HTTPS pour une application web, une application mobile principale ou une application API dans [Azure Application Service](../app-service/app-service-value-prop-what-is.md) qui utilise un nom de domaine personnalisé. Il traite de l’authentification de serveur uniquement. Si vous avez besoin d’authentification commun (y compris l’authentification du client), voir [Procédure pour configurer TLS commun l’authentification pour le Service d’application](app-service-web-configure-tls-mutual-auth.md).

Pour sécuriser avec HTTPS une application qui comporte un nom de domaine personnalisé, vous ajoutez un certificat pour ce nom de domaine. Par défaut, Azure permet de sécuriser le ** \*. azurewebsites.net** domaine génériques avec un certificat SSL unique, afin que vos clients puissent accéder déjà votre application en * *https://*&lt;appname >*. azurewebsites.net**. Mais si vous souhaitez utiliser un domaine personnalisé, tel que **www.contoso.com contoso.com**, ****, et ** \*. contoso.com**, le certificat par défaut ne peut pas sécuriser qui. En outre, telles que tous les [certificats génériques](https://casecurity.org/2014/02/26/pros-and-cons-of-single-domain-multi-domain-and-wildcard-certificates/), le certificat par défaut n’est pas moins sûr qu’un domaine personnalisé et un certificat pour ce domaine personnalisé.   

>[AZURE.NOTE] Vous pouvez obtenir de l’aide d’experts en Azure à tout moment sur les [forums Azure](https://azure.microsoft.com/support/forums/). Prise en charge plus personnalisé, accédez à [Azure prend en charge](https://azure.microsoft.com/support/options/) et cliquez sur **Prise en charge**.

<a name="bkmk_domainname"></a>
## <a name="what-you-need"></a>Vous avez besoin
Pour sécuriser votre nom de domaine personnalisé avec HTTPS, vous liez un certificat SSL personnalisé à ce domaine personnalisé dans Azure. Avant de lier un certificat personnalisé, vous devez effectuer les opérations suivantes :

- **Configurer le domaine personnalisé** - Service application autorise uniquement l’ajout de certificat d’un nom de domaine est déjà configuré dans votre application. Pour plus d’informations, voir [mapper un nom de domaine personnalisé pour une application Azure](web-sites-custom-domain-name.md). 
- **Évoluer au niveau de base ou une version ultérieure** Offres de Service d’application dans les niveaux de prix inférieurs ne prend en charge les certificats SSL personnalisés. Pour plus d’informations, voir [mettre à l’échelle d’une application dans Azure](web-sites-scale.md). 
- **Obtenez un certificat SSL** - si vous n’en avez pas déjà, vous devez obtenir un auprès d’un approuvée [autorité de certification](http://en.wikipedia.org/wiki/Certificate_authority) (CA). Le certificat remplissent les conditions suivantes :

    - Il est signé par une autorité de certification approuvée (pas de serveurs autorité de certification privés).
    - Il contient une clé privée.
    - Elle est créée pour l’échange de clés et exporté vers un. Fichier PFX.
    - Il utilise un minimum de chiffrement 2048 bits.
    - Son nom de sujet correspond au domaine personnalisé qu'il faut la banque d’informations sécurisé. Pour sécuriser plusieurs domaines avec un certificat, vous devez utiliser un nom générique (par exemple, ** \*. contoso.com**) ou spécifier des valeurs subjectAltName.
    - Il est fusionné avec tous les **[certificats intermédiaires](http://en.wikipedia.org/wiki/Intermediate_certificate_authorities)** utilisés par votre autorité de certification. Dans le cas contraire, vous pouvez rencontrer des problèmes d’interopérabilité irreproducible sur certains clients.

        >[AZURE.NOTE] Pour obtenir un certificat SSL qui répond à la configuration requise, le plus simple consiste à         [acheter une dans le portail Azure directement](web-sites-purchase-ssl-web-site.md). Cet article vous explique comment procéder manuellement et comment le lier à votre domaine personnalisé dans le Service d’application.
        >   
        > **Les certificats de chiffrement de courbe elliptique (ECC)** fonctionnent avec le Service d’application, mais en dehors de l’étendue de cet article. Travailler avec votre autorité de certification sur les étapes exactes pour créer des certificats ECC.

<a name="bkmk_getcert"></a>
## <a name="step-1-get-an-ssl-certificate"></a>Étape 1. Obtenez un certificat SSL

Étant donné que les autorités de certification fournissent les différents types de certificat SSL à différents prix, vous devez commencer par décider du type de certificat SSL à acheter. Pour sécuriser un seul nom de domaine (**www.contoso.com**), il vous suffit un certificat de base. Pour sécuriser plusieurs noms de domaine (**contoso.com** *et* **www.contoso.com** 
*et* **mail.contoso.com**), vous avez besoin d’un [certificat générique](http://en.wikipedia.org/wiki/Wildcard_certificate) ou un certificat avec un [Autre nom de sujet](http://en.wikipedia.org/wiki/SubjectAltName) (`subjectAltName`).

Une fois que vous connaissez le certificat SSL à acheter, vous envoyez une demande de certificat de signature (CSR) à une autorité de certification. Lorsque vous récupérez certificat demandé de l’autorité de certification, vous générez puis un fichier .pfx à partir du certificat. Vous pouvez effectuer ces étapes à l’aide de l’outil de votre choix. Voici des instructions pour les outils courants :

- [Étapes Certreq.exe](#bkmk_certreq) - l’utilitaire de Windows pour la création de demandes de certificats. Il fait partie de Windows depuis Windows XP, Windows Server 2000.
- [Le Gestionnaire des étapes](#bkmk_iismgr) - l’outil de choix si vous êtes déjà familiers de ce dernier.
- [Étapes OpenSSL](#bkmk_openssl) - un [outil open source et disponibilité sur plusieurs plateformes](https://www.openssl.org). Utiliser pour vous aider à obtenir un certificat SSL à partir de n’importe quelle plate-forme.
- [étapes subjectAltName à l’aide de OpenSSL](#bkmk_subjectaltname) - procédure prise `subjectAltName` certificats.

Si vous souhaitez tester le programme d’installation dans le Service d’application avant d’acheter un certificat, vous pouvez générer un [certificat auto-signé](https://en.wikipedia.org/wiki/Self-signed_certificate). Ce didacticiel vous propose deux manières de générer il :

- [Certificat auto-signé, Certreq.exe étapes](#bkmk_sscertreq)
- [Certificat auto-signé, OpenSSL étapes](#bkmk_ssopenssl)

<a name="bkmk_certreq"></a>
### <a name="get-a-certificate-using-certreqexe"></a>Obtenir un certificat en utilisant Certreq.exe

1. Créer un fichier (par exemple, **myrequest.txt**) et copiez le texte suivant et enregistrez-le dans un répertoire de travail. Remplacer le `<your-domain>` espace réservé avec le nom de domaine personnalisé de votre application.

        [NewRequest]
        Subject = "CN=<your-domain>"  ; E.g. "CN=www.contoso.com", or "CN=*.contoso.com" for a wildcard certificate
        Exportable = TRUE
        KeyLength = 2048              ; Required minimum is 2048
        KeySpec = 1
        KeyUsage = 0xA0
        MachineKeySet = True
        ProviderName = "Microsoft RSA SChannel Cryptographic Provider"
        ProviderType = 12
        HashAlgorithm = SHA256

        [EnhancedKeyUsageExtension]
        OID=1.3.6.1.5.5.7.3.1         ; Server Authentication

    Pour plus d’informations sur les options de conseiller et d’autres options disponibles, voir la [documentation de référence Certreq](https://technet.microsoft.com/library/dn296456.aspx).

4. Dans une invite de commandes, `CD` dans votre répertoire de travail et exécutez la commande suivante pour créer le conseiller du service clientèle :

        certreq -new myrequest.txt myrequest.csr

    **myrequest.CSR** est maintenant créé dans votre répertoire de travail en cours.

5. Soumettre **myrequest.csr** à une autorité de certification pour obtenir un certificat SSL. Vous téléchargez le fichier ou copiez son contenu à partir d’un éditeur de texte dans un formulaire web.

    Pour une liste des autorités de certification approuvés par Microsoft, voir [Microsoft programme de certificat racine approuvé : Participants][cas].

6. Une fois que l’autorité de certification a répondu à vous avec un certificat (. Fichier limitée), enregistrez-la dans votre répertoire de travail. Ensuite, exécutez la commande suivante pour terminer le conseiller en attente.

        certreq -accept -user <certificate-name>.cer

    Cette commande stocke le certificat terminé dans le magasin de certificats Windows.

6. Si votre autorité de certification utilise les certificats intermédiaires, installez-les avant de poursuivre. Qu’elles se présentent généralement télécharger séparément de votre autorité de certification et dans plusieurs formats pour les types de serveurs web différent. Sélectionnez la version de Microsoft IIS.

    Une fois que vous avez téléchargé les certificats, droit chacun d’eux dans l’Explorateur Windows, puis sélectionnez  **installer certificat**. Utilisez les valeurs par défaut dans l' **Assistant Importation de certificat**et poursuivre la sélection **suivante** jusqu'à ce que l’importation est terminée.

7. Pour exporter votre certificat SSL à partir du magasin de certificats, appuyez sur `Win` + `R` et exécuter **certmgr.msc** pour lancer le Gestionnaire de certificats. Sélectionnez **personnel** > **certificats**. Dans la colonne **Délivré à** , vous devez voir une entrée avec votre nom de domaine personnalisé et vous avez utilisé pour générer le certificat dans la colonne **Délivré par** l’autorité de certification.

    ![Insérer une image du Gestionnaire de certificat ici][certmgr]

9. Cliquez sur le certificat, puis sélectionnez **Toutes les tâches** > **Exporter**. Dans l' **Assistant Exportation de certificat**, cliquez sur **suivant**, puis sélectionnez **Oui, exporter la clé privée**et puis cliquez à nouveau sur **suivant** .

    ![Exporter la clé privée][certwiz1]

10. Sélectionnez **Échange d’informations personnelles - PKCS #12**, **inclure tous les certificats dans le chemin d’accès de certificat si possible**, **Exporter toutes les propriétés étendues**. Ensuite, cliquez sur **suivant**.

    ![inclure tous les certificats et les propriétés étendues][certwiz2]

11. Sélectionnez le **mot de passe**, puis entrez et confirmez le mot de passe. Cliquez sur **suivant**.

    ![spécifier un mot de passe][certwiz3]

12. Fournir un chemin d’accès et le nom de fichier pour le certificat exporté, avec l' extension **.pfx**. Cliquez sur **suivant** pour terminer.

    ![fournir un chemin d’accès de fichier][certwiz4]

Vous êtes maintenant prêt à télécharger le fichier exporté PFX au Service d’application. Voir [étape 2. Télécharger et lier le certificat SSL personnalisé](#bkmk_configuressl).

<a name="bkmk_iismgr"></a>
### <a name="get-a-certificate-using-the-iis-manager"></a>Obtenir un certificat en utilisant le Gestionnaire IIS

1. Générer un conseiller du service clientèle avec le Gestionnaire des services Internet pour envoyer à l’autorité de certification. Pour plus d’informations sur la génération d’un conseiller du service clientèle, voir [demander un certificat de serveur Internet (IIS 7)][iiscsr].

3. Soumettre votre conseiller du service clientèle pour une autorité de certification pour obtenir un certificat SSL. Pour une liste des autorités de certification approuvés par Microsoft, voir [Microsoft programme de certificat racine approuvé : Participants][cas].


3. Terminer la CSR avec le certificat que l’autorité de certification renvoie à vous. Pour plus d’informations sur l’exécution de la CSR, consultez [installer un certificat de serveur Internet (IIS 7)][installcertiis].

4. Si votre autorité de certification utilise les certificats intermédiaires, installez-les avant de poursuivre. Qu’elles se présentent généralement télécharger séparément de votre autorité de certification et dans plusieurs formats pour les types de serveurs web différent. Sélectionnez la version de Microsoft IIS.

    Une fois que vous avez téléchargé les certificats, droit chacun d’eux dans l’Explorateur Windows, puis sélectionnez **installer certificat**. 
    Utilisez les valeurs par défaut dans l' **Assistant Importation de certificat**et poursuivre la sélection **suivante** jusqu'à ce que l’importation est terminée.

4. Exportez le certificat SSL à partir du Gestionnaire IIS. Pour plus d’informations sur l’exportation le certificat, voir [Exporter un certificat de serveur (IIS 7)][exportcertiis]. 

    >[AZURE.IMPORTANT] Dans l' **Assistant Exportation de certificat**, assurez-vous que vous sélectionnez **Oui, exporter la clé privée**  
    >
    >![Exporter la clé privée][certwiz1]  
    >
    > et également sélectionner **l’Échange d’informations personnelles - PKCS #12**, **inclure tous les certificats dans le chemin d’accès de certificat si possible**et     **Exporter toutes les propriétés étendues**.
    >
    >![inclure tous les certificats et les propriétés étendues][certwiz2]

Vous êtes maintenant prêt à télécharger le fichier exporté PFX au Service d’application. Voir [étape 2. Télécharger et lier le certificat SSL personnalisé](#bkmk_configuressl).

<a name="bkmk_openssl"></a>
### <a name="get-a-certificate-using-openssl"></a>Obtenir un certificat à l’aide de OpenSSL

1. Dans un ligne de commande terminal, `CD` dans un répertoire de travail générer une clé privée et un conseiller du service clientèle en exécutant la commande suivante :

        openssl req -sha256 -new -nodes -keyout myserver.key -out server.csr -newkey rsa:2048

2. Lorsque vous y êtes invité, entrez les informations appropriées. Par exemple :

        Country Name (2 letter code)
        State or Province Name (full name) []: Washington
        Locality Name (eg, city) []: Redmond
        Organization Name (eg, company) []: Microsoft
        Organizational Unit Name (eg, section) []: Azure
        Common Name (eg, YOUR name) []: www.microsoft.com
        Email Address []:

        Please enter the following 'extra' attributes to be sent with your certificate request

        A challenge password []:

    Lorsque vous avez terminé, vous devez disposer des deux fichiers dans votre répertoire de travail : **myserver.key** et **server.csr**. 
    La **server.csr** contient le conseiller du service clientèle, et vous devez **myserver.key** plus tard.

3. Soumettre votre conseiller du service clientèle pour une autorité de certification pour obtenir un certificat SSL. Pour une liste des autorités de certification approuvés par Microsoft, voir [Microsoft programme de certificat racine approuvé : Participants][cas].


4. Une fois que l’autorité de certification vous envoie le certificat demandé, enregistrez-le dans un fichier nommé **myserver.crt** dans votre répertoire de travail. Si votre autorité de certification fournit dans un format de texte, il vous suffit copiez le contenu dans **myserver.crt** dans un éditeur de texte et enregistrez-le. Votre fichier doit ressembler à ce qui suit :

        -----BEGIN CERTIFICATE-----
        MIIDJDCCAgwCCQCpCY4o1LBQuzANBgkqhkiG9w0BAQUFADBUMQswCQYDVQQGEwJV
        UzELMAkGA1UECBMCV0ExEDAOBgNVBAcTB1JlZG1vbmQxEDAOBgNVBAsTB0NvbnRv
        c28xFDASBgNVBAMTC2NvbnRvc28uY29tMB4XDTE0MDExNjE1MzIyM1oXDTE1MDEx
        NjE1MzIyM1owVDELMAkGA1UEBhMCVVMxCzAJBgNVBAgTAldBMRAwDgYDVQQHEwdS
        ZWRtb25kMRAwDgYDVQQLEwdDb250b3NvMRQwEgYDVQQDEwtjb250b3NvLmNvbTCC
        ASIwDQYJKoZIhvcNAQEBBQADggEPADCCAQoCggEBAN96hBX5EDgULtWkCRK7DMM3
        enae1LT9fXqGlbA7ScFvFivGvOLEqEPD//eLGsf15OYHFOQHK1hwgyfXa9sEDPMT
        3AsF3iWyF7FiEoR/qV6LdKjeQicJ2cXjGwf3G5vPoIaYifI5r0lhgOUqBxzaBDZ4
        xMgCh2yv7NavI17BHlWyQo90gS2X5glYGRhzY/fGp10BeUEgIs3Se0kQfBQOFUYb
        ktA6802lod5K0OxlQy4Oc8kfxTDf8AF2SPQ6BL7xxWrNl/Q2DuEEemjuMnLNxmeA
        Ik2+6Z6+WdvJoRxqHhleoL8ftOpWR20ToiZXCPo+fcmLod4ejsG5qjBlztVY4qsC
        AwEAATANBgkqhkiG9w0BAQUFAAOCAQEAVcM9AeeNFv2li69qBZLGDuK0NDHD3zhK
        Y0nDkqucgjE2QKUuvVSPodz8qwHnKoPwnSrTn8CRjW1gFq5qWEO50dGWgyLR8Wy1
        F69DYsEzodG+shv/G+vHJZg9QzutsJTB/Q8OoUCSnQS1PSPZP7RbvDV9b7Gx+gtg
        7kQ55j3A5vOrpI8N9CwdPuimtu6X8Ylw9ejWZsnyy0FMeOPpK3WTkDMxwwGxkU3Y
        lCRTzkv6vnHrlYQxyBLOSafCB1RWinN/slcWSLHADB6R+HeMiVKkFpooT+ghtii1
        A9PdUQIhK9bdaFicXPBYZ6AgNVuGtfwyuS5V6ucm7RE6+qf+QjXNFg==
        -----END CERTIFICATE-----

5. Dans le terminal de ligne de commande, exécutez la commande suivante pour exporter **myserver.pfx** à partir de **myserver.key** et **myserver.crt**:

        openssl pkcs12 -export -out myserver.pfx -inkey myserver.key -in myserver.crt

    Lorsque vous y êtes invité, définissez un mot de passe pour protéger le fichier .pfx.

    > [AZURE.NOTE] Si votre autorité de certification utilise les certificats intermédiaires, vous devez les inclure avec la `-certfile` paramètre. Qu’elles se présentent généralement télécharger séparément de votre autorité de certification et dans plusieurs formats pour les types de serveurs web différent. Sélectionnez la version avec le `.pem` extension.
    >
    > Votre `openssl -export` commande doit ressembler à l’exemple suivant, ce qui crée un fichier .pfx qui inclut les certificats intermédiaires à partir du fichier **intermédiaire cets.pem** :
    >  
    > `openssl pkcs12 -chain -export -out myserver.pfx -inkey myserver.key -in myserver.crt -certfile intermediate-cets.pem`

Vous êtes maintenant prêt à télécharger le fichier exporté PFX au Service d’application. Voir [étape 2. Télécharger et lier le certificat SSL personnalisé](#bkmk_configuressl).

<a name="bkmk_subjectaltname"></a>
### <a name="get-a-subjectaltname-certificate-using-openssl"></a>Obtenir un certificat SubjectAltName en utilisant OpenSSL

1. Créer un fichier nommé **sancert.cnf**, copiez le texte suivant et enregistrez-le dans un répertoire de travail :

        # -------------- BEGIN custom sancert.cnf -----
        HOME = .
        oid_section = new_oids
        [ new_oids ]
        [ req ]
        default_days = 730
        distinguished_name = req_distinguished_name
        encrypt_key = no
        string_mask = nombstr
        req_extensions = v3_req # Extensions to add to certificate request
        [ req_distinguished_name ]
        countryName = Country Name (2 letter code)
        countryName_default =
        stateOrProvinceName = State or Province Name (full name)
        stateOrProvinceName_default =
        localityName = Locality Name (eg, city)
        localityName_default =
        organizationalUnitName  = Organizational Unit Name (eg, section)
        organizationalUnitName_default  =
        commonName              = Your common name (eg, domain name)
        commonName_default      = www.mydomain.com
        commonName_max = 64
        [ v3_req ]
        subjectAltName=DNS:ftp.mydomain.com,DNS:blog.mydomain.com,DNS:*.mydomain.com
        # -------------- END custom sancert.cnf -----

    Dans la ligne qui commence par `subjectAltName`, remplacez la valeur par tous les noms de domaine que vous voulez banque d’informations sécurisé (en plus de  `commonName`). Par exemple :

        subjectAltName=DNS:sales.contoso.com,DNS:support.contoso.com,DNS:fabrikam.com

    Vous n’avez pas besoin de modifier un autre champ, y compris `commonName`. Vous devrez les spécifier dans les étapes suivantes.

1. Dans un ligne de commande terminal, `CD` dans votre répertoire de travail et exécuter la commande suivante :

        openssl req -sha256 -new -nodes -keyout myserver.key -out server.csr -newkey rsa:2048 -config sancert.cnf

2. Lorsque vous y êtes invité, entrez les informations appropriées. Par exemple :

        Country Name (2 letter code) []: US
        State or Province Name (full name) []: Washington
        Locality Name (eg, city) []: Redmond
        Organizational Unit Name (eg, section) []: Azure
        Your common name (eg, domain name) []: www.microsoft.com

    Une fois que vous avez terminé, vous devez disposer des deux fichiers dans votre répertoire de travail : **myserver.key** et **server.csr**. 
    La **server.csr** contient la CSR, et vous devez **myserver.key** plus tard.

3. Soumettre votre conseiller du service clientèle pour une autorité de certification pour obtenir un certificat SSL. Pour une liste des autorités de certification approuvés par Microsoft, voir [Microsoft programme de certificat racine approuvé : Participants][cas].


4. Une fois que l’autorité de certification vous envoie le certificat demandé, enregistrez-le dans un fichier nommé **myserver.crt**. Si votre autorité de certification fournit dans un format de texte, il vous suffit copiez le contenu dans **myserver.crt** dans un éditeur de texte et enregistrez-le. Le fichier doit ressembler à ce qui suit :

        -----BEGIN CERTIFICATE-----
        MIIDJDCCAgwCCQCpCY4o1LBQuzANBgkqhkiG9w0BAQUFADBUMQswCQYDVQQGEwJV
        UzELMAkGA1UECBMCV0ExEDAOBgNVBAcTB1JlZG1vbmQxEDAOBgNVBAsTB0NvbnRv
        c28xFDASBgNVBAMTC2NvbnRvc28uY29tMB4XDTE0MDExNjE1MzIyM1oXDTE1MDEx
        NjE1MzIyM1owVDELMAkGA1UEBhMCVVMxCzAJBgNVBAgTAldBMRAwDgYDVQQHEwdS
        ZWRtb25kMRAwDgYDVQQLEwdDb250b3NvMRQwEgYDVQQDEwtjb250b3NvLmNvbTCC
        ASIwDQYJKoZIhvcNAQEBBQADggEPADCCAQoCggEBAN96hBX5EDgULtWkCRK7DMM3
        enae1LT9fXqGlbA7ScFvFivGvOLEqEPD//eLGsf15OYHFOQHK1hwgyfXa9sEDPMT
        3AsF3iWyF7FiEoR/qV6LdKjeQicJ2cXjGwf3G5vPoIaYifI5r0lhgOUqBxzaBDZ4
        xMgCh2yv7NavI17BHlWyQo90gS2X5glYGRhzY/fGp10BeUEgIs3Se0kQfBQOFUYb
        ktA6802lod5K0OxlQy4Oc8kfxTDf8AF2SPQ6BL7xxWrNl/Q2DuEEemjuMnLNxmeA
        Ik2+6Z6+WdvJoRxqHhleoL8ftOpWR20ToiZXCPo+fcmLod4ejsG5qjBlztVY4qsC
        AwEAATANBgkqhkiG9w0BAQUFAAOCAQEAVcM9AeeNFv2li69qBZLGDuK0NDHD3zhK
        Y0nDkqucgjE2QKUuvVSPodz8qwHnKoPwnSrTn8CRjW1gFq5qWEO50dGWgyLR8Wy1
        F69DYsEzodG+shv/G+vHJZg9QzutsJTB/Q8OoUCSnQS1PSPZP7RbvDV9b7Gx+gtg
        7kQ55j3A5vOrpI8N9CwdPuimtu6X8Ylw9ejWZsnyy0FMeOPpK3WTkDMxwwGxkU3Y
        lCRTzkv6vnHrlYQxyBLOSafCB1RWinN/slcWSLHADB6R+HeMiVKkFpooT+ghtii1
        A9PdUQIhK9bdaFicXPBYZ6AgNVuGtfwyuS5V6ucm7RE6+qf+QjXNFg==
        -----END CERTIFICATE-----

5. Dans le terminal de ligne de commande, exécutez la commande suivante pour exporter **myserver.pfx** à partir de **myserver.key** et **myserver.crt**:

        openssl pkcs12 -export -out myserver.pfx -inkey myserver.key -in myserver.crt

    Lorsque vous y êtes invité, définissez un mot de passe pour protéger le fichier .pfx.

    > [AZURE.NOTE] Si votre autorité de certification utilise les certificats intermédiaires, vous devez les inclure avec la `-certfile` paramètre. Qu’elles se présentent généralement télécharger séparément de votre autorité de certification et dans plusieurs formats pour les types de serveurs web différent. Sélectionnez la version avec le `.pem` extension).
    >
    > Votre `openssl -export` commande doit ressembler à l’exemple suivant, ce qui crée un fichier .pfx qui inclut les certificats intermédiaires à partir du fichier **intermédiaire cets.pem** :
    >  
    > `openssl pkcs12 -chain -export -out myserver.pfx -inkey myserver.key -in myserver.crt -certfile intermediate-cets.pem`

Vous êtes maintenant prêt à télécharger le fichier exporté PFX au Service d’application. Voir [étape 2. Télécharger et lier le certificat SSL personnalisé](#bkmk_configuressl).

<a name="bkmk_sscertreq"></a>
### <a name="generate-a-self-signed-certificate-using-certreqexe"></a>Générer un certificat auto-signé à l’aide de Certreq.exe ###

>[AZURE.IMPORTANT] Les certificats auto-signé sont uniquement à des fins de test. La plupart des navigateurs renvoyer des erreurs lors de la visite d’un site Web qui est sécurisé par un certificat auto-signé. Certains navigateurs peuvent refuser même accédez au site. 

1. Créer un fichier texte (par exemple, **mycert.txt**), copiez le texte suivant et enregistrez le fichier dans un répertoire de travail. Remplacer le `<your-domain>` espace réservé avec le nom de domaine personnalisé de votre application.

        [NewRequest]
        Subject = "CN=<your-domain>"  ; E.g. "CN=www.contoso.com", or "CN=*.contoso.com" for a wildcard certificate
        Exportable = TRUE
        KeyLength = 2048              ; KeyLength can be 2048, 4096, 8192, or 16384 (required minimum is 2048)
        KeySpec = 1
        KeyUsage = 0xA0
        MachineKeySet = True
        ProviderName = "Microsoft RSA SChannel Cryptographic Provider"
        ProviderType = 12
        HashAlgorithm = SHA256
        RequestType = Cert            ; Self-signed certificate
        ValidityPeriod = Years
        ValidityPeriodUnits = 1

        [EnhancedKeyUsageExtension]
        OID=1.3.6.1.5.5.7.3.1         ; Server Authentication

    Le paramètre important est `RequestType = Cert`, qui spécifie un certificat auto-signé. 
    Pour plus d’informations sur les options de conseiller et d’autres options disponibles, voir la [documentation de référence Certreq](https://technet.microsoft.com/library/dn296456.aspx).

4. Dans l’invite de commandes `CD` à votre répertoire de travail et exécuter la commande suivante :

        certreq -new mycert.txt mycert.crt
    
    Votre nouveau certificat auto-signé est maintenant installé dans le magasin de certificats.

7. Pour exporter le certificat du magasin de certificats, appuyez sur `Win` + `R` et exécuter **certmgr.msc** pour lancer le Gestionnaire de certificats. Sélectionnez **personnel** > **certificats**. Dans la colonne **Délivré à** , vous devez voir une entrée avec votre nom de domaine personnalisé et vous avez utilisé pour générer le certificat dans la colonne **Délivré par** l’autorité de certification.

    ![Insérer une image du Gestionnaire de certificat ici][certmgr]

9. Cliquez sur le certificat, puis sélectionnez **Toutes les tâches** > **Exporter**. Dans l' **Assistant Exportation de certificat**, cliquez sur **suivant**, puis sélectionnez **Oui, exporter la clé privée**et puis cliquez à nouveau sur **suivant** .

    ![Exporter la clé privée][certwiz1]

10. Sélectionnez **Échange d’informations personnelles - PKCS #12**, **inclure tous les certificats dans le chemin d’accès de certificat si possible**, **Exporter toutes les propriétés étendues**. Ensuite, cliquez sur **suivant**.

    ![inclure tous les certificats et les propriétés étendues][certwiz2]

11. Sélectionnez le **mot de passe**, puis entrez et confirmez le mot de passe. Cliquez sur **suivant**.

    ![spécifier un mot de passe][certwiz3]

12. Fournir un chemin d’accès et le nom de fichier pour le certificat exporté, avec l' extension **.pfx**. Cliquez sur **suivant** pour terminer.

    ![fournir un chemin d’accès de fichier][certwiz4]

Vous êtes maintenant prêt à télécharger le fichier exporté PFX au Service d’application. Voir [étape 2. Télécharger et lier le certificat SSL personnalisé](#bkmk_configuressl).

<a name="bkmk_ssopenssl"></a>
###<a name="generate-a-self-signed-certificate-using-openssl"></a>Générer un certificat auto-signé à l’aide de OpenSSL ###

>[AZURE.IMPORTANT] Les certificats auto-signé sont uniquement à des fins de test. La plupart des navigateurs renvoyer des erreurs lors de la visite d’un site Web qui est sécurisé par un certificat auto-signé. Certains navigateurs peuvent refuser même accédez au site. 

1. Créer un fichier texte nommé **serverauth.cnf**, puis copiez le contenu suivant et enregistrez-le dans un répertoire de travail :

        [ req ]
        default_bits           = 2048
        default_keyfile        = privkey.pem
        distinguished_name     = req_distinguished_name
        attributes             = req_attributes
        x509_extensions        = v3_ca

        [ req_distinguished_name ]
        countryName         = Country Name (2 letter code)
        countryName_min         = 2
        countryName_max         = 2
        stateOrProvinceName     = State or Province Name (full name)
        localityName            = Locality Name (eg, city)
        0.organizationName      = Organization Name (eg, company)
        organizationalUnitName      = Organizational Unit Name (eg, section)
        commonName          = Common Name (eg, your app's domain name)
        commonName_max          = 64
        emailAddress            = Email Address
        emailAddress_max        = 40

        [ req_attributes ]
        challengePassword       = A challenge password
        challengePassword_min       = 4
        challengePassword_max       = 20

        [ v3_ca ]
         subjectKeyIdentifier=hash
         authorityKeyIdentifier=keyid:always,issuer:always
         basicConstraints = CA:false
         keyUsage=nonRepudiation, digitalSignature, keyEncipherment
         extendedKeyUsage = serverAuth

2. Dans un ligne de commande terminal, `CD` dans votre répertoire de travail et exécuter la commande suivante :

        openssl req -sha256 -x509 -nodes -days 365 -newkey rsa:2048 -keyout myserver.key -out myserver.crt -config serverauth.cnf

    Cette commande crée deux fichiers : **myserver.crt** (le certificat auto-signé) et **myserver.key** (clé privée), en fonction des paramètres dans **serverauth.cnf**.

3. Exportez le certificat vers un fichier .pfx en exécutant la commande suivante :

        openssl pkcs12 -export -out myserver.pfx -inkey myserver.key -in myserver.crt

    Lorsque vous y êtes invité, définissez un mot de passe pour protéger le fichier .pfx.

Vous êtes maintenant prêt à télécharger le fichier exporté PFX au Service d’application. Voir [étape 2. Télécharger et lier le certificat SSL personnalisé](#bkmk_configuressl).

<a name="bkmk_configuressl"></a>
## <a name="step-2-upload-and-bind-the-custom-ssl-certificate"></a>Étape 2. Télécharger et lier le certificat SSL personnalisé

Avant de poursuivre, passez en revue la section [que vous avez besoin](#bkmk_domainname) et vérifiez que :

- vous avez un domaine personnalisé qui correspond à votre application Azure,
- votre application est en cours d’exécution en couche **base** ou supérieur, et
- vous avez un certificat SSL pour le domaine personnalisé à partir d’une autorité de certification.


1. Dans votre navigateur, ouvrez le ** [portail Azure.](https://portal.azure.com/)**
2.  Cliquez sur l’option de **Service d’application** sur le côté gauche de la page.
3.  Cliquez sur le nom de votre application auquel vous souhaitez affecter ce certificat. 
4.  Dans les **paramètres**, cliquez sur **certificats SSL**
5.  Cliquez sur **Télécharger le certificat**
6.  Sélectionnez le fichier .pfx que vous avez exporté à [l’étape 1](#bkmk_getcert) et spécifiez le mot de passe que vous créez avant. Ensuite, cliquez sur **Télécharger** pour télécharger le certificat. Vous devez maintenant voir votre certificat téléchargé dans la carte de **certificat SSL** .
7. Dans les **liaisons ssl** section, cliquez sur **Ajouter des liaisons**
8. Dans la carte **Ajouter SSL liaison** utiliser les menus déroulants pour sélectionner le nom de domaine pour sécuriser avec SSL et le certificat à utiliser. Vous pouvez également choisir d’utiliser **[Indication de nom de serveur (SNI)](http://en.wikipedia.org/wiki/Server_Name_Indication)** ou SSL basée sur IP.

    ![Insérer une image de liaisons SSL](./media/web-sites-configure-ssl-certificate/sslbindings.png)

       •    IP based SSL associates a certificate with a domain name by mapping the dedicated public IP address of the server to the domain name. This requires each domain name (contoso.com, fabricam.com, etc.) associated with your service to have a dedicated IP address. This is the traditional          method of associating SSL certificates with a web server.
       •    SNI based SSL is an extension to SSL and **[Transport Layer Security](http://en.wikipedia.org/wiki/Transport_Layer_Security)** (TLS) that allows multiple domains to share the same IP address, with separate security certificates for each domain. Most modern browsers (including Internet Explorer, Chrome, Firefox and Opera) support SNI, however older browsers may not support SNI. For more information on SNI, see the **[Server Name Indication](http://en.wikipedia.org/wiki/Server_Name_Indication)** article on Wikipedia.
     
9. Cliquez sur **Ajouter la liaison** pour enregistrer les modifications et activer le protocole SSL.

## <a name="step-3-change-your-domain-name-mapping-ip-based-ssl-only"></a>Étape 3. Modifier votre mappage de nom de domaine (IP en tenant compte SSL uniquement)

Si vous utilisez uniquement les liaisons **SNI SSL** , ignorez cette section. Plusieurs liaisons **SNI SSL** peuvent travailler ensemble sur l’adresse IP partagée existante affecté à votre application. Toutefois, si vous créez une liaison **SSL basée sur IP** , Service d’application crée une adresse IP dédiée pour la liaison, car le **SSL basée sur IP** requiert une. Uniquement une adresse IP dédiée adresse peut être créée, ainsi qu’une seule liaison **SSL basée sur IP** peut-être être ajoutée.

En raison de cette adresse IP dédiée, vous devrez configurer votre application davantage si :

- Vous avez [utilisé un enregistrement A pour mapper votre domaine personnalisé](web-sites-custom-domain-name.md#a) pour votre application Azure et que vous venez d’ajouter une liaison **SSL basée sur IP** . Dans ce scénario, vous devez remapper existant un enregistrement pour qu’il pointe vers l’adresse IP dédiée en procédant comme suit :

    1. Une fois que vous avez configuré une adresse IP en fonction de liaison SSL, une adresse IP dédiée est affectée à votre application. Vous pouvez trouver cette adresse IP dans la page de **domaine personnalisé** sous paramètres de votre application, juste au-dessus de la section **noms d’hôte** . Elle est indiquée comme **Adresse IP externe**
    
        ![Adresse IP virtuelle](./media/web-sites-configure-ssl-certificate/virtual-ip-address.png)

    2. [Remapper l’enregistrement A de votre nom de domaine personnalisé pour cette nouvelle adresse IP](web-sites-custom-domain-name.md#a).

- Vous disposez déjà d’une ou plusieurs liaisons **SNI SSL** dans votre application, et que vous venez d’ajouter une liaison **SSL basée sur IP** . Une fois que la liaison est terminée, votre * &lt;appname >*. azurewebsites.net de domaine nom pointe vers la nouvelle adresse IP. Par conséquent, tout existant [mappage CNAME à partir du domaine personnalisé](web-sites-custom-domain-name.md#cname) à * &lt;appname >*. azurewebsites.net, y compris ceux qui la **SNI SSL** sécurisé, reçoit également le trafic sur la nouvelle adresse, qui est créé pour la **SSL basée sur IP** uniquement. Dans ce scénario, vous devez envoyer le trafic **SSL SNI** revenir à l’adresse IP partagé d’origine en procédant comme suit :

    1. Identifiez tous les [mappages de CNAME des domaines personnalisés](web-sites-custom-domain-name.md#cname) dans votre application qui a une liaison **SNI SSL** .

    2. Remapper chaque enregistrement CNAME pour **sni.** &lt;appname >. azurewebsites.net au lieu de &lt;appname >. azurewebsites.net.

## <a name="step-4-test-https-for-your-custom-domain"></a>Étape 4. Test HTTPS pour votre domaine personnalisé

Tout ce qu’il reste à faire est maintenant pour vous assurer que HTTPS fonctionne pour votre domaine personnalisé. Dans différents navigateurs, accédez à `https://<your.custom.domain>` pour voir qu’il gère votre application.

- Si votre application génère vous certificat des erreurs de validation, vous utilisez probablement un certificat auto-signé.

- Si qui n’est pas le cas, vous pouvez avoir laissé des certificats intermédiaires lorsque vous exportez votre certificat .pfx. Revenez à [ce que vous devez](#bkmk_domainname) vérifier que votre CSR répond à la configuration requise par application de Service.

<a name="bkmk_enforce"></a>
## <a name="enforce-https-on-your-app"></a>Appliquer HTTPS dans votre application

Si vous souhaitez autoriser l’accès HTTP à votre application, ignorez cette étape. Application Service effectue *pas* appliquer HTTPS, afin que les visiteurs peuvent toujours accéder à votre application à l’aide de HTTP. Si vous souhaitez appliquer HTTPS pour votre application, vous pouvez définir une règle de réécriture dans le `web.config` fichier de votre application. Chaque application de Service d’application a ce fichier, quel que soit le cadre de la langue de votre application.

> [AZURE.NOTE] Il est la redirection spécifiques à une langue de requêtes. ASP.NET MVC pouvez utiliser le filtre [RequireHttps](http://msdn.microsoft.com/library/system.web.mvc.requirehttpsattribute.aspx) au lieu de la règle de réécriture dans `web.config` (voir [déployer une application ASP.NET MVC 5 sécurisée pour une application web](web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database.md)).

Procédez comme suit :

1. Accédez à la console de débogage Kudu pour votre application. Son adresse est `https://<appname>.scm.azurewebsites.net/DebugConsole`.

2. Dans la console de débogage, CD à `D:\home\site\wwwroot`.

3. Ouvrir `web.config` en cliquant sur le bouton crayon.

    ![](./media/web-sites-configure-ssl-certificate/openwebconfig.png)

    Si vous déployez votre application avec Visual Studio ou Git, application Service génère automatiquement l’appropriée `web.config` pour votre application .NET, PHP, Node.js ou Python dans la racine de l’application. 
    Si `web.config` n’existe pas, exécutez `touch web.config` dans l’invite de commande sur le web pour créer votre document. Ou bien, vous pouvez créer dans votre projet local et redéployez votre code.

4. Si vous aviez créer un `web.config`, copiez le code suivant et enregistrez-le. Si vous avez ouvert un web.config existant, puis il vous suffit de copier toute la `<rule>` ajouter des balises dans votre `web.config`de `configuration/system.webServer/rewrite/rules` élément.

        <?xml version="1.0" encoding="UTF-8"?>
        <configuration>
          <system.webServer>
            <rewrite>
              <rules>
                <!-- BEGIN rule TAG FOR HTTPS REDIRECT -->
                <rule name="Force HTTPS" enabled="true">
                  <match url="(.*)" ignoreCase="false" />
                  <conditions>
                    <add input="{HTTPS}" pattern="off" />
                  </conditions>
                  <action type="Redirect" url="https://{HTTP_HOST}/{R:1}" appendQueryString="true" redirectType="Permanent" />
                </rule>
                <!-- END rule TAG FOR HTTPS REDIRECT -->
              </rules>
            </rewrite>
          </system.webServer>
        </configuration>

    Cette règle renvoie un 301 HTTP (permanente redirection) pour le protocole HTTPS chaque fois que l’utilisateur demande une page à l’aide de HTTP. Il redirige de http://contoso.com vers https://contoso.com.

    >[AZURE.IMPORTANT] S’il existe déjà autres `<rule>` des balises dans votre `web.config`, puis placez le texte copié `<rule>` balise avant l’autre `<rule>` balises.

4. Enregistrez le fichier dans la console de débogage Kudu. Il doit prendre effet immédiatement rediriger toutes les requêtes vers HTTPS.

Pour plus d’informations sur le module de réécriture d’URL IIS, consultez la documentation de [La réécriture d’URL](http://www.iis.net/downloads/microsoft/url-rewrite) .

## <a name="more-resources"></a>Ressources complémentaires ##
- [Centre de gestion de la confidentialité de Microsoft Azure](/support/trust-center/security/)
- [Options de configuration déverrouillées dans des Sites Web Azure](/blog/2014/01/28/more-to-explore-configuration-options-unlocked-in-windows-azure-web-sites/)
- [Activer la journalisation des Diagnostics](web-sites-enable-diagnostic-log.md)
- [Configurer des applications web dans le Service d’application Azure](web-sites-configure.md)
- [Portail de gestion Azure](https://manage.windowsazure.com)

>[AZURE.NOTE] Si vous voulez commencer à utiliser le Service d’application Azure avant de vous inscrire pour un compte Azure, accédez à [Essayer le Service application](http://go.microsoft.com/fwlink/?LinkId=523751), où vous pouvez créer une application starter courte immédiatement dans le Service d’application. Aucune carte de crédit obligatoire ; Aucune engagements.

[customdomain]: web-sites-custom-domain-name.md
[iiscsr]: http://technet.microsoft.com/library/cc732906(WS.10).aspx
[cas]: http://social.technet.microsoft.com/wiki/contents/articles/31634.microsoft-trusted-root-certificate-program-participants-v-2016-april.aspx
[installcertiis]: http://technet.microsoft.com/library/cc771816(WS.10).aspx
[exportcertiis]: http://technet.microsoft.com/library/cc731386(WS.10).aspx
[openssl]: http://www.openssl.org/
[portal]: https://manage.windowsazure.com/
[tls]: http://en.wikipedia.org/wiki/Transport_Layer_Security
[staticip]: ./media/web-sites-configure-ssl-certificate/staticip.png
[website]: ./media/web-sites-configure-ssl-certificate/sslwebsite.png
[scale]: ./media/web-sites-configure-ssl-certificate/sslscale.png
[standard]: ./media/web-sites-configure-ssl-certificate/sslreserved.png
[pricing]: /pricing/details/
[configure]: ./media/web-sites-configure-ssl-certificate/sslconfig.png
[uploadcert]: ./media/web-sites-configure-ssl-certificate/ssluploadcert.png
[uploadcertdlg]: ./media/web-sites-configure-ssl-certificate/ssluploaddlg.png
[sslbindings]: ./media/web-sites-configure-ssl-certificate/sslbindings.png
[sni]: http://en.wikipedia.org/wiki/Server_Name_Indication
[certmgr]: ./media/web-sites-configure-ssl-certificate/waws-certmgr.png
[certwiz1]: ./media/web-sites-configure-ssl-certificate/waws-certwiz1.png
[certwiz2]: ./media/web-sites-configure-ssl-certificate/waws-certwiz2.png
[certwiz3]: ./media/web-sites-configure-ssl-certificate/waws-certwiz3.png
[certwiz4]: ./media/web-sites-configure-ssl-certificate/waws-certwiz4.png



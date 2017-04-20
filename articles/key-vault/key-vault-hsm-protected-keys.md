<properties
    pageTitle="Comment faire pour générer et transférer des clés protégées par HSM pour l’archivage sécurisé de clé Azure | Microsoft Azure"
    description="Utilisez cet article pour vous aider à planifier, générer, puis le transférer votre propre clés protégées par HSM à utiliser avec l’archivage sécurisé de clé Azure."
    services="key-vault"
    documentationCenter=""
    authors="cabailey"
    manager="mbaldwin"
    tags="azure-resource-manager"/>

<tags
    ms.service="key-vault"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/24/2016"
    ms.author="cabailey"/>
#<a name="how-to-generate-and-transfer-hsm-protected-keys-for-azure-key-vault"></a>Comment faire pour générer et transférer protégées par HSM touches de direction pour l’archivage sécurisé de clé Azure

##<a name="introduction"></a>Introduction

Pour garantie, lorsque vous utilisez l’archivage sécurisé Azure clé, vous pouvez importer ou générer des clés dans les modules de sécurité matérielle (HSM) qui ne laissez jamais la limite HSM. Ce scénario est souvent désigné comme *mettre votre propre clé*, ou BYOK. Les modules HSM sont FIPS 140-2 niveau 2 validées. L’archivage sécurisé clé Azure utilise famille de nShield Thales de HSM pour protéger vos clés.

Utilisez les informations dans cette rubrique pour vous aider à planifier, générer, puis le transférer votre propre clés protégées par HSM à utiliser avec l’archivage sécurisé de clé Azure.

Cette fonctionnalité n’est pas disponible pour la Chine Azure. 

>[AZURE.NOTE] Pour plus d’informations sur l’archivage sécurisé de clé Azure, voir [Quel est l’archivage sécurisé de clé Azure ?](key-vault-whatis.md)  
>
>Pour un didacticiel mise en route de mise en route, qui inclut la création d’un archivage sécurisé clé pour les clés protégées par HSM, voir [prise en main l’archivage sécurisé de clé Azure](key-vault-get-started.md).

Plus d’informations sur la génération et transfert d’une clé protégées par HSM via Internet :

- Vous générez la clé à partir d’un poste de travail en mode hors connexion, ce qui permet de réduire la surface d’attaque.

- La clé est chiffrée avec une clé Exchange clé (KEK), ce qui reste chiffré jusqu'à ce qu’il est transféré vers le HSM Azure clé l’archivage sécurisé. Uniquement la version de votre clé cryptée laisse le poste de travail d’origine.

- L’ensemble d’outils définit les propriétés de votre clé de client qui lie votre clé au milieu de la sécurité de l’archivage sécurisé de clé Azure. Donc après que le HSM Azure clé l’archivage sécurisé recevoir et déchiffrer votre clé, uniquement ces HSM s’en servir. Votre clé ne peuvent pas être exportée. Cette liaison est appliquée par le HSM Thales.

- La clé de Exchange clé (KEK) qui est utilisé pour chiffrer votre clé est généré à l’intérieur de la HSM Azure clé l’archivage sécurisé et n’a pas peut être exportée. Les HSM se mettent en œuvre qu’il ne peut y avoir aucune version effacer de la KEK en dehors des HSM. En outre, l’ensemble d’outils inclut attestation de Thales que la KEK pas être exporté et a été généré à l’intérieur d’un module HSM authentique qui a été développé par des Thales.

- L’ensemble d’outils inclut attestation de Thales que le monde de sécurité de l’archivage sécurisé de clé Azure a également été généré sur un HSM genuine développé par Thales. Cette attestation s’avère vous que Microsoft utilise genuine matériel.

- Microsoft utilise distincts KEKs et séparer univers de sécurité dans chaque région géographique. Cette séparation garantit que votre clé peut être utilisée uniquement dans des centres de données dans la région dans lequel vous avez le chiffré. Par exemple, une clé à partir d’un client européenne ne peuvent pas être utilisée dans les centres de données en Amérique du Nord ou en Asie.

##<a name="more-information-about-thales-hsms-and-microsoft-services"></a>Plus d’informations sur les services HSM Thales et Microsoft

Thales e-sécurité est un leader global de chiffrement des données et des solutions de sécurité informatiques aux services financiers, haute technologie, fabrication, pour le gouvernement et secteurs de technologie. Avec une année de 40 confortable protection d’entreprise et des informations pour le gouvernement, Thales solutions sont utilisées par quatre des cinq plus grandes énergétique et aerospace sociétés. Leurs solutions sont également utilisées par 22 pays OTAN et sécuriser plus de 80 pour cent des transactions de paiement dans le monde.

Microsoft a collaboré avec Thales pour améliorer l’état de l’image clipart de HSM. Ces améliorations permettent d’obtenir les avantages classiques de services hébergés sans désaffecté contrôler vos clés. En particulier, ces améliorations permettent à Microsoft de gérer les HSM afin que vous n’avez pas à. Comme un service cloud, l’archivage sécurisé de clé Azure évolution à bref délai à respecter les pointes de l’utilisation de votre organisation. En même temps, votre clé est protégée à l’intérieur HSM de Microsoft : vous gardez le contrôle sur le cycle de vie clé, car vous générez la clé et transférez vers HSM de Microsoft.

##<a name="implementing-bring-your-own-key-byok-for-azure-key-vault"></a>Mise en œuvre mettre votre propre clé (BYOK) pour l’archivage sécurisé de clé Azure

Utiliser les informations et les procédures suivantes si vous génère votre propre clé protégées par HSM et puis transférez-le vers l’archivage sécurisé de clé Azure — les premier votre propre situation clé (BYOK).


##<a name="prerequisites-for-byok"></a>Conditions préalables pour BYOK

Consultez le tableau suivant pour une liste des conditions préalables pour mettre votre propre clé (BYOK) pour l’archivage sécurisé de clé Azure.

|Configuration requise|Plus d’informations|
|---|---|
|Un abonnement à Azure|Pour créer un archivage sécurisé de clé Azure, vous avez besoin d’un abonnement Azure : [vous inscrire à la version d’évaluation gratuite](https://azure.microsoft.com/pricing/free-trial/)|
|Le niveau de service Azure clé l’archivage sécurisé Premium pour prendre en charge des clés protégées par HSM|Pour plus d’informations sur les fonctionnalités et les niveaux de service pour l’archivage sécurisé de clé Azure, voir le site Web [Azure clé l’archivage sécurisé tarifs](https://azure.microsoft.com/pricing/details/key-vault/) .|
|Thales HSM, les cartes à puce et les logiciels de prise en charge|Vous devez avoir accès à un Module de sécurité matériel Thales et opérationnelles notions de Thales HSM. Voir [Module de sécurité Thales matériel](https://www.thales-esecurity.com/msrms/buy) pour la liste des modèles compatibles, ou pour acheter un module HSM si vous n’en avez pas.|
|Matérielle et logicielle suivantes :<ol><li>Un x64 en mode hors connexion poste de travail avec un système d’exploitation Windows minimal de Windows 7 et Thales nShield logiciels au moins version 11.50.<br/><br/>Si ce poste de travail s’exécute Windows 7, vous devez [installer Microsoft .NET Framework 4.5](http://download.microsoft.com/download/b/a/4/ba4a7e71-2906-4b2d-a0e1-80cf16844f5f/dotnetfx45_full_x86_x64.exe).</li><li>Un poste de travail qui est connecté à Internet et comporte un système d’exploitation Windows minimal de Windows 7.</li><li>Un lecteur USB ou autre dispositif de stockage portable qui comporte au moins 16 Mo libérer de l’espace.</li></ol>|Pour des raisons de sécurité, nous vous recommandons de que le premier poste de travail n’est pas connecté à un réseau. Toutefois, cette recommandation n’est pas appliquée par programme.<br/><br/>Notez que dans les instructions qui suivent, ce poste de travail est appelé le poste de travail hors connexion.</p></blockquote><br/>En outre, si votre clé de client correspond à un réseau de production, nous vous recommandons d’utiliser un poste de travail deuxième, distincte pour télécharger l’ensemble d’outils la clé du client. Mais à des fins de test, vous pouvez utiliser le même poste de travail en tant que la première partie.<br/><br/>Notez que dans les instructions qui suivent, cette deuxième poste de travail est appelé le poste de travail connecté à Internet.</p></blockquote><br/>|

##<a name="generate-and-transfer-your-key-to-azure-key-vault-hsm"></a>Générer et transférer votre clé à Azure clé l’archivage sécurisé HSM

Vous allez utiliser les cinq étapes suivantes pour générer et transférer votre clé à un HSM de l’archivage sécurisé Azure clé :

- [Étape 1 : Préparer votre poste de travail connecté à Internet](#step-1-prepare-your-internet-connected-workstation)
- [Étape 2 : Préparer votre poste de travail hors connexion](#step-2-prepare-your-disconnected-workstation)
- [Étape 3 : Générer votre clé](#step-3-generate-your-key)
- [Étape 4 : Préparer votre clé pour le transfert](#step-4-prepare-your-key-for-transfer)
- [Étape 5 : Transférer votre clé à l’archivage sécurisé de clé Azure](#step-5-transfer-your-key-to-azure-key-vault)

## <a name="step-1-prepare-your-internet-connected-workstation"></a>Étape 1 : Préparer votre poste de travail connecté à Internet
Pour cette première étape, procédez comme suit sur votre poste de travail qui est connecté à Internet.


###<a name="step-11-install-azure-powershell"></a>Étape 1.1 : Installer PowerShell Azure

Dans le poste de travail connecté à Internet, téléchargez et installez le module Azure PowerShell qui inclut les applets de commande pour gérer l’archivage sécurisé de clé Azure. Cette fonctionnalité nécessite une version minimale de 0.8.13.

Pour obtenir des instructions d’installation, Découvrez [comment installer et configurer Azure PowerShell](../powershell-install-configure.md).

###<a name="step-12-get-your-azure-subscription-id"></a>Étape 1.2 : Obtenir votre ID d’abonnement Azure

Démarrer une session PowerShell Azure et se connecter à votre compte Azure à l’aide de la commande suivante :

        Add-AzureAccount
Dans la fenêtre du navigateur contextuel, entrez votre nom d’utilisateur compte Azure et le mot de passe. Ensuite, utilisez la commande [Get-AzureSubscription](https://msdn.microsoft.com/library/azure/dn790366.aspx) :

        Get-AzureSubscription
À partir de la sortie, recherchez l’ID de l’abonnement que vous utiliserez pour l’archivage sécurisé de clé Azure. Vous aurez besoin ultérieurement cette ID de l’abonnement.

Ne fermez pas la fenêtre PowerShell Azure.

###<a name="step-13-download-the-byok-toolset-for-azure-key-vault"></a>Étape 1.3 : Télécharger l’ensemble d’outils BYOK pour l’archivage sécurisé de clé Azure

Atteindre le Microsoft Download Center et [Télécharger l’ensemble d’outils Azure clé l’archivage sécurisé BYOK](http://www.microsoft.com/download/details.aspx?id=45345) pour votre région géographique ou d’une instance d’Azure. Utilisez les informations suivantes pour identifier le nom du package pour la télécharger et son hachage package ça 256 correspondante :

---

**Amérique du Nord :**

States.zip KeyVault-BYOK-outils-Royaume-Uni

305F44A78FEB750D1D478F6A0C345B097CD5551003302FA465C73D9497AB4A03

---

**Europe :**

KeyVault-BYOK-outils-Europe.zip

C73BB0628B91471CA7F9ADFCE247561C6016A5103EF1A315D49C3EA23AFC0B9C

---

**Pays d’Asie :**

KeyVault-BYOK-outils-AsiaPacific.zip

BE9A84B6C76661929F9FDAD627005D892B3B8F9F19F351220BB4F9C356694192

---

**Amérique Latine :**

KeyVault-BYOK-outils-LatinAmerica.zip
    
9E8EE11972DECE8F05CD898AF64C070C375B387CED716FDCB788544AE27D3D23

---

**Japon :**

KeyVault-BYOK-outils-Japan.zip

E6B88C111D972A02ABA3325F8969C4E36FD7565C467E9D7107635E3DDA11A8B2

---

**Australie :**

KeyVault-BYOK-outils-Australia.zip

7660D7A675506737857B14F527232BE51DC269746590A4E5AB7D50EDD220675D

---

[**Pour le gouvernement Azure :**](https://azure.microsoft.com/features/gov/)

KeyVault-BYOK-outils-USGovCloud.zip

53801A3043B0F8B4A50E8DC01A935C2BFE61F94EE027445B65C52C1ACC2B5E80

---

**Canada :**

KeyVault-BYOK-outils-Canada.zip

A42D9407B490E97693F8A5FA6B60DC1B06B1D1516EDAE7C9A71AA13E12CF1345

---

**Allemagne :**

KeyVault-BYOK-outils-Germany.zip

4795DA855E027B2CA8A2FF1E7AE6F03F772836C7255AFC68E576410BDD28B48E

---
**Inde :**

KeyVault-BYOK-outils-India.zip

26853511EB767A33CF6CD880E78588E9BBE04E619B17FBC77A6B00A5111E800C

---

Pour valider l’intégrité de votre téléchargé ensemble d’outils BYOK, à partir de votre session PowerShell Azure, utilisez l’applet de commande [Get-FileHash](https://technet.microsoft.com/library/dn520872.aspx) .

    Get-FileHash KeyVault-BYOK-Tools-*.zip

L’ensemble d’outils comprend les éléments suivants :

- Un package de clé Exchange clé (KEK) qui comporte un nom commençant par **BYOK-KEK - pkg-.**
- Un package de sécurité du monde qui comporte un nom commençant par **BYOK-SecurityWorld - pkg-.**
- Un script python nommé v**erifykeypackage.py.**
- Un fichier exécutable de ligne de commande de fichiers nommé **KeyTransferRemote.exe** et DLL associés.
- Un Package redistribuable Visual C++, nommé **vcredist_x64.exe.**

Copier le package vers un lecteur USB ou tout autre stockage portable.

##<a name="step-2-prepare-your-disconnected-workstation"></a>Étape 2 : Préparer votre poste de travail hors connexion

Pour cette étape deuxième, effectuez les procédures suivantes sur le poste de travail n’est pas connecté à un réseau (Internet ou votre réseau interne).


###<a name="step-21-prepare-the-disconnected-workstation-with-thales-hsm"></a>Étape 2.1 : Préparer le poste de travail déconnecté avec Thales HSM

Installer le logiciel de prise en charge nCipher (Thales) sur un ordinateur Windows, puis joignez un HSM Thales sur cet ordinateur.

Assurez-vous que les outils Thales sont dans votre chemin d’accès (**%nfast_home%\bin** et **%nfast_home%\python\bin**). Par exemple, tapez les informations suivantes :

        set PATH=%PATH%;”%nfast_home%\bin”;”%nfast_home%\python\bin”

Pour plus d’informations, voir le guide de l’utilisateur inclus avec le HSM Thales.

###<a name="step-22-install-the-byok-toolset-on-the-disconnected-workstation"></a>Étape 2.2 : Installer l’ensemble d’outils BYOK sur le poste de travail hors connexion

Copiez le package d’ensemble d’outils BYOK le lecteur USB ou tout autre stockage portable et effectuez les opérations suivantes :

1. Extraire les fichiers à partir du package téléchargé dans n’importe quel dossier.
2. À partir de ce dossier, exécutez vcredist_x64.exe.
3. Suivez les instructions pour l’installation les composants d’exécution VisualC pour Visual Studio 2013.

##<a name="step-3-generate-your-key"></a>Étape 3 : Générer votre clé

Pour cette étape tiers, effectuez les procédures suivantes sur le poste de travail hors connexion.

###<a name="step-31-create-a-security-world"></a>Étape 3.1 : Créer un environnement de sécurité

Démarrez une invite de commande et exécutez le programme de nouveau monde Thales.

    new-world.exe --initialize --cipher-suite=DLf1024s160mRijndael --module=1 --acs-quorum=2/3

Ce programme crée un fichier de **Sécurité du monde** à % NFAST_KMDATA%\local\world, qui correspond au dossier C:\ProgramData\nCipher\Key gestion Data\local. Vous pouvez utiliser des valeurs différentes pour le quorum mais dans notre exemple, vous êtes invité à entrer des trois cartes vierges et des codes confidentiels pour chacun d'entre eux. Puis, toutes les deux cartes donnent accès complet au milieu de la sécurité. Ces cartes devient l' **Administrateur carte défini** pour le nouveau monde de sécurité.

Procédez comme suit :

- Sauvegardez le fichier du monde. Sécuriser et protéger le fichier du monde, les cartes d’administrateur et leur code PIN et vérifiez qu’aucune personne n’ayant accès à plusieurs cartes.

###<a name="step-32-validate-the-downloaded-package"></a>Étape 3.2 : Valider le package téléchargé

Cette étape est facultative, mais recommandé afin que vous pouvez valider les données suivantes :

- La clé de Exchange qui est inclus dans l’ensemble d’outils a été générée à partir d’un module HSM Thales authentique.
- Le hachage du monde sécurité qui est inclus dans l’ensemble d’outils a été généré dans un HSM Thales authentique.
- La clé de Exchange est non exportable.

>[AZURE.NOTE]Pour valider le package téléchargé, le doit être connecté, sous tension et peut comporter qu’un univers sécurité dessus (tel que celui que vous venez de créer).

Pour valider le package téléchargé :

1.  Exécuter le script verifykeypackage.py en liant une des opérations suivantes, selon votre région géographique ou instance d’Azure :
    - En Amérique du Nord :

            python verifykeypackage.py -k BYOK-KEK-pkg-NA-1 -w BYOK-SecurityWorld-pkg-NA-1
    - Pour l’Europe :

            python verifykeypackage.py -k BYOK-KEK-pkg-EU-1 -w BYOK-SecurityWorld-pkg-EU-1
    - Pour l’Asie :

            python verifykeypackage.py -k BYOK-KEK-pkg-AP-1 -w BYOK-SecurityWorld-pkg-AP-1
    - Pour l’Amérique latine :

            python verifykeypackage.py -k BYOK-KEK-pkg-LATAM-1 -w BYOK-SecurityWorld-pkg-LATAM-1
    - Pour le Japon :

            python verifykeypackage.py -k BYOK-KEK-pkg-JPN-1 -w BYOK-SecurityWorld-pkg-JPN-1
    - Pour l’Australie :

            python verifykeypackage.py -k BYOK-KEK-pkg-AUS-1 -w BYOK-SecurityWorld-pkg-AUS-1
    - Pour le [Gouvernement Azure](https://azure.microsoft.com/features/gov/), qui utilise l’instance de pour le gouvernement des États-Unis d’Azure :

            python verifykeypackage.py -k BYOK-KEK-pkg-USGOV-1 -w BYOK-SecurityWorld-pkg-USGOV-1
    - Pour le Canada :

            python verifykeypackage.py -k BYOK-KEK-pkg-CANADA-1 -w BYOK-SecurityWorld-pkg-CANADA-1
    - Pour l’Allemagne :

            python verifykeypackage.py -k BYOK-KEK-pkg-GERMANY-1 -w BYOK-SecurityWorld-pkg-GERMANY-1
    - Pour l’Inde :

            python verifykeypackage.py -k BYOK-KEK-pkg-INDIA-1 -w BYOK-SecurityWorld-pkg-INDIA-1
    >[AZURE.TIP]Le logiciel Thales inclut python en %NFAST_HOME%\python\bin

2.  Vérifiez que vous consultez la rubrique suivante, qui indique si la validation réussit : **résultat : réussite**

Ce script valide la chaîne signataire jusqu'à la clé racine Thales. Le hachage de cette clé racine est incorporé dans le script et sa valeur doit être **59178a47 de508c3f 291277ee 184f46c4 f1d9c639**. Vous pouvez également confirmer cette valeur séparément, visitez le [site Web de Thales](http://www.thalesesec.com/).

Vous êtes maintenant prêt à créer une nouvelle clé.

###<a name="step-33-create-a-new-key"></a>Étape 3.3 : Créer une nouvelle clé

Générer une clé à l’aide du programme **generatekey** Thales.

Exécutez la commande suivante pour générer la clé :

    generatekey --generate simple type=RSA size=2048 protect=module ident=contosokey plainname=contosokey nvram=no pubexp=

Lorsque vous exécutez cette commande, utilisez les instructions suivantes :

- Le paramètre *protéger* doit être définie à la valeur **module**, comme indiqué. Cela crée une clé protégées par un module. L’ensemble d’outils BYOK ne reconnaît pas les clés protégées par OCS.

- Remplacez la valeur de *contosokey* pour **l’identification** et **plainname** par n’importe quelle valeur de chaîne. Pour réduire les frais généraux administratifs et réduire les risques d’erreurs, nous vous recommandons d’utiliser la même valeur pour les deux. La valeur **d’identification** doit contenir uniquement des nombres, des tirets et des lettres minuscules.

- Le pubexp est vide (par défaut) dans cet exemple, mais vous pouvez spécifier des valeurs spécifiques. Pour plus d’informations, consultez la documentation de Thales.

Cette commande crée un fichier de clé sous forme de jetons dans votre dossier %NFAST_KMDATA%\local avec un nom commençant par **key_simple_**, suivi de l' **identification** qui a été spécifié dans la commande. Par exemple : **key_simple_contosokey**. Ce fichier contient une clé chiffrée.

Sauvegardez ce fichier sous forme de jetons de clé dans un emplacement sécurisé.

>[AZURE.IMPORTANT] Lorsque vous transférez plus tard votre clé pour l’archivage sécurisé de clé Azure, Microsoft ne peut pas exporter cette clé avec vous afin qu’il devienne extrêmement important que vous sauvegardez votre clé et sécurité du monde en toute sécurité. Contactez Thales pour conseils et meilleures pratiques pour la sauvegarde de votre clé.

Vous êtes maintenant prêt à transférer votre clé à l’archivage sécurisé de clé Azure.

##<a name="step-4-prepare-your-key-for-transfer"></a>Étape 4 : Préparer votre clé pour le transfert

Pour cette étape quatrième, effectuez les procédures suivantes sur le poste de travail hors connexion.

###<a name="step-41-create-a-copy-of-your-key-with-reduced-permissions"></a>Étape 4.1 : Créer une copie de votre clé avec des autorisations restreintes

Pour réduire les autorisations sur votre clé, à partir d’une invite de commandes, exécutez une des opérations suivantes, selon votre région géographique ou instance d’Azure :

- En Amérique du Nord :

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-NA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-NA-1
- Pour l’Europe :

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-EU-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-EU-1
- Pour l’Asie :

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-AP-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-AP-1
- Pour l’Amérique latine :

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-LATAM-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-LATAM-1
- Pour le Japon :

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-JPN-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-JPN-1
- Pour l’Australie :

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-AUS-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-AUS-1
- Pour le [Gouvernement Azure](https://azure.microsoft.com/features/gov/), qui utilise l’instance de pour le gouvernement des États-Unis d’Azure :

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-USGOV-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-USGOV-1
- Pour le Canada :

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-CANADA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-CANADA-1
- Pour l’Allemagne :

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-GERMANY-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-GERMANY-1
- Pour l’Inde :

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-INDIA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-INDIA-1


Lorsque vous exécutez cette commande, remplacez *contosokey* avec la même valeur que vous avez spécifié dans **3.3 étape : créer une nouvelle clé** à partir de l’étape de [générer votre clé](#step-3-generate-your-key) .

Vous êtes invité à plug-in vos cartes sécurité du monde d’administration.

Lorsque la commande est terminée, vous voyez **résultat : réussite** et la copie de votre clé avec des autorisations restreintes se trouvent dans le fichier nommé key_xferacId_<contosokey>.

###<a name="step-42-inspect-the-new-copy-of-the-key"></a>Étape 4.2 : Vérifiez que la nouvelle copie de la clé

Vous pouvez également exécuter le Thales utilitaires pour vérifier les autorisations minimales sur la nouvelle clé :

- aclprint.py :

        "%nfast_home%\bin\preload.exe" -m 1 -A xferacld -K contosokey "%nfast_home%\python\bin\python" "%nfast_home%\python\examples\aclprint.py"
- kmfile-dump.exe :

        "%nfast_home%\bin\kmfile-dump.exe" "%NFAST_KMDATA%\local\key_xferacld_contosokey"
Lorsque vous exécutez ces commandes, remplacer contosokey avec la même valeur que vous avez spécifié dans **3.3 étape : créer une nouvelle clé** à partir de l’étape de [générer votre clé](#step-3-generate-your-key) .

###<a name="step-43-encrypt-your-key-by-using-microsofts-key-exchange-key"></a>Étape 4.3 : Chiffrer votre clé à l’aide clé de Microsoft Exchange

Exécutez une des commandes suivantes, selon votre région géographique ou instance d’Azure :

- En Amérique du Nord :

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-NA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-NA-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
- Pour l’Europe :

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-EU-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-EU-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
- Pour l’Asie :

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-AP-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-AP-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
- Pour l’Amérique latine :

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-LATAM-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-LATAM-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
- Pour le Japon :

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-JPN-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-JPN-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
- Pour l’Australie :

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-AUS-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-AUS-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
- Pour le [Gouvernement Azure](https://azure.microsoft.com/features/gov/), qui utilise l’instance de pour le gouvernement des États-Unis d’Azure :

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-USGOV-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-USGOV-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
- Pour le Canada :

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-CANADA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-CANADA-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
- Pour l’Allemagne :

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-GERMANY-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-GERMANY-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
- Pour l’Inde :

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-INDIA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-INDIA-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey


Lorsque vous exécutez cette commande, utilisez les instructions suivantes :

- Remplacez *contosokey* par l’identificateur que vous avez utilisé pour générer la clé dans **3.3 étape : créer une nouvelle clé** à partir de l’étape de [générer votre clé](#step-3-generate-your-key) .

- Remplacez *SubscriptionID* par l’ID de l’abonnement Azure qui contient votre l’archivage sécurisé clé. Vous avez récupéré cette valeur auparavant, dans **étape 1.2 : obtenir votre ID d’abonnement Azure** à partir de l’étape de [préparer votre poste de travail connecté à Internet](#step-1-prepare-your-internet-connected-workstation) .

- Remplacez *ContosoFirstHSMKey* par une étiquette qui est utilisée pour votre nom de fichier de sortie.

Lorsque cette opération terminée avec succès, elle affiche **résultat : réussite** et il est un nouveau fichier dans le dossier actif qui comporte le nom suivant : TransferPackage -*ContosoFirstHSMkey*.byok

###<a name="step-44-copy-your-key-transfer-package-to-the-internet-connected-workstation"></a>Étape 4.4 : Copier votre package de transfert de clés pour le poste de travail connecté à Internet

Utilisez un lecteur USB ou tout autre stockage portable pour copier le fichier de sortie à partir de l’étape précédente (KeyTransferPackage ContosoFirstHSMkey.byok) sur votre poste de travail connecté à Internet.

##<a name="step-5-transfer-your-key-to-azure-key-vault"></a>Étape 5 : Transférer votre clé à l’archivage sécurisé de clé Azure

Pour cette étape finale, du poste de travail connecté à Internet, utilisez l’applet de commande [Add-AzureKeyVaultKey](https://msdn.microsoft.com/library/azure/dn868048\(v=azure.300\).aspx) pour télécharger le package de transfert de clés que vous avez copiée du poste de travail déconnecté le HSM de l’archivage sécurisé Azure clé :

    Add-AzureKeyVaultKey -VaultName 'ContosoKeyVaultHSM' -Name 'ContosoFirstHSMkey' -KeyFilePath 'c:\TransferPackage-ContosoFirstHSMkey.byok' -Destination 'HSM'

Si le téléchargement réussit, vous verrez affiché les propriétés de la clé que vous venez d’ajouter.


##<a name="next-steps"></a>Étapes suivantes

Vous pouvez désormais utiliser cette touche protégées par HSM dans votre l’archivage sécurisé clé. Pour plus d’informations, consultez la section **Si vous souhaitez utiliser un module de sécurité matériel (HSM)** dans le didacticiel [prise en main de l’archivage sécurisé de clé Azure](key-vault-get-started.md) .

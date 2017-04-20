<properties 
    pageTitle="Ajouter un certificat dans le magasin d’autorité de certification Java | Microsoft Azure" 
    description="Découvrez comment ajouter un certificat d’autorité de certification au magasin de certificats (cacerts) autorité de certification Java pour Twilio service ou le Bus des services Azure." 
    services="" 
    documentationCenter="java" 
    authors="rmcmurray" 
    manager="wpickett" 
    editor=""/>

<tags 
    ms.service="multiple" 
    ms.workload="na" 
    ms.tgt_pltfrm="na" 
    ms.devlang="Java" 
    ms.topic="article" 
    ms.date="08/11/2016" 
    ms.author="robmcm"/>

# <a name="adding-a-certificate-to-the-java-ca-certificates-store"></a>Ajout d’un certificat dans le magasin de certificats d’autorité de certification Java
Les étapes suivantes montrent comment ajouter un certificat d’autorité de certification dans le magasin de certificats (cacerts) autorité de certification Java. L’exemple utilisé concerne l’autorité de certification requise par le service Twilio. Informations fournies ultérieurement dans la rubrique décrivent comment installer l’autorité de certification pour le Bus des services Azure. 

Vous pouvez utiliser keytool pour ajouter l’autorité de certification avant compression votre JDK et son ajout à Azure **approot** dossier de votre projet, ou vous pouvez exécuter une tâche de démarrage Azure qui utilise keytool pour ajouter le certificat. Cela suppose que vous allez ajouter un certificat d’autorité de certification avant le JDK zippé en cours. En outre, un certificat d’autorité de certification spécifique sera utilisé dans l’exemple, mais les étapes permettant d’obtenir un certificat d’autorité de certification différent l’importation dans le magasin cacerts devrait se présenter.

## <a name="to-add-a-certificate-to-the-cacerts-store"></a>Pour ajouter un certificat dans le magasin cacerts

1. À l’invite qui est définie sur **jdk\jre\lib\security** dossier de votre JDK, exécutez la procédure suivante pour afficher les certificats sont installés :

    `keytool -list -keystore cacerts`

    Vous serez invité pour le mot de passe store. Le mot de passe par défaut est à **Modifier**. (Si vous voulez modifier le mot de passe, voir la documentation keytool à <http://docs.oracle.com/javase/7/docs/technotes/tools/windows/keytool.html>.) Cet exemple suppose que le certificat avec MD5 empreinte 67:CB:9 D : C0:13:24:8A:82:9B:B2:17:1E:D1:1B:EC:D4 n’est pas répertorié, et que vous souhaitez importer (ce certificat particulier est requise par le service Twilio API).
2. Obtenir le certificat dans la liste des certificats répertoriés au [GeoTrust racine certificats](http://www.geotrust.com/resources/root-certificates/). Cliquez sur le lien pour le certificat avec numéro de série 35:DE:F4:CF et enregistrez-le dans le dossier **jdk\jre\lib\security** . À des fins de cet exemple, il a été enregistré dans un fichier nommé **Equifax\_banque d’informations\_certificat\_Authority.cer**.
3. Importer le certificat via la commande suivante :

    `keytool -keystore cacerts -importcert -alias equifaxsecureca -file Equifax_Secure_Certificate_Authority.cer`

    Lorsque vous êtes invité à approuver ce certificat, si le certificat a MD5 empreinte 67:CB:9 D : C0:13:24:8A:82:9B:B2:17:1E:D1:1B:EC:D4, répondre en tapant **y**.
4. Exécutez la commande suivante pour vérifier que l’autorité de certification a été importée avec succès :

    `keytool -list -keystore cacerts`

5. Code postal JDK et ajoutez-les à Azure **approot** dossier de votre projet.

Pour plus d’informations sur keytool, voir <http://docs.oracle.com/javase/7/docs/technotes/tools/windows/keytool.html>.

## <a name="azure-root-certificates"></a>Certificats racine Azure

Vos applications qui utilisent des services Azure (par exemple, Bus des services Azure) devront approuver le certificat racine CyberTrust Baltimore. (Au début du 15 avril 2013, Azure a commencé à la migration depuis la racine de Global GTE CyberTrust à la racine de CyberTrust Baltimore. Cette migration a plusieurs mois).

La Baltimore certificat peut déjà être installé sur votre Boutique cacerts, il vous suffit d’exécuter la **keytool-liste** commande tout d’abord pour déterminer s’il existe déjà.

Si vous voulez ajouter la racine de CyberTrust Baltimore, il a 02:00:00:b9 numéro de série et SHA1 empreinte d4:de:20:d0:5e:66:fc:53:fe:1a:50:88:2 c : 78:db:28:52:ca:e4:74. Il peut être téléchargé à partir de <https://cacert.omniroot.com/bc2025.crt>, enregistrées dans un fichier avec une extension **.cer**local et puis importées à l’aide de **keytool** comme indiqué ci-dessus.

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur les certificats racine utilisé par Azure, voir [Migration de certificat racine Azure](http://blogs.msdn.com/b/windowsazure/archive/2013/03/15/windows-azure-root-certificate-migration.aspx).

Pour plus d’informations sur Java, voir le [Centre de développement Java](/develop/java/).

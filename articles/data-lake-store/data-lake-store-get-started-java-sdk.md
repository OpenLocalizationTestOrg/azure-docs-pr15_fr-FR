<properties
   pageTitle="Utiliser des données Lake Store Java SDK pour développer des applications | Microsoft Azure"
   description="Utiliser Azure données Lake Store Java SDK pour développer des applications"
   services="data-lake-store"
   documentationCenter=""
   authors="nitinme"
   manager="jhubbard"
   editor="cgronlun"/>

<tags
   ms.service="data-lake-store"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="10/17/2016"
   ms.author="nitinme"/>

# <a name="get-started-with-azure-data-lake-store-using-java"></a>Prise en main Azure données Lake Store à l’aide de Java

> [AZURE.SELECTOR]
- [Portail](data-lake-store-get-started-portal.md)
- [PowerShell](data-lake-store-get-started-powershell.md)
- [KIT DE DÉVELOPPEMENT .NET](data-lake-store-get-started-net-sdk.md)
- [Java SDK](data-lake-store-get-started-java-sdk.md)
- [API REST](data-lake-store-get-started-rest-api.md)
- [Azure infrastructure du langage commun](data-lake-store-get-started-cli.md)
- [Node.js](data-lake-store-manage-use-nodejs.md)

Découvrez comment utiliser Azure données Lake Store Java SDK pour effectuer des opérations de base telles que la création de dossiers, chargement et téléchargement des fichiers de données, etc.. Pour plus d’informations sur les données Lake, voir [Azure données Lake Store](data-lake-store-overview.md).

Vous pouvez accéder aux documents sur Azure données Lake Store en [documents Azure données Lake Store Java API](https://azure.github.io/azure-data-lake-store-java/javadoc/)l’API SDK Java.

## <a name="prerequisites"></a>Conditions préalables

* Kit de développement Java (JDK 7 ou version ultérieure, à l’aide Java version 1.7 ou version ultérieure)
* Compte Azure données Lake Store. Suivez les instructions sur la [prise en main Azure données Lake Store à l’aide du portail Azure](data-lake-store-get-started-portal.md).
* [Maven](https://maven.apache.org/install.html). Ce didacticiel utilise Maven pour les dépendances de génération et project. Bien qu’il soit possible de générer sans l’aide d’un système de génération comme Maven ou Gradle, ces Assurez-vous de systèmes est beaucoup plus facile de gérer les dépendances.
* (Facultatif) Et IDE comme [IntelliJ idée](https://www.jetbrains.com/idea/download/) ou [Eclipse](https://www.eclipse.org/downloads/) ou similaires.

## <a name="how-do-i-authenticate-using-azure-active-directory"></a>Comment s’authentifier à l’aide d’Azure Active Directory ?

Dans ce didacticiel, nous utilisons un secret Azure AD application client pour récupérer un jeton Azure Active Directory (service à service d’authentification). Ce jeton nous permet de créer un objet de client Data Lake Store pour effectuer les opérations de fichier et le répertoire opérations. Pour obtenir des instructions sur la façon de s’authentifier avec Azure données Lake Store à l’aide de la secret client, nous effectuer les étapes suivantes :

1. Créer une application web Azure AD
2. Récupérer les ID de client, secret client et jeton point de terminaison de l’application web Azure AD.
3. Configurer l’accès de l’application web Azure AD sur le fichier/dossier de données Lake Store vous souhaitez accéder à partir de l’application Java que vous créez.

Pour obtenir des instructions sur la façon d’effectuer ces étapes, voir [créer une application Active Directory](data-lake-store-authenticate-using-active-directory.md#create-an-active-directory-application).

Azure Active Directory propose d’autres options pour récupérer un jeton. Vous pouvez sélectionner à partir d’un nombre de différents mécanismes d’authentification pour l’adapter à votre scénario, par exemple, une application en cours d’exécution dans un navigateur, une application distribué sous la forme d’une application de bureau ou une application serveur en cours d’exécution en local ou sur une machine virtuelle Azure. Vous pouvez également choisir provenant de différents types d’informations d’identification, tels que les mots de passe, certificats, authentification multifacteur de 2, etc.. En outre, Azure Active Directory vous permet de synchroniser vos utilisateurs Active Directory local avec le cloud. Pour plus d’informations, voir [Scénarios d’authentification pour Azure Active Directory](../active-directory/active-directory-authentication-scenarios.md). 

## <a name="create-a-java-application"></a>Créer une application Java

Le code exemple disponible [sur GitHub](https://azure.microsoft.com/documentation/samples/data-lake-store-java-upload-download-get-started/) parcours vous guide dans le processus de création de fichiers dans le magasin, concaténation de fichiers, téléchargement d’un fichier et supprimer des fichiers dans le magasin. Cette section de l’article guident à travers les parties principales du code.

1. Créer un projet Maven à l’aide [mvn archétype](https://maven.apache.org/guides/getting-started/maven-in-five-minutes.html) à partir de la ligne de commande ou un IDE. Pour obtenir des instructions sur la création d’un projet Java à l’aide de IntelliJ, voir [ici](https://www.jetbrains.com/help/idea/2016.1/creating-and-running-your-first-java-application.html). Pour obtenir des instructions sur la création d’un projet à l’aide de Eclipse, voir [ici](http://help.eclipse.org/mars/index.jsp?topic=%2Forg.eclipse.jdt.doc.user%2FgettingStarted%2Fqs-3.htm). 

2. Ajouter les dépendances suivantes à votre fichier de **pom.xml** Maven. Ajouter l’extrait suivant du texte entre la ** \</version >** balise et la ** \</project >** balise :

        <dependencies>
          <dependency>
            <groupId>com.microsoft.azure</groupId>
            <artifactId>azure-data-lake-store-sdk</artifactId>
            <version>2.0.4-SNAPSHOT</version>
          </dependency>
          <dependency>
            <groupId>org.slf4j</groupId>
            <artifactId>slf4j-nop</artifactId>
            <version>1.7.21</version>
          </dependency>
        </dependencies>

    La première dépendance consiste à utiliser le Kit de développement de données Lake Store (`azure-datalake-store`) à partir du référentiel maven. La deuxième dépendance (`slf4j-nop`) consiste à spécifier la structure de la journalisation à utiliser pour cette application. Le Kit de développement de données Lake Store utilise façade [slf4j](http://www.slf4j.org/) journalisation, qui vous permet de choisir un certain nombre d’aux cadres journalisation courantes, telles que log4j, Java journalisation, logback, etc., ou aucun enregistrement. Dans cet exemple, nous désactivons journalisation, nous utilisons donc la liaison **slf4j nop** . Pour utiliser d’autres options de journalisation dans votre application, voir [ici](http://www.slf4j.org/manual.html#projectDep).

### <a name="add-the-application-code"></a>Ajouter le code d’application

Il existe trois parties principales au code.

1. Obtenir le jeton Azure Active Directory

2. Utiliser le jeton pour créer un client données Lake Store.

3. Utiliser le client de données Lake Store pour effectuer des opérations.

#### <a name="step-1-obtain-an-azure-active-directory-token"></a>Étape 1 : Obtenir un jeton Azure Active Directory.

Le Kit de développement de données Lake Store propose des méthodes utiles qui vous permettent d’obtenir les jetons de sécurité nécessaires pour communiquer avec le compte de données Lake Store. Toutefois, le Kit de développement ne force pas que ces méthodes uniquement être utilisé. Vous pouvez utiliser tout autre moyen permettant d’obtenir le jeton également, à l’aide du [Kit de développement Azure Active Directory](https://github.com/AzureAD/azure-activedirectory-library-for-java)ou votre propre code personnalisé.

Pour utiliser le Kit de développement de données Lake Store pour obtenir jeton pour l’application Active Directory Web que vous avez créée, utilisez les méthodes statiques dans `AzureADAuthenticator` cours. Remplacez **Remplissage-ici** par les valeurs réelles pour l’application Azure Active Directory Web.

    private static String clientId = "FILL-IN-HERE";
    private static String authTokenEndpoint = "FILL-IN-HERE";
    private static String clientKey = "FILL-IN-HERE";

    AzureADToken token = AzureADAuthenticator.getTokenUsingClientCreds(authTokenEndpoint, clientId, clientKey);

#### <a name="step-2-create-an-azure-data-lake-store-client-adlstoreclient-object"></a>Étape 2 : Créer un objet de client (ADLStoreClient) Azure données Lake Store

Création d’un objet [ADLStoreClient](https://azure.github.io/azure-data-lake-store-java/javadoc/) , vous devez spécifier le nom du compte données Lake Store et du jeton Azure Active Directory que vous généré à la dernière étape. Notez que le nom du compte données Lake Store doit être un nom de domaine complet. Par exemple, remplacez **Remplissage-ici** s’intitule **mydatalakestore.azuredatalakestore.net**.

    private static String accountFQDN = "FILL-IN-HERE";  // full account FQDN, not just the account name
    ADLStoreClient client = ADLStoreClient.createClient(accountFQDN, token);

### <a name="step-3-use-the-adlstoreclient-to-perform-file-and-directory-operations"></a>Étape 3 : Utiliser la ADLStoreClient pour effectuer les opérations de fichiers et des répertoires

Le code ci-dessous contient extraits d’exemples de certaines opérations courantes. Vous pouvez consulter la complète [documents de données Lake Store Java SDK API](https://azure.github.io/azure-data-lake-store-java/javadoc/) de l’objet **ADLStoreClient** pour afficher d’autres opérations.
 
Notez que les fichiers sont lues et écrites dans à l’aide de flux Java standard. Cela signifie que vous pouvez superposer des flux de données Java sur les flux de données Lake Store pour bénéficier de fonctionnalités Java standard (par exemple, imprimer flux de données de sortie mise en forme, ou l’un des flux de compression ou le chiffrement de fonctionnalités supplémentaires sur le haut, etc..).

    // set file permission
    client.setPermission(filename, "744");

    // append to file
    stream = client.getAppendStream(filename);
    stream.write(getSampleContent());
    stream.close();

    // Read File
    InputStream in = client.getReadStream(filename);
    byte[] b = new byte[64000];
    while (in.read(b) != -1) {
        System.out.write(b);
    }
    in.close();

    // concatenate the two files into one
    List<String> fileList = Arrays.asList("/a/b/c.txt", "/a/b/d.txt");
    client.concatenateFiles("/a/b/f.txt", fileList);

    //rename the file
    client.rename("/a/b/f.txt", "/a/b/g.txt");

    // list directory contents
    List<DirectoryEntry> list = client.enumerateDirectory("/a/b", 2000);
    System.out.println("Directory listing for directory /a/b:");
    for (DirectoryEntry entry : list) {
        printDirectoryInfo(entry);
    }

    // delete directory along with all the subdirectories and files in it
    client.deleteRecursive("/a");

#### <a name="step-4-build-and-run-the-application"></a>Étape 4 : Créer et exécuter l’application

1. Pour exécuter à partir d’un IDE, recherchez et appuyez sur le bouton **exécuter** . Pour exécuter à partir de Maven, utilisez [: l’exécution de l’exécution](http://www.mojohaus.org/exec-maven-plugin/exec-mojo.html).

2. Pour générer un jar autonome que vous pouvez exécuter à partir de ligne de commande créer le bocal avec toutes les dépendances inclus, à l’aide du [plug-in d’assemblage Maven](http://maven.apache.org/plugins/maven-assembly-plugin/usage.html). La pom.xml dans l' [exemple de code source sur github](https://github.com/Azure-Samples/data-lake-store-java-upload-download-get-started/blob/master/pom.xml) contient un exemple de cette procédure.


## <a name="next-steps"></a>Étapes suivantes

- [Sécuriser les données dans les données Lake Store](data-lake-store-secure-data.md)
- [Utiliser des données Azure Lake Analytique avec données Lake Store](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
- [Utiliser Azure HDInsight avec données Lake Store](data-lake-store-hdinsight-hadoop-use-portal.md)

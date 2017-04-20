<properties
    pageTitle="Azure AD Java prise en main | Microsoft Azure"
    description="Découvrez comment créer une application de ligne de commande Java qui se connecte les utilisateurs à accéder à une API."
    services="active-directory"
    documentationCenter="java"
    authors="brandwe"
    manager="mbaldwin"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
  ms.tgt_pltfrm="na"
    ms.devlang="java"
    ms.topic="article"
    ms.date="09/16/2016"
    ms.author="brandwe"/>


# <a name="using-java-command-line-app-to-access-an-api-with-azure-ad"></a>À l’aide de l’application Java ligne de commande pour accéder à une API avec Azure AD

[AZURE.INCLUDE [active-directory-devguide](../../includes/active-directory-devguide.md)]

Azure AD rend plus simple à confier la gestion des identités de votre application web fournissant unique connexion et déconnexion avec uniquement quelques lignes de code.  Dans les applications web Java, vous pouvez le faire à l’aide de l’implémentation de la ADAL4J pilotés par la Communauté Microsoft.

  Ici, nous allons utiliser ADAL4J pour :
- Vous connecter à l’utilisateur à l’application à l’aide d’Azure AD en tant que le fournisseur d’identité.
- Afficher des informations sur l’utilisateur.
- Connectez-vous à l’utilisateur se déconnecter de l’application.

Pour ce faire, vous devez :

1. Enregistrer une application avec Azure Active Directory
2. Configurer votre application pour utiliser la bibliothèque ADAL4J.
3. Utiliser la bibliothèque ADAL4J pour émettre des demandes de connexion et déconnexion à Azure Active Directory.
4. Afficher les données relatives à l’utilisateur.

Pour démarrer, [Téléchargez la structure de l’application](https://github.com/Azure-Samples/active-directory-java-webapp-openidconnect/archive/skeleton.zip) ou [Télécharger l’exemple terminé](https://github.com/Azure-Samples/active-directory-java-webapp-openidconnect\/archive/complete.zip).  Vous devez également un client Azure AD dans lesquelles vous pouvez enregistrer votre application.  Si vous n’en avez pas déjà fait, [Découvrez comment obtenir un](active-directory-howto-tenant.md).

## <a name="1--register-an-application-with-azure-ad"></a>1. enregistrer une Application avec Azure Active Directory
Pour permettre à votre application pour l’authentification des utilisateurs, vous devez tout d’abord enregistrer une nouvelle application dans votre client.

- Se connecter au portail de gestion Azure.
- Dans le volet de navigation gauche, cliquez sur **Active Directory**.
- Sélectionnez le client de l’endroit où vous souhaitez enregistrer l’application.
- Cliquez sur l’onglet **Applications** , et cliquez sur Ajouter dans le bac d’alimentation bas.
- Suivez les invites et créez une nouvelle **Application Web et/ou WebAPI**.
    - Le **nom** de l’application décrira votre application aux utilisateurs finaux
    - L' **Authentification URL** est l’URL de base de votre application.  Valeur par défaut de la structure est `http://localhost:8080/adal4jsample/`.
    - L' **Application ID URI** est un identificateur unique pour votre application.  La convention consiste à utiliser `https://<tenant-domain>/<app-name>`, par exemple :`http://localhost:8080/adal4jsample/`
- Une fois que vous avez terminé l’enregistrement, AAD affecter votre application un identificateur unique client.  Vous devez cette valeur dans les sections suivantes, donc copier à partir de l’onglet Configurer.

Une fois dans le portail de votre application créer une **Application Secret** pour votre application et Recopier vers le bas.  Vous en aurez besoin peu de temps.


## <a name="2-set-up-your-app-to-use-adal4j-library-and-prerequisites-using-maven"></a>2. configurer votre application pour utiliser une bibliothèque ADAL4J et les conditions préalables à l’aide de Maven
Ici, nous allons configurer ADAL4J pour utiliser le protocole d’authentification OpenID se connecter.  ADAL4J seront utilisées pour émettre des demandes de connexion et déconnexion, gérer la session de l’utilisateur et obtenir des informations sur l’utilisateur, entre autres.

-   Dans le répertoire racine de votre projet, ouvrir/créer `pom.xml` et recherchez la `// TODO: provide dependencies for Maven` et remplacer par les éléments suivants :

```Java
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/maven-v4_0_0.xsd">
    <modelVersion>4.0.0</modelVersion>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>public-client-adal4j-sample</artifactId>
    <packaging>jar</packaging>
    <version>0.0.1-SNAPSHOT</version>
    <name>public-client-adal4j-sample</name>
    <url>http://maven.apache.org</url>
    <properties>
        <spring.version>3.0.5.RELEASE</spring.version>
    </properties>

    <dependencies>
        <dependency>
            <groupId>com.microsoft.azure</groupId>
            <artifactId>adal4j</artifactId>
            <version>1.1.2</version>
        </dependency>
        <dependency>
            <groupId>com.nimbusds</groupId>
            <artifactId>oauth2-oidc-sdk</artifactId>
            <version>4.5</version>
        </dependency>
        <dependency>
            <groupId>org.json</groupId>
            <artifactId>json</artifactId>
            <version>20090211</version>
        </dependency>
        <dependency>
            <groupId>javax.servlet</groupId>
            <artifactId>javax.servlet-api</artifactId>
            <version>3.0.1</version>
            <scope>provided</scope>
        </dependency>
        <dependency>
            <groupId>org.slf4j</groupId>
            <artifactId>slf4j-log4j12</artifactId>
            <version>1.7.5</version>
        </dependency>
</dependencies>
    <build>
        <finalName>public-client-adal4j-sample</finalName>
        <plugins>
                <plugin>
            <groupId>org.codehaus.mojo</groupId>
            <artifactId>exec-maven-plugin</artifactId>
            <version>1.2.1</version>
            <configuration>
                <mainClass>PublicClient</mainClass>
            </configuration>
        </plugin>
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-compiler-plugin</artifactId>
                <configuration>
                    <source>1.7</source>
                    <target>1.7</target>
                    <encoding>UTF-8</encoding>
                </configuration>
            </plugin>
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-dependency-plugin</artifactId>
                <executions>
                    <execution>
                        <id>install</id>
                        <phase>install</phase>
                        <goals>
                            <goal>sources</goal>
                        </goals>
                    </execution>
                </executions>
            </plugin>
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-resources-plugin</artifactId>
                <version>2.5</version>
                <configuration>
                    <encoding>UTF-8</encoding>
                </configuration>
            </plugin>
            <plugin>
        <artifactId>maven-assembly-plugin</artifactId>
        <executions>
          <execution>
            <phase>package</phase>
            <goals>
              <goal>single</goal>
            </goals>
          </execution>
        </executions>
        <configuration>
          <descriptorRefs>
            <descriptorRef>jar-with-dependencies</descriptorRef>
          </descriptorRefs>
        </configuration>
      </plugin>
      <plugin>
  <groupId>org.apache.maven.plugins</groupId>
  <artifactId>maven-assembly-plugin</artifactId>
  <configuration>
    <archive>
      <manifest>
        <mainClass>PublicClient</mainClass>
      </manifest>
    </archive>
  </configuration>
</plugin>
        </plugins>
    </build>

</project>


```



## <a name="3-create-the-java-publicclient-file"></a>3. créer le fichier PublicClient java

Comme indiqué ci-dessus, nous allons utiliser l’API de graphique pour obtenir des données sur l’utilisateur connecté. Pour ce faire facile pour nous nous devons créer un fichier pour représenter un **Objet d’annuaire** et un fichier individuel pour représenter l' **utilisateur** afin que le modèle OO de Java peut être utilisé.

1. Créer un fichier nommé `DirectoryObject.java` que nous utiliserons pour stocker les données de base sur n’importe quel DirectoryObject (vous pouvez n’hésitez pas à utiliser ce ultérieurement pour toutes les autres requêtes de graphique que vous pouvez le faire). Vous pouvez copier/coller cette ci-dessous :

```Java
import java.io.BufferedReader;
import java.io.InputStreamReader;
import java.util.concurrent.ExecutorService;
import java.util.concurrent.Executors;
import java.util.concurrent.Future;

import javax.naming.ServiceUnavailableException;

import com.microsoft.aad.adal4j.AuthenticationContext;
import com.microsoft.aad.adal4j.AuthenticationResult;

public class PublicClient {

    private final static String AUTHORITY = "https://login.microsoftonline.com/common/";
    private final static String CLIENT_ID = "2a4da06c-ff07-410d-af8a-542a512f5092";

    public static void main(String args[]) throws Exception {

        try (BufferedReader br = new BufferedReader(new InputStreamReader(
                System.in))) {
            System.out.print("Enter username: ");
            String username = br.readLine();
            System.out.print("Enter password: ");
            String password = br.readLine();

            AuthenticationResult result = getAccessTokenFromUserCredentials(
                    username, password);
            System.out.println("Access Token - " + result.getAccessToken());
            System.out.println("Refresh Token - " + result.getRefreshToken());
            System.out.println("ID Token - " + result.getIdToken());
        }
    }

    private static AuthenticationResult getAccessTokenFromUserCredentials(
            String username, String password) throws Exception {
        AuthenticationContext context = null;
        AuthenticationResult result = null;
        ExecutorService service = null;
        try {
            service = Executors.newFixedThreadPool(1);
            context = new AuthenticationContext(AUTHORITY, false, service);
            Future<AuthenticationResult> future = context.acquireToken(
                    "https://graph.windows.net", CLIENT_ID, username, password,
                    null);
            result = future.get();
        } finally {
            service.shutdown();
        }

        if (result == null) {
            throw new ServiceUnavailableException(
                    "authentication result was null");
        }
        return result;
    }
}


```


##<a name="compile-and-run-the-sample"></a>Compiler et exécuter l’exemple

Placez-vous arrière dans le répertoire racine et exécutez la commande suivante pour créer l’exemple vous stockez simplement collaborer à l’aide `maven`. Utiliser la `pom.xml` fichier que vous avez écrit pour les dépendances.

`$ mvn package`

Vous disposez à présent une `adal4jsample.war` de fichiers dans votre `/targets` répertoire. Vous pouvez déployer dans votre conteneur Tomcat, visitez l’URL 

`http://localhost:8080/adal4jsample/`


> [AZURE.NOTE] 
Il est très facile à déployer un WAR avec les serveurs Tomcat dernières. Accédez simplement à `http://localhost:8080/manager/` et suivez les instructions de téléchargement de votre '' adal4jsample.war' fichier. Il va autodeploy pour vous avec le point de terminaison correct.

##<a name="next-steps"></a>Étapes suivantes

Félicitations ! Vous avez maintenant une application Java qui comporte la fonctionnalité permettant d’authentification des utilisateurs, en toute sécurité de travail API Web utilisant 2.0 OAuth d’appel et obtenez des informations de base sur l’utilisateur.  Si vous n’avez pas déjà, c’est le temps de remplir votre client avec certains utilisateurs.

Pour référence, l’exemple terminé (sans vos valeurs configuration) [est fourni sous forme d’un fichier zip ici](https://github.com/Azure-Samples/active-directory-java-webapp-openidconnect/archive/complete.zip), ou vous pouvez cloner GitHub :

```git clone --branch complete https://github.com/Azure-Samples/active-directory-java-webapp-openidconnect.git```


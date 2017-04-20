<properties 
    pageTitle="Télécharger une application web Java personnalisée dans Azure" 
    description="Ce didacticiel montre comment télécharger une application web Java personnalisée dans Azure Application Service Web Apps." 
    services="app-service\web" 
    documentationCenter="java" 
    authors="rmcmurray" 
    manager="wpickett" 
    editor=""/>

<tags 
    ms.service="app-service-web" 
    ms.workload="web" 
    ms.tgt_pltfrm="na" 
    ms.devlang="Java" 
    ms.topic="article" 
    ms.date="08/11/2016" 
    ms.author="robmcm"/>

# <a name="upload-a-custom-java-web-app-to-azure"></a>Télécharger une application web Java personnalisée dans Azure

Cette rubrique explique comment télécharger une application web Java personnalisée dans [Azure Application Service] Web Apps. Des informations qui s’applique à n’importe quelle application web ou site Web Java et également quelques exemples pour des applications spécifiques sont incluses.

Notez que Azure fournit un moyen pour la création d’applications web Java à l’aide de la configuration du portail Azure l’interface utilisateur et le Azure Marketplace, comme indiqué à [créer une application web Java dans le Service d’application Azure](web-sites-java-get-started.md). Ce didacticiel est pour les scénarios dans lesquels vous ne souhaitez pas utiliser la configuration du portail Azure interface utilisateur ou le Azure Marketplace.  

## <a name="configuration-guidelines"></a>Instructions de configuration

Les paramètres prévues pour les applications web Java personnalisées sur Azure décrites ci-dessous.

- Le port HTTP utilisé par le processus Java est attribué dynamiquement.  Le processus doit utiliser le port à partir de la variable d’environnement `HTTP_PLATFORM_PORT`.
- Tous les ports d’écoute autre que le récepteur HTTP unique doivent être désactivés.  Dans Tomcat, qui inclut l’arrêt, HTTPS et AJP ports.
- Le conteneur doit être configurée pour le trafic IPv4 uniquement.
- La commande de **démarrage** de l’application doit être définie dans la configuration.
- Applications qui requièrent des répertoires avec écrivent qu’il soit nécessaire d’autorisation à rechercher dans le répertoire de contenu de l’application web Azure, ce qui correspond à **D:\home**.  La variable d’environnement `HOME` fait référence à D:\home.  

Vous pouvez définir des variables d’environnement comme requis dans le fichier web.config.

## <a name="webconfig-httpplatform-configuration"></a>configuration de httpPlatform Web.config

Les informations suivantes décrivent la mise en forme **httpPlatform** dans web.config.
                                 
**arguments** (Par défaut = « »). Arguments au fichier exécutable ou script spécifié dans le paramètre **processPath** .

Exemples (symbolisés par **processPath** inclus) :

    processPath="%HOME%\site\wwwroot\bin\tomcat\bin\catalina.bat"
    arguments="start"
    
    processPath="%JAVA_HOME\bin\java.exe"
    arguments="-Djava.net.preferIPv4Stack=true -Djetty.port=%HTTP\_PLATFORM\_PORT% -Djetty.base=&quot;%HOME%\site\wwwroot\bin\jetty-distribution-9.1.0.v20131115&quot; -jar &quot;%HOME%\site\wwwroot\bin\jetty-distribution-9.1.0.v20131115\start.jar&quot;"


**processPath** - chemin d’accès au fichier exécutable ou script qui lancera un processus à l’écoute de requêtes HTTP.

Exemples :


    processPath="%JAVA_HOME%\bin\java.exe"

    processPath="%HOME%\site\wwwroot\bin\tomcat\bin\startup.bat"

    processPath="%HOME%\site\wwwroot\bin\tomcat\bin\catalina.bat"
                                                                                       
**rapidFailsPerMinute** (Par défaut = 10.) Nombre de fois où le processus spécifié dans **processPath** est autorisé à blocage par minute. Si cette limite est dépassée, **HttpPlatformHandler** s’arrête de lancement du processus pour le reste de la minute.
                                    
**requestTimeout** (Par défaut = « 00 : 02:00 ».) Durée dont **HttpPlatformHandler** attendre une réponse à partir du processus à l’écoute sur `%HTTP_PLATFORM_PORT%`.

**startupRetryCount** (Par défaut = 10.) Nombre de fois **que httpplatformhandler** essaiera de lancer le processus spécifié dans **processPath**. Voir **startupTimeLimit** pour plus d’informations.

**startupTimeLimit** (Par défaut = 10 secondes.) Durée pour laquelle **HttpPlatformHandler** attend pour le fichier exécutable/script démarrer un processus à l’écoute sur le port.  Si ce délai dépassé, **HttpPlatformHandler** s’arrêter le processus et essayez de lancer **startupRetryCount** fois.
                                                                                      
**stdoutLogEnabled** (Par défaut = « true ».) Si vrai, **stdout** et **stderr** pour le processus spécifié dans le paramètre **processPath** seront redirigés vers le fichier spécifié dans **stdoutLogFile** (voir section **stdoutLogFile** ).
                                    
**stdoutLogFile** (Default="d:\home\LogFiles\httpPlatformStdout.log ».) Chemin d’accès absolu dont **stdout** et **stderr** à partir du processus spécifié dans **processPath** seront enregistrés.
                                    
> [AZURE.NOTE] `%HTTP_PLATFORM_PORT%`est un espace réservé spécial qui doit être spécifié dans le cadre **d’arguments** ou dans le cadre de la liste de **environmentVariables** **httpPlatform** . Cela sera remplacé par un port généré en interne par **HttpPlatformHandler** afin que le processus spécifié par **processPath** pouvez écouter sur ce port.

## <a name="deployment"></a>Déploiement

Java web en fonction des applications peuvent être déployées facilement via la plupart des moyens mêmes qui sont utilisés avec les Services d’Information Internet (IIS) en fonction des applications web.  FTP, Git et Kudu sont toutes prises en charge en tant que mécanismes de déploiement, est la capacité SCM intégrée pour les applications web. WebDeploy fonctionne comme un protocole, cependant, comme Java n’est pas développée dans Visual Studio, WebDeploy ne tient pas avec des exemples d’utilisation de déploiement Java web app.

## <a name="application-configuration-examples"></a>Configuration de l’application exemples

Pour les applications suivantes, un fichier web.config et l’application de configuration est fournie sous forme d’exemples pour montrer comment activer votre application Java sur application Service Web Apps.

### <a name="tomcat"></a>Tomcat
Il existe deux variantes Tomcat qui sont fournies avec application de Service Web Apps, il est toujours tout à fait possible télécharger des instances spécifiques du client. Voici un exemple d’une installation de Tomcat avec un autre Java Virtual Machine (JVM).

    <?xml version="1.0" encoding="UTF-8"?>
    <configuration>
      <system.webServer>
        <handlers>
          <add name="httpPlatformHandler" path="*" verb="*" modules="httpPlatformHandler" resourceType="Unspecified" />
        </handlers>
        <httpPlatform processPath="%HOME%\site\wwwroot\bin\tomcat\bin\startup.bat" 
            arguments="">
          <environmentVariables>
            <environmentVariable name="CATALINA_OPTS" value="-Dport.http=%HTTP_PLATFORM_PORT%" />
            <environmentVariable name="CATALINA_HOME" value="%HOME%\site\wwwroot\bin\tomcat" />
            <environmentVariable name="JRE_HOME" value="%HOME%\site\wwwroot\bin\java" /> <!-- optional, if not specified, this will default to %programfiles%\Java -->
            <environmentVariable name="JAVA_OPTS" value="-Djava.net.preferIPv4Stack=true" />
          </environmentVariables>
        </httpPlatform>
      </system.webServer>
    </configuration>

Sur le côté Tomcat, il existe quelques modifications de configuration qui doivent être effectuées. Le fichier server.xml doit être modifié pour définir :

-   Port arrêt = -1
-   Port du connecteur HTTP = ${port.http}
-   Adresse du connecteur HTTP = « 127.0.0.1 »
-   Commentez connecteurs HTTPS et AJP
-   Le paramètre IPv4 peut également être défini dans le fichier catalina.properties dans laquelle vous pouvez ajouter`java.net.preferIPv4Stack=true`
    
Appels Direct3d ne sont pas prises en charge dans l’application de Service Web Apps. Pour désactiver les messages, ajoutez l’option Java suivante doit votre application passez des appels :`-Dsun.java2d.d3d=false`

### <a name="jetty"></a>Jetée

C’est le cas pour Tomcat, clients peuvent télécharger leurs propres instances pour jetée. Dans le cas d’une installation complète de jetée en cours d’exécution, la configuration présente comme suit :

    <?xml version="1.0" encoding="UTF-8"?>
    <configuration>
      <system.webServer>
        <handlers>
          <add name="httppPlatformHandler" path="*" verb="*" modules="httpPlatformHandler" resourceType="Unspecified" />
        </handlers>
        <httpPlatform processPath="%JAVA_HOME%\bin\java.exe" 
             arguments="-Djava.net.preferIPv4Stack=true -Djetty.port=%HTTP_PLATFORM_PORT% -Djetty.base=&quot;%HOME%\site\wwwroot\bin\jetty-distribution-9.1.0.v20131115&quot; -jar &quot;%HOME%\site\wwwroot\bin\jetty-distribution-9.1.0.v20131115\start.jar&quot;"
            startupTimeLimit="20"
          startupRetryCount="10"
          stdoutLogEnabled="true">
        </httpPlatform>
      </system.webServer>
    </configuration>

La configuration jetée doit être modifié dans le start.ini pour définir `java.net.preferIPv4Stack=true`.

### <a name="springboot"></a>Springboot
Afin d’obtenir un Springboot application en cours d’exécution vous devez télécharger votre fichier JAR ou guerre et ajoutez le fichier web.config suivant. Le fichier web.config est placé dans le dossier wwwroot. Dans le fichier web.config ajustez les arguments pour qu’il pointe vers votre fichier JAR, dans l’exemple suivant, que le fichier JAR se trouve dans le dossier wwwroot également.  

    <?xml version="1.0" encoding="UTF-8"?>
    <configuration>
      <system.webServer>
        <handlers>
          <add name="httpPlatformHandler" path="*" verb="*" modules="httpPlatformHandler" resourceType="Unspecified" />
        </handlers>
        <httpPlatform processPath="%JAVA_HOME%\bin\java.exe"
            arguments="-Djava.net.preferIPv4Stack=true -Dserver.port=%HTTP_PLATFORM_PORT% -jar &quot;%HOME%\site\wwwroot\my-web-project.jar&quot;">
        </httpPlatform>
      </system.webServer>
    </configuration>


### <a name="hudson"></a>Hudson

Notre test utilisé la guerre Hudson 3.1.2 et l’instance Tomcat 7.0.50 par défaut, mais sans utiliser l’interface utilisateur de sa configuration.  Hudson étant un logiciel de création d’outil, il est conseillé d’installer sur des instances dédiées où l’indicateur **AlwaysOn** peut être définis sur l’application web.

1. Dans le répertoire de racine de votre application web, c'est-à-dire, **d:\home\site\wwwroot**, créez un répertoire **d’applications Web** (s’il est déjà présent) et placez Hudson.war dans **d:\home\site\wwwroot\webapps**.
2. Téléchargez maven apache 3.0.5 (compatible avec Hudson) et placez-le dans **d:\home\site\wwwroot**.
3. Créez web.config dans **d:\home\site\wwwroot** et collez le contenu suivant qu’il contient :
    
        <?xml version="1.0" encoding="UTF-8"?>
        <configuration>
          <system.webServer>
            <handlers>
              <add name="httppPlatformHandler" path="*" verb="*" 
        modules="httpPlatformHandler" resourceType="Unspecified" />
            </handlers>
            <httpPlatform processPath="%AZURE_TOMCAT7_HOME%\bin\startup.bat"
        startupTimeLimit="20"
        startupRetryCount="10">
        <environmentVariables>
          <environmentVariable name="HUDSON_HOME" 
        value="%HOME%\site\wwwroot\hudson_home" />
          <environmentVariable name="JAVA_OPTS" 
        value="-Djava.net.preferIPv4Stack=true -Duser.home=%HOME%/site/wwwroot/user_home -Dhudson.DNSMultiCast.disabled=true" />
        </environmentVariables>            
            </httpPlatform>
          </system.webServer>
        </configuration>

    À ce stade, l’application web peut être redémarrée pour prendre les modifications.  Se connecter à http://yourwebapp/hudson pour démarrer Hudson.

4. Une fois que Hudson se configure, vous devriez voir l’écran suivant :

    ![Hudson](./media/web-sites-java-custom-upload/hudson1.png)
    
5. Accéder à la page de configuration Hudson : **Gérer Hudson**, puis cliquez sur **Configuration du système**.
6. Configurer le JDK comme indiqué ci-dessous :

    ![Configuration Hudson](./media/web-sites-java-custom-upload/hudson2.png)

7. Configurez Maven comme indiqué ci-dessous :

    ![Configuration Maven](./media/web-sites-java-custom-upload/maven.png)

8. Enregistrer les paramètres. Hudson doit maintenant être configuré et prêt à l’emploi.

Pour plus d’informations sur Hudson, voir [http://hudson-ci.org](http://hudson-ci.org).

### <a name="liferay"></a>Liferay

Liferay est pris en charge dans l’application de Service Web Apps. Dans la mesure où Liferay peut nécessiter une mémoire importante, l’application web doit s’exécuter sur un collaborateur dédié moyen ou grand, ce qui peut fournir suffisamment de mémoire. Liferay prend également plusieurs minutes pour démarrer. Pour cette raison, il est recommandé de configurer l’application web sur **Toujours actif**.  

À l’aide de Liferay 6.1.2 que Communauté édition GA3 fourni avec Tomcat, les fichiers suivants ont été modifiés après avoir téléchargé Liferay :

**Server.Xml**

- Modifier le port arrêt-1.
- Connecteur modifier HTTP      `<Connector port="${port.http}" protocol="HTTP/1.1" connectionTimeout="600000" address="127.0.0.1" URIEncoding="UTF-8" />`
- Commentez le connecteur AJP.

Dans le dossier **liferay\tomcat-7.0.40\webapps\ROOT\WEB-INF\classes** , créez un fichier nommé **portal ext.properties**. Ce fichier doit contenir une seule ligne, comme illustré ici :

    liferay.home=%HOME%/site/wwwroot/liferay

Au niveau du répertoire même que le dossier tomcat 7.0.40, créez un fichier nommé **web.config** avec le contenu suivant :

    <?xml version="1.0" encoding="UTF-8"?>
    <configuration>
      <system.webServer>
        <handlers>
    <add name="httpPlatformHandler" path="*" verb="*"
         modules="httpPlatformHandler" resourceType="Unspecified" />
        </handlers>
        <httpPlatform processPath="%HOME%\site\wwwroot\tomcat-7.0.40\bin\catalina.bat" 
                      arguments="run" 
                      startupTimeLimit="10" 
                      requestTimeout="00:10:00" 
                      stdoutLogEnabled="true">
          <environmentVariables>
      <environmentVariable name="CATALINA_OPTS" value="-Dport.http=%HTTP_PLATFORM_PORT%" />
      <environmentVariable name="CATALINA_HOME" value="%HOME%\site\wwwroot\tomcat-7.0.40" />
            <environmentVariable name="JRE_HOME" value="D:\Program Files\Java\jdk1.7.0_51" /> 
            <environmentVariable name="JAVA_OPTS" value="-Djava.net.preferIPv4Stack=true" />
          </environmentVariables>
        </httpPlatform>
      </system.webServer>
    </configuration>

Sous le bloc **httpPlatform** , le **requestTimeout** est défini sur « 00 : 10:00 ».  Il peut être réduite mais puis vous serez susceptibles d’afficher des erreurs de délai d’attente pendant Liferay est démarrage.  Si cette valeur est modifiée, le **connectionTimeout** dans le fichier server.xml tomcat doit également être modifiées.  

Il est important de noter que la varariable environnement JRE_HOME est spécifié dans le fichier web.config ci-dessus pour pointer vers le JDK 64 bits. La valeur par défaut est 32 bits, mais dans la mesure où Liferay peuvent nécessiter des niveaux élevés de mémoire, il est recommandé d’utiliser le JDK 64 bits.

Après avoir effectué ces modifications, redémarrez votre application web Liferay en cours d’exécution, puis ouvrez http://yourwebapp. Le portail Liferay est disponible depuis la racine de l’application web. 

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur Liferay, voir [http://www.liferay.com](http://www.liferay.com).

Pour plus d’informations sur Java, voir le [Centre de développement Java](/develop/java/).

[AZURE.INCLUDE [app-service-web-whats-changed](../../includes/app-service-web-whats-changed.md)]

[AZURE.INCLUDE [app-service-web-try-app-service](../../includes/app-service-web-try-app-service.md)]
 
 
<!-- External Links -->
[Service application Azure]: http://go.microsoft.com/fwlink/?LinkId=529714

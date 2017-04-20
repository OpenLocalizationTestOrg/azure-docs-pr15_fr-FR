<properties
   pageTitle="Présentation application tissu de Service et des stratégies de sécurité service | Microsoft Azure"
   description="Une vue d’ensemble de l’exécution d’une application de Service tissu sous système et des comptes de sécurité local, y compris le point SetupEntry où une application a besoin pour effectuer une action dotés de privilèges avant le début"
   services="service-fabric"
   documentationCenter=".net"
   authors="msfussell"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="09/22/2016"
   ms.author="mfussell"/>

# <a name="configure-security-policies-for-your-application"></a>Configurer des stratégies de sécurité pour votre application
TISSU Service Azure offre la possibilité pour sécuriser les applications qui s’exécutent dans le cluster sous différents comptes d’utilisateurs. Service TISSU permet également de sécuriser les ressources utilisées par les applications au moment de déploiement sous le compte d’utilisateur tels que des fichiers, des répertoires et des certificats. Ainsi, applications en cours d’exécution, même dans un environnement hébergé partagé, sécurisé entre eux. 

Par défaut, les applications de Service tissu s’exécutent sous le compte que le processus Fabric.exe s’exécute sous. Service TISSU permet également d’exécuter des applications sous un compte d’utilisateur local ou un compte système local, spécifié dans le manifeste de l’application. Types de comptes système local pris en charge pour sont **LocalUser**, **service réseau**, **service local**et **système local**.

 Lorsque vous exécutez tissu de Service sur Windows Server dans votre centre de données à l’aide du programme d’installation autonome, vous pouvez utiliser des comptes de domaine Active Directory (AD).

Les groupes d’utilisateurs peuvent être définis et créés afin qu’un ou plusieurs utilisateurs peuvent être ajoutés à chaque groupe à être gérés ensemble. Ceci est utile lorsque vous avez plusieurs utilisateurs pour les points d’entrée de services différente et ils doivent être certains privilèges courantes sont disponibles au niveau du groupe.

## <a name="configure-the-policy-for-service-setupentrypoint"></a>Configurer la stratégie de service SetupEntryPoint

Comme indiqué dans le [modèle d’application](service-fabric-application-model.md) le **SetupEntryPoint** est un point d’entrée dotés de privilèges qui s’exécute avec les mêmes informations d’identification en tant que Service tissu (généralement le compte de *service réseau* ) avant le point d’entrée. Le fichier exécutable spécifié par un **point d’entrée** est généralement l’hôte de service longue afin qu’une entrée d’installation séparés pointent évite d’avoir à exécuter l’hôte de service exécutable avec des privilèges élevés pour une période prolongée. Le fichier exécutable spécifié par un **point d’entrée** est exécuté après **SetupEntryPoint** a quitté avec succès. Le processus qui en résulte est contrôlé et redémarré, à partir de nouveau **SetupEntryPoint**, si elle se termine jamais ou se bloque.

Voici un exemple de manifeste service simple qui montre la SetupEntryPoint et point d’entrée principal pour le service.

~~~
<?xml version="1.0" encoding="utf-8" ?>
<ServiceManifest Name="MyServiceManifest" Version="SvcManifestVersion1" xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  <Description>An example service manifest</Description>
  <ServiceTypes>
    <StatelessServiceType ServiceTypeName="MyServiceType" />
  </ServiceTypes>
  <CodePackage Name="Code" Version="1.0.0">
    <SetupEntryPoint>
      <ExeHost>
        <Program>MySetup.bat</Program>
        <WorkingFolder>CodePackage</WorkingFolder>
      </ExeHost>
    </SetupEntryPoint>
    <EntryPoint>
      <ExeHost>
        <Program>MyServiceHost.exe</Program>
      </ExeHost>
    </EntryPoint>
  </CodePackage>
  <ConfigPackage Name="Config" Version="1.0.0" />
</ServiceManifest>
~~~

### <a name="configure-the-policy-using-a-local-account"></a>Configurer la stratégie à l’aide d’un compte local

Après avoir configuré le service pour avoir point une configuration d’entrée, vous pouvez modifier les autorisations de sécurité qu’elle s’exécute sous le manifeste d’application. L’exemple suivant montre comment configurer le service s’exécute sous privilèges du compte utilisateur administrateur.

~~~
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="MyApplicationType" ApplicationTypeVersion="1.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
   <ServiceManifestImport>
      <ServiceManifestRef ServiceManifestName="MyServiceTypePkg" ServiceManifestVersion="1.0.0" />
      <ConfigOverrides />
      <Policies>
         <RunAsPolicy CodePackageRef="Code" UserRef="SetupAdminUser" EntryPointType="Setup" />
      </Policies>
   </ServiceManifestImport>
   <Principals>
      <Users>
         <User Name="SetupAdminUser">
            <MemberOf>
               <SystemGroup Name="Administrators" />
            </MemberOf>
         </User>
      </Users>
   </Principals>
</ApplicationManifest>
~~~

Tout d’abord, créez une section **entités** avec un nom d’utilisateur, par exemple SetupAdminUser. Cela indique que l’utilisateur est membre du groupe administrateurs système.

Ensuite, sous la section **ServiceManifestImport** , configurez une stratégie pour appliquer cette entité de sécurité à **SetupEntryPoint**. Service TISSU vous indique que lorsque le fichier **MySetup.bat** est exécuté, il doit être RunAs avec des privilèges d’administrateur. Étant donné que vous avez *pas* appliqué une stratégie du point d’entrée principal, le code dans **MyServiceHost.exe** s’exécute sous le compte de **service réseau** système. Il s’agit du compte par défaut tous les points d’entrée de service sont exécutées sous la forme.

Nous allons maintenant ajouter le fichier MySetup.bat au projet Visual Studio pour tester les privilèges d’administrateur. Dans Visual Studio, avec le bouton droit sur le projet de service et ajoutez un nouveau fichier nommé MySetup.bat.

Ensuite, assurez-vous que le fichier MySetup.bat est inclus dans le package de service. Par défaut, il n’est pas. Sélectionnez le fichier, avec le bouton droit pour afficher le menu contextuel, puis sélectionnez **Propriétés**. Dans la boîte de dialogue Propriétés, vérifiez que le **Copier dans le répertoire de sortie** est défini sur **Copier si plus récent**. Voir l’écran suivant capture.

![Visual Studio CopyToOutput pour le fichier de commandes SetupEntryPoint][image1]

Maintenant, ouvrez le fichier MySetup.bat et ajoutez les commandes suivantes :

~~~
REM Set a system environment variable. This requires administrator privilege
setx -m TestVariable "MyValue"
echo System TestVariable set to > out.txt
echo %TestVariable% >> out.txt

REM To delete this system variable us
REM REG delete "HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Session Manager\Environment" /v TestVariable /f
~~~

Ensuite, créer et déployer la solution vers un cluster de développement local.  Une fois que le service a déjà commencé, comme illustré dans l’Explorateur de tissu de Service, vous constatez que le MySetup.bat a réussi de deux façons : Ouvrez une invite de commande PowerShell et tapez :

~~~
PS C:\ [Environment]::GetEnvironmentVariable("TestVariable","Machine")
MyValue
~~~

Ensuite, notez le nom du nœud où le service a été déployé et mise en route dans l’Explorateur de tissu Service, par exemple, nœud 2. Ensuite, accédez au dossier application instance travail pour rechercher le fichier out.txt qui affiche la valeur de **TestVariable**. Pour exemple si ce service a été déployé sur nœud 2, puis vous pouvez accéder à ce chemin d’accès pour les **MyApplicationType**:

~~~
C:\SfDevCluster\Data\_App\Node.2\MyApplicationType_App\work\out.txt
~~~

###  <a name="configure-the-policy-using-local-system-accounts"></a>Configurer la stratégie à l’aide de comptes système local
Il est souvent préférable d’exécuter le script de démarrage à l’aide d’un compte système local plutôt qu’un compte administrateurs comme illustré qui précède. En cours d’exécution le RunAs stratégie en tant qu’administrateur généralement ne fonctionne pas correctement dans la mesure où machines ont utilisateur (contrôle d’accès) activée par défaut. Dans ce cas, **est recommandé d’exécuter le SetupEntryPoint en tant que système local au lieu d’un utilisateur local ajouté au groupe Administrateurs**. L’exemple suivant illustre la définition de la SetupEntryPoint à exécuter en tant que système local.

~~~
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="MyApplicationType" ApplicationTypeVersion="1.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
   <ServiceManifestImport>
      <ServiceManifestRef ServiceManifestName="MyServiceTypePkg" ServiceManifestVersion="1.0.0" />
      <ConfigOverrides />
      <Policies>
         <RunAsPolicy CodePackageRef="Code" UserRef="SetupLocalSystem" EntryPointType="Setup" />
      </Policies>
   </ServiceManifestImport>
   <Principals>
      <Users>
         <User Name="SetupLocalSystem" AccountType="LocalSystem" />
      </Users>
   </Principals>
</ApplicationManifest>
~~~

##  <a name="launch-powershell-commands-from-a-setupentrypoint"></a>Lancement des commandes PowerShell à partir d’un SetupEntryPoint
Pour exécuter PowerShell à partir du point **SetupEntryPoint** , vous pouvez exécuter **PowerShell.exe** dans un fichier de commandes qui pointe vers un fichier de PowerShell. Tout d’abord, ajoutez un fichier de PowerShell pour le projet de service, tel que **MySetup.ps1**. N’oubliez pas de définir la propriété *Copier si plus récent* afin que le fichier est également inclus dans le package de service. L’exemple suivant montre un exemple de fichier lot pour lancer un fichier PowerShell appelé MySetup.ps1, qui définit une variable d’environnement appelée **TestVariable**.


MySetup.bat pour lancer le fichier PowerShell.

~~~
powershell.exe -ExecutionPolicy Bypass -Command ".\MySetup.ps1"
~~~

Dans le fichier PowerShell, ajoutez le code suivant pour définir une variable d’environnement système :

~~~
[Environment]::SetEnvironmentVariable("TestVariable", "MyValue", "Machine")
[Environment]::GetEnvironmentVariable("TestVariable","Machine") > out.txt
~~~

**Remarque :** Par défaut lorsque le fichier de commandes s’exécute il ressemble au niveau du dossier applications appelé **fonctionne** pour les fichiers. Dans ce cas, lors de l’exécution MySetup.bat nous voulons afin de rechercher la MySetup.ps1 dans le même dossier, qui est le dossier du **package code** application. Pour modifier ce dossier, définissez le dossier de travail comme illustré ci-dessous.

~~~
<SetupEntryPoint>
    <ExeHost>
    <Program>MySetup.bat</Program>
    <WorkingFolder>CodePackage</WorkingFolder>
    </ExeHost>
</SetupEntryPoint>
~~~

## <a name="using-console-redirection-for-local-debugging"></a>À l’aide de la redirection de console pour le débogage local
Parfois, il est utile pour voir le résultat de la console d’exécuter un script pour le débogage. Pour ce faire, vous pouvez définir une stratégie de redirection console qui écrit la sortie vers un fichier. La sortie du fichier est écrites dans le dossier d’application appelé **log** sur le nœud où l’application est déployée et exécuter (voir où trouver cette dans l’exemple précédent).

**Remarque : jamais** utiliser la stratégie de redirection console dans une application déployée en production, cela peut avoir un impact sur le basculement d’application. **Uniquement** à utiliser pour le développement local et le débogage.  

L’exemple suivant illustre la définition de la redirection de la console avec une valeur FileRetentionCount.

~~~
<SetupEntryPoint>
    <ExeHost>
    <Program>MySetup.bat</Program>
    <WorkingFolder>CodePackage</WorkingFolder>
    <ConsoleRedirection FileRetentionCount="10"/>
    </ExeHost>
</SetupEntryPoint>
~~~

Si vous modifiez maintenant le fichier MySetup.ps1 pour écrire une commande **écho** , cela écrit dans le fichier de sortie pour le débogage.

~~~
Echo "Test console redirection which writes to the application log folder on the node that the application is deployed to"
~~~

**Une fois que vous avez déboguer le script, supprimez immédiatement cette stratégie de redirection de console**

## <a name="configure-policy-for-service-code-packages"></a>Configurer la stratégie pour les packages de code de service 
Dans la procédure précédente, vous avez appris à appliquer la stratégie RunAs à SetupEntryPoint. Nous allons examiner de plus près comment créer des identités différentes qui peuvent être appliquées en tant que stratégies de service.

### <a name="create-local-user-groups"></a>Créer des groupes d’utilisateurs locaux
Groupes d’utilisateurs peuvent être définis et créées pour autoriser un ou plusieurs utilisateurs à ajouter à un groupe. Ceci est particulièrement utile si vous avez plusieurs utilisateurs pour les points d’entrée de services différente et ils doivent être certains privilèges courantes sont disponibles au niveau du groupe. L’exemple suivant montre un groupe local appelé **LocalAdminGroup** en tant qu’administrateur. Deux utilisateurs, Customer1 et Customer2, deviennent membres de ce groupe local.

~~~
<Principals>
 <Groups>
   <Group Name="LocalAdminGroup">
     <Membership>
       <SystemGroup Name="Administrators"/>
     </Membership>
   </Group>
 </Groups>
  <Users>
     <User Name="Customer1">
        <MemberOf>
           <Group NameRef="LocalAdminGroup" />
        </MemberOf>
     </User>
    <User Name="Customer2">
      <MemberOf>
        <Group NameRef="LocalAdminGroup" />
      </MemberOf>
    </User>
  </Users>
</Principals>
~~~

### <a name="create-local-users"></a>Créer des utilisateurs locaux
Vous pouvez créer un utilisateur local qui peut être utilisé pour sécuriser un service au sein de l’application. Lorsqu’un type de compte **LocalUser** est spécifié dans la section entités du manifeste d’application, Service TISSU crée des comptes d’utilisateurs locaux sur les ordinateurs où l’application est déployée. Par défaut, ces comptes ne disposent pas les mêmes noms que ceux qui sont spécifiés dans le manifeste d’application (par exemple, Customer3 dans l’exemple suivant). En revanche, ils sont générés dynamiquement et des mots de passe aléatoires.

~~~
<Principals>
  <Users>
     <User Name="Customer3" AccountType="LocalUser" />
  </Users>
</Principals>
~~~

<!-- If an application requires that the user account and password be same on all machines (for example, to enable NTLM authentication), the cluster manifest must set NTLMAuthenticationEnabled to true. The cluster manifest must also specify an NTLMAuthenticationPasswordSecret that will be used to generate the same password across all machines.

<Section Name="Hosting">
      <Parameter Name="EndpointProviderEnabled" Value="true"/>
      <Parameter Name="NTLMAuthenticationEnabled" Value="true"/>
      <Parameter Name="NTLMAuthenticationPassworkSecret" Value="******" IsEncrypted="true"/>
 </Section>
-->

### <a name="assign-policies-to-the-service-code-packages"></a>Affecter des stratégies pour les modules de code de service
La section **RunAsPolicy** pour un **ServiceManifestImport** Spécifie le compte à partir de la section entités qui doit être utilisé pour exécuter un lot de code. Il associe également packages code à partir du manifeste de service aux comptes d’utilisateurs dans la section entités. Vous pouvez spécifier pour le paramétrage ou les points d’entrée principale, ou vous pouvez spécifier `All` pour l’appliquer aux deux. L’exemple suivant illustre différentes stratégies appliquées :

~~~
<Policies>
<RunAsPolicy CodePackageRef="Code" UserRef="LocalAdmin" EntryPointType="Setup"/>
<RunAsPolicy CodePackageRef="Code" UserRef="Customer3" EntryPointType="Main"/>
</Policies>
~~~

Si **EntryPointType** n’est pas spécifié, la valeur par défaut est défini sur EntryPointType = « Principale ». Si vous spécifiez **SetupEntryPoint** est particulièrement utile lorsque vous voulez exécuter certaine opération de configuration des privilèges élevés sous un compte système. Le code de service peut s’exécuter sous un compte doté de privilèges inférieur.

### <a name="apply-a-default-policy-to-all-service-code-packages"></a>Appliquer une stratégie par défaut à tous les modules de code de service
La section **DefaultRunAsPolicy** est utilisée pour spécifier un compte d’utilisateur par défaut pour tous les packages de code qui n’ont pas un spécifique **RunAsPolicy** définis. Si la plupart des packages code spécifiés dans manifeste du service utilisé par une application est nécessaire pour s’exécuter sous le même utilisateur, l’application peut simplement définir une stratégie de RunAs par défaut avec ce compte d’utilisateur. L’exemple suivant spécifie que si un package de code ne comporte pas un **RunAsPolicy** spécifié, le package de code doit s’exécuter sous la **MyDefaultAccount** spécifiée dans la section entités.

~~~
<Policies>
  <DefaultRunAsPolicy UserRef="MyDefaultAccount"/>
</Policies>
~~~
### <a name="using-an-active-directory-domain-group-or-user"></a>À l’aide d’un utilisateur ou un groupe de domaine Active Directory
Pour tissu de Service installé sur Windows Server avec le programme d’installation autonome, vous pouvez exécuter le service sous les informations d’identification d’un utilisateur Active Directory (AD) ou un compte de groupe. Remarque : Il est Active Directory sur site au sein de votre domaine et non avec Azure Active Directory (DAS). En utilisant un domaine utilisateur ou groupe, vous pouvez alors accéder d’autres ressources dans le domaine (par exemple, partages de fichiers) qui ont été accordés des autorisations.

L’exemple suivant montre un utilisateur AD appelé *TestUser* avec leur domaine de mot de passe chiffré à l’aide d’un certificat appelé *MonCert*. Vous pouvez utiliser la `Invoke-ServiceFabricEncryptText` commande Powershell pour créer le texte de chiffrement secrète. Consultez cet article [Gestion des secrets dans les applications de Service TISSU](service-fabric-application-secret-management.md) pour plus d’informations sur la façon de. La clé privée du certificat à déchiffrer le mot de passe doit être déployée sur l’ordinateur local dans une méthode de hors (dans Azure il s’agit via le Gestionnaire de ressources). Puis, lorsque Service TISSU déploie le package de service à l’ordinateur, il est en mesure de déchiffrer le code secret et ainsi que le nom d’utilisateur, vous authentifier avec Active Directory pour exécuter sous les informations d’identification.

~~~
<Principals>
  <Users>
    <User Name="TestUser" AccountType="DomainUser" AccountName="Domain\User" Password="[Put encrypted password here using MyCert certificate]" PasswordEncrypted="true" />
  </Users>
</Principals>
<Policies>
  <DefaultRunAsPolicy UserRef="TestUser" />
  <SecurityAccessPolicies>
    <SecurityAccessPolicy ResourceRef="MyCert" PrincipalRef="TestUser" GrantRights="Full" ResourceType="Certificate" />
  </SecurityAccessPolicies>
</Policies>
<Certificates>
~~~


## <a name="assign-securityaccesspolicy-for-http-and-https-endpoints"></a>Attribuez SecurityAccessPolicy pour les points de terminaison HTTP et HTTPS
Si vous appliquez une stratégie RunAs à un service, et manifeste de service déclare ressources de point de terminaison avec le protocole HTTP, vous devez spécifier une **SecurityAccessPolicy** pour vous assurer que les ports affectés à ces points de terminaison sont correctement répertorié pour le compte d’utilisateur RunAs qui s’exécute le service sous contrôle d’accès. Dans le cas contraire, **http.sys** n’a pas accès au service, et vous obtenez des échecs des appels à partir du client. L’exemple suivant applique le compte Customer3 à un point de terminaison appelé **ServiceEndpointName**, lui affecter des droits d’accès complet.

~~~
<Policies>
   <RunAsPolicy CodePackageRef="Code" UserRef="Customer1" />
   <!--SecurityAccessPolicy is needed if RunAsPolicy is defined and the Endpoint is http -->
   <SecurityAccessPolicy ResourceRef="EndpointName" PrincipalRef="Customer1" />
</Policies>
~~~

Du point de terminaison HTTPS, vous devez également indiquer le nom du certificat pour revenir au client. Vous pouvez le faire à l’aide **EndpointBindingPolicy**, avec le certificat défini dans une section de certificats dans le manifeste d’application.

~~~
<Policies>
   <RunAsPolicy CodePackageRef="Code" UserRef="Customer1" />
  <!--SecurityAccessPolicy is needed if RunAsPolicy is defined and the Endpoint is http -->
   <SecurityAccessPolicy ResourceRef="EndpointName" PrincipalRef="Customer1" />
  <!--EndpointBindingPolicy is needed if the EndpointName is secured with https -->
  <EndpointBindingPolicy EndpointRef="EndpointName" CertificateRef="Cert1" />
</Policies
~~~


## <a name="a-complete-application-manifest-example"></a>Un exemple de manifeste d’application complète
Le manifeste d’application suivant montre la plupart des paramètres différents :

~~~
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="Application3Type" ApplicationTypeVersion="1.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
   <Parameters>
      <Parameter Name="Stateless1_InstanceCount" DefaultValue="-1" />
   </Parameters>
   <ServiceManifestImport>
      <ServiceManifestRef ServiceManifestName="Stateless1Pkg" ServiceManifestVersion="1.0.0" />
      <ConfigOverrides />
      <Policies>
         <RunAsPolicy CodePackageRef="Code" UserRef="Customer1" />
         <RunAsPolicy CodePackageRef="Code" UserRef="LocalAdmin" EntryPointType="Setup" />
        <!--SecurityAccessPolicy is needed if RunAsPolicy is defined and the Endpoint is http -->
         <SecurityAccessPolicy ResourceRef="EndpointName" PrincipalRef="Customer1" />
        <!--EndpointBindingPolicy is needed the EndpointName is secured with https -->
        <EndpointBindingPolicy EndpointRef="EndpointName" CertificateRef="Cert1" />
     </Policies>
   </ServiceManifestImport>
   <DefaultServices>
      <Service Name="Stateless1">
         <StatelessService ServiceTypeName="Stateless1Type" InstanceCount="[Stateless1_InstanceCount]">
            <SingletonPartition />
         </StatelessService>
      </Service>
   </DefaultServices>
   <Principals>
      <Groups>
         <Group Name="LocalAdminGroup">
            <Membership>
               <SystemGroup Name="Administrators" />
            </Membership>
         </Group>
      </Groups>
      <Users>
         <User Name="LocalAdmin">
            <MemberOf>
               <Group NameRef="LocalAdminGroup" />
            </MemberOf>
         </User>
         <!--Customer1 below create a local account that this service runs under -->
         <User Name="Customer1" />
         <User Name="MyDefaultAccount" AccountType="NetworkService" />
      </Users>
   </Principals>
   <Policies>
      <DefaultRunAsPolicy UserRef="LocalAdmin" />
   </Policies>
   <Certificates>
     <EndpointCertificate Name="Cert1" X509FindValue="FF EE E0 TT JJ DD JJ EE EE XX 23 4T 66 "/>
  </Certificates>
</ApplicationManifest>
~~~


<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Étapes suivantes

* [Comprendre le modèle d’application](service-fabric-application-model.md)
* [Spécifier des ressources dans un manifeste de service](service-fabric-service-manifest-resources.md)
* [Déployer une application](service-fabric-deploy-remove-applications.md)

[image1]: ./media/service-fabric-application-runas-security/copy-to-output.png

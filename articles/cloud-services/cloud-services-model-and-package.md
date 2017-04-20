<properties
    pageTitle="Qu’est un modèle de Service Cloud et un package | Microsoft Azure"
    description="Décrit le modèle de service cloud (.csdef, .cscfg) et le package (.cspkg) dans Azure"
    services="cloud-services"
    documentationCenter=""
    authors="Thraka"
    manager="timlt"
    editor=""/>
<tags
    ms.service="cloud-services"
    ms.workload="tbd"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/06/2016"
    ms.author="adegeo"/>

# <a name="what-is-the-cloud-service-model-and-how-do-i-package-it"></a>Quel est le modèle de Service Cloud et comment le package ?
Un service cloud est créé à partir de trois composants, la définition de service _(.csdef)_, la configuration de service _(.cscfg)_et un package de service _(.cspkg)_. Fichiers **ServiceDefinition.csdef** et de **ServiceConfig.cscfg** sont basés sur XML et décrivent la structure du service cloud et comment elle est configurée ; collectivement appelés le modèle. Le **ServicePackage.cspkg** est un fichier zip qui est généré à partir de la **ServiceDefinition.csdef** et entre autres, contient toutes les dépendances binaire requis. Azure crée un service cloud à partir de la **ServicePackage.cspkg** et la **ServiceConfig.cscfg**.

Une fois que le service cloud s’exécute dans Azure, vous pouvez le reconfigurer via le fichier **ServiceConfig.cscfg** , mais vous ne pouvez pas modifier la définition.

## <a name="what-would-you-like-to-know-more-about"></a>Que voulez-vous en savoir plus ?

* Je veux en savoir plus sur les fichiers [ServiceDefinition.csdef](#csdef) et [ServiceConfig.cscfg](#cscfg) .
* Je sais déjà à ce sujet, donnez-moi [quelques exemples](#next-steps) de ce que je peux configurer.
* Je veux créer la [ServicePackage.cspkg](#cspkg).
* J’utilise Visual Studio et je veux...
    * [Créer un nouveau service cloud][vs_create]
    * [Reconfigurer un service cloud existant][vs_reconfigure]
    * [Déploiement d’un projet de Service Cloud][vs_deploy]
    * [Bureau à distance dans une instance de service cloud][remotedesktop]

<a name="csdef"></a>
## <a name="servicedefinitioncsdef"></a>ServiceDefinition.csdef
Le fichier **ServiceDefinition.csdef** spécifie les paramètres qui sont utilisés par Azure pour configurer un service cloud. Le [Schéma de définition du Service Azure (fichier .csdef)](https://msdn.microsoft.com/library/azure/ee758711.aspx) fournit le format autorisé pour un fichier de définition de service. L’exemple suivant montre les paramètres qui peuvent être définis pour les rôles Web et de travail :

```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceDefinition name="MyServiceName" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition">
  <WebRole name="WebRole1" vmsize="Medium">
    <Sites>
      <Site name="Web">
        <Bindings>
          <Binding name="HttpIn" endpointName="HttpIn" />
        </Bindings>
      </Site>
    </Sites>
    <Endpoints>
      <InputEndpoint name="HttpIn" protocol="http" port="80" />
      <InternalEndpoint name="InternalHttpIn" protocol="http" />
    </Endpoints>
    <Certificates>
      <Certificate name="Certificate1" storeLocation="LocalMachine" storeName="My" />
    </Certificates>
    <Imports>
      <Import moduleName="Connect" />
      <Import moduleName="Diagnostics" />
      <Import moduleName="RemoteAccess" />
      <Import moduleName="RemoteForwarder" />
    </Imports>
    <LocalResources>
      <LocalStorage name="localStoreOne" sizeInMB="10" />
      <LocalStorage name="localStoreTwo" sizeInMB="10" cleanOnRoleRecycle="false" />
    </LocalResources>
    <Startup>
      <Task commandLine="Startup.cmd" executionContext="limited" taskType="simple" />
    </Startup>
  </WebRole>

  <WorkerRole name="WorkerRole1">
    <ConfigurationSettings>
      <Setting name="DiagnosticsConnectionString" />
    </ConfigurationSettings>
    <Imports>
      <Import moduleName="RemoteAccess" />
      <Import moduleName="RemoteForwarder" />
    </Imports>
    <Endpoints>
      <InputEndpoint name="Endpoint1" protocol="tcp" port="10000" />
      <InternalEndpoint name="Endpoint2" protocol="tcp" />
    </Endpoints>
  </WorkerRole>
</ServiceDefinition>
```

Vous pouvez faire référence à la [[schéma de définition du Service]] pour mieux comprendre le schéma XML utilisé ici, cependant, voici une explication rapide de certains éléments :

**Sites**  
Contient les définitions pour les applications web ou sites Web qui sont hébergées dans IIS7.

**InputEndpoints**  
Contient les définitions de points de terminaison qui sont utilisées pour contacter le service cloud.

**InternalEndpoints**  
Contient les définitions de points de terminaison qui sont utilisés par les instances de rôles pour communiquer avec eux.

**ConfigurationSettings**  
Contient les définitions de paramètre pour les fonctionnalités d’un rôle spécifique.

**Certificats**  
Contient les définitions de certificats qui sont nécessaires pour un rôle. L’exemple précédent montre un certificat utilisé pour la configuration d’Azure se connecter.

**LocalResources**  
Contient les définitions des ressources de stockage local. Une ressource de stockage local est un répertoire réservé sur le système de fichiers de la machine virtuelle dans lequel s’exécute une instance d’un rôle.

**Importe des fichiers**  
Contient les définitions de modules importés. L’exemple précédent montre les modules de connexion Bureau à distance et Azure se connecter.

**Démarrage**  
Contient des tâches qui sont exécutent lorsque le rôle démarre. Les tâches sont définies dans un fichier exécutable ou .cmd.



<a name="cscfg"></a>
## <a name="serviceconfigurationcscfg"></a>ServiceConfiguration.cscfg
La configuration des paramètres de votre service de nuage est déterminée par les valeurs dans le fichier **ServiceConfiguration.cscfg** . Vous indiquez le nombre d’instances que vous souhaitez déployer pour chaque rôle dans ce fichier. Les valeurs pour les paramètres de configuration que vous avez définie dans le fichier de définition de service sont ajoutés au fichier de configuration du service. Les empreintes pour tous les certificats de gestion associés au service cloud sont également ajoutées au fichier. Le [Schéma de Configuration du Service Azure (fichier .cscfg)](https://msdn.microsoft.com/library/azure/ee758710.aspx) fournit le format autorisé pour un fichier de configuration de service.

Le fichier de configuration de service n’est inclu dans l’application, mais est téléchargé dans Azure dans un fichier distinct et est utilisé pour configurer le service cloud. Vous pouvez télécharger un fichier de configuration de service sans redéployez votre service cloud. Les valeurs de configuration du service cloud peuvent être modifiées pendant l’exécution du service cloud. L’exemple suivant montre les paramètres de configuration pouvant être définies pour les rôles Web et de travail :

```xml
<?xml version="1.0"?>
<ServiceConfiguration serviceName="MyServiceName" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceConfiguration">
  <Role name="WebRole1">
    <Instances count="2" />
    <ConfigurationSettings>
      <Setting name="SettingName" value="SettingValue" />
    </ConfigurationSettings>

    <Certificates>
      <Certificate name="CertificateName" thumbprint="CertThumbprint" thumbprintAlgorithm="sha1" />
      <Certificate name="Microsoft.WindowsAzure.Plugins.RemoteAccess.PasswordEncryption"
         thumbprint="CertThumbprint" thumbprintAlgorithm="sha1" />
    </Certificates>
  </Role>
</ServiceConfiguration>
```

Vous pouvez faire référence au [Schéma de Configuration de Service](https://msdn.microsoft.com/library/azure/ee758710.aspx) permettant de mieux comprendre le schéma XML utilisé ici, cependant, voici une explication rapide des éléments :

**Instances**  
Configure le nombre d’exécution d’instances pour le rôle. Pour empêcher le potentiellement deviennent indisponibles pendant la mise à niveau de votre service cloud, il est conseillé que vous déployez plusieurs instances des rôles web public. En procédant ainsi, vous remplissez les instructions fournies dans la [Azure calculer Service (contrat de niveau)](http://azure.microsoft.com/support/legal/sla/), qui garantit la connectivité externe 99,95 % des rôles avec accès Internet lors du déploiement d’un service de deux ou plusieurs instances de rôle.

**ConfigurationSettings**  
Configure les paramètres pour les instances en cours d’exécution pour un rôle. Le nom de la `<Setting>` éléments doivent correspondre aux définition des critères utilisés dans le fichier de définition de service.

**Certificats**  
Configure les certificats utilisés par le service. Le code précédent montre comment définir le certificat pour le module d’accès à distance. La valeur de l’attribut *empreinte* doit être définie à l’empreinte numérique du certificat à utiliser.

<p/>

 >[AZURE.NOTE] L’empreinte numérique du certificat peut être ajouté au fichier de configuration à l’aide d’un éditeur de texte ou la valeur peut être ajoutée sous l’onglet **certificats** de la page **Propriétés** du rôle dans Visual Studio.



## <a name="defining-ports-for-role-instances"></a>Définition des ports pour les instances de rôle
Azure permet de point d’entrée qu’un seul à un rôle web. Cela signifie que tout le trafic s’effectue par une adresse IP. Vous pouvez configurer votre site Web pour partager un port en configurant l’en-tête d’hôte pour diriger la demande vers l’emplacement correct. Vous pouvez également configurer vos applications pour écouter les ports connus sur l’adresse IP.

L’exemple suivant illustre la configuration d’un rôle web avec une application web et de site Web. Le site Web est configuré comme l’emplacement d’entrée par défaut sur le port 80, et les applications web sont configurées pour recevoir des demandes d’un en-tête autre hôte qui est appelé « mail.mysite.cloudapp.net ».

```xml
<WebRole>
  <ConfigurationSettings>
    <Setting name="DiagnosticsConnectionString" />
  </ConfigurationSettings>
  <Endpoints>
    <InputEndpoint name="HttpIn" protocol="http" <mark>port="80"</mark> />
    <InputEndpoint name="Https" protocol="https" port="443" certificate="SSL"/>
    <InputEndpoint name="NetTcp" protocol="tcp" port="808" certificate="SSL"/>
  </Endpoints>
  <LocalResources>
    <LocalStorage name="Sites" cleanOnRoleRecycle="true" sizeInMB="100" />
  </LocalResources>
  <Site name="Mysite" packageDir="Sites\Mysite">
    <Bindings>
      <Binding name="http" endpointName="HttpIn" />
      <Binding name="https" endpointName="Https" />
      <Binding name="tcp" endpointName="NetTcp" />
    </Bindings>
  </Site>
  <Site name="MailSite" packageDir="MailSite">
    <Bindings>
      <Binding name="mail" endpointName="HttpIn" <mark>hostheader="mail.mysite.cloudapp.net"</mark> />
    </Bindings>
    <VirtualDirectory name="artifacts" />
    <VirtualApplication name="storageproxy">
      <VirtualDirectory name="packages" packageDir="Sites\storageProxy\packages"/>
    </VirtualApplication>
  </Site>
</WebRole>
```


## <a name="changing-the-configuration-of-a-role"></a>Modification de la configuration d’un rôle
Vous pouvez mettre à jour la configuration de votre service cloud lors de son exécution dans Azure, sans déconnecter le service. Pour modifier les informations de configuration, vous pouvez télécharger un fichier de configuration, ou modifier le fichier de configuration en place et s’appliquent à votre service en cours d’exécution. Les modifications suivantes peuvent être apportées à la configuration d’un service :

- **Modification des valeurs de paramètres de configuration**  
Quand un paramètre des modifications de configuration, une instance de rôle pouvez choisir appliquer les modifications pendant que l’instance est en ligne ou pour Corbeille normalement l’instance et appliquer la modification tandis que l’instance est en mode hors connexion.

- **Modification de la topologie de service d’instances de rôles**  
Modifications de la topologie ne concernent pas les instances en cours d’exécution, à l’exception de l’endroit où une instance est supprimée. Toutes les instances restants généralement n’avez pas besoin à recycler ; Toutefois, vous pouvez choisir de recycler des instances de rôle en réponse à une modification de la topologie.

- **Modification de l’empreinte numérique du certificat**  
Vous pouvez mettre à jour un certificat uniquement lorsqu’une instance de rôle est en mode hors connexion. Si un certificat est ajouté, supprimé ou modifié alors qu’une instance de rôle est connectée, Azure normalement prend l’instance en mode hors connexion pour mettre à jour le certificat et mettez-le en ligne une fois que la modification est terminée.

### <a name="handling-configuration-changes-with-service-runtime-events"></a>Gestion des changements de configuration avec les événements pour l’exécution du Service
La [Bibliothèque d’exécution Azure](https://msdn.microsoft.com/library/azure/mt419365.aspx) inclut l’espace de noms [Microsoft.WindowsAzure.ServiceRuntime](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.aspx) , qui fournit des classes pour interagir avec l’environnement Azure à partir de code en cours d’exécution dans une instance d’un rôle. La classe [RoleEnvironment](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleenvironment.aspx) définit les événements suivants sont déclenchés avant et après un changement de configuration :

- **Événements de [modification](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleenvironment.changing.aspx)**  
Cela se produit avant la modification de configuration est appliquée à une instance spécifique d’un rôle vous donner une chance de prendre des instances de rôle si nécessaire.
- **Événement [Changed](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleenvironment.changed.aspx)**  
Se produit après que la modification de configuration est appliquée à une instance d’un rôle spécifiée.

> [AZURE.NOTE] Étant donné que les modifications de certificat toujours prennent les instances d’un rôle en mode hors connexion, ils ne déclenchent pas les événements RoleEnvironment.Changing ou RoleEnvironment.Changed.

<a name="cspkg"></a>
## <a name="servicepackagecspkg"></a>ServicePackage.cspkg
Pour déployer une application comme un service cloud dans Azure, vous devez tout d’abord empaqueter l’application dans le format approprié. Vous pouvez utiliser l’outil de ligne de commande **CSPack** (installé avec le [Kit de développement logiciel Azure](https://azure.microsoft.com/downloads/)) pour créer le fichier de package au lieu de Visual Studio.

**CSPack** utilise le contenu du fichier de configuration de service et fichier de définition de service pour définir le contenu du package. **CSPack** génère un fichier de package d’application (.cspkg) que vous pouvez télécharger sur Azure à l’aide du [portail Azure](cloud-services-how-to-create-deploy-portal.md#create-and-deploy). Par défaut, le package se nomme `[ServiceDefinitionFileName].cspkg`, mais vous pouvez spécifier un autre nom à l’aide de la `/out` option de **CSPack**.

**CSPack** se trouve généralement à  
`C:\Program Files\Microsoft SDKs\Azure\.NET SDK\[sdk-version]\bin\`

>[AZURE.NOTE]
CSPack.exe (sous windows) est disponible en exécutant le raccourci **invite de commandes de Microsoft Azure** qui est installé avec le Kit de développement.  
>  
>Exécutez le programme CSPack.exe seul à voir la documentation relative à tous les commutateurs et les commandes.

<p />

>[AZURE.TIP]
Exécuter votre service cloud localement dans l' **Émulateur de calcul Microsoft Azure**, utilisez l’option **/copyonly** que cette option permet de copier les fichiers binaires de l’application à une mise en page annuaire à partir de laquelle ils peuvent être exécutés dans l’émulateur cluster.

### <a name="example-command-to-package-a-cloud-service"></a>Exemple de commande pour créer un package un service cloud
L’exemple suivant crée un package d’application qui contient les informations pour un rôle web. La commande spécifie le fichier de définition de service à utiliser, le répertoire où les fichiers binaires peuvent être trouvés, et le nom du fichier de package.

    cspack [DirectoryName]\[ServiceDefinition]
           /role:[RoleName];[RoleBinariesDirectory]
           /sites:[RoleName];[VirtualPath];[PhysicalPath]
           /out:[OutputFileName]

Si l’application contient un rôle web et un rôle de collaborateur, la commande suivante est utilisée :

    cspack [DirectoryName]\[ServiceDefinition]
           /out:[OutputFileName]
           /role:[RoleName];[RoleBinariesDirectory]
           /sites:[RoleName];[VirtualPath];[PhysicalPath]
           /role:[RoleName];[RoleBinariesDirectory];[RoleAssemblyName]

Où les variables sont définies comme suit :

| Variable                  | Valeur |
| ------------------------- | ----- |
| \[Nom du répertoire\]         | Le répertoire racine du projet qui contient le fichier .csdef du projet Azure sous-répertoire.|
| \[ServiceDefinition\]     | Le nom du fichier de définition du service. Par défaut, ce fichier est nommé ServiceDefinition.csdef.  |
| \[OutputFileName\]        | Le nom du fichier de package généré. En règle générale, cela est défini sur le nom de l’application. Si aucun nom de fichier n’est spécifié, le package d’application est créé en tant que \[ApplicationName\].cspkg.|
| \[RoleName\]              | Le nom du rôle, telle que définie dans le fichier de définition de service.|
| \[RoleBinariesDirectory] | L’emplacement des fichiers binaires pour le rôle.|
| \[VirtualPath\]           | Les répertoires physiques pour chaque chemin d’accès virtuel défini dans la section Sites de la définition du service.|
| \[PhysicalPath\]          | Les répertoires physiques du contenu pour chaque chemin d’accès virtuel défini dans le nœud de site de la définition du service.|
| \[RoleAssemblyName\]      | Le nom du fichier binaire pour le rôle.| 


## <a name="next-steps"></a>Étapes suivantes

Je suis création d’un package de service cloud et je veux...

* [Configurer le Bureau à distance pour une instance de service cloud][remotedesktop]
* [Déploiement d’un projet de Service Cloud][deploy]

J’utilise Visual Studio et je veux...

* [Créer un nouveau service cloud][vs_create]
* [Reconfigurer un service cloud existant][vs_reconfigure]
* [Déploiement d’un projet de Service Cloud][vs_deploy]
* [Configurer le Bureau à distance pour une instance de service cloud][vs_remote]

[deploy]: cloud-services-how-to-create-deploy-portal.md
[remotedesktop]: cloud-services-role-enable-remote-desktop.md
[vs_remote]: ../vs-azure-tools-remote-desktop-roles.md
[vs_deploy]: ../vs-azure-tools-cloud-service-publish-set-up-required-services-in-visual-studio.md
[vs_reconfigure]: ../vs-azure-tools-configure-roles-for-cloud-service.md
[vs_create]: ../vs-azure-tools-azure-project-create.md

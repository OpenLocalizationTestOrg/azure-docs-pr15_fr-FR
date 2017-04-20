<properties
   pageTitle="Service tissu et conteneurs déploiement | Microsoft Azure"
   description="TISSU de service et l’utilisation de conteneurs pour déployer des applications microservice. Cet article décrit les fonctionnalités qui tissu de Service fournit des conteneurs et comment déployer une image de conteneur Windows dans un cluster"
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
   ms.date="10/24/2016"
   ms.author="msfussell"/>

# <a name="preview-deploy-a-windows-container-to-service-fabric"></a>Aperçu : Déployer un conteneur Windows pour tissu de Service

> [AZURE.SELECTOR]
- [Déployer Windows conteneur](service-fabric-deploy-container.md)
- [Déployer Docker conteneur](service-fabric-deploy-container-linux.md)

Cet article vous guide dans la mise en œuvre des services dans des conteneurs de Windows. 

>[AZURE.NOTE] Cette fonctionnalité est en mode Aperçu pour Linux et pas actuellement disponibles pour une utilisation avec Windows Server 2016. Après cela, il s’agit disponibles dans preview pour Windows Server 2016 dans la version suivante de tissu de Service et pris en charge dans la version suivante.

Service TISSU présente plusieurs fonctionnalités de conteneur qui vous aident à créer des applications qui sont composées de microservices sont en conteneur. Il s’agit des services. 

Les fonctionnalités incluent ;

- L’activation et déploiement de l’image conteneur
- Gouvernance de ressources
- Authentification référentiel
- Port conteneur sur le port hôte
- Découverte de conteneur à l’autre et communication
- Capacité à configurer et définir des variables d’environnement

Vous permet de passer en revue chacune des fonctionnalités à son tour lors de la compression d’un service en conteneur à inclure dans votre application.

## <a name="packaging-a-windows-container"></a>Le conditionnement un conteneur de Windows

Lors de la compression un conteneur, vous pouvez choisir d’utiliser un modèle de projet Visual Studio ou [créer manuellement le package d’application](#manually). À l’aide de Visual Studio, la structure de package d’application et les fichiers manifeste sont créés par l’Assistant Nouveau projet pour vous (bientôt dans la version suivante).

## <a name="using-visual-studio-to-package-an-existing-container-image"></a>À l’aide de Visual Studio pour compresser une image existante conteneur

>[AZURE.NOTE] Dans une version ultérieure du Kit de développement logiciel des outils de Visual Studio, vous ne pourrez pas ajouter un conteneur à l’application de la même façon que vous pouvez ajouter un invité exécutable aujourd'hui. Voir la rubrique [déployer un invité exécutable pour tissu de Service](service-fabric-deploy-existing-app.md) . Vous devez actuellement effectuer emballage manuel comme décrit ci-dessous.

<a id="manually"></a>
## <a name="manually-packaging-and-deploying-a-container"></a>Manuellement empaqueter et déployer un conteneur
Le processus d’emballage manuellement un service en conteneur est basé sur les opérations suivantes :

1. Publiés les conteneurs dans votre référentiel.
2. Créer la structure du répertoire package.
3. Modifier le fichier manifeste du service.
4. Modifier le fichier manifeste d’application.

## <a name="container-image-deployment-and-activation"></a>Déploiement d’une image conteneur et l’activation.
Dans le [modèle d’application](service-fabric-application-model.md)du Service tissu, un conteneur représente une application hôte dans lequel plusieurs réplicas de service sont placés. Pour déployer et activer un conteneur, placez le nom de l’image conteneur dans un `ContainerHost` élément dans le manifeste de service.

Dans le manifeste de service ajouter un `ContainerHost` pointez sur l’entrée et définir le `ImageName` le nom du référentiel de conteneur et image. Le manifeste partiel suivant montre un exemple de déploiement le conteneur appelé *myimage:v1* à partir d’un référentiel appelé *myrepo*

    <CodePackage Name="Code" Version="1.0">
        <EntryPoint>
          <ContainerHost>
            <ImageName>myrepo/myimagename:v1</ImageName>
            <Commands></Commands>
          </ContainerHost>
        </EntryPoint>
    </CodePackage>

Vous pouvez fournir des commandes d’entrée à l’image conteneur en spécifiant l’option `Commands` élément avec une virgule délimités le jeu de commandes pour exécuter l’intérieur du conteneur. 

## <a name="resource-governance"></a>Gouvernance de ressources
Gouvernance de la ressource est une fonctionnalité du conteneur et restreint les ressources utilisables par le conteneur sur l’hôte. La `ResourceGovernancePolicy`, spécifiée dans le manifeste d’application, permet de déclarer les limites des ressources pour un package de code de service. Limites de ressources peuvent être définies pour ;

- Mémoire
- MemorySwap
- CpuShares (épaisseur relative du processeur)
- MemoryReservationInMB  
- BlkioWeight (épaisseur relative BlockIO). 

>[AZURE.NOTE] Dans une version ultérieure, prise en charge pour spécifier les limites de IO bloc spécifique comme sorties par, en lecture/écriture/s et autres personnes ne sera possibles.


    <ServiceManifestImport>
        <ServiceManifestRef ServiceManifestName="FrontendServicePackage" ServiceManifestVersion="1.0"/>
        <Policies>
            <ResourceGovernancePolicy CodePackageRef="FrontendService.Code" CpuShares="500" 
            MemoryInMB="1024" MemorySwapInMB="4084" MemoryReservationInMB="1024" />
        </Policies>
    </ServiceManifestImport>


## <a name="repository-authentication"></a>Authentification référentiel
Pour télécharger un conteneur, vous devrez peut-être fournir des informations d’identification au référentiel conteneur. Les informations d’identification de connexion spécifiées dans le manifeste *d’application* permettent de spécifier les informations de connexion ou d’une clé SSH pour télécharger l’image conteneur depuis le référentiel d’images.  L’exemple suivant montre un compte appelé *TestUser* ainsi que le mot de passe en texte clair. Ce n’est **pas** recommandé.


    <ServiceManifestImport>
        <ServiceManifestRef ServiceManifestName="FrontendServicePackage" ServiceManifestVersion="1.0"/>
        <Policies>
            <ContainerHostPolicies CodePackageRef="FrontendService.Code">
                <RepositoryCredentials AccountName="TestUser" Password="12345" PasswordEncrypted="false"/>
            </ContainerHostPolicies>
        </Policies>
    </ServiceManifestImport>

Le mot de passe peut et doit être chiffrée à l’aide d’un certificat déployé sur l’ordinateur.

L’exemple suivant montre un compte appelé *TestUser* avec le mot de passe chiffré à l’aide d’un certificat appelé *MonCert*. Vous pouvez utiliser la `Invoke-ServiceFabricEncryptText` commande Powershell pour créer le texte de chiffrement secrète pour le mot de passe. Consultez cet article [Gestion des secrets dans les applications de Service TISSU](service-fabric-application-secret-management.md) pour plus d’informations sur la façon de. La clé privée du certificat à déchiffrer le mot de passe doit être déployée sur l’ordinateur local dans une méthode de hors (dans Azure il s’agit via processeur). Puis, lorsque Service TISSU déploie le package de service à l’ordinateur, il est en mesure de déchiffrer le code secret et ainsi que le nom du compte, vous authentifier avec le référentiel de conteneur à l’aide de ces informations d’identification.


    <ServiceManifestImport>
        <ServiceManifestRef ServiceManifestName="FrontendServicePackage" ServiceManifestVersion="1.0"/>
        <Policies>
            <ContainerHostPolicies CodePackageRef="FrontendService.Code">
                <RepositoryCredentials AccountName="TestUser" Password="[Put encrypted password here using MyCert certificate ]" PasswordEncrypted="true"/>
            </ContainerHostPolicies>
        </Policies>
    </ServiceManifestImport>

## <a name="container-port-to-host-port-mapping"></a>Port conteneur sur le port hôte
Vous pouvez configurer un port hôte utilisé pour communiquer avec le conteneur en spécifiant une `PortBinding` manifeste d’application. La liaison de port mappe le port que le service écoute l’intérieur du conteneur vers un port sur l’hôte.


    <ServiceManifestImport>
        <ServiceManifestRef ServiceManifestName="FrontendServicePackage" ServiceManifestVersion="1.0"/>
        <Policies>
            <ContainerHostPolicies CodePackageRef="FrontendService.Code">
                <PortBinding ContainerPort="8905"/>
            </ContainerHostPolicies>
        </Policies>
    </ServiceManifestImport>


## <a name="container-to-container-discovery-and-communication"></a>Découverte de conteneur à l’autre et communication
À l’aide de la `PortBinding` stratégie vous pouvez mapper un port conteneur vers un `Endpoint` dans le manifeste de service, comme le montre l’exemple suivant. Le point de terminaison `Endpoint1` pouvez spécifier un port fixe, par exemple le port 80 ou ne spécifier aucun port du tout, auquel cas un port aléatoire à partir de la plage de ports des groupes application est choisi pour vous.

Des conteneurs invité, spécifiant une `Endpoint` comme ainsi dans le manifeste de service Service TISSU publier automatiquement ce point de terminaison du service d’appellation afin que d’autres services qui s’exécutent dans le cluster de découvrir ce conteneur à l’aide de requêtes reste service résoudre. 

    <ServiceManifestImport>
        <ServiceManifestRef ServiceManifestName="FrontendServicePackage" ServiceManifestVersion="1.0"/>
        <Policies>
            <ContainerHostPolicies CodePackageRef="FrontendService.Code">
                <PortBinding ContainerPort="8905" EndpointRef="Endpoint1"/>
            </ContainerHostPolicies>
        </Policies>
    </ServiceManifestImport>

En enregistrant avec le service d’attribution de noms, vous pouvez faire aisément communication conteneur à l’autre dans le code dans le conteneur de votre utilisation du serveur [proxy inverse](service-fabric-reverseproxy.md). Il vous souhaitez est fournir le port d’écoute http proxy inverse et le nom des services que vous souhaitez communiquer avec en définissant les variables d’environnement. Sur la façon de procéder, voir la section suivante.  

## <a name="configure-and-set-environment-variables"></a>Configurer et définir des variables d’environnement
Variables d’environnement peuvent être spécifié ennemi chaque module de code dans le service manifeste pour les deux services déployés dans des conteneurs ou en tant qu’invité/processus exécutables. Ces valeurs de variable d’environnement peuvent être substituées spécifiquement dans le manifeste d’application ou spécifiés pendant le déploiement en tant que paramètres de l’application.

Le manifeste de service extrait de code XML suivant montre un exemple de comment spécifier des variables d’environnement pour un package de code. 

    <ServiceManifest Name="FrontendServicePackage" Version="1.0" xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
        <Description>a guest executable service in a container</Description>
        <ServiceTypes>
            <StatelessServiceType ServiceTypeName="StatelessFrontendService"  UseImplicitHost="true"/>
        </ServiceTypes>
        <CodePackage Name="FrontendService.Code" Version="1.0">
            <EntryPoint>
            <ContainerHost>
                <ImageName>myrepo/myimage:v1</ImageName>
                <Commands></Commands>
            </ContainerHost>
            </EntryPoint>
            <EnvironmentVariables>
                <EnvironmentVariable Name="HttpGatewayPort" Value=""/>
                <EnvironmentVariable Name="BackendServiceName" Value=""/>
            </EnvironmentVariables>
        </CodePackage>
    </ServiceManifest>

Ces variables d’environnement peuvent être remplacés au niveau de manifeste de l’application :

    <ServiceManifestImport>
        <ServiceManifestRef ServiceManifestName="FrontendServicePackage" ServiceManifestVersion="1.0"/>
        <EnvironmentOverrides CodePackageRef="FrontendService.Code">
            <EnvironmentVariable Name="BackendServiceName" Value="[BackendSvc]"/>
            <EnvironmentVariable Name="HttpGatewayPort" Value="19080"/>
        </EnvironmentOverrides>
    </ServiceManifestImport>

Dans l’exemple ci-dessus, nous avons spécifié une valeur explicite pour le `HttpGateway` variable d’environnement (19000) lors de la valeur de `BackendServiceName` paramètre est défini la `[BackendSvc]` paramètre de l’application. Cela vous permet de spécifier la valeur de `BackendServiceName`valeur au moment de déploiement de l’application et n’est une valeur fixe dans le manifeste. 

## <a name="complete-examples-for-application-and-service-manifest"></a>Exemples d’application et manifeste de service complets
Voici un exemple de manifeste d’application qui présente les fonctionnalités de conteneur.


    <ApplicationManifest ApplicationTypeName="SimpleContainerApp" ApplicationTypeVersion="1.0" xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
        <Description>A simple service container application</Description>
        <Parameters>
            <Parameter Name="ServiceInstanceCount" DefaultValue="3"></Parameter>
            <Parameter Name="BackEndSvcName" DefaultValue="bkend"></Parameter>
        </Parameters>
        <ServiceManifestImport>
            <ServiceManifestRef ServiceManifestName="FrontendServicePackage" ServiceManifestVersion="1.0"/>
            <EnvironmentOverrides CodePackageRef="FrontendService.Code">
                <EnvironmentVariable Name="BackendServiceName" Value="[BackendSvcName]"/>
                <EnvironmentVariable Name="HttpGatewayPort" Value="19080"/>
            </EnvironmentOverrides>
            <Policies>
                <ResourceGovernancePolicy CodePackageRef="Code" CpuShares="500" MemoryInMB="1024" MemorySwapInMB="4084" MemoryReservationInMB="1024" />
                <ContainerHostPolicies CodePackageRef="FrontendService.Code">
                    <RepositoryCredentials AccountName="username" Password="****" PasswordEncrypted="true"/>
                    <PortBinding ContainerPort="8905" EndpointRef="Endpoint1"/>
                </ContainerHostPolicies>
            </Policies>
        </ServiceManifestImport>
    </ApplicationManifest>


Voici un exemple manifeste de service (spécifié dans le manifeste d’application précédente) qui présente les fonctionnalités de conteneur

    <ServiceManifest Name="FrontendServicePackage" Version="1.0" xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
        <Description> A service that implements a stateless frontend in a container</Description>
        <ServiceTypes>
            <StatelessServiceType ServiceTypeName="StatelessFrontendService"  UseImplicitHost="true"/>
        </ServiceTypes>
        <CodePackage Name="FrontendService.Code" Version="1.0">
            <EntryPoint>
            <ContainerHost>
                <ImageName>myrepo/myimage:v1</ImageName>
                <Commands></Commands>
            </ContainerHost>
            </EntryPoint>
            <EnvironmentVariables>
                <EnvironmentVariable Name="HttpGatewayPort" Value=""/>
                <EnvironmentVariable Name="BackendServiceName" Value=""/>
            </EnvironmentVariables>
        </CodePackage>
        <ConfigPackage Name="FrontendService.Config" Version="1.0" />
        <DataPackage Name="FrontendService.Data" Version="1.0" />
        <Resources>
            <Eendpoints>
                <Endpoint Name="Endpoint1" Port="80"  UriScheme="http" />
            </Eendpoints>
        </Resources>
    </ServiceManifest>

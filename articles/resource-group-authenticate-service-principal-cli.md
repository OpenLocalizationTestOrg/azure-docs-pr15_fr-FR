<properties
   pageTitle="Créez le service principal avec Azure infrastructure du langage commun | Microsoft Azure"
   description="Décrit comment utiliser Azure infrastructure du langage commun pour créer une application Active Directory et principal du service et lui accorder l’accès aux ressources via un contrôle d’accès basé sur un rôle. Il indique comment authentifier application avec un mot de passe ou un certificat."
   services="azure-resource-manager"
   documentationCenter="na"
   authors="tfitzmac"
   manager="timlt"
   editor="tysonn"/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="multiple"
   ms.workload="na"
   ms.date="09/30/2016"
   ms.author="tomfitz"/>

# <a name="use-azure-cli-to-create-a-service-principal-to-access-resources"></a>Azure infrastructure du langage commun permet de créer une entité de service pour accéder aux ressources

> [AZURE.SELECTOR]
- [PowerShell](resource-group-authenticate-service-principal.md)
- [Azure infrastructure du langage commun](resource-group-authenticate-service-principal-cli.md)
- [Portail](resource-group-create-service-principal-portal.md)


Lorsque vous avez une application ou un script qui doit accéder aux ressources, il est probablement ne souhaitez pas exécuter ce processus sous vos propres informations d’identification. Vous devrez différentes autorisations que vous voulez pour l’application, et vous ne souhaitez pas l’application de continuer à utiliser vos informations d’identification si vos responsabilités modifier. À la place, vous créez une identité de l’application qui inclut des informations d’authentification et les attributions de rôle. Chaque fois que l’application est exécutée, il authentifie avec ces informations d’identification. Cette rubrique vous montre comment utiliser [Azure infrastructure du langage commun pour Mac, Linux et Windows](xplat-cli-install.md) pour configurer une application pour s’exécuter sous sa propre identité et les informations d’identification.

Avec Azure infrastructure du langage commun, vous avez deux possibilités pour l’authentification de l’application AD :

 - mot de passe
 - certificat

Cette rubrique indique comment utiliser les deux options dans Azure infrastructure du langage commun. Si vous souhaitez connecter à Azure depuis une infrastructure de programmation (telle Python, Ruby ou Node.js), l’authentification par mot de passe peut-être être la meilleure option. Avant de décider d’utiliser un mot de passe ou un certificat, consultez la section [exemples d’applications](#sample-applications) pour obtenir des exemples d’authentification dans les cadres différents.

## <a name="active-directory-concepts"></a>Concepts relatifs à Active Directory

Dans cet article, vous créez deux objets - l’application Active Directory (AD) et le service principal. L’application AD est une représentation globale de votre application. Il contient les informations d’identification (un id de l’application et un mot de passe ou certificat). Le service principal est la représentation de votre application dans un Active Directory local. Il contient l’attribution de rôle. Cette rubrique se concentre sur une application unique client dans lequel l’application est destinée à exécuter au sein d’une organisation. Vous utilisez généralement applications unique client pour les applications de métier qui s’exécutent au sein de votre organisation. Dans une application unique client, vous avez une application AD et principal d’un service.

Vous vous demandez peut-être - pourquoi dois-je les deux objets ? Cette approche est plus pertinent pour lorsque vous envisagez d’applications clients multiples. Vous utilisez généralement clients à plusieurs applications pour les applications logicielles en tant que service (SaaS), où votre application est exécutée dans nombreux différents abonnements. Pour les applications clients multiples, vous avez une application AD et plusieurs identités de service (un dans chaque Active Directory qui autorise l’accès à l’application). Pour configurer une application cliente multiples, consultez le [guide du développeur autorisation avec l’API du Gestionnaire de ressources Azure](resource-manager-api-authentication.md).

## <a name="required-permissions"></a>Autorisations requises

Pour effectuer cette rubrique, vous devez disposer des autorisations suffisantes dans votre Azure Active Directory et votre abonnement Azure. En particulier, vous devez être en mesure de créer une application dans l’annuaire Active Directory et affecter l’entité de service à un rôle. 

Dans votre Active Directory, votre compte doit être un administrateur (par exemple, **l’Administrateur Global** ou **Administrateur de l’utilisateur**). Si votre compte est attribué au rôle **d’utilisateur** , vous devez être un administrateur élever vos autorisations.

Dans votre abonnement, votre compte doit avoir `Microsoft.Authorization/*/Write` accès, ce qui est accordé via le rôle de [propriétaire](./active-directory/role-based-access-built-in-roles.md#owner) ou [Administrateur de l’accès utilisateur](./active-directory/role-based-access-built-in-roles.md#user-access-administrator) . Si votre compte est affecté au rôle de **collaborateur** , vous recevez un message d’erreur lorsque vous tentez d’affecter l’entité de service à un rôle. Là encore, votre administrateur d’abonnement doit vous accorder un accès suffisant.

À présent, passez à une section pour l’authentification de [mot de passe](#create-service-principal-with-password) ou [certificat](#create-service-principal-with-certificate) .

## <a name="create-service-principal-with-password"></a>Créez le service principal avec mot de passe

Dans cette section, vous exécutez les étapes pour créer l’application AD avec un mot de passe et affectez le rôle de lecteur au service principal.

Passons en revue ces étapes.

1. Connectez-vous à votre compte.

        azure login

1. Vous avez deux possibilités pour créer l’application AD. Vous pouvez créer l’application AD et le service principal en une étape ou créer séparément. Créez-les en une étape si vous n’avez pas besoin de spécifier une page d’accueil de l’identificateur URI pour votre application. Créer séparément si vous avez besoin définir ces valeurs d’une application web. Les deux options sont affichent dans cette étape.

     - Pour créer le service et application AD principal en une étape, fournissent le nom de l’application et un mot de passe, comme indiqué dans la commande suivante :
     
            azure ad sp create -n exampleapp -p {your-password}     
     
     - Pour créer l’application AD séparément, entrez le nom de l’application, une page d’accueil URI, identificateur URI et un mot de passe, comme indiqué dans la commande suivante :
     
            azure ad app create -n exampleapp --home-page http://www.contoso.org --identifier-uris https://www.contoso.org/example -p <Your_Password>

         La commande précédente renvoie une valeur de l’identificateur. Pour créer une entité de service, fournissent cette valeur en tant que paramètre dans la commande suivante :
     
            azure ad sp create -a <AppId>
     
     Si votre compte n’a pas les [autorisations requises](#required-permissions) dans l’annuaire Active Directory, vous voyez un message d’erreur indiquant « Authentication_Unauthorized » ou « aucun abonnement ne trouvé dans le contexte ».
    
     Pour les deux options, la nouvelle entité de service est renvoyée. L' **Id de l’objet** est nécessaire lors de l’octroi d’autorisations. Le guid répertorié avec les **Noms principaux de Service** est nécessaire lors de la connexion. Ce guid est la même valeur que l’id de l’application. Dans les exemples d’applications, cette valeur correspond à l' **ID de Client**. 
    
        info:    Executing command ad sp create
        + Creating application exampleapp
        / Creating service principal for application 7132aca4-1bdb-4238-ad81-996ff91d8db+
        data:    Object Id:               ff863613-e5e2-4a6b-af07-fff6f2de3f4e
        data:    Display Name:            exampleapp
        data:    Service Principal Names:
        data:                             7132aca4-1bdb-4238-ad81-996ff91d8db4
        data:                             https://www.contoso.org/example
        info:    ad sp create command OK

2. Accorder le service principal de votre abonnement. Dans cet exemple, vous ajoutez l’entité de service au rôle de **lecteur** , qui accorde l’autorisation de lire toutes les ressources de l’abonnement. Pour les autres rôles, voir [RBAC : rôles intégrés](./active-directory/role-based-access-built-in-roles.md). Pour le paramètre **nom principal de service** , fournissent **ObjectId** que vous avez utilisé lors de la création de l’application. 

        azure role assignment create --objectId ff863613-e5e2-4a6b-af07-fff6f2de3f4e -o Reader -c /subscriptions/{subscriptionId}/

     Si votre compte n’a pas les autorisations suffisantes pour attribuer un rôle, vous recevez un message d’erreur. Le message indique votre compte **n’a pas d’autorisation pour effectuer l’action « Microsoft.Authorization/roleAssignments/write' sur étendue « / abonnements / {guid} »**. 

Voilà ! Votre application AD et principal de service sont configurés. La section suivante vous montre comment ouvrir une session avec les informations d’identification via Azure infrastructure du langage commun. Si vous voulez utiliser les informations d’identification dans votre application de code, vous n’avez pas besoin continuer à cette rubrique. Vous pouvez atteindre les [exemples d’applications](#sample-applications) pour obtenir des exemples de connecter avec votre id de l’application et le mot de passe. 

### <a name="provide-credentials-through-azure-cli"></a>Fournir des informations d’identification via Azure infrastructure du langage commun

À présent, vous devez ouvrir une session en tant que l’application pour effectuer des opérations.

1. Chaque fois que vous êtes connecté en tant qu’une entité de service, vous devez fournir l’id de client de l’annuaire de votre application AD. Un client est une instance d’Active Directory. Pour récupérer l’id de client pour votre abonnement actuellement authentifié, utilisez :

        azure account show

     Qui retourne :

        info:    Executing command account show
        data:    Name                        : Windows Azure MSDN - Visual Studio Ultimate
        data:    ID                          : {guid}
        data:    State                       : Enabled
        data:    Tenant ID                   : {guid}
        data:    Is Default                  : true
        ...

     Si vous avez besoin obtenir l’id client d’un autre abonnement, utilisez la commande suivante :

        azure account show -s {subscription-id}

2. Si vous avez besoin récupérer l’id de client à utiliser pour la connexion, utilisez :

        azure ad sp show -c exampleapp --json

     La valeur à utiliser pour la connexion est le guid répertorié dans les noms principaux de service.

        [
          {
            "objectId": "ff863613-e5e2-4a6b-af07-fff6f2de3f4e",
            "objectType": "ServicePrincipal",
            "displayName": "exampleapp",
            "appId": "7132aca4-1bdb-4238-ad81-996ff91d8db4",
            "servicePrincipalNames": [
              "https://www.contoso.org/example",
              "7132aca4-1bdb-4238-ad81-996ff91d8db4"
            ]
          }
        ]

3. Connectez-vous en tant que l’entité de service.

        azure login -u 7132aca4-1bdb-4238-ad81-996ff91d8db4 --service-principal --tenant {tenant-id}

    Vous êtes invité au mot de passe. Fournir le mot de passe que vous avez spécifié lors de la création de l’application AD.

        info:    Executing command login
        Password: ********

Vous êtes maintenant authentifié en tant que l’entité de service pour l’entité de service que vous avez créé.

## <a name="create-service-principal-with-certificate"></a>Créez le service principal avec certificat

Dans cette section, vous effectuez les étapes pour :

- créer un certificat auto-signé
- créer l’application AD avec le certificat et l’entité de service
- affecter le rôle de lecteur à l’entité de service

Pour effectuer cette procédure, vous devez disposer [OpenSSL](http://www.openssl.org/) installé.

1. Créer un certificat auto-signé.

        openssl req -x509 -days 3650 -newkey rsa:2048 -out cert.pem -nodes -subj '/CN=exampleapp'

2. Combiner les clés privées et.

        cat privkey.pem cert.pem > examplecert.pem

3. Ouvrez le fichier **examplecert.pem** et recherchez la séquence de caractères entre **situés à début certificat situés à** et **situés à mettre fin à certificat situés à**longue. Copiez les données de certificat. Vous passez ces données en tant que paramètre lors de la création du service principal.

1. Connectez-vous à votre compte.

        azure login

1. Vous avez deux possibilités pour créer l’application AD. Vous pouvez créer l’application AD et le service principal en une étape ou créer séparément. Créez-les en une étape si vous n’avez pas besoin de spécifier une page d’accueil de l’identificateur URI pour votre application. Créer séparément si vous avez besoin définir ces valeurs d’une application web. Les deux options sont affichent dans cette étape.

     - Pour créer le service et application AD principal en une étape, fournissent le nom de l’application et les données de certificat, comme indiqué dans la commande suivante :
     
            azure ad sp create -n exampleapp --cert-value <certificate data>
     
     - Pour créer l’application AD séparément, entrez le nom de l’application, une page d’accueil URI, identificateur URI et les données de certificat, comme indiqué dans la commande suivante :
     
            azure ad app create -n exampleapp --home-page http://www.contoso.org --identifier-uris https://www.contoso.org/example --cert-value <certificate data>

         La commande précédente renvoie une valeur de l’identificateur. Pour créer une entité de service, fournissent cette valeur en tant que paramètre dans la commande suivante :
     
            azure ad sp create -a <AppId>
  
     Si votre compte n’a pas les [autorisations requises](#required-permissions) dans l’annuaire Active Directory, vous voyez un message d’erreur indiquant « Authentication_Unauthorized » ou « aucun abonnement ne trouvé dans le contexte ».
    
     Pour les deux options, la nouvelle entité de service est renvoyée. L’Id d’objet est nécessaire lors de l’octroi d’autorisations. Le guid répertorié avec les **Noms principaux de Service** est nécessaire lors de la connexion. Ce guid est la même valeur que l’id de l’application. Dans les exemples d’applications, cette valeur correspond à l' **ID de Client**. 
    
        info:    Executing command ad sp create
        - Creating service principal for application 4fd39843-c338-417d-b549-a545f584a74+
        data:    Object Id:        7dbc8265-51ed-4038-8e13-31948c7f4ce7
        data:    Display Name:     exampleapp
        data:    Service Principal Names:
        data:                      4fd39843-c338-417d-b549-a545f584a745
        data:                      https://www.contoso.org/example
        info:    ad sp create command OK
        
2. Accorder le service principal de votre abonnement. Dans cet exemple, vous ajoutez l’entité de service au rôle de **lecteur** , qui accorde l’autorisation de lire toutes les ressources de l’abonnement. Pour les autres rôles, voir [RBAC : rôles intégrés](./active-directory/role-based-access-built-in-roles.md). Pour le paramètre **nom principal de service** , fournissent **ObjectId** que vous avez utilisé lors de la création de l’application. 

        azure role assignment create --objectId 7dbc8265-51ed-4038-8e13-31948c7f4ce7 -o Reader -c /subscriptions/{subscriptionId}/

     Si votre compte n’a pas les autorisations suffisantes pour attribuer un rôle, vous recevez un message d’erreur. Le message indique votre compte **n’a pas d’autorisation pour effectuer l’action « Microsoft.Authorization/roleAssignments/write' sur étendue « / abonnements / {guid} »**. 

### <a name="provide-certificate-through-automated-azure-cli-script"></a>Fournir le certificat par le biais script Azure infrastructure du langage commun automatisé

À présent, vous devez ouvrir une session en tant que l’application pour effectuer des opérations.

1. Chaque fois que vous êtes connecté en tant qu’une entité de service, vous devez fournir l’id de client de l’annuaire de votre application AD. Un client est une instance d’Active Directory. Pour récupérer l’id de client pour votre abonnement actuellement authentifié, utilisez :

        azure account show

     Qui retourne :

        info:    Executing command account show
        data:    Name                        : Windows Azure MSDN - Visual Studio Ultimate
        data:    ID                          : {guid}
        data:    State                       : Enabled
        data:    Tenant ID                   : {guid}
        data:    Is Default                  : true
        ...

     Si vous avez besoin obtenir l’id client d’un autre abonnement, utilisez la commande suivante :

        azure account show -s {subscription-id}

1. Pour récupérer l’empreinte numérique du certificat et supprimer les caractères inutiles, utilisez :

        openssl x509 -in "C:\certificates\examplecert.pem" -fingerprint -noout | sed 's/SHA1 Fingerprint=//g'  | sed 's/://g'
    
     Qui renvoie une valeur de l’empreinte numérique semblable à :

        30996D9CE48A0B6E0CD49DBB9A48059BF9355851

2. Si vous avez besoin récupérer l’id de client à utiliser pour la connexion, utilisez :

        azure ad sp show -c exampleapp

     La valeur à utiliser pour la connexion est le guid répertorié dans les noms principaux de service.

        [
          {
            "objectId": "7dbc8265-51ed-4038-8e13-31948c7f4ce7",
            "objectType": "ServicePrincipal",
            "displayName": "exampleapp",
            "appId": "4fd39843-c338-417d-b549-a545f584a745",
            "servicePrincipalNames": [
              "https://www.contoso.org/example",
              "4fd39843-c338-417d-b549-a545f584a745"
            ]
          }
        ]

1. Connectez-vous en tant que l’entité de service.

        azure login --service-principal --tenant {tenant-id} -u 4fd39843-c338-417d-b549-a545f584a745 --certificate-file C:\certificates\examplecert.pem --thumbprint {thumbprint}

Vous êtes maintenant authentifié en tant que le service principal pour l’application Active Directory que vous avez créé.

## <a name="sample-applications"></a>Exemples d’applications

Les exemples d’application suivants montrent comment se connecter en tant que l’entité de service.

**.NET**

- [Déployer un SSH activé machine virtuelle avec un modèle avec .NET](https://azure.microsoft.com/documentation/samples/resource-manager-dotnet-template-deployment/)
- [Gérer les ressources Azure et groupes de ressources avec .NET](https://azure.microsoft.com/documentation/samples/resource-manager-dotnet-resources-and-groups/)

**Java**

- [Mise en route des ressources - déployer à l’aide de modèles Gestionnaire de ressources Azure - dans Java](https://azure.microsoft.com/documentation/samples/resources-java-deploy-using-arm-template/)
- [Mise en route des ressources - gérer le groupe de ressources - dans Java](https://azure.microsoft.com/documentation/samples/resources-java-manage-resource-group//)

**Python**

- [Déployer un SSH activé machine virtuelle avec un modèle dans Python](https://azure.microsoft.com/documentation/samples/resource-manager-python-template-deployment/)
- [Gestion des ressources Azure et groupes de ressources avec Python](https://azure.microsoft.com/documentation/samples/resource-manager-python-resources-and-groups/)

**Node.js**

- [Déployer un SSH activé machine virtuelle avec un modèle dans Node.js](https://azure.microsoft.com/documentation/samples/resource-manager-node-template-deployment/)
- [Gérer les ressources et groupes avec Node.js ressources Azure](https://azure.microsoft.com/documentation/samples/resource-manager-node-resources-and-groups/)

**Ruby**

- [Déployer un SSH activé machine virtuelle avec un modèle dans Ruby](https://azure.microsoft.com/documentation/samples/resource-manager-ruby-template-deployment/)
- [Gestion des ressources Azure et groupes de ressources Ruby](https://azure.microsoft.com/documentation/samples/resource-manager-ruby-resources-and-groups/)


## <a name="next-steps"></a>Étapes suivantes
  
- Pour plus d’informations sur l’intégration d’une application dans Azure pour la gestion des ressources, consultez le [guide du développeur autorisation avec l’API du Gestionnaire de ressources Azure](resource-manager-api-authentication.md).
- Pour obtenir plus d’informations sur l’utilisation des certificats et Azure infrastructure du langage commun, voir [l’authentification basée sur le certificat avec Azure des identités de Service à partir de la ligne de commande Linux](http://blogs.msdn.com/b/arsen/archive/2015/09/18/certificate-based-auth-with-azure-service-principals-from-linux-command-line.aspx). 

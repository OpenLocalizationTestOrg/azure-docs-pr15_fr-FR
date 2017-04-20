<properties
    pageTitle="Qu’est-il advenu de mon projet WebApi (Visual Studio Azure Active Directory connecté service) | Microsoft Azure "
    description="Décrit ce qui arrive à votre projet MVC WebApi vous vous connectez à Azure AD à l’aide de Visual Studio"
  services="active-directory"
    documentationCenter=""
    authors="TomArcher"
    manager="douge"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="web"
    ms.tgt_pltfrm="vs-what-happened"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/15/2016"
    ms.author="tarcher"/>

# <a name="what-happened-to-my-webapi-project-visual-studio-azure-active-directory-connected-service"></a>Qu’est-il advenu de mon projet WebApi (Visual Studio Azure Active Directory connecté service)

> [AZURE.SELECTOR]
> - [Prise en main](vs-active-directory-webapi-getting-started.md)
> - [Que s'est-il passé](vs-active-directory-webapi-what-happened.md)

##<a name="references-have-been-added"></a>Références ont été ajoutées

###<a name="nuget-package-references"></a>Références de package NuGet

- `Microsoft.Owin`
- `Microsoft.Owin.Host.SystemWeb`
- `Microsoft.Owin.Security`
- `Microsoft.Owin.Security.ActiveDirectory`
- `Microsoft.Owin.Security.Jwt`
- `Microsoft.Owin.Security.OAuth`
- `Owin`
- `System.IdentityModel.Tokens.Jwt`

###<a name="net-references"></a>Références .NET

- `Microsoft.Owin`
- `Microsoft.Owin.Host.SystemWeb`
- `Microsoft.Owin.Security`
- `Microsoft.Owin.Security.ActiveDirectory`
- `Microsoft.Owin.Security.Jwt`
- `Microsoft.Owin.Security.OAuth`
- `Owin`
- `System.IdentityModel.Tokens.Jwt`

##<a name="code-changes"></a>Modifications du code

###<a name="code-files-were-added-to-your-project"></a>Fichiers de code ont été ajoutés à votre projet

Une classe de démarrage d’authentification, **App_Start/Startup.Auth.cs** a été ajouté à votre projet contenant une logique de démarrage pour l’authentification Azure Active Directory.

###<a name="startup-code-was-added-to-your-project"></a>Code de démarrage a été ajouté à votre projet

Si vous avez déjà une classe de démarrage dans votre projet, la méthode de **Configuration** a été mis à jour pour inclure un appel à `ConfigureAuth(app)`. Dans le cas contraire, classe de démarrage a été ajoutée à votre projet.


###<a name="your-appconfig-or-webconfig-file-has-new-configuration-values"></a>Votre fichier app.config ou web.config inclut les nouvelles valeurs de configuration.

Les entrées de configuration suivantes ont été ajoutées.
```
    `<appSettings>
            <add key="ida:ClientId" value="ClientId from the new Azure AD App" />
            <add key="ida:Tenant" value="Your selected Azure AD Tenant" />
            <add key="ida:Audience" value="The App ID Uri from the wizard" />
    </appSettings>`
```

###<a name="an-azure-ad-app-was-created"></a>Une application AD Azure a été créée

Une Application Azure AD a été créée dans le répertoire que vous avez sélectionné dans l’Assistant.

[En savoir plus sur Azure Active Directory](https://azure.microsoft.com/services/active-directory/)

##<a name="if-i-checked-disable-individual-user-accounts-authentication-what-additional-changes-were-made-to-my-project"></a>Si j’ai vérifié *désactiver l’authentification des comptes d’utilisateurs individuels*, quelles modifications supplémentaires ont été apportées à mon projet ?
Références de package NuGet ont été supprimées et fichiers ont été supprimées et sauvegardés. Selon l’état de votre projet, vous devrez peut-être supprimer des références supplémentaires ou des fichiers manuellement, ou modifier le code selon le cas.

###<a name="nuget-package-references-removed-for-those-present"></a>Références de package NuGet supprimés (pour ceux présents)

- `Microsoft.AspNet.Identity.Core`
- `Microsoft.AspNet.Identity.EntityFramework`
- `Microsoft.AspNet.Identity.Owin`

###<a name="code-files-backed-up-and-removed-for-those-present"></a>Fichiers de code sauvegardé et supprimé (pour ceux présents)

Chacun des fichiers suivants a été sauvegardé et supprimé à partir du projet. Fichiers de sauvegarde sont situés dans un dossier « Sauvegarde » à la racine du répertoire du projet.

- `App_Start\IdentityConfig.cs`
- `Controllers\AccountController.cs`
- `Controllers\ManageController.cs`
- `Models\IdentityModels.cs`
- `Providers\ApplicationOAuthProvider.cs`

###<a name="code-files-backed-up-for-those-present"></a>Fichier de code sauvegardé (pour ceux présents)

Chacun des fichiers suivants ont été sauvegardée avant d’être remplacés. Fichiers de sauvegarde sont situés dans un dossier « Sauvegarde » à la racine du répertoire du projet.

- `Startup.cs`
- `App_Start\Startup.Auth.cs`

##<a name="if-i-checked-read-directory-data-what-additional-changes-were-made-to-my-project"></a>Si j’ai vérifié *lecture répertoire de données*, les modifications supplémentaires ont été apportées à mon projet ?

###<a name="additional-changes-were-made-to-your-appconfig-or-webconfig"></a>Autres modifications ont été apportées à votre app.config ou web.config

Les entrées de configuration supplémentaires suivantes ont été ajoutées.

```
    `<appSettings>
        <add key="ida:Password" value="Your Azure AD App's new password" />
    </appSettings>`
```

###<a name="your-azure-active-directory-app-was-updated"></a>Votre Azure Active Directory l’application a été mis à jour
Votre Azure Active Directory l’application a été mis à jour pour inclure l’autorisation *lecture répertoire de données* et une clé supplémentaire a été créée qui a été ensuite utilisé en tant que *Ida : mot de passe* dans la `web.config` fichier.

[En savoir plus sur Azure Active Directory](https://azure.microsoft.com/services/active-directory/)

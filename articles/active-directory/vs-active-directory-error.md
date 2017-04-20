<properties 
    pageTitle="Erreur lors de la détection de l’authentification" 
    description="L’Assistant de connexion active directory a détecté un type d’authentification incompatible" 
    services="active-directory" 
    documentationCenter="" 
    authors="TomArcher" 
    manager="douge" 
    editor=""/>
  
<tags 
    ms.service="active-directory" 
    ms.workload="web" 
    ms.tgt_pltfrm="vs-getting-started" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="08/15/2016" 
    ms.author="tarcher"/>

# <a name="error-during-authentication-detection"></a>Erreur lors de la détection de l’authentification

Lors de la détection de code authentification précédent, l’Assistant a détecté un type d’authentification incompatible.   

##<a name="what-is-being-checked"></a>Qu’en cours de vérification ?

**Remarque :** Afin de détecter correctement le code d’authentification précédent dans un projet, le projet doit être généré.  Si vous avez rencontré cette erreur et que vous n’avez pas un code d’authentification précédent dans votre projet, reconstruire et réessayez.

###<a name="project-types"></a>Types de projets

L’Assistant vérifie le type de projet vous développez afin qu’il peut injection de la logique d’authentification appropriée dans le projet.  S’il existe un contrôleur qui est dérivée de `ApiController` dans le projet, le projet sera considéré comme un projet WebAPI.  Si uniquement les contrôleur dérivent `MVC.Controller` dans le projet, le projet sera considéré comme un projet MVC.  Rien d’autre n’est pas pris en charge par l’Assistant.  Projets Web Forms ne sont pas actuellement pris en charge.

###<a name="compatible-authentication-code"></a>Code d’authentification compatibles

L’Assistant vérifie également les paramètres d’authentification qui ont été précédemment configurés avec l’Assistant ou sont compatibles avec l’Assistant.  Si tous les paramètres sont présents, il est considéré comme un ré-entrant cas, l’Assistant s’ouvre et affiche les paramètres.  Si seulement certains paramètres sont présents, il est considéré comme un cas d’erreur.

Dans un projet MVC, l’Assistant vérifie que les paramètres suivants, qui résultent précédente utilisation de l’Assistant :

    <add key="ida:ClientId" value="" />
    <add key="ida:Tenant" value="" />
    <add key="ida:AADInstance" value="" />
    <add key="ida:PostLogoutRedirectUri" value="" />

Par ailleurs, l’Assistant vérifie un des paramètres suivants dans un projet d’API Web, qui résultent précédente utilisation de l’Assistant :

    <add key="ida:ClientId" value="" />
    <add key="ida:Tenant" value="" />
    <add key="ida:Audience" value="" />

###<a name="incompatible-authentication-code"></a>Code d’authentification incompatible

Enfin, l’Assistant essaie de détecter les versions de code d’authentification qui ont été configurées avec des versions précédentes de Visual Studio. Si vous avez reçu cette erreur, cela signifie que votre projet contienne un type d’authentification incompatible. L’Assistant détecte les types d’authentification de versions précédentes de Visual Studio suivants :

* Authentification Windows 
* Comptes d’utilisateurs individuels 
* Comptes professionnels 
 

Pour détecter l’authentification Windows dans un projet MVC, l’Assistant recherche le `authentication` élément à partir de votre fichier **web.config** .

<pre>
    &lt;configuration&gt;
        &lt;system.web&gt;
            <span style="background-color: yellow">&lt;mode d’authentification = « Windows » /&gt;</span>
        &lt;/System.Web&gt;
    &lt;configuration&gt;
</pre>

Pour détecter l’authentification Windows dans un projet d’API Web, l’Assistant recherche le `IISExpressWindowsAuthentication` élément à partir de votre fichier de projet **.csproj** :

<pre>
    &lt;Projet&gt;
&lt;PropertyGroup&gt;
            <span style="background-color: yellow">&lt;IISExpressWindowsAuthentication&gt;activé&lt;/IISExpressWindowsAuthentication&gt;</span>
        &lt;/PropertyGroup > &lt;/de projet        &gt;
</pre>

Pour détecter l’authentification des comptes d’utilisateurs individuels, l’Assistant recherche l’élément package à partir de votre fichier **Packages.config** .

<pre>
    &lt;packages&gt;
        <span style="background-color: yellow">&lt;id="Microsoft.AspNet.Identity.EntityFramework du package « version = « 2.1.0 » targetFramework = « net45 » /&gt;</span>
    &lt;/ensembles&gt;
</pre>

Pour détecter un ancien formulaire d’authentification de compte d’organisation, l’Assistant recherche pour l’élément suivant à partir de **web.config**:

<pre>
    &lt;configuration&gt;
        &lt;appSettings&gt;
            <span style="background-color: yellow">&lt;Ajouter clé = valeur de « ida : domaine » = « *** « /&gt;</span>
        &lt;/appSettings&gt;
    &lt;configuration&gt;
</pre>

Pour modifier le type d’authentification, supprimez le type d’authentification incompatibles et réexécutez l’Assistant.

Pour plus d’informations, voir [Scénarios d’authentification pour Azure AD](active-directory-authentication-scenarios.md).
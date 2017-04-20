<properties 
    pageTitle="Commencer avec Azure Active Directory et Visual Studio connecté services (projets MVC) | Microsoft Azure" 
    description="Comment faire pour commencer à utiliser Azure Active Directory dans les projets MVC après que la connexion à ou en créant une annonce Azure à l’aide de Visual Studio services connectés" 
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

# <a name="getting-started-with-azure-active-directory-and-visual-studio-connected-services-mvc-projects"></a>Prise en main Azure Active Directory et Visual Studio connecté services (projets MVC)

> [AZURE.SELECTOR]
> - [Prise en main](vs-active-directory-dotnet-getting-started.md)
> - [Que s'est-il passé](vs-active-directory-dotnet-what-happened.md)
 
##<a name="requiring-authentication-to-access-controllers"></a>Nécessite une authentification contrôleurs d’accès 

Tous les contrôleurs dans votre projet ont été ornés avec l’attribut **Autoriser** . Cet attribut nécessite que l’utilisateur d’être authentifié avant d’accéder à ces contrôleurs. Pour permettre au responsable de l’accès anonyme, supprimez cet attribut à partir du contrôleur. Si vous souhaitez définir les autorisations à un niveau plus précis, appliquez l’attribut à chaque méthode qui nécessite une autorisation au lieu d’appliquer à la classe contrôleur.
 
##<a name="adding-signin--signout-controls"></a>Ajout de connexion / SignOut contrôles 

Pour ajouter un les contrôles de la fenêtre de connexion à/SignOut à votre affichage, vous pouvez utiliser la vue partielle **_LoginPartial.cshtml** pour ajouter la fonctionnalité à un de vos affichages. Voici un exemple de la fonctionnalité ajoutée à l’affichage standard **_Layout.cshtml** . (Notez le dernier élément de la balise div avec barre de navigation de classe réduction) :

<pre>
    &lt;! DOCTYPE html&gt; 
&lt;html&gt; 
&lt;head&gt; 
&lt;meta charset="utf-8" /&gt; 
&lt;meta name="viewport" content="width=device-width, initial-scale=1.0"&gt; 
&lt;title&gt;@ViewBag.Title - My ASP.NET Application&lt;/title&gt; 
@Styles.Render("~/Content/css") @Scripts.Render("~/bundles/modernizr") &lt;/head&gt; 
&lt;body&gt; 
&lt;div class="navbar navbar-inverse navbar-fixed-top"&gt; 
&lt;div class="container"&gt; 
&lt;div class="navbar-header"&gt; 
&lt;button type="button" class="navbar-toggle" data-toggle="collapse" data-target=".navbar-collapse"&gt; 
&lt;span class="icon-bar"&gt;&lt;/span&gt; 
&lt;span class="icon-bar"&gt;&lt;/span&gt; 
&lt;span class="icon-bar"&gt;&lt;/span&gt; 
&lt;/button&gt; 
@Html.ActionLink("Application name", "Index", "Home", new { area = "" }, new { @class = "navbar-brand" }) &lt;/div&gt; 
&lt;div class="navbar-collapse collapse"&gt; 
&lt;ul class="nav navbar-nav"&gt; 
&lt;li&gt;@Html.ActionLink("Home", "Index", "Home")&lt;/li&gt; 
&lt;li&gt;@Html.ActionLink("About", "About", "Home")&lt;/li&gt; 
&lt;li&gt;@Html.ActionLink("Contact", "Contact", "Home")&lt;/li&gt; 
&lt;/ul&gt; 
                    <span style="background-color:yellow">@Html.Partial("_LoginPartial")</span> 
                &lt;/ div&gt; 
&lt;/div&gt; 
&lt;/div&gt; 
&lt;div class="container body-content"&gt; 
@RenderBody() &lt;hr /&gt; 
&lt;footer&gt; 
&lt;p&gt;&amp;copy; @DateTime.Now.Year - My ASP.NET Application&lt;/p&gt; 
&lt;/footer&gt; 
&lt;/div&gt; 
@Scripts.Render("~/bundles/jquery") @Scripts.Render("~/bundles/bootstrap") @RenderSection("scripts", required: false) &lt;/body&gt; 
&lt;/html                                                                                                                                                                                                                                                                                                                                                                                                                                                           &gt;
</pre>

[En savoir plus sur Azure Active Directory](https://azure.microsoft.com/services/active-directory/) 

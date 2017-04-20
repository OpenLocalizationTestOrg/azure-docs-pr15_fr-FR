<properties
 pageTitle="Comment gérer l’expiration du contenu Azure Web applications/Cloud Services, ASP.NET et IIS dans Azure CDN | Microsoft Azure"
 description="Décrit comment gérer l’expiration du contenu de service cloud dans Azure CDN"
 services="cdn"
 documentationCenter=".NET"
 authors="camsoper"
 manager="erikre"
 editor=""/>
<tags
 ms.service="cdn"
 ms.workload="media"
 ms.tgt_pltfrm="na"
 ms.devlang="dotnet"
 ms.topic="article"
 ms.date="09/19/2016"
 ms.author="casoper"/>

# <a name="how-to-manage-expiration-of-azure-web-appscloud-services-aspnet-or-iis-content-in-azure-cdn"></a>Comment gérer l’expiration du contenu Azure Web applications/Cloud Services, ASP.NET ou IIS dans Azure CDN

> [AZURE.SELECTOR]
- [Services de Cloud/applications Web Azure, ASP.NET ou IIS](cdn-manage-expiration-of-cloud-service-content.md)
- [Service de stockage sur Azure blob](cdn-manage-expiration-of-blob-content.md)

Fichiers à partir de n’importe quel serveur web accessible au public d’origine peuvent être mises en cache dans Azure CDN jusqu'à ce que sa durée de vie (TTL) s’écoule.  La durée de vie est déterminée par l' [en-tête de *Contrôle du Cache* ](http://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html#sec14.9) dans la réponse HTTP à partir du serveur d’origine.  Cet article décrit comment définir `Cache-Control` en-têtes pour Azure Web Apps, Azure Cloud Services, les applications et sites Internet Information Services, ce qui sont configuré de la même manière.

>[AZURE.TIP] Vous pouvez choisir de ne définir aucun TTL sur un fichier.  Dans ce cas, Azure CDN applique automatiquement une durée de vie de sept jours par défaut.
>
>Pour plus d’informations sur le fonctionnement de Azure CDN pour accélérer l’accès aux fichiers et d’autres ressources, consultez la [Vue d’ensemble de Azure CDN](./cdn-overview.md).

## <a name="setting-cache-control-headers-in-configuration"></a>Définir des en-têtes de contrôle du Cache de configuration

Pour le contenu statique, tels que des images et des feuilles de style, vous pouvez contrôler la fréquence de mise à jour en modifiant les fichiers **applicationHost.config** ou **web.config** pour votre application web.  L’élément **system.webServer\staticContent\clientCache** dans le fichier de configuration définira la `Cache-Control` en-tête pour votre contenu. Pour **web.config**, les paramètres de configuration affecte tous les éléments dans le dossier et tous ses sous-dossiers, à moins d’avoir remplacé au niveau du sous-dossier.  Par exemple, vous pouvez définir une valeur par défaut time to live à la racine pour que tout le contenu statique mis en cache pour 3 jours, mais ont un sous-dossier qui possède un contenu plus variable avec un facteur de cache de 6 heures.  Pour **applicationHost.config**, toutes les applications sur le site seront affectées, mais peuvent être remplacées dans les fichiers **web.config** dans les applications.

Le XML indique et l’exemple de paramètre **clientCache** pour spécifier une durée de vie maximale de 3 jours suivants :  

```xml
<configuration>
    <system.webServer>
        <staticContent>
            <clientCache cacheControlMode="UseMaxAge" cacheControlMaxAge="3.00:00:00" />
        </staticContent>
    </system.webServer>
</configuration>
```

Spécification **UseMaxAge** ajoute un `Cache-Control: max-age=<nnn>` en-tête à la réponse en fonction de la valeur spécifiée dans l’attribut **CacheControlMaxAge** . Le format de la période définie est pour l’attribut **cacheControlMaxAge** est <days>. <hours>:<min>:<sec>. Pour plus d’informations sur le nœud **clientCache** , voir [Cache Client <clientCache> ](http://www.iis.net/ConfigReference/system.webServer/staticContent/clientCache).  

## <a name="setting-cache-control-headers-in-code"></a>En-têtes de contrôle du Cache de paramètre dans le Code

Pour les applications ASP.NET, vous pouvez définir le comportement de cache par programme en définissant la propriété **propriété HttpResponse.Cache** CDN. Pour plus d’informations sur la propriété **propriété HttpResponse.Cache** , voir [Propriété HttpResponse.Cache propriété](http://msdn.microsoft.com/library/system.web.httpresponse.cache.aspx) et [Classe HttpCachePolicy](http://msdn.microsoft.com/library/system.web.httpcachepolicy.aspx).  

Si vous voulez mettre en cache du contenu d’application dans ASP.NET par programme, vérifiez que le contenu est marqué comme pouvant être mises en définissant HttpCacheability à tout le *monde*. En outre, assurez-vous qu’une validation du cache est définie. La validation du cache peut être une dernière modification horodatage définie par l’appel SetLastModified ou une valeur etag définie par l’appel SetETag. Si vous le souhaitez, vous pouvez également spécifier un délai d’expiration du cache en appelant SetExpires, ou vous pouvez s’appuient sur les heuristiques de cache par défaut décrits précédemment dans ce document.  

Par exemple, pour mettre en cache du contenu pour une heure, ajoutez les éléments suivants :  

```csharp
// Set the caching parameters.
Response.Cache.SetExpires(DateTime.Now.AddHours(1));
Response.Cache.SetCacheability(HttpCacheability.Public);
Response.Cache.SetLastModified(DateTime.Now);
```

## <a name="next-steps"></a>Étapes suivantes

- [En savoir plus d’informations sur l’élément **clientCache**](http://www.iis.net/ConfigReference/system.webServer/staticContent/clientCache)
- [Consultez la documentation de la propriété **propriété HttpResponse.Cache**](http://msdn.microsoft.com/library/system.web.httpresponse.cache.aspx) 
- [Lire la documentation de la **Classe HttpCachePolicy**](http://msdn.microsoft.com/library/system.web.httpcachepolicy.aspx).  

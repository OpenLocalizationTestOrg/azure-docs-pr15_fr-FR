<properties
    pageTitle="Limiter l’accès à votre contenu Azure CDN par pays | Microsoft Azure"
    description="Découvrez comment limiter l’accès à votre contenu Azure CDN en utilisant la fonctionnalité de filtrage Geo."
    services="cdn"
    documentationCenter=""
    authors="camsoper, rli"
    manager="akucer"
    editor=""/>

<tags
    ms.service="cdn"
    ms.workload="tbd"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/14/2016"
    ms.author="Lichard"/>

#<a name="restrict-access-to-your-content-by-country---akamai"></a>Limiter l’accès à votre contenu par pays - Akamai

> [AZURE.SELECTOR]
- [Verizon](cdn-restrict-access-by-country.md)
- [Akamai Standard](cdn-restrict-access-by-country-akamai.md)

##<a name="overview"></a>Vue d’ensemble

Lorsqu’un utilisateur demande votre contenu par défaut, le contenu est pris en charge quelle que soit l’endroit où l’utilisateur fait cette demande à partir de. Dans certains cas, vous souhaiterez limiter l’accès à votre contenu par pays. Cette rubrique explique comment utiliser la fonctionnalité de **Filtrage Geo** afin de configurer le service pour autoriser ou bloquer l’accès par pays.

> [AZURE.IMPORTANT] Les produits Verizon et Akamai fournissent les mêmes fonctionnalités de filtrage geo, mais diffère de l’interface utilisateur. Ce document décrit l’interface de **Azure CDN Standard à partir d’Akamai**. Pour geo-filtrage avec **Azure CDN Standard/Premium auprès de Verizon**, voir [limiter l’accès à votre contenu par pays - Verizon](cdn-restrict-access-by-country.md).

Pour plus d’informations sur les considérations qui s’appliquent à la configuration de ce type de restriction, consultez la section [Considérations](cdn-restrict-access-by-country.md#considerations) à la fin de la rubrique.  

![Filtrage de pays](./media/cdn-filtering/cdn-country-filtering-akamai.png)

##<a name="step-1-define-the-directory-path"></a>Étape 1 : Définir le chemin d’accès

Sélectionnez votre point de terminaison au sein du portail et rechercher de l’onglet Filtrage Geo dans la barre de navigation gauche pour trouver cette fonctionnalité.

Lorsque vous configurez un filtre de pays, vous devez spécifier le chemin d’accès relatif à l’emplacement auquel les utilisateurs seront autorisés ou accès refusés. Vous pouvez appliquer un filtrage geo pour tous vos fichiers avec « / » ou dossiers sélectionnés en spécifiant des chemins d’accès « / images / ». Vous pouvez également appliquer le filtrage geo à un seul fichier en spécifiant le fichier et en excluant la barre oblique « / pictures/city.png ».

Filtre de chemin d’accès du répertoire exemple :

    /                                 
    /Photos/
    /Photos/Strasbourg/
    /Photos/Strasbourg/city.png

##<a name="step-2-define-the-action-block-or-allow"></a>Étape 2 : Définir l’action : bloquer ou autoriser

**Bloc :** Les utilisateurs des pays spécifiés seront être refuser l’accès aux ressources demandées à partir de ce chemin d’accès récursive. Si aucune option de filtrage pays n’ont été configurées pour cet emplacement, puis tous les autres utilisateurs seront autorisés à accéder.

**Autoriser :** Seuls les utilisateurs des pays spécifiés seront autorisés à accéder aux ressources demandées à partir de ce chemin d’accès récursive.

##<a name="step-3-define-the-countries"></a>Étape 3 : Définir les pays

Sélectionnez les pays que vous souhaitez bloquer ou autoriser le chemin d’accès. Pour plus d’informations, voir [Azure CDN Akamai de codes de pays spécifiques](https://msdn.microsoft.com/library/mt761717.aspx).

Par exemple, la règle de blocage /Photos/Strasbourg/filtrer les fichiers, notamment :

    http://<endpoint>.azureedge.net/Photos/Strasbourg/1000.jpg
    http://<endpoint>.azureedge.net/Photos/Strasbourg/Cathedral/1000.jpg


##<a name="country-codes"></a>Codes de pays

La fonctionnalité de **Filtrage Geo** utilise des codes de pays pour définir les pays à partir de laquelle une demande est autorisée ou bloquée pour un répertoire sécurisé. Vous trouverez les codes de pays dans [Azure CDN Akamai de codes de pays spécifiques](https://msdn.microsoft.com/library/mt761717.aspx). 

##<a id="considerations"></a>Considérations relatives à la

- Il peut prendre jusqu'à quelques minutes pour que les modifications apportées à votre pays configuration de filtrage prenne effet.
- Cette fonctionnalité ne prend pas en charge les caractères génériques (par exemple, « * »).
- La configuration de filtrage geo associée le chemin d’accès relatif sera appliquée de manière récursive ce chemin d’accès.
- Une seule règle peut être appliquée à la même chemin d’accès relatif (vous ne pouvez pas créer plusieurs filtres de pays qui pointent vers le même chemin relatif. Toutefois, un dossier peut avoir plusieurs filtres pays. Il s’agit en raison de la nature récursive de filtres de pays. En d’autres termes, un filtre de pays différents peut être affecté à un sous-dossier d’un dossier préconfiguré.


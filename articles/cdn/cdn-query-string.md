<properties
    pageTitle="Contrôle Azure CDN comportement de requêtes avec les chaînes de requête de cache | Microsoft Azure"
    description="Chaîne de requête CDN Azure mise en cache des contrôles comment les fichiers doivent être mis en cache lorsqu’ils contiennent des chaînes de requête."
    services="cdn"
    documentationCenter=""
    authors="camsoper"
    manager="erikre"
    editor=""/>

<tags
    ms.service="cdn"
    ms.workload="tbd"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/28/2016"
    ms.author="casoper"/>

#<a name="controlling-caching-behavior-of-cdn-requests-with-query-strings"></a>Contrôle du comportement de mise en cache de requêtes CDN avec chaînes de requête

> [AZURE.SELECTOR]
- [Standard](cdn-query-string.md)
- [Azure CDN Premium auprès de Verizon](cdn-query-string-premium.md)

##<a name="overview"></a>Vue d’ensemble

Chaîne de requête mise en cache des contrôles comment les fichiers doivent être mis en cache lorsqu’ils contiennent des chaînes de requête.

> [AZURE.IMPORTANT] Les produits Standard et Premium CDN offrent la même chaîne de requête la mise en cache, mais diffère de l’interface utilisateur.  Ce document décrit l’interface de **Azure CDN Standard à partir d’Akamai** et **Azure CDN Standard de Verizon**.  Requête chaîne la mise en cache avec **Azure CDN Premium auprès de Verizon**, voir [comportement de mise en cache de contrôle de requêtes CDN avec les chaînes de requête - Premium](cdn-query-string-premium.md).

Trois modes sont disponibles :

- **Ignorer les chaînes de requête**: il s’agit du mode par défaut.  Le nœud de bord CDN passera la chaîne de requête auprès du demandeur à l’origine dans la première requête et le cache du bien.  Toutes les demandes suivantes pour cette ressource sont pris en charge à partir du nœud bord ignore la chaîne de requête avant l’expiration de l’actif mis en cache.
- **Ignorer la mise en cache de l’URL avec des chaînes de requête**: dans ce mode, demandes de chaînes de requête ne sont pas mises en cache sur le nœud de bord CDN.  Le nœud de bord récupère les biens directement à partir de l’origine et passe au demandeur avec chaque requête.
- **Mettre en cache chaque URL unique**: ce mode traite chaque demande avec une chaîne de requête comme une ressource unique avec son propre cache.  Par exemple, la réponse à l’origine d’une demande de *foo.ashx?q=bar* serait être mis en cache sur le nœud de bord et retournée pour cache ultérieures avec ce même chaîne de requête.  Une demande de *foo.ashx?q=somethingelse* serait mise en cache comme un actif distinct avec sa propre durée de vie.

##<a name="changing-query-string-caching-settings-for-standard-cdn-profiles"></a>Modification des paramètres pour les profils CDN standards de cache de chaîne de requête

1. À partir de la carte de profil CDN, cliquez sur le point de terminaison CDN que vous souhaitez gérer.

    ![Points de terminaison CDN profil carte](./media/cdn-query-string/cdn-endpoints.png)

    La carte de point de terminaison CDN s’ouvre.

2. Cliquez sur le bouton **configurer** .

    ![Bouton gérer de carte de profil CDN](./media/cdn-query-string/cdn-config-btn.png)

    La carte CDN Configuration s’ouvre.

3. Sélectionnez un paramètre dans le menu déroulant **comportement de cache de chaîne de requête** .

    ![Chaîne de requête CDN options de mise en cache](./media/cdn-query-string/cdn-query-string.png)

4. Après avoir effectué votre sélection, cliquez sur le bouton **Enregistrer** .

> [AZURE.IMPORTANT] Les modifications apportées aux paramètres ne soient pas visibles d’emblée, que le temps requis pour l’enregistrement de se propager par le biais du CDN.  Les profils <b>Azure CDN à partir d’Akamai</b> , propagation doivent effectuer généralement en une minute.  Les profils <b>Azure CDN de Verizon</b> , propagation doivent effectuer généralement dans les 90 minutes, mais dans certains cas, peut prendre plus de temps.

<properties
    pageTitle="Contrôle Azure CDN Premium auprès de Verizon comportement de requêtes avec les chaînes de requête de cache | Microsoft Azure"
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

#<a name="controlling-caching-behavior-of-cdn-requests-with-query-strings---premium"></a>Contrôle du comportement de mise en cache de requêtes CDN avec les chaînes de requête - Premium

> [AZURE.SELECTOR]
- [Standard](cdn-query-string.md)
- [Azure CDN Premium auprès de Verizon](cdn-query-string-premium.md)

##<a name="overview"></a>Vue d’ensemble

Chaîne de requête mise en cache des contrôles comment les fichiers doivent être mis en cache lorsqu’ils contiennent des chaînes de requête.

> [AZURE.IMPORTANT] Les produits Standard et Premium CDN offrent la même chaîne de requête la mise en cache, mais diffère de l’interface utilisateur.  Ce document décrit l’interface de **Azure CDN Premium auprès de Verizon**.  Requête chaîne la mise en cache avec **Azure CDN Standard à partir d’Akamai** et **Azure CDN Standard de Verizon**, voir [comportement de mise en cache de contrôle de requêtes CDN avec chaînes de requête](cdn-query-string.md).

Trois modes sont disponibles :

- **cache de standard**: il s’agit du mode par défaut.  Le nœud de bord CDN passera la chaîne de requête auprès du demandeur à l’origine dans la première requête et le cache du bien.  Toutes les demandes suivantes pour cette ressource sont pris en charge à partir du nœud bord ignore la chaîne de requête avant l’expiration de l’actif mis en cache.
- **no-cache**: dans ce mode, demandes de chaînes de requête ne sont pas mises en cache sur le nœud de bord CDN.  Le nœud de bord récupère les biens directement à partir de l’origine et passe au demandeur avec chaque requête.
- **cache unique**: ce mode traite chaque demande avec une chaîne de requête comme une ressource unique avec son propre cache.  Par exemple, la réponse à l’origine d’une demande de *foo.ashx?q=bar* serait être mis en cache sur le nœud de bord et retournée pour cache ultérieures avec ce même chaîne de requête.  Une demande de *foo.ashx?q=somethingelse* serait mise en cache comme un actif distinct avec sa propre durée de vie.

##<a name="changing-query-string-caching-settings-for-premium-cdn-profiles"></a>Modification des paramètres pour les profils CDN premium de cache de chaîne de requête

1. À partir de la carte de profil CDN, cliquez sur le bouton **Gérer** .

    ![Bouton gérer de carte de profil CDN](./media/cdn-query-string-premium/cdn-manage-btn.png)

    Le portail de gestion CDN s’ouvre.

2. Placez le curseur sur l’onglet **Grande HTTP** , puis placez le curseur sur le Lanceur de **Paramètres du Cache** .  Cliquez sur **la mise en cache d’une chaîne de requête**.

    Options de mise en cache de chaîne de requête sont affichent.

    ![Chaîne de requête CDN options de mise en cache](./media/cdn-query-string-premium/cdn-query-string.png)

3. Après avoir effectué votre sélection, cliquez sur le bouton de **mise à jour** .


> [AZURE.IMPORTANT] Les modifications apportées aux paramètres ne soient pas visibles d’emblée, que le temps requis pour l’enregistrement de se propager par le biais du CDN.  Les profils <b>Azure CDN de Verizon</b> , propagation doivent effectuer généralement dans les 90 minutes, mais dans certains cas, peut prendre plus de temps.

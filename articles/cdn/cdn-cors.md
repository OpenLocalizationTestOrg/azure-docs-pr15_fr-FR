<properties
    pageTitle="À l’aide d’Azure CDN avec CORS | Microsoft Azure"
    description="Découvrez comment utiliser Azure contenu remise réseau (CDN) pour le partage avec ressource Cross-Origin (CORS)."
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
    ms.date="09/30/2016"
    ms.author="casoper"/>
    
# <a name="using-azure-cdn-with-cors"></a>À l’aide d’Azure CDN avec CORS     

## <a name="what-is-cors"></a>Quelles sont les CORS ?

CORS (croisées Origin partage des ressources) est une fonctionnalité HTTP qui permet à une application web s’exécutant sur un domaine accéder aux ressources dans un autre domaine. Afin de réduire les risques d’attaques par scripts intersites, tous les navigateurs web modernes implémentent une restriction de sécurité connue sous le [même origine stratégie](http://www.w3.org/Security/wiki/Same_Origin_Policy).  Ainsi, une page web à partir d’appeler des API dans un autre domaine.  CORS fournit un moyen sécurisé pour permettre à un domaine (le domaine d’origine) appeler des API dans un autre domaine.
 
## <a name="how-it-works"></a>Mode de fonctionnement
1.  Le navigateur envoie la demande d’OPTIONS avec un en-tête **Origin** HTTP. La valeur de cet en-tête est le domaine pris en charge la page parent. Lorsqu’une page à partir de https://www.contoso.com tente d’accéder aux données d’un utilisateur dans le domaine fabrikam.com, l’en-tête de la requête suivante est envoyé à fabrikam.com : 
    
    `Origin: https://www.contoso.com`
 
2.  Le serveur peut répondre avec les éléments suivants :
    - Un en-tête **Accès contrôle-autoriser-origine** dans sa réponse indiquant les sites d’origine sont autorisées. Par exemple :
        
        `Access-Control-Allow-Origin: https://www.contoso.com`
        
    - Une page d’erreur si le serveur n’autorise pas la demande d’origine croisée
    - Un en-tête **Accès contrôle-autoriser-origine** avec un caractère générique qui permet à tous les domaines :
        
        `Access-Control-Allow-Origin: *`
 
Pour les requêtes complexes HTTP, il existe une demande « en amont » terminée premier pour déterminer si elle a l’autorisation avant d’envoyer l’ensemble de la requête.
 
## <a name="wildcard-or-single-origin-scenarios"></a>Scénarios d’origine unique ou des caractères génériques

CORS sur Azure CDN fonctionnera automatiquement avec aucune configuration supplémentaire lorsque l’en-tête **d’Accès contrôle-autoriser-origine** est défini sur une origine unique ou de caractères génériques (*).  Le CDN stockera la première réponse et les demandes suivantes utilisent le même en-tête.
 
Si les demandes ont déjà été apportées au CDN avant CORS définie sur l’origine du lot, vous devrez effacer le contenu de votre contenu de point de terminaison pour recharger le contenu avec l’en-tête **d’Accès contrôle-autoriser-origine** .
 
## <a name="multiple-origin-scenarios"></a>Plusieurs scénarios d’origine

Si vous avez besoin permettre à une liste spécifique d’origines pour pouvoir être CORS, les choses deviennent un peu plus compliqués. Le problème se produit lorsque le CDN met en cache l’en-tête **Accès contrôle-autoriser-origine** pour la première origine CORS.  Une origine CORS différente lorsqu’une demande suivante, le CDN est pris en charge l’en-tête **Accès contrôle-autoriser-origine** mis en cache, qui ne correspondre pas.  Il existe plusieurs méthodes pour résoudre ce problème.
 
### <a name="azure-cdn-premium-from-verizon"></a>Azure CDN Premium auprès de Verizon

La meilleure façon d’activer cette option consiste à utiliser **Azure CDN Premium auprès de Verizon**, qui présente certaines fonctionnalités avancées. 
 
Vous devez créer [une règle](cdn-rules-engine.md) vérifier l’en-tête **d’origine** dans la demande.  S’il s’agit d’une origine valide, votre règle définira l’en-tête **d’Accès contrôle-autoriser-origine** avec l’origine fournie dans la demande.  Si l’origine spécifiée dans l’en-tête **d’origine** n’est pas autorisée, votre règle doit omettre l’en-tête **Accès contrôle-autoriser-origine** qui entraînera le navigateur de rejeter la demande. 
 
Il existe deux façons d’effectuer cette opération avec le moteur de règles.  Dans les deux cas, l’en-tête **d’Accès contrôle-autoriser-origine** à partir de serveur d’origine du fichier est ignorée complètement, moteur de règles de CDN complètement gère les origines CORS autorisés.

#### <a name="one-regular-expression-with-all-valid-origins"></a>Une expression régulière avec toutes les origines valides
 
Dans ce cas, vous devez créer une expression régulière qui inclut toutes les origines que vous souhaitez autoriser : 

    https?:\/\/(www\.contoso\.com|contoso\.com|www\.microsoft\.com|microsoft.com\.com)$
 
> [AZURE.TIP] **Azure CDN de Verizon** utilise des [Expressions régulières Compatible Perl](http://pcre.org/) comme son moteur des expressions régulières.  Vous pouvez utiliser un outil comme [101 Expressions régulières](https://regex101.com/) pour valider votre expression régulière.  Notez que le caractère « / » est valide dans les expressions régulières et ne doit pas être d’échappement, cependant, ce caractère d’échappement est considéré comme une meilleure pratique et attendu par certains validateurs regex.

Si l’expression régulière correspond, votre règle remplace l’en-tête **d’Accès contrôle-autoriser-origine** (le cas échéant) à l’origine avec l’origine qui a envoyé la demande.  Vous pouvez également ajouter des en-têtes CORS supplémentaires, tels que **Contrôle-autoriser-méthodes d’accès**.

![Exemple de règles avec les expressions régulières](./media/cdn-cors/cdn-cors-regex.png)
 
#### <a name="request-header-rule-for-each-origin"></a>Règle d’en-tête de demande pour chaque origine.

Plutôt que d’expressions régulières, vous pouvez à la place créer une règle distincte pour chaque origine que vous souhaitez autoriser à l’aide de la **Demande en-tête génériques** [correspondent à condition](https://msdn.microsoft.com/library/mt757336.aspx#Anchor_1). Comme avec la méthode d’expressions régulières, le moteur de règles seul définit les en-têtes CORS. 
  
![Exemple de règles sans expressions régulières](./media/cdn-cors/cdn-cors-no-regex.png)

> [AZURE.TIP] Dans l’exemple ci-dessus, l’utilisation du caractère générique * indique le moteur de règles pour correspondre à la fois HTTP et HTTPS.
 
### <a name="azure-cdn-standard"></a>Azure CDN Standard

Sous profils Azure CDN Standard, le seul mécanisme afin de permettre plusieurs origines sans recourir à l’origine de génériques consiste à utiliser [la mise en cache de chaîne de requête](cdn-query-string.md).  Vous devez activer le paramètre de chaîne de requête pour le point de terminaison CDN, puis utilisez une chaîne de requête unique pour les demandes de chaque domaine autorisé. Cela se traduit par le CDN mise en cache d’un objet distinct pour chaque chaîne de requête unique. Cette approche n’est pas idéale, cependant, car elle entraîne dans plusieurs copies d’un même fichier mis en cache sur le CDN.  


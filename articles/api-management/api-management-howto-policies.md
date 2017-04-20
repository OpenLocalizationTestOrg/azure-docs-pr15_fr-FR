<properties 
    pageTitle="Stratégies de gestion des API Azure | Microsoft Azure" 
    description="Découvrez comment créer, modifier et configurer des stratégies de gestion des API." 
    services="api-management" 
    documentationCenter="" 
    authors="steved0x" 
    manager="erikre" 
    editor=""/>

<tags 
    ms.service="api-management" 
    ms.workload="mobile" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/25/2016" 
    ms.author="sdanie"/>


#<a name="policies-in-azure-api-management"></a>Stratégies de gestion des API Azure

Gestion des API Azure stratégies sont une fonctionnalité puissante du système qui permettent de l’éditeur modifier le comportement de l’API grâce à la configuration. Les stratégies sont un ensemble d’instructions qui sont exécutées dans un ordre séquentiel dans la demande ou de réponse d’une API. Instructions populaires incluent la conversion du format dans le fichier XML en JSON et appelez taux limitation pour limiter la quantité d’appels entrants à partir d’un développeur. Plus grand nombre de stratégies sont disponibles prêts à l’emploi.

Voir la [Référence de la stratégie][] pour une liste complète des instructions de stratégie et leurs paramètres.

Les stratégies sont appliquées à l’intérieur de la passerelle qui se trouve entre le client de l’API et de l’API managée. La passerelle reçoit toutes les demandes et les inchangée à l’API sous-jacente transfère généralement. Toutefois une stratégie pouvez appliquer des modifications à la demande entrant et les réponses sortantes.

Expressions de stratégie peuvent être utilisées en tant que valeurs d’attribut ou valeurs de texte dans un des stratégies de gestion de l’API, sauf indication contraire de la stratégie. Certaines stratégies telles que les stratégies de [flux de contrôle][] et [Définissez variable][] basées sur des expressions de stratégie. Pour plus d’informations, voir [Stratégies avancées][] et [expressions de stratégie][].

## <a name="scopes"> </a>Comment configurer des stratégies
Stratégies peuvent être configurées globalement ou au niveau d’un [produit][], [API][] ou une [opération][]. Pour configurer une stratégie, accédez à l’éditeur de stratégies dans le portail de publisher.

![Menu stratégies][policies-menu]

L’éditeur de stratégies se compose de trois sections principales : l’étendue de la stratégie (haut), la définition de stratégie où vous modifiez les stratégies (à gauche) et les instructions de la liste (à droite) :

![Éditeur de stratégies][policies-editor]

Pour commencer à configurer une stratégie, que vous devez d’abord sélectionner l’étendue à laquelle la stratégie doit être appliqué. Dans la capture d’écran ci-dessous la **Starter** produit est sélectionné. Notez que le symbole carré en regard du nom de la stratégie indique qu’une stratégie est déjà appliquée à ce niveau.

![Étendue][policies-scope]

Dans la mesure où une stratégie a déjà été appliquée, la configuration est affichée dans la vue définition.

![Configurer][policies-configure]

La stratégie est affichée en lecture seule dans un premier temps. Pour modifier la définition, cliquez sur l’action **Configurer la stratégie** .

![Modifier][policies-edit]

La définition de stratégie est un document XML simple qui décrit une séquence d’instructions entrantes et sortantes. Le code XML peut être modifié directement dans la fenêtre Définition. Une liste d’instructions est fournie vers la droite et les instructions applicables à la portée actuelle sont activées et mis en surbrillance ; comme indiqué par l’instruction **Limite appeler taux** dans la capture d’écran ci-dessus.

Cliquer sur une instruction activée ajoute le code XML approprié à l’emplacement du curseur dans l’affichage de la définition. 

>[AZURE.NOTE] Si la stratégie que vous voulez ajouter n’est pas activée, vérifiez que vous êtes dans l’étendue correcte pour cette stratégie. Chaque déclaration de stratégie est conçue pour une utilisation dans certaines zones et des sections de stratégie. Pour passer en revue les sections de stratégie et les étendues pour une stratégie, consultez la section de **l’utilisation** de cette stratégie dans la [Référence de la stratégie][].

Une liste complète des instructions de stratégie et leurs paramètres sont disponibles dans la [Référence de la stratégie][].

Par exemple, pour ajouter une nouvelle instruction pour limiter les demandes entrantes aux adresses IP spécifiées, placez le curseur juste à l’intérieur du contenu de la `inbound` élément XML et cliquez sur l’instruction **restreindre l’appelant adresses IP** .

![Stratégies de restriction][policies-restrict]

Cette action ajoutera un extrait de code XML pour le `inbound` élément qui fournit des conseils sur la configuration de l’instruction.

    <ip-filter action="allow | forbid">
        <address>address</address>
        <address-range from="address" to="address"/>
    </ip-filter>

Pour limiter les demandes entrantes et accepter uniquement ceux d’une adresse IP 1.2.3.4 modifiez le code XML comme suit :

    <ip-filter action="allow">
        <address>1.2.3.4</address>
    </ip-filter>

![Enregistrer][policies-save]

Une fois terminée configurer les instructions pour la stratégie, cliquez sur **Enregistrer** et les modifications seront propagées à la passerelle de gestion des API immédiatement.

##<a name="sections"> </a>Configuration de la stratégie présentation

Une stratégie est une série d’instructions qui s’exécutent dans l’ordre pour une demande et une réponse. La configuration est correctement divisée en `inbound`, `backend`, `outbound`, et `on-error` sections comme indiqué dans la configuration suivante.

    <policies>
      <inbound>
        <!-- statements to be applied to the request go here -->
      </inbound>
      <backend>
        <!-- statements to be applied before the request is forwarded to 
             the backend service go here -->
      </backend>
      <outbound>
        <!-- statements to be applied to the response go here -->
      </outbound>
      <on-error>
        <!-- statements to be applied if there is an error condition go here -->
      </on-error>
    </policies> 

Si une erreur est générée pendant le traitement d’une demande, tout restant les étapes le `inbound`, `backend`, ou `outbound` sections sont ignorées et l’exécution atteint les instructions dans la `on-error` section. En plaçant des instructions de stratégie dans la `on-error` section que vous puissiez consulter l’erreur à l’aide de la `context.LastError` propriété, consulter et personnaliser la réponse d’erreur à l’aide du `set-body` stratégie et configurer que se passe-t-il si une erreur se produit. Il existe des codes d’erreur pour obtenir la procédure intégrée et les erreurs pouvant se produire pendant le traitement d’instructions de stratégie. Pour plus d’informations, voir [Gestion des erreurs dans les stratégies de gestion des API](https://msdn.microsoft.com/library/azure/mt629506.aspx).

Dans la mesure où vous pouvez spécifier des stratégies à différents niveaux (général, produit, api et opération) la configuration fournit une méthode pour vous permettent de spécifier l’ordre dans lequel les instructions de la définition de stratégie être exécutées en ce qui concerne la stratégie parent. 

Les étendues de stratégie sont évaluées dans l’ordre suivant.

1. Étendue globale
2. Portée du produit
3. Étendue de l’API
4. Cadre de l’opération

Les instructions qu’ils contiennent sont évaluées selon la position de la `base` élément, le cas échéant.

Par exemple, si vous avez une stratégie au niveau global et à une stratégie configurée pour une API, puis chaque fois que cette API particulière est utilisée deux stratégies est appliquées. Gestion des API permet pour le classement déterministe des instructions de stratégie combinées via l’élément de base. 

    <policies>
        <inbound>
            <cross-domain />
            <base />
            <find-and-replace from="xyz" to="abc" />
        </inbound>
    </policies>

Dans la définition de stratégie exemple ci-dessus, le `cross-domain` instruction est exécuté avant que toutes les stratégies plus élevés qui à son tour, suivis par les `find-and-replace` stratégie.

Si la même stratégie apparaît deux fois dans la déclaration de stratégie, la plus récemment évaluée stratégie est appliquée. Cela permet de remplacer les stratégies définies en une étendue plus élevée. Pour afficher les stratégies dans la portée actuelle dans l’éditeur de stratégie, cliquez sur **Recalculer la stratégie en cours pour l’étendue sélectionnée**.

Notez ne qu’aucune stratégie parent stratégie globale et à l’aide de la `<base>` élément qu’il contient n’a aucun effet. 

## <a name="next-steps"></a>Étapes suivantes

Consultez suivant vidéo sur les expressions de stratégie.

> [AZURE.VIDEO policy-expressions-in-azure-api-management]

[Référence de la stratégie]: api-management-policy-reference.md
[Produit]: api-management-howto-add-products.md
[API]: api-management-howto-add-products.md#add-apis 
[Opération]: api-management-howto-add-operations.md

[Stratégies avancées]: https://msdn.microsoft.com/library/azure/dn894085.aspx
[Flux de contrôle]: https://msdn.microsoft.com/library/azure/dn894085.aspx#choose
[Définir la variable]: https://msdn.microsoft.com/library/azure/dn894085.aspx#set_variable
[Expressions de stratégie]: https://msdn.microsoft.com/library/azure/dn910913.aspx

[policies-menu]: ./media/api-management-howto-policies/api-management-policies-menu.png
[policies-editor]: ./media/api-management-howto-policies/api-management-policies-editor.png
[policies-scope]: ./media/api-management-howto-policies/api-management-policies-scope.png
[policies-configure]: ./media/api-management-howto-policies/api-management-policies-configure.png
[policies-edit]: ./media/api-management-howto-policies/api-management-policies-edit.png
[policies-restrict]: ./media/api-management-howto-policies/api-management-policies-restrict.png
[policies-save]: ./media/api-management-howto-policies/api-management-policies-save.png

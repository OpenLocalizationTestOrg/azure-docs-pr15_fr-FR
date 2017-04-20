<properties 
    pageTitle="Comment utiliser les propriétés de stratégies de gestion des API Azure" 
    description="Découvrez comment utiliser les propriétés de stratégies de gestion des API Azure." 
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


# <a name="how-to-use-properties-in-azure-api-management-policies"></a>Comment utiliser les propriétés de stratégies de gestion des API Azure

Stratégies de gestion des API sont une fonctionnalité puissante du système qui permettent de l’éditeur modifier le comportement de l’API grâce à la configuration. Les stratégies sont un ensemble d’instructions qui sont exécutées dans un ordre séquentiel dans la demande ou de réponse d’une API. Instructions de stratégie peuvent être créées à l’aide de valeurs texte littéral, des expressions de stratégie et propriétés. 

Chaque instance de service de gestion de l’API dispose d’une collection properties de paires clé/valeur qui s’appliquent à l’instance du service. Ces propriétés peuvent être utilisées pour gérer les valeurs de chaîne constante dans toutes les stratégies et configuration de l’API. Chaque propriété possède les attributs suivants.


| Attribut | Type            | Description                                                                                             |
|-----------|-----------------|---------------------------------------------------------------------------------------------------------|
| Nom      | chaîne          | Le nom de la propriété. Il peut contenir uniquement des lettres, des chiffres, période, tiret et caractères de soulignement. |
| Valeur     | chaîne          | Valeur de la propriété. Il ne peut pas être vide ou comporter que des espaces blancs.                           |
| Code secret    | valeur booléenne         | Détermine si la valeur est un code secret et doit être chiffrée ou non.                                |
| Balises      | tableau de chaînes | Facultatif balises qui lorsque fournies peuvent être utilisées pour filtrer la liste des propriétés.                               |

Propriétés sont configurées dans le portail publisher sous l’onglet **Propriétés** . Dans l’exemple suivant, trois propriétés sont configurées.

![Propriétés][api-management-properties]

Valeurs de propriétés peuvent contenir des chaînes littérales et [expressions de stratégie](https://msdn.microsoft.com/library/azure/dn910913.aspx). Le tableau suivant montre les trois exemples de propriétés précédent ainsi que leurs attributs. La valeur de `ExpressionProperty` est une expression de stratégie qui retourne une chaîne contenant la date et l’heure. La propriété `ContosoHeaderValue` est marqué comme un code secret, afin que sa valeur n’est pas affichée.

| Nom               | Valeur                      | Code secret | Balises    |
|--------------------|----------------------------|--------|---------|
| ContosoHeader      | TrackingId                 | Faux  | Contoso |
| ContosoHeaderValue | ••••••••••••••••••••••     | Vrai   | Contoso |
| ExpressionProperty | @(DateTime.Now.ToString()) | Faux  |         |

## <a name="to-use-a-property"></a>Utiliser une propriété

Pour utiliser une propriété dans une stratégie, placez le nom de la propriété à l’intérieur d’une paire d’accolades comme double `{{ContosoHeader}}`, comme illustré dans l’exemple suivant.

    <set-header name="{{ContosoHeader}}" exists-action="override">
      <value>{{ContosoHeaderValue}}</value>
    </set-header>

Dans cet exemple, `ContosoHeader` est utilisé comme le nom d’un en-tête dans un `set-header` stratégie, et `ContosoHeaderValue` est utilisée comme valeur de cet en-tête. Lorsque cette stratégie est évaluée au cours d’une demande ou une réponse à la passerelle de gestion des API, `{{ContosoHeader}}` et `{{ContosoHeaderValue}}` sont remplacées par leurs valeurs de propriété respectives.

Propriétés peuvent être utilisées comme attribut complète ou valeurs d’élément comme illustré dans l’exemple précédent, mais ils peuvent également être insérés dans ou combinés avec partie d’une expression de texte littéral, comme le montre l’exemple suivant :`<set-header name = "CustomHeader{{ContosoHeader}}" ...>`

Propriétés peuvent également contenir des expressions de stratégie. Dans l’exemple suivant, la `ExpressionProperty` est utilisé.

    <set-header name="CustomHeader" exists-action="override">
        <value>{{ExpressionProperty}}</value>
    </set-header>

Lorsque cette stratégie est évaluée, `{{ExpressionProperty}}` est remplacée par sa valeur : `@(DateTime.Now.ToString())`. Dans la mesure où la valeur est une expression de stratégie, l’expression est évaluée et la stratégie continue de son exécution.

Vous pouvez tester cette absence dans le portail de développement en appelant une opération qui comporte une stratégie des propriétés dans l’étendue. Dans l’exemple suivant, une opération est appelée avec l’exemple précédent deux `set-header` stratégies avec les propriétés. Notez que la réponse contienne deux en-têtes personnalisés qui ont été configurés à l’aide de stratégies avec les propriétés.

![Portail pour les développeurs][api-management-send-results]

Si vous examinez la [trace inspecteur d’API](api-management-howto-api-inspector.md) pour un appel incluant des stratégies de l’exemple précédent deux avec les propriétés, vous pouvez voir deux `set-header` stratégies avec les valeurs de propriété insérées ainsi que l’évaluation d’une expression stratégie pour la propriété contenant l’expression de stratégie.

![Suivi des API inspecteur][api-management-api-inspector-trace]

Notez que tandis que les valeurs de propriété peuvent contenir des expressions de stratégie, les valeurs de propriété ne peut pas contenir d’autres propriétés. Si le texte qui contient une référence de propriété est utilisé pour une valeur de propriété, tel que `Property value text {{MyProperty}}`, cette référence de la propriété ne remplacée et sera incluse dans le cadre de la valeur de propriété.

## <a name="to-create-a-property"></a>Pour créer une propriété

Pour créer une propriété, cliquez sur **Ajouter une propriété** sous l’onglet **Propriétés** .

![Ajouter des propriétés][api-management-properties-add-property-menu]

**Nom** et la **valeur** sont des valeurs requises. Si cette valeur de propriété est un code secret, cochez la case à cocher **il s’agit d’un code secret** . Entrez une ou plusieurs balises facultatifs pour vous aider à organiser vos propriétés, puis cliquez sur **Enregistrer**.

![Ajouter des propriétés][api-management-properties-add-property]

Lorsqu’une nouvelle propriété est enregistrée, la zone de texte de **la propriété de recherche** est remplie avec le nom de la nouvelle propriété et la nouvelle propriété s’affiche. Pour afficher toutes les propriétés, désactivez la zone de texte de **la propriété de recherche** et appuyez sur ENTRÉE.

![Propriétés][api-management-properties-property-saved]

Pour plus d’informations sur la création d’une propriété à l’aide de l’API REST, voir [créer une propriété à l’aide de l’API REST](https://msdn.microsoft.com/library/azure/mt651775.aspx#Put).

## <a name="to-edit-a-property"></a>Pour modifier une propriété

Pour modifier une propriété, cliquez sur **Modifier** en regard de la propriété à modifier.

![Modifier la propriété][api-management-properties-edit]

Apportez les modifications souhaitées, puis cliquez sur **Enregistrer**. Si vous changez le nom de la propriété, les stratégies qui font référence à cette propriété sont automatiquement mises à jour pour utiliser le nouveau nom.

![Modifier la propriété][api-management-properties-edit-property]

Pour plus d’informations sur la modification d’une propriété à l’aide de l’API REST, voir [Modifier une propriété à l’aide de l’API REST](https://msdn.microsoft.com/library/azure/mt651775.aspx#Patch).

## <a name="to-delete-a-property"></a>Pour supprimer une propriété

Pour supprimer une propriété, cliquez sur **Supprimer** en regard de la propriété à supprimer.

![Supprimer la propriété][api-management-properties-delete]

Cliquez sur **Oui, supprimez-la** pour confirmer.

![Confirmer la suppression][api-management-delete-confirm]

>[AZURE.IMPORTANT] Si la propriété est référencée par les stratégies, vous ne pourrez pas correctement supprimez-le jusqu'à ce que vous supprimiez la propriété de toutes les stratégies qui l’utilisent.

Pour plus d’informations sur la suppression d’une propriété à l’aide de l’API REST, voir [Supprimer une propriété à l’aide de l’API REST](https://msdn.microsoft.com/library/azure/mt651775.aspx#Delete).

## <a name="to-search-and-filter-properties"></a>Pour rechercher et filtre de propriétés

L’onglet **Propriétés** inclut des fonctionnalités pour vous aider à gérer vos propriétés de filtrage et de recherche. Pour filtrer la liste des propriétés par nom de la propriété, entrez un terme de recherche dans la zone de texte de **la propriété de recherche** . Pour afficher toutes les propriétés, désactivez la zone de texte de **la propriété de recherche** et appuyez sur ENTRÉE.

![Recherche][api-management-properties-search]

Pour filtrer la liste des propriétés par les valeurs de la balise, entrez une ou plusieurs balises dans la zone de texte **filtre par les balises** . Pour afficher toutes les propriétés, désactivez la zone de texte **Filtrer par des balises** et appuyez sur ENTRÉE.

![Filtre][api-management-properties-filter]

## <a name="next-steps"></a>Étapes suivantes

-   Pour plus d’informations sur l’utilisation des stratégies
    -   [Stratégies de gestion des API](api-management-howto-policies.md)
    -   [Référence de la stratégie](https://msdn.microsoft.com/library/azure/dn894081.aspx)
    -   [Expressions de stratégie](https://msdn.microsoft.com/library/azure/dn910913.aspx)

## <a name="watch-a-video-overview"></a>Regarder une vidéo de présentation

> [AZURE.VIDEO use-properties-in-policies]

[api-management-properties]: ./media/api-management-howto-properties/api-management-properties.png
[api-management-properties-add-property]: ./media/api-management-howto-properties/api-management-properties-add-property.png
[api-management-properties-edit-property]: ./media/api-management-howto-properties/api-management-properties-edit-property.png
[api-management-properties-add-property-menu]: ./media/api-management-howto-properties/api-management-properties-add-property-menu.png
[api-management-properties-property-saved]: ./media/api-management-howto-properties/api-management-properties-property-saved.png
[api-management-properties-delete]: ./media/api-management-howto-properties/api-management-properties-delete.png
[api-management-properties-edit]: ./media/api-management-howto-properties/api-management-properties-edit.png
[api-management-delete-confirm]: ./media/api-management-howto-properties/api-management-delete-confirm.png
[api-management-properties-search]: ./media/api-management-howto-properties/api-management-properties-search.png
[api-management-send-results]: ./media/api-management-howto-properties/api-management-send-results.png
[api-management-properties-filter]: ./media/api-management-howto-properties/api-management-properties-filter.png
[api-management-api-inspector-trace]: ./media/api-management-howto-properties/api-management-api-inspector-trace.png


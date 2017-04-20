<properties 
    pageTitle="Utiliser les fonctionnalités de l’application logique | Microsoft Azure" 
    description="Découvrez comment utiliser les fonctionnalités avancées des applications logique." 
    authors="stepsic-microsoft-com" 
    manager="erikre" 
    editor="" 
    services="logic-apps" 
    documentationCenter=""/>

<tags
    ms.service="logic-apps"
    ms.workload="integration"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="03/28/2016"
    ms.author="stepsic"/> 
    
# <a name="use-logic-apps-features"></a>Utiliser les fonctionnalités des applications de logique

Dans la [rubrique précédente](app-service-logic-create-a-logic-app.md), vous avez créé votre première application logique. Nous allons maintenant vous montrer générer un processus plus complète à l’aide du Lanceur d’applications Services logique. Cette rubrique présente les nouveaux concepts applications logique suivants :

- Logique conditionnelle, qui exécute une action uniquement si une condition est remplie.
- Mode code pour modifier une application logique existant.
- Options pour démarrer un flux de travail.

Avant de terminer cette rubrique, vous devez effectuer la procédure dans [créer une nouvelle application logique](app-service-logic-create-a-logic-app.md). Dans le [portail Azure], accédez à votre application logique et cliquez sur **déclencheurs et les Actions** dans le résumé afin de modifier la définition de l’application logique.

## <a name="reference-material"></a>Informations de référence

Vous pouvez trouver utile les documents suivants :

- [Gestion et runtime API REST](https://msdn.microsoft.com/library/azure/mt643787.aspx) -, notamment comment appeler directement des applications de logique
- [Référence du langage](https://msdn.microsoft.com/library/azure/mt643789.aspx) - une liste complète de toutes les fonctions/expressions prises en charge
- [Types d’actions et déclencheur](https://msdn.microsoft.com/library/azure/mt643939.aspx) : les différents types d’actions et les entrées qu’ils prennent
- [Vue d’ensemble du Service d’application](../app-service/app-service-value-prop-what-is.md) - description du quels sont les composants pour déterminer quand créer une solution

## <a name="adding-conditional-logic"></a>Ajouter une logique conditionnelle

Bien que le flux d’origine fonctionne, il existe certaines zones qui peuvent être améliorés. 


### <a name="conditional"></a>Conditionnelle
Cette application logique peut entraîner vous prise un grand nombre de messages électroniques. Les étapes suivantes ajoutent une logique pour vous assurer qu’uniquement recevoir un message électronique lorsque la tweet provient d’une personne disposant d’un certain nombre de suivent. 

1. Cliquez sur le signe plus et recherchez l’action *Utilisateur obtenir* Twitter.

2. Passer dans le champ **Tweeted en** à partir du déclencheur pour récupérer les informations relatives à l’utilisateur Twitter.

    ![Obtenir l’utilisateur](./media/app-service-logic-use-logic-app-features/getuser.png)

3. Cliquez à nouveau sur le signe plus, mais cette fois, sélectionnez **Ajouter une Condition**

4. Dans la première zone, cliquez sur le **...** sous **Obtenir un utilisateur** pour rechercher le champ **Nb suivent** .

5. Dans la liste déroulante, sélectionnez **supérieur à**

6. Dans la deuxième zone, tapez le nombre de suivent vous voulez que les utilisateurs ont.

    ![Conditionnelle](./media/app-service-logic-use-logic-app-features/conditional.png)

7.  Pour finir, boîte de glisser-déplacer le courrier électronique dans la zone **Si Oui** . Cela signifie que vous obtiendrez uniquement messages électroniques lorsque le nombre de liaisons est remplie.

## <a name="repeating-over-a-list-with-foreach"></a>Extensible sur une liste avec forEach

La boucle forEach spécifie un tableau pour répéter une action sur. Si elle n’est pas un tableau le flux échoue. Par exemple, si vous avez action1 qui renvoie une matrice de messages, et vous souhaitez envoyer chaque message, vous pouvez inclure cette instruction forEach dans les propriétés de votre action : forEach :"@action('action1').outputs.messages"
 

## <a name="using-the-code-view-to-edit-a-logic-app"></a>À l’aide de l’affichage de code pour modifier une application logique

Outre le concepteur, vous pouvez modifier directement le code qui définit une application logique, comme suit. 

1. Cliquez sur le bouton **mode Code** dans la barre de commandes. 

    Cette action ouvre un éditeur complet qui montre la définition que vous venez de modifier.

    ![Mode code](./media/app-service-logic-use-logic-app-features/codeview.png)

    À l’aide de l’éditeur de texte, vous pouvez copier et coller des actions de l’application logique même ou entre les applications logique. Vous pouvez également facilement ajouter ou supprimer des sections entières à partir de la définition, et vous pouvez également partager des définitions avec d’autres personnes.

2. Après avoir apporté vos modifications dans le mode code, cliquez simplement sur **Enregistrer**. 

### <a name="parameters"></a>Paramètres
Il existe certaines fonctionnalités des applications de logique qui peut uniquement être utilisé dans le mode code. Paramètres en est un exemple. Paramètres facilitent la réutilisation de valeurs dans l’ensemble de votre application logique. Par exemple, si vous avez une adresse de messagerie que vous souhaitez utiliser dans plusieurs actions, vous devez le définir en tant que paramètre.

Les éléments suivants met à jour votre application logique existant pour utiliser des paramètres pour le terme de requête.

1. Dans le mode code, recherchez la `parameters : {}` objet et insérer l’objet rubrique suivantes :

        "topic" : {
            "type" : "string",
            "defaultValue" : "MicrosoftAzure"
        }
    
2. Faites défiler jusqu'à la `twitterconnector` action, recherchez la valeur de la requête et remplacez-le par `#@{parameters('topic')}`.
    Vous pouvez également utiliser la fonction **concat** pour joindre deux ou plusieurs chaînes, par exemple : `@concat('#',parameters('topic'))` est identique à ce qui précède. 
 
Les paramètres sont un bon moyen pour extraire les valeurs qui vous serez susceptibles de changer souvent. Ils sont particulièrement utiles lorsque vous avez besoin de remplacer les paramètres dans différents environnements. Pour plus d’informations sur la façon de remplacement des paramètres en fonction de l’environnement, reportez-vous à notre [documentation API REST](https://msdn.microsoft.com/library/mt643787.aspx).

À présent, lorsque vous cliquez sur **Enregistrer**, chaque heure pouvez-vous tout nouveau tweet qui ont plus de 5 retweets remis à un dossier nommé **tweets** dans votre Dropbox.

Pour plus d’informations sur les définitions de l’application logique, voir [créer des définitions de logique d’application](app-service-logic-author-definitions.md).

## <a name="starting-a-logic-app-workflow"></a>Démarrer un flux de travail de l’application logique
Il existe plusieurs options pour démarrer le flux de travail définie dans l’application logique vous. Un flux de travail peut toujours être démarré à la demande dans le [portail Azure].

### <a name="recurrence-triggers"></a>Déclencheurs de périodicité
Un déclencheur périodicité s’exécute à un intervalle que vous spécifiez. Lorsque le déclencheur comportant une logique conditionnelle, le déclencheur détermine si le flux de travail doit exécuter. Un déclencheur indique qu’il doit s’exécuter en renvoyant une `200` code d’état. Lorsqu’elle n’a pas besoin de s’exécuter, elle renvoie un `202` code d’état.

### <a name="callback-using-rest-apis"></a>Rappel à l’aide des API REST
Services peuvent appeler un point de terminaison de l’application logique pour démarrer un flux de travail. Pour plus d’informations, consultez [applications logique comme points de terminaison pouvant être appelé par](app-service-logic-connector-http.md) . Pour démarrer ce type de logique application à la demande, cliquez sur le bouton **Exécuter maintenant** dans la barre de commandes. 

<!-- Shared links -->
[Portail Azure]: https://portal.azure.com 
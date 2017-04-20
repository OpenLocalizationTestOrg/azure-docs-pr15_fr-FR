<properties
    pageTitle="Tableau de bord Power BI sur flux Analytique | Microsoft Azure"
    description="Utiliser un tableau de bord Power BI en continu en temps réel pour collecter à la décision et analyser des données volumineux à partir d’une tâche de flux de données Analytique."
    keywords="tableau de bord analytique, tableau de bord en temps réel"
    services="stream-analytics"
    documentationCenter=""
    authors="jeffstokes72"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="stream-analytics"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="data-services"
    ms.date="09/26/2016"
    ms.author="jeffstok"/>

#  <a name="stream-analytics--power-bi-a-real-time-analytics-dashboard-for-streaming-data"></a>Flux Analytique et Power BI : un tableau de bord analytique en temps réel pour le flux de données

Azure Analytique de flux de données vous permet de tirer parti de l’un des principaux outils d’analyse décisionnelle, Microsoft Power BI. Découvrez comment utiliser Azure flux Analytique pour analyser volumineuses, données de diffusion en continu et obtenir l’aperçu d’un tableau de bord d’analytique Power BI en temps réel.

Utilisez [Microsoft Power BI](https://powerbi.com/) pour créer rapidement un tableau de bord actif. [Regardez une vidéo illustrant le scénario](https://www.youtube.com/watch?v=SGUpT-a99MA).

Dans cet article, découvrez comment créer vos propres outils d’analyse décisionnelle personnalisé à l’aide de Power BI comme résultat pour vos travaux Azure flux Analytique et d’utiliser un tableau de bord en temps réel.

## <a name="prerequisites"></a>Conditions préalables

* Compte Microsoft Azure
* Une entrée pour la tâche de flux Analytique consommer le flux des données à partir de. Flux Analytique accepte l’entrée du stockage Azure événement Hubs ou Blob Azure.  
* Compte professionnel ou scolaire pour Power BI

## <a name="create-azure-stream-analytics-job"></a>Créer des travaux Azure flux Analytique

À partir du [Portail classique Azure](https://manage.windowsazure.com), cliquez sur **nouveau Data Services, flux Analytique, création rapide**.

Spécifier les valeurs suivantes, puis cliquez sur **créer une Analytique de flux de travail**:

* **Nom de la tâche** : entrez un nom de la tâche. Par exemple, **DeviceTemperatures**.
* **Région** - sélectionnez la zone où vous souhaitez le travail situé. Envisagez de placer le travail et le hub de l’événement dans la même région pour des performances optimales et éviter engager de frais de transfert de données entre les régions.
* **Compte de stockage** - cliquez sur le compte de stockage que vous voulez utiliser pour stocker les données d’analyse pour toutes les tâches de flux de données Analytique qui s’exécutent dans cette zone. Vous avez la possibilité de choisir un compte de stockage existant ou créez-en un.

Dans le volet gauche pour répertorier les tâches de flux de données Analytique, cliquez sur **Flux Analytique** .

![graphique1][graphic1]

> [AZURE.TIP] La nouvelle tâche est indiquée avec le statut **Non démarré**. Notez que le bouton **Démarrer** en bas de la page est désactivé. Ce comportement est attendu que vous devez configurer la tâche entrée, la sortie, la requête, et ainsi de suite avant de commencer à la tâche.

## <a name="specify-job-input"></a>Spécifier une entrée de travail

Pour ce didacticiel, nous sommes en supposant que vous utilisez un concentrateur de l’événement comme entrée avec sérialisation JSON et codage UTF-8.

* Cliquez sur le nom du travail.
* Cliquez sur **entrées** à partir du haut de la page, puis cliquez sur **Ajouter une entrée**. La boîte de dialogue qui s’ouvre vous aidera à un certain nombre de procédure pour configurer votre saisie.
*   Sélectionnez le **flux de données**, puis cliquez sur le bouton droit.
*   Sélectionnez **Hub de l’événement**, puis cliquez sur le bouton droit.
*   Tapez ou sélectionnez les valeurs suivantes dans la troisième page :
  * **Alias d’entrée** - Entrez un nom convivial pour cette tâche d’entrée. Notez que vous utiliserez ce nom dans la requête ultérieurement.
  * **Événement concentrateur** - si le Hub de l’événement que vous avez créé se trouve dans le même abonnement en tant que la tâche de flux Analytique, sélectionnez l’espace de noms figurant dans le hub de l’événement.
*   Si votre plateforme d’événement se trouve dans un autre abonnement, sélectionnez **Utiliser événement Hub à partir d’un autre abonnement** et entrer manuellement les informations de **Service Bus Namespace**, **Événement concentrateur nom**, **Nom de la stratégie événement concentrateur**, **Événement concentrateur stratégie clé**et **Le nombre d’événements concentrateur Partition**.

> [AZURE.NOTE]  Cet exemple utilise le numéro par défaut des partitions, qui est 16.

* **Nom de l’événement concentrateur** : sélectionnez le nom du concentrateur Azure événement que vous avez.
* **Nom de la stratégie concentrateur événement** - sélectionnez la stratégie d’événement concentrateur pour le Hub de l’événement que vous utilisez. Assurez-vous que cette stratégie a gérer les autorisations.
*   **Événement concentrateur consommateur groupe** – vous pouvez laissez cette zone vide ou spécifier un groupe de fournisseurs que vous avez sur votre plateforme d’événement. Notez que chaque groupe grand public d’un concentrateur événement peut avoir uniquement 5 lecteurs à la fois. Par conséquent, déterminez le groupe consommateur appropriée pour votre travail en conséquence. Si vous laissez le champ vide, il utilisera le groupe de fournisseurs par défaut.

*   Cliquez sur le bouton droit.
*   Spécifiez les valeurs suivantes :
  * **Format d’événement sérialiseur** - JSON
  * **Codage** - UTF8
*   Cliquez sur le bouton de vérification pour ajouter cette source et vérifier que flux Analytique peut se connecter au Hub de l’événement.

## <a name="add-power-bi-output"></a>Ajouter la sortie de Power BI

1.  Cliquez sur la **sortie** à partir du haut de la page, puis cliquez sur **Ajouter une sortie**. Vous verrez que Power BI répertoriées sous forme d’une option de sortie.

    ![graphique2][graphic2]  

2.  Sélectionnez **Power BI** , puis sur le bouton droit.
3.  Vous verrez un écran semblable à celui-ci :

    ![graphic3][graphic3]  

4.  Dans cette étape, fournir un compte professionnel ou scolaire pour la sortie de tâche de flux Analytique. Si vous avez déjà compte Power BI, sélectionnez **Autoriser maintenant**. Dans le cas contraire, cliquez sur **Inscrivez-vous maintenant**. [Voici un blog bonne étude de détails de Power BI de connexion](http://blogs.technet.com/b/powerbisupport/archive/2015/02/06/power-bi-sign-up-walkthrough.aspx).

    ![graphic11][graphic11]  

5.  Ensuite, vous verrez un écran semblable à celui-ci :

    ![graphic4][graphic4]  

Indiquez les valeurs comme indiqué ci-dessous :

* **Alias de sortie** – vous pouvez placer les alias de sortie facile pour vous y référer. Cet alias de sortie s’avère particulièrement utile si vous décidez d’avoir plusieurs sorties pour votre projet. Dans ce cas, vous devez faire référence à ce résultat dans votre requête. Par exemple, nous allons utiliser la valeur d’alias de sortie = « OutPbi ».
* **Nom du Dataset** : attribuez un nom de jeu de données souhaité pour votre Power BI de sortie d’avoir. Par exemple, nous allons utiliser « pbidemo ».
*   **Nom de la table** : fournir un nom de la table sous le jeu de données de votre sortie Power BI. Imaginons que nous appelons « pbidemo ». Pour l’instant, sortie de Power BI des tâches de flux de données Analytique ne peut avoir une table dans un jeu de données.
*   **Espace de travail** , sélectionnez un espace de travail de votre client Power BI dans lesquelles le jeu de données est créée.

>   [AZURE.NOTE] Vous ne devez pas explicitement créer ce jeu de données et une table dans votre compte de Power BI Ils seront créés automatiquement lorsque vous démarrez votre tâche de flux de données Analytique et la tâche démarre sortie pompage dans Power BI. Si votre requête de travail ne renvoie pas tous les résultats, le jeu de données et la table n’est créées.

*   Cliquez sur **OK**, **Tester la connexion** et à présent vous exportez configuration est terminée.

>   [AZURE.WARNING] Sachez également que si Power BI a déjà un jeu de données et une table portant le même nom que celui que vous avez indiquées dans cette tâche de flux de données Analytique, les données existantes seront remplacées.


## <a name="write-query"></a>Écrire des requêtes

Accédez à l’onglet **requête** de votre travail. Rédigez votre requête, que dont la sortie souhaité dans votre Power BI. Par exemple, cela peut être une opération telle que la requête SQL suivante :

    SELECT
        MAX(hmdt) AS hmdt,
        MAX(temp) AS temp,
        System.TimeStamp AS time,
        dspl
    INTO
        OutPBI
    FROM
        Input TIMESTAMP BY time
    GROUP BY
        TUMBLINGWINDOW(ss,1),
        dspl



Démarrez votre travail. Vérifier que votre plateforme d’événement reçoit des événements et votre requête génère les résultats attendus. Si votre requête renvoie 0 lignes, tables et Power BI dataset pas est automatiquement créées.

## <a name="create-the-dashboard-in-power-bi"></a>Créer le tableau de bord dans Power BI

Accédez à [Powerbi.com](https://powerbi.com) et connectez-vous avec votre compte professionnel ou scolaire. Si la requête de tâche de flux Analytique renvoie des résultats, vous verrez que votre jeu de données est déjà créé :

![graphic5][graphic5]

Pour créer le tableau de bord, accédez à l’option de tableaux de bord et créer un tableau de bord.

![graphic6][graphic6]

Dans cet exemple, nous allons étiquette il « Démonstration du tableau de bord ».

Cliquez sur le jeu de données créée par votre tâche de flux Analytique (pbidemo dans notre exemple). Vous allez être dirigé vers une page pour créer un graphique en haut de ce groupe de données. Vous trouverez ci-dessous, mais un exemple des rapports que vous pouvez créer :

Sélectionnez note temp et ajuster le minutage des champs. Il accède automatiquement à la valeur et de l’axe du graphique :

![graphic7][graphic7]

Grâce à cette, vous obtiendrez automatiquement un graphique comme indiqué ci-dessous :

![graphic8][graphic8]

Dans la section valeur, cliquez sur la liste déroulante vers le bas pour temp et sélectionnez **moyenne** pour la température et sur le graphique, cliquez sur **visualisation** puis **graphique en courbes**:

![graphic9][graphic9]

Vous obtenez un graphique en courbes de moyenne dans le temps.  À l’aide de l’option de code confidentiel comme indiqué ci-dessous, vous pouvez épingler cela à votre tableau de bord que vous avez créé précédemment :

![graphic10][graphic10]

Maintenant lorsque vous affichez le tableau de bord avec ce rapport épinglé, vous verrez mise à jour d’état en temps réel. Essayez de modifier les données dans vos événements – Pique-notes temp ou un élément tel quel, vous pouvez constater l’effet de reflétées dans votre tableau de bord en temps réel.

Notez que ce didacticiel montre comment créer, mais un type de graphique d’un dataset. Power BI peut vous aider à créer d’autres outils d’analyse décisionnelle client pour votre organisation. Pour un autre exemple d’un tableau de bord Power BI, regardez la vidéo [Prise en main Power BI](https://youtu.be/L-Z_6P56aas?t=1m58s) .

Pour plus d’informations sur la configuration d’une sortie de Power BI et d’utiliser des groupes de Power BI, passez en revue la [section Power BI](stream-analytics-define-outputs.md#power-bi) de [que compréhension flux Analytique sorties](stream-analytics-define-outputs.md "compréhension flux Analytique sorties"). Une autre ressource utile pour en savoir plus sur la création de tableaux de bord avec Power BI est [tableaux de bord dans Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-dashboards/).

## <a name="limitations-and-best-practices"></a>Limitations et des pratiques recommandées

Power BI emploie contraintes concurrence et débit comme indiqué ici : [https://powerbi.microsoft.com/pricing](https://powerbi.microsoft.com/pricing "Tarifs de Power BI")

En raison de celles Power BI voit MSN lui-même plus naturellement au cas où Azure flux Analytique n’une réduction de la charge des données importantes.
Nous recommandons d’utiliser TumblingWindow ou HoppingWindow pour vous assurer que push données serait au maximum 1 push/seconde et que votre requête arrive au sein de la configuration requise de débit – vous pouvez utiliser l’équation suivante pour calculer la valeur pour donner à votre fenêtre en secondes :

![equation1](./media/stream-analytics-power-bi-dashboard/equation1.png)  

Par exemple, si vous disposez de 1 000 périphériques envoyer des données par seconde, vous êtes sur le SKU Pro Power BI qui prend en charge de 1 000 000 lignes/heure et vous souhaitez obtenir la moyenne des données par unité dans Power BI possibles au plus un push toutes les 4 secondes par périphérique (comme illustré ci-dessous) :  

![equation2](./media/stream-analytics-power-bi-dashboard/equation2.png)  

Ce qui signifie que nous pouvons modifier la requête d’origine pour :

    SELECT
        MAX(hmdt) AS hmdt,
        MAX(temp) AS temp,
        System.TimeStamp AS time,
        dspl
    INTO
        OutPBI
    FROM
        Input TIMESTAMP BY time
    GROUP BY
        TUMBLINGWINDOW(ss,4),
        dspl

### <a name="powerbi-view-refresh"></a>Actualisation de l’affichage PowerBI

Une question courante est « Pourquoi ne le tableau de bord-mise à jour automatique dans PowerBI ? ».

Pour ce faire, dans PowerBI utiliser Q & A et posez une question à laquelle tels que « Valeur maximale en temp où l’horodatage est aujourd'hui » et l’épingler cette vignette au tableau de bord.

### <a name="renew-authorization"></a>Renouveler l’autorisation

Vous devrez s’authentifier à nouveau votre compte Power BI si son mot de passe a changé depuis votre travail a été créé ou dernier authentifié. Si l’authentification multifacteur (MFA) est configurée sur votre client Azure Active Directory (DAS), vous devez également renouveler autorisation Power BI toutes les deux semaines. Un problème de ce problème est aucune sortie des tâches et une « erreur d’utilisateur d’authentifier » dans les journaux d’opération :

![graphic12][graphic12]

De même, si une tâche tente de démarrer alors que le jeton a expiré, une erreur se produit et le début de la tâche échoue. L’erreur présentera comme ci-dessous :

![Erreur de validation PowerBI](./media/stream-analytics-power-bi-dashboard/stream-analytics-power-bi-dashboard-token-expire.png) 
 

Pour résoudre ce problème, arrêter votre travail en cours d’exécution et accédez à votre sortie Power BI.  Cliquez sur le lien « Renouvellement d’autorisation », puis redémarrez votre travail à partir de la dernière fois arrêté pour éviter toute perte de données.

![Renouvellement de validation PowerBI](./media/stream-analytics-power-bi-dashboard/stream-analytics-power-bi-dashboard-token-renew.png) 

Une fois que l’autorisation est actualisée avec Power BI, vous verrez une alerte verte dans la zone autorisation :

![Renouvellement de validation PowerBI](./media/stream-analytics-power-bi-dashboard/stream-analytics-power-bi-dashboard-token-renewed.png) 

## <a name="get-help"></a>Obtenir de l’aide
Pour obtenir une aide supplémentaire, essayez de notre [forum Azure flux Analytique](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>Étapes suivantes

- [Présentation des flux de données Azure Analytique](stream-analytics-introduction.md)
- [Commencer à utiliser Azure flux Analytique](stream-analytics-get-started.md)
- [Échelle Azure flux Analytique travaux](stream-analytics-scale-jobs.md)
- [Référence du langage flux Azure Analytique requête](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Référence de l’API REST gestion Analytique flux Azure](https://msdn.microsoft.com/library/azure/dn835031.aspx)


[graphic1]: ./media/stream-analytics-power-bi-dashboard/1-stream-analytics-power-bi-dashboard.png
[graphic2]: ./media/stream-analytics-power-bi-dashboard/2-stream-analytics-power-bi-dashboard.png
[graphic3]: ./media/stream-analytics-power-bi-dashboard/3-stream-analytics-power-bi-dashboard.png
[graphic4]: ./media/stream-analytics-power-bi-dashboard/4-stream-analytics-power-bi-dashboard.png
[graphic5]: ./media/stream-analytics-power-bi-dashboard/5-stream-analytics-power-bi-dashboard.png
[graphic6]: ./media/stream-analytics-power-bi-dashboard/6-stream-analytics-power-bi-dashboard.png
[graphic7]: ./media/stream-analytics-power-bi-dashboard/7-stream-analytics-power-bi-dashboard.png
[graphic8]: ./media/stream-analytics-power-bi-dashboard/8-stream-analytics-power-bi-dashboard.png
[graphic9]: ./media/stream-analytics-power-bi-dashboard/9-stream-analytics-power-bi-dashboard.png
[graphic10]: ./media/stream-analytics-power-bi-dashboard/10-stream-analytics-power-bi-dashboard.png
[graphic11]: ./media/stream-analytics-power-bi-dashboard/11-stream-analytics-power-bi-dashboard.png
[graphic12]: ./media/stream-analytics-power-bi-dashboard/12-stream-analytics-power-bi-dashboard.png
[graphic13]: ./media/stream-analytics-power-bi-dashboard/13-stream-analytics-power-bi-dashboard.png

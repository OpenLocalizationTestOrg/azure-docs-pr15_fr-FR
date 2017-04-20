<properties
   pageTitle="Obtenir des informations à partir des données Azure le centre de sécurité avec Power BI | Microsoft Azure"
   description="Le pack de contenu Azure sécurité Centre Power BI facilite la rechercher des alertes de sécurité, recommandations, attaque des ressources et des tendances, basé sur un jeu de données qui a été créé pour vos rapports."
   services="security-center"
   documentationCenter="na"
   authors="YuriDio"
   manager="swadhwa"
   editor=""/>

<tags
   ms.service="security-center"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="09/22/2016"
   ms.author="yurid"/>

# <a name="get-insights-from-azure-security-center-data-with-power-bi"></a>Obtenir des informations à partir des données Azure le centre de sécurité avec Power BI
Le [Tableau de bord Power BI](http://aka.ms/azure-security-center-power-bi) pour le centre de sécurité Azure vous permet de visualiser, leur analyse et filtrer les recommandations et les alertes de sécurité à partir de n’importe où, y compris votre appareil mobile. Utilisez le tableau de bord Power BI pour révéler des tendances et attaque motifs - afficher les alertes de sécurité par ressource ou adresse IP source et les risques unaddressed par ressource ou l’âge. 

Vous pouvez également combiner les recommandations du centre de sécurité et les alertes de sécurité avec d’autres données dans différentes, par exemple à l’aide de données à partir des [Journaux d’Audit Azure](https://powerbi.microsoft.com/blog/monitor-azure-audit-logs-with-power-bi/) et [L’audit la base de données SQL Azure](https://powerbi.microsoft.com/blog/monitor-your-azure-sql-database-auditing-activity-with-power-bi/). Les deux proposent des tableaux de bord Power BI, et vous pouvez également exporter ces données vers Excel pour les rapports facile de l’état de sécurité de vos ressources cloud.

##<a name="using-azure-security-center-dashboard-to-access-power-bi"></a>À l’aide du tableau de bord centre de sécurité Azure à accéder à Power BI
Vous pouvez également utiliser le tableau de bord centre de sécurité Azure pour accéder aux rapports de Power BI. Suivez les étapes pour effectuer cette tâche : 

1. Dans le tableau de bord **Centre de sécurité Azure** , cliquez sur bouton **exploration dans Power BI** .

    ![Vous connecter au centre de sécurité Azure à l’aide de Power BI](./media/security-center-powerbi/security-center-powerbi-fig1-new10.png) 

2. La carte **Explorer dans Power BI** s’ouvre sur le côté droit, comme illustré dans l’écran suivant :

    ![Vous connecter au centre de sécurité Azure à l’aide de Power BI](./media/security-center-powerbi/security-center-powerbi-fig1-new2.png)

3. Si vous créez le tableau de bord Power BI pour la première fois, vous pouvez choisir une des options suivantes dans la carte **Explorer dans Power BI** : 

    - **Tableau de bord de sécurité insights**: sélectionnez cette option si vous souhaitez créer un tableau de bord qui inclut l’état de sécurité, threads et détectés. Cette option est un plus courant pour rôle DevOps qui est responsable de l’analyse de leur état de protection et détecté alertes abonnements.
    - **Tableau de bord de gestion des stratégies**: sélectionnez cette option si vous voulez Explorer stratégie de gestion et d’application.  Cette option est un plus courant pour le service informatique Central qui sont plus axée sur gouvernance. Ils peuvent utiliser ce tableau de bord pour améliorer la visibilité et des idées sur adhésion aux stratégies de sécurité au sein de leur organisation.
    - Si vous disposez déjà d’un tableau de bord Power BI, cliquez sur **Aller à votre tableau de bord Power BI en cours**.

4. Dans cet exemple, cliquez sur l’option **sécurité informations dashboard** . Si c’est la première fois, vous créez un tableau de bord Power BI pour le centre de sécurité vous êtes invité à installer le module de contenu. Cliquez sur **obtenir** un bouton dans la fenêtre **contenus modules à Power BI** comme illustré dans l’écran suivant :

    ![Tableau de bord perspectives sur la sécurité Centre sécurité Azure](./media/security-center-powerbi/security-center-powerbi-fig1-new3.png)

5. La fenêtre de **connexion Azure sécurité Centre de sécurité analyse** s’affichent. Assurez-vous que la méthode **d’authentification** est **oauth2 ne** comme indiqué ci-dessous et cliquez sur le bouton **se connecter** .
    
    ![Authentification](./media/security-center-powerbi/security-center-powerbi-fig1-new4.png)

6. Vous devrez peut-être vous authentifier à nouveau avec vos informations d’identification Azure. Après l’authentification de votre tableau de bord est créé. Une fois que le tableau de bord est créé vous consultez un rapport avec la structure similaire comme indiqué dans l’écran suivant :

    ![Tableau de bord Power BI](./media/security-center-powerbi/security-center-powerbi-fig1-new5.png)


> [AZURE.NOTE] Une actualisation du rapport doit avoir lieu au quotidien. Au cas où vous rencontrez un problème sur cette actualisation, lisez [Les problèmes potentiels actualiser avec le centre de sécurité Azure Power BI](https://blogs.msdn.microsoft.com/azuresecurity/2016/04/07/azure-security-center-power-bi-refresh-fails/), pour plus d’informations sur la façon de résoudre les problèmes.

Ici, vous pouvez voir le nombre d’alertes de sécurité et de recommandations, ainsi que le nombre de machines virtuelles, les bases de données SQL Azure et les ressources réseau contrôlés par le centre de sécurité Azure.

Un lien vers le centre de sécurité Azure vous redirige vers le portail Azure. Les graphiques permettent de visualiser les informations sur les recommandations de sécurité et alertes, y compris :

- État de sécurité de ressources
- Recommandations en attente
- Machine virtuelle recommandations
- Alertes dans le temps
- Ressources attaqués
- Adresses IP attaque

Derrière chaque graphique, il existe des informations supplémentaires. Sélectionnez une vignette pour afficher des informations supplémentaires. Par exemple, la vignette de **l’État de la sécurité ressource** affiche des détails supplémentaires sur en attente de recommandations par des ressources comme indiqué dans l’écran suivant :

![Recommandations](./media/security-center-powerbi/security-center-powerbi-fig1-new6.png)

Si vous cliquez sur n’importe quelle ligne de ce graphique, les autres colonnes allez gris arrière et vous concentrer uniquement sur celui que vous avez sélectionné. Pour revenir au tableau de bord, cliquez sur **Centre de sécurité Azure** sous l’option de **tableaux de bord** dans le volet gauche de cette page.

> [AZURE.NOTE] Si vous voulez personnaliser vos rapports en ajoutant des champs supplémentaires ou en modifiant les effets visuels existants, vous pouvez modifier le rapport. Pour plus d’informations, lisez [interagir avec un rapport en mode édition dans Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-interact-with-a-report-in-editing-view/) .

Les mosaïques **alertes au fil du temps, ressources attaque** et **Adresses IP intrus** aura la sortie similaire lorsque vous cliquez sur chacun des. Cela se produit car le rapport regroupe les informations concernant ces trois variables et appelle **ressources sous attaque** comme indiqué dans l’écran suivant :

![Ressources sous attaque](./media/security-center-powerbi/security-center-powerbi-fig1-new7.png)

À ce stade vous pouvez également enregistrer une copie de ce rapport, l’imprimer ou publier sur le web en utilisant les options disponibles dans le menu **fichier** .

![Menu fichier](./media/security-center-powerbi/security-center-powerbi-fig8.png)

## <a name="exploring-your-azure-security-center-data-with-power-bi-services"></a>Exploration de vos données Azure le centre de sécurité avec les services Power BI

Se connecter aux [Services de Pack contenu Power BI](https://msit.powerbi.com/groups/me/getdata/services) dans Power BI et exécutez les étapes suivantes :

1. Dans la fenêtre **Contenu Pack pour Power BI** , vous verrez deux options comme illustré ci-dessous.

    ![Pack de contenu pour Power BI](./media/security-center-powerbi/security-center-powerbi-fig1-new.png)

    >[AZURE.NOTE] Si déjà exécuté la première partie de cet article, vous verrez qu’une seule option, qui est la gestion des stratégies de centre de sécurité Azure.

2. Pour cet exemple, cliquez sur **obtenir** dans la vignette de **Gestion des stratégies de centre de sécurité Azure** .

3. Dans la fenêtre **se connecter à la gestion des stratégies de centre de sécurité Azure** , veillez à sélectionner **oauth2 ne** sous **Méthode d’authentification** de liste déroulante comme indiqué ci-dessous et cliquez sur le bouton **se connecter** .

    ![Fenêtre de gestion des stratégies](./media/security-center-powerbi/security-center-powerbi-fig1-new8.png)

4. Vous êtes redirigé vers une page d’authentification dans laquelle vous devez taper les informations d’identification que vous utilisez pour vous connecter au centre de sécurité Azure. Une fois le processus d’authentification terminée, Power BI démarre l’importation de données pour créer vos rapports. Pendant ce temps, vous pouvez voir le message suivant sur la droite de votre navigateur :

    ![Vous connecter au centre de sécurité Azure à l’aide de Power BI](./media/security-center-powerbi/security-center-powerbi-fig4.png)

    >[AZURE.NOTE] le tableau de bord est créé pour la première fois peut prendre plu de temps, principalement pour les scénarios où vous disposez de plusieurs abonnements. 

5. Une fois le processus terminé, votre tableau de bord centre Power BI Azure sécurité va charger avec le rapport de **Gestion des stratégies** similaire à celui illustré ci-dessous :

    ![Tableau de bord de gestion des stratégies](./media/security-center-powerbi/security-center-powerbi-fig1-new9.png)

## <a name="see-also"></a>Voir aussi
Dans ce document, vous avez appris comment utiliser Power BI dans le centre de sécurité Azure. Pour en savoir plus sur le centre de sécurité Azure, voir les rubriques suivantes :

- [Guide d’exploitation et de planification de la sécurité Azure centre](security-center-planning-and-operations-guide.md) , apprenez à planifier adoption Azure le centre de sécurité.
- [Définition de stratégies de sécurité dans le centre de sécurité Azure](security-center-policies.md) , découvrez comment configurer les paramètres de sécurité dans le centre de sécurité Azure
- [Gérer et répondre aux alertes de sécurité dans le centre de sécurité Azure](security-center-managing-and-responding-alerts.md) , découvrez comment gérer et répondre aux alertes de sécurité
- [Forum aux questions sur Centre de sécurité Azure](security-center-faq.md) : Forum aux questions sur l’utilisation du service de recherche
- [Blog sur la sécurité Azure](http://blogs.msdn.com/b/azuresecurity/) : rechercher des billets de blog sur la sécurité Azure et conformité

<properties
    pageTitle="Gérer un espace de travail d’apprentissage automatique | Microsoft Azure"
    description="Gérer l’accès aux espaces de travail apprentissage automatique Azure et déployer et gérer les services web API ML"
    services="machine-learning"
    documentationCenter=""
    authors="garyericson"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="machine-learning"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/05/2016"
    ms.author="garye"/>


# <a name="manage-an-azure-machine-learning-workspace"></a>Gérer un espace de travail d’apprentissage automatique Azure

>[AZURE.NOTE] Les procédures décrites dans cet article concernent services Web classique Azure Machine formation. Pour plus d’informations sur la gestion des services Web dans le portail Machine Learning Web Services, voir [gérer un service Web à l’aide du portail Azure Machine Learning Web Services](machine-learning-manage-new-webservice.md).

À l’aide du portail Azure classique, vous pouvez gérer vos espaces de travail d’apprentissage automatique pour :

- Surveiller l’utilisation de l’espace de travail
- Configurer l’espace de travail pour autoriser ou refuser l’accès
- Gérer les services Web créés dans l’espace de travail
- Supprimer l’espace de travail

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

En outre, l’onglet tableau de bord fournit une vue d’ensemble de l’utilisation de votre espace de travail et un coup de œil rapide de vos informations d’espace de travail.  

> [AZURE.TIP] Dans Azure Machine apprentissage Studio, sous l’onglet **SERVICES WEB** , vous pouvez ajouter, mettre à jour ou supprimer un service Web de formation Machine.

Pour gérer un espace de travail :

1.  Connectez-vous au [portail classique Azure](https://manage.windowsazure.com/) à l’aide de votre compte Microsoft Azure : utilisez le compte associé à l’abonnement Azure.
2.  Dans le panneau de services Microsoft Azure, cliquez sur **Apprentissage automatique**.
3.  Cliquez sur l’espace de travail que vous voulez gérer.

La page espace de travail comporte trois onglets :

- **Tableau de bord** - vous permet d’afficher l’utilisation des espace de travail et des informations
- **Configurer** - permet de gérer l’accès à l’espace de travail
- **SERVICES WEB** - vous permet de gérer les services Web qui ont été publiées à partir de cet espace de travail

## <a name="to-monitor-how-the-workspace-is-being-used"></a>Pour contrôler l’utilisation de l’espace de travail

Cliquez sur l’onglet **tableau de bord** .

À partir du tableau de bord, vous pouvez afficher l’utilisation générale de votre espace de travail et obtenir un coup de œil rapide des informations d’espace de travail.

- Le graphique **Calculer** affiche les ressources de cluster utilisés par l’espace de travail. Vous pouvez modifier l’affichage pour afficher relative ou valeurs absolues, et vous pouvez modifier la période affichée dans le graphique.
- **Vue d’ensemble de l’utilisation** affiche Azure stockage utilisé par l’espace de travail.
- **Clin de œil rapide** fournit un récapitulatif des informations de l’espace de travail et des liens utiles.

> [AZURE.NOTE] Le lien **se connecter au Studio ML** ouvre Machine apprentissage Studio Account Microsoft que vous êtes connecté. Le Account Microsoft que vous avez utilisé pour vous connecter au portail classique Azure pour créer un espace de travail n’est pas automatiquement autorisé pour ouvrir cet espace de travail. Pour ouvrir un espace de travail, vous devez être connecté au Account Microsoft qui a été défini comme le propriétaire de l’espace de travail, ou vous devez recevoir une invitation de propriétaire pour participer à l’espace de travail.


## <a name="to-grant-or-suspend-access-for-users"></a>Pour accorder ou suspendre l’accès des utilisateurs ##

Cliquez sur l’onglet **configurer** .

Sous l’onglet configuration, vous pouvez :

- Suspendre l’accès à l’espace de travail d’apprentissage automatique en cliquant sur Refuser. Les utilisateurs pourront n’est plus ouvrir l’espace de travail en Machine apprentissage Studio. Pour rétablir l’accès, cliquez sur Autoriser.

Pour gérer des comptes supplémentaires qui ont accès à l’espace de travail dans Studio d’apprentissage Machine, cliquez sur **se connecter au Studio ML** dans l’onglet **tableau de bord** (voir la remarque précédente concernant les **se connecter au Studio ML**). Cette action ouvre l’espace de travail dans Machine apprentissage Studio. À partir de là, cliquez sur l’onglet **paramètres** , puis **utilisateurs**. Vous pouvez cliquer sur **Inviter d’autres utilisateurs** pour autoriser l’accès à l’espace de travail, ou sélectionnez un utilisateur, cliquez sur **Supprimer**.


## <a name="to-manage-web-services-in-this-workspace"></a>Pour gérer les services web dans cet espace de travail

Cliquez sur l’onglet **SERVICES WEB** .

Cela permet d’afficher une liste des services web publiées à partir de cet espace de travail.
Pour gérer un service web, cliquez sur le nom dans la liste pour ouvrir la page du service Web.

Un service Web peut comporter un ou plusieurs points de terminaison définis.

- Vous pouvez définir plusieurs points de terminaison outre le point de terminaison « Par défaut ». Pour ajouter le point de terminaison, cliquez sur **Gérer les points de terminaison** en bas du tableau de bord pour ouvrir le portail Azure Machine Learning Web Services.

- Pour supprimer un point de terminaison (vous ne pouvez pas supprimer le point de terminaison « Par défaut »), cliquez sur la case à cocher au début de la ligne de point de terminaison, puis cliquez sur **Supprimer**. Cela supprime le point de terminaison du service Web.

    > [AZURE.NOTE] Si une application utilise le point de terminaison du service web lorsque le point de terminaison est supprimé, l’application de recevoir un message d’erreur la prochaine fois qu’il tente d’accéder au service.

Cliquez sur le nom d’un point de terminaison du service Web pour l’ouvrir. 

Dans le tableau de bord, vous pouvez afficher l’utilisation générale de votre service Web sur une période donnée. Vous pouvez sélectionner la période à afficher dans le menu déroulant période dans le coin supérieur droit des graphiques de l’utilisation. Le tableau de bord affiche les informations suivantes :

- **Demandes dans le temps** affiche un graphique étape du nombre de demandes sur une période sélectionnée. Il peut aider à identifier si vous rencontrez pointes de l’utilisation.
- **Demandes de requête-réponse** affiche le nombre total d’appels de requête-réponse que le service a reçu via la période sélectionnée et combien d'entre eux a échoué.
- **Calculer les temps de requête-réponse** affiche une moyenne du temps nécessaire pour exécuter les demandes reçues.
- **Requêtes de lots** affiche le nombre total de requêtes de lots le service a reçu via la période sélectionnée et combien d'entre eux a échoué.
- **Latence moyenne de travail** affiche une moyenne de la durée nécessaire pour exécuter les demandes reçues.
- **Erreurs** affiche le nombre de regroupement des erreurs qui se sont produites sur les appels au service web.
- **Les coûts de services** affiche les frais pour le plan de facturation associé au service.

À partir de la page Configurer, vous pouvez mettre à jour les propriétés suivantes :

* **Description** vous permet d’entrer une description pour le service Web. Description est un champ obligatoire.
* **Journalisation** permet d’activer ou désactiver la journalisation sur le point de terminaison des erreurs. Pour plus d’informations sur la journalisation, voir Activer la [journalisation pour les services web apprentissage automatique](machine-learning-web-services-logging.md).
* **Activer les exemples de données** vous permet de fournir des exemples de données que vous pouvez utiliser pour tester le service de requête-réponse. Si vous avez créé le service web dans Machine apprentissage Studio, les données d’exemple sont considérées à partir des données du votre utilisé pour former votre modèle. Si vous avez créé le service par programme, les données provient les données d’exemple fournies dans le cadre du package JSON.

[consume]: machine-learning-consume-web-services.md
[marketplace]: machine-learning-publish-web-service-to-azure-marketplace.md

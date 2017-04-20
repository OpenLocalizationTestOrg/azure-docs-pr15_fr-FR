<properties
    pageTitle="Gestion d’un service Web à l’aide du portail Azure Machine apprentissage Web Serivces | Microsoft Azure"
    description="Gérer l’accès aux espaces de travail apprentissage automatique Azure et déployer et gérer les services web API ML"
    services="machine-learning"
    documentationCenter=""
    authors="vDonGlover"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="machine-learning"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/04/2016"
    ms.author="v-donglo"/>


# <a name="manage-a-web-service-using-the-azure-machine-learning-web-services-portal"></a>Gestion d’un service Web à l’aide du portail Azure Machine Learning Web Services

Vous pouvez gérer votre nouvelle Machine formation et les services Web classique à l’aide du portail Microsoft Azure Machine Learning Web Services. Dans la mesure où les services Web classique et nouveau site Web services sont basés sur des technologies sous-jacentes différentes, vous devez fonctionnalités de gestion des légèrement différente pour chacun d’eux.

Dans le portail Machine Learning Services Web, vous pouvez :

- Surveiller l’utilisation du service web.
- Configurer la description, mettre à jour les clés pour le service web (New uniquement), mettre à jour votre stockage compte clé (nouveau uniquement), activer la journalisation et activer ou désactiver les exemples de données.
- Supprimer le service web.
- Créer, supprimer ou mettre à jour facturation plans (New uniquement).
- Ajouter et supprimer des points de terminaison (classique uniquement)

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

## <a name="manage-new-web-services"></a>Gérer les services de nouveau site Web

Pour gérer vos services nouveau site Web :

1.  Connectez-vous au portail [Microsoft Azure Machine Learning Services Web](https://services.azureml.net/quickstart) à l’aide de votre compte Microsoft Azure : utilisez le compte associé à l’abonnement Azure.
2.  Dans le menu, cliquez sur **Services Web**.

Cela permet d’afficher une liste des services Web déployés pour votre abonnement. 

Pour gérer un service Web, cliquez sur Services Web. À partir de la page Web Services, vous pouvez :

- Cliquez sur le service web pour gérer.
- Cliquez sur la facturation Plan pour le service web mettre à jour.
- Supprimer un service web.
- Copie d’un service web et le déployez dans une autre région.

Lorsque vous cliquez sur un service web, la page de démarrage rapide de service web s’ouvre. La page de démarrage rapide de service web comporte deux options de menu qui vous permettent de gérer votre service web :

- **Tableau de bord** - vous permet d’afficher l’utilisation du service Web.
- **Configurer** - permet d’ajouter un texte descriptif, mettre à jour la clé pour le compte de stockage associé au service Web et activer ou désactiver les exemples de données.

### <a name="monitoring-how-the-web-service-is-being-used"></a>Utilisation du service web de surveillance ###

Cliquez sur l’onglet **tableau de bord** .

Dans le tableau de bord, vous pouvez afficher l’utilisation générale de votre service Web sur une période donnée. Vous pouvez sélectionner la période à afficher dans le menu déroulant période dans le coin supérieur droit des graphiques de l’utilisation. Le tableau de bord affiche les informations suivantes :

- **Demandes dans le temps** affiche un graphique étape du nombre de demandes sur une période sélectionnée. Il peut aider à identifier si vous rencontrez pointes de l’utilisation.
- **Demandes de requête-réponse** affiche le nombre total d’appels de requête-réponse que le service a reçu via la période sélectionnée et combien d'entre eux a échoué.
- **Calculer les temps de requête-réponse** affiche une moyenne du temps nécessaire pour exécuter les demandes reçues.
- **Requêtes de lots** affiche le nombre total de requêtes de lots le service a reçu via la période sélectionnée et combien d'entre eux a échoué.
- **Latence moyenne de travail** affiche une moyenne de la durée nécessaire pour exécuter les demandes reçues.
- **Erreurs** affiche le nombre de regroupement des erreurs qui se sont produites sur les appels au service web.
- **Les coûts de services** affiche les frais pour le plan de facturation associé au service.

### <a name="configuring-the-web-service"></a>Configuration du service web ###

Cliquez sur l’option de menu **configurer** .

Vous pouvez mettre à jour les propriétés suivantes :

* **Description** vous permet d’entrer une description pour le service Web.
* **Titre** vous permet d’entrer un titre pour le service Web
* **Touches** vous permet de faire pivoter vos clés d’API principales et secondaires.
* **Clé de compte de stockage** vous permet de mettre à jour la clé pour le compte de stockage associé aux modifications de service Web. 
* **Activer les exemples de données** vous permet de fournir des exemples de données que vous pouvez utiliser pour tester le service de requête-réponse. Si vous avez créé le service web dans Machine apprentissage Studio, les données d’exemple sont considérées à partir des données du votre utilisé pour former votre modèle. Si vous avez créé le service par programme, les données provient les données d’exemple fournies dans le cadre du package JSON.

### <a name="managing-billing-plans"></a>Gestion des plans de facturation ###

Cliquez sur l’option de menu **Plans** à partir de la page de démarrage rapide de services web. Vous pouvez également cliquer sur le plan associé au service Web spécifique pour gérer cette offre.

* **Nouveau** permet de créer une nouvelle offre.
* **Plan d’ajout/suppression instance** vous permet « évoluer » un plan existant à ajouter de la capacité.
* **Mise à niveau/déclassement** permet de « évoluer » un plan existant à ajouter de la capacité.
* **Supprimer** permet de supprimer un plan.

Cliquez sur un plan pour afficher son tableau de bord. Le tableau de bord vous donne une capture instantanée ou planifier l’utilisation sur une période donnée. Pour sélectionner la période de temps pour afficher, cliquez sur la liste déroulante **période** dans l’angle supérieur droit du tableau de bord. 

Le tableau de bord offre fournit les informations suivantes :

* **Description du plan** affiche des informations sur les coûts et les capacités associés au plan.
* **Plan d’utilisation** affiche le nombre de transactions et les heures de calcul qui ont été imputées sur le plan.
* **Services Web** affiche le nombre de services Web qui utilisent ce plan.
* **Haut par appels de Service Web** affiche les services Web quatre premières sont effectuer des appels sont imputées sur le plan.
* **Services Web de haut en hr calculer** affiche les services Web quatre premières qui utilisent des ressources cluster sont imputées sur le plan.

## <a name="manage-classic-web-services"></a>Gérer les Services Web classique

> [AZURE.NOTE] Les procédures décrites dans cette section sont relatifs à la gestion des services web classique via le portail Azure Machine Learning Web Services. Pour plus d’informations sur la gestion des services Web classique via le Studio apprentissage Machine et le portail classique Azure, voir [gérer un espace de travail d’apprentissage automatique Azure](machine-learning-manage-workspace.md).

Pour gérer vos services Web classique :

1.  Connectez-vous au portail [Microsoft Azure Machine Learning Services Web](https://services.azureml.net/quickstart) à l’aide de votre compte Microsoft Azure : utilisez le compte associé à l’abonnement Azure.
2.  Dans le menu, cliquez sur **Services Web classique**.

Pour gérer un Service Web classique, cliquez sur **Services Web classique**. À partir de la page Services Web classique, vous pouvez :

- Cliquez sur le service web pour afficher les points de terminaison associés.
- Supprimer un service web.

Lorsque vous gérez un service Web classique, vous gérer chacun des points de terminaison séparément. Lorsque vous cliquez sur un service web dans la page Web Services, la liste des points de terminaison associé au service s’ouvre. 

Dans la page de point de terminaison de Service Web classique, vous pouvez ajouter et supprimer des points de terminaison du service. Pour plus d’informations sur l’ajout de points de terminaison, voir [Création d’une extrémité](machine-learning-create-endpoint.md).

Cliquez sur un points de terminaison pour ouvrir la page de démarrage rapide de service web. Dans la page de démarrage rapide, il existe deux options de menu qui vous permettent de gérer votre service web :

- **Tableau de bord** - vous permet d’afficher l’utilisation du service Web.
- **Configurer** - vous permet d’ajouter un texte descriptif, activer la journalisation des erreurs et désactiver, mise à jour de la clé pour le stockage du compte associé au service Web et activer et désactiver les exemples de données.

### <a name="monitoring-how-the-web-service-is-being-used"></a>Utilisation du service web de surveillance ###

Cliquez sur l’onglet **tableau de bord** .

Dans le tableau de bord, vous pouvez afficher l’utilisation générale de votre service Web sur une période donnée. Vous pouvez sélectionner la période à afficher dans le menu déroulant période dans le coin supérieur droit des graphiques de l’utilisation. Le tableau de bord affiche les informations suivantes :

- **Demandes dans le temps** affiche un graphique étape du nombre de demandes sur une période sélectionnée. Il peut aider à identifier si vous rencontrez pointes de l’utilisation.
- **Demandes de requête-réponse** affiche le nombre total d’appels de requête-réponse que le service a reçu via la période sélectionnée et combien d'entre eux a échoué.
- **Calculer les temps de requête-réponse** affiche une moyenne du temps nécessaire pour exécuter les demandes reçues.
- **Requêtes de lots** affiche le nombre total de requêtes de lots le service a reçu via la période sélectionnée et combien d'entre eux a échoué.
- **Latence moyenne de travail** affiche une moyenne de la durée nécessaire pour exécuter les demandes reçues.
- **Erreurs** affiche le nombre de regroupement des erreurs qui se sont produites sur les appels au service web.
- **Les coûts de services** affiche les frais pour le plan de facturation associé au service.

### <a name="configuring-the-web-service"></a>Configuration du service web ###

Cliquez sur l’option de menu **configurer** .

Vous pouvez mettre à jour les propriétés suivantes :

* **Description** vous permet d’entrer une description pour le service Web. Description est un champ obligatoire.
* **Journalisation** permet d’activer ou désactiver la journalisation sur le point de terminaison des erreurs. Pour plus d’informations sur la journalisation, voir Activer la [journalisation pour les services web apprentissage automatique](machine-learning-web-services-logging.md).
* **Activer les exemples de données** vous permet de fournir des exemples de données que vous pouvez utiliser pour tester le service de requête-réponse. Si vous avez créé le service web dans Machine apprentissage Studio, les données d’exemple sont considérées à partir des données du votre utilisé pour former votre modèle. Si vous avez créé le service par programme, les données provient les données d’exemple fournies dans le cadre du package JSON.

## <a name="grant-or-suspend-access-to-web-services-for-users-in-the-portal"></a>Accorder ou suspendre l’accès aux services Web pour les utilisateurs dans le portail

À l’aide du portail Azure classique, vous pouvez autoriser ou refuser l’accès à des utilisateurs spécifiques.

### <a name="access-for-users-of-new-web-services"></a>Accès aux utilisateurs de services de nouveau site Web

Pour permettre à d’autres utilisateurs de travailler avec vos services Web dans le portail Azure Machine Learning Web Services, vous devez les ajouter en tant qu’aux administrateurs de la co-création dans votre abonnement Azure.

Connectez-vous au [portail classique Azure](https://manage.windowsazure.com/) à l’aide de votre compte Microsoft Azure : utilisez le compte associé à l’abonnement Azure.

1. Dans le volet de navigation, cliquez sur **paramètres**, puis cliquez sur **administrateurs**.
2. Dans la partie inférieure de la fenêtre, cliquez sur **Ajouter**. 
3. Dans la boîte de dialogue ADD A co-création ADMINISTRATOR, tapez l’adresse de messagerie de la personne que vous voulez ajouter en tant qu’administrateur Co-création, puis sélectionnez l’abonnement auquel vous souhaitez que l’administrateur pour accéder à la.
4. Cliquez sur **Enregistrer**.

### <a name="access-for-users-of-classic-web-services"></a>Accès aux utilisateurs de services Web classique

Pour gérer un espace de travail :

Connectez-vous au [portail classique Azure](https://manage.windowsazure.com/) à l’aide de votre compte Microsoft Azure : utilisez le compte associé à l’abonnement Azure.

1. Dans le panneau de services Microsoft Azure, cliquez sur **Apprentissage automatique**.
1. Cliquez sur l’espace de travail que vous voulez gérer.
1. Cliquez sur l’onglet **configurer** .

Sous l’onglet configuration, vous pouvez interrompre l’accès à l’espace de travail d’apprentissage automatique en cliquant sur **Refuser**. Les utilisateurs pourront n’est plus ouvrir l’espace de travail en Machine apprentissage Studio. Pour rétablir l’accès, cliquez sur **Autoriser**.

À des utilisateurs spécifiques :

Pour gérer des comptes supplémentaires qui ont accès à l’espace de travail dans Studio d’apprentissage Machine, cliquez sur **se connecter au Studio ML** dans l’onglet **tableau de bord** . Cette action ouvre l’espace de travail dans Machine apprentissage Studio. À partir de là, cliquez sur l’onglet **paramètres** , puis **utilisateurs**. Vous pouvez cliquer sur **Inviter d’autres utilisateurs** pour autoriser l’accès à l’espace de travail, ou sélectionnez un utilisateur, cliquez sur **Supprimer**.

> [AZURE.NOTE] Le lien **se connecter au Studio ML** ouvre Machine apprentissage Studio Account Microsoft que vous êtes connecté. Le Account Microsoft que vous avez utilisé pour vous connecter au portail classique Azure pour créer un espace de travail n’est pas automatiquement autorisé pour ouvrir cet espace de travail. Pour ouvrir un espace de travail, vous devez être connecté au Account Microsoft qui a été défini comme le propriétaire de l’espace de travail, ou vous devez recevoir une invitation de propriétaire pour participer à l’espace de travail.

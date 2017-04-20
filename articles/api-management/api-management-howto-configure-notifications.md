<properties 
    pageTitle="Comment faire pour configurer les notifications et les modèles de courrier électronique dans la gestion des API Azure" 
    description="Découvrez comment configurer les notifications et modèles dans Azure API de gestion de messagerie." 
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

# <a name="how-to-configure-notifications-and-email-templates-in-azure-api-management"></a>Comment faire pour configurer les notifications et les modèles de courrier électronique dans la gestion des API Azure

Gestion des API offre la possibilité pour configurer les notifications d’événements spécifiques et configurer les modèles de courrier électronique qui sont utilisés pour communiquer avec les administrateurs et les développeurs d’une instance de gestion de l’API. Cette rubrique explique comment configurer les notifications pour les événements disponibles et fournit une vue d’ensemble de la configuration des modèles de courrier électronique utilisés pour ces événements.

## <a name="publisher-notifications"> </a>Configurer les notifications de publisher

Pour configurer les notifications, cliquez sur **Gérer** dans le portail classique Azure pour votre service de gestion de l’API. Cela vous permet d’accéder au portail de gestion de l’API de publisher.

![Portail de Publisher][api-management-management-console]

>Si vous n’avez pas encore créé une instance de service de gestion des API, voir [créer une instance de service de gestion de l’API][] dans le didacticiel [mise en route de la gestion des API Azure][] .

Dans le menu de **Gestion de l’API** à gauche pour afficher les notifications disponibles, cliquez sur **Notifications** .

![Notifications de Publisher][api-management-publisher-notifications]

La liste suivante d’événements peut être configurée pour les notifications.

-   **Demandes d’abonnement (pour demander une approbation)** - les destinataires de messagerie spécifiée et les utilisateurs recevront les notifications par courrier électronique à propos des demandes d’abonnement pour les produits API pour demander une approbation.
-   **Nouveaux abonnements** - les destinataires de messagerie spécifiée et les utilisateurs recevront des notifications par courrier électronique sur les nouveaux abonnements produit API.
-   **Demandes Galerie d’application** : les destinataires de messagerie spécifiée et les utilisateurs recevront les notifications par courrier électronique lorsque des nouvelles applications sont envoyées à la galerie d’applications.
-   **Cci** - les destinataires de messagerie spécifiée et les utilisateurs reçoivent messagerie une copie carbone invisible de tous les messages électroniques envoyés aux développeurs.
-   **Nouveau problème ou commentaire** - les destinataires de messagerie spécifiée et les utilisateurs recevront des notifications par courrier électronique lorsqu’un nouveau problème ou commentaire est présenté dans le portail développeur.
-   **Message clôturer mon compte** - les destinataires de messagerie spécifiée et les utilisateurs recevront notifications par courrier électronique lorsqu’un compte est fermé.
-   **Limite de quota Approaching abonnement** - les destinataires de messagerie et les utilisateurs suivants recevoir des notifications par courrier électronique lors de l’utilisation d’abonnement obtient près de quota d’utilisation.

Pour chaque événement, vous pouvez spécifier les destinataires de messagerie à l’aide de la zone de texte adresse e-mail ou vous pouvez sélectionner des utilisateurs à partir d’une liste.

Pour spécifier les adresses de messagerie pour être averti, entrez-les dans la zone de texte adresse e-mail. Si vous avez plusieurs adresses de messagerie, séparés par des virgules.

![Destinataires de notification][api-management-email-addresses]

Pour indiquer les utilisateurs pour être averti, cliquez sur **Ajouter un destinataire**, cochez la case en regard de l’utilisateur doit être informé, puis cliquez sur **OK**.

>Notez que seuls les administrateurs sont affichées dans la liste.

Après avoir configuré les destinataires de notification, cliquez sur **Enregistrer** pour appliquer les destinataires de notification mis à jour.

>Si vous naviguez en dehors de l’onglet **Notifications Publisher** le portail publisher vous avertit s’il y a des modifications non enregistrées.

## <a name="email-templates"> </a>Configurer les modèles de courrier électronique

Gestion des API fournit des modèles de courrier électronique pour les messages électroniques qui sont envoyés au cours d’administration et l’utilisation du service. Les modèles de messagerie suivants sont fournis.

-   Présentation de la galerie application approuvée
-   Lettre de dernières informations pour les développeurs
-   Limite de quota pour les développeurs approche de notification
-   Inviter un utilisateur
-   Nouveau commentaire est ajouté à un problème
-   Nouveau problème reçu
-   Nouvel abonnement activé
-   Confirmation d’abonnement renouvelé
-   Demande d’abonnement décline
-   Demande d’abonnement reçue

Ces modèles peuvent être modifiés selon les besoins.

Pour afficher et configurer les modèles de courrier électronique pour votre instance de gestion de l’API, cliquez sur **Notifications** dans le menu de **Gestion de l’API** sur la gauche, puis sélectionnez l’onglet **Modèles de courrier électronique** .

![Modèles de courrier électronique][api-management-email-templates]

Pour afficher ou modifier un modèle spécifique, sélectionnez-le dans la liste déroulante de **modèles** .

![Liste des modèles de courrier électronique][api-management-email-templates-list]

Chaque modèle de courrier électronique comporte un objet au format texte brut et une définition de corps au format HTML. Chaque élément peut être personnalisée selon les besoins.

![Éditeur de modèle de courrier électronique][api-management-email-template]

La liste **paramètres** contient la liste des paramètres, où inséré dans l’objet ou le corps, sera remplacé la valeur désignée lorsque le message est envoyé. Pour insérer un paramètre, placez le curseur à l’endroit où vous souhaitez le paramètre pour accéder, puis cliquez sur la flèche vers la gauche du nom du paramètre.

Cliquez sur **Aperçu** ou **Envoyer un test** pour voir comment le courrier électronique doit ressembler ou envoyer un message électronique de test.

>Notez que les paramètres ne sont pas remplacées par des valeurs réelles lors de l’aperçu ou l’envoi d’un test.
>
>Pour enregistrer les modifications dans le modèle de courrier électronique, cliquez sur **Enregistrer**ou annuler les modifications cliquez sur **Annuler**.



[api-management-management-console]: ./media/api-management-howto-configure-notifications/api-management-management-console.png
[api-management-publisher-notifications]: ./media/api-management-howto-configure-notifications/api-management-publisher-notifications.png
[api-management-email-addresses]: ./media/api-management-howto-configure-notifications/api-management-email-addresses.png


[api-management-email-templates]: ./media/api-management-howto-configure-notifications/api-management-email-templates.png
[api-management-email-templates-list]: ./media/api-management-howto-configure-notifications/api-management-email-templates-list.png
[api-management-email-template]: ./media/api-management-howto-configure-notifications/api-management-email-template.png


[Configure publisher notifications]: #publisher-notifications
[Configure email templates]: #email-templates

[How to create and use groups]: api-management-howto-create-groups.md
[How to associate groups with developers]: api-management-howto-create-groups.md#associate-group-developer

[Prise en main avec la gestion des API Azure]: api-management-get-started.md
[Créer une instance de service de gestion de l’API]: api-management-get-started.md#create-service-instance
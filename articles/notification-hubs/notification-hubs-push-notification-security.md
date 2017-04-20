<properties
    pageTitle="Sécurité pour Hubs de Notification"
    description="Cette rubrique explique la sécurité pour hubs notification Azure."
    services="notification-hubs"
    documentationCenter=".net"
    authors="ysxu"
    manager="erikre"
    editor=""/>

<tags
    ms.service="notification-hubs"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-multiple"
    ms.devlang="multiple"
    ms.topic="article"
    ms.date="06/29/2016"
    ms.author="yuaxu"/>

#<a name="security"></a>Sécurité

##<a name="overview"></a>Vue d’ensemble

Cette rubrique décrit le modèle de sécurité d’Azure Notification Hubs. Notification Hubs étant une entité Bus des services, il en œuvre le même modèle de sécurité que Bus des services. Pour plus d’informations, consultez les rubriques [D’authentification Bus de Service](https://msdn.microsoft.com/library/azure/dn155925.aspx) .

##<a name="shared-access-signature-security-sas"></a>Accès partagé Signature sécurité (sa) 

Notification Hubs mettent en œuvre, un modèle de sécurité au niveau de l’entité appelées associations de sécurité (partagés accès Signature). Ce modèle permet aux utilisateurs de messagerie déclarer jusqu'à 12 règles d’autorisation dans leur description qui accorder des droits relatifs à l’entité.

Chaque règle contient un nom, une valeur de clé (secret partagé) et un ensemble de droits, comme indiqué dans la section « Revendications de sécurité ». Lorsque vous créez un concentrateur de Notification, deux règles sont automatiquement créées : une avec des droits d’écouter (qui utilise de l’application client) et une contenant tous les droits (dont le système principal de l’application utilise).

Lors de la gestion de l’inscription à partir des applications clientes, si les informations envoyées par notifications n’est pas sensible (par exemple, mises à jour de la météo), une façon courante d’accéder à un concentrateur de Notification pour donner à la valeur de clé de la règle d’accès écouter uniquement à l’application client et de vous donner la valeur de clé de l’accès complet règle sur le serveur principal de l’application.

Il n’est pas recommandé incorporer la valeur de clé dans les applications de client du Windows Store. Une façon d’éviter l’incorporation de la valeur de clé est pour que l’application cliente récupérer à partir de la version serveur application au démarrage.

Il est important de comprendre que la clé avec un accès écouter permet à une application client pour vous inscrire à n’importe quelle balise. Si votre application doit limiter les enregistrements à des marqueurs spécifiques pour des clients spécifiques (par exemple, lorsque les balises représentent des ID utilisateur), la principale application doit effectuer les enregistrements. Pour plus d’informations, voir gestion de l’enregistrement. Notez que de cette façon, l’application client n’ont pas accès direct aux Hubs de Notification.

##<a name="security-claims"></a>Revendications de sécurité

Similaire à d’autres entités, opérations concentrateur de Notification sont autorisées pour trois revendications de sécurité : écouter, envoyer et gérer.

| Réclamer | Description | Opérations autorisées |
|-------|-------------|--------------------|
| Écouter | Créer/mettre à jour, lisez et supprimer les enregistrements unique | Créer/mettre à jour l’enregistrement<br><br>Lire l’enregistrement<br><br>Lire tous les enregistrements d’une poignée<br><br>Supprimer l’enregistrement |
| Envoyer | Envoyer des messages vers le hub de notification | Envoyer des messages |
| Gérer | CRUDs sur Hubs Notification (y compris la mise à jour des informations d’identification de la solution et des clés de sécurité) et lire des enregistrements en fonction de balises | Hubs notification créer/mise à jour/en lecture/supprimer<br><br>Lire des enregistrements par une balise |


Notification Hubs acceptent revendications accordées en jetons de contrôle d’accès Microsoft Azure et en jetons signature générés à l’aide des clés partagées configurés directement sur le Hub de Notification.
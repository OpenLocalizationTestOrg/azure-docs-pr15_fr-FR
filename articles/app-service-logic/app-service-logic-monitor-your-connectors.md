<properties
    pageTitle="Gérer et analyser vos connecteurs et les applications de l’API dans le Service d’application | Microsoft Azure"
    description="Afficher les performances des liens et des applications de l’API dans les applications logique ; architecture microservices"
    services="app-service\logic"
    documentationCenter=".net,nodejs,java"
    authors="MandiOhlinger"
    manager="anneta"
    editor="cgronlun"/>

<tags
    ms.service="logic-apps"
    ms.workload="integration"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/18/2016"
    ms.author="mandia"/>

# <a name="manage-and-monitor-your-built-in-api-apps-and-connectors"></a>Gérer et analyser vos applications API et les connecteurs intégrés

>[AZURE.NOTE] Cette version de l’article s’applique à la version du schéma logique applications 2014-12-01-aperçu.

Vous avez créé une application API intégrée. Quoi encore ?

Dans Azure, chaque application API est un site web séparé hébergés sur Azure. Par conséquent, vous pouvez facilement repérer le nombre de requêtes est effectué et consultez la quantité de données est utilisé par le connecteur. Vous pouvez également sauvegarder votre application API, créer des alertes, autoriser Tinfoil sécurité et ajouter des utilisateurs et les rôles.

Cette rubrique décrit quelques-unes des différentes options pour gérer votre application API.

Pour afficher ces fonctionnalités intégrées, ouvrez votre application API dans le [portail Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040). Si l’application API se trouve sur votre startboard, sélectionnez-la pour ouvrir les propriétés. Vous pouvez également sélectionner **Parcourir**, sélectionnez **Applications API**, puis sélectionnez votre application API :

![][browse]

## <a name="see-the-properties-you-entered"></a>Voir les propriétés que vous avez entré

Lorsque vous ouvrez l’application API, il existe plusieurs tâches et fonctionnalités disponibles :

![][settings]

Vous pouvez :

- **Paramètres** présentant des informations spécifiques sur l’application de l’API, y compris les détails de votre abonnement et répertorie les utilisateurs qui ont accès à votre application API. Vous pouvez également augmenter ou diminuer le nombre d’instances de votre application API à l’aide de la fonction échelle ; parmi d’autres fonctionnalités.
- Utilisez les boutons **Démarrer** et **Arrêter** pour contrôler l’application API.
- Lorsque des mises à jour sont apportées aux fichiers sous-jacente utilisées par votre application API, vous pouvez cliquer sur **mise à jour** pour obtenir les dernières versions. Par exemple, s’il existe un correctif ou une mise à jour de sécurité publié par Microsoft, en cliquant sur **mettre à jour** automatiquement mises à jour votre application API afin d’inclure ce correctif.
- Permet de **Planifier la modification** ou le déclassement basées sur l’utilisation de données de l’application API. Vous pouvez également utiliser cette fonctionnalité pour afficher l’utilisation de vos données.
- Lorsque vous créez un connecteur comportant des tables, comme le connecteur SQL, vous pouvez éventuellement entrer un nom de table pour vous connecter à. Un schéma basé sur la table est automatiquement créées et disponibles lorsque vous cliquez sur **Télécharger des schémas**. Vous pouvez ensuite utiliser ce schéma téléchargé pour créer une transformation ou une carte.

## <a name="change-your-connector-or-api-configuration-values-you-entered"></a>Modifier votre connecteur ou vous avez entré les valeurs de configuration API

Après avoir configuré ou créé votre connecteur intégré, vous pouvez modifier les valeurs que vous avez entré. Par exemple, si vous avez configuré le connecteur SQL et que vous voulez modifier le nom de SQL Server ou le nom de la table, vous pouvez procédez comme suit dans la carte API application pour votre lien.

Vous pouvez effectuer :

1. Ouvrez votre connecteur ou API application. Lorsque vous faites, la carte API application s’ouvre.
2. Dans **Essentials**, cliquez sur le lien hypertexte sous la propriété hôte. Le lien hypertexte est un élément nommé comme *slackconnector* ou *microsoftsqlconnector123*:

    ![][apiapphost]

3. Dans la carte API Application Host, sélectionnez **paramètres**. Dans la carte de paramètres, sélectionnez **Paramètres de l’Application**. Les valeurs de votre configuration sont répertoriés sous **Paramètres de l’application**:

    ![][hostsettings]

4. Cliquez sur le paramètre que vous voulez modifier, entrez la nouvelle valeur et **Enregistrer** vos modifications.


## <a name="install-the-hybrid-connection-manager---optional"></a>Installer le Gestionnaire de connexions hybride - facultatif

![][hcsetup]

Le Gestionnaire de connexions hybride vous donne la possibilité de se connecter à un système en local, telles que SQL Server ou SAP. Cette connectivité hybride utilise Bus des services Azure pour se connecter et contrôler la sécurité entre vos ressources Azure et vos ressources locales.

Consultez [l’aide du Gestionnaire de connexion hybride dans le Service d’application Azure](app-service-logic-hybrid-connection-manager.md).

> [AZURE.NOTE] Gestionnaire de connexions hybride est requis uniquement si vous vous connectez à une ressource locale derrière le pare-feu. Si vous vous connectez pas à un système local, le Gestionnaire de connexions hybride peut ne pas figurer dans votre carte de connecteur.

## <a name="monitor-the-performance"></a>Surveiller les performances
Indicateurs de performance sont des fonctionnalités intégrées et inclus avec chaque application API que vous créez. Les mesures sont spécifiques à votre application hébergée dans Azure API. Indicateurs d’exemple :

![][monitoring]

Vous pouvez :

- Sélectionnez les **demandes et les erreurs** d’ajouter des indicateurs de performance, y compris les codes d’erreur HTTP connus fréquemment, comme 200, 400 ou 500 codes d’état HTTP. Vous pouvez également voir le temps de réponse, voir le nombre de requêtes est apporté à l’application API et la quantité de données est émis et la quantité de données viennent. Selon les mesures de performances, vous pouvez créer des alertes de messagerie si une métrique dépasse un seuil de votre choix.
- Dans **l’utilisation**, vous pouvez voir combien **UC** est utilisée par l’API application, passez en revue le **Quota d’utilisation** actuelle dans Mo et voir votre utilisation maximale de données en fonction de votre niveau de coût. **Durée estimée** peut vous aider à déterminer le coût potentiel de l’exécution de votre application API.
- Sélectionnez les **processus** pour ouvrir l’Explorateur de processus. Ceci affiche vos instances web et leurs propriétés, y compris l’utilisation des threads Nb et la quantité de mémoire.

À l’aide de ces outils, vous pouvez déterminer si le Plan de Service d’application doivent être agrandie ou mise à l’échelle vers le bas, en fonction des besoins de votre entreprise. Ces fonctionnalités sont intégrées au portail avec pas d’autres outils requis.

## <a name="control-the-security"></a>Contrôler la sécurité

Applications API utiliser la sécurité basée sur les rôles. Ces rôles s’appliquent à l’expérience Azure entière, y compris les applications de l’API et d’autres ressources Azure. Les rôles sont les suivantes :

Rôle | Description
--- | ---
Propriétaire | Ont un accès total à l’expérience de gestion et permettent d’accéder à d’autres utilisateurs ou groupes.
Collaboration | Ont un accès total à l’expérience de gestion. Ne peut pas accéder à d’autres utilisateurs ou groupes.
Reader | Peut afficher toutes les ressources à l’exception des secrets.
Administrateur de l’accès utilisateur | Peuvent voir toutes les ressources, créer/gérer les rôles et créer/gérer prend en charge des tickets.

Voir [contrôle d’accès basé sur un rôle dans le portail Microsoft Azure](../active-directory/role-based-access-control-configure.md).

Vous pouvez facilement ajouter des utilisateurs et les affecter des rôles spécifiques à votre application API. Le portail affiche les utilisateurs qui ont accès et son rôle :

![][access]  

- Sélectionnez **les utilisateurs** à ajouter un utilisateur, attribuer un rôle et supprimer un utilisateur.
- Sélectionnez les **rôles** pour voir tous les utilisateurs dans un rôle spécifique, ajouter un utilisateur à un rôle et supprimer un utilisateur d’un rôle.


## <a name="more-good-stuff"></a>Plus intéressants
- Sélectionnez **la définition de l’API** pour ouvrir le fichier Swagger automatiquement créé pour votre application API spécifique.
- Sélectionnez **dépendances** pour afficher les fichiers requis par votre application API. Par exemple, si vous utilisez le connecteur SAP, vous installez certains fichiers supplémentaires dans le Gestionnaire de connexions de hybride local. Ces dépendances sont représentées dans votre carte application API.

>[AZURE.IMPORTANT] Lorsque vous ouvrez les propriétés de votre application API et regardez sous **Essentials**, il existe **hôte** et la **passerelle** liens qui ouvrent des cartes nouveau :
>
> ![][host]
>
>Ces propriétés sont spécifiques au site Web qui héberge votre application API. Lorsque vous utilisez une API application intégrée ou le connecteur, la plupart d'entre elles ne s’appliquent vraiment et nous vous recommandons de ne pas la mettre à jour ces propriétés. Si vous avez créé votre propre application API dans Visual Studio et déployé à votre abonnement Azure, vous pouvez utiliser les cartes hôte et la passerelle. <br/><br/>


>[AZURE.NOTE] Pour commencer avec les applications logique avant de vous inscrire pour un compte Azure, accédez à [Essayer logique d’application](https://tryappservice.azure.com/?appservice=logic). Vous pouvez créer une application de logique courte starter. Aucune carte de crédit obligatoire et aucune engagements.

## <a name="read-more"></a>En savoir plus

[Surveiller des applications de votre logique](app-service-logic-monitor-your-logic-apps.md)<br/>
[Liens et des API liste d’applications dans le Service d’application](app-service-logic-connectors-list.md)<br/>
[Contrôle d’accès basé sur un rôle dans le portail Microsoft Azure](../active-directory/role-based-access-control-configure.md)<br/>
[L’utilisation du Gestionnaire de connexion hybride dans le Service d’application Azure](app-service-logic-hybrid-connection-manager.md)


<!--Image references-->
[browse]: ./media/app-service-logic-monitor-your-connectors/browse.png
[settings]: ./media/app-service-logic-monitor-your-connectors/settings.png
[hcsetup]: ./media/app-service-logic-monitor-your-connectors/hcsetup.png
[monitoring]: ./media/app-service-logic-monitor-your-connectors/monitoring.png
[access]: ./media/app-service-logic-monitor-your-connectors/access.png
[host]: ./media/app-service-logic-monitor-your-connectors/host.png
[hostsettings]: ./media/app-service-logic-monitor-your-connectors/hostsettings.png
[apiapphost]: ./media/app-service-logic-monitor-your-connectors/apiapphost.png

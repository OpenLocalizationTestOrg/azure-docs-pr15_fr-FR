<properties
    pageTitle="Prise en main relais hybride connexions | Microsoft Azure"
    description="Comment écrire une application console c# pour les connexions hybride"
    services="service-bus"
    documentationCenter=".net"
    authors="jtaubensee"
    manager="timlt"
    editor=""/>

<tags
    ms.service="service-bus"
    ms.devlang="tbd"
    ms.topic="hero-article"
    ms.tgt_pltfrm="dotnet"
    ms.workload="na"
    ms.date="10/28/2016"
    ms.author="jotaub"/>

# <a name="get-started-with-relay-hybrid-connections"></a>Prise en main relais hybride connexions

[AZURE.INCLUDE [relay-selector-hybrid-connections](../../includes/relay-selector-hybrid-connections.md)]

## <a name="what-will-be-accomplished"></a>Quelle sera effectué

Dans la mesure où les connexions hybride requiert un client et un composant de serveur, nous allons créer deux applications console dans ce didacticiel. Voici les étapes :

1. Créer un espace de noms de relais, à l’aide du portail Azure.

2. Créer une connexion hybride, à l’aide du portail Azure.

3. Écrire un serveur application console pour recevoir des messages.

4. Écrire un client application console pour envoyer des messages.

## <a name="prerequisites"></a>Conditions préalables

1. [Visual Studio 2013 ou Visual Studio 2015](http://www.visualstudio.com). Les exemples dans ce didacticiel utilisent Visual Studio 2015.

2. Un abonnement Azure.

[AZURE.INCLUDE [create-account-note](../../includes/create-account-note.md)]

## <a name="1-create-a-namespace-using-the-azure-portal"></a>1. créer un espace de noms à l’aide du portail Azure

Si vous disposez déjà d’un espace de noms relais créé, accéder à la section [créer une connexion hybride à l’aide du portail Azure](#2-create-a-hybrid-connection-using-the-azure-portal) .

[AZURE.INCLUDE [relay-create-namespace-portal](../../includes/relay-create-namespace-portal.md)]

## <a name="2-create-a-hybrid-connection-using-the-azure-portal"></a>2. créer une connexion hybride à l’aide du portail Azure

Si vous avez déjà une connexion hybride créée, accéder à la section [Création d’une application serveur](#3-create-a-server-application-listener) .

[AZURE.INCLUDE [relay-create-hybrid-connection-portal](../../includes/relay-create-hybrid-connection-portal.md)]

## <a name="3-create-a-server-application-listener"></a>3. créer une application de serveur (récepteur)

Pour écouter et recevoir des messages depuis le relais, nous allez écrire une application de console c# à l’aide de Visual Studio.

[AZURE.INCLUDE [relay-hybrid-connections-dotnet-get-started-server](../../includes/relay-hybrid-connections-dotnet-get-started-server.md)]

## <a name="4-create-a-client-application-sender"></a>4. créer une application cliente (expéditeur)

Pour envoyer des messages pour le relais, nous allez écrire une application de console c# à l’aide de Visual Studio.

[AZURE.INCLUDE [relay-hybrid-connections-dotnet-get-started-client](../../includes/relay-hybrid-connections-dotnet-get-started-client.md)]

## <a name="5-run-the-applications"></a>5. exécuter les applications

1. Exécutez l’application serveur.

2. Exécutez l’application cliente et entrez du texte.

3. Assurez-vous que la console d’application server extrait le texte qui a été entré dans l’application cliente.

![applications d’exécution](./media/relay-hybrid-connections-dotnet-get-started/running-applications.png)

Félicitations, vous avez créé une application de connexions hybride de bout en bout.

## <a name="next-steps"></a>Étapes suivantes :

- [Relais Forum aux questions](relay-faq.md)
- [Créer un espace de noms](relay-create-namespace-portal.md)
- [Prise en main nœud](relay-hybrid-connections-node-get-started.md)
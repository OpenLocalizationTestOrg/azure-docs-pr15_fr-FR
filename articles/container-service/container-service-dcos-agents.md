<properties
   pageTitle="Public et privé DC/OS Agent Pools ACS | Microsoft Azure"
   description="Fonctionnement des pools d’agent publiques et privées avec un cluster de Service de conteneur Azure."
   services="container-service"
   documentationCenter=""
   authors="Thraka"
   manager="timlt"
   editor=""
   tags="acs, azure-container-service"
   keywords="Docker, conteneurs, Micro-services, Mesos, Azure"/>

<tags
   ms.service="container-service"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/16/2016"
   ms.author="timlt"/>

# <a name="dcos-agent-pools-for-azure-container-service"></a>Pools d’Agent DC/système d’exploitation pour le Service de conteneur Azure

Service de conteneur Azure DC/système d’exploitation divise agents en pools publics ou privés. Un déploiement peut être effectué à un pool, qui peuvent affecter d’accessibilité entre machines dans votre service de conteneur. Les ordinateurs peuvent être visible sur internet (public) ou conservés interne (privé). Cet article contient un bref aperçu des raisons pour lesquelles il existe un pool publique et privé.

### <a name="private-agents"></a>Agents privés

Agent privé nœuds exécuter via un réseau non prenant. Ce réseau est uniquement accessible à partir de la zone administrateur ou via le routeur de bordure de zone public. Par défaut, DC/système d’exploitation lance applications sur nœuds agent privé. Pour plus d’informations sur la sécurité du réseau, consultez la [documentation DC/système d’exploitation](https://dcos.io/docs/1.7/administration/securing-your-cluster/) .

### <a name="public-agents"></a>Agents publics

Agent public nœuds exécuter DC/OS applications et services via un réseau accessible au public. Pour plus d’informations sur la sécurité du réseau, consultez la [documentation DC/système d’exploitation](https://dcos.io/docs/1.7/administration/securing-your-cluster/) .

## <a name="using-agent-pools"></a>À l’aide de l’agent

Par défaut, **Marathon** déploie toute nouvelle application pour les nœuds agent *privé* . Vous devez explicitement déployer l’application sur le nœud *public* lors de la création de l’application. Sélectionnez l’onglet **facultatif** , entrez **slave_public** pour la valeur **Acceptée rôles des ressources** . Ce processus est décrit [ici](container-service-mesos-marathon-ui.md#deploy-a-docker-formatted-container) et dans la documentation [DC\OS](https://dcos.io/docs/1.7/administration/installing/custom/create-public-agent/) .

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur la [gestion de vos conteneurs DC/système d’exploitation](container-service-mesos-marathon-ui.md).

Découvrez comment [Ouvrir le pare-feu](container-service-enable-public-access.md) fourni par Azure pour autoriser l’accès public à votre conteneur DC/système d’exploitation.
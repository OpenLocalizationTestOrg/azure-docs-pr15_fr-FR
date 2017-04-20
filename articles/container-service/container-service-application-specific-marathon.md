<properties
   pageTitle="Application ou un service de Marathon spécifiques à l’utilisateur | Microsoft Azure"
   description="Créer une application ou un service de Marathon spécifiques à l’utilisateur"
   services="container-service"
   documentationCenter=""
   authors="rgardler"
   manager="timlt"
   editor=""
   tags="acs, azure-container-service"
   keywords="Conteneurs, Marathon, Micro-services, DC/système d’exploitation, Azure"/>

<tags
   ms.service="container-service"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="04/12/2016"
   ms.author="rogardle"/>

# <a name="create-an-application-or-user-specific-marathon-service"></a>Créer une application ou un service de Marathon spécifiques à l’utilisateur

Service de conteneur Azure propose un ensemble de serveurs maîtres sur lequel nous préconfigurez Mesos Apache et Marathon. Il peuvent être utilisés pour organiser vos applications sur le cluster, mais il est préférable de ne pas utiliser les serveurs maîtres à cet effet. Par exemple, peaufinage la configuration de Marathon requiert Connectez les serveurs maîtres et apporter des modifications--Cela encourage serveurs maîtres uniques qui sont un peu différentes à partir du standard et que vous devez être soignés et gérées séparément. En outre, la configuration requise par une équipe peut être pas la configuration optimale pour une autre équipe.

Dans cet article, nous allons expliquent comment ajouter une application ou un service de Marathon spécifiques à l’utilisateur.

Étant donné que ce service appartient à un seul utilisateur ou de l’équipe, ils sont autorisés à configurer d’aucune manière qu’ils souhaitent. En outre, Azure conteneur Service assuré que le service continue de fonctionner. Si le service échoue, Azure conteneur Service redémarre pour vous. La plupart du temps vous ne même Notez qu’il avait temps d’arrêt.

## <a name="prerequisites"></a>Conditions préalables

[Déployer une instance de Service de conteneur Azure](container-service-deployment.md) avec type orchestrator DC/système d’exploitation et [Vérifiez que votre client peut se connecter à votre cluster](container-service-connect.md). En outre, procédez comme suit.

[AZURE.INCLUDE [install the DC/OS CLI](../../includes/container-service-install-dcos-cli-include.md)]

## <a name="create-an-application-or-user-specific-marathon-service"></a>Créer une application ou un service de Marathon spécifiques à l’utilisateur

Commencez par créer un fichier de configuration JSON qui définit le nom du service d’application que vous souhaitez créer. Ici, nous utilisons `marathon-alice` en tant que le nom du cadre. Enregistrez le fichier sous s’intitule `marathon-alice.json`:

```json
{"marathon": {"framework-name": "marathon-alice" }}
```

Ensuite, utilisez l’infrastructure du langage commun DC/système d’exploitation pour installer l’instance Marathon avec les options qui sont définies dans votre fichier de configuration :

```bash
dcos package install --options=marathon-alice.json marathon
```

Vous devriez maintenant voir votre `marathon-alice` service en cours d’exécution dans l’onglet Services de l’interface utilisateur DC/système d’exploitation. L’interface utilisateur sera `http://<hostname>/service/marathon-alice/` si vous souhaitez y accéder directement.

## <a name="set-the-dcos-cli-to-access-the-service"></a>Définir l’infrastructure du langage commun DC/système d’exploitation pour accéder au service

Vous pouvez éventuellement configurer votre infrastructure du langage commun DC/système d’exploitation pour accéder à ce nouveau service en définissant la `marathon.url` propriété pointe vers le `marathon-alice` instance comme suit :

```bash
dcos config set marathon.url http://<hostname>/service/marathon-alice/
```

Vous pouvez vérifier l’instance de Marathon fonctionne par rapport à votre infrastructure du langage commun avec la `dcos config show` commande. Vous pouvez revenir à l’utilisation de votre service Marathon maître avec la commande `dcos config unset marathon.url`.

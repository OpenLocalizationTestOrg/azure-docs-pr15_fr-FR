<properties
   pageTitle="Prise en main Azure infrastructure du langage commun lot | Microsoft Azure"
   description="Obtenir une brève introduction aux commandes lot dans Azure infrastructure du langage commun pour la gestion des ressources de service lot Azure"
   services="batch"
   documentationCenter=""
   authors="mmacy"
   manager="timlt"
   editor=""/>

<tags
   ms.service="batch"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="multiple"
   ms.workload="big-compute"
   ms.date="09/30/2016"
   ms.author="marsma"/>

# <a name="get-started-with-azure-batch-cli"></a>Prise en main Azure infrastructure du langage commun par lots

La disponibilité sur plusieurs plateformes Azure de ligne de commande Interface (Azure) vous permet de gérer vos comptes lot et des ressources telles que des pools, des projets et des tâches dans shell de commande Linux, Mac et Windows. Avec l’infrastructure du langage commun Azure, vous pouvez effectuer et de nombreuses tâches mêmes que procéder avec l’API lot, portail Azure et applets de commande PowerShell lot de script.

Cet article est basé sur Azure infrastructure du langage commun version 0.10.5.

## <a name="prerequisites"></a>Conditions préalables

* [Installer l’infrastructure du langage commun Azure](../xplat-cli-install.md)

* [Se connecter l’infrastructure du langage commun Azure à votre abonnement Azure](../xplat-cli-connect.md)

* Passer en **mode Gestionnaire de ressources**:`azure config mode arm`

>[AZURE.TIP] Nous vous recommandons que vous mettez à jour votre installation Azure infrastructure du langage commun fréquemment afin de tirer parti des améliorations et mises à jour de service.

## <a name="command-help"></a>Commande d’aide

Vous pouvez afficher un texte d’aide pour toutes les commandes dans l’infrastructure du langage commun Azure en ajoutant `-h` comme une seule option après la commande. Par exemple :

* Pour obtenir de l’aide pour le `azure` de commande, tapez :`azure -h`
* Pour obtenir une liste de toutes les commandes du lot dans l’infrastructure du langage commun, utilisez :`azure batch -h`
* Pour obtenir de l’aide sur la création d’un compte lot, entrez :`azure batch account create -h`

En cas de doute, utilisez la `-h` option de ligne de commande pour obtenir de l’aide sur n’importe quelle commande Azure infrastructure du langage commun.

## <a name="create-a-batch-account"></a>Créer un compte lot

Syntaxe :

    azure batch account create [options] <name>

Exemple :

    azure batch account create --location "West US"  --resource-group "resgroup001" "batchaccount001"

Crée un nouveau compte lot avec les paramètres spécifiés. Vous devez spécifier au moins un emplacement, le groupe de ressources et le nom du compte. Si vous n’avez pas un groupe de ressources, créez-le en exécutant `azure group create`et indiquez l’un des régions Azure (par exemple, « ouest nous ») pour la `--location` option. Par exemple :

    azure group create --name "resgroup001" --location "West US"

> [AZURE.NOTE] Le nom de compte doit être unique au sein de la région Azure que le compte est créé. Il peut contenir uniquement des caractères alphanumériques en minuscules et doit être 3-24 caractères. Vous ne pouvez pas utiliser des caractères spéciaux tels que `-` ou `_` dans les noms des comptes lot.

### <a name="linked-storage-account-autostorage"></a>Compte de stockage liées (autostorage)

Vous pouvez (le cas échéant) lier un compte de stockage **objectif général** à votre compte lot lors de sa création. La fonctionnalité [packages d’application](batch-application-packages.md) du lot utilise le stockage blob dans un objectif général lié compte de stockage, comme le fait la bibliothèque [Lot fichier Conventions .NET](batch-task-output.md) . Ces fonctionnalités facultatives vous aideront à déployer les applications qu'exécuteront vos tâches lot et conserver les données qu’ils produisent.

Pour lier un compte de stockage Azure existant vers un nouveau compte lot lors de sa création, spécifiez la `--autostorage-account-id` option. Cette option nécessite l’ID de ressource complet du compte de stockage.

Tout d’abord, afficher les détails de votre compte de stockage :

    azure storage account show --resource-group "resgroup001" "storageaccount001"

Puis utilisez la valeur **d’Url** pour le `--autostorage-account-id` option. La valeur de l’Url commence par « / abonnements / » et affiche votre abonnement ID et des ressources chemin d’accès du compte de stockage :

    azure batch account create --location "West US"  --resource-group "resgroup001" --autostorage-account-id "/subscriptions/8ffffff8-4444-4444-bfbf-8ffffff84444/resourceGroups/resgroup001/providers/Microsoft.Storage/storageAccounts/storageaccount001" "batchaccount001"

## <a name="delete-a-batch-account"></a>Supprimer un compte par lots

Syntaxe :

    azure batch account delete [options] <name>

Exemple :

    azure batch account delete --resource-group "resgroup001" "batchaccount001"

Supprime le compte lot spécifié. Lorsque vous y êtes invité, confirmez que vous voulez supprimer le compte (suppression du compte peut prendre du temps).

## <a name="manage-account-access-keys"></a>Gérer les touches d’accès compte

Vous avez besoin d’une touche d’accès pour [créer et modifier des ressources](#create-and-modify-batch-resources) dans votre compte lot.

### <a name="list-access-keys"></a>Touches d’accès de liste

Syntaxe :

    azure batch account keys list [options] <name>

Exemple :

    azure batch account keys list --resource-group "resgroup001" "batchaccount001"

Répertorie les touches de compte pour le compte lot donné.

### <a name="generate-a-new-access-key"></a>Générer une nouvelle clé d’accès

Syntaxe :

    azure batch account keys renew [options] --<primary|secondary> <name>

Exemple :

    azure batch account keys renew --resource-group "resgroup001" --primary "batchaccount001"

Régénère la clé de compte spécifié pour le compte lot donné.

## <a name="create-and-modify-batch-resources"></a>Créer et modifier des ressources de traitement par lots

Vous pouvez utiliser l’infrastructure du langage commun Azure pour créer, lire, mettre à jour et supprimer des ressources de traitement par lots (CRUD) comme des pools, calculer des nœuds, les tâches et les tâches. Ces opérations nécessitent votre nom de compte la feuille, touche d’accès rapide et point de terminaison. Vous pouvez également définir ces avec la `-a`, `-k`, et `-u` options ou définir des [variables d’environnement](#credential-environment-variables) qui l’infrastructure du langage commun utilise automatiquement (si rempli).

### <a name="credential-environment-variables"></a>Variables d’environnement d’informations d’identification

Vous pouvez définir `AZURE_BATCH_ACCOUNT`, `AZURE_BATCH_ACCESS_KEY`, et `AZURE_BATCH_ENDPOINT` variables d’environnement au lieu de spécifier `-a`, `-k`, et `-u` options sur la ligne de commande pour chaque commande que vous exécutez. L’infrastructure du langage commun lot utilise ces variables (si la valeur) afin que vous pouvez omettre le `-a`, `-k`, et `-u` options. Le reste de cet article suppose l’utilisation de ces variables.

>[AZURE.TIP] Liste de vos clés avec `azure batch account keys list`et afficher le point de terminaison du compte avec `azure batch account show`.

### <a name="json-files"></a>Fichiers JSON

Lorsque vous créez des ressources lot comme des pools et les tâches, vous pouvez spécifier un fichier JSON contenant la configuration de la nouvelle ressource au lieu de passer des options de ligne de commande comme ses paramètres. Par exemple :

`azure batch pool create my_batch_pool.json`

Pendant que vous pouvez effectuer de nombreuses opérations de création d’une ressource en utilisant uniquement les options de ligne de commande, certaines fonctionnalités requièrent un fichier au format JSON contenant des détails sur la ressource. Par exemple, vous devez utiliser un fichier JSON si vous voulez spécifier des fichiers de ressources pour une tâche de démarrage.

Pour trouver le JSON nécessaire à la création d’une ressource, reportez-vous à la [référence de l’API REST lot] [ rest_api] documentation sur MSDN. Chaque sujet « Ajouter le *type de ressource*» contient exemple JSON pour la création de la ressource, vous pouvez utiliser en tant que modèles pour vos fichiers JSON. Par exemple, vous trouverez JSON pour la création d’une liste dans [Ajouter un pool à un compte][rest_add_pool].

>[AZURE.NOTE] Si vous spécifiez un fichier JSON lorsque vous créez une ressource, tous les autres paramètres que vous spécifiez sur la ligne de commande pour cette ressource sont ignorées.

## <a name="create-a-pool"></a>Créer un pool

Syntaxe :

    azure batch pool create [options] [json-file]

Exemple (Configuration de la Machine virtuelle) :

    azure batch pool create --id "pool001" --target-dedicated 1 --vm-size "STANDARD_A1" --image-publisher "Canonical" --image-offer "UbuntuServer" --image-sku "14.04.2-LTS" --node-agent-id "batch.node.ubuntu 14.04"

Exemple (Configuration des Services Cloud) :

    azure batch pool create --id "pool002" --target-dedicated 1 --vm-size "small" --os-family "4"

Crée un pool de nœuds de calcul dans le service lot.

Comme indiqué dans [vue d’ensemble de la fonctionnalité lot](batch-api-basics.md#pool), vous avez deux possibilités lorsque vous sélectionnez un système d’exploitation pour les nœuds dans votre pool : **Configuration de Machine virtuelle** et **Configuration des Services Cloud**. Utiliser la `--image-*` options permettant de créer des pools de Configuration de Machine virtuelle, et `--os-family` de créer des pools de Configuration des Services Cloud. Vous ne pouvez pas spécifier les deux `--os-family` et `--image-*` options.

Vous pouvez spécifier pool [packages d’application](batch-application-packages.md) et la ligne de commande pour [Démarrer la tâche](batch-api-basics.md#start-task). Toutefois, pour spécifier les fichiers de ressources pour cette tâche, vous devez utiliser à la place un [fichier JSON](#json-files).

Supprimer un pool avec :

    azure batch pool delete [pool-id]

>[AZURE.TIP] Consultez la [liste des images de machine virtuelle](batch-linux-nodes.md#list-of-virtual-machine-images) valeurs appropriée pour le `--image-*` options.

## <a name="create-a-job"></a>Créer une tâche

Syntaxe :

    azure batch job create [options] [json-file]

Exemple :

    azure batch job create --id "job001" --pool-id "pool001"

Ajoute une tâche à ce compte lot et spécifie le pool sur lequel exécutent ses tâches.

Supprimer une tâche avec :

    azure batch job delete [job-id]

## <a name="list-pools-jobs-tasks-and-other-resources"></a>Liste des pools, tâches, tâches et autres ressources

Chaque type de ressource lot prend en charge un `list` commande des requêtes de votre compte lot et répertorie les ressources de ce type. Par exemple, vous pouvez répertorier les pools dans votre compte et les tâches dans un projet :

    azure batch pool list
    azure batch task list --job-id "job001"

### <a name="listing-resources-efficiently"></a>Liste ressources efficacement

Pour les requêtes plus rapide, vous pouvez spécifier des options de clause **Sélectionnez**, **filtrer**et **développer** pour `list` opérations. Utilisez ces options pour limiter la quantité de données renvoyées par le service lot. Étant donné que tout le filtrage produit côté serveur, seules les données que qui vous intéresse dépasse le câble. Utilisez ces clauses pour enregistrer la bande passante (et par conséquent temps) lorsque vous effectuez les opérations de liste.

Par exemple, elle retournera uniquement des pools dont les ID commencent par « renderTask » :

    azure batch task list --job-id "job001" --filter-clause "startswith(id, 'renderTask')"

L’infrastructure du langage commun lot prend en charge tous les trois clauses pris en charge par le service lot :

* `--select-clause [select-clause]`Retourner un sous-ensemble de propriétés pour chaque entité
* `--filter-clause [filter-clause]`Retourner uniquement les entités qui correspondent à l’expression OData spécifiée
* `--expand-clause [expand-clause]`Obtenir les informations entité en un seul appel reste sous-jacente. La clause développer prend en charge uniquement la `stats` propriété pour le moment.

Pour plus d’informations sur les trois clauses et requêtes liste performantes avec eux, voir [interroger le service Azure lot efficacement](batch-efficient-list-queries.md).

## <a name="application-package-management"></a>Gestion de package d’application

Packages d’application fournissent un moyen simple de déployer des applications sur les nœuds de calcul dans vos pools. Avec l’infrastructure du langage commun Azure, vous pouvez télécharger des packages d’application, gérer les versions de package et supprimer les packages.

Pour créer une nouvelle application et ajouter une version de package :

**Créer** une application :

    azure batch application create "resgroup001" "batchaccount001" "MyTaskApplication"

**Ajouter** un package d’application :

    azure batch application package create "resgroup001" "batchaccount001" "MyTaskApplication" "1.10-beta3" package001.zip

**Activer** le package :

    azure batch application package activate "resgroup001" "batchaccount001" "MyTaskApplication" "1.10-beta3" zip

Définir la **version par défaut** pour l’application :

    azure batch application set "resgroup001" "batchaccount001" "MyTaskApplication" --default-version "1.10-beta3"

### <a name="deploy-an-application-package"></a>Déployer un package d’application

Vous pouvez spécifier une ou plusieurs des packages d’application pour le déploiement lorsque vous créez un nouveau pool. Lorsque vous spécifiez un package au moment de la création de pool, il est déployé sur chaque nœud que le pool de jointures nœud. Les packages sont également déployés lorsqu’un nœud est redémarré ou que.

Spécifier la `--app-package-ref` option lors de la création d’un pool pour déployer un package d’application sur les nœuds du pool dès qu’ils rejoignent le pool. La `--app-package-ref` option accepte une liste délimitée par des points-virgules d’ID d’application à déployer les nœuds de calcul.

    azure batch pool create --pool-id "pool001" --target-dedicated 1 --vm-size "small" --os-family "4" --app-package-ref "MyTaskApplication"

Lorsque vous créez une liste en utilisant les options de ligne de commande, vous ne pouvez pas actuellement spécifier *quelle* version de package d’application à déployer les nœuds de calcul, par exemple « 1.10-version bêta 3 ». Par conséquent, vous devez d’abord spécifier une version par défaut pour l’application avec `azure batch application set [options] --default-version <version-id>` avant de créer le pool (voir la section précédente). Vous pouvez toutefois, spécifier une version du package pour le pool si vous utilisez un [fichier JSON](#json-files) au lieu de la ligne de commande options lorsque vous créez le pool.

Vous trouverez plus d’informations sur les packages d’application dans [le déploiement d’applications avec Azure lot des packages d’application](batch-application-packages.md).

>[AZURE.IMPORTANT] Vous devez [lier un compte de stockage Azure](#linked-storage-account-autostorage) à votre compte lot à utiliser des packages d’application.

### <a name="update-a-pools-application-packages"></a>Mettre à jour des packages d’application d’un pool

Pour mettre à jour les applications attribuées à un pool existant, émettre la `azure batch pool set` commande avec le `--app-package-ref` option :

    azure batch pool set --pool-id "pool001" --app-package-ref "MyTaskApplication2"

Pour déployer le nouveau package d’application pour calculer les nœuds déjà présents dans un pool existant, vous devez redémarrer ou recréez ces nœuds :

    azure batch node reboot --pool-id "pool001" --node-id "tvm-3105992504_1-20160930t164509z"

>[AZURE.TIP] Vous pouvez obtenir une liste des nœuds dans un pool, ainsi que leur ID nœud, avec `azure batch node list`.

N’oubliez pas que vous devez déjà avoir configuré l’application avec une version par défaut avant le déploiement (`azure batch application set [options] --default-version <version-id>`).

## <a name="troubleshooting-tips"></a>Conseils de dépannage

Cette section est destinée à vous fournir les ressources à utiliser lors de la résolution des problèmes d’Azure infrastructure du langage commun. Il ne résoudra pas nécessairement tous les problèmes, mais cela peut vous aider à limiter la cause et vous indiquons des ressources d’aide.

* Utiliser `-h` pour obtenir le **texte d’aide** pour n’importe quelle commande infrastructure du langage commun

* Utiliser `-v` et `-vv` pour afficher le résultat de la commande **détaillée** ; `-vv` est détaillée « supplémentaires » et affiche les demandes REST et les réponses réel. Ces commutateurs sont utiles pour l’affichage de la sortie complète de l’erreur.

* Vous pouvez afficher le **résultat de la commande en tant que JSON** avec la `--json` option. Par exemple, `azure batch pool show "pool001" --json` affiche les propriétés de pool001 au format JSON. Vous pouvez ensuite copier et modifier cette sortie à utiliser dans un `--json-file` (voir [fichiers JSON](#json-files) plus haut dans cet article).

* Le [forum lot sur MSDN] [ batch_forum] est une ressource très utile et analysée par les membres de l’équipe lot. Veillez à publier vos questions il si vous rencontrez des problèmes ou voulez obtenir de l’aide d’une opération spécifique.

* Pas chaque opération de ressources par lots est pris en charge par l’infrastructure du langage commun Azure. Par exemple, vous ne pouvez pas spécifier actuellement une *version* de package l’application d’un pool, uniquement l’ID du package. Dans ce cas, vous devrez peut-être fournir un `--json-file` pour votre commande au lieu d’utiliser les options de ligne de commande. Veillez à maintenir à jour avec la dernière version d’infrastructure du langage commun choisir des améliorations futures.

## <a name="next-steps"></a>Étapes suivantes

*  Consultez [déploiement d’applications avec des packages d’application Azure lot](batch-application-packages.md) pour découvrir comment utiliser cette fonctionnalité pour gérer et déployer les applications que vous exécutez sur des nœuds de cluster lot.

* Voir [interroger le service lot efficacement](batch-efficient-list-queries.md) plus sur réduire le nombre d’éléments et le type d’informations qui sont renvoyées pour les requêtes au lot.

[batch_forum]: https://social.msdn.microsoft.com/forums/azure/en-US/home?forum=azurebatch
[github_readme]: https://github.com/Azure/azure-xplat-cli/blob/dev/README.md
[rest_api]: https://msdn.microsoft.com/library/azure/dn820158.aspx
[rest_add_pool]: https://msdn.microsoft.com/library/azure/dn820174.aspx
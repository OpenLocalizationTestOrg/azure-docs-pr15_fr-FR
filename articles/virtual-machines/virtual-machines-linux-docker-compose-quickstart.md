<properties
   pageTitle="Docker et composition sur une machine virtuelle | Microsoft Azure"
   description="Brève introduction à l’utilisation de la composition et Docker sur des machines virtuelles de Linux dans Azure"
   services="virtual-machines-linux"
   documentationCenter=""
   authors="iainfoulds"
   manager="timlt"
   editor=""
   tags="azure-resource-manager"/>

<tags
   ms.service="virtual-machines-linux"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure-services"
   ms.date="09/22/2016"
   ms.author="iainfou"/>

# <a name="get-started-with-docker-and-compose-to-define-and-run-a-multi-container-application-on-an-azure-virtual-machine"></a>Prise en main Docker et composition pour définir et exécuter une application conteneur à plusieurs sur une machine virtuelle Azure

Prise en main des Docker et [composition](http://github.com/docker/compose) pour définir et exécuter une application complexe sur une machine virtuelle Linux dans Azure. Avec la composition, vous utilisez un fichier texte simple pour définir une application qui se compose de plusieurs conteneurs Docker. Vous faire pivoter votre application dans une seule commande qui fait tout pour déployer votre environnement défini. 

Par exemple, cet article vous explique comment configurer rapidement un blog WordPress avec un base de données MariaDB SQL sur une Ubuntu VM le serveur principal. Vous pouvez également utiliser composition pour configurer les applications plus complexes.


## <a name="step-1-set-up-a-linux-vm-as-a-docker-host"></a>Étape 1 : Configurer un VM Linux en tant qu’un hôte Docker

Vous pouvez utiliser différentes procédures Azure et des images disponibles ou des modèles de gestionnaire de ressources sur le marché Azure pour créer un Linux VM et configurez-la comme un hôte Docker. Par exemple, voir [à l’aide de l’Extension de la machine virtuelle Docker pour déployer votre environnement](virtual-machines-linux-dockerextension.md) pour créer rapidement une Ubuntu VM avec l’extension Azure Docker machine virtuelle en utilisant un [modèle de démarrage rapide](https://github.com/Azure/azure-quickstart-templates/tree/master/docker-simple-on-ubuntu). 

Lorsque vous utilisez l’extension Docker machine virtuelle, votre ordinateur virtuel est configuré automatiquement en tant qu’un hôte Docker et composition est déjà installée. L’exemple de cet article vous montre comment utiliser [Azure interface de ligne de commande pour Mac, Linux et Windows](../xplat-cli-install.md) (la Azure) en mode de gestionnaire de ressources pour créer la machine virtuelle.

La commande base du document précédent crée un groupe de ressources nommé `myResourceGroup` dans les `West US` emplacement et déploie une machine virtuelle avec l’extension Azure Docker machine virtuelle installée :

```bash
azure group create --name myResourceGroup --location "West US" \
  --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/docker-simple-on-ubuntu/azuredeploy.json
```

## <a name="step-2-verify-that-compose-is-installed"></a>Étape 2 : Vérifier que le message est installé

Une fois que le déploiement est terminé, SSH à votre hôte Docker nouveau en utilisant le DNS nommez vous avez fourni pendant le déploiement. Vous pouvez utiliser `azure vm show -g myDockerResourceGroup -n myDockerVM` pour afficher les détails de votre machine virtuelle, y compris le nom DNS.

Pour vérifier que la composition est installée sur l’ordinateur virtuel, exécutez la commande suivante :

```bash
docker-compose --version
```

Vous voyez ressemblant à `docker-compose 1.6.2, build 4d72027`.

>[AZURE.TIP] Si vous utilisez une autre méthode pour créer un hôte Docker et que vous devez installer composition vous-même, consultez la [documentation de composition](https://github.com/docker/compose/blob/882dc673ce84b0b29cd59b6815cb93f74a6c4134/docs/install.md).


## <a name="step-3-create-a-docker-composeyml-configuration-file"></a>Étape 3 : Créer un fichier de configuration docker compose.yml

Ensuite, vous créez un `docker-compose.yml` fichier, qui est simplement un fichier texte de configuration, pour définir les conteneurs Docker exécution sur l’ordinateur virtuel. Le fichier Spécifie l’image à s’exécuter sur chaque conteneur (ou elle peut être une build à partir d’un Dockerfile), variables d’environnement nécessaires et les dépendances, les ports et les liens entre les conteneurs. Pour plus d’informations sur la syntaxe de fichier yml, voir [référence de fichier de composition](http://docs.docker.com/compose/yml/).

Créer la `docker-compose.yml` de fichiers comme suit :

```bash
touch docker-compose.yml
```

Utilisez votre éditeur de texte pour ajouter des données dans le fichier. L’exemple suivant utilise la `vi` éditeur :

```bash
vi docker-compose.yml
```

Collez l’exemple suivant dans votre fichier texte. Cette configuration utilise des images à partir du [Registre DockerHub](https://registry.hub.docker.com/_/wordpress/) installer WordPress (le libres blogs et le contenu système de gestion) et une base de données MariaDB SQL version serveur lié. Entrez votre propre `MYSQL_ROOT_PASSWORD` comme suit :

```bash
wordpress:
  image: wordpress
  links:
    - db:mysql
  ports:
    - 80:80

db:
  image: mariadb
  environment:
    MYSQL_ROOT_PASSWORD: <your password>
```

## <a name="step-4-start-the-containers-with-compose"></a>Étape 4 : Précédez les conteneurs de composition

Dans le même répertoire que votre `docker-compose.yml` fichier, exécutez la commande suivante (selon votre environnement, vous devrez peut-être exécuter `docker-compose` à l’aide de `sudo`.) :

```bash
docker-compose up -d

```

Cette commande démarre les conteneurs Docker spécifiés dans `docker-compose.yml`. Il faut une ou deux minutes pour cette étape Terminer. Vous voyez une sortie similaire à l’exemple suivant :

```bash
Creating wordpress_db_1...
Creating wordpress_wordpress_1...
...
```

>[AZURE.NOTE] Veillez à utiliser l’option **-d** au démarrage afin que les conteneurs de s’exécuter en arrière-plan en permanence.

Pour vérifier que les conteneurs sont vers le haut, tapez `docker-compose ps`. Vous devriez voir quelque chose comme :

```bash
Name             Command             State              Ports
-------------------------------------------------------------------------
wordpress_db_1     /docker-           Up                 3306/tcp
             entrypoint.sh
             mysqld
wordpress_wordpr   /entrypoint.sh     Up                 0.0.0.0:80->80
ess_1              apache2-for ...                       /tcp
```

Vous pouvez désormais être connecté à WordPress directement sur l’ordinateur virtuel sur le port 80. Ouvrez un navigateur web et entrez le nom DNS de votre ordinateur virtuel (tel que `http://myresourcegroup.westus.cloudapp.azure.com`). Normalement, le WordPress écran de démarrage, où vous pouvez terminer l’installation et commencer à utiliser l’application.

![Écran d’accueil WordPress][wordpress_start]


## <a name="next-steps"></a>Étapes suivantes

* Atteindre le [guide de l’utilisateur machine virtuelle Docker extension](https://github.com/Azure/azure-docker-extension/blob/master/README.md) pour plus d’options configurer votre machine virtuelle Docker Docker et composition. Par exemple, une option consiste à placer le fichier yml composition (converti en JSON) directement dans la configuration de l’extension Docker machine virtuelle.
* Consultez le [guide de l’utilisateur](http://docs.docker.com/compose/) pour obtenir des exemples de créer et déployer des applications conteneur multiples et de [référence de ligne de commande de composition](http://docs.docker.com/compose/reference/) .
* Utiliser un modèle Azure le Gestionnaire de ressources, soit votre propre ou un contribué à partir de la [Communauté](https://azure.microsoft.com/documentation/templates/), à déployer un ordinateur virtuel Azure avec Docker et une application configuré avec composition. Par exemple, le modèle [déployer un blog WordPress avec Docker](https://github.com/Azure/azure-quickstart-templates/tree/master/docker-wordpress-mysql) utilise Docker et composition pour déployer rapidement WordPress avec un serveur principal MySQL sur une VM Ubuntu.
* Essayez d’intégration Docker composer avec un cluster [Docker par essaim](virtual-machines-linux-docker-swarm.md) . Reportez-vous [à l’aide de composer avec essaim](https://docs.docker.com/compose/swarm/) scénarios.

<!--Image references-->

[wordpress_start]: ./media/virtual-machines-linux-docker-compose-quickstart/WordPress.png

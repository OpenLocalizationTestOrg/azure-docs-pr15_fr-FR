<properties
   pageTitle="Installer MongoDB sur une machine virtuelle Linux | Microsoft Azure"
   description="Découvrez comment installer et configurer MongoDB sur une machine virtuelle Linux dans Azure à l’aide du modèle de déploiement Gestionnaire de ressources."
   services="virtual-machines-linux"
   documentationCenter=""
   authors="iainfoulds"
   manager="timlt"
   editor=""/>

<tags
   ms.service="virtual-machines-linux"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure"
   ms.date="09/29/2016"
   ms.author="iainfou"/>

# <a name="install-and-configure-mongodb-on-a-linux-vm-in-azure"></a>Installer et configurer MongoDB sur une machine virtuelle Linux dans Azure
[MongoDB](http://www.mongodb.org) est une base de données NoSQL open source, High performance populaires. Cet article vous explique comment installer et configurer MongoDB sur un Linux VM dans Azure à l’aide du modèle de déploiement Gestionnaire de ressources. Exemples apparaissent ce détail comment à :

- [Installer et configurer une instance MongoDB base manuellement](#manually-install-and-configure-mongodb-on-a-vm)
- [Créer une instance MongoDB base à l’aide d’un modèle de gestionnaire de ressources](#create-basic-mongodb-instance-on-centos-using-a-template)
- [Créer un MongoDB complexe sharded cluster avec réplica définit à l’aide d’un modèle de gestionnaire de ressources](#create-a-complex-mongodb-sharded-cluster-on-centos-using-a-template)


## <a name="prerequisites"></a>Conditions préalables
Cet article suppose que les éléments suivants :

- un compte Azure ([Obtenez un essai gratuit](https://azure.microsoft.com/pricing/free-trial/)).
- l' [Infrastructure du langage commun Azure](../xplat-cli-install.md) connecté à l’aide`azure login`
- l’infrastructure du langage commun Azure *doit être* en mode Azure le Gestionnaire de ressources à l’aide`azure config mode arm`


## <a name="manually-install-and-configure-mongodb-on-a-vm"></a>Installez et configurez MongoDB sur un ordinateur virtuel manuellement
MongoDB [fournissent des instructions d’installation](https://docs.mongodb.com/manual/administration/install-on-linux/) pour Linux distros notamment chapeau rouge / CentOS, SUSE, Ubuntu et Debian. L’exemple suivant crée un `CoreOS` machine virtuelle à l’aide d’un code SSH stocké à `.ssh/azure_id_rsa.pub`. Répondez aux invites pour nom de compte de stockage, nom DNS et les informations d’identification d’administrateur :

```bash
azure vm quick-create --ssh-publickey-file .ssh/azure_id_rsa.pub --image-urn CentOS
```

Ouvrez une session sur l’ordinateur virtuel à l’aide de l’adresse IP affichée à la fin de l’étape de création d’une machine virtuelle précédente :

```bash
ssh ops@40.78.23.145
```

Pour ajouter les sources d’installation pour MongoDB, créez un `yum` fichier référentiel comme suit :

```bash
sudo touch /etc/yum.repos.d/mongodb-org-3.2.repo
```

Ouvrez le fichier mis en pension MongoDB à modifier. Ajoutez les lignes suivantes :

```bash
[mongodb-org-3.2]
name=MongoDB Repository
baseurl=https://repo.mongodb.org/yum/redhat/$releasever/mongodb-org/3.2/x86_64/
gpgcheck=1
enabled=1
gpgkey=https://www.mongodb.org/static/pgp/server-3.2.asc
```

Installer à l’aide de MongoDB `yum` comme suit :

```bash
sudo yum install -y mongodb-org
```

Par défaut, SELinux est appliquée aux images CentOS vous empêche d’accéder aux MongoDB. Installer les outils de gestion de stratégie et configurez SELinux pour autoriser MongoDB à employer comme suit sur son 27017 le port TCP par défaut. 

```bash
sudo yum install -y policycoreutils-python
sudo semanage port -a -t mongod_port_t -p tcp 27017
```

Démarrer le service MongoDB comme suit :

```bash
sudo service mongod start
```

Vérifiez l’installation MongoDB en vous connectant à l’aide de l’ordinateur local `mongo` client :

```bash
mongo
```

Maintenant tester l’instance MongoDB en ajoutant des données, puis recherche :

```
> db
test
> db.foo.insert( { a : 1 } )  
> db.foo.find()  
{ "_id" : ObjectId("57ec477cd639891710b90727"), "a" : 1 }
> exit
```

Si vous le souhaitez, configurez MongoDB démarre automatiquement pendant un redémarrage du système :

```bash
sudo chkconfig mongod on
```


## <a name="create-basic-mongodb-instance-on-centos-using-a-template"></a>Créer une base MongoDB instance sur CentOS à l’aide d’un modèle
Vous pouvez créer une instance de MongoDB base sur un ordinateur virtuel CentOS unique en utilisant le modèle de démarrage rapide Azure suivante à partir de Github. Ce modèle utilise l’extension de Script personnalisé pour Linux pour ajouter un `yum` référentiel à votre machine virtuelle CentOS nouvellement créé, puis installer MongoDB.

- [Instance MongoDB base sur CentOS](https://github.com/Azure/azure-quickstart-templates/tree/master/mongodb-on-centos) - https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/mongodb-on-centos/azuredeploy.json

L’exemple suivant crée un groupe de ressources avec le nom `myResourceGroup` dans les `WestUS` région. Entrez vos propres valeurs comme suit :

```bash
azure group create --name myResourceGroup --location WestUS \
    --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/mongodb-on-centos/azuredeploy.json
```

> [AZURE.NOTE] L’infrastructure du langage commun Azure vous renvoie à une invite de quelques secondes de la création du déploiement, mais l’installation et la configuration prend quelques minutes. Vérifier l’état du déploiement avec `azure group deployment show myResourceGroup`, entrer le nom de votre groupe de ressources en conséquence. Attendez que la `ProvisioningState` affiche « Réussite » avant d’essayer SSH de la machine virtuelle.

Une fois que le déploiement est terminé, SSH de la machine virtuelle. Obtenir l’adresse IP de votre machine virtuelle à l’aide du `azure vm show` commande comme dans l’exemple suivant :

```bash
azure vm show --resource-group myResourceGroup --name myVM
```

À la fin de la sortie, le `Public IP address` s’affiche. SSH à votre machine virtuelle avec l’adresse IP de votre ordinateur virtuel :

```bash
ssh ops@138.91.149.74
```

Vérifiez l’installation MongoDB en vous connectant à l’aide de l’ordinateur local `mongo` client comme suit :

```bash
mongo
```

Maintenant tester l’instance en ajoutant des données et de recherche comme suit :

```
> db
test
> db.foo.insert( { a : 1 } )  
> db.foo.find()  
{ "_id" : ObjectId("57ec477cd639891710b90727"), "a" : 1 }
> exit
```


## <a name="create-a-complex-mongodb-sharded-cluster-on-centos-using-a-template"></a>Créer un Cluster Sharded MongoDB complexes sur CentOS à l’aide d’un modèle
Vous pouvez créer un cluster sharded MongoDB complexe en utilisant le modèle de démarrage rapide Azure suivant à partir de Github. Ce modèle respecte les [recommandations de MongoDB cluster sharded](https://docs.mongodb.com/manual/core/sharded-cluster-components/) pour fournir une redondance et disponibilité. Le modèle crée deux milieu des fragments, avec trois nœuds dans chaque ensemble réplica. Une réplique du serveur de configuration de la définir avec trois nœuds est également créée, plus deux `mongos` à rendent cohérentes au milieu des fragments aux applications à partir des serveurs de routeur.

- [MongoDB ont Cluster sur CentOS](https://github.com/Azure/azure-quickstart-templates/tree/master/mongodb-sharding-centos) - https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/mongodb-sharding-centos/azuredeploy.json

> [AZURE.WARNING] Déploiement de ce cluster sharded MongoDB complexe requiert plus de 20 cœurs, qui est généralement le nombre de core par défaut par région pour un abonnement. Ouvrez une demande de support Azure pour augmenter le nombre de base.

L’exemple suivant crée un groupe de ressources avec le nom `myResourceGroup` dans les `WestUS` région. Entrez vos propres valeurs comme suit :

```bash
azure group create --name myResourceGroup --location WestUS \
    --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/mongodb-sharding-centos/azuredeploy.json
```

> [AZURE.NOTE] L’infrastructure du langage commun Azure vous renvoie à une invite de quelques secondes de la création du déploiement, mais l’installation et la configuration peuvent prendre plus d’une heure. Vérifier l’état du déploiement avec `azure group deployment show myResourceGroup`, le nom de votre groupe de ressources en ajustant en conséquence. Attendez que la `ProvisioningState` affiche « Réussite » avant de vous connecter aux ordinateurs virtuels.


## <a name="next-steps"></a>Étapes suivantes
Dans ces exemples, vous connectez à l’instance MongoDB localement à partir de la machine virtuelle. Si vous voulez vous connecter à l’instance MongoDB à partir d’une autre machine virtuelle ou réseau, vérifiez la approprié [règles du groupe de sécurité réseau sont créées](virtual-machines-linux-nsg-quickstart.md).

Pour plus d’informations sur la création de l’utilisation des modèles, consultez la [vue d’ensemble du Gestionnaire de ressources Azure](../azure-resource-manager/resource-group-overview.md).

Les modèles de gestionnaire de ressources Azure utilisent l’Extension de Script personnalisé pour télécharger et exécuter des scripts sur vos ordinateurs virtuels. Pour plus d’informations, reportez-vous [à l’aide de l’Extension de Script personnalisé Azure avec le Machines virtuelles Linux](virtual-machines-linux-extensions-customscript.md).
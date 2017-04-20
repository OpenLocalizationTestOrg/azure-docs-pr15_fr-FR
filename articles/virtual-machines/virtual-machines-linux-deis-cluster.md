<properties
   pageTitle="Déployer un nœud 3 Deis cluster | Microsoft Azure"
   description="Cet article décrit comment créer un nœud 3 Deis cluster sur Azure à l’aide d’un modèle de gestionnaire de ressources Azure"
   services="virtual-machines-linux"
   documentationCenter=""
   authors="HaishiBai"
   manager="timlt"
   editor=""
   tags="azure-resource-manager"/>

<tags
   ms.service="virtual-machines-linux"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure-services"
   ms.date="06/24/2015"
   ms.author="hbai"/>

# <a name="deploy-a-3-node-deis-cluster"></a>Déployer un nœud 3 Deis cluster

Cet article vous guide dans la mise en service un [Deis](http://deis.io/) cluster sur Azure. Il traite les différentes étapes des certificats requis pour le déploiement et la mise à l’échelle d’un exemple **accédez** d’application sur le cluster mis en service que vous venez de créer.

Le diagramme suivant illustre l’architecture du système déployé. Un administrateur système gère le cluster à l’aide Deis outils tels que **deis** et **deisctl**. Connexions sont établies via un équilibrage de charge Azure, qui transfère les connexions à l’un du membre nœuds sur le cluster. Le niveau d’accès clients déployé applications via l’équilibrage de charge également. Dans ce cas, l’équilibrage de charge transfère le trafic vers un Deis maillage routeur qui davantage achemine le trafic vers conteneurs Docker correspondants hébergé sur le cluster.

  ![Diagramme d’architecture de cluster Desis déployé](media/virtual-machines-linux-deis-cluster/architecture-overview.png)

Pour exécuter les étapes suivantes, vous devez :

 * Un abonnement Azure actif. Si vous n’en avez pas, vous pouvez obtenir un gratuit sur [azure.com](https://azure.microsoft.com/).
 * Un id scolaire ou utilisation des ressources Azure groupes. Si vous avez un compte personnel et connectez-vous à l’aide d’un id Microsoft, vous devez [créer un id de travail de votre enregistrement personnel](virtual-machines-windows-create-aad-work-id.md).
 * Des options--selon votre système d’exploitation de client-- [Azure PowerShell](../powershell-install-configure.md) ou l' [Infrastructure du langage commun pour Mac, Linux et Windows Azure](../xplat-cli-install.md).
 * [OpenSSL](https://www.openssl.org/). OpenSSL est utilisé pour générer les certificats nécessaires.
 * Un client Git comme [Git Bash](https://git-scm.com/).
 * Pour tester l’exemple d’application, vous devez également un serveur DNS. Vous pouvez utiliser les serveurs DNS ou les services qui prennent en charge des enregistrements de caractères génériques.
 * Un ordinateur pour exécuter Deis les outils clients. Vous pouvez utiliser un ordinateur local ou un ordinateur virtuel. Vous pouvez exécuter ces outils sur presque n’importe quelle distribution Linux, mais les instructions suivantes utilisent Ubuntu.

## <a name="provision-the-cluster"></a>Mise en service le cluster

Dans cette section, vous allez utiliser un modèle [Azure le Gestionnaire de ressources](../azure-resource-manager/resource-group-overview.md) à partir des libres référentiel [modèles de démarrage rapide azure](https://github.com/Azure/azure-quickstart-templates). Tout d’abord, vous allez copier vers le bas le modèle. Ensuite, vous devez créer une nouvelle paire de clés SSH pour l’authentification. Et ensuite, vous devez configurer un nouvel identifiant pour vous cluster. Et enfin, vous allez utiliser le script Shell ou le script PowerShell pour le cluster de mise en service.

1. Cloner le référentiel : [https://github.com/Azure/azure-quickstart-templates](https://github.com/Azure/azure-quickstart-templates).

        git clone https://github.com/Azure/azure-quickstart-templates

2. Accédez au dossier modèle :

        cd azure-quickstart-templates\deis-cluster-coreos

3. Créer une nouvelle paire de clés SSH à l’aide de ssh keygen :

        ssh-keygen -t rsa -b 4096 -c "[your_email@domain.com]"

4. Générer un certificat à l’aide de la clé privée ci-dessus :

        openssl req -x509 -days 365 -new -key [your private key file] -out [cert file to be generated]

5. Accédez à [https://discovery.etcd.io/new](https://discovery.etcd.io/new) pour générer un nouveau jeton de cluster, qui ressemble quelque chose comme :

        https://discovery.etcd.io/6a28e078895c5ec737174db2419bb2f3
<br />
Chaque cluster CoreOS doit comporter un jeton unique à partir de ce service gratuit. Pour plus d’informations, consultez [documentation CoreOS](https://coreos.com/docs/cluster-management/setup/cluster-discovery/) .

6. Modifiez le fichier **cloud config.yaml** pour remplacer le jeton de **découverte** existant par le nouveau jeton :

        #cloud-config
        ---
        coreos:
          etcd:
            # generate a new token for each unique cluster from https://discovery.etcd.io/new
            # uncomment the following line and replace it with your discovery URL
            discovery: https://discovery.etcd.io/3973057f670770a7628f917d58c2208a
        ...

7. Modifier le fichier **azuredeploy parameters.json** : ouvrez le certificat que vous avez créé à l’étape 4 dans un éditeur de texte. Copier tout le texte entre `----BEGIN CERTIFICATE-----` et `-----END CERTIFICATE-----` dans le paramètre **sshKeyData** (vous devez supprimer tous les caractères de saut de ligne).

8. Modifier le paramètre **newStorageAccountName** . Il s’agit du compte de stockage des disques machine virtuelle du système d’exploitation. Nom de ce compte doit être unique.

9. Modifier le paramètre **publicDomainName** . Cela va faire partie du nom DNS associé à l’adresse IP publique d’équilibrage de charge. Le nom de domaine complet final ont le format de _[valeur de ce paramètre]_. _[Région]_. cloudapp.azure.com. Par exemple, si vous spécifiez le nom comme deishbai32 et le groupe de ressources est déployé à la région Ouest américain, le nom de domaine complet finale à votre programme d’équilibrage de charge sera deishbai32.westus.cloudapp.azure.com.

10. Enregistrez le fichier de paramètres. Et ensuite, vous pouvez configurer le cluster à l’aide de PowerShell Azure :

        .\deploy-deis.ps1 -ResourceGroupName [resource group name] -ResourceGroupLocation "West US" -TemplateFile
        .\azuredeploy.json -ParametersFile .\azuredeploy-parameters.json -CloudInitFile .\cloud-config.yaml

  ou Azure infrastructure du langage commun :

        ./deploy-deis.sh -n "[resource group name]" -l "West US" -f ./azuredeploy.json -e ./azuredeploy-parameters.json
        -c ./cloud-config.yaml  

11. Une fois que le groupe de ressources est configuré, vous voyez toutes les ressources dans le groupe sur Azure portail classique. Comme indiqué dans l’écran suivant, le groupe de ressources contient un réseau virtuel avec trois machines virtuelles, qui sont jointes au même jeu de disponibilité. Le groupe contient également un équilibrage de charge qui comporte une adresse IP publique associé.

  ![Le groupe de ressources généré sur Azure portail classique](media/virtual-machines-linux-deis-cluster/resource-group.png)

## <a name="install-the-client"></a>Installer le client

Vous devez **deisctl** au contrôle votre Deis cluster. Bien que deisctl est installée automatiquement dans tous les nœuds de cluster, il est recommandé d’utiliser deisctl sur un ordinateur d’administration distinct. En outre, étant donné que tous les nœuds sont configurés avec uniquement les adresses IP privées, vous devez utiliser le protocole SSH tunnel via l’équilibrage de charge qui comporte une adresse IP publique, pour vous connecter aux ordinateurs nœud. Voici les étapes de configuration deisctl sur un Ubuntu distincte physique ou machine virtuelle.

1. Installer deisctl:mkdir deis

        cd deis
        curl -sSL http://deis.io/deisctl/install.sh | sh -s 1.6.1
        sudo ln -fs $PWD/deisctl /usr/local/bin/deisctl

2. Ajoutez votre clé privée ssh agent :

        eval `ssh-agent -s`
        ssh-add [path to the private key file, see step 1 in the previous section]

3. Configurer deisctl :

        export DEISCTL_TUNNEL=[public ip of the load balancer]:2223

Le modèle définit les règles de trafic entrant NAT qui sont mappées 2223 à 1, 2224 à l’instance 2 et 2225 à 3 instance d’instance. Cela permet redondance à l’aide de l’outil deisctl. Vous pouvez examiner les règles suivantes dans Azure portail classique :

![Règles NAT sur l’équilibrage de charge](media/virtual-machines-linux-deis-cluster/nat-rules.png)

> [AZURE.NOTE] Le modèle seulement prend en charge 3 nœuds. Il s’agit en raison d’une limitation dans le Gestionnaire de ressources Azure modèle NAT définition de la règle, qui ne prend pas en charge syntaxe de boucle.

## <a name="install-and-start-the-deis-platform"></a>Installez et démarrez la Deis plate-forme

Vous pouvez désormais utiliser deisctl pour installer et démarrer le Deis plateforme :

    deisctl config platform set domain=[some domain]
    deisctl config platform set sshPrivateKey=[path to the private key file]
    deisctl install platform
    deisctl start platform

> [AZURE.NOTE] Démarrage de la plateforme prend un certain temps (autant que 10 minutes). En particulier, le démarrage du service du Générateur de rapports peut prendre beaucoup de temps. Et parfois nécessaire quelques essaie de réussite : si l’opération semble se bloquer, essayez de taper `ctrl+c` pour arrêter l’exécution de la commande, puis réessayez.

Vous pouvez utiliser `deisctl list` pour vérifier si tous les services en cours d’exécution :

    deisctl list
    UNIT                            MACHINE                 LOAD    ACTIVE          SUB
    deis-builder.service            ebe3005e.../10.0.0.6    loaded  active          running
    deis-controller.service         ebe3005e.../10.0.0.6    loaded  active          running
    deis-database.service           9c79bbdd.../10.0.0.5    loaded  active          running
    deis-logger.service             9c79bbdd.../10.0.0.5    loaded  active          running
    deis-logspout.service           8d658d5a.../10.0.0.4    loaded  active          running
    deis-logspout.service           9c79bbdd.../10.0.0.5    loaded  active          running
    deis-logspout.service           ebe3005e.../10.0.0.6    loaded  active          running
    deis-publisher.service          8d658d5a.../10.0.0.4    loaded  active          running
    deis-publisher.service          9c79bbdd.../10.0.0.5    loaded  active          running
    deis-publisher.service          ebe3005e.../10.0.0.6    loaded  active          running
    deis-registry@1.service         ebe3005e.../10.0.0.6    loaded  active          running
    deis-router@1.service           ebe3005e.../10.0.0.6    loaded  active          running
    deis-router@2.service           8d658d5a.../10.0.0.4    loaded  active          running
    deis-router@3.service           9c79bbdd.../10.0.0.5    loaded  active          running
    deis-store-daemon.service       8d658d5a.../10.0.0.4    loaded  active          running
    deis-store-daemon.service       9c79bbdd.../10.0.0.5    loaded  active          running
    deis-store-daemon.service       ebe3005e.../10.0.0.6    loaded  active          running
    deis-store-gateway@1.service    9c79bbdd.../10.0.0.5    loaded  active          running
    deis-store-metadata.service     8d658d5a.../10.0.0.4    loaded  active          running
    deis-store-metadata.service     9c79bbdd.../10.0.0.5    loaded  active          running
    deis-store-metadata.service     ebe3005e.../10.0.0.6    loaded  active          running
    deis-store-monitor.service      8d658d5a.../10.0.0.4    loaded  active          running
    deis-store-monitor.service      9c79bbdd.../10.0.0.5    loaded  active          running
    deis-store-monitor.service      ebe3005e.../10.0.0.6    loaded  active          running
    deis-store-volume.service       8d658d5a.../10.0.0.4    loaded  active          running
    deis-store-volume.service       9c79bbdd.../10.0.0.5    loaded  active          running
    deis-store-volume.service       ebe3005e.../10.0.0.6    loaded  active          running

Félicitations ! À présent que vous avez cumulé Deis clsuter sur Azure ! Ensuite, nous allons déployer un échantillon application atteindre pour afficher le cluster en action.

## <a name="deploy-and-scale-a-hello-world-application"></a>Déployer et mettre à l’échelle une application Hello World

Les étapes suivantes montrent comment déployer un « Hello World » accédez application pour le cluster. Les étapes sont basées sur [Deis documentation](http://docs.deis.io/en/latest/using_deis/using-dockerfiles/#using-dockerfiles).

1. Pour le routage maillage fonctionne correctement, vous devez disposer d’un enregistrement A génériques pour votre domaine en pointant sur l’adresse IP publique de l’équilibrage de charge. La capture d’écran suivante montre l’enregistrement A un exemple d’enregistrement des domaines sur GoDaddy :

    ![Enregistrement GoDaddy A](media/virtual-machines-linux-deis-cluster/go-daddy.png)
<p />
2. Installation deis :

        mkdir deis
        cd deis
        curl -sSL http://deis.io/deis-cli/install.sh | sh
        ln -fs $PWD/deis /usr/local/bin/deis
        
3. Créer une nouvelle clé SSH et ajoutez la clé publique à GitHub (bien entendu, vous pouvez également réutiliser vos clés existantes). Pour créer une nouvelle paire de clés SSH, utilisez :

        cd ~/.ssh
        ssh-keygen (press [Enter]s to use default file names and empty passcode)

4. Ajouter id_rsa.pub ou la clé publique de votre choix, à GitHub. Vous pouvez effectuer ceci en utilisant le bouton clé ajouter SSH dans l’écran de configuration de touches SSH :

  ![Clé Github](media/virtual-machines-linux-deis-cluster/github-key.png)
<p />
5. Enregistrer un nouvel utilisateur :

        deis register http://deis.[your domain]
<p />
6. Ajouter le code SSH :

        deis keys:add [path to your SSH public key]
  <p />      
7. Créer une application.

        git clone https://github.com/deis/helloworld.git
        cd helloworld
        deis create
        git push deis master
<p />
8.La diffusion git se déclenche images Docker pour être généré et déployé, qui peut prendre quelques minutes. À partir de mon expérience, il peut arriver que, étape 10 (push image vers le référentiel privé) peut se bloquer. Dans ce cas, vous pouvez arrêter le processus, supprimer l’application à l’aide ' deis applications : destroy – <application name> ` to remove the application and try again. You can use `deis apps:list » pour trouver le nom de votre application. Si tout fonctionne, vous devriez voir les éléments suivants à la fin des sorties de commande :

        -----> Launching...
               done, lambda-underdog:v2 deployed to Deis
               http://lambda-underdog.artitrack.com
               To learn more, use `deis help` or visit http://deis.io
        To ssh://git@deis.artitrack.com:2222/lambda-underdog.git
         * [new branch]      master -> master
<p />
9. Vérifiez si l’application fonctionne :

        curl -S http://[your application name].[your domain]
  Vous devriez voir :

        Welcome to Deis!
        See the documentation at http://docs.deis.io/ for more information.
        (you can use geis apps:list to get the name of your application).
<p />
10. Échelle de 3 instances de l’application :

        deis scale cmd=3
<p />
11. Si vous le souhaitez, vous pouvez utiliser deis informations pour examiner les détails de votre application. Les sorties suivantes sont à partir de mon déploiement de l’application :

        deis info
        === lambda-underdog Application
        {
          "updated": "2015-05-22T06:14:10UTC",
          "uuid": "10c74ee7-b7ff-4786-967a-7e65af7eabc3",
          "created": "2015-05-22T06:07:55UTC",
          "url": "lambda-underdog.artitrack.com",
          "owner": "haishi",
          "id": "lambda-underdog",
          "structure": {
            "cmd": 3
          }
        }

        === lambda-underdog Processes
        --- cmd:
        cmd.1 up (v2)
        cmd.2 up (v2)
        cmd.3 up (v2)

        === lambda-underdog Domains
        No domains

## <a name="next-steps"></a>Étapes suivantes

Cet article aborde toutes les étapes de mise en service un nouveau Deis cluster sur Azure à l’aide d’un modèle Azure le Gestionnaire de ressources. Le modèle prend en charge redondance connexions ainsi que pour les applications déployées d’équilibrage de charge des outils. Le modèle permet également d’éviter à l’aide d’adresses IP public sur des nœuds de membre, qui enregistre précieux ressources IP publics et fournit un environnement plus sécurisé d’héberger des applications. Pour plus d’informations, consultez les articles suivants :

[Vue d’ensemble du Gestionnaire de ressources Azure] [resource-group-overview]  
[L’utilisation de l’infrastructure du langage commun Azure] [azure-command-line-tools]  
[À l’aide de PowerShell Azure avec le Gestionnaire de ressources Azure] [powershell-azure-resource-manager]  

[azure-command-line-tools]: ../xplat-cli-install.md
[resource-group-overview]: ../azure-resource-manager/resource-group-overview.md
[powershell-azure-resource-manager]: ../powershell-azure-resource-manager.md

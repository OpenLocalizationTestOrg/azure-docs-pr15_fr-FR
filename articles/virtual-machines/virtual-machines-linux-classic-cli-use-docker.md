<properties
    pageTitle="À l’aide de l’Extension de la machine virtuelle Docker pour Linux sur Azure"
    description="Décrit Docker et les extensions de Machines virtuelles Azure et montre comment créer par programme des Machines virtuelles sur Azure qui sont des hôtes docker à partir de la ligne de commande à l’aide de l’infrastructure du langage commun Azure."
    services="virtual-machines-linux"
    documentationCenter=""
    authors="squillace"
    manager="timlt"
    editor="tysonn"
    tags="azure-service-management"/>

<tags
    ms.service="virtual-machines-linux"
    ms.devlang="multiple"
    ms.topic="article"
    ms.tgt_pltfrm="vm-linux"
    ms.workload="infrastructure-services"
    ms.date="08/29/2016"
    ms.author="rasquill"/>

# <a name="using-the-docker-vm-extension-from-the-azure-command-line-interface-azure-cli"></a>À l’aide de l’Extension de la machine virtuelle Docker à partir de la ligne de commande Azure Interface (Azure infrastructure du langage commun)

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]



Cette rubrique décrit comment créer une machine virtuelle avec l’Extension de la machine virtuelle Docker depuis le mode d’administration (asm) service dans Azure infrastructure du langage commun sur n’importe quelle plate-forme. [Docker](https://www.docker.com/) est une des approches virtualisation les plus populaires qui utilise des [conteneurs de Linux](http://en.wikipedia.org/wiki/LXC) plutôt que machines virtuelles comme un moyen d’isoler les données et de l’informatique sur les ressources partagées. Vous pouvez utiliser l’extension Docker machine virtuelle et l' [Agent de Linux Azure](virtual-machines-linux-agent-user-guide.md) pour créer une machine virtuelle Docker qui héberge un nombre quelconque de conteneurs de vos applications sur Azure. Pour obtenir une description détaillée des conteneurs et leurs avantages, consultez le [Tableau blanc de niveau élevé Docker](http://channel9.msdn.com/Blogs/Regular-IT-Guy/Docker-High-Level-Whiteboard).


##<a name="how-to-use-the-docker-vm-extension-with-azure"></a>Comment utiliser l’Extension de la machine virtuelle Docker avec Azure
Pour utiliser l’extension Docker machine virtuelle avec Azure, vous devez installer une version de l' [Interface de ligne de commande Azure](https://github.com/Azure/azure-sdk-tools-xplat) de Azure supérieurs 0.8.6 (comme de la rédaction la version actuelle est 0.10.0). Vous pouvez installer l’infrastructure du langage commun Azure sur Mac, Linux et Windows.


L’ensemble du processus d’utiliser Docker sur Azure est simple :

+ Installer l’infrastructure du langage commun Azure et ses dépendances sur l’ordinateur à partir duquel vous souhaitez contrôler Azure (sous Windows, il s’agit une distribution Linux fonctionne comme une machine virtuelle)
+ Utilisez les commandes Azure infrastructure du langage commun Docker pour créer un hôte Docker machine virtuelle dans Azure
+ Utiliser les commandes Docker locales pour gérer vos conteneurs Docker dans votre machine virtuelle Docker dans Azure.


### <a name="install-the-azure-command-line-interface-azure-cli"></a>Installer le Azure Interface de ligne (commande Azure)

Pour installer et configurer l’infrastructure du langage commun Azure, Découvrez [comment installer l’Interface de ligne de commande Azure](../xplat-cli-install.md). Pour confirmer l’installation, tapez `azure` à l’invite de commande et après quelques instants courte vous devriez voir l’art Azure infrastructure du langage commun ASCII, qui répertorie les commandes de base à votre disposition. Si l’installation fonctionne correctement, vous devez être en mesure de taper `azure help vm` et si un de la liste de commandes est « docker ».

> [AZURE.NOTE] Docker dispose d’outils pour Windows, [Docker Machine](https://docs.docker.com/installation/windows/), que vous pouvez également utiliser pour automatiser la création d’un client docker que vous pouvez utiliser pour l’utiliser avec Azure machines virtuelles en tant qu’hôtes docker.

### <a name="connect-the-azure-cli-to-to-your-azure-account"></a>Connecter l’infrastructure du langage commun Azure à votre compte Azure
Avant de pouvoir utiliser l’infrastructure du langage commun Azure vous devez associer vos informations d’identification de compte Azure à l’infrastructure du langage commun Azure sur votre plateforme. La section [comment se connecter à votre abonnement Azure](../xplat-cli-connect.md) explique comment télécharger et importer votre fichier **.publishsettings** ou associer votre infrastructure du langage commun Azure un id d’organisation.

> [AZURE.NOTE] Il existe quelques différences de comportement lorsque vous utilisez une ou les autres méthodes d’authentification, afin de veillez à lire le document ci-dessus pour comprendre les fonctionnalités différentes.

### <a name="install-docker-and-use-the-docker-vm-extension-for-azure"></a>Installer Docker et utiliser l’Extension de la machine virtuelle Docker pour Azure
Suivez les [instructions d’installation Docker](https://docs.docker.com/installation/#installation) pour installer Docker localement sur votre ordinateur.

Pour utiliser Docker avec une Machine virtuelle Azure, l’image Linux utilisée pour la machine virtuelle doit être l' [Agent de machine virtuelle Linux Azure](virtual-machines-linux-agent-user-guide.md) est installé. Pour l’instant, il existe uniquement deux types d’images fournissant ceci :

+ Une image Ubuntu à partir de la galerie d’images Azure ou

+ Une image Linux personnalisée que vous avez créés avec l’Agent de machine virtuelle Linux Azure installé et configuré. Pour plus d’informations sur la création d’un VM Linux personnalisé avec l’Agent de machine virtuelle Azure, consultez [Azure Linux machine virtuelle Agent](virtual-machines-linux-agent-user-guide.md) .

### <a name="using-the-azure-image-gallery"></a>À l’aide de la galerie d’images Azure

À partir d’un Bash ou session Terminal Server, utilisez la commande Azure infrastructure du langage commun suivante pour rechercher l’image Ubuntu plus récente dans la galerie machine virtuelle à utiliser en tapant

`azure vm image list | grep Ubuntu-14_04`

Sélectionnez une des noms image, tel que `b39f27a8b8c64d52b05eac6a62ebad85__Ubuntu-14_04_4-LTS-amd64-server-20160516-en-us-30GB`et utilisez la commande suivante pour créer une nouvelle machine virtuelle à l’aide de cette image.

```
azure vm docker create -e 22 -l "West US" <vm-cloudservice name> "b39f27a8b8c64d52b05eac6a62ebad85__Ubuntu-14_04_4-LTS-amd64-server-20160516-en-us-30GB" <username> <password>
```

Dans cet exemple :

+ * &lt;nom machine virtuelle cloudservice&gt; * est le nom de la machine virtuelle qui sera utilisée comme l’ordinateur hôte du conteneur Docker dans Azure

+  * &lt;nom d’utilisateur&gt; * est le nom d’utilisateur de l’utilisateur racine par défaut de la machine virtuelle

+ * &lt;mot de passe&gt; * est le mot de passe du compte *nom d’utilisateur* qui répond aux normes de complexité pour Azure

> [AZURE.NOTE] Pour l’instant, un mot de passe doit comporter au moins 8 caractères, contenir minuscule et une majuscule, un nombre et un caractère spécial tel qu’un des caractères suivants : `!@#$%^&+=`. Non, la période à la fin de la phrase précédente n’est pas un caractère spécial.

Si la commande a réussi, vous devriez voir les éléments suivants, selon les arguments précis et les options que vous avez utilisé :

![](./media/virtual-machines-linux-classic-cli-use-docker/dockercreateresults.png)

> [AZURE.NOTE] Création d’une machine virtuelle peut prendre quelques minutes, mais une fois qu’il a été configuré (la valeur d’état est `ReadyRole`) le processus Docker (le service Docker) démarre et vous pouvez vous connecter à l’hôte de conteneur Docker.

Pour tester la machine virtuelle Docker que vous avez créé dans Azure, tapez

`docker --tls -H tcp://<vm-name-you-used>.cloudapp.net:2376 info`

où * &lt;machine virtuelle-nom-vous-utilisé&gt; * est le nom de la machine virtuelle que vous avez utilisé dans votre appel à `azure vm docker create`. Vous devriez voir doit ressembler à la suivante, qui indique que votre ordinateur virtuel hôte Docker est vers le haut et en cours d’exécution dans Azure et en attente pour vos commandes. 

À présent, vous pouvez tenter de vous connecter à l’aide de votre client docker pour obtenir des informations (dans certaines configurations de client Docker, tel que celui sur Mac, vous devrez peut-être utiliser `sudo`) :

    sudo docker --tls -H tcp://testsshasm.cloudapp.net:2376 info
    Password:
    Containers: 0
    Images: 0
    Storage Driver: devicemapper
    Pool Name: docker-8:1-131781-pool
    Pool Blocksize: 65.54 kB
    Backing Filesystem: extfs
    Data file: /dev/loop0
    Metadata file: /dev/loop1
    Data Space Used: 1.821 GB
    Data Space Total: 107.4 GB
    Data Space Available: 28 GB
    Metadata Space Used: 1.479 MB
    Metadata Space Total: 2.147 GB
    Metadata Space Available: 2.146 GB
    Udev Sync Supported: true
    Deferred Removal Enabled: false
    Data loop file: /var/lib/docker/devicemapper/devicemapper/data
    Metadata loop file: /var/lib/docker/devicemapper/devicemapper/metadata
    Library Version: 1.02.77 (2012-10-15)
    Execution Driver: native-0.2
    Logging Driver: json-file
    Kernel Version: 3.19.0-28-generic
    Operating System: Ubuntu 14.04.3 LTS
    CPUs: 1
    Total Memory: 1.637 GiB
    Name: testsshasm
    WARNING: No swap limit support

Pour être sûr qu’il s’agit d’utilisation, vous pouvez examiner la machine virtuelle pour l’extension Docker :

    azure vm extension get testsshasm
    info: Executing command vm extension get
    + Getting virtual machines
    data: Publisher Extension name ReferenceName Version State
    data: -------------------- --------------- ------------------------- ------- ------
    data: Microsoft.Azure.E... DockerExtension DockerExtension 1.* Enable
    info: vm extension get command OK

### <a name="docker-host-vm-authentication"></a>Machine virtuelle authentification de l’hôte docker

Outre la création de la machine virtuelle Docker, la `azure vm docker create` commande crée automatiquement les certificats nécessaires pour permettre à votre ordinateur client Docker pour vous connecter à l’hôte de conteneur Azure à l’aide de HTTPS, et les certificats sont stockées sur le client et l’hôte machines, le cas échéant. Sous autres tentatives, les certificats existants sont réutilisés et partagées avec le nouvel hôte.

Par défaut, les certificats sont placées dans `~/.docker`, et Docker sera configuré pour s’exécuter sur port **2376**. Si vous voulez utiliser un autre port ou un répertoire, puis vous pouvez utiliser une des opérations suivantes `azure vm docker create` options de ligne de commande pour configurer votre conteneur Docker hébergent machine virtuelle pour utiliser un autre port ou certificats différents pour les clients qui se connectent :

```
-dp, --docker-port [port]              Port to use for docker [2376]
-dc, --docker-cert-dir [dir]           Directory containing docker certs [.docker/]
```

Le processus Docker sur l’hôte est configuré pour écouter et authentifier les connexions client sur le port spécifié à l’aide de ces certificats générés par le `azure vm docker create` commande. L’ordinateur client doit disposer de ces certificats pour accéder à l’hôte Docker.

> [AZURE.NOTE] Un hôte du réseau en cours d’exécution sans ces certificats peuvent être à toutes les personnes qui peuvent pour vous connecter à l’ordinateur. Avant de modifier la configuration par défaut, assurez-vous de comprendre les risques liés à vos ordinateurs et applications.

## <a name="next-steps"></a>Étapes suivantes

* Vous êtes prêt à accéder au [Guide de l’utilisateur Docker] et utiliser votre machine virtuelle Docker. Pour créer une machine virtuelle compatibles avec les Docker dans le nouveau portail, voir [comment utiliser l’Extension de la machine virtuelle Docker grâce au portail].

* L’extension Azure Docker machine virtuelle prend également en charge composer Docker, qui utilise un fichier YAML déclaratif pour prendre une application basés sur un développeur dans tous les environnements et générer un déploiement cohérent. Voir [Prise en main Docker et composition pour définir et exécuter une application conteneur à plusieurs sur une machine virtuelle Azure].  

<!--Anchors-->
[Subheading 1]: #subheading-1
[Subheading 2]: #subheading-2
[Subheading 3]: #subheading-3
[Next steps]: #next-steps

[How to use the Docker VM Extension with Azure]: #How-to-use-the-Docker-VM-Extension-with-Azure
[Virtual Machine Extensions for Linux and Windows]: #Virtual-Machine-Extensions-For-Linux-and-Windows
[Container and Container Management Resources for Azure]: #Container-and-Container-Management-Resources-for-Azure



<!--Link references-->
[Link 1 to another azure.microsoft.com documentation topic]: virtual-machines-windows-hero-tutorial.md
[Link 2 to another azure.microsoft.com documentation topic]: ../web-sites-custom-domain-name.md
[Link 3 to another azure.microsoft.com documentation topic]: ../storage-whatis-account.md
[Comment utiliser l’Extension de la machine virtuelle Docker avec le portail]: http://azure.microsoft.com/documentation/articles/virtual-machines-docker-with-portal/

[Guide de l’utilisateur docker]: https://docs.docker.com/userguide/
 
[Prise en main Docker et composition pour définir et exécuter une application conteneur à plusieurs sur une machine virtuelle Azure]:virtual-machines-linux-docker-compose-quickstart.md
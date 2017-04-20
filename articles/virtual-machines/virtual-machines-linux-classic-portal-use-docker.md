<properties
    pageTitle="À l’aide de l’Extension Docker machine virtuelle pour Linux | Microsoft Azure"
    description="Décrit Docker et les extensions de Machines virtuelles Azure et comment créer des Machines virtuelles Azure qui sont des hôtes docker à l’aide de l’infrastructure du langage commun Azure dans le modèle de déploiement classique."
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
    ms.date="05/27/2016"
    ms.author="rasquill"/>


# <a name="using-the-docker-vm-extension-with-the-azure-classic-portal"></a>À l’aide de l’Extension de la machine virtuelle Docker grâce au portail classique Azure

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]


[Docker](https://www.docker.com/) est une des approches virtualisation les plus populaires qui utilise des [conteneurs de Linux](http://en.wikipedia.org/wiki/LXC) plutôt que machines virtuelles comme un moyen d’isoler les données et de l’informatique sur les ressources partagées. Vous pouvez utiliser l’extension Docker machine virtuelle gérée par [Azure Linux Agent] pour créer une machine virtuelle Docker qui héberge un nombre quelconque de conteneurs de vos applications sur Azure.

> [AZURE.NOTE] Cette rubrique décrit comment créer une machine virtuelle Docker à partir du portail classique Azure. Pour apprendre à créer une machine virtuelle Docker à la ligne de commande, voir [comment utiliser l’Extension de machine virtuelle Docker à partir de l’Interface de ligne de Azure (commande Azure)]. Pour obtenir une description détaillée des conteneurs et leurs avantages, consultez le [Tableau blanc de niveau élevé Docker](http://channel9.msdn.com/Blogs/Regular-IT-Guy/Docker-High-Level-Whiteboard).

## <a name="create-a-new-vm-from-the-image-gallery"></a>Créer une nouvelle machine virtuelle à partir de la galerie d’images
La première étape nécessite une machine virtuelle Azure à partir d’une image Linux qui prend en charge l’Extension de machine virtuelle Docker, avec une image Ubuntu 14.04 LTS à partir de la galerie d’images comme une image d’un serveur exemple et Ubuntu 14.04 bureau comme un client. Dans le portail, cliquez sur **+ Nouveau** dans le coin inférieur gauche pour créer une nouvelle instance de machine virtuelle et sélectionnez une image Ubuntu 14.04 LTS dans les options disponibles ou dans la galerie d’Image terminée, comme illustré ci-dessous.

> [AZURE.NOTE] Pour l’instant, seules les images Ubuntu 14.04 LTS plus récentes que juillet 2014 prend en charge l’Extension de la machine virtuelle Docker.

![Créer une nouvelle Ubuntu Image](./media/virtual-machines-linux-classic-portal-use-docker/ChooseUbuntu.png)

## <a name="create-docker-certificates"></a>Créer des certificats Docker

Une fois que la machine virtuelle a été créée, assurez-vous que Docker est installé sur votre ordinateur client. (Pour plus d’informations, voir [instructions d’installation Docker](https://docs.docker.com/installation/#installation).)

Créez les fichiers de certificat et la clé pour la communication Docker selon [Docker en cours d’exécution avec https] et placez-les dans les **`~/.docker`** répertoire sur votre ordinateur client.

> [AZURE.NOTE] L’Extension de machine virtuelle Docker dans le portail nécessite actuellement des informations d’identification sont en base 64 codé.

À la ligne de commande, utilisez **`base64`** ou un autre outil codage favori pour créer des rubriques codé en base 64. Comment procéder avec un ensemble de fichiers de certificat et la clé simple peut se présenter comme suit :

```
 ~/.docker$ ls
 ca-key.pem  ca.pem  cert.pem  key.pem  server-cert.pem  server-key.pem
 ~/.docker$ base64 ca.pem > ca64.pem
 ~/.docker$ base64 server-cert.pem > server-cert64.pem
 ~/.docker$ base64 server-key.pem > server-key64.pem
 ~/.docker$ ls
 ca64.pem    ca.pem    key.pem            server-cert.pem   server-key.pem
 ca-key.pem  cert.pem  server-cert64.pem  server-key64.pem
```

## <a name="add-the-docker-vm-extension"></a>Ajouter l’Extension de la machine virtuelle Docker
Pour ajouter l’Extension de la machine virtuelle Docker, recherchez l’instance machine virtuelle que vous avez créé et faites défiler jusqu'à **Extensions** et cliquez dessus pour faire apparaître les Extensions machine virtuelle, comme illustré ci-dessous.
> [AZURE.NOTE] Cette fonctionnalité est prise en charge dans le portail preview uniquement : https://portal.azure.com/

![](./media/virtual-machines-linux-classic-portal-use-docker/ClickExtensions.png)
### <a name="add-an-extension"></a>Ajouter une Extension
Cliquez sur le **+ Ajouter** pour afficher les Extensions de machine virtuelle possibles, vous pouvez ajouter à cet ordinateur virtuel.

![](./media/virtual-machines-linux-classic-portal-use-docker/ClickAdd.png)
### <a name="select-the-docker-vm-extension"></a>Sélectionnez l’Extension de la machine virtuelle Docker
Sélectionnez l’Extension de machine virtuelle Docker, qui affiche la description de Docker et liens importants, puis sur **créer** dans la partie inférieure pour commencer la procédure d’installation.

![](./media/virtual-machines-linux-classic-portal-use-docker/ChooseDockerExtension.png)

![](./media/virtual-machines-linux-classic-portal-use-docker/CreateButtonFocus.png)
### <a name="add-your-certificate-and-key-files"></a>Ajouter votre certificat et les fichiers de clé :

Dans les champs du formulaire, entrez les versions codé en base 64 de votre autorité de certification, votre certificat de serveur et votre clé de serveur, comme le montre l’illustration suivante.

![](./media/virtual-machines-linux-classic-portal-use-docker/AddExtensionFormFilled.png)

> [AZURE.NOTE] Notez que (comme dans l’image ci-dessus) le 2376 est remplie par défaut. Vous pouvez entrer un point de terminaison, mais l’étape suivante sera pour ouvrir le point de terminaison correspondant. Si vous changez la valeur par défaut, veillez à ouvrir le point de terminaison correspondant à l’étape suivante.

## <a name="add-the-docker-communication-endpoint"></a>Ajouter le point de terminaison de Communication Docker
Lorsque vous affichez le groupe de ressources que vous avez créé, sélectionnez le groupe de sécurité réseau associé à votre ordinateur virtuel, puis cliquez sur **Règles de sécurité de trafic entrant** pour afficher les règles comme illustré ici.

![](./media/virtual-machines-linux-classic-portal-use-docker/AddingEndpoint.png)

Cliquez sur **+ Ajouter** pour ajouter une autre règle, puis dans le cas par défaut, entrez un nom pour le point de terminaison (dans cet exemple, **Docker**) et 2376 'plage de Port de Destination ». Définissez la valeur protocole montrant **TCP**, puis cliquez sur **OK** pour créer la règle.

![](./media/virtual-machines-linux-classic-portal-use-docker/AddEndpointFormFilledOut.png)


## <a name="test-your-docker-client-and-azure-docker-host"></a>Tester votre Client Docker et Azure Docker hôte
Localiser et copier le nom de domaine de votre ordinateur virtuel, puis sur la ligne de commande de votre ordinateur client, tapez `docker --tls -H tcp://` *dockerextension* `.cloudapp.net:2376 info` (où *dockerextension* est remplacée par le sous-domaine pour votre machine virtuelle).

Le résultat doit ressembler à ceci :

```
$ docker --tls -H tcp://dockerextension.cloudapp.net:2376 info
Containers: 0
Images: 0
Storage Driver: devicemapper
 Pool Name: docker-8:1-131214-pool
 Pool Blocksize: 65.54 kB
 Data file: /var/lib/docker/devicemapper/devicemapper/data
 Metadata file: /var/lib/docker/devicemapper/devicemapper/metadata
 Data Space Used: 305.7 MB
 Data Space Total: 107.4 GB
 Metadata Space Used: 729.1 kB
 Metadata Space Total: 2.147 GB
 Library Version: 1.02.82-git (2013-10-04)
Execution Driver: native-0.2
Kernel Version: 3.13.0-36-generic
WARNING: No swap limit support
```

Une fois que vous effectuez les étapes ci-dessus, vous avez à présent un hôte Docker entièrement fonctionnel en cours d’exécution sur un ordinateur virtuel Azure, configuré pour être connecté à distance à partir d’autres clients.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Étapes suivantes

Vous êtes prêt à accéder au [Guide de l’utilisateur Docker] et utiliser votre machine virtuelle Docker. Si vous souhaitez automatiser la création des hôtes de Docker sur machines virtuelles Azure via l’interface de ligne de commande, voir [comment utiliser l’Extension de machine virtuelle Docker à partir de l’Interface de ligne de Azure (commande Azure)]

<!--Anchors-->
[Create a new VM from the Image Gallery]: #createvm
[Create Docker Certificates]: #dockercerts
[Add the Docker VM Extension]: #adddockerextension
[Test Docker Client and Azure Docker Host]: #testclientandserver
[Next steps]: #next-steps

<!--Image references-->
[StartingPoint]: ./media/StartingPoint.png
[StartingPoint]: ./media/StartingPoint.png
[StartingPoint]: ./media/StartingPoint.png
[StartingPoint]: ./media/StartingPoint.png
[StartingPoint]: ./media/StartingPoint.png
[StartingPoint]: ./media/StartingPoint.png
[StartingPoint]: ./media/StartingPoint.png
[StartingPoint]: ./media/StartingPoint.png
[6]: ./media/markdown-template-for-new-articles/pretty49.png
[7]: ./media/markdown-template-for-new-articles/channel-9.png


<!--Link references-->
[Comment utiliser l’Extension de machine virtuelle Docker à partir de l’Interface de ligne de Azure (commande Azure)]: http://azure.microsoft.com/documentation/articles/virtual-machines-docker-with-xplat-cli/
[Agent de Linux Azure]: virtual-machines-linux-agent-user-guide.md
[Link 3 to another azure.microsoft.com documentation topic]: ../storage-whatis-account.md

[En cours d’exécution Docker avec https]: http://docs.docker.com/articles/https/
[Guide de l’utilisateur docker]: https://docs.docker.com/userguide/

<properties
   pageTitle="Déploiement d’Azure machine virtuelle avec Chef | Microsoft Azure"
   description="Découvrez comment utiliser Chef faire machine virtuelle automatisée déploiement et la configuration de Microsoft Azure"
   services="virtual-machines-windows"
   documentationCenter=""
   authors="diegoviso"
   manager="timlt"
   tags="azure-service-management,azure-resource-manager"
   editor=""/>

<tags ms.service="virtual-machines-windows" ms.workload="infrastructure-services"
ms.tgt_pltfrm="vm-multiple"
ms.devlang="na"
ms.topic="article"
ms.date="05/19/2015"
ms.author="diviso"/>

# <a name="automating-azure-virtual-machine-deployment-with-chef"></a>Automatisation du déploiement d’Azure machine virtuelle avec Chef

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

Chef est un excellent outil pour l’exécution d’automatisation et souhaitée configurations état.

Avec notre dernière version de l’api cloud, Chef intégration transparente avec Azure, vous donnant la possibilité de mise en service et déployer des États configuration via une commande unique.

Dans cet article, je vais vous montrer comment configurer votre environnement Chef pour configurer des ordinateurs virtuels Azure et vous guide dans la création d’une stratégie ou « Livre de recettes » et puis déploiement de ce livre de recettes pour une machine virtuelle Azure.

C’est parti !

## <a name="chef-basics"></a>Concepts de base chef

Avant de commencer, je vous conseille de que vous passez en revue les concepts de base du Chef. Il est très matérielle <a href="http://www.chef.io/chef" target="_blank">ici</a> et j’est recommandé de vous avez une lecture rapide avant d’essayer de cette procédure. J’ai récapitulatif toutefois les notions de base avant de commencer.

Le diagramme suivant illustre l’architecture Chef de haut niveau.

![][2]

Chef comporte trois principaux composants architecturales : Chef Server, Chef Client (nœud) et Chef de poste de travail.

Le serveur Chef est notre point de gestion et il existe deux possibilités pour le serveur Chef : une solution hébergée ou une solution en local. Nous allons utiliser une solution hébergée.

Le Chef Client (nœud) est l’agent qui se trouve sur les serveurs que vous gérez.

Le poste de travail Chef est notre poste de travail d’administration dans lequel nous créons notre stratégies et exécuter notre gestion des commandes. Nous exécutez la commande **couteau** du poste de travail Chef permettant de gérer notre infrastructure.

Il existe également le concept de « Cuisine » et « Recettes ». Il s’agit des efficacement les stratégies nous définir et appliquer à nos serveurs.

## <a name="preparing-the-workstation"></a>Préparer le poste de travail

Tout d’abord, vous permet de préparer le poste de travail. J’utilise un poste de travail Windows standard. Nous avons besoin créer un répertoire pour stocker vos fichiers de configuration et livre de cuisine.

Tout d’abord créer un répertoire appelé C:\chef.

Puis créer un deuxième répertoire appelé c:\chef\cookbooks.

Maintenant, nous avons besoin de télécharger notre fichier paramètres Azure afin que Chef peut communiquer avec notre abonnement Azure.

Télécharger vos paramètres [ici](https://manage.windowsazure.com/publishsettings/)de publication.

Enregistrez le fichier de paramètres de publication dans C:\chef.

##<a name="creating-a-managed-chef-account"></a>Création d’un compte Chef géré

S’inscrire à un compte Chef hébergé [ici](https://manage.chef.io/signup).

Pendant le processus d’inscription, vous devrez créer une organisation.

![][3]

Une fois que votre organisation est créée, téléchargez le kit de démarrage.

![][4]

> [AZURE.NOTE] Si vous recevez un message vous avertit que vos clés seront également réinitialisés, il est OK pour continuer que nous n’ont aucun encore configuré d’infrastructure existant.

Ce fichier starter kit zip contient votre organisation config fichiers et des clés.

##<a name="configuring-the-chef-workstation"></a>Configurer le poste de travail Chef

Extraire le contenu de la chef-starter.zip à C:\chef.

Copier tous les fichiers sous chef-starter\chef-mis en pension\.chef à votre annuaire c:\chef.

Dans l’annuaire doit maintenant ressembler à l’exemple suivant.

![][5]

Vous disposez à présent quatre fichiers notamment le fichier de publication Azure dans la racine de c:\chef.

Les fichiers PEM contiennent votre organisation et les clés privées d’administration pour les communications tandis que le fichier knife.rb contient la configuration de votre couteau. Nous avons besoin de modifier le fichier knife.rb.

Ouvrez le fichier dans votre éditeur de choix et modifier le « cookbook_path » en supprimant la /... / du chemin d’accès afin qu’elle apparaisse comme indiqué ci-après.

    cookbook_path  ["#{current_dir}/cookbooks"]

Ajoutez également ce qui suit ligne reflète le nom de votre Azure publie le fichier de configuration.

    knife[:azure_publish_settings_file] = "yourfilename.publishsettings"

Votre fichier knife.rb doit maintenant ressembler à l’exemple suivant.

![][6]

Ces lignes garantit que couteau fait référence à l’annuaire de livre de cuisine sous c:\chef\cookbooks et utilise également notre fichier de paramètres de publication Azure pendant les opérations Azure.

## <a name="installing-the-chef-development-kit"></a>Installer le Kit de développement Chef

Suivant, [Téléchargez et installez](http://downloads.getchef.com/chef-dk/windows) ChefDK (Chef Development Kit) pour configurer votre poste de travail Chef.

![][7]

Installer dans l’emplacement par défaut de c:\opscode. Cette opération prendra environ 10 minutes.

Confirmez que votre variable PATH contient des entrées pour C:\opscode\chefdk\bin ; C:\opscode\chefdk\embedded\bin;c:\users\yourusername\.chefdk\gem\ruby\2.0.0\bin

S’ils n'existent pas, vérifiez que vous ajoutez ces chemins d’accès !

*REMARQUE L’ORDRE DU CHEMIN D’ACCÈS EST IMPORTANT !* Si vos chemins d’accès opscode ne sont pas dans l’ordre correct qu'avoir des problèmes.

Redémarrez votre poste de travail avant de poursuivre.

Ensuite, nous allons installer l’extension couteau Azure. Cela permet de couteau avec le « Azure plug-in ».

Exécutez la commande suivante.

    chef gem install knife-azure ––pre

> [AZURE.NOTE] L’argument – pré garantit que vous recevez la dernière version RC du plug-in Azure couteau qui permet d’accéder à la dernière série d’API.

Il est probable qu’un nombre de dépendances sera également installé en même temps.

![][8]


Pour vous assurer que tout est configuré correctement, exécutez la commande suivante.

    knife azure image list

Si tout est correctement configuré, vous verrez une liste d’images Azure disponibles défiler.

Félicitations. Le poste de travail a été configurée !

##<a name="creating-a-cookbook"></a>Création d’un livre de recettes

Un livre de recettes est utilisée par le Chef pour définir un ensemble de commandes que vous souhaitez exécuter sur votre client géré. Création d’un livre de recettes est simple et nous utilisons la commande **chef générer un livre de recettes** pour générer notre modèle conseils et astuces. J’ai en appelant mon serveur web conseils et astuces que je souhaite une stratégie qui déploie automatiquement IIS.

Sous votre répertoire C:\Chef, exécutez la commande suivante.

    chef generate cookbook webserver

Cette opération génère un jeu de fichiers sous le répertoire C:\Chef\cookbooks\webserver. Nous devons maintenant définir le jeu de commandes que nous aimerions nos clients Chef d’exécuter sur notre machine virtuelle gérée.

Les commandes sont stockées dans le fichier default.rb. Dans ce fichier, je vais à définir un jeu de commandes qui installe IIS, démarre IIS et copie un fichier de modèle dans le dossier wwwroot.

Modifier le fichier C:\chef\cookbooks\webserver\recipes\default.rb et ajoutez les lignes suivantes.

    powershell_script 'Install IIS' do
        action :run
        code 'add-windowsfeature Web-Server'
    end

    service 'w3svc' do
        action [ :enable, :start ]
    end

    template 'c:\inetpub\wwwroot\Default.htm' do
        source 'Default.htm.erb'
        rights :read, 'Everyone'
    end

Enregistrez le fichier une fois que vous avez terminé.

## <a name="creating-a-template"></a>Création d’un modèle

Comme mentionné précédemment, nous avons besoin générer un fichier de modèle qui sera utilisé en tant que notre page default.html.

Exécutez la commande suivante pour générer le modèle.

    chef generate template webserver Default.htm

Maintenant, accédez au fichier C:\chef\cookbooks\webserver\templates\default\Default.htm.erb. Modifier le fichier en ajoutant du code « Hello World » HTML simple et enregistrer le fichier.



## <a name="upload-the-cookbook-to-the-chef-server"></a>Télécharger le livre sur le serveur Chef

Dans cette étape, nous allons prendre une copie de livre de cuisine que nous avons créés sur votre ordinateur local et téléchargez-le sur le serveur hébergé Chef. Une fois téléchargé, le livre s’affichent sous l’onglet **stratégie** .

    knife cookbook upload webserver

![][9]

## <a name="deploy-a-virtual-machine-with-knife-azure"></a>Déployer une machine virtuelle avec couteau Azure

Nous peut désormais déployer une machine virtuelle Azure et appliquer le livre « Serveur Web » qui va installer notre page de web IIS web service et par défaut.

Pour ce faire, utilisez la commande **créer server couteau azure** .

Suis exemple de la commande s’affiche ensuite.

    knife azure server create --azure-dns-name 'diegotest01' --azure-vm-name 'testserver01' --azure-vm-size 'Small' --azure-storage-account 'portalvhdsxxxx' --bootstrap-protocol 'cloud-api' --azure-source-image 'a699494373c04fc0bc8f2bb1389d6106__Windows-Server-2012-Datacenter-201411.01-en.us-127GB.vhd' --azure-service-location 'Southeast Asia' --winrm-user azureuser --winrm-password 'myPassword123' --tcp-endpoints 80,3389 --r 'recipe[webserver]'

Les paramètres sont explicites. Remplacer vos variables particuliers et exécuter.

> [AZURE.NOTE] Par le biais de la ligne de commande, je suis également automatisation mes règles de filtre de point de terminaison réseau en utilisant le paramètre – tcp-points de terminaison. J’ai ouvert les ports 80 et 3389 pour fournir un accès à ma page web et la session RDP.

Une fois que vous exécutez la commande, accédez au portail Azure et vous verrez votre ordinateur commencer mise en service.

![][13]

L’invite s’affiche ensuite.

![][10]

Une fois que le déploiement est terminé, nous devons pouvoir se connecter au service web sur le port 80 que nous ayons ouvert le port quand nous sa mise en service de la machine virtuelle avec la commande couteau Azure. Comme la machine virtuelle uniquement dans mon service cloud cet ordinateur virtuel, je vais se connecter avec l’url du service cloud.

![][11]

Comme vous pouvez le voir, je reçois creative avec mon code HTML.

N’oubliez pas de que nous pouvons également vous connecter à une session RDP à partir du portail Azure classique via le port 3389.

J’ai espérons que cela a été utile ! Accédez et démarrer votre infrastructure en tant que voyage code avec Azure aujourd'hui !


<!--Image references-->
[2]: ./media/virtual-machines-windows-chef-automation/2.png
[3]: ./media/virtual-machines-windows-chef-automation/3.png
[4]: ./media/virtual-machines-windows-chef-automation/4.png
[5]: ./media/virtual-machines-windows-chef-automation/5.png
[6]: ./media/virtual-machines-windows-chef-automation/6.png
[7]: ./media/virtual-machines-windows-chef-automation/7.png
[8]: ./media/virtual-machines-windows-chef-automation/8.png
[9]: ./media/virtual-machines-windows-chef-automation/9.png
[10]: ./media/virtual-machines-windows-chef-automation/10.png
[11]: ./media/virtual-machines-windows-chef-automation/11.png
[13]: ./media/virtual-machines-windows-chef-automation/13.png


<!--Link references-->

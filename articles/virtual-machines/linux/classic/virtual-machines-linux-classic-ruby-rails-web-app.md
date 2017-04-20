<properties
    pageTitle="Héberger un Ruby sur site Web Rails dans un Linux VM | Microsoft Azure"
    description="Configurer et héberger un Ruby sur site Web basée sur les Rails dans Azure à l’aide d’une machine virtuelle Linux."
    services="virtual-machines-linux"
    documentationCenter="ruby"
    authors="rmcmurray"
    manager="wpickett"
    editor=""
    tags="azure-service-management"/>

<tags
    ms.service="virtual-machines-linux"
    ms.workload="web"
    ms.tgt_pltfrm="vm-linux"
    ms.devlang="ruby"
    ms.topic="article"
    ms.date="08/11/2016"
    ms.author="robmcm"/>

# <a name="ruby-on-rails-web-application-on-an-azure-vm"></a>Ruby sur application Web Rails sur un ordinateur virtuel Azure

Ce didacticiel montre comment héberger un Ruby sur site Web Rails dans Azure à l’aide d’une machine virtuelle Linux.  

Ce didacticiel a été validé avec Ubuntu Server 14.04 LTS. Si vous utilisez une autre distribution Linux, vous devrez peut-être modifier les étapes pour installer les Rails.

> [AZURE.IMPORTANT] Azure comporte deux modèles de déploiement différents pour la création et utilisation des ressources : [Gestionnaire de ressources et classique](../../../resource-manager-deployment-model.md).  Cet article décrit l’utilisation du modèle de déploiement classique. Microsoft recommande la plupart des nouveaux déploiements d’utiliser le modèle de gestionnaire de ressources.

## <a name="create-an-azure-vm"></a>Créer une machine virtuelle Azure

Commencez par créer une machine virtuelle Azure avec une image Linux.

Pour créer la machine virtuelle, vous pouvez utiliser le portail classique Azure ou l’Interface de ligne de commande Azure (CLI).

### <a name="azure-management-portal"></a>Portail de gestion Azure

1. Se connecter au [portail classique Azure](http://manage.windowsazure.com)
2. Cliquez sur **Nouveau** > **Calculer** > **Machine virtuelle** > **Création rapide**. Sélectionner une image Linux.
3. Entrez un mot de passe.

Une fois que la machine virtuelle est mis en service, cliquez sur le nom de la machine virtuelle, puis cliquez sur **tableau de bord**. Recherchez le point de terminaison SSH, répertorié sous **SSH détails**.

### <a name="azure-cli"></a>Azure infrastructure du langage commun

Suivez les étapes décrites dans [créer une Linux en cours d’exécution Machine virtuelle][vm-instructions].

Une fois que la machine virtuelle est configurée, vous pouvez obtenir le point de terminaison SSH en exécutant la commande suivante :

    azure vm endpoint list <vm-name>  

## <a name="install-ruby-on-rails"></a>Installer Ruby sur Rails

1. Utiliser le protocole SSH pour vous connecter à la machine virtuelle.

2. À partir de la session SSH, utilisez les commandes suivantes pour installer Ruby sur la machine virtuelle :

        sudo apt-get update -y
        sudo apt-get upgrade -y
        sudo apt-get install ruby ruby-dev build-essential libsqlite3-dev zlib1g-dev nodejs -y

    L’installation peut prendre quelques minutes. Lorsqu’elle est terminée, utilisez la commande suivante pour vérifier que Ruby est installé :

        ruby -v

    Retourne la version de Ruby qui a été installé.

3. Utilisez la commande suivante pour installer Rails :

        sudo gem install rails --no-rdoc --no-ri -V

    Utiliser les--indicateurs non rdoc et--intégrité référentielle non permet d’ignorer l’installation de la documentation, qui est plus rapide.
    Cette commande faudra probablement beaucoup de temps à s’exécuter, afin de l’ajout de -V Affiche des informations sur la progression de l’installation.

## <a name="create-and-run-an-app"></a>Créer et exécuter une application

Lorsque vous êtes toujours connecté viaSSH, exécutez les commandes suivantes :

    rails new myapp
    cd myapp
    rails server -b 0.0.0.0 -p 3000

La commande [Nouveau](http://guides.rubyonrails.org/command_line.html#rails-new) crée une nouvelle application Rails. La commande [server](http://guides.rubyonrails.org/command_line.html#rails-server) démarre le serveur web WEBrick qui est fourni avec Rails. (Pour une utilisation de production, il est probablement conseillé à utiliser un serveur différent, par exemple licorne ou voyageurs.)

Vous devriez voir résultat semblable au suivant.

    => Booting WEBrick
    => Rails 4.2.1 application starting in development on http://0.0.0.0:3000
    => Run `rails server -h` for more startup options
    => Ctrl-C to shutdown server
    [2015-06-09 23:34:23] INFO  WEBrick 1.3.1
    [2015-06-09 23:34:23] INFO  ruby 1.9.3 (2013-11-22) [x86_64-linux]
    [2015-06-09 23:34:23] INFO  WEBrick::HTTPServer#start: pid=27766 port=3000

## <a name="add-an-endpoint"></a>Ajouter un point de terminaison

1. Accédez au [portail classique Azure] [ management-portal] et sélectionnez votre machine virtuelle.

    ![liste des machines virtuelles][vmlist]

2. Sélectionnez les **points de terminaison** en haut de la page, puis cliquez sur **+ Ajouter un point de terminaison** en bas de la page.

    ![page points de terminaison][endpoints]

3. Dans la boîte de dialogue **Ajouter un point de terminaison** , sélectionnez « Ajouter un point de terminaison autonome », puis cliquez sur la flèche **suivant** .

    ![boîte de dialogue Nouveau point de terminaison][new-endpoint1]

3. Dans la page suivante de la boîte de dialogue, entrez les informations suivantes :

    * **Nom**: HTTP

    * **Protocole**: TCP

    * **PORT PUBLIC**: 80

    * **PORT privé**: 3000

    Cela créera un port public 80 qui achemine le trafic vers le port privé 3000, où le serveur Rails est à l’écoute.

    ![boîte de dialogue Nouveau point de terminaison][new-endpoint]

4. Activez la case à cocher pour enregistrer le point de terminaison.

5. Un message s’affiche indiquant la **Mise à jour en cours**. Une fois que ce message a disparu, le point de terminaison est actif. Vous pouvez maintenant tester votre application en accédant au nom DNS de votre machine virtuelle. Le site Web doit ressembler à ce qui suit :

    ![page rails par défaut][default-rails-cloud]

## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous l’avez fait la plupart des étapes manuellement. Dans un environnement de production, vous rédigez votre application sur un ordinateur de développement et déploiement de la machine virtuelle Azure. En outre, la plupart des environnements de production hébergent l’application Rails conjointement avec un autre processus serveur tels que Apache ou NginX, qui gère demande routage vers plusieurs instances de l’application Rails et servant des ressources statiques. Pour plus d’informations, voir http://rubyonrails.org/deploy/.

Pour en savoir plus sur Ruby sur Rails, visitez le [Ruby sur Rails Guides][rails-guides].

Pour utiliser les services Azure à partir de votre application prononciation, voir :

* [Stocker des données non structurées à l’aide d’objets BLOB][blobs]

* [Paires de clé/valeur de banque d’informations à l’aide de tableaux][tables]

* [Fournir du contenu de bande passante élevée avec le réseau de distribution de contenu][cdn-howto]

<!-- WA.com links -->
[blobs]: ../../../storage/storage-ruby-how-to-use-blob-storage.md
[cdn-howto]: https://azure.microsoft.com/develop/ruby/app-services/
[management-portal]: https://manage.windowsazure.com/
[tables]: ../../../storage/storage-ruby-how-to-use-table-storage.md
[vm-instructions]: ../../virtual-machines-linux-classic-createportal.md

<!-- External Links -->
[rails-guides]: http://guides.rubyonrails.org/
[sqlite3]: http://www.sqlite.org/

<!-- Images -->

[default-rails-cloud]: ./media/virtual-machines-linux-classic-ruby-rails-web-app/basicrailscloud.png
[vmlist]: ./media/virtual-machines-linux-classic-ruby-rails-web-app/vmlist.png
[endpoints]: ./media/virtual-machines-linux-classic-ruby-rails-web-app/endpoints.png
[new-endpoint]: ./media/virtual-machines-linux-classic-ruby-rails-web-app/newendpoint.png
[new-endpoint1]: ./media/virtual-machines-linux-classic-ruby-rails-web-app/newendpoint1.png

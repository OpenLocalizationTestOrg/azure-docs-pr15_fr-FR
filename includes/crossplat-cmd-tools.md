#<a name="how-to-use-the-azure-command-line-tools-for-mac-and-linux"></a>Comment utiliser les outils de ligne de commande Azure pour Mac et Linux

Ce guide décrit comment utiliser les outils de ligne de commande Azure pour Mac et Linux pour créer et gérer des services dans Azure. Les scénarios présentés incluent **l’installation des outils**, **l’importation de vos paramètres de publication**, **créer et gérer des sites Web Azure**et **créer et gérer des Machines virtuelles Azure**. Pour la documentation de référence complète, voir [Azure outil de ligne de commande pour Mac et Documentation Linux][reference-docs]. 

##<a name="table-of-contents"></a>Table des matières
* [Quels sont les outils de ligne de commande Azure pour Mac et Linux](#Overview)
* [Comment installer les outils de ligne de commande Azure pour Mac et Linux](#Download)
* [Comment créer un compte Azure](#CreateAccount)
* [Comment télécharger et importer les paramètres de publication](#Account)
* [Comment créer et gérer un Site Web d’Azure](#WebSites)
* [Comment créer et gérer une Machine virtuelle Azure](#VMs)


<h2><a id="Overview"></a>Quels sont les outils de ligne de commande Azure pour Mac et Linux</h2>

Les outils de ligne de commande Azure pour Mac et Linux sont un ensemble d’outils de ligne de commande pour le déploiement et la gestion des services Azure.
 
Les tâches prises en charge sont les suivants :

* Importer les paramètres de publication.
* Créer et gérer des sites Web Azure.
* Créer et gérer des Machines virtuelles Azure.

Pour une liste complète des commandes prises en charge, tapez `azure -help` à la ligne de commande après l’installation d’outils, ou consultez la [documentation de référence][reference-docs].

<h2><a id="Download">Comment installer les outils de ligne de commande Azure pour Mac et Linux</a></h2>

La liste suivante contient des informations permettant d’installer les outils de ligne de commande, selon votre système d’exploitation :

* **Mac**: télécharger [programme d’installation du Kit de développement logiciel Azure][mac-installer]. Ouvrez le fichier téléchargé .pkg et suivez les étapes d’installation comme vous y êtes invité.

* **Linux**: installer la dernière version de [Node.js] [ nodejs-org] (voir [Installer Node.js via le Gestionnaire de Package][install-node-linux]), puis exécutez la commande suivante :

        npm install azure-cli -g

    **Remarque**: vous devrez peut-être exécuter cette commande avec des privilèges élevés :

        sudo npm install azure-cli -g

* **Windows**: exécuter le programme d’installation Winows (fichier .msi), qui est disponible ici : [Outils de ligne de commande Azure][windows-installer].


Pour tester l’installation, tapez `azure` à l’invite. Si l’installation a réussi, vous verrez une liste de tous les disponibles `azure` commandes.

<h2><a id="CreateAccount"></a>Comment créer un compte Azure</h2>

Pour utiliser les outils de ligne de commande Azure pour Mac et Linux, vous avez besoin d’un compte Azure.

Ouvrez un navigateur web et accédez à [http://www.windowsazure.com] [ windowsazuredotcom] et cliquez sur **version d’évaluation gratuite** dans le coin supérieur droit.

![Site Web Azure][Azure Web Site]

Suivez les instructions pour la création d’un compte.

<h2><a id="Account"></a>Comment télécharger et importer les paramètres de publication</h2>

Pour commencer, vous devez tout d’abord télécharger et importer vos paramètres de publication. Cela vous permettra d’utiliser les outils pour créer et gérer des Services Azure. Pour télécharger vos paramètres de publication, utilisez la `account download` commande :

    azure account download

Vous ouvrez votre navigateur par défaut et vous invite à se connecter au portail de gestion. Une fois connecté, votre `.publishsettings` fichier est téléchargé. Prenez note de l’emplacement d’enregistrement de ce fichier.

Ensuite, importez les `.publishsettings` fichier en exécutant la commande suivante, remplaçant `{path to .publishsettings file}` avec le chemin d’accès à votre `.publishsettings` fichier :

    azure account import {path to .publishsettings file}

Vous pouvez supprimer toutes les informations stockées par la <code>import</code> commande à l’aide de la <code>account clear</code> commande :

    azure account clear

Pour afficher la liste des options pour `account` commandes, utilisez la `-help` option :

    azure account -help

Après avoir importé vos paramètres de publication, vous devez supprimer la `.publishsettings` fichier pour des raisons de sécurité.

> [AZURE.NOTE] Lorsque vous importez des paramètres de publication, les informations d’identification pour accéder à votre abonnement Azure sont stockées dans votre `user` dossier. Votre `user` dossier est protégé par votre système d’exploitation. Toutefois, il est recommandé d’utiliser des étapes supplémentaires pour chiffrer votre `user` dossier. Vous pouvez le faire des façons suivantes :    
> 
> - Sous Windows, modifiez les propriétés du dossier, ou utilisez BitLocker.
> - Sur Mac, activez FileVault pour le dossier.
> - Sous Ubuntu, utilisez la fonctionnalité de répertoire accueil chiffré. D’autres versions de Linux proposent des fonctionnalités équivalentes.

Vous êtes maintenant prêt à créer et à la gestion des sites Web Azure et Machines virtuelles Azure.  

<h2><a id="WebSites"></a>Comment créer et gérer un site Web Azure</h2>

###<a name="create-a-website"></a>Créer un site Web

Pour créer un site Web Azure, tout d’abord créer un répertoire vide appelé `MySite` , puis naviguez dans ce répertoire.

Ensuite, exécutez la commande suivante :

    azure site create MySite --git

Le résultat de cette commande contiendra l’URL par défaut pour le site Web nouvellement créé. La `--git` option vous permet d’utiliser git à publier sur votre site Web en créant des référentiels git dans les deux votre annuaire application locale et dans le centre de données de votre site Web. Notez que si votre dossier local est déjà un référentiel git, la commande Ajouter un nouvel appel distant vers le référentiel existant et pointant vers le référentiel dans le centre de données de votre site Web.

Notez que vous pouvez exécuter le `azure site create` commande avec une des options suivantes :

* `--location [location name]`. Cette option vous permet de spécifier un emplacement du centre de données dans laquelle votre site Web est créé (par exemple, « ouest nous »). Si vous omettez cette option, vous serez invite à choisir un emplacement.
* `--hostname [custom host name]`. Cette option permet de spécifier un nom d’hôte personnalisé pour votre site Web.

Vous pouvez ensuite ajouter du contenu à votre répertoire de site Web. Utiliser le flux git régulières (`git add`, `git commit`) pour valider le contenu. Utilisez la commande git suivantes pour distribuer le contenu de votre site Web vers Azure : 

    git push azure master

###<a name="set-up-publishing-from-github"></a>Configurer la publication de GitHub

Pour configurer la publication continue à partir d’un référentiel GitHub, utilisez la `--GitHub` option lors de la création d’un site :

    auzre site create MySite --github --githubusername username --githubpassword password --githubrepository githubuser/reponame

Si vous avez un cloner locale d’un référentiel GitHub ou si vous disposez d’un référentiel avec une référence à distance à un référentiel GitHub, cette commande Publier automatiquement code dans le référentiel GitHub à votre site. Dès lors, toutes les modifications vers le référentiel GitHub être publiées automatiquement à votre site.

Lorsque vous configurez la publication de GitHub, la branche par défaut utilisée est la branche maître. Pour spécifier une autre branche, exécutez la commande suivante à partir de votre référentiel local :

    azure site repository <branch name>

###<a name="configure-app-settings"></a>Configurer les paramètres de l’application

Paramètres de l’application sont des paires clé-valeur qui sont disponibles pour votre application en cours d’exécution. Lorsque vous définissez pour un site Web Azure, les valeurs de paramètre application remplace des paramètres avec la même clé qui sont définis dans le fichier Web.config de votre site. Pour les applications Node.js et PHP, les paramètres de l’application sont disponibles en tant que variables d’environnement. L’exemple suivant vous montre comment définir une paire clé-valeur :

    azure site config add <key>=<value> 

Pour afficher la liste de toutes les paires clé/valeur, utilisez la commande suivante :

    azure site config list 

Ou, si vous connaissez la touche et que vous souhaitez faire figurer la valeur, vous pouvez utiliser :

    azure site config get <key> 

Si vous voulez modifier la valeur d’une clé existante, vous devez tout d’abord supprimer la clé existante et puis ajoutez-le à nouveau. La commande Effacer est la suivante :

    azure site config clear <key> 

###<a name="list-and-show-sites"></a>Liste et afficher les sites

Pour mettre votre site Web, utilisez la commande suivante :

    azure site list

Pour obtenir des informations détaillées sur un site, utilisez la `site show` commande. L’exemple suivant affiche les détails pour `MySite`:

    azure site show MySite

###<a name="stop-start-or-restart-a-site"></a>Arrêter, démarrer ou redémarrer un site

Vous pouvez arrêter, démarrer ou redémarrer un site avec le `site stop`, `site start`, ou `site restart` commandes :

    azure site stop MySite
    azure site start MySite
    azure site restart MySite

###<a name="delete-a-site"></a>Supprimer un site

Enfin, vous pouvez supprimer un site avec la `site delete` commande :

    azure site delete MySite

Notez que si vous exécutez une des au-dessus de commandes dans le dossier dans lequel vous avez exécuté `site create`, vous n’avez pas besoin spécifier le nom du site `MySite` comme dernier paramètre.

Pour afficher une liste complète des `site` commandes, utilisez la `-help` option :

    azure site -help 

<h2><a id="VMs"></a>Comment créer et gérer une Machine virtuelle Azure</h2>

une Machine virtuelle Azure est créé à partir d’une image de machine virtuelle (fichier .vhd) que vous fournissez ou qui est disponible dans la galerie d’Image. Pour afficher des images disponibles, utilisez la `vm image list` commande :

    azure vm image list

Vous pouvez configurer et démarrer une machine virtuelle à partir d’une des images disponibles avec la `vm create` commande. L’exemple suivant montre comment créer une machine virtuelle Linux (appelé `myVM`) à partir d’une image dans la galerie d’Image (CentOS 6.2). Le nom d’utilisateur racine et le mot de passe de la machine virtuelle sont `myusername` et `Mypassw0rd` respectivement. (Notez que la `--location` paramètre spécifie le centre de données dans lequel la machine virtuelle est créée. Si vous omettez le `--location` paramètre, vous devrez choisir un emplacement.)

    azure vm create myVM OpenLogic__OpenLogic-CentOS-62-20120509-en-us-30GB.vhd myusername --location "West US"

Vous pouvez envisager passage la `--ssh` indicateur (Linux) ou `--rdp` marque (Windows) pour `vm create` pour activer les connexions à distance sur l’ordinateur virtuel nouvellement créé.

Si vous le feriez Configurez plutôt une machine virtuelle à partir d’une image personnalisée, vous pouvez créer une image à partir d’un fichier .vhd avec la `vm image create` de commande, puis utilisez le `vm create` commande mise en service de la machine virtuelle. L’exemple suivant montre comment créer une image Linux (appelé `myImage`) à partir d’un fichier .vhd local. (La `--location` paramètre spécifie les données dans lequel l’image est stockée.)

    azure vm image create myImage /path/to/myImage.vhd --os linux --location "West US"

Au lieu de créer une image à partir d’un .vhd local, vous pouvez créer une image à partir d’un .vhd stockées dans le stockage Blob Azure. Vous pouvez faire avec les `blob-url` paramètre :

    azure vm image create myImage --blob-url <url to .vhd in Blob Storage> --os linux

Après avoir créé une image, vous pouvez configurer une machine virtuelle à partir de l’image à l’aide de `vm create`. La commande suivante crée une machine virtuelle appelée `myVM` à partir de l’image créé ci-dessus (`myImage`).

    azure vm create myVM myImage myusername --location "West US"

Une fois que vous avez configuré une machine virtuelle, vous souhaiterez créer des points de terminaison pour permettre l’accès à votre machine virtuelle (par exemple). L’exemple suivant utilise la `vm create endpoint` commande pour ouvrir le port externe 22 et port local 22 sur `myVM`:

    azure vm endpoint create myVM 22 22

Vous pouvez obtenir des informations détaillées sur une machine virtuelle (y compris l’adresse IP, nom DNS et informations de point de terminaison) avec la `vm show` commande :

    azure vm show myVM

Arrêter, Démarrer, ou redémarrez l’ordinateur virtuel, utilisez une des commandes suivantes :

    azure vm shutdown myVM
    azure vm start myVM
    azure vm restart myVM

Enfin, pour supprimer la machine virtuelle, utilisez la `vm delete` commande :

    azure vm delete myVM

Pour obtenir une liste complète des commandes pour créer et gérer des machines virtuelles, utilisez la `-h` option :

    azure vm -h

<!-- IMAGES -->
[Azure Web Site]: ./media/crossplat-cmd-tools/freetrial.png

<!-- LINKS -->
[nodejs-org]: http://nodejs.org/
[install-node-linux]: https://github.com/joyent/node/wiki/Installing-Node.js-via-package-manager
[mac-installer]: http://go.microsoft.com/fwlink/?LinkId=252249
[windows-installer]: http://go.microsoft.com/fwlink/?LinkID=275464
[reference-docs]: http://go.microsoft.com/fwlink/?LinkId=252246
[windowsazuredotcom]: http://www.windowsazure.com


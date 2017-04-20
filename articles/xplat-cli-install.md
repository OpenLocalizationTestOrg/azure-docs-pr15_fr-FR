<properties
    pageTitle="Installer l’Interface de ligne de commande Azure | Microsoft Azure"
    description="Installer l’Interface de ligne de commande Azure (CLI) pour Mac, Linux et Windows commencer à utiliser les services Azure"
    editor=""
    manager="timlt"
    documentationCenter=""
    authors="squillace"
    services="virtual-machines-linux,virtual-network,storage,azure-resource-manager"
    tags="azure-resource-manager,azure-service-management"/>

<tags
    ms.service="multiple"
    ms.workload="multiple"
    ms.tgt_pltfrm="command-line-interface"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/04/2016"
    ms.author="rasquill"/>
    
# <a name="install-the-azure-cli"></a>Installer l’infrastructure du langage commun Azure

> [AZURE.SELECTOR]
- [PowerShell](powershell-install-configure.md)
- [Azure infrastructure du langage commun](xplat-cli-install.md)

Installez rapidement l’Interface de ligne Azure (Azure commande) pour utiliser un jeu de commandes shell open source pour la création et gestion des ressources dans Microsoft Azure. Vous disposez de plusieurs options pour installer ces outils disponibilité sur plusieurs plateformes sur votre ordinateur : 

* **npm package** - exécuter npm (le Gestionnaire de package pour JavaScript) pour installer le dernier package Azure infrastructure du langage commun sur votre distribution Linux ou un système d’exploitation. Nécessite node.js et npm sur votre ordinateur.
* **Programme d’installation** - télécharger un programme d’installation pour faciliter l’installation sur Mac ou Windows.
* **Conteneur docker** - démarrer à l’aide de l’infrastructure du langage commun dernière dans un conteneur Docker prête à exécuter. Nécessite hôte Docker sur votre ordinateur.
    
Pour plus d’options générales, voir le référentiel de projet sur [GitHub](https://github.com/azure/azure-xplat-cli). 

Une fois l’infrastructure du langage commun Azure est installé, [vous connecter avec votre abonnement Azure](xplat-cli-connect.md) et exécuter les commandes **azure** à partir de l’interface de ligne de commande (Bash, Terminal, invite de commandes et ainsi de suite) pour travailler avec vos ressources Azure.



## <a name="option-1-install-an-npm-package"></a>Option 1 : Installer un package de npm

Pour installer l’infrastructure du langage commun à partir d’un package de npm, vérifiez que vous avez téléchargé et installé les [dernières Node.js et npm](https://nodejs.org/en/download/package-manager/). Ensuite, exécutez **npm installer** pour installer le module azure-infrastructure du langage commun : 

    npm install -g azure-cli

Dans les versions de Linux, vous devrez peut-être utiliser **sudo** pour exécuter la commande __npm__ , comme suit :

    sudo npm install -g azure-cli

> [AZURE.NOTE]Si vous avez besoin installer ou mettre à jour Node.js et npm sur votre distribution Linux ou un système d’exploitation, nous vous recommandons d’installer la version la plus récente LTS Node.js (4.x). Si vous utilisez une version antérieure, vous pouvez obtenir des erreurs d’installation. 

Si vous préférez, téléchargez le dernier Linux [fichier tar] [ linux-installer] pour le package npm localement. Ensuite, installez le package npm téléchargé comme suit (sur les éditions Linux que vous devrez peut-être utiliser **sudo**) :

    npm install -g <path to downloaded tar file>

## <a name="option-2-use-an-installer"></a>Option 2 : Utiliser un programme d’installation

Si vous utilisez un ordinateur Mac ou Windows, les programmes d’installation infrastructure du langage commun suivants sont disponibles pour téléchargement :

* [Programme d’installation de Mac OS X][mac-installer]

* [Windows MSI][windows-installer] 

>[AZURE.TIP]Sous Windows, vous pouvez également télécharger l' [Web Platform Installer](https://go.microsoft.com/?linkid=9828653) pour installer l’infrastructure du langage commun. Ce programme d’installation vous donne l’option d’installation supplémentaires Azure SDK et les outils de ligne de commande après l’installation de l’infrastructure du langage commun. 


## <a name="option-3-use-a-docker-container"></a>Option 3 : Utiliser un conteneur Docker

Si vous avez configuré votre ordinateur en tant qu’un hôte [Docker](https://docs.docker.com/engine/understanding-docker/) , vous pouvez exécuter l’infrastructure du langage commun Azure dernières dans un conteneur Docker. Exécutez la commande suivante (sur les éditions Linux que vous devrez peut-être utiliser **sudo**) :

```
docker run -it microsoft/azure-cli
```


## <a name="run-azure-cli-commands"></a>Exécuter des commandes Azure infrastructure du langage commun
Une fois l’infrastructure du langage commun Azure est installé, exécutez la commande **azure** à partir de votre interface utilisateur de ligne de commande (Bash, Terminal, invite de commandes et ainsi de suite). Par exemple, pour exécuter la commande help, tapez les informations suivantes :

```
azure help
```
> [AZURE.NOTE]Dans certaines versions de Linux, vous pouvez recevoir une erreur semblable à `/usr/bin/env: ‘node’: No such file or directory`. Cette erreur provient d’installations récentes de Node.js être installée en /usr/bin/nodejs. Pour y remédier, créez un SYLK à /usr/bin/node en exécutant cette commande :

```
sudo ln -s /usr/bin/nodejs /usr/bin/node
```

Pour afficher la version de l’infrastructure du langage commun Azure que vous avez installée, tapez les informations suivantes :

```
azure --version
```

Vous êtes maintenant prêt ! Pour accéder à toutes les commandes d’infrastructure du langage commun pour l’utiliser avec vos propres ressources, [se connecter à votre abonnement depuis l’interface Azure Azure](xplat-cli-connect.md).

>[AZURE.NOTE] Lorsque vous utilisez Azure infrastructure du langage commun pour la première fois, vous voyez un message vous demandant si vous souhaitez autoriser Microsoft à recueillir les informations d’utilisation. La participation est volontaire. Si vous choisissez de participer, vous pouvez arrêter à tout moment en exécutant `azure telemetry --disable`. Pour activer la participation à tout moment, exécutez `azure telemetry --enable`.


## <a name="update-the-cli"></a>Mise à jour de l’infrastructure du langage commun

Microsoft publie souvent des versions mises à jour de l’infrastructure du langage commun Azure. Réinstaller l’infrastructure du langage commun à l’aide du programme d’installation pour votre système d’exploitation, ou exécuter le conteneur Docker dernière. Ou, si vous avez les dernières Node.js et npm installé, mettre à jour en tapant ce qui suit (dans les éditions Linux que vous devrez peut-être utiliser **sudo**).

```
npm update -g azure-cli
```

## <a name="enable-tab-completion"></a>Activer la saisie automatique par tabulation

Onglet saisie semi-automatique des commandes de l’infrastructure du langage commun est pris en charge pour Mac et Linux.

Pour l’activer dans zsh, exécutez :

```
echo '. <(azure --completion)' >> .zshrc
```

Pour l’activer dans bash, exécutez :

```
azure --completion >> ~/azure.completion.sh
echo 'source ~/azure.completion.sh' >> ~/.bash_profile
```


## <a name="next-steps"></a>Étapes suivantes 

* [Se connecter depuis l’interface à votre abonnement Azure](xplat-cli-connect.md) pour créer et gérer des ressources Azure.

* Pour en savoir plus sur l’infrastructure du langage commun Azure, téléchargez le code source, signaler des problèmes ou contribuer au projet, visitez le [référentiel GitHub pour l’infrastructure du langage commun Azure](https://github.com/azure/azure-xplat-cli).

* Si vous avez des questions sur l’utilisation de l’infrastructure du langage commun Azure ou Azure, consultez les [Forums Azure](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurescripting).

* Si vous le souhaitez, vous pouvez également essayer l' basée sur les Python [Azure infrastructure du langage commun 2.0 Preview](https://github.com/azure/azure-cli).

[mac-installer]: http://aka.ms/mac-azure-cli
[windows-installer]: http://aka.ms/webpi-azure-cli
[linux-installer]: http://aka.ms/linux-azure-cli
[cliasm]: virtual-machines-command-line-tools.md
[cliarm]: ./virtual-machines/azure-cli-arm-commands.md

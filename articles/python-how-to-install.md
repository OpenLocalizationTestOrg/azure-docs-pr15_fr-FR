<properties
    pageTitle="Installer Python et le Kit de développement - Azure"
    description="Découvrez comment installer Python et le Kit de développement à utiliser avec Azure."
    services=""
    documentationCenter="python"
    authors="lmazuel"
    manager="wpickett"
    editor=""/>

<tags
    ms.service="multiple"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="python"
    ms.topic="article"
    ms.date="09/06/2016"
    ms.author="lmazuel"/>

# <a name="installing-python-and-the-sdk"></a>L’installation de Python et le Kit de développement

Python est facile à configurer sur Windows et est préinstallé sur un Mac, Linux et [Bash pour Windows](https://msdn.microsoft.com/commandline/wsl/about). Ce guide vous guide dans l’installation et vous préparer votre ordinateur pour une utilisation avec Azure.

## <a name="whats-in-the-python-azure-sdk"></a>Nouveautés dans les Python SDK Azure ?

Le Kit de développement Azure pour Python inclut des composants qui vous permettent de développer, déployer et gérer des applications Python pour Azure. Plus précisément, le Kit de développement Azure pour Python inclut les éléments suivants :

* **Bibliothèques de gestion**. Ces bibliothèques de classes fournissent une interface de gestion des ressources Azure, tels que les comptes de stockage, machines virtuelles.

* **Bibliothèques d’exécution**. Ces bibliothèques de classes fournissent une interface pour accéder aux fonctionnalités Azure, telles que les bus de stockage et le service.

## <a name="which-python-and-which-version-to-use"></a>Les Python et la version à utiliser

Il existe plusieurs versions d’interpréteurs Python - Voici quelques exemples :

* CPython - le relais Python standard et les plus fréquemment utilisés
* PyPy - rapide et respecter les exigences implémentation alternative CPython
* IronPython - relais Python qui s’exécute sur .net/CLR
* Jython - relais Python qui s’exécute sur la Machine virtuelle Java

**CPython** version2.7 ou v3.3 + PyPy 5.4.0 testé et sont pris en charge pour le Kit de développement Python Azure.

## <a name="where-to-get-python"></a>Où la trouver Python ?

Il existe plusieurs méthodes pour obtenir CPython :

* Directement à partir de [www.python.org][]
* À partir d’une autorité distro tels que [www.continuum.io][], [www.enthought.com][] ou [www.activestate.com][]
* Créer à partir de la source !

À moins d’avoir un besoin spécifique, nous vous recommandons des deux premières options.

## <a name="sdk-installation-on-windows-linux-and-macos-client-libraries-only"></a>Installation du Kit de développement logiciel sous Windows, Linux et Mac OS (bibliothèques client uniquement)

Si vous avez déjà Python installé, vous pouvez utiliser pip pour installer un ensemble de toutes les bibliothèques client dans votre environnement de Python 3.3 + ou 2.7 Python existant. Les packages sont alors téléchargées à partir de l' [Index de Package Python][] (PyPI).

Vous devrez peut-être les droits d’administrateur :

- Linux et Mac OS, utilisez le `sudo` commande : `sudo pip install azure-mgmt-compute`.
- Windows : ouvrir votre PowerShell/invite de commandes en tant qu’administrateur

Vous pouvez installer individuellement chaque bibliothèque pour chaque service Azure :

```console
   $ pip install azure-batch          # Install the latest Batch runtime library
   $ pip install azure-mgmt-scheduler # Install the latest Storage management library
```

Aperçu packages peuvent être installés à l’aide de la `--pre` indicateur :

```console
   $ pip install --pre azure-mgmt-compute # will install only the latest Compute Management library
```

Vous pouvez également installer un jeu de bibliothèques Azure dans une seule ligne à l’aide du `azure` meta package. Dans la mesure où pas tous les packages dans ce package meta sont publiées stable encore, la `azure` meta package est toujours en mode Aperçu avant. Toutefois, les packages core, à partir de perspectives de qualité/conformité code peuvent être considéré comme « stables » pour le moment
- Il porte embauche l’étiquette en tant que tel synchronisé avec d’autres langues dès que possible. Nous n'avons pas l’intention de toute modification majeure davantage jusque là.

Dans la mesure où il s’agit d’une version d’évaluation, vous devez utiliser le `--pre` indicateur :

```console
   $ pip install --pre azure
```
   
ou directement

```console
   $ pip install azure==2.0.0rc6
```

## <a name="getting-more-packages"></a>Obtention des Packages

L' [Index de Package Python][] (PyPI) contient une sélection de bibliothèques Python enrichie.  Si vous avez choisi d’installer une Distro, vous aurez déjà la plupart des bits intéressantes pour différents scénarios de développement web à l’informatique technique.


## <a name="python-tools-for-visual-studio"></a>Outils Python pour Visual Studio

[Outils Python pour Visual Studio][] (PTVS) est un plug-in libre/OSS à partir de Microsoft, qui se transforme VS en un IDE Python entière :

![Comment-à-installer-python-ptvs](./media/python-how-to-install/how-to-install-python-ptvs.png)

À l’aide de PTVS est facultative, mais il est recommandé que vous donne prise en charge Python et projet/Solution Web, le débogage, profil, fenêtre interactive, la modification du modèle et Intellisense.

PTVS facilite également à déployer Microsoft Azure, avec prise en charge pour le déploiement sur [Les Services en nuage][] et les [sites Web][].

PTVS fonctionne avec votre Visual Studio 2013 existant ou installation 2015.  Pour la documentation, des téléchargements et des discussions, voir [Python Tools pour Visual Studio].  

## <a name="python-azure-scenarios-for-linux-and-macos"></a>Python scénarios Azure pour Linux et Mac OS

Pour Linux ou Mac OS, principales scénarios Azure qui sont prises en charge :

1. Utilisation des Services Azure en utilisant les bibliothèques client pour Python

2. Exécution de votre application dans une machine virtuelle Linux

3. Développement et de publication à des sites Web Azure à l’aide de Git

Le premier scénario vous permet de créer des applications web riches tirer parti des fonctionnalités telles que le [stockage blob][], [stockage file d’attente][], le [stockage de tables][] etc. via wrappers Pythonic PaaS Azure pour l’API REST Azure. Ces fonctionne de la même façon que dans Windows, Mac et Linux.  Vous pouvez également utiliser ces bibliothèques client à partir de votre ordinateur de développement local ou un VM Linux s’exécutant sur Azure.

Pour le scénario machine virtuelle, vous simplement démarrez un VM Linux de votre choix (Ubuntu, CentOS, Suse) et que vous souhaitez exécuter/gérer.  Par exemple, vous pouvez exécuter [IPython][] réplication/bloc-notes sur votre ordinateur Windows/Mac/Linux et pointez sur votre navigateur exécutant le moteur IPython sur Azure machine virtuelle de plusieurs processeurs Windows ou Linux. Consultez le didacticiel [IPython bloc-notes sur Azure][] pour plus d’informations.

Pour plus d’informations sur la configuration un VM Linux, consultez le didacticiel [créer un Linux en cours d’exécution Machine virtuelle][] .

À l’aide de déploiement Git, vous pouvez développer une application web Python et publiez-le sur un site Web Azure à partir d’un système d’exploitation.  Lorsque vous appuyez sur votre référentiel vers Azure, il crée automatiquement un environnement virtuel et pip installer vos packages requis.

Pour plus d’informations sur le développement et de publication de sites Web Azure, consultez les didacticiels pour la [Création de sites Web avec Django][], [Création de sites Web avec bouteilles][]et [Création de sites Web avec ballon][]. Pour plus d’informations sur l’utilisation de n’importe quelle infrastructure compatible WSGI, voir [Configuration Python avec Azure des sites Web][].


## <a name="additional-software-and-resources"></a>Logiciel supplémentaire et ressources :

* [Azure SDK pour Python ReadTheDocs](http://azure-sdk-for-python.readthedocs.io/en/latest/)
* [Azure SDK pour Python Github](https://github.com/Azure/azure-sdk-for-python)
* [Exemples de Azure officiels pour Python](https://azure.microsoft.com/documentation/samples/?platform=python)
* [Distribution de Python Analytique continue][]
* [Distribution Enthought Python][]
* [Distribution ActiveState Python][]
* [SciPy - une suite de bibliothèques Python scientifique][]
* [NumPy - une bibliothèque de valeurs numériques pour Python][]
* [Projet Django - web maturité framework/CMS][]
* [IPython - un bloc-notes/réplication avancé pour Python][]
* [Bloc-notes IPython sur Azure][]
* [Python Tools pour Visual Studio sur GitHub][]
* [Centre de développement Python](/develop/python/)

[Distribution de Python Analytique continue]: http://continuum.io
[Distribution Enthought Python]: http://www.enthought.com
[Distribution ActiveState Python]: http://www.activestate.com
[www.Python.org]: http://www.python.org
[www.continuum.IO]: http://continuum.io
[www.enthought.com]: http://www.enthought.com
[www.activestate.com]: http://www.activestate.com
[SciPy - une suite de bibliothèques Python scientifique]: http://www.scipy.org
[NumPy - une bibliothèque de valeurs numériques pour Python]: http://www.numpy.org
[Projet Django - web maturité framework/CMS]: http://www.djangoproject.com
[IPython - un bloc-notes/réplication avancé pour Python]: http://ipython.org
[IPython]: http://ipython.org
[Bloc-notes IPython sur Azure]: virtual-machines-linux-jupyter-notebook.md
[Services en nuage]: cloud-services-python-ptvs.md
[Sites Web]: web-sites-python-ptvs-django-mysql.md
[Outils Python pour Visual Studio]: http://aka.ms/ptvs
[Python Tools pour Visual Studio sur GitHub]: https://github.com/microsoft/ptvs
[Python Package Index]: http://pypi.python.org/pypi
[Microsoft Azure SDK for Python 2.7]: http://go.microsoft.com/fwlink/?LinkId=254281
[Microsoft Azure SDK for Python 3.4]: http://go.microsoft.com/fwlink/?LinkID=516990
[Setting up a Linux VM via the Azure portal]: create-and-configure-opensuse-vm-in-portal.md
[How to use the Azure Command-Line Interface]: crossplat-cmd-tools.md
[Créer une Machine virtuelle Linux en cours d’exécution]: virtual-machines-linux-quick-create-cli.md
[Création de sites Web avec Django]: web-sites-python-create-deploy-django-app.md
[Création de sites Web avec bouteilles]: web-sites-python-create-deploy-bottle-app.md
[Création de sites Web avec ballon]: web-sites-python-create-deploy-flask-app.md
[Configuration Python avec les sites Web Azure]: web-sites-python-configure.md
[stockage de table]: storage-python-how-to-use-table-storage.md
[stockage de file d’attente]: storage-python-how-to-use-queue-storage.md
[stockage d’objets BLOB]: storage-python-how-to-use-blob-storage.md

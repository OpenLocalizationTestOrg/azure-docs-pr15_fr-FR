<properties
   pageTitle="Configurer votre environnement de développement sur Mac OS X | Microsoft Azure"
   description="Installer les outils, le Kit de développement logiciel et le runtime et créez un cluster de développement local. Au terme de ce programme d’installation, vous serez prêt à créer des applications sur Mac OS X."
   services="service-fabric"
   documentationCenter=".net"
   authors="seanmck"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotNet"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="09/25/2016"
   ms.author="seanmck"/>

# <a name="set-up-your-development-environment-on-mac-os-x"></a>Configurer votre environnement de développement sur Mac OS X

> [AZURE.SELECTOR]
-[Windows](service-fabric-get-started.md)
- [Linux](service-fabric-get-started-linux.md)
- [OSX](service-fabric-get-started-mac.md)

Vous pouvez créer des applications de Service tissu à exécuter sur clusters Linux à l’aide de Mac OS X. Cet article explique comment configurer mon Mac pour le développement.

## <a name="prerequisites"></a>Conditions préalables

TISSU de service n’est pas exécuté en mode natif sous OS X. Pour exécuter un cluster de Service TISSU local, nous fournissons une préconfiguré Ubuntu machine virtuelle Vagrant et VirtualBox. Avant de commencer, vous devez :

- [Vagrant (v1.8.4 ou version ultérieure)](http://wwww.vagrantup.com/downloads)
- [VirtualBox](http://www.virtualbox.org/wiki/Downloads)

## <a name="create-the-local-vm"></a>Créer la machine virtuelle locale

Pour créer la machine virtuelle locale contenant un cluster de Service TISSU 5-nœud, procédez comme suit :

1. Cloner le mis en pension Vagrantfile

    ```bash
    git clone https://github.com/azure/service-fabric-linux-vagrant-onebox.git
    ```

2. Accédez à la cloner locale de la mis en pension

    ```bash
    cd service-fabric-linux-vagrant-onebox
    ```

3. (Facultatif) Modifier les paramètres de mémoire virtuelle par défaut

    Par défaut, la machine virtuelle locale est configurée comme suit :

    - 3 Go de mémoire allouée
    - Réseau privé hôte configuré à IP 192.168.50.50 l’activation de relais du trafic de l’hôte de Mac

    Vous pouvez modifier un de ces paramètres ou ajouter des autres champs de configuration de la machine virtuelle dans la Vagrantfile. Consultez la [documentation Vagrant](http://www.vagrantup.com/docs) pour la liste complète des options de configuration.

4. Créer la machine virtuelle

    ```bash
    vagrant up
    ```

    Cette étape télécharge l’image de machine virtuelle préconfiguré, démarrage localement et puis paramétrez un tissu Service local cluster qu’il contient. Vous devriez pouvoir prendre quelques minutes. Si le programme d’installation est terminée avec succès, vous verrez un message dans la sortie indiquant que le cluster démarre.

    ![Le programme d’installation démarrage après la mise en service des machines virtuelles du cluster][cluster-setup-script]

5. Vérifiez si le cluster a été configuré correctement en accédant à l’Explorateur de tissu Service en http://192.168.50.50:19080/Explorer (en supposant que vous avez conservé l’adresse IP de réseau privé par défaut).

    ![Service TISSU Explorer affiché à partir de l’hôte de Mac][sfx-mac]


## <a name="install-the-service-fabric-plugin-for-eclipse-neon-optional"></a>Installer le plug-in tissu de Service pour néon Éclipse (facultatif)

Service TISSU fournit un plug-in pour l’Eclipse de macros néon qui peut simplifier le processus de création et de déploiement de services Java.

1. Dans Eclipse, vérifiez que vous avez Buildship version 1.0.17 ou version ultérieure est installé. Vous pouvez vérifier les versions des composants installés en choisissant **Aide > Détails de l’Installation**. Vous pouvez mettre à jour Buildship en suivant les instructions [ici][buildship-update].

2. Pour installer le plug-in tissu de Service, choisissez **Aide > installer le logiciel nouveau...**

3. Dans la zone de texte « Utiliser », entrez : http://dl.windowsazure.com/eclipse/servicefabric.

4. Cliquez sur Ajouter.

    ![Plug-in de néon Eclipse pour tissu de Service][sf-eclipse-plugin-install]

5. Sélectionnez le plug-in tissu de Service et cliquez sur Suivant.

6. Passez en revue l’installation et acceptez le contrat de licence utilisateur final.

## <a name="next-steps"></a>Étapes suivantes

- [Créer votre première application Service tissu pour Linux](service-fabric-create-your-first-linux-application-with-java.md)

<!-- Links -->

- [Créer un cluster de tissu de Service dans le portail Azure](service-fabric-cluster-creation-via-portal.md)
- [Créer un cluster de Service tissu en utilisant le Gestionnaire de ressources Azure](service-fabric-cluster-creation-via-arm.md)
- [Comprendre le modèle d’application tissu de Service](service-fabric-application-model.md)

<!-- Images -->
[cluster-setup-script]: ./media/service-fabric-get-started-mac/cluster-setup-mac.png
[sfx-mac]: ./media/service-fabric-get-started-mac/sfx-mac.png
[sf-eclipse-plugin-install]: ./media/service-fabric-get-started-mac/sf-eclipse-plugin-install.png
[buildship-update]: https://projects.eclipse.org/projects/tools.buildship

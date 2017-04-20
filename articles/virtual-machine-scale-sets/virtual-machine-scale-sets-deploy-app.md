<properties
    pageTitle="Déployer une application sur les jeux d’échelle Machine virtuelle | Microsoft Azure"
    description="Déployer une application sur les jeux d’échelle Machine virtuelle"
    services="virtual-machine-scale-sets"
    documentationCenter=""
    authors="gbowerman"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machine-scale-sets"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/26/2016"
    ms.author="guybo"/>

# <a name="deploy-an-app-on-virtual-machine-scale-sets"></a>Déployer une application sur les jeux d’échelle Machine virtuelle

Une application qui s’exécute sur une échelle de machine virtuelle définie est généralement déployée dans une des suivantes manières :

- L’installation de nouveaux logiciels sur une image de la plateforme au moment du déploiement. Une image de la plateforme dans ce contexte est une image de système d’exploitation à partir d’Azure Marketplace, comme 16.04 Ubuntu, Windows Server 2012 R2, etc..

Vous pouvez installer de nouveaux logiciels sur une image platform à l’aide d’une [Extension de la mémoire virtuelle](../virtual-machines/virtual-machines-windows-extensions-features.md). Une extension de mémoire virtuelle est un logiciel qui s’exécute lors d’un ordinateur virtuel est déployé. Vous pouvez exécuter n’importe quel code que vous aimez au moment du déploiement à l’aide d’une extension de script personnalisé. [Ici](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-lapstack-autoscale) est un exemple de modèle du Gestionnaire de ressources Azure avec deux extensions machine virtuelle : un Extension de Script personnalisé Linux pour installer Apache et PHP et d’une Extension de Diagnostic pour émettre des données de performance utilisées par échelle Azure.

L’avantage de cette approche est que vous disposez d’un niveau de séparation entre votre code d’application et le système d’exploitation et que vous pouvez maintenir votre application séparément. Bien entendu que signifie qu’il est également pièces plus mobiles et l’heure de déploiement machine virtuelle peut être plus longue si il y a beaucoup pour le script Téléchargez et configurez.

**Si vous passez des informations sensibles dans votre commande Extension de Script personnalisé (par exemple, un mot de passe), veillez à spécifier la `commandToExecute` dans les `protectedSettings` attribut de l’Extension de Script personnalisé à la place de la `settings` attribut.**

- Créer une image de machine virtuelle personnalisée qui inclut le système d’exploitation et de l’application dans un seul disque dur virtuel. Ici l’ensemble d’échelle comprend un ensemble de machines virtuelles copiée à partir d’une image que vous avez créée, qu’il vous faut mettre à jour. Cette approche requiert aucune configuration supplémentaire au moment du déploiement machine virtuelle. Toutefois, dans le `2016-03-30` version de jeux d’échelle machine virtuelle (et versions antérieures), les disques du système d’exploitation pour les ordinateurs virtuels dans l’ensemble d’échelle sont limités à un compte de stockage unique. Par conséquent, vous pouvez contenir un maximum de 40 machines virtuelles dans un jeu d’échelle, plutôt que d’utiliser la machine virtuelle 100 par échelle définissez limite avec des images de plateforme. Pour plus d’informations, voir [Échelle Set création Overview](./virtual-machine-scale-sets-design-overview.md) .

- Déployer une plateforme ou une image personnalisée qui équivaut à un hôte conteneur, puis installez votre application comme un ou plusieurs conteneurs que vous gérez avec un outil de gestion orchestrator ou de configuration. Agréable dans cette approche est que vous avez extrait votre infrastructure cloud à partir de la couche d’application et que vous pouvez conserver séparément.

## <a name="what-happens-when-a-vm-scale-set-scales-out"></a>Que se passe-t-il lorsqu’une échelle de machine virtuelle définie peut évoluer ?

Lorsque vous ajoutez un ou plusieurs ordinateurs virtuels à une échelle définie en augmentant la capacité – si manuellement ou via échelle – l’application est installée automatiquement. Pour exemple, si la valeur de l’échelle a extensions définies, ils s’exécutent sur un nouvel ordinateur virtuel chaque fois qu’il est créé. Si l’ensemble d’échelle est basé sur une image personnalisée, tout nouvel ordinateur virtuel est une copie de l’image personnalisée source. Si la valeur de l’échelle de machines virtuelles sont des hôtes de conteneur, puis vous devrez code de démarrage pour charger les conteneurs dans une Extension de Script personnalisé, ou une extension peut installer un agent qu’inscrit avec un orchestrator cluster (par exemple, Azure conteneur Service).

## <a name="how-do-you-manage-application-updates-in-vm-scale-sets"></a>Comment gérer les mises à jour de l’application de jeux d’échelle machine virtuelle ?

Les mises à jour application de jeux d’échelle de machine virtuelle, trois approches principales suivent à partir de trois méthodes de déploiement d’application précédente :

* Mise à jour avec les extensions machine virtuelle. Toutes les extensions de machine virtuelle qui sont définies pour une machine virtuelle échelle définie sont exécutées chaque fois qu’un nouvel ordinateur virtuel est déployé, une machine virtuelle existante est qu’ou une extension de mémoire virtuelle est mis à jour. Si vous devez mettre à jour votre application, mise à jour d’une application via les extensions est une approche viable directement : il vous suffit de mettre à jour de la définition d’extension. Une méthode simple pour cela est en modifiant la fileUris pour pointer vers le nouveau logiciel.

* L’approche image personnalisée immuable. Lorsque vous gâteaux l’application (ou les composants d’application) dans une image de machine virtuelle vous pouvez vous concentrer sur la création d’un pipeline fiable pour automatiser génération, de test et de déploiement des images. Vous pouvez créer votre architecture afin de faciliter le remplacement rapide d’un ensemble d’échelle intermédiaire en production. Un bon exemple de cette approche est le [pilote d’Azure Spinnaker fonctionne](https://github.com/spinnaker/deck/tree/master/app/scripts/modules/azure) - [http://www.spinnaker.io/](http://www.spinnaker.io/).

Programme de compression et Terraform prennent également en charge le Gestionnaire de ressources Azure, afin que vous pouvez également définir vos images « comme code » et les créer dans Azure, puis utilisez le disque dur virtuel dans votre jeu d’échelle. Toutefois, cette procédure fait deviendrait problématique pour les images Marketplace, où scripts extensions/personnalisée font plus importants dans la mesure où vous ne manipuler directement bits à partir de Marketplace.

* Mettre à jour des conteneurs. Extraire la gestion du cycle de vie des applications à un niveau au-dessus de l’infrastructure cloud, par exemple par encapsulation applications et les composants d’application dans des conteneurs et gérer ces conteneurs par le biais orchestrators conteneur et responsables de configuration comme Chef/Marionnette.

L’échelle définie machines virtuelles deviennent un substrat stable pour les conteneurs et requièrent uniquement occasionnelle sécurité et mises à jour du système d’exploitation. Comme indiqué, le Service de conteneur Azure est un bon exemple de cette approche et création d’un service autour de celle-ci.

## <a name="how-do-you-roll-out-an-os-update-across-update-domains"></a>Comment vous déployer une mise à jour du système d’exploitation sur les domaines de mise à jour ?

Supposons que vous souhaitez mettre à jour votre image de système d’exploitation tout en conservant l’exécution machine virtuelle échelle définie. Une pour le faire consiste à mettre à jour de la machine virtuelle images une machine virtuelle à la fois. Vous pouvez le faire avec PowerShell ou Azure infrastructure du langage commun. Il existe des commandes distinctes pour mettre à jour le modèle de machine virtuelle échelle définie (comment sa configuration est définie) et pour émettre des appels « mise à niveau manuelle » sur machines virtuelles individuels.

[Ici](https://github.com/gbowerman/vmsstools) est un exemple de script Python permettant d’automatiser le processus de mise à jour d’un domaine une mise à jour machine virtuelle échelle définie à la fois. (Inconvénient : il est plus d’une preuve de concept qu’une solution opérationnelle sécurité renforcée, vous souhaiterez peut-être ajouter quelques etc. vérification des erreurs.).

<properties
    pageTitle="Configurer une machine virtuelle en tant que bloc-notes IPython serveur | Microsoft Azure"
    description="Définissez l’un Azure Machine virtuelle pour une utilisation dans un environnement scientifique données avec IPython serveur pour analytique avancées."
    services="machine-learning"
    documentationCenter=""
    authors="bradsev"
    manager="jhubbard"
    editor="cgronlun"  />

<tags
    ms.service="machine-learning"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/19/2016"
    ms.author="xibingao;bradsev" />

# <a name="set-up-an-azure-virtual-machine-as-an-ipython-notebook-server-for-advanced-analytics"></a>Configurer une machine virtuelle Azure comme un serveur IPython bloc-notes pour analytique avancées

Cette rubrique indique comment mettre en service et configurer une machine virtuelle Azure avancées analytique qui peut être utilisée dans le cadre d’un environnement scientifique de données. La machine virtuelle Windows est configurée avec prise en charge d’outils tels que tels que le bloc-notes IPython, Explorateur de stockage Azure, AzCopy, ainsi que d’autres utilitaires pouvant être utilisés pour les projets analytique avancées. Explorateur de stockage Azure et AzCopy, par exemple, fournissent peut être pratique pour télécharger des données à Azure blob storage à partir de votre ordinateur local ou pour le télécharger sur votre ordinateur local depuis le stockage blob.

## <a name="create-vm"></a>Étape 1 : Créer un objectif général Azure machine virtuelle

Si vous disposez déjà d’une machine virtuelle Azure et que vous voulez simplement configurer un serveur IPython bloc-notes sur celui-ci, vous pouvez ignorer cette étape et passez à le [à l’étape 2 : ajouter un point de terminaison pour les blocs-notes IPython pour une machine virtuelle existante](#add-endpoint).

Avant de commencer le processus de création d’une machine virtuelle sur Azure, vous devez déterminer la taille de l’ordinateur qui est nécessaire pour traiter les données de leur projet. Machines de petite taille ont moins de mémoire et cœurs d’UC moins que machines plus grande, mais ils sont également moins coûteuses. Pour une liste des prix et des types d’ordinateurs, consultez la page <a href="http://azure.microsoft.com/pricing/details/virtual-machines/" target="_blank">Machines virtuelles tarifs</a>

1. Se connecter au <a href="https://manage.windowsazure.com" target="_blank">Portail classique Azure</a>, puis cliquez sur **Nouveau** dans le coin inférieur gauche. Une fenêtre s’affiche. Sélectionnez **Calculer** -> **MACHINE virtuelle** -> **FROM galerie**.

    ![Créer l’espace de travail][24]

2. Choisissez une des images suivantes :

    * Centre de données Windows Server 2012 R2
    * Expérience Windows Server Essentials (Windows Server 2012 R2)

    Ensuite, cliquez sur la flèche pointant vers la droite en bas à droite pour passer de la page suivante de la configuration.

    ![Créer l’espace de travail][25]

3. Entrez un nom pour la machine virtuelle que vous voulez créer, sélectionnez la taille de l’ordinateur (par défaut : A3) en fonction de la taille des données va au processus de l’ordinateur et le plus puissant souhaité l’ordinateur pour être (taille de la mémoire et le nombre de cœurs cluster), entrez un nom d’utilisateur et mot de passe pour l’ordinateur. Ensuite, cliquez sur la flèche pointant vers la droite pour accéder à la page suivante de la configuration.

    ![Créer l’espace de travail][26]

4. Sélectionnez la **Région/affinité groupe/virtuelle réseau** qui contient le **Compte de stockage** que vous envisagez d’utiliser cet ordinateur virtuel, puis ce compte de stockage. Ajouter un point de terminaison en bas dans le champ **points de terminaison** en entrant le nom du point de terminaison (« IPython » ici). Vous pouvez choisir n’importe quelle chaîne comme **nom** du point de fin et tout entier compris entre 0 et 65 536 qui est **disponible** en tant que le **PORT PUBLIC**. Le **PORT privé** doit être **9999**. Les utilisateurs doivent **éviter** l’utilisation de ports publics qui ont déjà été affectées pour les services internet. <a href="http://www.chebucto.ns.ca/~rakerman/port-table.html" target="_blank">Ports pour les Services Internet</a> fournit la liste des ports qui ont été affectées et doit être évitée.

    ![Créer l’espace de travail][27]

5. Activez la case à cocher pour démarrer la machine virtuelle processus de mise en service.

    ![Créer l’espace de travail][28]


Il peut prendre 25 de 15 minutes pour terminer la machine virtuelle processus de mise en service. Une fois que la machine virtuelle a été créée, l’état de cet ordinateur doit afficher comme **en cours d’exécution**.

![Créer l’espace de travail][29]

## <a name="add-endpoint"></a>Étape 2 : Ajouter un point de terminaison pour les blocs-notes IPython pour une machine virtuelle existante

Si vous avez créé la machine virtuelle en suivant les instructions à l’étape 1, puis le point de terminaison pour IPython bloc-notes a déjà été ajouté et vous pouvez ignorer cette étape.

Si la machine virtuelle existe déjà, et vous devez ajouter un point de terminaison pour IPython bloc-notes que vous installerez à l’étape 3 ci-dessous, première connexion au portail classique Azure, sélectionnez la machine virtuelle et ajoutez le point de terminaison pour serveur IPython bloc-notes. La figure suivante contient une capture d’écran du portail après que le point de terminaison pour IPython bloc-notes a été ajoutée à une machine virtuelle Windows.

![Créer l’espace de travail][17]

## <a name="run-commands"></a>Étape 3 : Installer IPython bloc-notes et autres outils de prise en charge

Après la création de la machine virtuelle, utilisez le protocole RDP (Remote Desktop) pour vous connecter à la machine virtuelle Windows. Pour plus d’informations, voir [comment ouvrir une session sur un ordinateur virtuel exécutant Windows Server](../virtual-machines/virtual-machines-windows-classic-connect-logon.md). Ouvrez l' **invite de commandes** (**pas la fenêtre de commande Powershell**) en tant qu' **administrateur** et exécutez la commande suivante.

    set script='https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/MachineSetup/Azure_VM_Setup_Windows.ps1'

    @powershell -NoProfile -ExecutionPolicy unrestricted -Command "iex ((new-object net.webclient).DownloadString(%script%))"

Une fois l’installation terminée, le serveur IPython bloc-notes est lancé automatiquement dans le *C:\\utilisateurs\\\<nom d’utilisateur\>\\Documents\\IPython blocs-notes* répertoire.

Lorsque vous y êtes invité, entrez un mot de passe pour le bloc-notes IPython et le mot de passe de l’administrateur de l’ordinateur. Cela permet le bloc-notes IPython à exécuter en tant que service sur l’ordinateur.

## <a name="access"></a>Étape 4 : Access IPython blocs-notes à partir d’un navigateur web
Pour accéder au serveur IPython bloc-notes, ouvrez un site web navigateur et entrée *nom DNS de l’ordinateur https://&#60;virtual > : & #60 ; numéro de port public >* dans la zone de texte URL. Ici, le *& #60 ; numéro de port public >* doit être le numéro de port que vous avez spécifié lorsque le point de terminaison IPython bloc-notes a été ajouté.

La *& #60 ; nom DNS machine virtuelle >* pouvez trouver sur le portail d’Azure classique. Une fois la connexion au portail classique, cliquez sur **MACHINES virtuelles**, sélectionnez l’ordinateur que vous avez créé, puis **tableau de bord**, le nom DNS est affiché comme suit :

![Créer l’espace de travail][19]

Vous rencontrez un message d’avertissement indiquant _présente un problème avec le certificat de sécurité de ce site Web_ (Internet Explorer) ou _votre connexion n’est pas privée_ (Chrome), comme indiqué dans les figures suivantes. Cliquez sur **Continuer pour ce site Web (non recommandé)** (Internet Explorer) ou **Avancé** , puis * *continuer à & #60 ;* Nom DNS*> (potentiellement dangereux) ** (Chrome) pour continuer. Puis entrez le mot de passe que vous avez spécifié précédemment pour accéder aux blocs-notes IPython.

**Internet Explorer :**
![créer l’espace de travail][20]

**Chrome :**
![créer l’espace de travail][21]

Une fois que vous ouvrez une session le bloc-notes IPython, un répertoire *DataScienceSamples* s’affichera sur le navigateur. Ce répertoire contient exemple IPython blocs-notes partagés par Microsoft pour aider les utilisateurs à effectuer les tâches de données scientifique. Ces exemples d’agendas IPython sont extraits de [**référentiel Github**](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/iPythonNotebooks) aux machines virtuelles pendant le serveur IPython bloc-notes configurer processus. Microsoft met à jour et met à jour ce référentiel fréquemment. Les utilisateurs peuvent visiter le référentiel Github pour obtenir les blocs-notes IPython exemple plus récemment mis à jour.
![Créer l’espace de travail][18]

## <a name="upload"></a>Étape 5 : Télécharger un bloc-notes IPython existant à partir d’un ordinateur local sur le serveur IPython bloc-notes

Blocs-notes IPython offrent un moyen simple pour les utilisateurs de télécharger un bloc-notes existant IPython sur l’ordinateur local sur le serveur IPython bloc-notes sur les ordinateurs virtuels. Une fois que les utilisateurs se connectent le bloc-notes IPython dans un navigateur web, cliquez sur dans le **répertoire** qui seront téléchargé vers le bloc-notes IPython. Ensuite, sélectionnez un fichier .ipynb IPython bloc-notes à télécharger à partir de l’ordinateur local dans l' **Explorateur de fichiers**et faites glisser-déplacer vers le répertoire IPython bloc-notes sur un navigateur web. Cliquez sur le bouton **Télécharger** pour télécharger le fichier .ipynb sur le serveur IPython bloc-notes. D’autres utilisateurs peuvent démarrer puis dans l’aide de leur navigateur web.

![Créer l’espace de travail][22]

![Créer l’espace de travail][23]


##<a name="shutdown"></a>Arrêt et supprimer l’allocation machine virtuelle lorsqu’elles pas en cours d’utilisation

Machines virtuelles Azure sont facturés comme **payez uniquement ce que vous utilisez**. Pour vous assurer que vous ne soyez pas facturé lorsque vous N'utilisez pas votre machine virtuelle, il doit se trouver dans l’état **arrêté (Deallocated)** lorsqu’elles pas en cours d’utilisation.

> [AZURE.NOTE] Si vous arrêtez la machine virtuelle à partir d’à l’intérieur de la machine virtuelle (à l’aide des options d’alimentation Windows), la machine virtuelle est arrêtée mais reste allouée. Pour vous assurer que vous ne pas continuer à seront facturés, arrêtez toujours machines virtuelles à partir du [Portail classique Azure](http://manage.windowsazure.com/). Vous pouvez également arrêter la machine virtuelle via Powershell en appelant **ShutdownRoleOperation** avec « PostShutdownAction » égale à « StoppedDeallocated ».

Pour arrêter et libérer la machine virtuelle :

1. Connectez-vous au [Portail classique Azure](http://manage.windowsazure.com/) à l’aide de votre compte.  

2. Sélectionnez **MACHINES virtuelles** à partir de la barre de navigation gauche.

3. Dans la liste des machines virtuelles, cliquez sur le nom de votre machine virtuelle, puis accédez à la page de **tableau de bord** .

4. Dans la partie inférieure de la page, cliquez sur **Arrêter**.

![Machine virtuelle arrêt][15]

La machine virtuelle sera libérée mais pas supprimée. Vous pouvez redémarrer votre machine virtuelle à tout moment à partir du portail classique Azure.

## <a name="your-azure-vm-is-ready-to-use-whats-next"></a>Votre ordinateur virtuel Azure est prête à utiliser : quel est l’avenir ?

Votre machine virtuelle est maintenant prête à utiliser dans vos exercices scientifique de données. La machine virtuelle est également prête à utiliser comme un serveur IPython bloc-notes pour l’exploration et la transformation de données et d’autres tâches conjointement avec apprentissage automatique Azure et le processus d’équipe données scientifique.

Étapes suivantes dans le processus d’équipe données scientifique sont mappées dans les [Rubriques d’apprentissage](https://azure.microsoft.com/documentation/learning-paths/cortana-analytics-process/) et peuvent inclure les étapes qui déplacement des données dans HDInsight, processus et aperçu en vue de la formation à partir des données avec Azure Machine d’apprentissage.


[15]: ./media/machine-learning-data-science-setup-virtual-machine/vmshutdown.png
[17]: ./media/machine-learning-data-science-setup-virtual-machine/add-endpoints-after-creation.png
[18]: ./media/machine-learning-data-science-setup-virtual-machine/sample-ipnbs.png
[19]: ./media/machine-learning-data-science-setup-virtual-machine/dns-name-and-host-name.png
[20]: ./media/machine-learning-data-science-setup-virtual-machine/browser-warning-ie.png
[21]: ./media/machine-learning-data-science-setup-virtual-machine/browser-warning.png
[22]: ./media/machine-learning-data-science-setup-virtual-machine/upload-ipnb-1.png
[23]: ./media/machine-learning-data-science-setup-virtual-machine/upload-ipnb-2.png
[24]: ./media/machine-learning-data-science-setup-virtual-machine/create-virtual-machine-1.png
[25]: ./media/machine-learning-data-science-setup-virtual-machine/create-virtual-machine-2.png
[26]: ./media/machine-learning-data-science-setup-virtual-machine/create-virtual-machine-3.png
[27]: ./media/machine-learning-data-science-setup-virtual-machine/create-virtual-machine-4.png
[28]: ./media/machine-learning-data-science-setup-virtual-machine/create-virtual-machine-5.png
[29]: ./media/machine-learning-data-science-setup-virtual-machine/create-virtual-machine-6.png

<properties
    pageTitle="Comment utiliser secondaire Azure plug-in avec intégration continue Hudson | Microsoft Azure"
    description="Décrit comment utiliser secondaire Azure plug-in avec une intégration continue Hudson."
    services="virtual-machines-linux"
    documentationCenter=""
    authors="rmcmurray"
    manager="wpickett"
    editor="" />

<tags
    ms.service="virtual-machines-linux"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-multiple"
    ms.devlang="java"
    ms.topic="article"
    ms.date="09/20/2016"
    ms.author="robmcm"/>

# <a name="how-to-use-the-azure-slave-plug-in-with-hudson-continuous-integration"></a>Comment utiliser secondaire Azure plug-in avec intégration continue Hudson

Plug-in pour Hudson secondaire Azure vous permet de mise en service des nœuds esclave sur Azure lors de l’exécution distribué génère.

## <a name="install-the-azure-slave-plug-in"></a>Installer le plug-in Azure secondaire

1. Dans le tableau de bord Hudson, cliquez sur **Gérer Hudson**.

1. Dans la page **Gérer les Hudson** , cliquez sur **Gérer les plug-ins**.

1. Cliquez sur l’onglet **disponible** .

1. Cliquez sur **Rechercher** et tapez **Azure** pour limiter la liste des plug-ins pertinents.

    Si vous choisissez cette option pour faire défiler la liste des plug-ins disponibles, vous trouverez secondaire Azure plug-in sous la section **gestion du Cluster et créer les Distributed** dans l’onglet **d’autres personnes** .

1. Activez la case à cocher **Azure esclave**du plug-in.

1. Cliquez sur **installer**.

1. Redémarrez Hudson.

Maintenant que le plug-in est installé, la procédure suivante serait pour configurer le plug-in dans votre profil abonnement Azure et créer un modèle qui sera utilisé lors de la création de la machine virtuelle pour le nœud esclave.

## <a name="configure-the-azure-slave-plug-in-with-your-subscription-profile"></a>Configurer secondaire Azure plug-in dans votre profil de l’abonnement

Un profil d’abonnement, également appelé paramètres de publication, est un fichier XML qui contient des informations d’identification sécurisées et des informations supplémentaires que vous devez travailler avec Azure dans votre environnement de développement. Pour configurer secondaire Azure plug-in, vous devez :

* Votre id de l’abonnement
* Un certificat de gestion de votre abonnement

Vous la trouverez dans votre [profil de l’abonnement]. Voici un exemple d’un profil de l’abonnement.

    <?xml version="1.0" encoding="utf-8"?>

        <PublishData>

        <PublishProfile SchemaVersion="2.0" PublishMethod="AzureServiceManagementAPI">

        <Subscription

            ServiceManagementUrl="https://management.core.windows.net"

            Id="<Subscription ID>"

            Name="Pay-As-You-Go"
            ManagementCertificate="<Management certificate value>" />

        </PublishProfile>

    </PublishData>

Une fois que votre profil d’abonnement, procédez comme suit pour configurer secondaire Azure plug-in.

1. Dans le tableau de bord Hudson, cliquez sur **Gérer Hudson**.

1. Cliquez sur **configurer le système**.

1. Faites défiler la page jusqu'à la section **Cloud** .

1. Cliquez sur **Ajouter nouveau cloud > Microsoft Azure**.

    ![ajouter de nouveaux cloud][add new cloud]

    Ceci affiche les champs où vous devez entrer les détails de votre abonnement.

    ![configurer les profils][configure profile]

1. Copiez le certificat abonnement id et la gestion de votre profil d’abonnement et collez-les dans les champs appropriés.

    Lorsque vous copiez le certificat de gestion et id d’abonnement, **ne pas** inclure les guillemets entourant les valeurs.

1. Cliquez sur **configuration vérifier**.

1. Lorsque la configuration vérifiée avec succès, cliquez sur **Enregistrer**.

## <a name="set-up-a-virtual-machine-template-for-the-azure-slave-plug-in"></a>Configurer un modèle de machine virtuelle pour secondaire Azure plug-in

Un modèle de machine virtuelle définit les paramètres que du plug-in utilisera pour créer un nœud esclave sur Azure. Dans la procédure suivante nous allons créer le modèle pour un Ubuntu VM.

1. Dans le tableau de bord Hudson, cliquez sur **Gérer Hudson**.

1. Cliquez sur **configurer le système**.

1. Faites défiler la page jusqu'à la section **Cloud** .

1. Dans la section **Cloud** , recherchez **Ajouter Azure Machine virtuelle modèle** et cliquez sur le bouton **Ajouter** .

    ![Ajouter un modèle de machine virtuelle][add vm template]

1. Spécifiez un nom de service cloud dans le champ **nom** . Si le nom que vous spécifiez fait référence à un service cloud existant, la machine virtuelle sera déployée dans ce service. Dans le cas contraire, Azure créera un nouvel identifiant.

1. Dans le champ **Description** , entrez le texte qui décrit le modèle que vous créez. Ces informations sont applique uniquement aux fins de document à l’appui et ne sont pas utilisées dans une machine virtuelle de mise en service.

1. Dans le champ **étiquettes** , entrez **linux**. Cette étiquette est utilisée pour identifier le modèle que vous créez et par la suite utilisée pour référencer le modèle lors de la création d’une tâche Hudson.

1. Sélectionnez une région dans lequel la machine virtuelle doit être créée.

1. Sélectionnez la taille de mémoire virtuelle appropriée.

1. Spécifiez un compte de stockage dans lequel la machine virtuelle doit être créée. Assurez-vous qu’il est dans la même région en tant que le service cloud, que vous allez utiliser. Si vous voulez nouveau stockage doit être créé, vous pouvez laissez ce champ vide.

1. Date et heure de rétention spécifie le nombre de minutes avant que Hudson supprime un esclave inactif. Laisser ce champ à la valeur par défaut de 60.

1. Dans **l’utilisation**, sélectionnez la condition appropriée lorsque ce nœud esclave sera utilisé. Pour l’instant, sélectionnez **Utilisez ce nœud autant que possible**.

    À ce stade, votre formulaire peut se présenter un peu comme ceci :

    ![configuration de modèle][template config]

1. Dans **Image famille ou Id** , vous devez spécifier quelle image système est installé sur votre ordinateur virtuel. Vous pouvez sélectionner dans une liste de familles de l’image ou spécifier une image personnalisée.

    Si vous voulez sélectionner dans une liste de familles de l’image, entrez le premier caractère (respecte la casse) du nom de famille de l’image. Par exemple, à taper **U** fait apparaître une liste de familles de serveur Ubuntu. Une fois que vous sélectionnez dans la liste, Jenkins utilise la dernière version de cette image système à partir de cette famille lors de la configuration de votre ordinateur virtuel.

    ![Liste de famille du système d’exploitation][OS family list]

    Si vous avez une image personnalisée que vous souhaitez utiliser à la place, entrez le nom de cette image personnalisée. Noms image personnalisée ne sont pas affichés dans une liste de sorte que vous devez vous assurer que le nom est entré correctement.    

    Pour ce didacticiel, tapez **U** pour afficher une liste d’images Ubuntu, puis **Ubuntu Server 14.04 LTS**.

1. Pour **lancer la méthode**, sélectionnez **SSH**.

1. Copiez le script ci-dessous et collez-le dans le champ **script d’initialisation** .

        # Install Java

        sudo apt-get -y update

        sudo apt-get install -y openjdk-7-jdk

        sudo apt-get -y update --fix-missing

        sudo apt-get install -y openjdk-7-jdk

        # Install git

        sudo apt-get install -y git

        #Install ant

        sudo apt-get install -y ant

        sudo apt-get -y update --fix-missing

        sudo apt-get install -y ant

    Le **script d’initialisation** sera exécutée après que la machine virtuelle est créée. Dans cet exemple, le script installe Java, git et ant.

1. Dans les champs **nom d’utilisateur** et **mot de passe** , entrez vos valeurs par défaut pour le compte d’administrateur qui est créé sur votre ordinateur virtuel.

1. Cliquez sur **Vérifier le modèle** pour vérifier si les paramètres que vous avez spécifié sont valides.

1. Cliquez sur **Enregistrer**.

## <a name="create-a-hudson-job-that-runs-on-a-slave-node-on-azure"></a>Créer une tâche Hudson qui s’exécute sur un nœud esclave Azure

Dans cette section, vous allez créer une tâche Hudson qui s’exécutera sur un nœud esclave Azure.

1. Dans le tableau de bord Hudson, cliquez sur **Nouvelle tâche**.

1. Entrez un nom pour la tâche que vous créez.

1. Pour le type de tâche, sélectionnez **créer une tâche de logiciels de forme libre**.

1. Cliquez sur **OK**.

1. Dans la page configuration de la tâche, sélectionnez **restreindre où ce projet peut être exécuté**.

1. Sélectionnez le **menu nœud et étiquette** et **linux** (nous spécifié cette étiquette lors de la création du modèle de machine virtuelle dans la section précédente).

1. Dans la section **créer** , cliquez sur **Ajouter générer étape** et sélectionnez **Execute shell**.

1. Modifier le script suivant, en remplaçant **{votre nom de compte github}**, **{nom de votre projet}**et **{répertoire de votre projet}** par les valeurs appropriées, puis collez le script modifié dans la zone de texte qui s’affiche.

        # Clone from git repo

        currentDir="$PWD"

        if [ -e {your project directory} ]; then

            cd {your project directory}

            git pull origin master

        else

            git clone https://github.com/{your github account name}/{your project name}.git

        fi

        # change directory to project

        cd $currentDir/{your project directory}

        #Execute build task

        ant

1. Cliquez sur **Enregistrer**.

1. Dans le tableau de bord Hudson, recherchez la tâche que vous venez de créer et cliquez sur l’icône de **planifier une génération** .

Hudson ensuite créer un nœud esclave en utilisant le modèle créé dans la section précédente et exécuter le script que vous avez indiquée à l’étape de génération de cette tâche.

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur l’utilisation de Azure avec Java, voir le [Centre de développement Java Azure].

<!-- URL List -->

[Centre de développement Java Azure]: https://azure.microsoft.com/develop/java/
[profil de l’abonnement]: http://go.microsoft.com/fwlink/?LinkID=396395

<!-- IMG List -->

[add new cloud]: ./media/virtual-machines-azure-slave-plugin-for-hudson/hudson-setup-addcloud.png
[configure profile]: ./media/virtual-machines-azure-slave-plugin-for-hudson/hudson-setup-configureprofile.png
[add vm template]: ./media/virtual-machines-azure-slave-plugin-for-hudson/hudson-setup-addnewvmtemplate.png
[template config]: ./media/virtual-machines-azure-slave-plugin-for-hudson/hudson-setup-templateconfig1-withdata.png
[OS family list]: ./media/virtual-machines-azure-slave-plugin-for-hudson/hudson-oslist.png


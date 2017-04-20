<properties
    pageTitle="Comment utiliser secondaire Azure plug-in avec intégration continue Jenkins | Microsoft Azure"
    description="Décrit comment utiliser secondaire Azure plug-in avec une intégration continue Jenkins."
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

# <a name="how-to-use-the-azure-slave-plug-in-with-jenkins-continuous-integration"></a>Comment utiliser secondaire Azure plug-in avec intégration continue Jenkins

Vous pouvez utiliser secondaire Azure plug-in pour Jenkins à disposition des nœuds esclave sur Azure lors de l’exécution distribué génère.

## <a name="install-the-azure-slave-plug-in"></a>Installer le plug-in secondaire Azure

1. Dans le tableau de bord Jenkins, cliquez sur **Gérer Jenkins**.

1. Dans la page **Gérer les Jenkins** , cliquez sur **Gérer les plug-ins**.

1. Cliquez sur l’onglet **disponible** .

1. Dans le champ filtre au-dessus de la liste des plug-ins disponibles, tapez **Azure** pour limiter la liste des plug-ins pertinents.

    Si vous choisissez cette option pour faire défiler la liste des plug-ins disponibles, vous trouverez secondaire Azure plug-in sous la section **gestion du Cluster et créer les distribué** .

1. Activez la case à cocher **Azure esclave du plug-in** .

1. Cliquez sur **installer sans redémarrage** ou **Télécharger maintenant et installer après le redémarrage**.

Maintenant que le plug-in est installé, les étapes suivantes sont pour configurer le plug-in dans votre profil abonnement Azure et créer un modèle qui sera utilisé lors de la création de la machine virtuelle pour le nœud esclave.


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

            Id="<Subscription ID value>"

            Name="Pay-As-You-Go"
            ManagementCertificate="<Management certificate value>" />

        </PublishProfile>

    </PublishData>

Une fois que vous avez votre profil d’abonnement, procédez comme suit pour configurer secondaire Azure plug-in :

1. Dans le tableau de bord Jenkins, cliquez sur **Gérer Jenkins**.

1. Cliquez sur **configurer le système**.

1. Faites défiler la page jusqu'à la section **Cloud** .

1. Cliquez sur **Ajouter nouveau cloud > Microsoft Azure**.

    ![section du cloud][cloud section]

    Ceci affiche les champs où vous devez entrer les détails de votre abonnement.

    ![configuration d’un abonnement][subscription configuration]

1. Copiez les valeurs de certificat abonnement id et la gestion de votre profil d’abonnement et collez-les dans les champs appropriés.

    Lorsque vous copiez le certificat de gestion et id d’abonnement, n’incluez pas les guillemets entourant les valeurs.

1. Cliquez sur **vérifier la Configuration**.

1. Lorsque la configuration vérifiée être correcte, cliquez sur **Enregistrer**.

## <a name="set-up-a-virtual-machine-template-for-the-azure-slave-plug-in"></a>Configurer un modèle de machine virtuelle pour secondaire Azure plug-in

Un modèle de machine virtuelle définit les paramètres utilisés par le plug-in pour créer un nœud esclave sur Azure. Dans la procédure suivante, nous allons créer un modèle pour une machine virtuelle Ubuntu.

1. Dans le tableau de bord Jenkins, cliquez sur **Gérer Jenkins**.

1. Cliquez sur **configurer le système**.

1. Faites défiler la page jusqu'à la section **Cloud** .

1. Dans la section **Cloud** , **Ajouter un modèle de Machine virtuelle Azure**de rechercher, puis cliquez sur **Ajouter**.

    ![Ajouter un modèle de machine virtuelle][add vm template]

    Ceci affiche les champs où vous entrez les détails sur le modèle que vous créez.

    ![configuration générale vide][blank general configuration]

1. Dans la zone **nom** , entrez un nom de service cloud Azure. Si le nom que vous avez entré fait référence à un service cloud existant, l’ordinateur virtuel sera déployé dans ce service. Dans le cas contraire, Azure créera un nouvel identifiant.

1. Dans la zone **Description** , entrez le texte qui décrit le modèle que vous créez. Cela s’applique uniquement aux vos enregistrements et n’est pas utilisé dans une machine virtuelle.

1. La zone **étiquettes** est utilisée pour identifier le modèle que vous créez et par la suite utilisée pour référencer le modèle lors de la création d’une tâche Jenkins. Dans notre cas, entrez **linux** dans cette zone.

1. Dans la liste des **régions** , cliquez sur la zone où la machine virtuelle seront créée.

1. Dans la liste **Taille de la Machine virtuelle** , cliquez sur la taille appropriée.

1. Dans la zone **Nom de compte de stockage** , spécifiez un compte de stockage où la machine virtuelle seront créée. Assurez-vous qu’il est dans la même région en tant que le service cloud, que vous allez utiliser. Si vous voulez nouveau stockage doit être créé, vous pouvez laisser cette case vide.

1. Date et heure de rétention spécifie le nombre de minutes avant que Jenkins supprime un esclave inactif. Laisser ce champ à la valeur par défaut de 60. Vous pouvez également choisir d’arrêter secondaire au lieu de sa suppression lorsqu’il est inactif. Pour ce faire, activez la case à cocher **Arrêt uniquement (ne supprimez pas) après le temps de rétention** .

1. Dans la liste de **l’utilisation** , cliquez sur la condition appropriée lorsque ce nœud esclave sera utilisé. Pour l’instant, cliquez sur **Utilisez ce nœud autant que possible**.

    À ce stade, votre formulaire doit ressembler quelque peu similaire à ceci :

    ![configuration de modèle général de point de contrôle][checkpoint general template config]

    L’étape suivante consiste à fournir des détails sur l’image de système d’exploitation que vous voulez votre esclave être créées dans.

1. Dans la zone **Image famille ou Id** , vous devez spécifier quelle image système sera installé sur votre ordinateur virtuel. Vous pouvez sélectionner dans une liste de familles de l’image ou spécifier une image personnalisée.

    Si vous voulez sélectionner dans une liste de familles de l’image, entrez le premier caractère (respecte la casse) du nom de famille de l’image. Par exemple, à taper **U** fait apparaître une liste de familles de serveur Ubuntu. Une fois que vous sélectionnez dans la liste, Jenkins utilise la dernière version de cette image système à partir de cette famille lors de la configuration de votre machine virtuelle.

    ![Exemple de liste Image système d’exploitation][OS Image list sample]

    Si vous avez une image personnalisée que vous souhaitez utiliser à la place, entrez le nom de cette image personnalisée. Noms image personnalisée ne sont pas affichés dans une liste, afin que vous devez vous assurer que le nom est entré correctement.

    Pour ce didacticiel, tapez **U** pour faire apparaître une liste d’images Ubuntu, puis cliquez sur **Ubuntu Server 14.04 LTS**.

1. Dans la liste **Méthode barre de lancement** , cliquez sur **SSH**.

1. Copiez le script ci-dessous et collez-le dans la zone de **Script d’initialisation** .

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

    Le script d’initialisation sera exécuté après que la machine virtuelle est créée. Dans cet exemple, le script installe Java, Git et ant.

1. Dans les zones **nom d’utilisateur** et **mot de passe** , entrez vos valeurs par défaut pour le compte d’administrateur qui est créé sur votre ordinateur virtuel.

1. Cliquez sur **Vérifier le modèle** pour vérifier si les paramètres que vous avez spécifié sont valides.

1. Cliquez sur **Enregistrer**.


## <a name="create-a-jenkins-job-that-runs-on-a-slave-node-on-azure"></a>Créer une tâche Jenkins qui s’exécute sur un nœud esclave Azure

Dans cette section, vous allez créer une tâche Jenkins qui s’exécutera sur un nœud esclave Azure. Vous devez disposer de votre propre projet vers le haut sur GitHub à suivre.

1. Dans le tableau de bord Jenkins, cliquez sur **Nouvel élément**.

1. Entrez un nom pour la tâche que vous créez.

1. Pour le type de projet, cliquez sur **projet Freestyle**.

1. Cliquez sur **Ok**.

1. Dans la page configuration de la tâche, sélectionnez **restreindre où ce projet peut être exécuté**.

1. Dans la zone **Expression de l’étiquette** , entrez **linux**. Dans la section précédente, nous avons créé un modèle d’esclave que nous nommé **linux**, qui est ce que nous devons spécifier ici.

1. Dans la section **créer** , cliquez sur **Ajouter générer étape** et sélectionnez **Execute shell**.

1. Modifier le script suivant, en remplaçant **(votre nom de compte GitHub)**, **(votre nom de projet)**et **(votre répertoire de projet)** par les valeurs appropriées, puis collez le script modifié dans la zone de texte qui s’affiche.

        # Clone from git repo

        currentDir="$PWD"

        if [ -e (your project directory) ]; then

            cd (your project directory)

            git pull origin master

        else

            git clone https://github.com/(your GitHub account name)/(your project name).git

        fi

        # change directory to project

        cd $currentDir/(your project directory)

        #Execute build task

        ant

1. Cliquez sur **Enregistrer**.

1. Dans le tableau de bord Jenkins, pointez sur la tâche que vous venez de créer et cliquez sur la flèche déroulante pour afficher les options de la tâche.

1. Cliquez sur **créer maintenant**.

Jenkins ensuite créer un nœud esclave en utilisant le modèle créé dans la section précédente et exécuter le script que vous avez indiquée à l’étape de génération de cette tâche.

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur l’utilisation de Azure avec Java, voir le [Centre de développement Java Azure].

<!-- URL List -->

[Centre de développement Java Azure]: https://azure.microsoft.com/develop/java/
[profil de l’abonnement]: http://go.microsoft.com/fwlink/?LinkID=396395

<!-- IMG List -->

[cloud section]: ./media/virtual-machines-azure-slave-plugin-for-jenkins/jenkins-cloud-section.png
[subscription configuration]: ./media/virtual-machines-azure-slave-plugin-for-jenkins/jenkins-account-configuration-fields.png
[add vm template]: ./media/virtual-machines-azure-slave-plugin-for-jenkins/jenkins-add-vm-template.png
[blank general configuration]: ./media/virtual-machines-azure-slave-plugin-for-jenkins/jenkins-slave-template-general-configuration-blank.png
[checkpoint general template config]: ./media/virtual-machines-azure-slave-plugin-for-jenkins/jenkins-slave-template-general-configuration.png
[OS Image list sample]: ./media/virtual-machines-azure-slave-plugin-for-jenkins/jenkins-os-family-list-sample.png
<properties
    pageTitle="Activer l’accès à distance pour les déploiements Azure dans Éclipse"
    description="Découvrez comment activer l’accès à distance pour les déploiements Azure à l’aide de la boîte à outils Azure pour Eclipse."
    services=""
    documentationCenter="java"
    authors="rmcmurray"
    manager="wpickett"
    editor=""/>

<tags
    ms.service="multiple"
    ms.workload="na"
    ms.tgt_pltfrm="multiple"
    ms.devlang="Java"
    ms.topic="article"
    ms.date="08/11/2016" 
    ms.author="robmcm"/>

<!-- Legacy MSDN URL = https://msdn.microsoft.com/library/azure/hh690951.aspx -->

# <a name="enabling-remote-access-for-azure-deployments-in-eclipse"></a>Activer l’accès à distance pour les déploiements Azure dans Éclipse

Pour aider à résoudre vos déploiements, vous pouvez activer et utiliser l’accès distant pour vous connecter à la machine virtuelle qui héberge votre déploiement. La fonctionnalité d’accès à distance s’appuie sur le protocole RDP (Remote Desktop). Vous pouvez configurer l’accès à distance pour votre déploiement une fois que vous avez publié sur Azure, ou si vous utilisez Eclipse avec un système d’exploitation Windows, vous pouvez configurer l’accès à distance avant de publier sur Azure. Notez que vous aurez besoin d’un client de bureau à distance est compatible avec votre système d’exploitation pour vous connecter à machine virtuelle de votre déploiement dans Azure.

## <a name="how-to-enable-remote-access-before-you-deploy-to-azure"></a>Comment activer l’accès à distance avant de déployer sur Azure

> [AZURE.NOTE] Pour activer l’accès à distance avant de déployer votre application sur Azure, vous devez exécuter Eclipse sous Windows.

L’image suivante montre les propriétés **d’Accès à distance** boîte de dialogue utilisée pour activer l’accès à distance.

![][ic719494]

Il existe deux manières d’afficher la boîte de dialogue des propriétés **D’accès à distance** :

* Cliquez sur le lien **Avancé** dans la section **Accès à distance** de la boîte de dialogue **publier sur Azure** .
* Ouvrez la boîte de dialogue **Propriétés** de votre projet Azure.

Lorsque vous créez un nouveau projet de déploiement d’Azure, le projet n’ont pas accès à distance activés par défaut. Toutefois, vous pouvez facilement activer l’accès à distance en spécifiant le nom d’utilisateur et mot de passe dans la boîte de dialogue **publier sur Azure** . Le mot de passe d’accès à distance est chiffré à l’aide de certificats X.509. Si vous n’utilisez pas de votre propre certificat, fournissent le chiffrement s’appuie sur un certificat auto-signé fourni avec le plug-in Azure pour Eclipse. Ce certificat auto-signé est dans le dossier de **certificat** de votre projet Azure, stockée dans un fichier de certificat public (SampleRemoteAccessPublic.cer) et un fichier de certificat personnel informations d’échange (SampleRemoteAccessPrivate.pfx). Celle-ci contient la clé privée pour le certificat, et qu’il possède un mot de passe par défaut, **le mot de passe1**. Toutefois, étant donné que ce mot de passe est connue, le certificat par défaut doit être utilisé uniquement pour apprendre à utiliser à des fins non pour un déploiement de production. Donc différente à des fins pédagogiques, lorsque vous voulez activé sessions à distance pour vos déploiements, vous devez cliquer sur le lien **Avancé** dans la boîte de dialogue **publier sur Azure** permettent de spécifier votre propre certificat. Notez que vous devez télécharger la version PFX du certificat à votre service hébergé au sein du portail de gestion Azure, afin que Azure peut déchiffrer le mot de passe utilisateur.

Le reste de ce didacticiel montre comment activer l’accès à distance pour un projet de déploiement d’Azure qui a été créé avec l’accès à distance est désactivé. Dans le cadre de ce didacticiel, nous allons créer un nouveau certificat auto-signé et son fichier .pfx créera un mot de passe de votre choix. Vous avez également la possibilité d’utilisation d’un certificat émis par une autorité de certification.

## <a name="how-to-enable-remote-access-after-you-have-deployed-to-azure"></a>Comment activer l’accès à distance une fois que vous avez déployé sur Azure

Pour activer l’accès à distance une fois que vous avez déployé sur Azure, procédez comme suit :

1. Se connecter au portail de gestion Azure à l’aide de votre compte Azure
1. Dans votre liste des **Services en nuage**, sélectionnez votre service cloud déployé
1. Dans la page web du service cloud, cliquez sur le lien **configurer**
1. Au bas de la page de configuration, cliquez sur le lien **à distance**
1. Lorsque la boîte de dialogue s’affiche :
    * Spécifiez le rôle dont vous souhaitez activer l’accès à distance
    * Activez la case à cocher **Activer le Bureau à distance**
    * Spécifiez un nom d’utilisateur et mot de passe que vous voulez utiliser pour l’accès à distance
    * Sélectionnez le certificat à utiliser
1. Cliquez sur **OK** 

Vous verrez un message indiquant que la modification de votre configuration est en cours, qui peut prendre quelques minutes. Une fois la modification de la configuration terminée, suivez les étapes décrites dans la section **pour se connecter à distance** plus loin dans cet article.
    
## <a name="how-to-enable-remote-access-in-your-package"></a>Comment activer l’accès à distance dans votre package

1. Au sein de volet de l’Explorateur de projet de Eclipse, avec le bouton droit de votre projet Azure, puis cliquez sur **Propriétés**.

1. Dans la boîte de dialogue **Propriétés** , dans le volet de gauche, développez **Azure** , puis cliquez sur **Accès à distance**.

1. Dans la boîte de dialogue **Accès à distance** , vérifiez que **Activer tous les rôles accepter des connexions de bureau à distance avec ces informations d’identification** est cochée.

1. Spécifiez un nom d’utilisateur pour la connexion Bureau à distance.

1. Spécifiez et confirmez le mot de passe pour l’utilisateur. Les valeurs de nom et mot de passe utilisateur définies dans cette boîte de dialogue seront utilisés lorsque vous effectuez une connexion Bureau à distance. (Notez qu’il s’agit d’un mot de passe distinct à partir de votre mot de passe PFX.)

1. Spécifiez la date d’expiration pour le compte d’utilisateur.

1. Cliquez sur **Nouveau** pour créer un nouveau certificat auto-signé. (Par ailleurs, vous pouvez sélectionner un certificat à partir de votre système de fichiers ou d’espace de travail via les boutons **d’espace de travail** ou de **système de fichiers** , respectivement, mais à des fins de ce didacticiel, nous allons créer un nouveau certificat.)

    * Dans la boîte de dialogue **Nouveau certificat** , spécifiez et confirmez le mot de passe que vous utiliserez pour votre fichier PFX.

    * Accepter la valeur fournie par **Nom (NC)**, ou utilisez un nom personnalisé.

    * Spécifiez le chemin d’accès et le nom où le nouveau certificat, sous forme de .cer, doit être enregistré. Pour cette étape et l’étape suivante, vous pouvez utiliser le dossier de **certificat** de votre projet Azure, mais vous pouvez choisir un autre emplacement. Dans le cadre de ce didacticiel, nous allons utiliser **c:\mycert\mycert.cer**. (Créer le dossier **c:\mycert** avant de poursuivre, ou utilisez un dossier existant si vous le souhaitez).

    * Spécifiez le chemin d’accès et le nom où le nouveau certificat et sa clé privée, sous forme de .pfx, doit être enregistrés. Dans le cadre de ce didacticiel, nous allons utiliser **c:\mycert\mycert.pfx**. Votre boîte de dialogue **Nouveau certificat** doit ressembler à ce qui suit (mettre à jour les chemins d’accès de dossier si vous n’utilisez pas **c:\mycert**) :

        ![][ic712275]

    * Cliquez sur **OK** pour fermer la boîte de dialogue **Nouveau certificat** .

1. Votre boîte de dialogue **Accès à distance** doit ressembler à ce qui suit :</p>

    ![][ic719495]

1. Cliquez sur **OK** pour fermer la boîte de dialogue **Accès à distance** .
    
Reconstruire votre application, avec la version définie pour le déploiement vers le cloud.

## <a name="to-log-in-remotely"></a>Pour se connecter à distance

Une fois que votre instance de rôle est prêt, vous pouvez à distance à se connecter à la machine virtuelle qui héberge votre application.

* Si vous utilisez Éclipse sur Windows et vous sélectionnez l’option **Démarrer le Bureau à distance sur déployer** pendant votre déploiement vers Azure, s’affiche avec un écran de connexion connexion Bureau à distance au démarrage de votre déploiement. Lorsque vous êtes invité au nom d’utilisateur et mot de passe, entrez les valeurs que vous avez spécifié pour l’utilisateur distant et seront en mesure de se connecter.

* Une autre façon de vous connecter à distance est via le <a href="http://go.microsoft.com/fwlink/?LinkID=512959">Portail de gestion Azure</a>:

    * Dans la vue **Cloud Services** du portail de gestion Azure, cliquez sur votre service cloud et cliquez sur **Instances**, cliquez sur une instance spécifique, puis cliquez sur le bouton **se connecter** . Le bouton de **connexion** s’affiche comme suit dans la barre de commandes :

        ![][ic659273]

    * Après avoir cliqué sur le bouton **se connecter** , vous devrez ouvrir un fichier RDP. Ouvrez le fichier et suivez les invites. (Vous pouvez également enregistrer ce fichier sur votre ordinateur local et puis exécutez le fichier en double-cliquant dessus pour se connecter à distance à votre machine virtuelle sans avoir à vous déplacer tout d’abord le portail de gestion.)

    * Lorsque vous êtes invité au nom d’utilisateur et mot de passe, entrez les valeurs que vous avez spécifié pour l’utilisateur distant et seront en mesure de se connecter.

> [AZURE.NOTE] Si vous exécutez un système d’exploitation non Windows, vous devez utiliser un client de bureau à distance est compatible avec votre système d’exploitation et suivez les étapes pour configurer ce client avec les paramètres dans le fichier RDP que vous avez téléchargé.

## <a name="see-also"></a>Voir aussi

[Kit de ressources Azure pour Éclipse][]

[Création d’une Application World Hello pour Azure dans Éclipse][]

[Installez le Kit de ressources de Azure pour Éclipse][] 

Pour plus d’informations sur l’utilisation de Azure avec Java, voir le [Centre de développement Java Azure][].

<!-- URL List -->

[Centre de développement Java Azure]: http://go.microsoft.com/fwlink/?LinkID=699547
[Azure Management Portal]: http://go.microsoft.com/fwlink/?LinkID=512959
[Kit de ressources Azure pour Éclipse]: http://go.microsoft.com/fwlink/?LinkID=699529
[Création d’une Application World Hello pour Azure dans Éclipse]: http://go.microsoft.com/fwlink/?LinkID=699533
[Installez le Kit de ressources de Azure pour Éclipse]: http://go.microsoft.com/fwlink/?LinkId=699546

<!-- IMG List -->

[ic712275]: ./media/azure-toolkit-for-eclipse-enabling-remote-access-for-azure-deployments/ic712275.png
[ic719495]: ./media/azure-toolkit-for-eclipse-enabling-remote-access-for-azure-deployments/ic719495.png
[ic719494]: ./media/azure-toolkit-for-eclipse-enabling-remote-access-for-azure-deployments/ic719494.png
[ic659273]: ./media/azure-toolkit-for-eclipse-enabling-remote-access-for-azure-deployments/ic659273.png

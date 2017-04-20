<properties
    pageTitle="Déploiement des déploiements"
    description="Découvrez comment déployer des déploiements à l’aide de la boîte à outils Azure pour Eclipse."
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

<!-- Legacy MSDN URL = https://msdn.microsoft.com/library/azure/dn268601.aspx -->

# <a name="deploying-large-deployments"></a>Déploiement des déploiements #

Si votre déploiement est trop volumineux pour être inclus dans le dossier approot par défaut, vous pouvez utiliser une ressource de stockage local comme dossier racine de déploiement pour votre JDK et serveur d’applications.

## <a name="to-use-a-local-storage-resource-as-the-deployment-root-folder-for-large-deployments"></a>Pour utiliser une ressource de stockage local en tant que dossier racine de déploiement pour les déploiements de grande taille ##

1. Créer une nouvelle ressource de stockage local. Le nom de la ressource n’a pas d’importance. Ressources de stockage sont définies au niveau du rôle. Est la plus rapide pour accéder à la boîte de dialogue de configuration du stockage local, à partir de laquelle vous pouvez créer une nouvelle ressource de stockage local, en procédant comme suit : avec le bouton droit le rôle dans l’affichage de **l’Explorateur de projets** (développez le nœud de votre projet Azure si vous ne voyez pas le rôle), cliquez sur **Azure**, puis cliquez sur **Stockage Local**. Dans la boîte de dialogue **Stockage Local** , cliquez sur **Ajouter** pour créer une nouvelle ressource de stockage local.
1. Définir la taille souhaitée au moins de 2 048 Mo (rien moins peut provoquer le même fichier taille des problèmes que vous pouvez rencontrer dans l’approot).
1. Assurez-vous que l’option **Effacer le contenu lorsque l’instance de rôle est suppression initiale** est activée ; Cela vous aidera empêcher logique de démarrage de déploiement de s’exécuter dans les conflits avec les fichiers existants dans la ressource lorsque l’instance de rôle est suppression initiale.
1. Assurez-vous que la valeur de la **variable d’environnement le stockage de chemin d’accès de la ressource après le déploiement** est définie avec la chaîne **DEPLOYROOT**. Votre boîte de dialogue stockage local ressource est semblable à ce qui suit.
    ![][ic667943]

Par ailleurs, si vous utilisez **DEPLOYROOT** comme le *nom* de la ressource locale et ne pas modifier la variable d’environnement généré automatiquement nom (qui est définie à **DEPLOYROOT_PATH** le cas échéant), qui envisageable ainsi que votre application.

Vous trouverez plus d’informations sur la création d’une ressource de stockage local propriétés de [stockage Local][].

## <a name="see-also"></a>Voir aussi ##

[Kit de ressources Azure pour Éclipse][]

[Création d’une Application World Hello pour Azure dans Éclipse][]

[Installez le Kit de ressources de Azure pour Éclipse][] 

Pour plus d’informations sur l’utilisation de Azure avec Java, voir le [Centre de développement Java Azure][].

<!-- URL List -->

[Centre de développement Java Azure]: http://go.microsoft.com/fwlink/?LinkID=699547
[Kit de ressources Azure pour Éclipse]: http://go.microsoft.com/fwlink/?LinkID=699529
[Création d’une Application World Hello pour Azure dans Éclipse]: http://go.microsoft.com/fwlink/?LinkID=699533
[Installez le Kit de ressources de Azure pour Éclipse]: http://go.microsoft.com/fwlink/?LinkId=699546
[Propriétés du stockage local]: http://go.microsoft.com/fwlink/?LinkID=699525#local_storage_properties

<!-- IMG List -->

[ic667943]: ./media/azure-toolkit-for-eclipse-deploying-large-deployments/ic667943.png

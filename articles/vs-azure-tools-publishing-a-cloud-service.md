<properties
   pageTitle="Publication d’un Service de nuage en utilisant les outils Azure | Microsoft Azure"
   description="Découvrez comment publier des projets de service cloud Azure à l’aide de Visual Studio."
   services="visual-studio-online"
   documentationCenter="na"
   authors="TomArcher"
   manager="douge"
   editor="" />
<tags
   ms.service="multiple"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="multiple"
   ms.date="08/15/2016"
   ms.author="tarcher" />

# <a name="publishing-a-cloud-service-using-the-azure-tools"></a>Publication d’un Service de nuage en utilisant les outils d’Azure

En utilisant les outils Azure pour Microsoft Visual Studio, vous pouvez publier votre application Azure directement à partir de Visual Studio. Visual Studio prend en charge la publication intégrée pour la mise en place ou l’environnement de Production d’un service cloud.

Avant de pouvoir publier une application Azure, vous devez posséder un abonnement Azure. Vous devez également configurer un compte de service et de stockage cloud devant être utilisé par votre application. Vous pouvez configurer ces sur le [portail classique Azure](http://go.microsoft.com/fwlink/?LinkID=213885).

>[AZURE.IMPORTANT] Lorsque vous publiez, vous pouvez sélectionner l’environnement de déploiement de votre service cloud. Vous devez également sélectionner un compte de stockage qui permet de stocker le package d’application pour le déploiement. Après le déploiement, le package d’application est supprimé à partir du compte de stockage.

Lorsque vous développez et tester une application Azure, vous pouvez utiliser le déploiement Web pour publier les modifications apportées par incréments pour vos rôles web. Une fois que vous publiez votre application dans un environnement de déploiement, déploiement Web vous permet de déployer des modifications directement à la machine virtuelle exécutant le rôle web. Vous n’avez pas à publier votre ensemble de l’application Azure chaque fois que vous voulez mettre à jour votre rôle web pour tester les modifications et package. Avec cette approche, vous pouvez avoir vos modifications de rôle web disponibles dans le cloud pour le test sans attendre à ce que votre application publiée dans un environnement de déploiement.

Utilisez les procédures suivantes pour publier votre application Azure et mettre à jour un rôle web à l’aide de déploiement Web :

- Publier ou compresser une application à partir de Visual Studio Azure

- Mettre à jour un rôle web dans le cadre du développement et cycle de test

## <a name="publish-or-package-an-azure-application-from-visual-studio"></a>Publier ou compresser une Application à partir de Visual Studio Azure

Lorsque vous publiez votre application Azure, vous pouvez effectuer une des opérations suivantes :

- Créer un package de service : vous pouvez utiliser ce package et le fichier de configuration du service pour publier votre application dans un environnement de déploiement à partir du [Portail classique Azure](http://go.microsoft.com/fwlink/?LinkID=213885).

- Publier votre projet Visual Studio Azure : pour publier votre application directement sur Azure, vous utilisez l’Assistant Publication. Pour plus d’informations, voir [Publier Assistant Création d’applications Azure](vs-azure-tools-publish-azure-application-wizard.md).

### <a name="to-create-a-service-package-from-visual-studio"></a>Pour créer un package de service à partir de Visual Studio

1. Lorsque vous êtes prêt à publier votre application, ouvrez l’Explorateur de solutions, ouvrez le menu contextuel pour le projet Azure qui contient vos rôles et choisissez Publier.

1. Pour créer un package de service uniquement, procédez comme suit :  

  1. Dans le menu contextuel pour le projet Azure, choisissez **Package**.

  1. Dans la boîte de dialogue **Package Azure Application** , sélectionnez la configuration du service pour lequel vous voulez créer un package, puis la configuration de génération.

  1. (facultatif) Pour activer Bureau à distance pour le service cloud après que l’avoir publiée, activez la case à cocher **Activer le Bureau à distance pour tous les rôles** , puis sur **paramètres** pour configurer le Bureau à distance. Si vous souhaitez déboguer votre service cloud après sa publication, activez le débogage distant en sélectionnant **Débogueur distant activer pour tous les rôles**.

      Pour plus d’informations, voir [à l’aide de bureau à distance avec Azure rôles](vs-azure-tools-remote-desktop-roles.md).

  1. Pour créer le lot, cliquez sur le lien de **Package** .

      Explorateur de fichiers indique l’emplacement du fichier du package nouvellement créé. Vous pouvez copier cet emplacement afin que vous puissiez l’utiliser à partir du [portail classique Azure](http://go.microsoft.com/fwlink/?LinkID=213885).

  1. Pour publier ce package dans un environnement de déploiement, vous devez utiliser cet emplacement comme emplacement de Package lorsque vous créez un service cloud et déployez ce package dans un environnement grâce au [portail classique Azure](http://go.microsoft.com/fwlink/?LinkID=213885).

1. (Facultatif) Pour annuler le processus de déploiement, dans le menu contextuel pour la ligne dans le journal d’activité, cliquez sur **Annuler et supprimer**. Cela arrête le processus de déploiement et supprime l’environnement de déploiement d’Azure.

    >[AZURE.NOTE] Pour supprimer cet environnement déploiement après que qu’il a été déployé, vous devez utiliser le [portail classique Azure](http://go.microsoft.com/fwlink/?LinkID=213885).

1. (Facultatif) Une fois que vos instances de rôles ont commencé, Visual Studio affiche automatiquement l’environnement de déploiement dans le nœud de **Services de Cloud** dans l’Explorateur de serveur. À partir de là, vous pouvez afficher l’état des instances de rôle individuels. Consultez [les ressources de gestion Azure avec l’Explorateur de Cloud](vs-azure-tools-resources-managing-with-cloud-explorer.md). L’illustration suivante montre les instances de rôle lorsqu’ils sont toujours dans l’état de l’initialisation :

    ![VST_DeployComputeNode](./media/vs-azure-tools-publishing-a-cloud-service/IC744134.png)

## <a name="update-a-web-role-as-part-of-the-development-and-testing-cycle"></a>Mettre à jour un rôle Web dans le cadre du développement et Cycle de test

Si l’infrastructure de votre application serveur principal est stable, mais les rôles d’administrateur web devront souvent plus mises à jour, vous pouvez utiliser le déploiement Web pour mettre à jour qu’un rôle web dans votre projet. Ceci est pratique lorsque vous ne voulez pas reconstruire et redéployez les rôles de travail principal, ou si vous avez plusieurs rôles web et que vous souhaitez mettre à jour uniquement un des rôles web.

### <a name="requirements"></a>Configuration requise

Voici la configuration requise pour utiliser le déploiement Web pour mettre à jour votre rôle web :

- **De développement et de test sert uniquement :** Les modifications sont apportées directement à la machine virtuelle où le rôle web est en cours d’exécution. Si cette machine virtuelle doit être suppression initiale, les modifications sont perdues car le package d’origine que vous avez publié sert à recréer la machine virtuelle pour le rôle. Vous devez publier votre application pour obtenir les dernières modifications pour le rôle web.

- **Uniquement les rôles web pouvant être mis à jour :** Rôles de travail ne peut pas être mis à jour. En outre, vous ne peut pas mettre à jour le RoleEntryPoint dans role.cs web.

- **Peut seulement prendre en charge une seule instance d’un rôle web :** Vous ne peut pas contenir plusieurs instances de n’importe quel rôle web dans votre environnement de déploiement. Toutefois, plusieurs rôles web chaque avec une seule instance sont prises en charge.

- **Vous devez activer des connexions Bureau à distance :** Cela est nécessaire afin que déploiement Web peuvent utiliser l’utilisateur et mot de passe pour vous connecter à la machine virtuelle pour déployer les modifications sur le serveur qui exécute Internet Information Services (IIS). En outre, vous devrez peut-être vous connecter à la machine virtuelle pour ajouter un certificat de confiance à IIS sur cet ordinateur virtuel. (Ainsi que la connexion à distance IIS qui est utilisé par déploiement Web est sécurisée).

La procédure suivante suppose que vous utilisez l’Assistant **Application de Azure publier** .

### <a name="to-enable-web-deploy-when-you-publish-your-application"></a>Pour activer le déploiement Web lorsque vous publiez votre Application

1. Pour activer l' **Activer le déploiement Web** pour la case à cocher de tous les web rôles, vous devez d’abord configurer connexions Bureau à distance. Sélectionnez **Activer le Bureau à distance** pour tous les rôles, puis fournissez les informations d’identification qui servira à vous connecter à distance dans la zone de **Configuration de bureau à distance** qui s’affiche. Pour plus d’informations, reportez-vous [à l’aide de bureau à distance avec Azure rôles](vs-azure-tools-remote-desktop-roles.md) .

1. Pour activer le déploiement Web pour tous les rôles dans votre application web, sélectionnez **Activer le déploiement Web pour tous les rôles web**.

    Un triangle jaune apparaît. Déployer Web utilise un certificat auto-signé approuvé par défaut, ce qui n’est pas recommandé pour le téléchargement des données sensibles. Si vous avez besoin sécuriser ce processus pour les données sensibles, vous pouvez ajouter un certificat SSL à utiliser pour les connexions de déploiement Web. Ce certificat doit être un certificat approuvé. Pour plus d’informations sur la façon de procéder, voir la section **À rendre Web déployer Secure** plus loin dans cette rubrique.

1. Cliquez sur **suivant** pour afficher l’écran de **Résumé** , puis sur **Publier** pour déployer le service cloud.

    Le service cloud est publié. La machine virtuelle créé a des connexions à distance activées pour IIS afin que déploiement Web pouvant être utilisée pour mettre à jour vos rôles web sans les republier.

    >[AZURE.NOTE] Si vous avez plusieurs instances configuré pour un rôle web, un message d’avertissement s’affiche indiquant que chaque rôle web est limités à une instance uniquement dans le package est créé pour publier votre application. Sélectionnez **OK** pour continuer. Comme indiqué dans la section Configuration requise, vous pouvez avoir plusieurs rôles web mais qu’une seule instance de chaque rôle.

### <a name="to-update-your-web-role-by-using-web-deploy"></a>Pour mettre à jour votre rôle Web à l’aide de déploiement Web

1. Pour utiliser le déploiement Web, apportez les modifications de code au projet pour une de vos rôles web dans Visual Studio que vous souhaitez publier, puis avec le bouton droit ce nœud de projet dans votre solution et pointez sur **Publier**. La boîte de dialogue **Publier le site Web** s’affiche.

1. (Facultatif) Si vous avez ajouté un certificat SSL approuvé à utiliser pour les connexions à distance pour IIS, vous pouvez désactiver la case à cocher **Autoriser les certificats non approuvés** . Pour plus d’informations sur la façon d’ajouter un certificat pour renforcer la sécurité de déploiement Web, voir la section **À rendre Web déployer sécurisé** plus loin dans cette rubrique.

1. Pour utiliser le déploiement Web, le mécanisme de publication doit le nom d’utilisateur et mot de passe que vous avez configuré pour votre connexion Bureau à distance lorsque vous avez publié tout d’abord le package.

  1. Dans la zone **nom d’utilisateur**, entrez le nom d’utilisateur.

  1. Dans **mot de passe**, entrez le mot de passe.

  1. (Facultatif) Si vous voulez enregistrer ce mot de passe dans ce profil, cliquez sur **Enregistrer le mot de passe**.

1. Pour publier les modifications apportées à votre rôle web, cliquez sur **Publier**.

    La ligne d’état affiche **Publier en main**. Une fois la publication terminée, **publication réussie** s’affiche. Les modifications ont été déployées maintenant au rôle web sur votre ordinateur virtuel. Vous pouvez maintenant commencer votre application Azure dans l’environnement Azure pour tester vos modifications.

### <a name="to-make-web-deploy-secure"></a>Pour sécuriser Web déployer

1. Déployer Web utilise un certificat auto-signé approuvé par défaut, ce qui n’est pas recommandé pour le téléchargement des données sensibles. Si vous avez besoin sécuriser ce processus pour les données sensibles, vous pouvez ajouter un certificat SSL à utiliser pour les connexions de déploiement Web. Ce certificat doit être un certificat approuvé, vous obtenez à partir d’une autorité de certification (CA).

    Pour sécuriser déploiement Web pour chaque machine virtuelle pour chacun de vos rôles web, vous devez télécharger le certificat de confiance que vous souhaitez utiliser pour le web déployer au [portail classique Azure](http://go.microsoft.com/fwlink/?LinkID=213885). Cela permet de garantir que le certificat est ajouté à la machine virtuelle qui est créée pour le rôle web lorsque vous publiez votre application.

1. Pour ajouter un certificat SSL fiable à IIS à utiliser pour les connexions à distance, procédez comme suit :

  1. Pour vous connecter à la machine virtuelle exécutant le rôle web, sélectionnez l’instance du rôle web dans **L’Explorateur Cloud** ou **Explorateur de serveurs**, puis la commande **se connecter à l’aide de bureau à distance** . Pour plus d’informations sur la façon de vous connecter à la machine virtuelle, voir [L’aide de bureau à distance avec Azure rôles](vs-azure-tools-remote-desktop-roles.md).

      Votre navigateur vous invite à télécharger un. Fichier RDP.

  1. Pour ajouter un certificat SSL, ouvrez le service de gestion dans le Gestionnaire des services Internet. Dans le gestionnaire, activer SSL en ouvrant le lien **liaisons** dans le volet **Action** . La boîte de dialogue **Ajouter la liaison de Site** apparaît. Sélectionnez **Ajouter**, puis HTTPS dans la liste déroulante **Type** . Dans la liste des **certificats SSL** , sélectionnez le certificat SSL que vous aviez signé par une autorité de certification et que vous avez téléchargées sur le [portail classique Azure](http://go.microsoft.com/fwlink/?LinkID=213885). Pour plus d’informations, voir [Configurer les paramètres de connexion pour le Service de gestion](http://go.microsoft.com/fwlink/?LinkId=215824).

      >[AZURE.NOTE] Si vous ajoutez un certificat SSL fiable, le triangle jaune n’apparaît plus dans l' **Assistant Publication**.

## <a name="include-files-in-the-service-package"></a>Inclure des fichiers dans le Package de Service

Vous devrez peut-être inclure des fichiers spécifiques dans votre package de services afin qu’ils soient disponibles sur l’ordinateur virtuel qui est créé pour un rôle. Par exemple, vous souhaiterez peut-être ajouter un .exe ou un fichier .msi qui est utilisé par un script de démarrage pour votre package de services. Ou vous devrez peut-être ajouter un assembly nécessitant un projet de rôle web rôle ou collaborateur. Pour inclure des fichiers, ils doivent être ajoutés à la solution pour votre application Azure.

### <a name="to-include-files-in-the-service-package"></a>Pour inclure les fichiers dans le package de service

1. Pour ajouter un assembly à un package de service, procédez comme suit :

  1. Dans **L’Explorateur** , ouvrez le nœud de projet pour le projet auquel il manque l’assembly référencé.

  1. Pour ajouter l’assembly au projet, ouvrir le menu contextuel du dossier **références** , puis sur **Ajouter une référence**. La boîte de dialogue Ajouter une référence s’affiche.

  1. Sélectionnez la référence que vous voulez ajouter, puis sélectionnez le bouton **OK** .

      La référence est ajoutée à la liste sous le dossier **références** .

  1. Ouvrir le menu contextuel pour l’assembly que vous avez ajoutée, puis sélectionnez **Propriétés**. La fenêtre **Propriétés** s’affiche.

      Pour inclure cet assembly dans le package de service, dans la **liste copie locale** choisissez **True**.

1. Dans **L’Explorateur** , ouvrez le nœud de projet pour le projet auquel il manque l’assembly référencé.

1. Pour ajouter l’assembly au projet, ouvrir le menu contextuel du dossier **références** , puis sur **Ajouter une référence**. La boîte de dialogue **Ajouter une référence** s’affiche.

1. Sélectionnez la référence que vous voulez ajouter, puis sélectionnez le bouton **OK** .

    La référence est ajoutée à la liste sous le dossier **références** .

1. Ouvrir le menu contextuel pour l’assembly que vous avez ajoutée, puis sélectionnez **Propriétés**. La fenêtre Propriétés s’affiche.

1. Pour inclure cet assembly dans le package de service, dans la liste de **Copie locale** , choisissez **True**.

1. Pour inclure des fichiers dans le package de service qui ont été ajoutés à votre projet de rôle web, ouvrez le menu contextuel pour le fichier et puis cliquez sur **Propriétés**. Dans la fenêtre **Propriétés** , choisissez **contenu** dans la liste **Action de génération** .

1. Pour inclure des fichiers dans le package de service qui ont été ajoutés à votre projet de rôle de collaborateur, ouvrir le menu contextuel pour le fichier, puis sur **Propriétés**. Dans la fenêtre **Propriétés** , cliquez sur **Copier si plus récent** dans la zone de liste **Copier dans le répertoire de sortie** .

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur la publication sur Azure à partir de Visual Studio, voir [Publier Assistant Création d’applications Azure](vs-azure-tools-publish-azure-application-wizard.md).

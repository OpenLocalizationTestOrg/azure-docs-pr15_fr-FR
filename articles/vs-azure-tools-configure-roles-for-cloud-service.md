<properties
   pageTitle="Configurer les rôles d’un Service Cloud Azure avec Visual Studio | Microsoft Azure"
   description="Découvrez comment installer et configurer des rôles pour les services en nuage Azure à l’aide de Visual Studio."
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

# <a name="configure-the-roles-for-an-azure-cloud-service-with-visual-studio"></a>Configurer les rôles d’un Service Cloud Azure avec Visual Studio

Un service cloud Azure peut contenir au moins un collaborateur ou rôles web. Pour chaque rôle, vous devez définir comment ce rôle a été configurée et également configurer l’exécution de ce rôle. Pour en savoir plus sur les rôles dans les services en nuage, voir la vidéo d' [Introduction aux Services Cloud Azure](https://channel9.msdn.com/Series/Windows-Azure-Cloud-Services-Tutorials/Introduction-to-Windows-Azure-Cloud-Services). Les informations relatives à votre service cloud sont stockés dans les fichiers suivants :

- **ServiceDefinition.csdef**

    Fichier de définition du service définit les paramètres pour l’exécution de votre service cloud, y compris les rôles requis, points de terminaison et la taille de la machine virtuelle. Aucune des données stockées dans ce fichier peuvent être modifiées lorsque votre rôle est en cours d’exécution.

- **ServiceConfiguration.cscfg**

    Le fichier de configuration de service configure le nombre d’instances d’un rôle est exécuté et les valeurs des paramètres définis pour un rôle. Les données stockées dans ce fichier peuvent être modifiées pendant l’exécution de votre rôle.

Pour pouvoir stocker les différentes valeurs pour ces paramètres pour l’exécution de votre rôle, vous pouvez avoir plusieurs configurations de service. Vous pouvez utiliser une configuration de services différente pour chaque environnement de déploiement. Par exemple, vous pouvez définir votre chaîne de connexion de compte de stockage pour utiliser l’émulateur de stockage Azure local dans une configuration du service local et créer un autre configuration du service pour utiliser le stockage Azure dans le cloud.

Lorsque vous créez un nouveau service cloud Azure dans Visual Studio, deux configurations de service sont créées par défaut. Ces configurations sont ajoutées à votre projet Azure. Les configurations sont nommées :

- ServiceConfiguration.Cloud.cscfg

- ServiceConfiguration.Local.cscfg

## <a name="configure-an-azure-cloud-service"></a>Configurer un service cloud Azure

Vous pouvez configurer un service cloud Azure à partir de l’Explorateur de solutions dans Visual Studio, comme le montre l’illustration suivante.

![Configurer le Service de nuage](./media/vs-azure-tools-configure-roles-for-cloud-service/IC713462.png)

### <a name="to-configure-an-azure-cloud-service"></a>Configurer un service cloud Azure

1. Pour configurer chaque rôle dans votre projet Azure à partir de **L’Explorateur de solutions**, ouvrez le menu contextuel pour le rôle dans le projet Azure, puis sur **Propriétés**.

    Une page avec le nom du rôle s’affiche dans l’éditeur Visual Studio. La page affiche les champs de l’onglet **Configuration** .

1. Dans la liste de **Configuration du Service** , sélectionnez le nom de la configuration du service que vous voulez modifier.

    Si vous souhaitez apporter des modifications à toutes les configurations de service pour ce rôle, vous pouvez choisir **Toutes les Configurations**.

    >[AZURE.IMPORTANT] Si vous choisissez une configuration de service spécifiques, certaines propriétés sont désactivées car ils ne peuvent être définies pour toutes les configurations. Pour modifier ces propriétés, vous devez choisir toutes les Configurations.

    Vous pouvez sélectionner maintenant un onglet pour mettre à jour les propriétés activées sur cette vue.

## <a name="change-the-number-of-role-instances"></a>Modifier le nombre d’instances de rôles

Pour améliorer les performances de votre service cloud, vous pouvez modifier le nombre d’instances d’un rôle en cours d’exécution, en fonction du nombre d’utilisateurs ou de la charge attendue pour un rôle particulier. Un ordinateur virtuel distinct est créé pour chaque instance d’un rôle lorsque le service cloud s’exécute dans Azure. Cela affecte la facturation pour le déploiement de ce service cloud. Pour plus d’informations sur la facturation, voir [comprendre votre facture pour Microsoft Azure](billing/billing-understand-your-bill.md).

### <a name="to-change-the-number-of-instances-for-a-role"></a>Pour modifier le nombre d’instances pour un rôle

1. Sélectionnez l’onglet **Configuration** .

1. Dans la liste de **Configuration du Service** , cliquez sur la configuration du service que vous souhaitez mettre à jour.

    >[AZURE.NOTE] Vous pouvez définir le nombre d’instances pour une configuration de service spécifique ou pour toutes les configurations de service.

1. Dans la zone de texte **nombre d’instances** , entrez le nombre d’instances que vous souhaitez démarrer pour ce rôle.

    >[AZURE.NOTE] Chaque instance est exécuté sur un ordinateur virtuel distinct lorsque vous publiez votre service cloud sur Azure.

1. Cliquez sur le bouton **Enregistrer** dans la barre d’outils pour enregistrer ces modifications sur le fichier de configuration de service.

## <a name="manage-connection-strings-for-storage-accounts"></a>Gérer les chaînes de connexion pour les comptes de stockage

Vous pouvez ajouter, supprimer ou modifier des chaînes de connexion pour vos configurations de service. Vous souhaiterez différentes chaînes de connexion pour les configurations de services différente. Par exemple, vous souhaiterez une chaîne de connexion locaux pour une configuration de service local qui a la valeur `UseDevelopmentStorage=true`. Vous souhaiterez également configurer une configuration du service cloud qui utilise un compte de stockage dans Azure.

>[AZURE.WARNING] Lorsque vous entrez les informations de clé de compte de stockage Azure pour une chaîne de connexion de compte de stockage, ces informations sont stockées localement dans le fichier de configuration de service. Toutefois, ces informations ne sont actuellement pas stockées en tant que texte chiffré.

En utilisant une valeur différente pour chaque configuration du service, vous n’avez pas à utiliser différentes chaînes de connexion dans votre service de nuage ou modifier votre code lorsque vous publiez votre service cloud sur Azure. Vous pouvez utiliser le même nom pour la chaîne de connexion dans votre code et la valeur sera différente, selon la configuration du service que vous sélectionnez lorsque vous créez votre service cloud ou lorsque vous publiez ce dernier.

### <a name="to-manage-connection-strings-for-storage-accounts"></a>Pour gérer les chaînes de connexion pour les comptes de stockage

1. Sélectionnez l’onglet **paramètres** .

1. Dans la liste de **Configuration du Service** , cliquez sur la configuration du service que vous souhaitez mettre à jour.

    >[AZURE.NOTE] Vous pouvez mettre à jour les chaînes de connexion pour une configuration de service spécifique, mais si vous avez besoin ajouter ou supprimer une chaîne de connexion, vous devez sélectionner toutes les Configurations.

1. Pour ajouter une chaîne de connexion, cliquez sur le bouton **Ajouter un paramètre** . Une nouvelle entrée est ajoutée à la liste.

1. Dans la zone de texte **nom** , tapez le nom que vous voulez utiliser pour la chaîne de connexion.

1. Dans la liste déroulante **Type** , sélectionnez **Chaîne de connexion**.

1. Pour modifier la valeur de la chaîne de connexion, cliquez sur le bouton points de suspension (...). La boîte de dialogue **Créer une chaîne de connexion stockage** s’affiche.

1. Pour utiliser l’émulateur de compte de stockage local, cliquez sur le bouton d’option **émulateur de stockage de Microsoft Azure** , puis sur le bouton **OK** .

1. Pour utiliser un compte de stockage dans Azure, cliquez sur le bouton d’option de **votre abonnement** et sélectionnez le compte de stockage de votre choix.

1. Pour utiliser les informations d’identification personnalisées, cliquez sur le bouton options **entrés manuellement les informations d’identification** . Entrez le nom de compte de stockage et la clé primaire ou de la deuxième. Pour plus d’informations sur la création d’un compte de stockage et entrez les informations du compte de stockage dans la boîte de dialogue **Créer une chaîne de connexion stockage** , voir [préparer pour publier ou déployer une application Azure depuis Visual Studio](vs-azure-tools-cloud-service-publish-set-up-required-services-in-visual-studio.md).

1. Pour supprimer une chaîne de connexion, sélectionnez la chaîne de connexion, puis cliquez sur le bouton **Supprimer un paramètre** .

1. Cliquez sur l’icône **Enregistrer** dans la barre d’outils pour enregistrer ces modifications sur le fichier de configuration de service.

1. Pour accéder à la chaîne de connexion dans le fichier de configuration de service, vous devez obtenir la valeur du paramètre de configuration. Le code suivant montre un exemple où stockage blob est créé et des données téléchargées à l’aide d’une chaîne de connexion `MyConnectionString` à partir du fichier de configuration de service lorsqu’un utilisateur choisit **Button1** dans la page Default.aspx dans le rôle web pour un service cloud Azure. Ajoutez les éléments suivants à l’aide des instructions Default.aspx.cs :

    ```
    using Microsoft.WindowsAzure;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.ServiceRuntime;
    ```

1. Ouvrez Default.aspx.cs en mode Création et ajoutez un bouton de la boîte à outils. Ajoutez le code suivant à la `Button1_Click` méthode. Ce code utilise `GetConfigurationSettingValue` pour obtenir la valeur à partir du fichier de configuration de service pour la chaîne de connexion. Un blob est créée dans le compte de stockage référencées dans la chaîne de connexion `MyConnectionString` et enfin le programme ajoute du texte au blob.

    ```
    protected void Button1_Click(object sender, EventArgs e)
    {
        // Setup the connection to Azure Storage
        var storageAccount = CloudStorageAccount.Parse(RoleEnvironment.GetConfigurationSettingValue("MyConnectionString"));
        var blobClient = storageAccount.CreateCloudBlobClient();
        // Get and create the container
        var blobContainer = blobClient.GetContainerReference("quicklap");
        blobContainer.CreateIfNotExists();
        // upload a text blob
        var blob = blobContainer.GetBlockBlobReference(Guid.NewGuid().ToString());
        blob.UploadText("Hello Azure");

    }
    ```

## <a name="add-custom-settings-to-use-in-your-azure-cloud-service"></a>Ajouter des paramètres personnalisés à utiliser dans votre service cloud Azure

Paramètres personnalisés dans le fichier de configuration de service vous permettent d’ajouter un nom et une valeur pour une chaîne pour une configuration de service spécifique. Vous pouvez choisir d’utiliser ce paramètre pour configurer une fonctionnalité dans votre service de nuage en lisant la valeur du paramètre et l’utilisation de cette valeur pour contrôler la logique dans votre code. Vous pouvez modifier ces valeurs de configuration de service sans avoir à recréer votre package de services ou lorsque votre service cloud s’exécute. Votre code peut vérifier pour les notifications de lorsqu’un paramètre est modifié. Consultez [l’Événement RoleEnvironment.Changing](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleenvironment.changing.aspx).

Vous pouvez ajouter, supprimer ou modifier des paramètres personnalisés pour vos configurations de service. Vous pouvez définir différentes valeurs pour ces chaînes pour les configurations de services différente.

En utilisant une valeur différente pour chaque configuration du service, vous n’avez pas à utiliser différentes chaînes dans votre service de nuage ou modifier votre code lorsque vous publiez votre service cloud sur Azure. Vous pouvez utiliser le même nom pour la chaîne dans votre code et la valeur sera différente, selon la configuration du service que vous sélectionnez lorsque vous créez votre service cloud ou lorsque vous publiez ce dernier.

### <a name="to-add-custom-settings-to-use-in-your-azure-cloud-service"></a>Pour ajouter des paramètres personnalisés à utiliser dans votre service cloud Azure

1. Sélectionnez l’onglet **paramètres** .

1. Dans la liste de **Configuration du Service** , cliquez sur la configuration du service que vous souhaitez mettre à jour.

    >[AZURE.NOTE] Vous pouvez mettre à jour des chaînes pour une configuration de service spécifique, mais si vous avez besoin ajouter ou supprimer une chaîne, vous devez sélectionner **Toutes les Configurations**.

1. Pour ajouter une chaîne, cliquez sur le bouton **Ajouter un paramètre** . Une nouvelle entrée est ajoutée à la liste.

1. Dans la zone de texte **nom** , tapez le nom que vous souhaitez utiliser pour la chaîne.

1. Dans la liste déroulante **Type** , choisissez **chaîne**.

1. Pour ajouter ou modifier la valeur de la chaîne, dans la zone **valeur** , tapez la nouvelle valeur.

1. Pour supprimer une chaîne, sélectionnez la chaîne, puis cliquez sur le bouton **Supprimer un paramètre** .

1. Cliquez sur le bouton **Enregistrer** dans la barre d’outils pour enregistrer ces modifications sur le fichier de configuration de service.

1. Pour accéder à la chaîne dans le fichier de configuration de service, vous devez obtenir la valeur du paramètre de configuration.

    Vous devez vous assurer que le suivant en utilisant les instructions sont déjà ajoutées au Default.aspx.cs comme vous l’avez fait dans la procédure précédente.

    ```
    using Microsoft.WindowsAzure;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.ServiceRuntime;
    ```

1. Ajoutez le code suivant à la `Button1_Click` méthode pour accéder à cette chaîne de la même façon que vous accédez à une chaîne de connexion. Votre code peut puis exécuter du code spécifique en fonction de la valeur de la chaîne de paramètres pour le fichier de configuration du service utilisé.

    ```
    var settingValue = RoleEnvironment.GetConfigurationSettingValue("MySetting");
    if (settingValue == “ThisValue”)
    {
    // Perform these lines of code
    }
    ```

## <a name="manage-local-storage-for-each-role-instance"></a>Gérer le stockage local pour chaque instance de rôle

Vous pouvez ajouter le stockage de système de fichiers locaux pour chaque instance d’un rôle. Vous pouvez stocker des données locales ici qui ne sont pas besoin d’être accessible par d’autres rôles. Les données que vous n’avez pas besoin d’enregistrer dans la table, blob ou le stockage de base de données SQL peuvent être stockées dans cette page. Par exemple, vous pouvez utiliser ce stockage local en cache des données qui devront être utilisé à nouveau. Ces données enregistrées ne sont pas accessibles par les autres instances d’un rôle. 

Paramètres de stockage local s’appliquent à toutes les configurations de service. Vous ne pouvez ajouter, supprimer ou modifier le stockage local pour toutes les configurations de service.

### <a name="to-manage-local-storage-for-each-role-instance"></a>Pour gérer le stockage local pour chaque instance de rôle

1. Sélectionnez l’onglet **Stockage Local** .

1. Dans la liste de **Configuration du Service** , sélectionnez **Toutes les Configurations**.

1. Pour ajouter une entrée de stockage local, cliquez sur le bouton **Ajouter un stockage Local** . Une nouvelle entrée est ajoutée à la liste.

1. Dans la zone de texte **nom** , tapez le nom que vous souhaitez utiliser cet espace de stockage local.

1. Dans la zone de texte **taille** , entrez la taille en Mo dont vous avez besoin pour cet espace de stockage local.

1. Pour supprimer les données de ce stockage local lors de la machine virtuelle pour ce rôle est suppression initiale, activez la case à cocher **Nettoyer rôle Corbeille** .

1. Pour modifier une entrée de stockage local existante, sélectionnez la ligne que vous devez mettre à jour. Vous pouvez ensuite modifier les champs, comme décrit dans les étapes précédentes.

1. Pour supprimer une entrée de stockage local, sélectionnez l’entrée de stockage dans la liste, puis cliquez sur le bouton de **Suppression de stockage Local** .

1. Pour enregistrer ces modifications aux fichiers de configuration du service, cliquez sur l’icône **Enregistrer** dans la barre d’outils.

1. Pour accéder au stockage local que vous avez ajoutés dans le fichier de configuration de service, vous devez obtenir la valeur du paramètre de configuration ressources locales. Utilisez les lignes de code suivantes pour accéder à cette valeur et créez un fichier nommé **MyStorageTest.txt** et écrivez une ligne de données de test dans ce fichier. Vous pouvez ajouter ce code dans la `Button_Click` méthode que vous avez utilisées dans les procédures précédentes :

1. Vous devez vous assurer que le suivant en utilisant instructions sont ajoutées à Default.aspx.cs :

    ```
    using System.IO;
    using System.Text;
    ```

1. Ajoutez le code suivant à la `Button1_Click` méthode. Cela crée le fichier dans le stockage local et écrit les données de test dans ce fichier.

    ```
    // Retrieve an object that points to the local storage resource
    LocalResource localResource = RoleEnvironment.GetLocalResource("LocalStorage1");

    //Define the file name and path
    string[] paths = { localResource.RootPath, "MyStorageTest.txt" };
    String filePath = Path.Combine(paths);

    using (FileStream writeStream = File.Create(filePath))
    {
          Byte[] textToWrite = new UTF8Encoding(true).GetBytes("Testing Web role storage");
          writeStream.Write(textToWrite, 0, textToWrite.Length);
    }
    ```

1. (Facultatif) Pour afficher ce fichier que vous avez créé lors de l’exécution de votre service cloud localement, procédez comme suit :

  1. Exécutez le rôle web et sélectionnez **Button1** pour vous assurer que le code à l’intérieur de `Button1_Click` est appelée.

  1. Dans la zone de notification, ouvrez le menu contextuel de l’icône Azure, puis sélectionnez **Afficher l’interface utilisateur émulateur de calculer**. La boîte de dialogue **Émulateur de calcul Azure** s’affiche.

  1. Sélectionnez le rôle web.

  1. Dans la barre de menus, cliquez sur **Outils**, **Ouvrir magasin local**. Une fenêtre de l’Explorateur Windows apparaît.

  1. Dans la barre de menus, entrez **MyStorageTest.txt** dans la zone de texte **Rechercher** , puis sur **entrée** pour démarrer la recherche.

    Le fichier s’affiche dans les résultats de recherche.

  1. Pour afficher le contenu du fichier, ouvrez le menu contextuel du fichier, puis sélectionnez **Ouvrir**.

## <a name="collect-cloud-service-diagnostics"></a>Recueillir les diagnostics de service cloud

Vous pouvez collecter des données de diagnostic pour votre service cloud Azure. Ces données sont ajoutées à un compte de stockage. Vous souhaiterez différentes chaînes de connexion pour les configurations de services différente. Par exemple, vous pouvez définir un compte de stockage local pour une configuration de service local qui a la valeur UseDevelopmentStorage = true. Vous souhaiterez également configurer une configuration du service cloud qui utilise un compte de stockage dans Azure. Pour plus d’informations sur les diagnostics de Windows Azure, voir collecter les données journalisation par à l’aide de diagnostic Azure.

>[AZURE.NOTE] La configuration du service local est déjà configurée pour utiliser les ressources locales. Si vous utilisez la configuration du service cloud pour publier votre service cloud Azure, la chaîne de connexion que vous spécifiez lors de la publication est également utilisée pour la chaîne de connexion diagnostics sauf si vous avez spécifié une chaîne de connexion. Si vous compressez votre service cloud à l’aide de Visual Studio, la chaîne de connexion dans la configuration du service n’est pas modifiée.

### <a name="to-collect-cloud-service-diagnostics"></a>Pour collecter les diagnostics de service cloud

1. Sélectionnez l’onglet **Configuration** .

1. Dans la liste de **Configuration du Service** , cliquez sur la configuration du service que vous souhaitez mettre à jour ou sélectionnez **Toutes les Configurations**.

    >[AZURE.NOTE] Vous pouvez mettre à jour le compte de stockage pour une configuration de service spécifique, mais si vous souhaitez activer ou désactiver les diagnostics, vous devez choisir toutes les Configurations.

1. Pour activer les diagnostics, activez la case à cocher **Activer les Diagnostics** .

1. Pour modifier la valeur du compte de stockage, cliquez sur le bouton points de suspension (...).

    La boîte de dialogue **Créer une chaîne de connexion stockage** s’affiche.

1. Pour utiliser une chaîne de connexion locale, choisissez stockage Azure émulateur option et cliquez sur le bouton **OK** .

1. Pour utiliser un compte de stockage associé à votre abonnement Azure, choisissez l’option de **votre abonnement** .

1. Pour utiliser un compte de stockage pour la chaîne de connexion locale, choisissez l’option **entrés manuellement les informations d’identification** .

    Pour plus d’informations sur la création d’un compte de stockage et entrez les informations du compte de stockage dans la boîte de dialogue **Créer une chaîne de connexion stockage** , voir [préparer pour publier ou déployer une application Azure depuis Visual Studio](vs-azure-tools-cloud-service-publish-set-up-required-services-in-visual-studio.md).

1. Sélectionnez le compte de stockage que vous souhaitez utiliser dans la zone **nom de compte**.

    Si vous êtes manuellement entrer vos informations d’identification de compte de stockage, copiez ou tapez votre clé primaire dans la **clé de compte**. Cette clé peut être copiée à partir du [portail classique Azure](http://go.microsoft.com/fwlink/?LinkID=213885). Pour copier cette clé, procédez comme suit dans l’affichage des **Comptes de stockage** dans le [portail classique Azure](http://go.microsoft.com/fwlink/?LinkID=213885):
    
  1. Sélectionnez le compte de stockage que vous souhaitez utiliser pour votre service cloud.

  1. Cliquez sur le bouton **Gérer les touches d’accès rapide** situé au bas de l’écran. La boîte de dialogue **Gérer les touches d’accès** s’affiche.

  1. Pour copier la touche d’accès rapide, cliquez sur le bouton **Copier dans le Presse-papiers** . Vous pouvez maintenant coller cette clé dans le champ de **clé de compte** .

1. Pour utiliser le compte de stockage que vous fournissez, comme la chaîne de connexion pour les diagnostics (et la mise en cache) lorsque vous publiez votre service cloud sur Azure, activez la case à cocher **mettre à jour développement stockage chaînes de connexion pour les Diagnostics et de mise en cache avec stockage Azure lors de la publication sur Azure les informations d’identification du compte** .

1. Cliquez sur le bouton **Enregistrer** dans la barre d’outils pour enregistrer ces modifications sur le fichier de configuration de service.

## <a name="change-the-size-of-the-virtual-machine-used-for-each-role"></a>Modifier la taille de la machine virtuelle utilisée pour chaque rôle

Vous pouvez définir la taille de la machine virtuelle pour chaque rôle. Vous ne pouvez définir cette taille pour toutes les configurations de service. Si vous sélectionnez une taille inférieure de l’ordinateur, puis moins processeur cœurs, mémoire et disque local stockage est attribué. La bande passante allouée est également plus petite. Pour plus d’informations sur ces tailles et les ressources affectées, voir [formats pour les Services en nuage](cloud-services/cloud-services-sizes-specs.md).

Les ressources nécessaires pour chaque machine virtuelle dans Azure affecte le coût de l’exécution de votre service cloud dans Azure. Pour plus d’informations sur la facturation Azure, voir [comprendre votre facture pour Microsoft Azure](billing/billing-understand-your-bill.md).

### <a name="to-change-the-size-of-the-virtual-machine"></a>Pour modifier la taille de la machine virtuelle

1. Sélectionnez l’onglet **Configuration** .

1. Dans la liste de **Configuration du Service** , sélectionnez **Toutes les Configurations**.

1. Pour sélectionner la taille de la machine virtuelle pour ce rôle, choisissez la taille appropriée dans la liste **taille de mémoire virtuelle** .

1. Cliquez sur le bouton **Enregistrer** dans la barre d’outils pour enregistrer ces modifications sur le fichier de configuration de service.

## <a name="manage-endpoints-and-certificates-for-your-roles"></a>Gérer les points de terminaison, les certificats pour vos rôles

Vous configurez des points de terminaison de mise en réseau en spécifiant le protocole, le numéro de port et, pour HTTPS, les informations du certificat SSL. Versions antérieures à juin 2012 prend en charge HTTP, HTTPS et TCP. La version de juin 2012 prend en charge les protocoles et UDP. Vous ne pouvez pas utiliser UDP pour les points de terminaison d’entrée dans l’émulateur cluster. Vous pouvez utiliser ce protocole uniquement pour les points de terminaison internes.

Pour améliorer la sécurité de votre service cloud Azure, vous pouvez créer des points de terminaison qui utilisent le protocole HTTPS. Par exemple, si vous avez un service cloud qui est utilisé par les clients aux commandes d’achat, vous voulez vous assurer que leurs informations sont protégées en utilisant SSL.

Vous pouvez également ajouter des points de terminaison pouvant être utilisées en interne ou à l’extérieur. Points de terminaison externes sont appelés extrémités d’entrée. Un point de terminaison d’entrée permet à un autre point d’accès aux utilisateurs de votre service cloud. Si vous disposez d’un service WCF, vous souhaiterez peut-être exposer un point de terminaison interne d’un rôle web à utiliser pour accéder à ce service.

>[AZURE.IMPORTANT] Vous pouvez uniquement mettre à jour les points de terminaison pour toutes les configurations de service.

Si vous ajoutez des points de terminaison HTTPS, vous devez utiliser un certificat SSL. Pour ce faire, vous pouvez associer des certificats à votre rôle pour toutes les configurations de service et les utiliser pour vos points de terminaison.

>[AZURE.IMPORTANT] Ces certificats ne sont pas compressées avec votre service. Vous devez télécharger vos certificats séparément sur Azure via le [portail classique Azure](http://go.microsoft.com/fwlink/?LinkID=213885).

Les certificats de gestion associer votre configurations de service s’appliquent uniquement lorsque votre service cloud s’exécute dans Azure. Lorsque votre service cloud s’exécute dans l’environnement de développement local, un certificat standard est géré par l’émulateur cluster Azure est utilisé.

### <a name="to-add-a-certificate-to-a-role"></a>Pour ajouter un certificat à un rôle

1. Sélectionnez l’onglet **certificats** .

1. Dans la liste de **Configuration du Service** , sélectionnez **Toutes les Configurations**.

    >[AZURE.NOTE] Pour ajouter ou supprimer des certificats, vous devez sélectionner toutes les Configurations. Vous pouvez mettre à jour le nom et l’empreinte numérique pour une configuration de service spécifiques si nécessaire.

1. Pour ajouter un certificat pour ce rôle, cliquez sur le bouton **Ajouter un certificat** . Une nouvelle entrée est ajoutée à la liste.

1. Dans la zone de texte **nom** , entrez le nom pour le certificat.

1. Dans la liste **Emplacement de stockage** , choisissez l’emplacement pour le certificat que vous voulez ajouter.

1. Dans la liste **Nom de stocker** , choisissez le magasin que vous souhaitez utiliser pour sélectionner le certificat.

1. Pour ajouter le certificat, cliquez sur le bouton points de suspension (...). La boîte de dialogue de **Sécurité Windows** s’affiche.

1. Sélectionnez le certificat que vous souhaitez utiliser dans la liste, puis choisissez le bouton **OK** .

    >[AZURE.NOTE] Lorsque vous ajoutez un certificat du magasin de certificats, les certificats intermédiaires sont ajoutés automatiquement aux paramètres de configuration pour vous. Ces certificats intermédiaires doivent également être téléchargés vers Azure afin de configurer correctement votre service pour SSL.

1. Pour supprimer un certificat, sélectionnez le certificat, puis cliquez sur le bouton **Supprimer le certificat** .

1. Cliquez sur l’icône **Enregistrer** dans la barre d’outils pour enregistrer ces modifications aux fichiers de configuration du service.

### <a name="to-manage-endpoints-for-a-role"></a>Pour gérer les points de terminaison pour un rôle

1. Sélectionnez l’onglet **points de terminaison** .

1. Dans la liste de **Configuration du Service** , sélectionnez **Toutes les Configurations**.

1. Pour ajouter un point de terminaison, cliquez sur le bouton **Ajouter un point de terminaison** . Une nouvelle entrée est ajoutée à la liste.

1. Dans la zone de texte **nom** , tapez le nom que vous souhaitez utiliser pour ce point de terminaison.

1. Dans la liste **Type** , choisissez le type de point de terminaison dont vous avez besoin.

1. Dans la liste **protocole** , choisissez le protocole du point de terminaison dont vous avez besoin.

1. S’il s’agit d’un point de terminaison d’entrée, dans la zone de texte **Port Public** , entrez le port public à utiliser.

1. Dans la zone de texte **Port privé** , tapez le port privé à utiliser.

1. Si le point de terminaison nécessite le protocole https, sélectionnez un certificat à utiliser dans la liste **Nom du certificat SSL** .

    >[AZURE.NOTE] Cette liste répertorie les certificats que vous avez ajoutés à ce rôle dans l’onglet **certificats** .

1. Cliquez sur le bouton **Enregistrer** dans la barre d’outils pour enregistrer ces modifications aux fichiers de configuration du service.

## <a name="next-steps"></a>Étapes suivantes
En savoir plus sur les projets Azure dans Visual Studio en lisant l’article [configuration d’un projet Azure](vs-azure-tools-configuring-an-azure-project.md). Pour plus d’informations à propos du schéma de service cloud [Référence du schéma](https://msdn.microsoft.com/library/azure/dd179398)de lecture.

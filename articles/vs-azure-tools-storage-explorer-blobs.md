<properties
    pageTitle="Gérer les ressources de stockage d’objets Blob Azure avec l’Explorateur de stockage (Preview) | Microsoft Azure"
    description="Gérer les objets Blob Azure conteneurs et des objets BLOB avec l’Explorateur de stockage (Preview)"
    services="storage"
    documentationCenter="na"
    authors="TomArcher"
    manager="douge"
    editor="" />

 <tags
    ms.service="storage"
    ms.devlang="multiple"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="na"
    ms.date="08/17/2016"
    ms.author="tarcher" />

# <a name="manage-azure-blob-storage-resources-with-storage-explorer-preview"></a>Gérer les ressources de stockage d’objets Blob Azure avec l’Explorateur de stockage (Preview)

## <a name="overview"></a>Vue d’ensemble

[Stockage d’objets Blob Azure](./storage/storage-dotnet-how-to-use-blobs.md) est un service de stockage de grandes quantités de données non structurées, tels que du texte ou des données binaires, qui sont accessibles à partir de n’importe où dans le monde via HTTP ou HTTPS.
Vous pouvez utiliser le stockage d’objets Blob pour exposer des données publiquement dans le monde, ou pour stocker les données d’application en privé. Dans cet article, vous allez apprendre à utiliser l’Explorateur d’espace de stockage (Preview) pour travailler avec des conteneurs blob et des objets BLOB.

## <a name="prerequisites"></a>Conditions préalables

Pour effectuer les étapes décrites dans cet article, vous devez les éléments suivants :

- [Télécharger et installer Explorateur de stockage (preview)](http://www.storageexplorer.com)
- [Se connecter à un compte de stockage Azure ou un service](./vs-azure-tools-storage-manage-with-storage-explorer.md#connect-to-a-storage-account-or-service)

## <a name="create-a-blob-container"></a>Créer un conteneur blob

Tous les objets BLOB doivent se trouver dans un conteneur blob, qui est simplement un regroupement logique des objets BLOB. Un compte peut contenir un nombre illimité de conteneurs et chaque conteneur peut stocker un nombre illimité d’objets BLOB.

Les étapes suivantes montrent comment créer un conteneur blob dans l’Explorateur de stockage (Preview).

1.  Ouvrez l’Explorateur d’espace de stockage (Preview).
1.  Dans le volet gauche, développez le compte de stockage dans lequel vous souhaitez créer le conteneur blob.
1.  Avec le bouton droit **Conteneurs Blob**et - dans le menu contextuel - Sélectionnez **Créer un conteneur Blob**.

    ![Créer un menu contextuel de conteneurs blob][0]

1.  Une zone de texte s’affichent sous le dossier **Conteneurs Blob** . Entrez le nom de votre conteneur blob. Voir la section [règles d’appellation de conteneur](./storage/storage-dotnet-how-to-use-blobs.md#create-a-container) pour obtenir la liste des règles et des restrictions sur les objets blob conteneurs de dénomination.

    ![Créer la zone de texte conteneurs Blob][1]

1.  Appuyez sur **entrée** lorsque vous avez terminé pour créer le conteneur blob ou **ÉCHAP** pour annuler. Une fois que le conteneur blob a été créé, il s’affiche sous le dossier **Conteneurs Blob** pour le compte de stockage sélectionné.

    ![Conteneur BLOB créé][2]

## <a name="view-a-blob-containers-contents"></a>Afficher le contenu d’un conteneur blob

Conteneurs BLOB contiennent des objets BLOB et dossiers (qui peuvent contenir des objets BLOB).

Les étapes suivantes expliquent comment afficher le contenu d’un conteneur blob dans l’Explorateur de stockage (Preview) :

1.  Ouvrez l’Explorateur d’espace de stockage (Preview).
1.  Dans le volet gauche, développez le compte de stockage contenant le conteneur blob que vous souhaitez afficher.
1.  Développez du compte stockage **Blob conteneurs**.
1.  Avec le bouton droit le conteneur blob que vous souhaitez afficher, puis - dans le menu contextuel - Sélectionnez **Ouvrir l’éditeur de conteneur Blob**.
Vous pouvez également double-cliquer sur le conteneur blob que vous souhaitez afficher.

    ![Menu contextuel de blob ouvrir conteneur éditeur][19]

1.  Le volet principal affiche le contenu du conteneur blob.

    ![Éditeur de conteneur BLOB][3]

## <a name="delete-a-blob-container"></a>Supprimer un conteneur blob

Conteneurs BLOB peuvent être facilement créés et supprimés selon vos besoins. (Pour savoir comment supprimer des objets BLOB individuels, reportez-vous à la section [Gestion des objets BLOB dans un conteneur blob](./#managing-blobs-in-a-blob-container).)

Les étapes suivantes expliquent comment supprimer un conteneur blob dans l’Explorateur de stockage (Preview) :

1.  Ouvrez l’Explorateur d’espace de stockage (Preview).
1.  Dans le volet gauche, développez le compte de stockage contenant le conteneur blob que vous souhaitez afficher.
1.  Développez du compte stockage **Blob conteneurs**.
1.  Avec le bouton droit le conteneur blob que vous voulez supprimer, puis - dans le menu contextuel - Sélectionnez **Supprimer**.
Vous pouvez également appuyer sur **Supprimer** pour supprimer le conteneur blob actuellement sélectionné.

    ![Supprimer le menu contextuel de conteneur blob][4]

1.  Sélectionnez **Oui** dans la boîte de dialogue de confirmation.

    ![Supprimer des objets blob confirmation conteneur][5]

## <a name="copy-a-blob-container"></a>Copier un conteneur blob

Explorateur de stockage (Preview) vous permet de copier un conteneur blob dans le Presse-papiers, et collez ce conteneur blob dans un autre compte de stockage. (Pour voir comment copier des objets BLOB individuels, reportez-vous à la section [Gestion des objets BLOB dans un conteneur blob](./#managing-blobs-in-a-blob-container).)

Les étapes suivantes illustrent comment copier un conteneur blob un stockage compte à l’autre.

1.  Ouvrez l’Explorateur d’espace de stockage (Preview).
1.  Dans le volet gauche, développez le compte de stockage contenant le conteneur blob que vous souhaitez copier.
1.  Développez du compte stockage **Blob conteneurs**.
1.  Avec le bouton droit le conteneur blob que vous souhaitez copier, puis - dans le menu contextuel - Sélectionnez **Copier Blob conteneur**.

    ![Menu contextuel de copie blob conteneur][6]

1.  Double-cliquez sur le compte de stockage souhaité « cible » dans lequel vous voulez coller le conteneur blob et - dans le menu contextuel - Sélectionnez **Coller Blob conteneur**.

    ![Menu contextuel de collage blob conteneur][7]

## <a name="get-the-sas-for-a-blob-container"></a>Obtenir les associations de sécurité pour un conteneur blob

Une [signature d’un accès partagé (sa)](./storage/storage-dotnet-shared-access-signature-part-1.md) offre un accès délégué aux ressources dans votre compte de stockage.
Cela signifie que vous pouvez accorder qu'un client des autorisations limitées aux objets de votre compte de stockage pour une période spécifiée de temps et avec un ensemble spécifique d’autorisations, sans avoir à partager les touches d’accès de votre compte.

Les étapes suivantes montrent comment créer une associations de sécurité pour un conteneur blob :

1.  Ouvrez l’Explorateur d’espace de stockage (Preview).
1.  Dans le volet gauche, développez le compte de stockage contenant le conteneur blob pour lequel vous souhaitez obtenir une associations de sécurité.
1.  Développez du compte stockage **Blob conteneurs**.
1.  Avec le bouton droit le conteneur blob souhaité, puis - dans le menu contextuel - Sélectionnez **Obtenir la Signature des accès partagés**.

    ![Obtenir le menu contextuel associations de sécurité][8]

1.  Dans la boîte de dialogue **Partagé Access Signature** , spécifier la stratégie, dates de début et d’expiration, le fuseau horaire et y accéder de niveaux que hiérarchiques pour la ressource.

    ![Accéder aux options associations de sécurité][9]

1.  Lorsque vous avez terminé de spécifier les options associations de sécurité, sélectionnez **créer**.

1.  Une deuxième boîte de dialogue **Partagé Access Signature** affiche alors qui répertorie le conteneur blob ainsi que l’URL et vous pouvez utiliser pour accéder à la ressource de stockage de requête.
Sélectionnez **Copier** en regard de l’URL que vous souhaitez copier dans le Presse-papiers.

    ![Copiez l’URL associations de sécurité][10]

1.  Lorsque vous avez terminé, sélectionnez **Fermer**.

## <a name="manage-access-policies-for-a-blob-container"></a>Gérer les stratégies d’accès pour un conteneur blob

Les étapes suivantes expliquent comment gérer (ajouter et supprimer) accéder aux stratégies pour un conteneur blob :

1.  Ouvrez l’Explorateur d’espace de stockage (Preview).
1.  Dans le volet gauche, développez le compte de stockage contenant le conteneur blob dont vous souhaitez gérer les stratégies d’accès.
1.  Développez du compte stockage **Blob conteneurs**.
1.  Sélectionnez le conteneur blob souhaité, puis - dans le menu contextuel - Sélectionnez **Gérer les stratégies d’accès**.

    ![Gérer le menu contextuel stratégies d’accès][11]

1.  La boîte de dialogue **Stratégies d’accès** permet de répertorier les stratégies d’accès déjà créées pour le conteneur blob sélectionné.

    ![Accéder aux options de stratégie][12]        

1.  Suivez ces étapes en fonction de la tâche de gestion de stratégie access :

    - **Ajouter une nouvelle stratégie d’accès** - sélectionnez **Ajouter**. Une fois généré, la boîte de dialogue **Stratégies d’accès** affiche la stratégie d’accès nouvellement ajouté (avec les paramètres par défaut).
    - **Modifier une stratégie d’accès** - apporter les modifications souhaitées, puis sélectionnez **Enregistrer**.
    - **Supprimer une stratégie d’accès** - sélectionnez **Supprimer** en regard de la stratégie d’accès que vous souhaitez supprimer.

## <a name="set-the-public-access-level-for-a-blob-container"></a>Définir le niveau d’accès Public pour un conteneur blob

Par défaut, chaque conteneur blob est défini sur « Aucun accès public ».

Les étapes suivantes illustrent comment spécifier un niveau d’accès public pour un conteneur blob.

1.  Ouvrez l’Explorateur d’espace de stockage (Preview).
1.  Dans le volet gauche, développez le compte de stockage contenant le conteneur blob dont vous souhaitez gérer les stratégies d’accès.
1.  Développez du compte stockage **Blob conteneurs**.
1.  Sélectionnez le conteneur blob souhaité, puis - dans le menu contextuel - Sélectionnez **Définir niveau d’accès Public**.

    ![Définir le menu contextuel niveau d’une accès public][13]

1.  Dans la boîte de dialogue **Définir le niveau du conteneur accès Public** , spécifiez le niveau d’accès souhaité.

    ![Définir les options de niveau d’accès du public][14]

1.  Sélectionnez **Appliquer**.

## <a name="managing-blobs-in-a-blob-container"></a>Gestion des objets BLOB dans un conteneur blob

Une fois que vous avez créé un conteneur blob, vous pouvez télécharger un blob dans ce conteneur blob, téléchargez un blob sur votre ordinateur local, ouvrez un blob sur votre ordinateur local et bien plus encore.

Les étapes suivantes montrent comment gérer les objets BLOB (et les dossiers) dans un conteneur blob.

1.  Ouvrez l’Explorateur d’espace de stockage (Preview).
1.  Dans le volet gauche, développez le compte de stockage contenant le conteneur blob que vous souhaitez gérer.
1.  Développez du compte stockage **Blob conteneurs**.
1.  Double-cliquez sur le conteneur blob que vous souhaitez afficher.
1.  Le volet principal affiche le contenu du conteneur blob.

    ![Conteneur de blob d’affichage][3]

1.  Le volet principal affiche le contenu du conteneur blob.

1.  Suivez ces étapes en fonction de la tâche que vous souhaitez effectuer :

    - **Télécharger des fichiers dans un conteneur blob**

        1.  Barre d’outils du volet principal, sélectionnez **Télécharger**, puis sur **Télécharger des fichiers** à partir du menu déroulant.

            ![Fichiers menu télécharger][15]

        1.  Dans la boîte de dialogue **télécharger des fichiers** , sélectionnez le bouton points de suspension (...****) sur le côté droit de la zone de texte pour sélectionner l’ou les fichiers que vous souhaitez télécharger des **fichiers** .

            ![Options de fichiers de téléchargement][16]

        1.  Spécifier le type de **type Blob**. L’article de la [prise en main stockage d’objets Blob Azure à l’aide de .NET](./storage/storage-dotnet-how-to-use-blobs.md#blob-service-concepts) explique les différences entre les différents types d’objets blob.

        1.  Vous pouvez également spécifier un dossier cible dans lequel les fichiers sélectionnés seront téléchargés. Si le dossier cible n’existe pas, il sera créé.

        1.  Sélectionnez **Télécharger**.

    - **Télécharger un dossier dans un conteneur blob**

        1.  Barre d’outils du volet principal, sélectionnez **Télécharger**, puis sur **Télécharger le dossier** dans le menu déroulant.

            ![Dossier menu télécharger][17]

        1.  Dans la boîte de dialogue **Télécharger le dossier** , sélectionnez le bouton points de suspension (...****) sur le côté droit de la zone de texte pour sélectionner le dossier dont vous souhaitez télécharger le contenu du **dossier** .

            ![Télécharger les options des dossiers][18]

        1.  Spécifier le type de **type Blob**. L’article de la [prise en main stockage d’objets Blob Azure à l’aide de .NET](./storage/storage-dotnet-how-to-use-blobs.md#blob-service-concepts) explique les différences entre les différents types d’objets blob.

        1.  Vous pouvez également spécifier un dossier cible dans lequel le contenu du dossier sélectionné sera téléchargé. Si le dossier cible n’existe pas, il sera créé.

        1.  Sélectionnez **Télécharger**.

    - **Téléchargez un blob sur votre ordinateur local**

        1.  Sélectionnez le blob que vous souhaitez télécharger.

        1.  Barre d’outils du volet principal, sélectionnez **Télécharger**.

        1.  Dans la boîte de dialogue **préciser où vous souhaitez enregistrer le blob téléchargé** , spécifiez l’emplacement où vous souhaitez le blob téléchargé, et le nom que vous souhaitez lui donner.  

        1.  Cliquez sur **Enregistrer**.

    - **Ouvrir un blob sur votre ordinateur local**

        1.  Sélectionnez le blob que vous souhaitez ouvrir.

        1.  Dans la barre d’outils du volet principal, sélectionnez **Ouvrir**.

        1.  Le blob est téléchargé et ouverts à l’aide de l’application associée au type de fichier sous-jacent de blob.

    - **Copier un blob dans le Presse-papiers**

        1.  Sélectionnez le blob que vous souhaitez copier.

        1.  Barre d’outils du volet principal, sélectionnez **Copier**.

        1.  Dans le volet gauche, accédez à un autre conteneur blob, puis double-cliquez dessus pour l’afficher dans le volet principal.

        1.  Barre d’outils du volet principal, sélectionnez **Coller** pour créer une copie de l’objet blob.

    - **Supprimer un blob**

        1.  Sélectionnez le blob que vous voulez supprimer.

        1.  Dans la barre d’outils du volet principal, cliquez sur **Supprimer**.

        1.  Sélectionnez **Oui** dans la boîte de dialogue de confirmation.

## <a name="next-steps"></a>Étapes suivantes

- Afficher la [dernière version d’espace de stockage Explorer (Preview) notes de publication et vidéos](http://www.storageexplorer.com).
- Découvrez comment [créer des applications à l’aide de fichiers, des tableaux, des files d’attente et des objets BLOB Azure](https://azure.microsoft.com/documentation/services/storage/).

[0]: ./media/vs-azure-tools-storage-explorer-blobs/blob-containers-create-context-menu.png
[1]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-create.png
[2]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-create-done.png
[3]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-editor.png
[4]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-delete-context-menu.png
[5]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-delete-confirmation.png
[6]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-copy-context-menu.png
[7]: ./media/vs-azure-tools-storage-explorer-blobs/blob-containers-paste-context-menu.png
[8]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-get-sas-context-menu.png
[9]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-get-sas-options.png
[10]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-get-sas-urls.png
[11]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-manage-access-policies-context-menu.png
[12]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-manage-access-policies-options.png
[13]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-set-public-access-level-context-menu.png
[14]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-set-public-access-level-options.png
[15]: ./media/vs-azure-tools-storage-explorer-blobs/blob-upload-files-menu.png
[16]: ./media/vs-azure-tools-storage-explorer-blobs/blob-upload-files-options.png
[17]: ./media/vs-azure-tools-storage-explorer-blobs/blob-upload-folder-menu.png
[18]: ./media/vs-azure-tools-storage-explorer-blobs/blob-upload-folder-options.png
[19]: ./media/vs-azure-tools-storage-explorer-blobs/blob-container-open-editor-context-menu.png
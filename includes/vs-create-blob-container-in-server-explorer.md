Vous pouvez créer des files d’attente de stockage Azure à l’aide de Visual Studio **Explorateur de serveurs**.

![Objets BLOB Server Explorer][Image1]

1. Dans le menu **affichage** , choisissez **Explorateur de serveurs**.
2. Dans l’Explorateur de serveurs, développez le nœud **Azure** pour votre abonnement, développez le nœud de **stockage** et le nœud pour le compte de stockage que vous avez spécifié dans le service de stockage Azure connecté.
3. Sélectionnez le nœud **files d’attente** , puis **Créez la file d’attente** dans le menu contextuel.
4. Entrez un nom pour le conteneur et cliquez sur **OK**.   

Par défaut, le nouveau conteneur est privé, et vous devez spécifier votre clé d’accès de stockage pour télécharger des objets BLOB à partir de ce conteneur. Si vous souhaitez publier les fichiers dans le conteneur, sélectionnez le conteneur dans **l’Explorateur de serveurs** , puis appuyez sur `F4` pour afficher la fenêtre de **Propriétés** . Définir l' **accès en lecture Public** au **Blob**. Toute personne sur Internet peut voir objets BLOB dans un conteneur public, mais vous pouvez modifier ou supprimer les uniquement si vous disposez de la clé d’accès appropriés.


[Image1]: ./media/vs-create-blob-container-in-server-explorer/vs-storage-create-blob-containers-in-Server-Explorer.png
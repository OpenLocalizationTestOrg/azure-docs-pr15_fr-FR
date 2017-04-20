<properties
    pageTitle="Prise en main avec l’Explorateur de stockage (Preview) | Microsoft Azure"
    description="Gérer les ressources de stockage Azure avec l’Explorateur de stockage (Preview)"
    services="storage"
    documentationCenter="na"
    authors="TomArcher"
    manager="douge"
    editor="" />

 <tags
    ms.service="storage"
    ms.devlang="multiple"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="na"
    ms.workload="na"
    ms.date="08/17/2016"
    ms.author="tarcher" />

# <a name="getting-started-with-storage-explorer-preview"></a>Prise en main avec l’Explorateur de stockage (Preview)

## <a name="overview"></a>Vue d’ensemble 

Explorateur de stockage Microsoft Azure (Preview) est une application autonome qui vous permet de travailler facilement avec les données de stockage Azure sous Windows, OS X et Linux. Dans cet article, vous allez découvrir les différentes méthodes de connexion à et gestion de vos comptes de stockage Azure.

![Explorateur de stockage de Microsoft Azure (Preview)][15]

## <a name="prerequisites"></a>Conditions préalables

- [Télécharger et installer Explorateur de stockage (preview)](http://www.storageexplorer.com)

## <a name="connect-to-a-storage-account-or-service"></a>Se connecter à un compte de stockage ou un service

Explorateur de stockage (Preview) fournit un large éventail moyens de se connecter à des comptes de stockage. Connexion aux comptes de stockage associés à vos abonnements Azure, connexion à des comptes de stockage et de services partagés d’autres abonnements Azure, même connexion et la gestion du stockage local à l’aide de l’émulateur de stockage Azure comprend :

- [Se connecter à un abonnement Azure](#connect-to-an-azure-subscription) - gérer les ressources de stockage appartenant à votre abonnement Azure.
- [Travail avec le stockage de développement local](#work-with-local-development-storage) - gérer le stockage local à l’aide de l’émulateur de stockage Azure. 
- [Attacher au stockage externe](#attach-or-detach-an-external-storage-account) - gérer les ressources de stockage appartenant à un autre abonnement Azure à l’aide de nom du compte et la clé du compte de stockage.
- [Compte de stockage joindre à l’aide de sa](#attach-storage-account-using-sas) - gérer les ressources de stockage appartenant à un autre abonnement Azure à l’aide d’une associations de sécurité.
- [Service de joindre à l’aide de sa](#attach-service-using-sas) - gestion d’un service de stockage spécifique (conteneur blob, file d’attente ou table) appartenant à un autre abonnement Azure à l’aide d’une associations de sécurité.

## <a name="connect-to-an-azure-subscription"></a>Se connecter à un abonnement Azure

> [AZURE.NOTE] Si vous n’avez pas un compte Azure, vous pouvez [vous inscrire à une version d’évaluation gratuite](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F) ou [activer vos avantages d’abonné Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F).

1. Dans l’Explorateur d’espace de stockage (Preview), sélectionnez **paramètres du compte Azure**. 

    ![Paramètres du compte Azure][0]

1. Le volet de gauche affiche désormais tous les comptes Microsoft que vous vous êtes connecté. Pour vous connecter à un autre compte, sélectionnez **Ajouter un compte**, puis suivez les boîtes de dialogue pour vous connecter avec un compte Microsoft associé au moins un abonnement Azure actif.

    ![Ajouter un compte][1]

1. Une fois connecté avec un compte Microsoft avec succès, le volet gauche s’affichent les abonnements Azure associés à ce compte. Sélectionnez les abonnements Azure avec laquelle vous souhaitez travailler, puis **Appliquer**. (Sélection active ou désactive **tous les abonnements pour** sélectionner tous ou aucun des abonnements Azure répertoriés).

    ![Sélectionnez abonnements Azure][3]

1. Le volet de gauche affiche désormais les comptes de stockage associés les abonnements Azure sélectionnés.

    ![Abonnements Azure sélectionnés][4]

## <a name="work-with-local-development-storage"></a>Travailler avec le stockage de développement local

Explorateur de stockage (Preview) vous permet d’utiliser contre stockage local à l’aide de l’émulateur de stockage Azure. Cela permet d’écrire du code pour et tester le stockage sans nécessairement disposer d’un compte de stockage déployé sur Azure (étant donné que le compte de stockage est en cours émuler par l’émulateur de stockage Azure).

>[AZURE.NOTE] L’émulateur de stockage Azure est pris en charge uniquement pour Windows. 

1. Dans le volet gauche de l’Explorateur d’espace de stockage (Preview), développez la **(locale et associés** > **Comptes de stockage** > nœud**(Development)** .

    ![Nœud de développement local][21]

1. Si vous n’avez pas encore installé l’émulateur de stockage Azure, vous êtes invité à le faire via une barre d’informations. Si la barre d’informations s’affiche, sélectionnez **Télécharger la version la plus récente**et installer l’émulateur. 

    ![Télécharger invite émulateur de stockage Azure][22]

1. Une fois l’émulateur est installé, vous aurez la possibilité de créer et utiliser des tables, des files d’attente et des objets BLOB locales. Pour apprendre à utiliser avec chaque type de compte de stockage, sélectionnez sur le lien approprié ci-dessous :

    - [Gérer les ressources de stockage blob Azure](./vs-azure-tools-storage-explorer-blobs.md)
    - Gérer les ressources de stockage de fichier Azure partager - *bientôt disponible*
    - Gérer les ressources de stockage Azure file d’attente - *bientôt disponible*
    - Gérer les ressources de stockage de table Azure - *bientôt disponible*

## <a name="attach-or-detach-an-external-storage-account"></a>Attacher ou détacher un compte de stockage externe

Explorateur de stockage (Preview) permet de joindre aux comptes de stockage externe afin que les comptes de stockage pouvant être facilement partagés. Cette section explique comment joindre à (et détacher) comptes de stockage externes.

### <a name="get-the-storage-account-credentials"></a>Obtenir les informations d’identification du compte de stockage

Pour partager un compte de stockage externe, le propriétaire de ce compte doit tout d’abord obtenir les informations d’identification - nom de compte et clé - pour le compte et de les partager avec la personne que vous souhaitez associer à ce compte (externe). Obtenir les informations d’identification du compte de stockage peut être exécuté via le portail Azure en procédant comme suit : 

1.  Connectez-vous au [portail Azure](https://portal.azure.com).
1.  Sélectionnez **Parcourir**.
1.  Sélectionnez **comptes de stockage**.
1.  Dans la carte de **Stockage comptes** , sélectionnez le compte de stockage de votre choix.
1.  Dans la carte de **paramètres** du compte de stockage sélectionné, sélectionnez **touches d’accès rapide**.

    ![Option de touches d’accès][5]
    
1.  Dans la carte de **touches d’accès rapide** , copiez les valeurs de **Nom de compte de stockage** et **clé 1** à utiliser lors de la connexion au compte de stockage. 

    ![Touches d’accès rapide][6]

### <a name="attach-to-an-external-storage-account"></a>Joindre à un compte de stockage externe
Pour joindre à un compte de stockage externe, vous devez nom et la clé du compte. La section *obtenir les informations d’identification du compte de stockage* explique comment faire pour obtenir ces valeurs à partir du portail Azure. Toutefois, notez que le portail, la clé de compte est appelée « clé 1 » jusqu'à l’emplacement dans lequel l’Explorateur d’espace de stockage (Preview) vous invite à fournir une clé de compte, vous allez entrer (ou collez) la valeur « clé 1 ». 
 
1.  Dans l’Explorateur d’espace de stockage (Preview), sélectionnez **se connecter au stockage Azure**.

    ![Se connecter à l’option de stockage Azure][23]

1.  Dans la boîte de dialogue **se connecter au stockage Azure** , spécifiez la clé de compte (valeur « clé 1 » à partir du portail Azure) et sélectionnez **suivant**.

    ![Se connecter à la boîte de dialogue stockage Azure][24] 

1.  Dans la boîte de dialogue **Joindre un stockage externe** , entrez le nom de compte de stockage dans la zone **nom du compte** , spécifiez les autres paramètres de votre choix et cliquez sur **suivant** lorsque vous avez terminé. 

    ![Joindre la boîte de dialogue stockage externe][8]

1.  Dans la boîte de dialogue **Résumé de connexion** , vérifiez les informations. Si vous souhaitez modifier, sélectionnez **Nouveau** et entrez à nouveau les paramètres souhaités. Une fois que vous avez terminé, sélectionnez **se connecter**.

1.  Une fois connecté, le compte de stockage externe s’affichera avec le texte **(externe)** ajouté au nom du compte de stockage. 

    ![Résultat de la connexion à un compte de stockage externe][9]

### <a name="detach-from-an-external-storage-account"></a>Détacher d’un compte de stockage externe

1.  Double-cliquez sur le compte de stockage externe que vous souhaitez détacher et - dans le menu contextuel - Sélectionnez **Détacher**.

    ![Détacher de l’option de stockage][10]

1.  Lorsque la zone de message de confirmation s’affiche, sélectionnez **Oui** pour confirmer la détacher à partir du compte de stockage externe.

## <a name="attach-storage-account-using-sas"></a>Joindre le compte de stockage à l’aide des associations de sécurité

Un [sa (partagés accès Signature)](storage/storage-dotnet-shared-access-signature-part-1.md) donne l’administrateur d’un abonnement Azure la fonctionnalité permettant d’accorder l’accès à un compte de stockage sur une base temporaire sans avoir à fournir ses informations d’identification de l’abonnement Azure. 

Pour illustrer cela, supposons utilisateur a est un administrateur d’un abonnement Azure et l’utilisateur a souhaite autoriser utilisateur b pour accéder à un compte de stockage pour une période limitée avec certaines autorisations :

1. Utilisateur a génère un sa (composé de la chaîne de connexion pour le compte de stockage) pour une période spécifique et avec les autorisations souhaitées.
1. L’utilisateur a partage les associations de sécurité avec la personne qui souhaitent accès au compte de stockage - utilisateur b, dans notre exemple.  
1. Utilisateur b utilise l’Explorateur de stockage (Preview) pour joindre au compte appartenant à l’utilisateur a à l’aide des associations de sécurité fournies. 

### <a name="get-a-sas-for-the-account-you-want-to-share"></a>Obtenir une sa pour le compte que vous voulez partager

1.  Dans l’Explorateur d’espace de stockage (Preview), double-cliquez sur le compte de stockage à partager, puis - dans le menu contextuel - Sélectionnez **Obtenir la Signature des accès partagés**.

    ![Obtenir l’option de menu contextuel associations de sécurité][13]

1. Dans la boîte de dialogue **Partagé Access Signature** , spécifiez l’intervalle de temps et les autorisations que vous souhaitez pour le compte, puis sélectionnez **créer**.

    ![Boîte de dialogue associations de sécurité][14]
 
1. Une deuxième boîte de dialogue **Partagé Access Signature** apparaît affichant les associations de sécurité. Sélectionnez **Copier** en regard de la **Chaîne de connexion** pour le copier dans le Presse-papiers. Sélectionnez **Fermer** pour fermer la boîte de dialogue.

### <a name="attach-to-the-shared-account-using-the-sas"></a>Joindre au compte partagé les associations de sécurité

1.  Dans l’Explorateur d’espace de stockage (Preview), sélectionnez **se connecter au stockage Azure**.

    ![Se connecter à l’option de stockage Azure][23]

1.  Dans la boîte de dialogue **se connecter au stockage Azure** , spécifiez la chaîne de connexion et puis sélectionnez **suivant**.

    ![Se connecter à la boîte de dialogue stockage Azure][24] 

1.  Dans la boîte de dialogue **Résumé de connexion** , vérifiez les informations. Si vous souhaitez modifier, sélectionnez **Nouveau** et entrez à nouveau les paramètres souhaités. Une fois que vous avez terminé, sélectionnez **se connecter**.

1.  Une fois connecté, le compte de stockage s’affichera avec le texte (sa) ajouté au nom du compte que vous avez fourni.

    ![Le résultat de pièce jointe à un compte à l’aide des associations de sécurité][17]

## <a name="attach-service-using-sas"></a>Joindre service à l’aide des associations de sécurité

La section [compte de stockage de joindre à l’aide de sa](#attach-storage-account-using-sas) illustre comment un administrateur d’abonnement Azure peut accorder l’accès temporaire à un compte de stockage en générant (partage et) une associations de sécurité du compte de stockage. De même, un sa peut être généré pour un service spécifique au sein d’un compte de stockage (conteneur blob, file d’attente ou table).  

### <a name="generate-a-sas-for-the-service-you-want-to-share"></a>Générer une associations de sécurité pour le service que vous voulez partager

Dans ce contexte, un service peut être un conteneur blob, file d’attente ou table. Les sections suivantes expliquent comment générer les associations de sécurité pour le service répertorié :

- [Obtenir les associations de sécurité pour un conteneur blob](./vs-azure-tools-storage-explorer-blobs.md#get-the-sas-for-a-blob-container)
- Obtenir les associations de sécurité pour un partage de fichiers - *bientôt disponible*
- Obtenir les associations de sécurité pour une file d’attente - *bientôt disponible*
- Obtenir les associations de sécurité pour un tableau - *bientôt disponible*

### <a name="attach-to-the-shared-account-service-using-the-sas"></a>Joindre pour le service de compte partagé en utilisant les associations de sécurité

1.  Dans l’Explorateur d’espace de stockage (Preview), sélectionnez **se connecter au stockage Azure**.

    ![Se connecter à l’option de stockage Azure][23]

1.  Dans la boîte de dialogue **se connecter au stockage Azure** , spécifiez l’URI SAS et sélectionnez **suivant**.

    ![Se connecter à la boîte de dialogue stockage Azure][24] 

1.  Dans la boîte de dialogue **Résumé de connexion** , vérifiez les informations. Si vous souhaitez modifier, sélectionnez **Nouveau** et entrez à nouveau les paramètres souhaités. Une fois que vous avez terminé, sélectionnez **se connecter**.

1.  Une fois connecté, le service nouvellement attaché s’afficheront sous le nœud **(Service sa)** .

    ![Résultat d’attacher à un service partagé à l’aide des associations de sécurité][20]

## <a name="search-for-storage-accounts"></a>Rechercher des comptes de stockage

Si vous avez une longue liste de comptes de stockage, un moyen rapide de localiser un compte de stockage particulier consiste à utiliser la zone de recherche dans la partie supérieure du volet gauche. 

Lorsque vous tapez dans la zone de recherche, le volet de gauche affiche uniquement les comptes de stockage qui correspondent à la valeur de recherche que vous avez entré jusqu'à qui pointent. La capture d’écran suivante montre un exemple où j’ai recherché tous les comptes de stockage dont le nom de compte de stockage contient le texte « tarcher ».

![Recherche de compte de stockage][11]
    
Pour effacer la recherche, sélectionnez le bouton **x** dans la zone de recherche.

## <a name="next-steps"></a>Étapes suivantes
- [Gérer les ressources de stockage blob Azure avec l’Explorateur de stockage (Preview)](./vs-azure-tools-storage-explorer-blobs.md)

[0]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/settings-icon.png
[1]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/add-account-link.png
[3]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/subscriptions-list.png
[4]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/storage-accounts-list.png
[5]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/access-keys.png
[6]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/access-keys-copy.png
[8]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/attach-external-storage-dlg.png
[9]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/external-storage-account.png
[10]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/detach-external-storage.png
[11]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/storage-account-search.png
[12]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/detach-external-storage-confirmation.png
[13]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/get-sas-context-menu.png
[14]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/get-sas-dlg1.png
[15]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/mase.png
[17]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/attach-account-using-sas-finished.png
[20]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/attach-service-using-sas-finished.png
[21]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/local-storage-drop-down.png
[22]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/download-storage-emulator.png
[23]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/connect-to-azure-storage-icon.png
[24]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/connect-to-azure-storage-next.png

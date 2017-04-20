<properties
    pageTitle=" Créer un compte Azure Media Services via le portail Azure | Microsoft Azure"
    description="Ce didacticiel vous guide tout au long des étapes de création d’un compte Azure Media Services avec le portail Azure."
    services="media-services"
    documentationCenter=""
    authors="Juliako"
    manager="erikre"
    editor=""/>

<tags
    ms.service="media-services"
    ms.workload="media"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="10/24/2016"
    ms.author="juliako"/>


# <a name="create-an-azure-media-services-account-using-the-azure-portal"></a>Créer un compte Azure Media Services à l’aide du portail Azure

> [AZURE.SELECTOR]
- [Portail](media-services-portal-create-account.md)
- [PowerShell](media-services-manage-with-powershell.md)
- [RESTE](http://msdn.microsoft.com/library/azure/dn194267.aspx)

> [AZURE.NOTE] Pour effectuer ce didacticiel, vous avez besoin d’un compte Azure. Pour plus d’informations, voir [Azure la version d’évaluation gratuite](https://azure.microsoft.com/pricing/free-trial/). 

Le portail Azure fournit un moyen de créer rapidement un compte Azure Media Services (AMS). Vous pouvez utiliser votre compte pour accéder aux Services de support qui vous permettent de stocker, chiffrer, coder, gérer et diffuser du contenu multimédia dans Azure. À la fois que vous créez un compte Media Services, vous également créer un compte de stockage associé (ou utilisez une existante) dans la même région géographique comme compte Media Services.

Cet article décrit quelques concepts de base courantes et montre comment créer un compte Media Services avec le portail Azure.

## <a name="concepts"></a>Concepts

Accès aux Services de support nécessite deux comptes associés :

- Un compte Media Services. Votre compte vous donne accès à un ensemble de sur le nuage Media Services qui sont disponibles dans Azure. Un compte Media Services ne stocke pas du contenu multimédia réel. À la place qu’il stocke les métadonnées sur du contenu multimédia et les tâches de traitement des éléments multimédias dans votre compte. Au moment de la que création du compte, vous sélectionnez une zone de Services de support disponible. La région que vous sélectionnez est un centre de données qui stocke les enregistrements de métadonnées pour votre compte.

    Régions Media Services (AMS) disponibles sont les suivants : Europe du Nord, Europe ouest, US ouest, US Extrême-Orient, Asie du Sud-est, Asie de l’est, Japon ouest, Moyen-Orient Japon. Media Services n’utilise pas affinités.
    
    AMS est désormais également disponible dans les centres de données suivant : Brésil Sud, Inde ouest, Sud Inde et Inde Central. Vous pouvez désormais utiliser le portail Azure pour créer des comptes de Service de support et effectuer diverses tâches décrites ici. Toutefois, encodage Live n’est pas activée dans ces centres de données. En outre, pas tous les types de codage réservés unités sont disponibles dans ces centres de données.
    
    - Brésil sud : Seuls Standard et unités réservés codage de base sont disponibles.
    - Inde ouest, Sud Inde : 

- Un compte de stockage Azure. Comptes de stockage doivent se trouver dans la même région géographique comme compte Media Services. Lorsque vous créez un compte Media Services, vous pouvez choisir un compte de stockage existant dans la même région, ou vous pouvez créer un nouveau compte de stockage dans la même région. Si vous supprimez un compte Media Services, les objets BLOB dans votre compte de stockage connexes ne sont pas supprimés.

## <a name="create-an-ams-account"></a>Créer un compte AMS

Les étapes décrites dans cette section montrent comment créer un compte AMS.

1. Ouvrez une session sur le [portail Azure](https://portal.azure.com/).
2. Cliquez sur **+ nouvelle** > **Web + Mobile** > **Media Services**.

    ![Créent des Services de support](./media/media-services-portal-vod-get-started/media-services-new1.png)

3. Dans **Créer un compte de SERVICES de support** Entrez les valeurs requises.

    ![Créent des Services de support](./media/media-services-portal-vod-get-started/media-services-new3.png)
    
    1. Dans la zone **Nom du compte**, entrez le nom du nouveau compte AMS. Un nom de compte Media Services est tous les nombres en minuscules ou des lettres sans espace et 3 à 24 caractères.
    2. Abonnement, sélectionnez parmi les différents abonnements Azure que vous avez accès.
    
    2. Dans le **Groupe de ressources**, sélectionnez la ressource nouvelle ou existante.  Un groupe de ressources est un ensemble de ressources qui partagent le cycle de vie, les autorisations et les stratégies. En savoir plus [ici](azure-resource-manager/resource-group-overview.md#resource-groups).
    3. Dans un **emplacement**, sélectionnez la région géographique qui sera utilisée pour stocker les enregistrements de métadonnées et éléments multimédias de votre compte Media Services. Cette zone est utilisée pour traiter et diffuser vos médias. Uniquement les zones des Services de support disponibles apparaissent dans la zone de liste déroulante. 
    
    3. Dans le **Compte de stockage**, sélectionnez un compte de stockage pour fournir le stockage blob du contenu multimédia à partir de votre compte Media Services. Vous pouvez sélectionner un compte de stockage existant dans la même région géographique comme votre compte Media Services, ou vous pouvez créer un compte de stockage. Un nouveau compte de stockage est créé dans la même région. Les règles de noms de compte de stockage sont les mêmes que pour les comptes Media Services.

        En savoir plus sur le stockage [ici](storage-introduction.md).

    4. Sélectionnez **Ajouter au tableau de bord** pour afficher la progression du déploiement compte.
    
7. Cliquez sur **créer** en bas de l’écran.

    Une fois que le compte est créé avec succès, le statut devient **en cours d’exécution**. 

    ![Paramètres des Services de support](./media/media-services-portal-vod-get-started/media-services-settings.png)

    Pour gérer votre compte AMS (par exemple, télécharger des vidéos, Encoder actifs, surveiller l’avancement de tâche) utilisez la fenêtre **paramètres** .

## <a name="manage-keys"></a>Gérer les clés

Vous devez le nom du compte et les informations de clé primaires par programme pour accéder à la Media Services compte.

1. Dans le portail Azure, sélectionnez votre compte. 

    La fenêtre **paramètres** apparaît à droite. 

2. Dans la fenêtre **paramètres** , sélectionnez **clés**. 

    Les fenêtres de **gérer des clés** affiche le nom du compte et les clés principales et secondaires s’affiche. 
3. Appuyez sur le bouton Copier pour copier les valeurs.
    
    ![Media Services clés](./media/media-services-portal-vod-get-started/media-services-keys.png)

## <a name="next-steps"></a>Étapes suivantes

Vous pouvez maintenant télécharger des fichiers vers votre compte AMS. Pour plus d’informations, voir [télécharger des fichiers](media-services-portal-upload-files.md).

## <a name="media-services-learning-paths"></a>Rubriques d’apprentissage sur Media Services

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Fournir des commentaires

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]



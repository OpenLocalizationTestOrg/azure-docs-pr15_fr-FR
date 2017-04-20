<properties 
   pageTitle="Ajout de Services mobiles à l’aide de Services connectés dans Visual Studio | Microsoft Azure"
   description="Ajouter des Services Mobile à l’aide de la boîte de dialogue Visual Studio ajouter Services connectés"
   services="visual-studio-online"
   documentationCenter="na"
   authors="mlhoop"
   manager="douge"
   editor="" />
<tags 
   ms.service="visual-studio-online"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="mobile"
   ms.date="12/16/2015"
   ms.author="mlearned" />

# <a name="adding-mobile-services-by-using-visual-studio-connected-services"></a>Ajout de Services mobiles à l’aide de Visual Studio connecté Services

Avec Visual Studio 2015, vous pouvez vous connecter aux Services Mobile Azure à l’aide de la boîte de dialogue **Ajouter un Service connecté** . Vous pouvez vous connecter à partir de n’importe quelle application client c#, toute application JavaScript ou disponibilité sur plusieurs plateformes Cordova application. Une fois que vous vous connectez, vous pouvez créer et accéder aux données, créer des API personnalisé et les tâches planifiées ou prendre en charge les notifications de transmission.  L’opération de services connectés ajoute toutes les références appropriées et le code de connexion. Vous pouvez également tirer parti de la prise en charge intégrée pour l’authentification constituée de plusieurs jeux d’identité populaires, tels que Azure AD, Facebook, Twitter et Accounts Microsoft.

## <a name="supported-project-types"></a>Types de projets pris en charge

>[AZURE.NOTE] Dans Visual Studio 2015, l’ajout de Services mobiles Azure à un projets Windows universel (Windows 10) à l’aide de la boîte de dialogue Ajouter des Services connectés n’est pas pris en charge. Vous pouvez ajouter des Services mobiles Azure en installant des packages appropriés à l’aide du Gestionnaire de Package NuGet pour votre projet.

Vous pouvez utiliser la boîte de dialogue Services connectés pour vous connecter aux Services Mobile Azure dans les types suivants de projet.

- Projets .NET Windows 8.1 Store, téléphone et application universel

- Projets JavaScript Windows 8.1 Store, téléphone et application universel

- Projets créés à l’aide de Visual Studio Tools pour Apache Cordova


## <a name="connect-to-azure-mobile-services-using-the-add-connected-services-dialog"></a>Se connecter aux Services de Mobile Azure à l’aide de la boîte de dialogue Ajouter des Services connectés

1. Vérifiez que vous avez un compte Azure. Si vous n’avez pas un compte Azure, vous pouvez vous inscrire à une [version d’évaluation gratuite](http://go.microsoft.com/fwlink/?LinkId=518146).

1. Ouvrez la boîte de dialogue **Ajouter des Services connectés** .
 - Pour les applications .NET, ouvrez votre projet dans Visual Studio, ouvrez le menu contextuel pour le nœud **références** dans l’Explorateur d’et puis cliquez sur **Ajouter un Service connecté**
 
        ![Connecting to Azure Mobile Service](./media/vs-azure-tools-connected-services-add-mobile-services/IC797635.png)

 - Pour les projets d’application Cordova Apache, ouvrez votre projet dans Visual Studio, ouvrir le menu contextuel pour le nœud de projet dans l’Explorateur et puis cliquez sur **Ajouter un Service connecté**.

1. Dans la boîte de dialogue **Ajouter un Service connecté** , sélectionnez **Azure Mobile Services**, puis le bouton **configurer** . Vous pouvez être invité à vous connecter à Azure si vous n’avez pas déjà fait.

    ![Ajout d’un Service Mobile Azure](./media/vs-azure-tools-connected-services-add-mobile-services/IC797636.png)

1. Dans la boîte de dialogue **Services mobiles Azure** , choisissez un service mobile existant si vous en avez. Si vous avez besoin créer un nouveau service mobile Azure, suivez la procédure ci-dessous pour le faire. Dans le cas contraire, passez à l’étape suivante.

    Pour créer un nouveau compte de service mobile :
    1. Cliquez sur le lien de **Création de Service **dans la partie inférieure de la boîte de dialogue.
        ![Ajouter un nouveau service connecté mobile](./media/vs-azure-tools-connected-services-add-mobile-services/IC797637.png)




    2. Dans la boîte de dialogue **Créer le Service Mobile** , vous pouvez choisir un service mobile en aval JavaScript ou un service mobile en aval .NET dans la liste déroulante **pour l’exécution** . 
  
        ![Création d’un service mobile](./media/vs-azure-tools-connected-services-add-mobile-services/IC797638.png)

        Un service en aval JavaScript est simple et puissante. Si vous créez un service mobile en aval JavaScript, le code JavaScript côté serveur est stocké dans le cloud, mais vous pouvez modifier des scripts de serveur à l’aide de l’Explorateur de serveur ou le portail de gestion Azure. 

        Un service mobile en aval .NET vous donne la puissance et la flexibilité de l’API Web et entité Framework. Si vous créez un service mobile en aval .NET, un projet est créé et ajouté à votre solution. 

    1. Choisir la **région** dans lequel vous souhaitez que le service mobile, puis entrez un nom d’utilisateur et mot de passe pour le serveur.
 
    1. Une fois que vous avez entré toutes les informations requises, cliquez sur le bouton **créer** pour créer le service mobile.
    2. Le nouveau service mobile doit apparaître dans la liste des services dans la boîte de dialogue **Services mobiles Azure** . Sélectionnez le nouveau service mobile dans la liste, puis sélectionnez le bouton **Ajouter** pour ajouter le service à votre projet.
    

1. Passez en revue la page de démarrage qui s’affiche et découvrez comment votre projet a été modifié. Une page mise en route apparaît dans votre navigateur chaque fois que vous ajoutez un service connecté. Vous pouvez passer en revue les étapes suivantes suggérées et exemples de code, ou passer à la page où est passée pour afficher les références ont été ajoutés à votre projet, et comment vos fichiers de code et de configuration ont été modifiés.

1. En utilisant les exemples de code comme un repère, commencer à écrire du code pour accéder à votre service mobile.

## <a name="how-your-project-is-modified"></a>Comment votre projet a été modifié

Comment Visual Studio modifie votre projet dépend du type de projet. Pour c# applications client, voir [qu’est devenue – projets c#](http://go.microsoft.com/fwlink/p/?LinkId=513119). Pour les applications de client JavaScript, voir [qu’est devenue – JavaScript projets](http://go.microsoft.com/fwlink/p/?LinkId=513120). Pour les applications Cordova, voir [qu’est devenue – Cordova projets](http://go.microsoft.com/fwlink/p/?LinkId=513116).


##<a name="next-steps"></a>Étapes suivantes

Poser des questions et obtenir de l’aide : 

 - [Forum MSDN : Services mobiles Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=azuremobile)

 - [Azure Services mobiles au Blog de l’équipe Microsoft Azure](https://azure.microsoft.com/blog/topics/mobile/)

 - [Azure Services mobiles en azure.microsoft.com](https://azure.microsoft.com/services/mobile-services/)

 - [Documentation de Services mobiles Azure à l’adresse azure.microsoft.com](https://azure.microsoft.com/documentation/services/mobile-services/)



